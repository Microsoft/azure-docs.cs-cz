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
ms.openlocfilehash: f25c39b602449be3ab9d1cd7e67d6fcfc78afb17
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="monitoring-usage-and-estimated-costs"></a>Sledování využití a odhadované náklady

V centru monitorování na portálu Azure **využití a odhadované náklady** stránka vysvětluje použití základní monitorovací funkce, jako například [výstrahy, metriky, oznámení](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics ](https://azure.microsoft.com/pricing/details/log-analytics/), a [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Pro zákazníky na cenovou plány, které jsou k dispozici před 2018 duben to také zahrnuje zakoupené prostřednictvím k přehledům využití analýzy protokolů a nabízet Analytics.

Na této stránce mohou uživatelé zobrazit jejich využití prostředků za posledních 31 dní agregovat jedno předplatné. Rozbalení in zobrazit trendy využití po dobu 31 dnů. Velké množství dat je potřeba použít současně pro tento odhad, proto buďte trpěliví jako načtení stránky.

Tento příklad ukazuje odhad příslušné náklady a sledování využití:

![Snímek obrazovky portálu odhadované náklady a využití](./media/monitoring-usage-and-estimated-costs/001.png)

Vyberte odkaz ve sloupci měsíční využití otevřete graf, který zobrazuje trendy využití za posledních 31 dnů období:

![Zahrnuto na uzel panelu grafu – snímek obrazovky](./media/monitoring-usage-and-estimated-costs/002.png)

Tady je jiné podobné využití a souhrn náklady. Tento příklad ukazuje předplatné v nové duben 2018 na základě spotřeby cenový model. Poznámka: nedostatek žádné fakturaci na základě uzlu. Přijímání dat a uchování pro analýzy protokolů a Application Insights jsou nyní ohlášeny nové běžné měření.

![Využití a odhadované náklady na portálu obrazovka – duben 2018 ceny](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Nový cenový model

V dubnu 2018 byl vydán nový cenový model monitorování. Jeho součástí ceny cloud zařízení, na základě spotřeby. Platíte jenom pro co používáte, aniž by na základě uzlu závazky. Podrobnosti o nový cenový model jsou k dispozici pro [výstrahy, metriky, oznámení](https://azure.microsoft.com/pricing/details/monitor/), [analýzy protokolů](https://azure.microsoft.com/pricing/details/log-analytics/), a [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

Pro zákazníky, kteří začít používat analýzy protokolů nebo Application Insights po 2. dubna 2018 nový cenový model je jedinou možností. Pro zákazníky, kteří už používají tyto služby se přesouvat na nový cenový model volitelné.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Hodnocení dopadu nový cenový model

Nový cenový model může mít různé dopady na každého zákazníka a to na základě jejich monitorování způsobů využití. Pro zákazníky, kteří používali před 2. dubna 2018, analýzy protokolů nebo Application Insights **využití a odhadované náklady** stránky v Azure monitorování Odhadne všechny změny v nákladech, pokud se přesouvají do nový cenový model. Poskytuje způsob, jak přesunout předplatné do nového modelu. Pro většinu zákazníků bude výhodné nový cenový model. Pro zákazníky s vzorce používání zejména vysoké dat nebo v oblastech vyšší náklady nemusí to být případ.

Zobrazíte odhad náklady pro odběry, které jste zvolili na **využití a odhadované náklady** vyberte blue hlavičky v horní části stránky. Je vhodné provést tento jeden odběr současně, protože se jedná o úrovni, na které můžete používat nový cenový model.

![Sledování využití a odhadované náklady na nový snímek obrazovky cenovou modelu](./media/monitoring-usage-and-estimated-costs/004.png)

Nová stránka zobrazuje podobné verzi na předchozí stránku s zelená banner:

![Sledování využití a odhadované náklady na aktuální cenová snímku obrazovky modelu](./media/monitoring-usage-and-estimated-costs/005.png)

Stránce také ukazuje jinou sadu měřidla, které odpovídají nový cenový model. Tento seznam je příklad:

- Přehledy a Analytics\Overage na uzel
- Přehledy a Analytics\Included na uzel
- Nadlimitní Data Insights\Basic aplikací
- Insights\Included Data aplikací

Nový cenový model nemá přidělení zahrnuté dat na základě uzlu. Proto tato měřidla přijímání dat spojují se do nové běžné data přijímání měřidla názvem **sdílené přijímání Services\Data**. 

Existuje jiné změny dat nasávaného analýzy protokolů nebo Application Insights v oblastech s vyšší náklady. Data pro tyto oblasti vysokou náklady budou zobrazeny s novou místní měřidla. Příkladem je **přijímání dat (USA – západ centrální)**.

> [!NOTE]
> Na jedno předplatné odhadované náklady není zohlednit na úrovni účtu na uzlu oprávnění předplatného Operations Management Suite (OMS). V takovém případě najdete vaším zástupcem účet podrobné informace o nový cenový model.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Nové ceny modelu a oprávnění předplatné služby Operations Management Suite

Zákazníci, kteří si zakoupili služby Microsoft Operations Management Suite E1 a E2 jsou způsobilé pro každý uzel data přijímání oprávnění pro [analýzy protokolů](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite) a [Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-pricing#the-price-plans). Pro příjem těchto oprávnění pro pracovní prostory analýzy protokolů nebo Application Insights prostředky v daném předplatném, že je předplatné cenový model musí zůstat v předběžné – duben 2018 cenový model. To je, kde jsou k dispozici "Podle uzlu (OMS)" cenová úroveň a Application Insights "Enterprise" cenových plánů analýzy protokolů. V závislosti na počtu uzlů sady, které vaše organizace koupila přesunutí, že některá předplatná na nový cenový model stále může být výhodné. Ale vyžaduje pečlivě zvážit.

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Změny při přesunování na nový cenový model

Přesun předplatné do nový cenový model bude změnit cenovou úroveň pro každý analýzy protokolů na nové vrstvě za GB a přesune všechny (nazývané "pergb2018" ve službě Správce prostředků Azure). Tento přesun se také změní všechny prostředky Application Insights v plánu podnikového na základní plán. Odhad nákladů ukazuje důsledky tyto změny.

## <a name="moving-to-the-new-pricing-model"></a>Přesunutí na nový cenový model

Pokud jste se rozhodli použít nový cenový model pro předplatné, vyberte **ceny Výběr modelu** možnost v horní části **využití a odhadované náklady** stránky:

![Sledování využití a odhadované náklady na nový snímek obrazovky cenovou modelu](./media/monitoring-usage-and-estimated-costs/006.png)

**Ceny Výběr modelu** otevře se stránka. Zobrazuje seznam všech předplatných, které jste si zobrazili na předchozí stránce:

![Snímek obrazovky výběru modelu – ceny](./media/monitoring-usage-and-estimated-costs/007.png)

Chcete-li přesunout předplatné nový cenový model, stačí vybrat pole a pak vyberte **Uložit**. Je můžete přesunout zpět do starší cenový model stejným způsobem. Mějte na paměti tento vlastník předplatného nebo chcete-li změnit model tvorby cen jsou potřeba oprávnění přispěvatele.
