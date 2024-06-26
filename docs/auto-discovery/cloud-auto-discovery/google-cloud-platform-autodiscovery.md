---
title: "Google Cloud Platform Autodiscovery"
sidebar_position: 4
---

import ThemedImage from '@theme/ThemedImage'
import useBaseUrl from '@docusaurus/useBaseUrl'

### GCP Discovery Items

| Cloud Service/Object Name | Name Where to locate in D42 | Accessed API | Information Generated |
|----------------------------|------------------------------|--------------|-----------------------|
| K8s (GKE) Discovery         | Devices -> Unknown           | [Compute API](https://www.googleapis.com/discovery/v1/apis/compute/v1/rest), [Container API](https://www.googleapis.com/discovery/v1/apis/container/v1/rest) | Containers, pods, clusters |
| Networks (as VRF Groups)    | Network -> VRF Groups        | [Compute API](https://www.googleapis.com/discovery/v1/apis/compute/v1/rest) | Name |
| Subnets                     | Networks -> Subnets          | [Compute API](https://www.googleapis.com/discovery/v1/apis/compute/v1/rest) | Mask, name, VRF Group |
| SQL DB                      |                              | [SQL Admin API](https://www.googleapis.com/discovery/v1/apis/sqladmin/v1beta4/rest) | Tables, instances, etc. |
| VMs                         | Devices -> All Devices       | [Compute API](https://www.googleapis.com/discovery/v1/apis/compute/v1/rest) | Type, Name, RAM, OS, CPU, cores, etc. |

### Pre-requisites

For Google Cloud Discovery, you will need a user account with the built-in Google Cloud Platform "Viewer" role.

### Configuring Google Cloud Discovery

Device42 can discover your inventory on the Google Cloud Platform. Simply create a new job, add your credentials, and you'll be off discovering all of your GCP VMs.

To create a new Google Cloud discovery job, go to **Discovery > Cloud** and click **+ Add Cloud Autodiscovery**. 

<ThemedImage
alt="Add Google Cloud discovery form"
sources={{
    light: useBaseUrl("/assets/images/google-cloud-platform-autodiscovery/add-cloud-autodiscovery-light.png"),
    dark: useBaseUrl("/assets/images/google-cloud-platform-autodiscovery/add-cloud-autodiscovery-dark.png"),
  }}
/>

1. Choose **Google Cloud** as the discovery **Type**.

2. Browse to your Google Cloud Engine JSON key. Open it in a text editor and copy the contents:

![Google Cloud Engine JSON key](/assets/images/google-cloud-platform-autodiscovery/google-json-key.png)

3. Paste the copied JSON in its entirety into the **Credentials JSON** field:

<ThemedImage
alt="Add JSON as new Secret"
sources={{
    light: useBaseUrl("/assets/images/google-cloud-platform-autodiscovery/add-json-key-light.png"),
    dark: useBaseUrl("/assets/images/google-cloud-platform-autodiscovery/add-json-key-dark.png"),
  }}
/>

4. Save and run your job. Optionally, create a schedule to run it automatically.

Data discovered on the Google Cloud Platform is similar to what you might be familiar with on AWS EC2 instances, namely:

- Discovered Google Cloud VMs are added as virtual devices.
- Cloud information is added inline in Device42 for each CI.

**Options for GCE are as follows:**

- Select **Kubernetes Discovery** to discover Kubernetes clusters hosted on your cloud platform.

<ThemedImage
alt="Kubernetes Discovery option"
sources={{
    light: useBaseUrl("/assets/images/google-cloud-platform-autodiscovery/kubernetes-discovery-option-light.png"),
    dark: useBaseUrl("/assets/images/google-cloud-platform-autodiscovery/kubernetes-discovery-option-dark.png"),
  }}
  style={{ width: '70%' }}
/>

- **Strip Domain Name:** Strip the domain name from the discovered name (everything after the first period).
- **Object category for discovered devices:** Choose a category to assign to discovered devices.
- **Overwrite existing object categories:** Select this option to overwrite any previously assigned categories with the current selection.

**Service Level Option**

You can set the **Service Level** (for example, "Development", "Deployment", or "Production") of the job to be applied to the discovered items. See [Service Level and Object Category Options](index.mdx#service-level-and-object-category-options) for details.

### GCP Account Tags

Select your Google Cloud account from the list page under **Infrastructure > Cloud Infrastructure > Cloud Accounts**.

If available, the discovered account-level tags will be listed under the **Vendor Custom Fields** section.

![GCP cloud account tags](/assets/images/GCP-tags.png)
