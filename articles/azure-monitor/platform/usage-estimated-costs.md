---
title: Monitorování využití a odhadované náklady ve službě Azure Monitor
description: Přehled procesu použití Azure monitorování využití a odhadované náklady na stránce
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 7117e7287f601b306893cb02dc5d7599d7c6224d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60453757"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Monitorování využití a odhadované náklady ve službě Azure Monitor

> [!NOTE]
> Tento článek popisuje, jak zobrazit využití a odhadované náklady napříč více monitorovacích funkcí Azure pro různé cenové modely.  Přečtěte si následující články související informace.
> - [Spravovat náklady pomocí řízení objemu dat a uchovávání dat v Log Analytics](manage-cost-storage.md) popisuje, jak řídit své náklady tak, že změníte vaše data uchovávají.
> - [Analýza využití dat v Log Analytics](../../azure-monitor/platform/data-usage.md) popisuje, jak analyzovat a upozornění na data využití.
> - [Správa cen a objemů dat ve službě Application Insights](../../azure-monitor/app/pricing.md) popisuje, jak analyzovat využití dat v Application Insights.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

V centru monitorování na portálu Azure Portal **využití a odhadované náklady** stránka vysvětluje použití základní monitorovací funkce, jako [upozornění, metriky, oznámení](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics ](https://azure.microsoft.com/pricing/details/log-analytics/), a [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Pro zákazníky na cenové plány, které jsou k dispozici před dubnem 2018 to také zahrnuje využití Log Analytics koupíte přehledy a analýzy nabízejí.

Na této stránce Uživatelé mohou zobrazit jejich využití prostředků za posledních 31 dnů, agregují na jedno předplatné. Procházení se změnami zobrazit trendy využití po dobu 31 dní. Velké množství dat, musí pocházet dohromady pro tento odhad, proto prosím buďte trpěliví jako načtení stránky.

Tento příklad ukazuje monitorování využití a odhad výsledné náklady:

![Využití a odhadované náklady na portálu obrazovka](./media/usage-estimated-costs/001.png)

Vyberte odkaz ve sloupci měsíční využití otevřete graf, který zobrazuje trendy využití za posledních 31 dnů:

![Zahrnuto na uzel panelu snímek obrazovky grafu](./media/usage-estimated-costs/002.png)

Tady je další podobné využití a náklady na souhrn. Tento příklad ukazuje předplatného v novém. dubna 2018 založenou na skutečné spotřebě cenovém modelu. Všimněte si, absenci jakékoli účtování podle uzlů. Příjem dat a jejich uchovávání pro Log Analytics a Application Insights se teď reportovány nový měřič běžné.

![Využití a odhadované náklady na portálu obrazovka – ceny. dubna 2018](./media/usage-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Nový cenový model

V dubnu 2018 [monitorování nový cenový model byl uvolněn](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/).  Tato funkce přizpůsobený cloudu, založenou na skutečné spotřebě ceny. Platíte jenom za využité, bez závazků založené na uzlu. Podrobnosti o novém cenovém modelu jsou k dispozici pro [upozornění, metriky, oznámení](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) a [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Nový cenový model pro zákazníky připojení ke službě Log Analytics nebo Application Insights po 2. dubnem 2018, je jedinou možností. Pro zákazníky, kteří již používají tyto služby přechod na nový cenový model je volitelný.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Hodnocení dopadu na nový cenový model
Nový cenový model může mít jiný dopad na jednotlivých zákazníků na základě jejich monitorování způsobů využití. Pro zákazníky, kteří používali před 2. dubnem 2018, Log Analytics nebo Application Insights **využití a odhadované náklady** stránky ve službě Azure Monitor odhady žádné změny v náklady na jejich přesunutí na nový cenový model. Poskytuje způsob, jak přejít u určitého předplatného na nový model. Pro většinu zákazníků bude nejvýhodnější nový cenový model. Pro zákazníky s vzory využití zejména vysoké dat nebo v oblastech vyšší náklady nemusí to být případ.

Chcete-li zobrazit odhad nákladů pro předplatné, které jste vybrali na **využití a odhadované náklady** vyberte modrý banner v horní části stránky. Je vhodné provést tento jeden odběr v době, protože to je úroveň, jakou můžou být přijata nový cenový model.

![Monitorování využití a odhadované náklady na nový cenový model snímku obrazovky](./media/usage-estimated-costs/004.png)

Nová stránka zobrazuje podobné verzi předchozí stránka s zelené hlavičky:

![Monitorování využití a odhadované náklady pro aktuální cenový model – snímek obrazovky](./media/usage-estimated-costs/005.png)

Na stránce také ukazuje různé sady měřiče, které odpovídají na nový cenový model. Tento seznam je příklad:

- Insight and Analytics\Overage za uzel
- Insight and Analytics\Included za uzel
- Nadlimitní Data úrovně Insights\Basic aplikace
- Insights\Included Data aplikací

Nový cenový model nemá přidělení zahrnutých dat založené na uzlu. Proto těchto měřičů ingestování dat jsou sloučeny do nový common data ingestování měřič volá **sdílené Ingestování Services\Data**. 

Existuje jiná změna dat přijatých do Log Analytics nebo v oblastech s vyšší náklady na Application Insights. Data pro tyto oblasti vysoké náklady se zobrazí se nové dílčím měřením. Příkladem je **Ingestování (USA – Středozápad)**.

> [!NOTE]
> Na jedno předplatné odhadované náklady není faktor na úrovni účtu, za uzel oprávnění v Operations Management Suite (OMS) předplatného. Podrobnější diskuzi o novém cenovém modelu najdete v tomto případě vašeho obchodního zástupce.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Nový cenový model modelu a nároky na Operations Management Suite předplatné

Zákazníci, kteří si zakoupili Microsoft Operations Management Suite E1 a E2 nárok nároků příjem dat podle uzlu [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) a [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing). Chcete-li tyto nároky pracovních prostorů Log Analytics a prostředky Application Insights se zobrazí v daném předplatném: 

- Cenový model předplatného musí zůstat v modelu pre dubna 2018.
- Pracovní prostory log Analytics používali "Za uzel (OMS)," cenovou úroveň.
- Prostředky Application Insights používali "Enterprise" cenový plán.

V závislosti na počtu uzlů, které vaše organizace koupila, sady přesunutí některých předplatných na nový cenový model může být výhodné, ale to vyžaduje, aby pečlivě zvážit. Obecně je vhodné jednoduše zůstat v model před dubnem 2018, jak je popsáno výše.

> [!WARNING]
> Pokud vaše organizace zakoupila Microsoft Operations Management Suite E1 a E2, je obvykle nejlepší mít předplatného na cenový model pre dubna 2018. 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Změny při přechodu na nový cenový model

Nový cenový model zjednodušuje Log Analytics a Application Insights cenové možnosti pro pouze jednu úroveň (nebo plán). Přesun předplatného na nový cenový model se:

- Změnit cenovou úroveň pro každý Log Analytics na novou úroveň Per GB (označované jako "pergb2018" v Azure Resource Manageru)
- Žádné prostředky Application Insights v plánu Enterprise se změní na základní plán.

Odhad nákladů ukazuje účinky tyto změny.

> [!WARNING]
> Tady důležitá Poznámka: Pokud používáte Azure Resource Manageru a Powershellu k nasazení [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) nebo [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) v rámci předplatného jste přesunuli na nový cenový model. Pokud zadáte cenovou úroveň/plán než "pergb2018" pro Log Analytics nebo "Základní" pro službu Application Insights, spíše než selhání nasazení kvůli určení neplatný cenové úrovně nebo plán, proběhne úspěšně **ale budou používat pouze platné cenové úrovně nebo plán** (neplatí pro Log Analytics zdarma úroveň, kde je generována neplatná zpráva cenová úroveň).
>

## <a name="moving-to-the-new-pricing-model"></a>Přechod na nový cenový model

Pokud jste se rozhodli přijme nový cenový model pro dané předplatné, přejděte na každý prostředek Application Insights, otevřete **využití a odhadované náklady** a ujistěte se, že je v cenové úrovni Basic a přejděte na každý Log Analytics pracovní prostor, otevřete **cenová úroveň** stránky a přejděte **Per GB (2018)** cenovou úroveň. 

> [!NOTE]
> Požadavek, aby všechny prostředky Application Insights a pracovní prostory Log Analytics v rámci daného předplatného přijmout nejnovější cenový model se odebralo, umožní větší funkce a jednodušší konfigurace. 

## <a name="automate-moving-to-the-new-pricing-model"></a>Automatizovat přechod na nový cenový model

Jak bylo uvedeno výše, už není potřeba přesunout všechny prostředky pro sledování v rámci předplatného na nový cenový model ve stejnou dobu a proto ``migratetonewpricingmodel`` akce nebude mít žádný vliv. Nyní můžete přesunout prostředky Application Insights a pracovní prostory Log Analytics samostatně do nejnovější cenové úrovně.  

Automatizace této změny je popsána pro Application Insights s použitím [Set-AzureRmApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/azurerm.applicationinsights/set-azurermapplicationinsightspricingplan) s ``-PricingPlan "Basic"`` a používání Log Analytics [Set-AzureRmOperationalInsightsWorkspace](https://docs.microsoft.com/powershell/module/AzureRM.OperationalInsights/Set-AzureRmOperationalInsightsWorkspace) s ``-sku "PerGB2018"``. 

