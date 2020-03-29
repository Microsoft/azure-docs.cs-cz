---
title: Životní cyklus aplikace v service fabric
description: Popisuje vývoj, nasazování, testování, upgrade, údržbu a odebírání aplikací Service Fabric.
ms.topic: conceptual
ms.date: 1/19/2018
ms.openlocfilehash: beeb1f1512cf94582dd561fa768f2e8e6649d686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378000"
---
# <a name="service-fabric-application-lifecycle"></a>Životní cyklus aplikace Service Fabric
Stejně jako u jiných platforem aplikace na Azure Service Fabric obvykle prochází následující fáze: návrh, vývoj, testování, nasazení, upgrade, údržbu a odebrání. Service Fabric poskytuje prvotřídní podporu pro celý životní cyklus aplikací cloudových aplikací, od vývoje přes nasazení, každodenní správu a údržbu až po případné vyřazení z provozu. Model služby umožňuje několik různých rolí k účasti nezávisle na životním cyklu aplikace. Tento článek obsahuje přehled rozhraní API a jak jsou používány různými rolemi v průběhu fází životního cyklu aplikace Service Fabric.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="service-model-roles"></a>Role modelu služby
Role modelu služby jsou:

* **Vývojář služeb**: Vyvíjí modulární a obecné služby, které lze změnit a použít ve více aplikacích stejného typu nebo různých typů. Službu fronty lze například použít k vytvoření aplikace pro prodej jízdenek (helpdesk) nebo aplikace elektronického obchodu (nákupní košík).
* **Vývojář aplikace**: Vytvoří aplikace integrací kolekce služeb, aby splňovaly určité specifické požadavky nebo scénáře. Například e-commerce web může integrovat "JSON bezstátní front-end služby", "Aukce stateful Service" a "Queue Stateful Service" k vytvoření aukční řešení.
* **Správce aplikace**: Rozhoduje o konfiguraci aplikace (vyplnění parametrů šablony konfigurace), nasazení (mapování na dostupné prostředky) a kvalitě služby. Například správce aplikace rozhodne národní prostředí jazyka (angličtina pro Spojené státy nebo japonština pro Japonsko, například) aplikace. Různé nasazené aplikace mohou mít různá nastavení.
* **Operátor**: Nasazuje aplikace na základě konfigurace aplikace a požadavků určených správcem aplikace. Například operátor zřídí a nasadí aplikaci a zajistí, že běží v Azure. Operátoři monitorují informace o stavu a výkonu aplikací a podle potřeby udržují fyzickou infrastrukturu.

## <a name="develop"></a>Vývoj
1. *Vývojář služby* vyvíjí různé typy služeb pomocí [programovacího](service-fabric-reliable-actors-introduction.md) modelu Reliable Actors nebo [Reliable Services.](service-fabric-reliable-services-introduction.md)
2. *Vývojář služby* deklarativně popisuje rozvinuté typy služeb v souboru manifestu služby skládající se z jednoho nebo více balíčků kódu, konfigurace a dat.
3. *Vývojář aplikace* pak vytvoří aplikaci pomocí různých typů služeb.
4. *Vývojář aplikace* deklarativně popisuje typ aplikace v manifestu aplikace odkazováním na manifesty služby základních služeb a odpovídajícím přepsáním a parametrizací různých nastavení konfigurace a nasazení základních služeb.

Podívejte [se na příklady Začínáme se spolehlivými herci](service-fabric-reliable-actors-get-started.md) a [Začínáme se spolehlivými službami.](service-fabric-reliable-services-quick-start.md)

## <a name="deploy"></a>Nasazení
1. *Správce aplikace* přizpůsobí typ aplikace konkrétní aplikaci, která má být nasazena do clusteru Service Fabric, zadáním příslušných parametrů prvku **ApplicationType** v manifestu aplikace.
2. *Operátor* nahraje balíček aplikace do úložiště bitových kopií clusteru pomocí metody [ **CopyApplicationPackage** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) nebo [rutiny **Copy-ServiceFabricApplicationPackage** ](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Balíček aplikace obsahuje manifest aplikace a kolekci balíčků služeb. Service Fabric nasazuje aplikace z balíčku aplikace uloženého v úložišti bitových obrázků, což může být úložiště objektů blob Azure nebo systémová služba Service Fabric.
3. *Operátor* pak zřídí typ aplikace v cílovém clusteru z nahraného balíčku aplikace pomocí metody [ **ProvisionApplicationAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [rutiny **Register-ServiceFabricApplicationType** ](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype)nebo [ **Operace Zřizování rest aplikace** ](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
4. Po zřízení aplikace *operátor* spustí aplikaci s parametry zadanými *správcem aplikace* pomocí metody [ **CreateApplicationAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [rutiny **New-ServiceFabricApplication** ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication)nebo [ **operace Vytvořit aplikaci** REST](https://docs.microsoft.com/rest/api/servicefabric/create-an-application).
5. Po nasazení aplikace *operátor* používá [metodu **CreateServiceAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), [rutinu **New-ServiceFabricService** ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice)nebo operaci Create [ **Service** REST](https://docs.microsoft.com/rest/api/servicefabric/create-a-service) k vytvoření nových instancí služby pro aplikaci na základě dostupných typů služeb.
6. Aplikace je nyní spuštěna v clusteru Service Fabric.

Viz [Nasazení aplikace](service-fabric-deploy-remove-applications.md) pro příklady.

## <a name="test"></a>Test
1. Po nasazení do clusteru místního vývoje nebo testovacího clusteru vývojář *služby* spustí předdefinovaný scénář testu převzetí služeb při selhání pomocí tříd [**FailoverTestParameterParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters) a [**FailoverTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario) nebo [rutiny **Invoke-ServiceFabricFailoverTestScenario** ](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps). Scénář testu převzetí služeb při selhání spustí zadanou službu prostřednictvím důležitých přechodů a převzetí služeb při selhání, aby bylo zajištěno, že je stále k dispozici a funguje.
2. *Vývojář služby* pak spustí předdefinovaný scénář chaos test pomocí [**ChaosTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters) a [**ChaosTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario) třídy nebo [ **Invoke-ServiceFabricChaosTestScenario** rutina](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps). Scénář testu chaosu náhodně indukuje více uzlů, balíček kódu a chyb replik do clusteru.
3. *Vývojář služby* [testuje komunikaci mezi službami](service-fabric-testability-scenarios-service-communication.md) vytvářením testovacích scénářů, které přesouvají primární repliky po clusteru.

Další informace naleznete [v tématu Úvod do služby analýzy poruch.](service-fabric-testability-overview.md)

## <a name="upgrade"></a>Upgrade
1. *Vývojář služby* aktualizuje základní služby instance aplikace nebo opravuje chyby a poskytuje novou verzi manifestu služby.
2. *Vývojář aplikace* přepíše a parametrizuje nastavení konfigurace a nasazení konzistentních služeb a poskytuje novou verzi manifestu aplikace. Vývojář aplikace pak začlení nové verze manifestů služby do aplikace a poskytuje novou verzi typu aplikace v aktualizovaném balíčku aplikace.
3. *Správce aplikace* začlení novou verzi typu aplikace do cílové aplikace aktualizací příslušných parametrů.
4. *Operátor* nahraje aktualizovaný balíček aplikace do úložiště bitových kopií clusteru pomocí metody [ **CopyApplicationPackage** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) nebo [rutiny **Copy-ServiceFabricApplicationPackage** ](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Balíček aplikace obsahuje manifest aplikace a kolekci balíčků služeb.
5. *Operátor* zřídí novou verzi aplikace v cílovém clusteru pomocí [ **provisionApplicationAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **Register-ServiceFabricApplicationType** rutina](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype)nebo [ **zřídit operaci REST aplikace** ](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
6. *Operátor* inovuje cílovou aplikaci na novou verzi pomocí metody [ **UpgradeApplicationAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [rutiny **Start-ServiceFabricApplicationUpgrade** ](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade)nebo [ **operace Upgrade rest aplikace** ](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application).
7. *Operátor* zkontroluje průběh upgradu pomocí metody [ **GetApplicationUpgradeProgressAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [rutiny **Get-ServiceFabricApplicationUpgrade** ](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricapplicationupgrade)nebo operace Rest získání [ **upgradu aplikace** ](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. V případě potřeby *operátor* upraví a znovu použije parametry aktuálního upgradu aplikace pomocí metody [ **UpdateApplicationUpgradeAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [rutiny **Update-ServiceFabricApplicationUpgrade** ](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade)nebo [ **operace REST aktualizace upgradu aplikace** ](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade).
9. V případě potřeby *operátor* vrátí aktuální upgrade aplikace pomocí metody [ **RollbackApplicationUpgradeAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [rutiny **Start-ServiceFabricRollback** ](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback)nebo operace [ **REST upgradu aplikace vrácení zpět** ](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade).
10. Service Fabric upgraduje cílovou aplikaci spuštěnou v clusteru bez ztráty dostupnosti některé z jejích základních služeb.

Podívejte se na [příklady v kurzu pro upgrade aplikací.](service-fabric-application-upgrade-tutorial.md)

## <a name="maintain"></a>Údržba
1. Pro upgrady a opravy operačního systému service fabric rozhraní s infrastrukturou Azure zaručit dostupnost všech aplikací spuštěných v clusteru.
2. Pro upgrady a opravy platformy Service Fabric service fabric upgraduje sám bez ztráty dostupnosti některé z aplikací spuštěných v clusteru.
3. *Správce aplikace* schválí přidání nebo odebrání uzlů z clusteru po analýze dat využití historické kapacity a předpokládané budoucí poptávky.
4. Operátor *operator* přidá a odebere uzly určené *správcem aplikace*.
5. Když jsou přidány nové uzly nebo existující uzly jsou odebrány z clusteru, Service Fabric automaticky vyrovnává vyvažování spuštěných aplikací ve všech uzlech v clusteru k dosažení optimálního výkonu.

## <a name="remove"></a>Odebrat
1. *Operátor* může odstranit určitou instanci spuštěné služby v clusteru bez odebrání celé aplikace pomocí metody [ **DeleteServiceAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), [rutiny **Remove-ServiceFabricService** ](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice)nebo operace Odstranit [ **službu** REST](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service).  
2. *Operátor* může také odstranit instanci aplikace a všechny její služby pomocí [ **deleteApplicationAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **Remove-ServiceFabricApplication** rutina](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricapplication)nebo [ **odstranit aplikaci** REST operace](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application).
3. Jakmile jsou aplikace a služby zastaveny, *operátor* může zrušit zřízení typu aplikace pomocí metody [ **UnprovisionApplicationAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [rutiny **Unregister-ServiceFabricApplicationType** ](https://docs.microsoft.com/powershell/module/servicefabric/unregister-servicefabricapplicationtype)nebo [ **Unprovision operace REST aplikace** ](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application). Zrušení zřízení typu aplikace neodebere balíček aplikace z ImageStore. Balíček aplikace je nutné odebrat ručně.
4. *Operátor* odebere balíček aplikace z ImageStore pomocí [ **RemoveApplicationPackage** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) nebo [ **Remove-ServiceFabricApplicationPackage** rutina](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps).

Viz [Nasazení aplikace](service-fabric-deploy-remove-applications.md) pro příklady.

## <a name="next-steps"></a>Další kroky
Další informace o vývoji, testování a správě aplikací a služeb Service Fabric naleznete v tématu:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)
* [Upgrade aplikací](service-fabric-application-upgrade.md)
* [Přehled testovatelnosti](service-fabric-testability-overview.md)
