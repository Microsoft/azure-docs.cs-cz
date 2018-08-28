---
title: Použití Ansible ke správě virtuálního počítače s Linuxem v Azure
description: Zjistěte, jak pomocí Ansible spravovat virtuální počítač s Linuxem v Azure.
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/21/2018
ms.openlocfilehash: 66106346b298fae22cce47081916a6c8eec8fd40
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "40250649"
---
# <a name="use-ansible-to-manage-a-linux-virtual-machine-in-azure"></a>Použití Ansible ke správě virtuálního počítače s Linuxem v Azure
Ansible umožňuje automatizovat nasazování a konfiguraci prostředků ve vašem prostředí. Pomocí Ansible můžete spravovat virtuální počítače Azure stejně jako jakékoli jiné prostředky. V tomto článku se dozvíte, jak pomocí playbooku Ansible spustit a zastavit virtuální počítač s Linuxem. 

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure** – Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Nakonfigurujte Azure Cloud Shell** nebo **Nainstalujte a nakonfigurujte Ansible na virtuálním počítači s Linuxem**.

  **Konfigurace služby Azure Cloud Shell**

  1. **Konfigurace služby Azure Cloud Shell** – Pokud se službou Azure Cloud Shell teprve začínáte, v článku [Rychlý start pro Bash ve službě Azure Cloud Shell](/azure/cloud-shell/quickstart) se dozvíte, jak spustit a nakonfigurovat Cloud Shell. 

  1. **Virtuální počítač s Linuxem** – Pokud nemáte přístup k virtuálnímu počítači s Linuxem, můžete si [vytvořit virtuální počítač pomocí Ansible](ansible-create-vm.md).

  **-- nebo --**

  **Instalace a konfigurace Ansible na virtuálním počítači s Linuxem**

  1. **Instalace Ansible** –Nainstalujte Ansible na [podporovanou platformu Linux](/azure/virtual-machines/linux/ansible-install-configure#install-ansible-on-an-azure-linux-virtual-machine).

  1. **Konfigurace Ansible** - [Vytvoření přihlašovacích údajů Azure a konfigurace Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="use-ansible-to-deallocate-stop-an-azure-virtual-machine"></a>Použití Ansible k uvolnění (zastavení) virtuálního počítače Azure
Tato část ukazuje, jak pomocí Ansible uvolnit (zastavit) virtuální počítač Azure.

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otevřete [Cloud Shell](/azure/cloud-shell/overview).

1. Následujícím způsobem vytvořte soubor `azure_vm_stop.yml`, který bude obsahovat váš playbook, a otevřete ho v editoru VI:

  ```azurecli-interactive
  vi azure_vm_stop.yml
  ```

1. Stisknutím klávesy **I** přejděte do režimu vkládání.

1. Vložte do editoru následující vzorový kód:

    ```yaml
    - name: Stop Azure VM
    hosts: localhost
    connection: local
    tasks:
    - name: Deallocate the virtual machine
        azure_rm_virtualmachine:
            resource_group: myResourceGroup
            name: myVM
            allocated: no 
    ```

1. Stisknutím klávesy **Esc** ukončete režim vkládání.

1. Uložte soubor a zadáním následujícího příkazu ukončete editor vi:

    ```bash
    :wq
    ```

1. Spusťte ukázkový playbook Ansible.

  ```bash
  ansible-playbook azure_vm_stop.yml
  ```

1. Výstup bude vypadat podobně jako v následujícím příkladu, který ukazuje úspěšné uvolnění (zastavení) virtuálního počítače:

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="use-ansible-to-start-a-deallocated-stopped-azure-virtual-machine"></a>Použití Ansible ke spuštění uvolněného (zastaveného) virtuálního počítače Azure
Tato část ukazuje, jak pomocí Ansible spustit uvolněný (zastavený) virtuální počítač Azure.

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otevřete [Cloud Shell](/azure/cloud-shell/overview).

1. Následujícím způsobem vytvořte soubor `azure_vm_start.yml`, který bude obsahovat váš playbook, a otevřete ho v editoru VI:

  ```azurecli-interactive
  vi azure_vm_start.yml
  ```

1. Stisknutím klávesy **I** přejděte do režimu vkládání.

1. Vložte do editoru následující vzorový kód:

    ```yaml
    - name: Start Azure VM
    hosts: localhost
    connection: local
    tasks:
    - name: Start the virtual machine
        azure_rm_virtualmachine:
            resource_group: myResourceGroup
            name: myVM
    ```

1. Stisknutím klávesy **Esc** ukončete režim vkládání.

1. Uložte soubor a zadáním následujícího příkazu ukončete editor vi:

    ```bash
    :wq
    ```

1. Spusťte ukázkový playbook Ansible.

  ```bash
  ansible-playbook azure_vm_start.yml
  ```

1. Výstup bude vypadat podobně jako v následujícím příkladu, který ukazuje úspěšné spuštění virtuálního počítače:

    Výstup bude vypadat podobně jako v následujícím příkladu, který ukazuje úspěšné spuštění virtuálního počítače:

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"] 
> [Použití Ansible ke správě dynamického inventáře Azure](../../ansible/ansible-manage-azure-dynamic-inventories.md)