# Ansible playbooks to create a cloud-based bastion instance
The `bastion` project contains a `site.yaml` file and _roles_
for the formation (and removal) of a bastion machine to support the
Galaxy processing cluster as described in our [galaxy-cloud] project.

## Provider environment
You will need to set provider-specific environment variables before you
can run this playbook. If you're using OpenStack you should `source` the
keystone file provided by your stack provider. This sets up the essential
credentials to create and access cloud resources.
    
## Playbook configuration
The playbook relies on a number of _roles_ in the project. Where appropriate,
each role exposes its key variables in a corresponding `defaults/main.yaml`
file but the main (common) variables have been placed in
`group_vars/all/main.yaml`.

At the very least you should provide your own values for: -

-   `instance_base_name`. A tag prepended to the cloud objects created
    (instances and volumes)
-   `bastion_addr`. The IP address (from a pool you own) to assign to the
    bastion node.

>   Feel free to review all the variables so that you can decide whether
    you'd like to provide your own values for them.  

The easiest way to over-ride the built-in values is to provide your
own YAML-based [parameters] file called `parameters`. The project `parameters`
file is excluded from the repository using `.gitignore`.

## Running the playbook
With environment variables set and a `parameters` file written,
run the following from the project root, ideally from inside a Python 3
[virtual environment] on your local control machine (a desktop or laptop): -

    $ pip install -r requirements.txt
    $ ansible-playbook site.yaml --extra-vars "@parameters"

The playbook creates and installs a dedicated SSH key-pair for the cloud
provider. Its name is based on the `instance_base_name` you use. The key is
typically called `id_rsa_<instance_base_name>` and will be written to
your local SSH directory. For convenience the playbook ends with a message
that provides you with the SSH command you need to run to connect to the
new bastion instance. It'll be something like: -

    $ ssh -i ~/.ssh/id_rsa_graph abc12345@192.168.0.1

When you login to the bastion you'll be placed into a Python 3
virtual environment suitable for running our other playbooks.

A copy of the generated key-pair is also installed on the bastion for use
there.

To destroy the bastion, from your local workstation, run: -

    $ ansible-playbook unsite.yaml --extra-vars "@parameters"

---

[galaxy-cloud]: https://github.com/InformaticsMatters/ansible-galaxy-cloud
[parameters]: https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#passing-variables-on-the-command-line
[virtual environment]: https://docs.python.org/3/tutorial/venv.html
