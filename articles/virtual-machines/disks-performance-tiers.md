---
title: Změna výkonu služby Azure Managed disks
description: Přečtěte si o úrovních výkonu pro spravované disky a Naučte se měnit úrovně výkonu pro existující spravované disky.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 404f435e321e53694807a627121d84f6cbf6724d
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92359675"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Úrovně výkonu pro Managed Disks (Preview)

Azure Disk Storage v současné době nabízí integrované možnosti shlukování, které poskytují vyšší výkon pro zpracování krátkodobého neočekávaného provozu. SSD úrovně Premium mají flexibilitu na zvýšení výkonu disku bez zvýšení skutečné velikosti disku. Tato funkce vám umožní odpovídat potřebám výkonu vašich úloh a snižovat náklady. 

> [!NOTE]
> Tato funkce je aktuálně ve verzi Preview. 

Tato funkce je ideální pro události, které dočasně vyžadují trvale vyšší úroveň výkonu, jako je prázdninové nákupy, testování výkonu nebo spouštění školicích prostředí. Pro zpracování těchto událostí můžete použít vyšší úroveň výkonu, pokud ji potřebujete. Pak se můžete vrátit do původní úrovně, pokud už nebudete potřebovat další výkon.

## <a name="how-it-works"></a>Jak to funguje

Při prvním nasazení nebo zřízení disku je základní úroveň výkonu pro tento disk nastavena na základě zřízené velikosti disku. Vyšší úroveň výkonu můžete použít k uspokojení vyšší poptávky. Když už tuto úroveň výkonu nepotřebujete, můžete se vrátit na počáteční základní úroveň výkonu.

Vaše fakturace se mění v průběhu změny vaší úrovně. Pokud například zřídíte disk P10 (128 GiB), úroveň výkonu základní úrovně se nastaví jako P10 (500 IOPS a 100 MB/s). Bude se vám účtovat sazba za P10. Vrstvu můžete upgradovat tak, aby odpovídala výkonu P50 (7 500 IOPS a 250 MB/s), aniž by se zvýšila velikost disku. Během upgradu se vám bude účtovat sazba za P50. Pokud už vyšší výkon nepotřebujete, můžete se vrátit na úroveň P10. Na disk se bude znovu účtovat P10 sazba.

| Velikost disku | Základní úroveň výkonu | Dá se upgradovat na |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 GiB | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 GiB | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32 GiB | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64 GiB | P6 | P10, P15, P20, P30, P40, P50 |
| 128 GiB | P10 | P15, P20, P30, P40, P50 |
| 256 GB | P15 | P20, P30, P40, P50 |
| 512 GiB | P20 | P30, P40, P50 |
| 1 TiB | P30 | P40, P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | Žádné |
| 8 TiB | P60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 TB | P80 | Žádné |

Informace o fakturaci najdete v tématu [ceny za spravované disky](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="restrictions"></a>Omezení

- Tato funkce se v současné době podporuje jenom pro Premium SSD.
- Než budete moct změnit vrstvu disku, musíte virtuální počítač buď zrušit, nebo ho odpojit od spuštěného virtuálního počítače.
- Použití úrovní výkonu P60, P70 a P80 je omezené na disky 4 096 GiB nebo vyšší.
- Úroveň výkonu disku se dá downgradovat jenom jednou za 24 hodin.

## <a name="regional-availability"></a>Regionální dostupnost

Možnost upravit úroveň výkonu spravovaného disku je v současnosti dostupná jenom na SSD Premium v oblastech Východní USA 2, Střed USA – jih, Středozápadní USA a Austrálie – jih – východ.

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

Pokud potřebujete změnit velikost disku, abyste mohli využít vyšší úrovně výkonu, přečtěte si tyto články:

- [Rozbalení virtuálních pevných disků na virtuálním počítači se systémem Linux pomocí Azure CLI](linux/expand-disks.md)
- [Rozšíření spravovaného disku připojeného k virtuálnímu počítači s Windows](windows/expand-os-disk.md)
