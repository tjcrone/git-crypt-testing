# Git-Crypt Testing

These are a set of files and directories for testing multiple git-crypt keys in a single repository. 

Based on my testing, git-crypt is relatively smart about not re-encrypting an already-encrypted file. For this reason, I think the easiest way to migrate to multiple keys in one repo is for the key master to generate a new key for the new deployment using
```
git-crypt init -k NEW_KEY_NAME
```
and provide this to the individuals reponsible for administering the new deployment to use for their secrets files. This new key will need to be added to the CircleCI environment variables, and the .gitignore and .circleci/config.yml will need to be updated accordingly.

Eventually it would probably be best to migrate all users of git-crypt to a separate key for each deployment and remove the default key from .gitattributes completely, but a bit of care will need to be taked to avoid exposing secrets. This is almost certainly doable and probably best earlier rather than later while the number of people who have the shared key is small.
