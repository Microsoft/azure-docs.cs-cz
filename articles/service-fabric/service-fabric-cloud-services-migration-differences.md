---
title: Rozdíly mezi Cloud Services a Service Fabric
description: Koncepční přehled migrace aplikací z Cloud Services na Service Fabric.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: c7e7d346b5a39a262d1d55265becadb1c718cc04
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575767"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Přečtěte si o rozdílech mezi Cloud Services a Service Fabric před migrací aplikací.
Microsoft Azure Service Fabric je cloudová aplikační platforma nové generace pro vysoce škálovatelné a vysoce spolehlivé distribuované aplikace. Zavádí mnoho nových funkcí pro balení, nasazování, upgradování a správu distribuovaných cloudových aplikací. 

Toto je úvodní příručka pro migraci aplikací z Cloud Services na Service Fabric. Zaměřuje se především na strukturální a návrh rozdílů mezi Cloud Services a Service Fabric.

## <a name="applications-and-infrastructure"></a>Aplikace a infrastruktura
Základní rozdíl mezi Cloud Services a Service Fabric je vztah mezi virtuálními počítači, úlohami a aplikacemi. Úloha, kterou napíšete, se definuje jako kód, který zapisujete k provedení konkrétního úkolu nebo poskytnutí služby.

* **Cloud Services o nasazení aplikací jako virtuálních počítačů.** Kód, který zapisujete, je pevně spojený s instancí virtuálního počítače, jako je například webová role nebo role pracovního procesu. K nasazení úlohy v Cloud Services slouží k nasazení jedné nebo více instancí virtuálních počítačů, které spouštějí úlohu. Neexistují žádné oddělené aplikace a virtuální počítače, takže není k dispozici žádná formální definice aplikace. Aplikaci si můžete představit jako sadu instancí webové role nebo role pracovního procesu v rámci nasazení Cloud Services nebo jako celé nasazení Cloud Services. V tomto příkladu se aplikace zobrazuje jako sada instancí rolí.

![Cloud Services aplikací a topologie][1]

* **Service Fabric o nasazení aplikací do stávajících virtuálních počítačů nebo počítačů se systémem Service Fabric v systému Windows nebo Linux.** Služby, které zapisujete, jsou zcela oddělené od základní infrastruktury, která je abstraktní Service Fabric aplikační platformou, takže je možné aplikaci nasadit do více prostředí. Zatížení v Service Fabric se nazývá "služba" a jedna nebo více služeb se seskupují ve formě uživatelsky definované aplikace, která běží na platformě Service Fabric aplikace. Do jednoho clusteru Service Fabric lze nasadit více aplikací.

![Service Fabric aplikací a topologie][2]

Service Fabric sám je vrstva aplikační platformy, která běží na Windows nebo Linux, zatímco Cloud Services je systém pro nasazení virtuálních počítačů spravovaných Azure s připojenými úlohami.
Model aplikace Service Fabric má několik výhod:

* Doba rychlého nasazení. Vytváření instancí virtuálních počítačů může být časově náročné. V Service Fabric jsou virtuální počítače nasazené jenom jednou, aby tvořily cluster, který je hostitelem platformy Service Fabric aplikace. Od tohoto okamžiku je možné balíčky aplikací nasadit do clusteru velmi rychle.
* Hostování s vysokou hustotou. V Cloud Services virtuální počítač role pracovního procesu hostuje jednu úlohu. V Service Fabric jsou aplikace oddělené od virtuálních počítačů, které je spouštějí, což znamená, že můžete nasadit velký počet aplikací na malý počet virtuálních počítačů, což může snížit celkové náklady pro větší nasazení.
* Platforma Service Fabric může běžet kdekoli s počítači s Windows serverem nebo Linux, ať už se jedná o Azure, nebo v místním prostředí. Platforma poskytuje abstrakci vrstvy nad základní infrastrukturou, takže vaše aplikace může běžet v různých prostředích. 
* Správa distribuovaných aplikací. Service Fabric je platforma, která nejen hostuje distribuované aplikace, ale také pomáhá spravovat životní cyklus nezávisle na hostitelském VIRTUÁLNÍm počítači nebo v životním cyklu počítače.

## <a name="application-architecture"></a>Architektura aplikace
Architektura Cloud Services aplikace obvykle zahrnuje řadu externích závislostí služby, jako je například Service Bus, tabulka Azure a Blob Storage, SQL, Redis a další pro správu stavu a dat aplikace a komunikace mezi webovými a pracovními rolemi v nasazení Cloud Services. Příklad kompletní Cloud Services aplikace může vypadat takto:  

![Architektura Cloud Services][9]

Service Fabric aplikace se také můžou rozhodnout použít stejné externí služby v úplné aplikaci. Pomocí tohoto ukázkového Cloud Services architektury je nejjednodušší cesta migrace z Cloud Services na Service Fabric nahradit pouze Cloud Services nasazení pomocí Service Fabric aplikace, přičemž celková architektura zůstane stejná. Webové a pracovní role lze přenést do Service Fabric bezstavových služeb s minimálními změnami kódu.

![Service Fabric architektury po jednoduché migraci][10]

V této fázi by měl systém dál fungovat stejně jako dřív. Využitím stavových funkcí Service Fabric můžete v případě potřeby vytvářet externí úložiště stavu jako stavové služby. To je víc, než jednoduchá migrace webových a pracovních rolí do Service Fabric bezstavových služeb, protože to vyžaduje psaní vlastních služeb, které v rámci vaší aplikace poskytují ekvivalentní funkce jako externí služby. K těmto výhodám patří: 

* Odebírají se externí závislosti. 
* Sjednocení modelů nasazení, správy a upgradu. 

Příklad výsledné architektury internalizing tyto služby by mohl vypadat takto:

![Service Fabric architektura po úplné migraci][11]

## <a name="communication-and-workflow"></a>Komunikace a pracovní postup
Většina aplikací cloudových služeb se skládá z více než jedné úrovně. Podobně Service Fabric aplikace se skládá z více než jedné služby (obvykle mnoho služeb). Dva běžné komunikační modely jsou přímá komunikace a prostřednictvím externího trvalého úložiště.

### <a name="direct-communication"></a>Přímá komunikace
Díky přímé komunikaci můžou vrstvy komunikovat přímo prostřednictvím koncového bodu vystaveného každou vrstvou. V bezstavových prostředích, jako je například Cloud Services, to znamená výběr instance role virtuálního počítače, buď náhodným, nebo kruhovým dotazem, Vyrovnávání zatížení a přímé připojení ke koncovému bodu.

![Cloud Services přímá komunikace][5]

 Přímá komunikace je běžným komunikačním modelem v Service Fabric. Hlavním rozdílem mezi Service Fabric a Cloud Services je to, že se v Cloud Services připojíte k virtuálnímu počítači, zatímco v Service Fabric se připojíte ke službě. Toto je důležitý rozdíl z několika důvodů:

* Služby v Service Fabric nejsou vázány na virtuální počítače, které je hostují. služby se můžou v clusteru pohybovat a ve skutečnosti se očekává, že se budou pohybovat z různých důvodů: Vyrovnávání zatížení, převzetí služeb při selhání, upgradování aplikací a infrastruktury a omezení umístění nebo zatížení. To znamená, že se adresa instance služby může kdykoli změnit. 
* Virtuální počítač v Service Fabric může hostovat několik služeb, každý s jedinečnými koncovými body.

Service Fabric poskytuje mechanismus zjišťování služeb nazvaný Naming Service, který se dá použít k překladu adres koncových bodů služeb. 

![Diagram, který ukazuje, jak Service Fabric poskytuje mechanismus zjišťování služeb nazvaný Naming Service, který se dá použít k překladu adres koncových bodů služeb.][6]

### <a name="queues"></a>Fronty
Běžným komunikačním mechanismem mezi vrstvami v bezstavových prostředích, jako je například Cloud Services, je použití externí fronty úložiště k trvaleí pracovních úloh z jedné úrovně do druhé. Běžným scénářem je webová vrstva, která odesílá úlohy do fronty Azure nebo Service Bus, kde můžou instance rolí pracovních procesů vyřadit z fronty a zpracovat úlohy.

![Komunikace s Cloud Services Queue][7]

Stejný komunikační model lze použít v Service Fabric. To může být užitečné při migraci existující aplikace Cloud Services do Service Fabric. 

![Service Fabric přímá komunikace][8]

## <a name="parity"></a>Parity
[Cloud Services je podobná Service Fabric ve stupni řízení a jednoduchosti použití, ale teď je to starší služba a Service Fabric se doporučuje pro nový vývoj](/azure/architecture/guide/technology-choices/compute-decision-tree). Toto je porovnání rozhraní API:


| **Rozhraní API cloudové služby** | **Rozhraní API pro Service Fabric** | **Poznámky** |
| --- | --- | --- |
| RoleInstance. getId – | FabricRuntime. GetNodeContext. NodeId nebo. NodeName | ID je vlastnost Node. |
| RoleInstance. GetFaultDomain | FabricClient. QueryManager. GetNodeList | Filtrovat podle node a použít vlastnost FD |
| RoleInstance. GetUpgradeDomain | FabricClient. QueryManager. GetNodeList | Filtrovat podle node a použít vlastnost upgrade |
| RoleInstance. GetInstanceEndpoints | FabricRuntime. GetActivationContext nebo pojmenování (ResolveService) | CodePackageActivationContext, která je poskytována v rámci FabricRuntime. GetActivationContext a v replikách prostřednictvím ServiceInitializationParameters. CodePackageActivationContext poskytovaných během. Spuštění |
| RoleEnvironment. GetRole | FabricClient. QueryManager. GetNodeList | Chcete-li provést stejný druh filtrování podle typu, můžete získat seznam typů uzlů z manifestu clusteru prostřednictvím FabricClient. ClusterManager. GetClusterManifest a z něj převzít typy rolí nebo uzlů. |
| RoleEnvironment. GetIsAvailable | Connect-WindowsFabricCluster nebo vytvoření FabricRuntime ukazatele na konkrétní uzel | * |
| RoleEnvironment. GetLocalResource | CodePackageActivationContext. log/Temp/Work | * |
| RoleEnvironment. GetCurrentRoleInstance | CodePackageActivationContext. log/Temp/Work | * |
| LocalResource.GetRootPath | CodePackageActivationContext. log/Temp/Work | * |
| Role. GetInstances | FabricClient. QueryManager. GetNodeList nebo ResolveService | * |
| RoleInstanceEndpoint.GetIPEndpoint | FabricRuntime. GetActivationContext nebo pojmenování (ResolveService) | * |

## <a name="next-steps"></a>Další kroky
Nejjednodušší cesta migrace z Cloud Services do Service Fabric je nahrazení pouze Cloud Servicesho nasazení s využitím aplikace Service Fabric a udržování celé architektury aplikace přibližně stejně. Následující článek popisuje Průvodce převodem webové role nebo role pracovního procesu na bezstavovou službu Service Fabric.

* [Jednoduchá migrace: převod webové role nebo role pracovního procesu na bezstavovou službu Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

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