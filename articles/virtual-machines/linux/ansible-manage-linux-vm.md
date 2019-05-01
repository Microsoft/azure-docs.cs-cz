---
title: Rychlý start – Správa virtuálních počítačů s Linuxem v Azure pomocí Ansible | Dokumentace Microsoftu
description: V tomto rychlém startu zjistěte, jak spravovat virtuální počítač s Linuxem v Azure pomocí Ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: cab179980f6093bf259556fd690e55c99a817c79
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717552"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>Rychlý start: Správa virtuálních počítačů s Linuxem v Azure pomocí Ansible

Ansible umožňuje automatizovat nasazování a konfiguraci prostředků ve vašem prostředí. V tomto článku použijete Ansible playbook spuštění a zastavení virtuálního počítače s Linuxem. 

## <a name="prerequisites"></a>Požadavky

- [!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>Zastavit virtuální počítač

V této části pomocí Ansible zrušit přidělení virtuálního počítače Azure (Zastavit).

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otevřete [Cloud Shell](/azure/cloud-shell/overview).

1. Vytvořte soubor s názvem `azure-vm-stop.yml`a otevřete ho v editoru:

    ```azurecli-interactive
    code azure-vm-stop.yml
    ```

1. Vložte do editoru následující vzorový kód:

    ```yaml
    - name: Stop Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Stop virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
            allocated: no
    ```

1. Nahradit `{{ resource_group_name }}` a `{{ vm_name }}` zástupné symboly vašimi hodnotami.

1. Uložte soubor a ukončete editor.

1. Spuštění playbooku pomocí `ansible-playbook` příkaz:

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. Po spuštění playbooku, se zobrazí výstup podobný následující výsledky:

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="start-a-virtual-machine"></a>Spuštění virtuálního počítače

V této části pomocí Ansible spustí uvolnění (zastavené) Azure virtuální počítač.

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otevřete [Cloud Shell](/azure/cloud-shell/overview).

1. Vytvořte soubor s názvem `azure-vm-start.yml`a otevřete ho v editoru:

    ```azurecli-interactive
    code azure-vm-start.yml
    ```

1. Vložte do editoru následující vzorový kód:

    ```yaml
    - name: Start Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Start virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
    ```

1. Nahradit `{{ resource_group_name }}` a `{{ vm_name }}` zástupné symboly vašimi hodnotami.

1. Uložte soubor a ukončete editor.

1. Spuštění playbooku pomocí `ansible-playbook` příkaz:

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. Po spuštění playbooku, se zobrazí výstup podobný následující výsledky:

    ```bash
    PLAY [Start Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"] 
> [Kurz: Správa Azure dynamického inventáře pomocí Ansible](~/articles/ansible/ansible-manage-azure-dynamic-inventories.md)