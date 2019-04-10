#ZBMed Ansible Workshop: Docker

In this tutorial, we will create an Ansible Role for the installation and configuration of docker on a single host.
[Docker](https://docker.io) is a framework for creating and running containers. Containers are a lightweight and portable solution for running and packaging
applications.

We will not only install the docker engine, but also make some small configuration changes.

Requirements:
* A running and reachable instance on the cloud.
* A working ansible installation (eg. laptop) on a system which is able to reach the cloud instance.


## **Project Structure**

This repo contains some directorys and files. Some of them has marked tasks on them.

* **/hosts** contains the inventory of our infrastructure.
* **/ansible.cfg** describes parameters about connecting to targeted instances, like path to ssh-keys, usernames and other various options.
* **/site.yml** The main ansible playbook for executing tasks and roles. This is the entrypoint for ansible.
* **/roles/zbmed.docker/** The role which will actually install and configure docker, the quintessence of this tutorial.


Roles itself have a defined folder structure, seperating various classes of tasks. You can read more about roles [here](https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse_roles.html).
Not every folder is needed for this tutorial, here a quick overview for our case:

| Folder | Description |
| ------------- | -------------:|
| zbmed.docker/defaults | Default variables which are used for generating config files and such. | 
| zbmed.docker/handlers | Special tasks which are called when a change happens in a task. |
| zbmed.docker/tasks | Actual working tasks in order to install and configure docker. |
| zbmed.docker/templates | Holds configuration files which can be generated with variables from zbmed.docker/defaults |


## **Executing Ansible**

In order to execute this playbook/role simply call this command with the current working directory of this project:

`ansible-playbook -i hosts site.yml`


## **Tasks**



**Task 1**

In order for ansible correctly connecting to instances, we need to tell ansible on how to actually connect to these.
For this we have the **ansible.cfg** file. In there you have to add the path to your private ssh-key you probably have created earlier this day.


**Task 2**

We now have to define the list of our instances. For the sake of simplicity, we will target only one host created earlier this day.
Simply edit the **hosts** file and add the FloatingIP-Address (or DNS-Name) of your instance under the ***all*** tag.
You can find your IP-Address in the OpenStack-Dashboard. You can also use the auto-generated DNS-Name which goes by the following schema:

`<INSTANCE_NAME>.ZBMEDWorkshop.projects.bi.denbi.de`

**Task 3**

Try out if the basic setup is working by executing inside the project folder.

`ansible all -b -i hosts -m ping`

You should receive a **success** message.

**Task 4**

Fill out the gaps in **roles/zbmed.docker/tasks/main.yml**. The description is given in the file.

**Task 5**

Open up **zbmed.docker/defaults/main.yml** and **zbmed.docker/templates/daemon.json.j2** at the same time.

Can you figure out what their relationship is?

**Task 6**

We wan't to make use of [ansible-galaxy](galaxy.ansible.com). Ansible Galaxy is a community-driven repository sorely for sharing
Ansible Roles with other users. Downloading and using those roles is very easy. Ansible provides for this the `ansible-galaxy` CLI Tool.
Let's try to install a foreign role, which installs an Web-IDE (Theia) for browsing and editing files on the instance via browser.


First download the role with:

`ansible-galaxy install jkrue.theia-ide`

After that, open the **site.yml** file and add the role **jkrue.theia-ide** directly under the **zbmed.docker** role.

Now finally execute the completed playbook/role with `ansible-playbook -i hosts site.yml` and control that everything gets installed.

On finish, you can open Theia on your browser (if you are using one of our provided x2go instances) and point it to `http://localhost:8080`.

If you are not using our x2go instances, you have to create a portforwarding to your local machine via:

`ssh -i <path_to_private_key> -L 8080:localhost:8080 ubuntu@<floatingip_or_dns_of_instance>`