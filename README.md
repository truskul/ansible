# ansible (installation test)

A test playbook written in Ansible to install software, with Docker Compose included to test the playbook.


The playbook includes the assumption that we only install packages that have been defined in inventory, but do not have them installed on a specific host. On the other hand, when such a package does not exist or cannot be installed, Ansible aborts and returns an error code (2).  If, on the other hand, the installation succeeds, it returns a code of 0. You can check this with the `echo $?` command after executing the playbook.
## Installation

The following list of software is required to properly run the playbook:

- Git
- Python 3
- Ansible
- Docker

Clone the project

```bash
git clone https://github.com/truskul/ansible
```

## Appendix

For the sake of smooth and efficient execution of the task, I decided to make some concessions and did not do everything as expected:
- Playbook should not execute through the `root` user. Instead, the playbook should include a `become: true` condition that would run the task in question as an administrator.
- I have enabled SSH login via the `root` user. The password for this user is `root` - it must be specified when starting the playbook - we must specify it in the prompt whenever the `--ask-pass` flag is used. This is because I did not want to generate and share a private key. This is an obvious anti-pattern and such a solution should never be used production-wise. We should use the Ansible Valut solution to create and manage secrets.
- Authentication should be done with a private key, not with a password.
- I used the ready-made image provided by the Ubuntu system with the sshd service installed to accept connections via the SSH protocol.
- The inventory files should never be in the repository. They have been included for the purpose of testing.
- Registering whether a package exists could be solved using the `facts_packages` module, while Ansible has some limitations on templates and the use of conditional statements in them. For the purpose of the task, we assume that we are using an Ubuntu system, which has a built-in `command` program.

## Testing

To check whether containers are reachable and whether operations can be performed on them, you can check this with the following command:
```bash
ansible all -m ping -v --ask-pass
```
## Run Locally

Go to the project directory

```bash
cd ansible
```

Run `docker-compose.yml` to create the containers on which the playbook will execute.

```bash
docker-compose up -d
```

Once the containers are created, you can proceed to launch the playbook. For testing purposes, two inventories have been created, assuming two scenario:
- `inventory/hosts_exists.yml` - here we assume that the software defined in the variables is installable.
- `inventory/hosts_exists.yml` - here we assume that one of the packages defined in the variables does not exist.


To start the playbook, run the following command:
```bash
ansible-playbook install-software.yml -i inventory/hosts_exists.yml --ask-pass
```
or
```bash
ansible-playbook install-software.yml -i inventory/hosts_not_exists.yml --ask-pass
```
In the prompt, enter the password for the `root` user (root).