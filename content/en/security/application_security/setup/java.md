---
title: Enabling AAP for Java
code_lang: java
type: multi-code-lang
code_lang_weight: 0
aliases:
  - /security_platform/application_security/getting_started/java
  - /security/application_security/getting_started/java
  - /security/application_security/threats/setup/threat_detection/java
  - /security/application_security/threats_detection/java
  - /security/application_security/setup/aws/fargate/java
further_reading:
- link: "/security/application_security/add-user-info/"
  tag: "Documentation"
  text: "Adding user information to traces"
- link: 'https://github.com/DataDog/dd-trace-java'
  tag: "Source Code"
  text: 'Java Datadog library source code'
- link: "/security/default_rules/?category=cat-application-security"
  tag: "Documentation"
  text: "OOTB App and API Protection Rules"
- link: "/security/application_security/troubleshooting"
  tag: "Documentation"
  text: "Troubleshooting App and API Protection"

---

You can monitor App and API Protection for Java apps running in Docker, Kubernetes, Amazon ECS, and AWS Fargate.

{{% appsec-getstarted %}}

{{< callout btn_hidden="true" header="Shortcut options before manual setup:" >}}
**Single Step APM Instrumentation**: For faster setup with automatic instrumentation, consider using [Single Step APM Instrumentation][1] which automatically installs the Datadog SDK with no additional configuration required.

Once SSI is set up, you can enable App and API Protection by going to your APM service in the Datadog app → Service Configuration section → Enable Application Security Monitoring.
{{< /callout >}}

## Overview

Datadog Application Security Management (ASM) provides App and API Protection (AAP) capabilities including:
- **Application Security Monitoring**: Real-time threat detection and protection against attacks like SQL injection, XSS, and more
- **Software Composition Analysis (SCA)**: Identification of vulnerable dependencies in your codebase
- **Interactive Application Security Testing (IAST)**: Runtime vulnerability detection during testing

ASM works by leveraging the Datadog Java tracing library to monitor HTTP requests, analyze patterns, and detect security threats in real-time. The library integrates seamlessly with your existing application without requiring code changes.

For detailed compatibility information, including supported Java versions, frameworks, and deployment environments, see [Single Step Instrumentation Compatibility][2].

## Agent setup

Before enabling AAP for your Java applications, ensure you have the Datadog Agent installed and configured. The Agent collects and forwards security telemetry from your applications to Datadog.

**[Install or update the Datadog Agent][3]** to the latest version. AAP requires Agent version 7.41.1 or higher for optimal performance and feature support.

## Library setup

To enable AAP capabilities, you need the Datadog Java tracing library (version 0.94.0 or higher) installed in your application environment.

### Download the library

Download the latest version of the Datadog Java library:

{{< tabs >}}
{{% tab "Wget" %}}
```shell
wget -O dd-java-agent.jar 'https://dtdg.co/latest-java-tracer'
```
{{% /tab %}}
{{% tab "cURL" %}}
```shell
curl -Lo dd-java-agent.jar 'https://dtdg.co/latest-java-tracer'
```
{{% /tab %}}
{{% tab "Dockerfile" %}}
```dockerfile
ADD 'https://dtdg.co/latest-java-tracer' dd-java-agent.jar
```
{{% /tab %}}
{{< /tabs >}}

### Verify compatibility

To check that your service's language and framework versions are supported for AAP capabilities, see [Single Step Instrumentation Compatibility][2].

## Service configuration

### Standalone billing alternative

If you want to use Application Security Management without APM tracing functionality, you can deploy with [Standalone App and API Protection][4]. This configuration reduces the amount of APM data sent to Datadog to the minimum required by App and API Protection products.

To enable standalone mode:
1. Set `DD_APM_TRACING_ENABLED=false` environment variable
2. Keep `DD_APPSEC_ENABLED=true` environment variable
3. This configuration will minimize APM data while maintaining full security monitoring capabilities

### Deployment configuration

Configure your deployment environment to enable AAP:

{{< tabs >}}
{{% tab "Docker CLI" %}}

Add the AAP environment variable to your `docker run` command:

```shell
docker run [...] -e DD_APPSEC_ENABLED=true [...]
```

{{% /tab %}}
{{% tab "Dockerfile" %}}

Add the following environment variable to your container Dockerfile:

```dockerfile
ENV DD_APPSEC_ENABLED=true
```

{{% /tab %}}
{{% tab "Kubernetes" %}}

Update your deployment configuration file and add the AAP environment variable:

```yaml
spec:
  template:
    spec:
      containers:
        - name: <CONTAINER_NAME>
          image: <CONTAINER_IMAGE>/<TAG>
          env:
            - name: DD_APPSEC_ENABLED
              value: "true"
```

{{% /tab %}}
{{% tab "Amazon ECS" %}}

Update your ECS task definition JSON file by adding this in the environment section:

```json
"environment": [
  ...,
  {
    "name": "DD_APPSEC_ENABLED",
    "value": "true"
  }
]
```

{{% /tab %}}
{{% tab "AWS Fargate" %}}

Set the `-Ddd.appsec.enabled` flag or the `DD_APPSEC_ENABLED` environment variable to `true` in your service invocation:

```shell
java -javaagent:dd-java-agent.jar \
     -Ddd.appsec.enabled=true \
     -jar <YOUR_SERVICE>.jar \
     <YOUR_SERVICE_FLAGS>
```

{{% /tab %}}
{{< /tabs >}}

## Enabling AAP

### Run your application with AAP enabled

Start your Java application with the Datadog agent and AAP enabled:

**From the command line:**
```shell
java -javaagent:/path/to/dd-java-agent.jar -Ddd.appsec.enabled=true -Ddd.service=<MY_SERVICE> -Ddd.env=<MY_ENV> -jar path/to/app.jar
```

**Important considerations:**
- **File system requirements**: Read-only file systems are not currently supported. The application must have access to a writable `/tmp` directory.
- **Service identification**: Always specify `DD_SERVICE` (or `-Ddd.service`) and `DD_ENV` (or `-Ddd.env`) for proper service identification in Datadog.

### Verify AAP is working

{{% appsec-getstarted-2-plusrisk %}}

{{< img src="/security/application_security/appsec-getstarted-threat-and-vuln_2.mp4" alt="Video showing Signals explorer and details, and Vulnerabilities explorer and details." video="true" >}}

If you need additional assistance, contact [Datadog support][5].

## Further Reading

{{< partial name="whats-next/whats-next.html" >}}

[1]: https://docs.datadoghq.com/tracing/trace_collection/automatic_instrumentation/single-step-apm/?tab=
[2]: https://docs.datadoghq.com/tracing/trace_collection/automatic_instrumentation/single-step-apm/compatibility/?tab=java#tracer-libraries
[3]: https://app.datadoghq.com/fleet/install-agent/latest?platform=overview
[4]: /security/application_security/guide/standalone_application_security/
[5]: /help
