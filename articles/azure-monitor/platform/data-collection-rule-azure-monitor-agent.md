---
title: Konfigurace shromažďování dat pro agenta Azure Monitorho (Preview)
description: V této části najdete popis postupu vytvoření pravidla shromažďování dat pro shromažďování dat z virtuálních počítačů pomocí agenta Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/19/2020
ms.openlocfilehash: 94c926c555a4bc96ac3c6fbe773650e16554bcf2
ms.sourcegitcommit: 5ae2f32951474ae9e46c0d46f104eda95f7c5a06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "95315698"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>Konfigurace shromažďování dat pro agenta Azure Monitorho (Preview)

Pravidla shromažďování dat (DCR) definují data přicházející do Azure Monitor a určují, kam se mají odeslat. Tento článek popisuje, jak vytvořit pravidlo shromažďování dat pro shromažďování dat z virtuálních počítačů pomocí agenta Azure Monitor.

Úplný popis pravidel shromažďování dat najdete [v tématu pravidla shromažďování dat v Azure monitor (Preview)](data-collection-rule-overview.md).

> [!NOTE]
> Tento článek popisuje, jak nakonfigurovat data pro virtuální počítače s agentem Azure Monitor, který je aktuálně ve verzi Preview. Popis agentů, kteří jsou všeobecně k dispozici a jak je používat ke shromažďování dat, najdete v tématu [Přehled agentů Azure monitor](agents-overview.md) .

## <a name="data-collection-rule-associations"></a>Přidružení pravidel pro shromažďování dat

Pokud chcete pro virtuální počítač použít DCR, vytvořte přidružení pro virtuální počítač. Virtuální počítač může mít přidružení k více chcete odeslat obecnou a k němu může být přidruženo několik virtuálních počítačů. To vám umožní definovat sadu chcete odeslat obecnou, každý příslušný požadavek a použít je jenom pro virtuální počítače, na kterých se vztahují. 

Představte si například prostředí se sadou virtuálních počítačů, na kterých běží obchodní aplikace, a další spuštěné SQL Server. Můžete mít jedno výchozí pravidlo shromažďování dat, které se vztahuje na všechny virtuální počítače a samostatná pravidla shromažďování dat, která shromažďují data konkrétně pro obchodní aplikace a pro SQL Server. Přidružení pro virtuální počítače k pravidlům shromažďování dat by vypadalo podobně jako v následujícím diagramu.

![Diagram znázorňuje virtuální počítače hostující liniovou aplikaci a SQL Server přidružená k pravidlům shromažďování dat s názvem Central-i t-default a LOB-App pro obchodní aplikace a střední-i t-ve výchozím nastavení a s q l pro SQL Server.](media/data-collection-rule-azure-monitor-agent/associations.png)



## <a name="create-rule-and-association-in-azure-portal"></a>Vytvořit pravidlo a přidružení v Azure Portal

Můžete použít Azure Portal k vytvoření pravidla shromažďování dat a přidružení virtuálních počítačů k tomuto pravidlu v rámci předplatného. Agent Azure Monitor se automaticky nainstaluje a vytvoří se spravovaná identita pro všechny virtuální počítače, které ještě nemají nainstalovanou.

V nabídce **Azure monitor** v Azure Portal vyberte **pravidla shromažďování dat** z oddílu **Nastavení** . Kliknutím na **Přidat** přidejte nové pravidlo shromažďování dat a přiřazení.

[![Pravidla shromažďování dat](media/azure-monitor-agent/data-collection-rules.png)](media/azure-monitor-agent/data-collection-rules.png#lightbox)

Kliknutím na **Přidat** vytvořte nové pravidlo a sadu přidružení. Zadejte **název pravidla** a zadejte **předplatné** a **skupinu prostředků**. Určuje, kde bude vytvořena DCR. Virtuální počítače a jejich přidružení mohou být v rámci předplatného nebo skupiny prostředků v tenantovi.

[![Základy pravidla shromažďování dat](media/azure-monitor-agent/data-collection-rule-basics.png)](media/azure-monitor-agent/data-collection-rule-basics.png#lightbox)

Na kartě **virtuální počítače** přidejte virtuální počítače, které by měly mít použité pravidlo shromažďování dat. Měly by být uvedené virtuální počítače Azure i servery s podporou ARC Azure ve vašem prostředí. Agent Azure Monitor se nainstaluje na virtuální počítače, které ještě nemají nainstalované.

[![Virtuální počítače pravidla shromažďování dat](media/azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

Na kartě **shromáždit a poskytnout** klikněte na **Přidat zdroj dat** a přidejte zdroj dat a cílovou sadu. Vyberte **typ zdroje dat** a zobrazí se odpovídající podrobnosti k výběru. Pro čítače výkonu můžete vybrat z předdefinované sady objektů a jejich vzorkovací frekvence. Pro události můžete vybrat ze sady protokolů nebo zařízení a úrovně závažnosti. 

[![Základní zdroj dat](media/azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


Chcete-li zadat další protokoly a čítače výkonu, vyberte možnost **vlastní**. Pak můžete zadat [cestu XPath ](https://www.w3schools.com/xml/xpath_syntax.asp) pro všechny konkrétní hodnoty, které mají být shromažďovány. Příklady najdete v části [Sample DCR](data-collection-rule-overview.md#sample-data-collection-rule) .

[![Vlastní zdroj dat](media/azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

Na kartě **cíl** přidejte jeden nebo více cílů pro zdroj dat. Zdroje dat událostí a syslog systému Windows lze odesílat pouze do protokolů Azure Monitor. Čítače výkonu se můžou posílat do obou Azure Monitor metrik a protokolů Azure Monitor.

[![Cíl](media/azure-monitor-agent/data-collection-rule-destination.png)](media/azure-monitor-agent/data-collection-rule-destination.png#lightbox)

Klikněte na **Přidat zdroj dat** a pak **Zkontrolujte + vytvořit** a zkontrolujte podrobnosti pravidla shromažďování dat a přidružení k sadě virtuálních počítačů. Kliknutím na **vytvořit** vytvořte.

> [!NOTE]
> Po vytvoření pravidla shromažďování dat a přidružení může trvat až 5 minut, než se data odešlou do cílů.


## <a name="create-rule-and-association-using-rest-api"></a>Vytvoření pravidla a přidružení pomocí REST API

Použijte následující postup k vytvoření pravidla shromažďování dat a přidružení pomocí REST API.

1. Ručně vytvořte soubor DCR pomocí formátu JSON, který je zobrazený v [ukázkové DCR](data-collection-rule-overview.md#sample-data-collection-rule).

2. Vytvořte pravidlo pomocí [REST API](/rest/api/monitor/datacollectionrules/create#examples).

3. Vytvořte přidružení pro každý virtuální počítač k pravidlu shromažďování dat pomocí [REST API](/rest/api/monitor/datacollectionruleassociations/create#examples).


## <a name="create-association-using-resource-manager-template"></a>Vytvoření asociace pomocí šablony Správce prostředků

Pomocí šablony Správce prostředků nemůžete vytvořit pravidlo shromažďování dat, ale můžete vytvořit přidružení mezi virtuálním počítačem Azure nebo serverem s podporou ARC Azure pomocí šablony Správce prostředků. Ukázkové šablony najdete [v tématu Správce prostředků ukázek šablon pro pravidla shromažďování dat v Azure monitor](../samples/resource-manager-data-collection-rules.md) .

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [agentovi Azure monitor](azure-monitor-agent-overview.md).
- Přečtěte si další informace o [pravidlech shromažďování dat](data-collection-rule-overview.md).
