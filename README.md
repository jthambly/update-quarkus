[![Verify GitHub Action](https://github.com/jthambly/update-quarkus/actions/workflows/verify.yml/badge.svg?branch=master)](https://github.com/jthambly/update-quarkus/actions/workflows/verify.yml)

# update-quarkus

This project provides the GitHub Action workflows to update project files to use a newer version of [Quarkus](https://quarkus.io/).

## How to use

Copy the [action file](.github/workflows/update-quarkus.yaml) to your projects `.github/workflows/` folder.

The action will run automatically once a week or can be [manually run](https://docs.github.com/en/actions/managing-workflow-runs/manually-running-a-workflow).

Once commenced:

 - It will look to see if there is a newer Quarkus version available. 
 - If so:
   - it will create a new branch, 
   - update and commit the new project files, 
   - create a pull request for your review,
   - include links to Quarkus documentation, and
   - include a migration checklist.

## Customization

There are two areas within the action file you may want to review and if necessary, configure.

[**PROJECT_FILES**](.github/workflows/update-quarkus.yaml#L142)

To determine which files are associated with Quarkus (as opposed to your files), the update process will generate a blank project using your current Quarkus version. Using this information, it will know which unmodified files to delete.

If for some reason, it cannot generate a blank project (perhaps if a version is blocked for security reasons etc..) it will resort to a secondary method.
In this case, it will update files with newer ones and may remove any files **not protected/listed** underneath the [PROJECT_FILES](.github/workflows/update-quarkus.yaml#L142) section.

*It is intended that this need will be removed in a later release, and the update process will attempt a best-effort approach.*

The following blank project versions are known not to generate correctly:
 - 1.10.0.Final
 - 1.11.0.Final
 - 1.11.1.Final

[**GIT_MSG - Migration Checklist**](.github/workflows/update-quarkus.yaml#L196)

Upon the creation of a pull request, a checklist will be added as a comment. 
A basic checklist has already been provided, but you may wish to update it for your requirements.

## Under Development - GitHub Action

Development work is underway to create this as a custom GitHub Action to make this simpler. If you wish to try this out please continue reading.

### Inputs

#### `github_token`

The GitHub token will need to be passed onto this action, for it to be able to operate with your repository.

#### `diagnostics`

If you wish to print out additional information such as the working tree, or environment variables, set it to `true`. Default `false`.

### Outputs
 
*There are no outputs for this Action.*

### Example usage

```

name: update-quarkus
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

    - name: Gather Existing POM information
      id: quarkus-pom
      uses: jthambly/quarkus-pom-action@v1
      with:
        filename: working-copy/pom.xml

    - name: quarkus-update-action
      uses: jthambly/update-quarkus@v1.0.7
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
      env:
        CURRENT_VERSION: ${{ steps.quarkus-pom.outputs.current_version }}
        PROJECT_GROUP: ${{ steps.quarkus-pom.outputs.project_group }}
        PROJECT_NAME: ${{ steps.quarkus-pom.outputs.project_name }}
        QUARKUS_EXT: ${{ steps.quarkus-pom.outputs.quarkus_extensions }}

```