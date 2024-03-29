# Ansible role [graylog](https://galaxy.ansible.com/ui/standalone/roles/buluma/graylog/documentation)

Install and configure Graylog log management.

|GitHub|Version|Issues|Pull Requests|Downloads|
|------|-------|------|-------------|---------|
|[![github](https://github.com/buluma/ansible-role-graylog/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-graylog/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-graylog.svg)](https://github.com/buluma/ansible-role-graylog/releases/)|[![Issues](https://img.shields.io/github/issues/buluma/ansible-role-graylog.svg)](https://github.com/buluma/ansible-role-graylog/issues/)|[![PullRequests](https://img.shields.io/github/issues-pr-closed-raw/buluma/ansible-role-graylog.svg)](https://github.com/buluma/ansible-role-graylog/pulls/)|[![Ansible Role](https://img.shields.io/ansible/role/d/buluma/graylog)](https://galaxy.ansible.com/ui/standalone/roles/buluma/graylog/documentation)|

## [Example Playbook](#example-playbook)

This example is taken from [`molecule/default/converge.yml`](https://github.com/buluma/ansible-role-graylog/blob/master/molecule/default/converge.yml) and is tested on each push, pull request and release.

```yaml
---
- name: Converge
  hosts: all
  vars:
    # Graylog
    graylog_install_elasticsearch: True
    graylog_install_mongodb: True
    graylog_install_java: False
    graylog_not_testing: False
    # TODO: change to defaults var
    graylog_version: "{{ lookup('env', 'GRAYLOG_VERSION') | regex_search('^\\d+\\.\\d+') }}"
    graylog_full_version: "'{{ lookup('env', 'GRAYLOG_VERSION') }}-{{ lookup('env', 'GRAYLOG_REVISION') }}'"
    graylog_mongodb_version: 4.4
    graylog_mongodb_package_name: mongodb-org
    graylog_mongodb_service_name: mongod
    graylog_http_bind_address: "{{ hostvars[inventory_hostname]['ansible_default_ipv4']['address'] }}:9000"
    graylog_http_publish_uri: "http://{{ hostvars[inventory_hostname]['ansible_default_ipv4']['address'] }}:9000/"
    graylog_http_external_uri: "http://localhost:9000/"

    # Elasticsearch
    es_major_version: "6.x"
    es_version: "6.8.10"
    es_instance_name: "graylog"
    es_scripts: False
    es_templates: False
    es_version_lock: False
    es_heap_size: "1g"
    es_java_install: True
    update_java: True
    oss_version: True

    es_config:
      node.name: "graylog"
      cluster.name: "graylog"
      http.port: 9200
      transport.tcp.port: 9300
      network.host: "0.0.0.0"
      node.data: True
      node.master: True

    # Plugins
    graylog_install_enterprise_plugins: True
    graylog_install_integrations_plugins: True
    graylog_install_enterprise_integrations_plugins: True

  tasks:
    - name: "Include graylog-ansible-role"
      ansible.builtin.include_role:
        name: "buluma.graylog"

    - debug:
        msg: "Graylog is up at {{ graylog_http_external_uri }}"

  become: yes
```

The machine needs to be prepared. In CI this is done using [`molecule/default/prepare.yml`](https://github.com/buluma/ansible-role-graylog/blob/master/molecule/default/prepare.yml):

```yaml
---
- name: Prepare
  hosts: all
  gather_facts: no
  become: yes

  roles:
    - role: buluma.bootstrap
    - role: buluma.core_dependencies
```

Also see a [full explanation and example](https://buluma.github.io/how-to-use-these-roles.html) on how to use these roles.

## [Role Variables](#role-variables)

The default values for the variables are set in [`defaults/main.yml`](https://github.com/buluma/ansible-role-graylog/blob/master/defaults/main.yml):

```yaml
---

# Requirements
graylog_java_ppa: "ppa:webupd8team/java"
graylog_java_repo: "deb http://ppa.launchpad.net/webupd8team/java/ubuntu xenial main"
graylog_java_src_repo: "deb-src http://ppa.launchpad.net/webupd8team/java/ubuntu xenial main"
graylog_java_repo_keyserver: "keyserver.ubuntu.com"
graylog_java_repo_key: "EEA14886"

graylog_package_state: "present"
graylog_version: 4.3
# graylog_apt_deb_url: "https://packages.graylog2.org/repo/packages/graylog-{{ graylog_version }}-repository_latest.deb"
graylog_apt_deb_url: "https://packages.graylog2.org/repo/packages/graylog-4.2-repository_latest.deb"
graylog_manage_apt_repo: True
graylog_yum_rpm_url: "https://packages.graylog2.org/repo/packages/graylog-{{ graylog_version }}-repository_latest.rpm"
graylog_manage_yum_repo: True
graylog_es_debian_pin_version: "6.*"

# General
graylog_is_master: True
graylog_node_id_file: "/etc/graylog/server/node-id"
graylog_password_secret: "2jueVqZpwLLjaWxV"
graylog_root_username: "admin"
graylog_root_password_sha2: "8c6976e5b5410415bde908bd4dee15dfb167a9c873fc4bb8a81f6f2ab448a918"
graylog_bin_dir: "/usr/share/graylog-server/bin"
graylog_data_dir: "/var/lib/graylog-server"
graylog_plugin_dir: "/usr/share/graylog-server/plugin"
graylog_root_email: ""
graylog_root_timezone: "UTC"
graylog_alert_check_interval: 60
graylog_disable_sigar: False

# REST
graylog_http_bind_address: "0.0.0.0:9000"
graylog_http_publish_uri: "http://0.0.0.0:9000/"
graylog_http_external_uri: "http://0.0.0.0:9000/"
graylog_http_enable_cors: True
graylog_http_enable_gzip: True
graylog_http_enable_tls: False
graylog_http_tls_cert_file: "/path/to/graylog.crt"
graylog_http_tls_key_file: "/path/to/graylog.key"
graylog_http_tls_key_password: ""
graylog_http_max_header_size: 8192
graylog_http_max_initial_line_length: 4096
graylog_http_thread_pool_size: 16

# Search
graylog_elasticsearch_hosts: "http://127.0.0.1:9200"
graylog_elasticsearch_template_name: "graylog-internal"
graylog_allow_leading_wildcard_searches: False
graylog_allow_highlighting: False
graylog_elasticsearch_config_file: ""
graylog_elasticsearch_index_prefix: "graylog"
graylog_elasticsearch_analyzer: "standard"
graylog_elasticsearch_output_batch_size: 500
graylog_elasticsearch_output_flush_interval: 1
graylog_elasticsearch_disable_version_check: True
graylog_elasticsearch_http_enabled: False
graylog_disable_index_optimization: True
graylog_index_optimization_max_num_segments: 1
graylog_elasticsearch_discovery_enabled: False
graylog_elasticsearch_discovery_frequency: "30s"
graylog_elasticsearch_discovery_filter: ""
graylog_elasticsearch_max_total_connections: 20
graylog_elasticsearch_max_total_connections_per_route: 2
graylog_elasticsearch_max_retries: 2
graylog_elasticsearch_compression_enabled: False
graylog_elasticsearch_shards: 4
graylog_elasticsearch_replicas: 0
graylog_index_field_type_periodical_interval: "1h"
graylog_elasticsearch_index_optimization_jobs: 20

# Retention
graylog_no_retention: False
graylog_retention_strategy: "delete"
graylog_rotation_strategy: "count"
graylog_elasticsearch_max_docs_per_index: 20000000
graylog_elasticsearch_max_size_per_index: 1073741824
graylog_elasticsearch_max_time_per_index: "1d"
graylog_elasticsearch_max_number_of_indices: 20


# Processing
graylog_processbuffer_processors: 5
graylog_outputbuffer_processors: 3
graylog_processor_wait_strategy: "blocking"
graylog_dead_letters_enabled: False
graylog_lb_recognition_period_seconds: 3
graylog_lb_throttle_threshold_percentage: 95
graylog_stream_processing_max_faults: 3
graylog_message_journal_enabled: True
graylog_message_journal_dir: "/var/lib/graylog-server/journal"
graylog_message_journal_max_age: "12h"
graylog_message_journal_max_size: "5gb"
graylog_message_journal_flush_age: "1m"
graylog_message_journal_flush_interval: 1000000
graylog_message_journal_segment_age: "1h"
graylog_message_journal_segment_size: "100mb"
graylog_output_fault_count_threshold: 5
graylog_output_fault_penalty_seconds: 30
graylog_async_eventbus_processors: 2

# Timeouts
graylog_elasticsearch_cluster_discovery_timeout: 5000
graylog_elasticsearch_discovery_initial_state_timeout: "3s"
graylog_elasticsearch_request_timeout: "1m"
graylog_index_ranges_cleanup_interval: "1h"
graylog_stream_processing_timeout: 2000
graylog_output_module_timeout: 10000
graylog_stale_master_timeout: 2000
graylog_shutdown_timeout: 30000
graylog_http_connect_timeout: "5s"
graylog_http_read_timeout: "10s"
graylog_http_write_timeout: "10s"
graylog_ldap_connection_timeout: 2000
graylog_dashboard_widget_default_cache_time: "10s"
graylog_elasticsearch_idle_timeout: ""
graylog_elasticsearch_socket_timeout: "60s"
graylog_elasticsearch_index_optimization_timeout: "1h"
graylog_elasticsearch_connect_timeout: "10s"

# Content packs
graylog_content_packs_loader_enabled: True
graylog_content_packs_dir: "/usr/share/graylog-server/contentpacks"
graylog_content_packs_auto_install: ""

# Buffer
graylog_udp_recvbuffer_sizes: 1048576
graylog_ring_size: 65536
graylog_inputbuffer_ring_size: 65536
graylog_inputbuffer_processors: 2
graylog_inputbuffer_wait_strategy: "blocking"
graylog_outputbuffer_processor_keep_alive_time: 5000
graylog_outputbuffer_processor_threads_core_pool_size: 3
graylog_outputbuffer_processor_threads_max_pool_size: 30

# MongoDB
graylog_mongodb_version: 4.4
graylog_mongodb_ubuntu_repo: "deb [ arch=amd64,arm64 ] http://repo.mongodb.org/apt/ubuntu {{ ansible_distribution_release }}/mongodb-org/{{ graylog_mongodb_version }} multiverse"
graylog_mongodb_ubuntu_key: "https://www.mongodb.org/static/pgp/server-{{ graylog_mongodb_version }}.asc"
graylog_mongodb_debian_repo: "deb http://repo.mongodb.org/apt/debian {{ ansible_distribution_release }}/mongodb-org/{{ graylog_mongodb_version }} main"
graylog_mongodb_debian_key: "https://www.mongodb.org/static/pgp/server-{{ graylog_mongodb_version }}.asc"
graylog_mongodb_redhat_repo: "https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/{{ graylog_mongodb_version }}/x86_64/"
graylog_mongodb_redhat_key: "https://www.mongodb.org/static/pgp/server-{{ graylog_mongodb_version }}.asc"
graylog_mongodb_uri: "mongodb://127.0.0.1:27017/graylog"
graylog_mongodb_max_connections: 100
graylog_mongodb_threads_allowed_to_block_multiplier: 5
graylog_mongodb_package_name: mongodb-org
graylog_mongodb_service_name: mongod
graylog_mongodb_bind_ip: "127.0.0.1"
graylog_mongodb_bind_port: "27017"

# Drools
graylog_rules_file: ""

# Mail
graylog_transport_email_enabled: False
graylog_transport_email_hostname: ""
graylog_transport_email_port: 587
graylog_transport_email_use_auth: True
graylog_transport_email_use_tls: True
graylog_transport_email_use_ssl: True
graylog_transport_email_auth_username: ""
graylog_transport_email_auth_password: ""
graylog_transport_email_subject_prefix: "[graylog]"
graylog_transport_email_from_email: ""
graylog_transport_email_web_interface_url: ""

# Proxy
graylog_http_proxy_uri: ""
graylog_proxied_requests_thread_pool_size: 32
graylog_trusted_proxies: ""
graylog_non_proxy_hosts: ""

# Web UI
graylog_web_enable: True
graylog_web_listen_uri: "http://0.0.0.0:9000/"
graylog_web_endpoint_uri: ""
graylog_enable_cors: True
graylog_enable_gzip: True
graylog_web_enable_tls: False
graylog_web_tls_cert_file: ""
graylog_web_tls_key_file: ""
graylog_web_tls_key_password: ""
graylog_web_max_header_size: 8192
graylog_web_max_initial_line_length: 4096
graylog_web_thread_pool_size: 16

# JVM
graylog_gc_warning_threshold: "1s"
graylog_server_heap_size: "1500m"
graylog_server_java: "/usr/bin/java"
graylog_server_java_opts_extra: ""
graylog_server_java_opts: "-Djava.net.preferIPv4Stack=true -Xms{{ graylog_server_heap_size }} -Xmx{{ graylog_server_heap_size }} -XX:NewRatio=1 -server -XX:+ResizeTLAB -XX:-OmitStackTraceInFastThrow {{graylog_server_java_opts_extra}}"
graylog_server_args: ""
graylog_server_wrapper: ""

# Install Switches
graylog_install_elasticsearch: True
graylog_install_mongodb: True
graylog_install_nginx: False  # Deprecated
graylog_install_java: True

# Disable steps which break tests
graylog_not_testing: True

# Plugins
graylog_install_enterprise_plugins: False
graylog_install_integrations_plugins: False
graylog_install_enterprise_integrations_plugins: False

graylog_additional_config: {}

required_vars:
  - graylog_version
```

## [Requirements](#requirements)

- pip packages listed in [requirements.txt](https://github.com/buluma/ansible-role-graylog/blob/master/requirements.txt).

## [State of used roles](#state-of-used-roles)

The following roles are used to prepare a system. You can prepare your system in another way.

| Requirement | GitHub | Version |
|-------------|--------|--------|
|[buluma.bootstrap](https://galaxy.ansible.com/buluma/bootstrap)|[![Ansible Molecule](https://github.com/buluma/ansible-role-bootstrap/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-bootstrap/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-bootstrap.svg)](https://github.com/shadowwalker/ansible-role-bootstrap)|
|[buluma.core_dependencies](https://galaxy.ansible.com/buluma/core_dependencies)|[![Ansible Molecule](https://github.com/buluma/ansible-role-core_dependencies/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-core_dependencies/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-core_dependencies.svg)](https://github.com/shadowwalker/ansible-role-core_dependencies)|
|[lean_delivery.java](https://galaxy.ansible.com/buluma/lean_delivery.java)|[![Ansible Molecule](https://github.com/buluma/lean_delivery.java/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/lean_delivery.java/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/lean_delivery.java.svg)](https://github.com/shadowwalker/lean_delivery.java)|
|[elastic.elasticsearch](https://galaxy.ansible.com/buluma/elastic.elasticsearch)|[![Ansible Molecule](https://github.com/buluma/elastic.elasticsearch/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/elastic.elasticsearch/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/elastic.elasticsearch.svg)](https://github.com/shadowwalker/elastic.elasticsearch)|

## [Dependencies](#dependencies)

Most roles require some kind of preparation, this is done in `molecule/default/prepare.yml`. This role has a "hard" dependency on the following roles:

- {'role': 'lean_delivery.java', 'version': '7.1.0', 'when': 'graylog_install_java'}
- {'role': 'elastic.elasticsearch', 'src': 'https://github.com/elastic/ansible-elasticsearch.git', 'version': 'main', 'scm': 'git', 'when': 'graylog_install_elasticsearch'}

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://buluma.github.io/) for further information.

Here is an overview of related roles:

![dependencies](https://raw.githubusercontent.com/buluma/ansible-role-graylog/png/requirements.png "Dependencies")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/buluma):

|container|tags|
|---------|----|
|[EL](https://hub.docker.com/repository/docker/buluma/enterpriselinux/general)|7, 8|
|[Debian](https://hub.docker.com/repository/docker/buluma/debian/general)|all|
|[Ubuntu](https://hub.docker.com/repository/docker/buluma/ubuntu/general)|all|

The minimum version of Ansible required is 2.5, tests have been done to:

- The previous version.
- The current version.
- The development version.

If you find issues, please register them in [GitHub](https://github.com/buluma/ansible-role-graylog/issues)

## [Changelog](#changelog)

[Role History](https://github.com/buluma/ansible-role-graylog/blob/master/CHANGELOG.md)

## [License](#license)

[Apache-2.0](https://github.com/buluma/ansible-role-graylog/blob/master/LICENSE)

## [Author Information](#author-information)

[Shadow Walker](https://buluma.github.io/)

