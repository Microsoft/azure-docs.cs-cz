---
title: "Co je Cloudová služba modelu a balíček | Microsoft Docs"
description: "Popisuje model cloudové služby (.csdef, .cscfg) a balíčku (.cspkg) v Azure"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 4ce2feb5-0437-496c-98da-1fb6dcb7f59e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: 0589f2efeaaafc35bcb9d869c391a0533fe6e502
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>Co je Cloudová služba modelu a jak ho balíček?
Cloudová služba je vytvořená z tří součástí definice služby *(.csdef)*, konfigurace služby *(.cscfg)*a balíček služby *(.cspkg)*. Obě **ServiceDefinition.csdef** a **ServiceConfig.cscfg** soubory formátu XML a popisují strukturu cloudové služby a jak jsou nakonfigurované; se nazývají modelu. **ServicePackage.cspkg** je soubor zip, který se generují z **ServiceDefinition.csdef** a mimo jiné obsahuje všechny požadované závislosti na základě binární. Azure vytvoří cloudové služby i **ServicePackage.cspkg** a **ServiceConfig.cscfg**.

Jakmile Cloudová služba běží v Azure, můžete ji prostřednictvím překonfigurovat **ServiceConfig.cscfg** soubor, ale nelze změnit definici.

## <a name="what-would-you-like-to-know-more-about"></a>Co chcete vědět více o?
* Chci vědět více o [ServiceDefinition.csdef](#csdef) a [ServiceConfig.cscfg](#cscfg) soubory.
* Již vědět o, mě [některé příklady](#next-steps) na Co mám nakonfigurovat.
* Vytvořit [ServicePackage.cspkg](#cspkg).
* Používám Visual Studio a chcete...
  * [Vytvoření cloudové služby][vs_create]
  * [Znovu nakonfigurujte stávající cloudovou službu][vs_reconfigure]
  * [Nasazení projektu cloudové služby][vs_deploy]
  * [Vzdálená plocha do instance cloudové služby][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
**ServiceDefinition.csdef** souboru Určuje nastavení, které používají Azure ke konfigurování cloudové služby. [Azure schématu definice služby (.csdef souboru)](https://msdn.microsoft.com/library/azure/ee758711.aspx) poskytuje povolený formát souboru definice služby. Následující příklad ukazuje nastavení, které lze definovat pro webové a pracovní role:

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

Můžete se podívat do [služby definice schématu](https://msdn.microsoft.com/library/azure/ee758711.aspx) lépe porozumět schématu XML použít se zde, ale tady je rychlý vysvětlení některých prvků:

**weby**  
Obsahuje definice pro weby nebo webové aplikace, které jsou hostované ve službě IIS7.

**InputEndpoints**  
Obsahuje definice pro koncové body, které se používají ke kontaktování cloudové služby.

**InternalEndpoints**  
Obsahuje definice pro koncové body, které jsou používány instance rolí pro komunikaci mezi sebou.

**ConfigurationSettings**  
Obsahuje definice nastavení pro funkce určité role.

**Certifikáty**  
Obsahuje definice pro certifikáty, které jsou potřebné pro roli. Předchozí příklad kódu ukazuje certifikát, který se používá pro konfiguraci Azure připojit.

**LocalResources**  
Obsahuje definice pro místní prostředky pro úložiště. Prostředek Místní úložiště je vyhrazené adresáře v systému souborů virtuálního počítače, ve kterém je spuštěna instance role.

**Importy**  
Obsahuje definice pro importovaných modulů. Předchozí příklad kódu ukazuje moduly pro připojení ke vzdálené ploše a Azure připojit.

**Spuštění**  
Obsahuje úlohy, které se spustí při spuštění role. Úkoly jsou definovány v .cmd nebo spustitelný soubor.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
Konfigurace nastavení pro cloudové služby je určen podle hodnot v **souboru ServiceConfiguration.cscfg** souboru. Můžete zadat počet instancí, které chcete nasadit pro každou roli v tomto souboru. Hodnoty pro nastavení konfigurace, která jste zadali v souboru definice služby se přidají do konfiguračního souboru služby. Kryptografické otisky pro všechny certifikáty pro správu, které jsou spojeny s cloudovou službou jsou rovněž přidány do souboru. [Schéma konfigurace služby Azure (.cscfg souboru)](https://msdn.microsoft.com/library/azure/ee758710.aspx) poskytuje povolený formát pro konfigurační soubor služby.

Konfigurační soubor služby není spojených s aplikací, ale nahraje do Azure jako samostatný soubor a slouží ke konfiguraci cloudové služby. Můžete nahrát nový soubor konfigurace služby bez opětovného nasazení cloudové služby. Hodnoty konfigurace pro cloudové služby lze změnit, když běží v cloudové službě. Následující příklad ukazuje nastavení konfigurace, které lze definovat pro webové a pracovní role:

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

Můžete se podívat do [schéma konfigurace služby](https://msdn.microsoft.com/library/azure/ee758710.aspx) pro lepší pochopení schématu XML použít se zde, ale tady je rychlý vysvětlení elementů:

**Instance**  
Konfiguruje počet spuštěných instancí role. Abyste zabránili potenciálně stává stále k dispozici při upgradech cloudové služby, doporučujeme nasadit více než jednu instanci směřujících webové role. Nasazením více než jednu instanci, jsou splněny podle pokynů v [Azure výpočetní služby smlouvou o úrovni (SLA)](http://azure.microsoft.com/support/legal/sla/), což zaručuje 99,95 % externí připojení internetových rolí, pokud dvě nebo více instancí role jsou nasazeny pro službu.

**ConfigurationSettings**  
Konfiguruje nastavení pro spuštěné instance role. Název `<Setting>` elementy se musí shodovat definice nastavení v definičním souboru služby.

**Certifikáty**  
Nakonfiguruje certifikáty, které používají službu. Předchozí příklad kódu ukazuje, jak definovat certifikát pro modul vzdáleného přístupu. Hodnota *kryptografický otisk* musí být nastaven na kryptografický otisk certifikátu používat.

<p/>

> [!NOTE]
> Kryptografický otisk certifikátu lze přidat do konfiguračního souboru pomocí textového editoru. Nebo hodnota lze přidat na **certifikáty** kartě **vlastnosti** stránky role v sadě Visual Studio.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Definování porty pro instance rolí
Azure umožňuje pouze jeden vstupní bod do webové role. Znamená, že dojde k všechny přenosy přes jednu IP adresu. Můžete nakonfigurovat své weby pro sdílení port nakonfigurováním hlavičku hostitele pro směrování požadavku na správné místo. Můžete také nakonfigurovat aplikace tak, aby naslouchala na dobře známé porty IP adresu.

Následující příklad ukazuje konfiguraci pro webové role s webových stránek a webových aplikací. Web je nakonfigurován jako výchozí umístění položky na portu 80 a webových aplikací konfigurovány tak, aby přijímal požadavky z hlavičku alternativním hostiteli, který se nazývá "mail.mysite.cloudapp.net".

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
Konfigurace cloudové služby můžete aktualizovat, když je spuštěná v Azure, bez nutnosti převádět služby do režimu offline. Chcete-li změnit informace o konfiguraci, můžete nahrát nový soubor konfigurace, nebo upravit konfigurační soubor na místě a použijte ho pro spuštěnou službu. Ke konfiguraci služby můžete provedeny následující změny:

* **Změna hodnoty nastavení konfigurace**  
  Při konfiguraci nastavení změny role instance můžete zvolit použití změny, zatímco instance online nebo recyklovat instance řádně a použití změn při instance je offline.
* **Změna topologie služby instancí role**  
  Topologie změny neovlivňují spuštěné instance, s výjimkou případů, kdy je odebírána instance. Všechny zbývající instance obecně nemusí být recyklována; Můžete však recyklovat instance rolí v reakci na změnu topologie.
* **Změna kryptografický otisk certifikátu**  
  Certifikát lze aktualizovat pouze pokud je role instance offline. Pokud certifikát přidat, odstranit nebo změnit, pokud je role instance online, Azure řádně trvá instance offline aktualizovat certifikát a převeďte ho zpátky do online režimu, po dokončení změn.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Zpracování změny konfigurace s událostmi služby modulu Runtime
[Knihovna Runtime Azure](https://msdn.microsoft.com/library/azure/mt419365.aspx) zahrnuje [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.aspx) názvů, který poskytuje třídy pro interakci s prostředím Azure z role. [RoleEnvironment](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) třída definuje následující události, které jsou vyvolány před a po změně konfigurace:

* **[Změna](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx) událostí**  
  K tomu dojde, před použitím změn konfigurace do zadané instance role s možností umožňuje vypnout instance rolí, v případě potřeby.
* **[Změnit](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changed.aspx) událostí**  
  Vyskytne se po změně konfigurace se použije k zadané instanci role.

> [!NOTE]
> Protože změny certifikátu vždy provést instancí role do offline režimu, vyvolají není RoleEnvironment.Changing nebo RoleEnvironment.Changed událostí.
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
Pokud chcete nasadit aplikaci jako cloudové služby v Azure, musí nejprve balíčku aplikace v příslušném formátu. Můžete použít **CSPack** nástroj příkazového řádku (nainstalované s [Azure SDK](https://azure.microsoft.com/downloads/)) k vytvoření souboru balíčku jako alternativu k sadě Visual Studio.

**CSPack** používá obsah souboru definice služby a konfigurační soubor služby zadat obsah balíčku. **CSPack** generuje balíčku souboru aplikace (.cspkg), který můžete nahrát do Azure pomocí [portál Azure](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Ve výchozím nastavení, balíček s názvem `[ServiceDefinitionFileName].cspkg`, ale můžete zadat jiný název pomocí `/out` možnost **CSPack**.

**CSPack** se nachází v  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> CSPack.exe (v systému windows) je k dispozici spuštěním **Microsoft Azure příkazového řádku** zástupce, který je nainstalován pomocí sady SDK.  
> 
> Spusťte CSPack.exe program samostatně najdete v dokumentaci o všech možných přepínačích a příkazy.
> 
> 

<p />

> [!TIP]
> Spustit místně v cloudové služby **Microsoft Azure výpočetní emulátor**, použijte **/copyonly** možnost. Tato možnost zkopíruje binární soubory pro aplikaci do adresáře rozložení, ze kterého se můžete spustit v emulátoru služby výpočty v.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Příklad do balíčku cloudové služby
Následující příklad vytvoří balíček aplikace, který obsahuje informace pro webové role. Příkaz určuje soubor definice služby, kterou chcete použít, adresáři, kde můžete najít binární soubory, a název souboru balíčku.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Pokud aplikace obsahuje webovou roli a roli pracovního procesu, je použít následující příkaz:

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
| \[DirectoryName\] |Podadresáři v kořenovém adresáři projektu, který obsahuje soubor .csdef Azure projektu. |
| \[ServiceDefinition\] |Název souboru definice služby. Ve výchozím nastavení je tento soubor s názvem ServiceDefinition.csdef. |
| \[OutputFileName\] |Název souboru vygenerovaný balíček. Obvykle je nastavena na název aplikace. Pokud není zadán žádný název souboru, balíček aplikace je vytvořen jako \[ApplicationName\].cspkg. |
| \[RoleName\] |Název role, jak jsou definovány v souboru definice služby. |
| \[RoleBinariesDirectory] |Umístění binární soubory pro roli. |
| \[VirtualPath\] |Fyzické adresáře pro každý virtuální cestu definovaný v oddílu lokality definice služby. |
| \[PhysicalPath\] |Fyzické adresáře obsah pro každý virtuální cestu definovanou v uzlu lokality definice služby. |
| \[RoleAssemblyName\] |Název binární soubor pro roli. |

## <a name="next-steps"></a>Další postup
Vytváření balíčku cloudové služby a chcete...

* [Instalační program vzdálené plochy pro instanci cloudové služby][remotedesktop]
* [Nasazení projektu cloudové služby][deploy]

Používám Visual Studio a chcete...

* [Vytvořte novou cloudovou službu][vs_create]
* [Znovu nakonfigurujte stávající cloudovou službu][vs_reconfigure]
* [Nasazení projektu cloudové služby][vs_deploy]
* [Instalační program vzdálené plochy pro instanci cloudové služby][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: cloud-services-role-enable-remote-desktop-visual-studio.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md
