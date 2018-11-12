---
title: Starší verze Enterprise plán podrobnosti o cenách pro službu Azure Application Insights | Dokumentace Microsoftu
description: Správa svazků telemetrii a sledování nákladů ve službě Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: mbullwin
ms.openlocfilehash: b2a93c7d3b512a34ab5d2e4fd020415739466c2e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235288"
---
# <a name="enterprise-plan-details"></a>Podrobnosti plánu Enterprise

Azure Application Insights má dvě cenové plány: Basic a Enterprise. [Základní](app-insights-pricing.md) cenový plán je výchozí plán. Zahrnuje všechny funkce plánu Enterprise, bez dalších poplatků. Základní plán účtuje především podle objemu dat, který se ingestuje. 

Plán Enterprise je poplatek za uzel a každý uzel přijme denní příděl dat. V podnikové síti cenový plán, bude vám účtována dat přijatých nad zahrnutý příspěvek. Pokud používáte Operations Management Suite, měli byste zvolit plán Enterprise. 

Aktuální ceny měny a oblasti, naleznete v tématu [ceny za Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> V dubnu 2018 jsme [zavedené](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) nový cenový model pro monitorování Azure. Tento model přijímá mezi kompletní portfolio služeb monitorování jednoduchý model "s průběžnými platbami". Další informace o [nový cenový model](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), jak k [posoudit dopad přechodu na tento model](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) založené na vaše vzorce používání a [tom, jak začít používat nový model](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)

## <a name="how-the-enterprise-plan-works"></a>Jak funguje plánu Enterprise

* Platíte za každý uzel, který odesílá telemetrická data pro všechny aplikace v plánu Enterprise.
 * A *uzlu* je počítač fyzický nebo virtuální server nebo instance platformy jako služby role, který je hostitelem vaší aplikace.
 * Vývoj pro počítače, klientský prohlížeč a mobilní zařízení se nepočítají jako uzly.
 * Pokud má vaše aplikace několik komponent, které odesílají telemetrická data, jako jsou webové služby a back-end pracovní proces, komponenty se počítá samostatně.
 * [Live Metrics Stream](app-insights-live-stream.md) dat se nepočítá ceny účely. V předplatném poplatky se podle počtu uzlů, ne podle aplikací. Pokud máte pět uzlů, které odesílají telemetrii pro 12 aplikací, platí se za pět uzlů.
* I když poplatky se zaokrouhlují za měsíc, platíte jenom pro všechny hodiny, kdy uzel odesílá telemetrická data z aplikace. Hodinová sazba se v uvozovkách měsíční poplatek vydělí 744 (počet hodin za měsíc s 31 dny).
* Pro každý uzel, který je zjištěn (s hodinovou členitost) je zadána přidělení dat svazku alespoň 200 MB za den. Přidělení nepoužívaná data se přenesou z jeden den na další.
 * Pokud se rozhodnete podniku cenový plán, každé předplatné získá denní příděl dat na základě počtu uzlů, které posílají telemetrická data do prostředky Application Insights v tomto předplatném. Ano Pokud máte pět uzlů, které posílají data celý den, budete mít ve fondu příděl 1 GB použito pro všechny prostředky Application Insights v tomto předplatném. Nebude vadit, když některé uzly posílají více dat než jiné uzly, protože poskytovaná data se sdílejí napříč všemi uzly. Pokud v daném dni prostředky Application Insights zobrazí více dat, než je součástí denní přidělování dat pro toto předplatné, vztahují poplatky za Nadlimitní data úrovně za GB. 
 * Denní příděl dat se počítá jako počtu hodin za den (pomocí času UTC), že každý uzel odesílá telemetrii, dělený 24, vynásobený 200 MB. Ano, pokud máte čtyři uzly, které odesílají telemetrii během 15 z 24 hodin za den, zahrnutých dat pro tento den by ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. Za cenu 2.30 USD za GB za překročení limitu dat náklady by rovný 1,15 USD uzly odeslání 1 GB dat daný den.
 * Denní příděl plánu Enterprise se nesdílí s aplikacemi, pro které jste zvolili základní plán. Nepoužité příspěvek není přenesou v detailech. 

## <a name="examples-of-how-to-determine-distinct-node-count"></a>Příklady toho, jak určit počet různých uzlů

| Scénář                               | Celková denní počet uzlů |
|:---------------------------------------|:----------------:|
| aplikace: 1 pomocí 3 instancí služby Azure App Service a 1 virtuální server | 4 |
| běžící na virtuálních počítačích 2; 3 aplikace prostředky Application Insights pro tyto aplikace jsou ve stejném předplatném a v plánu Enterprise | 2 | 
| 4 aplikace, jehož prostředky aplikace Insights je ve stejném předplatném; každou aplikaci spuštěnou 2 instance 16 špičku a 4 instance během 8 hodin ve špičce | 13.33 | 
| Cloudové služby s 1 Role pracovního procesu a 1 webová Role, každé spuštění 2 instancí | 4 | 
| Clusteru Azure Service Fabric 5 uzly se systémem 50 mikroslužeb; Každá mikroslužba spuštěné instance 3 | 5|

* Uzly přesné, počítací závisí, na které Application Insights SDK aplikace používá. 
  * V SDK verze 2.2 a novější, Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) a [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) sestavy každého hostitele aplikace jako uzel. Příklady jsou název počítače pro fyzický server a hostitelů virtuálních počítačů nebo název instance pro cloud services.  Jedinou výjimkou je aplikace, která se použije pouze [.NET Core](https://dotnet.github.io/) a sadu SDK Application Insights Core. V takovém případě je pro všechny hostitele hlášených pouze jeden uzel, protože název hostitele není k dispozici. 
  * Pro starší verze sady SDK [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) se chová jako novější verze sady SDK, ale [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) hlásí pouze jeden uzel bez ohledu na počet hostitelů aplikací. 
  * Pokud vaše aplikace používá k nastavení sady SDK **instance role** vlastní hodnoty, ve výchozím nastavení, stejnou hodnotu se používá k určení počtu uzlů. 
  * Pokud používáte novou verzi sady SDK aplikace, která se spouští z klientských počítačů nebo mobilních zařízení, počet uzlů může vrátit číslo, které je moc velká (z důvodu velký počet klientských počítačů nebo mobilních zařízení). 
