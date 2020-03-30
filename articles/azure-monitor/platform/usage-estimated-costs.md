---
title: Monitorování využití a odhadovaných nákladů ve službě Azure Monitor
description: Přehled procesu používání azure monitoru a stránky odhadované náklady
author: dalekoetke
services: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 22c7243cd966f458610c2abc67ed5540b37357b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658811"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Monitorování využití a odhadovaných nákladů ve službě Azure Monitor

> [!NOTE]
> Tento článek popisuje, jak zobrazit využití a odhadované náklady napříč několika funkcemi monitorování Azure. Související články pro konkrétní součásti Azure Monitoru zahrnují:
> - [Správa využití a nákladů pomocí protokolů Azure Monitor](manage-cost-storage.md) popisuje, jak řídit náklady změnou doby uchovávání dat a jak analyzovat a upozorňovat na využití dat.
> - [Správa využití a nákladů pro Application Insights](../../azure-monitor/app/pricing.md) popisuje, jak analyzovat využití dat v Application Insights.

## <a name="azure-monitor-pricing-model"></a>Cenový model Azure Monitoru

Základní model fakturace Azure Monitoru je cenový model vhodný pro cloud a spotřeba ("průběžně svámí"). Platíte jenom za to, co používáte. Podrobnosti o cenách jsou k dispozici pro [výstrahy, metriky, oznámení](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) a [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Kromě modelu průběžných plateb pro data protokolu má Služba Log Analytics rezervace kapacity, které umožňují uložit až 25 % ve srovnání s průběžnou cenou průběžných plateb. Cena za rezervaci kapacity vám umožní zakoupit rezervaci od 100 GB/den. Veškeré využití nad úrovní rezervace bude účtováno podle sazby průběžných plateb. [Přečtěte si další informace](https://azure.microsoft.com/pricing/details/monitor/) o cenách rezervací kapacity.

Někteří zákazníci budou mít přístup ke [starším cenovým úrovním Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers) a [ke starší cenové úrovni Přehledy podnikových aplikací](https://docs.microsoft.com/azure/azure-monitor/app/pricing#legacy-enterprise-per-node-pricing-tier). 

## <a name="understanding-your-azure-monitor-costs"></a>Principy nákladů na Azure Monitor

Existují dvě fáze pro pochopení nákladů. První je při zvažování Azure Monitor jako vaše řešení monitorování. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>Odhad nákladů na správu prostředí

Pokud ještě nepoužíváte protokoly monitorování Azure, můžete pomocí [cenové kalkulačky Azure Monitoru](https://azure.microsoft.com/pricing/calculator/?service=monitor) odhadnout náklady na používání Azure Monitoru. Začněte zadáním "Azure Monitor" do vyhledávacího pole a kliknutím na výslednou dlaždici Azure Monitor. Posuňte se dolů po stránce na Azure Monitor a v rozevíracím seznamu Typ vyberte jednu z možností:

- Dotazy a výstrahy metrik  
- Log Analytics
- Application Insights

V každém z nich vám cenová kalkulačka pomůže odhadnout pravděpodobné náklady na základě očekávaného využití.

Například pomocí Analýzy protokolů můžete zadat počet virtuálních připojení a GB dat, která očekáváte, že budete shromažďovat z každého virtuálního účtu. Obvykle 1 GB až 3 GB datového měsíce se ingestuje z typického virtuálního počítače Azure. Pokud už vyhodnocujete protokoly azure monitoru, můžete použít statistiky dat z vlastního prostředí. Níže naleznete postup určení [počtu monitorovaných virtuálních počítačů](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) a [objemu dat, která pracovní prostor ingestuje](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume).

Podobně pro Application Insights, pokud povolíte funkci "Odhad objemu dat na základě aktivity aplikace", můžete poskytnout vstupy o vaší aplikaci (požadavky za měsíc a zobrazení stránek za měsíc, v případě, že budete shromažďovat telemetrii na straně klienta), a pak kalkulačka vám řekne medián a 90 procent množství dat shromážděných podobnými aplikacemi. Tyto aplikace pokrývají rozsah konfigurace Application Insights (např. některé mají výchozí vzorkování, některé nemají vzorkování atd.), takže stále máte ovládací prvek pro snížení objemu dat, která ingestujete hluboko pod střední úroveň pomocí vzorkování. Ale to je výchozí bod pochopit, co ostatní, podobné zákazníci vidí. [Přečtěte si další informace](https://docs.microsoft.com/azure/azure-monitor/app/pricing#estimating-the-costs-to-manage-your-application) o odhadu nákladů pro Application Insights.

### <a name="understanding-your-usage-and-estimated-costs"></a>Pochopení využití a odhadovaných nákladů

Je důležité pochopit a sledovat vaše využití jednou pomocí Azure Monitor a existují bohaté sady nástrojů pro usnadnění tohoto. 

Azure poskytuje velké množství užitečných funkcí v centru [Azure Cost Management + Billing.](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Po otevření centra **Azure Cost Management + Billing** klikněte na Správa **nákladů** a vyberte [obor](https://docs.microsoft.com/azure/cost-management/understand-work-scopes) (sadu prostředků k prozkoumání). 

Pak se zobrazí náklady na Azure Monitor za posledních 30 dní, klikněte na dlaždici **Denní náklady,** zvolte "Posledních 30 dní" v části Relativní data a přidejte filtr, který vybere názvy služeb:

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. Statistiky a analýza

Výsledkem je zobrazení, jako je:

![Snímek obrazovky Azure Cost Management](./media/usage-estimated-costs/010.png)

Zde můžete přejít k podrobnostem z tohoto souhrnu kumulovaných nákladů a získat tak jemnější podrobnosti v zobrazení Náklady podle zdroje. V aktuálních cenových úrovních azure protokolu data se účtuje na stejnou sadu měřičů, ať už pochází z Log Analytics nebo Application Insights. Chcete-li oddělit náklady od využití služby Log Analytics nebo Application Insights, můžete přidat filtr pro **typ prostředku**. Chcete-li zobrazit všechny náklady na přehledy aplikací, filtrujte typ prostředku na "microsoft.insights/components" a pro náklady analýzy protokolů filtrujte typ prostředku na "microsoft.operationalinsights/workspace". 

Další podrobnosti o využití je k dispozici [stažením využití z webu Azure Portal](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). Ve stažené tabulce uvidíte využití na prostředek Azure za den. V této tabulce aplikace Excel lze využití z prostředků Application Insights najít nejprve filtrováním ve sloupci Kategorie měřiče, který zobrazuje "Application Insights" a "Log Analytics", a přidáním filtru do sloupce Instanci, který obsahuje microsoft.insights/komponenty.  Většina application insights využití se hlásí na měřiče s kategorie měřiče Log Analytics, protože existuje jeden back-end protokolů pro všechny součásti Azure Monitor.  Pouze prostředky Application Insights na starších cenových úrovních a vícekrokových webových testech jsou hlášeny s kategorií měřičů application insights.  Využití je zobrazeno ve sloupci "Spotřebované množství" a jednotka pro každou položku je zobrazena ve sloupci Měrná jednotka.  K dispozici jsou další podrobnosti, které vám pomohou [pochopit vaši fakturu za Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

> [!NOTE]
> Použití **správy nákladů** v centru Azure Cost Management + **Billing** je upřednostňovaný přístup k širšímu pochopení nákladů na monitorování.  Prostředí **využití a odhadované náklady** pro [analýzu protokolů](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs) a [přehledy aplikací](https://docs.microsoft.com/azure/azure-monitor/app/pricing#understand-your-usage-and-estimate-costs) poskytují podrobnější přehledy pro každou z těchto částí Azure Monitoru.

Další možností pro zobrazení využití Azure Monitoru je stránka **Využití a odhadované náklady** v centru Monitor. To ukazuje využití základních funkcí monitorování, jako je [upozornění, metriky, oznámení](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)a [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Pro zákazníky, kteří mají cenové plány dostupné před dubnem 2018, to zahrnuje také využití Log Analytics zakoupené prostřednictvím nabídky Přehledy a Analytics.

Na této stránce mohou uživatelé zobrazit využití prostředků za posledních 31 dní, agregované na předplatné. `Drill-ins`zobrazovat trendy využití za 31denní období. Mnoho dat se musí spojit pro tento odhad, takže buďte trpěliví, když se stránka načte.

Tento příklad ukazuje monitorování využití a odhad výsledných nákladů:

![Snímek obrazovky portálu Využití a odhadované náklady](./media/usage-estimated-costs/001.png)

Výběrem odkazu ve sloupci měsíční využití otevřete graf, který zobrazuje trendy využití za poslední 31denní období: 

![Snímek obrazovky grafu zahrnutý na panelu uzlů](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Nároky předplatného sady Operations Management Suite

Zákazníci, kteří si zakoupili sadu Microsoft Operations Management Suite E1 a E2, mají nárok na nároky na přihlašování dat pro [analýzu](https://www.microsoft.com/cloud-platform/operations-management-suite) protokolů a [přehledy aplikací](https://docs.microsoft.com/azure/application-insights/app-insights-pricing). Chcete-li získat tyto nároky pro pracovní prostory Analýzy protokolů nebo prostředky Application Insights v daném předplatném: 

- Pracovní prostory Log Analytics by měly používat cenovou úroveň "Per-node (OMS)".
- Prostředky Application Insights by měly používat cenovou úroveň "Enterprise".

V závislosti na počtu uzlů sady, které vaše organizace zakoupila, může být přesunutí některých předplatných do cenové úrovně průběžných plateb (za GB) výhodné, ale to vyžaduje pečlivé zvážení.

> [!WARNING]
> Pokud má vaše organizace aktuální sadu Microsoft Operations Management Suite E1 a E2, je obvykle nejlepší zachovat pracovní prostory Log Analytics v cenové úrovni "Per-node (OMS)" a vaše prostředky Application Insights v cenové úrovni Enterprise. 
>
