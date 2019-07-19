---
title: Vyberte správnou SKU virtuálního počítače pro váš cluster Azure Průzkumník dat.
description: Tento článek popisuje, jak vybrat optimální velikost SKU pro cluster Azure Průzkumník dat.
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 0239111ca56dfe431a00eee83c79eedccc66c927
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226151"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Vyberte správnou SKU virtuálního počítače pro váš cluster Azure Průzkumník dat. 

Azure Průzkumník dat obsahuje několik SKU virtuálních počítačů, ze kterých si můžete vybrat při vytváření nového clusteru nebo optimalizaci clusteru pro změnu zatížení. Virtuální počítače se pečlivě rozhodly, aby umožňovaly optimální náklady na jakékoli zatížení. 

Velikost a skladová položka virtuálního počítače v clusteru pro správu dat je plně spravovaná službou Azure Průzkumník dat. Je určena faktory, jako je velikost virtuálního počítače modulu a úloha přijímání. 

SKU virtuálního počítače pro cluster Engine se dá kdykoli změnit škálováním [clusteru](manage-cluster-vertical-scaling.md). Proto je nejlepší začít s minimální velikostí SKU, která odpovídá počátečnímu scénáři. Pamatujte na to, že při škálování clusteru dojde k výpadku až 30 minut, než se cluster znovu vytvoří pomocí nové SKU virtuálního počítače.

> [!TIP]
> Pro cluster Azure Průzkumník dat se vztahují výpočetní prostředky [(rezervované instance)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) .  

Tento článek popisuje různé možnosti pro SKU virtuálních počítačů a poskytuje technické informace, které vám můžou usnadnit optimální volbu.

## <a name="select-the-cluster-type"></a>Vybrat typ clusteru

Azure Průzkumník dat nabízí dva typy clusterů:

* **Produkční**: Provozní clustery obsahují dva uzly pro clustery a správu dat a jsou provozovány v rámci [smlouvy SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)pro Azure Průzkumník dat.

* **Vývoj a testování (bez smlouvy SLA)** : Clustery pro vývoj a testování mají jeden uzel D11_v2 pro cluster Engine a jeden uzel D1 pro cluster pro správu dat. Tento typ clusteru představuje nejnižší konfiguraci nákladů z důvodu nízkého počtu instancí a bez poplatků za modul pro zápis do kódu. Pro tuto konfiguraci clusteru neexistuje žádná smlouva SLA, protože nemá redundanci.

## <a name="sku-types"></a>Typy SKU

Při vytváření clusteru Azure Průzkumník dat vyberte *optimální* SKU virtuálního počítače pro plánované zatížení. Azure Průzkumník dat má dvě rodiny SKU, ze kterých si můžete vybrat:

* **D_V2**: **D** SKU je výpočetní prostředí optimalizované a poskytované ve dvou charakterech.
    * Samotný virtuální počítač
    * Virtuální počítač s disky Premium Storage

* **LS**: **L** SKU je úložiště optimalizované. Má mnohem větší velikost SSD, než je cena **D** SKU.

Následující tabulka uvádí hlavní rozdíly mezi dostupnými typy SKU:
 
|**Přidělen** | **D SKU** | **L SKU**
|---|---|---
|**Malé SKU**|Minimální velikost je 11 ' se dvěma jádry|Minimální velikost je L4 se čtyřmi jádry.
|**Dostupnost**|K dispozici ve všech oblastech (verze DS + PS má vyšší omezení dostupnosti)|K dispozici v několika oblastech
|**Cena za GB mezipaměti na jádro**|Vysoká s SKU D SKU, nízká s verzí DS + PS|Nejlevnější s možností *průběžné platby*
|**Ceny vyhrazené (rezervované instance)**|Vysoká sleva (více než 55% u závazku na tři roky)|Nižší sleva (20% pro závazek na tři roky)  

## <a name="select-your-cluster-vm"></a>Vyberte virtuální počítač clusteru. 

Pokud chcete vybrat virtuální počítač clusteru, [nakonfigurujte vertikální škálování](manage-cluster-vertical-scaling.md#configure-vertical-scaling). 

Různé možnosti pro SKU virtuálních počítačů umožňují optimalizovat náklady na nezbytné požadavky na výkon a Hot cache pro požadovaný scénář. Pokud scénář vyžaduje optimální výkon pro velký objem dotazů, ideální SKU by mělo být optimalizované pro výpočty. Na druhou stranu platí, že pokud scénář vyžaduje dotazování velkých objemů dat s poměrně nižším zatížením dotazů, sníží se u optimalizované skladové položky úložiště náklady a stále vám bude poskytovat vynikající výkon.

Počet instancí na cluster pro malé SKU je omezený, takže je vhodnější použít větší virtuální počítače, které mají větší velikost paměti RAM. Velikost paměti RAM je nutná pro některé typy dotazů, které přivedou více požadavků na prostředek paměti RAM, například dotazy, které `joins`používají. Proto při zvažování možností škálování doporučujeme škálovat až větší SKU, než můžete škálovat přidáním dalších instancí.

## <a name="vm-options"></a>Možnosti virtuálního počítače

Následující tabulka uvádí technické specifikace pro virtuální počítače clusteru Azure Průzkumník dat:

|**Název**| **Kategorie** | **Velikost SSD** | **Jader** | **SRAM** | **Disky úložiště úrovně Premium (1 TB)**| **Minimální počet instancí na cluster** | **Maximální počet instancí na cluster**
|---|---|---|---|---|---|---|---
|D11_v2| Optimalizované pro výpočty | 75 GB    | 2 | 14 GB | 0 | 1 | 8 (kromě SKU pro vývoj/testování, kde se nachází 1)
|D12_v2| Optimalizované pro výpočty | 150 GB   | 4 | 28 GB | 0 | 2 | 16
|D13_v2| Optimalizované pro výpočty | 307 GB   | 8 | 56 GB | 0 | 2 | 1 000
|D14_v2| Optimalizované pro výpočty | 614 GB   | 16| 112 GB | 0 | 2 | 1 000
|DS13_v2 + 1 TB PS| Optimalizované úložiště | 1 TB | 8 | 56 GB | 1 | 2 | 1 000
|DS13_v2 + 2 TB PS| Optimalizované úložiště | 2 TB | 8 | 56 GB | 2 | 2 | 1 000
|DS14_v2 + zřizuje PS| Optimalizované úložiště | 3 TB | 16 | 112 GB | 2 | 2 | 1 000
|DS14_v2 + 4 TB PS| Optimalizované úložiště | 4 TB | 16 | 112 GB | 4 | 2 | 1 000
|L4s_v1| Optimalizované úložiště | 650 GB | 4 | 32 GB | 0 | 2 | 16
|L8s_v1| Optimalizované úložiště | 1,3 TB | 8 | 64 GB | 0 | 2 | 1 000
|L16s_1| Optimalizované úložiště | 2,6 TB | 16| 128 GB | 0 | 2 | 1 000

* Prohlédněte si aktualizovaný seznam SKU pro virtuální počítače na oblast pomocí [rozhraní Azure Průzkumník dat ListSkus API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet). 
* Přečtěte si další informace o [různých skladových](/azure/virtual-machines/windows/sizes-compute)položkách Compute. 

## <a name="next-steps"></a>Další postup

* Cluster motoru se dá kdykoli [škálovat nahoru nebo dolů](manage-cluster-vertical-scaling.md) změnou SKU virtuálního počítače pro různé potřeby. 

* Velikost clusteru motoru se dá [škálovat a](manage-cluster-horizontal-scaling.md) změnit tak, aby se změnila kapacita se měnícími se požadavky.

