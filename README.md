# Anonymous Repository Convert
Convert an existing repository to an anonymous reposity with new user, email, and merge messages.

## Problem
You may find you have some code you would like to share publicly.

The code exists on a profile you do not want to expose publicly.

The user, email, and merge commit messages show the information of the existing public profile.

We have:
```
Author: Real Guy <real.guy@gmail.com>
Merge branch 'master' of github.com:Real-Guy/SuperRadCode
```
We need:
```
Author: Anonymous <anonymous@protonmail.com>
Merge branch 'master' of github.com:Anonymous/SuperRadCode
```


## Solution
I would recomend having a backup of the infomation ready.

If you make a mistake you can easily start over.
### Convert
Make a local clone of the existing reposity.

Then run:
```
git filter-branch -f --env-filter '
WRONG_EMAIL="real.guy@gmail.com>"
NEW_NAME="Anonymous"
NEW_EMAIL="anonymous@protonmail.com"

if [ "$GIT_COMMITTER_EMAIL" = "$WRONG_EMAIL" ]
then
    export GIT_COMMITTER_NAME="$NEW_NAME"
    export GIT_COMMITTER_EMAIL="$NEW_EMAIL"
fi
if [ "$GIT_AUTHOR_EMAIL" = "$WRONG_EMAIL" ]
then
    export GIT_AUTHOR_NAME="$NEW_NAME"
    export GIT_AUTHOR_EMAIL="$NEW_EMAIL"
fi
' --tag-name-filter cat -- --branches --tags
```
You will most likely need to run multiple commands, so we will add the force (-f) flag.

You will want to run `git log` to make sure there are not other committers like "user@host.site.com".

Once all your commits have the proper user/email we may still have merges that show the old reposity URL.

Run as much as needed:
```
git filter-branch --msg-filter '
sed "s/github.com:Real-Guy/github.com:Anonymous/g"
' --tag-name-filter cat -- --branches --tags
```
This is a very simple `sed` command that can be used other ways for commit messages.

Simply use the same format:
```
git filter-branch --msg-filter '
sed "s/<old infomation>/<new infomation>/g"
' --tag-name-filter cat -- --branches --tags
```
### Push
By now, you should have all the old information removed.

Change the remote info, switch to default branch and force push to the new repository:
```
git remote rm origin
git remote add origin git@github.com:Anonymous/SuperRadCode.git
```
Switch to new default branch, and force push to new reposity:
```
git branch -M main
git push -f --set-upstream origin main
```
This will produce an exact replica of the commit history under another account.

This will work even if the code pre-dates the account creation date.
