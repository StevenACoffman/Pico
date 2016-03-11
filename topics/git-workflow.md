### Workflow
1. Fork and clone.
1. Add the upstream repository as a new remote to your clone.
   `git remote add upstream git@github.com:something/somethinelse.git`
1. Create a new branch
   `git checkout -b name-of-branch`
1. Commit and push as usual on your branch.
   `git push origin name-of-branch`
1. Switch back to master and pull (fetch and merge)
   `git checkout master && git pull`
1. Create a temporary branch
   `git checkout -b tmpsquash`
1. `git merge --squash name-of-branch`
1. `git commit -m 'Summary Comment Goes Here'`

### Alternate Workflow
1. Fork and clone.
1. Add the upstream repository as a new remote to your clone.
   `git remote add upstream git@github.com:something/somethinelse.git`
1. When you're ready to submit a pull request, create a new branch.
   `git checkout -b squashed_feature`
1. When you're ready to submit a pull request, rebase your branch onto
   the upstream master (squashing all but first commit) so that you can resolve any conflicts:
   `git fetch upstream && git rebase -i upstream/master`
1. You may need to push with `--force` up to your branch after resolving conflicts.
   `git push -f origin name-of-branch`
1. When you've got everything solved, push up to your branch and send the pull request as usual.
