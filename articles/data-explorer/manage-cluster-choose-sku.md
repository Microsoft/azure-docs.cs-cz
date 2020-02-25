---
title: Vyberte správnou SKU virtuálního počítače pro cluster Azure Průzkumník dat.
description: Tento článek popisuje, jak vybrat optimální velikost SKU pro cluster Azure Průzkumník dat.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 2d078f9715a0cfa171f0c88776a4ab78c15215a8
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561846"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Vyberte správnou SKU virtuálního počítače pro váš cluster Azure Průzkumník dat. 

Když vytvoříte nový cluster nebo optimalizujete cluster pro změnu úlohy, Azure Průzkumník dat nabízí několik SKU virtuálních počítačů, ze kterých si můžete vybrat. Virtuální počítače se pečlivě vybraly, aby vám poskytovaly optimální náklady na jakékoli zatížení. 

Velikost a skladová položka virtuálního počítače v clusteru pro správu dat je plně spravovaná službou Azure Průzkumník dat. Jsou určeny takovými faktory jako velikost virtuálního počítače a úlohy příjmu modulu. 

SKU virtuálního počítače pro cluster Engine můžete kdykoli změnit změnou [velikosti clusteru](manage-cluster-vertical-scaling.md). Nejlepší je začít s nejmenší velikostí SKU, která odpovídá počátečnímu scénáři. Pamatujte na to, že při škálování clusteru dojde k výpadku po dobu až 30 minut, než se cluster znovu vytvoří s novou SKU virtuálního počítače.

> [!TIP]
> [Rezervované instance (ri)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) COMPUTE se vztahují na cluster Azure Průzkumník dat.  

Tento článek popisuje různé možnosti pro SKU virtuálních počítačů a poskytuje technické informace, které vám pomůžou dosáhnout nejlepšího výběru.

## <a name="select-a-cluster-type"></a>Vybrat typ clusteru

Azure Průzkumník dat nabízí dva typy clusterů:

* **Výroba**: provozní clustery obsahují dva uzly pro clustery a správu dat a jsou provozovány v rámci [smlouvy SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)pro Azure Průzkumník dat.

* **Vývoj a testování (bez smlouvy SLA)** : clustery pro vývoj a testování mají jeden uzel D11 v2 pro cluster Engine a jeden uzel D1 pro cluster pro správu dat. Tento typ clusteru představuje nejnižší konfiguraci nákladů z důvodu nedostatku počtu instancí a žádného zpoplatnění za kód stroje. Pro tuto konfiguraci clusteru neexistuje žádná smlouva SLA, protože nemá redundanci.

## <a name="sku-types"></a>Typy SKU

Když vytváříte cluster Azure Průzkumník dat, vyberte *optimální* SKU virtuálního počítače pro plánované zatížení. Můžete si vybrat z následujících dvou rodin Azure Průzkumník dat SKU:

* **D v2**: SKU d je výpočetní prostředí optimalizované a přináší se dvěma typy:
    * Samotný virtuální počítač
    * Virtuální počítač s disky Premium Storage

* **Ls**: L SKU je optimalizované pro úložiště. Má mnohem větší velikost SSD než obdobně cena D SKU.

Klíčové rozdíly mezi dostupnými typy SKU jsou popsány v následující tabulce:
 
| Atribut | D SKU | L SKU |
|---|---|---
|**Malé SKU**|Minimální velikost je D11 se dvěma jádry|Minimální velikost je L4 se čtyřmi jádry |
|**Dostupnost**|K dispozici ve všech oblastech (verze DS + PS má vyšší omezení dostupnosti)|K dispozici v několika oblastech |
|**Cena za&nbsp;GB mezipaměti na jádro**|Vysoká s SKU D SKU, nízká s verzí DS + PS|Nejnižší s možností průběžných plateb |
|**Ceny rezervovaných instancí (rezervované instance)**|Vysoká sleva (nad 55&nbsp;procent pro závazek na tři roky)|Nižší sleva (20&nbsp;procent pro závazek na tři roky) |  

## <a name="select-your-cluster-vm"></a>Vyberte virtuální počítač clusteru. 

Pokud chcete vybrat virtuální počítač clusteru, [nakonfigurujte vertikální škálování](manage-cluster-vertical-scaling.md#configure-vertical-scaling). 

Díky různým možnostem SKU virtuálních počítačů, ze kterých si můžete vybrat, můžete optimalizovat náklady na požadavky na výkon a za horkou mezipaměť pro váš scénář. 
* Pokud pro velký objem dotazů potřebujete optimální výkon, ideální SKU by mělo být optimalizované pro výpočty. 
* Pokud potřebujete zadat dotaz na velké objemy dat s relativně nízkým zatížením dotazů, může SKU optimalizované pro úložiště snížit náklady a stále poskytovat vynikající výkon.

Vzhledem k tomu, že počet instancí na cluster pro malé SKU je omezený, je vhodnější použít větší virtuální počítače, které mají větší velikost paměti RAM. Pro některé typy dotazů, které přivedou více požadavků na prostředek paměti RAM, je potřeba více paměti RAM, například dotazy, které používají `joins`. Proto Pokud zvažujete možnosti škálování, doporučujeme, abyste místo horizontálního navýšení kapacity nastavili kapacitu na větší skladovou jednotku.

## <a name="vm-options"></a>Možnosti virtuálního počítače

Technické specifikace pro virtuální počítače clusteru Azure Průzkumník dat jsou popsány v následující tabulce:

|**Název**| **Kategorie** | **Velikost SSD** | **Jader** | **SRAM** | **Disky úložiště úrovně Premium (1&nbsp;TB)**| **Minimální počet instancí na cluster** | **Maximální počet instancí na cluster**
|---|---|---|---|---|---|---|---
|D11 v2| optimalizované pro výpočty | 75&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 1 | 8 (kromě SKU pro vývoj/testování, což je 1)
|D12 v2| optimalizované pro výpočty | 150&nbsp;GB   | 4 | 28&nbsp;GB | 0 | 2 | 16
|D13 v2| optimalizované pro výpočty | 307&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1 000
|D14 v2| optimalizované pro výpočty | 614&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1 000
|DS13 v2 + 1&nbsp;TB&nbsp;PS| optimalizované pro úložiště | 1&nbsp;TB | 8 | 56&nbsp;GB | 1 | 2 | 1 000
|DS13 v2 + 2&nbsp;TB&nbsp;PS| optimalizované pro úložiště | 2&nbsp;TB | 8 | 56&nbsp;GB | 2 | 2 | 1 000
|DS14 v2 + 3&nbsp;TB&nbsp;PS| optimalizované pro úložiště | 3&nbsp;TB | 16 | 112&nbsp;GB | 2 | 2 | 1 000
|DS14 v2 + 4&nbsp;TB&nbsp;PS| optimalizované pro úložiště | 4&nbsp;TB | 16 | 112&nbsp;GB | 4 | 2 | 1 000
|L4S úrovně v1| optimalizované pro úložiště | 650&nbsp;GB | 4 | 32&nbsp;GB | 0 | 2 | 16
|L8s úrovně v1| optimalizované pro úložiště | 1,3&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1 000
|L16s_1| optimalizované pro úložiště | 2,6&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1 000

* Aktualizovaný seznam SKU pro virtuální počítače můžete zobrazit na jednu oblast pomocí rozhraní Azure Průzkumník dat [ListSkus API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet). 
* Přečtěte si další informace o [různých SKU](/azure/virtual-machines/windows/sizes). 

## <a name="next-steps"></a>Další kroky

* Cluster Engine můžete kdykoli [škálovat nebo škálovat](manage-cluster-vertical-scaling.md) v závislosti na proměnlivých potřebách. 

* Velikost clusteru modulu můžete [škálovat nebo škálovat](manage-cluster-horizontal-scaling.md) tak, aby se změnila kapacita v závislosti na změněných požadavcích.

