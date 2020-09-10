ci-operator/rudder ansible role

        Parse and pre-process a Helm chart to create dynamic inventory hosts for each resource object found therein. 

        This role will retrieve a chart, optionally from a git repository where the chart's values.yaml file is maintained, 
        and store its configuration values and repository information in a configmap in the target namespace. If this configmap
        is present, default configuration values will be read from its contents and any configuration values provided by this
        role will override and update existing values in the configmap. 

        Because this role stores files on the ansible host, a 'mgmt_dir' path must be provided for persistence and idempotency.
        The result of running this role for will be the creation of dynamic inventory hosts in the 'k8s_resource' group; one
        inventory host will be added for each unique resource definition found in the templated chart.

        Note that the only changes made to a cluster by this role are to an 'app-config-*' configmap - this role applies neither
        the chart itself nor the processed resource definitions to the cluster.
        This role is designed to pair with the 'ci-operator/resource' role which will apply to a kubernetes cluster any resource
        definition found in the 'resource' variable which is populated by this role in each new dynamic inventory host.



VARIABLES
        app_name: The configmap's postfix, and the prefix for values file found in repository - e.g., {{ app_name }}-values.yaml
        app_repo: Git repository URL where the Helm {{ app_name }}-values.yaml file is found.
        app_branch: Git branch where the Helm values file is found (default: 'master')
        app_version: The version passed to Helm for templating the chart.
        app_chart: URL or canonical name of Helm chart.
        app_owner: Metadata string to be stored in app-config configmap.
        app_params: A dictionary of additional parameter values which will be used to template the chart.



EXAMPLE
  roles:
    - role: ci-operator/rudder
      vars: 
        app_name: minio
        app_repo: git@gitlab.cee.redhat.com:josiah/rp-helm-exd.git
        app_branch: exd
        app_version: 2.5.12
        app_chart: stable/minio
        app_owner: "{{ owner_email }}"
        app_params:
          NAMESPACE: "{{ inventory_hostname }}"
          DOMAIN: "mydomain.com"
