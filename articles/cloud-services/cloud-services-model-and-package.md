---
title: Co je balíček a model cloudové služby | Dokumentace Microsoftu
description: Popisuje balíček (.cspkg) v Azure a model cloudové služby (.csdef, .cscfg)
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 4ce2feb5-0437-496c-98da-1fb6dcb7f59e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: 7e43a32a415e58925bda5195b3943afca315f9be
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238178"
---
# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>Jaký je model cloudové služby a jak ho balíček?
Cloudové služby je vytvořen z tři komponenty, definice služby *(.csdef)*, konfigurace služby *(.cscfg)* a balíček služby *(.cspkg)*. Oba **ServiceDefinition.csdef** a **ServiceConfig.cscfg** soubory jsou založené na XML a popisují strukturu cloudové služby a jak je nakonfigurován; nazývají modelu. **ServicePackage.cspkg** je soubor zip, který je generován z **ServiceDefinition.csdef** a mimo jiné obsahuje všechny požadované závislosti založené na binární soubor. Azure vytvoří cloudovou službu z obou **ServicePackage.cspkg** a **ServiceConfig.cscfg**.

Jakmile je Cloudová služba běží v Azure, můžete změnit konfiguraci prostřednictvím **ServiceConfig.cscfg** soubor, ale nelze změnit definici.

## <a name="what-would-you-like-to-know-more-about"></a>Co byste chtěli dozvědět víc o?
* Chci vědět více o [ServiceDefinition.csdef](#csdef) a [ServiceConfig.cscfg](#cscfg) soubory.
* Už vím o tom, dejte mi [příklady](#next-steps) na Co můžu nakonfigurovat.
* Chci vytvořit [ServicePackage.cspkg](#cspkg).
* Používám Visual Studio a chci...
  * [Vytvořit cloudovou službu][vs_create]
  * [Překonfigurovat existující cloudovou službu][vs_reconfigure]
  * [Nasadit projekt cloudové služby][vs_deploy]
  * [Instance cloudové služby pomocí vzdálené plochy][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
**ServiceDefinition.csdef** soubor Určuje nastavení, které Azure používá ke konfiguraci cloudové služby. [Azure schématu definice služby (.csdef souboru)](https://msdn.microsoft.com/library/azure/ee758711.aspx) povolený formát stanoví definiční soubor služby. Následující příklad ukazuje nastavení, které lze definovat pro webové a pracovní role:

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

Můžete se podívat do [schématu definice služby](https://msdn.microsoft.com/library/azure/ee758711.aspx) pro lepší přehled schématu XML se tady použít, ale tady je rychlý vysvětlení některých prvků:

**Weby**  
Obsahuje definice pro weby nebo webové aplikace, které jsou hostované ve službě IIS7.

**InputEndpoints**  
Obsahuje definice pro koncové body, které se používají ke kontaktování cloudové služby.

**InternalEndpoints**  
Obsahuje definice pro koncové body, které jsou používány instancí role ke komunikaci mezi sebou.

**ConfigurationSettings**  
Obsahuje nastavení definice funkcí konkrétní role.

**Certifikáty**  
Obsahuje definice pro certifikáty, které jsou potřeba pro roli. Předchozí příklad kódu ukazuje certifikát, který se používá pro konfiguraci Azure připojit.

**LocalResources**  
Obsahuje definice pro prostředky místní úložiště. Místní úložiště prostředků je vyhrazené adresáře v systému souborů virtuálního počítače, ve kterém je spuštěna instance role.

**Importy**  
Obsahuje definice pro importované moduly. Předchozí příklad kódu ukazuje moduly pro připojení ke vzdálené ploše a připojení k Azure.

**Po spuštění**  
Obsahuje úlohy, které jsou spouštěny, když se role spustila. Úkoly jsou definovány v .cmd nebo spustitelný soubor.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>Soubor ServiceConfiguration.cscfg
Konfigurace nastavení pro cloudovou službu se určuje podle hodnoty **souboru ServiceConfiguration.cscfg** souboru. Zadáte počet instancí, které chcete nasadit pro každou roli v tomto souboru. Hodnoty pro nastavení konfigurace, které jste definovali v definičním souboru služby jsou přidány do konfiguračního souboru služby. Kryptografický otisk pro jakékoli certifikáty pro správu, které jsou spojené s cloudovou službou přidají také do souboru. [Schématu konfigurace služby Azure (.cscfg souboru)](https://msdn.microsoft.com/library/azure/ee758710.aspx) poskytuje povolený formát konfiguračního souboru služby.

Konfigurační soubor služby není zabalena v aplikaci, ale je nahráli do Azure jako samostatný soubor a slouží ke konfiguraci cloudové služby. Můžete nahrát nový soubor konfigurace služby bez opětovného nasazení cloudové služby. Hodnoty konfigurace pro cloudovou službu můžete změnit, když běží v cloudové službě. Následující příklad ukazuje nastavení konfigurace, které lze definovat pro webové a pracovní role:

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

Můžete se podívat do [schématu konfigurace služby](https://msdn.microsoft.com/library/azure/ee758710.aspx) pro lepší pochopení schématu XML se tady použít, ale tady je rychlý vysvětlení prvky:

**Instance**  
Konfiguruje počet spuštěných instancí role. Abyste zabránili potenciálně nedostupností během upgradu vaší cloudové služby, se doporučuje nasadit více než jeden výskyt určených pro webové role. Nasazení více než jednu instanci, jsou splněny pokyny v [Azure Compute služby smlouva o úrovni (SLA)](https://azure.microsoft.com/support/legal/sla/), což zaručuje externí připojení v 99,95 % pro role přístupem k Internetu, pokud dvě nebo více instancí role jsou nasadit službu.

**ConfigurationSettings**  
Konfiguruje nastavení pro spuštěné instance role. Název `<Setting>` prvky musí odpovídat definici nastavení v definičním souboru služby.

**Certifikáty**  
Nakonfiguruje certifikáty používané službou. Předchozí příklad kódu ukazuje, jak definovat certifikát pro modul vzdáleného přístupu. Hodnota *kryptografický otisk* atribut musí být nastaven na kryptografický otisk certifikátu pro použití.

<p/>

> [!NOTE]
> Kryptografický otisk certifikátu je přidat do konfiguračního souboru pomocí textového editoru. Nebo hodnoty lze přidat na **certifikáty** karty **vlastnosti** stránce role v sadě Visual Studio.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Definování porty pro instance rolí
Azure umožňuje pouze jeden vstupní bod do webové role. To znamená, že dojde k veškerý provoz přes jednu IP adresu. Můžete nakonfigurovat své weby pro sdílení portu nakonfigurováním hlavičku hostitele pro přesměrování požadavku do správného umístění. Můžete také nakonfigurovat tak, aby naslouchala na dobře známé porty u IP adresy vašich aplikací.

Následující příklad ukazuje konfiguraci pro webovou roli webu a webových aplikací. Web je nakonfigurován jako výchozí umístění pro položku na portu 80 a webových aplikací jsou nakonfigurované pro příjem požadavků z alternativním hostiteli hlavičky, která se nazývá "mail.mysite.cloudapp.net".

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
      <Binding name="mail" endpointName="HttpIn" hostheader="mail.mysite.cloudapp.net" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>Změna konfigurace role
Když je spuštěný v Azure, bez nutnosti převádět služby v offline režimu doporučí aktualizovat konfiguraci cloudové služby. Chcete-li změnit informace o konfiguraci, můžete nahrát nový soubor konfigurace nebo upravit konfigurační soubor na místě a použijte ji pro spuštěnou službu. Ke konfiguraci služby můžete provést následující změny:

* **Změna hodnoty nastavení konfigurace**  
  Při nastavení změny konfigurace, instanci role můžete zvolit použití změny instance je online, nebo recyklovat instance bez výpadku a použití změn při instanci je offline.
* **Změna topologie služby instance role**  
  Změny topologie neovlivní spuštěné instance, s výjimkou případů, kdy se odebírá instance. Všechny zbývající instance obvykle není nutné k provedení recyklace; Můžete však recyklovat instance rolí v reakci na změnu topologie.
* **Změna kryptografický otisk certifikátu**  
  Certifikát lze aktualizovat pouze pokud je role instance offline. Pokud certifikát přidat, odstranit nebo změnit instanci role je online, Azure řádně trvá instance offline aktualizace certifikátu a pak zpátky online po dokončení změny.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Zpracování změny konfigurace s událostmi služby modulu Runtime
[Knihovny prostředí Runtime Azure](https://msdn.microsoft.com/library/azure/mt419365.aspx) zahrnuje [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.aspx) obor názvů, který poskytuje třídy pro interakci s prostředím Azure z role. [RoleEnvironment](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) třída definuje následující události, které jsou vyvolány před a po změně konfigurace:

* **[Změna](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx) událostí**  
  K tomu dojde, než se do zadané instance role s možností až shodí instance rolí v případě potřeby zavádí změna konfigurace.
* **[Změnit](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changed.aspx) událostí**  
  Nastane, jakmile se k zadané instanci role zavádí změna konfigurace.

> [!NOTE]
> Protože změny certifikátů je instancí role vždy převést do režimu offline, jejich nevyvolávejte RoleEnvironment.Changing nebo RoleEnvironment.Changed událostí.
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
Pokud chcete nasadit aplikace jako cloudové služby v Azure, musí nejprve zabalení aplikace ve správném formátu. Můžete použít **CSPack** nástroj příkazového řádku (součástí [sady Azure SDK](https://azure.microsoft.com/downloads/)) k vytvoření souboru balíčku jako alternativu k sadě Visual Studio.

**CSPack** pomocí obsah definiční soubor služby a konfigurační soubor služby definuje obsah balíčku. **CSPack** generuje soubor balíčku aplikace (.cspkg), který můžete nahrát do Azure s použitím [webu Azure portal](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Ve výchozím nastavení, je balíček s názvem `[ServiceDefinitionFileName].cspkg`, ale můžete zadat jiný název pomocí `/out` možnost **CSPack**.

**CSPack** se nachází v  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> Po spuštění je k dispozici CSPack.exe (v systému windows) **příkazového řádku Microsoft Azure** zástupce, který je nainstalován se sadou SDK.  
> 
> Spusťte CSPack.exe program samostatně najdete v dokumentaci o všech možných přepínače a příkazy.
> 
> 

<p />

> [!TIP]
> Spustit místně do cloudové služby **emulátoru Microsoft Azure Compute**, použijte **/copyonly** možnost. Tato možnost zkopíruje binární soubory pro aplikaci do rozložení adresáře, ze kterého bylo možné spouštět v emulátoru služby compute.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Ukázkový příkaz pro balíček cloudové služby
Následující příklad vytvoří balíček aplikace, který obsahuje informace pro webovou roli. Tento příkaz Určuje definiční soubor služby používat adresář, kde můžete najít binární soubory, a názvem souboru balíčku.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Pokud aplikace obsahuje webovou roli a roli pracovního procesu, je pomocí následujícího příkazu:

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
| \[NazevAdresare\] |Podadresář kořenovém adresáři projektu, která obsahuje soubor .csdef Azure projektu. |
| \[ServiceDefinition\] |Název souboru definice služby. Ve výchozím nastavení je tento soubor s názvem ServiceDefinition.csdef. |
| \[OutputFileName\] |Název souboru pro vygenerovaný balíček. To obvykle nastavena na název aplikace. Pokud není zadán žádný název souboru, jako je vytvořen balíček aplikace \[ApplicationName\].cspkg. |
| \[RoleName\] |Název role, jak je definováno v definičním souboru služby. |
| \[RoleBinariesDirectory] |Umístění binárních souborů pro tuto roli. |
| \[VirtualPath\] |Fyzických adresářů pro jednotlivé virtuální cesty definované v části webů v definici služby. |
| \[PhysicalPath\] |Fyzické adresáře obsah pro každý virtuální cestu definovanou v uzlu Web definice služby. |
| \[RoleAssemblyName\] |Název binárního souboru pro danou roli. |

## <a name="next-steps"></a>Další postup
Vytvářím balíček cloudové služby a chci...

* [Nastavení vzdálené plochy pro instanci cloudové služby][remotedesktop]
* [Nasadit projekt cloudové služby][deploy]

Používám Visual Studio a chci...

* [Vytvořit novou cloudovou službu][vs_create]
* [Překonfigurovat existující cloudovou službu][vs_reconfigure]
* [Nasadit projekt cloudové služby][vs_deploy]
* [Nastavení vzdálené plochy pro instanci cloudové služby][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: cloud-services-role-enable-remote-desktop-visual-studio.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md
