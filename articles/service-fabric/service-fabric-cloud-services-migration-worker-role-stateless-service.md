---
title: Převést Azure Cloud Services aplikace Service Fabric | Dokumentace Microsoftu
description: Tato příručka porovnává Cloud Services – webové a pracovní role a Service Fabric bezstavové služby, které vám pomohou migrovat ze služby Cloud Services do Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 5880ebb3-8b54-4be8-af4b-95a1bc082603
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 4eed3825d52fe52025077980e21f3763cc5751ac
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44049945"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Příručka k převodu webové a pracovní role pro bezstavové služby Service Fabric
Tento článek popisuje, jak migrovat Cloud Services – webové a pracovní role do bezstavové služby Service Fabric. Toto je nejjednodušší postupu migrace ze služby Cloud Services do Service Fabric pro aplikace, jejichž architektury se to zůstat přibližně stejná.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Projekt cloudové služby na projekt aplikace Service Fabric
 Projekt cloudové služby a projekt aplikace Service Fabric mají podobné struktury a obě představují jednotka nasazení aplikace – to znamená, že každý definovat kompletní balíček, který je nasazen na spuštění aplikace. Projekt cloudové služby obsahuje jeden nebo více webových nebo role pracovního procesu. Obdobně projekt aplikace Service Fabric obsahuje jednu nebo víc služeb. 

Rozdíl je, že projekt cloudové služby páry v odstupu nasazení aplikace s nasazením virtuálního počítače a proto obsahuje konfigurační nastavení virtuálních počítačů, že projekt aplikace Service Fabric definuje pouze aplikace, která se nasadí do sady stávající virtuální počítače v clusteru Service Fabric. Samotný cluster Service Fabric se nasadit jenom jednou, pomocí šablony Resource Manageru nebo webu Azure portal a do zařízení můžete nasadit několik aplikací Service Fabric.

![Porovnání projekt Service Fabric a Cloud Services][3]

## <a name="worker-role-to-stateless-service"></a>Role pracovního procesu pro bezstavové služby
Role pracovního procesu koncepčně, představuje bezstavovou úlohu, to znamená každou instanci sady funkcí je stejný jako a je možné směrovat požadavky na jakoukoli instanci v každém okamžiku. Každá instance není očekáván pamatovat předchozí žádosti. Stav, který pracuje úlohy se spravuje přes nebo externího stavu úložiště, jako je Azure Table Storage nebo Azure Document DB. V Service Fabric tento typ úlohy je reprezentován bezstavovou službu. Nejjednodušším přístupem při migraci Role pracovního procesu do Service Fabric můžete udělat převedením kód Role pracovního procesu pro bezstavové služby.

![Role pracovního procesu pro bezstavové služby][4]

## <a name="web-role-to-stateless-service"></a>Webové Role pro bezstavové služby
Podobně jako u rolí pracovního procesu, webové Role také představuje bezstavového zatížení, a proto koncepčně je příliš lze mapovat na bezstavovou službu Service Fabric. Ale na rozdíl od webových rolí, Service Fabric nepodporuje službu IIS. K migraci webové aplikace z webové Role pro bezstavové služby vyžaduje, první přesun webové rozhraní, které mohou být v místním prostředí a nezávisí na serveru IIS nebo System.Web, jako je například 1 technologie ASP.NET Core.

| **Aplikace** | **Podporuje se** | **Následující způsob migrace** |
| --- | --- | --- |
| Webové formuláře ASP.NET |Ne |Převést na ASP.NET Core 1 MVC |
| ASP.NET MVC |S migrací |Upgrade na technologie ASP.NET Core 1 MVC |
| Webové rozhraní API technologie ASP.NET |S migrací |Použít server v místním prostředí nebo ASP.NET Core 1 |
| ASP.NET Core 1 |Ano |neuvedeno |

## <a name="entry-point-api-and-lifecycle"></a>Vstupní bod rozhraní API a životního cyklu
Role pracovního procesu a Service Fabric service API nabídka podobně jako vstupní body: 

| **Vstupní bod** | **Role pracovního procesu** | **Služba Service Fabric** |
| --- | --- | --- |
| Zpracování |`Run()` |`RunAsync()` |
| Spuštění virtuálního počítače |`OnStart()` |neuvedeno |
| Zastavení virtuálního počítače |`OnStop()` |neuvedeno |
| Otevřít naslouchací proces pro požadavky klientů |neuvedeno |<ul><li> `CreateServiceInstanceListener()` pro bezstavové</li><li>`CreateServiceReplicaListener()` pro stavová</li></ul> |

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

Obě mají primární "spustit" přepsání, ve kterém začíná zpracování. Kombinace služeb Service Fabric `Run`, `Start`, a `Stop` do jedním vstupním bodem `RunAsync`. Vaše služba by měla začít pracovat, kdy `RunAsync` spustí a by se měla zastavit při práci `RunAsync` signalizován CancellationToken metody. 

Existuje několik klíčových rozdílů mezi životního cyklu a životního cyklu služeb rolí pracovního procesu a Service Fabric:

* **Životní cyklus** největším rozdílem je, že Role pracovního procesu je virtuální počítač a tak jeho životní cyklus se váže k virtuálnímu počítači, který zahrnuje události spuštění a zastavení virtuálního počítače. Služba Service Fabric obsahuje životního cyklu, která je oddělená od životního cyklu virtuálního počítače, takže neobsahuje události spuštění a zastavení, hostitele virtuálního počítače nebo počítače, protože nesouvisí.
* **Doba života:** instance Role pracovního procesu se recyklovat, pokud `Run` metoda ukončení. `RunAsync` Metoda ve službě Service Fabric můžete ale doběhla do konce a bude zajištěna instance služby. 

Service Fabric představuje vstupní bod nastavení volitelné komunikace pro služby, které naslouchat žádostem klienta. Vstupní bod RunAsync i komunikaci jsou volitelné přepsání služby Service Fabric – vaše služba se rozhodnout naslouchají pouze na požadavky klientů, nebo pouze spustit smyčku zpracování, nebo obojí – to je důvod, proč metodě RunAsync může ukončit bez restartování instance služby, protože může dál pro naslouchání požadavků klienta.

## <a name="application-api-and-environment"></a>Aplikace rozhraní API a prostředí
Prostředí Cloud Services rozhraní API poskytuje informace o a funkcionalita pro aktuální instanci virtuálního počítače a taky informace o ostatních instancí role virtuálního počítače. Obsahuje informace o jeho modulu runtime Service Fabric a některé informace o uzlu služby je aktuálně spuštěné. 

| **Úlohu prostředí** | **Cloud Services** | **Service Fabric** |
| --- | --- | --- |
| Nastavení konfigurace a oznámení o změně |`RoleEnvironment` |`CodePackageActivationContext` |
| Lokální úložiště |`RoleEnvironment` |`CodePackageActivationContext` |
| Informace o koncovém bodu |`RoleInstance` <ul><li>Aktuální instance: `RoleEnvironment.CurrentRoleInstance`</li><li>Další role a instance: `RoleEnvironment.Roles`</li> |<ul><li>`NodeContext` pro aktuální uzel adresu</li><li>`FabricClient` a `ServicePartitionResolver` zjišťování koncových bodů služby</li> |
| Emulace prostředí |`RoleEnvironment.IsEmulated` |neuvedeno |
| Událost souběžných změny |`RoleEnvironment` |neuvedeno |

## <a name="configuration-settings"></a>Nastavení konfigurace
Nastavení konfigurace v rámci cloudových služeb se nastavují pro roli virtuálního počítače a platí pro všechny instance dané role virtuálního počítače. Tato nastavení jsou nastavení v souborech ServiceConfiguration.*.cscfg páry klíč hodnota a je přístupný přímo prostřednictvím RoleEnvironment. V Service Fabric nastavení platí zvlášť pro každou službu a pro každou aplikaci, nikoli k virtuálnímu počítači, protože virtuální počítač může být hostitelem více služeb a aplikací. Služba se skládá z tři balíčky:

* **Kód:** obsahuje služby spustitelné soubory, binární soubory, knihovny DLL a všechny další soubory, které služba potřebuje ke spuštění.
* **Config:** všechny konfigurační soubory a nastavení služby.
* **Data:** statické datové soubory související se službou.

Každá z těchto balíčků lze nezávisle označovat verzí a upgradovat. Podobně jako cloudové služby, balíček config je možné programově přistupovat přes rozhraní API a události jsou k dispozici pro oznámení služby změní konfiguraci balíčku. Souboru Settings.xml lze použít pro konfigurace klíč hodnota a programový přístup podobně jako v části Nastavení aplikace ze souboru App.config. Ale na rozdíl od služeb Cloud Services, Service Fabric config balíček může obsahovat všechny konfigurační soubory v libovolném formátu, ať už jde o XML, JSON, YAML nebo vlastní binární formát. 

### <a name="accessing-configuration"></a>Přístup ke konfiguraci
#### <a name="cloud-services"></a>Cloud Services
Nastavení konfigurace ze ServiceConfiguration.*.cscfg přístupné prostřednictvím `RoleEnvironment`. Tato nastavení jsou globálně k dispozici pro všechny instance rolí ve stejném nasazení cloudové služby.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Každá služba má svůj vlastní balíček individuální konfigurace. Všechny aplikace v clusteru není dostupný žádný předdefinovaný mechanismus pro globální nastavení konfigurace. Když použijete Service Fabric speciální Settings.xml konfigurační soubor v rámci konfiguračního balíčku, hodnoty v Settings.xml můžete přepsat na úrovni aplikace, nastavení konfigurace na úrovni aplikace je to možné.

Nastavení konfigurace jsou přístupy do každá instance služby ve službě `CodePackageActivationContext`.

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

### <a name="configuration-update-events"></a>Události konfigurace služby aktualizací
#### <a name="cloud-services"></a>Cloud Services
`RoleEnvironment.Changed` Událost se používá pro všechny instance rolí upozornit, když dojde ke změně v prostředí, jako je například změna konfigurace. To umožňuje využívat aktualizace konfigurace bez recyklace role instance nebo restartování pracovního procesu.

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
Každý typ tři balíčku ve službě - kód, konfigurace a Data - mají události, které oznámí instance služby je balíček aktualizován, přidání nebo odebrání. Služba může obsahovat více balíčků každého typu. Služba může mít například více balíčků konfigurace každý samostatně vyvíjených a je možné upgradovat. 

Tyto události jsou k dispozici pro využívání změny v balíčcích služby bez restartování instance služby.

```csharp

this.Context.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## <a name="startup-tasks"></a>Úlohy po spuštění
Úlohy po spuštění jsou akce prováděné před spuštěním aplikace. Úlohy po spuštění se obvykle používá ke spouštění skriptů instalace použitím zvýšených oprávnění. Cloud Services a Service Fabric podporovat úlohy při spuštění. Hlavní rozdíl je, že v cloudových službách, úlohy po spuštění se váže k virtuálnímu počítači protože je součástí role instance, zatímco v Service Fabric úlohy po spuštění je vázán na službu, která není vázána k žádné konkrétní virtuální počítač.

| Service Fabric | Cloud Services |
| --- | --- | --- |
| Umístění konfigurace |ServiceDefinition.csdef |
| Oprávnění |"omezené" nebo "se zvýšenými oprávněními" |
| Pořadí úloh |"jednoduchý", "pozadí", "popředí" |

### <a name="cloud-services"></a>Cloud Services
V cloudových službách je nakonfigurovaný vstupní bod spuštění podle role v ServiceDefinition.csdef. 

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
V Service Fabric je na službu v souboru ServiceManifest.xml nakonfigurovaný vstupní bod spuštění:

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

## <a name="a-note-about-development-environment"></a>Poznámka o vývojovém prostředí
Cloud Services a Service Fabric jsou integrované se sadou Visual Studio pomocí šablon projektů a podporu ladění, konfigurace a nasazení místně a do Azure. Cloud Services a Service Fabric také poskytují místní vývojové prostředí runtime. Rozdíl je, když modul runtime vývoj pro cloudové služby emuluje prostředí Azure, na kterém běží, Service Fabric se nepoužívá emulátor – používá dokončení modulu runtime Service Fabric. Service Fabric prostředí, ve kterém můžete spustit na svém místním vývojovém počítači je stejné prostředí, na kterém běží v produkčním prostředí.

## <a name="next-steps"></a>Další postup
Další informace o Service Fabric Reliable Services a základní rozdíly mezi cloudovými službami a architekturu aplikace Service Fabric pochopit, jak využít výhod kompletní sadu funkcí Service Fabric.

* [Začínáme se službou Service Fabric Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Průvodce koncepční rozdíly mezi cloudovými službami a Service Fabric](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
