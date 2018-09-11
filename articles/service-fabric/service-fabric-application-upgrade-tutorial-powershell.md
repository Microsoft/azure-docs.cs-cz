---
title: Upgrade aplikace Service Fabric pomocí Powershellu | Dokumentace Microsoftu
description: Tento článek vás provedl možnostmi, které nasazení aplikace Service Fabric, změny kódu a zavádí upgrade pomocí Powershellu.
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
ms.openlocfilehash: c1005d60df0b1cfd3b24be954ab4ff1b18c8f7a8
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44348765"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Upgrade aplikace Service Fabric pomocí Powershellu
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Nejčastěji používané a doporučený postup upgradu je monitorovaném upgradu se zajištěním provozu.  Azure Service Fabric monitoruje stav aplikace se upgraduje na základě sady zásad stavu. Po upgradu aktualizační doména (UD), Service Fabric vyhodnocuje stav aplikace a pokračuje na další aktualizační doménu nebo selže v závislosti na stavu zásad upgradu.

Upgrade monitorovanou aplikaci je možné provádět pomocí spravované nebo nativní rozhraní API, Powershellu, rozhraní příkazového řádku Azure, Javu nebo REST. Pokyny k provedení upgradu pomocí sady Visual Studio najdete v tématu [upgrade vaší aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md).

Pomocí Service Fabric monitorovat postupné upgrady správce aplikace lze nakonfigurovat hodnocení zásad stavu, který používá Service Fabric k určení, jestli je aplikace v pořádku. Kromě toho může správce nakonfigurovat akce, která má být provedeny, když selže hodnocení stavu (například způsobem automatického vrácení zpět.) Tato část vás provede monitorovaných upgrade pro jednu z ukázek sadu SDK, které používá PowerShell. Následující video Microsoft Virtual Academy vás také provede upgrade aplikací: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=OrHJH66yC_6406218965">
<img src="./media/service-fabric-application-upgrade-tutorial-powershell/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Krok 1: Vytvoření a nasazení ukázky vizuálních objektů
Vytvářejte a publikujte aplikaci kliknutím pravým tlačítkem na projekt aplikace **VisualObjectsApplication,** a vyberete **publikovat** příkazu.  Další informace najdete v tématu [kurz upgradu aplikace Service Fabric](service-fabric-application-upgrade-tutorial.md).  Alternativně můžete použít PowerShell k nasazení vaší aplikace.

> [!NOTE]
> Před použitím některých z těchto příkazů Service Fabric může v prostředí PowerShell, musíte nejprve připojte ke clusteru pomocí `Connect-ServiceFabricCluster` rutiny. Podobně se předpokládá, že Cluster je už nastavený na místním počítači. Přečtěte si článek na [nastavení vývojového prostředí Service Fabric](service-fabric-get-started.md).
> 
> 

Po vytvoření projektu v sadě Visual Studio, můžete použít příkaz prostředí PowerShell [kopírování ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) ImageStore zkopírujte balíček aplikace. Pokud chcete ověřit balíček aplikace místně, použijte [testovací ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) rutiny. Dalším krokem je registrace aplikace pomocí modulu runtime Service Fabric [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) rutiny. Následující krok, je spustit instanci aplikace s použitím [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) rutiny.  Tyto tři kroky, které jsou obdobou použití **nasadit** položky nabídky v sadě Visual Studio.  Po dokončení zřizování by měl vyčistit balíčku aplikace zkopírovaná z úložiště imagí za účelem snížení spotřebované prostředky.  Pokud typ aplikace se už nevyžaduje, mělo by neregistrované ze stejného důvodu. Zobrazit [nasazení a odeberte aplikací pomocí prostředí PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) Další informace.

Teď můžete použít [Service Fabric Explorer zobrazíte cluster a aplikace](service-fabric-visualizing-your-cluster.md). Aplikace má webová služba, která se dá Navigovat v aplikaci Internet Explorer tak, že zadáte [ http://localhost:8081/visualobjects ](http://localhost:8081/visualobjects) do adresního řádku.  Měli byste vidět některé s plovoucí desetinnou čárkou vizuální objekty přesouvat na obrazovce.  Kromě toho můžete použít [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) ke kontrole stavu aplikace.

## <a name="step-2-update-the-visual-objects-sample"></a>Krok 2: Ukázku aktualizovat, a vizuálních objektů
Můžete si všimnout, že s verzí, který je nasazený v kroku 1, ne otočit vizuální objekty. Umožňuje upgradovat tuto aplikaci do jednoho kde také otočit vizuální objekty.

Vyberte projekt VisualObjects.ActorService v rámci řešení VisualObjects a otevřete soubor StatefulVisualObjectActor.cs. V rámci tohoto souboru, přejděte k metodě `MoveObject`, okomentujte `this.State.Move()`a zrušte komentář u `this.State.Move(true)`. Tato změna otočí objekty po upgradu služby.

Musíme také aktualizovat *ServiceManifest.xml* souboru (packageroot) nebo projektu **VisualObjects.ActorService**. Aktualizace *CodePackage* a verze aktualizace service 2.0 a odpovídající řádky *ServiceManifest.xml* souboru.
Můžete použít Visual Studio *upravit soubory manifestu* možnosti po kliknutí pravým tlačítkem na řešení k provedení změn souboru manifestu.

Po provedení změny v manifestu by měl vypadat nějak takto (zvýrazněné části se změny zobrazily):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Nyní *ApplicationManifest.xml* souboru (nalezené pod **VisualObjects** projektu v rámci **VisualObjects** řešení) je aktualizována na verzi 2.0 **VisualObjects.ActorService** projektu. Kromě toho verze aplikace se aktualizuje a 2.0.0.0 z 1.0.0.0. *ApplicationManifest.xml* by měl vypadat jako následující fragment kódu:

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Nyní, sestavte projekt výběrem jenom **ActorService** projektu a klikněte pravým tlačítkem myši a vyberete **sestavení** možnost v sadě Visual Studio. Pokud vyberete **sestavit vše znovu**, měli byste aktualizovat verze pro všechny projekty, protože kód by byly změněny. Dále můžeme kliknutím pravým tlačítkem na balíček aktualizovanou aplikaci ***VisualObjectsApplication***, výběrem nabídky Service Fabric a zvolením **balíčku**. Tato akce vytvoří balíček aplikace, které je možné nasadit.  Aktualizovaná aplikace je připravená k nasazení.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Krok 3: Při rozhodování o stavu zásady a parametry upgradu
Seznamte se s [parametry upgradu aplikace](service-fabric-application-upgrade-parameters.md) a [procesu upgradu](service-fabric-application-upgrade.md) získat dobrý přehled o různé parametry upgradu, vypršení časových limitů a kritéria stavu použít. V tomto návodu je kritéria hodnocení stavu služby nastavenou na výchozí hodnotu (a doporučené) hodnoty, které znamená, že by měly být všechny služby a instance *v pořádku* po upgradu.  

Však Řekněme zvýšit *HealthCheckStableDuration* na 60 sekund (tak, že služby jsou v pořádku po dobu 20 sekund před provedením upgradu na další aktualizační domény).  Také nastavíme *UpgradeDomainTimeout* bude 1 200 sekund a *UpgradeTimeout* bude 3000 sekund.

A konečně, také nastavíme *UpgradeFailureAction* k vrácení zpět. Tato možnost vyžaduje Service Fabric vrátit k předchozí verzi aplikace, pokud nalezne nějaké problémy při upgradu. Proto při spuštění upgradu (v kroku 4), jsou určeny následující parametry:

FailureAction = vrácení zpět

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>Krok 4: Příprava aplikací pro upgrade
Aplikace je teď vytvořené a připraveno k upgradu. Pokud otevřete okno Powershellu jako správce a zadejte [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps), ponechte vás informuje, že je aplikace typu 1.0.0.0 **VisualObjects** , která je nasazena.  

Balíček aplikace se ukládají následující relativní cesta kde nekomprimovaný Service Fabric SDK - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. V tomto adresáři, kde je uložen balíček aplikace, měli byste najít složku "Balíček". Zkontrolujte časové razítko k zajištění, že je na nejnovější verzi (budete muset cesty příslušným způsobem upravte také).

Nyní Pojďme zkopírujte balíček aktualizovanou aplikaci do ImageStore Fabric Service (kde jsou balíčky aplikací uložené Service Fabric). Parametr *ApplicationPackagePathInImageStore* informuje o tom, kde může najít balíček aplikace Service Fabric. Jsme umístili aktualizovanou aplikaci do "VisualObjects\_V2" pomocí následujícího příkazu (budete muset znovu příslušným způsobem upravte cesty).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

Dalším krokem je registrace aplikace s využitím Service Fabric, který se dá provést pomocí [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) příkaz:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Pokud ve výstupu předchozího příkazu neproběhne úspěšně, je pravděpodobné, že potřebujete opětovné sestavení všech služeb. Jak je uvedeno v kroku 2, budete muset aktualizovat vaše webová služba verze.

Doporučujeme odebrat balíček aplikace po úspěšné registraci aplikace.  Odstranění balíčků aplikací z úložiště image store uvolnit systémové prostředky.  Udržování balíčky nepoužívané aplikace využívá diskové úložiště a vede k problémům s výkonem aplikací.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>Krok 5: Spuštění upgradu aplikace
Nyní jsme všechno máte nastavené a spusťte upgrade aplikace pomocí [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) příkaz:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Název aplikace je stejný, jak je popsáno v *ApplicationManifest.xml* souboru. Service Fabric používá tento název k identifikaci, kterou aplikaci se neupgradují v. Pokud jste nastavili časové limity, které je příliš krátký, může dojít k selhání zpráva oznamující, problém. Řešení potíží v oddílu nebo zvýšení časových limitů.

Teď, když aplikace upgrade bude pokračovat, můžete monitorovat pomocí Service Fabric Explorer, nebo pomocí [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) příkazu Powershellu: 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

Za pár minut, stav, který jste získali pomocí předchozího příkazu Powershellu, by měl uvést, že se upgradovala všech aktualizačních domén (Dokončit). A měli byste najít, že jste spustili vizuálních objektů v okně prohlížeče otáčení!

Upgrade z verze 2 na verzi 3, nebo mezi 2 a verzí 1 jako cvičení, můžete zkusit. Přesouvá z verze 2 verze 1 se také považuje za upgrade. Pohrajte si s vypršení časových limitů a zásady stavu proveďte vlastní zkušenosti s nimi. Při nasazování do clusteru Azure, je potřeba odpovídajícím způsobem nastavit parametry. Je vhodné nastavení časových limitů obdržíte.

## <a name="next-steps"></a>Další postup
[Upgrade vaší aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgrade aplikace pomocí sady Visual Studio.

Řídí, jak vaše aplikace upgradovala pomocí [parametry upgradu](service-fabric-application-upgrade-parameters.md).

Díky upgradů aplikace kompatibilní učit, jak používat [serializaci dat](service-fabric-application-upgrade-data-serialization.md).

Zjistěte, jak používat pokročilé funkce při upgradu aplikace rekapitulací [Pokročilá témata](service-fabric-application-upgrade-advanced.md).

Vyřešit běžné problémy v upgradech aplikací odkazující na kroky v [řešení potíží s upgrady aplikací](service-fabric-application-upgrade-troubleshooting.md).

