---
title: Správa vertikálního škálování clusteru (škálování navýšit kapacitu) tak, aby odpovídala poptávce v Průzkumníku dat Azure
description: Tento článek popisuje kroky, jak vertikálně navýšit kapacitu a vertikálně navýšit kapacitu clusteru Azure Data Explorer na základě měnící se poptávky.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 95275598febae2b6b0355a7bc3e512490dae500d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560435"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>Správa vertikálního škálování clusteru (škálování nahoru) v Průzkumníku dat Azure tak, aby vyhovovaly měnícím se poptávce

Vhodné nastavení velikosti clusteru je důležité pro výkon Průzkumníka dat Azure. Velikost statického clusteru může vést k nedostatečnému využití nebo nadměrnému využití, z nichž ani jedno není ideální.

Vzhledem k tomu, že poptávku v clusteru nelze předpovědět s absolutní přesností, je lepším přístupem *škálování* clusteru, přidávání a odebírání kapacity a prostředků procesoru s měnící se poptávkou. 

Existují dva pracovní postupy pro škálování clusteru Průzkumníka dat Azure:

* [Horizontální škálování](manage-cluster-horizontal-scaling.md), nazývané také změna měřítka dovnitř a ven.
* Vertikální škálování, nazývané také škálování nahoru a dolů.

Tento článek vysvětluje pracovní postup vertikálního měřítka:

## <a name="configure-vertical-scaling"></a>Konfigurace svislého měřítka

1. Na webu Azure Portal přejděte na prostředek clusteru Azure Data Explorer. V části **Nastavení**vyberte **Změnit kapacitu**.

1. V okně **Vertikálně navýšit** kapacitu se zobrazí seznam dostupných soupoložek pro váš cluster. Například na následujícím obrázku jsou k dispozici pouze čtyři sku.

    ![Vertikální navýšení kapacity](media/manage-cluster-vertical-scaling/scale-up.png)

    Skladové položky jsou zakázány, protože se jedná o aktuální skladovou položku nebo nejsou k dispozici v oblasti, kde je cluster umístěn.

1. Chcete-li změnit skladovou položku, vyberte novou skladovou položku a klepněte na **tlačítko Vybrat**.

> [!NOTE]
> * Proces vertikálníškálování může trvat několik minut a během této doby bude cluster pozastaven. 
> * Škálování může poškodit výkon clusteru.
> * Cena je odhad virtuálních počítačů clusteru a nákladů na službu Azure Data Explorer. Ostatní náklady nejsou zahrnuty. Na stránce [odhadu odhadu](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html) průzkumníka dat Azure najdete odhad a [na stránce s cenami](https://azure.microsoft.com/pricing/details/data-explorer/) v Azure Data Exploreru úplné informace o cenách.

Teď jste nakonfigurovali vertikální škálování pro cluster Průzkumníka dat Azure. Přidejte další pravidlo pro vodorovné měřítko. Pokud potřebujete pomoc s problémy s škálováním clusteru, [otevřete žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) na webu Azure Portal.

## <a name="next-steps"></a>Další kroky

* [Spravujte horizontální škálování clusteru,](manage-cluster-horizontal-scaling.md) abyste dynamicky škálovali počet instancí na základě zadaných metrik.

* Sledujte využití prostředků podle následujícího článku: [Sledujte výkon, stav a využití Azure Data Exploreru pomocí metrik](using-metrics.md).

