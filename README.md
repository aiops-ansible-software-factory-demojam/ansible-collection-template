# demo.${REPO_NAME}

${REPO_DESCRIPTION}

This collection was generated from the Ansible collection golden path in
Backstage. It follows the homelab starter layout with a role and Molecule
scenario. CNV uses CentOS Stream 10; AWS uses pinned RHEL 9 and RHEL 10 images.

## Get started

Run these commands from the collection root in a demo Omnigent agent sandbox:

```sh
ansible-galaxy collection install -r extensions/molecule/requirements-test.yml
ansible-lint
ansible-galaxy collection build --output-path /tmp
make molecule
```

The default scenario prints hello world on each selected guest, then verifies
SSH connectivity and the operating system. `roles/example` remains a starter
role; add a scenario to converge your role and verify its intended behavior.

The sandbox supplies Ansible Development Tools, SSH, the selected test
identity, `MOLECULE_GLOB`, and preloaded dependencies. CNV also supplies a
scoped `KUBECONFIG`; AWS supplies its credential-file path, network and image
settings. Run from the collection root so the shared `config.yml` is discovered.
`make molecule` runs every scenario; `make test` is an alias. Production
collection dependencies still belong in `galaxy.yml`.

Common inventory loads from `utils/inventory/base/`, followed by the selected
`kubevirt/` or `aws/` host layer and the generated connection inventory.
CNV clones the CentOS Stream 10 DataSource into `molecule-tests`, with two vCPUs,
2 GiB RAM, a disposable 30 GiB disk and PodIP SSH. Its namespace quota permits
four VMs and 120 GiB of requested disks. AWS uses the sandbox's pinned RHEL 9
and RHEL 10 images in a provided public subnet, with `t3.medium` instances,
encrypted 30 GiB gp3 disks and public-IP SSH as `ec2-user`.

Test requirements pin the published `david_igou.molecule_provisioners:0.0.7-alpha`
release. The sandbox preloads its verified Galaxy artifact. Shared inventory
maps the mounted profile to the release's `mp_defaults.aws` fields, including
the expected account and bootstrap-owned security group. Tests retain that group
and network, and destroy their own guests, disks and generated SSH keys.

The shared provisioner assigns run identities and retains cleanup state in
Molecule's ephemeral directory. After an interrupted run, use `make destroy`
from the same sandbox and collection. These tests need the selected backend's
credentials and reachable guest networking; a standalone workspace must supply
those inputs. Verification checks every connected guest against its expected
OS and major version. This demo runs one coding agent.

To add another OS image, add a host to
the selected `extensions/molecule/utils/inventory/<backend>/hosts.yml`, with
a distinct inventory name and expected OS/major version. CNV hosts specify
`mp.kubevirt.boot_source`; AWS hosts specify an exact `mp.aws.image_id`. Common
settings stay in `utils/inventory/base/group_vars/molecule.yml`.
For CNV, grant `get` for the new DataSource in the GitOps `molecule-image-cloner` Role
before using it. The disk must be at least as large as the source image, and the
guest must support cloud-init SSH key injection. Each CNV VM gets one boot DataVolume.

To add a scenario, create a directory under `extensions/molecule/` with only
`molecule.yml`, `converge.yml`, and `verify.yml`. Set `scenario.name` to the
directory name. The shared `config.yml` supplies inventory and lifecycle paths;
scenario playbooks target `hosts: molecule`. Import
`../utils/playbooks/verify.yml` to reuse host connectivity and OS checks, then
add behavioral assertions for your scenario. No lifecycle copies or role
symlinks are needed; shared `config.yml` supplies Molecule's role search path.

## Layout

- `galaxy.yml` defines the collection package.
- `roles/example/` is the starter role.
- `extensions/molecule/config.yml` shares lifecycle configuration across scenarios.
- `extensions/molecule/requirements-test.yml` pins the provisioner release.
- `extensions/molecule/utils/inventory/` tracks boot images and expected OS versions for all scenarios.
- `extensions/molecule/utils/playbooks/` supplies create, prepare, destroy, and common host verification.
- `extensions/molecule/default/` contains only `molecule.yml`, hello-world `converge.yml`, and `verify.yml`.
- `ansible.cfg` and `Makefile` configure collection resolution and root-level test commands.
- `devfile.yaml` defines development commands for editors that support Devfiles.

## License

MIT License. See [LICENSE](LICENSE).
