---
title: Vytvoření a konfigurace služby Azure Kubernetes Service clustery v Azure pomocí Ansible
description: Zjistěte, jak použít Ansible k vytvoření a Správa clusteru Azure Kubernetes Service v Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, cloud shell, playbooku, aks, kontejner, Kubernetes
author: tomarcher
manager: jpconnock
editor: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 07/11/2018
ms.author: tarcher
ms.openlocfilehash: 6d7c5f961256e0ae1831bd76353cadd761f4b8ac
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009200"
---
# <a name="create-and-configure-azure-kubernetes-service-clusters-in-azure-using-ansible"></a>Vytvoření a konfigurace služby Azure Kubernetes Service clustery v Azure pomocí Ansible
Ansible umožňuje automatizovat nasazení a konfiguraci prostředků ve vašem prostředí. Ansible můžete použít ke správě Azure Kubernetes Service (AKS). Tento článek ukazuje, jak použít Ansible k vytvoření a konfigurace clusteru služby Azure Kubernetes Service.

## <a name="prerequisites"></a>Požadavky
- **Předplatné Azure** – Pokud nemáte předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) předtím, než začnete.
- **Konfigurace Ansible** - [Azure vytvořit přihlašovací údaje a konfigurace Ansible](../virtual-machines/linux/ansible-install-configure.md#create-azure-credentials)
- **Ansible a moduly sady Azure Python SDK** 
  - [CentOS 7.4](../virtual-machines/linux/ansible-install-configure.md#centos-74)
  - [Ubuntu 16.04 LTS](../virtual-machines/linux/ansible-install-configure.md#ubuntu-1604-lts)
  - [SLES 12 SP2](../virtual-machines/linux/ansible-install-configure.md#sles-12-sp2)
- **Instanční objekt Azure** – když [vytváří se instanční objekt služby](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal), mějte na paměti následující hodnoty: **appId**, **displayName**, **heslo** , a **tenanta**.

> [!Note]
> Ansible 2.6 je potřeba spustit následující playbooky ukázka v tomto kurzu. 

## <a name="create-a-managed-aks-cluster"></a>Vytvoření spravovaného clusteru AKS
Playbook Ansible následující ukázka vytvoří skupinu prostředků a clusteru AKS, který se nachází ve skupině prostředků:

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

Následující odrážky pomoct vysvětlit předchozí kód playbook Ansible:
- První oddíl v rámci **úlohy** definuje skupinu prostředků s názvem **myResourceGroup** v rámci **eastus** umístění. 
- Druhá část v rámci **úlohy** definuje cluster AKS, s názvem **myAKSCluster** v rámci **myResourceGroup** skupinu prostředků. 

K vytvoření clusteru AKS pomocí Ansible, uložit předchozí ukázka playbook jako `azure_create_aks.yml`, a spusťte playbook pomocí následujícího příkazu:

  ```bash
  ansible-playbook azure_create_aks.yml
  ```

Výstup **ansible playbook* příkaz by mohl vypadat podobně jako následující znázorňující, že byly úspěšně vytvoří AKS cluster:

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

Ukázkové scénáře v předchozí části definuje dva uzly. Pokud potřebujete více nebo méně úloh kontejneru v clusteru, můžete snadno upravit počet uzlů. Ukázkové scénáře v této části se zvyšuje počet uzlů ze dvou uzlů na tři. Změna počtu uzlů se provádí tak, že změníte **počet** hodnotu **agent_pool_profiles** bloku. 

Zadejte vlastní `ssh_key`, `client_id`, a `client_secret` v **service_principal** blok:

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

Škálování clusteru Azure Kubernetes Service pomocí Ansible, uložit jako předchozí playbook *azure_configure_aks.yml*, a spusťte playbook následujícím způsobem:

  ```bash
  ansible-playbook azure_configure_aks.yml
  ```

Následující výstup ukazuje, že byly úspěšně vytvoří AKS cluster:

  ```bash
  PLAY [Scale AKS cluster] ***************************************************************

  TASK [Gathering Facts] ******************************************************************
  ok: [localhost]

  TASK [Scaling an existed AKS cluster] **************************************************
  changed: [localhost]

  PLAY RECAP ******************************************************************************
  localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"] 
> [Kurz: Škálování aplikace ve službě Azure Kubernetes Service (AKS)](https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-scale)