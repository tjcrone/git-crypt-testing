# Git-Crypt Testing

These are a set of files and directories for testing multiple git-crypt keys in a single repository. Use these files to run git-crypt tests on a repo with multiple keys.

## Important Note

The keys in this repo are dummy keys that have been published. They should not be used for real work. The keys you use for real work are secrets and must be kept secret.

## Results and Discussion

Based on my testing, git-crypt is relatively smart about not re-encrypting an already-encrypted file. For this reason, I think the easiest way to migrate to multiple keys in one repo is for the keymaster to generate a new key for a new deployment using:

`git-crypt init -k NEW_KEY_NAME`

and provide the resulting key to the individuals reponsible for administering the new deployment to use with git-crypt. This new key will need to be added to the CircleCI environment variables, and the .gitattributes and .circleci/config.yml will need to be updated accordingly.

Eventually it would probably be best to migrate all users of git-crypt to a separate key for each deployment and remove the default key from .gitattributes completely, but a bit of care will need to be taked to avoid exposing secrets. This is almost certainly doable and probably best earlier rather than later while the number of people who have the shared key is small.

Some things worth remembering:

* Despite some (possibly deprecated?) [advice](https://github.com/AGWA/git-crypt/issues/63#issuecomment-143017854) to run git-crypt init only once, I think the best way to add a new named keys is to rerun git-crypt init. This is because git-crypt keygen is marked as deprecated, and generates a key that looks like a default key regardless of the name (see below). The git-crypt init process seems to cause no problems with an already initialized repository, creates a correctly formatted named key, and creates the required filters in .git/config.
* Keys know who they are, and operate like the key they were when they started life. That is, the name of a key is embedded in the key (in plain text), except for default keys which appear not to have an embedded name. For this reason, it is not possible change the name of an existing key, such as a default key, and make it work like a named key. For example, here is the "ooi" key in this repo:
```
00000000: 0047 4954 4352 5950 544b 4559 0000 0002  .GITCRYPTKEY....
00000010: 0000 0001 0000 0003 6f6f 6900 0000 0000  ........ooi.....
00000020: 0000 0100 0000 0400 0000 0000 0000 0300  ................
00000030: 0000 20a3 21be 3efd 204a 1674 2dfd 267b  .. .!.>. J.t-.&{
00000040: 9680 466b 9b35 078c 60d2 6f95 9d7a cdc7  ..Fk.5..`.o..z..
00000050: bc7f 0f00 0000 0500 0000 40c8 09e6 e918  ..........@.....
00000060: 0941 e198 8df6 c125 1328 2071 4747 70af  .A.....%.( qGGp.
00000070: e365 e7ea 64fe 0e2b 1bbd 49e5 6e81 a5d3  .e..d..+..I.n...
00000080: c111 706c adb0 3ec4 cb38 0ba1 9a75 0968  ..pl..>..8...u.h
00000090: f04f 6189 e671 5044 7b1c 7000 0000 00    .Oa..qPD{.p....
```
This is the key that will be used with the ooi filter regardless of the key's filename, so renaming keys is not recommended because it will just cause confusion.
* The best way for new users to initialize their repo is to use `git-crypt unlock KEY_NAME` rather than git-crypt init. This is because unlock appears to do everything that init does but does not create a new default key. Creating a new default key is bad because this key will be used with the default filter and git-crypt will complain that the files encrypted with the default filter have changed. This is one of the reasons to move away from the default key entirely.
