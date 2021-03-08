[![Verify GitHub Action](https://github.com/jthambly/update-quarkus/actions/workflows/verify.yml/badge.svg)](https://github.com/jthambly/update-quarkus/actions/workflows/verify.yml)

# update-quarkus (quarkus-update-action)

This project is for a GitHub Action that helps simplify the updating of [Quarkus](https://quarkus.io/) project files contained in a given GitHub repository.

## How to use

To use, copy the [example code](#example-usage) snippet into a new file under the projects `.github/workflows/` folder. 

For example, in a file named `.github/workflows/update-quarkus.yaml`.

In the [provided example](#example-usage), the action will run automatically once a week and has the option to be [run manually](https://docs.github.com/en/actions/managing-workflow-runs/manually-running-a-workflow).

It is important to note, that when [checking out](https://github.com/actions/checkout#usage) the repository, the path needs to be set to use `working-copy`.

For more information on creating GitHub Actions, please visit the recommended [Documentation](https://docs.github.com/en/actions/creating-actions).

## What it does

Once commenced:

 - It will look to see if there is a newer Quarkus version available. 
 - If so:
   - it will create a new branch, 
   - update and commit the new project files, 
   - create a pull request for your review,
   - include links to Quarkus documentation, and
   - include a migration checklist (if provided).

### Updating files

It will attempt to determine which Quarkus files it should remove from the given project, before copying the newer files across. 

It decides this by using the following process:

1) If you have had a previous update, the process will leave a file listing with a checksum. 
   Upon a subsequent update, it will know which files to remove; and with the included checksums, it will not touch modified files.

2) If no previous update information is available, it will generate a blank project using the current version.
   It will use this blank project information to determine which unmodified Quarkus files to remove.

3) Failing this, in particular for [certain Quarkus versions](https://github.com/jthambly/update-quarkus/issues/54#issue-820449828), it will not have enough information to automatically detect relevant files.
   In this case, it will remove file names based on known Quarkus files. It will not however know which files have been modified, and which have not.

*The non-Quarkus project files will be safe. However, given that some of these backup processes may not be 100% accurate, it is always important to review changed files during the pull request review.*

### Adding a Checklist

Upon the creation of a pull request, you can include a checklist. An example has been included in the [example code](#example-usage) snippet and uses the standard GitHub [task list format](https://docs.github.com/en/github/managing-your-work-on-github/about-task-lists).

## Action information

## Inputs

### `github_token`

The GitHub token will need to be passed onto this action, for it to be able to operate with the given repository.

### `checklist`

An optional [checklist](https://docs.github.com/en/github/managing-your-work-on-github/about-task-lists) can be published along with the final pull request. By default, none will be added.

### `diagnostics`

If you wish to print out additional information such as the working tree, or environment variables, set it to `true`. Default `false`.

## Outputs
 
*There are no outputs for this Action.*

## Example usage

```

# Update your Quarkus project to a newer version

name: quarkus-update-action
on:
  schedule:
  - cron: '0 0 * * 0'
  workflow_dispatch:
  
jobs:
  update:
    runs-on: ubuntu-latest
    steps:

    - name: Check out the repository using working-copy as its path
      uses: actions/checkout@v2
      with:
        path: working-copy

    - name: Update Quarkus version
      uses: jthambly/update-quarkus@v1.0.11
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
        checklist: |
          Migration Checklist:
         
          - [x] Reviewers and Assignees assigned
          - [x] Link to Migration Documentation provided
          - [ ] Migration Documentation reviewed
          - [ ] Changes reviewed against existing project
          - [ ] File modifications reviewed
          - [ ] Automated checks performed
          - [ ] Manual QA performed

```