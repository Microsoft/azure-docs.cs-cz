---
title: Kurz – konfigurace služby Virtual Machine Scale Sets v Azure pomocí Ansible
description: Naučte se používat Ansible k vytváření a konfiguraci služby Virtual Machine Scale Sets v Azure.
keywords: Ansible, Azure, DevOps, bash, PlayBook, virtuální počítač, sada škálování virtuálních počítačů, VMSS
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 1d9b8cd207596aefa01af852627f11cb9b4ce5dc
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241731"
---
# <a name="tutorial-configure-virtual-machine-scale-sets-in-azure-using-ansible"></a>Kurz: Konfigurace služby Virtual Machine Scale Sets v Azure pomocí Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Konfigurace prostředků pro virtuální počítač
> * Konfigurace sady škálování
> * Škálujte škálu nastavenou zvýšením instancí virtuálních počítačů. 

## <a name="prerequisites"></a>Požadované součásti

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-a-scale-set"></a>Konfigurace sady škálování

PlayBook kód v této části definuje následující zdroje:

* **Skupina prostředků** , do které budou nasazeny všechny prostředky.
* **Virtuální síť** v adresním prostoru 10.0.0.0/16
* **Podsíť** v rámci virtuální sítě
* **Veřejná IP adresa** , která umožňuje přístup k prostředkům přes Internet
* **Skupina zabezpečení sítě** , která řídí tok síťového provozu v rámci sady škálování a mimo ni
* **Nástroj pro vyrovnávání zatížení** , který distribuuje provoz napříč sadou definovaných virtuálních počítačů pomocí pravidel nástroje pro vyrovnávání zatížení
* **Sada škálování virtuálních počítačů** , která používá všechny vytvořené prostředky

Existují dva způsoby, jak získat ukázkovou PlayBook:

* [Stáhněte si PlayBook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml) a uložte ho do `vmss-create.yml`.
* Vytvořte nový soubor s názvem `vmss-create.yml` a zkopírujte do něj následující obsah:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    vmss_lb_name: myScaleSetLb
    location: eastus
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        address_prefixes: "10.0.0.0/16"
    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ vmss_name }}"
    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
    - name: Create Network Security Group that allows SSH
      azure_rm_securitygroup:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        rules:
          - name: SSH
            protocol: Tcp
            destination_port_range: 22
            access: Allow
            priority: 1001
            direction: Inbound

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_lb_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 8080
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 8080
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create Scale Set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vmss_name }}"
        subnet_name: "{{ vmss_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
        load_balancer: "{{ vmss_lb_name }}"
        data_disks:
          - lun: 0
            disk_size_gb: 20
            managed_disk_type: Standard_LRS
            caching: ReadOnly
          - lun: 1
            disk_size_gb: 30
            managed_disk_type: Standard_LRS
            caching: ReadOnly
```

Před spuštěním PlayBook se podívejte na následující poznámky:

* V části `vars` nahraďte zástupný text `{{ admin_password }}` vlastním heslem.

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook vmss-create.yml
```

Po spuštění PlayBook se zobrazí výstup podobný následujícímu výsledku:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Create public IP address] 
changed: [localhost]

TASK [Create Network Security Group that allows SSH] 
changed: [localhost]

TASK [Create a load balancer] 
changed: [localhost]

TASK [Create Scale Set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=8    changed=7    unreachable=0    failed=0

```

## <a name="view-the-number-of-vm-instances"></a>Zobrazit počet instancí virtuálních počítačů

[Nakonfigurovaná sada škálování](#configure-a-scale-set) má v současné době dvě instance. K potvrzení této hodnoty slouží následující postup:

1. Přihlaste se k [portálu Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Přejděte do nastavené škály, kterou jste nakonfigurovali.

1. V závorkách se zobrazí název sady škálování s počtem instancí: `Standard_DS1_v2 (2 instances)`

1. Počet instancí s [Azure Cloud Shell](https://shell.azure.com/) můžete také ověřit spuštěním následujícího příkazu:

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    Výsledky spuštění příkazu Azure CLI v Cloud Shell ukazují, že existují tři instance: 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="scale-out-a-scale-set"></a>Horizontální navýšení kapacity sady škálování

PlayBook kód v této části načte informace o sadě škálování a změní kapacitu ze dvou na tři.

Existují dva způsoby, jak získat ukázkovou PlayBook:

* [Stáhněte si PlayBook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml) a uložte ho do `vmss-scale-out.yml`.
* Vytvořte nový soubor s názvem `vmss-scale-out.yml` a zkopírujte do něj následující obsah:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
  tasks: 
    - name: Get scaleset info
      azure_rm_virtualmachine_scaleset_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        format: curated
      register: output_scaleset

    - name: Dump scaleset info
      debug:
        var: output_scaleset

    - name: Modify scaleset (change the capacity to 3)
      set_fact:
        body: "{{ output_scaleset.ansible_facts.azure_vmss[0] | combine({'capacity': 3}, recursive=True) }}"

    - name: Update something in that scale set
      azure_rm_virtualmachinescaleset: "{{ body }}"
```

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook vmss-scale-out.yml
```

Po spuštění PlayBook se zobrazí výstup podobný následujícímu výsledku:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get scaleset info] 
ok: [localhost]

TASK [Dump scaleset info] 
ok: [localhost] => {
    "output_scaleset": {
        "ansible_facts": {
            "azure_vmss": [
                {
                    ......
                }
            ]
        },
        "changed": false,
        "failed": false
    }
}

TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] 
ok: [localhost]

TASK [Update something in that scale set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=5    changed=1    unreachable=0    failed=0
```

## <a name="verify-the-results"></a>Ověřit výsledky

Pomocí Azure Portal Ověřte výsledky své práce:

1. Přihlaste se k [portálu Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Přejděte do nastavené škály, kterou jste nakonfigurovali.

1. V závorkách se zobrazí název sady škálování s počtem instancí: `Standard_DS1_v2 (3 instances)` 

1. Změnu pomocí [Azure Cloud Shell](https://shell.azure.com/) můžete také ověřit spuštěním následujícího příkazu:

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    Výsledky spuštění příkazu Azure CLI v Cloud Shell ukazují, že existují tři instance: 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Kurz: nasazení aplikací do služby Virtual Machine Scale Sets v Azure pomocí Ansible](./ansible-deploy-app-vmss.md)