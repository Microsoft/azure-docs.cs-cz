---
title: Rozdíly mezi cloudových služeb a prostředků infrastruktury služby | Microsoft Docs
description: Koncepční přehled migrace aplikací z cloudové služby Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 0b87b1d3-88ad-4658-a465-9f05a3376dee
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 35ab4a9bdd66bf3571e7f189191550f88e17cee2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Další informace o rozdílech mezi cloudové služby a Service Fabric před migrací aplikace.
Microsoft Azure Service Fabric je platforma aplikace generace cloudu pro vysoce škálovatelnou a vysoce spolehlivé distribuované aplikace. Přináší mnoho nových funkcí pro balení, nasazování, upgrade a správu distribuovaných cloudové aplikace. 

Toto je úvodní příručka k migraci aplikace z cloudové služby na Service Fabric. Ho se zaměřují hlavně na architektury a návrhu rozdíly mezi cloudové služby a Service Fabric.

## <a name="applications-and-infrastructure"></a>Aplikace a infrastrukturu
Základní rozdíl mezi cloudové služby a Service Fabric se o vztah mezi virtuálními počítači, úlohy a aplikace. Zde zatížení je definován jako kód, který zápisu na provedení určitého úkolu nebo poskytovat služby.

* **Cloudové služby je o nasazení aplikací jako virtuální počítače.** Kód, který můžete psát je úzce párované do instance virtuálních počítačů, jako je Web nebo Role pracovního procesu. K nasazení úloh ve cloudové služby je nasadit jeden nebo více instancí virtuálního počítače, které spouštění úloh. Neexistuje žádné oddělení aplikací a virtuální počítače, a proto neexistuje formální definice aplikace. Aplikace můžete představit jako sadu instancí webu nebo Role pracovního procesu v rámci nasazení cloudové služby nebo celé nasazení cloudové služby. V tomto příkladu se aplikace zobrazí jako sada instancí role.

![Cloudové služby aplikace a topologie][1]

* **Service Fabric je o nasazení aplikací na existující virtuální počítače nebo počítačů Service Fabric systémem Windows nebo Linux.** Služby, které můžete psát jsou zcela odpojeného od základní infrastruktury, který je rychle abstrahované platformou aplikace Service Fabric, aby aplikace můžete nasadit na několik prostředí. Úlohy v Service Fabric se nazývá "služba" a jednu nebo více služeb jsou seskupené v oficiálně definované aplikací, který běží na platformu aplikací Service Fabric. Více aplikací můžete nasadit na jeden cluster Service Fabric.

![Aplikace Service Fabric a topologie][2]

Service Fabric, samotné je aplikační platforma vrstvu, která běží na systému Windows nebo Linux, zatímco cloudové služby je systém pro nasazení virtuálních počítačů Azure spravovat pomocí úlohy připojený.
Model aplikace Service Fabric přináší řadu výhod:

* Rychlé nasazení časy. Vytváření instancí virtuálních počítačů může být časově náročná. V Service Fabric jsou virtuální počítače nasazené pouze po k vytvoření clusteru, který je hostitelem aplikace platformy Service Fabric. Od této chvíle balíčky aplikací lze nasadit do clusteru velmi rychle.
* Hostování s vysokou hustotou. Virtuální počítač Role pracovního procesu v cloudové služby, hostuje jednu úlohu. V Service Fabric jsou oddělené od virtuální počítače, které spustit, což znamená, že můžete nasadit na velký počet aplikací malý počet virtuálních počítačů, které můžete snížit celkové náklady pro rozsáhlejší nasazení aplikace.
* Service Fabric platformy, mohou spouštět odkudkoli, má Windows Server nebo Linux počítače, jestli je Azure nebo místní. Platforma poskytuje abstraktní vrstvu nad základní infrastrukturou, takže aplikace můžete spustit v různých prostředích. 
* Správa distribuovaných aplikací. Service Fabric je platforma, že pouze hostitelé distribuované aplikace, ale také pomáhá spravovat životní cyklus nezávisle na hostování virtuálních počítačů nebo počítač životního cyklu.

## <a name="application-architecture"></a>Architektura aplikace
Architektura aplikace cloudové služby obvykle obsahuje mnoho závislostí externí služby, jako Service Bus, Azure Table a úložiště objektů Blob, SQL, Redis a dalších ke správě stavu a dat aplikaci a komunikace mezi webovou a Role pracovního procesu v nasazení cloudové služby. Příkladem dokončení aplikace cloudové služby může vypadat například takto:  

![Architektura cloudových služeb][9]

Aplikace Service Fabric můžete také vybrat použít stejné externích služeb v hotové aplikace. V tomto příkladu architektura cloudové služby, ta nejjednodušší cesta migrace z cloudové služby na Service Fabric je nahradit pouze nasazení cloudové služby aplikace Service Fabric udržuje přehled architektury stejné. Webové a rolí pracovního procesu můžete přesně do Service Fabric bezstavové služby s minimálními změnami kódu.

![Architektura Service Fabric po jednoduché migrace][10]

V této fázi systému by měly být nadále fungovat stejně jako před. Pořízení využít stavové funkcí Service Fabric, externí stav úložiště můžete internalized jako stavové služby, kde je to možné. Toto je složitější než jednoduchá migrace webových a rolí pracovního procesu bezstavové služby Service Fabric, protože vyžaduje zápis vlastní služby, které poskytují ekvivalentní funkce do vaší aplikace jako předtím externích služeb. Mezi výhody tak patří: 

* Odebrání externí závislosti 
* Sjednotit nasazení, správu a modely upgradu. 

Příklad výsledné architekturu internalizing těchto služeb může vypadat například takto:

![Architektura Service Fabric po plné migraci][11]

## <a name="communication-and-workflow"></a>Komunikace a pracovní postup
Většina aplikací Cloudová služba se skládá z více než jednu úroveň. Podobně aplikace Service Fabric se skládá z více než jedna služba (obvykle mnoho služby). Dvě běžné komunikace modely jsou přímou komunikaci a prostřednictvím externí odolné úložiště.

### <a name="direct-communication"></a>Přímé komunikaci
Pomocí přímou komunikaci vrstev komunikovat přímo prostřednictvím koncového bodu vystavené každou úroveň. V bezstavové prostředích, jako je cloudové služby, tato znamená výběr instanci role virtuálního počítače, buď náhodně nebo kruhového dotazování pro vyrovnávání zatížení a propojíte svůj koncový bod přímo.

![Cloudové služby přímé komunikaci][5]

 Přímé komunikaci je běžný komunikační model v Service Fabric. Klíčovým rozdílem mezi Service Fabric a Cloud Services je připojení k virtuálnímu počítači, že v cloudové služby, zatímco v Service Fabric připojit ke službě. Jde o důležité odlišení několik důvodů:

* Služby v Service Fabric nejsou vázány na virtuální počítače, které jsou hostiteli je; služby mohou pohyb v clusteru a ve skutečnosti, očekává se, že pohyb z různých důvodů: prostředek vyrovnávání, převzetí služeb při selhání, aplikace a infrastrukturu upgrady a omezení umístění nebo zatížení. To znamená, že můžete kdykoli změnit adresu instance služby. 
* Virtuální počítač v Service Fabric může být hostitelem více služeb, každý s koncovými body jedinečný.

Service Fabric nabízí mechanismus pro zjišťování služby, názvem služby DNS, který můžete použít k vyřešení adresy koncových bodů služby. 

![Přímé komunikaci Service Fabric][6]

### <a name="queues"></a>Fronty
Běžné mechanismus komunikace mezi vrstvami v bezstavové prostředích, jako je cloudové služby je pomocí frontu externího úložiště spolehlivě ukládat pracovní úkoly z jedné vrstvy do druhé. Obvyklým scénářem je webové vrstvy, která odesílá úlohy do fronty Azure nebo Service Bus, kde můžete instancí Role pracovního procesu dequeue – a zpracování úloh.

![Cloud Services fronty komunikace][7]

Stejný model komunikace lze v Service Fabric. To může být užitečné při migraci do Service Fabric existující aplikaci cloudové služby. 

![Přímé komunikaci Service Fabric][8]

## <a name="next-steps"></a>Další kroky
Ta nejjednodušší cesta migrace z cloudové služby na Service Fabric je nahradit pouze nasazení cloudové služby aplikace Service Fabric udržuje přehled architektury aplikace přibližně stejné. V následujícím článku poskytuje vodítko můžete převést webu nebo Role pracovního procesu na bezstavové služby Service Fabric.

* [Jednoduchá migrace: převést webu nebo Role pracovního procesu na bezstavové služby Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png
