---
title: Škálování clusteru Průzkumník dat Azure tak, aby vyhovovaly měnících se požadavků
description: Tento článek popisuje kroky pro horizontální navýšení kapacity a škálování na méně instancí clusteru Průzkumník dat Azure na základě změny poptávky.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 15ef5282e0a073e870f2ac12b5fc442407535770
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408438"
---
# <a name="manage-cluster-scale-out-to-accommodate-changing-demand"></a>Správa clusteru škálování tak, aby vyhovovaly měnících se požadavků

Nastavování velikosti clusteru správně je důležité pro výkon Průzkumník dat Azure. Ale nemůže být předpovězen nároky na cluster s 100 % přesností. Velikost statické clusteru může vést k snížení využití nebo nadměrné využívání, ani jedno z nich je ideální. Lepším řešením je *škálování* cluster, přidávání a odebírání kapacitu s měnícími poptávky. Existují dva pracovní postupy pro škálování, škálování zatížení a škálování na víc systémů. Tento článek vysvětluje, škálování na více instancí pracovního postupu.

V tomto článku se dozvíte, jak spravovat cluster horizontální navýšení kapacity, označované také jako automatické škálování. Automatické škálování umožňuje škálování automaticky na základě předdefinovaných pravidel a plány počet instancí. Určete nastavení automatického škálování pro váš cluster na webu Azure Portal, jak je popsáno níže.

Přejděte ke svému clusteru a v části **nastavení** vyberte **horizontální navýšení kapacity**. V části **konfigurovat**vyberte **povolit automatické škálování**.

![Povolit automatické škálování](media/manage-cluster-scaling/enable-autoscale.png)

Následující obrázek znázorňuje tok následujících několika krocích. Další podrobnosti níže na obrázku, budeme poskytovat.

![Pravítko měřítka](media/manage-cluster-scaling/scale-rule.png)

1. V části **název nastavení automatického škálování**, zadejte název, jako třeba *horizontální navýšení kapacity: využití mezipaměti*.

1. V části **režim škálování**vyberte **škálování podle metriky**. Tento režim umožňuje dynamické škálování; Můžete také vybrat **škálovat na konkrétní počet instancí**.

1. Vyberte **přidat pravidlo**.

1. V **pravítko měřítka** části na pravé straně, zadejte hodnoty pro každé nastavení.

    **Kritéria**

    | Nastavení | Popis a hodnotu |
    | --- | --- | --- |
    | **Časová agregace** | Vyberte kritéria agregaci, jako je například **průměrné**. |
    | **Název metriky** | Vyberte metriku, chcete, aby operace škálování na základě na, například **využití mezipaměti**. |
    | **Statistika agregačního intervalu** | Výběr mezi **průměrné**, **minimální**, **maximální**, a **součet**. |
    | **– Operátor** | Vyberte příslušnou možnost, například **větší než nebo rovna hodnotě**. |
    | **Prahová hodnota** | Zvolte příslušnou hodnotu. Například 80 % využití mezipaměti je dobrým výchozím bodem. |
    | **Doba trvání (v minutách)** | Zvolte odpovídající množství času pro systém vás pod rouškou zpět při výpočtu metrik. Začněte s výchozí hodnotu 10 minut. |
    |  |  |

    **Akce**

    | Nastavení | Popis a hodnotu |
    | --- | --- | --- |
    | **Operace** | Vyberte odpovídající možnost škálování na méně instancí nebo horizontální navýšení kapacity. |
    | **Počet instancí** | Vyberte počet uzlů nebo instance, které chcete přidat nebo odebrat, když se splní podmínku metriky. |
    | **Přestávka (minuty)** | Zvolte vhodný časový interval čekání mezi operace škálování. Začněte s výchozí pět minut. |
    |  |  |

1. Vyberte **Přidat**.

1. V **limity instancí** části na levé straně, zadejte hodnoty pro každé nastavení.

    | Nastavení | Popis a hodnotu |
    | --- | --- | --- |
    | *Minimum* | Počet instancí, které váš cluster nebude škálování níže, bez ohledu na využití. |
    | *Maximální počet* | Počet instancí, které váš cluster nebude škálování nad, bez ohledu na využití. |
    | *Výchozí* | Výchozí počet instancí, používá, pokud dochází k problémům čtení metrik prostředku. |
    |  |  |

1. Vyberte **Uložit**.

Nyní jste nakonfigurovali operace škálování pro váš cluster Průzkumník dat Azure. Přidáte další pravidlo pro operace škálování na méně instancí. To clusteru umožní škálování dynamicky na základě metrik, který zadáte.

Můžete také provést [škálování clusteru nahoru](manage-cluster-scale-up.md) vhodné k určení velikosti clusteru.

Pokud potřebujete pomoc s problémy škálování clusteru, otevřete žádost o podporu v [webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
