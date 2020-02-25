---
title: Správa vertikálního škálování clusteru (horizontální navýšení kapacity) tak, aby odpovídalo poptávce v Azure Průzkumník dat
description: Tento článek popisuje kroky pro horizontální navýšení a snížení kapacity clusteru Azure Průzkumník dat na základě změny poptávky.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 95275598febae2b6b0355a7bc3e512490dae500d
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560435"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>Správa vertikálního škálování clusteru (horizontální navýšení kapacity) v Azure Průzkumník dat, aby se vešla měnící poptávka

Odpovídající změna velikosti clusteru je zásadní pro výkon Azure Průzkumník dat. Statická velikost clusteru může vést k použití nebo převzetí služeb při selhání, ani z toho, co není ideální.

Vzhledem k tomu, že poptávka na clusteru se nedá předpovědět s absolutní přesností, lepším řešením je *škálování* clusteru, přidání a odebrání kapacity a prostředků procesoru se měnícím se poptávkou. 

K dispozici jsou dva pracovní postupy pro škálování clusteru Azure Průzkumník dat:

* [Horizontální škálování](manage-cluster-horizontal-scaling.md), označované také jako škálování v a ven.
* Vertikální škálování, označované také jako horizontální navýšení kapacity.

Tento článek vysvětluje pracovní postup vertikálního škálování:

## <a name="configure-vertical-scaling"></a>Konfigurace vertikálního škálování

1. V Azure Portal přejdete do svého prostředku clusteru Azure Průzkumník dat. V části **Nastavení**vyberte **horizontální navýšení kapacity**.

1. V okně **horizontální navýšení kapacity** se zobrazí seznam dostupných SKU pro váš cluster. Například na následujícím obrázku jsou k dispozici pouze čtyři SKU.

    ![Vertikální navýšení kapacity](media/manage-cluster-vertical-scaling/scale-up.png)

    SKU jsou zakázané, protože se jedná o aktuální SKU, nebo nejsou dostupné v oblasti, ve které se cluster nachází.

1. Pokud chcete změnit SKLADOVOU položku, vyberte novou SKLADOVOU položku a klikněte na **Vybrat**.

> [!NOTE]
> * Proces vertikálního škálování může trvat několik minut a během této doby se cluster pozastaví. 
> * Horizontální navýšení kapacity může mít vliv na výkon vašeho clusteru.
> * Cena představuje odhad virtuálních počítačů clusteru a nákladů služby Azure Průzkumník dat. Ostatní náklady nejsou zahrnuty. Informace o odhadech a cenách Azure Průzkumník dat najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/data-explorer/) za službu Azure Průzkumník dat [cost Estimator](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html) .

Nyní jste nakonfigurovali vertikální škálování pro váš cluster Azure Průzkumník dat. Přidejte další pravidlo pro horizontální škálování. Pokud potřebujete pomoc s problémy s škálováním clusteru, [otevřete žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) v Azure Portal.

## <a name="next-steps"></a>Další kroky

* [Správa horizontálního škálování clusteru](manage-cluster-horizontal-scaling.md) pro dynamické horizontální navýšení kapacity počtu instancí na základě metrik, které zadáte.

* Pomocí tohoto článku sledujte využití prostředků: [monitorování výkonu, stavu a využití Azure Průzkumník dat pomocí metrik](using-metrics.md).

