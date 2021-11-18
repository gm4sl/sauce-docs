---
id: apifctl-cicd-integration
title: CI/CD Platform Integration with apifctl
sidebar_label: CI/CD Integration with apifctl
description: "Using Sauce Labs API Testing or CLI, you can seamlessly integrate continuous API testing into your CI/CD pipeline."
---

import useBaseUrl from '@docusaurus/useBaseUrl';
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

Execute API tests and interact with Sauce Labs API Testing (either locally or in a pipeline) using our API Testing CLI tool, `apifctl`.

## Usage

`$ docker run quay.io/saucelabs/apifctl [COMMAND] [OPTIONS]`

## What You'll Need
* A Sauce Labs account ([Log in](https://accounts.saucelabs.com/am/XUI/#login/) or sign up for a [free trial license](https://saucelabs.com/sign-up)).
* Your Sauce Labs [Username](https://app.saucelabs.com/user-settings) and [Access Key](https://app.saucelabs.com/user-settings).
* An existing API Testing Project. For details on how to create one, see [API Testing Quickstart](/api-testing/quickstart/).


### Adding Webhooks
To utilize most `apifctl` functionalities, you'll need to add a webhook to your API Testing Project. To generate a webhook:

1. Log in to Sauce Labs, then click **API TESTING** > **Get Started**.<br/><img src={useBaseUrl('img/api-fortress/2021/09/landingPage.png')} alt="API Testing landing page" width="500" />
1. Navigate to your Project and select the **WebHooks** tab.<br/><img src={useBaseUrl('img/api-fortress/2021/04/webHooksSection.png')} alt="webhook screenshot"/>
1. Select **Create Hook**.<br/><img src={useBaseUrl('img/api-fortress/2021/04/createHook.png')} alt="Create New WebHook"/>
1. Enter a name for your webhook (description is optional), then click **Save**.<br/><img src={useBaseUrl('img/api-fortress/2021/04/sampleHook.png')} alt="sample webhook details" width="500" />
1. The generated **Hook URL** will then appear. Your Sauce Labs username, Sauce API Testing endpoint, and `{hook_id}` will populate automatically. For security reasons, you'll need to add your own access key.
  ```bash
  https://{SAUCE_USERNAME}:{SAUCE_ACCESS_KEY}@{SAUCE_API_ENDPOINT}/{hook_id}
  ```
1. Copy the URL to your clipboard and then you can use it either locally or as part of CI build.<br/>
   <img src={useBaseUrl('img/api-fortress/2021/04/hookURL.png')} alt="sample Hook URL"/>

You can then reuse this Webhook for future tests within that Project by returning to the **Webhooks** section and copying it. Webhooks are Project-specific.

## `apifctl` Commands

### `run`

Runs a single test, using `hookId` and `testId`. The `testId` is typically located in the URL when viewing the test through the API Testing UI (e.g., `/api-testing/project/<projectId>/test/<testId>/`).

```bash
docker run quay.io/saucelabs/apifctl run
```

Available Options:
* [<code>-H &#60;webhook&#62;</code>](#-h-webhook) <small>| REQUIRED | STRING |</small>
* [<code>-i &#60;test ID&#62;</code>](#-i-test-id) <small>| REQUIRED | STRING |</small>
* [<code>-E &#60;environment variable(s)&#62;</code>](#-e-environment-variables) <small>| OPTIONAL | STRING |</small>  
* [<code>-S &#60;execute synchronously&#62;</code>](#-s) <small>| OPTIONAL |</small>
* [<code>-T &#60;tunnel ID&#62;</code>](#-t-tunnel-id) <small>| OPTIONAL | STRING |</small>  
* [<code>-f &#60;data format&#62;</code>](#-f-data-format) <small>| OPTIONAL | STRING |</small>  

```bash title="Full Example"
docker run quay.io/saucelabs/apifctl run \
-H https://<username>:<accessKey>@api.us-west-1.saucelabs.com/api-testing/rest/v4/<hookId> \
-i 123a1a123456a12345aa1aaa
```

---
### `run-all`

Runs all the complete tests in the Project referenced by the hook.

```bash
docker run quay.io/saucelabs/apifctl run-all
```

Available Options:
* [<code>-H &#60;webhook&#62;</code>](#-h-webhook) <small>| REQUIRED | STRING |</small>
* [<code>-E &#60;environment variable(s)&#62;</code>](#-e-environment-variables) <small>| OPTIONAL | STRING |</small>  
* [<code>-S &#60;execute synchronously&#62;</code>](#-s) <small>| OPTIONAL |</small>
* [<code>-T &#60;tunnel ID&#62;</code>](#-t-tunnel-id) <small>| OPTIONAL | STRING |</small>  
* [<code>-f &#60;data format&#62;</code>](#-f-data-format) <small>| OPTIONAL | STRING |</small>   

```bash title="Full Example"
docker run quay.io/saucelabs/apifctl run-all -H \
https://<username>:<accessKey>@api.us-west-1.saucelabs.com/api-testing/rest/v4/<hookId>
```

---
### `run-tag`

Runs all the tests marked with a specific tag.

```bash
docker run quay.io/saucelabs/apifctl run-tag
```

Available Options:
* [<code>-H &#60;webhook&#62;</code>](#-h-webhook) <small>| REQUIRED | STRING |</small>
* [<code>-tag &#60;tag(s)&#62;</code>](#-tag-tags) <small>| REQUIRED | STRING |</small>
* [<code>-E &#60;environment variable(s)&#62;</code>](#-e-environment-variables) <small>| OPTIONAL | STRING |</small>  
* [<code>-S &#60;execute synchronously&#62;</code>](#-s) <small>| OPTIONAL |</small>
* [<code>-T &#60;tunnel ID&#62;</code>](#-t-tunnel-id) <small>| OPTIONAL | STRING |</small>  
* [<code>-f &#60;data format&#62;</code>](#-f-data-format) <small>| OPTIONAL | STRING |</small>   

```bash title="Full Example"
docker run quay.io/saucelabs/apifctl run-tag \
-H https://<username>:<accessKey>@api.us-west-1.saucelabs.com/api-testing/rest/v4/<hookId> \
-tag production
```

---
### `exec`

Sends a test residing in the local file system (directory with `unit.xml` and `input.xml`) to the cloud for execution.

```bash
docker run quay.io/saucelabs/apifctl exec
```

Available Options:
* [<code>-H &#60;webhook&#62;</code>](#-h-webhook) <small>| REQUIRED | STRING |</small>
* [<code>-p &#60;local path to test files&#62;</code>](#-p-local-path-to-file) <small>| REQUIRED | STRING |</small>
* [<code>-E &#60;environment variable(s)&#62;</code>](#-e-environment-variables) <small>| OPTIONAL | STRING |</small>  
* [<code>-S &#60;execute synchronously&#62;</code>](#-s) <small>| OPTIONAL |</small>
* [<code>-T &#60;tunnel ID&#62;</code>](#-t-tunnel-id) <small>| OPTIONAL | STRING |</small>  
* [<code>-t &#60;tags&#62;</code>](#-t-tags) <small>| OPTIONAL | STRING |</small>  
* [<code>-n &#60;name of test&#62;</code>](#-n-name-of-test) <small>| OPTIONAL | STRING |</small>  
* [<code>-tag &#60;tag(s)&#62;</code>](#-tag-tags) <small>| OPTIONAL | STRING |</small>


```bash title="Full Example"
docker run -v $(pwd)/tests:/tests quay.io/saucelabs/apifctl exec \
-H https://<username>:<accessKey>@api.us-west-1.saucelabs.com/api-testing/rest/v4/<hookId> \
-p /tests/test_abc \
-n local_test \
-t product,production
```

---
### `upload`

Uploads a local test residing in your local file system (directory with `unit.xml` and `input.xml`) to the cloud for storage.

```bash
docker run quay.io/saucelabs/apifctl upload
```

Available Options:
* [<code>-H &#60;webhook&#62;</code>](#-h-webhook) <small>| REQUIRED | STRING |</small>
* [<code>-p &#60;local path to test files&#62;</code>](#-p-local-path-to-file) <small>| REQUIRED | STRING |</small>
* [<code>-n &#60;name of test&#62;</code>](#-n-name-of-test) <small>| OPTIONAL | STRING |</small>  
* [<code>-t &#60;tag(s)&#62;</code>](#-t-tags) <small>| OPTIONAL | STRING |</small>
* [<code>-d &#60;test description&#62;</code>](#-d-test-description) <small>| OPTIONAL | STRING |</small>

```bash title="Full Example"
docker run -v $(pwd)/tests:/tests quay.io/saucelabs/apifctl upload \
-H https://<username>:<accessKey>@api.us-west-1.saucelabs.com/api-testing/rest/v4/<hookId> \
-p /tests/test_abc
```

---
### `vault-get`

Displays the Project vault from the cloud.

```bash
docker run quay.io/saucelabs/apifctl vault-get
```

Available Options:
* [<code>-H &#60;webhook&#62;</code>](#-h-webhook) <small>| REQUIRED | STRING |</small>

---
### `vault-update`

It updates the Project vault on the cloud.

```bash
docker run quay.io/saucelabs/apifctl vault-update
```

Available Options:
* [<code>-H &#60;webhook&#62;</code>](#-h-webhook) <small>| REQUIRED | STRING |</small>
* [<code>-p &#60;local path to file&#62;</code>](#-p-local-path-to-file) <small>| OPTIONAL | STRING |</small>
* [<code>-v &#60;variables&#62;</code>](#-v-variables) <small>| OPTIONAL | STRING |</small>

```bash title="Full Example"
docker run quay.io/saucelabs/apifctl vault-update \
-p /tests/vault/vault.json \
-v key=production,id=123
```

---
### `events`

Displays all events of a Project.

```bash
docker run quay.io/saucelabs/apifctl events  
```

Available Options:
* [<code>-H &#60;webhook&#62;</code>](#-h-webhook) <small>| REQUIRED | STRING |</small>
* [<code>-f &#60;from time&#62;</code>](#-f-from-time) <small>| OPTIONAL | STRING |</small>
* [<code>-t &#60;to time&#62;</code>](#-t-to-time) <small>| OPTIONAL | STRING |</small>
* [<code>-l &#60;limit&#62;</code>](#-l-limit) <small>| OPTIONAL | INTEGER |</small>
* [<code>-o &#60;offset&#62;</code>](#-o-offset) <small>| OPTIONAL | INTEGER |</small>

---
### `event`

Displays a specific event using its event ID.

```bash
docker run quay.io/saucelabs/apifctl event
```

Available Options:
* [<code>-H &#60;webhook&#62;</code>](#-h-webhook) <small>| REQUIRED | STRING |</small>
* [<code>-i &#60;event ID&#62;</code>](#-i-event-id) <small>| REQUIRED | STRING |</small>

```bash title=Full Example"
docker run quay.io/saucelabs/apifctl event \
-H https://john.smith:{access_key}@api.us-west-1.saucelabs.com/api-testing/rest/v4/36acf9c1-d5ad-4273-a233-a85470e1f502
-i 123456789abc1a1abcdef123 \
```

---
### `metrics`

Displays metrics of a Project.

```bash
docker run quay.io/saucelabs/apifctl metrics
```

Available Options:
* [<code>-H &#60;webhook&#62;</code>](#-h-webhook) <small>| REQUIRED | STRING |</small>
* [<code>-f &#60;from time&#62;</code>](#-f-from-time) <small>| OPTIONAL | STRING |</small>
* [<code>-t &#60;to time&#62;</code>](#-t-to-time) <small>| OPTIONAL | STRING |</small>
* [<code>-l &#60;limit&#62;</code>](#-l-limit) <small>| OPTIONAL | INTEGER |</small>
* [<code>-o &#60;offset&#62;</code>](#-o-offset) <small>| OPTIONAL | INTEGER |</small>

---

<br/>


## `apifctl` Options

### `-H <webhook>`
<p><small>| STRING |</small></p>

Specifies the webhook URL. This option is required for all commands.

```bash
-H https://{SAUCE_USERNAME}:{SAUCE_ACCESS_KEY}@{SAUCE_REST_API_URL}/<hookID>
```

---
### `-E <environment variable(s)>`
<p><small>| STRING |</small></p>

Sets environment variables with your command. Enter your variables as a comma-separated list. For use with **[`run`](#run)**, **[`run-all`](#run-all)**, **[`run-tag`](#run-tag)**, **[`exec`](#exec)** commands.

```bash
-E domain=staging.example.com,id=123
```

---
### `-S`

Syncs execution so that `apifctl` will wait until all results are available, and then prints them. For use with **[`run`](#run)**, **[`run-all`](#run-all)**, **[`run-tag`](#run-tag)**, and **[`exec`](#exec)** commands.

---
### `-f <data format>`
<p><small>| STRING |</small></p>

Sets the data format to one of two possible values: `json` or `junit`. Default is `json`. For use with **[`run`](#run)**, **[`run-all`](#run-all)**, **[`run-tag`](#run-tag)**, **[`exec`](#exec)** commands.

```bash
-f junit
```

---
### `-T <tunnel ID>`
<p><small>| STRING |</small></p>

Specifies your tunnel ID for running tests using Sauce Connect Proxy. For use with **[`run`](#run)**, **[`run-all`](#run-all)**, **[`run-tag`](#run-tag)**, **[`exec`](#exec)** commands.

<table id="table-api">  
  <tbody>
  <tr>
  <td colSpan='2'><strong>Parameters for <code>-T</code></strong></td>
  </tr>
    <tr>
     <td><code>&#60;username&#62;:&#60;tunnelName&#62;</code></td>
     <td><p><small>| OPTIONAL | STRING |</small></p><p>In case of shared tunnels, tunnel names may have duplicates, therefore you can provide an explicit owner to exactly identify which tunnel (e.g., <code>kim:sauceconnect</code>).</p></td>
    </tr>
  </tbody>
  <tbody>
    <tr>
     <td><code>&#60;tunnelName&#62;</code></td>
     <td><p><small>| OPTIONAL | STRING |</small></p><p>Also known as "tunnel-identifier" in the Sauce Connect world. That's a named tunnel. This is the suggested way to use the tunnels (e.g., <code>-T sauceconnect</code>).</p></td>
    </tr>
  </tbody>
  <tbody>
    <tr>
     <td><code>&#60;anonymous&#62;</code></td>
     <td><p><small>| OPTIONAL | STRING |</small></p><p>If you start an unnamed tunnel, you can use anonymous (using the same user's credentials) to use the first unnamed tunnel available. This is not recommended and SC support of this mode for the future is uncertain (e.g., <code>-T anonymous</code>.</p></td>
    </tr>
  </tbody>
  <tbody>
    <tr>
     <td><code>&#60;tunnelId&#62;</code></td>
     <td><p><small>| OPTIONAL | STRING |</small></p><p>The unique id of the tunnel. Generally used with unnamed tunnels. For the same reason as the previous mode, this is not recommended (e.g., <code>-T 1a12345678900a12345678aaa123456a</code>).</p></td>
    </tr>
  </tbody>
</table>

---
### `-p <local path to file>`
<p><small>| STRING |</small></p>

For the **[`exec`](#exec)** and **[`upload`](#upload)** commands: This is the local path to the folder containing both your unit.xml and input.xml test files. In the below example, `product_update` is the folder containing unit.xml and input.xml.

```bash
-p /tests/product_update
```

For the **[`vault-update`](#vault-update)** command: This is the local path to the folder containing your Vault file.

```bash
-p /tests/vault/vault.json
```

---
### `-f <from time>`
<p><small>| STRING |</small></p>

Identifies the start date of the events you want to see. For use with **[`metrics`](#metrics)** and **[`events`](#events)** commands.

```bash
-f 2021-10-21T14:00
```

---
### `-t <to time>`
<p><small>| STRING |</small></p>

Identifies the end date of the events you want to see. For use with **[`metrics`](#metrics)** and **[`events`](#events)** commands.

```bash
-t 2021-10-31T15:00
```

---
### `-t <tag(s)>`
<p><small>| STRING |</small></p>

"Adds a set of tags to the test. Format as a comma-separated list of tags you want to assign to the test. For use with **[`exec`](#exec)** and **[`upload`](#upload)** commands.

```bash
-t product,production
```

---
### `-tag <tag(s)>`
<p><small>| STRING |</small></p>

The **[`run-tag`](#run-tag)** command will run all the tests in your Project marked with that tag. Format as a comma-separated list of tags you want to assign to the test. For use with **[`run-tag`](#run-tag)** command only.

In this example, it will run all the tests in Project labeled with the `production` tag.
```bash
-tag production
```

---
### `-n <name of test>`
<p><small>| STRING |</small></p>

Identifies the name of the test you want to assign to. Default is the name of the test directory. For use with **[`exec`](#exec)** and **[`upload`](#upload)** commands.

```bash
-n local_test
```

---
### `-i <test ID>`
<p><small>| STRING |</small></p>

Identifies the ID of a complete test. For use with **[`run`](#run)** command only.

```bash
-i 123a1a123456a12345aa1aaa
```

---
### `-d <test description>`
<p><small>| STRING |</small></p>

The description you want to add to your test. For use with **[`upload`](#upload)** command only.

---
### `-v <variables>`
<p><small>| STRING |</small></p>

It’s a String in the format `key=value` with the list of the variable separated by a comma. For use with **[`vault-update`](#vault-update)** command only.

---
### `-l <limit>`
<p><small>| INTEGER |</small></p>

Identifies the maximum number of metrics and events to be shown in the list. Default value is `100`. For use with **[`events`](#events)** and **[`metrics`](#metrics)** commands.

---
### `-o <offset>`
<p><small>| INTEGER |</small></p>

Specifies the number of events and metrics to be skipped from the beginning of the list. Default value is `0`. For use with **[`events`](#events)** and **[`metrics`](#metrics)** commands.

---
### `-i <event ID>`
<p><small>| STRING |</small></p>

Identifies the ID of the event you want to see. For use with **[`event`](#event)** command only.

---