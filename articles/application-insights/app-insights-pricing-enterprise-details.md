---
title: Starší verze Enterprise plán podrobné informace o cenách pro Azure Application Insights | Microsoft Docs
description: Správa telemetrie svazků a sledování nákladů ve službě Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 6e7591ccf0f21099474a08dda088422c377135f6
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="enterprise-plan-details"></a>Podrobnosti plánu Enterprise

Application Insights obsahuje dvě cenovou plány. Výchozí plán nazývá [základní](app-insights-pricing.md), který zahrnuje všechny stejné funkce jako plánu podnikového na bez přidání náklady a faktur hlavně na objemu dat požity. Pokud používáte služby Operations Management Suite, musí vyjádřit výslovný pro organizace plán, který má uzlům účtují společně s denně přídavky data a následně vám bude účtovat data požity výše zahrnuté povoleného užívání.

Najdete v článku [Application Insights stránce s cenami](http://azure.microsoft.com/pricing/details/application-insights/) pro aktuální ceny měny a oblasti.

## <a name="heres-how-the-enterprise-plan-works"></a>Tady je Princip plánu podnikového

* Platíte za uzlu, který odesílá telemetrická data pro všechny aplikace v plánu podnikového.
 * A *uzlu* je počítač fyzický nebo virtuální server nebo instanci role platforma jako služba, který je hostitelem vaší aplikace.
 * Vývoj pro počítače, klientský prohlížeč a mobilní zařízení nejsou počítají jako uzly.
 * Pokud vaše aplikace obsahuje několik komponent, které odesílají telemetrická data, jako jsou webové služby a pracovní back-end, se počítají samostatně.
 * [Živý datový proud metriky](app-insights-live-stream.md) dat není součet ceny purposes.* napříč předplatné, jsou vaše poplatky na uzel a ne aplikace. Pokud máte pět uzlů odesílání telemetrie 12 aplikace a pak zřizování je pro pět uzlů.
* I když poplatky jsou v uvozovkách za měsíc, se vám účtovat pouze pro všechny hodinu, ve kterém uzlu odesílá telemetrická data z aplikace. Hodinové poplatků je uvozovkách měsíčních poplatků / 744 (počet hodin v měsíci 31 dnů).
* Pro každý uzel zjistil (se hodinách) je zadané přidělení objemu dat 200 MB za den. Přidělení nevyužité dat není přenesené z jednoho dne na další.
 * Pokud si zvolíte možnost cenovou Enterprise, každé předplatné získá denní příspěvek dat na základě počtu uzlů odesílání telemetrie do zdroje Application Insights v tomto předplatném. Takže pokud máte 5 uzly odeslání dat celý den, budete mít ve fondu příspěvek ve výši 1 GB použito pro všechny prostředky Application Insights v tomto předplatném. Pokud některé uzly jsou odesílá více dat než jiné uzly, protože zahrnuté data sdílena pro všechny uzly nezáleží. Pokud určitý den zobrazí prostředky Application Insights více dat, než je součástí denní přidělení data pro toto předplatné, použít Nadlimitní dat poplatků za GB. 
 * Denní data příspěvek se počítá jako počtem hodin za den (pomocí UTC), každý uzel odesílá telemetrii dělený 24 časy 200 MB. Takže pokud máte 4 uzly odesílat telemetrii během 15 za 24 hodin dne, budou zahrnuty údaje pro daný den ((4 x 15) / 24) x 200 MB = 500 MB. Za cenu 2.30 USD za GB za nadměrné využívání dat bude zřizování rovný 1,15 USD Pokud uzly odeslat 1 GB dat daný den.
 * Organizace, které denní příspěvek plánu není sdílený s aplikací, pro které jste zvolili možnost Základní a nepoužívané povoleného užívání nepřenáší z každodenní. 

## <a name="here-are-some-examples-of-determining-distinct-node-count"></a>Zde jsou některé příklady určování počet jedinečných uzlů

| Scénář                               | Celkový počet uzlů na každý den |
|:---------------------------------------|:----------------:|
| aplikace: 1 používá 3 instancí služby Azure App Service a 1 virtuálního serveru | 4 |
| 3 aplikací běžících na 2 virtuální počítače a prostředky Application Insights pro tyto aplikace jsou ve stejném předplatném a v plánu podnikového | 2 | 
| 4 aplikace, jejíž statistiky aplikace prostředky jsou ve stejném předplatném. Každá aplikace spouští 2 instance 16 špičku a 4 instancí dobu ve špičce 8. | 13.33 | 
| Cloudové služby s 1 Role pracovního procesu a 1 webové Role, všechny spuštěné instance 2 | 4 | 
| Cluster Service Fabric 5 uzlu službou 50 micro-services, každý micro-spuštěné instance 3 | 5|

* Přesné uzlu počítání chování závisí, na kterém je pomocí Application Insights SDK aplikace. 
  * Ve verzích sady SDK, 2.2 a a vyšší, Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) nebo [sady SDK webové](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) sestava bude každý hostitel aplikace jako uzel, například název počítače pro fyzického serveru a hostitelé virtuálních počítačů nebo Název instance v případě cloudové služby.  Jedinou výjimkou je aplikace jen pomocí [.NET Core](https://dotnet.github.io/) a Application Insights Core SDK, ve kterém případu jenom jeden uzel se ohlásí pro všechny hostitele, protože název hostitele není k dispozici. 
  * Pro starší verze sady SDK [sady SDK webové](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) budou chovat stejně jako novější verze sady SDK, ale [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) oznámí jenom jeden uzel bez ohledu na počet hostitelů skutečné aplikací. 
  * Pokud vaše aplikace používá sady SDK k nastavení roleInstance na vlastní hodnotu, ve výchozím nastavení stejné hodnoty se použije k určení počtu uzlů. 
  * Pokud používáte novou verzi sady SDK k aplikaci, která se spouští z klientských počítačů nebo mobilních zařízení, je možné, že počet uzlů může vrátit číslo, které je moc velké (z velký počet klientských počítačů nebo mobilních zařízení). 
