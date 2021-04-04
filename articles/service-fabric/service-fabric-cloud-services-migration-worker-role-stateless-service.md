---
title: Převod aplikací Cloud Services Azure na Service Fabric
description: Tato příručka porovnává Cloud Services webové a pracovní role a Service Fabric bezstavových služeb, které vám pomůžou migrovat z Cloud Services na Service Fabric.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: cf593f793aabf2a0650684ed8d02fe02d756ec2b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575733"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Průvodce převodem webových rolí a rolí pracovních procesů na Service Fabric bezstavových služeb
Tento článek popisuje, jak migrovat Cloud Services webové a pracovní role do Service Fabric bezstavových služeb. Jedná se o nejjednodušší cestu migrace z Cloud Services do Service Fabric pro aplikace, jejichž celková architektura bude přibližně stejná.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Projekt cloudové služby do Service Fabric projektu aplikace
 Projekt cloudové služby a projekt Service Fabric aplikace mají podobnou strukturu a obě představují jednotku nasazení pro vaši aplikaci – to znamená, že každý z nich definuje kompletní balíček, který je nasazen pro spuštění vaší aplikace. Projekt cloudové služby obsahuje jednu nebo více webových nebo pracovních rolí. Podobně projekt aplikace Service Fabric obsahuje jednu nebo více služeb. 

Rozdílem je to, že projekt cloudové služby Couples nasazení aplikace s nasazením virtuálního počítače, a v něm obsahuje nastavení konfigurace virtuálního počítače, zatímco projekt Service Fabric aplikace definuje jenom aplikaci, která se nasadí do sady existujících virtuálních počítačů v clusteru Service Fabric. Samotný cluster Service Fabric se nasazuje jenom jednou, a to buď prostřednictvím šablony Správce prostředků, nebo prostřednictvím Azure Portal, a do ní můžete nasadit několik Service Fabric aplikací.

![Porovnání projektu Service Fabric a Cloud Services][3]

## <a name="worker-role-to-stateless-service"></a>Role pracovního procesu do bezstavové služby
V koncepčním případě role pracovního procesu představuje bezstavové úlohy, což znamená, že každá instance úlohy je shodná a požadavky lze kdykoli směrovat do libovolné instance. U každé instance se neočekává zapamatovat si předchozí požadavek. Stav, na kterém zatížení funguje, je spravováno externím úložištěm stavů, jako je například Azure Table Storage nebo Azure Cosmos DB. V Service Fabric tento typ úlohy představuje bezstavovou službu. Nejjednodušší přístup k migraci role pracovního procesu na Service Fabric lze provést převodem kódu role pracovního procesu na bezstavovou službu.

![Role pracovního procesu do bezstavové služby][4]

## <a name="web-role-to-stateless-service"></a>Webová role do bezstavové služby
Podobně jako role pracovního procesu představuje webová role také nestavové úlohy, takže je možné, že ji lze namapovat na nestavovou službu Service Fabric. Na rozdíl od webových rolí ale Service Fabric nepodporuje službu IIS. Migrace webové aplikace z webové role do bezstavové služby vyžaduje nejprve přesun na webovou architekturu, která může být v místním prostředí a nezávisí na službě IIS nebo System. Web, jako je například ASP.NET Core 1.

| **Aplikace** | **Podporováno** | **Cesta migrace** |
| --- | --- | --- |
| ASP.NET – webové formuláře |No |Převést na ASP.NET Core 1 MVC |
| ASP.NET MVC |S migrací |Upgrade na ASP.NET Core 1 MVC |
| Rozhraní API pro ASP.NET Web |S migrací |Použití samoobslužného serveru nebo ASP.NET Core 1 |
| ASP.NET Core 1 |Yes |– |

## <a name="entry-point-api-and-lifecycle"></a>Rozhraní API a životní cyklus vstupního bodu
Role pracovního procesu a rozhraní API služby Service Fabric nabízejí podobné vstupní body: 

| **Vstupní bod** | **Role pracovního procesu** | **Služba Service Fabric** |
| --- | --- | --- |
| Zpracování |`Run()` |`RunAsync()` |
| Spuštění virtuálního počítače |`OnStart()` |– |
| Zastavení virtuálního počítače |`OnStop()` |– |
| Otevřít naslouchací proces pro požadavky klientů |– |<ul><li> `CreateServiceInstanceListener()` pro bezstavové</li><li>`CreateServiceReplicaListener()` pro stav</li></ul> |

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

### <a name="service-fabric-stateless-service"></a>Bezstavová služba Service Fabric
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

Obě mají při zahájení zpracování primární přepsání "spustit". Služby Service Fabric Services kombinují `Run` , `Start` a `Stop` do jediného vstupního bodu `RunAsync` . Vaše služba by měla začít pracovat `RunAsync` , když se spustí, a měla by přestat fungovat, když `RunAsync` je CancellationToken metoda signalizována. 

Existuje několik klíčových rozdílů mezi životním cyklem a životností rolí pracovních procesů a Service Fabricch služeb:

* **Životní cyklus:** Největší rozdíl spočívá v tom, že role pracovního procesu je virtuální počítač, a proto je jeho životní cyklus svázán s virtuálním počítačem, což zahrnuje události při spuštění a zastavení virtuálního počítače. Služba Service Fabric má životní cyklus, který je oddělený od životního cyklu virtuálního počítače, takže nezahrnuje události pro dobu, kdy se virtuální počítač hostitele nebo počítač spouští a zastavuje, protože nesouvisí.
* **Doba života:** Instance role pracovního procesu se recykluje, pokud se `Run` Metoda ukončí. `RunAsync`Metoda ve službě Service Fabric může ale běžet až po dokončení a instance služby zůstane v provozu. 

Service Fabric poskytuje volitelnou vstupní bod nastavení komunikace pro služby, které naslouchají požadavkům klientů. Vstupní bod RunAsync i komunikace jsou volitelné přepsání ve službě Service Fabric Services – vaše služba se může rozhodnout, že bude naslouchat pouze na požadavky klientů, nebo spustit pouze výpočetní smyčku, nebo oboje, což znamená, že metoda RunAsync je povolena, aniž by bylo nutné restartovat instanci služby, protože může nadále naslouchat žádostem klienta.

## <a name="application-api-and-environment"></a>Rozhraní API a prostředí aplikace
Rozhraní Cloud Services API prostředí poskytuje informace a funkce pro aktuální instanci virtuálního počítače a také informace o ostatních instancích rolí virtuálních počítačů. Service Fabric poskytuje informace související s modulem runtime a některé informace o uzlu, na kterém je služba aktuálně spuštěná. 

| **Úloha prostředí** | **Cloudové služby** | **Service Fabric** |
| --- | --- | --- |
| Nastavení konfigurace a oznámení o změně |`RoleEnvironment` |`CodePackageActivationContext` |
| Místní úložiště |`RoleEnvironment` |`CodePackageActivationContext` |
| Informace o koncovém bodu |`RoleInstance` <ul><li>Aktuální instance: `RoleEnvironment.CurrentRoleInstance`</li><li>Další role a instance: `RoleEnvironment.Roles`</li> |<ul><li>`NodeContext` pro aktuální adresu uzlu</li><li>`FabricClient` a `ServicePartitionResolver` pro zjišťování koncových bodů služby</li> |
| Emulace prostředí |`RoleEnvironment.IsEmulated` |– |
| Současná událost změny |`RoleEnvironment` |– |

## <a name="configuration-settings"></a>Nastavení konfigurace
Nastavení konfigurace v Cloud Services jsou nastavena pro roli virtuálního počítače a platí pro všechny instance této role virtuálního počítače. Tato nastavení jsou páry klíč-hodnota nastavené v souborech ServiceConfiguration. *. cscfg a dají se získat přímo prostřednictvím RoleEnvironment. V Service Fabric se nastavení aplikují jednotlivě na každou službu a na každou aplikaci, nikoli na virtuální počítač, protože virtuální počítač může hostovat několik služeb a aplikací. Služba se skládá ze tří balíčků:

* **Kód:** obsahuje spustitelné soubory služby, binární soubory, knihovny DLL a všechny další soubory, které musí služba spustit.
* **Config:** všechny konfigurační soubory a nastavení pro službu.
* **Data:** statické datové soubory přidružené ke službě.

Každý z těchto balíčků může být nezávisle a upgradován. Podobně jako u Cloud Services se k konfiguračnímu balíčku dá získat přístup prostřednictvím rozhraní API a události, které upozorní službu změny konfiguračního balíčku. Soubor Settings.xml lze použít pro konfiguraci klíč-hodnota a programový přístup podobně jako oddíl nastavení aplikace v App.config souboru. Na rozdíl od Cloud Services konfigurační balíček Service Fabric může obsahovat libovolné konfigurační soubory v libovolném formátu, ať už se jedná o soubor XML, JSON, YAML nebo vlastní binární formát. 

### <a name="accessing-configuration"></a>Přístup ke konfiguraci
#### <a name="cloud-services"></a>Cloud Services
K nastavení konfigurace z ServiceConfiguration. *. cscfg se dá dostat prostřednictvím `RoleEnvironment` . Tato nastavení jsou globálně dostupná pro všechny instance rolí ve stejném nasazení cloudové služby.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Každá služba má vlastní jednotlivé konfigurační balíčky. Neexistuje žádný integrovaný mechanismus pro nastavení globálních konfigurací, který je přístupný pro všechny aplikace v clusteru. Při použití speciálního konfiguračního souboru Settings.xml Service Fabric v rámci konfiguračního balíčku se hodnoty v Settings.xml mohou přepsat na úrovni aplikace, což umožňuje nastavení konfigurace na úrovni aplikace.

Konfigurační nastavení jsou přístupná v rámci jednotlivých instancí služby prostřednictvím služby `CodePackageActivationContext` .

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
`RoleEnvironment.Changed`Událost se používá k oznamování všech instancí rolí, když dojde ke změně v prostředí, jako je například změna konfigurace. Tato funkce se používá ke zpracování aktualizací konfigurace bez recyklace instancí rolí nebo restartování pracovního procesu.

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
Každý ze tří typů balíčků v kódu služby, konfiguraci a dat – obsahuje události, které upozorňují na instance služby při aktualizaci, přidání nebo odebrání balíčku. Služba může obsahovat více balíčků každého typu. Například služba může mít více konfiguračních balíčků, každou samostatnou verzi a upgrade. 

Tyto události jsou k dispozici pro využívání změn v balíčcích služeb bez restartování instance služby.

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
Úlohy po spuštění jsou akce, které se provádí před spuštěním aplikace. Spouštěcí úloha se obvykle používá ke spouštění skriptů pro instalaci pomocí zvýšených oprávnění. Cloud Services i Service Fabric podporují spouštění úloh. Hlavním rozdílem je to, že v Cloud Services je úloha po spuštění vázaná na virtuální počítač, protože je součástí instance role, zatímco v Service Fabric úloha po spuštění je vázaná na službu, která není vázaná na žádný konkrétní virtuální počítač.

| Service Fabric | Cloud Services |
| --- | --- |
| Umístění konfigurace |ServiceDefinition. csdef |
| Oprávnění |"omezené" nebo "zvýšené" |
| Sekvencování |"jednoduché", "pozadí", "popředí" |

### <a name="cloud-services"></a>Cloud Services
V Cloud Services je pro každou roli v ServiceDefinition. csdef nakonfigurovaný vstupní bod spouštění. 

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
V Service Fabric je pro každou službu v ServiceManifest.xml nakonfigurovaný vstupní bod spouštění:

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

## <a name="a-note-about-development-environment"></a>Poznámka k vývojovému prostředí
Cloud Services i Service Fabric jsou integrovány se sadou Visual Studio se šablonami projektů a podporují ladění, konfiguraci a nasazení jak místně, tak i do Azure. Cloud Services i Service Fabric také poskytují prostředí místního prostředí pro vývoj. Rozdílem je to, že zatímco modul runtime vývoje cloudové služby emuluje prostředí Azure, na kterém běží, Service Fabric nepoužívá emulátor – používá kompletní Service Fabric runtime. Prostředí Service Fabric, které spustíte na místním vývojovém počítači, je stejné jako prostředí, které běží v produkčním prostředí.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o Service Fabric Reliable Services a základních rozdílech mezi Cloud Services a Service Fabric aplikační architekturou, abyste pochopili, jak využít kompletní sadu Service Fabric funkcí.

* [Začínáme s Service Fabric Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Koncepční příručka k rozdílům mezi Cloud Services a Service Fabric](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
