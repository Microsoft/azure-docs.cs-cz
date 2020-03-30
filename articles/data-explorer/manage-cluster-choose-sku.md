---
title: Výběr správné skladové položky virtuálního počítače pro cluster Průzkumník a dat Azure
description: Tento článek popisuje, jak vybrat optimální velikost skladové položky pro cluster Průzkumník a dat Azure.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 2d078f9715a0cfa171f0c88776a4ab78c15215a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561846"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Vyberte správnou skladovou položku virtuálního počítače pro cluster Průzkumníka dat Azure. 

Když vytvoříte nový cluster nebo optimalizujete cluster pro měnící se úlohu, Azure Data Explorer nabízí více virtuálních počítačů (VM) skum, ze kterých si můžete vybrat. Virtuální aplikace byly pečlivě vybrány tak, aby vám poskytly co nejoptimálnější náklady pro jakékoli zatížení. 

Velikost a skladová položka virtuálního počítače clusteru pro správu dat jsou plně spravované službou Azure Data Explorer. Jsou určeny takovými faktory, jako je velikost virtuálního počítače motoru a zatížení přilnavého počítače. 

Skladovou položku virtuálního času pro cluster motoru můžete kdykoli změnit [navýšením kapacity clusteru](manage-cluster-vertical-scaling.md). Nejlepší je začít s nejmenší velikostí skladové položky, která odpovídá počátečnímu scénáři. Mějte na paměti, že škálování clusteru má za následek prostoje až 30 minut, zatímco cluster je znovu vytvořen s novou skladovou položkou virtuálního času.

> [!TIP]
> Vypočítat [rezervované instance (RI)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) se vztahuje na cluster Azure Data Explorer.  

Tento článek popisuje různé možnosti skladové položky virtuálního zařízení a poskytuje technické podrobnosti, které vám mohou pomoci provést nejlepší volbu.

## <a name="select-a-cluster-type"></a>Výběr typu clusteru

Azure Data Explorer nabízí dva typy clusterů:

* **Produkční :** Produkční clustery obsahují dva uzly pro clustery správy motorů a dat a jsou provozovány v rámci [sla](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)Průzkumníka dat Azure .

* **Vývoj a testování (bez sla):** Clustery pro vývoj a testování mají jeden uzel D11 v2 pro cluster motoru a jeden uzel D1 pro cluster pro správu dat. Tento typ clusteru je konfigurace s nejnižšími náklady z důvodu nízkého počtu instancí a bez poplatku za značky motoru. Pro tuto konfiguraci clusteru neexistuje žádná sla, protože postrádá redundanci.

## <a name="sku-types"></a>Typy skladových položk

Když vytvoříte cluster Průzkumník a dat Azure, vyberte *optimální* skladovou položku virtuálního počítače pro plánované úlohy. Můžete si vybrat z následujících dvou rodin sku souaplikací průzkumníka dat Azure:

* **D v2**: D SKU je optimalizován pro výpočet a je dodáván ve dvou variantách:
    * Samotný virtuální počítač
    * Virtuální počítače dodávaný s disky úložiště premium

* **LS**: Skladová položka L je optimalizovaná pro úložiště. Má mnohem větší velikost SSD než podobně oceněná D SKU.

Hlavní rozdíly mezi dostupnými typy skladových položek jsou popsány v následující tabulce:
 
| Atribut | D Skladová položka | Skladová položka L |
|---|---|---
|**Malé SKUs**|Minimální velikost je D11 se dvěma jádry|Minimální velikost je L4 se čtyřmi jádry |
|**Dostupnost**|K dispozici ve všech oblastech (verze DS+PS má omezenější dostupnost)|K dispozici v několika oblastech |
|**Cena&nbsp;za GB mezipaměti za jádro**|Vysoká s D SKU, nízká s verzí DS + PS|Nejnižší s možností průběžných plateb |
|**Ceny rezervovaných instancí (RI)**|Vysoká sleva (více než 55&nbsp;procent na tříletý závazek)|Nižší sleva (20&nbsp;procent pro tříletý závazek) |  

## <a name="select-your-cluster-vm"></a>Výběr virtuálního počítače clusteru 

Chcete-li vybrat virtuální počítač clusteru, [nakonfigurujte vertikální škálování](manage-cluster-vertical-scaling.md#configure-vertical-scaling). 

S různými možnostmi sku virtuálních her, ze kterých si můžete vybrat, můžete optimalizovat náklady na výkon a požadavky na aktivní mezipaměti pro váš scénář. 
* Pokud potřebujete nejoptimálnější výkon pro vysoký objem dotazu, ideální skladová položka by měla být optimalizována pro výpočetní prostředí. 
* Pokud potřebujete dotazovat velké objemy dat s relativně nižší zatížení dotazu, skladová položka optimalizovaná pro úložiště může pomoci snížit náklady a stále poskytovat vynikající výkon.

Vzhledem k tomu, že počet instancí na cluster u malých skum je omezená, je vhodnější použít větší virtuální chodů, které mají větší paměť RAM. Další paměť RAM je potřeba pro některé typy dotazů, které kladou větší poptávku na prostředek paměti RAM, jako jsou dotazy, které používají `joins`. Proto při zvažování škálování možnosti, doporučujeme škálovat až na větší skladovou položku spíše než horizontální navýšení kapacity přidáním další instance.

## <a name="vm-options"></a>Možnosti virtuálního zařízení

Technické specifikace pro virtuální počítače clusteru Azure Data Explorer jsou popsány v následující tabulce:

|**Název**| **Kategorie** | **Velikost SSD** | **Cores** | **Ram** | **Disky s prémiovým úložištěm (1&nbsp;TB)**| **Minimální počet instancí na cluster** | **Maximální počet instancí na cluster**
|---|---|---|---|---|---|---|---
|D11 v2| optimalizované pro výpočty | 75&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 1 | 8 (s výjimkou sku pro vývoj a testování, což je 1)
|D12 v2| optimalizované pro výpočty | 150&nbsp;GB   | 4 | 28&nbsp;GB | 0 | 2 | 16
|D13 v2| optimalizované pro výpočty | 307&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1 000
|D14 v2| optimalizované pro výpočty | 614&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1 000
|DS13 v2 + 1&nbsp;TB&nbsp;PS| optimalizované pro úložiště | 1&nbsp;TB | 8 | 56&nbsp;GB | 1 | 2 | 1 000
|DS13 v2 + 2&nbsp;TB&nbsp;PS| optimalizované pro úložiště | 2&nbsp;TB | 8 | 56&nbsp;GB | 2 | 2 | 1 000
|DS14 v2 + 3&nbsp;TB&nbsp;PS| optimalizované pro úložiště | 3&nbsp;TB | 16 | 112&nbsp;GB | 2 | 2 | 1 000
|DS14 v2 + 4&nbsp;TB&nbsp;PS| optimalizované pro úložiště | 4&nbsp;TB | 16 | 112&nbsp;GB | 4 | 2 | 1 000
|L4s v1| optimalizované pro úložiště | 650&nbsp;GB | 4 | 32&nbsp;GB | 0 | 2 | 16
|L8s v1| optimalizované pro úložiště | 1,3&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1 000
|L16s_1| optimalizované pro úložiště | 2,6&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1 000

* Aktualizovaný seznam skladových položek virtuálních zařízení na oblast můžete zobrazit pomocí rozhraní Azure Data Explorer [ListSkus API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet). 
* Další informace o [různých skum](/azure/virtual-machines/windows/sizes). 

## <a name="next-steps"></a>Další kroky

* Cluster motoru můžete kdykoli [vertikálně navýšit nebo vertikálně navýšit](manage-cluster-vertical-scaling.md) kapacitu podle měnící se skladové položky virtuálního počítače v závislosti na různých potřebách. 

* Velikost clusteru motoru můžete [škálovat nebo škálovat](manage-cluster-horizontal-scaling.md) a měnit tak kapacitu v závislosti na měnících se požadavcích.

