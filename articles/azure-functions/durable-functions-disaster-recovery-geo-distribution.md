---
title: Zotavení po havárii a geografická rozptýlenost v Durable Functions – Azure
description: Přečtěte si o zotavení po havárii a geografická rozptýlenost v Durable Functions.
services: functions
author: MS-Santi
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: bbfbf351b8976f4140b6dd98a9a54ba982c3d865
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399393"
---
# <a name="disaster-recovery-and-geo-distribution"></a>Zotavení po havárii a geografická distribuce

## <a name="overview"></a>Přehled
Ve službě Azure Functions trvalý všechny stavy se ukládají ve službě Azure Storage. A [centra úloh](durable-functions-task-hubs.md) je logický kontejner prostředků služby Azure Storage, které se používají pro Orchestrace. Funkce nástroje Orchestrator a aktivity pouze komunikovat mezi sebou když patří do stejné centra úloh.
Popsané scénáře navrhnout možnosti nasazení pro zvýšení dostupnosti a minimalizovat prostoje během aktivity pro zotavení po havárii.
Je důležité si všimněte, že tyto scénáře jsou založeny na konfigurace aktivní – pasivní, protože se řídit využití služby Azure Storage. Tento model spočívá v nasazení aplikace funkcí zálohování (pasivní) do jiné oblasti. Traffic Manager monitorovat aplikace primární (aktivní) funkce dostupnosti. To převezme do aplikace funkci zálohování Pokud selže primární. Další informace najdete v tématu [Traffic Manageru](https://azure.microsoft.com/services/traffic-manager/)společnosti [metody směrování provozu Priority.](../traffic-manager/traffic-manager-routing-methods.md#a-name--priorityapriority-traffic-routing-method)


>[!NOTE]
>- Navrhované konfiguraci aktivní-pasivní zajišťuje, že klient vždy moct aktivovat novou Orchestrace přes protokol HTTP. Ale následkem s dvě aplikace function App sdílení stejné úložiště, zpracování na pozadí budou distribuována mezi jejich soutěží o ceny na stejné fronty zpráv. Tuto konfiguraci s sebou nese náklady za Přidání odchozího přenosu dat pro sekundární funkci aplikace.
>- Základní centra účtů a úloh úložiště jsou vytvořené v primární oblasti a sdílí obě aplikace function App.
>- Všechny aplikace function App, které jsou nasazeny redundantně, musejí sdílet stejnou funkci přístupové klíče v případě při aktivaci prostřednictvím protokolu HTTP. Poskytuje modul Runtime služby Functions [rozhraní API pro správu](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) , která umožňuje uživatelům programově přidat, odstranit a aktualizovat klíče funkce.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Scénář 1 – výpočetní prostředky se sdíleným úložištěm s vyrovnáváním zatížení
Pokud se nezdaří výpočetní infrastruktura v Azure, se můžou stát nedostupnými aplikace function app. Aby se minimalizovala možnost které, tento scénář používá dvě funkce aplikace nasazené do různých oblastí. Traffic Manager je nakonfigurována ke zjišťování problémů s primární funkce aplikace automaticky přesměrování provozu do aplikace function app v sekundární oblasti. Tuto aplikaci function app se hovoří o stejný účet Azure Storage a Centrum úkolů. Proto stav aplikace function App se neztratí a práce můžete pokračovat v normálně. Jakmile se stav obnovení do primární oblasti Azure Traffic Manageru se spustí směrování žádostí na tuto aplikaci function app.


![Diagram znázorňující scénář 1.](media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Při použití tento scénář nasazení má několik výhod:
- Pokud výpočetní infrastruktura selže, práce může pokračovat v převzetí přes oblast bez ztráty stavu.
- Traffic Manager automaticky postará o automatické převzetí služeb při selhání na aplikaci funkcí v pořádku.
- Traffic Manager automaticky znovu naváže provozu do aplikace primární funkce po výpadek byl opraven.

Ale zvažte použití tohoto scénáře:
- Pokud aplikace function app se nasadí pomocí vyhrazený plán služby App Service, replikaci výpočetní infrastruktura v převzetí nad datovým centrem zvýší náklady.
- Tento scénář popisuje výpadky na výpočetní infrastrukturu, ale účet úložiště i nadále být jediným bodem selhání pro funkce aplikace. Pokud dojde k výpadku úložiště, aplikace utrpí výpadek.
- Pokud aplikace function app se převzaly při selhání, bude vyšší latence od jeho účet úložiště bude přistupovat napříč oblastmi.
- Přístupu ke službě storage z jiné oblasti, kde je umístěné s sebou nese náklady v vyšší náklady kvůli sítě výchozí přenos.
- Tento scénář závisí na Traffic Manager. Vzhledem k tomu [jak funguje Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), může být nějakou dobu klientskou aplikaci, která využívá odolné funkce je potřeba poslat dotaz znova adresu funkce aplikace z Traffic Manageru. 


## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Scénář 2 – výpočetní bez místní úložiště s vyrovnáváním zatížení
Předchozí scénář popisuje pouze v případě selhání v výpočetní infrastruktury. Jestliže služba úložiště, jinak dojde k výpadku aplikace function app.
K zajištění nepřetržitého provozu odolná služba functions, tento scénář využívá účet místního úložiště na každou oblast, do které jsou nasazené aplikace function App.

![Diagram znázorňující scénář 2.](media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Tento přístup přidá vylepšení v předchozím scénáři:
- Pokud se aplikace function app nezdaří, Traffic Manager se postará o přebírání služeb při selhání do sekundární oblasti. Ale vzhledem k tomu, že aplikace function app se spoléhá na svůj vlastní účet úložiště, odolná služba functions pokračovat v práci.
- Verzi převzetí služeb při selhání není žádné další latence v převzetí přes oblast, protože jsou umístěné aplikace function app a účtu úložiště.
- Selhání ve vrstvě úložiště budou při odolné funkce, které zase aktivuje přesměrování na převzetí přes oblast dojít k chybám. Znovu protože aplikace function app a úložiště jsou izolované na oblast, odolné funkce budou nadále fungovat.
 
Důležité informace pro tento scénář:
- Pokud aplikace function app se nasadí pomocí vyhrazený plán služby App Service, replikaci výpočetní infrastruktura v převzetí nad datovým centrem zvýší náklady.
- Aktuální stav není převzetí služeb při selhání, což znamená, že spuštění a byl vytvořen kontrolní bod funkce se nezdaří. Záleží jen na klientské aplikaci zkuste to znovu nebo restartování práce.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Scénář 3 – výpočetní prostředky s využitím úložiště GRS sdílené s vyrovnáváním zatížení
Tento scénář je úprava přes prvního scénáře, implementace sdíleného úložiště účtu. Hlavním rozdílem, že účet úložiště se vytvoří s povolenou geografickou replikací.
Funkčně tento scénář nabízí stejné výhody jako scénář 1, ale umožňuje další data recovery výhody:
- Geograficky redundantní úložiště (GRS) a maximalizovat dostupnost pro váš účet úložiště GRS jen pro čtení (RA-GRS).
- Pokud dojde k výpadku oblasti služby storage, jednu z možností je, že provoz datového centra určit, že úložiště musí být převzetí služeb při selhání do sekundární oblasti. V takovém případě přístup k účtu úložiště budete přesměrováni transparentně kopii geograficky replikovaného účtu úložiště, bez zásahu uživatele.
- V tomto případě stav odolná služba functions se zachovají až po poslední replikace účtu úložiště, který probíhá každých několik minut.
Stejně jako jiné scénáře jsou důležité aspekty:
- Převzetí služeb při selhání do repliky se provádí pomocí operátorů datacenter a může trvat nějakou dobu. Do té doby se aplikace function app dochází kvůli výpadku.
- Je vyšší náklady pro účty geograficky replikovaného úložiště.
- Asynchronně vyvolá GRS. Nejnovější transakcí může dojít ke ztrátě některých kvůli latenci procesu replikace.

![Diagram znázorňující scénář 3.](media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)


## <a name="next-steps"></a>Další postup

Další informace o [navrhování aplikací s vysokou dostupností pomocí RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md)
