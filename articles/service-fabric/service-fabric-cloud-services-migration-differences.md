---
title: Rozdíly mezi cloudovými službami a infrastrukturou služeb
description: Koncepční přehled pro migraci aplikací z cloudových služeb do service fabric.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 283ad2c63bb59771dab7881522e737f773ab1705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463378"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Zjistěte, jak rozdíly mezi cloudovými službami a service fabric před migrací aplikací.
Microsoft Azure Service Fabric je cloudová aplikační platforma nové generace pro vysoce škálovatelné a vysoce spolehlivé distribuované aplikace. Zavádí mnoho nových funkcí pro balení, nasazování, upgrade a správu distribuovaných cloudových aplikací. 

Toto je úvodní průvodce migrací aplikací z cloudových služeb do service fabric. Zaměřuje se především na architektonické a konstrukční rozdíly mezi cloudovými službami a service fabric.

## <a name="applications-and-infrastructure"></a>Aplikace a infrastruktura
Zásadní rozdíl mezi cloudovými službami a service fabric je vztah mezi virtuálními aplikacemi, úlohami a aplikacemi. Úloha je zde definována jako kód, který píšete k provedení určitéúlohy nebo poskytování služby.

* **Cloudové služby je o nasazení aplikací jako virtuálních aplikací.** Kód, který napíšete, je pevně spojen s instancí virtuálního uživatele, jako je například role webu nebo pracovního procesu. Nasazení úlohy v cloudových službách znamená nasazení jedné nebo více instancí virtuálních virtuálních připojení, které spouštějí úlohy. Neexistuje žádné oddělení aplikací a virtuálních aplikací, a proto neexistuje žádná formální definice aplikace. Aplikaci si lze myslet jako sadu instancí webových nebo pracovních rolí v rámci nasazení cloudových služeb nebo jako celé nasazení cloudových služeb. V tomto příkladu je aplikace zobrazena jako sada instancí rolí.

![Aplikace cloudových služeb a topologie][1]

* **Service Fabric je o nasazení aplikací do existujících virtuálních počítačů nebo počítačů se systémem Service Fabric ve Windows nebo Linuxu.** Služby, které napíšete, jsou zcela odděleny od základní infrastruktury, která je abstrahována aplikační platformou Service Fabric, takže aplikace může být nasazena do více prostředí. Úloha v Service Fabric se nazývá "služba" a jedna nebo více služeb jsou seskupeny v formálně definované aplikaci, která běží na platformě aplikace Service Fabric. Do jednoho clusteru Service Fabric lze nasadit více aplikací.

![Aplikace Service Fabric a topologie][2]

Service Fabric sám je vrstva aplikační platformy, která běží na Windows nebo Linux, zatímco cloudové služby je systém pro nasazení virtuálních počítačích spravovaných Azure s připojenými úlohami.
Model aplikace Service Fabric má řadu výhod:

* Rychlé časy nasazení. Vytváření instancí virtuálních montovny může být časově náročné. V Service Fabric virtuálních aplikací jsou nasazeny pouze jednou tvořit cluster, který je hostitelem platformy aplikace Service Fabric. Od tohoto okamžiku lze balíčky aplikací nasadit do clusteru velmi rychle.
* Hosting s vysokou hustotou. V cloudových službách hostuje virtuální msa role pracovního procesu jednu úlohu. V Service Fabric aplikace jsou oddělené od virtuálních počítače, které je spouštějí, což znamená, že můžete nasadit velký počet aplikací na malý počet virtuálních počítače, což může snížit celkové náklady na větší nasazení.
* Platforma Service Fabric může běžet kdekoli, kde mají počítače se systémem Windows Server nebo Linux, ať už je to Azure nebo místně. Platforma poskytuje vrstvu abstrakce přes základní infrastrukturu, takže vaše aplikace můžete spustit v různých prostředích. 
* Distribuovaná správa aplikací. Service Fabric je platforma, která nejen hostuje distribuované aplikace, ale také pomáhá spravovat jejich životní cyklus nezávisle na hostitelském virtuálním počítači nebo životním cyklu počítače.

## <a name="application-architecture"></a>Architektura aplikace
Architektura aplikace cloudových služeb obvykle zahrnuje mnoho závislostí externích služeb, jako je service bus, Azure Table a Blob Storage, SQL, Redis a další, které spravují stav a data aplikace a komunikace mezi webem a Role pracovních prostředků v nasazení cloudových služeb. Příklad kompletní aplikace cloudových služeb může vypadat takto:  

![Architektura cloudových služeb][9]

Aplikace Service Fabric můžete také zvolit použití stejné externí služby v kompletní aplikaci. Pomocí tohoto příkladu architektury cloudových služeb je nejjednodušší cestou migrace z cloudových služeb do service fabric nahradit pouze nasazení cloudových služeb aplikací Service Fabric, přičemž celková architektura je stejná. Role webu a pracovního procesu lze přenést na bezstavové služby Service Fabric s minimálními změnami kódu.

![Architektura Service Fabric po jednoduché migraci][10]

V této fázi by měl systém nadále fungovat stejně jako dříve. S využitím stavových funkcí Service Fabric, externí úložiště stavu lze internalizovat jako stavové služby, kde je to možné. To je více než jednoduchá migrace webových a pracovních rolí do služby Fabric bezstavové služby, protože vyžaduje psaní vlastní služby, které poskytují stejné funkce pro vaši aplikaci jako externí služby dříve. Mezi výhody tohoto plnění patří: 

* Odebrání externích závislostí 
* Sjednocení modelů nasazení, správy a upgradu. 

Příklad výsledné architektury internalizace těchto služeb může vypadat takto:

![Architektura Service Fabric po úplné migraci][11]

## <a name="communication-and-workflow"></a>Komunikace a pracovní postup
Většina aplikací cloudové služby se skládá z více než jedné úrovně. Podobně service fabric aplikace se skládá z více než jednu službu (obvykle mnoho služeb). Dva běžné komunikační modely jsou přímá komunikace a externí trvanlivé úložiště.

### <a name="direct-communication"></a>Přímá komunikace
S přímou komunikací vrstvy můžete komunikovat přímo prostřednictvím koncového bodu vystavené každou vrstvu. V bezstavových prostředích, jako jsou cloudové služby, to znamená vybrat instanci role virtuálního uživatele, náhodně nebo kruhovédotazování pro vyvážení zatížení a připojení k jeho koncovému bodu přímo.

![Přímá komunikace cloudových služeb][5]

 Přímá komunikace je běžný komunikační model v Service Fabric. Hlavní rozdíl mezi Service Fabric a cloudových služeb je, že v cloudových službách se připojíte k virtuálnímu virtuálnímu provozu, zatímco v Service Fabric se připojíte ke službě. To je důležitý rozdíl z několika důvodů:

* Služby ve službě Fabric nejsou vázány na virtuální chody, které je hostují; služby se mohou pohybovat v clusteru a ve skutečnosti se očekává, že se budou pohybovat z různých důvodů: vyrovnávání prostředků, převzetí služeb při selhání, upgrady aplikací a infrastruktury a omezení umístění nebo zatížení. To znamená, že adresa instance služby se může kdykoli změnit. 
* Virtuální počítač v Service Fabric může hostovat více služeb, z nichž každá má jedinečné koncové body.

Service Fabric poskytuje mechanismus zjišťování služby, nazvaný pojmenování služby, které lze použít k vyřešení adresy koncového bodu služeb. 

![Přímá komunikace Service Fabric][6]

### <a name="queues"></a>Fronty
Běžný majestátný komunikační mechanismus mezi úrovněmi v bezstavových prostředích, jako jsou cloudové služby, je použití fronty externího úložiště k trvale ukládání pracovních úloh z jedné vrstvy do druhé. Běžným scénářem je webová vrstva, která odesílá úlohy do fronty Azure nebo service busu, kde instance role pracovního procesu můžou úlohy dequeueovat a zpracovávat.

![Komunikace fronty cloudových služeb][7]

Stejný komunikační model lze použít v Service Fabric. To může být užitečné při migraci existující aplikace cloudových služeb do Service Fabric. 

![Přímá komunikace Service Fabric][8]

## <a name="parity"></a>Parity
[Cloudové služby se podobají Service Fabric v míře kontroly versus snadné použití, ale je to nyní starší služby a Service Fabric se doporučuje pro nový vývoj](https://docs.microsoft.com/azure/app-service/overview-compare); Následuje porovnání rozhraní API:


| **Rozhraní API cloudových služeb** | **Rozhraní API infrastruktury služeb** | **Poznámky** |
| --- | --- | --- |
| Roleinstance.GetID | FabricRuntime.GetNodeContext.NodeId nebo . Název_uzlu | ID je vlastnost NodeName |
| RoleInstance.getfaultdomain | FabricClient.QueryManager.GetNodeList | Filtrovat na NodeName a použít FD vlastnost |
| RoleInstance.GetUpgradeDomain | FabricClient.QueryManager.GetNodeList | Filtrování na NodeName a použití vlastnosti Upgrade |
| RoleInstance.GetInstanceEndpoints | FabricRuntime.GetActivationContext nebo pojmenování (ResolveService) | CodePackageActivationContext, který je poskytován fabricruntime.GetActivationContext a v rámci repliky prostřednictvím ServiceInitializationParameters.CodePackageActivationContext poskytované během . Inicializovat |
| RoleEnvironment.GetRoles | FabricClient.QueryManager.GetNodeList | Pokud chcete provést stejný druh filtrování podle typu, můžete získat seznam typů uzlů z manifestu clusteru prostřednictvím FabricClient.ClusterManager.GetClusterManifest a uchopit typy role/uzlu odtud. |
| roleprostředí.getisavailable | Connect-WindowsFabricCluster nebo vytvoření fabricruntime ukázal na konkrétní uzel | * |
| RoleEnvironment.GetLocalResource | CodePackageActivationContext.Log/Temp/Work | * |
| roleEnvironment.GetCurrentRoleInstance | CodePackageActivationContext.Log/Temp/Work | * |
| LocalResource.GetRootPath | CodePackageActivationContext.Log/Temp/Work | * |
| Role.GetInstances | FabricClient.QueryManager.GetNodeList nebo ResolveService | * |
| RoleInstanceEndpoint.GetIPEndpoint | FabricRuntime.GetActivationContext nebo pojmenování (ResolveService) | * |

## <a name="next-steps"></a>Další kroky
Nejjednodušší cesta migrace z cloudových služeb do Service Fabric je nahradit pouze nasazení cloudových služeb aplikací Service Fabric, zachování celkové architektury vaší aplikace zhruba stejné. V následujícím článku například naleznete průvodce převodem role webu nebo pracovního procesu na bezstavovou službu Service Fabric.

* [Jednoduchá migrace: převod role webu nebo pracovního procesu na službu Bez stavové struktury služby Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

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
