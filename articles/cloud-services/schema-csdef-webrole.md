---
title: Schéma webových rolí Azure cloudových služeb – zabezpečení virtuálních webových rolí | Dokumenty společnosti Microsoft
description: Webová role Azure je přizpůsobená pro programování webových aplikací podporujících ASP.NET, PHP, WCF a FastCGI. Další informace o elementech definice služby webové role.
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
author: tgore03
ms.author: tagore
ms.openlocfilehash: 4368bb38a280461fdd77348de60a0e5793ee9582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535676"
---
# <a name="azure-cloud-services-definition-webrole-schema"></a>Schéma webových rolí definice azure cloudových služeb
Webová role Azure je role, která je přizpůsobena pro programování webových aplikací, jak podporuje služba IIS 7, jako je ASP.NET, PHP, Windows Communication Foundation a FastCGI.

Výchozí přípona pro soubor definice služby je .csdef.

## <a name="basic-service-definition-schema-for-a-web-role"></a>Schéma definice základní služby pro webovou roli  
Základní formát definičního souboru služby obsahujícího webovou roli je následující.

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
Soubor definice služby obsahuje tyto prvky, popsané podrobně v následujících částech v tomto tématu:  

[Webová role](#WebRole)

[Nastavení konfigurace](#ConfigurationSettings)

[Nastavení](#Setting)

[Místní zdroje](#LocalResources)

[Localstorage](#LocalStorage)

[Koncové body](#Endpoints)

[Vnitřní koncový bod](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[Přidělit PublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Certifikáty](#Certificates)

[Certifikát](#Certificate)

[Dovoz](#Imports)

[Import](#Import)

[Modul runtime](#Runtime)

[Prostředí](#Environment)

[Proměnná](#Variable)

[Hodnota instance role](#RoleInstanceValue)

[NetFxEntryPoint](#NetFxEntryPoint)

[Lokality](#Sites)

[Web](#Site)

[Virtuální aplikace](#VirtualApplication)

[Virtuální aplikace](#VirtualApplication)

[Vazby](#Bindings)

[Vazby](#Binding)

[Spuštění](#Startup)

[Úkol](#Task)

[Obsah](#Contents)

[Obsah](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="webrole"></a><a name="WebRole"></a>Webová role  
Tento `WebRole` prvek popisuje roli přizpůsobenou pro programování webových aplikací, jak podporuje služby IIS 7 a ASP.NET. Služba může obsahovat nula nebo více webových rolí.

Následující tabulka popisuje atributy `WebRole` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Název webové role. Název role musí být jedinečný.|  
|enableNativeCodeExecution|Boolean|Nepovinný parametr. Výchozí hodnota `true`je ; Nativní spuštění kódu a úplný vztah důvěryhodnosti jsou ve výchozím nastavení povoleny. Nastavte tento `false` atribut zakázat spuštění nativního kódu pro webovou roli a místo toho použijte částečný vztah důvěryhodnosti Azure.|  
|vmsize|řetězec|Nepovinný parametr. Nastavte tuto hodnotu pro změnu velikosti virtuálního počítače, který je přidělen roli. Výchozí hodnota je `Small`. Další informace najdete [v tématu Velikosti virtuálních strojů pro cloudové služby](cloud-services-sizes-specs.md).|  

##  <a name="configurationsettings"></a><a name="ConfigurationSettings"></a>Nastavení konfigurace  
Prvek `ConfigurationSettings` popisuje kolekci nastavení konfigurace pro webovou roli. Tento prvek je nadřazený `Setting` prvek.

##  <a name="setting"></a><a name="Setting"></a>Nastavení  
Prvek `Setting` popisuje dvojici názvů a hodnot, která určuje nastavení konfigurace pro instanci role.

Následující tabulka popisuje atributy `Setting` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Jedinečný název pro nastavení konfigurace.|  

Nastavení konfigurace role jsou dvojice názvů a hodnot, které jsou deklarovány v souboru definice služby a nastaveny v konfiguračním souboru služby.

##  <a name="localresources"></a><a name="LocalResources"></a>Místní zdroje  
Prvek `LocalResources` popisuje kolekci prostředků místního úložiště pro webovou roli. Tento prvek je nadřazený `LocalStorage` prvek.

##  <a name="localstorage"></a><a name="LocalStorage"></a>Localstorage  
Prvek `LocalStorage` identifikuje prostředek místního úložiště, který poskytuje místo v systému souborů pro službu za běhu. Role může definovat nula nebo více prostředků místního úložiště.

> [!NOTE]
>  Prvek `LocalStorage` se může zobrazit jako `WebRole` podřízený prvek pro podporu kompatibility s dřívějšími verzemi sady Azure SDK.

Následující tabulka popisuje atributy `LocalStorage` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Jedinečný název místního úložiště.|  
|cleanOnRoleRecycle|Boolean|Nepovinný parametr. Označuje, zda má být místní úložiště vyčištěno při restartování role. Výchozí hodnota `true`je .|  
|sizeInMb|int|Nepovinný parametr. Požadované množství úložného prostoru, které chcete přidělit pro místní úložiště v MB. Pokud není zadán, výchozí přidělené místo úložiště je 100 MB. Minimální velikost úložného prostoru, který může být přidělen, je 1 MB.<br /><br /> Maximální velikost místních prostředků závisí na velikosti virtuálního počítače. Další informace najdete [v tématu Velikosti virtuálních strojů pro cloudové služby](cloud-services-sizes-specs.md).|  
  
Název adresáře přiděleného místnímu prostředku úložiště odpovídá hodnotě poskytnuté pro atribut name.

##  <a name="endpoints"></a><a name="Endpoints"></a>Koncové body  
Prvek `Endpoints` popisuje kolekci vstupní (externí), interní a instanční vstupní koncové body pro roli. Tento prvek je nadřazený `InputEndpoint`prvek , `InternalEndpoint`a `InstanceInputEndpoint` prvky.

Vstupní a vnitřní koncové body jsou přiděleny samostatně. Služba může mít celkem 25 vstupních vstupních, interních a instančních vstupních koncových bodů, které mohou být přiděleny mezi 25 rolí povolených ve službě. Například pokud máte 5 rolí můžete přidělit 5 vstupníkoncové body na roli nebo můžete přidělit 25 vstupních koncových bodů na jednu roli nebo můžete přidělit 1 vstupní koncový bod každý 25 rolí.

> [!NOTE]
>  Každá nasazená role vyžaduje jednu instanci na roli. Výchozí zřizování pro předplatné je omezeno na 20 jader a proto je omezeno na 20 instancí role. Pokud vaše aplikace vyžaduje více instancí, než je poskytováno výchozí zřizování viz [Fakturace, správa předplatného a podpora kvót](https://azure.microsoft.com/support/options/) pro další informace o zvýšení kvóty.

##  <a name="inputendpoint"></a><a name="InputEndpoint"></a>Vstupní koncový bod  
Prvek `InputEndpoint` popisuje externí koncový bod pro webovou roli.

Můžete definovat více koncových bodů, které jsou kombinací koncových bodů HTTP, HTTPS, UDP a TCP. Můžete zadat libovolné číslo portu, které zvolíte pro vstupní koncový bod, ale čísla portů určená pro každou roli ve službě musí být jedinečná. Pokud například zadáte, že webová role používá port 80 pro protokol HTTP a port 443 pro protokol HTTPS, můžete zadat, že druhá webová role používá port 8080 pro protokol HTTP a port 8043 pro protokol HTTPS.

Následující tabulka popisuje atributy `InputEndpoint` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Jedinečný název pro externí koncový bod.|  
|Protokol|řetězec|Povinná hodnota. Přenosový protokol pro externí koncový bod. Pro webovou roli jsou `HTTP` `HTTPS`možné `UDP`hodnoty `TCP`, , , nebo .|  
|port|int|Povinná hodnota. Port pro externí koncový bod. Můžete zadat libovolné číslo portu, které zvolíte, ale čísla portů určená pro každou roli ve službě musí být jedinečná.<br /><br /> Možné hodnoty se pohybují mezi 1 a 65535, včetně (Azure SDK verze 1.7 nebo vyšší).|  
|certifikát|řetězec|Vyžadováno pro koncový bod HTTPS. Název certifikátu definovaného `Certificate` elementem.|  
|localPort|int|Nepovinný parametr. Určuje port používaný pro interní připojení v koncovém bodě. Atribut `localPort` mapuje externí port v koncovém bodě na interní port v roli. To je užitečné ve scénářích, kde role musí komunikovat s vnitřní součástna portu, který se liší od toho, který je vystaven externě.<br /><br /> Pokud není zadán, `localPort` hodnota je stejná jako `port` atribut. Nastavte hodnotu `localPort` "*" pro automatické přiřazení nepřiděleného portu, který je zjistitelný pomocí rozhraní RUNTIME API.<br /><br /> Možné hodnoty se pohybují mezi 1 a 65535, včetně (Azure SDK verze 1.7 nebo vyšší).<br /><br /> Atribut `localPort` je k dispozici pouze pomocí sady Azure SDK verze 1.3 nebo vyšší.|  
|ignoreRoleInstanceStatus|Boolean|Nepovinný parametr. Pokud je hodnota tohoto atributu nastavena na `true`, je stav služby ignorován a koncový bod nebude odebrán nástrojem pro vyrovnávání zatížení. Nastavení této `true` hodnoty na užitečné pro ladění zaneprázdněných instancí služby. Výchozí hodnota je `false`. **Poznámka:**  Koncový bod může stále přijímat provoz i v případě, že role není ve stavu Připraveno.|  
|loadBalancerProbe|řetězec|Nepovinný parametr. Název sondy pro vyrovnávání zatížení přidružený ke vstupnímu koncovému bodu. Další informace naleznete v [tématu LoadBalancerProbe Schéma](schema-csdef-loadbalancerprobe.md).|  

##  <a name="internalendpoint"></a><a name="InternalEndpoint"></a>Vnitřní koncový bod  
Prvek `InternalEndpoint` popisuje vnitřní koncový bod pro webovou roli. Interní koncový bod je k dispozici pouze pro jiné instance rolí spuštěné v rámci služby; není k dispozici klientům mimo službu. Webové role, které `Sites` neobsahují prvek může mít pouze jeden HTTP, UDP nebo TCP interní koncový bod.

Následující tabulka popisuje atributy `InternalEndpoint` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Jedinečný název interního koncového bodu.|  
|Protokol|řetězec|Povinná hodnota. Přenosový protokol pro vnitřní koncový bod. Možné hodnoty `HTTP` `TCP`jsou `UDP`, `ANY`, , nebo .<br /><br /> Hodnota `ANY` určuje, že je povolen libovolný protokol, libovolný port.|  
|port|int|Nepovinný parametr. Port používaný pro vnitřní připojení s vyrovnáváním zatížení v koncovém bodě. Koncový bod s vyrovnáváním zatížení používá dva porty. Port používaný pro veřejnou IP adresu a port používaný na privátní IP adrese. Obvykle se jedná o tyto jsou nastaveny na stejné, ale můžete použít různé porty.<br /><br /> Možné hodnoty se pohybují mezi 1 a 65535, včetně (Azure SDK verze 1.7 nebo vyšší).<br /><br /> Atribut `Port` je k dispozici pouze pomocí sady Azure SDK verze 1.3 nebo vyšší.|  

##  <a name="instanceinputendpoint"></a><a name="InstanceInputEndpoint"></a>InstanceInputEndpoint  
Prvek `InstanceInputEndpoint` popisuje koncový bod vstupu instance do webové role. Koncový bod vstupu instance je přidružen k konkrétní instanci role pomocí předávání portů v systému vyrovnávání zatížení. Každý koncový bod vstupu instance je mapován na konkrétní port z rozsahu možných portů. Tento prvek je nadřazený `AllocatePublicPortFrom` prvek.

Prvek `InstanceInputEndpoint` je k dispozici pouze pomocí sady Azure SDK verze 1.7 nebo vyšší.

Následující tabulka popisuje atributy `InstanceInputEndpoint` prvku.
  
| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Jedinečný název koncového bodu.|  
|localPort|int|Povinná hodnota. Určuje interní port, který budou poslouchat všechny instance rolí, aby bylo možné přijímat příchozí přenosy předávané z doby tažného systému. Možné hodnoty se pohybují mezi 1 a 65535 včetně.|  
|Protokol|řetězec|Povinná hodnota. Přenosový protokol pro vnitřní koncový bod. Možné hodnoty jsou `udp` nebo `tcp`. Použijte `tcp` pro provoz založený na http/https.|  
  
##  <a name="allocatepublicportfrom"></a><a name="AllocatePublicPortFrom"></a>Přidělit PublicPortFrom  
Prvek `AllocatePublicPortFrom` popisuje rozsah veřejných portů, který mohou externí zákazníci použít pro přístup ke každému koncovému bodu vstupu instance. Číslo veřejného (VIP) portu je přiděleno z tohoto rozsahu a přiřazeno každému koncovému bodu instance jednotlivých rolí během nasazení a aktualizace klienta. Tento prvek je nadřazený `FixedPortRange` prvek.

Prvek `AllocatePublicPortFrom` je k dispozici pouze pomocí sady Azure SDK verze 1.7 nebo vyšší.

##  <a name="fixedport"></a><a name="FixedPort"></a>FixedPort  
Prvek `FixedPort` určuje port pro vnitřní koncový bod, který umožňuje připojení s vyrovnáváním zatížení v koncovém bodě.

Prvek `FixedPort` je k dispozici pouze pomocí sady Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `FixedPort` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|port|int|Povinná hodnota. Port pro vnitřní koncový bod. To má stejný účinek `FixedPortRange` jako nastavení min a max na stejný port.<br /><br /> Možné hodnoty se pohybují mezi 1 a 65535, včetně (Azure SDK verze 1.7 nebo vyšší).|  

##  <a name="fixedportrange"></a><a name="FixedPortRange"></a>FixedPortRange  
Prvek `FixedPortRange` určuje rozsah portů, které jsou přiřazeny k koncovému koncovému bodu nebo vstupnímu koncovému bodu instance, a nastaví port používaný pro připojení s vyrovnáváním zatížení v koncovém bodě.

> [!NOTE]
>  Prvek `FixedPortRange` funguje odlišně v závislosti na prvku, ve kterém je umístěn. Když `FixedPortRange` je prvek `InternalEndpoint` v elementu, otevře všechny porty v nástrojpro vyrovnávání zatížení v rozsahu min a max atributy pro všechny virtuální počítače, na kterých je role spuštěna. Pokud `FixedPortRange` je prvek `InstanceInputEndpoint` v elementu, otevře pouze jeden port v rozsahu min a max atributy na každém virtuálním počítači spuštěné role.

Prvek `FixedPortRange` je k dispozici pouze pomocí sady Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `FixedPortRange` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|min|int|Povinná hodnota. Minimální port v rozsahu. Možné hodnoty se pohybují mezi 1 a 65535, včetně (Azure SDK verze 1.7 nebo vyšší).|  
|max|řetězec|Povinná hodnota. Maximální port v rozsahu. Možné hodnoty se pohybují mezi 1 a 65535, včetně (Azure SDK verze 1.7 nebo vyšší).|  

##  <a name="certificates"></a><a name="Certificates"></a>Certifikáty  
Prvek `Certificates` popisuje shromažďování certifikátů pro webovou roli. Tento prvek je nadřazený `Certificate` prvek. Role může mít libovolný počet přidružených certifikátů. Další informace o použití prvku certifikátů naleznete [v tématu Úprava souboru definice služby pomocí certifikátu](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="certificate"></a><a name="Certificate"></a>Certifikát  
Prvek `Certificate` popisuje certifikát, který je přidružen k webové roli.

Následující tabulka popisuje atributy `Certificate` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Název tohoto certifikátu, který se používá k odkazování na `InputEndpoint` něj, když je přidružen k elementu HTTPS.|  
|Storelocation|řetězec|Povinná hodnota. Umístění úložiště certifikátů, kde lze tento certifikát nalézt v místním počítači. Možné hodnoty `CurrentUser` `LocalMachine`jsou a .|  
|Storename|řetězec|Povinná hodnota. Název úložiště certifikátů, kde je tento certifikát umístěn v místním počítači. Možné hodnoty zahrnují předdefinované `My`názvy `CA` `Trust`obchodů `Disallowed` `TrustedPeople`, `TrustedPublisher` `Root` `AuthRoot`, `AddressBook`, , , , , nebo jakýkoli vlastní název obchodu. Pokud je zadán vlastní název obchodu, úložiště se automaticky vytvoří.|  
|permissionLevel|řetězec|Nepovinný parametr. Určuje přístupová oprávnění udělená procesům rolí. Pokud chcete, aby pouze procesy se zvýšenými oprávněními měly přístup k soukromému klíči, zadejte `elevated` oprávnění. `limitedOrElevated`oprávnění umožňuje všem procesům rolí přístup k soukromému klíči. Možné hodnoty jsou `limitedOrElevated` nebo `elevated`. Výchozí hodnota je `limitedOrElevated`.|  

##  <a name="imports"></a><a name="Imports"></a>Dovoz  
Prvek `Imports` popisuje kolekci modulů importu pro webovou roli, která přidává součásti do hostovaného operačního systému. Tento prvek je nadřazený `Import` prvek. Tento prvek je volitelný a role může mít pouze jeden blok importu. 

Prvek `Imports` je k dispozici pouze pomocí sady Azure SDK verze 1.3 nebo vyšší.

##  <a name="import"></a><a name="Import"></a>Import  
Prvek `Import` určuje modul, který má být přidán do hostovaného operačního systému.

Prvek `Import` je k dispozici pouze pomocí sady Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `Import` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|název modulu|řetězec|Povinná hodnota. Název modulu, který chcete importovat. Platné importní moduly jsou:<br /><br /> - Vzdálený přístup<br />- RemoteForwarder<br />- Diagnostika<br /><br /> Moduly Vzdáleného přístupu a Vzdáleného vzdáleného dálového připojení umožňují konfigurovat instanci role pro připojení ke vzdálené ploše. Další informace naleznete v [tématu Enable Remote Desktop Connection](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> Modul Diagnostika umožňuje shromažďovat diagnostická data pro instanci role.|  

##  <a name="runtime"></a><a name="Runtime"></a>Runtime  
Prvek `Runtime` popisuje kolekci nastavení proměnných prostředí pro webovou roli, která řídí runtime prostředí hostitelského procesu Azure. Tento prvek je nadřazený `Environment` prvek. Tento prvek je volitelný a role může mít pouze jeden blok runtime.

Prvek `Runtime` je k dispozici pouze pomocí sady Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `Runtime` prvku:  

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|Executioncontext|řetězec|Nepovinný parametr. Určuje kontext, ve kterém je spuštěn proces role. Výchozí kontext `limited`je .<br /><br /> -   `limited`– Proces je spuštěn bez oprávnění správce.<br />-   `elevated`– Proces je spuštěn s oprávněními správce.|  

##  <a name="environment"></a><a name="Environment"></a>Prostředí  
Prvek `Environment` popisuje kolekci nastavení proměnných prostředí pro webovou roli. Tento prvek je nadřazený `Variable` prvek. Role může mít libovolný počet proměnných prostředí nastavit.

##  <a name="variable"></a><a name="Variable"></a>Proměnné  
Prvek `Variable` určuje proměnnou prostředí, která má být nastavena v provozním zařízení hosta.

Prvek `Variable` je k dispozici pouze pomocí sady Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `Variable` prvku:  

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Název proměnné prostředí, která má být nastavena.|  
|value|řetězec|Nepovinný parametr. Hodnota nastavená pro proměnnou prostředí. Je nutné zahrnout atribut hodnoty `RoleInstanceValue` nebo prvek.|  

##  <a name="roleinstancevalue"></a><a name="RoleInstanceValue"></a>Hodnota instance role  
Prvek `RoleInstanceValue` určuje xPath, ze kterého chcete načíst hodnotu proměnné.

Následující tabulka popisuje atributy `RoleInstanceValue` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|Xpath|řetězec|Nepovinný parametr. Cesta k umístění nastavení nasazení pro instanci. Další informace naleznete v [tématu Configuration variables with XPath](cloud-services-role-config-xpath.md).<br /><br /> Je nutné zahrnout atribut hodnoty `RoleInstanceValue` nebo prvek.|  

##  <a name="entrypoint"></a><a name="EntryPoint"></a>Entrypoint  
Prvek `EntryPoint` určuje vstupní bod pro roli. Tento prvek je nadřazený `NetFxEntryPoint` prvků. Tyto prvky umožňují zadat jinou aplikaci než výchozí soubor WaWorkerHost.exe, která bude sloužit jako vstupní bod role.

Prvek `EntryPoint` je k dispozici pouze pomocí sady Azure SDK verze 1.5 nebo vyšší.

##  <a name="netfxentrypoint"></a><a name="NetFxEntryPoint"></a>NetFxEntryPoint  
Prvek `NetFxEntryPoint` určuje program, který má být spuštěn pro roli.

> [!NOTE]
>  Prvek `NetFxEntryPoint` je k dispozici pouze pomocí sady Azure SDK verze 1.5 nebo vyšší.

Následující tabulka popisuje atributy `NetFxEntryPoint` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|Assemblyname|řetězec|Povinná hodnota. Cesta a název souboru sestavení obsahující vstupní bod. Cesta je relativní ke složce `commandLine` ** \\%ROLEROOT%\Approot** (nezadávejte ** \\%ROLEROOT%\Approot** v , předpokládá se). **%ROLEROOT%** je proměnná prostředí spravovaná službou Azure a představuje umístění kořenové složky pro vaši roli. Složka ** \\%ROLEROOT%\Approot** představuje složku aplikace pro vaši roli.<br /><br /> U rolí HWC je cesta vždy relativní vzhledem ke složce ** \\%ROLEROOT%\Approot\bin.**<br /><br /> Pokud u úplných webových rolí služby IIS a IIS Express nelze sestavení nalézt vzhledem ke ** \\složce %ROLEROOT%\Approot,** bude prohledána ** \\přihrádka %ROLEROOT%\Approot\.**<br /><br /> Toto chování fall back pro úplnou iis není doporučený osvědčený postup a možná i odebrány v budoucích verzích.|  
|cílFrameworkVersion|řetězec|Povinná hodnota. Verze rozhraní .NET framework, na kterém bylo sestavení vytvořeno. Například, `targetFrameworkVersion="v4.0"`.|  

##  <a name="sites"></a><a name="Sites"></a>Stránky  
Prvek `Sites` popisuje kolekci webů a webových aplikací, které jsou hostovány ve webové roli. Tento prvek je nadřazený `Site` prvek. Pokud nezadáte `Sites` prvek, vaše webová role je hostována jako starší webová role a ve vaší webové roli může být hostován pouze jeden web. Tento prvek je volitelný a role může mít pouze jeden blok lokalit.

Prvek `Sites` je k dispozici pouze pomocí sady Azure SDK verze 1.3 nebo vyšší.

##  <a name="site"></a><a name="Site"></a>Stránky  
Prvek `Site` určuje web nebo webovou aplikaci, která je součástí webové role.

Prvek `Site` je k dispozici pouze pomocí sady Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `Site` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Název webové stránky nebo aplikace.|  
|physicalDirectory|řetězec|Umístění adresáře obsahu pro kořenový adresář webu. Umístění lze zadat jako absolutní cestu nebo vzhledem k umístění .csdef.|  

##  <a name="virtualapplication"></a><a name="VirtualApplication"></a>Virtuální aplikace  
Prvek `VirtualApplication` definuje aplikaci v Internetové informační službě (IIS) 7 je seskupení souborů, které doručují obsah nebo poskytují služby přes protokoly, jako je například HTTP. Při vytváření aplikace ve službě IIS 7 se cesta aplikace stane součástí adresy URL webu.

Prvek `VirtualApplication` je k dispozici pouze pomocí sady Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `VirtualApplication` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Určuje název pro identifikaci virtuální aplikace.|  
|physicalDirectory|řetězec|Povinná hodnota. Určuje cestu ve vývojovém počítači, který obsahuje virtuální aplikaci. V emulátoru výpočetního prostředí je služby IIS nakonfigurována pro načtení obsahu z tohoto umístění. Při nasazování do Azure, obsah fyzického adresáře jsou zabaleny spolu se zbytkem služby. Když je balíček služeb nasazený do Azure, služba IIS je nakonfigurovaná s umístěním rozbaleného obsahu.|  

##  <a name="virtualdirectory"></a><a name="VirtualDirectory"></a>Virtualdirectory  
Prvek `VirtualDirectory` určuje název adresáře (označovaný také jako cesta), který zadáte ve službě IIS a namapuje te na fyzický adresář na místním nebo vzdáleném serveru.

Prvek `VirtualDirectory` je k dispozici pouze pomocí sady Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `VirtualDirectory` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Určuje název pro identifikaci virtuálního adresáře.|  
|value|physicalDirectory|Povinná hodnota. Určuje cestu ve vývojovém počítači, který obsahuje obsah webu nebo virtuálního adresáře. V emulátoru výpočetního prostředí je služby IIS nakonfigurována pro načtení obsahu z tohoto umístění. Při nasazování do Azure, obsah fyzického adresáře jsou zabaleny spolu se zbytkem služby. Když je balíček služeb nasazený do Azure, služba IIS je nakonfigurovaná s umístěním rozbaleného obsahu.|  

##  <a name="bindings"></a><a name="Bindings"></a>Vazby  
Prvek `Bindings` popisuje kolekci vazeb pro web. Je nadřazený `Binding` prvek prvku. Prvek je vyžadován `Site` pro každý prvek. Další informace o konfiguraci koncových bodů naleznete v [tématu Povolení komunikace pro instance rolí](cloud-services-enable-communication-role-instances.md).

Prvek `Bindings` je k dispozici pouze pomocí sady Azure SDK verze 1.3 nebo vyšší.

##  <a name="binding"></a><a name="Binding"></a>Vazba  
Prvek `Binding` určuje informace o konfiguraci potřebné pro žádosti o komunikaci s webem nebo webovou aplikací.

Prvek `Binding` je k dispozici pouze pomocí sady Azure SDK verze 1.3 nebo vyšší.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Určuje název k identifikaci vazby.|  
|název koncového bodu|řetězec|Povinná hodnota. Určuje název koncového bodu, na který se má vytvořit vazbu.|  
|hostHeader|řetězec|Nepovinný parametr. Určuje název hostitele, který umožňuje hostovat více serverů s různými názvy hostitelů v jedné kombinaci čísel IP adresa/port.|  

##  <a name="startup"></a><a name="Startup"></a>Spuštění  
Prvek `Startup` popisuje kolekci úloh, které se spustí při spuštění role. Tento prvek může být `Variable` nadřazený prvku. Další informace o použití úloh při spuštění role naleznete v tématu [Konfigurace úloh při spuštění](cloud-services-startup-tasks.md). Tento prvek je volitelný a role může mít pouze jeden spouštěcí blok.

Následující tabulka popisuje atribut `Startup` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|Prioritou|int|Pouze pro interní použití.|  

##  <a name="task"></a><a name="Task"></a>Úkol  
Prvek `Task` určuje úlohu spuštění, která probíhá při spuštění role. Úlohy při spuštění lze použít k provádění úloh, které připravují roli ke spuštění těchto instalačních softwarových součástí nebo ke spuštění jiných aplikací. Úkoly spustit v pořadí, ve `Startup` kterém se zobrazí v rámci bloku prvku.

Prvek `Task` je k dispozici pouze pomocí sady Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `Task` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|Commandline|řetězec|Povinná hodnota. Skript, například soubor CMD, obsahující příkazy ke spuštění. Příkaz při spuštění a dávkové soubory musí být uloženy ve formátu ANSI. Formáty souborů, které na staví značku pořadí bajtů na začátku souboru, nebudou správně zpracovány.|  
|Executioncontext|řetězec|Určuje kontext, ve kterém je skript spuštěn.<br /><br /> -   `limited`[Výchozí] – Spustit se stejnými oprávněními jako role hostující proces.<br />-   `elevated`– Spustit s oprávněními správce.|  
|taskType|řetězec|Určuje chování spuštění příkazu.<br /><br /> -   `simple`[Výchozí] – Systém čeká na ukončení úlohy před spuštěním jiných úloh.<br />-   `background`– Systém nečeká na ukončení úlohy.<br />-   `foreground`– Podobně jako na pozadí, s výjimkou role není restartován, dokud všechny úkoly popředí ukončit.|  

##  <a name="contents"></a><a name="Contents"></a>Obsah  
Prvek `Contents` popisuje kolekci obsahu pro webovou roli. Tento prvek je nadřazený `Content` prvek.

Prvek `Contents` je k dispozici pouze pomocí sady Azure SDK verze 1.5 nebo vyšší.

##  <a name="content"></a><a name="Content"></a>Obsah  
Prvek `Content` definuje zdrojové umístění obsahu, který se má zkopírovat do virtuálního počítače Azure, a cílovou cestu, do které se zkopíruje.

Prvek `Content` je k dispozici pouze pomocí sady Azure SDK verze 1.5 nebo vyšší.

Následující tabulka popisuje atributy `Content` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|destination|řetězec|Povinná hodnota. Umístění na virtuálním počítači Azure, do kterého je umístěn obsah. Toto umístění je relativní vzhledem ke složce **%ROLEROOT%\Approot**.|  

Tento prvek je nadřazený prvek `SourceDirectory` prvku.

##  <a name="sourcedirectory"></a><a name="SourceDirectory"></a>SourceDirectory  
Prvek `SourceDirectory` definuje místní adresář, ze kterého je obsah zkopírován. Tento prvek slouží k určení místního obsahu ke kopírování do virtuálního počítače Azure.

Prvek `SourceDirectory` je k dispozici pouze pomocí sady Azure SDK verze 1.5 nebo vyšší.

Následující tabulka popisuje atributy `SourceDirectory` prvku.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|cesta|řetězec|Povinná hodnota. Relativní nebo absolutní cesta k místnímu adresáři, jehož obsah se zkopíruje do virtuálního počítače Azure. Rozšíření proměnných prostředí v cestě adresáře je podporováno.|  
  
## <a name="see-also"></a>Viz také
[Schéma definice cloudové služby (klasické)](schema-csdef-file.md)




