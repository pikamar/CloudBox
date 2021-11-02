# CloudDevIde

docker build --build-arg PYTHON_VERSION=3.9.4 --build-arg ANSIBLE_VERSION=4.7.0 -t pikamar/ansible-ide .

docker run -d -v $(pwd):/workspace -p 8181:8181 --name ansible pikamar/ansible-ide:latest --auth marex99:veiklais

docker run -d -v $(pwd):/ansible -p 8181:8181 --name ansible pikamar/ansible-ide:latest --auth marex99:veiklais

docker exec -it ansible bash

docker rm --force ansible

docker stop ansible


docker run --rm -it -v $(pwd):/ansible/playbooks --workdir /ansible/playbooks --entrypoint ansible-playbook pikamar/ansible-ide --version