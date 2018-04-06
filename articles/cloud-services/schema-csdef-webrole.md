---
title: Azure Cloud Services Def. Schéma WebRole | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
ms.prod: azure
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 85368e4e-a0db-4c02-8dbc-8e2928fa6091
caps.latest.revision: 60
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: b2873f61dcab9a14089949f27f40ca5bedaf14ee
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="azure-cloud-services-definition-webrole-schema"></a>Definice schématu WebRole služby Azure Cloud Services
Azure webovou roli je role, která je přizpůsobené pro programování webové aplikace podporuje ve službě IIS 7, jako je ASP.NET, PHP, WCF a FastCGI.

Výchozí přípona souboru definice služby je .csdef.

## <a name="basic-service-definition-schema-for-a-web-role"></a>Definice schématu základní služby pro webovou roli  
Základní formát souboru definice služby obsahující webové role je následující.

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
Soubor definice služby zahrnuje tyto prvky, které jsou podrobně popsány v následujících částech v tomto tématu:  

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

[certifikát](#Certificate)

[Importy](#Imports)

[Import](#Import)

[Modul runtime](#Runtime)

[Prostředí](#Environment)

[Variable](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[NetFxEntryPoint](#NetFxEntryPoint)

[weby](#Sites)

[Lokality](#Site)

[VirtualApplication](#VirtualApplication)

[VirtualApplication](#VirtualApplication)

[Vazby](#Bindings)

[Vazba](#Binding)

[Spuštění](#Startup)

[Úkol](#Task)

[Obsah](#Contents)

[Obsah](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WebRole"></a> WebRole  
`WebRole` Element popisuje roli, které je přizpůsobené pro programování webové aplikace podporuje ve službě IIS 7 a ASP.NET. Služba může obsahovat nula nebo více webové role.

Následující tabulka popisuje atributy `WebRole` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Název pro webovou roli. Název role musí být jedinečný.|  
|enableNativeCodeExecution|Boolean|Volitelné. Výchozí hodnota je `true`; nativní spuštění kódu a úplný vztah důvěryhodnosti, které jsou ve výchozím nastavení povolené. Nastavte tento atribut `false` chcete zakázat provádění nativního kódu pro webovou roli a místo toho použít Azure částečnou důvěryhodností.|  
|vmsize|řetězec|Volitelné. Nastavením této hodnoty můžete změnit velikost virtuálního počítače, který je vymezena k roli. Výchozí hodnota je `Small`. Další informace najdete v tématu [velikostí virtuálních počítačů pro cloudové služby](cloud-services-sizes-specs.md).|  

##  <a name="ConfigurationSettings"></a> ConfigurationSettings  
`ConfigurationSettings` Element popisuje kolekci nastavení konfigurace pro webové role. Tento element má nadřazený prvek `Setting` elementu.

##  <a name="Setting"></a> Nastavení  
`Setting` Element popisuje pár název a hodnotu, která určuje nastavení konfigurace pro instanci role.

Následující tabulka popisuje atributy `Setting` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Jedinečný název pro nastavení konfigurace.|  

Nastavení konfigurace pro role jsou dvojice názvů a hodnot, které jsou deklarované v definičním souboru služby a nastavení v konfiguračním souboru služby.

##  <a name="LocalResources"></a> LocalResources  
`LocalResources` Element popisuje kolekci prostředků místní úložiště pro webovou roli. Tento element má nadřazený prvek `LocalStorage` elementu.

##  <a name="LocalStorage"></a> LocalStorage  
`LocalStorage` Element identifikuje prostředek Místní úložiště, který poskytuje místo systému souborů pro službu za běhu. Role může definovat žádný nebo více prostředků místní úložiště.

> [!NOTE]
>  `LocalStorage` Může se objevit jako podřízený element `WebRole` element pro podporu kompatibilitu s předchozími verzemi sady Azure SDK.

Následující tabulka popisuje atributy `LocalStorage` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Jedinečný název pro místní úložiště.|  
|cleanOnRoleRecycle|Boolean|Volitelné. Určuje, zda by měl při restartování roli vyčistit místní úložiště. Výchozí hodnota je `true`.|  
|sizeInMb|celá čísla|Volitelné. Požadované množství místa úložiště pro místní úložiště, v MB. Pokud není zadáno, je místo výchozí úložiště přidělené 100 MB. Minimální množství prostoru úložiště, který může být přidělen je 1 MB.<br /><br /> Maximální velikost místních prostředků je závislý na velikosti virtuálního počítače. Další informace najdete v tématu [velikostí virtuálních počítačů pro cloudové služby](cloud-services-sizes-specs.md).|  
  
Hodnota zadaná pro atribut název odpovídá název adresáře přidělená k prostředku Místní úložiště.

##  <a name="Endpoints"></a> Koncové body  
`Endpoints` Element popisuje kolekci vstupu (externí), interní a instance vstupních koncových bodů pro roli. Tento element má nadřazený prvek `InputEndpoint`, `InternalEndpoint`, a `InstanceInputEndpoint` elementy.

Vstup a vnitřních koncových bodů jsou přiděleny samostatně. Služby mohou mít celkem 25 vstupu, interní, a instance vstupní koncové body, které mohou být přiděleny mezi 25 rolí povolené ve službě. Například pokud máte 5 role můžete přidělit 5 vstupních koncových bodů na roli nebo můžete přidělit 25 vstupních koncových bodů do jedné role nebo kterou můžete přidělit 1 vstupní koncový bod každý 25 rolí.

> [!NOTE]
>  Každá role nasazení vyžaduje jedna instance na roli. Výchozí zřizování pro odběr je omezený na 20 jader a proto je omezený na 20 instancí role. Pokud vaše aplikace vyžaduje více instancí, než je dostupné ve výchozím nastavení zřizování najdete [podpora k fakturaci, Správa předplatného a kvóty](https://azure.microsoft.com/support/options/) Další informace o zvýšení vaší kvóty.

##  <a name="InputEndpoint"></a> InputEndpoint  
`InputEndpoint` Element popisuje externí koncový bod webové role.

Můžete definovat více koncových bodů, které představují kombinaci HTTP, HTTPS, UDP a TCP koncové body. Můžete zadat libovolné číslo portu, které zvolíte pro vstupní koncový bod, ale zadaný pro každou roli v rámci služby čísla portů musí být jedinečný. Například pokud jste určili, že webové role používá port 80 pro protokol HTTP a port 443 pro protokol HTTPS, může pak zadáte, druhý webové role používá port 8080 pro protokol HTTP a port 8043 pro protokol HTTPS.

Následující tabulka popisuje atributy `InputEndpoint` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Jedinečný název pro externí koncový bod.|  
|Protokol|řetězec|Povinná hodnota. Přenosový protokol pro externí koncový bod. Pro roli webové možné hodnoty jsou `HTTP`, `HTTPS`, `UDP`, nebo `TCP`.|  
|port|celá čísla|Povinná hodnota. Port pro externí koncový bod. Můžete zadat libovolné číslo portu, který zvolíte, ale zadaný pro každou roli v rámci služby čísla portů musí být jedinečný.<br /><br /> Možné hodnoty jsou v rozsahu od 1 do 65535, (Azure SDK 1.7 nebo vyšší verze).|  
|certifikát|řetězec|Vyžaduje se pro koncový bod HTTPS. Název certifikátu definované `Certificate` elementu.|  
|localPort|celá čísla|Volitelné. Určuje port používaný pro vnitřní spojení na koncový bod. `localPort` Atribut externí port pro koncový bod se mapuje na interní port v roli. To je užitečné v situacích, kde roli musí komunikovat na komponentu interní na portu, liší od ten, který je zveřejněný externě.<br /><br /> Pokud není zadaný, hodnota `localPort` je stejný jako `port` atribut. Nastavte hodnotu `localPort` na "*" automaticky přiřadit nepřidělené port, který je zjistitelný na rozhraní API.<br /><br /> Možné hodnoty jsou v rozsahu od 1 do 65535, (Azure SDK 1.7 nebo vyšší verze).<br /><br /> `localPort` Atribut je pouze dostupné pomocí sady Azure SDK verze 1.3 nebo vyšší.|  
|ignoreRoleInstanceStatus|Boolean|Volitelné. Pokud hodnota tohoto atributu nastavena na `true`, stav služby je ignorován a koncový bod se neodeberou nástroje pro vyrovnávání zatížení. Nastavení této hodnoty na `true` užitečné pro ladění zaneprázdněn instancí služby. Výchozí hodnota je `false`. **Poznámka:** koncový bod může stále přijímat provoz i v případě, že role není ve stavu Připraveno.|  
|loadBalancerProbe|řetězec|Volitelné. Název sondu nástroje pro vyrovnávání zatížení, který je přidružený vstupní koncový bod. Další informace najdete v tématu [LoadBalancerProbe schématu](schema-csdef-loadbalancerprobe.md).|  

##  <a name="InternalEndpoint"></a> InternalEndpoint  
`InternalEndpoint` Element popisuje vnitřní koncový bod webové role. Vnitřní koncový bod je k dispozici pouze pro další instance rolí, který je spuštěn v rámci služby; není k dispozici pro klienty mimo službu. Webové role, které neobsahují `Sites` element může obsahovat pouze jeden HTTP, UDP nebo TCP vnitřní koncový bod.

Následující tabulka popisuje atributy `InternalEndpoint` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Jedinečný název pro vnitřní koncový bod.|  
|Protokol|řetězec|Povinná hodnota. Přenosový protokol pro vnitřní koncový bod. Možné hodnoty jsou `HTTP`, `TCP`, `UDP`, nebo `ANY`.<br /><br /> Hodnota `ANY` Určuje, že je povolen libovolný protokol libovolný port.|  
|port|celá čísla|Volitelné. Port používaný pro připojení interní skupinu s vyrovnáváním zatížení na koncovém bodu. Koncový bod používá dva porty skupinu s vyrovnáváním zatížení. Port používaný pro veřejnou IP adresu a port používaný v privátní IP adresu. Obvykle se jedná o jsou nastaveny na hodnotu stejné, ale můžete použít jiné porty.<br /><br /> Možné hodnoty jsou v rozsahu od 1 do 65535, (Azure SDK 1.7 nebo vyšší verze).<br /><br /> `Port` Atribut je pouze dostupné pomocí sady Azure SDK verze 1.3 nebo vyšší.|  

##  <a name="InstanceInputEndpoint"></a> InstanceInputEndpoint  
`InstanceInputEndpoint` Element popisuje vstupní koncový bod instance webové role. Instance vstupní koncový bod je přidružen k instanci určité role pomocí přesměrování portu nástroji pro vyrovnávání zatížení. Každé instanci vstupní koncový bod je namapována na specifického portu z rozsahu možných portů. Tento element má nadřazený prvek `AllocatePublicPortFrom` elementu.

`InstanceInputEndpoint` Element je pouze dostupné pomocí verze sady Azure SDK 1.7 nebo vyšší.

Následující tabulka popisuje atributy `InstanceInputEndpoint` elementu.
  
| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Jedinečný název pro koncový bod.|  
|localPort|celá čísla|Povinná hodnota. Určuje vnitřní port, který bude všechny instance rolí poslouchat aby bylo možné přijímat příchozí provoz, které jsou předávány z nástroje pro vyrovnávání zatížení. Možné hodnoty jsou v rozsahu od 1 do 65535 (včetně).|  
|Protokol|řetězec|Povinná hodnota. Přenosový protokol pro vnitřní koncový bod. Možné hodnoty jsou `udp` nebo `tcp`. Použití `tcp` na základě provoz protokolu http nebo https.|  
  
##  <a name="AllocatePublicPortFrom"></a> AllocatePublicPortFrom  
`AllocatePublicPortFrom` Element popisuje rozsahu veřejný port, který lze použít externí zákazníky pro přístup k každou instanci vstupní koncový bod. Veřejné číslo portu (VIP) se přidělené z tohoto rozsahu a přiřadí do každý koncový bod instance jednotlivých rolí během nasazení klienta a aktualizace. Tento element má nadřazený prvek `FixedPortRange` elementu.

`AllocatePublicPortFrom` Element je pouze dostupné pomocí verze sady Azure SDK 1.7 nebo vyšší.

##  <a name="FixedPort"></a> FixedPort  
`FixedPort` Element určuje port pro vnitřní koncový bod, který umožňuje načíst vyrovnáváním připojení na koncovém bodu.

`FixedPort` Element je pouze dostupné pomocí sady Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `FixedPort` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|port|celá čísla|Povinná hodnota. Port pro vnitřní koncový bod. Výsledek stejný jako nastavení `FixedPortRange` min a max na stejný port.<br /><br /> Možné hodnoty jsou v rozsahu od 1 do 65535, (Azure SDK 1.7 nebo vyšší verze).|  

##  <a name="FixedPortRange"></a> FixedPortRange  
`FixedPortRange` Element určuje rozsah portů, které jsou přiřazeny k vnitřní koncový bod nebo vstupní koncový bod instance, a nastaví použije portu pro zatížení vyrovnáváním připojení na koncový bod.

> [!NOTE]
>  `FixedPortRange` Element funguje jinak v závislosti na element, ve kterém se nachází. Když `FixedPortRange` elementu je `InternalEndpoint` prvek, otevře se všechny porty pro vyrovnávání zatížení v rámci rozsahu atributy min a max pro všechny virtuální počítače, na kterých role běží. Když `FixedPortRange` elementu je `InstanceInputEndpoint` prvek, otevře se jenom jeden port v rozsahu atributy min a max na každém virtuálním počítači s rolí.

`FixedPortRange` Element je pouze dostupné pomocí sady Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `FixedPortRange` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|min|celá čísla|Povinná hodnota. Minimální port v rozsahu. Možné hodnoty jsou v rozsahu od 1 do 65535, (Azure SDK 1.7 nebo vyšší verze).|  
|max|řetězec|Povinná hodnota. Maximální port v rozsahu. Možné hodnoty jsou v rozsahu od 1 do 65535, (Azure SDK 1.7 nebo vyšší verze).|  

##  <a name="Certificates"></a> Certifikáty  
`Certificates` Element popisuje kolekce certifikátů pro webovou roli. Tento element má nadřazený prvek `Certificate` elementu. Role může mít libovolný počet přidružené certifikáty. Další informace o používání certifikátů element najdete v tématu [upravit soubor definice služby pomocí certifikátu](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="Certificate"></a> certifikát  
`Certificate` Element popisuje certifikát, který je přidružen webové role.

Následující tabulka popisuje atributy `Certificate` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Název tohoto certifikátu, který slouží k odkazování na ho, když je přidružen HTTPS `InputEndpoint` elementu.|  
|storeLocation|řetězec|Povinná hodnota. Umístění úložiště certifikátů, kde může najít tento certifikát v místním počítači. Možné hodnoty jsou `CurrentUser` a `LocalMachine`.|  
|storeName|řetězec|Povinná hodnota. Název úložiště certifikátů, které tento certifikát se nachází v místním počítači. Možné hodnoty patří názvy integrované úložiště `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot`, `AddressBook`, nebo libovolný název vlastního úložiště. Pokud je název vlastního úložiště, se automaticky vytvoří úložiště.|  
|permissionLevel|řetězec|Volitelné. Určuje oprávnění k přístupu na procesy role. Pokud chcete pouze zvýšenými procesy mohli přístup k privátnímu klíči a potom zadejte `elevated` oprávnění. `limitedOrElevated` oprávnění umožní přístup k privátnímu klíči všechny procesy role. Možné hodnoty jsou `limitedOrElevated` nebo `elevated`. Výchozí hodnota je `limitedOrElevated`.|  

##  <a name="Imports"></a> Importy  
`Imports` Element popisuje kolekce importu modulů pro webovou roli, které přidat součásti do hostovaného operačního systému. Tento element má nadřazený prvek `Import` elementu. Tento element je volitelný a role může mít pouze jeden blok importy. 

`Imports` Element je pouze dostupné pomocí sady Azure SDK verze 1.3 nebo vyšší.

##  <a name="Import"></a> Import  
`Import` Element určuje modulu pro přidání do hostovaného operačního systému.

`Import` Element je pouze dostupné pomocí sady Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `Import` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|moduleName|řetězec|Povinná hodnota. Název modulu k importu. Platný importovat moduly jsou:<br /><br /> -RemoteAccess<br />-RemoteForwarder<br />-Diagnostiky<br /><br /> Moduly RemoteAccess a RemoteForwarder umožňují konfigurovat vaše role instance pro připojení ke vzdálené ploše. Další informace najdete v části [povolit připojení ke vzdálené ploše](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> Modul diagnostiky umožňuje shromažďování diagnostických dat pro instanci role.|  

##  <a name="Runtime"></a> Modul runtime  
`Runtime` Element popisuje kolekci nastavení proměnné prostředí pro webovou roli určující běhové prostředí procesu hostitele Azure. Tento element má nadřazený prvek `Environment` elementu. Tento element je volitelný a role může mít pouze jeden blok modulu runtime.

`Runtime` Element je pouze dostupné pomocí sady Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `Runtime` element:  

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|executionContext|řetězec|Volitelné. Určuje kontext, ve kterém je spuštěn proces Role. Výchozí kontext je `limited`.<br /><br /> -   `limited` – Tento proces se spustí bez oprávnění správce.<br />-   `elevated` – Tento proces se spustí s oprávněními správce.|  

##  <a name="Environment"></a> Prostředí  
`Environment` Element popisuje kolekci nastavení proměnné prostředí pro webovou roli. Tento element má nadřazený prvek `Variable` elementu. Role může mít libovolný počet sada proměnných prostředí.

##  <a name="Variable"></a> Proměnná  
`Variable` Element určuje proměnná prostředí do hostovaného operačního.

`Variable` Element je pouze dostupné pomocí sady Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `Variable` element:  

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Název proměnné prostředí, chcete-li nastavit.|  
|hodnota|řetězec|Volitelné. Hodnota k nastavení proměnné prostředí. Musí obsahovat atribut value nebo `RoleInstanceValue` elementu.|  

##  <a name="RoleInstanceValue"></a> RoleInstanceValue  
`RoleInstanceValue` Element určuje výraz xPath, ze kterého chcete načíst hodnotu proměnné.

Následující tabulka popisuje atributy `RoleInstanceValue` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|výraz XPath|řetězec|Volitelné. Cestu k umístění z nastavení nasazení pro instanci. Další informace najdete v tématu [konfigurační proměnné s XPath](cloud-services-role-config-xpath.md).<br /><br /> Musí obsahovat atribut value nebo `RoleInstanceValue` elementu.|  

##  <a name="EntryPoint"></a> Vstupní bod  
`EntryPoint` Element určuje vstupní bod pro roli. Tento element má nadřazený prvek `NetFxEntryPoint` elementy. Tyto prvky umožňují určit jiné než výchozí WaWorkerHost.exe aplikace tak, aby fungoval jako vstupní bod role.

`EntryPoint` Element je pouze dostupné pomocí sady Azure SDK verze 1.5 nebo vyšší.

##  <a name="NetFxEntryPoint"></a> NetFxEntryPoint  
`NetFxEntryPoint` Element určuje program, který chcete spustit pro roli.

> [!NOTE]
>  `NetFxEntryPoint` Element je pouze dostupné pomocí sady Azure SDK verze 1.5 nebo vyšší.

Následující tabulka popisuje atributy `NetFxEntryPoint` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|assemblyName|řetězec|Povinná hodnota. Název a cesta k souboru sestavení obsahující vstupní bod. Cesta je relativní vzhledem ke složce  **\\%ROLEROOT%\Approot** (nezadávejte  **\\%ROLEROOT%\Approot** v `commandLine`, předpokládá se,). **% ROLEROOT %** je proměnná prostředí spravován Azure a představuje umístění kořenové složky pro vaši roli.  **\\%ROLEROOT%\Approot** složky představuje složka aplikace pro vaši roli.<br /><br /> Pro možnostmi hostitele role cestu je vždy vzhledem k  **\\%ROLEROOT%\Approot\bin** složky.<br /><br /> Pro úplnou službu IIS a služby IIS Express webové role, pokud je sestavení nelze nalézt vzhledem k  **\\%ROLEROOT%\Approot** složku,  **\\%ROLEROOT%\Approot\bin** prohledají se.<br /><br /> Podzim zpět chování pro úplnou službu IIS není osvědčený postup doporučujeme a může být odebrán v budoucích verzích.|  
|targetFrameworkVersion|řetězec|Povinná hodnota. Verze rozhraní .NET framework, na kterém byl vytvořený sestavení. Například, `targetFrameworkVersion="v4.0"`.|  

##  <a name="Sites"></a> weby  
`Sites` Element popisuje kolekci webů a webových aplikací, které jsou hostované ve webové roli. Tento element má nadřazený prvek `Site` elementu. Pokud nezadáte `Sites` elementu vaši webovou roli je hostována jako starší verze webovou roli a může mít pouze jeden web hostovaný v vaši webovou roli. Tento element je volitelný a role může mít pouze jeden blok lokalit.

`Sites` Element je pouze dostupné pomocí sady Azure SDK verze 1.3 nebo vyšší.

##  <a name="Site"></a> Lokality  
`Site` Element určuje web nebo webovou aplikaci, která je součástí webovou roli.

`Site` Element je pouze dostupné pomocí sady Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `Site` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Název webu nebo aplikace.|  
|physicalDirectory|řetězec|Umístění obsahu adresáře pro kořenovému adresáři webu. Umístění lze zadat jako absolutní cestu nebo relativní k umístění .csdef.|  

##  <a name="VirtualApplication"></a> VirtualApplication  
`VirtualApplication` Element definuje aplikace v Internetové informační služby (IIS) 7 je seskupení souborů, které dodává obsah nebo poskytuje služby přes protokoly, jako je například HTTP. Když vytvoříte aplikaci ve službě IIS 7, stane se cesta aplikace součástí adresy URL webu.

`VirtualApplication` Element je pouze dostupné pomocí sady Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `VirtualApplication` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Určuje název pro identifikaci virtuální aplikace.|  
|physicalDirectory|řetězec|Povinná hodnota. Určuje cestu na vývojovém počítači, který obsahuje virtuální aplikace. V emulátoru výpočtů služba IIS byla nakonfigurovaná k získání obsahu z tohoto umístění. Při nasazení do Azure, obsah fyzický adresář spojených spolu s ostatními službu. Když se nasadí balíček služby Azure, služby IIS je nakonfigurovaný s umístění rozbalené obsah.|  

##  <a name="VirtualDirectory"></a> VirtualDirectory  
`VirtualDirectory` Element určuje název adresáře (také označované jako cestu), zadejte ve službě IIS a mapovat je na fyzický adresář na místním nebo vzdáleném serveru.

`VirtualDirectory` Element je pouze dostupné pomocí sady Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `VirtualDirectory` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Určuje název pro identifikaci virtuální adresář.|  
|hodnota|physicalDirectory|Povinná hodnota. Určuje cestu na vývojovém počítači, který obsahuje obsah virtuálního adresáře na webu. V emulátoru výpočtů služba IIS byla nakonfigurovaná k získání obsahu z tohoto umístění. Při nasazení do Azure, obsah fyzický adresář spojených spolu s ostatními službu. Když se nasadí balíček služby Azure, služby IIS je nakonfigurovaný s umístění rozbalené obsah.|  

##  <a name="Bindings"></a> Vazby  
`Bindings` Element popisuje kolekci vazby pro web. Je nadřazený element `Binding` elementu. Element je vyžadována pro každý `Site` elementu. Další informace o konfiguraci koncových bodů najdete v tématu [povolit komunikaci pro instance rolí](cloud-services-enable-communication-role-instances.md).

`Bindings` Element je pouze dostupné pomocí sady Azure SDK verze 1.3 nebo vyšší.

##  <a name="Binding"></a> Vazba  
`Binding` Element určuje konfigurační informace požadované pro požadavky na komunikaci s web nebo webovou aplikaci.

`Binding` Element je pouze dostupné pomocí sady Azure SDK verze 1.3 nebo vyšší.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|jméno|řetězec|Povinná hodnota. Určuje název pro identifikaci vazby.|  
|endpointName|řetězec|Povinná hodnota. Určuje název koncového bodu pro vazbu.|  
|Hostitel|řetězec|Volitelné. Určuje název hostitele, který umožňuje hostování více lokalit s různé názvy hostitelů, na jedné kombinaci IP adresu nebo Port číslo.|  

##  <a name="Startup"></a> Spuštění  
`Startup` Element popisuje kolekci úloh, které jsou spuštěny při spuštění role. Tento element může být nadřazeného `Variable` elementu. Další informace o používání spuštění úlohy role, naleznete v části [jak nakonfigurovat spuštění úlohy](cloud-services-startup-tasks.md). Tento element je volitelný a role může mít pouze jeden blok spuštění.

Následující tabulka popisuje atribut `Startup` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|priorita|celá čísla|Pouze pro interní použití.|  

##  <a name="Task"></a> Úloha  
`Task` Element určuje úloha spuštění, který probíhá při spuštění role. Spuštění úlohy slouží k provádění úloh, které připravují role, kterou chcete spustit softwarové součásti takové instalaci nebo spuštění jiných aplikací. Spuštění úlohy v pořadí, ve kterém jsou zobrazeny v rámci `Startup` element bloku.

`Task` Element je pouze dostupné pomocí sady Azure SDK verze 1.3 nebo vyšší.

Následující tabulka popisuje atributy `Task` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|příkazového řádku|řetězec|Povinná hodnota. Skript, jako je soubor CMD, obsahující příkazů pro spuštění. Spuštění příkazu a dávkové soubory musí být uložena ve formátu ANSI. Formáty souborů, které nastavit značky pořadí bajtů na začátku souboru nebudou správně zpracovat.|  
|executionContext|řetězec|Určuje kontext, ve kterém je skript spuštěn.<br /><br /> -   `limited` [Výchozí] – spusťte se stejnými oprávněními jako role hostující proces.<br />-   `elevated` – Spusťte s oprávněním správce.|  
|taskType|řetězec|Určuje chování při spuštění příkazu.<br /><br /> -   `simple` [Výchozí] – systém čeká na úlohu chcete-li ukončit předtím, než se spustí další úlohy.<br />-   `background` – Systém nečeká na ukončení úlohy.<br />-   `foreground` – Podobně jako na pozadí, s výjimkou role není restartovat, dokud všechny úlohy popředí ukončete.|  

##  <a name="Contents"></a> Obsah  
`Contents` Element popisuje kolekci obsahu pro webovou roli. Tento element má nadřazený prvek `Content` elementu.

`Contents` Element je pouze dostupné pomocí sady Azure SDK verze 1.5 nebo vyšší.

##  <a name="Content"></a> Obsah  
`Content` Element definuje umístění zdroje obsahu, který se má zkopírovat virtuální počítač Azure a cílovou cestu, do kterého se zkopíruje.

`Content` Element je pouze dostupné pomocí sady Azure SDK verze 1.5 nebo vyšší.

Následující tabulka popisuje atributy `Content` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|Cílový|řetězec|Povinná hodnota. Umístění v Azure virtuální počítač, ke které je umístěn obsah. Toto umístění je relativní vzhledem ke složce **%ROLEROOT%\Approot**.|  

Tento element má nadřazený element `SourceDirectory` elementu.

##  <a name="SourceDirectory"></a> SourceDirectory  
`SourceDirectory` Element definuje místní adresář, ze kterého se obsah zkopíruje. Chcete-li určit místní obsah zkopírovat do virtuálního počítače Azure pomocí tohoto prvku.

`SourceDirectory` Element je pouze dostupné pomocí sady Azure SDK verze 1.5 nebo vyšší.

Následující tabulka popisuje atributy `SourceDirectory` elementu.

| Atribut | Typ | Popis |  
| --------- | ---- | ----------- |  
|path|řetězec|Povinná hodnota. Relativní nebo absolutní cesta k místnímu adresáři, jejichž obsah se zkopírují na virtuální počítač Azure. Rozšíření proměnných prostředí v cestě adresáře je podporována.|  
  
## <a name="see-also"></a>Viz také
[Cloudové služby (klasické) definice schématu](schema-csdef-file.md)
