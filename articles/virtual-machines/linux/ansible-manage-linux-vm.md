---
title: Rychlý start – Správa virtuálních počítačů s Linuxem v Azure pomocí Ansible | Dokumentace Microsoftu
description: V tomto rychlém startu zjistěte, jak spravovat virtuální počítač s Linuxem v Azure pomocí Ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: c4878902425a26086ad77647ea06568f2110ccfe
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668619"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>Rychlý start: Správa virtuálních počítačů s Linuxem v Azure pomocí Ansible

Ansible umožňuje automatizovat nasazování a konfiguraci prostředků ve vašem prostředí. V tomto článku použijete Ansible playbook spuštění a zastavení virtuálního počítače s Linuxem. 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

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