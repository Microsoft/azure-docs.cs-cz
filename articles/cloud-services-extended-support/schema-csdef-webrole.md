---
title: Azure Cloud Services (Rozšířená podpora) def. schéma webrole | Microsoft Docs
description: Informace týkající se webové role pro Cloud Services (Rozšířená podpora)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: fef3e3cc63fb9e1ca6aa64cf799a620f187db76f
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744325"
---
# <a name="azure-cloud-services-extended-support-definition-webrole-schema"></a>Schéma webrole webrole Azure Cloud Services (Rozšířená podpora) definice

Webová role Azure je role, která je přizpůsobená pro programování webových aplikací, jak je podporovaná službou IIS 7, jako je ASP.NET, PHP, Windows Communication Foundation a FastCGI.

Výchozí přípona souboru definice služby je csdef.

## <a name="basic-service-definition-schema-for-a-web-role"></a>Základní schéma definice služby pro webovou roli  
Základní formát definičního souboru služby, který obsahuje webovou roli, je následující.

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
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>  
      </InternalEndpoint>  
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">  
         <AllocatePublicPortFrom>  
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>  
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
Definiční soubor služby obsahuje tyto prvky, které jsou podrobně popsány v následujících částech tohoto tématu:  

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

[Objem](#Imports)

[Import](#Import)

[Runtime (Modul runtime)](#Runtime)

[Prostředí](#Environment)

[Proměnná](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[NetFxEntryPoint](#NetFxEntryPoint)

[Lokality](#Sites)

[Web](#Site)

[VirtualApplication](#VirtualApplication)

[VirtualApplication](#VirtualApplication)

[Vazby](#Bindings)

[Vazba](#Binding)

[Spuštění](#Startup)

[Úkol](#Task)

[Obsah](#Contents)

[Obsah](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="webrole"></a><a name="WebRole"></a> WebRole  
`WebRole`Element popisuje roli, která je upravena pro programování webových aplikací, jak je podporována službou IIS 7 a ASP.NET. Služba může obsahovat nula nebo více webových rolí.

Následující tabulka popisuje atributy `WebRole` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|name|řetězec|Povinná hodnota. Název webové role. Název role musí být jedinečný.|  
|enableNativeCodeExecution|boolean|Nepovinný parametr. Výchozí hodnota je `true` ; ve výchozím nastavení je povoleno spuštění nativního kódu a Plná důvěra. Nastavením tohoto atributu `false` zakážete spuštění nativního kódu pro webovou roli a místo toho použijete částečnou důvěryhodnost Azure.|  
|VMSize|řetězec|Nepovinný parametr. Nastavte tuto hodnotu, chcete-li změnit velikost virtuálního počítače, který je přidělen roli. Výchozí hodnota je `Small`. Další informace najdete v tématu [velikosti virtuálních počítačů pro Cloud Services](available-sizes.md).|  

##  <a name="configurationsettings"></a><a name="ConfigurationSettings"></a> ConfigurationSettings  
`ConfigurationSettings`Element popisuje kolekci konfiguračních nastavení pro webovou roli. Tento prvek je nadřazeným prvkem `Setting` elementu.

##  <a name="setting"></a><a name="Setting"></a> Nastavením  
`Setting`Element popisuje dvojici název a hodnota, která určuje nastavení konfigurace pro instanci role.

Následující tabulka popisuje atributy `Setting` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|name|řetězec|Povinná hodnota. Jedinečný název pro nastavení konfigurace.|  

Konfigurační nastavení role jsou páry název-hodnota, které jsou deklarovány v souboru definice služby a nastaveny v konfiguračním souboru služby.

##  <a name="localresources"></a><a name="LocalResources"></a> LocalResources  
`LocalResources`Element popisuje kolekci prostředků místního úložiště pro webovou roli. Tento prvek je nadřazeným prvkem `LocalStorage` elementu.

##  <a name="localstorage"></a><a name="LocalStorage"></a> LocalStorage  
`LocalStorage`Element identifikuje místní prostředek úložiště, který poskytuje prostor systému souborů pro službu za běhu. Role může definovat nula nebo více prostředků místního úložiště.

> [!NOTE]
>  `LocalStorage`Element se může zobrazit jako podřízený `WebRole` prvek elementu pro podporu kompatibility se staršími verzemi sady Azure SDK.

Následující tabulka popisuje atributy `LocalStorage` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|name|řetězec|Povinná hodnota. Jedinečný název pro místní úložiště.|  
|cleanOnRoleRecycle|boolean|Nepovinný parametr. Označuje, jestli se má při restartování role vyčistit místní úložiště. Výchozí hodnota je `true`.|  
|sizeInMb|int|Nepovinný parametr. Požadovaný objem prostoru úložiště, který se má přidělit místnímu úložišti, v MB Pokud není zadaný, použije se výchozí přidělený prostor úložiště 100 MB. Minimální velikost úložného prostoru, který může být přidělen, je 1 MB.<br /><br /> Maximální velikost místních prostředků závisí na velikosti virtuálního počítače. Další informace najdete v tématu [velikosti virtuálních počítačů pro Cloud Services](available-sizes.md).|  
  
Název adresáře, který je přidělený místnímu prostředku úložiště, odpovídá hodnotě zadané pro atribut Name.

##  <a name="endpoints"></a><a name="Endpoints"></a> Bod  
`Endpoints`Element popisuje kolekci vstupních (externích), interních a vstupních koncových bodů instance pro roli. Tento prvek je nadřazenými `InputEndpoint` `InternalEndpoint` prvky, a `InstanceInputEndpoint` .

Vstupní a interní koncové body jsou přidělovány samostatně. Služba může mít celkem 25 vstupních, interních a vstupních koncových bodů, které se dají přidělit přes 25 rolí povolených ve službě. Například pokud má 5 rolí, můžete přidělit 5 vstupních koncových bodů pro každou roli nebo můžete přidělit 25 vstupních koncových bodů jedné roli nebo můžete přidělit 1 vstupní koncový bod každé 25 rolí.

> [!NOTE]
>  Každá nasazená role vyžaduje jednu instanci na roli. Výchozí zřizování pro předplatné je omezené na 20 jader a proto je omezené na 20 instancí role. Pokud vaše aplikace vyžaduje více instancí, než je poskytované výchozím zřizováním, přečtěte si další informace o zvýšení kvóty v tématu [fakturace, Správa předplatných a podpora kvót](https://azure.microsoft.com/support/options/) .

##  <a name="inputendpoint"></a><a name="InputEndpoint"></a> InputEndpoint  
`InputEndpoint`Element popisuje externí koncový bod pro webovou roli.

Můžete definovat několik koncových bodů, které jsou kombinací koncových bodů HTTP, HTTPS, UDP a TCP. Můžete zadat libovolné číslo portu, které zvolíte pro vstupní koncový bod, ale čísla portů zadaná pro každou roli ve službě musí být jedinečná. Pokud například zadáte, že webová role používá port 80 pro protokol HTTP a port 443 pro protokol HTTPS, můžete určit, že druhá webová role bude pro protokol HTTP a port 8043 pro protokol HTTPS používat port 8080.

Následující tabulka popisuje atributy `InputEndpoint` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|name|řetězec|Povinná hodnota. Jedinečný název externího koncového bodu.|  
|protokol|řetězec|Povinná hodnota. Transportní protokol pro externí koncový bod. U webové role jsou možné hodnoty `HTTP` , `HTTPS` , `UDP` nebo `TCP` .|  
|port|int|Povinná hodnota. Port externího koncového bodu. Můžete zadat libovolné číslo portu, které zvolíte, ale čísla portů zadaná pro každou roli ve službě musí být jedinečná.<br /><br /> Možné hodnoty jsou v rozsahu od 1 do 65535, včetně (Azure SDK verze 1,7 nebo novější).|  
|certifikát|řetězec|Vyžaduje se pro koncový bod HTTPS. Název certifikátu, který je definován `Certificate` elementem.|  
|localPort|int|Nepovinný parametr. Určuje port, který se používá pro interní připojení ke koncovému bodu. `localPort`Atribut mapuje externí port na koncovém bodu na interní port role. To je užitečné ve scénářích, kdy musí role komunikovat s interní komponentou na portu, který se liší od toho, který je přístupný externě.<br /><br /> Pokud není zadán, hodnota `localPort` je shodná s `port` atributem. Nastavením hodnoty `localPort` na "*" automaticky přiřadíte nepřidělený port, který bude zjistitelný pomocí běhového rozhraní API.<br /><br /> Možné hodnoty jsou v rozsahu od 1 do 65535, včetně (Azure SDK verze 1,7 nebo novější).<br /><br /> `localPort`Atribut je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.|  
|ignoreRoleInstanceStatus|boolean|Nepovinný parametr. Pokud je hodnota tohoto atributu nastavená na `true` , stav služby se ignoruje a koncový bod se neodebere nástrojem pro vyrovnávání zatížení. Nastavení této hodnoty na `true` užitečnou pro ladění instancí zaneprázdněné služby. Výchozí hodnota je `false`. **Poznámka:**  Koncový bod může i nadále přijímat přenosy i v případě, že role není v připraveném stavu.|  
|loadBalancerProbe|řetězec|Nepovinný parametr. Název testu nástroje pro vyrovnávání zatížení, který je přidružený ke vstupnímu koncovému bodu. Další informace najdete v tématu [LoadBalancerProbe Schema](schema-csdef-loadbalancerprobe.md).|  

##  <a name="internalendpoint"></a><a name="InternalEndpoint"></a> InternalEndpoint  
`InternalEndpoint`Element popisuje interní koncový bod pro webovou roli. Interní koncový bod je k dispozici pouze pro jiné instance rolí běžící v rámci služby. není k dispozici pro klienty mimo službu. Webové role, které neobsahují `Sites` element, můžou mít jenom jeden interní koncový bod HTTP, UDP nebo TCP.

Následující tabulka popisuje atributy `InternalEndpoint` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|name|řetězec|Povinná hodnota. Jedinečný název vnitřního koncového bodu.|  
|protokol|řetězec|Povinná hodnota. Transportní protokol pro vnitřní koncový bod. Možné hodnoty jsou `HTTP` , `TCP` , `UDP` nebo `ANY` .<br /><br /> Hodnota `ANY` Určuje, že libovolný protokol je povolený.|  
|port|int|Nepovinný parametr. Port používaný pro interní připojení s vyrovnáváním zatížení na koncovém bodu. Koncový bod s vyrovnáváním zatížení používá dva porty. Port používaný pro veřejnou IP adresu a port použitý na privátní IP adrese. Obvykle se jedná o stejné nastavení, ale můžete zvolit, aby se používaly různé porty.<br /><br /> Možné hodnoty jsou v rozsahu od 1 do 65535, včetně (Azure SDK verze 1,7 nebo novější).<br /><br /> `Port`Atribut je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.|  

##  <a name="instanceinputendpoint"></a><a name="InstanceInputEndpoint"></a> InstanceInputEndpoint  
`InstanceInputEndpoint`Element popisuje vstupní koncový bod instance pro webovou roli. Vstupní koncový bod instance je spojen s konkrétní instancí role pomocí předávání portů v nástroji pro vyrovnávání zatížení. Každý vstupní koncový bod instance je namapovaný na konkrétní port z rozsahu možných portů. Tento prvek je nadřazeným prvkem `AllocatePublicPortFrom` elementu.

`InstanceInputEndpoint`Element je k dispozici pouze pomocí sady Azure SDK verze 1,7 nebo vyšší.

Následující tabulka popisuje atributy `InstanceInputEndpoint` prvku.
  
| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|name|řetězec|Povinná hodnota. Jedinečný název koncového bodu.|  
|localPort|int|Povinná hodnota. Určuje interní port, k němuž budou poslouchat všechny instance rolí, aby se přijímal příchozí provoz předaný z nástroje pro vyrovnávání zatížení. Možné hodnoty jsou v rozsahu od 1 do 65535, včetně.|  
|protokol|řetězec|Povinná hodnota. Transportní protokol pro vnitřní koncový bod. Možné hodnoty jsou `udp` nebo `tcp`. Používá se `tcp` pro přenosy založené na http/https.|  
  
##  <a name="allocatepublicportfrom"></a><a name="AllocatePublicPortFrom"></a> AllocatePublicPortFrom  
`AllocatePublicPortFrom`Element popisuje rozsah veřejných portů, který mohou externí zákazníci použít pro přístup ke každému vstupnímu koncovému bodu instance. Číslo portu veřejného (VIP) se přiděluje z tohoto rozsahu a přiřadí se každému koncovému bodu instance role během nasazování a aktualizace tenanta. Tento prvek je nadřazeným prvkem `FixedPortRange` elementu.

`AllocatePublicPortFrom`Element je k dispozici pouze pomocí sady Azure SDK verze 1,7 nebo vyšší.

##  <a name="fixedport"></a><a name="FixedPort"></a> FixedPort  
`FixedPort`Element určuje port interního koncového bodu, který umožňuje připojení s vyrovnáváním zatížení na koncovém bodu.

`FixedPort`Element je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.

Následující tabulka popisuje atributy `FixedPort` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|port|int|Povinná hodnota. Port interního koncového bodu. To má stejný účinek jako nastavení `FixedPortRange` minimální a maximální hodnoty na stejný port.<br /><br /> Možné hodnoty jsou v rozsahu od 1 do 65535, včetně (Azure SDK verze 1,7 nebo novější).|  

##  <a name="fixedportrange"></a><a name="FixedPortRange"></a> FixedPortRange  
`FixedPortRange`Prvek určuje rozsah portů, které jsou přiřazeny internímu koncovému bodu nebo vstupnímu koncovému bodu instance, a nastaví port používaný pro připojení s vyrovnáváním zatížení na koncovém bodu.

> [!NOTE]
>  `FixedPortRange`Element pracuje odlišně v závislosti na elementu, ve kterém se nachází. Když `FixedPortRange` je element v `InternalEndpoint` prvku, otevře všechny porty v nástroji pro vyrovnávání zatížení v rámci rozsahu atributů min a Max pro všechny virtuální počítače, na kterých je role spuštěna. Když `FixedPortRange` je element v `InstanceInputEndpoint` elementu, otevře se pouze jeden port v rozsahu atributů min a Max na každém virtuálním počítači, na kterém je role spuštěná.

`FixedPortRange`Element je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.

Následující tabulka popisuje atributy `FixedPortRange` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|min|int|Povinná hodnota. Minimální port v rozsahu. Možné hodnoty jsou v rozsahu od 1 do 65535, včetně (Azure SDK verze 1,7 nebo novější).|  
|max|řetězec|Povinná hodnota. Maximální port v rozsahu. Možné hodnoty jsou v rozsahu od 1 do 65535, včetně (Azure SDK verze 1,7 nebo novější).|  

##  <a name="certificates"></a><a name="Certificates"></a> Certifikáty  
`Certificates`Element popisuje kolekci certifikátů pro webovou roli. Tento prvek je nadřazeným prvkem `Certificate` elementu. Role může mít libovolný počet přidružených certifikátů. Další informace o použití prvku certifikáty najdete v tématu [Úprava definičního souboru služby pomocí certifikátu](../cloud-services/cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="certificate"></a><a name="Certificate"></a> Certifikát  
`Certificate`Element popisuje certifikát, který je přidružen k webové roli.

Následující tabulka popisuje atributy `Certificate` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|name|řetězec|Povinná hodnota. Název pro tento certifikát, který se používá k odkazování na něj, je-li přidružen k `InputEndpoint` elementu https.|  
|storeLocation|řetězec|Povinná hodnota. Umístění úložiště certifikátů, ve kterém se tento certifikát nachází na místním počítači. Možné hodnoty jsou `CurrentUser` a `LocalMachine` .|  
|storeName|řetězec|Povinná hodnota. Název úložiště certifikátů, ve kterém se tento certifikát nachází na místním počítači. Mezi možné hodnoty patří vestavěné názvy obchodů,,,, `My` `Root` `CA` `Trust` `Disallowed` , `TrustedPeople` , `TrustedPublisher` , `AuthRoot` , `AddressBook` nebo libovolný název vlastního úložiště. Pokud je zadán název vlastního úložiště, je úložiště automaticky vytvořeno.|  
|permissionLevel|řetězec|Nepovinný parametr. Určuje přístupová oprávnění udělená procesům rolí. Pokud chcete, aby přístup k privátnímu klíči měly jenom procesy se zvýšenými oprávněními, zadejte `elevated` oprávnění. `limitedOrElevated` oprávnění umožňuje všem procesům rolí přístup k privátnímu klíči. Možné hodnoty jsou `limitedOrElevated` nebo `elevated`. Výchozí hodnota je `limitedOrElevated`.|  

##  <a name="imports"></a><a name="Imports"></a> Objem  
`Imports`Element popisuje kolekci modulů importu pro webovou roli, která přidávají součásti do hostovaného operačního systému. Tento prvek je nadřazeným prvkem `Import` elementu. Tento prvek je nepovinný a role může mít jenom jeden blok importů. 

`Imports`Element je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.

##  <a name="import"></a><a name="Import"></a> Importovat  
`Import`Element určuje modul, který má být přidán do hostovaného operačního systému.

`Import`Element je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.

Následující tabulka popisuje atributy `Import` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|moduleName|řetězec|Povinná hodnota. Název modulu, který se má importovat Platné moduly importu jsou:<br /><br /> – RemoteAccess<br />- RemoteForwarder<br />– Diagnostika<br /><br /> Moduly RemoteAccess a RemoteForwarder umožňují nakonfigurovat instanci role pro připojení ke vzdálené ploše. Další informace najdete v tématu [rozšíření](extensions.md).<br /><br /> Modul diagnostiky umožňuje shromažďovat diagnostická data pro instanci role.|  

##  <a name="runtime"></a><a name="Runtime"></a> Runtime  
`Runtime`Element popisuje kolekci nastavení proměnných prostředí pro webovou roli, která řídí běhové prostředí hostitelského procesu Azure. Tento prvek je nadřazeným prvkem `Environment` elementu. Tento prvek je nepovinný a role může mít jenom jeden blok runtime.

`Runtime`Element je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.

Následující tabulka popisuje atributy `Runtime` prvku:  

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|executionContext|řetězec|Nepovinný parametr. Určuje kontext, ve kterém se spustí proces role. Výchozí kontext je `limited` .<br /><br /> -   `limited` – Proces se spustí bez oprávnění správce.<br />-   `elevated` – Proces se spustí s oprávněními správce.|  

##  <a name="environment"></a><a name="Environment"></a> Hlediska  
`Environment`Element popisuje kolekci nastavení proměnných prostředí pro webovou roli. Tento prvek je nadřazeným prvkem `Variable` elementu. Role může mít nastaven libovolný počet proměnných prostředí.

##  <a name="variable"></a><a name="Variable"></a> Variabilní  
`Variable`Element určuje proměnnou prostředí, která se má nastavit v hostovaném operačním systému.

`Variable`Element je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.

Následující tabulka popisuje atributy `Variable` prvku:  

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|name|řetězec|Povinná hodnota. Název proměnné prostředí, kterou chcete nastavit.|  
|hodnota|řetězec|Nepovinný parametr. Hodnota, která má být nastavena pro proměnnou prostředí. Musíte zahrnout buď atribut hodnoty, nebo `RoleInstanceValue` element.|  

##  <a name="roleinstancevalue"></a><a name="RoleInstanceValue"></a> RoleInstanceValue  
`RoleInstanceValue`Prvek určuje cestu XPath, ze které má být načtena hodnota proměnné.

Následující tabulka popisuje atributy `RoleInstanceValue` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|cestou|řetězec|Nepovinný parametr. Cesta umístění nastavení nasazení pro instanci Další informace naleznete v tématu [konfigurační proměnné s XPath](../cloud-services/cloud-services-role-config-xpath.md).<br /><br /> Musíte zahrnout buď atribut hodnoty, nebo `RoleInstanceValue` element.|  

##  <a name="entrypoint"></a><a name="EntryPoint"></a> Bod  
`EntryPoint`Prvek určuje vstupní bod pro roli. Tento prvek je nadřazený `NetFxEntryPoint` prvky. Tyto prvky umožňují zadat jinou aplikaci, než je výchozí WaWorkerHost.exe, která bude sloužit jako vstupní bod role.

`EntryPoint`Element je k dispozici pouze pomocí sady Azure SDK verze 1,5 nebo vyšší.

##  <a name="netfxentrypoint"></a><a name="NetFxEntryPoint"></a> NetFxEntryPoint  
`NetFxEntryPoint`Element určuje program, který se má spustit pro roli.

> [!NOTE]
>  `NetFxEntryPoint`Element je k dispozici pouze pomocí sady Azure SDK verze 1,5 nebo vyšší.

Následující tabulka popisuje atributy `NetFxEntryPoint` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|Doplňk|řetězec|Povinná hodnota. Cesta a název souboru sestavení, který obsahuje vstupní bod. Cesta je relativní ke složce **\\ %ROLEROOT%\Approot** (nespecifikuje **\\ %ROLEROOT%\Approot** v `commandLine` , předpokládá se). **% ROLEROOT%** je proměnná prostředí udržovaná službou Azure, která představuje umístění kořenové složky pro vaši roli. Složka **\\ %ROLEROOT%\Approot** představuje složku aplikace pro vaši roli.<br /><br /> Pro role umožní je cesta vždy relativní vzhledem ke složce **\\ %ROLEROOT%\Approot\bin** .<br /><br /> V případě úplných webových rolí služby IIS a IIS Express, pokud nebylo nalezeno sestavení vzhledem ke složce **\\ %ROLEROOT%\Approot** , je prohledána **\\ %ROLEROOT%\Approot\bin** .<br /><br /> Toto chování při návratu do úplné služby IIS není doporučeným osvědčeným postupem a v budoucích verzích je možná odebrat.|  
|targetFrameworkVersion|řetězec|Povinná hodnota. Verze rozhraní .NET Framework, na které bylo sestavení sestaveno. Například `targetFrameworkVersion="v4.0"`.|  

##  <a name="sites"></a><a name="Sites"></a> Místa  
`Sites`Element popisuje kolekci webů a webových aplikací, které jsou hostovány ve webové roli. Tento prvek je nadřazeným prvkem `Site` elementu. Pokud nezadáte `Sites` element, Webová role je hostována jako starší webová role a můžete mít pouze jeden web hostovaný ve vaší webové roli. Tento prvek je nepovinný a role může mít jenom jeden blok webů.

`Sites`Element je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.

##  <a name="site"></a><a name="Site"></a> Webovém  
`Site`Element určuje web nebo webovou aplikaci, která je součástí webové role.

`Site`Element je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.

Následující tabulka popisuje atributy `Site` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|name|řetězec|Povinná hodnota. Název webu nebo aplikace.|  
|physicalDirectory|řetězec|Umístění adresáře obsahu pro kořen webu Umístění lze zadat jako absolutní cestu nebo relativní vzhledem k umístění csdef.|  

##  <a name="virtualapplication"></a><a name="VirtualApplication"></a> VirtualApplication  
`VirtualApplication`Element definuje aplikaci v Internetová informační služba (IIS) 7 je seskupení souborů, které doručují obsah, nebo poskytuje služby přes protokoly, jako je http. Když ve službě IIS 7 vytvoříte aplikaci, bude cesta aplikace součástí adresy URL webu.

`VirtualApplication`Element je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.

Následující tabulka popisuje atributy `VirtualApplication` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|name|řetězec|Povinná hodnota. Určuje název, který identifikuje virtuální aplikaci.|  
|physicalDirectory|řetězec|Povinná hodnota. Určuje cestu k vývojovému počítači, který obsahuje virtuální aplikaci. V emulátoru výpočetní služby je služba IIS nakonfigurovaná tak, aby načetla obsah z tohoto umístění. Při nasazování do Azure se obsah fyzického adresáře zabalí spolu se zbytkem služby. Po nasazení balíčku služby do Azure se služba IIS nakonfiguruje s umístěním rozbaleného obsahu.|  

##  <a name="virtualdirectory"></a><a name="VirtualDirectory"></a> Virtualdirector  
`VirtualDirectory`Element určuje název adresáře (také označovaný jako cesta), který zadáte ve službě IIS a mapuje na fyzický adresář na místním nebo vzdáleném serveru.

`VirtualDirectory`Element je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.

Následující tabulka popisuje atributy `VirtualDirectory` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|name|řetězec|Povinná hodnota. Určuje název, který identifikuje virtuální adresář.|  
|hodnota|physicalDirectory|Povinná hodnota. Určuje cestu k vývojovému počítači, který obsahuje obsah webu nebo virtuálního adresáře. V emulátoru výpočetní služby je služba IIS nakonfigurovaná tak, aby načetla obsah z tohoto umístění. Při nasazování do Azure se obsah fyzického adresáře zabalí spolu se zbytkem služby. Po nasazení balíčku služby do Azure se služba IIS nakonfiguruje s umístěním rozbaleného obsahu.|  

##  <a name="bindings"></a><a name="Bindings"></a> Vazeb  
`Bindings`Element popisuje kolekci vazeb pro web. Je nadřazeným prvkem `Binding` elementu. Element je vyžadován pro každý `Site` prvek. Další informace o konfiguraci koncových bodů najdete v tématu [povolení komunikace pro instance rolí](../cloud-services/cloud-services-enable-communication-role-instances.md).

`Bindings`Element je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.

##  <a name="binding"></a><a name="Binding"></a> Ovládacího  
`Binding`Element určuje informace o konfiguraci požadované pro požadavky na komunikaci s webem nebo webovou aplikací.

`Binding`Element je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|name|řetězec|Povinná hodnota. Určuje název pro identifikaci vazby.|  
|koncový bod|řetězec|Povinná hodnota. Určuje název koncového bodu, ke kterému se má vytvořit vazba.|  
|hostHeader|řetězec|Nepovinný parametr. Určuje název hostitele, který umožňuje hostovat více lokalit s různými názvy hostitelů na jedné kombinaci IP adresy nebo čísla portu.|  

##  <a name="startup"></a><a name="Startup"></a> Úvod  
`Startup`Element popisuje kolekci úkolů, které se spouštějí při spuštění role. Tento element může být nadřazený `Variable` prvek elementu. Další informace o použití úloh po spuštění role najdete v tématu [Postup konfigurace úloh po spuštění](../cloud-services/cloud-services-startup-tasks.md). Tento prvek je nepovinný a role může mít jenom jeden spouštěcí blok.

Následující tabulka popisuje atribut `Startup` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|upřednostněn|int|Pouze pro interní použití.|  

##  <a name="task"></a><a name="Task"></a> Hybn  
`Task`Element určuje úlohu po spuštění, která probíhá při spuštění role. Úlohy po spuštění lze použít k provádění úloh, které připravují roli ke spouštění takových softwarových součástí nebo spouštění jiných aplikací. Úlohy jsou spouštěny v pořadí, ve kterém jsou uvedeny v rámci `Startup` bloku elementu.

`Task`Element je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.

Následující tabulka popisuje atributy `Task` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|Řádek|řetězec|Povinná hodnota. Skript, jako je například soubor CMD, obsahující příkazy, které mají být spuštěny. Spouštěcí příkaz a dávkové soubory musí být uloženy ve formátu ANSI. Formáty souborů, které nastavují značku pořadí bajtů na začátku souboru, nebudou pracovat správně.|  
|executionContext|řetězec|Určuje kontext, ve kterém se skript spustí.<br /><br /> -   `limited` [Výchozí] – spusťte se stejnými oprávněními jako role hostující proces.<br />-   `elevated` – Spusťte s oprávněními správce.|  
|taskType|řetězec|Určuje chování při provádění příkazu.<br /><br /> -   `simple` [Výchozí] – systém čeká na ukončení úlohy před spuštěním jakékoli jiné úlohy.<br />-   `background` – Systém nečeká na ukončení úlohy.<br />-   `foreground` – Podobně jako na pozadí, s výjimkou role nebude restartován, dokud nebudou všechny úlohy na popředí ukončeny.|  

##  <a name="contents"></a><a name="Contents"></a> Obsah  
`Contents`Prvek popisuje shromažďování obsahu webové role. Tento prvek je nadřazeným prvkem `Content` elementu.

`Contents`Element je k dispozici pouze pomocí sady Azure SDK verze 1,5 nebo vyšší.

##  <a name="content"></a><a name="Content"></a> Sušin  
`Content`Element definuje zdrojové umístění obsahu, který se má zkopírovat na virtuální počítač Azure, a cílovou cestu, do které se má zkopírovat.

`Content`Element je k dispozici pouze pomocí sady Azure SDK verze 1,5 nebo vyšší.

Následující tabulka popisuje atributy `Content` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|destination|řetězec|Povinná hodnota. Umístění na virtuálním počítači Azure, na který je umístěn obsah. Toto umístění je relativní vzhledem ke složce **%ROLEROOT%\Approot**.|  

Tento prvek je nadřazeným prvkem `SourceDirectory` elementu.

##  <a name="sourcedirectory"></a><a name="SourceDirectory"></a> SourceDirectory  
`SourceDirectory`Element definuje místní adresář, ze kterého se kopíruje obsah. Tento prvek slouží k zadání místního obsahu pro kopírování do virtuálního počítače Azure.

`SourceDirectory`Element je k dispozici pouze pomocí sady Azure SDK verze 1,5 nebo vyšší.

Následující tabulka popisuje atributy `SourceDirectory` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|program|řetězec|Povinná hodnota. Relativní nebo absolutní cesta k místnímu adresáři, jehož obsah se zkopíruje na virtuální počítač Azure. Rozšíření proměnných prostředí v cestě k adresáři je podporováno.|  
  
## <a name="see-also"></a>Viz také
[Schéma definice cloudové služby (Rozšířená podpora)](schema-csdef-file.md).




