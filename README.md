nfsflush
========

The Network File System (NFS) is commonly used on Unix to share a
local filesystem over a network.  However, in its quest to make remote
filesystems appear local, it is
[well-known](https://twitter.com/mjdominus/status/55735733578768385)
that its behaviour can be a inconsistent.  Commands that should never
fail, such as `mkdir -p` and `ln -sf`, sometimes do.  This is
especially true when clients expect an immediate, consistent view of
the filesystem and there are many other clients writing concurrently
to the same files or directories.

An excellent resource for application programmers who need to work
with NFS is the NFS Coding HOWTO
<http://irccrew.org/~cras/nfs-coding-howto.html>, which describes many
behaviours where NFS diverges from POSIX semantics, from reads and
writes to memory-mapped files and attribute/file handle caching.  The
NFS Coding HOWTO includes source code for a valuable
[NFS cache tester](http://dovecot.org/tools/nfstest.c) tool that
empirically tests various ways of forcing an operating system to
flush its various NFS caches.

This tiny program, `nfsflush`, flushes an NFS client's attribute cache
for a given path, by performing a `open()` immediately by a `close()`,
as recommended in the
["Attribute Caching"](http://irccrew.org/~cras/nfs-coding-howto.html#attrcache)
section of the NFS Coding HOWTO.  This seems to be as cross-platform
as you can get, and we've successfully tested that this works on at
least RHEL 6.1 with various NFS servers (Linux NFSv4, NetApp, Isilon).
Simply run `nfsflush` when you need a consistent, updated view of the
server, which is typically before problematic commands that fail
weirdly where they shouldn't (such as `mkdir -p` or `ln -sf`).

Enjoy appropriately.
