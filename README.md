---
---
# Ansible Automation Platform Troubleshooting Guide

GitHub repo: [https://github.com/myllynen/aap-troubleshooting-guide](https://github.com/myllynen/aap-troubleshooting-guide)  
Themed page: [https://myllynen.github.io/aap-troubleshooting-guide](https://myllynen.github.io/aap-troubleshooting-guide)

## Introduction

This page provides basic Ansible Automation Platform (AAP)
troubleshooting tips and tricks.

For Red Hat Ansible Automation Controller troubleshooting docs, see the
[AAP Troubleshooting page](https://docs.ansible.com/automation-controller/latest/html/administration/troubleshooting.html).

For Red Hat Ansible Automation Controller generic user guide, see the
[AAP User Guide](https://docs.ansible.com/automation-controller/latest/html/userguide/index.html).

## Ansible Automation Platform Setup

For understanding Ansible Automation Platform setup check its topology
view, instances, and settings Web UI pages. The location of these page
would be something like:

* [https://aap.example.com/#/topology_view](https://aap.example.com/#/topology_view)
* [https://aap.example.com/#/instances](https://aap.example.com/#/instances)
* [https://aap.example.com/#/settings](https://aap.example.com/#/settings)

In case working on the command line use the following command to
display AAP instances and topology:

```
awx-manage list_instances
```

## Troubleshooting Jobs

### Jobs Overview

To see currently running jobs visit the jobs page on the Web UI:

* [https://aap.example.com/#/jobs](https://aap.example.com/#/jobs)

The jobs type of _Workflow Job_ are parent jobs that are running the
configured number of actual jobs in parallel, see _Job Slicing_ in the
corresponding _Template → Details_ page. Jobs are also subject of
AAP-wide default configurations, see _Settings → Job_ settings. Pay
special attention to the _Job Type_ parameter in the _Template →
Details_ page and always use `Check` or `Run` as appropriate.

### Investigating Individual Job

For details of a running or a past job navigate to _Jobs → Number/Name
→ Details_. Here the following information is found:

* Project
* Inventory
* Job status
* Verbosity
* Name of the Execution Node
* Revision (can be used to check the contents in a git repo)

For the job output check the _Output_ tab for the job. Hint: using
_Ctrl+-_ in your browser should zoom out the page which might make the
output slightly easier to read. Another option is to download the
output and open it in a text editor (other than Notepad which doesn't
handle Unix text files properly). Third, and sometimes the best, option
is to open the API page which shows the output in full page mode, for
instance (obviously replace the job number as appropriate):

[https://aap.example.com/api/v2/jobs/1234/stdout/](https://aap.example.com/api/v2/jobs/1234/stdout/)

The output can be downloaded in plain text format by adding
`?format=txt` to end of the URL.

### Debugging a Job

In case a job is not completing as expected it may be a good idea to
increase verbosity and disable job slicing to allow better see what is
going on. Visit the job template page, set _Job Slicing_ to `1` and
apply suitable value for _Verbosity_. Note that high verbosity values
(`3` and above) may in some cases cause secrets to be logged. According
to Ansible developers this is intended. A reasonable starting verbosity
level is often `2`.

If there are one or few hosts in the current inventory known to be
problematic those could be (temporarily) excluded by editing the
template and adding the offending hosts in the _Limit_ string prefixed
by an exclamation mark (`!`).

## Investigating AAP Under the Hood

In some cases it might be required to check AAP and system log on AAP
controller and/or execution node(s). First, check the AAP topology and
possible job details to identify relevant nodes.

On the command line `awx-manage list_instances` can be used to display
AAP instances and topology. For details about automation mesh use the
`receptorctl` command. For instance, to display basics about the
current node and automation mesh setup and status use:

```
receptorctl --socket /run/awx-receptor/receptor.sock status
```

To ping a node as part of the automation mesh use:

```
receptorctl --socket /run/awx-receptor/receptor.sock ping en-1.example.com
```

On controller nodes the most relevant logs are typically in
_/var/log/tower_.

On execution nodes the most relevant logs are probably
_/var/log/messages_ and in _/var/log/receptor_.

## Additional Information

* [Ansible Automation Controller admin guide](https://docs.ansible.com/automation-controller/latest/html/administration/index.html)
* [Ansible Automation Controller user guide](https://docs.ansible.com/automation-controller/latest/html/userguide/index.html)

# See Also

See also
[https://github.com/myllynen/rhel-troubleshooting-guide](https://github.com/myllynen/rhel-troubleshooting-guide).

See also
[https://github.com/myllynen/aap-automation](https://github.com/myllynen/aap-automation).
