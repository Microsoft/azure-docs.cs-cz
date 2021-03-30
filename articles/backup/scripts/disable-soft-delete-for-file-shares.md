---
title: Ukázka skriptu – zakázání obnovitelného odstranění pro sdílenou složku
description: Naučte se, jak pomocí skriptu zakázat obnovitelné odstranění sdílených složek v účtu úložiště.
ms.topic: sample
ms.date: 02/02/2020
ms.openlocfilehash: 172a7bd19ffd07ca7900a457c447362343e5f185
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "84122794"
---
# <a name="disable-soft-delete-for-file-shares-in-a-storage-account"></a>Zakázat obnovitelné odstranění pro sdílené složky v účtu úložiště

Tento dokument vysvětluje postup, jak v účtu úložiště zakázat obnovitelné odstranění sdílených složek.

Postupujte takto:

1. Nainstalujte armclient. Informace o tom, jak ho nainstalovat, najdete na [tomto odkazu](https://github.com/projectkudu/ARMClient).

2. Uložte následující dva soubory textu žádosti do složky na vašem počítači.

    ```json
    rqbody-enableSoftDelete.json

    {
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled":true,
        "days": 14
        }
    },
    "cors": {
        "corsRules": []
    }
    }

    rqbody-disableSoftDelete.json

    {
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled":false,
        "days": 0
        }
    },
    "cors": {
        "corsRules": []
    }
    }
    ```

3. Udržujte si ID Azure Resource Manager účtu úložiště (ARM) užitečné. Příklad: `/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare/providers/Microsoft.Storage/storageAccounts/inquirytest`

4. Přihlaste se pomocí přihlašovacích údajů spuštěním **armclient přihlášení**.

5. Získejte aktuální vlastnosti obnovitelného odstranění sdílených složek v účtu úložiště.

    Následující operace GET načte vlastnosti obnovitelného odstranění pro sdílené složky v účtu *inquirytest* :

    ```cmd
    armclient get /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01
    ```

    ```output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/de
    fault",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "cors": {
        "corsRules": []
        },
        "shareDeleteRetentionPolicy": {
        "enabled": true,
        "days": 14
        }
    }
    }
    ```

6. Zakažte obnovitelné odstranění pro sdílené složky v účtu úložiště.

    Následující operace vložení zakáže vlastnosti obnovitelného odstranění pro sdílené složky v účtu *inquirytest* :

    ```cmd
    armclient put /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01 .\rqbody-disableSoftDelete.json
    ```

    ```Output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/de
    fault",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled": false,
        "days": 0
        }
    }
    }
    ```

7. Pokud chcete znovu povolit obnovitelné odstranění, použijte následující ukázku.

    Následující operace PUT povoluje vlastnosti obnovitelného odstranění sdílených složek v účtu "inquirytest".

    ```cmd
    armclient put /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01 .\rqbody-EnableSoftDelete.json
    ```

    ```Output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled": true,
        "days": 14
        }
    }
    }
    ```
