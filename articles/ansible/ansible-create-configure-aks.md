---
title: Kurz – konfigurace clusterů Azure Kubernetes Service (AKS) v Azure pomocí Ansible | Dokumentace Microsoftu
description: Zjistěte, jak pomocí Ansible vytvořit a spravovat cluster Azure Kubernetes Service v Azure.
keywords: ansible, azure, devops, bash, cloud shell, playbooku, aks, kontejner, aks, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 64b8eb12c755f41cde28067b5c145c4e95066886
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766046"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>Kurz: Konfigurace clusterů Azure Kubernetes Service (AKS) v Azure pomocí Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS může být nakonfigurován pro použití [Azure Active Directory (AD)](/azure/active-directory/) k ověřování uživatelů. Po nakonfigurování používáte k přihlášení do clusteru AKS tokenu ověřování Azure AD. RBAC může být založen na identitu uživatele nebo členství ve skupině adresáře.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření clusteru AKS
> * Konfigurace clusteru AKS

## <a name="prerequisites"></a>Požadavky

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>Vytvoření spravovaného clusteru AKS

Playbook ukázka vytvoří skupinu prostředků a v rámci skupiny prostředků clusteru AKS.

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

Před spuštěním playbooku, viz následující poznámky:

- První oddíl v rámci `tasks` definuje skupinu prostředků s názvem `myResourceGroup` v rámci `eastus` umístění.
- Druhá část v rámci `tasks` definuje cluster AKS, s názvem `myAKSCluster` v rámci `myResourceGroup` skupinu prostředků.
- Pro `your_ssh_key` zástupného symbolu, zadejte veřejný klíč RSA v jednořádkovém formátu – začíná na "ssh-rsa" (bez uvozovek).

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook azure_create_aks.yml
```

Spuštění playbooku zobrazí výsledky podobné následující výstup:

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

Ukázkový playbook v předchozí části definuje dva uzly. Upravit počet uzlů úpravou `count` hodnotu `agent_pool_profiles` bloku.

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

Před spuštěním playbooku, viz následující poznámky:

- Pro `your_ssh_key` zástupného symbolu, zadejte veřejný klíč RSA v jednořádkovém formátu – začíná na "ssh-rsa" (bez uvozovek).

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook azure_configure_aks.yml
```

Spuštění playbooku zobrazí výsledky podobné následující výstup:

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

Ukázka playbook odstraní AKS cluster.

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

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook azure_delete_aks.yml
```

Spuštění playbooku zobrazí výsledky podobné následující výstup:

```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [azure_rm_aks] 

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Škálování aplikace ve službě Azure Kubernetes Service (AKS)](/azure/aks/tutorial-kubernetes-scale)