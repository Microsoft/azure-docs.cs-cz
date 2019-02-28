---
title: Škálování clusteru Průzkumník dat Azure tak, aby vyhovovaly měnících se požadavků
description: Tento článek popisuje kroky pro horizontální navýšení kapacity a škálování v clusteru Azure Průzkumník dat na základě změny poptávky.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 7b99dcf621eac90d4b75d6827102b656f95aafbb
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958877"
---
# <a name="manage-cluster-scale-out-to-accommodate-changing-demand"></a>Správa clusteru škálování tak, aby vyhovovaly měnících se požadavků

Nastavování velikosti clusteru správně je důležité pro výkon Průzkumník dat Azure. Ale nemůže být předpovězen nároky na cluster s absolutní přesnost. Velikost statické clusteru může vést k nízkého využití nebo overutilization, ani jedno z nich je ideální.

Lepším řešením je *škálování* cluster, přidávání a odebírání kapacitu s měnícími poptávky. Existují dva pracovní postupy pro škálování: škálování zatížení a škálování na víc systémů. Tento článek vysvětluje, škálování na více instancí pracovního postupu.

Tento článek ukazuje, jak spravovat cluster horizontální navýšení kapacity, označované také jako automatické škálování. Automatické škálování umožňuje škálovat počet instancí automaticky na základě předdefinovaných pravidel a plány. Zadejte nastavení automatického škálování pro váš cluster na webu Azure Portal, jak je popsáno v tomto článku.

Přejděte ke svému clusteru. V části **nastavení**vyberte **horizontální navýšení kapacity**. V části **konfigurovat**vyberte **povolit automatické škálování**.

![Povolit automatické škálování](media/manage-cluster-scaling/enable-autoscale.png)

Následující obrázek znázorňuje tok následujících několika krocích. Další podrobnosti najdete níže na obrázku.

![Pravítko měřítka](media/manage-cluster-scaling/scale-rule.png)

1. V **název nastavení automatického škálování** pole, zadejte název, jako třeba *horizontální navýšení kapacity: využití mezipaměti*.

1. Pro **režim škálování**vyberte **škálování podle metriky**. Tento režim umožňuje dynamické škálování. Můžete také vybrat **škálovat na konkrétní počet instancí**.

1. Vyberte **+ přidat pravidlo**.

1. V **pravítko měřítka** části na pravé straně, zadejte hodnoty pro každé nastavení.

    **Kritéria**

    | Nastavení | Popis a hodnotu |
    | --- | --- | --- |
    | **Časová agregace** | Vyberte kritéria agregaci, jako je například **průměrné**. |
    | **Název metriky** | Vyberte metriku, chcete, aby operace škálování na základě na, například **využití mezipaměti**. |
    | **Statistika agregačního intervalu** | Výběr mezi **průměrné**, **minimální**, **maximální**, a **součet**. |
    | **– Operátor** | Vyberte příslušnou možnost, například **větší než nebo rovna hodnotě**. |
    | **Prahová hodnota** | Zvolte příslušnou hodnotu. Pro využití mezipaměti, třeba 80 procent je dobrým výchozím bodem. |
    | **Doba trvání (v minutách)** | Zvolte odpovídající množství času pro systém vás pod rouškou zpět při výpočtu metrik. Začněte s výchozí hodnotu 10 minut. |
    |  |  |

    **Akce**

    | Nastavení | Popis a hodnotu |
    | --- | --- | --- |
    | **Operace** | Vyberte příslušnou možnost v vertikálně nebo horizontálně navýšit kapacitu. |
    | **Počet instancí** | Vyberte počet uzlů nebo instance, které chcete přidat nebo odebrat, když se splní podmínku metriky. |
    | **Přestávka (minuty)** | Zvolte vhodný časový interval čekání mezi operace škálování. Začněte s výchozí pět minut. |
    |  |  |

1. Vyberte **Přidat**.

1. V **limity instancí** části na levé straně, zadejte hodnoty pro každé nastavení.

    | Nastavení | Popis a hodnotu |
    | --- | --- | --- |
    | **Minimum** | Počet instancí, které váš cluster nebude škálování níže, bez ohledu na využití. |
    | **Maximální počet** | Počet instancí, které váš cluster nebude škálování nad, bez ohledu na využití. |
    | **Výchozí** | Výchozí počet instancí. Toto nastavení se používá, pokud dochází k problémům s čtení metrik prostředku. |
    |  |  |

1. Vyberte **Uložit**.

Nyní jste nakonfigurovali operace škálování pro váš cluster Průzkumník dat Azure. Přidáte další pravidlo pro operace škálování na méně instancí. Tato konfigurace umožňuje škálovat cluster dynamicky na základě metrik, který zadáte.

Můžete také [spravovat vertikálně navýšit kapacitu clusteru](manage-cluster-scale-up.md) vhodné k určení velikosti clusteru.

Pokud potřebujete pomoc s problémy škálování clusterů [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) na webu Azure Portal.
