# Enterprise Release Notes

### 2308.22.7.patch6

- Fixes a UI issue on the branch when running a task in a DTE context

### 2308.22.7.patch5

- Fixes a UI issue when navigating to branches containg slashes

### 2308.22.7.patch4

- Updates the frontend image to remove some vulnerability issues

### 2308.22.7.patch3

- Fixes a compatibility issue with the latest `nx-cloud` release

### 2308.22.7.patch2

- Fix: github member invites

### 2308.22.7.patch1

- Feature: self-signed certificate support for aggregator
  - This is needed if you are using self-signed certificate for your external Mongo instance
  - See [here](https://github.com/nrwl/nx-cloud-helm/blob/main/PROXY-GUIDE.md#supporting-self-signed-ssl-certificates) for usage details.
- Fix: aggregator issue when creating text Mongo indexes

### 2308.22.7

In our last big release, we announced a completely new UI, rebuilt from the ground up in React. In this release, the frontend team
has continued that effort and wrapped the React app with the [Remix](https://remix.run/) framework. This is the same technology that powers our public https://cloud.nx.app/
product. It's faster, it handles resource caching better, and should allow the frontend team to ship features quicker than ever before.

##### Helm package compatibility

When upgrading to this version and anything above it, you will need to use Helm version 0.11.1:

| Chart Version | Compatible Images                  |
| ------------- | ---------------------------------- |
| <= `0.10.11`  | `2306.01.2.patch4` **and earlier** |
| >= `0.11.0`   | `2308.22.7` **and later**          |

##### VCS proxy support

- For the GitHub/Bitbucket/Gitlab integrations to work, NxCloud needs to make HTTP calls to GitHub/GitLab to post comments
- If are behind a proxy however, these requests might fail
- If you are using our [Helm chart](https://github.com/nrwl/nx-cloud-helm/), you can now configure this option to unblock the vcs integration and allow it to work with your proxy:
  ```yaml
  vcsHttpsProxy: '<your-proxy-address>'
  ```

##### Misc updates

- UI enhancements of the run details screen
- UI enhancements of the task details screen
- fixes and better error handling for the DTE screen
- failed runs are now sorted at the top
- web app performance improvements for large workspaces
- more structured NxAPI pod logs (allows for better debugging)

##### Bug fixes

- Fixed an issue with applying licenses on orgs owned by non-installation admin accounts

##### Breaking changes - MongoDB migration

In the last big release we announced [the deprecation of Mongo 4.2](/nx-cloud/reference/release-notes#breaking-changes)
With this release, we have now stopped supporting Mongo 4.2 completely. Please upgrade Mongo to version 6 before installing this new image. You will find instructions [here](/nx-cloud/reference/release-notes#breaking-changes).

### 2306.01.2.patch4

- Fixes an issue with new licenses expiring sooner than original end date

### 2306.01.2.patch3

- Fixes an issue with multiple admin organizations being created on new installations
- Fixes an issue where Enterprise licenses could not be applied on some new orgs

### 2306.01.2.patch2

- Fixes an issue with the `single-image` container where the aggregation would block the API from starting up

### 2306.01.2.patch1

- Fixes an issue where admin users were not being created on new installations.

### 2306.01.2

This is one of our biggest NxCloud On-Prem releases. It also marks a change in our release process which will be explained at the end.

##### Brand new UI

A few months ago we announced a complete re-design of the NxCloud UI! It's faster, easier to use and pleasant to look at! We're now bringing this to On-Prem users as well:

You can read more about it in our [announcement blog post](https://blog.nrwl.io/nx-cloud-3-0-faster-more-efficient-modernized-36ac5ae33b86).

##### Pricing updates

While before we provided you with a separate coupon for each workspace, we have now changed to "organization-wide licenses": you receive a single coupon for your whole organization, that gives you unlimited access for the agreed number of workspaces. You are then free to delete, create and re-shuffle your workspaces as often as you want without requiring new coupons for us (as long as you stay within your limit of workspaces).

You will see some updates in the UI to reflect this, however, **you don't need to do anything once you update your images!** We'll automatically migrate you to this, based on your current number of enabled workspaces!

##### Proxy updates

One of the features of NxCloud is its integrations with your repository hosting solution. When you open up a Pull Request, you can configure NxCloud to post a comment to it once your CI has finished running, with a summary of all the tasks that succeeded and failed on that code change, and a link to your branch on NxCloud so you can further analyse your run. Your developers save time, and allows them to skip digging through long CI logs.

Before, if you had a self-hosted instance of GitHub, Gitlab or Bitbucket, calls from NxCloud to your code-hosting provider would fail, because they'd be using a self-signed certificate, which NxCloud wouldn't recognise.

[We now support self-signed SVN certificates, via a simple k8s configMap.](https://github.com/nrwl/nx-cloud-helm/blob/main/PROXY-GUIDE.md#supporting-self-signed-ssl-certificates)

[We've also made updates to the runner, to support any internal proxies you might have within your intranet.](https://github.com/nrwl/nx-cloud-helm/blob/main/PROXY-GUIDE.md#supporting-self-signed-ssl-certificates)

##### DTE performance

We completely re-wrote our Task Distribution engine, which should result in much fewer errors due to agent timeouts, increased performance and more deterministic task distribution.

We've also added a new internal task queueing system, which should further improve the performance of DTE. While this is an implementation detail which will be automatically enabled in future releases, you can test it out today by setting [`enableMessageQueue: true`](https://github.com/nrwl/nx-cloud-helm/blob/main/charts/nx-cloud/values.yaml#L18) in your Helm config.

You can read more about the recent DTE improvements in our [NxCloud 3.0 blog post](https://blog.nrwl.io/nx-cloud-3-0-faster-more-efficient-modernized-36ac5ae33b86).

##### Misc updates

- We have fixed issues related to OpenShift deployments. [Special thanks to minijus](https://github.com/nrwl/nx-cloud-helm/pull/32) for his work on the Helm charts and helping us test the changes.

##### Breaking changes

NxCloud uses MongoDB internally as its data store. While we've always used Mongo 4.2, in the latest release we started targetting Mongo 6.0. It's a much lighter process, with improved performance, and quicker reads and writes.

While you can still upgrade to this new image even if you are on Mongo 4.2 (nothing will break), **we strongly recommend you upgrade your Database to Mongo 6.0 to make sure nothing breaks in the future.** [We wrote a full guide on how you can approach the upgrade here](https://github.com/nrwl/nx-cloud-helm/blob/main/MONGO-OPERATOR-GUIDE.md#upgrading-to-mongo-6).
If you need assistance, please get in touch at [cloud-support@nrwl.io](mailto:cloud-support@nrwl.io).

###### Migration from Community Edition to Enterprise

On May 16th, 2023 we announced our plans to sunset the Community Edition of NxCloud On-Prem to align with our new pricing plans. If you are on the Community Edition, please follow these steps to migrate:

1. Use this image: `2306.01.2.patch3`
2. Switch to private Enterprise by setting `NX_CLOUD_MODE=private-enterprise` (or `mode: 'private-enterprise'` if using Helm).
3. Reach out to us at [cloud-support@nrwl.io](mailto:cloud-support@nrwl.io). You will get a FREE, unlimited-use coupon for the next 3 months so you can trial Nx Enterprise.

##### New release process

With this update, we are also changing our release process:

1. We'll start adding release notes with every new version published
2. We switch to using [calver](https://calver.org/) versioning for our images
3. We stopped publishing the `latest` tag.
4. We will be emailing Enterprise admins with every new release. If you do not get these emails, please send us an email at [cloud-support@nrwl.io](mailto:cloud-support@nrwl.io) to get added

Any questions at all or to report issues with the new release [please get in touch!](mailto:cloud-support@nrwl.io)

### 13-02-2023T23-45-24

- Feat: Targettable agents for DTE. You can now ask specific agents to pick up specific tasks (via `--targets
- Fix: DTE fixes for 404 not found artefacts errors
- Fix: issue when using GitHub integration with self-hosted GitHub instances

### 26-01-2023T21-22-48

- Misc: Fixes to the Gitlab integration

### 05-01-2023T17-53-45

- Misc: This release contains small bug fixes and UI improvements.

### 14-12-2022T19-43-44

- Feat: IAM Role Auth. We have now deprecated "aws_access_key_id" and "aws_access_key_secret" in favor of service accounts and IAM roles for accessing AWS resources. See the [new guide here](https://github.com/nrwl/nx-cloud-helm/blob/main/aws-guide/AWS-GUIDE.md) for details.

### 13-10-2022T16-45-30

- Misc: This release mostly contains improvements that apply to the Public SASS version of NxCloud. No significant changes for the On-Prem version.

### 13-10-2022T16-45-30

- Feat: Private Cloud now runs completely as Kubernetes cluster. See the [Helm example repo](https://github.com/nrwl/nx-cloud-helm) for more details

### 05-08-2022T15-42-20

- Fix: issue with retrieving hashes during reads
- Feat: added route to display container version at `/version`
- Misc: forward api errors to stderr so k8s clusters can process them better

### 02-08-2022T16-11-36

- Note: The version naming scheme for the containers was changed to better track date/time of releases and to support embedding of the version inside the web UI
- Feat: view the container version under the `/errors` route
- Feat: BitBucket login (note: does not support self-hosted instances of BitBucket Server)
- Feat: New system-ui font scheme
- Fix: branch screen sorting performance improvements

### 2.4.11

- Fixes an intermittent container start-up issue when running a self-contained Mongo instance
- Fixes an issue with the self-hosted file-server where it would fail to create the initial directories

### 2.4.10

- Fix an issue with the admin password not being set correctly

### 2.4.9

- Align all NxCloud images to this version. No new fixes or features included.

### 2.4.8

{% callout type="caution" title="IMPORTANT" %}
The default container mode has changed from `COMMUNITY` to `ENTERPRISE`. If you are running a Community version of the container, you will need to make sure the `NX_CLOUD_MODE=private-community` is explicitly set (otherwise your container will fail to start-up).
{% /callout %}

- Fix: Web app performance improvements
- Fix: issue with GitHub logged in admins not being able to download logs
- Fix: issue with billing page when multiple access tokens were attached to the same org
- Fix: multiple Mongo DBs used to be created if a default DB was not provided in the connection string. Now it always defaults to the provided `NX_CLOUD_MONGO_DB_NAME`

### 2.4.7

- Misc: performance improvements to DB indexes
- Misc: improvements to hash differ to use regex
- Misc: export more collections for debug purposes (workspaces and organizations)

### 2.4.6

- Fix: issue with navigating to organizations/workspaces in the web app

### 2.4.5

- Feat: filters to branch and run list pages
- Fix: improved `MD5` cache artifact archiving
- Misc: various UI and UX improvements to the NxCloud dashboards

### 2.4.4

- Fix: Missing artefact retrieval error when using read-tokens
- Fix: Performance improvements to the branch page and run groups sorting
- Fix: better handling of artefact `.tar` archiving

### 2.4.3

- Feat: Billing page messaging improvements
- Fix: runs sorting on branch page

### 2.4.2

- Feat: DTE post-run report
- Feat: Hash Detail tool flow improvements

### 2.4.1

- Feat: Admins can now easily export debug info for error investigation
- Fix: branch screen run group sorting

### 2.4.0

- Feat: [GitLab Auth Support](https://nx.app/docs/private-cloud-gitlab-auth)/private-cloud-gitlab-auth
- Feat: Hash diffing tool improvements
- Feat: show message on branch page if workspace is unclaimed
- Fix: Agent out of memory warning
- Feat: cache inner runs
- Fix: include correct GitHub workflows path
- Fix: default to most recent run group on branch page
- Fix: handle DTEs with no tasks
- Fix: await process checkout sessions

### 2.3.1

- Feat: Increase file-server default cached artifact limit. If you are not using an external file storage (such as S3), then the cached assets will now be kept by default from 2 weeks to 4 weeks, increasing the chance of cache hits.
- Feat: "Download cache usage" data from the "Time saved" workspace page

### 2.3.0

- Feat: GitHub Integration - no token is now necessary in "`nx.json`" for the GitHub integration to work (you still need to provide as an env var for caching to work). To connect your workspace to GitHub without an access token in "`nx.json`" just pass in the "`NX_CLOUD_INTEGRATION_DEFAULT_WORKSPACE_ID=<your-workspace-id>`" env var
- Misc: better error handling (report less false positives)
- Fix: Scheduled tasks locking

### 2.2.16

- Misc: DB performance improvements (old records clean-up aggregator, indexes etc.)

### 2.2.15

- Feat: Add options to control database load
- Fix: Better exception handling in the API

### 2.2.14

- Feat: Optimize event processing to increase the throughput of workspaces with a very high number of agents.
- Fix: Gracefully recover when stats aggregation fails

### 2.2.13

- Feat: Hash diffing tool enhancements

### 2.2.12

- Feat: DTE visualisation improvements for larger workspaces
- Fix: billing page not displaying subscriptions for Private Community

### 2.2.11

- Feat: Better error handling for scheduled tasks
- Fix: branch screen not loading

### 2.2.10

- Feat: Various UI improvements to the NxCloud screens
- Feat: Hash detail diff tool
- Feat: GitHub app comment revamp
- Feat: DTE visualisation

### 2.2.9

- Fix: DTE bug fixes caused by incorrectly batched tasks

### 2.2.8

- Fix: various DTE bug fixes
- Feat: Add `NX_CLOUD_DISTRIBUTED_EXECUTION_AGENT_COUNT` env var for more explicitly optimising DTEs
- Feat: Send GitHub workspace membership invites by email
- Fix: improve container start-up time
- Feat: If Mongo connection fails during container start-up it keeps retrying up to a max number of times (configurable via `MONGO_MAX_RETRIES`)
- Feat: expose "/ping" endpoint (can be useful for K8s readinessProbe)
  - `curl --fail http://localhost:8081/nx-cloud/ping --header "authorization: your-nx-cloud-access-token"`
- Feat: billing estimator (on billing page)
- Fix: ignore ending slash on `NX_CLOUD_APP_URL` (in case it's added by mistake)

### 2.2.7

- Feat: `VERBOSE=1` env variable option to output extra information during container initialisation
- Feat: `MONGO_REPAIR=1` env variable option to trigger a [Mongo Repair](https://docs.mongodb.com/manual/tutorial/recover-data-following-unexpected-shutdown/) if the container data gets corrupted

### 2.2.3

- Fix: Reset the memory limits to best work on an instance with 8GB of RAM.
- Fix: Set the default `NX_CLOUD_MODE` to "community".

### 2.2

- [Nx Cloud 2.2](https://blog.nrwl.io/%EF%B8%8F-nx-cloud-2-2-%EF%B8%8F-b7656ed5ce7c)

### 2.0

- [Overview of Nx Cloud 2.0](https://blog.nrwl.io/introducing-nx-cloud-2-0-f1e5c2002a65)
