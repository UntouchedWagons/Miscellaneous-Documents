# Using unprivileged accounts with Syncoid

Most of the time when we do ZFS replication, we use published root ssh keys--meaning that replication looks something like this:

```
root@sendbox:~# zfs send pool/ds@1 | ssh root@recvbox zfs receive pool/ds
```

Although this is convenient, it opens up a can of security worms: if recvbox allows the root user on sendbox root shells, that means an attacker who gets root on sendbox implicitly also has root on recvbox and vice versa.

ZFS delegation allows you to use unprivileged system accounts instead. This means that gaining root on sendbox no longer implicitly grants an attacker root on recvbox, or vice versa--while the attacker can still pivot from one box to the other, they arrive on the second box in an unprivileged account. This is very much worth doing for security purposes!

The very, very TL;DR of what you need looks like this:

```
root@sendbox:~# zfs allow senduser send,hold pool/ds
root@recvbox:~# zfs allow recvuser receive,create,mount pool
```

This allows the following unprivileged replication to succeed:

```
senduser@sendbox:~$ zfs send pool/ds@1 | ssh recvuser@recvbox zfs receive pool/ds
```

This is also sufficient to allow a syncoid command to work properly:

```
senduser@sendbox:~$ syncoid --no-privilege-elevation --no-sync-snap pool/ds recvuser@recvbox:pool/ds
```

In fact, syncoid is the only reason we needed to grant the hold permission to senduser in the first place: bare-bones ZFS replication doesn't require it, but syncoid directly issues a hold on the sending side while replication is live. (We're also assuming you're using the most recent syncoid from master: if you're running an older RELEASE version, you also need to grant rollback on the receiver side.)

This is the absolute most barebones explanation of unprivileged replication, of course. If you'd like a more detailed one, I wrote up a full article on the process for Allan Jude's consulting company, Klara Systems: https://klarasystems.com/articles/improving-replication-security-with-openzfs-delegation/

I highly recommend reading the full article if you're interested in the hows, whys, and the process of figuring things out rather than just the barebones recipe shown above. =)
