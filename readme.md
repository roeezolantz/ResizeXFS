As others have pointed out, XFS filesystem cannot be shrunk.

So your best bet is to backup `/home`, remove and recreate its volume in a smaller size and give the rest to your `/root` volume.

• backup the contents of /home
> tar -czvf /root/home.tgz -C /home .

• test the backup
> tar -tvf /root/home.tgz

• unmount home
> umount /dev/mapper/centos-home

• remove the home logical volume
> lvremove /dev/mapper/centos-home

• recreate a new 400GB logical volume for `/home`, format and mount it
> lvcreate -L 400GB -n home centos
> mkfs.xfs /dev/centos/home
> mount /dev/mapper/centos-home

• extend your `/root` volume with ALL of the remaining space and resize (-r) the file system while doing so
> lvextend -r -l +100%FREE /dev/mapper/centos-root

• restore your backup
> tar -xzvf /root/home.tgz -C /home

• check `/etc/fstab` for any mapping of `/home` volume. IF it is using UUID you should update the UUID portion. (Since we created a new volume, UUID has changed)

That's it.

Hope this helps.
