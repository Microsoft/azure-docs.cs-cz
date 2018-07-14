---
title: Azure Cloud Services Def. Schéma WebRole | Dokumentace Microsoftu
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 85368e4e-a0db-4c02-8dbc-8e2928fa6091
caps.latest.revision: 60
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: e548841f334705aa71ada92c43ccde207a1f6318
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39002309"
---
# <a name="azure-cloud-services-definition-webrole-schema"></a>Schéma WebRole definice služby Azure Cloud Services
Azure web role je role, který je přizpůsobený pro programování webové aplikace, protože IIS 7, jako je ASP.NET, PHP, Windows Communication Foundation a FastCGI.

Výchozí přípona pro definiční soubor služby je .csdef.

## <a name="basic-service-definition-schema-for-a-web-role"></a>Schématu definice služby na úrovni Basic pro webové role  
Základní formát souboru definice služby obsahující webové role je následujícím způsobem.

```xml
<ServiceDefinition …>  
  <WebRole name="<web-role-name>" vmsize="<web-role-size>" enableNativeCodeExecution="[true|false]">  
    <Certificates>  
      <Certificate name="<certificate-name>" storeLocation="<certificate-store>" storeName="<store-name>" />  
    </Certificates>      
    <ConfigurationSettings>  
      <Setting name="<setting-name>" />  
    </ConfigurationSettings>  
    <Imports>  
      <Import moduleName="<import-module>"/>  
    </Imports>  
    <Endpoints>  
      <InputEndpoint certificate="<certificate-name>" ignoreRoleInstanceStatus="[true|false]" name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<port-number>" port="<port-number>" loadBalancerProbe="<load-balancer-probe-name>" />  
      <InternalEndpoint name="<internal-endpoint-name>" protocol="[http|tcp|udp|any]" port="<port-number>">  
         <FixedPort port="<port-number>"/>  
         <FixedPortRange min="<minium-port-number>" max="<maximum-port-number>"/>  
      </InternalEndpoint>  
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">  
         <AllocatePublicPortFrom>  
            <FixedPortRange min="<minium-port-number>" max="<maximum-port-number>"/>  
         </AllocatePublicPortFrom>  
      </InstanceInputEndpoint>  
    </Endpoints>  
    <LocalResources>  
      <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />  
    </LocalResources>  
    <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />  
    <Runtime executionContext="[limited|elevated]">  
      <Environment>  
         <Variable name="<variable-name>" value="<variable-value>">  
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>  
          </Variable>            
      </Environment>  
      <EntryPoint>  
         <NetFxEntryPoint assemblyName="<name-of-assembly-containing-entrypoint>" targetFrameworkVersion="<.net-framework-version>"/>  
      </EntryPoint>  
    </Runtime>  
    <Sites>  
      <Site name="<web-site-name>">  
        <VirtualApplication name="<application-name>" physicalDirectory="<directory-path>"/>  
        <VirtualDirectory name="<directory-path>" physicalDirectory="<directory-path>"/>  
        <Bindings>  
          <Binding name="<binding-name>" endpointName="<endpoint-name-bound-to>" hostHeader="<url-of-the-site>"/>  
        </Bindings>  
      </Site>  
    </Sites>  
    <Startup priority="<for-internal-use-only>">  
      <Task commandLine="<command-to=execute>" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">  
        <Environment>  
         <Variable name="<variable-name>" value="<variable-value>">  
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>  
          </Variable>            
        </Environment>  
      </Task>  
    </Startup>  
    <Contents>  
      <Content destination="<destination-folder-name>" >  
        <SourceDirectory path="<local-source-directory>" />  
      </Content>  
    </Contents>  
  </WebRole>  
</ServiceDefinition>  
```  

## <a name="schema-elements"></a>Prvky schématu  
Definiční soubor služby obsahuje tyto prvky, které jsou podrobně popsány v následujících oddílech v tomto tématu:  

[WebRole](#WebRole)

[ConfigurationSettings](#ConfigurationSettings)

[Nastavení](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Koncové body](#Endpoints)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Certifikáty](#Certificates)

[Certifikát](#Certificate)

[Importy](#Imports)

[Import](#Import)

[Modul runtime](#Runtime)

[Prostředí](#Environment)

[Proměnná](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[NetFxEntryPoint](#NetFxEntryPoint)

[Weby](#Sites)

[Lokality](#Site)

[VirtualApplication](#VirtualApplication)

[VirtualApplication](#VirtualApplication)

[Vazby](#Bindings)

[Vytvoření vazby](#Binding)

[Po spuštění](#Startup)

[Úkol](#Task)

[Obsah](#Contents)

[Obsah](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WebRole"></a> WebRole  
`WebRole` Element popisuje role, který je přizpůsobený pro programování webové aplikace, podporuje službu IIS 7 a ASP.NET. Služba může obsahovat nula nebo více webových rolí.

Následující tabulka popisuje atributy `WebRole` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Název pro webovou roli. Název role musí být jedinečný.|  
|enableNativeCodeExecution|Boolean|Volitelné. Výchozí hodnota je `true`; nativní provádění kódu a úplný vztah důvěryhodnosti, které jsou ve výchozím nastavení povolená. Tento atribut nastavte na `false` zakázat spouštění nativního kódu pro webovou roli a místo toho použít Azure částečným vztahem důvěryhodnosti.|  
|vmsize|řetězec|Volitelné. Nastavením této hodnoty můžete změnit velikost virtuálního počítače, který je přidělený k roli. Výchozí hodnota je `Small`. Další informace najdete v tématu [velikostí virtuálních počítačů pro Cloud Services](cloud-services-sizes-specs.md).|  

##  <a name="ConfigurationSettings"></a> ConfigurationSettings  
`ConfigurationSettings` Element popisuje soubor konfiguračních nastavení pro webovou roli. Tento element je nadřazeného člena `Setting` elementu.

##  <a name="Setting"></a> Nastavení  
`Setting` Element popisuje dvojice název a hodnotu, která určuje nastavení konfigurace pro instanci role.

Následující tabulka popisuje atributy `Setting` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Jedinečný název pro nastavení konfigurace.|  

Nastavení konfigurace pro role jsou páry název-hodnota, které jsou deklarovány v souboru definice služby a nastavte v konfiguračním souboru služby.

##  <a name="LocalResources"></a> LocalResources  
`LocalResources` Element popisuje kolekci prostředků místní úložiště pro webovou roli. Tento element je nadřazeného člena `LocalStorage` elementu.

##  <a name="LocalStorage"></a> LocalStorage  
`LocalStorage` Element identifikuje prostředek Místní úložiště, který poskytuje místo systému souborů služby za běhu. Role může definovat nula nebo více prostředků místní úložiště.

> [!NOTE]
>  `LocalStorage` Element lze použít jako podřízený objekt `WebRole` element na podporu kompatibilita s předchozími verzemi sady Azure SDK.

Následující tabulka popisuje atributy `LocalStorage` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Jedinečný název pro místní úložiště.|  
|cleanOnRoleRecycle|Boolean|Volitelné. Určuje, zda by měl při restartování role vyčistit místní úložiště. Výchozí hodnota je `true`.|  
|Hodnota parametru sizeInMb|int|Volitelné. Požadované množství místa k přidělení pro místní úložiště v MB. Pokud není zadán, výchozí přidělené místo úložiště je 100 MB. Minimální množství místa, které mohou být přiděleny je 1 MB.<br /><br /> Maximální velikost místních prostředků závisí na velikosti virtuálního počítače. Další informace najdete v tématu [velikostí virtuálních počítačů pro Cloud Services](cloud-services-sizes-specs.md).|  
  
Název adresáře přidělená k prostředku Místní úložiště odpovídá hodnota zadaná pro atribut name.

##  <a name="Endpoints"></a> Koncové body  
`Endpoints` Element popisuje kolekci vstup (externí), interní a instance vstupních koncových bodů pro roli. Tento element je nadřazeného člena `InputEndpoint`, `InternalEndpoint`, a `InstanceInputEndpoint` elementy.

Vstup a koncovým bodům s interním přidělují samostatně. Služba může mít celkem 25 vstupu, interní, a instance vstupní koncové body, které mohou být přiděleny v 25 rolích povolené ve službě. Například pokud máte 5 rolí můžete přidělit 5 vstupní koncové body na roli nebo můžete přidělit 25 vstupní koncové body do jedné role nebo je můžete přidělit 1 vstupní koncový bod každé 25 rolím.

> [!NOTE]
>  Každá role nasazení vyžaduje jedna instance na roli. Výchozí zřizování pro předplatné je omezený na 20 jádry a proto je omezený na 20 instancí role. Pokud vaše aplikace vyžaduje víc instancí, než je k dispozici ve výchozím nastavení zřizování najdete v tématu [fakturace, správu předplatného a kvóty podpory](https://azure.microsoft.com/support/options/) Další informace o zvýšení vaší kvóty.

##  <a name="InputEndpoint"></a> InputEndpoint  
`InputEndpoint` Element popisuje externí koncový bod webové role.

Můžete definovat více koncových bodů, které jsou kombinací HTTP, HTTPS, UDP a TCP koncových bodů. Můžete zadat libovolné číslo portu, který jste vybrali pro vstupní koncový bod, ale zadaný pro každou roli ve službě čísla portů musí být jedinečný. Například pokud určíte, že webová role používá port 80 pro protokol HTTP a port 443 pro protokol HTTPS, můžete pak určit, že druhý webové role používá port 8080 pro protokol HTTP a port 8043 pro protokol HTTPS.

Následující tabulka popisuje atributy `InputEndpoint` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Jedinečný název pro externí koncový bod.|  
|Protokol|řetězec|Povinná hodnota. Přenosový protokol pro externí koncový bod. Pro webovou roli možné hodnoty jsou `HTTP`, `HTTPS`, `UDP`, nebo `TCP`.|  
|port|int|Povinná hodnota. Port pro externí koncový bod. Můžete zadat libovolné číslo portu, který zvolíte, ale zadaný pro každou roli ve službě čísla portů musí být jedinečný.<br /><br /> Možné hodnoty jsou v rozsahu od 1 do 65535 (včetně) (Azure SDK verze 1.7 nebo vyšší).|  
|certifikát|řetězec|Vyžaduje se pro koncový bod HTTPS. Název certifikátu určené `Certificate` elementu.|  
|localPort|int|Volitelné. Určuje port používaný pro interní připojení na koncový bod. `localPort` Atribut mapuje externí port v koncovém bodě na interní port na roli. To je užitečné v situacích, kde role musí komunikovat na interní komponenty na portu, liší od ten, který je vystaven externě.<br /><br /> Pokud nezadáte hodnotu `localPort` je stejné jako `port` atribut. Nastavte hodnotu `localPort` na "*" automaticky přiřadit nepřidělenou port, který je zjistitelné pomocí rozhraní API modulu runtime.<br /><br /> Možné hodnoty jsou v rozsahu od 1 do 65535 (včetně) (Azure SDK verze 1.7 nebo vyšší).<br /><br /> `localPort` Atribut je pouze k dispozici prostřednictvím Azure SDK verze 1.3 nebo vyšší.|  
|ignoreRoleInstanceStatus|Boolean|Volitelné. Pokud je hodnota tohoto atributu nastavena na `true`, se ignoruje stav služby a koncového bodu se neodeberou nástroje pro vyrovnávání zatížení. Tuto hodnotu nastavíte na `true` užitečné pro ladění zaneprázdněný instancí služby. Výchozí hodnota je `false`. **Poznámka:** koncový bod může stále přijímat provoz i v případě, že role není ve stavu Připraveno.|  
|loadBalancerProbe|řetězec|Volitelné. Název přidružený vstupní koncový bod test paměti nástroje pro vyrovnávání zatížení. Další informace najdete v tématu [LoadBalancerProbe schéma](schema-csdef-loadbalancerprobe.md).|  

##  <a name="InternalEndpoint"></a> InternalEndpoint  
`InternalEndpoint` Element popisuje vnitřní koncový bod webové role. Vnitřní koncový bod je k dispozici jenom do ostatních instancí role v rámci služby není k dispozici pro klienty mimo službu. Webové role, které nejsou zahrnuté `Sites` element může obsahovat pouze jeden HTTP, UDP nebo TCP interní koncový bod.

Následující tabulka popisuje atributy `InternalEndpoint` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Jedinečný název pro vnitřní koncový bod.|  
|Protokol|řetězec|Povinná hodnota. Přenosový protokol pro vnitřní koncový bod. Možné hodnoty jsou `HTTP`, `TCP`, `UDP`, nebo `ANY`.<br /><br /> Hodnota `ANY` Určuje, že je povolen libovolný protokol jiný port.|  
|port|int|Volitelné. Port používaný pro připojení s vyrovnáváním zatížení interní na koncovém bodu. Skupinu s vyrovnáváním zatížení koncový bod používá dva porty. Port používaný pro veřejnou IP adresu a port používaný na privátní IP adresu. Obvykle se jedná o tyto vlastnosti se nastaví na stejnou, ale můžete použít jiné porty.<br /><br /> Možné hodnoty jsou v rozsahu od 1 do 65535 (včetně) (Azure SDK verze 1.7 nebo vyšší).<br /><br /> `Port` Atribut je pouze k dispozici prostřednictvím Azure SDK verze 1.3 nebo vyšší.|  

##  <a name="InstanceInputEndpoint"></a> InstanceInputEndpoint  
`InstanceInputEndpoint` Element popisuje vstupní koncový bod instance webové role. Vstupní koncový bod instance je přidružen k instanci určité role pomocí přesměrování portu v nástroji pro vyrovnávání zatížení. Vstupní koncový bod každé instance je namapovaná na konkrétní port z rozsahu portů je to možné. Tento element je nadřazeného člena `AllocatePublicPortFrom` elementu.

`InstanceInputEndpoint` Elementu je jenom k dispozici prostřednictvím Azure SDK verze 1.7 nebo vyšší.

Následující tabulka popisuje atributy `InstanceInputEndpoint` elementu.
  
| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Jedinečný název pro koncový bod.|  
|localPort|int|Povinná hodnota. Určuje interní port, který bude naslouchat všem instancím rolí k přijímání příchozího provozu předány z nástroje pro vyrovnávání zatížení. Možné hodnoty jsou v rozsahu od 1 do 65535 (včetně).|  
|Protokol|řetězec|Povinná hodnota. Přenosový protokol pro vnitřní koncový bod. Možné hodnoty jsou `udp` nebo `tcp`. Použití `tcp` http/https podle provozu.|  
  
##  <a name="AllocatePublicPortFrom"></a> AllocatePublicPortFrom  
`AllocatePublicPortFrom` Element popisuje rozsah veřejný port, který je možné ke každé instanci vstupní koncový bod externí zákazníky. Číslo veřejného portu (VIP) se přiděluje z tohoto rozsahu a přiřadí do každého koncového bodu instance jednotlivých rolí během nasazení klienta a aktualizace. Tento element je nadřazeného člena `FixedPortRange` elementu.

`AllocatePublicPortFrom` Elementu je jenom k dispozici prostřednictvím Azure SDK verze 1.7 nebo vyšší.

##  <a name="FixedPort"></a> FixedPort  
`FixedPort` Prvek určuje port pro vnitřní koncový bod, který umožňuje načtení vyvážené připojení na koncový bod.

`FixedPort` Element je pouze k dispozici prostřednictvím Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `FixedPort` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|port|int|Povinná hodnota. Port pro vnitřní koncový bod. To má stejný účinek jako nastavení `FixedPortRange` min a max na stejný port.<br /><br /> Možné hodnoty jsou v rozsahu od 1 do 65535 (včetně) (Azure SDK verze 1.7 nebo vyšší).|  

##  <a name="FixedPortRange"></a> FixedPortRange  
`FixedPortRange` Prvek určuje rozsah portů, které jsou přiřazeny k interní koncový bod nebo vstupní koncový bod instance a Nastaví port, který se používá pro zatížení s vyrovnáváním připojení na koncový bod.

> [!NOTE]
>  `FixedPortRange` Element funguje jinak v závislosti na element, ve kterém se nachází. Když `FixedPortRange` elementu je `InternalEndpoint` prvek, otevře se všechny porty nástroje pro vyrovnávání zatížení v rámci rozsahu atributy min a max pro všechny virtuální počítače, na kterých role běží. Když `FixedPortRange` elementu je `InstanceInputEndpoint` prvek, otevře se pouze jeden port v rozsahu atributy min a max na každém virtuálním počítači s rolí.

`FixedPortRange` Element je pouze k dispozici prostřednictvím Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `FixedPortRange` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|min|int|Povinná hodnota. Minimální port v rozsahu. Možné hodnoty jsou v rozsahu od 1 do 65535 (včetně) (Azure SDK verze 1.7 nebo vyšší).|  
|max|řetězec|Povinná hodnota. Maximální číslo portu v rozsahu. Možné hodnoty jsou v rozsahu od 1 do 65535 (včetně) (Azure SDK verze 1.7 nebo vyšší).|  

##  <a name="Certificates"></a> Certifikáty  
`Certificates` Element popisuje kolekce certifikátů pro webovou roli. Tento element je nadřazeného člena `Certificate` elementu. Role může mít libovolný počet přidružené certifikáty. Další informace o používání elementu certifikáty, najdete v části [upravit soubor definice služby s certifikátem](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="Certificate"></a> Certifikát  
`Certificate` Element popisuje certifikát, který je přidružen k webové roli.

Následující tabulka popisuje atributy `Certificate` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Název tohoto certifikátu, který se používá k na ni odkazují, když je přidružen HTTPS `InputEndpoint` elementu.|  
|storeLocation|řetězec|Povinná hodnota. Umístění úložiště certifikátů, kde může najít tohoto certifikátu na místním počítači. Možné hodnoty jsou `CurrentUser` a `LocalMachine`.|  
|storeName|řetězec|Povinná hodnota. Název úložiště certifikátů, pokud tento certifikát je umístěn v místním počítači. Možné hodnoty zahrnují názvy předdefinovaných úložiště `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot`, `AddressBook`, nebo libovolný název vlastního úložiště. Pokud není zadán název vlastní úložiště, automaticky se vytvoří ve storu.|  
|permissionLevel|řetězec|Volitelné. Určuje přístupová oprávnění udělená role procesy. Pokud chcete umožnit přístup k privátnímu klíči a poté zadejte pouze se zvýšenými oprávněními procesy `elevated` oprávnění. `limitedOrElevated` oprávnění umožňuje všechny procesy rolí pro přístup k privátnímu klíči. Možné hodnoty jsou `limitedOrElevated` nebo `elevated`. Výchozí hodnota je `limitedOrElevated`.|  

##  <a name="Imports"></a> Importy  
`Imports` Element popisuje kolekci importovat moduly pro webové role, které přidat součásti do hostovaného operačního systému. Tento element je nadřazeného člena `Import` elementu. Tento element je volitelné a role může mít pouze jeden blok importy. 

`Imports` Element je pouze k dispozici prostřednictvím Azure SDK verze 1.3 nebo vyšší.

##  <a name="Import"></a> Import  
`Import` Prvek určuje modul a přidejte do hostovaného operačního systému.

`Import` Element je pouze k dispozici prostřednictvím Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `Import` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|moduleName|řetězec|Povinná hodnota. Název modulu k importu. Neplatný import moduly jsou:<br /><br /> -RemoteAccess<br />-RemoteForwarder<br />-Diagnostics<br /><br /> Moduly RemoteAccess a RemoteForwarder umožňují nakonfigurovat pro připojení ke vzdálené ploše instance role. Další informace najdete v části [povolit připojení ke vzdálené ploše](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> Modulu Diagnostika umožňuje shromažďovat diagnostická data pro instanci role.|  

##  <a name="Runtime"></a> Modul runtime  
`Runtime` Element popisuje kolekci nastavení proměnné prostředí pro webovou roli, která řídí běhové prostředí Azure hostitelského procesu. Tento element je nadřazeného člena `Environment` elementu. Tento element je volitelné a role může mít pouze jeden blok modulu runtime.

`Runtime` Element je pouze k dispozici prostřednictvím Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `Runtime` element:  

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|kontextu executionContext|řetězec|Volitelné. Určuje kontext, ve kterém je spuštěn proces Role. Výchozí kontext je `limited`.<br /><br /> -   `limited` – Proces se spustí bez oprávnění správce.<br />-   `elevated` – Proces se spustí s oprávněními správce.|  

##  <a name="Environment"></a> Prostředí  
`Environment` Element popisuje kolekci nastavení proměnné prostředí pro webovou roli. Tento element je nadřazeného člena `Variable` elementu. Role může mít libovolný počet nastavení proměnné prostředí.

##  <a name="Variable"></a> Proměnná  
`Variable` Prvek Určuje proměnnou prostředí k nastavení v hostovaného operačního.

`Variable` Element je pouze k dispozici prostřednictvím Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `Variable` element:  

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Název proměnné prostředí pro nastavení.|  
|hodnota|řetězec|Volitelné. Hodnota k nastavení proměnné prostředí. Musí obsahovat atribut hodnoty nebo `RoleInstanceValue` elementu.|  

##  <a name="RoleInstanceValue"></a> RoleInstanceValue  
`RoleInstanceValue` Prvek určuje výraz xPath, ze kterého se má načíst hodnotu proměnné.

Následující tabulka popisuje atributy `RoleInstanceValue` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|výraz XPath|řetězec|Volitelné. Cesta k umístění nastavení nasazení pro instanci. Další informace najdete v tématu [konfigurační proměnné, jejichž výraz XPath](cloud-services-role-config-xpath.md).<br /><br /> Musí obsahovat atribut hodnoty nebo `RoleInstanceValue` elementu.|  

##  <a name="EntryPoint"></a> Vstupní bod  
`EntryPoint` Prvek určuje vstupní bod pro roli. Tento element je nadřazeného člena `NetFxEntryPoint` elementy. Tyto prvky umožňují určit jiné než výchozí WaWorkerHost.exe aplikace tak, aby fungoval jako vstupní bod role.

`EntryPoint` Element je pouze k dispozici prostřednictvím Azure SDK verze 1.5 nebo vyšší.

##  <a name="NetFxEntryPoint"></a> NetFxEntryPoint  
`NetFxEntryPoint` Prvek určuje program, který má spustit pro roli.

> [!NOTE]
>  `NetFxEntryPoint` Element je pouze k dispozici prostřednictvím Azure SDK verze 1.5 nebo vyšší.

Následující tabulka popisuje atributy `NetFxEntryPoint` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|assemblyName|řetězec|Povinná hodnota. Název a cesta k souboru sestavení obsahující vstupní bod. Cesta je relativní vzhledem ke složce  **\\%ROLEROOT%\Approot** (nezadávejte  **\\%ROLEROOT%\Approot** v `commandLine`, předpokládá se). **ROLEROOT %** je spravován proměnnou prostředí Azure a představuje umístění kořenové složky pro vaši roli. **\\%ROLEROOT%\Approot** složky představuje složka aplikace pro vaši roli.<br /><br /> Pro role HWC cesta je vždy vzhledem k  **\\%ROLEROOT%\Approot\bin** složky.<br /><br /> Pro úplnou službu IIS a služby IIS Express webové role, pokud sestavení nelze najít vzhledem k  **\\%ROLEROOT%\Approot** složku,  **\\%ROLEROOT%\Approot\bin** je prohledána.<br /><br /> Jessi combsové zpět chování pro úplnou službu IIS se nejedná osvědčený postup doporučujeme a možná odebrán v budoucích verzích.|  
|Parametr targetFrameworkVersion|řetězec|Povinná hodnota. Verze rozhraní .NET framework, na kterém byla vytvořena sestavení. Například, `targetFrameworkVersion="v4.0"`.|  

##  <a name="Sites"></a> Weby  
`Sites` Element popisuje kolekci webů a webových aplikací, které jsou hostované ve webové roli. Tento element je nadřazeného člena `Site` elementu. Pokud nezadáte `Sites` elementu, webové role je hostovaný jako starší webové role a může mít jenom jeden web hostovaný na vaši webovou roli. Tento element je volitelné a role může mít pouze jeden blok lokalit.

`Sites` Element je pouze k dispozici prostřednictvím Azure SDK verze 1.3 nebo vyšší.

##  <a name="Site"></a> Lokality  
`Site` Prvek Určuje web nebo webovou aplikaci, která je součástí webové role.

`Site` Element je pouze k dispozici prostřednictvím Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `Site` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Název webu nebo aplikaci.|  
|physicalDirectory|řetězec|Umístění obsahu adresáře pro kořenový adresář webu. Umístění se dá nastavit jako absolutní nebo relativní k umístění .csdef.|  

##  <a name="VirtualApplication"></a> VirtualApplication  
`VirtualApplication` Element definuje aplikaci v Internetové informační služby (IIS) 7 je seskupení souborů, které dodává obsah nebo poskytuje služby přes protokoly, jako je například HTTP. Při vytváření aplikace ve službě IIS 7 cesta aplikace se stane součástí adresy URL webu.

`VirtualApplication` Element je pouze k dispozici prostřednictvím Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `VirtualApplication` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Určuje název pro identifikaci virtuálních aplikací.|  
|physicalDirectory|řetězec|Povinná hodnota. Určuje cestu na vývojovém počítači, který obsahuje virtuální aplikace. V emulátoru služby compute služba IIS konfigurována načítat obsah z tohoto umístění. Při nasazování do Azure, jsou sbaleny obsah fyzický adresář spolu se zbývajícími služby. Při nasazení balíčku služby do Azure, služba IIS konfigurována s umístěním rozbalené obsah.|  

##  <a name="VirtualDirectory"></a> VirtualDirectory  
`VirtualDirectory` Prvek určuje název adresáře (také označované jako cesty) zadejte ve službě IIS a mapování na fyzický adresář na místním nebo vzdáleném serveru.

`VirtualDirectory` Element je pouze k dispozici prostřednictvím Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `VirtualDirectory` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Určuje název pro identifikaci virtuálního adresáře.|  
|hodnota|physicalDirectory|Povinná hodnota. Určuje cestu na vývojovém počítači, který obsahuje web nebo obsah virtuálního adresáře. V emulátoru služby compute služba IIS konfigurována načítat obsah z tohoto umístění. Při nasazování do Azure, jsou sbaleny obsah fyzický adresář spolu se zbývajícími služby. Při nasazení balíčku služby do Azure, služba IIS konfigurována s umístěním rozbalené obsah.|  

##  <a name="Bindings"></a> Vazby  
`Bindings` Element popisuje kolekci vazby pro web. Je nadřazený element `Binding` elementu. Element se vyžaduje pro každý `Site` elementu. Další informace o konfiguraci koncových bodů najdete v tématu [povolit komunikaci pro instance rolí](cloud-services-enable-communication-role-instances.md).

`Bindings` Element je pouze k dispozici prostřednictvím Azure SDK verze 1.3 nebo vyšší.

##  <a name="Binding"></a> Vytvoření vazby  
`Binding` Prvek určuje informace o konfiguraci vyžadované u požadavků ke komunikaci s web nebo webovou aplikaci.

`Binding` Element je pouze k dispozici prostřednictvím Azure SDK verze 1.3 nebo vyšší.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Určuje název pro identifikaci vazby.|  
|endpointName|řetězec|Povinná hodnota. Určuje název koncového bodu k vytvoření vazby.|  
|Hostitel|řetězec|Volitelné. Určuje název hostitele, který umožňuje hostování několika webů s názvy jiného hostitele, na jedné kombinaci IP Adresa/Port číslo.|  

##  <a name="Startup"></a> Po spuštění  
`Startup` Element popisuje kolekci úloh, které spustí při spuštění role. Tento element může být nadřazený prvek `Variable` elementu. Další informace o použití úlohy po spuštění role najdete v tématu [postupy konfigurace úloh při spuštění](cloud-services-startup-tasks.md). Tento element je volitelné a role může mít pouze jeden blok při spuštění.

Následující tabulka popisuje atribut `Startup` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|priorita|int|Pouze pro interní použití.|  

##  <a name="Task"></a> Úloha  
`Task` Prvek určuje úloha po spuštění, které u něho při spuštění role. Úlohy po spuštění lze použít k provádění úloh, které připravují roli, kterou chcete spustit tyto instalace softwarových komponent nebo jiné aplikace. Spouštění úloh v pořadí, ve kterém jsou uvedeny v rámci `Startup` element bloku.

`Task` Element je pouze k dispozici prostřednictvím Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `Task` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|příkazový řádek|řetězec|Povinná hodnota. Skript, například soubor CMD, který obsahuje příkazy se spustí. Po spuštění příkazu a dávkové soubory musí být uložen ve formátu ANSI. Formáty souborů, které nastavit značku pořadí bajtů na začátku souboru nezpracuje správně.|  
|kontextu executionContext|řetězec|Určuje kontext, ve kterém je spuštěný skript.<br /><br /> -   `limited` [Výchozí] – spuštění se stejnými oprávněními jako role, která hostuje procesu.<br />-   `elevated` – Spusťte s oprávněními správce.|  
|taskType|řetězec|Určuje chování při spuštění příkazu.<br /><br /> -   `simple` [Výchozí] – systém čeká na ukončit před další úkoly jsou spuštěny úlohy.<br />-   `background` – Systém nečeká na ukončit úlohy.<br />-   `foreground` – Podobně jako na pozadí, s výjimkou role nerestartuje, dokud se ukončit všechny úlohy v popředí.|  

##  <a name="Contents"></a> Obsah  
`Contents` Element popisuje sadu obsahu pro webovou roli. Tento element je nadřazeného člena `Content` elementu.

`Contents` Element je pouze k dispozici prostřednictvím Azure SDK verze 1.5 nebo vyšší.

##  <a name="Content"></a> Obsah  
`Content` Element definuje umístění zdroje obsahu se zkopírují do virtuálních počítačů Azure a cílovou cestu, do které se kopírují.

`Content` Element je pouze k dispozici prostřednictvím Azure SDK verze 1.5 nebo vyšší.

Následující tabulka popisuje atributy `Content` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|cíl|řetězec|Povinná hodnota. Umístění v Azure virtuální počítač, na který je umístěn obsah. Toto umístění je složkou **%ROLEROOT%\Approot**.|  

Tento element je nadřazeného elementu `SourceDirectory` elementu.

##  <a name="SourceDirectory"></a> SourceDirectory  
`SourceDirectory` Element definuje místní adresář, ze kterého je zkopírován obsah. Tento element slouží k určení místní obsah ke zkopírování do virtuálních počítačů Azure.

`SourceDirectory` Element je pouze k dispozici prostřednictvím Azure SDK verze 1.5 nebo vyšší.

Následující tabulka popisuje atributy `SourceDirectory` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|path|řetězec|Povinná hodnota. Relativní nebo absolutní cesta k místnímu adresáři, jejíž obsah bude zkopírován do virtuálních počítačů Azure. Rozšíření proměnných prostředí v cestě adresáře se podporuje.|  
  
## <a name="see-also"></a>Viz také
[Cloudové služby (klasické) schématu definice](schema-csdef-file.md)
