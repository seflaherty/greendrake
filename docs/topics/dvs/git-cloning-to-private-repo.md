---
tags: git, clone, private
---

# Cloning a Public Repo to a Private One

## Context
I wish to use `ramanshah`'s bookkeeping workbook to track how well I am doing a being a power connector.


## Dependencies
* [git](https://git-scm.com/doc)
* GitHub account
* GitHub Official CLI [gh-cli](https://cli.github.com/manual/)


## What was Done
1. Get a bare clone of the repo `gh repo clone https://github.com/seflaherty/contector.git -- --bare`
2. Change into the bare clone directory `cd contector.git`
3. Open a browser tab and go to your GitHub. Create a new Private repo
4. Back at the CLI, mirror your clone `git push --mirror https://github.com/seflaherty/power-connector-xlsx.git`
5. Move out of the base clone directory, then delete it `cd .. && Remove-Item  'contector.git' -Force -Recurse` 
    * See [[ps-miscellany]]
6. Clone down the Private repo `gh repo clone https://github.com/seflaherty/power-connector-xlsx.git`
7. Change into the Private clone directory `cd power-connector-xlsx`
8. Create a new branch `git checkout -b my_xlsx`
9. Rename the original workbook `git mv .\power_circles.xlsx .\my_power_circles.xlsx`
10. Set the remote origin to track the branch `git push --set-upstream origin my_xlsx`
11. Modify file 'my_power_circles.xlsx' and commit it to the Private repo at will


## Resources
[ISBN: How to be a Power Connector](https://isbnsearch.org/isbn/9780071830737)
[Public Repo (Fork)](https://github.com/seflaherty/contector)
[GitHub Docs: Duplicating a Repo](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/duplicating-a-repository)