# Releasing

## Webapp

### Planning

1. Create an [organisation wide project](https://github.com/orgs/medic/projects?query=is%3Aopen+sort%3Aname-asc). We use [semver](http://semver.org) so if there are breaking changes increment the major, otherwise if there are new features increment the minor, otherwise increment the service pack. Breaking changes in our case relate to updated software requirements (egs: CouchDB, node, minimum browser versions), broken backwards compatibility in an api, or a major visual update that requires user retraining.
2. Add all the issues to be worked on to the project. Ideally each minor release will have one or two features, a handful of improvements, and plenty of bugs.

### Development

1. When development is ready to begin one of the engineers should be nominated as a Release Manager. They will be resonsible for making sure the following tasks are completed though not necessarily completing them.
2. Set the version number in package.json and package-lock.json and submit a PR. The easiest way to do this is to use `npm --no-git-tag-version version <major|minor|patch>`.
3. For major or minor releases raise a new issue called `Update dependencies for <version>` with a description that links to [the documentation](update-dependencies.md). This should be done early in the release cycle so find a volunteer to take this on and assign it to them.
4. Write an update in the weekly Product Team call agenda summarising development and acceptance testing progress and identifying any blockers. The release manager is to update this every week until the version is released.

### Releasing

Once all issues have passed acceptance testing and have been merged into `master` release testing can begin.

1. Create a new release branch from `master` named `<major>.<minor>.x` in medic. Post a message to #development using this template:
  ```
@core_devs I've just created the `<major>.<minor>.x` release branch. Please be aware that any further changes intended for this release will have to be merged to `master` then backported. Thanks!
  ```
2. Build a beta named `<major>.<minor>.<patch>-beta.1` by pushing a git tag and when CI completes successfully notify the QA team that it's ready for release testing.
3. [Import translations keys](translations.md#adding-new-keys) into POE and notify the #translations Slack channel translate new and updated values, for example:
  ```
@channel I've just updated the translations in POE. These keys have been added: "<added-list>", and these keys have been updated: "<updated-list>"
  ```
4. Create a new document in the [release-notes folder](https://github.com/medic/medic/tree/master/release-notes) in `master`. Ensure all issues are in the GH Project, that they're correct labelled, and have human readable descriptions. Use [this script](https://github.com/medic/medic/blob/master/scripts/changelog-generator) to export the issues into our changelog format. Manually document any known migration steps and known issues. Provide description, screenshots, videos, and anything else to help communicate particularly important changes.
5. Create a Google Doc in the [blog posts folder](https://drive.google.com/drive/u/0/folders/0B2PTUNZFwxEvMHRWNTBjY2ZHNHc) with the draft of a blog post promoting the release based on the release notes above. Once it's ready ask Alix to review it.
6. Until release testing passes, make sure regressions are fixed in `master`, cherry-pick them into the release branch, and release another beta.
7. [Export the translations](translations.md#exporting-changes-from-poeditor-to-github), delete empty translation files and commit to `master`. Cherry-pick the commit into the release branch. 
8. Create a release in GitHub from the release branch so it shows up under the [Releases tab](https://github.com/medic/medic/releases) with the naming convention `<major>.<minor>.<patch>`. This will create the git tag automatically. Link to the release notes in the description of the release.
9. Confirm the release build completes successfully and the new release is available on the [market](https://staging.dev.medicmobile.org/builds/releases). Make sure that the document has new entry with `id: medic:medic:<major>.<minor>.<patch>`
10. Add the release to the [Supported versions](../installation/supported-software.md#supported-versions) and update the EOL date and status of previous releases.
11. Announce the release in #products and #cht-contributors using this template:
```
@channel *We're excited to announce the release of {{version}}*

New features include {{key_features}}. We've also implemented loads of other improvements and fixed a heap of bugs.

Read the release notes for full details: {{url}}

Following our support policy, versions {{versions}} are no longer supported. Projects running these versions should start planning to upgrade in the near future. For more details read our software support documentation: https://github.com/medic/medic-docs/blob/master/installation/supported-software.md#supported-versions

To see what's scheduled for the next releases have a read of the product roadmap: https://github.com/orgs/medic/projects?query=is%3Aopen+sort%3Aname-asc
```
12. Announce the release on the [CHT forum](https://forum.communityhealthtoolkit.org/), under the Development category. You can use the previous message and omit `@channel`.
13. :beer:

## Android apps

All medic Android projects automatically build, sign, and publish builds via Travis. To create a new release:

1. Determine what version the build will be. This should be a valid semver (eg. `v1.1.1`). Increment the semver appropriately based on the latest release version:
    * [medic-android](https://github.com/medic/medic-android/releases)
    * [medic-gateway](https://github.com/medic/medic-gateway/releases)
    * [medic-collect](https://github.com/medic/medic-collect/releases)
    * [rdt-capture](https://github.com/medic/rdt-capture/releases)
1. Tag the commit in `master` which you'd like to release. Use `git tag v1.1.1` with the version from above and then run `git push --tags`.
1. Wait for the build to complete. You can monitor the build via travis-ci.org.
1. **To Release via the Google Play Store** - Login to the [Google Play Console](https://play.google.com/apps/publish/) and select the app flavor for release. Navigate to "Release Management > Alpha" and click "Release to Beta". Repeat this for each flavor.
1. **To Side-Load** (for Collect and Gateway) - Navigate to the GitHub Releases page (linked above) and download the relevant APKs for distribution.
