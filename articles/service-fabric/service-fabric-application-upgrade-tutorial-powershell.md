---
title: Upgrade aplikace Service Fabric pomocí prostředí PowerShell | Microsoft Docs
description: Tento článek vás provede možností nasazení aplikace Service Fabric, změna kódu a zavádění upgrade pomocí prostředí PowerShell.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: 9bc75748-96b0-49ca-8d8a-41fe08398f25
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 44f4dc3a9c876e383a6e4df8ef5f467f2b93eaa9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Upgrade aplikace Service Fabric pomocí prostředí PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Nejčastěji používané a doporučený postup upgradu je monitorovaných postupného upgradu.  Azure Service Fabric monitoruje stav aplikace upgraduje na základě sady zásad stavu. Po upgradu domény služby aktualizace (UD), Service Fabric vyhodnocuje stav aplikace a buď pokračuje do domény další aktualizace nebo upgradu v závislosti na zásadách stavu se nezdaří.

Monitorované aplikaci upgrade lze provést pomocí spravovaným nebo nativním rozhraní API, Powershellu nebo REST. Pokyny k provedení upgradu pomocí sady Visual Studio, najdete v části [upgrade vaší aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md).

Pomocí Service Fabric monitorovat postupné upgrady Správce aplikací můžete nakonfigurovat zásad vyhodnocení stavu, který Service Fabric používá k určení, zda je aplikace v pořádku. Kromě toho může správce nakonfigurovat akce, které mají být provedeny, když vyhodnocení stavu selže (například provádění automatického vrácení zpět.) Tato část vás provede monitorovaných upgrade pro jednu z ukázky sady SDK, které používá prostředí PowerShell. Následující Microsoft Virtual Academy video vás také provede upgrade aplikace: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=OrHJH66yC_6406218965">
<img src="./media/service-fabric-application-upgrade-tutorial-powershell/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Krok 1: Vytvoření a nasazení ukázkové vizuální objekty
Vytvoření a publikování aplikace kliknutím pravým tlačítkem na projekt aplikace **VisualObjectsApplication,** a výběrem **publikovat** příkaz.  Další informace najdete v tématu [kurz upgradu aplikace Service Fabric](service-fabric-application-upgrade-tutorial.md).  Alternativně můžete použít PowerShell k nasazení aplikace.

> [!NOTE]
> Před všechny příkazy Service Fabric lze v prostředí PowerShell, musíte nejdřív připojit ke clusteru pomocí `Connect-ServiceFabricCluster` rutiny. Podobně se předpokládá, že clusteru je již nastaven na místním počítači. Najdete v článku na [nastavení vývojového prostředí Service Fabric](service-fabric-get-started.md).
> 
> 

Po vytvoření projektu v sadě Visual Studio, můžete použít příkaz prostředí PowerShell [kopie ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) zkopírovat balíček aplikace do úložišti ImageStore. Pokud chcete ověřit balíček aplikace místně, použijte [Test ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) rutiny. Dalším krokem je registrace aplikace pomocí modulu runtime Service Fabric [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) rutiny. Následující krok je spuštění instance aplikace pomocí [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) rutiny.  Tyto tři kroky jsou podobná pomocí **nasadit** položky nabídky v sadě Visual Studio.  Po dokončení zřizování musí vyčistit balíček zkopírovaný aplikace z úložiště image store kvůli snížení prostředky spotřebované.  Pokud je typ aplikace se už nevyžaduje, je nutné zrušit registraci ze stejného důvodu. V tématu [nasazení a odeberte aplikací pomocí prostředí PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) Další informace.

Teď můžete použít [Service Fabric Explorer zobrazíte cluster a aplikace](service-fabric-visualizing-your-cluster.md). Aplikace má webová služba, která lze procházet k v aplikaci Internet Explorer tak, že zadáte [ http://localhost:8081/visualobjects ](http://localhost:8081/visualobjects) na panelu Adresa.  Měli byste vidět některé plovoucí vizuální objekty manipulaci se na obrazovce.  Kromě toho můžete použít [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) a zkontrolujte stav aplikace.

## <a name="step-2-update-the-visual-objects-sample"></a>Krok 2: Aktualizace Ukázka vizuální objekty
Můžete si všimnout, že s verzí, který byl nasazen v kroku 1, není otočit vizuální objekty. Umožňuje upgradovat tuto aplikaci do jednoho kde také otočit vizuální objekty.

Vyberte projekt VisualObjects.ActorService v rámci řešení VisualObjects a otevřete soubor StatefulVisualObjectActor.cs. V rámci tohoto souboru, přejděte k metodě `MoveObject`, komentář `this.State.Move()`a zrušte komentář u `this.State.Move(true)`. Tato změna otočí objekty po upgradu služby.

Také je potřeba aktualizovat *ServiceManifest.xml* projektu v souboru (pod PackageRoot) **VisualObjects.ActorService**. Aktualizace *CodePackage* a verze 2.0 a odpovídající řádky v *ServiceManifest.xml* souboru.
Visual Studio můžete použít *upravit soubory manifestu* možnost po kliknutí pravým tlačítkem na řešení provést změny souboru manifestu.

Po provedení změn v manifestu by měl vypadat takto (zvýrazněné části zobrazit změny):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Nyní *ApplicationManifest.xml* souboru (v části **VisualObjects** projektu v části **VisualObjects** řešení) se aktualizuje na verzi 2.0 **VisualObjects.ActorService** projektu. Kromě toho verze aplikace se aktualizuje 2.0.0.0 z 1.0.0.0. *ApplicationManifest.xml* by měl vypadat jako následující fragment kódu:

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Nyní, sestavte projekt tak, že vyberete jenom na **ActorService** projekt a potom kliknete pravým tlačítkem a vyberete **sestavení** možnost v sadě Visual Studio. Pokud vyberete **znovu vytvořit všechny**, verze pro všechny projekty, by měl aktualizovat, protože kód by změnily. Dále umožňuje kliknutím pravým tlačítkem na balíček aktualizovanou aplikaci ***VisualObjectsApplication***, vyberte v nabídce Service Fabric a zvolením **balíček**. Tato akce vytvoří balíček aplikace, které se dá nasadit.  Aktualizovaná aplikace je připravená k nasazení.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Krok 3: Rozhodněte o zásady stavu a upgradujte parametry
Seznamte se s [parametry upgradu aplikace](service-fabric-application-upgrade-parameters.md) a [procesu upgradu](service-fabric-application-upgrade.md) získat dostatečné povědomí o různé parametry upgradu, vypršení časových limitů a kritérium stavu použít. V tomto návodu je kritéria hodnocení stavu služby nastavenou na výchozí (a doporučené) hodnoty, které znamená, že by měly být všechny služby a instance *pořádku* po upgradu.  

Však umožňuje zvýšit *HealthCheckStableDuration* na 60 sekund (tak, aby služby jsou v pořádku po dobu 20 sekund před upgradem na další aktualizaci domény).  Umožňuje také nastavit *UpgradeDomainTimeout* být 1200 sekund a *UpgradeTimeout* být 3000 sekund.

Nakonec můžeme také nastavit *UpgradeFailureAction* vrátit zpět. Tato možnost vyžaduje Service Fabric na vrátit zpět na předchozí verzi aplikace, pokud zjistí jakékoli problémy při upgradu. Proto při spouštění upgradu (v kroku 4), jsou zadány následující parametry:

FailureAction = vrácení zpět

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>Krok 4: Příprava aplikací pro upgrade
Aplikace je teď vytvořený a připravené k upgradu. Pokud jste otevře okno prostředí PowerShell jako správce a zadejte [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps), ponechte vás informuje, že je aplikace typu 1.0.0.0 **VisualObjects** který je nasazen.  

Aplikace je uložen pod následující relativní cestu, kde nekomprimovaným Service Fabric SDK - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. V tomto adresáři, kde je uložen balíček aplikace byste měli najít složku "Balíček". Zkontrolujte časová razítka zajistit, že je na nejnovější verzi (budete muset upravit cesty správně také).

Nyní Pojďme zkopírujte balíček aktualizovanou aplikaci služby prostředků infrastruktury úložiště bitových kopií (kde jsou balíčky aplikací uložené pomocí Service Fabric). Parametr *ApplicationPackagePathInImageStore* informuje o tom, kde ji můžete najít balíček aplikace Service Fabric. Jsme umístili aktualizovanou aplikaci "VisualObjects\_V2" pomocí následujícího příkazu (budete muset znovu správně Změna cest).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

Dalším krokem je zaregistrovat tuto aplikaci Service Fabric, která je možné provést pomocí [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) příkaz:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Pokud předchozí příkaz k neúspěchu, je pravděpodobné, že je nutné znovu vytvořit všechny služby. Jak je uvedeno v kroku 2, budete muset aktualizovat vaši webovou verzi.

Doporučujeme odebrat balíček aplikace, po úspěšném zaregistrování aplikace.  Odstranění balíčky aplikací z úložiště image store uvolnit systémové prostředky.  Udržování balíčků nepoužívané aplikace využívá diskového úložiště a vede k problémům s výkonem aplikace.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>Krok 5: Spusťte upgrade aplikace
Nyní jsme všechno nastavené ke spuštění upgradu aplikace pomocí [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) příkaz:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Název aplikace je stejný, jak je popsáno v *ApplicationManifest.xml* souboru. Service Fabric používá k identifikaci, která aplikace je získávání upgradovat tento název. Pokud jste nastavili vypršení časových limitů jako příliš krátká, můžete se setkat selhání zpráva, že problém. V části řešení potíží, případně zvyšte vypršení časových limitů.

Nyní, jako aplikace upgradu bude pokračovat, můžete monitorovat pomocí Service Fabric Explorer nebo pomocí [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) příkaz prostředí PowerShell: 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

Za několik minut, stav, který jste získali pomocí předchozí příkaz prostředí PowerShell, stavu, aby byly upgradovány všechny aktualizace domény (Dokončit). A by měl zjistit, že vizuální objekty v okně prohlížeče spustili otáčení!

Pokuste se upgrade z verze 2 na verze 3 nebo z verze 2 na verze 1 jako cvičení. Přesun z verze 2 na verze 1 také považuje upgrade. Přehrání s vypršení časových limitů a zásad stavu proveďte vlastní obeznámeni s nimi. Při nasazení do Azure clusteru, parametry nutné nastavit správně. Je vhodné můžete nastavit vypršení časových limitů.

## <a name="next-steps"></a>Další postup
[Upgrade vaší aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgrade aplikace pomocí sady Visual Studio.

Řídí, jak vaše aplikace upgraduje pomocí [upgrade parametry](service-fabric-application-upgrade-parameters.md).

Zkontrolujte upgradů aplikace kompatibilní podle naučit se používat [serializace dat](service-fabric-application-upgrade-data-serialization.md).

Další informace o použití pokročilých funkcí při upgradu vaší aplikace tím, že odkazuje na [Advanced témata](service-fabric-application-upgrade-advanced.md).

Řešení běžných potíží v upgradů aplikací podle kroků v části [řešení potíží s aplikací upgrady](service-fabric-application-upgrade-troubleshooting.md).

