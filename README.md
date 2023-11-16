# vagrant-slurm

Requires `libvirt`, or Virtualbox (in this case you'll need to modify `Vagrantfile`)

`vagrant up --no-parallel` to start the VMs (`no-parallel` ensures controller is up before computes start)
`vagrant ssh controller` to connect.
Slurm commands (`sbatch`, `squeue`, `sinfo` etc. should work as expected)
