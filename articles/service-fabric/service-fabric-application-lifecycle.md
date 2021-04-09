---
title: Životní cyklus aplikace v Service Fabric
description: Popisuje vývoj, nasazení, testování, upgrade, údržbu a odebírání aplikací Service Fabric.
ms.topic: conceptual
ms.date: 1/19/2018
ms.openlocfilehash: ae0c79cdaafc8fc016d463a01046f0a02121330a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785731"
---
# <a name="service-fabric-application-lifecycle"></a>Životní cyklus aplikace Service Fabric
Stejně jako u jiných platforem aplikace v Azure Service Fabric obvykle prochází následujícími fázemi: návrh, vývoj, testování, nasazení, upgrade, údržba a odebrání. Service Fabric poskytuje prvotřídní podporu pro plný životní cyklus aplikací cloudových aplikací, od vývoje prostřednictvím nasazení, každodenní správy a údržby až po případné vyřazení z provozu. Model služby umožňuje, aby se v životním cyklu aplikace nezávisle účastnilo několik různých rolí. Tento článek poskytuje přehled rozhraní API a způsob jejich používání různými rolemi v průběhu fáze životního cyklu Service Fabric aplikací.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="service-model-roles"></a>Role modelu služby
Role modelu služby jsou:

* **Vývojář služeb**: vyvíjí modulární a obecné služby, které se dají změnit na účel a použít ve více aplikacích stejného typu nebo různých typů. Například služba fronty se dá použít k vytvoření aplikace pro lístky (helpdesk) nebo aplikace elektronického obchodování (nákupní košík).
* **Vývojář aplikací**: vytváří aplikace integrací kolekce služeb, které splňují určité konkrétní požadavky nebo scénáře. Web elektronického obchodování může například integrovat "bezstavovou Front-End službu JSON," "stavovou službu pro aukci" a "stavovou službu fronty" k sestavení řešení pro aukci.
* **Správce aplikací**: provede rozhodnutí o konfiguraci aplikace (naplňování parametrů šablony konfigurace), nasazení (mapování na dostupné prostředky) a kvalitě služby. Například správce aplikace rozhoduje o národním prostředí (například anglicky pro USA nebo japonštinu pro Japonsko) aplikace. Jiná nasazená aplikace může mít různá nastavení.
* **Operátor**: nasadí aplikace na základě konfigurace aplikace a požadavků určených správcem aplikace. Například operátor zřídí a nasadí aplikaci a zajistí, že je spuštěná v Azure. Operátoři monitorují informace o stavu aplikace a výkonu a udržují fyzickou infrastrukturu podle potřeby.

## <a name="develop"></a>Vývoj
1. *Vývojář služby* vyvíjí různé typy služeb pomocí [Reliable Actors](service-fabric-reliable-actors-introduction.md) nebo [Reliable Services](service-fabric-reliable-services-introduction.md) programovacího modelu.
2. *Vývojář služby* deklarativně popisuje rozvinuté typy služeb v souboru manifestu služby, který se skládá z jednoho nebo více balíčků kódu, konfigurace a datových sad.
3. *Vývojář aplikace* potom vytvoří aplikaci s použitím různých typů služeb.
4. *Vývojář aplikace* deklarativně popisuje typ aplikace v manifestu aplikace tím, že odkazuje na manifesty služby prvků a patřičně Přepisuje a Parametrizace různá nastavení konfigurace a nasazení základních služeb.

Příklady najdete v tématu Začínáme [se Reliable Actors](service-fabric-reliable-actors-get-started.md) a [Začínáme s Reliable Services](service-fabric-reliable-services-quick-start.md) .

## <a name="deploy"></a>Nasadit
1. *Správce aplikace* vysadí typ aplikace na konkrétní aplikaci, která se má nasadit do clusteru Service Fabric, zadáním příslušných parametrů elementu **typu ApplicationType** v manifestu aplikace.
2. *Operátor* nahraje balíček aplikace do úložiště imagí clusteru pomocí [metody **CopyApplicationPackage**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) nebo [rutiny **copy-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/copy-servicefabricapplicationpackage). Balíček aplikace obsahuje manifest aplikace a kolekci balíčků služeb. Service Fabric nasadí aplikace z balíčku aplikace uloženého v úložišti imagí, což může být úložiště objektů BLOB v Azure nebo služba Service Fabric System.
3. *Operátor* pak zřídí typ aplikace v cílovém clusteru z nahraného balíčku aplikace pomocí [metody **ProvisionApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [rutiny **Register-ServiceFabricApplicationType**](/powershell/module/servicefabric/register-servicefabricapplicationtype)nebo [ **zřízení** operace REST aplikace](/rest/api/servicefabric/provision-an-application).
4. Po zřízení aplikace *operátor* spustí aplikaci s parametry poskytnutými *správcem aplikace* pomocí [metody **CreateApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [rutiny **New-ServiceFabricApplication**](/powershell/module/servicefabric/new-servicefabricapplication)nebo [operace **Vytvoření REST aplikace**](/rest/api/servicefabric/create-an-application).
5. Po nasazení aplikace *operátor* používá [metodu **CreateServiceAsync**](/dotnet/api/system.fabric.fabricclient.servicemanagementclient), [rutinu **New-ServiceFabricService**](/powershell/module/servicefabric/new-servicefabricservice), nebo [operaci REST **služby Create Service**](/rest/api/servicefabric/create-a-service) k vytváření nových instancí služby pro aplikaci na základě dostupných typů služeb.
6. Aplikace je nyní spuštěna v clusteru Service Fabric.

Příklady najdete v tématu [nasazení aplikace](service-fabric-deploy-remove-applications.md) .

## <a name="test"></a>Test
1. Po nasazení do místního vývojového clusteru nebo testovacího clusteru spustí *vývojář služby* integrovaný scénář testování převzetí služeb při selhání pomocí tříd [**FailoverTestScenarioParameters**](/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters) a [**FailoverTestScenario**](/dotnet/api/system.fabric.testability.scenario.failovertestscenario) nebo [rutiny **Invoke-ServiceFabricFailoverTestScenario**](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario). Scénář testu převzetí služeb při selhání spouští zadanou službu prostřednictvím důležitých přechodů a převzetí služeb při selhání, aby bylo zajištěno, že bude stále k dispozici a
2. *Vývojář služby* potom spustí integrovaný scénář testu chaos pomocí tříd [**ChaosTestScenarioParameters**](/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters) a [**ChaosTestScenario**](/dotnet/api/system.fabric.testability.scenario.chaostestscenario) nebo [rutiny **Invoke-ServiceFabricChaosTestScenario**](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario). Scénář testu chaos náhodně vyzkouší více než do clusteru více uzlů, balíčků kódu a chyb replik.
3. *Vývojář služby* [testuje komunikaci](service-fabric-testability-scenarios-service-communication.md) mezi službami a vytvářením testovacích scénářů, které přesouvají primární repliky kolem clusteru.

Další informace najdete v [úvodu ke službě analýzy chyb](service-fabric-testability-overview.md) .

## <a name="upgrade"></a>Upgrade
1. *Vývojář služby* aktualizuje prvky služeb instance aplikace nebo opravuje chyby a poskytuje novou verzi manifestu služby.
2. *Vývojář aplikace* Přepisuje a parameterizes nastavení konfigurace a nasazení konzistentních služeb a poskytuje novou verzi manifestu aplikace. Vývojář aplikace potom v rámci aplikace zahrnuje nové verze manifestů služby a v aktualizovaném balíčku aplikace poskytuje novou verzi typu aplikace.
3. *Správce aplikace* zahrnuje novou verzi typu aplikace do cílové aplikace tím, že aktualizuje příslušné parametry.
4. *Operátor* nahraje aktualizovaný balíček aplikace do úložiště imagí clusteru pomocí [metody **CopyApplicationPackage**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) nebo [rutiny **copy-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/copy-servicefabricapplicationpackage). Balíček aplikace obsahuje manifest aplikace a kolekci balíčků služeb.
5. *Operátor* zřídí novou verzi aplikace v cílovém clusteru pomocí [metody **ProvisionApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [rutiny **Register-ServiceFabricApplicationType**](/powershell/module/servicefabric/register-servicefabricapplicationtype)nebo [ **zřízení** operace REST aplikace](/rest/api/servicefabric/provision-an-application).
6. *Operátor* upgraduje cílovou aplikaci na novou verzi pomocí [metody **UpgradeApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [rutiny **Start-ServiceFabricApplicationUpgrade**](/powershell/module/servicefabric/start-servicefabricapplicationupgrade)nebo [ **upgradu** operace REST aplikace](/rest/api/servicefabric/upgrade-an-application).
7. *Operátor* kontroluje průběh upgradu pomocí [metody **GetApplicationUpgradeProgressAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [rutiny **Get-ServiceFabricApplicationUpgrade**](/powershell/module/servicefabric/get-servicefabricapplicationupgrade)nebo [operace REST s **průběhem upgradu aplikace**](/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. V případě potřeby *operátor* mění a znovu použije parametry aktuálního upgradu aplikace pomocí [metody **UpdateApplicationUpgradeAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [rutiny **Update-ServiceFabricApplicationUpgrade**](/powershell/module/servicefabric/update-servicefabricapplicationupgrade)nebo [operace REST **upgradu aplikace Update**](/rest/api/servicefabric/update-an-application-upgrade).
9. V případě potřeby vrátí *operátor* zpátky aktuální upgrade aplikace pomocí [metody **RollbackApplicationUpgradeAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [rutiny **Start-ServiceFabricApplicationRollback**](/powershell/module/servicefabric/start-servicefabricapplicationrollback)nebo [operace REST **Upgrade aplikace zpět**](/rest/api/servicefabric/rollback-an-application-upgrade).
10. Service Fabric upgraduje cílovou aplikaci běžící v clusteru, aniž by došlo ke ztrátě dostupnosti jakékoli ze svých služeb.

Příklady najdete v [kurzu upgradu aplikace](service-fabric-application-upgrade-tutorial.md) .

## <a name="maintain"></a>Údržba
1. Pro upgrady a opravy operačního systému Service Fabric rozhraní s infrastrukturou Azure, aby se zajistila dostupnost všech aplikací spuštěných v clusteru.
2. Pro upgrady a opravy na Service Fabric platformu Service Fabric sám sebe upgradovat, aniž by došlo ke ztrátě dostupnosti jakékoli aplikace spuštěné v clusteru.
3. *Správce aplikace* schválí přidávání nebo odebírání uzlů z clusteru po analýze historických dat o využití kapacity a předpokládané budoucí poptávky.
4. *Operátor* přidá a odebere uzly určené *správcem aplikace*.
5. Když se do clusteru přidají nové uzly nebo se stávající uzly odeberou z clusteru, Service Fabric automaticky vyrovnávat spuštěné aplikace napříč všemi uzly v clusteru, aby se dosáhlo optimálního výkonu.

## <a name="remove"></a>Odebrat
1. *Operátor* může odstranit konkrétní instanci spuštěné služby v clusteru bez odebrání celé aplikace pomocí [metody **DeleteServiceAsync**](/dotnet/api/system.fabric.fabricclient.servicemanagementclient), [rutiny **Remove-ServiceFabricService**](/powershell/module/servicefabric/remove-servicefabricservice)nebo [operace REST **služby Delete**](/rest/api/servicefabric/delete-a-service).  
2. *Operátor* může také odstranit instanci aplikace a všechny její služby pomocí [metody **DeleteApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [rutiny **Remove-ServiceFabricApplication**](/powershell/module/servicefabric/remove-servicefabricapplication)nebo [operace **Delete Application** REST](/rest/api/servicefabric/delete-an-application).
3. Po zastavení aplikace a služeb může *operátor* zrušit zřízení typu aplikace pomocí [metody **UnprovisionApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [rutiny **Unregister-ServiceFabricApplicationType**](/powershell/module/servicefabric/unregister-servicefabricapplicationtype)nebo [ **zrušení zřízení** operace REST aplikace](/rest/api/servicefabric/unprovision-an-application). Zrušení zřízení typu aplikace neodebere balíček aplikace z úložiště bitových kopií. Balíček aplikace je nutné odebrat ručně.
4. *Operátor* odebere balíček aplikace z úložiště bitových kopií pomocí [metody **RemoveApplicationPackage**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) nebo [rutiny **Remove-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/remove-servicefabricapplicationpackage).

Příklady najdete v tématu [nasazení aplikace](service-fabric-deploy-remove-applications.md) .

## <a name="next-steps"></a>Další kroky
Další informace o vývoji, testování a správě Service Fabric aplikací a služeb najdete v těchto tématech:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)
* [Upgrade aplikací](service-fabric-application-upgrade.md)
* [Přehled možností testování](service-fabric-testability-overview.md)
