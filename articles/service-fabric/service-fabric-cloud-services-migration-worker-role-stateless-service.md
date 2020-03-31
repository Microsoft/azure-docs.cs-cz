---
title: Převod aplikací Cloudových služeb Azure na Service Fabric
description: Tato příručka porovnává cloudové služby webové a pracovní role a service fabric bezstavové služby pomoci migrovat z cloudových služeb do service fabric.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: caf067f793ca2086bc068907e86a82266627d128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463338"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Průvodce převodem rolí webu a pracovního procesu na bezstavové služby Service Fabric
Tento článek popisuje, jak migrovat webové a pracovní role cloudových služeb do bezstavových služeb Service Fabric. Toto je nejjednodušší cesta migrace z cloudových služeb do service fabric pro aplikace, jejichž celková architektura zůstane zhruba stejná.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Projekt cloudové služby do projektu aplikace Service Fabric
 Projekt cloudové služby a projekt aplikace Service Fabric mají podobnou strukturu a obě představují jednotku nasazení pro vaši aplikaci – to znamená, že každý definuje kompletní balíček, který je nasazen ke spuštění aplikace. Projekt cloudové služby obsahuje jednu nebo více rolí webu nebo pracovního procesu. Podobně projekt aplikace Service Fabric obsahuje jednu nebo více služeb. 

Rozdíl je v tom, že projekt cloudové služby spojuje nasazení aplikace s nasazením virtuálního počítače a proto obsahuje nastavení konfigurace virtuálního počítače, zatímco projekt aplikace Service Fabric definuje jenom aplikaci, která bude nasazená do sady existující virtuální chody v clusteru Service Fabric. Samotný cluster Service Fabric se nasadí jenom jednou, buď prostřednictvím šablony Správce prostředků, nebo prostřednictvím portálu Azure, a na něj se dá nasadit více aplikací Service Fabric.

![Porovnání projektů Service Fabric a Cloud Services][3]

## <a name="worker-role-to-stateless-service"></a>Role pracovního procesu pro bezstavové služby
Koncepčně role pracovního procesu představuje bezstavové zatížení, což znamená, že každá instance úlohy je identická a požadavky mohou být kdykoli směrovány do libovolné instance. Neočekává se, že by si každá instance pamatovala předchozí požadavek. Stav, na kterém pracovní vytížení funguje, je spravován externím úložištěm stavu, jako je Azure Table Storage nebo Azure Cosmos DB. V Service Fabric tento typ úlohy je reprezentován bezstavové služby. Nejjednodušší přístup k migraci role pracovního procesu na service fabric lze provést převodem kódu role pracovníka na bezstavovou službu.

![Role pracovního procesu ke službě bez stavové služby][4]

## <a name="web-role-to-stateless-service"></a>Webová role pro bezstavovou službu
Podobně jako role pracovního procesu, webová role také představuje bezstavové úlohy, a tak koncepčně příliš lze mapovat na službu Service Fabric bezstavové. Na rozdíl od webových rolí však service fabric nepodporuje službu IIS. Migrace webové aplikace z webové role do bezstavové služby vyžaduje první přesunutí do webového rámce, který může být hostovaný samostatně a nezávisí na službě IIS nebo System.Web, například ASP.NET jádrem 1.

| **Aplikace** | **Podporovány** | **Cesta migrace** |
| --- | --- | --- |
| ASP.NET – webové formuláře |Ne |Převést na ASP.NET Jádra 1 MVC |
| ASP.NET MVC |S migrací |Upgrade na ASP.NET Core 1 MVC |
| Webové rozhraní API ASP.NET |S migrací |Použití serveru s vlastním hostitelem nebo ASP.NET jádra 1 |
| ASP.NET jádro 1 |Ano |Není dostupné. |

## <a name="entry-point-api-and-lifecycle"></a>Vstupní bod API a životní cyklus
Nastavení API služby Role pracovního procesu a service fabric nabízejí podobné vstupní body: 

| **Vstupní bod** | **Role pracovního procesu** | **Služba Service Fabric** |
| --- | --- | --- |
| Zpracování |`Run()` |`RunAsync()` |
| Spuštění virtuálního virtuálního montovna |`OnStart()` |Není dostupné. |
| Zastavení virtuálního virtuálního montova |`OnStop()` |Není dostupné. |
| Otevřít naslouchací proces pro požadavky klientů |Není dostupné. |<ul><li> `CreateServiceInstanceListener()`pro bezstátní</li><li>`CreateServiceReplicaListener()`pro stavové</li></ul> |

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

### <a name="service-fabric-stateless-service"></a>Služba Service Fabric bezstavová služba
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

Oba mají primární "Spustit" přepsat, ve kterém chcete zahájit zpracování. Služby Service `Run` `Start`Fabric `Stop` kombinují , a `RunAsync`do jednoho vstupního bodu . Vaše služba by `RunAsync` měla začít pracovat při `RunAsync` spuštění a měla by přestat fungovat, když je signalizována metoda CancellationToken. 

Existuje několik klíčových rozdílů mezi životním cyklem a životností pracovních rolí a služeb Service Fabric:

* **Životní cyklus:** Největší rozdíl je, že role pracovního procesu je virtuální hod, a proto jeho životní cyklus je vázaný na virtuální hod, který zahrnuje události při spuštění a zastavení virtuálního aplikace. Služba Service Fabric má životní cyklus, který je oddělený od životního cyklu virtuálního počítače, takže nezahrnuje události při spuštění a zastavení hostitelského virtuálního počítače nebo počítače, protože spolu nesouvisí.
* **Životnost:** Instance role pracovního procesu se zrecykluje, pokud se `Run` metoda ukončí. Metoda `RunAsync` ve službě Service Fabric však může spustit až do dokončení a instance služby zůstane nahoru. 

Service Fabric poskytuje volitelný vstupní bod nastavení komunikace pro služby, které naslouchá požadavkům klientů. Vstupní bod RunAsync i komunikace jsou volitelné přepsání ve službách Service Fabric - vaše služba se může rozhodnout pouze naslouchat požadavkům klientů nebo spustit pouze smyčku zpracování nebo obojí - což je důvod, proč je metoda RunAsync povolena ukončení bez restartování instance služby, protože může nadále naslouchat požadavkům klientů.

## <a name="application-api-and-environment"></a>Aplikační ROZHRANÍ API a prostředí
Rozhraní API prostředí cloudových služeb poskytuje informace a funkce pro aktuální instanci virtuálního aplikace a také informace o dalších instancích rolí virtuálních virtuálních mích. Service Fabric poskytuje informace týkající se jeho runtime a některé informace o uzlu služby je aktuálně spuštěna na. 

| **Úloha prostředí** | **Cloudové služby** | **Service Fabric** |
| --- | --- | --- |
| Nastavení konfigurace a změna oznámení |`RoleEnvironment` |`CodePackageActivationContext` |
| Místní úložiště |`RoleEnvironment` |`CodePackageActivationContext` |
| Informace o koncovém bodu |`RoleInstance` <ul><li>Aktuální instance:`RoleEnvironment.CurrentRoleInstance`</li><li>Další role a instance:`RoleEnvironment.Roles`</li> |<ul><li>`NodeContext`pro aktuální adresu uzlu</li><li>`FabricClient`a `ServicePartitionResolver` pro zjišťování koncového bodu služby</li> |
| Emulace prostředí |`RoleEnvironment.IsEmulated` |Není dostupné. |
| Souběžná událost změny |`RoleEnvironment` |Není dostupné. |

## <a name="configuration-settings"></a>Nastavení konfigurace
Nastavení konfigurace v cloudových službách jsou nastavené pro roli virtuálního počítače a platí pro všechny instance této role virtuálního počítače. Tato nastavení jsou dvojice klíč-hodnota nastavena v ServiceConfiguration.*.cscfg soubory a lze přistupovat přímo prostřednictvím RoleEnvironment. V Service Fabric nastavení platí jednotlivě pro každou službu a pro každou aplikaci, nikoli na virtuální počítač, protože virtuální počítač může hostovat více služeb a aplikací. Služba se skládá ze tří balíčků:

* **Kód:** obsahuje spustitelné soubory služby, binární soubory, knihovny DLL a všechny další soubory, které musí služba spustit.
* **Config:** všechny konfigurační soubory a nastavení pro službu.
* **Data:** statické datové soubory přidružené ke službě.

Každý z těchto balíčků lze nezávisle verzí a upgrade. Podobně jako cloudové služby, konfigurační balíček lze přistupovat programově prostřednictvím rozhraní API a události jsou k dispozici upozornit službu na změnu balíčku konfigurace. Soubor Settings.xml lze použít pro konfiguraci klíč-hodnota a programový přístup podobný části nastavení aplikace souboru App.config. Na rozdíl od cloudových služeb však konfigurační balíček Service Fabric může obsahovat libovolné konfigurační soubory v libovolném formátu, ať už se jedná o XML, JSON, YAML nebo vlastní binární formát. 

### <a name="accessing-configuration"></a>Přístup ke konfiguraci
#### <a name="cloud-services"></a>Cloud Services
Nastavení konfigurace z ServiceConfiguration.*.cscfg lze `RoleEnvironment`přistupovat prostřednictvím . Tato nastavení jsou globálně dostupná pro všechny instance rolí ve stejném nasazení cloudové služby.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Každá služba má svůj vlastní individuální konfigurační balíček. Neexistuje žádný vestavěný mechanismus pro globální nastavení konfigurace přístupné všechny aplikace v clusteru. Při použití speciálního konfiguračního souboru Settings.xml služby Service Fabric v konfiguračním balíčku lze hodnoty v souboru Settings.xml přepsat na úrovni aplikace, což umožňuje nastavení konfigurace na úrovni aplikace.

Nastavení konfigurace jsou přístupy v rámci každé `CodePackageActivationContext`instance služby prostřednictvím služby .

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
Událost `RoleEnvironment.Changed` se používá k upozornění všech instancí role, když dojde ke změně v prostředí, jako je například změna konfigurace. Používá se ke spotřebě aktualizací konfigurace bez recyklace role instance nebo restartování pracovního procesu.

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
Každý ze tří typů balíčků ve službě – Kód, Konfigurace a Data – má události, které upozorňují instanci služby při aktualizaci, přidání nebo odebrání balíčku. Služba může obsahovat více balíčků každého typu. Služba může mít například více balíčků konfigurace, z nichž každý je jednotlivě verzí a rozšiřitelný. 

Tyto události jsou k dispozici pro využití změn v balíčcích služeb bez restartování instance služby.

```csharp

this.Context.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## <a name="startup-tasks"></a>Úlohy při spuštění
Úlohy při spuštění jsou akce, které jsou prováděny před spuštěním aplikace. Úloha při spuštění se obvykle používá ke spuštění instalačních skriptů se zvýšenými oprávněními. Cloudové služby i Service Fabric podporují úlohy spuštění. Hlavní rozdíl je, že v cloudových službách je úloha při spuštění svázaná s virtuálním uživatelem, protože je součástí instance role, zatímco v Service Fabric je úloha při spuštění svázaná se službou, která není svázaná s žádným konkrétním virtuálním uživatelem.

| Service Fabric | Cloud Services |
| --- | --- |
| Umístění konfigurace |ServiceDefinition.csdef |
| Oprávnění |"omezená" nebo "zvýšená" |
| Sekvencování |"jednoduché", "pozadí", "popředí" |

### <a name="cloud-services"></a>Cloud Services
V cloudových službách je v souboru ServiceDefinition.csdef konfigurován spouštěcí bod pro všechny role. 

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
V service fabric je v servicemanifestu.xml konfigurován pospuštění pro všechny služby:

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
Cloudové služby i Service Fabric jsou integrované s Visual Studio se šablonami projektů a podporou ladění, konfigurace a nasazení místně i do Azure. Cloudové služby i Service Fabric také poskytují prostředí runtime místního vývoje. Rozdíl je v tom, že zatímco runtime vývoje cloudové služby emuluje prostředí Azure, ve kterém běží, Service Fabric nepoužívá emulátor – používá kompletní service fabric runtime. Prostředí Service Fabric, které spustíte v místním vývojovém počítači, je stejné prostředí, které běží v produkčním prostředí.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o spolehlivých službách Service Fabric a zásadních rozdílech mezi architekturou cloudových služeb a aplikací Service Fabric, abyste pochopili, jak využít celou sadu funkcí Service Fabric.

* [Začínáme se spolehlivými službami Service Fabric](service-fabric-reliable-services-quick-start.md)
* [Koncepční průvodce rozdíly mezi cloudovými službami a service fabric](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
