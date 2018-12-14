## Use this repo to deploy Kubernetes cluster in Google Cloud using Ansible.

Based on Kubernetes-The-Hard-Way by Kelsey Hightower https://github.com/kelseyhightower/kubernetes-the-hard-way/ just for automating process of deployment on GCP with ansible

### Preparation
Setup Google Cloud SDK on your PC

```ansible-playbook installsdk.yml```

After installation, use following command to initialize gcloud and choose your project and region/zone

```gcloud init```

Copy environment variables file and fill in all required variables in file ```.env```. More information about dynamic inventory setup: https://docs.ansible.com/ansible/latest/scenario_guides/guide_gce.html

```cp .env.example .env```

Also copy gce.ini.example file and fill in all required variables in section ```[gce]``` in file ```gce.ini```

```cp inventory/gce.ini.example inventory/gce.ini```

Add settings for your group vars - fill in ```all.yml``` based on your project, zone and region for installation kubernetes cluster

```cp inventory/group_vars/all.yml.example inventory/group_vars/all.yml```

Generate ssh keys:

```ssh-keygen -f ./ssh/k8s_key -P "" -C k8s```

Add content of ```./ssh/k8s_key.pub``` to your Google Cloud console GUI to Compute Engine->Metadata->SSH keys

Export environment variables:

```export $(cat .env)```

### Provision resources
Build all basic instances/networks/firewall rules etc.

```ansible-playbook prepare.yml```

Once playbook is executed, you can verify that instances are up and running

```gcloud compute instances list```

> output

```
NAME          ZONE        MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP     STATUS
controller-0  us-west1-c  n1-standard-1               10.240.0.2  XX.XXX.XXX.XXX  RUNNING
controller-1  us-west1-c  n1-standard-1               10.240.0.3  XX.XXX.X.XX     RUNNING
controller-2  us-west1-c  n1-standard-1               10.240.0.4  XX.XXX.XXX.XX   RUNNING
worker-0      us-west1-c  n1-standard-1               10.240.0.5  XXX.XXX.XXX.XX  RUNNING
worker-1      us-west1-c  n1-standard-1               10.240.0.6  XX.XXX.XX.XXX   RUNNING
worker-2      us-west1-c  n1-standard-1               10.240.0.7  XXX.XXX.XX.XX   RUNNING
```

### Bring up Kubernetes Cluster

Execute following command, make coffee and enjoy

```ansible-playbook site.yml```

Verify test busybox container is running:

```kubectl get pods -l run=busybox```

> output

```
NAME                      READY   STATUS    RESTARTS   AGE
busybox-bd8fb7cbd-vflm9   1/1     Running   0          10s
```

#### Perfect! Kubernetes Cluster with DNS add-on is up and running

### Clean Up Resources

To delete resources on GCP execute

```ansible-playbook 14-cleanup.yml```
