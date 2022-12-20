# StatusCheck

StatusCheck is the service used for checking the status of specified website:

+ vika.cn
+ bbs.vika.cn
+ apitable.com
+ integration.vika.ltd

## Features

+ url status check
+ scan broken links with a given url

## Usage

### status-check

1. Create stepci workflow: `.github/stepci/url-status-check.yml`

```yaml
version: "1.1"
name: Status Check
env:
  host: example.com
tests:
  example.com:
    steps:
      - name: GET request
        http:
          url: https://example.com
          method: GET
          check:
            status: /^20/

```
You can add a step for checking new sites .

2. Run the workflow by github every minute

3. Get report from github action `Status check`
```azure
 PASS  example.com

Tests: 0 failed, 1 passed, 1 total
Steps: 0 failed, 0 skipped, 1 passed, 1 total
Time:  3.832s, estimated 4s

Workflow passed after 3.832s
Give us your feedback on https://step.ci/feedback
```

### Scan broken links

The example below will be executed every monday at 14:00pm, and it will scan all broken links in these two urls (`https://example1.com` and `https://example2.com`).

if some broken links are detected, it will create a bug issue and assign it to some team members.

```yaml
name: 'Broken Links Check'

on:
  schedule:
    - cron: 0 6 * * 1 # every monday at 14:00pm UTC+8:00
  workflow_dispatch:

jobs:
  linkinator:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Scan links
        id: scan-links
        uses: JustinBeckwith/linkinator-action@v1
        with:
          paths: "https://example1.com, https://example2.com"
          config: .github/linkinator.config.json

      - name: Create new issue as bug report
        if: ${{ failure() }}
        uses: JasonEtco/create-an-issue@v2
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          SUMMARY_LINK: https://github.com/${{ github.repository }}/actions/runs/${{ github.run_id }}
        with:
          filename: .github/broken-links-check-issue-template.md
```

<br/>

#### How to custom your workflow

1. **Create workflow yaml file**

> [broken-link.yaml](./.github/workflows/broken-link.yaml) is a real workflow example for you.
change the value in `jobs.steps[1].with.paths` to which url you want to monitor. If more than one url that you want to monitor, please add a comma separated each url.

<br/>

2. **modify the issue template**

> when a broken link is detected, workflow will create a new bug issue. If you want to modify the issue template, please add a markdown file under `./github/` folder ([example file](./.github/broken-links-check-issue-template.md)):

```markdown
---
title: Some broken links detected
assignees: kwp-lab, garyli27, Javen-Woo
labels: bug
---

## 🔍 Broken Links check

there are several links are broken.

👉 Please see the [action summary]({{ env.SUMMARY_LINK }})
```

<br/>

3. **How to ignore the special links we don't want to marked as broken links?**

> please modify the file `./github/linkinator.config.json`, append urls to the value of `skip` property :

```json
{
    "timeout": 5000,
    "verbosity": "error",
    "retry": 3,
    "skip": [
        "https://vika.cn/xmlrpc.php",
        "https://vika.cn/comments/feed/",
        "https://vika.cn/partners/",
        "https://apitable.com/comments/feed/",
        "https://apitable.com/void\\(0\\);",
        "https://beian.miit.gov.cn",
        "https://cn.idgcapital.com",
        "http://www.tiantu.com.cn/cn/index.aspx",
        "https://caicloud.io/",
        "https://ruanzu.com/"
    ]
}
```

<br/>

4. **Can I run the github workflow manually?**

> Yes, go to the [action page](https://github.com/vikadata/StatusCheck/actions/workflows/broken-link.yaml) and click the button "Run workflow"


## Reference

[linkinator-action](https://github.com/JustinBeckwith/linkinator-action)

[stepci](https://github.com/stepci/stepci)
