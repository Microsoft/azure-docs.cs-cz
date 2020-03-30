---
title: Úvodní příručka – Správa linuxových virtuálních počítačů v Azure pomocí Ansible
description: V tomto rychlém startu se dozvíte, jak spravovat virtuální počítač SIP v Azure pomocí
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: d94858391951aaf9387394afeb5ad2ae373fa7b5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239537"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>Úvodní příručka: Správa linuxových virtuálních počítačů v Azure pomocí Ansible

Ansible umožňuje automatizovat nasazování a konfiguraci prostředků ve vašem prostředí. V tomto článku použijete playbook Ansible ke spuštění a zastavení virtuálního počítače Linux. 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>Zastavení virtuálního počítače

V této části použijete Ansible navrátit (zastavit) virtuální počítač Azure.

1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otevřete [prostředí Cloud Shell](/azure/cloud-shell/overview).

1. Vytvořte soubor `azure-vm-stop.yml`s názvem a otevřete jej v editoru:

    ```bash
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

1. `{{ resource_group_name }}` Nahraďte `{{ vm_name }}` zástupné symboly a svými hodnotami.

1. Uložte tento soubor a ukončete editor.

1. Spusťte playbook `ansible-playbook` pomocí příkazu:

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. Po spuštění playbooku se zobrazí výstup podobný následujícím výsledkům:

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

V této části použijete Ansible ke spuštění přiděleného (zastaveného) virtuálního počítače Azure.

1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otevřete [prostředí Cloud Shell](/azure/cloud-shell/overview).

1. Vytvořte soubor `azure-vm-start.yml`s názvem a otevřete jej v editoru:

    ```bash
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

1. `{{ resource_group_name }}` Nahraďte `{{ vm_name }}` zástupné symboly a svými hodnotami.

1. Uložte tento soubor a ukončete editor.

1. Spusťte playbook `ansible-playbook` pomocí příkazu:

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. Po spuštění playbooku se zobrazí výstup podobný následujícím výsledkům:

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