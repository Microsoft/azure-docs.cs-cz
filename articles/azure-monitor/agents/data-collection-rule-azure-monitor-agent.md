---
title: Konfigurace shromažďování dat pro agenta Azure Monitorho (Preview)
description: V této části najdete popis postupu vytvoření pravidla shromažďování dat pro shromažďování dat z virtuálních počítačů pomocí agenta Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2021
ms.openlocfilehash: 8943986bf8e8c082889d3a0b18618ac54c75e6d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105022972"
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

> [!IMPORTANT]
> V současné době se jedná o známý problém, kdy Pokud pravidlo shromažďování dat vytvoří spravovanou identitu na virtuálním počítači, který už má přiřazenou spravovanou identitu, je identita přiřazená uživateli zakázaná.

V nabídce **Azure monitor** v Azure Portal vyberte **pravidla shromažďování dat** z oddílu **Nastavení** . Kliknutím na **Přidat** přidejte nové pravidlo shromažďování dat a přiřazení.

[![Pravidla shromažďování dat](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png#lightbox)

Kliknutím na **Přidat** vytvořte nové pravidlo a sadu přidružení. Zadejte **název pravidla** a zadejte **předplatné** a **skupinu prostředků**. Určuje, kde bude vytvořena DCR. Virtuální počítače a jejich přidružení mohou být v rámci předplatného nebo skupiny prostředků v tenantovi.

[![Základy pravidla shromažďování dat](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png#lightbox)

Na kartě **virtuální počítače** přidejte virtuální počítače, které by měly mít použité pravidlo shromažďování dat. Měly by být uvedené virtuální počítače Azure i servery s podporou ARC Azure ve vašem prostředí. Agent Azure Monitor se nainstaluje na virtuální počítače, které ještě nemají nainstalované.

[![Virtuální počítače pravidla shromažďování dat](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

Na kartě **shromáždit a poskytnout** klikněte na **Přidat zdroj dat** a přidejte zdroj dat a cílovou sadu. Vyberte **typ zdroje dat** a zobrazí se odpovídající podrobnosti k výběru. Pro čítače výkonu můžete vybrat z předdefinované sady objektů a jejich vzorkovací frekvence. Pro události můžete vybrat ze sady protokolů nebo zařízení a úrovně závažnosti. 

[![Základní zdroj dat](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


Chcete-li určit další protokoly a čítače výkonu z [aktuálně podporovaných zdrojů dat](azure-monitor-agent-overview.md#data-sources-and-destinations) nebo filtrovat události pomocí dotazů XPath, vyberte možnost **vlastní**. Pak můžete zadat [cestu XPath ](https://www.w3schools.com/xml/xpath_syntax.asp) pro všechny konkrétní hodnoty, které mají být shromažďovány. Příklady najdete v části [Sample DCR](data-collection-rule-overview.md#sample-data-collection-rule) .

[![Vlastní zdroj dat](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

Na kartě **cíl** přidejte jeden nebo více cílů pro zdroj dat. Zdroje dat událostí a syslog systému Windows lze odesílat pouze do protokolů Azure Monitor. Čítače výkonu se můžou posílat do obou Azure Monitor metrik a protokolů Azure Monitor.

[![Cíl](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png#lightbox)

Klikněte na **Přidat zdroj dat** a pak **Zkontrolujte + vytvořit** a zkontrolujte podrobnosti pravidla shromažďování dat a přidružení k sadě virtuálních počítačů. Kliknutím na **vytvořit** vytvořte.

> [!NOTE]
> Po vytvoření pravidla shromažďování dat a přidružení může trvat až 5 minut, než se data odešlou do cílů.

## <a name="limit-data-collection-with-custom-xpath-queries"></a>Omezení shromažďování dat pomocí vlastních dotazů XPath
Vzhledem k tomu, že se vám účtují data shromážděná v pracovním prostoru Log Analytics, měli byste shromažďovat jenom data, která potřebujete. Při použití základní konfigurace v Azure Portal máte jenom omezené možnosti filtrovat události, které se mají shromažďovat. Pro protokoly aplikací a systému se jedná o všechny protokoly s určitou závažností. V protokolech zabezpečení se jedná o všechny úspěšné audity nebo všechny protokoly neúspěšných auditů.

Chcete-li zadat další filtry, je nutné použít vlastní konfiguraci a zadat výraz XPath, který odfiltruje události, které ne. Položky XPath jsou zapsány ve formuláři `LogName!XPathQuery` . Například můžete chtít vracet pouze události z protokolu událostí aplikace s ID události 1035. Dotaz xpathquery pro tyto události by byl `*[System[EventID=1035]]` . Vzhledem k tomu, že chcete načíst události z protokolu událostí aplikace, bude cesta XPath `Application!*[System[EventID=1035]]`

Seznam omezení, která podporuje protokol událostí systému Windows, naleznete v tématu [omezení xpath 1,0](/windows/win32/wes/consuming-events#xpath-10-limitations) .

> [!TIP]
> `Get-WinEvent`K otestování platnosti dotaz xpathquery použijte rutinu PowerShellu s `FilterXPath` parametrem. Příklad ukazuje následující skript.
> 
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
>
> - Pokud jsou vráceny události, dotaz je platný.
> - Pokud se zobrazí zpráva *, že se nenašly žádné události, které by odpovídaly zadaným kritériím výběru*, dotaz může být platný, ale na místním počítači neexistují žádné odpovídající události.
> - Pokud se zobrazí zpráva, *že zadaný dotaz není platný* , syntaxe dotazu je neplatná. 

V následující tabulce jsou uvedeny příklady pro filtrování událostí pomocí vlastního XPath.

| Description |  XPath |
|:---|:---|
| Shromažďovat pouze systémové události s ID události = 4648 |  `System!*[System[EventID=4648]]`
| Shromažďovat pouze systémové události s ID události = 4648 a názvem procesu consent.exe | `Security!*[System[(EventID=4648)]] and *[EventData[Data[@Name='ProcessName']='C:\Windows\System32\consent.exe']]` |
| Shromáždí všechny události kritické, chyby, upozornění a informací z protokolu událostí systému s výjimkou ID události = 6 (načtený ovladač). |  `System!*[System[(Level=1 or Level=2 or Level=3) and (EventID != 6)]]` |
| Shromažďovat všechny události zabezpečení úspěšné a neúspěšné s výjimkou ID události 4624 (úspěšné přihlášení) |  `Security!*[System[(band(Keywords,13510798882111488)) and (EventID != 4624)]]` |


## <a name="create-rule-and-association-using-rest-api"></a>Vytvoření pravidla a přidružení pomocí REST API

Použijte následující postup k vytvoření pravidla shromažďování dat a přidružení pomocí REST API.

1. Ručně vytvořte soubor DCR pomocí formátu JSON, který je zobrazený v [ukázkové DCR](data-collection-rule-overview.md#sample-data-collection-rule).

2. Vytvořte pravidlo pomocí [REST API](/rest/api/monitor/datacollectionrules/create#examples).

3. Vytvořte přidružení pro každý virtuální počítač k pravidlu shromažďování dat pomocí [REST API](/rest/api/monitor/datacollectionruleassociations/create#examples).


## <a name="create-association-using-resource-manager-template"></a>Vytvoření asociace pomocí šablony Správce prostředků

Pomocí šablony Správce prostředků nemůžete vytvořit pravidlo shromažďování dat, ale můžete vytvořit přidružení mezi virtuálním počítačem Azure nebo serverem s podporou ARC Azure pomocí šablony Správce prostředků. Ukázkové šablony najdete [v tématu Správce prostředků ukázek šablon pro pravidla shromažďování dat v Azure monitor](./resource-manager-data-collection-rules.md) .



## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [agentovi Azure monitor](azure-monitor-agent-overview.md).
- Přečtěte si další informace o [pravidlech shromažďování dat](data-collection-rule-overview.md).
