---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 5e912b76c2ef68aa01dae57d1b42abc386a8e67b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89482117"
---
1. Pomocí `az group create` příkazu vytvořte skupinu prostředků:

    ```azurecli
    az group create --name KeyVault-PythonQS-rg --location eastus
    ```

    Pokud dáváte přednost, můžete změnit "eastus" na místo poblíž.

1. Použijte `az keyvault create` k vytvoření trezoru klíčů:

    ```azurecli
    az keyvault create --name <your-unique-keyvault-name> --resource-group KeyVault-PythonQS-rg
    ```

    Nahraďte `<your-unique-keyvault-name>` názvem, který je jedinečný v rámci všech Azure. Obvykle používáte svůj osobní nebo název společnosti společně s dalšími čísly a identifikátory. 

1. Vytvořte proměnnou prostředí, která poskytuje název Key Vault kódu:

    # <a name="cmd"></a>[přepsat](#tab/cmd)

    ```cmd
    set KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    ---
