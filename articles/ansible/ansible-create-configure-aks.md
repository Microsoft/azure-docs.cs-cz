---
title: Kurz – konfigurace clusterů Azure Kubernetes Service (AKS) v Azure s využitím Ansible
description: Zjistěte, jak pomocí Ansible vytvořit a spravovat cluster Azure Kubernetes Service v Azure.
keywords: Ansible, Azure, DevOps, bash, cloudshellu, PlayBook, AKS, Container, AKS, Kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 11/04/2019
ms.openlocfilehash: b0839cf418cd30f62623e046960c32d41537609a
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614384"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>Kurz: konfigurace clusterů Azure Kubernetes Service (AKS) v Azure pomocí Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS je možné nakonfigurovat tak, aby pro ověřování uživatelů používala [Azure Active Directory (AD)](/azure/active-directory/) . Po nakonfigurování se pomocí ověřovacího tokenu Azure AD přihlásíte ke clusteru AKS. RBAC může být založen na identitě nebo členství uživatele ve skupině adresáře.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření clusteru AKS
> * Konfigurace clusteru AKS

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>Vytvoření spravovaného clusteru AKS

Vzorový PlayBook vytvoří v rámci skupiny prostředků skupinu prostředků a cluster AKS.

Uložte následující ukázkový playbook jako `azure_create_aks.yml`:

```yml
- name: Create Azure Kubernetes Service
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
    aks_version: aks_version
tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create a managed Azure Container Services (AKS) cluster
    azure_rm_aks:
      name: "{{ aks_name }}"
      location: "{{ location }}"
      resource_group: "{{ resource_group }}"
      dns_prefix: "{{ aks_name }}"
      kubernetes_version: "{{aks_version}}"
      linux_profile:
        admin_username: "{{ username }}"
        ssh_key: "{{ ssh_key }}"
      service_principal:
        client_id: "{{ client_id }}"
        client_secret: "{{ client_secret }}"
      agent_pool_profiles:
        - name: default
          count: 2
          vm_size: Standard_D2_v2
      tags:
        Environment: Production
```

Před spuštěním PlayBook se podívejte na následující poznámky:

- První oddíl v rámci `tasks` definuje skupinu prostředků s názvem `myResourceGroup` v umístění `eastus`.
- Druhá část v rámci `tasks` definuje cluster AKS s názvem `myAKSCluster` v rámci skupiny prostředků `myResourceGroup`.
- Pro zástupný symbol `your_ssh_key` zadejte svůj veřejný klíč RSA v jednořádkovém formátu – začněte řetězcem SSH-RSA (bez uvozovek).
- Pro zástupný text `aks_version` použijte příkaz [AZ AKS get-versions](/cli/azure/aks?view=azure-cli-latest#az-aks-get-versions) .

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook azure_create_aks.yml
```

Spuštění PlayBook zobrazí výsledky podobné následujícímu výstupu:

```Output
PLAY [Create AKS] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create an Azure Container Services (AKS) cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="scale-aks-nodes"></a>Škálování uzlů AKS

Ukázkový playbook v předchozí části definuje dva uzly. Počet uzlů upravíte úpravou hodnoty `count` v bloku `agent_pool_profiles`.

Uložte následující ukázkový playbook jako `azure_configure_aks.yml`:

```yml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

Před spuštěním PlayBook se podívejte na následující poznámky:

- Pro zástupný symbol `your_ssh_key` zadejte svůj veřejný klíč RSA v jednořádkovém formátu – začněte řetězcem SSH-RSA (bez uvozovek).

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook azure_configure_aks.yml
```

Spuštění PlayBook zobrazí výsledky podobné následujícímu výstupu:

```Output
PLAY [Scale AKS cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Scaling an existed AKS cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="delete-a-managed-aks-cluster"></a>Odstranění spravovaného clusteru AKS

Vzorový PlayBook odstraní cluster AKS.

Uložte následující ukázkový playbook jako `azure_delete_aks.yml`:


```yml
- name: Delete a managed Azure Container Services (AKS) cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    aks_name: myAKSCluster
  tasks:
  - name:
    azure_rm_aks:
      name: "{{ aks_name }}"
      resource_group: "{{ resource_group }}"
      state: absent
  ```

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook azure_delete_aks.yml
```

Spuštění PlayBook zobrazí výsledky podobné následujícímu výstupu:

```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [azure_rm_aks] 

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Škálování aplikace ve službě Azure Kubernetes Service (AKS)](/azure/aks/tutorial-kubernetes-scale)