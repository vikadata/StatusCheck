# StatusCheck
StatusCheck is the service used to check the status of the website
Checking website
+ vika.cn
+ apitable.com
+ integration.vika.ltd 

### Usage

##### broken-link
1. Create example workflow
**.github/workflow/broken-link.yml**
```azure
name: Check Website Broken Links

on:
schedule:
    - cron: '0 * * * *'
workflow_dispatch: # run manually
jobs:
  check:
    name: Broken Link Check
    runs-on: ubuntu-latest
    steps:
      - name: Check for broken links
        id: link-report
        uses: celinekurpershoek/link-checker@v1.0.2
        with:
          # Required:
          url: "https://example.com"
          # optional:
          honorRobotExclusions: false
          ignorePatterns: "github,google"
          recursiveLinks: true # Check all URLs on all reachable pages (could take a while)
      - name: Get the result
        run: echo "${{steps.link-report.outputs.result}}"
```

2. Run the workflow by github per hour

3. Get report from github action `Check Website Broken Links`
```azure
=== BROKEN LINK CHECKER ===
Running broken link checker on URL:  https://example.com
Configuration: 
 Honor robot exclusions: false, 
 Exclude URLs that match: github,google, 
 Resursive URLs: true
 ✓ Checked 1 link(s), no broken links found! 
```

##### status-check
1. Create stepci workflow.yaml
   **.github/stepci/url-status-check.yml**
```azure
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

### Reference
[link-checker](https://github.com/celinekurpershoek/link-checker)

[stepci](https://github.com/stepci/stepci)