---
title: Sofy integration
description: How to integrate your workflows with Sofy using codemagic.yaml
weight: 11
---

**Sofy** is a testing automation platform that uses testing capabilities to enable tests without writing a single line of code. It is possible to publish binaries generated by **Codemagic** and schedule automation tests with **Sofy** via `codemagic.yaml`.

A sample project that shows how to configure Sofy integration is available in our [Sample projects repository](https://github.com/codemagic-ci-cd/codemagic-sample-projects/tree/main/integrations/sofy_integration_demo_project).


## Configuring Sofy access

You will need to sign up with [Sofy](https://sofy.ai/) so you can obtain an **API Key** which is required for deploying your apps via **Codemagic**. In addition, you will also need the **Sofy subscription key** and the **Schedule ID**, both obtainable from your Sofy account settings.

1. Open your Codemagic app settings, and go to the **Environment variables** tab.
2. Enter the desired **_Variable name_**, e.g. `SOFY_API_KEY`.
3. Copy and paste the API key string as **_Variable value_**.
4. Enter the variable group name, e.g. **_sofy_credentials_**. Click the button to create the group.
5. Make sure the **Secure** option is selected.
6. Click the **Add** button to add the variable.
7. Repeat the steps to also add `SOFY_SUBSCRIPTION_KEY` and `SOFY_SCHEDULE_ID` variables.

8. Add the variable group to your `codemagic.yaml` file
{{< highlight yaml "style=paraiso-dark">}}
  environment:
    groups:
      - sofy_credentials
{{< /highlight >}}


## Real Time and App Automation

The following examples show how to use **cURL** commands in your `codemagic.yaml` to deploy and test your app with Sofy.

#### Uploading apps

{{< highlight yaml "style=paraiso-dark">}}
  scripts:
    - name: Publish APK / AAB / IPA to Sofy
      script: | 
        curl --location --request POST 'https://api.sofy.ai/api/AppTests/buildUpload' \
          --header "SubscriptionKey: $SOFY_SUBSCRIPTION_KEY" \
          --form "applicationFile=@/build/app/outputs/flutter-apk/app-release.apk"
{{< /highlight >}}


#### Scheduling automation tests

{{< highlight yaml "style=paraiso-dark">}}
  scripts:
    - name: Schedule an automation test with Sofy
      script: | 
        curl --location --request POST 'https://api.sofy.ai/api/CICD/ScheduleAutomatedTestRun' \
          --header 'Content-Type: application/json' \
          --data-raw '{
            "APIKey": "$SOFY_API_KEY",
            "ScheduledID" :$SOFY_SCHEDULE_ID
            }'
{{< /highlight >}}


#### Checking the status of scheduled tests

{{< highlight yaml "style=paraiso-dark">}}
  scripts:
    - name: Schedule an automation test with Sofy
      script: | 
        curl --location --request POST 'https://api.sofy.ai/api/CICD/ScheduleAutomatedTestRunStatus' \
          --header 'Content-Type: application/json'     
          --data-raw '{
            "APIKey": "$SOFY_API_KEY",
            "ScheduledID": $SOFY_SCHEDULE_ID
            }'
{{< /highlight >}}




As soon as your **.ipa** and **.apk** are successfully built, they will appear in the **Sofy UI** and any preferred devices can be selected for testing with **Real Time**. If devices need to be pre-chosen, then some capabilities provided by **Sofy** device settings must be injected into your project's test scripts first. 
