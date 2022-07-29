# cloudserver-config

Centralized Makefiles for cloud server configuration

The aim of this project is to centralize the configuration scripts for new cloud server.

The server will be configured for Laravel PHP Application

For now it supports:
- Ubuntu Server 22
- Debian 11

Feel free to use this simple project as you wish.

## Usage

To use the correct Makefile:
- ssh inside your cloud server
- clone this repository: `git clone https://github.com/marco-introini/cloudserver-config.git`
- create a symlink to the correct Makefile according to your linux distro. For example: `ln -s cloudserver-config/ubuntu22/Makefile .`
- View available commands with `make info` 

## Contributions

If you want you can improve the Makefile and, best at all, add another for other Cloud Servers. Thank you!