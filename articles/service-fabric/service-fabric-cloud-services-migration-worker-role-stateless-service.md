---
title: Převést aplikací Azure Cloud Services na mikroslužeb | Microsoft Docs
description: Tato příručka porovná bezstavové služby Cloud Services – webové a rolí pracovního procesu a Service Fabric můžete migrovat z cloudové služby do Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 5880ebb3-8b54-4be8-af4b-95a1bc082603
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: bb8f2f8a6f0905716c34796a5b16c38f406ae64c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Průvodce převodu Web a rolí pracovního procesu na bezstavové služby Service Fabric
Tento článek popisuje, jak migrovat Service Fabric bezstavové služby Cloud Services – webové a rolí pracovního procesu. Je to ta nejjednodušší cesta migrace z cloudové služby na Service Fabric pro aplikace, jejichž přehled architektury přechází zůstane zhruba stejná.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Projekt cloudové služby na projekt aplikace Service Fabric
 Projekt cloudové služby a aplikace Service Fabric projektu mají podobnou strukturou a obě představují jednotky nasazení pro vaši aplikaci – to znamená, že každý definovat kompletní balíček, který je nasazen na spuštění vaší aplikace. Cloudové služby projektu obsahuje jeden nebo více webu nebo rolí pracovního procesu. Podobně projektu aplikace Service Fabric obsahuje jednu nebo více služeb. 

Rozdíl je, že projekt cloudové služby páry v odstupu nasazení s nasazení virtuálního počítače a proto obsahuje nastavení konfigurace virtuálního počítače v, zatímco aplikace Service Fabric projektu definuje pouze aplikace, která bude nasazena na sadu existující virtuální počítače v clusteru Service Fabric. Samotný cluster Service Fabric je nasazen pouze jednou, prostřednictvím šablonu Resource Manageru nebo prostřednictvím portálu Azure a do zařízení můžete nasadit několik aplikací Service Fabric.

![Porovnání projektu Service Fabric a cloudové služby][3]

## <a name="worker-role-to-stateless-service"></a>Role pracovního procesu pro bezstavové služby
Role pracovního procesu koncepčně, představuje bezstavového zatížení, což znamená, každá instance úlohy je stejný jako a můžete kdykoli směrovat požadavky na jakoukoli instanci. Každá instance neočekává se, mějte na paměti, předchozí požadavek. Stav, který zpracovává zatížení spravuje úložiště služby externí stavu, například Azure Table Storage nebo Azure DB dokumentu. V Service Fabric tento typ úlohy je reprezentována bezstavové služby. Nejjednodušším přístupem při migraci Role pracovního procesu na Service Fabric lze provést převod kód Role pracovního procesu pro bezstavové služby.

![Role pracovního procesu pro bezstavové služby][4]

## <a name="web-role-to-stateless-service"></a>Webovou roli na bezstavové služby
Podobně jako u Role pracovního procesu, webové Role také reprezentuje bezstavového zatížení, a proto koncepčně je příliš lze mapovat na bezstavové služby Service Fabric. Ale na rozdíl od webových rolí, Service Fabric nepodporuje službu IIS. K migraci webové aplikace z webové Role bezstavové služby vyžaduje první přesun webové rozhraní, které může být samoobslužně hostovaná a není závislá na System.Web, například 1 jádro ASP.NET nebo služby IIS.

| **Aplikace** | **Podporuje se** | **Cesty migrace** |
| --- | --- | --- |
| ASP.NET – webové formuláře |Ne |Převést na MVC ASP.NET Core 1 |
| ASP.NET MVC |Pomocí nástroje Migrace |Upgrade na technologii ASP.NET pro základní 1 MVC |
| Webové rozhraní API technologie ASP.NET |Pomocí nástroje Migrace |Použít vlastním hostováním server nebo ASP.NET Core 1 |
| ASP.NET Core 1 |Ano |neuvedeno |

## <a name="entry-point-api-and-lifecycle"></a>Vstupní bod rozhraní API a životního cyklu
Rozhraní API nabídka podobně jako vstupní body služby Role pracovního procesu a Service Fabric: 

| **Vstupní bod** | **Role pracovního procesu** | **Služba Service Fabric** |
| --- | --- | --- |
| Zpracování |`Run()` |`RunAsync()` |
| Spuštění virtuálního počítače |`OnStart()` |neuvedeno |
| Zastavení virtuálního počítače |`OnStop()` |neuvedeno |
| Otevřete naslouchací proces pro požadavky klientů |neuvedeno |<ul><li> `CreateServiceInstanceListener()` pro bezstavové</li><li>`CreateServiceReplicaListener()` pro stateful</li></ul> |

### <a name="worker-role"></a>Role pracovního procesu
```csharp

using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
        }

        public override bool OnStart()
        {
        }

        public override void OnStop()
        {
        }
    }
}

```

### <a name="service-fabric-stateless-service"></a>Bezstavové služby Service Fabric
```csharp

using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace Stateless1
{
    public class Stateless1 : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
        }

        protected override Task RunAsync(CancellationToken cancelServiceInstance)
        {
        }
    }
}

```

Mají obě primární "spustit" přepsání ve kterém se má začít zpracování. Combine služby Service Fabric `Run`, `Start`, a `Stop` do jeden vstupní bod, `RunAsync`. Služby by měl začínat práce při `RunAsync` spustí a by se měla zastavit při práci `RunAsync` signalizace CancellationToken metody. 

Existuje několik hlavní rozdíly mezi životního cyklu a životního cyklu služeb rolí pracovního procesu a Service Fabric:

* **Životní cyklus:** největších rozdíl je, že Role pracovního procesu je virtuální počítač, a proto je životního cyklu vázaný na virtuální počítač, který zahrnuje události spustí nebo zastaví virtuální počítač. Služba Service Fabric má životního cyklu, která je oddělená od životního cyklu virtuálních počítačů, takže neobsahuje události spuštění nebo zastavení, hostitel virtuálního počítače nebo počítače, protože nesouvisí.
* **Doba života:** instance Role pracovního procesu bude recyklovat, pokud `Run` metoda ukončí. `RunAsync` Na dokončení ale můžete spustit metoda ve službě Service Fabric a instance služby zůstanou nahoru. 

Service Fabric představuje vstupní bod instalaci volitelné komunikace pro služby, které naslouchat žádostem klienta. Vstupní bod RunAsync i komunikace jsou volitelné přepsání v Service Fabric služeb – vaše služba rozhodnout komunikovat pouze na požadavky klientů, nebo jenom spustit smyčku zpracování, nebo obojí -, které je důvod, proč je dovoleno metodě RunAsync ukončení bez restartování instance služby, protože může být nadále naslouchat žádostem klienta.

## <a name="application-api-and-environment"></a>Aplikace rozhraní API a prostředí
Cloudové služby prostředí rozhraní API poskytuje informace a funkce pro aktuální instanci virtuálního počítače a také informace o další instance rolí virtuálních počítačů. Obsahuje informace o jeho modulu runtime Service Fabric a některé informace o uzlu služby je v aktuálně spuštěna. 

| **Úloha prostředí** | **Cloud Services** | **Service Fabric** |
| --- | --- | --- |
| Nastavení konfigurace a oznámení o změně |`RoleEnvironment` |`CodePackageActivationContext` |
| Lokální úložiště |`RoleEnvironment` |`CodePackageActivationContext` |
| Informace o koncovém |`RoleInstance` <ul><li>Aktuální instance: `RoleEnvironment.CurrentRoleInstance`</li><li>Další role a instance: `RoleEnvironment.Roles`</li> |<ul><li>`NodeContext` pro aktuální uzel adresu</li><li>`FabricClient` a `ServicePartitionResolver` pro koncový bod zjišťování služby</li> |
| Emulace prostředí |`RoleEnvironment.IsEmulated` |neuvedeno |
| Událost souběžných změny |`RoleEnvironment` |neuvedeno |

## <a name="configuration-settings"></a>Nastavení konfigurace
Nastavení konfigurace v cloudové služby jsou nastavené pro roli virtuálního počítače a pro všechny instance dané role virtuálních počítačů. Tato nastavení jsou páry klíč hodnota nastavená v souborech ServiceConfiguration.*.cscfg a je možné přistupovat přímo prostřednictvím RoleEnvironment. V Service Fabric nastavení jednotlivě pro každou službu a pro každou aplikaci, nikoli k virtuálnímu počítači, protože virtuální počítač může být hostitelem více služeb a aplikací. Služba se skládá ze tří balíčky:

* **Kód:** obsahuje služby spustitelné soubory, binární soubory, knihovny DLL a všechny soubory, které služba potřebuje ke spuštění.
* **Konfigurace:** všechny konfigurační soubory a nastavení služby.
* **Data:** statických dat soubory spojené s touto službou.

Každý z těchto balíčků může být nezávisle verzí a upgradovaný. Podobně jako cloudové služby, konfigurační balíček je možné programově přistupovat pomocí rozhraní API a události jsou k dispozici oznámení službu ke změně konfigurace balíčku. Soubor souborech Settings.xml slouží pro klíč hodnota konfigurace a programový přístup podobná části Nastavení aplikace v souboru App.config. Ale na rozdíl od cloudové služby, konfigurační balíček Service Fabric může obsahovat všechny konfigurační soubory v libovolném formátu toho, jestli je XML, JSON, YAML nebo vlastní binární formát. 

### <a name="accessing-configuration"></a>Přístup ke konfiguraci
#### <a name="cloud-services"></a>Cloud Services
Nastavení konfigurace ze ServiceConfiguration.*.cscfg je možné přistupovat prostřednictvím `RoleEnvironment`. Tato nastavení jsou globálně dostupnou pro všechny instance rolí v jednom nasazení cloudové služby.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Každá služba má svůj vlastní balíček individuální konfigurace. Všechny aplikace v clusteru je dostupné žádné předdefinované mechanismus pro globální nastavení konfigurace. Pokud používáte Service Fabric speciální souborech Settings.xml konfigurační soubor v rámci konfigurace balíčku, hodnoty v souborech Settings.xml lze přepsat na úrovni aplikace, aby možné nastavení konfigurace na úrovni aplikace.

Nastavení konfigurace se přistupuje v rámci každá instance služby prostřednictvím služby `CodePackageActivationContext`.

```csharp

ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");

// Access Settings.xml
KeyedCollection<string, ConfigurationProperty> parameters = configPackage.Settings.Sections["MyConfigSection"].Parameters;

string value = parameters["Key"]?.Value;

// Access custom configuration file:
using (StreamReader reader = new StreamReader(Path.Combine(configPackage.Path, "CustomConfig.json")))
{
    MySettings settings = JsonConvert.DeserializeObject<MySettings>(reader.ReadToEnd());
}

```

### <a name="configuration-update-events"></a>Události aktualizace konfigurace
#### <a name="cloud-services"></a>Cloud Services
`RoleEnvironment.Changed` Událostí se používá k upozornění všechny instance rolí, když dojde ke změně v prostředí, například o změně konfigurace. To umožňuje využívat aktualizace konfigurace bez recyklace instance rolí nebo restartování pracovní proces.

```csharp

RoleEnvironment.Changed += RoleEnvironmentChanged;

private void RoleEnvironmentChanged(object sender, RoleEnvironmentChangedEventArgs e)
{
   // Get the list of configuration changes
   var settingChanges = e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>();
foreach (var settingChange in settingChanges) 
   {
      Trace.WriteLine("Setting: " + settingChange.ConfigurationSettingName, "Information");
   }
}

```

#### <a name="service-fabric"></a>Service Fabric
Každý typ tři balíček ve službě - kód, konfigurace a Data - mít události, které instanci služby balíček aktualizace, přidat nebo odebrat. Služby mohou obsahovat více balíčků každého typu. Služba může mít například více balíčků konfigurace se každý jednotlivě verzí a rozšiřitelný. 

Tyto události jsou k dispozici využívat změny v balíčky služeb bez restartování instance služby.

```csharp

this.Context.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## <a name="startup-tasks"></a>Spuštění úlohy
Spuštění úlohy se akcí, které předtím, než aplikaci spustí. Úloha spuštění se obvykle používá ke spouštění skriptů instalace použitím zvýšených oprávnění. Cloudové služby a Service Fabric podporu spuštění úloh. Hlavní rozdíl je, že v cloudové služby, úloha spuštění je vázaný na virtuální počítač protože je součástí instanci role, zatímco v Service Fabric úloha spuštění je vázaný na služby, která není vázaný na žádné konkrétní virtuální počítač.

| Service Fabric | Cloud Services |
| --- | --- | --- |
| Umístění konfigurace |ServiceDefinition.csdef |
| Oprávnění |"omezený" nebo "se zvýšenými oprávněními" |
| Pořadí úloh |"jednoduché", "pozadí", "popředí" |

### <a name="cloud-services"></a>Cloud Services
V cloudové služby je konfigurovat vstupní bod spuštění pro jednotlivé role v ServiceDefinition.csdef. 

```xml

<ServiceDefinition>
    <Startup>
        <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
            <Environment>
                <Variable name="MyVersionNumber" value="1.0.0.0" />
            </Environment>
        </Task>
    </Startup>
    ...
</ServiceDefinition>

```

### <a name="service-fabric"></a>Service Fabric
V Service Fabric je nakonfigurovaný vstupní bod spuštění pro službu v ServiceManifest.xml:

```xml

<ServiceManifest>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Startup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    ...
</ServiceManifest>

``` 

## <a name="a-note-about-development-environment"></a>Poznámka o vývojového prostředí
Cloudové služby a služby infrastruktury jsou integrované s Visual Studio pomocí šablony projektů a podpora pro ladění, konfigurace a nasazení místně a do Azure. Cloudové služby a Service Fabric také poskytuje prostředí runtime místní vývoj. Rozdílem je, že při běhu vývoj cloudové služby emuluje prostředí Azure, ve kterém běží, Service Fabric nepoužívá emulátor – používá dokončení modulu runtime Service Fabric. Service Fabric prostředí, ve kterém můžete spustit na místním vývojovém počítači je stejné prostředí, ve kterém běží v produkčním prostředí.

## <a name="next-steps"></a>Další postup
Další informace o spolehlivé služby Service Fabric a základní rozdíly mezi cloudové služby a architektury aplikací Service Fabric, abyste pochopili, jak chcete využít výhod úplnou sadu funkcí Service Fabric.

* [Začínáme se službami Reliable Services Service Fabric](service-fabric-reliable-services-quick-start.md)
* [Koncepční Průvodce rozdíly mezi cloudovými službami a Service Fabric](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
