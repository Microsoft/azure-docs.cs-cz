---
title: Změna výkonu služby Azure Managed disks
description: Přečtěte si o úrovních výkonu pro spravované disky a také o tom, jak změnit úrovně výkonu pro existující spravované disky.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 7da500c3f18b7bf7057b0c5875bc9b39136a6483
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396582"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Úrovně výkonu pro Managed Disks (Preview)

Azure Disk Storage v současné době nabízí integrované možnosti shlukování pro zajištění vyššího výkonu pro zpracování krátkodobého neočekávaného provozu. SSD úrovně Premium mají flexibilitu při zvyšování výkonu disku, aniž by bylo potřeba zvyšovat skutečnou velikost disku, což vám umožní odpovídat potřebám svého zatížení a snižovat náklady, tato funkce je aktuálně ve verzi Preview. To je ideální pro události, které dočasně vyžadují trvale vyšší úroveň výkonu, jako je třeba prázdninové nákupy, testování výkonu nebo spouštění školicích prostředí. Pokud chcete tyto události zpracovat, můžete podle potřeby vybrat vyšší úroveň výkonu a vrátit se do původní úrovně, pokud už další výkon není potřeba.

## <a name="how-it-works"></a>Jak to funguje

Při prvním nasazení nebo zřízení disku je základní úroveň výkonu pro tento disk nastavena na základě zřízené velikosti disku. Je možné vybrat vyšší úroveň výkonu pro splnění vyšších požadavků a v případě, že tento výkon již nepotřebujete, můžete se vrátit do počáteční základní úrovně výkonu.

Vaše fakturace se mění v průběhu změny vaší úrovně. Pokud například zřídíte disk P10 (128 GiB), vaše základní úroveň výkonu se nastaví jako P10 (500 IOPS a 100 MB/s) a bude se vám účtovat sazba P10. Vrstvu můžete aktualizovat tak, aby odpovídala výkonu P50 (7500 IOPS a 250 MB/s) bez zvýšení velikosti disku. během této doby se vám bude účtovat sazba za P50. Pokud už vyšší výkon nepotřebujete, můžete se vrátit na úroveň P10 a disk se znovu bude účtovat za P10 sazbu.

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

Informace o fakturaci najdete v tématu [ceny za spravované disky](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="restrictions"></a>Omezení

- V současné době se podporuje jenom pro prémiové SSD.
- Před změnou úrovní je potřeba disky odpojit od spuštěného virtuálního počítače.
- Použití úrovní výkonu P60, P70 a P80 je omezené na disky 4096 GiB nebo vyšší.
- Úroveň výkonu disků se dá změnit jenom jednou za 24 hodin.

## <a name="regional-availability"></a>Regionální dostupnost

Úprava úrovně výkonu spravovaného disku je aktuálně dostupná jenom pro Premium SSD v následujících oblastech:

- USA – středozápad 

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Vytvoření prázdného datového disku s úrovní vyšší než základní úroveň

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az login

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>Vytvoření disku s operačním systémem s úrovní vyšší než základní úroveň z image Azure Marketplace

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```
     
## <a name="update-the-tier-of-a-disk"></a>Aktualizace úrovně disku

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```
## <a name="show-the-tier-of-a-disk"></a>Zobrazit úroveň disku

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

## <a name="next-steps"></a>Další kroky

Pokud je nutné změnit velikost disku, aby bylo možné využít větší úrovně výkonu, přečtěte si naše články na předmětu:

- [Rozbalení virtuálních pevných disků na virtuálním počítači se systémem Linux pomocí Azure CLI](linux/expand-disks.md)
- [Rozšíření spravovaného disku připojeného k virtuálnímu počítači s Windows](windows/expand-os-disk.md)
