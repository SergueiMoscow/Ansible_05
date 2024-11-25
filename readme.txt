python3 -m venv venv

Установка molecule:
pip3 install ansible-dev-tools
pip3 install molecule docker-py molecule-docker

molecule init scenario default --driver-name docker

molecule login -s default --host centos7
molecule test