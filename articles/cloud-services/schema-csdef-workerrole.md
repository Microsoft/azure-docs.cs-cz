---
title: Schéma Azure Cloud Services def. role pracovního procesu | Microsoft Docs
services: cloud-services
ms.custom: ''
ms.date: 04/14/2015
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 41cd46bc-c479-43fa-96e5-d6c83e4e6d89
caps.latest.revision: 55
author: tgore03
ms.author: tagore
ms.openlocfilehash: 518fa0b64277d056796669a3c9f93c9c22325d91
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449020"
---
# <a name="azure-cloud-services-definition-workerrole-schema"></a>Role pracovního procesu schéma definice Azure Cloud Services
Role pracovního procesu Azure je role, která je užitečná pro zobecněný vývoj a může provádět zpracování na pozadí pro webovou roli.

Výchozí přípona souboru definice služby je. csdef.

## <a name="basic-service-definition-schema-for-a-worker-role"></a>Základní schéma definice služby pro roli pracovního procesu
Základní formát definičního souboru služby, který obsahuje roli pracovního procesu, je následující.

```xml
<ServiceDefinition …>
  <WorkerRole name="<worker-role-name>" vmsize="<worker-role-size>" enableNativeCodeExecution="[true|false]">
    <Certificates>
      <Certificate name="<certificate-name>" storeLocation="[CurrentUser|LocalMachine]" storeName="[My|Root|CA|Trust|Disallow|TrustedPeople|TrustedPublisher|AuthRoot|AddressBook|<custom-store>" />
    </Certificates>
    <ConfigurationSettings>
      <Setting name="<setting-name>" />
    </ConfigurationSettings>
    <Endpoints>
      <InputEndpoint name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<local-port-number>" port="<port-number>" certificate="<certificate-name>" loadBalancerProbe="<load-balancer-probe-name>" />
      <InternalEndpoint name="<internal-endpoint-name" protocol="[http|tcp|udp|any]" port="<port-number>">
         <FixedPort port="<port-number>"/>
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
      </InternalEndpoint>
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">
         <AllocatePublicPortFrom>
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
         </AllocatePublicPortFrom>
      </InstanceInputEndpoint>
    </Endpoints>
    <Imports>
      <Import moduleName="[RemoteAccess|RemoteForwarder|Diagnostics]"/>
    </Imports>
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
         <ProgramEntryPoint commandLine="<application>" setReadyOnProcessStart="[true|false]"/>
      </EntryPoint>
    </Runtime>
    <Startup priority="<for-internal-use-only>">
      <Task commandLine="" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">
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
  </WorkerRole>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Prvky schématu
Definiční soubor služby obsahuje tyto prvky, které jsou podrobně popsány v následujících částech tohoto tématu:

[Role pracovního procesu](#WorkerRole)

[ConfigurationSettings](#ConfigurationSettings)

[Nastavení](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Koncové body](#Endpoints)

[InputEndpoint](#InputEndpoint)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Certifikáty](#Certificates)

[Certifikát](#Certificate)

[Objem](#Imports)

[Import](#Import)

[Modul runtime](#Runtime)

[Prostředí](#Environment)

[EntryPoint](#EntryPoint)

[NetFxEntryPoint](#NetFxEntryPoint)

[ProgramEntryPoint](#ProgramEntryPoint)

[Variabilní](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[Startup](#Startup)

[Úkol](#Task)

[Obsah](#Contents)

[Obsah](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WorkerRole"></a>Role pracovního procesu
Element `WorkerRole` popisuje roli, která je užitečná pro zobecněný vývoj a může provádět zpracování na pozadí pro webovou roli. Služba může obsahovat nula nebo více rolí pracovního procesu.

Následující tabulka popisuje atributy prvku `WorkerRole`.

| Atribut | Typ | Popis |
| --------- | ---- | ----------- |
|jméno|string|Povinná hodnota. Název role pracovního procesu. Název role musí být jedinečný.|
|enableNativeCodeExecution|Boolean|Nepovinný parametr. Výchozí hodnota je `true`; ve výchozím nastavení je povoleno spuštění nativního kódu a plná důvěryhodnost. Nastavením tohoto atributu `false` zakážete spouštění nativního kódu pro roli pracovního procesu a místo toho použijete částečnou důvěryhodnost Azure.|
|VMSize|string|Nepovinný parametr. Nastavte tuto hodnotu, chcete-li změnit velikost virtuálního počítače, který je přidělen této roli. Výchozí hodnota je `Small`. Seznam možných velikostí virtuálních počítačů a jejich atributů najdete v tématu [velikosti virtuálních počítačů pro Cloud Services](cloud-services-sizes-specs.md).|

##  <a name="ConfigurationSettings"></a>ConfigurationSettings
Element `ConfigurationSettings` popisuje shromažďování konfiguračních nastavení pro roli pracovního procesu. Tento prvek je nadřazeným prvkem `Setting`.

##  <a name="Setting"></a>Nastavením
Element `Setting` popisuje dvojici název a hodnota, která určuje nastavení konfigurace pro instanci role.

Následující tabulka popisuje atributy prvku `Setting`.

| Atribut | Typ | Popis |
| --------- | ---- | ----------- |
|jméno|string|Povinná hodnota. Jedinečný název pro nastavení konfigurace.|

Konfigurační nastavení role jsou páry název-hodnota, které jsou deklarovány v souboru definice služby a nastaveny v konfiguračním souboru služby.

##  <a name="LocalResources"></a>LocalResources
`LocalResources` prvek popisuje shromažďování prostředků místního úložiště pro roli pracovního procesu. Tento prvek je nadřazeným prvkem `LocalStorage`.

##  <a name="LocalStorage"></a>LocalStorage
Element `LocalStorage` identifikuje místní prostředek úložiště, který poskytuje prostor systému souborů pro službu za běhu. Role může definovat nula nebo více prostředků místního úložiště.

> [!NOTE]
>  Element `LocalStorage` se může zobrazit jako podřízený prvek `WorkerRole`, aby podporoval kompatibilitu se staršími verzemi sady Azure SDK.

Následující tabulka popisuje atributy prvku `LocalStorage`.

| Atribut | Typ | Popis |
| --------- | ---- | ----------- |
|jméno|string|Povinná hodnota. Jedinečný název pro místní úložiště.|
|cleanOnRoleRecycle|Boolean|Nepovinný parametr. Označuje, jestli se má při restartování role vyčistit místní úložiště. Výchozí hodnota je `true`.|
|sizeInMb|int|Nepovinný parametr. Požadovaný objem prostoru úložiště, který se má přidělit místnímu úložišti, v MB Pokud není zadaný, použije se výchozí přidělený prostor úložiště 100 MB. Minimální velikost úložného prostoru, který může být přidělen, je 1 MB.<br /><br /> Maximální velikost místních prostředků závisí na velikosti virtuálního počítače. Další informace najdete v tématu [velikosti virtuálních počítačů pro Cloud Services](cloud-services-sizes-specs.md).|

Název adresáře, který je přidělený místnímu prostředku úložiště, odpovídá hodnotě zadané pro atribut Name.

##  <a name="Endpoints"></a>Bod
Element `Endpoints` popisuje shromažďování vstupních (externích), interních a vstupních koncových bodů instance pro roli. Tento prvek je nadřazenými prvky `InputEndpoint`, `InternalEndpoint`a `InstanceInputEndpoint`.

Vstupní a interní koncové body jsou přidělovány samostatně. Služba může mít celkem 25 vstupních, interních a vstupních koncových bodů, které se dají přidělit přes 25 rolí povolených ve službě. Například pokud má 5 rolí, můžete přidělit 5 vstupních koncových bodů pro každou roli nebo můžete přidělit 25 vstupních koncových bodů jedné roli nebo můžete přidělit 1 vstupní koncový bod každé 25 rolí.

> [!NOTE]
>  Každá nasazená role vyžaduje jednu instanci na roli. Výchozí zřizování pro předplatné je omezené na 20 jader a proto je omezené na 20 instancí role. Pokud vaše aplikace vyžaduje více instancí, než je poskytované výchozím zřizováním, přečtěte si další informace o zvýšení kvóty v tématu [fakturace, Správa předplatných a podpora kvót](https://azure.microsoft.com/support/options/) .

##  <a name="InputEndpoint"></a>InputEndpoint
Element `InputEndpoint` popisuje externí koncový bod pro roli pracovního procesu.

Můžete definovat několik koncových bodů, které jsou kombinací koncových bodů HTTP, HTTPS, UDP a TCP. Můžete zadat libovolné číslo portu, které zvolíte pro vstupní koncový bod, ale čísla portů zadaná pro každou roli ve službě musí být jedinečná. Pokud například zadáte, že role používá port 80 pro protokol HTTP a port 443 pro protokol HTTPS, můžete zadat, aby druhá role používala port 8080 pro protokol HTTP a port 8043 pro protokol HTTPS.

Následující tabulka popisuje atributy prvku `InputEndpoint`.

| Atribut | Typ | Popis |
| --------- | ---- | ----------- |
|jméno|string|Povinná hodnota. Jedinečný název externího koncového bodu.|
|protocol|string|Povinná hodnota. Transportní protokol pro externí koncový bod. V případě role pracovního procesu jsou možné hodnoty `HTTP`, `HTTPS`, `UDP`nebo `TCP`.|
|port|int|Povinná hodnota. Port externího koncového bodu. Můžete zadat libovolné číslo portu, které zvolíte, ale čísla portů zadaná pro každou roli ve službě musí být jedinečná.<br /><br /> Možné hodnoty jsou v rozsahu od 1 do 65535, včetně (Azure SDK verze 1,7 nebo novější).|
|certifikát|string|Vyžaduje se pro koncový bod HTTPS. Název certifikátu definovaného elementem `Certificate`.|
|localPort|int|Nepovinný parametr. Určuje port, který se používá pro interní připojení ke koncovému bodu. Atribut `localPort` mapuje externí port na koncovém bodu na interní port role. To je užitečné ve scénářích, kdy musí role komunikovat s interní komponentou na portu, který se liší od toho, který je přístupný externě.<br /><br /> Pokud není zadán, hodnota `localPort` je shodná s atributem `port`. Nastavením hodnoty `localPort` * automaticky přiřadíte nepřidělený port, který bude zjistitelný pomocí běhového rozhraní API.<br /><br /> Možné hodnoty jsou v rozsahu od 1 do 65535, včetně (Azure SDK verze 1,7 nebo novější).<br /><br /> Atribut `localPort` je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.|
|ignoreRoleInstanceStatus|Boolean|Nepovinný parametr. Pokud je hodnota tohoto atributu nastavená na `true`, stav služby se ignoruje a koncový bod se neodebere nástrojem pro vyrovnávání zatížení. Nastavení této hodnoty na `true` užitečné pro ladění zaneprázdněných instancí služby. Výchozí hodnota je `false`. **Poznámka:** Koncový bod může i nadále přijímat přenosy i v případě, že role není v připraveném stavu.|
|loadBalancerProbe|string|Nepovinný parametr. Název testu nástroje pro vyrovnávání zatížení, který je přidružený ke vstupnímu koncovému bodu. Další informace najdete v tématu [LoadBalancerProbe Schema](schema-csdef-loadbalancerprobe.md).|

##  <a name="InternalEndpoint"></a>InternalEndpoint
Element `InternalEndpoint` popisuje interní koncový bod pro roli pracovního procesu. Interní koncový bod je k dispozici pouze pro jiné instance rolí běžící v rámci služby. není k dispozici pro klienty mimo službu. Role pracovního procesu může mít až pět vnitřních koncových bodů HTTP, UDP nebo TCP.

Následující tabulka popisuje atributy prvku `InternalEndpoint`.

| Atribut | Typ | Popis |
| --------- | ---- | ----------- |
|jméno|string|Povinná hodnota. Jedinečný název vnitřního koncového bodu.|
|protocol|string|Povinná hodnota. Transportní protokol pro vnitřní koncový bod. Možné hodnoty jsou `HTTP`, `TCP`, `UDP`nebo `ANY`.<br /><br /> Hodnota `ANY` určuje, že jakýkoli protokol je povolený libovolný port.|
|port|int|Nepovinný parametr. Port používaný pro interní připojení s vyrovnáváním zatížení na koncovém bodu. Koncový bod s vyrovnáváním zatížení používá dva porty. Port používaný pro veřejnou IP adresu a port použitý na privátní IP adrese. Obvykle se jedná o stejné nastavení, ale můžete zvolit, aby se používaly různé porty.<br /><br /> Možné hodnoty jsou v rozsahu od 1 do 65535, včetně (Azure SDK verze 1,7 nebo novější).<br /><br /> Atribut `Port` je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.|

##  <a name="InstanceInputEndpoint"></a>InstanceInputEndpoint
Element `InstanceInputEndpoint` popisuje vstupní koncový bod instance pro roli pracovního procesu. Vstupní koncový bod instance je spojen s konkrétní instancí role pomocí předávání portů v nástroji pro vyrovnávání zatížení. Každý vstupní koncový bod instance je namapovaný na konkrétní port z rozsahu možných portů. Tento prvek je nadřazeným prvkem `AllocatePublicPortFrom`.

Element `InstanceInputEndpoint` je k dispozici pouze pomocí sady Azure SDK verze 1,7 nebo vyšší.

Následující tabulka popisuje atributy prvku `InstanceInputEndpoint`.

| Atribut | Typ | Popis |
| --------- | ---- | ----------- |
|jméno|string|Povinná hodnota. Jedinečný název koncového bodu.|
|localPort|int|Povinná hodnota. Určuje interní port, k němuž budou poslouchat všechny instance rolí, aby se přijímal příchozí provoz předaný z nástroje pro vyrovnávání zatížení. Možné hodnoty jsou v rozsahu od 1 do 65535, včetně.|
|protocol|string|Povinná hodnota. Transportní protokol pro vnitřní koncový bod. Možné hodnoty jsou `udp` nebo `tcp`. Pro provoz založený na protokolu HTTP/HTTPS použijte `tcp`.|

##  <a name="AllocatePublicPortFrom"></a>AllocatePublicPortFrom
Element `AllocatePublicPortFrom` popisuje rozsah veřejných portů, který mohou externí zákazníci použít pro přístup ke každému vstupnímu koncovému bodu instance. Číslo portu veřejného (VIP) se přiděluje z tohoto rozsahu a přiřadí se každému koncovému bodu instance role během nasazování a aktualizace tenanta. Tento prvek je nadřazeným prvkem `FixedPortRange`.

Element `AllocatePublicPortFrom` je k dispozici pouze pomocí sady Azure SDK verze 1,7 nebo vyšší.

##  <a name="FixedPort"></a>FixedPort
Element `FixedPort` Určuje port interního koncového bodu, který umožňuje připojení s vyrovnáváním zatížení na koncovém bodu.

Element `FixedPort` je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.

Následující tabulka popisuje atributy prvku `FixedPort`.

| Atribut | Typ | Popis |
| --------- | ---- | ----------- |
|port|int|Povinná hodnota. Port interního koncového bodu. To má stejný účinek jako nastavení `FixedPortRange` min a Max na stejný port.<br /><br /> Možné hodnoty jsou v rozsahu od 1 do 65535, včetně (Azure SDK verze 1,7 nebo novější).|

##  <a name="FixedPortRange"></a>FixedPortRange
Element `FixedPortRange` určuje rozsah portů, které jsou přiřazeny internímu koncovému bodu nebo vstupnímu koncovému bodu instance, a nastaví port používaný pro připojení s vyrovnáváním zatížení na koncovém bodu.

> [!NOTE]
>  Element `FixedPortRange` pracuje odlišně v závislosti na elementu, ve kterém se nachází. Když je element `FixedPortRange` v prvku `InternalEndpoint`, otevře všechny porty v nástroji pro vyrovnávání zatížení v rámci rozsahu atributů min a Max pro všechny virtuální počítače, na kterých je role spuštěná. Když je element `FixedPortRange` v prvku `InstanceInputEndpoint`, otevře se v rozsahu atributů min a Max na každém virtuálním počítači, na kterém je spuštěná role, jenom jeden port.

Element `FixedPortRange` je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.

Následující tabulka popisuje atributy prvku `FixedPortRange`.

| Atribut | Typ | Popis |
| --------- | ---- | ----------- |
|min|int|Povinná hodnota. Minimální port v rozsahu. Možné hodnoty jsou v rozsahu od 1 do 65535, včetně (Azure SDK verze 1,7 nebo novější).|
|max|string|Povinná hodnota. Maximální port v rozsahu. Možné hodnoty jsou v rozsahu od 1 do 65535, včetně (Azure SDK verze 1,7 nebo novější).|

##  <a name="Certificates"></a>Certifikáty
Element `Certificates` popisuje kolekci certifikátů pro roli pracovního procesu. Tento prvek je nadřazeným prvkem `Certificate`. Role může mít libovolný počet přidružených certifikátů. Další informace o použití prvku certifikáty najdete v tématu [Úprava definičního souboru služby pomocí certifikátu](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="Certificate"></a>Certifikát
Element `Certificate` popisuje certifikát, který je přidružený k roli pracovního procesu.

Následující tabulka popisuje atributy prvku `Certificate`.

| Atribut | Typ | Popis |
| --------- | ---- | ----------- |
|jméno|string|Povinná hodnota. Název pro tento certifikát, který se používá k odkazování na něj v případě, že je přidružen k elementu HTTPS `InputEndpoint`.|
|storeLocation|string|Povinná hodnota. Umístění úložiště certifikátů, ve kterém se tento certifikát nachází na místním počítači. Možné hodnoty jsou `CurrentUser` a `LocalMachine`.|
|storeName|string|Povinná hodnota. Název úložiště certifikátů, ve kterém se tento certifikát nachází na místním počítači. Mezi možné hodnoty patří předdefinované názvy obchodů `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot`, `AddressBook`nebo jakýkoli vlastní název úložiště. Pokud je zadán název vlastního úložiště, je úložiště automaticky vytvořeno.|
|permissionLevel|string|Nepovinný parametr. Určuje přístupová oprávnění udělená procesům rolí. Pokud chcete, aby přístup k privátnímu klíči měly jenom procesy se zvýšenými oprávněními, zadejte `elevated` oprávnění. oprávnění `limitedOrElevated` umožňuje všem procesům rolí přístup k privátnímu klíči. Možné hodnoty jsou `limitedOrElevated` nebo `elevated`. Výchozí hodnota je `limitedOrElevated`.|

##  <a name="Imports"></a>Objem
Element `Imports` popisuje kolekci modulů importu pro roli pracovního procesu, která přidávají součásti do hostovaného operačního systému. Tento prvek je nadřazeným prvkem `Import`. Tento prvek je nepovinný a role může mít jenom jeden blok runtime.

Element `Imports` je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.

##  <a name="Import"></a>Importovat
Element `Import` určuje modul, který se má přidat do hostovaného operačního systému.

Element `Import` je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.

Následující tabulka popisuje atributy prvku `Import`.

| Atribut | Typ | Popis |
| --------- | ---- | ----------- |
|moduleName|string|Povinná hodnota. Název modulu, který se má importovat Platné moduly importu jsou:<br /><br /> – RemoteAccess<br />- RemoteForwarder<br />– Diagnostika<br /><br /> Moduly RemoteAccess a RemoteForwarder umožňují nakonfigurovat instanci role pro připojení ke vzdálené ploše. Další informace najdete v tématu [povolení připojení ke vzdálené ploše](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> Modul diagnostiky umožňuje shromažďovat diagnostická data pro instanci role.|

##  <a name="Runtime"></a>Runtime
Element `Runtime` popisuje kolekci nastavení proměnných prostředí pro roli pracovního procesu, která řídí běhové prostředí hostitele Azure. Tento prvek je nadřazeným prvkem `Environment`. Tento prvek je nepovinný a role může mít jenom jeden blok runtime.

Element `Runtime` je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.

Následující tabulka popisuje atributy prvku `Runtime`:

| Atribut | Typ | Popis |
| --------- | ---- | ----------- |
|executionContext|string|Nepovinný parametr. Určuje kontext, ve kterém se spustí proces role. Výchozí kontext je `limited`.<br /><br /> -   `limited` – proces se spustí bez oprávnění správce.<br />-   `elevated` – proces se spustí s oprávněními správce.|

##  <a name="Environment"></a>Hlediska
Element `Environment` popisuje kolekci nastavení proměnných prostředí pro roli pracovního procesu. Tento prvek je nadřazeným prvkem `Variable`. Role může mít nastaven libovolný počet proměnných prostředí.

##  <a name="Variable"></a>Variabilní
Element `Variable` určuje proměnnou prostředí, která se má nastavit v hostovaném operačním systému.

Element `Variable` je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.

Následující tabulka popisuje atributy prvku `Variable`:

| Atribut | Typ | Popis |
| --------- | ---- | ----------- |
|jméno|string|Povinná hodnota. Název proměnné prostředí, kterou chcete nastavit.|
|hodnota|string|Nepovinný parametr. Hodnota, která má být nastavena pro proměnnou prostředí. Musíte zahrnout buď atribut hodnoty, nebo prvek `RoleInstanceValue`.|

##  <a name="RoleInstanceValue"></a>RoleInstanceValue
Element `RoleInstanceValue` Určuje cestu xPath, ze které se má načíst hodnota proměnné.

Následující tabulka popisuje atributy prvku `RoleInstanceValue`.

| Atribut | Typ | Popis |
| --------- | ---- | ----------- |
|cestou|string|Nepovinný parametr. Cesta umístění nastavení nasazení pro instanci Další informace naleznete v tématu [konfigurační proměnné s XPath](cloud-services-role-config-xpath.md).<br /><br /> Musíte zahrnout buď atribut hodnoty, nebo prvek `RoleInstanceValue`.|

##  <a name="EntryPoint"></a> EntryPoint
Element `EntryPoint` určuje vstupní bod pro roli. Tento prvek je nadřazený prvky `NetFxEntryPoint`. Tyto prvky umožňují zadat jinou aplikaci než výchozí WaWorkerHost. exe, která bude sloužit jako vstupní bod role.

Element `EntryPoint` je k dispozici pouze pomocí sady Azure SDK verze 1,5 nebo vyšší.

##  <a name="NetFxEntryPoint"></a> NetFxEntryPoint
Element `NetFxEntryPoint` určuje program, který se má pro roli spustit.

> [!NOTE]
>  Element `NetFxEntryPoint` je k dispozici pouze pomocí sady Azure SDK verze 1,5 nebo vyšší.

Následující tabulka popisuje atributy prvku `NetFxEntryPoint`.

| Atribut | Typ | Popis |
| --------- | ---- | ----------- |
|assemblyName|string|Povinná hodnota. Cesta a název souboru sestavení, který obsahuje vstupní bod. Cesta je relativní ke složce **\\%ROLEROOT%\Approot** (nespecifikuje **\\%ROLEROOT%\Approot** v `commandLine`, předpokládá se). **% ROLEROOT%** je proměnná prostředí udržovaná službou Azure, která představuje umístění kořenové složky pro vaši roli. Složka **\\%ROLEROOT%\Approot** představuje složku aplikace pro vaši roli.|
|targetFrameworkVersion|string|Povinná hodnota. Verze rozhraní .NET Framework, na které bylo sestavení sestaveno. Například, `targetFrameworkVersion="v4.0"`.|

##  <a name="ProgramEntryPoint"></a>ProgramEntryPoint
Element `ProgramEntryPoint` určuje program, který se má pro roli spustit. Element `ProgramEntryPoint` umožňuje zadat vstupní bod programu, který není založen na sestavení .NET.

> [!NOTE]
>  Element `ProgramEntryPoint` je k dispozici pouze pomocí sady Azure SDK verze 1,5 nebo vyšší.

Následující tabulka popisuje atributy prvku `ProgramEntryPoint`.

| Atribut | Typ | Popis |
| --------- | ---- | ----------- |
|Řádek|string|Povinná hodnota. Cesta, název souboru a jakékoli argumenty příkazového řádku programu, které mají být provedeny. Cesta je relativní ke složce **%ROLEROOT%\Approot** (nespecifikuje **%ROLEROOT%\Approot** v příkazovém řádku, předpokládá se). **% ROLEROOT%** je proměnná prostředí udržovaná službou Azure, která představuje umístění kořenové složky pro vaši roli. Složka **%ROLEROOT%\Approot** představuje složku aplikace pro vaši roli.<br /><br /> Pokud se program ukončí, role se recykluje, takže se obecně nastaví program, který bude pokračovat v běhu, a ne program, který se právě spustí a spustí omezený úkol.|
|setReadyOnProcessStart|Boolean|Povinná hodnota. Určuje, jestli instance role čeká na spuštění programu příkazového řádku pro signál. Tato hodnota musí být v tuto chvíli nastavena na `true`. Nastavení hodnoty na `false` je vyhrazeno pro budoucí použití.|

##  <a name="Startup"></a>Úvod
Element `Startup` popisuje kolekci úloh, které se spouštějí při spuštění role. Tento element může být nadřazeným prvkem prvku `Variable`. Další informace o použití úloh po spuštění role najdete v tématu [Postup konfigurace úloh po spuštění](cloud-services-startup-tasks.md). Tento prvek je nepovinný a role může mít jenom jeden spouštěcí blok.

Následující tabulka popisuje atribut prvku `Startup`.

| Atribut | Typ | Popis |
| --------- | ---- | ----------- |
|priorita|int|Pouze pro interní použití.|

##  <a name="Task"></a>Hybn
Element `Task` Určuje úlohu po spuštění, která probíhá při spuštění role. Úlohy po spuštění lze použít k provádění úloh, které připravují roli ke spouštění takových softwarových součástí nebo spouštění jiných aplikací. Úlohy jsou spouštěny v pořadí, ve kterém se nacházejí v rámci `Startup` bloku elementu.

Element `Task` je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.

Následující tabulka popisuje atributy prvku `Task`.

| Atribut | Typ | Popis |
| --------- | ---- | ----------- |
|Řádek|string|Povinná hodnota. Skript, jako je například soubor CMD, obsahující příkazy, které mají být spuštěny. Spouštěcí příkaz a dávkové soubory musí být uloženy ve formátu ANSI. Formáty souborů, které nastavují značku pořadí bajtů na začátku souboru, nebudou pracovat správně.|
|executionContext|string|Určuje kontext, ve kterém se skript spustí.<br /><br /> -   `limited` [výchozí] – spusťte se stejnými oprávněními jako role hostující proces.<br />-   `elevated` – spusťte s oprávněními správce.|
|taskType|string|Určuje chování při provádění příkazu.<br /><br /> -   `simple` [výchozí] – systém čeká na ukončení úlohy před spuštěním jakékoli jiné úlohy.<br />-   `background` – systém nečeká na ukončení úlohy.<br />-   `foreground` – podobně jako na pozadí se role nerestartuje, dokud nebudou všechny úlohy na popředí ukončeny.|

##  <a name="Contents"></a>Obsah
Element `Contents` popisuje shromažďování obsahu pro roli pracovního procesu. Tento prvek je nadřazeným prvkem `Content`.

Element `Contents` je k dispozici pouze pomocí sady Azure SDK verze 1,5 nebo vyšší.

##  <a name="Content"></a>Sušin
Element `Content` definuje zdrojové umístění obsahu, který se má zkopírovat na virtuální počítač Azure, a cílovou cestu, do které se má zkopírovat.

Element `Content` je k dispozici pouze pomocí sady Azure SDK verze 1,5 nebo vyšší.

Následující tabulka popisuje atributy prvku `Content`.

| Atribut | Typ | Popis |
| --------- | ---- | ----------- |
|destination|string|Povinná hodnota. Umístění na virtuálním počítači Azure, na který je umístěn obsah. Toto umístění je relativní vzhledem ke složce **%ROLEROOT%\Approot**.|

Tento prvek je nadřazeným prvkem prvku `SourceDirectory`.

##  <a name="SourceDirectory"></a>SourceDirectory
Element `SourceDirectory` definuje místní adresář, ze kterého se kopíruje obsah. Tento prvek slouží k zadání místního obsahu pro kopírování do virtuálního počítače Azure.

Element `SourceDirectory` je k dispozici pouze pomocí sady Azure SDK verze 1,5 nebo vyšší.

Následující tabulka popisuje atributy prvku `SourceDirectory`.

| Atribut | Typ | Popis |
| --------- | ---- | ----------- |
|Cesta|string|Povinná hodnota. Relativní nebo absolutní cesta k místnímu adresáři, jehož obsah se zkopíruje na virtuální počítač Azure. Rozšíření proměnných prostředí v cestě k adresáři je podporováno.|

## <a name="see-also"></a>Viz také
[Schéma definice cloudové služby (Classic)](schema-csdef-file.md)



