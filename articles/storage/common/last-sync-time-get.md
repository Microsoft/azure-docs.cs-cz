---
title: Kontrola vlastnosti Poslední čas synchronizace pro účet úložiště
titleSuffix: Azure Storage
description: Přečtěte si, jak zkontrolovat vlastnost **Poslední čas synchronizace** pro účet geograficky replikovaného úložiště. Vlastnost **Čas poslední synchronizace** označuje poslední čas, kdy byly všechny zápisy z primární oblasti úspěšně zapsány do sekundární oblasti.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/16/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 3a406ce6db060b9ff5be7bcadecb6c7ff7e65a1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165485"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>Kontrola vlastnosti Poslední čas synchronizace pro účet úložiště

Při konfiguraci účtu úložiště můžete určit, že vaše data se zkopírují do sekundární oblasti, která je stovky mil od primární oblasti. Geografická replikace nabízí odolnost pro vaše data v případě významného výpadku v primární oblasti, jako je například přírodní katastrofa. Pokud navíc povolíte přístup pro čtení do sekundární oblasti, zůstanou data k dispozici pro operace čtení, pokud primární oblast přestane být k dispozici. Můžete navrhnout aplikaci pro bezproblémové přepnutí na čtení ze sekundární oblasti, pokud primární oblast neodpovídá.

Geograficky redundantní úložiště (GRS) a geograficky zónově redundantní úložiště (GZRS) (preview) replikují data asynchronně do sekundární oblasti. Pro přístup pro čtení do sekundární oblasti povolte geograficky redundantní úložiště pro čtení (RA-GRS) nebo geograficky redundantní úložiště pro čtení (RA-GZRS). Další informace o různých možnostech redundance nabízené službou Azure Storage najdete v [tématu Redundance azure storage](storage-redundancy.md).

Tento článek popisuje, jak zkontrolovat vlastnost **Poslední čas synchronizace** pro váš účet úložiště, abyste mohli vyhodnotit jakýkoli nesoulad mezi primární a sekundární oblastí.

## <a name="about-the-last-sync-time-property"></a>O vlastnosti Čas poslední synchronizace

Vzhledem k tomu, že geografická replikace je asynchronní, je možné, že data zapsaná do primární oblasti ještě nebyla zapsána do sekundární oblasti v době výpadku. Vlastnost **Čas poslední synchronizace** označuje, kdy byla data z primární oblasti naposledy úspěšně zapsána do sekundární oblasti. Všechny zápisy provedené do primární oblasti před čas emajení poslední synchronizace jsou k dispozici ke čtení ze sekundárního umístění. Zápisy provedené do primární oblasti po poslední době synchronizace vlastnost může nebo nemusí být k dispozici pro čtení ještě.

Vlastnost **Čas poslední synchronizace** je hodnota data a času GMT.

## <a name="get-the-last-sync-time-property"></a>Získat vlastnost Čas poslední synchronizace

Pomocí powershellu nebo azure cli načíst hodnotu **poslední synchronizace čas** vlastnosti.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete získat čas poslední synchronizace pro účet úložiště s PowerShellem, nainstalujte modul Preview azure storage, který podporuje získání statistik geografické replikace. Například:

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
```

Potom zkontrolujte vlastnost **GeoReplicationStats.LastSyncTime** účtu úložiště. Nezapomeňte nahradit zástupné hodnoty vlastními hodnotami:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li získat čas poslední synchronizace pro účet úložiště s Azure CLI, zkontrolujte **geoReplicationStats.lastSyncTime** vlastnost účtu úložiště. Pomocí `--expand` parametru můžete vrátit hodnoty vlastností vnořených pod **geoReplicationStats**. Nezapomeňte nahradit zástupné hodnoty vlastními hodnotami:

```azurecli-interactive
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

---

## <a name="see-also"></a>Viz také

- [Redundance azure úložiště](storage-redundancy.md)
- [Změna možnosti redundance pro účet úložiště](redundancy-migration.md)
- [Navrhování vysoce dostupných aplikací pomocí geograficky redundantního úložiště pro přístup ke čtení](storage-designing-ha-apps-with-ragrs.md)