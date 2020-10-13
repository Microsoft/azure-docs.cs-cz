---
title: Monitorování využití a odhadovaných nákladů v Azure Monitor
description: Přehled procesu použití stránky s předpokládanými náklady na Azure Monitor a využití
author: dalekoetke
services: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: eb96537f67c61fb31759da020068f784d0e89993
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87323396"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Monitorování využití a odhadovaných nákladů v Azure Monitor

> [!NOTE]
> Tento článek popisuje, jak zobrazit využití a odhadované náklady napříč více funkcemi monitorování Azure. Mezi související články pro konkrétní součásti Azure Monitor patří:
> - [Správa využití a nákladů pomocí Azure Monitorch protokolů](manage-cost-storage.md) popisuje, jak řídit náklady změnou doby uchovávání dat a jak analyzovat a upozorňovat na využití vašich dat.
> - [Správa využití a nákladů pro Application Insights](../app/pricing.md) popisuje, jak analyzovat využití dat v Application Insights.

## <a name="azure-monitor-pricing-model"></a>Azure Monitor cenový model

Model fakturace Basic Azure Monitor je uživatelsky přívětivé ceny založené na spotřebě ("průběžné platby"). Platíte jenom za to, co používáte. Podrobnosti o cenách jsou k dispozici pro [výstrahy, metriky, oznámení](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) a [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Kromě modelu průběžných plateb pro data protokolu Log Analytics má rezervace kapacity, což vám umožní v porovnání s průběžnými platbami ušetřit až 25%. Cena za rezervaci kapacity vám umožní koupit rezervaci od 100 GB za den. Veškeré využití nad úrovní rezervace se bude účtovat podle tarifu průběžných plateb. [Přečtěte si další informace](https://azure.microsoft.com/pricing/details/monitor/) o cenách rezervací kapacity.

Někteří zákazníci budou mít přístup ke [starším úrovním Log Analytics cenové úrovně](./manage-cost-storage.md#legacy-pricing-tiers) a [starší verze Enterprise Application Insights cenové úrovně](../app/pricing.md#legacy-enterprise-per-node-pricing-tier). 

## <a name="understanding-your-azure-monitor-costs"></a>Porozumění nákladům na Azure Monitor

K dispozici jsou dvě fáze pro porozumění nákladům. První je při zvažování Azure Monitor jako řešení monitorování. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>Odhad nákladů na správu prostředí

Pokud protokoly Azure Monitor ještě nepoužíváte, můžete pomocí [cenové kalkulačky Azure monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) odhadnout náklady na používání Azure monitor. Začněte zadáním "Azure Monitor" do vyhledávacího pole a kliknutím na výslednou Azure Monitor dlaždici. Posuňte se dolů na stránku Azure Monitor a v rozevíracím seznamu Typ vyberte jednu z možností:

- Dotazy a výstrahy metriky  
- Log Analytics
- Application Insights

V každém z těchto řešení vám cenová Kalkulačka pomůže odhadnout náklady na základě očekávaného využití.

Například pomocí Log Analytics můžete zadat počet virtuálních počítačů a GB dat, která chcete shromažďovat z každého virtuálního počítače. Od typického virtuálního počítače Azure se typicky odchází 1 GB až 3 GB datového měsíce. Pokud už vyhodnocujete Azure Monitor protokoly, můžete použít statistiku dat z vlastního prostředí. Níže najdete informace o tom, jak určit [Počet monitorovaných virtuálních počítačů](./manage-cost-storage.md#understanding-nodes-sending-data) a objem dat, na které [váš pracovní prostor pracuje](./manage-cost-storage.md#understanding-ingested-data-volume).

Podobně jako u Application Insights, pokud povolíte funkci "objem dat odhadu na základě aktivity aplikace", můžete zadat vstupy o vaší aplikaci (požadavky za měsíc a zobrazení stránky měsíčně), pro případ, že budete shromažďovat telemetrii na straně klienta), a pak Kalkulačka oznámí medián a 90. percentil dat shromažďovaných podobnými aplikacemi. Tyto aplikace rozcházejí z rozsahu konfigurace Application Insights (například některé mají výchozí vzorkování, některé nemají žádné vzorkování atd.), takže stále máte kontrolu nad tím, jak omezit objem dat, která jsou až na střední úrovni, a to pomocí vzorkování. Ale jedná se o výchozí bod, který vám pomůže pochopit, co podobné zákazníky vidí. [Přečtěte si další informace](../app/pricing.md#estimating-the-costs-to-manage-your-application) o odhadu nákladů na Application Insights.

### <a name="understanding-your-usage-and-estimated-costs"></a>Princip využití a odhadovaných nákladů

Je důležité pochopit a sledovat vaše využití jednou pomocí Azure Monitor a máme bohatou sadu nástrojů, které vám to usnadní. 

Azure poskytuje skvělou užitečnou funkci centra [Azure cost management + fakturace](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json) . Po otevření centra **Azure cost management + fakturace** klikněte na **cost management** a vyberte [obor](../../cost-management-billing/costs/understand-work-scopes.md) (sadu prostředků, které se mají prozkoumat). 

Pokud chcete zobrazit Azure Monitor náklady za posledních 30 dní, klikněte na dlaždici **denní náklady** , v části relativní datum vyberte posledních 30 dní a přidejte filtr, který vybere názvy služeb:

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. Statistiky a analýza

Výsledkem je zobrazení, jako například:

![Snímek obrazovky Azure Cost Management](./media/usage-estimated-costs/010.png)

Odtud můžete přejít z tohoto souhrnného souhrnu nákladů a získat tak přesnější podrobnosti v zobrazení náklady podle prostředku. V aktuální cenové úrovni se data protokolu Azure účtují na stejné sadě měřičů, ať už pochází z Log Analytics nebo Application Insights. Pokud chcete oddělit náklady od Log Analytics nebo Application Insights využití, můžete přidat filtr na **typ prostředku**. Pokud chcete zobrazit všechny náklady na Application Insights, vyfiltrujte typ prostředku na "Microsoft. Insights/Components", a pokud chcete Log Analytics náklady, filtrujte typ prostředku na Microsoft. operationalinsights/Workspaces. 

Podrobnější informace o využití najdete na stránce [stažení vašeho využití z Azure Portal](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal). Ve stažených tabulkách můžete zobrazit využití podle prostředku Azure za den. V této excelové tabulce můžete využití vašich Application Insightsch prostředků najít při prvním filtrování ve sloupci měřiče měření, pokud chcete zobrazit Application Insights a Log Analytics, a pak přidat filtr na sloupec ID instance, který je obsahuje Microsoft. Insights/Components.  Většina využití Application Insights se oznamuje u měřičů s kategorií měřičů Log Analytics, protože pro všechny Azure Monitor komponenty existuje back-end s jedním protokolem.  Pouze Application Insights prostředky se staršími cenovými úrovněmi a webovými testy ve více krocích jsou hlášeny s kategorií měřičů Application Insights.  Využití se zobrazí ve sloupci "spotřebované množství" a jednotka pro každou položku je zobrazena ve sloupci Měrná jednotka.  K dispozici jsou také další podrobnosti, které vám pomůžou [porozumět informacím na faktuře za Microsoft Azure](../../cost-management-billing/understand/review-individual-bill.md). 

> [!NOTE]
> Použití **cost management** v centrálním centru **Azure cost management +** je upřednostňovaným přístupem k rozsáhlému porozumění nákladům na monitorování.  Prostředí pro **využití a odhadované náklady** [Log Analytics](./manage-cost-storage.md#understand-your-usage-and-estimate-costs)  a [Application Insights](../app/pricing.md#understand-your-usage-and-estimate-costs) poskytují hlubší přehledy pro každou z těchto částí Azure monitor.

Další možností zobrazení Azure Monitor využití jsou stránky **využití a odhadované náklady** v centru monitorování. Zobrazuje se tím použití základních monitorovacích funkcí, jako jsou například [výstrahy, metriky, oznámení](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)a [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Pro zákazníky, kteří mají cenové tarify k dispozici před dubna 2018, to zahrnuje i Log Analytics využití zakoupené prostřednictvím nabídky Insights a Analytics.

Na této stránce můžou uživatelé zobrazit své využití prostředků za posledních 31 dnů, agregované podle předplatného. `Drill-ins` Zobrazit trendy využití za 31 dní. Pro tento odhad je potřeba udělat spoustu dat, a proto se prosím ujistěte, že se stránka načítá.

Tento příklad ukazuje využití monitorování a odhad výsledných nákladů:

![Snímek obrazovky portálu využití a odhadované náklady](./media/usage-estimated-costs/001.png)

Vyberte odkaz ve sloupci měsíční využití a otevřete graf zobrazující trendy využití během posledních 31 dnů: 

![Snímek obrazovky pro pruhový graf na panelu uzlů](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Nároky na předplatné Operations Management Suite

Zákazníci, kteří si zakoupili Microsoft Operations Management Suite E1 a E2, mají nárok na nároky na přijímání dat pro [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) a [Application Insights](../app/pricing.md)pro jednotlivé uzly. Pro získání těchto nároků pro Log Analytics pracovní prostory nebo prostředky Application Insights v daném předplatném: 

- Log Analytics pracovní prostory by měly používat cenové úrovně "per-Node (OMS)".
- Application Insights prostředky by měly používat cenové úrovně "Enterprise".

V závislosti na počtu uzlů sady, kterou vaše organizace zakoupila, může být výhodné přesunout některá předplatná do cenové úrovně s průběžnými platbami (za GB), ale to vyžaduje pečlivou pozornost.

> [!WARNING]
> Pokud má vaše organizace aktuální Microsoft Operations Management Suite E1 a E2, většinou je nejlepší udržovat pracovní prostory v Log Analytics v cenové úrovni pro jednotlivé uzly (OMS) a vaše Application Insights prostředky v cenové úrovni "Enterprise". 
>

