# Collection development

Use fully qualified Ansible module names and keep roles idempotent. Put
configuration defaults in `roles/<role>/defaults/main.yml` and prefix their
names with the role name. Keep secret lookups outside roles.

Run `make molecule` from the collection root in the demo Omnigent sandbox.
It runs every scenario discovered by `MOLECULE_GLOB`. The sandbox selects the
backend and preloads the exact provisioner version from
`extensions/molecule/requirements-test.yml`. Keep shared settings in
`extensions/molecule/config.yml` and `utils/inventory/base/group_vars/`.
CNV uses `utils/inventory/kubevirt/hosts.yml` and CentOS Stream 10 through the
existing DataSource. AWS uses `utils/inventory/aws/hosts.yml` and the sandbox's
pinned RHEL 9 and RHEL 10 images, network and scoped credentials. Do not switch
backends or copy credentials into the repository.

Use the shared provisioner lifecycle playbooks in `utils/playbooks/`. Each
scenario contains only `molecule.yml`, `converge.yml` and `verify.yml`. Tests
must complete creation, convergence, idempotence, verification and destruction
for every selected host. After an interrupted run, use `make destroy` in the
same sandbox and collection so it can find the saved ownership record.
