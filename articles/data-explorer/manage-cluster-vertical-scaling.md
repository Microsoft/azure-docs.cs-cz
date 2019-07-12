---
title: Vertikální navýšení kapacity clusteru služby Průzkumník dat Azure tak, aby vyhovovaly měnících se požadavků
description: Tento článek popisuje kroky pro vertikální navýšení kapacity a vertikální snížení kapacity clusteru služby Azure Průzkumník dat na základě změny poptávky.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: dc9ca8bb592e699d19835efeafb91e81408ae297
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571527"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Správa clusteru vertikálně navýšit kapacitu tak, aby vyhovovaly měnících se požadavků

Existují dva pracovní postupy pro škálování clusteru služby Průzkumník dat Azure:
1. [Horizontální škálování](manage-cluster-horizontal-scaling.md), označované také jako vertikální navýšení nebo snížení kapacity.
2. Vertikální škálování, nazývá se také vertikálně navýšit nebo snížit kapacitu.

Tento článek ukazuje, jak spravovat vertikální škálování clusteru.

Nastavování velikosti clusteru správně je důležité pro výkon Průzkumník dat Azure. Ale nemůže být předpovězen nároky na cluster s absolutní přesnost. Velikost statické clusteru může vést k nízkého využití nebo overutilization, ani jedno z nich je ideální. Lepším řešením je *škálování* cluster, přidávání a odebírání kapacitu a prostředky procesoru s měnícími poptávky. 

## <a name="steps-to-configure-vertical-scaling"></a>Postup konfigurace vertikálního škálování

1. Přejděte ke svému clusteru. V části **nastavení**vyberte **vertikálně navýšit kapacitu**.

    Se zobrazí seznam dostupných skladových položek. Například následující obrázek, pouze čtyři skladové položky jsou k dispozici.

    ![Vertikální navýšení kapacity](media/manage-cluster-vertical-scaling/scale-up.png)

    Skladové položky jsou zakázané, protože aktuální SKU jsou nebo nejsou k dispozici v oblasti, kde se nachází clusteru.

1. Chcete-li změnit skladovou jednotku, vyberte SKU chcete a zvolte **vyberte** tlačítko.

> [!NOTE]
> Vertikální škálování proces může trvat několik minut a během této doby se vám váš cluster. Všimněte si, že škálování může poškodit svůj výkon clusteru.

Nyní jste provedli operaci vertikální navýšení nebo snížení pro váš cluster Průzkumník dat Azure.

Pokud potřebujete pomoc s problémy škálování clusterů [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) na webu Azure Portal.

## <a name="next-steps"></a>Další kroky

* [Správa clusteru horizontální škálování](manage-cluster-horizontal-scaling.md) pro dynamicky horizontálně navyšovat kapacitu počtu instancí na základě metrik, který zadáte.

* Monitorujte využití prostředků podle tohoto článku: [Sledování výkonu, stavu a využití s metrikami Průzkumník dat Azure](using-metrics.md).

