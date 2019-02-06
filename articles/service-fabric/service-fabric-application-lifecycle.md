---
title: Životní cyklus aplikace v Service Fabric | Dokumentace Microsoftu
description: Popisuje vývoj, nasazení, testování, upgrade, údržbu a odebírání aplikací Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 08837cca-5aa7-40da-b087-2b657224a097
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/19/2018
ms.author: ryanwi
ms.openlocfilehash: cb44311ecdf6a2c9284b14884184863237422f96
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754536"
---
# <a name="service-fabric-application-lifecycle"></a>Životní cyklus aplikace Service Fabric
Podle jiných platforem, aplikace na platformě Azure Service Fabric obvykle prochází následující fáze: návrh, vývoj, testování, nasazení, upgrade, údržbu a odebírání. Service Fabric poskytuje prvotřídní podporu pro celou aplikaci životního cyklu cloudových aplikací, od vývoje až po nasazení, každodenní správu a údržbu na případné vyřazení z provozu. Model služby umožňuje několik různých rolí se účastnit životního cyklu aplikací nezávisle na sobě. Tento článek poskytuje přehled rozhraní API a jak se používají v různých rolích v průběhu fáze životního cyklu aplikací Service Fabric.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

Následující video Microsoft Virtual Academy popisuje, jak spravovat životní cyklus vaší aplikace: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-application-lifecycle/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="service-model-roles"></a>Služba role modelu
Role modelu služby jsou:

* **Služba pro vývojáře**: Vyvíjí modulární a obecné služby, které lze použít ve více aplikacích stejného typu nebo různé typy a jinému účelu. Například služba front lze použít pro vytváření lístků podpory aplikaci (technické podpory) nebo aplikaci elektronického obchodování (nákupního košíku).
* **Vývojář aplikace**: Vytvoří aplikace integrací kolekce služeb, splnit určité požadavky nebo scénáře. Na webu elektronického obchodování může integrovat například "JSON bezstavové Front-End služby," "Aukce stavová služba" a "Stavová služba fronty" k vytvoření auctioning řešení.
* **Správce aplikace**: Díky rozhodnutí ohledně konfigurace aplikace (vyplňování v parametrech šablony konfigurace), nasazení (mapování na dostupné prostředky) a kvalitu služeb. Například aplikace správce rozhodne pro národní prostředí (v angličtině pro Spojené státy) a japonštině pro Japonsko, například aplikace. Různé nasazené aplikace mohou mít různá nastavení.
* **Operator**: Nasazení aplikace založené na konfiguraci aplikace a požadavky na zadaný správcem aplikace. Například operátor zřídí a nasadí aplikaci a zajistí, že běží v Azure. Operátory monitorovat informace o stavu a výkonu aplikace a spravovat fyzické infrastruktury, podle potřeby.

## <a name="develop"></a>Vývoj
1. A *služby pro vývojáře* vyvíjí různé typy služeb pomocí [Reliable Actors](service-fabric-reliable-actors-introduction.md) nebo [Reliable Services](service-fabric-reliable-services-introduction.md) programovací model.
2. A *služby pro vývojáře* deklarativně popisují typy služby vyvinuté v soubor manifestu služby, který se skládá z jednoho nebo více balíčků kódu, konfigurace a data.
3. *Vývojář aplikace* poté sestaví aplikaci pomocí různými typy služeb.
4. *Vývojář aplikace* deklarativně popisuje typ aplikace v manifestu aplikace odkazuje na službu manifesty základních služeb a odpovídajícím způsobem přepsání a Parametrizace různé nastavení konfigurace a nasazení základních služeb.

Zobrazit [Začínáme s Reliable Actors](service-fabric-reliable-actors-get-started.md) a [Začínáme s Reliable Services](service-fabric-reliable-services-quick-start.md) příklady.

## <a name="deploy"></a>Nasazení
1. *Správce aplikace* přizpůsobuje jim typ aplikace na konkrétní aplikaci nasadit do clusteru Service Fabric tak, že zadáte příslušné parametry **ApplicationType** element v manifestu aplikace.
2. *Operátor* nahrání balíčku aplikace do úložiště imagí clusteru pomocí [ **CopyApplicationPackage** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) nebo [  **Kopírování ServiceFabricApplicationPackage** rutiny](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Balíček aplikace obsahuje manifest aplikace a kolekce balíčků služeb. Service Fabric nasadí aplikace z balíčku aplikace, které jsou uloženy v úložišti imagí, které lze úložiště objektů blob v Azure nebo systémové služby Service Fabric.
3. *– Operátor* pak zřídí typu aplikace v cílovém clusteru pomocí balíčku nahrané aplikace [ **ProvisionApplicationAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient),  [ **Register-ServiceFabricApplicationType** rutiny](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype), nebo [ **zřízení aplikace** operace REST](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
4. Po zřízení aplikace, *operátor* aplikaci spustí pomocí parametrů poskytnutých *správce aplikace* pomocí [  **CreateApplicationAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **New-ServiceFabricApplication** rutiny](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication), nebo [ **vytvořením aplikace**  Operace REST](https://docs.microsoft.com/rest/api/servicefabric/create-an-application).
5. Po nasazení aplikace *operátor* používá [ **CreateServiceAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), [ **ServiceFabricService nový**  rutiny](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice), nebo [ **vytvořit službu** operace REST](https://docs.microsoft.com/rest/api/servicefabric/create-a-service) k vytvoření nové instance služby pro aplikaci na základě typů dostupnou službu.
6. Aplikace je nyní spuštěna v clusteru Service Fabric.

Zobrazit [nasazení aplikace](service-fabric-deploy-remove-applications.md) příklady.

## <a name="test"></a>Test
1. Po nasazení do místního vývojového clusteru nebo clusteru testu, *služby pro vývojáře* spustí testovací scénář integrované převzetí služeb při selhání pomocí [ **FailoverTestScenarioParameters** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters) a [ **FailoverTestScenario** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario) třídy, nebo [ **Invoke-ServiceFabricFailoverTestScenario** rutiny](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps). Scénáře převzetí služeb při selhání testu zadaná služba projde důležité přechody a převzetí služeb při selhání pro zajištění, že je stále k dispozici a fungují.
2. *Služby pro vývojáře* pak spustí pomocí integrované chaos testovací scénář [ **ChaosTestScenarioParameters** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters) a [  **ChaosTestScenario** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario#System_Fabric_Testability_Scenario_ChaosTestScenario) třídy, nebo [ **Invoke-ServiceFabricChaosTestScenario** rutiny](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps). Testovací scénář chaos náhodně indukuje více uzlu, balíček kódu a repliky chyb do clusteru.
3. *Služby pro vývojáře* [testuje komunikace service to service](service-fabric-testability-scenarios-service-communication.md) vytvářením testovací scénáře, které přepínají primární repliky kolem clusteru.

Zobrazit [Úvod do Fault Analysis Service](service-fabric-testability-overview.md) Další informace.

## <a name="upgrade"></a>Upgrade
1. A *služby pro vývojáře* základní služby instance aplikace aktualizací a/nebo opravy chyb a poskytuje nové verze manifestu služby.
2. *Vývojář aplikace* přepíše parametrizuje nastavení konfigurace a nasazení služby konzistentní a obsahuje novou verzi manifestu aplikace. Vývojář aplikace potom zahrnuje nové verze manifestů služby do aplikace a obsahuje novou verzi typu aplikace, balíčku aktualizovanou aplikaci.
3. *Správce aplikace* zahrnuje novou verzi typu aplikace do cílové aplikace aktualizací příslušné parametry.
4. *Operátor* nahraje balíček aktualizovanou aplikaci do clusteru image store pomocí [ **CopyApplicationPackage** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) nebo [ **Kopírování ServiceFabricApplicationPackage** rutiny](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Balíček aplikace obsahuje manifest aplikace a kolekce balíčků služeb.
5. *Operátor* zřizuje nové verze aplikace v cílovém clusteru pomocí [ **ProvisionApplicationAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [  **Register-ServiceFabricApplicationType** rutiny](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype), nebo [ **zřízení aplikace** operace REST](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
6. *Operátor* upgraduje cílovou aplikaci na novou verzi pomocí [ **UpgradeApplicationAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [  **Start-ServiceFabricApplicationUpgrade** rutiny](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade), nebo [ **Upgrade aplikace** operace REST](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application).
7. *Operátor* kontroluje průběh upgradu pomocí [ **GetApplicationUpgradeProgressAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [  **Get-ServiceFabricApplicationUpgrade** rutiny](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricapplicationupgrade), nebo [ **získat průběh upgradu aplikace** operace REST](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. V případě potřeby *operátor* změní a znovu použije parametry aktuální aplikace upgrade s využitím [ **UpdateApplicationUpgradeAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **Update-ServiceFabricApplicationUpgrade** rutiny](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade), nebo [ **aktualizace aplikace upgradu** operace REST](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade).
9. V případě potřeby *operátor* vrátí zpět aktuální aplikace upgrade s využitím [ **RollbackApplicationUpgradeAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **Start ServiceFabricApplicationRollback** rutiny](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback), nebo [ **vrácení upgradu aplikace** operace REST](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade).
10. Service Fabric se upgraduje je cílová aplikace spuštěna v clusteru bez ztráty dostupnost libovolnou z jejích základních služeb.

Zobrazit [kurz upgradu aplikace](service-fabric-application-upgrade-tutorial.md) příklady.

## <a name="maintain"></a>Udržovat
1. Pro opravy a upgrady operačního systému rozhraní Service Fabric s vaší stávající infrastrukturou Azure k zajištění dostupnosti všechny aplikace spuštěné v clusteru.
2. Upgrady a opravy na platformu Service Fabric Service Fabric upgraduje samotný bez ztráty dostupnosti u všech aplikací spuštěných v clusteru.
3. *Správce aplikace* schválí přidání nebo odebrání uzlů z clusteru po analýze kapacity historická data o využití a předpokládané budoucí poptávky.
4. *Operátor* přidává a odebírá uzly určené *správce aplikace*.
5. Při přidání nových uzlů k nebo existující uzly jsou odebrány z clusteru, Service Fabric automaticky rozděluje zátěž aktuálního spuštěných aplikací napříč všemi uzly v clusteru pro zajištění optimálního výkonu.

## <a name="remove"></a>Odebrat
1. *Operátor* můžete odstranit konkrétní instanci služby spuštěné v clusteru bez odebrání celé aplikace pomocí [ **DeleteServiceAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), [ **Odebrat ServiceFabricService** rutiny](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice), nebo [ **odstranění služby** operace REST](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service).  
2. *Operátor* můžete také odstranit instance aplikace a všechny její služby pomocí [ **DeleteApplicationAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [  **Odebrat ServiceFabricApplication** rutiny](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricapplication), nebo [ **odstranit aplikaci** operace REST](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application).
3. Po ukončení aplikace a služby mají *operátor* můžete zrušit zřízení typu aplikace pomocí [ **UnprovisionApplicationAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **Unregister-ServiceFabricApplicationType** rutiny](https://docs.microsoft.com/powershell/module/servicefabric/unregister-servicefabricapplicationtype), nebo [ **zrušit zřízení aplikace** operace REST](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application). Zrušení zřízení typu aplikace se neodebere balíček aplikace z ImageStore. Balíček aplikace musí odebrat ručně.
4. *Operátor* odebere balíček aplikace pomocí ImageStore [ **RemoveApplicationPackage** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) nebo [  **Odebrat ServiceFabricApplicationPackage** rutiny](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps).

Zobrazit [nasazení aplikace](service-fabric-deploy-remove-applications.md) příklady.

## <a name="next-steps"></a>Další postup
Další informace o vývoji testování a správu aplikací Service Fabric a služeb, naleznete v tématu:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)
* [Upgrade aplikací](service-fabric-application-upgrade.md)
* [Přehled testovatelnosti](service-fabric-testability-overview.md)
