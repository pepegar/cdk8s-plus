# Rotate

This document describes the changes required in order to create a new cdk8s-plus-XX library based 
on the latest kubernetes version. These steps should be executed in order.

## :one: Prerequisite

### Add new k8s spec to cdk8s repo

1. ([cdk8s repo](https://github.com/cdk8s-team/cdk8s)): Generate [`kubernetes-schema/vX.XX.X/_definitions.json`](https://github.com/cdk8s-team/cdk8s/tree/master/kubernetes-schemas)

    ```sh
    tools/import-spec.sh x.xx.x
    # provide the new version number e.g. 1.25.0
    ```

2. Create PR, review then merge the updated spec into cdk8s/master branch ([e.g. v25 PR](https://github.com/cdk8s-team/cdk8s/pull/1007)).

### Publish new branch to cdk8s-plus-go repo

3. ([cdk8s-plus-go repo](https://github.com/cdk8s-team/cdk8s-plus-go)): Create and publish a new branch for the new k8s version

    ```sh
    git checkout -b k8s.xx
    # e.g. k8s.25 for version 1.25.0
    git push --set-upstream origin k8s.xx
    ```

### Create a new backport label in cdk8s-plus' GitHub

4. Open the [cdk8s-plus GitHub label list](https://github.com/cdk8s-team/cdk8s-plus/issues/labels)

5. Add a new label for the **current** Kubernetes version.
   - Label name: `backport-to-k8s-XX/main`
   - Label color: `#F53E94`

  e.g. If you were upgrading from v24 -> v25 the new label would be `backport-to-k8s-24/main`

## :two: Create the new cdk8s-plus branch

6. Create a new branch in the [cdk8s-plus](https://github.com/cdk8s-team/cdk8s-plus) off the current default branch. 
The new branch should be named `k8s-XX/main` (e.g. `k8s-25/main` for K8s v1.25.0).

7. On a new branch, based off `k8s-XX/main`, do the following:

     1. Bump the minor version in [latest-k8s-version.txt](./projenrc/latest-k8s-version.txt)
     2. ([`README.md`](./README.md)): In the table of supported versions, add a new row and remove the oldest one.
     3. `yarn projen`
     4. `yarn rotate` # updates all version references in documenation
     5. `yarn build`
     6. Create a PR to the `k8s-XX/main` branch. (See [example](https://github.com/cdk8s-team/cdk8s-plus/pull/4260)).

8. Wait for the PR above to be merged and verify that automation builds/tags/releases the new version successfully.

9. Update cdk8s-plus default branch to the new branch in the [GitHub repo settings](https://github.com/cdk8s-team/cdk8s-plus/settings/branches).

10. Update any existing PRs to use k8s-XX/main as the base.

11. Rotate the backport labels on existing PRs. (for exmaple when rotating from k8s.29 to k8s.30, remove the `backport-to-k8s-27/main` label and add the `backport-to-k8s-29/main` label.

12. Delete the `backport-to-k8s-(XX - 3)/main` label. (for exmaple when rotating from k8s.29 to k8s.30, delete the `backport-to-k8s-27/main` label

## :three: Update Website

In the ([cdk8s repo](https://github.com/cdk8s-team/cdk8s)):

11. Create a new branch off of cdk8s/master and:

    1. Bump the minor version in [latest-k8s-version.txt](https://github.com/cdk8s-team/cdk8s/blob/master/src/latest-k8s-version.txt)
    2. `yarn projen`
    3. `yarn rotate-cdk8s-plus`

12.  Create a PR for the new branch, review then merge into cdk8s/master branch. (See [example](https://github.com/cdk8s-team/cdk8s/pull/1988))

## :four: Update Ops

In the ([cdk-ops](https://github.com/cdklabs/cdk-ops)):

13. Create a new branch and:

    1. Bump the minor version in [latest-cdk8s-plus-version.txt](https://github.com/cdklabs/cdk-ops/blob/master/latest-cdk8s-plus-version.txt)
    2. `yarn projen`

14. Create a PR and send for approval. (See [example](https://github.com/cdklabs/cdk-ops/pull/3323))
