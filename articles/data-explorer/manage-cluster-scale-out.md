---
title: Škálování clusteru Průzkumník dat Azure tak, aby vyhovovaly měnících se požadavků
description: Tento článek popisuje kroky pro horizontální navýšení kapacity a škálování na méně instancí clusteru Průzkumník dat Azure na základě změny poptávky.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 38dc7b70630276d51c75ca7e87f0b69ea7fe040a
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735617"
---
# <a name="manage-cluster-scale-out-to-accommodate-changing-demand"></a>Správa clusteru pro horizontální navýšení kapacity tak, aby vyhovovaly měnících se požadavků

Nastavování velikosti clusteru správně je důležité pro výkon Průzkumník dat Azure. Ale nemůže být předpovězen nároky na cluster s 100 % přesností. Velikost statické clusteru může vést k snížení využití nebo nadměrné využívání, ani jedno z nich je ideální. Lepším řešením je *škálování* cluster, přidávání a odebírání kapacitu s měnícími poptávky. Tento článek ukazuje, jak spravovat cluster horizontální navýšení kapacity.

Přejděte ke svému clusteru a v části **nastavení** vyberte **horizontální navýšení kapacity**. V části **konfigurovat**vyberte **povolit automatické škálování**.

![Povolit automatické škálování](media/manage-cluster-scaling/enable-autoscale.png)

Následující obrázek znázorňuje tok následujících několika krocích. Další podrobnosti níže na obrázku, budeme poskytovat.

![Pravítko měřítka](media/manage-cluster-scaling/scale-rule.png)

1. V části **název nastavení automatického škálování**, zadejte název, jako třeba *horizontální navýšení kapacity: využití mezipaměti*.

1. V části **režim škálování**vyberte **škálování podle metriky**. Tento režim umožňuje dynamické škálování; Můžete také vybrat **škálovat na konkrétní počet instancí**.

1. Vyberte **přidat pravidlo**.

1. V **pravítko měřítka** části na pravé straně, zadejte hodnoty pro každé nastavení.

    | Nastavení | Popis a hodnotu |
    | --- | --- | --- |
    | **Časová agregace** | Vyberte kritéria agregaci, jako je například **průměrné**. |
    | **Název metriky** | Vyberte metriku, chcete, aby operace škálování na základě na, například **využití mezipaměti**. |
    | **Statistika agregačního intervalu** | Výběr mezi **průměrné**, **minimální**, **maximální**, a **součet**. |
    | **– Operátor** | Vyberte příslušnou možnost, například **větší než nebo rovna hodnotě**. |
    | **Prahová hodnota** | Zvolte příslušnou hodnotu. Například 80 % využití mezipaměti je dobrým výchozím bodem. |
    | **Doba trvání** | Zvolte odpovídající množství času pro systém vás pod rouškou zpět při výpočtu metrik. Začněte s výchozí deset minut. |
    | **Operace** | Vyberte příslušnou možnost v vertikálně nebo horizontálně navýšit kapacitu. |
    | **Počet instancí** | Vyberte počet uzlů nebo instance, které chcete přidat nebo odebrat, když se splní podmínku metriky. |
    | **Přestávka (minuty)** | Zvolte vhodný časový interval čekání mezi operace škálování. Začněte s výchozí pět minut. |
    |  |  |

1. Vyberte **Přidat**.

1. V **limity instancí** části na levé straně, zadejte hodnoty pro každé nastavení.

    | Nastavení | Popis a hodnotu |
    | --- | --- | --- |
    | *Minimum* | Toto je počet instancí, které váš cluster nebude škálování níže, bez ohledu na využití. |
    | *Maximální počet* | Toto je počet instancí, které váš cluster nebude škálování nad, bez ohledu na využití. |
    | *Výchozí* | Výchozí počet instancí, používá, když se chyba při čtení metrik prostředku. |
    |  |  |

1. Vyberte **Uložit**.

Nyní jste nakonfigurovali operace škálování pro váš cluster Průzkumník dat Azure. Přidáte další pravidlo pro operace škálování na méně instancí. To clusteru umožní škálování dynamicky na základě metrik, který zadáte.

Pokud potřebujete pomoc s problémy škálování clusteru, otevřete žádost o podporu v [webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).