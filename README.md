I'm going to break this repository, but so far I haven't been able to.

Here's what I tried:

```
# Create a tree like HEAD's tree, but with 0s for the submodule oid in place of
# a real commit oid.

$ git cat-file -p HEAD | grep ^tree
tree 658f590cbf4ca68a6a3241d70d1d397597485efc

$ git cat-file -p 658f590cbf4ca68a6a3241d70d1d397597485efc
100644 blob 6ad869b685a60d308c494834798bffcb5bdbaf64    .gitmodules
100644 blob 620d7ac1038c5e3954418dea6fb56f9009dd1b3e    README.md
100644 blob ec312510b04f9b984a83decb5be4459490f0c5e1    go.mod
100644 blob aa699acaef0eeff60568122662fb097547d66ef8    go.sum
100644 blob b5e1c4125fefd0dd4ff877a58fef45ce56be4826    main.go
160000 commit f7ff5214d07941751ecbb5fb7d68bd7b4763b9e9  silver-eureka

$ (git cat-file tree 658f590cbf4ca68a6a3241d70d1d397597485efc | head -c 200; printf '\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0') | git hash-object --stdin -t tree --literal -w
628dbab9bfcf189e1b0e2a3172ef42417c8dc96d

$ git cat-file -p 628dbab9bfcf189e1b0e2a3172ef42417c8dc96d
100644 blob 6ad869b685a60d308c494834798bffcb5bdbaf64    .gitmodules
100644 blob 620d7ac1038c5e3954418dea6fb56f9009dd1b3e    README.md
100644 blob ec312510b04f9b984a83decb5be4459490f0c5e1    go.mod
100644 blob aa699acaef0eeff60568122662fb097547d66ef8    go.sum
100644 blob b5e1c4125fefd0dd4ff877a58fef45ce56be4826    main.go
160000 commit 0000000000000000000000000000000000000000  silver-eureka

# Create a commit with the new bad tree.

$ echo 'break things' | git commit-tree 628dbab9bfcf189e1b0e2a3172ef42417c8dc96d -p HEAD
cdbe2bd501e21cd2125da20f74e3dde392dc8fad

# Push it to github.

$ git push origin cdbe2bd501:refs/heads/broken
Enumerating objects: 8, done.
Counting objects: 100% (8/8), done.
Delta compression using up to 16 threads
Compressing objects: 100% (7/7), done.
Writing objects: 100% (7/7), 2.07 KiB | 2.07 MiB/s, done.
Total 7 (delta 4), reused 0 (delta 0), pack-reused 0
remote: error: object 628dbab9bfcf189e1b0e2a3172ef42417c8dc96d: nullSha1: contains entries pointing to null sha1
remote: fatal: fsck error in packed object
error: remote unpack failed: index-pack failed
To github.com:spraints/psychic-computing-machine.git
 ! [remote rejected] cdbe2bd501 -> broken (failed)
error: failed to push some refs to 'github.com:spraints/psychic-computing-machine.git'

```
