# Git workflow

App development loosely follows [GitHub Flow](https://docs.github.com/en/get-started/using-github/github-flow)[^1], where features and fixes are committed to branches, and then merged to `main` through pull requests (PR).

![git-workflow](../assets/images/git-workflow-simple-light.png)
<!-- /// caption
git workflow
/// -->

!!! tip
    - Commit often using short, descriptive comments.
    - Keep branches short-lived - merge into `main` often

## Start with an issue

When tackling a bugfix or embarking on the development of a new feature, begin by crafing a meaningful issue as a starting point.

![new-issue](../assets/videos/new-issue.gif)

## Create a branch

Next, create a branch and check it out in your local IDE

![create-branch](../assets/images/create-branch.png)

Commiting code work to the branch frequently ensures that someone else can step-in and help when needed. If you want to check that your approach is valid, use issue comments with @name-handles to get someone's attention.

## Pull requests & merging

When the branch satisfies the goals set out in the issue and you're ready to get it merged into main, create a pull request (PR) and ask for a review.

If the reviewer has suggestions or corrections, they normally point out the specific lines in question, and generate a comment thread in the PR.

[^1]: [https://docs.github.com/en/get-started/using-github/github-flow](https://docs.github.com/en/get-started/using-github/github-flow)
