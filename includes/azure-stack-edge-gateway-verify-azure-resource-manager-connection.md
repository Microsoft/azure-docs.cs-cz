---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/09/2021
ms.author: alkohli
ms.openlocfilehash: 1298673c475a0308f1db27641aae93837e6b5df3
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305548"
---
Ujistěte se, že se k přístupu k zařízení z klienta dají použít následující kroky.

Ověřte, že se váš klient může připojit k místnímu Azure Resource Manager. 

1. Volat rozhraní API místních zařízení k ověření:

    ```powershell
    login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

1. Zadejte uživatelské jméno `EdgeArmUser` a heslo pro připojení prostřednictvím Azure Resource Manager. Pokud heslo nebudete volat, [resetujte heslo pro Azure Resource Manager](../articles/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password.md) a použijte toto heslo pro přihlášení.