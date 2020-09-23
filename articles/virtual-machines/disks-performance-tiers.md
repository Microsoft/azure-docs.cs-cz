---
title: Zvýšení výkonu služby Azure Managed disk
description: Přečtěte si o úrovních výkonu pro spravované disky a také o tom, jak upgradovat úrovně výkonu pro vaše spravované disky.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/22/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: aa188babf56d4a825059fe6103e2e07745eb134f
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90974131"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Úrovně výkonu pro Managed Disks (Preview)

Azure Disk Storage v současné době nabízí integrované možnosti shlukování pro zajištění vyššího výkonu pro zpracování krátkodobého neočekávaného provozu. SSD úrovně Premium mají flexibilitu při zvyšování výkonu disku, aniž by bylo potřeba zvyšovat skutečnou velikost disku, což vám umožní odpovídat potřebám svého zatížení a snižovat náklady, tato funkce je aktuálně ve verzi Preview. To je ideální pro události, které dočasně vyžadují trvale vyšší úroveň výkonu, jako je třeba prázdninové nákupy, testování výkonu nebo spouštění školicích prostředí. Pokud chcete tyto události zpracovat, můžete podle potřeby vybrat vyšší úroveň výkonu a vrátit se do původní úrovně, pokud už další výkon není potřeba.

## <a name="how-it-works"></a>Jak to funguje

Při prvním nasazení nebo zřízení disku je základní úroveň výkonu pro tento disk nastavena na základě zřízené velikosti disku. Je možné vybrat vyšší úroveň výkonu pro splnění vyšších požadavků a v případě, že tento výkon již nepotřebujete, můžete se vrátit do počáteční základní úrovně výkonu. Pokud například zřídíte disk P10 (128 GiB), úroveň výkonu základní úrovně se nastaví jako P10 (500 IOPS a 100 MB/s). Vrstvu můžete aktualizovat tak, aby odpovídala výkonu P50 (7500 IOPS a 250 MB/s), aniž by se zvýšila velikost disku, a vrátit se na P10, pokud už vyšší výkon není potřeba.

| Velikost disku | Základní úroveň výkonu | Dá se upgradovat na |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 GiB | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 GiB | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32 GB | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64 GiB | P6 | P10, P15, P20, P30, P40, P50 |
| 128 GiB | P10 | P15, P20, P30, P40, P50 |
| 256 GB | P15 | P20, P30, P40, P50 |
| 512 GiB | P20 | P30, P40, P50 |
| 1 TiB | P30 | P40, P50 |
| 2 TB | P40 | P50 |
| 4 TiB | P50 | Žádné |
| 8 TiB | P60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 TB | P80 | Žádné |

## <a name="restrictions"></a>Omezení

- V současné době se podporuje jenom pro prémiové SSD.
- Před změnou úrovní je potřeba disky odpojit od spuštěného virtuálního počítače.
- Použití úrovní výkonu P60, P70 a P80 je omezené na disky 4096 GiB nebo vyšší.

## <a name="regional-availability"></a>Regionální dostupnost

Úprava úrovně výkonu spravovaného disku je aktuálně dostupná jenom pro Premium SSD v následujících oblastech:

- USA – středozápad 
- Východní 2 USA 
- Evropa – západ
- Východní Austrálie 
- Austrálie – jihovýchod 
- Indie – jih

## <a name="createupdate-a-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Vytvoří nebo aktualizuje datový disk s úrovní vyšší než základní úrovně.

1. Vytvořte prázdný datový disk s úrovní vyšší než základní úroveň nebo aktualizujte vrstvu disku vyšší než základní úroveň pomocí ukázkové šablony [CreateUpdateDataDiskWithTier.jsv](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateDataDiskWithTier.json) .

     ```cli
     subscriptionId=<yourSubscriptionIDHere>
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     diskSize=<yourDiskSizeHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az login
    
     az account set --subscription $subscriptionId
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateDataDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier" "dataDiskSizeInGb=$diskSize"
     ```

1. Potvrďte úroveň disku.

    ```cli
    az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="createupdate-an-os-disk-with-a-tier-higher-than-the-baseline-tier"></a>Vytvoří nebo aktualizuje disk s operačním systémem s úrovní vyšší než základní úroveň.

1. Vytvořte disk s operačním systémem z image Marketplace nebo aktualizujte vrstvu disku s operačním systémem vyšší než základní úroveň pomocí ukázkové šablony [CreateUpdateOSDiskWithTier.jsv](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateOSDiskWithTier.json) .

     ```cli
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateOSDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier"
     ```
 
 1. Potvrďte úroveň disku.
 
     ```cli
     az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="next-steps"></a>Další kroky

Pokud je nutné změnit velikost disku, aby bylo možné využít větší úrovně výkonu, přečtěte si naše články na předmětu:

- [Rozbalení virtuálních pevných disků na virtuálním počítači se systémem Linux pomocí Azure CLI](linux/expand-disks.md)
- [Rozšíření spravovaného disku připojeného k virtuálnímu počítači s Windows](windows/expand-os-disk.md)