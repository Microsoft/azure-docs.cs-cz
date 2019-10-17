---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 07cae1cee9810646de5bf9610a29991376736373
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391599"
---
K této chybě může dojít, když je služba Azure File Sync ze serveru nepřístupná. Tuto chybu můžete vyřešit pomocí následujících kroků:

1. Ověřte, že brána firewall neblokuje službu Windows `FileSyncSvc.exe`.
2. Ověřte, že je port 443 otevřený pro odchozí připojení ke službě Azure File Sync. Můžete to provést pomocí rutiny `Test-NetConnection`. Adresa URL zástupného znaku `<azure-file-sync-endpoint>` uvedená níže se může najít v dokumentu [Azure File Sync proxy serveru a nastavení brány firewall](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) . 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Ujistěte se, že konfigurace proxy serveru je nastavená jako očekávaná. To lze provést pomocí rutiny `Get-StorageSyncProxyConfiguration`. Další informace o konfiguraci proxy serveru pro Azure File Sync najdete v [nastavení proxy serveru Azure File Sync a brány firewall](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
    
4. Pokud potřebujete další pomoc při řešení potíží s připojením k síti, obraťte se na správce sítě.