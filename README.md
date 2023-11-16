# vagrant-slurm

Requires `libvirt` (and optionally `virt-manager`), or Virtualbox (in this case you'll need to modify `Vagrantfile`).


- `vagrant up --no-parallel` to start the VMs (`no-parallel` ensures controller is up before computes start)

- `vagrant ssh controller` to connect.

- `vagrant provision` will re-provision the VMs with Ansible. Automatically done with the first `vagrant up`

- Slurm commands (`sbatch`, `squeue`, `sinfo` etc. should work as expected)

- Uses Slurm `configless` to avoid duplicating `slurm.conf` across nodes.
