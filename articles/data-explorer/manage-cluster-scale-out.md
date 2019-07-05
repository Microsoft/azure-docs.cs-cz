---
title: Horizontální navýšení kapacity clusteru služby Průzkumník dat Azure
description: Tento článek popisuje kroky pro horizontální navýšení kapacity a škálování v clusteru Azure Průzkumník dat na základě změny poptávky.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 882f44683bbdc7f4eb49ff4912ca7a33187afbf8
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537905"
---
# <a name="manage-cluster-scale-out-to-accommodate-changing-demand"></a>Správa clusteru škálování tak, aby vyhovovaly měnících se požadavků

Nastavování velikosti clusteru správně je důležité pro výkon Průzkumník dat Azure. Ale nemůže být předpovězen nároky na cluster s absolutní přesnost. Velikost statické clusteru může vést k nízkého využití nebo overutilization, ani jedno z nich je ideální.

Lepším řešením je *škálování* cluster, přidávání a odebírání kapacitu s měnícími poptávky. Existují dva pracovní postupy pro škálování: škálování zatížení a škálování na víc systémů. Tento článek vysvětluje, škálování na více instancí pracovního postupu.

Tento článek ukazuje, jak spravovat cluster horizontální navýšení kapacity, označované také jako automatické škálování. Automatické škálování umožňuje škálovat počet instancí automaticky na základě předdefinovaných pravidel a plány. Zadejte nastavení automatického škálování pro váš cluster na webu Azure Portal, jak je popsáno v tomto článku.

## <a name="steps-to-configure-autoscale"></a>Postup konfigurace automatického škálování

Na webu Azure Portal přejděte do Průzkumníku dat prostředku clusteru. V části **nastavení** záhlaví, vyberte **horizontální navýšení kapacity**. Na **konfigurovat** kartu, vyberte možnost **povolit automatické škálování**.

   ![Povolit automatické škálování](media/manage-cluster-scaling/enable-autoscale.png)

Následující obrázek znázorňuje tok následujících několika krocích. Další podrobnosti podle obrázku.

1. V **název nastavení automatického škálování** pole, zadejte název, jako třeba *horizontální navýšení kapacity: využití mezipaměti*. 

   ![Pravítko měřítka](media/manage-cluster-scaling/scale-rule.png)

2. Pro **režim škálování**vyberte **škálování podle metriky**. Tento režim umožňuje dynamické škálování. Můžete také vybrat **škálovat na konkrétní počet instancí**.

3. Vyberte **+ přidat pravidlo**.

4. V **pravítko měřítka** části na pravé straně, zadejte hodnoty pro každé nastavení.

    **Kritéria**

    | Nastavení | Popis a hodnotu |
    | --- | --- |
    | **Časová agregace** | Vyberte kritéria agregaci, jako je například **průměrné**. |
    | **Název metriky** | Vyberte metriku, chcete, aby operace škálování na základě na, například **využití mezipaměti**. |
    | **Statistika agregačního intervalu** | Výběr mezi **průměrné**, **minimální**, **maximální**, a **součet**. |
    | **– Operátor** | Vyberte příslušnou možnost, například **větší než nebo rovna hodnotě**. |
    | **Prahová hodnota** | Zvolte příslušnou hodnotu. Pro využití mezipaměti, třeba 80 procent je dobrým výchozím bodem. |
    | **Doba trvání (v minutách)** | Zvolte odpovídající množství času pro systém vás pod rouškou zpět při výpočtu metrik. Začněte s výchozí hodnotu 10 minut. |
    |  |  |

    **Akce**

    | Nastavení | Popis a hodnotu |
    | --- | --- |
    | **Operace** | Vyberte příslušnou možnost v vertikálně nebo horizontálně navýšit kapacitu. |
    | **Počet instancí** | Vyberte počet uzlů nebo instance, které chcete přidat nebo odebrat, když se splní podmínku metriky. |
    | **Přestávka (minuty)** | Zvolte vhodný časový interval čekání mezi operace škálování. Začněte s výchozí pět minut. |
    |  |  |

5. Vyberte **Přidat**.

6. V **limity instancí** části na levé straně, zadejte hodnoty pro každé nastavení.

    | Nastavení | Popis a hodnotu |
    | --- | --- |
    | **Minimum** | Počet instancí, které váš cluster nebude škálování níže, bez ohledu na využití. |
    | **Maximální počet** | Počet instancí, které váš cluster nebude škálování nad, bez ohledu na využití. |
    | **Výchozí** | Výchozí počet instancí. Toto nastavení se používá, pokud dochází k problémům s čtení metrik prostředku. |
    |  |  |

7. Vyberte **Uložit**.

Nyní jste nakonfigurovali operace škálování pro váš cluster Průzkumník dat Azure. Přidáte další pravidlo pro operace škálování na méně instancí. Tato konfigurace umožňuje škálovat cluster dynamicky na základě metrik, který zadáte.

Pokud potřebujete pomoc s problémy škálování clusterů [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) na webu Azure Portal.

## <a name="next-steps"></a>Další postup

* [Sledování výkonu, stavu a využití s metrikami Průzkumník dat Azure](using-metrics.md)

* [Správa vertikálně navýšit kapacitu clusteru](manage-cluster-scale-up.md) vhodné k určení velikosti clusteru.
