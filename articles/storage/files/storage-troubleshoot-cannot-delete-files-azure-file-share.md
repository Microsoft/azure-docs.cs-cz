---
title: Sdílená složka Azure – Nepodařilo se odstranit soubory ze sdílené složky Azure
description: Identifikujte a odstraňte potíže při odstraňování souborů ze sdílené složky Azure.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 3d4f10745d90ccd83e7251af40d3e92a230f2fcd
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629678"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Sdílená složka Azure – Nepodařilo se odstranit soubory ze sdílené složky Azure

Chyba při odstraňování souborů ze sdílené složky Azure může mít několik příznaků:

**Příznak 1:**

Nepovedlo se odstranit soubor ve sdílené složce Azure kvůli jednomu z následujících dvou problémů:

* Soubor označený k odstranění
* Zadaný prostředek může být používán klientem SMB.

**Příznak 2:**

K zpracování tohoto příkazu není k dispozici dostatečná kvóta.

## <a name="cause"></a>Příčina

K chybě 1816 dochází, když se dosáhne horní meze souběžných otevřených popisovačů povolených pro určitý soubor v počítači, kam se sdílená složka sdílí. Další informace najdete v tématu [Kontrolní seznam pro výkon a škálovatelnost Azure Storage](../blobs/storage-performance-checklist.md).

## <a name="resolution"></a>Řešení

Snižte počet souběžných otevřených popisovačů uzavřením některých popisovačů.

## <a name="prerequisite"></a>Požadavek

### <a name="install-the-latest-azure-powershell-module"></a>Nainstalovat nejnovější modul Azure PowerShell

* [Instalace modulu Azure PowerShellu](/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Připojte se k Azure:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Vyberte předplatné cílového účtu úložiště:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Vytvořit kontext pro cílový účet úložiště:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>Získat aktuální otevřené popisovače sdílené složky:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Příklad výsledku:

|HandleId|Cesta|ClientIp|ClientPort|OpenTime|LastReconnectTime|Identifikátor|ParentId|SessionId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|Nová složka/test.zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|test.zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Zavřít otevřený popisovač:

Chcete-li zavřít otevřený popisovač, použijte následující příkaz:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>Další kroky

* [Řešení potíží se soubory Azure v systému Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Řešení potíží se soubory Azure v systému Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Řešit problémy se Synchronizací souborů Azure](storage-sync-files-troubleshoot.md)