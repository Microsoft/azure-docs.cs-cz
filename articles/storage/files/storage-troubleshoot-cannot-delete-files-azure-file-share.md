---
title: Sdílená složka Azure – Nepodařilo se odstranit soubory ze sdílené složky Azure
description: Identifikujte a vyřešte problémy s odstraněním souborů ze sdílené složky Azure.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: d3a3763a8964810626bcdc47da230a9ee406f1f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196478"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Sdílená složka Azure – Nepodařilo se odstranit soubory ze sdílené složky Azure

Selhání odstranění souborů ze sdílené ho složky Azure může mít několik příznaků:

**Příznak 1:**

Odstranění souboru ve sdílené složce Azure se nezdařilo z důvodu jednoho ze dvou následujících problémů:

* Soubor označený pro odstranění
* Zadaný prostředek může být používán klientem SMB.

**Příznak 2:**

Pro zpracování tohoto příkazu není k dispozici dostatečná kvóta.

## <a name="cause"></a>Příčina

Chyba 1816 nastane, když dosáhnete horní hranice souběžných otevřených popisovačů povolených pro soubor v počítači, kde je sdílená složka připojena. Další informace najdete v tématu [Azure Storage výkon a škálovatelnost kontrolní seznam](https://docs.microsoft.com/azure/storage/blobs/storage-performance-checklist).

## <a name="resolution"></a>Řešení

Snižte počet souběžných otevřených popisovačů zavřením některých popisovačů.

## <a name="prerequisite"></a>Požadavek

### <a name="install-the-latest-azure-powershell-module"></a>Instalace nejnovějšího modulu Azure PowerShellu

* [Instalace modulu Azure PowerShellu](https://docs.microsoft.com/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Připojte se k Azure:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Vyberte předplatné cílového účtu úložiště:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Vytvořte kontext pro cílový účet úložiště:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>Získejte aktuální otevřené popisovače sdílené složky:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Příklad výsledku:

|HandleId|Cesta|ClientIp|Klientský port|OpenTime|PosledníReconnectTime|FileId|Parentid|SessionId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|Nová složka/test.zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|test.zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Zavření otevřeného úchytu:

Chcete-li zavřít otevřený úchyt, použijte následující příkaz:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>Další kroky

* [Poradce při potížích se soubory Azure ve Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Poradce při potížích se soubory Azure v Linuxu](storage-troubleshoot-linux-file-connection-problems.md)
* [Řešení problémů se Synchronizací souborů Azure](storage-sync-files-troubleshoot.md)