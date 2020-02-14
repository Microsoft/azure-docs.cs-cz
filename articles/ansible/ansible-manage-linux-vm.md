---
title: Rychlý Start – Správa virtuálních počítačů se systémem Linux v Azure pomocí Ansible
description: V tomto rychlém startu se dozvíte, jak spravovat virtuální počítač se systémem Linux v Azure pomocí Ansible.
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 90113373535c835e5cddf707bcb520789e596aec
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202400"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>Rychlý Start: Správa virtuálních počítačů se systémem Linux v Azure pomocí Ansible

Ansible umožňuje automatizovat nasazování a konfiguraci prostředků ve vašem prostředí. V tomto článku použijete Ansible PlayBook ke spuštění a zastavení virtuálního počítače se systémem Linux. 

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>Zastavení virtuálního počítače

V této části použijete Ansible k navrácení (zastavení) virtuálního počítače Azure.

1. Přihlaste se k webu [Portál Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

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

1. Nahraďte `{{ resource_group_name }}` a zástupné symboly `{{ vm_name }}` hodnotami.

1. Uložte soubor a ukončete Editor.

1. Spusťte PlayBook pomocí příkazu `ansible-playbook`:

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. Po spuštění PlayBook se zobrazí výstup podobný následujícímu výsledku:

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

V této části použijete Ansible ke spuštění navráceného (zastaveného) virtuálního počítače Azure.

1. Přihlaste se k webu [Portál Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

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

1. Nahraďte `{{ resource_group_name }}` a zástupné symboly `{{ vm_name }}` hodnotami.

1. Uložte soubor a ukončete Editor.

1. Spusťte PlayBook pomocí příkazu `ansible-playbook`:

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. Po spuštění PlayBook se zobrazí výstup podobný následujícímu výsledku:

    ```bash
    PLAY [Start Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Kurz: Správa dynamických inventářů Azure pomocí Ansible](./ansible-manage-azure-dynamic-inventories.md)