# action-checkout-race-condition

This repo was created to demo a bug/race condition in actions/checkout@v1.
With the workflow in this repository there are ways to show the bug.


## Race condition

With the workflow in this repository the 'race condition' will trigger
when two commits are pushed in the same minute. [This is because the
workflow starts with `sleep 60`)

This means: steps 4, 5, 6, 7 all **need** to be done in the same minute.

### Steps to reproduce

1. Clone this repo
2. Enable GitHub actions
3. Create a new branch: `git checkout -b dummy-race`
4. Add a dummy commit: `echo foo > DUMMY1 && git add . && git commit -m 'Dummy race commit 1'`
5. Push the branch: `git push -u origin dummy-race`
6. Add a second dummy commit: `echo bar > DUMMY1 && git add . && git commit -m 'Dummy race commit 2'`
7. Push the branch: `git push origin dummy-race`
8. Check GitHub action run for 'Dummy commit 1' (it failed)
9. Check GitHub action run for 'Dummy commit 2' (it succeeded)

### Re-running a job

When re-running an old job the same problem also shows up.

### Steps to reproduce

1. Clone this repo
2. Enable GitHub actions
3. Create a new branch: `git checkout -b dummy-rerun`
4. Add a dummy commit: `echo foo > DUMMY2 && git add . && git commit -m 'Dummy rerun commit 1'`
5. Push the branch: `git push -u origin dummy-rerun`
6. Wait for the GitHub action run to complete (it should succeed)
7. Add a second dummy commit: `echo bar > DUMMY2 && git add . && git commit -m 'Dummy rerun commit 2'`
8. Push the branch: `git push origin dummy-rerun`
9. Wait for the GitHub action run to complete (it should succeed)
10. Re-run the GitHub action run of the 'Dummy commit 1': it now fails
