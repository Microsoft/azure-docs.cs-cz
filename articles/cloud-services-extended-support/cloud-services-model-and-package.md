---
title: Co je model a balíček Azure Cloud Service (Extended Support)
description: Popisuje model cloudové služby (Extended Support) (. csdef,. cscfg) a Package (. cspkg) v Azure.
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 3baea92d78cf15c35d2fb36692dab1edfd950699
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98744333"
---
# <a name="what-is-the-azure-cloud-service-model-and-how-do-i-package-it"></a>Co je model cloudové služby Azure a jak ho mám zabalit?

Cloudová služba se vytvoří ze tří součástí, definice služby *(. csdef)*, konfigurace služby *(. cscfg)* a balíčku služby *(. cspkg)*. Soubory **ServiceDefinition. csdef** a **ServiceConfig. cscfg** jsou založené na jazyce XML a popisují strukturu cloudové služby a způsob jejich konfigurace. souhrnně označovaný jako model. **ServicePack. cspkg** je soubor zip, který je generován z **ServiceDefinition. csdef** a mimo jiné, obsahuje všechny požadované binární závislosti. Azure vytvoří cloudovou službu z nástroje **ServicePack. cspkg** a **ServiceConfig. cscfg**.

Jakmile je cloudová služba spuštěná v Azure, můžete ji překonfigurovat prostřednictvím souboru **ServiceConfig. cscfg** , ale nemůžete definici změnit.

## <a name="what-would-you-like-to-know-more-about"></a>K čemu se chcete dozvědět více?
* Chci vědět více o souborech [ServiceDefinition. csdef](#csdef) a [ServiceConfig. cscfg](#cscfg) .
* Už o tom poznáte, podávají [několik příkladů](#next-steps) , jak můžu nakonfigurovat.
* Chci vytvořit [ServicePack. cspkg](#cspkg).
* Používám aplikaci Visual Studio a chci...
  * [Vytvoření cloudové služby] [vs_create]
  * [Změna konfigurace existující cloudové služby] [vs_reconfigure]
  * [Nasazení projektu cloudové služby] [vs_deploy]
  * [Vzdálená plocha do instance cloudové služby] [remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition. csdef
Soubor **ServiceDefinition. csdef** určuje nastavení, která Azure používá ke konfiguraci cloudové služby. [Schéma definice služby Azure (soubor. csdef)](schema-csdef-file.md) poskytuje povolený formát pro soubor definice služby. Následující příklad ukazuje nastavení, která lze definovat pro webovou roli a role pracovního procesu:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

Pro lepší porozumění schématu XML, které se tady používá, můžete použít [schéma definice služby](schema-csdef-file.md), ale tady je stručné vysvětlení některých prvků:

**Lokality**  
Obsahuje definice pro weby nebo webové aplikace, které jsou hostovány v IIS7.

**InputEndpoints**  
Obsahuje definice pro koncové body, které se používají ke kontaktování cloudové služby.

**InternalEndpoints**  
Obsahuje definice pro koncové body, které používají instance rolí ke vzájemné komunikaci.

**ConfigurationSettings**  
Obsahuje definice nastavení pro funkce konkrétní role.

**Certifikáty**  
Obsahuje definice certifikátů, které jsou potřeba pro roli. Předchozí příklad kódu ukazuje certifikát, který se používá pro konfiguraci Azure Connect.

**LocalResources**  
Obsahuje definice místních prostředků úložiště. Prostředek místního úložiště je rezervovaný adresář v systému souborů virtuálního počítače, ve kterém je spuštěná instance role.

**Objem**  
Obsahuje definice pro importované moduly. Předchozí příklad kódu ukazuje moduly pro Připojení ke vzdálené ploše a Azure Connect.

**Spuštění**  
Obsahuje úlohy, které se spouštějí při spuštění role. Úkoly jsou definovány v souboru. cmd nebo ve spustitelném souboru.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration. cscfg
Konfigurace nastavení pro cloudovou službu je určena hodnotami v souboru **ServiceConfiguration. cscfg** . Zadejte počet instancí, které chcete pro každou roli v tomto souboru nasadit. Hodnoty nastavení konfigurace, které jste definovali v definičním souboru služby, se přidají do konfiguračního souboru služby. Do souboru jsou přidány také kryptografické otisky pro všechny certifikáty pro správu, které jsou přidruženy ke cloudové službě. [Schéma konfigurace služby Azure (soubor. cscfg)](schema-cscfg-file.md) poskytuje povolený formát pro konfigurační soubor služby.

Konfigurační soubor služby není zabalený do aplikace, ale do Azure se nahraje jako samostatný soubor, který se používá ke konfiguraci cloudové služby. Můžete nahrát nový konfigurační soubor služby, aniž byste museli znovu nasazovat cloudovou službu. Konfigurační hodnoty pro cloudovou službu je možné změnit i v případě, že je cloudová služba spuštěná. Následující příklad ukazuje nastavení konfigurace, která lze definovat pro webovou roli a role pracovního procesu:

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Můžete se podívat na [schéma konfigurace služby](schema-cscfg-file.md) pro lepší porozumění schématu XML, které se tady používá, ale tady je rychlé vysvětlení těchto elementů:

**Instance**  
Konfiguruje počet spuštěných instancí této role. Aby nedocházelo k nedostupnosti cloudové služby během upgradů, doporučujeme nasadit více než jednu instanci webových rolí. Nasazením víc než jedné instance dodržujete pokyny ve [službě Azure compute smlouva SLA (SLA)](https://azure.microsoft.com/support/legal/sla/), která garantuje 99,95% externí konektivitu pro internetové role, když se pro službu nasadí dvě nebo víc instancí rolí.

**ConfigurationSettings**  
Konfiguruje nastavení pro spuštěné instance role. Název `<Setting>` elementů se musí shodovat s definicemi nastavení v definičním souboru služby.

**Certifikáty**  
Nakonfiguruje certifikáty používané službou. Předchozí příklad kódu ukazuje, jak definovat certifikát pro modul RemoteAccess. Hodnota atributu *kryptografického otisku* musí být nastavená na kryptografický otisk certifikátu, který se má použít.

<p/>

> [!NOTE]
> Kryptografický otisk certifikátu lze přidat do konfiguračního souboru pomocí textového editoru. Nebo lze hodnotu přidat na kartě **certifikáty** stránky **vlastnosti** role v aplikaci Visual Studio.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Definování portů pro instance rolí
Azure umožňuje webové roli jenom jeden vstupní bod. To znamená, že veškerý provoz probíhá přes jednu IP adresu. Své weby můžete nakonfigurovat tak, aby sdílely port tím, že nakonfigurujete hlavičku hostitele, aby požadavek směroval na správné místo. Můžete také nakonfigurovat své aplikace tak, aby naslouchaly dobře známým portům na IP adrese.

Následující ukázka ukazuje konfiguraci webové role s webem a webovou aplikací. Web je nakonfigurovaný jako výchozí umístění vstupu na portu 80 a webové aplikace jsou nakonfigurované tak, aby přijímaly požadavky z alternativní hlavičky hostitele, která se nazývá "mail.mysite.cloudapp.net".

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" port="80" />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" hostHeader="mail.mysite.cloudapp.net" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>Změna konfigurace role
Konfiguraci cloudové služby můžete aktualizovat, když běží v Azure, aniž byste museli službu přebírat offline. Chcete-li změnit informace o konfiguraci, můžete buď nahrát nový konfigurační soubor, nebo upravit konfigurační soubor na místě a použít ho pro spuštěnou službu. V konfiguraci služby je možné provést následující změny:

* **Změna hodnot nastavení konfigurace**  
  Když se změní nastavení konfigurace, může instance role zvolit, aby se změna projevila, když je instance online, nebo aby se instance korektně recykloval, a změny se projeví, když je instance offline.
* **Změna topologie služby instancí rolí**  
  Změny topologie neovlivňují spuštěné instance s výjimkou případů, kdy je instance odebrána. Všechny zbývající instance obvykle není nutné recyklovat; Můžete ale zvolit recyklaci instancí rolí v reakci na změnu topologie.
* **Změna kryptografického otisku certifikátu**  
  Certifikát lze aktualizovat pouze v případě, že je instance role v režimu offline. Pokud dojde k přidání, odstranění nebo změně certifikátu, když je instance role online, Azure bez problémů převezme instanci v režimu offline, aby aktualizovala certifikát, a po dokončení změny ho vrátí zpátky do online režimu.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Zpracování změn konfigurace s událostmi modulu runtime služby
Knihovna runtime Azure obsahuje obor názvů Microsoft. WindowsAzure. ServiceRuntime, který poskytuje třídy pro interakci s prostředím Azure z role. Třída RoleEnvironment definuje následující události, které jsou vyvolány před a po změně konfigurace:

* **Změna události**  
  K tomu dojde předtím, než se změna konfigurace použije u zadané instance role, což vám umožní v případě potřeby pořídit instance rolí.
* **Událost změny**  
  Vyvolá se po použití změny konfigurace u zadané instance role.

> [!NOTE]
> Vzhledem k tomu, že změny certifikátu vždy přebírají instance role v režimu offline, nevyvolávají RoleEnvironment. Změna nebo RoleEnvironment. změněné události.
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePack. cspkg
> [!NOTE]
> Maximální velikost balíčku, která se dá nasadit, je 600MB.

Pokud chcete nasadit aplikaci jako cloudovou službu v Azure, musíte nejdřív aplikaci zabalit v příslušném formátu. Pomocí nástroje příkazového řádku **CSPack** (nainstalovaného se sadou [Azure SDK](https://azure.microsoft.com/downloads/)) můžete vytvořit soubor balíčku jako alternativu k sadě Visual Studio.

**CSPack** používá obsah souboru definice služby a konfiguračního souboru služby k definování obsahu balíčku. **CSPack** vygeneruje soubor balíčku aplikace (. cspkg), který můžete nahrát do Azure pomocí [Azure Portal](../cloud-services/cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Ve výchozím nastavení je balíček pojmenován `[ServiceDefinitionFileName].cspkg` , ale můžete zadat jiný název pomocí `/out` možnosti **CSPack**.

**CSPack** se nachází na  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> CSPack.exe (ve Windows) je k dispozici spuštěním zástupce **příkazového řádku Microsoft Azure** , který se instaluje se sadou SDK.  
> 
> Spusťte program CSPack.exe sám sebou, abyste viděli dokumentaci o všech možných přepínačích a příkazech.
> 
> 

<p />

> [!TIP]
> Spusťte cloudovou službu místně v **emulátoru Microsoft Azure COMPUTE**, použijte možnost **/CopyOnly** . Tato možnost zkopíruje binární soubory aplikace do rozložení adresáře, ze kterého mohou být spuštěny v emulátoru služby Compute.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Příklad příkazu pro zabalení cloudové služby
Následující příklad vytvoří balíček aplikace, který obsahuje informace pro webovou roli. Příkaz Určuje definiční soubor služby, který se má použít, adresář, ve kterém se mají najít binární soubory, a název souboru balíčku.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Pokud aplikace obsahuje webovou roli a roli pracovního procesu, použije se tento příkaz:

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

Kde proměnné jsou definovány takto:

| Proměnná | Hodnota |
| --- | --- |
| \[DirectoryName\] |Podadresář v kořenovém adresáři projektu, který obsahuje soubor. csdef projektu Azure. |
| \[ServiceDefinition\] |Název definičního souboru služby. Ve výchozím nastavení má tento soubor název ServiceDefinition. csdef. |
| \[OutputFileName\] |Název vygenerovaného souboru balíčku. Obvykle je tato nastavení nastavena na název aplikace. Pokud není zadán žádný název souboru, vytvoří se balíček aplikace jako \[ ApplicationName \] . cspkg. |
| \[RoleName (Název role)\] |Název role definovaný v definičním souboru služby. |
| \[RoleBinariesDirectory] |Umístění binárních souborů pro roli. |
| \[VirtualPath\] |Fyzické adresáře pro každou virtuální cestu definovanou v části lokalita definice služby. |
| \[PhysicalPath\] |Fyzické adresáře obsahu pro každou virtuální cestu definovanou v uzlu lokalita definice služby. |
| \[RoleAssemblyName\] |Název binárního souboru pro roli. |

## <a name="next-steps"></a>Další kroky 
- Přečtěte si [požadavky na nasazení](deploy-prerequisite.md) pro Cloud Services (Rozšířená podpora).
- Nasaďte cloudovou službu (rozšířenou podporu) pomocí [Azure Portal](deploy-portal.md), [PowerShellu](deploy-powershell.md), [šablony](deploy-template.md) nebo sady [Visual Studio](deploy-visual-studio.md).
- Přečtěte si [Nejčastější dotazy](faq.md) k Cloud Services (Rozšířená podpora).

