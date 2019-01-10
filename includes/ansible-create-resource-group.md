---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 08/09/2018
ms.author: tarcher
ms.openlocfilehash: fe995535cd42571ad96f192883e48f4b6ea1eb0e
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54160399"
---
1. Ve Cloud Shellu vytvořte soubor s názvem `rg.yml`.

    ```bash
    vi rg.yml
    ```

1. Stisknutím klávesy **I** přejděte do režimu vkládání.

1. Do editoru vložte následující kód:

   ```yaml
   ---
   - hosts: localhost
     connection: local
     tasks:
       - name: Create resource group
         azure_rm_resourcegroup:
           name: ansible-rg
           location: eastus
         register: rg
       - debug:
           var: rg
   ```

1. Stisknutím klávesy **Esc** ukončete režim vkládání.

1. Uložte soubor a zadáním následujícího příkazu ukončete editor vi:

    ```bash
    :wq
    ```

1. Spuštění playbooku `rg.yml`:

   ```bash
   ansible-playbook rg.yml
   ```

Výsledky spuštění ansible příkaz by měl vypadat podobně jako následující výstup:

```output
PLAY [localhost] *********************************************************************************

TASK [Gathering Facts] ***************************************************************************
ok: [localhost]

TASK [Create resource group] *********************************************************************
changed: [localhost]

TASK [debug] *************************************************************************************
ok: [localhost] => {
    "rg": {
        "changed": true,
        "contains_resources": false,
        "failed": false,
        "state": {
            "id": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/ansible-rg",
            "location": "eastus",
            "name": "ansible-rg",
            "provisioning_state": "Succeeded",
            "tags": null
        }
    }
}

PLAY RECAP ***************************************************************************************
localhost                  : ok=3    changed=1    unreachable=0    failed=0
```