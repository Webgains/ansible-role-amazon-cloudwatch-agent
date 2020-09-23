ansible-role-amazon-cloudwatch-agent
======

This role will install the AWS CloudWatch Agent on Ubuntu (64-bit, precise/trusty/xenial).
- config and credentials are stored in /root/.aws/
- the process runs as root

*See the subdir "ExampleCfgs" for an example of how to configure this role.*


Variables
------

### aws_cwa_region
(Optional) AWS region.  Defaults to "us-east-1".
```
aws_cwa_region: "us-east-1"
```

### aws_cwa_namespace
The namespace in which your metrics will reside (e.g. 'myApplication').
```
aws_cwa_namespace: "myApplication"
```

### aws_cwa_logfiles
(Optional) List of logfiles that you wish to monitor, and their associated parameters -- for example:
```
aws_cwa_logfiles
  - file_path: '/var/log/syslog'
    log_group_name: 'log_group_name_goes_here'
    log_group_tags:
      - TagKey: TagValue
    log_stream_name: '{{ansible_hostname}}/log_stream_name'
    log_retention: '7'
    timestamp_format: '%b %-d %H:%M:%S'
    timezone: 'LOCAL'
```

#### "log_retention" is specified in days, and must be one of: 1, 3, 5, 7, 14, 30, 60, 90, 120, 150, 180, 365, 400, 545, 731, 1827, 3653.

#### Omitting the "log_retention" attribute will result in the associated log group having its retention policy, if any, deleted (i.e. 'Never Expire')

#### For the timestamp_format syntax, see https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-Configuration-File-Details.html#CloudWatch-Agent-Configuration-File-Logssection

### aws_cwa_disk_monitor_paths
(Optional) List of paths (i.e. different partitions) that you wish to monitor for space.  Defaults to "/".  For example:
```
aws_cwa_disk_monitor_paths:
  - "/"
  - "/opt"
```

### aws_cwa_key_access
AWS access key
```
aws_cwa_key_access: (encrypted)ACCESSKEYGOESHERE
```

### aws_cwa_key_secret
AWS secret key
```
aws_cwa_key_secret: (encrypted)SECRETKEYGOESHERE
```

### security_token
AWS security token
```
security_token: "{{ lookup('env','AWS_SECURITY_TOKEN') }}"
```
Used while assuming an assumed role

#### It's recommended that you declare `aws_cwa_key_access` and `aws_cwa_key_secret` in either a vault-encypted var file, or as vault-encrypted strings within a var file.

### aws_cwa_cfgs
Path (absolute, or relative to playbook) containing the TEMPLATE named "amazon-cloudwatch-agent.json.j2" for the CloudWatch Agent config

Only "amazon-cloudwatch-agent.json.j2" in the given directory will be processed.  The '.j2' extension will be removed.
```
aws_cwa_cfgs: "group_files/awscwa_cfg/webserver-stg"
```


Example Playbook
------

```
---
- hosts: all
  become: yes
  become_user: root
  roles:
  - ansible-role-amazon-cloudwatch-agent
```


Example Dir Structure
------

```
.
├── group_files
│   └── awscwa_cfg
│       └── webserver-stg
│           └── amazon-cloudwatch-agent.json.j2
├── group_vars
│   └── all.yml
├── inventory
├── main.yml
└── roles
    └── ansible-role-amazon-cloudwatch-agent
        ├── handlers
        │   └── main.yml
        ├── README.md
        └── tasks
            ├── main.yml
            ├── ubuntu-12.yml
            ├── ubuntu-14.yml
            └── ubuntu-16.yml
```


Author Information
------

abarrett@globeandmail.com

https://github.com/tgam-abarrett
