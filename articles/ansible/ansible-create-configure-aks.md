---
title: Kurz – konfigurace clusterů služby Azure Kubernetes Service (AKS) v Azure pomocí ansible
description: Zjistěte, jak pomocí Ansible vytvořit a spravovat cluster Azure Kubernetes Service v Azure.
keywords: ansible, azurové, devops, bash, cloudshell, playbook, aks, kontejner, aks, kubernetes
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 6672c3fac1c5d546a61622e3fd6df6c5397f87a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156682"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>Kurz: Konfigurace clusterů služby Azure Kubernetes Service (AKS) v Azure pomocí ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS lze nakonfigurovat tak, aby používal [azure active directory (AD)](/azure/active-directory/) pro ověřování uživatelů. Po konfiguraci použijete ověřovací token Azure AD k přihlášení do clusteru AKS. RBAC může být založen na identitě uživatele nebo členství ve skupině adresářů.

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

Ukázkový playbook vytvoří skupinu prostředků a cluster AKS v rámci skupiny prostředků.

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

Před spuštěním playbooku se podívejte na následující poznámky:

- První část `tasks` v rámci definuje `myResourceGroup` skupinu `eastus` prostředků pojmenovanou v umístění.
- Druhá část `tasks` v rámci definuje cluster `myAKSCluster` AKS pojmenovaný v rámci skupiny `myResourceGroup` prostředků.
- Pro `your_ssh_key` zástupný symbol zadejte svůj veřejný klíč RSA v jednořádkovém formátu – počínaje "ssh-rsa" (bez uvozovek).
- Pro `aks_version` zástupný symbol použijte příkaz [az aks get-versions.](/cli/azure/aks?view=azure-cli-latest#az-aks-get-versions)

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook azure_create_aks.yml
```

Spuštění playbooku zobrazuje výsledky podobné následujícímu výstupu:

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

Ukázkový playbook v předchozí části definuje dva uzly. Počet uzlů upravíte úpravou `count` hodnoty v `agent_pool_profiles` bloku.

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

Před spuštěním playbooku se podívejte na následující poznámky:

- Pro `your_ssh_key` zástupný symbol zadejte svůj veřejný klíč RSA v jednořádkovém formátu – počínaje "ssh-rsa" (bez uvozovek).

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook azure_configure_aks.yml
```

Spuštění playbooku zobrazuje výsledky podobné následujícímu výstupu:

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

Ukázkový playbook odstraní cluster AKS.

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook azure_delete_aks.yml
```

Spuštění playbooku zobrazuje výsledky podobné následujícímu výstupu:

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