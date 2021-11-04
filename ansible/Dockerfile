# Based on "Debian GNU/Linux 9 (stretch)"
FROM sapk/cloud9
LABEL maintainer="Mareks Pikalovs https://www.linkedin.com/in/mareks-pikalovs-b3649919/"

# Update packages and install python prerequisites
RUN apt update && apt upgrade -y && apt install -y wget \
    build-essential \
    libreadline-gplv2-dev \
    libncursesw5-dev \
    libssl-dev \
    libsqlite3-dev \
    tk-dev \
    libgdbm-dev \
    libc6-dev \
    libbz2-dev \
    libffi-dev \
    zlib1g-dev \
    zsh \
    && rm -rf /var/lib/apt/lists/*

ARG PYTHON_VERSION
ENV PYTHON_VERSION=${PYTHON_VERSION:-3.9.4}
RUN echo $PYTHON_VERSION

WORKDIR /tmp
# Install python from source
RUN wget https://www.python.org/ftp/python/$PYTHON_VERSION/Python-$PYTHON_VERSION.tgz
RUN tar xzf Python-$PYTHON_VERSION.tgz

WORKDIR /tmp/Python-$PYTHON_VERSION
RUN ./configure --enable-optimizations --prefix=/usr
RUN make install

RUN set -x && \
    echo "==> Install OH MY ZSH..."  && \
    wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh 

# Enable aliases and ZSH
COPY .bashrc /root

ARG ANSIBLE_VERSION
ENV ANSIBLE_VERSION=${ANSIBLE_VERSION:-4.7.0}
RUN echo $ANSIBLE_VERSION

RUN set -x && \
    echo "==> Upgrade Python packages..."  && \
    pip3 install --upgrade pip && \
    pip3 install ansible==$ANSIBLE_VERSION

RUN set -x && \
    echo "==> Adding hosts for convenience..."  && \
    mkdir -p /etc/ansible /ansible && \
    echo "[local]" >> /etc/ansible/hosts && \
    echo "localhost" >> /etc/ansible/hosts

ENV ANSIBLE_GATHERING smart
ENV ANSIBLE_HOST_KEY_CHECKING false
ENV ANSIBLE_RETRY_FILES_ENABLED false
ENV ANSIBLE_ROLES_PATH /workspace
ENV ANSIBLE_SSH_PIPELINING True
ENV PYTHONPATH /ansible/lib
ENV PATH /ansible/bin:$PATH
ENV ANSIBLE_LIBRARY /ansible/library

# Install additional ansible roles
COPY ansible.yaml /tmp/ansible.yaml
RUN ansible-galaxy collection install -r /tmp/ansible.yaml

RUN set -x && \
    echo "==> Cleaning up..."  && \
    rm -rf /tmp/*

WORKDIR /cloud9

#ENTRYPOINT ["ansible-playbook"]
# start cloud 9 IDE
CMD node server.js