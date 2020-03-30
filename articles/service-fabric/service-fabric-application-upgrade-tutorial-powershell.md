---
title: Upgrade aplikace Service Fabric pomocí PowerShellu
description: Tento článek vás provede prostředím nasazení aplikace Service Fabric, změnou kódu a zavedením upgradu pomocí prostředí PowerShell.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: b113b5a1042518e3b0d86e53796c5fe49afed418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426786"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Upgrade aplikace Service Fabric pomocí prostředí PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Nejčastěji používaným a doporučeným přístupem k upgradu je monitorovaná postupná inovace.  Azure Service Fabric monitoruje stav upgradované aplikace na základě sady zásad stavu. Po upgradu aktualizační domény (UD) Service Fabric vyhodnotí stav aplikace a buď přejde do další domény aktualizace nebo selže upgrade v závislosti na zásadách stavu.

Monitorovaný upgrade aplikace lze provést pomocí spravovaných nebo nativních api, PowerShellu, Azure CLI, Java nebo REST. Pokyny k provedení upgradu pomocí sady Visual Studio naleznete v [tématu Upgrade aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md).

Pomocí service fabric monitorované postupné inovace, správce aplikace můžete nakonfigurovat zásady vyhodnocení stavu, které Service Fabric používá k určení, zda je aplikace v pořádku. Kromě toho může správce nakonfigurovat akci, která má být provedena v případě selhání vyhodnocení stavu (například provedení automatického vrácení zpět.) Tato část vás provede monitorovaným upgradem pro jednu z ukázek sady SDK, která používá prostředí PowerShell. 

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Krok 1: Sestavení a nasazení ukázky vizuálních objektů
Vytvořte a publikujte aplikaci kliknutím pravým tlačítkem myši na projekt aplikace **VisualObjectsApplication** a výběrem příkazu **Publikovat.**  Další informace naleznete v [tématu Service Fabric application upgrade tutorial](service-fabric-application-upgrade-tutorial.md).  Případně můžete použít PowerShell k nasazení aplikace.

> [!NOTE]
> Předtím, než lze v prostředí PowerShell použít některý z příkazů Service Fabric, `Connect-ServiceFabricCluster` musíte se nejprve připojit ke clusteru pomocí rutiny. Podobně se předpokládá, že cluster již byl nastaven v místním počítači. Podívejte se na článek o [nastavení vývojového prostředí Service Fabric](service-fabric-get-started.md).
> 
> 

Po sestavení projektu v sadě Visual Studio můžete pomocí příkazu PowerShell [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) zkopírovat balíček aplikace do úložiště obrázků. Pokud chcete ověřit balíček aplikace místně, použijte [rutinu Test-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/test-servicefabricapplicationpackage) Dalším krokem je registrace aplikace do runtime Service Fabric pomocí rutiny [Register-ServiceFabricApplicationType.](/powershell/module/servicefabric/register-servicefabricapplicationtype) Následující krok je spustit instanci aplikace pomocí rutiny [New-ServiceFabricApplication.](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)  Tyto tři kroky jsou podobné použití položky nabídky **Nasazení** v sadě Visual Studio.  Po dokončení zřizování byste měli vyčistit zkopírovaný balíček aplikace z úložiště obrázků, abyste snížili spotřebované prostředky.  Pokud typ aplikace již není vyžadován, by měl být zrušen ze stejného důvodu. Další informace [najdete v tématu Nasazení a odebrání aplikací pomocí PowerShellu.](service-fabric-application-upgrade-tutorial-powershell.md)

Nyní můžete pomocí [aplikace Service Fabric Explorer zobrazit cluster a aplikaci](service-fabric-visualizing-your-cluster.md). Aplikace má webovou službu, na kterou lze v [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) aplikaci Internet Explorer přejít zadáním do adresního řádku.  Měli byste vidět některé plovoucí vizuální objekty pohybující se na obrazovce.  Kromě toho můžete použít [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) ke kontrole stavu aplikace.

## <a name="step-2-update-the-visual-objects-sample"></a>Krok 2: Aktualizace ukázky vizuálních objektů
Můžete si všimnout, že s verzí, která byla nasazena v kroku 1, vizuální objekty neotáčejí. Pojďme upgradovat tuto aplikaci na ten, kde vizuální objekty také otáčet.

Vyberte projekt VisualObjects.ActorService v rámci řešení VisualObjects a otevřete soubor StatefulVisualObjectActor.cs. V rámci tohoto souboru `MoveObject`přejděte `this.State.Move()`na metodu , komentář a odkomentujte `this.State.Move(true)`. Tato změna otočí objekty po upgradu služby.

Musíme také aktualizovat soubor *ServiceManifest.xml* (v části PackageRoot) projektu **VisualObjects.ActorService**. Aktualizujte *CodePackage* a verzi služby na 2.0 a odpovídající řádky v souboru *ServiceManifest.xml.*
Možnost Upravit soubory *manifestu* sady Visual Studio můžete použít po kliknutí pravým tlačítkem myši na řešení a provedete změny souboru manifestu.

Po provedení změn by měl manifest vypadat takto (zvýrazněné části zobrazují změny):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Nyní *ApplicationManifest.xml* soubor (nalezený v rámci projektu **VisualObjects** v rámci řešení **VisualObjects)** je aktualizován na verzi 2.0 projektu **VisualObjects.ActorService.** Kromě toho je verze aplikace aktualizována na 2.0.0.0 od 1.0.0.0. *Soubor ApplicationManifest.xml* by měl vypadat jako následující úryvek:

```xml
<ApplicationManifestxmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Nyní sestavte projekt výběrem pouze **projektu ActorService** a potom klikněte pravým tlačítkem myši a výběrem **možnosti sestavení** v sadě Visual Studio. Pokud vyberete **znovu sestavit vše**, měli byste aktualizovat verze pro všechny projekty, protože kód by se změnil. Dále zabalíme aktualizovanou aplikaci kliknutím pravým tlačítkem myši na ***VisualObjectsApplication***, výběrem nabídky Service Fabric a výběrem **možnosti Balíček**. Tato akce vytvoří balíček aplikace, který lze nasadit.  Aktualizovaná aplikace je připravena k nasazení.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Krok 3: Rozhodněte o zásadách stavu a parametrech upgradu
Seznamte se s [parametry upgradu aplikace](service-fabric-application-upgrade-parameters.md) a [proces upgradu](service-fabric-application-upgrade.md) získat dobré znalosti o různých parametrech upgradu, časové osy a kritérium stavu použít. Pro tento návod je kritérium vyhodnocení stavu služby nastaveno na výchozí (a doporučené) hodnoty, což znamená, že všechny služby a instance by měly být po upgradu *v pořádku.*  

Však pojďme zvýšit *HealthCheckStableDuration* na 180 sekund (tak, aby služby jsou v pořádku po dobu nejméně 120 sekund před upgrade pokračuje do další domény aktualizace).  Pojďme také nastavit *UpgradeDomainTimeout* být 1200 sekund a *UpgradeTimeout* být 3000 sekund.

Nakonec také nastavíme *upgradeFailureAction* na vrácení zpět. Tato možnost vyžaduje Service Fabric vrátit zpět aplikace na předchozí verzi, pokud dojde k problémům během upgradu. Proto při spuštění upgradu (v kroku 4) jsou určeny následující parametry:

Akce failureAction = vrácení zpět

HealthCheckStableDurationSec = 180

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>Krok 4: Příprava aplikace pro upgrade
Nyní je aplikace sestavena a připravena k upgradu. Pokud otevřete okno Prostředí PowerShell jako správce a zadejte [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps), měl by vás vědět, že se jedná o typ aplikace 1.0.0.0 **VisualObjects,** který byl nasazen.  

Balíček aplikace je uložen pod následující relativní cestou, kde jste nekomprimovali sadu Service Fabric SDK - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Měli byste najít složku "Balíček" v tomto adresáři, kde je uložen balíček aplikace. Zkontrolujte časová razítka a ujistěte se, že se jedná o nejnovější sestavení (možná budete muset odpovídajícím způsobem upravit cesty).

Nyní zkopírujeme aktualizovaný balíček aplikace do Service Fabric ImageStore (kde balíčky aplikací jsou uloženy service fabric). Parametr *ApplicationPackagePathInImageStore* informuje Service Fabric, kde může najít balíček aplikace. Jsme dali aktualizovanou aplikaci v "VisualObjects\_V2" s následujícím příkazem (možná budete muset změnit cesty znovu vhodně).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

Dalším krokem je registrace této aplikace pomocí service fabric, kterou lze provést pomocí příkazu [Register-ServiceFabricApplicationType:](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Pokud předchozí příkaz neproběhne úspěšně, je pravděpodobné, že budete potřebovat znovu sestavit všechny služby. Jak je uvedeno v kroku 2, bude pravděpodobně muset aktualizovat verzi webservice také.

Doporučujeme odebrat balíček aplikace po úspěšné registraci aplikace.  Odstraněním balíčků aplikací z úložiště bitové kopie uvolníte systémové prostředky.  Udržování nepoužívaných balíčků aplikací spotřebovává diskové úložiště a vede k problémům s výkonem aplikace.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>Krok 5: Spuštění upgradu aplikace
Nyní jsme všichni nastaveni na spuštění upgradu aplikace pomocí příkazu [Start-ServiceFabricApplicationUpgrade:](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps)

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Název aplikace je stejný jako v souboru *ApplicationManifest.xml.* Service Fabric používá tento název k identifikaci aplikace, která je stále upgradována. Pokud nastavíte časové úseky na příliš krátký, může dojít k selhání zprávy, která uvádí problém. Podívejte se do části řešení potíží nebo zvyšte časové úseky.

Nyní, jak probíhá upgrade aplikace, můžete sledovat pomocí Aplikace Service Fabric Explorer nebo pomocí příkazu [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) PowerShell: 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

Během několika minut by měl stav, který jste získali pomocí předchozího příkazu Prostředí PowerShell, uvést, že všechny aktualizační domény byly upgradovány (dokončeny). A měli byste zjistit, že vizuální objekty v okně prohlížeče se začaly otáčet!

Můžete zkusit upgrade z verze 2 na verzi 3 nebo z verze 2 na verzi 1 jako cvičení. Přechod z verze 2 na verzi 1 je také považován za upgrade. Hrajte si s časovými výčasy a zásadami stavu, abyste se s nimi seznámili. Při nasazování do clusteru Azure je třeba správně nastavit parametry. Je dobré nastavit time-outy konzervativně.

## <a name="next-steps"></a>Další kroky
[Inovace aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgradem aplikace pomocí sady Visual Studio.

Pomocí [parametrů upgradu](service-fabric-application-upgrade-parameters.md)můžete řídit, jak bude aplikace upgradována.

Pomocí aplikace upgrady kompatibilní tím, že se naučíte používat [serializaci dat](service-fabric-application-upgrade-data-serialization.md).

Naučte se používat pokročilé funkce při inovaci aplikace odkazem na [rozšířená témata](service-fabric-application-upgrade-advanced.md).

Běžné problémy při upgradu aplikací opravíte odkazem na kroky [při řešení potíží s inovacemi aplikací](service-fabric-application-upgrade-troubleshooting.md).

