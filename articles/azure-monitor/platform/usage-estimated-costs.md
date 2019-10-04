---
title: Monitorování využití a odhadovaných nákladů v Azure Monitor
description: Přehled procesu použití stránky s předpokládanými náklady na Azure Monitor a využití
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 787618b59cd18dd4c38892ddf0861808211671cb
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936624"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Monitorování využití a odhadovaných nákladů v Azure Monitor

> [!NOTE]
> Tento článek popisuje, jak zobrazit využití a odhadované náklady napříč více funkcemi monitorování Azure pro různé cenové modely. Mezi související články pro konkrétní součásti Azure Monitor patří:
> - [Správa využití a nákladů pomocí Azure Monitorch protokolů](manage-cost-storage.md) popisuje, jak řídit náklady změnou doby uchovávání dat a jak analyzovat a upozorňovat na využití vašich dat.
> - [Správa využití a nákladů pro Application Insights](../../azure-monitor/app/pricing.md) popisuje, jak analyzovat využití dat v Application Insights.

V centru monitorování Azure Portal se na stránce **využití a odhadované náklady** vysvětlují využívání základních monitorovacích funkcí, jako jsou například [výstrahy, metriky, oznámení](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)a [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Pro zákazníky, kteří mají cenové tarify k dispozici před dubna 2018, to zahrnuje i Log Analytics využití zakoupené prostřednictvím nabídky Insights a Analytics.

Na této stránce můžou uživatelé zobrazit své využití prostředků za posledních 31 dnů, agregované podle předplatného. V podrobnostech ukazují trendy využití za 31 dní. Pro tento odhad je potřeba udělat spoustu dat, a proto se prosím ujistěte, že se stránka načítá.

Tento příklad ukazuje využití monitorování a odhad výsledných nákladů:

![Snímek obrazovky portálu využití a odhadované náklady](./media/usage-estimated-costs/001.png)

Vyberte odkaz ve sloupci měsíční využití a otevřete graf zobrazující trendy využití během posledních 31 dnů:

![Snímek obrazovky pro pruhový graf na panelu uzlů](./media/usage-estimated-costs/002.png)

Zde je uvedeno další podobné informace o využití a nákladech. Tento příklad ukazuje předplatné v novém cenovém modelu založeném na spotřebě z dubna 2018. Všimněte si chybějících faktur založených na uzlech. Přijímání a uchovávání dat pro Log Analytics a Application Insights se teď hlásí na novém běžném měřiči.

![Využití a odhadované náklady – snímek obrazovky portálu – ceny po 2018. dubna](./media/usage-estimated-costs/003.png)

## <a name="pricing-model"></a>Cenový model

V dubnu 2018 [byl vydán nový cenový model monitorování](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/).  Tato funkce nabízí ceny založené na využití v cloudu ("průběžné platby"). Platíte jenom za to, co používáte, bez závazků na základě uzlů. Podrobnosti o novém cenovém modelu jsou k dispozici pro [výstrahy, metriky, oznámení](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) a [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Kromě modelu s průběžnými platbami, v září 2019 jsme přidali rezervace kapacity pro Log Analytics, které vám umožní v porovnání s průběžnými platbami ušetřit až 25%. Cena za rezervaci kapacity vám umožní koupit rezervaci od 100 GB za den. Veškeré využití nad úrovní rezervace se bude účtovat podle tarifu průběžných plateb. [Přečtěte si další informace](https://azure.microsoft.com/pricing/details/monitor/) o cenách rezervací kapacity.

Pro zákazníky, kteří se zaregistrují Log Analytics nebo Application Insights po 2. dubnu 2018 se jedná o jedinou možnost Nový cenový model. Pro zákazníky, kteří už tyto služby používají, je přesun na nový cenový model volitelný.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Vyhodnocení dopadu nového cenového modelu
Nový cenový model bude mít různé dopady na jednotlivé zákazníky na základě jejich způsobů použití monitorování. Pro zákazníky, kteří používali Log Analytics nebo Application Insights před 2. dubna 2018 se stránka **využití a odhadované náklady** v Azure monitor odhaduje při přesunu do nového cenového modelu jakákoli změna v ceně. Nabízí způsob, jak přesunout předplatné do nového modelu. U většiny zákazníků bude výhodné nový cenový model. Pro zákazníky, kteří mají obzvláště vysoké vzorce využití dat nebo v oblastech s vyššími náklady, to nemusí být případ.

Pokud chcete zobrazit odhad nákladů na předplatná, která jste vybrali na stránce **využití a odhadované náklady** , vyberte modrou hlavičku v horní části stránky. Toto předplatné je nejlepší udělat v jednom okamžiku, protože to je úroveň, na které se dá nový cenový model přijmout.

![Monitorovat využití a odhadované náklady na novém snímku obrazovky cenového modelu](./media/usage-estimated-costs/004.png)

Na nové stránce se zobrazí podobná verze předchozí stránky se zelenou bannerovou hlavičkou:

![Monitorovat využití a odhadované náklady v aktuálním cenovém modelu – snímek obrazovky](./media/usage-estimated-costs/005.png)

Na stránce se zobrazí také jiná sada měřičů, která odpovídá novému cenovému modelu. Tento seznam je příkladem:

- Přehledy a Analytics\Overage na uzel
- Přehledy a Analytics\Included na uzel
- Nadlimitní data aplikace Insights\Basic
- Data Insights\Included aplikace

Nový cenový model nemá zahrnuté datové alokace založené na uzlech. Proto jsou tyto měřiče příjmu dat zkombinovány do nového měření běžného příjmu dat nazývaného ingestování **sdílených Services\Data**. 

K dispozici je další změna dat, která se ingestují do Log Analytics nebo Application Insights v oblastech s vyššími náklady. Data pro tyto oblasti s vysokými náklady se budou zobrazovat s novými měřiči. Příkladem je **přijímání dat (USA – Středozápad)** .

> [!NOTE]
> Odhadované náklady na předplatné se nefaktorují do nároků na úrovni účtu v rámci odběru Operations Management Suite (OMS). Podrobnější diskusi k novému cenovému modelu v tomto případě najdete v zástupci svého účtu.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Nový cenový model a nároky na předplatné Operations Management Suite

Zákazníci, kteří si zakoupili Microsoft Operations Management Suite E1 a E2, mají nárok na nároky na přijímání dat pro [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) a [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing)pro jednotlivé uzly. Pro získání těchto nároků pro Log Analytics pracovní prostory nebo prostředky Application Insights v daném předplatném: 

- Cenový model předplatného musí zůstat v modelu staršího než dubna 2018.
- Log Analytics pracovní prostory by měly používat cenové úrovně "per-Node (OMS)".
- Application Insights prostředky by měly používat cenový plán "Enterprise".

V závislosti na počtu uzlů sady, kterou vaše organizace zakoupila, může být výhodnější přesunout některá předplatná do nového cenového modelu, ale to vyžaduje pečlivou pozornost. Obecně platí, že je vhodné jednoduše zůstat v modelu předběžného 2018, jak je popsáno výše.

> [!WARNING]
> Pokud vaše organizace zakoupila Microsoft Operations Management Suite E1 a E2, obvykle je nejlepší zachovat vaše předplatná v cenovém modelu starším než dubna 2018. 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Změny, když přesouváte do nového cenového modelu

Nový cenový model zjednodušuje Log Analytics a Application Insights cenové možnosti jenom na jednu úroveň (nebo plán). Při přesunu předplatného na nový cenový model dojde k následujícímu:

- Změňte cenovou úroveň pro každý Log Analytics na novou GB na GB (označovanou jako "pergb2018" v Azure Resource Manager).
- Všechny Application Insights prostředky v plánu Enterprise se změní na plán Basic.

Odhad nákladů zobrazuje důsledky těchto změn.

> [!WARNING]
> Pokud používáte Azure Resource Manager nebo PowerShell k nasazení [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) nebo [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) v předplatném, které jste přesunuli do nového cenového modelu, je tady důležitá Poznámka. Pokud zadáte cenovou úroveň/plán, který je jiný než "pergb2018" pro Log Analytics nebo "Basic" pro Application Insights namísto selhání nasazení kvůli zadání neplatné cenové úrovně nebo plánu, bude úspěšný, **ale bude používat jenom platnou cenovou úroveň/ plán** (netýká se Log Analytics úrovně Free, kde je vygenerována neplatná zpráva cenové úrovně).
>

## <a name="moving-to-the-new-pricing-model"></a>Přesun na nový cenový model

Pokud jste se rozhodli pro dané předplatné přijmout nový cenový model, přejděte ke každému Application Insights prostředku, otevřete **využití a odhadované náklady** a ujistěte se, že se nachází v cenové úrovni Basic, a pak přejděte ke každému pracovnímu prostoru Log Analytics a otevřete každou z **nich. Stránku cenová úroveň** a přejděte na cenovou úroveň na **GB (2018)** . 

> [!NOTE]
> Požadavek, aby všechny prostředky Application Insights a pracovní prostory Log Analytics v rámci daného předplatného přijaly nejnovější cenový model, který je teď odebraný, což umožňuje větší flexibilitu a snazší konfiguraci. 

## <a name="automate-moving-to-the-new-pricing-model"></a>Automatizace přesunu na nový cenový model

Jak bylo uvedeno výše, již není nutné pro přesun všech prostředků monitorování v rámci předplatného na nový cenový model současně, takže akce ``migratetonewpricingmodel`` již nebude mít žádný vliv. Nyní můžete přesouvat Application Insights prostředky a Log Analytics pracovní prostory samostatně do nejnovějších cenových úrovní.  

Automatizace této změny je popsána v Application Insights pomocí [set-AzureRmApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/azurerm.applicationinsights/set-azurermapplicationinsightspricingplan) s ``-PricingPlan "Basic"`` a Log Analytics pomocí [set-AzureRmOperationalInsightsWorkspace](https://docs.microsoft.com/powershell/module/AzureRM.OperationalInsights/Set-AzureRmOperationalInsightsWorkspace) s ``-sku "PerGB2018"``. 
