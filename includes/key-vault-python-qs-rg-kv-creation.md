---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 59359d37fe347c8568c7944f75accdbc04cddb93
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967114"
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

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```