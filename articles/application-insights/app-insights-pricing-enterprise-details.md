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
ms.openlocfilehash: 6df013506e4541fee7850850776d26e5c69a799d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="enterprise-plan-details"></a>Podrobnosti plánu Enterprise

Azure Application Insights obsahuje dvě cenovou plány: Basic a Enterprise. [Základní](app-insights-pricing.md) ceny plán je výchozí plán. Obsahuje všechny funkce Enterprise plánu, bez dalších poplatků. Základní plán faktur hlavně na objemu dat, který je konzumována. 

Plánu podnikového má poplatků za uzlu a každý uzel dostane denní příspěvek data. V podnikové síti ceny plán, budeme vám účtovat data požity výše zahrnuté povoleného užívání. Pokud používáte služby Operations Management Suite, měli byste vybrat plán Enterprise. 

Aktuální ceny měny a oblasti, najdete v části [Application Insights ceny](http://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-the-enterprise-plan-works"></a>Jak funguje plánu podnikového

* Platí pro každý uzel, který odesílá telemetrická data pro všechny aplikace v plánu podnikového.
 * A *uzlu* je počítač fyzický nebo virtuální server nebo instanci platforma jako služba role, který je hostitelem vaší aplikace.
 * Vývoj pro počítače, klientský prohlížeč a mobilní zařízení do počtu jako uzly.
 * Pokud vaše aplikace obsahuje několik komponent, které odesílají telemetrická data, jako jsou webové služby a pracovní back-end, komponenty, se počítají samostatně.
 * [Živý datový proud metriky](app-insights-live-stream.md) dat není počítá o cenách pro účely. V odběru jsou vaše poplatky na uzel a ne aplikace. Pokud máte pět uzlů, které odesílat telemetrická data pro 12 aplikací, zřizování je pro pět uzlů.
* I když poplatky jsou v uvozovkách za měsíc, se vám účtovat pouze pro všechny hodinu, ve kterém uzlu odesílá telemetrická data z aplikace. Hodinové poplatků je uvozovkách měsíčních poplatků dělený 744 (počet hodin v měsíci 31 dnů).
* Pro každý uzel, který je zjišťován (s hodinách) je zadané přidělení objemu dat 200 MB za den. Přidělení nevyužité dat není přenesené z jednoho dne na další.
 * Pokud si zvolíte podniku ceny plán, každé předplatné získá denní příspěvek dat na základě počtu uzlů, které odesílají telemetrická data do zdroje Application Insights v tomto předplatném. Ano Pokud máte pět uzlů, které odesílají data celý den, budete mít ve fondu příspěvek ve výši 1 GB použito pro všechny prostředky Application Insights v tomto předplatném. Pokud některé uzly odeslat více dat než jiné uzly, protože zahrnuté data sdílena pro všechny uzly nezáleží. Pokud určitý den zobrazí prostředky Application Insights více dat, než je součástí denní přidělení data pro toto předplatné, použít Nadlimitní dat poplatků za GB. 
 * Denní data příspěvek se počítá jako počtem hodin za den (pomocí UTC), aby každý uzel odesílá telemetrii dělený 24 násobí hodnotou 200 MB. Ano, pokud máte čtyři uzly, které odesílat telemetrická data během 15 za 24 hodin dne, zahrnuté data pro daný den by ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. Za cenu 2.30 USD za GB za nadměrné využívání dat bude zřizování rovný 1,15 USD Pokud uzly odeslat 1 GB dat daný den.
 * Denní příspěvek plán Enterprise není sdílený s aplikací, pro které jste zvolili základní plán. Nepoužívané povoleného užívání nepřenáší z každodenní. 

## <a name="examples-of-how-to-determine-distinct-node-count"></a>Příklady, jak určit počet jedinečných uzlů

| Scénář                               | Celkový počet uzlů na každý den |
|:---------------------------------------|:----------------:|
| aplikace: 1 pomocí 3 instancí služby Azure App Service a 1 virtuálního serveru | 4 |
| 3 aplikací běžících na virtuálních počítačích 2; prostředky Application Insights pro tyto aplikace jsou ve stejném předplatném a v plánu podnikového | 2 | 
| 4 aplikací, jejíž statistiky aplikace prostředky jsou ve stejném předplatném; každou aplikaci spuštěnou 2 instance 16 špičku a 4 instance 8 dobu ve špičce | 13.33 | 
| Cloudové služby s 1 Role pracovního procesu a 1 webové Role, všechny spuštěné instance 2 | 4 | 
| Cluster Azure Service Fabric 5 uzlu se systémem 50 mikroslužeb; Každý mikroslužbu spuštěné instance 3 | 5|

* Počítání přesné uzlu, závisí na které Application Insights SDK vaše aplikace používá. 
  * V sadě SDK verze 2.2 nebo novější, Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) a [sady SDK webové](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) sestavy každý hostitel aplikace jako uzel. Příklady jsou název počítače pro fyzického serveru a hostitelé virtuálních počítačů nebo název instance pro cloudové služby.  Jedinou výjimkou je aplikace, která používá jenom [.NET Core](https://dotnet.github.io/) a základní Application Insights SDK. V takovém případě je uvést pouze jeden uzel pro všechny hostitele, protože název hostitele není k dispozici. 
  * Pro starší verze sady SDK [sady SDK webové](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) chová jako novější verze sady SDK, ale [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) sestavy jenom jeden uzel, bez ohledu na počet hostitelů aplikací. 
  * Pokud vaše aplikace používá sadu SDK nastavit **roleInstance** na vlastní hodnotu, ve výchozím nastavení, stejné hodnoty slouží k určení počet uzlů. 
  * Pokud používáte novou verzi sady SDK k aplikaci, která se spouští z klientských počítačů nebo mobilních zařízení, počet uzlů může vrátit číslo, které je moc velké (kvůli velkému počtu klientských počítačů nebo mobilních zařízení). 
