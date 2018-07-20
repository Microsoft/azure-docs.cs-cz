---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 754562487f0fe9f825107445a3059cc15a1faa26
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39146232"
---
Této chybě může dojít, když není přístupný ze serveru pro službu Azure File Sync. Odstranění této chyby z práce prostřednictvím následujících kroků:

1. Ověření služby Windows `FileSyncSvc.exe` není blokován bránou firewall.
2. Ověřte, zda je port 443 otevřený pro odchozí připojení ke službě Azure File Sync. Můžete to provedete `Test-NetConnection` rutiny. Adresa URL `<azure-file-sync-endpoint>` zástupný symbol níže lze nalézt v [nastavení proxy serveru a brány firewall Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) dokumentu. 

    ```PowerShell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Ujistěte se, že je nastavena konfigurace proxy serveru, podle očekávání. To lze provést pomocí `Get-StorageSyncProxyConfiguration` rutiny. Další informace o konfiguraci konfiguraci proxy serveru pro Azure File Sync najdete v [nastavení proxy serveru a brány firewall Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```PowerShell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
    
4. Obraťte se na správce sítě o další pomoc, řešení potíží s připojení k síti.