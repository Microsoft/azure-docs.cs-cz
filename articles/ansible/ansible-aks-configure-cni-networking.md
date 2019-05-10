---
title: Kurz – konfigurace sítí Azure CNI s ve službě Azure Kubernetes Service (AKS) pomocí Ansible | Dokumentace Microsoftu
description: Zjistěte, jak použít Ansible ke konfiguraci kubenet sítí v clusteru Azure Kubernetes Service (AKS)
keywords: ansible, azure, devops, bash, cloud shell, playbooku, aks, kontejner, aks, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 2d43b1ffbb7910b16c81df2ff5b21e67dbcb0193
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231357"
---
# <a name="tutorial-configure-azure-cni-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Kurz: Konfigurace sítí Azure CNI s ve službě Azure Kubernetes Service (AKS) pomocí Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Používání AKS můžete nasadit cluster pomocí následující modely sítě:

- [Sítě Kubenet](/azure/aks/configure-kubenet) – jsou síťovým prostředkům obvykle vytvoření a konfiguraci při nasazení clusteru AKS.
- [Sítě Azure CNI](/azure/aks/configure-azure-cni) – AKS cluster je připojený k existující prostředky virtuální sítě (VNET) a konfigurací.

Další informace o možnostech sítě pro vaše aplikace ve službě AKS najdete v tématu [sítě koncepty pro aplikace ve službě AKS](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření clusteru AKS
> * Konfigurace sítí Azure CNI

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 

## <a name="create-a-virtual-network-and-subnet"></a>Vytvoření virtuální sítě a podsítě

Vzorový kód playbook v této části se používá pro:

- Vytvoření virtuální sítě
- Vytvořte podsíť ve virtuální síti

Uložte následující ukázkový playbook jako `vnet.yml`:

```yml
- name: Create vnet
  azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefixes_cidr:
          - 10.0.0.0/8

- name: Create subnet
  azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefix_cidr: 10.240.0.0/16
      virtual_network_name: "{{ name }}"
  register: subnet
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Vytvoření clusteru AKS ve virtuální síti

Vzorový kód playbook v této části se používá pro:

- Vytvoření clusteru AKS ve virtuální síti.

Uložte následující ukázkový playbook jako `aks.yml`:

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster within a VNet
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
          vnet_subnet_id: "{{ vnet_subnet_id }}"
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      network_profile:
          network_plugin: azure
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.2.0.10
          service_cidr: 10.2.0.0/24
  register: aks
```

Tady jsou některé důležité poznámky vzít v úvahu při práci s playbook ukázka:

- Použití `azure_rm_aks_version` modulu najít podporovanou verzi.
- `vnet_subnet_id` Je podsíť vytvořená v předchozím oddílu.
- Načte playbook `ssh_key` z `~/.ssh/id_rsa.pub`. Když ji upravíte, použijte jednořádkovém formátu – začíná na "ssh-rsa" (bez uvozovek).
- `client_id` a `client_secret` načtení hodnot z `~/.azure/credentials`, což je výchozí soubor přihlašovacích údajů. Můžete nastavit tyto hodnoty do vaší služby instančního objektu nebo načíst tyto hodnoty z proměnné prostředí:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="run-the-sample-playbook"></a>Spustit playbook vzorku

Vzorový kód playbook v této části se používá k testování různých funkcí zobrazených v celém tomto kurzu.

Uložte následující ukázkový playbook jako `aks-azure-cni.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exists
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Show AKS cluster detail
       debug:
           var: aks
```

Tady jsou některé důležité poznámky vzít v úvahu při práci s playbook ukázka:

- Změnit `aksansibletest` hodnota, která má název skupiny prostředků.
- Změnit `aksansibletest` hodnota, která má název vašeho AKS.
- Změnit `eastus` hodnotu pro vaše umístění skupiny prostředků.

Spusťte playbook ansible playbook příkazem:

```bash
ansible-playbook aks-azure-cni.yml
```

Po spuštění playbooku, se zobrazí výstup podobný následující výsledky:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exists] 
changed: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-cni/vnet.yml for localhost

TASK [Create vnet] 
changed: [localhost]

TASK [Create subnet] 
changed: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-cni/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "aks": {
        "aad_profile": {},
        "addon": {},
        "agent_pool_profiles": [
            {
                "count": 3,
                "name": "nodepool1",
                "os_disk_size_gb": 100,
                "os_type": "Linux",
                "storage_profile": "ManagedDisks",
                "vm_size": "Standard_D2_v2",
                "vnet_subnet_id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
            }
        ],
        "changed": true,
        "dns_prefix": "aksansibletest",
        "enable_rbac": false,
        "failed": false,
        "fqdn": "aksansibletest-0272707d.hcp.eastus.azmk8s.io",
        "id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "..."
        },
        "location": "eastus",
        "name": "aksansibletest",
        "network_profile": {
            "dns_service_ip": "10.2.0.10",
            "docker_bridge_cidr": "172.17.0.1/16",
            "network_plugin": "azure",
            "network_policy": null,
            "pod_cidr": null,
            "service_cidr": "10.2.0.0/24"
        },
        "node_resource_group": "MC_aksansibletest_aksansibletest_eastus",
        "provisioning_state": "Succeeded",
        "service_principal_profile": {
            "client_id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
        },
        "tags": null,
        "type": "Microsoft.ContainerService/ManagedClusters",
        "warnings": [
            "Azure API profile latest does not define an entry for ContainerServiceClient",
            "Azure API profile latest does not define an entry for ContainerServiceClient"
        ]
    }
}

PLAY RECAP 
localhost                  : ok=9    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte prostředky vytvořené v tomto článku. 

Vzorový kód playbook v této části se používá pro:

- Odstranit skupinu prostředků podle `vars` oddílu.

Uložte následující ukázkový playbook jako `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: {{ resource_group_name }}
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

Tady jsou některé důležité poznámky vzít v úvahu při práci s playbook ukázka:

- Nahradit `{{ resource_group_name }}` zástupný symbol s názvem vaší skupiny prostředků.
- Všechny prostředky v zadané skupině prostředků se odstraní.

Spusťte playbook ansible playbook příkazem:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Konfigurovat Azure Active Directory ve službě AKS pomocí Ansible](./ansible-aks-configure-rbac.md)