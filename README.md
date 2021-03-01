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

*The following versions are known not to generate correctly:
 - 1.10.0.Final
 - 1.11.0.Final
 - 1.11.1.Final*

[**GIT_MSG - Migration Checklist**](.github/workflows/update-quarkus.yaml#L196)

Upon the creation of a pull request, a checklist will be added as a comment. 
A basic checklist has already been provided, but you may wish to update it for your requirements.

## Future direction

It is intended that this will be created as a custom GitHub Action to make it simpler and cleaner for anyone wishing to use it.