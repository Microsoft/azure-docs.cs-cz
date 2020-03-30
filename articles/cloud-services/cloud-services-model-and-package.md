---
title: Co je model a balíček cloudových služeb | Dokumenty společnosti Microsoft
description: Popisuje model cloudové služby (.csdef, .cscfg) a balíček (.cspkg) v Azure
services: cloud-services
author: tanmaygore
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: 32603f4ab33e020245861e5dc66d2ade545fa627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247485"
---
# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>Co je model cloudové služby a jak ho zabalím?
Cloudová služba je vytvořena ze tří součástí, definice služby *(.csdef)*, konfigurace služby *(.cscfg)* a balíčku služby *(.cspkg)*. Soubory **ServiceDefinition.csdef** a **ServiceConfig.cscfg** jsou založeny na xml a popisují strukturu cloudové služby a její konfiguraci. model. **ServicePackage.cspkg** je soubor zip, který je generován z **ServiceDefinition.csdef** a mimo jiné obsahuje všechny požadované binární závislosti. Azure vytvoří cloudovou službu z **ServicePackage.cspkg** a **ServiceConfig.cscfg**.

Jakmile cloudová služba běží v Azure, můžete ji překonfigurovat prostřednictvím souboru **ServiceConfig.cscfg,** ale nemůžete změnit definici.

## <a name="what-would-you-like-to-know-more-about"></a>O čem byste chtěli vědět více?
* Chci se dozvědět více o [ServiceDefinition.csdef](#csdef) a [ServiceConfig.cscfg](#cscfg) soubory.
* Já už vím o tom, dej mi [nějaké příklady](#next-steps) o tom, co mohu nastavit.
* Chci vytvořit [ServicePackage.cspkg](#cspkg).
* Používám visual studio a chci...
  * [Vytvoření cloudové služby][vs_create]
  * [Změna konfigurace existující cloudové služby][vs_reconfigure]
  * [Nasazení projektu cloudové služby][vs_deploy]
  * [Vzdálená plocha do instance cloudové služby][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
Soubor **ServiceDefinition.csdef** určuje nastavení, která Azure používá ke konfiguraci cloudové služby. [Schéma definice služby Azure (soubor.csdef)](/previous-versions/azure/reference/ee758711(v=azure.100)) poskytuje povolený formát pro soubor definice služby. Následující příklad ukazuje nastavení, která lze definovat pro role webu a pracovního procesu:

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

Můžete odkazovat na [schéma definice služby](/previous-versions/azure/reference/ee758711(v=azure.100)) pro lepší pochopení schématu XML zde použité, ale zde je rychlé vysvětlení některých prvků:

**Lokality**  
Obsahuje definice webů nebo webových aplikací, které jsou hostovány v systému IIS7.

**Vstupní koncové body**  
Obsahuje definice pro koncové body, které se používají ke kontaktování cloudové služby.

**Vnitřní koncové body**  
Obsahuje definice pro koncové body, které jsou používány instance role ke vzájemné komunikaci.

**Nastavení konfigurace**  
Obsahuje definice nastavení pro funkce konkrétní role.

**Certifikáty**  
Obsahuje definice certifikátů, které jsou potřebné pro roli. Předchozí příklad kódu ukazuje certifikát, který se používá pro konfiguraci Azure Connect.

**Místní zdroje**  
Obsahuje definice pro prostředky místního úložiště. Prostředek místního úložiště je vyhrazený adresář v systému souborů virtuálního počítače, ve kterém je spuštěna instance role.

**Dovoz**  
Obsahuje definice pro importované moduly. Předchozí příklad kódu ukazuje moduly pro připojení ke vzdálené ploše a Azure Connect.

**Spuštění**  
Obsahuje úlohy, které jsou spuštěny při spuštění role. Úkoly jsou definovány v souboru CMD nebo spustitelném souboru.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
Konfigurace nastavení cloudové služby je určena hodnotami v souboru **ServiceConfiguration.cscfg.** Zadáte počet instancí, které chcete nasadit pro každou roli v tomto souboru. Hodnoty pro nastavení konfigurace, které jste definovali v souboru definice služby, budou přidány do konfiguračního souboru služby. Kryptografické otisky pro všechny certifikáty správy, které jsou přidruženy ke cloudové službě jsou také přidány do souboru. [Schéma konfigurace služby Azure (.cscfg File)](/previous-versions/azure/reference/ee758710(v=azure.100)) poskytuje povolený formát pro konfigurační soubor služby.

Konfigurační soubor služby není zabalen s aplikací, ale je odeslán do Azure jako samostatný soubor a slouží ke konfiguraci cloudové služby. Můžete nahrát nový konfigurační soubor služby bez opětovného nasazení cloudové služby. Hodnoty konfigurace pro cloudovou službu lze změnit, když je spuštěna cloudová služba. Následující příklad ukazuje nastavení konfigurace, které lze definovat pro role webu a pracovního procesu:

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

Můžete odkazovat na [schéma konfigurace služby](/previous-versions/azure/reference/ee758710(v=azure.100)) pro lepší pochopení schématu XML zde použité, ale zde je rychlé vysvětlení prvků:

**Instance**  
Konfiguruje počet spuštěných instancí pro roli. Chcete-li zabránit tomu, aby se vaše cloudová služba během upgradů potenciálně nestala nedostupnou, doporučujeme nasadit více než jednu instanci webových rolí. Nasazením více než jedné instance se při dodržování pokynů ve [smlouvě SLA (Azure Compute Service Level Agreement)](https://azure.microsoft.com/support/legal/sla/), která zaručuje 99,95 % externího připojení pro role orientované na Internet, když jsou pro službu nasazeny dvě nebo více instancí rolí.

**Nastavení konfigurace**  
Konfiguruje nastavení spuštěných instancí pro roli. Název `<Setting>` prvků musí odpovídat definicím nastavení v souboru definice služby.

**Certifikáty**  
Konfiguruje certifikáty, které služba používá. Předchozí příklad kódu ukazuje, jak definovat certifikát pro modul vzdáleného přístupu. Hodnota atributu *kryptografického otisku* musí být nastavena na kryptografický otisk certifikátu, který má být používán.

<p/>

> [!NOTE]
> Kryptografický otisk certifikátu lze přidat do konfiguračního souboru pomocí textového editoru. Nebo lze přidat hodnotu na kartě **Certifikáty** na stránce **Vlastnosti** role v sadě Visual Studio.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Definování portů pro instance rolí
Azure umožňuje pouze jeden vstupní bod do webové role. To znamená, že veškerý provoz probíhá prostřednictvím jedné ip adresy. Weby můžete nakonfigurovat tak, aby sdílely port, a to tak, že nakonfigurujete hlavičku hostitele tak, aby požadavek nasměrovala do správného umístění. Můžete také nakonfigurovat aplikace tak, aby naslouchaly známým portům na adrese IP.

Následující ukázka ukazuje konfiguraci webové role s webovou a webovou aplikací. Web je nakonfigurován jako výchozí umístění položky na portu 80 a webové aplikace jsou konfigurovány tak, aby přijímaly požadavky z hlavičky alternativního hostitele, která se nazývá "mail.mysite.cloudapp.net".

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
Konfiguraci cloudové služby můžete aktualizovat, když běží v Azure, aniž byste ji přepojili na offline. Chcete-li změnit informace o konfiguraci, můžete buď nahrát nový konfigurační soubor, nebo upravit konfigurační soubor na místě a použít jej na spuštěnou službu. V konfiguraci služby lze provést následující změny:

* **Změna hodnot nastavení konfigurace**  
  Když se změní nastavení konfigurace, instance role může použít změnu, když je instance online, nebo řádně recyklovat instanci a použít změnu, když je instance offline.
* **Změna topologie služby instancí rolí**  
  Změny topologie nemají vliv na spuštěné instance, s výjimkou případů, kdy je instance odebírána. Všechny zbývající instance obecně není nutné recyklovat; můžete však recyklovat instance rolí v reakci na změnu topologie.
* **Změna kryptografického otisku certifikátu**  
  Certifikát lze aktualizovat pouze v případě, že je instance role offline. Pokud je certifikát přidán, odstraněn nebo změněn, když je instance role online, Azure řádně přenese instanci do režimu offline, aby aktualizoval certifikát a po dokončení změny jej přepne zpět do režimu online.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Zpracování změn konfigurace pomocí událostí běhu služby
[Knihovna Azure Runtime library](/previous-versions/azure/reference/mt419365(v=azure.100)) zahrnuje obor názvů [Microsoft.WindowsAzure.ServiceRuntime,](/previous-versions/azure/reference/ee741722(v=azure.100)) který poskytuje třídy pro interakci s prostředím Azure z role. Třída [RoleEnvironment](/previous-versions/azure/reference/ee773173(v=azure.100)) definuje následující události, které jsou vyvolány před a po změně konfigurace:

* **[Změna](/previous-versions/azure/reference/ee758134(v=azure.100)) události**  
  K tomu dochází před změnou konfigurace se použije na zadanou instanci role dává možnost sundat instance role v případě potřeby.
* **[Změněná](/previous-versions/azure/reference/ee758129(v=azure.100)) událost**  
  Vyvolá se po použití změny konfigurace na zadanou instanci role.

> [!NOTE]
> Vzhledem k tomu, že změny certifikátu vždy přenesou instance role do offline, nevyvolávají události RoleEnvironment.Changing nebo RoleEnvironment.Changed.
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
> [!NOTE]
> Maximální velikost balíčku, který lze nasadit, je 600 MB

Chcete-li nasadit aplikaci jako cloudovou službu v Azure, musíte nejprve zabalit aplikaci v příslušném formátu. Pomocí nástroje příkazového řádku **CSPack** (nainstalovaného pomocí [sady Azure SDK](https://azure.microsoft.com/downloads/)) můžete vytvořit soubor balíčku jako alternativu k sadě Visual Studio.

**CsPack** používá obsah souboru definice služby a konfiguračnísoubor služby k definování obsahu balíčku. **CSPack** generuje soubor balíčku aplikace (.cspkg), který můžete nahrát do Azure pomocí [portálu Azure](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Ve výchozím nastavení je `[ServiceDefinitionFileName].cspkg`balíček pojmenován , ale můžete `/out` zadat jiný název pomocí možnosti **CSPack**.

**CSPack** se nachází na adrese  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> CSPack.exe (v systému Windows) je k dispozici spuštěním zástupce **příkazového řádku Microsoft Azure,** který je nainstalován s sadou SDK.  
> 
> Spusťte program CSPack.exe sám zobrazit dokumentaci o všech možných přepínačů a příkazů.
> 
> 

<p />

> [!TIP]
> Spusťte cloudovou službu místně v **emulátoru Microsoft Azure Compute Emulátor**, použijte možnost **/copyonly.** Tato možnost zkopíruje binární soubory pro aplikaci do rozložení adresáře, ze kterého mohou být spuštěny v emulátoru výpočetního prostředí.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Příklad příkazu pro balíček cloudové služby
Následující příklad vytvoří balíček aplikace, který obsahuje informace pro webovou roli. Příkaz určuje soubor definice služby, který má být používán, adresář, kde lze najít binární soubory, a název souboru balíčku.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Pokud aplikace obsahuje webovou roli i roli pracovního procesu, použije se následující příkaz:

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

Pokud jsou proměnné definovány takto:

| Proměnná | Hodnota |
| --- | --- |
| \[Název adresáře\] |Podadresář pod kořenovým adresářem projektu, který obsahuje soubor .csdef projektu Azure. |
| \[Definice služby\] |Název souboru definice služby. Ve výchozím nastavení se tento soubor nazývá ServiceDefinition.csdef. |
| \[Název výstupního souboru\] |Název generovaného souboru balíčku. Obvykle je to nastaveno na název aplikace. Pokud není zadán žádný název souboru, \[balíček aplikace je vytvořen jako ApplicationName\].cspkg. |
| \[RoleName (Název role)\] |Název role, jak je definována v souboru definice služby. |
| \[RoleBinariesDirectory] |Umístění binárních souborů pro roli. |
| \[Virtualpath\] |Fyzické adresáře pro každou virtuální cestu definovanou v části Weby v definici služby. |
| \[PhysicalPath\] |Fyzické adresáře obsahu pro každou virtuální cestu definovanou v uzlu webu definice služby. |
| \[Název_sestavení role\] |Název binárního souboru pro roli. |

## <a name="next-steps"></a>Další kroky
Vytvářím balíček cloudových služeb a chci...

* [Nastavení vzdálené plochy pro instanci cloudové služby][remotedesktop]
* [Nasazení projektu cloudové služby][deploy]

Používám visual studio a chci...

* [Vytvoření nové cloudové služby][vs_create]
* [Změna konfigurace existující cloudové služby][vs_reconfigure]
* [Nasazení projektu cloudové služby][vs_deploy]
* [Nastavení vzdálené plochy pro instanci cloudové služby][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: cloud-services-role-enable-remote-desktop-visual-studio.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md



