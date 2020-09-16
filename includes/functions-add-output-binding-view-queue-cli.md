---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 894a89126d1ee3ed909134f3e0dd914166568654
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90606869"
---
Tuto frontu můžete zobrazit v [Azure Portal](../articles/storage/queues/storage-quickstart-queues-portal.md) nebo v  [Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com/). Tuto frontu můžete také zobrazit v rozhraní příkazového řádku Azure CLI, jak je popsáno v následujících krocích:

1. Otevřetelocal.setting.jsprojektu funkce * na* soubor a zkopírujte hodnotu připojovacího řetězce. V terminálu nebo okně příkazového řádku spusťte následující příkaz, který vytvoří proměnnou prostředí s názvem `AZURE_STORAGE_CONNECTION_STRING` a místo ní bude vkládat konkrétní připojovací řetězec  `<MY_CONNECTION_STRING>` . (Tato proměnná prostředí znamená, že nemusíte zadávat připojovací řetězec ke každému následujícímu příkazu pomocí `--connection-string` argumentu.)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    export AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
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
    
1. Volitelné Pomocí [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) příkazu Zobrazte ve svém účtu fronty úložiště. Výstup z tohoto příkazu by měl zahrnovat frontu s názvem `outqueue` , která byla vytvořena při zapsání první zprávy do této fronty.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. Pomocí [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) příkazu si přečtěte zprávu z této fronty, která by měla být křestní jméno, které jste použili při předchozím testování funkce. Příkaz přečte a Odebere první zprávu z fronty. 

    # <a name="bash"></a>[bash](#tab/bash)
    
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

    Tento skript používá příkaz certutil k dekódování kolekce zpráv s kódováním base64 z místního dočasného souboru. Pokud není výstup, zkuste `> NUL` z skriptu odebrat, aby se zastavil výstup příkazu certutil, a to v případě, že dojde k chybě. 
    
    ---
    
    Vzhledem k tomu, že tělo zprávy je uložené v kódování [Base64](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding), je nutné zprávu dekódovat předtím, než se zobrazí. Po spuštění bude `az storage message get` zpráva odebrána z fronty. Pokud v nástroji existovala jenom jedna zpráva `outqueue` , při spuštění tohoto příkazu se nezobrazí zpráva a místo toho se zobrazí chyba.
