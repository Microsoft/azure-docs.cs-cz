---
title: Vytváření a konfigurace clusterů Azure Kubernetes Service v Azure pomocí Ansible
description: Zjistěte, jak pomocí Ansible vytvořit a spravovat cluster Azure Kubernetes Service v Azure.
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, container, Kubernetes
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/21/2018
ms.openlocfilehash: de692b29902145e44a055680d662c16ed90c56c2
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617171"
---
# <a name="create-and-configure-azure-kubernetes-service-clusters-in-azure-using-ansible"></a>Vytváření a konfigurace clusterů Azure Kubernetes Service v Azure pomocí Ansible
Ansible umožňuje automatizovat nasazování a konfiguraci prostředků ve vašem prostředí. Pomocí Ansible můžete spravovat službu Azure Kubernetes Service (AKS). V tomto článku se dozvíte, jak pomocí Ansible vytvořit a nakonfigurovat cluster Azure Kubernetes Service.

## <a name="prerequisites"></a>Požadavky
- **Předplatné Azure** – Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- **Instanční objekt Azure** – Při [vytváření instančního objektu](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal) si poznamenejte následující hodnoty: **appId**, **displayName**, **password** a **tenant**.

- **Nakonfigurujte Azure Cloud Shell** nebo **Nainstalujte a nakonfigurujte Ansible na virtuálním počítači s Linuxem**.

  **Konfigurace služby Azure Cloud Shell**

  1. **Konfigurace služby Azure Cloud Shell** – Pokud se službou Azure Cloud Shell teprve začínáte, v článku [Rychlý start pro Bash ve službě Azure Cloud Shell](/azure/cloud-shell/quickstart) se dozvíte, jak spustit a nakonfigurovat Cloud Shell. 

  **-- nebo --**

  **Instalace a konfigurace Ansible na virtuálním počítači s Linuxem**

  1. **Instalace Ansible** –Nainstalujte Ansible na [podporovanou platformu Linux](/azure/virtual-machines/linux/ansible-install-configure#install-ansible-on-an-azure-linux-virtual-machine).

  1. **Konfigurace Ansible** - [Vytvoření přihlašovacích údajů Azure a konfigurace Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

> [!Note]
> Ke spuštění následujících ukázkových playbooků v tomto kurzu se vyžaduje Ansible 2.6. 

## <a name="create-a-managed-aks-cluster"></a>Vytvoření spravovaného clusteru AKS
Následující ukázkový playbook Ansible vytvoří skupinu prostředků a v ní cluster AKS:

  ```yaml
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

Následující odrážky pomáhají vysvětlit předchozí kód playbooku Ansible:
- V první části bloku **tasks** se definuje skupina prostředků **myResourceGroup** v umístění **eastus**. 
- V druhé části bloku **tasks** se definuje cluster AKS **myAKSCluster** ve skupině prostředků **myResourceGroup**. 

Pokud chcete vytvořit cluster AKS pomocí Ansible, uložte předchozí ukázkový playbook jako `azure_create_aks.yml` a spusťte ho pomocí následujícího příkazu:

  ```bash
  ansible-playbook azure_create_aks.yml
  ```

Výstup příkazu *ansible-playbook* bude vypadat podobně jako v následujícím příkladu, který ukazuje úspěšné vytvoření clusteru AKS:

  ```bash
  PLAY [Create AKS] ****************************************************************************************

  TASK [Gathering Facts] ********************************************************************************************
  ok: [localhost]

  TASK [Create resource group] **************************************************************************************
  changed: [localhost]

  TASK [Create a Azure Container Services (AKS) cluster] ***************************************************
  changed: [localhost]

  PLAY RECAP *********************************************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0
  ```

## <a name="scale-aks-nodes"></a>Škálování uzlů AKS

Ukázkový playbook v předchozí části definuje dva uzly. Pokud ve vašem clusteru potřebujete více nebo méně úloh kontejneru, můžete počet uzlů snadno upravit. Ukázkový playbook v této části zvýší počet uzlů ze dvou na tři. Změna počtu uzlů se provádí úpravou hodnoty **count** v bloku **agent_pool_profiles**. 

V bloku **service_principal** zadejte vlastní hodnoty `ssh_key`, `client_id` a `client_secret`:

```yaml
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

Pokud chcete škálovat cluster Azure Kubernetes Service pomocí Ansible, uložte předchozí playbook jako *azure_configure_aks.yml* a spusťte ho následujícím způsobem:

  ```bash
  ansible-playbook azure_configure_aks.yml
  ```

Následující výstup ukazuje úspěšné vytvoření clusteru AKS:

  ```bash
  PLAY [Scale AKS cluster] ***************************************************************

  TASK [Gathering Facts] ******************************************************************
  ok: [localhost]

  TASK [Scaling an existed AKS cluster] **************************************************
  changed: [localhost]

  PLAY RECAP ******************************************************************************
  localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```
## <a name="delete-a-managed-aks-cluster"></a>Odstranění spravovaného clusteru AKS

Následující část ukázkového playbooku Ansible ukazuje, jak odstranit cluster AKS:

  ```yaml
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

Pokud chcete odstranit cluster Azure Kubernetes Service pomocí Ansible, uložte předchozí playbook jako *azure_delete_aks.yml* a spusťte ho následujícím způsobem:

  ```bash
  ansible-playbook azure_delete_aks.yml
  ```

Následující výstup ukazuje úspěšné odstranění clusteru AKS:
  ```bash
PLAY [Delete a managed Azure Container Services (AKS) cluster] ****************************

TASK [Gathering Facts] ********************************************************************
ok: [localhost]

TASK [azure_rm_aks] *********************************************************************

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```
  
## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"] 
> [Kurz: Škálování aplikace ve službě Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/tutorial-kubernetes-scale)
