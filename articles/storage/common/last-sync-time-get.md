---
title: Podívejte se na vlastnost čas poslední synchronizace pro účet úložiště.
titleSuffix: Azure Storage
description: Naučte se, jak ověřit vlastnost **čas poslední synchronizace** u geograficky replikovaného účtu úložiště. Vlastnost **čas poslední synchronizace** označuje poslední čas, kdy byly všechny zápisy z primární oblasti úspěšně zapsány do sekundární oblasti.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/16/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 3a406ce6db060b9ff5be7bcadecb6c7ff7e65a1f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77165485"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>Podívejte se na vlastnost čas poslední synchronizace pro účet úložiště.

Když konfigurujete účet úložiště, můžete určit, že se vaše data zkopírují do sekundární oblasti, která je od primární oblasti stovky mil. Geografická replikace nabízí odolnost za vaše data v případě významného výpadku v primární oblasti, například při přirozené havárii. Pokud navíc povolíte přístup pro čtení do sekundární oblasti, vaše data budou dostupná pro operace čtení, pokud primární oblast nebude k dispozici. Aplikaci můžete navrhnout tak, aby se hladce přepnula na čtení ze sekundární oblasti, pokud primární oblast nereaguje.

Geograficky redundantní úložiště (GRS) a geograficky redundantní úložiště (GZRS) (Preview) obě replikují data asynchronně do sekundární oblasti. Pro přístup pro čtení do sekundární oblasti povolte geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) nebo geograficky redundantní úložiště s přístupem pro čtení (RA-GZRS). Další informace o různých možnostech redundance nabízených nástrojem Azure Storage najdete v tématu [Azure Storage redundance](storage-redundancy.md).

Tento článek popisuje, jak ověřit vlastnost **čas poslední synchronizace** pro váš účet úložiště, abyste mohli vyhodnotit případné nesrovnalosti mezi primárními a sekundárními oblastmi.

## <a name="about-the-last-sync-time-property"></a>O vlastnosti čas poslední synchronizace

Vzhledem k tomu, že geografická replikace je asynchronní, je možné, že data zapsaná do primární oblasti se ještě v době výpadku nezapsala do sekundární oblasti. Vlastnost **čas poslední synchronizace** určuje, kdy byla data z primární oblasti úspěšně zapsána do sekundární oblasti. Všechna zápisy provedená v primární oblasti před poslední dobou synchronizace jsou k dispozici pro čtení ze sekundárního umístění. Zápisy provedené v primární oblasti po dobu poslední synchronizace, která ještě nemusí být k dispozici pro čtení.

Vlastnost **čas poslední synchronizace** je hodnota data a času GMT.

## <a name="get-the-last-sync-time-property"></a>Získá vlastnost čas poslední synchronizace.

K načtení hodnoty vlastnosti **čas poslední synchronizace** můžete použít PowerShell nebo rozhraní příkazového řádku Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete získat čas poslední synchronizace pro účet úložiště pomocí PowerShellu, nainstalujte modul Azure Storage Preview, který podporuje získávání statistik geografické replikace. Například:

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
```

Pak zkontrolujte vlastnost **GeoReplicationStats. LastSyncTime** účtu úložiště. Nezapomeňte nahradit hodnoty zástupných symbolů vlastními hodnotami:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete získat čas poslední synchronizace pro účet úložiště pomocí Azure CLI, podívejte se na vlastnost **geoReplicationStats. lastSyncTime** účtu úložiště. Použijte `--expand` parametr pro návrat hodnoty vlastností vnořených do **geoReplicationStats**. Nezapomeňte nahradit hodnoty zástupných symbolů vlastními hodnotami:

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

- [Azure Storage redundance](storage-redundancy.md)
- [Změna možnosti redundance pro účet úložiště](redundancy-migration.md)
- [Návrh vysoce dostupných aplikací s využitím geograficky redundantního úložiště s přístupem pro čtení](storage-designing-ha-apps-with-ragrs.md)