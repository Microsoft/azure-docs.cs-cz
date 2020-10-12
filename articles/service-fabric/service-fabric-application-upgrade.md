---
title: Upgrade aplikace Service Fabric
description: Tento článek obsahuje úvod k upgradu aplikace Service Fabric, včetně výběru režimů upgradu a provádění kontrol stavu.
ms.topic: conceptual
ms.date: 8/5/2020
ms.openlocfilehash: cb0c1c0049957244b94b59707b70e47dc53f6c9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88067507"
---
# <a name="service-fabric-application-upgrade"></a>Upgrade aplikace Service Fabric
Aplikace Azure Service Fabric je kolekce služeb. Během upgradu Service Fabric porovná nový [manifest aplikace](service-fabric-application-and-service-manifests.md) s předchozí verzí a určí, které služby v aplikaci vyžadují aktualizace. Service Fabric porovná čísla verzí v manifestech služby s čísly verzí v předchozí verzi. Pokud se služba nezměnila, tato služba se neupgraduje.

> [!NOTE]
> [ApplicationParameter](https://docs.microsoft.com/dotnet/api/system.fabric.description.applicationdescription.applicationparameters?view=azure-dotnet#System_Fabric_Description_ApplicationDescription_ApplicationParameters)s se nezachovají v rámci upgradu aplikace. Aby bylo možné zachovat aktuální parametry aplikace, uživatel by měl nejprve načíst parametry a předat je do volání rozhraní API pro upgrade, jak je znázorněno níže:
```powershell
$myApplication = Get-ServiceFabricApplication -ApplicationName fabric:/myApplication
$appParamCollection = $myApplication.ApplicationParameters

$applicationParameterMap = @{}
foreach ($pair in $appParamCollection)
{
    $applicationParameterMap.Add($pair.Name, $pair.Value);
}

Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/myApplication -ApplicationTypeVersion 2.0.0 -ApplicationParameter $applicationParameterMap -Monitored -FailureAction Rollback
```

## <a name="rolling-upgrades-overview"></a>Přehled postupné inovace
V rámci upgradu na postupné aplikaci se upgrade provádí ve fázích. V každé fázi se upgrade aplikuje na podmnožinu uzlů v clusteru, která se označuje jako aktualizační doména. V důsledku toho zůstává aplikace k dispozici v průběhu upgradu. Během upgradu může cluster obsahovat kombinaci starých a nových verzí.

Z tohoto důvodu musí být tyto dvě verze předávány a zpětně kompatibilní. Pokud nejsou kompatibilní, je správce aplikace zodpovědný za přípravu upgradu s více fázemi, aby bylo možné zachovat dostupnost. V rámci několika fází upgradu je prvním krokem upgrade na mezilehlou verzi aplikace, která je kompatibilní s předchozí verzí. Druhým krokem je upgrade konečné verze, která přerušuje kompatibilitu s předběžnou aktualizací verze, ale je kompatibilní s mezilehlou verzí.

Aktualizace domén je určena v manifestu clusteru při konfiguraci clusteru. Aktualizační domény neobdrží aktualizace v určitém pořadí. Aktualizační doména je logická jednotka nasazení aplikace. Aktualizace domén umožňují službám při upgradu zůstat při vysoké dostupnosti.

Nepostupné upgrady jsou možné v případě, že se upgrade použije na všechny uzly v clusteru, což je případ, kdy má aplikace jenom jednu aktualizační doménu. Tento přístup se nedoporučuje, protože služba přestane být dostupná a v době upgradu není dostupná. Kromě toho Azure neposkytuje žádné záruky, pokud je cluster nastavený jenom s jednou aktualizační doménou.

Po dokončení upgradu budou všechny služby a repliky (instance) zůstat ve stejné verzi i. e. Pokud je upgrade úspěšný, budou aktualizovány na novou verzi. Pokud se upgrade nepovede a vrátí se zpátky, vrátí se zpátky na starou verzi.

## <a name="health-checks-during-upgrades"></a>Kontroly stavu během upgradů
Pro upgrade musí být nastavené zásady stavu (nebo se můžou použít výchozí hodnoty). Upgrade je úspěšný, pokud jsou všechny aktualizační domény upgradovány v rámci zadaného časového limitu a pokud jsou všechny aktualizační domény považovány za v dobrém stavu.  V pořádku aktualizační doména znamená, že doména aktualizace úspěšně prošla všemi kontrolami stavu, které jsou zadány v zásadách stavu. Zásady stavu můžou například pověřit, že všechny služby v instanci aplikace musí být *v pořádku*, protože stav je definován Service Fabric.

Zásady stavu a kontroly během upgradu Service Fabric jsou služby a aplikace nezávislá. To znamená, že nejsou dokončeny žádné testy specifické pro danou službu.  Vaše služba může mít například požadavek propustnosti, ale Service Fabric nemá informace pro kontrolu propustnosti. Kontroly, které jsou prováděny, najdete v [článcích o stavu](service-fabric-health-introduction.md) . K kontrolám, ke kterým dojde během upgradu, patří testy, zda byl balíček aplikace správně zkopírován, zda byla instance spuštěna atd.

Stav aplikace je agregovanou podřízenou entitou aplikace. V krátkém Service Fabric vyhodnotí stav aplikace přes stav, který je v aplikaci uveden. Tento způsob vyhodnocuje také stav všech služeb pro aplikaci. Service Fabric dále vyhodnocuje stav aplikačních služeb agregací stavu svých podřízených objektů, jako je například replika služby. Jakmile je zásada stavu aplikace splněná, může upgrade pokračovat. Pokud dojde k porušení zásady stavu, upgrade aplikace se nezdařil.

## <a name="upgrade-modes"></a>Režimy upgradu
Režim, který doporučujeme pro upgrade aplikace, je monitorovaný režim, což je běžně používaný režim. Monitorovaný režim provede upgrade v jedné aktualizační doméně a pokud všechny kontroly stavu proběhnou (podle zadané zásady), se automaticky přesune k další aktualizační doméně.  Pokud dojde k selhání kontrol stavu nebo vypršení časových limitů, upgrade se vrátí zpátky pro aktualizační doménu, nebo se režim změní na nemonitorované ruční. Upgrade můžete nakonfigurovat tak, aby pro neúspěšné upgrady zvolil jeden z těchto dvou režimů. 

Nemonitorovaný ruční režim potřebuje ruční zásah po každém upgradu v aktualizační doméně pro zahájení upgradu v další aktualizační doméně. Neprovádí se žádné Service Fabric kontroly stavu. Správce provede kontrolu stavu nebo stavu před zahájením upgradu v další aktualizační doméně.

## <a name="upgrade-default-services"></a>Upgrade výchozích služeb
Některé výchozí parametry služby definované v [manifestu aplikace](service-fabric-application-and-service-manifests.md) je také možné upgradovat jako součást upgradu aplikace. V rámci upgradu lze změnit pouze parametry služby, které podporují změnu prostřednictvím [Update-ServiceFabricService](/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) . Chování při změně výchozích služeb během upgradu aplikace je následující:

1. Vytvoří se výchozí služby v manifestu nové aplikace, které v clusteru ještě neexistují.
2. Výchozí služby, které existují v předchozích i nových manifestech aplikace, jsou aktualizovány. Parametry výchozí služby v manifestu nové aplikace přepíšou parametry existující služby. Upgrade aplikace bude automaticky vrácen, pokud se nezdaří aktualizace výchozí služby.
3. Výchozí služby, které neexistují v manifestu nové aplikace, se odstraní, pokud existují v clusteru. **Všimněte si, že odstranění výchozí služby bude mít za následek odstranění všech stavů této služby a nebude možné je vrátit zpět.**

Když se upgrade aplikace vrátí zpátky, výchozí parametry služby se vrátí zpátky na původní hodnoty před zahájením upgradu, ale odstraněné služby se nedají znovu vytvořit se starým stavem.

> [!TIP]
> Nastavení konfigurace clusteru [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) musí mít *hodnotu true* , aby bylo možné povolit pravidla 2) a 3) výše (výchozí aktualizace služby a odstranění). Tato funkce je podporovaná od verze Service Fabric 5,5.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Upgrade více aplikací s koncovými body HTTPS
Při použití HTTP**S**musíte být opatrní, nepoužívejte **stejný port** pro různé instance stejné aplikace. Důvodem je to, že Service Fabric nebude moci upgradovat certifikát pro jednu z instancí aplikace. Například pokud aplikace 1 nebo aplikace 2 bude chtít upgradovat svůj certifikát 1 na CERT 2. Když dojde k upgradu, Service Fabric pravděpodobně vyčistil registraci certifikátu 1 s http.sys, i když ji jiná aplikace stále používá. Chcete-li tomu zabránit, Service Fabric zjistí, že na portu s certifikátem již je registrována jiná instance aplikace (z důvodu http.sys) a operace se nezdařila.

Proto Service Fabric nepodporuje upgrade dvou různých služeb pomocí **stejného portu** v různých instancích aplikace. Jinými slovy, nemůžete použít stejný certifikát pro různé služby na stejném portu. Pokud potřebujete mít sdílený certifikát na stejném portu, musíte zajistit, aby se služby nastavily na různých počítačích s omezením umístění. Nebo zvažte použití Service Fabric dynamické porty, pokud je to možné pro každou službu v každé instanci aplikace. 

Pokud se zobrazí zpráva o selhání upgradu s protokolem HTTPS, zobrazí se upozornění, že rozhraní API systému Windows HTTP server nepodporuje více certifikátů pro aplikace, které sdílejí port. "

## <a name="application-upgrade-flowchart"></a>Vývojový diagram upgradu aplikace
Vývojový diagram následující tento odstavec vám pomůže pochopit proces upgradu aplikace Service Fabric. Tento tok zejména popisuje, jak časové limity, včetně *HealthCheckStableDuration*, *HealthCheckRetryTimeout*a *UpgradeHealthCheckInterval*, pomůžou řídit, kdy se upgrade v jedné aktualizační doméně považuje za úspěšný nebo neúspěšný.

![Proces upgradu aplikace Service Fabric][image]

## <a name="next-steps"></a>Další kroky
[Upgrade aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgradem aplikace pomocí sady Visual Studio.

[Upgrade aplikace pomocí PowerShellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgradem aplikace pomocí PowerShellu.

Pomocí [parametrů upgradu](service-fabric-application-upgrade-parameters.md)lze řídit, jak se vaše aplikace upgradují.

Vyjistěte, aby byly upgrady aplikací kompatibilní, pomocí učení, jak používat [serializaci dat](service-fabric-application-upgrade-data-serialization.md).

Naučte se používat pokročilé funkce při upgradu vaší aplikace, které odkazují na [Pokročilá témata](service-fabric-application-upgrade-advanced.md).

Pomocí postupu v části [řešení potíží s upgrady aplikací](service-fabric-application-upgrade-troubleshooting.md)můžete opravit běžné problémy s upgrady aplikací.

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
