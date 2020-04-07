---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 44823ce888e97b308f29403612f598c0eb585ae5
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673373"
---
Frontu můžete zobrazit na [webu Azure Portal](../articles/storage/queues/storage-quickstart-queues-portal.md) nebo v [Průzkumníkovi úložiště Microsoft Azure](https://storageexplorer.com/). Frontu můžete také zobrazit v příkazovém příkazu k dispozici azure, jak je popsáno v následujících krocích:

1. Otevřete soubor *local.setting.json* projektu funkce a zkopírujte hodnotu připojovacího řetězce. V terminálu nebo příkazovém okně spusťte `AZURE_STORAGE_CONNECTION_STRING`následující příkaz a vytvořte proměnnou prostředí s názvem a místo ní vložte konkrétní připojovací řetězec `<MY_CONNECTION_STRING>`. (Tato proměnná prostředí znamená, že není nutné zadávat připojovací řetězec ke každému následnému příkazu pomocí argumentu.) `--connection-string`

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (Nepovinné) Pomocí [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) příkazu můžete zobrazit fronty úložiště ve vašem účtu. Výstup z tohoto příkazu by `outqueue`měl obsahovat frontu s názvem , která byla vytvořena, když funkce napsala svou první zprávu do této fronty.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. Pomocí [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) příkazu přečtěte zprávu z této fronty, která by měla být křestním jménem, které jste použili při testování funkce dříve. Příkaz přečte a odebere první zprávu z fronty. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Tento skript používá certutil k dekódování kolekce zpráv kódované base64 z místního dočasného souboru. Pokud neexistuje žádný výstup, `> NUL` zkuste odebrat ze skriptu zastavit potlačení certutil výstup, v případě, že dojde k chybě. 
    
    ---
    
    Vzhledem k tomu, že text zprávy je uložen [base64 kódované](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding), musí být zpráva dekódována před zobrazením. Po spuštění `az storage message get`bude zpráva odebrána z fronty. Pokud v aplikaci `outqueue`byla pouze jedna zpráva , nenačtete zprávu při druhém spuštění tohoto příkazu a místo toho se zobrazí chyba.