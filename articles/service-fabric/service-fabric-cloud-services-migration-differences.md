---
title: Rozdíly mezi cloudovými službami a Service Fabric | Dokumentace Microsoftu
description: Přehled migrace aplikací ze služby Cloud Services do Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 0b87b1d3-88ad-4658-a465-9f05a3376dee
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 8b486e617389e1611dfebf3d347d2d64df088593
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258640"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Další informace o rozdílech mezi cloudovými službami a Service Fabric před migrací aplikací.
Microsoft Azure Service Fabric je platforma aplikací generace cloud pro vysoce škálovatelné, vysoce spolehlivých distribuovaných aplikací. Zavádí řadu nových funkcí pro balení, nasazování, upgradu a správy distribuovaných cloudových aplikací. 

Toto je úvodní příručka k migraci aplikace ze služby Cloud Services do Service Fabric. Se zaměřují hlavně na architektury a rozdíly v návrhu mezi cloudovými službami a Service Fabric.

## <a name="applications-and-infrastructure"></a>Aplikace a infrastrukturu
Základní rozdíl mezi cloudovými službami a Service Fabric se o vztah mezi virtuálními počítači, úlohy a aplikace. Tady je definovaná jako kód, který napíšete k provedení určitého úkolu nebo poskytovat služby.

* **Cloud Services je o nasazení aplikací jako virtuální počítače.** Kód, který napíšete je těsně spjat s instance virtuálního počítače, jako jsou webové nebo pracovní Role. Nasazení úlohy v cloudových službách je nasadit jednu nebo víc instancí virtuálních počítačů, na kterých běží úlohy. Neexistuje žádný oddělení aplikací a virtuálních počítačů, a proto neexistuje žádné formální definice aplikace. Aplikace můžete představit jako sadu instance webové nebo pracovní Role v rámci nasazení Cloud Services nebo celé nasazení cloudové služby. V tomto příkladu se aplikace zobrazí jako sadu instancí rolí.

![Cloudové služby, aplikace a topologie][1]

* **Service Fabric je o nasazení aplikací na existující virtuální počítače nebo počítače, které běží Service Fabric ve Windows nebo Linuxu.** Služby, který napíšete jsou zcela oddělený od základní infrastruktury, která je okamžitě abstrahovaný platformou aplikace Service Fabric, aby je možné nasadit aplikaci do různých prostředí. Úlohy v Service Fabric se nazývá "služba" a jednu nebo více služeb jsou seskupené ve formálně definované aplikaci, která běží na platformě aplikace Service Fabric. Více aplikací můžete nasadit na jeden cluster Service Fabric.

![Aplikace Service Fabric a topologie][2]

Service Fabric, samotného je aplikační vrstva platformy, na kterém běží ve Windows nebo Linuxu, že cloudové služby je systém pro nasazení virtuálních počítačů se spravovanými Azure s úlohami, které jsou připojené.
Aplikační model Service Fabric má několik výhod:

* Časy rychlé nasazení. Vytváření instancí virtuálních počítačů může být časově náročné. V Service Fabric jsou virtuální počítače nasadit jenom po vytvoření clusteru, který hostuje aplikační platforma Service Fabric. Od této chvíle je možné nasadit balíčky aplikací do clusteru velmi rychle.
* Hostování s vysokou hustotou. V cloudových službách je hostitelem jedné úlohy virtuálního počítače Role pracovního procesu. V Service Fabric jsou oddělené od virtuálních počítačů, na kterých běží, což znamená, že můžete nasadit velký počet aplikací na malý počet virtuálních počítačů, což může snížit celkové náklady u větších nasazení aplikace.
* Service Fabric, které platforma dají spustit kdekoli, který má počítačů Windows serveru nebo Linuxu, ať už jde o Azure nebo místně. Platforma poskytuje abstraktní vrstvu nad základní infrastrukturou, může vaše aplikace spouštět na různých prostředí. 
* Správa distribuovaných aplikací. Service Fabric je platforma, že pouze hostitelé distribuované aplikace, ale také pomáhá spravovat životní cyklus nezávisle na hostování virtuálních počítačů nebo počítač životního cyklu.

## <a name="application-architecture"></a>Architektura aplikace
Architektura aplikace cloudové služby obvykle obsahuje mnoho závislosti externí služby, jako jsou služby Service Bus, Azure Table a Blob Storage, SQL, Redis a ostatní spravovat stav a data, aplikace a komunikace mezi webovou a Role pracovního procesu v nasazení Cloud Services. Příklad dokončené aplikace cloudové služby může vypadat takto:  

![Architektura cloudových služeb][9]

Aplikace Service Fabric můžete také zvolit použití stejné externí služby v dokončené aplikace. Použití tento příklad architektury cloudové služby, nejjednodušší způsob migrace ze služby Cloud Services do Service Fabric je nahraďte aplikace Service Fabric, udržování architektury stejné pouze nasazení Cloud Services. Webové a pracovní role můžete přenést do bezstavové služby Service Fabric s minimálními změnami kódu.

![Architektura Service Fabric po jednoduché přenesení][10]

V této fázi by ml fungovat stejně jako před systému. Díky Service Fabric stavové funkce, úložiště externího stavu můžete internalized jak stavová služeb, kde je to možné. To se tak zapojí víc než jednoduchá migrace webových a pracovních rolí pro bezstavové služby Service Fabric, jak vyžaduje vytváření vlastních služeb, které poskytují ekvivalentní funkce, které vaše aplikace jako předtím externích služeb. Mezi výhody tohoto postupu patří: 

* Odebrat externí závislosti 
* Sjednotit nasazení, správu a modely upgradu. 

Příklad výsledný architekturu internalizing těchto služeb může vypadat například takto:

![Architektura Service Fabric po plné migraci][11]

## <a name="communication-and-workflow"></a>Komunikaci a pracovní postupy
Většina aplikací Cloudovou službu se skládá z více než jedné vrstvy. Obdobně aplikace Service Fabric se skládá z více než jedna služba (obvykle mnoho služeb). Dvě běžné modely komunikace se přímá komunikace a prostřednictvím externího trvalého úložiště.

### <a name="direct-communication"></a>Přímá komunikace
Pomocí přímé komunikaci úrovně komunikovat přímo prostřednictvím koncového bodu určeného jednotlivé úrovně. V bezstavové prostředí Cloud Services, tento znamená, že vyberete instance role virtuálního počítače, buď náhodně nebo kruhové dotazování na Vyrovnávání zatížení a propojíte svůj koncový bod přímo.

![Cloud Services přímá komunikace][5]

 Přímá komunikace je společný model komunikace v Service Fabric. Klíčovým rozdílem mezi Service Fabric a Cloud Services je tento v Cloud Services připojit k virtuálnímu počítači, zatímco v Service Fabric připojit ke službě. Jde o důležité odlišení z několika důvodů:

* Služby v Service Fabric, které nejsou vázány na virtuální počítače, které jsou hostiteli. služby mohou pohybovat v clusteru a ve skutečnosti se očekává přesouvat z různých důvodů: Vyrovnávání prostředků, převzetí služeb při selhání, aplikace a infrastruktury upgrady a omezení umístění nebo zatížení. To znamená, že se že můžete kdykoli změnit adresu instance služby. 
* Virtuální počítač v Service Fabric může být hostitelem více služeb, z nichž každá s koncovými body jedinečný.

Service Fabric poskytuje mechanismus pro zjišťování služby, volá pojmenování Service, které je možné přeložit adresy koncových bodů služby. 

![Přímá komunikace Service Fabric][6]

### <a name="queues"></a>Fronty
Běžné mechanizmus pro komunikaci mezi vrstvami v bezstavové prostředí, jako je Cloud Services se má používat fronty služby storage externí trvale ukládat pracovní úkoly z jedné vrstvy do druhé. Běžný scénář, kdy je webová vrstva, která odesílá úloh do Azure Queue nebo služby Service Bus, ve kterém může instance Role pracovního procesu odstranění z fronty a zpracování úloh.

![Cloudové služby queue komunikace][7]

Stejný model komunikace je použít v Service Fabric. To může být užitečné při migraci stávající aplikace Cloud Services do Service Fabric. 

![Přímá komunikace Service Fabric][8]

## <a name="parity"></a>Parita
[Cloud Services je podobná Service Fabric, co se týče úrovně kontroly a snadnosti použití, ale je teď o starší službu a Service Fabric se doporučuje pro vývoj nových projektů](https://docs.microsoft.com/azure/app-service/overview-compare); porovnávání rozhraní API je následující:


| **Cloudová služba rozhraní API** | **Service Fabric rozhraní API** | **Poznámky** |
| --- | --- | --- |
| RoleInstance.GetID | FabricRuntime.GetNodeContext.NodeId nebo. NodeName | ID je vlastnost NodeName |
| RoleInstance.GetFaultDomain | FabricClient.QueryManager.GetNodeList | Filtrovat podle NodeName a použijte vlastnost FD |
| RoleInstance.GetUpgradeDomain | FabricClient.QueryManager.GetNodeList | Filtrovat podle NodeName a použijte vlastnost upgradu |
| RoleInstance.GetInstanceEndpoints | FabricRuntime.GetActivationContext nebo pojmenování (ResolveService) | CodePackageActivationContext, který je k dispozici FabricRuntime.GetActivationContext i v rámci repliky prostřednictvím ServiceInitializationParameters.CodePackageActivationContext využít ve verzi. Inicializace |
| RoleEnvironment.GetRoles | FabricClient.QueryManager.GetNodeList | Pokud chcete provést stejný druh filtrování podle typu, který můžete získat seznam typů uzlů z clusteru manifest prostřednictvím FabricClient.ClusterManager.GetClusterManifest a Vezmu typy rolí/uzlů z něj. |
| RoleEnvironment.GetIsAvailable | Připojit WindowsFabricCluster nebo vytvořit FabricRuntime odkazovala na jednotlivé uzly | * |
| RoleEnvironment.GetLocalResource | CodePackageActivationContext.Log/Temp/Work | * |
| RoleEnvironment.GetCurrentRoleInstance | CodePackageActivationContext.Log/Temp/Work | * |
| LocalResource.GetRootPath | CodePackageActivationContext.Log/Temp/Work | * |
| Role.GetInstances | FabricClient.QueryManager.GetNodeList nebo ResolveService | * |
| RoleInstanceEndpoint.GetIPEndpoint | FabricRuntime.GetActivationContext nebo pojmenování (ResolveService) | * |

## <a name="next-steps"></a>Další kroky
Nejjednodušší způsob migrace ze služby Cloud Services do Service Fabric je nahradit pouze nasazení Cloud Services s aplikací Service Fabric, udržování architektury aplikace zhruba stejný. V následujícím článku obsahuje tento průvodce pomůže převést webové nebo pracovní Role pro bezstavové služby Service Fabric.

* [Jednoduchá migrace: převést webové nebo pracovní Role pro bezstavové služby Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

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
