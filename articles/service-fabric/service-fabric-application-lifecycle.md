---
title: Životní cyklus aplikace v Service Fabric | Microsoft Docs
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
ms.openlocfilehash: e7dddfca3640615cb851fb6dce9eaa80260ccbf6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-application-lifecycle"></a>Životní cyklus aplikace Service Fabric
Jako s jinými platformami, aplikace na Azure Service Fabric obvykle projde má tyto fáze: návrh, vývoj, testování, nasazení, upgrade, údržbu a odebírání. Service Fabric nabízí prvotřídní podporu pro celou aplikaci životního cyklu aplikací cloudu, od vývoje přes nasazení, každodenní správu a údržbu na případné vyřazení z provozu. Model služby umožňuje několik různých rolí se zúčastnit nezávisle v průběhu životního cyklu aplikace. Tento článek obsahuje přehled rozhraní API a jak se používají různé role v průběhu fáze životního cyklu aplikace Service Fabric.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

V následujícím videu Microsoft Virtual Academy popisuje, jak spravovat životním cyklu aplikací: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-application-lifecycle/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="service-model-roles"></a>Role služby modelu
Role modelu služby jsou:

* **Služba vývojáře**: sama vyvinula modulární a obecné služby, které můžete jinému účelu a použít v několika aplikací stejného typu nebo různé typy. Například fronty služby slouží k vytváření lístků aplikace (technická podpora) nebo aplikace pro elektronické obchodování (nákupní košík).
* **Vývojář aplikace**: vytvoří aplikace integrací kolekci služeb splnit určité požadavky na konkrétní nebo scénářů. Webem elektronického obchodování například může integrovat "JSON bezstavové Front-End služby," "Aukce Stateful služba" a "Fronty Stateful služba" k vytvoření auctioning řešení.
* **Správce aplikací**: rozhoduje na konfiguraci aplikace (vyplnění konfigurační parametry šablony), nasazení (mapování na dostupné prostředky) a kvalitu služeb. Například Správce aplikací rozhodne pro národní prostředí (angličtina pro Spojené státy) nebo japonština pro Japonsko, například aplikace. Jiné nasazené aplikace mohou mít různá nastavení.
* **Operátor**: nasadí aplikace založené na konfiguraci aplikace a požadavky na určenou správcem aplikace. Například operátor zřídí a nasadí aplikaci a zajistí, že je spuštěna v Azure. Operátory i sledování informací o stavu a výkonu aplikací a spravovat fyzické infrastruktuře podle potřeby.

## <a name="develop"></a>Vývoj
1. A *služby vývojáře* sama vyvinula různých typů služeb pomocí [Reliable Actors](service-fabric-reliable-actors-introduction.md) nebo [spolehlivé služby](service-fabric-reliable-services-introduction.md) programovací model.
2. A *služby vývojáře* deklarativně popisuje typy vyvinuté služby v souboru manifestu služby skládající se z jedné nebo více balíčků kódu, konfigurace a data.
3. *Vývojář aplikace* pak sestavení aplikace pomocí typy jinou službu.
4. *Vývojář aplikace* deklarativně popisuje typ aplikace v manifestu aplikace odkazující na manifesty služby základní služby a odpovídajícím způsobem přepsání a Parametrizace různá nastavení konfigurace a nasazení základních služeb.

V tématu [začít pracovat s Reliable Actors](service-fabric-reliable-actors-get-started.md) a [Začínáme se službami Reliable Services](service-fabric-reliable-services-quick-start.md) příklady.

## <a name="deploy"></a>Nasazení
1. *Správce aplikací* přizpůsobuje jim typ aplikace na konkrétní aplikaci pro nasazení do clusteru Service Fabric zadáním příslušné parametry **ApplicationType** element v manifestu aplikace.
2. *Operátor* nahrávání balíčku aplikace do úložiště bitové kopie clusteru pomocí [ **CopyApplicationPackage** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CopyApplicationPackage_System_String_System_String_System_String_) nebo [ **kopie ServiceFabricApplicationPackage** rutiny](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Balíček aplikace obsahuje manifest aplikace a kolekce balíčky služeb. Service Fabric nasadí aplikace z balíčku aplikace, které jsou uložené v úložišti bitové kopie, které může být úložišti objektů blob v Azure nebo službu system Service Fabric.
3. *Operátor* pak zřídí typu aplikace v cílovém clusteru z nahrané aplikace pomocí balíčku [ **ProvisionApplicationAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_ProvisionApplicationAsync_System_String_System_TimeSpan_System_Threading_CancellationToken_), [ **Register-ServiceFabricApplicationType** rutiny](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype), nebo [ **zřídit aplikaci** operace REST](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
4. Po zřízení aplikace, *operátor* spustí aplikaci, která se parametry poskytl *Správce aplikací* pomocí [ **CreateApplicationAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CreateApplicationAsync_System_Fabric_Description_ApplicationDescription_System_TimeSpan_System_Threading_CancellationToken_), [ **New-ServiceFabricApplication** rutiny](https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricapplication), nebo [ **vytvořit aplikaci** operace REST](https://docs.microsoft.com/rest/api/servicefabric/create-an-application).
5. Po nasazení aplikace, *operátor* používá [ **CreateServiceAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient#System_Fabric_FabricClient_ServiceManagementClient_CreateServiceAsync_System_Fabric_Description_ServiceDescription_System_TimeSpan_System_Threading_CancellationToken_), [ **New-ServiceFabricService** rutiny](https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricservice), nebo [ **vytvořit službu** operace REST](https://docs.microsoft.com/rest/api/servicefabric/create-a-service) k vytvoření nové instance služby pro aplikaci na základě typů dostupných služeb.
6. Aplikace je nyní spuštěna v clusteru Service Fabric.

V tématu [nasazení aplikace](service-fabric-deploy-remove-applications.md) příklady.

## <a name="test"></a>Test
1. Po nasazení do místního vývojového clusteru nebo na zkušební cluster *služby vývojáře* spouští scénáře testu předdefinované převzetí služeb při selhání pomocí [ **FailoverTestScenarioParameters** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters#System_Fabric_Testability_Scenario_FailoverTestScenarioParameters) a [ **FailoverTestScenario** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario#System_Fabric_Testability_Scenario_FailoverTestScenario) třídy, nebo [ **Invoke-ServiceFabricFailoverTestScenario** rutiny](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps). Scénář testovací převzetí služeb při selhání je spuštěna služba zadaný prostřednictvím důležité přechody a převzetí služeb při selhání k zajištění, že je stále dostupné a funkční.
2. *Služby vývojáře* pak spustí předdefinované chaos testovací scénář pomocí [ **ChaosTestScenarioParameters** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters#System_Fabric_Testability_Scenario_ChaosTestScenarioParameters) a [ **ChaosTestScenario** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario#System_Fabric_Testability_Scenario_ChaosTestScenario) třídy, nebo [ **Invoke-ServiceFabricChaosTestScenario** rutiny](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps). Scénáře testu chaos náhodně indukuje více uzel, balíčku kódu a repliky chyb do clusteru.
3. *Služby vývojáře* [testy service-to-service komunikace](service-fabric-testability-scenarios-service-communication.md) vytvářením testovací scénáře, které přesunout primární repliky kolem clusteru.

V tématu [Úvod ke službě analýza selhání](service-fabric-testability-overview.md) Další informace.

## <a name="upgrade"></a>Upgrade
1. A *služby vývojáře* aktualizace základní služby instancí aplikace a opravy chyb a obsahuje novou verzi service manifest.
2. *Vývojář aplikace* přepsání a parameterizes nastavení konfigurace a nasazení služby konzistentní a obsahuje novou verzi souboru manifestu aplikace. Vývojář aplikace pak zahrnuje nové verze manifesty služby do aplikace a poskytuje nové verze typu aplikace v balíčku aktualizované aplikace.
3. *Správce aplikací* zahrnuje novou verzi typ aplikace do cílové aplikace aktualizací příslušné parametry.
4. *Operátor* odesílá balíček aktualizované aplikace do úložiště bitové kopie clusteru pomocí [ **CopyApplicationPackage** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CopyApplicationPackage_System_String_System_String_System_String_) nebo [ **kopie ServiceFabricApplicationPackage** rutiny](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Balíček aplikace obsahuje manifest aplikace a kolekce balíčky služeb.
5. *Operátor* zřídí pomocí nové verze aplikace v cílovém clusteru [ **ProvisionApplicationAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_ProvisionApplicationAsync_System_String_System_TimeSpan_System_Threading_CancellationToken_), [ **Register-ServiceFabricApplicationType** rutiny](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype), nebo [ **zřídit aplikaci** operace REST](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
6. *Operátor* upgraduje cílová aplikace na novou verzi pomocí [ **UpgradeApplicationAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UpgradeApplicationAsync_System_Fabric_Description_ApplicationUpgradeDescription_System_TimeSpan_System_Threading_CancellationToken_), [ **Start-ServiceFabricApplicationUpgrade** rutiny](https://docs.microsoft.com/powershell/servicefabric/vlatest/start-servicefabricapplicationupgrade), nebo [ **Upgrade aplikace** operace REST](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application).
7. *Operátor* kontroluje průběh upgradu pomocí [ **GetApplicationUpgradeProgressAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_GetApplicationUpgradeProgressAsync_System_Uri_System_TimeSpan_System_Threading_CancellationToken_), [ **Get-ServiceFabricApplicationUpgrade** rutiny](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricapplicationupgrade), nebo [ **získat průběh upgradu aplikace** operace REST](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. V případě potřeby *operátor* upraví a znovu použije parametry aktuální aplikace upgradovat pomocí [ **UpdateApplicationUpgradeAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UpdateApplicationUpgradeAsync_System_Fabric_Description_ApplicationUpgradeUpdateDescription_System_TimeSpan_System_Threading_CancellationToken_), [ **aktualizace ServiceFabricApplicationUpgrade** rutiny](https://docs.microsoft.com/powershell/servicefabric/vlatest/update-servicefabricapplicationupgrade), nebo [ **aktualizace aplikace upgradu** operace REST](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade).
9. V případě potřeby *operátor* vrátí zpět aktuální aplikaci upgradovat pomocí [ **RollbackApplicationUpgradeAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_RollbackApplicationUpgradeAsync_System_Uri_System_TimeSpan_System_Threading_CancellationToken_), [ **Start-ServiceFabricApplicationRollback** rutiny](https://docs.microsoft.com/powershell/servicefabric/vlatest/start-servicefabricapplicationrollback), nebo [ **vrácení upgradu aplikace** operace REST](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade).
10. Service Fabric upgraduje cílové aplikace běžící v clusteru bez ztráty dostupnost žádné z jejích základních služeb.

Najdete v článku [upgradu kurz aplikace](service-fabric-application-upgrade-tutorial.md) příklady.

## <a name="maintain"></a>Udržovat
1. Pro operační systém upgraduje a opravy Service Fabric rozhraní s infrastruktury Azure k zajištění dostupnosti všechny aplikace běžící v clusteru.
2. Pro upgrade a opravy pro platformu Service Fabric upgradů Service Fabric samotné bez ztráty dostupnosti všech aplikací spuštěných v clusteru.
3. *Správce aplikací* schválí přidání nebo odebrání uzlů z clusteru se po analýze kapacity historická data o využití a předpokládané budoucí poptávky.
4. *Operátor* přidá a odebere uzly určeného *Správce aplikací*.
5. Při přidání nových uzlů k nebo jsou odebrat existující uzly clusteru, Service Fabric automaticky zatížení – zůstatky spuštěné aplikace pro všechny uzly v clusteru a dosažení optimálního výkonu.

## <a name="remove"></a>Odebrat
1. *Operátor* můžete odstranit konkrétní instanci služby běžící v clusteru bez odebrání celá aplikace pomocí [ **DeleteServiceAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient#System_Fabric_FabricClient_ServiceManagementClient_DeleteServiceAsync_System_Fabric_Description_DeleteServiceDescription_System_TimeSpan_System_Threading_CancellationToken_), [ **odebrat ServiceFabricService** rutiny](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricservice), nebo [ **odstranění služby** operace REST](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service).  
2. *Operátor* můžete také odstranit instanci aplikace a všechny její služby pomocí [ **DeleteApplicationAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_DeleteApplicationAsync_System_Fabric_Description_DeleteApplicationDescription_System_TimeSpan_System_Threading_CancellationToken_), [ **odebrat ServiceFabricApplication** rutiny](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricapplication), nebo [ **odstranit aplikaci** operace REST](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application).
3. Po přestaly aplikací a služeb, *operátor* můžete zrušit zřízení typu aplikace pomocí [ **UnprovisionApplicationAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UnprovisionApplicationAsync_System_String_System_String_System_TimeSpan_System_Threading_CancellationToken_), [ **Unregister-ServiceFabricApplicationType** rutiny](https://docs.microsoft.com/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype), nebo [ **zrušit zřízení aplikace** operace REST](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application). Rušení zajišťování typ aplikace se neodebere balíčku aplikace z úložišti ImageStore. Balíček aplikace musíte ručně odebrat.
4. *Operátor* odebere balíčku aplikace z úložiště bitových kopií pomocí [ **RemoveApplicationPackage** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_RemoveApplicationPackage_System_String_System_String_) nebo [ **ServiceFabricApplicationPackage odebrat** rutiny](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps).

V tématu [nasazení aplikace](service-fabric-deploy-remove-applications.md) příklady.

## <a name="next-steps"></a>Další postup
Další informace o vývoji testování a Správa aplikací Service Fabric a služeb, najdete v části:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)
* [Upgrade aplikací](service-fabric-application-upgrade.md)
* [Přehled testovatelnosti](service-fabric-testability-overview.md)
