---
title: Sledování využití a odhadované náklady v Azure monitorování | Microsoft Docs
description: Přehled procesu používání Azure sledování využití a odhadované náklady na stránku
author: dalekoetke
manager: carmonmills
editor: mrbullwinkle
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: Dale.Koetke;mbullwin
ms.openlocfilehash: ce295c449b01de4fa99df9198805a6b0727c0d18
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="monitoring-usage-and-estimated-costs"></a>Sledování využití a odhadované náklady

V centru monitorování na portálu Azure **využití a odhadované náklady** stránky je určena k pochopení využití ze základní monitorovací funkce, jako například [výstrahy, metriky, oznámení](https://azure.microsoft.com/pricing/details/monitor/), [Protokolu analýzy](https://azure.microsoft.com/pricing/details/log-analytics/) a [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Pro zákazníky na cenovou plány, které jsou k dispozici před 2018 duben to také zahrnuje zakoupili prostřednictvím k přehledům využití analýzy protokolů a nabízet Analytics.

Na této stránce mohou uživatelé zobrazit jejich využití těchto prostředků za posledních 31 dní agregovat podle předplatného, procházení-in zobrazíte trend využití tohoto období. Je velké množství dat potřebných k přijetí změn dohromady a nastavit tento odhad, tak buďte trpěliví jako načtení stránky.
Tady je příklad zobrazuje odhad příslušné náklady a sledování využití:

![Snímek obrazovky portálu odhadované náklady a využití](./media/monitoring-usage-and-estimated-costs/001.png)

Kliknutím na odkaz ve sloupci měsíční využití se otevře graf trendů využití za posledních 31 dnů období:

![Zahrnuto na uzel snímek 671.47 GB](./media/monitoring-usage-and-estimated-costs/002.png)

Tady je jiné podobné využití a náklady shrnutí, v takovém případě pro předplatné za nové duben 2018 na základě spotřeby cenový model. Sledovat nedostatek žádné fakturaci na základě uzlu a že přijímání dat a uchování pro analýzy protokolů a Application Insights se teď hlásí na nové běžné měření.

![Snímek obrazovky portálu odhadované náklady a využití](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Nový cenový model

V dubnu 2018 byl vydán nový cenový model monitorování.  Tato funkce cloud zařízení, na základě spotřeby ceny. Platíte jenom pro co používáte, aniž by na základě uzlu závazky. Podrobnosti o nový cenový model jsou k dispozici pro [výstrahy, metriky, oznámení](https://azure.microsoft.com/pricing/details/monitor/), [analýzy protokolů](https://azure.microsoft.com/pricing/details/log-analytics/) a [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

Pro zákazníky registrace analýzy protokolů nebo Application Insights po 2. dubna 2018 nový cenový model je jedinou možností. Pro zákazníky, kteří již byla použití těchto služeb přesunu na nový cenový model je volitelný.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Hodnocení dopadu nový cenový model

Nový cenový model bude mít důsledky pro různé pro každého zákazníka a to na základě jejich monitorování způsobů využití. Pro zákazníky, kteří už používají analýzy protokolů nebo Application Insights před 2 2018 duben **využití a odhadované náklady** stránky v nástroji Sledování Azure poskytuje způsob, jak odhadnout všechny změny v náklady, pokud přejdete na nový cenový model a poskytuje způsob, jak přesunout předplatné do nového modelu. Pro většinu zákazníků nový cenový model bude výhodné, ale pro zákazníky s vzorce používání zejména vysoké dat nebo v oblastech vyšší náklady, to nemusí být případ.

Zobrazíte odhad náklady pro odběry jste vybrali na **využití a odhadované náklady** blue hlavičky v horní části stránky klikněte na tlačítko. Je vhodné provést tento jeden odběr současně, protože se na úrovni, na které můžete používat nový cenový model.

![Snímek obrazovky vyberte model – ceny](./media/monitoring-usage-and-estimated-costs/004.png)

Nyní se zobrazí podobné verzi této stránky se zelenou informační zpráva:

![Snímek obrazovky vyberte model – ceny](./media/monitoring-usage-and-estimated-costs/005.png)

V tomto poli se zobrazí jinou sadu měřidla – měřidla, které odpovídají nový cenový model. Například data přijímání měřidla, jako

1. Přehledy a Analytics\Overage na uzel
2. Přehledy a Analytics\Included na uzel
3. Nadlimitní Data Insights\Basic aplikací
4. Insights\Included Data aplikací

spojují se do nové běžné data přijímání měřidla názvem **sdílené přijímání Services\Data** vzhledem k tomu, že nový cenový model nemá oprávnění data zahrnuté podle uzlu.

Další změnou uvidíte je dat nasávaného analýzy protokolů nebo Application Insights v oblastech s vyšší poplatky za se zobrazí s novou místní měřidla tak, aby správně odrážela, například **"přijímání dat (střed USA – západ)**.

> [!NOTE]
> Náklady na odhadované za předplatné není dvoufaktorového do úrovně podle uzlu oprávnění účtu předplatného služby Operations Management Suite (OMS). Přečtěte si vaším zástupcem účet pro podrobné informace o nový cenový model v tomto případě.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Nové ceny modelu a oprávnění předplatné služby Operations Management Suite

Zákazníci, kteří si zakoupili služby Microsoft Operations Management Suite E1 a E2 jsou způsobilé pro oprávnění přijímání dat podle uzlu pro [analýzy protokolů](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite) a [Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-pricing#the-price-plans). Pro příjem těchto oprávnění pro analýzy protokolů pracovních prostorů nebo prostředkům v daném předplatném, že je předplatné cenový model musí zůstat v dubnu pre 2018 cenovou Application Insights modelu kde analýzy protokolů "za uzlu (OMS)" cenová úroveň a Application Insights "Enterprise" ceny plánu jsou k dispozici. V závislosti na počtu uzlů sady, které vaše organizace koupila přesunutí některých odběrů nový cenový model stále může být výhodné, ale vyžaduje pečlivě zvážit. 

## <a name="changes-when-moving-to-the-new-pricing-model"></a>Změny při přesunu na nový cenový model

Přesun předplatné do nový cenový model bude změnit cenovou úroveň pro každý analýzy protokolů na nové vrstvě za GB a přesune všechny (nazývané "pergb2018" ve službě Správce prostředků Azure). Tento přesun se také změní všechny prostředky Application Insights v plánu podnikového na základní plán. Tyto důsledky jsou uvedeny na odhad nákladů popsané výše. 

## <a name="moving-to-the-new-pricing-model"></a>Přesunutí na nový cenový model

Pokud jste se rozhodli použít nový cenový model pro předplatné, klikněte na tlačítko **ceny Výběr modelu** možnost v horní části **využití a odhadované náklady** stránky:

![Sledování využití odhadované náklady na nové cenovou modelu snímku obrazovky](./media/monitoring-usage-and-estimated-costs/006.png)

Otevře se **ceny Výběr modelu** stránky, výpis všechny odběry byly zobrazení na předchozí stránce:

![Snímek obrazovky výběru modelu – ceny](./media/monitoring-usage-and-estimated-costs/007.png)

Přesunout nový cenový model předplatné, je právě zaškrtněte políčko a klikněte na tlačítko **Uložit**.  Je můžete přesunout zpět do starší cenový model stejným způsobem. Mějte na paměti, že oprávnění vlastníka nebo přispěvatele předplatného je potřeba změnit cenový model.
