---
title: Podpora Azure IoT Hub MQTT 5 (Preview)
description: Další informace o podpoře MQTT 5 v IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: fb2cc0b81083936a67bcd465e0408b9f4b53996b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96603329"
---
# <a name="iot-hub-mqtt-5-support-overview-preview"></a>Přehled podpory IoT Hub MQTT 5 (Preview)

**Verze:** 2,0 **API-Version:** 2020-10-01-Preview

Tento dokument definuje rozhraní API roviny dat IoT Hub přes protokol MQTT verze 5,0. Kompletní definice v tomto rozhraní API najdete v tématu [Reference k rozhraní API](iot-hub-mqtt-5-reference.md) .

## <a name="prerequisites"></a>Požadavky

- [Povolit režim náhledu](iot-hub-preview-mode.md) na značce nové IoT Hub a vyzkoušet MQTT 5.
- Vyžaduje se předchozí znalost [specifikace MQTT 5](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html) .

## <a name="level-of-support-and-limitations"></a>Úroveň podpory a omezení

Podpora IoT Hub pro MQTT 5 je ve verzi Preview a omezená následujícími způsoby (prostřednictvím předávaného klientovi prostřednictvím `CONNACK` vlastností, pokud není výslovně uvedeno jinak):

- Zatím není k dispozici žádná oficiální podpora [sady SDK pro Azure IoT Hub zařízení](iot-hub-devguide-sdks.md) .
- Identifikátory předplatného se nepodporují.
- Sdílené odběry nejsou podporovány.
- `RETAIN` není podporováno.
- `Maximum QoS` je `1`.
- `Maximum Packet Size` je `256 KiB` (podléhá dalším omezením na operaci).
- Přiřazená ID klientů nejsou podporovaná.
- `Keep Alive` je omezené na `19 min` (max. zpoždění pro kontrolu živého provozu – `28.5 min` ).
- `Topic Alias Maximum` je `10`.
- `Response Information` není podporováno; `CONNACK` nevrátí `Response Information` vlastnost, i když `CONNECT` obsahuje `Request Response Information` vlastnost.
- `Receive Maximum` (maximální počet povolených nepotvrzených `PUBLISH` paketů (v směrech klienta-serveru) s `QoS: 1` ) je `16` .
- Jeden klient nemůže mít více než `50` odběry.
  Po dosažení limitu `SUBACK` vrátí `0x97` kód důvodu pro odběry (překročená kvóta).

## <a name="connection-lifecycle"></a>Životní cyklus připojení

### <a name="connection"></a>Připojení

Pokud chcete klienta připojit k IoT Hub pomocí tohoto rozhraní API, vytvořte připojení podle specifikace pro MQTT 5.
Klient musí odeslat `CONNECT` paket do 30 sekund po úspěšném ověření TLS handshake nebo server ukončí připojení.
Tady je příklad `CONNECT` paketu:

```yaml
-> CONNECT
    Protocol_Version: 5
    Clean_Start: 0
    Client_Id: D1
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    api-version: 2020-10-10
    host: abc.azure-devices.net
    sas-at: 1600987795320
    sas-expiry: 1600987195320
    client-agent: artisan;Linux
```

- `Authentication Method` vlastnost je povinná a určuje, která metoda ověřování se používá. Další informace o metodě ověřování najdete v tématu [ověřování](#authentication).
- `Authentication Data` zpracování vlastností závisí na `Authentication Method` . Pokud `Authentication Method` je nastaveno na `SAS` , pak `Authentication Data` je vyžadováno a musí obsahovat platný podpis. Další informace o datech ověřování najdete v tématu [ověřování](#authentication).
- `api-version` vlastnost je povinná a musí se nastavit na hodnotu verze rozhraní API poskytnutou v záhlaví této specifikace, aby se tato specifikace dala použít.
- `host` vlastnost definuje název hostitele tenanta. Vyžaduje se, pokud se v záznamu Hello klienta během TLS handshake neSNIo rozšíření.
- `sas-at` definuje čas připojení.
- `sas-expiry` definuje čas vypršení platnosti zadaného SAS.
- `client-agent` volitelně komunikuje informace o klientovi, který vytváří připojení.

> [!NOTE]
> `Authentication Method` a další vlastnosti v rámci specifikace se jmény velkými písmeny jsou vlastnosti první třídy v MQTT 5 – jsou popsány v podrobnostech ve specifikaci MQTT 5. `api-version` a jiné vlastnosti v případě pomlčky jsou vlastnosti uživatele specifické pro IoT Hub rozhraní API.

IoT Hub odpoví s `CONNACK` paketem, jakmile se dokončí s ověřováním a načítá data pro podporu připojení. Pokud je připojení úspěšně navázáno, `CONNACK` vypadá to, jako:

```yaml
<- CONNACK
    Session_Present: 1
    Reason_Code: 0x00
    Session_Expiry_Interval: 0xFFFFFFFF # included only if CONNECT specified value less than 0xFFFFFFFF and more than 0x00
    Receive_Maximum: 16
    Maximum_QoS: 1
    Retain_Available: 0
    Maximum_Packet_Size: 262144
    Topic_Alias_Maximum: 10
    Subscription_Identifiers_Available: 0
    Shared_Subscriptions_Available: 0
    Server_Keep_Alive: 1140 # included only if client did not specify Keep Alive or if it specified a bigger value
```

Tyto `CONNACK` vlastnosti paketů následují po [specifikaci MQTT 5](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901080). Odrážejí možnosti IoT Hub.

### <a name="authentication"></a>Authentication

`Authentication Method`Vlastnost v `CONNECT` klientovi definuje druh ověřování, které používá pro toto připojení:

- `SAS` -Sdílený přístupový podpis je uveden ve `CONNECT` `Authentication Data` vlastnosti,
- `X509` -klient spoléhá na ověření certifikátu klienta.

 Ověřování se nepovede, pokud metoda ověřování neodpovídá nakonfigurované metodě klienta v IoT Hub.

> [!NOTE]
> Toto rozhraní API vyžaduje, `Authentication Method` aby se vlastnost nastavila v `CONNECT` paketu. Pokud `Authentication Method` vlastnost není zadána, připojení se nezdařila s `Bad Request` odpovědí.

Ověřování uživatelského jména a hesla používané v předchozích verzích API se nepodporuje.

#### <a name="sas"></a>SAS

Při ověřování pomocí SAS musí klient poskytnout podpis kontextu připojení. To potvrzuje pravost připojení MQTT. Signatura musí být založená na jednom ze dvou ověřovacích klíčů v konfiguraci klienta v IoT Hub nebo na jednom ze dvou sdílených přístupových klíčů [zásad sdíleného přístupu](iot-hub-devguide-security.md).

Řetězec, který se má podepsat, musí být tvořen následujícím způsobem:

```text
{host name}\n
{Client Id}\n
{sas-policy}\n
{sas-at}\n
{sas-expiry}\n
```

- `host name` je odvozen buď od rozšíření SNI (prezentující klientem v záznamu Hello klienta během ověřování TLS), nebo `host` v `CONNECT` paketu.
- `Client Id` je identifikátor klienta v `CONNECT` paketu.
- `sas-policy` – Pokud je k dispozici, definuje IoT Hub zásady přístupu používané pro ověřování. Je kódovaný jako vlastnost uživatele v `CONNECT` paketu. Volitelné: vynechání znamená, že se místo toho použije nastavení ověřování v registru zařízení.
- `sas-at` – Pokud je k dispozici, určuje čas připojení aktuální čas. Je zakódována jako vlastnost uživatele `time` typu v `CONNECT` paketu.
- `sas-expiry` definuje čas vypršení platnosti ověřování. Jedná se `time` o vlastnost uživatele zadanou v `CONNECT` paketu. Tato vlastnost je povinná.

U volitelných parametrů, pokud je vynecháno, je nutné místo v řetězci pro podepsání použít prázdný řetězec.

HMAC-SHA256 se používá k vytvoření hodnoty hash řetězce na základě jednoho ze symetrických klíčů zařízení. Hodnota hash se pak nastaví jako hodnota `Authentication Data` Vlastnosti.

#### <a name="x509"></a>X509

Pokud `Authentication Method` je vlastnost nastavena na `X509` , IoT Hub ověřuje připojení na základě poskytnutého klientského certifikátu.

#### <a name="reauthentication"></a>Opětovné ověření

Pokud se použije ověřování na základě SAS, doporučujeme použít krátkodobé ověřovací tokeny. Aby se připojení ověřilo a zabránilo odpojení z důvodu vypršení platnosti, musí se klient znovu ověřit odesláním `AUTH` paketu `Reason Code: 0x19` (opakované ověření):

```yaml
-> AUTH
    Reason_Code: 0x19
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    sas-at: {current time}
    sas-expiry: {SAS expiry time}
```

Pravidly

- `Authentication Method` musí být stejná jako ta, která se používá pro počáteční ověřování.
- Pokud se připojení původně ověřilo pomocí SAS na základě zásad sdíleného přístupu, musí být signatura použitá při opakovaném ověřování založena na stejných zásadách.

Pokud se opakované ověření zdaří, IoT Hub odesílá `AUTH` paket pomocí `Reason Code: 0x00` (úspěch). V opačném případě IoT Hub odesílá `DISCONNECT` paket s `Reason Code: 0x87` (neautorizovaným) a ukončí připojení.

### <a name="disconnection"></a>Odpojení

Server může odpojit klienta z několika důvodů:

- Klient se nechová způsobem, který znemožňuje reagovat přímo na záporné potvrzení (nebo odpověď).
- Server se nedaří udržet stav připojení v aktuálním stavu.
- Klient se stejnou identitou se připojil.

Server se může odpojit od kódu příčiny definovaného ve specifikaci MQTT 5,0. Významné zmínky:

- `135` (Neautorizováno) Pokud se opakované ověření nepovede, aktuální platnost tokenu SAS nebo změny přihlašovacích údajů zařízení se zaplní.
- `142` (Převzetí relace) při otevření nového připojení ke stejné identitě klienta.
- `159` (Překročila se rychlost připojení), když rychlost připojení centra IoT překračuje  
- `131` (Chyba specifická pro implementaci) se používá pro všechny vlastní chyby definované v tomto rozhraní API. `status` a `reason` vlastnosti budou použity k sdělování dalších podrobností o příčině odpojení (podrobnosti naleznete v [odpovědi](#response) ).

## <a name="operations"></a>Operace

Všechny funkce v tomto rozhraní API se vyjadřují jako operace. Tady je příklad operace odeslání telemetrie:

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 3
    Topic: $iothub/telemetry
    Payload: Hello

<- PUBACK
    Packet_Id: 3
    Reason_Code: 0
```

Kompletní specifikace operací v tomto rozhraní API najdete v tématu [Reference k rozhraní API](iot-hub-mqtt-5-reference.md).

> [!NOTE]
> Všechny ukázky v této specifikaci se zobrazují z perspektivy klienta. Sign (podepsat) `->` znamená odeslání paketu klienta `<-` – příjem.

### <a name="message-topics-and-subscriptions"></a>Témata a odběry zpráv

Témata použitá v zprávách operací v tomto rozhraní API začínají na `$iothub/` .
Sémantika zprostředkovatele MQTT se nevztahuje na tyto operace (podrobnosti najdete[v části " \$ informace o") ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901246).
Témata od `$iothub/` , která nejsou definovaná v tomto rozhraní API, se nepodporují:

- Odeslání zpráv do nedefinovaného tématu má za následek `Not Found` odpověď (podrobnosti viz [odpověď](#response) níže),
- Přihlášení k odběru nedefinovaného tématu má za následek `SUBACK` `Reason Code: 0x8F` (neplatný filtr tématu).

Názvy témat a názvů vlastností rozlišují velká a malá písmena a musí být přesně shodné. Například `$iothub/telemetry/` není podporována, pokud `$iothub/telemetry` je.

> [!NOTE]
> Zástupné znaky v předplatných se `$iothub/..` nepodporují. To znamená, že klient se nemůže přihlásit k odběru `$iothub/+` nebo `$iothub/#` . Při pokusu o provedení této výsledků `SUBACK` se `Reason Code: 0xA2` vrátí (odběr se zástupnými znaky není podporován). `+`Místo parametrů cesty v názvu tématu pro operace, které mají, jsou podporovány pouze zástupné znaky s jedním segmentem ().

### <a name="interaction-types"></a>Typy interakcí

Všechny operace v tomto rozhraní API jsou založené na jednom ze dvou typů interakce:

- Zpráva s nepovinným potvrzením (MessageAck)
- Request-Response (ReqRep)

Operace se také liší podle směru (určení směr počáteční zprávy v Exchangi):

- Klient-server (c2s)
- Server-klient (s2c)

Například odeslání telemetrie je operace typu klient-server "zpráva s potvrzením", zatímco metoda manipulace s přímým přístupem je klientská operace typu Request-Response.

#### <a name="message-acknowledgement-interactions"></a>Interakce potvrzení zpráv

Zpráva s nepovinným potvrzením (MessageAck) se vyjadřuje jako `PUBLISH` Výměna `PUBACK` paketů a v MQTT. Potvrzení je nepovinné a odesilatel se může rozhodnout, že ho nepožaduje odesláním `PUBLISH` paketu `QoS: 0` .

> [!NOTE]
> Pokud `PUBACK` musí být vlastnosti v paketu zkráceny `Maximum Packet Size` , protože jsou deklarovány klientem, IoT Hub Zachová tolik vlastností uživatele, kolik jich bude možné do daného limitu přizpůsobit. Výše uvedené vlastnosti uživatele mají větší šanci na odeslání než ty, které jsou uvedené později. `Reason String` vlastnost má nejnižší prioritu.

##### <a name="example-of-simple-messageack-interaction"></a>Příklad jednoduché interakce MessageAck

Message:

```yaml
PUBLISH
    QoS: 1
    Packet_Id: 34
    Topic: $iothub/{request.path}
    Payload: <any>
```

Potvrzení (úspěch):

```yaml
PUBACK
    Packet_Id: 34
    Reason_Code: 0
```

#### <a name="request-response-interactions"></a>Interakce Request-Response

V rámci Request-Response (ReqRep) se do paketů převádí požadavky i odpovědi `PUBLISH` `QoS: 0` .

`Correlation Data` vlastnost musí být nastavena v obou parametrech a použita pro spárování paketu odpovědi pro požadavek.

Toto rozhraní API používá jedno téma odpovědi `$iothub/responses` pro všechny operace ReqRep. Přihlášení k odběru nebo odhlášení odběru z tohoto tématu pro operace mezi klientem a serverem není vyžadováno – Server předpokládá, že jsou všichni klienti přihlášení k odběru.

##### <a name="example-of-simple-reqrep-interaction"></a>Příklad jednoduché interakce ReqRep

Požadavek:

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/{request.path}
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

Odpověď (úspěch):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

Interakce ReqRep nepodporují `PUBLISH` pakety `QoS: 1` jako zprávy žádosti nebo odpovědi. Odeslání `PUBLISH` výsledků žádosti v `Bad Request` reakci.

Maximální délka podporovaná ve `Correlation Data` vlastnosti je 16 bajtů. Pokud `Correlation Data` je vlastnost v `PUBLISH` paketu nastavená na hodnotu delší než 16 bajtů, IoT Hub `DISCONNECT` posílá `Bad Request` výsledek a ukončí připojení. Toto chování platí jenom pro pakety vyměňované v rámci tohoto rozhraní API.

> [!NOTE]
> Korelační data jsou libovolná sekvence bajtů, například není zaručena, že by to byl řetězec UTF-8.
>
> ReqRep použít předdefinovaná témata pro odpověď; Vlastnost tématu odpovědi v `PUBLISH` paketu požadavku (Pokud je nastavena odesílatelem) je ignorována.

IoT Hub automaticky přihlásí klienta k tématům odpovědí pro všechny operace ReqRep mezi klientem a serverem. I v případě, že klient explicitně zruší odběr z tématu odpovědi, IoT Hub automaticky obnoví předplatné. U vzájemných interakcí ReqRep mezi servery je stále nutné se přihlásit k odběru zařízení.

### <a name="message-properties"></a>Vlastnosti zprávy

Vlastnosti operace – systém nebo uživatelsky definované – jsou vyjádřené jako vlastnosti paketů v MQTT 5.

Názvy vlastností uživatele rozlišují velká a malá písmena a musí být zadány přesně tak, jak jsou definovány. Například `Trace-ID` není podporována, pokud `trace-id` je.

Žádosti s vlastnostmi uživatele mimo specifikaci a s `@` výsledkem chyby předpony.

Vlastnosti systému jsou zakódovány buď jako vlastnosti první třídy (například `Content Type` ), nebo jako vlastnosti uživatele. Specifikace poskytuje vyčerpávající seznam podporovaných vlastností systému.
Všechny vlastnosti první třídy jsou ignorovány, pokud není podpora výslovně uvedena ve specifikaci.

Pokud jsou povolené uživatelsky definované vlastnosti, musí jejich názvy odpovídat formátu `@{property name}` . Uživatelsky definované vlastnosti podporují jenom platné řetězcové hodnoty UTF-8. například `MyProperty1` vlastnost s hodnotou `15` musí být kódována jako vlastnost uživatele s názvem `@MyProperty` a hodnotou `15` .

Pokud IoT Hub nerozpozná vlastnost uživatele, je považována za chybu a IoT Hub reaguje s `PUBACK` `Reason Code: 0x83` (chyba specifická pro implementaci) a `status: 0100` (chybný požadavek). Pokud potvrzení nebylo vyžádáno (QoS: 0), `DISCONNECT` paket se stejnou chybou se pošle zpátky a připojení se ukončí.

Toto rozhraní API definuje následující datové typy kromě těchto `string` :

- `time`: počet milisekund od `1970-01-01T00:00:00.000Z` . například `1600987195320` pro `2020-09-24T22:39:55.320Z` ,
- `u32`: celé číslo bez znaménka 32-bit integer,
- `u64`: celé číslo bez znaménka 64-bit integer,
- `i32`: číslo se znaménkem, které je 32-bit integer.

### <a name="response"></a>Odpověď

Interakce můžou vést k různým výsledkům: `Success` , `Bad Request` , `Not Found` a jiným.
Výsledky se od sebe liší podle `status` Vlastnosti uživatele. `Reason Code` v `PUBACK` závislosti na tom, co je to možné, se v paketech (pro interakce MessageAck) shodují `status` .

> [!NOTE]
> Pokud klient Určuje `Request Problem Information: 0` v paketu připojení, nebudou se k paketům posílat žádné vlastnosti uživatele, `PUBACK` aby vyhověly specifikaci MQTT 5, včetně `status` Vlastnosti. V takovém případě může klient stále spoléhat na to, `Reason Code` jestli je potvrzení kladné nebo záporné. 

Každá interakce má výchozí (nebo úspěch). Má `Reason Code` `0` `status` vlastnost a není nastavena. V opačném případě:

- V případě interakcí MessageAck je `PUBACK` `Reason Code` jiný než 0x0 (úspěch). `status` vlastnost může být přítomna k dalšímu objasnění výsledku.
- V případě interakcí ReqRep `PUBLISH` je `status` nastavená vlastnost Response.
- Vzhledem k tomu, že neexistuje žádný způsob, jak reagovat na MessageAck interakce `QoS: 0` přímo, se `DISCONNECT` místo toho pošle paket s informacemi o odezvě, které následují po odpojení.

Příklady:

Chybný požadavek (MessageAck):

```yaml
PUBACK
    Reason_Code: 131
    status: 0100
    reason: Unknown property `test`
```

Neautorizováno (MessageAck):

```yaml
PUBACK
    Reason_Code: 135
    status: 0101
```

Neautorizováno (ReqRep):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: ...
    status: 0101
```

V případě potřeby IoT Hub nastaví následující vlastnosti uživatele:

- `status` – Rozšířený kód IoT Hub pro stav operace. Tento kód lze použít k odlišení výsledků.
- `trace-id` – ID trasování pro operaci; IoT Hub může uchovávat další diagnostiku týkající se operace, která se dá použít k internímu šetření.
- `reason` – zpráva pro čtení, která poskytuje další informace o tom, proč operace skončila ve stavu označeném `status` vlastností.

> [!NOTE]
> Pokud vlastnost klienta nastaví `Maximum Packet Size` v paketu Connect na velmi malou hodnotu, nemusí se všechny vlastnosti uživatele vejít a v paketu se nezobrazí.
> 
> `reason` je určeno pouze pro lidi a neměl by se používat v klientské logice. Toto rozhraní API umožňuje kdykoli měnit zprávy bez upozornění nebo změny verze.
>
> Pokud klient odešle `RequestProblemInformation: 0` v paketu připojení, vlastnosti uživatele nebudou zahrnuty do potvrzení podle [specifikace MQTT 5](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901053).

#### <a name="status-code"></a>Stavový kód

`status` Vlastnost přenese stavový kód pro operaci. Je optimalizovaná pro efektivitu čtení počítačů.
Skládá se ze dvou bajtů unsigned integer kódovaných jako hex v řetězci jako `0501` .
Struktura kódu (bitová mapa):

```text
7 6 5 4 3 2 1 0 | 7 6 5 4 3 2 1 0
0 0 0 0 0 R T T | C C C C C C C C
```

První bajt se používá pro příznaky:

- bity 0 a 1 označují typ výsledků:
  - `00` – úspěch
  - `01` – Chyba klienta
  - `10` – Chyba serveru
- bit 2: `1` indikuje, že chyba je opakovaná
- bity 3 až 7 jsou rezervované a musí být nastavené na `0`

Druhý bajt obsahuje skutečný jedinečný kód odpovědi. Kódy chyb s různými příznaky můžou mít stejnou hodnotu druhého bajtu. Například mohou být `0001` ,, `0101` `0201` , `0301` kódy chyb mají jedinečný význam.

Například `Too Many Requests` je klient, opakovaná chyba s vlastním kódem `1` . Jeho hodnota je `0000 0101 0000 0001` nebo `0x0501` .

Klienti můžou použít bity typu k určení, jestli operace byla úspěšně ukončena. Klienti mohou také použít opakovaný bit k rozhodnutí, zda je rozumné pro opakování operace.

## <a name="recommendations"></a>Doporučení

### <a name="session-management"></a>Správa relací

`CONNACK` paket přináší `Session Present` vlastnost, která označuje, zda server obnovil dříve vytvořenou relaci. Tato vlastnost slouží k tomu, abyste zjistili, jestli se chcete přihlásit k odběru témat, nebo můžete přeskočit odběr, protože předplatné bylo předchozí.

Aby bylo možné spoléhat na to `Session Present` , klient musí sledovat odběry, které provedl (tj. odeslaný `SUBSCRIBE` paket a přijatý `SUBACK` s úspěšným kódem důvodu), nebo se přihlásit k odběru všech témat v jednom `SUBSCRIBE` / `SUBACK` serveru Exchange. V opačném případě, pokud klient odesílá dva `SUBSCRIBE` pakety a pouze jeden z nich je úspěšně zpracován serverem, server bude komunikovat v nástroji a bude `Session Present: 1` `CONNACK` mít pouze část předplatných klienta.

Aby se předešlo tomu, že se starší verze klienta přihlásila ke všem tématům, je lepší se přihlásit k nepodmíněnému přihlášení, když se změní chování klienta (například jako součást aktualizace firmwaru). K tomu, aby nedošlo k žádnému zastaralému předplatnému (z maximálního povoleného počtu předplatných), explicitně odhlaste odběry, které se už nepoužívají.

### <a name="batching"></a>Dávkování

Není k dispozici žádný speciální formát pro odeslání dávky zpráv. Aby se snížila režie operací náročných na prostředky v TLS a sítích, propojte pakety ( `PUBLISH` ,, `PUBACK` `SUBSCRIBE` a tak dále), než je předáte do základního zásobníku TLS/TCP. Klient může také usnadnit aliasy tématu v rámci "dávky":

- Uveďte kompletní název tématu v prvním `PUBLISH` paketu pro připojení a přidružte alias k tématu.
- Vložte následující pakety pro stejné téma s prázdným názvem tématu a vlastností aliasu tématu.

## <a name="migration"></a>Migrace

V této části jsou uvedené změny v rozhraní API v porovnání s [předchozím MQTT API](iot-hub-mqtt-support.md).

- Transportní protokol je MQTT 5. Dřív MQTT 3.1.1.
- Kontextové informace pro ověřování SAS jsou obsaženy v `CONNECT` paketu přímo namísto kódování společně s podpisem.
- Metoda ověřování slouží k označení používané metody ověřování.
- Sdílený přístupový podpis je vložen do vlastnosti ověřovací data. Použilo se pole dříve používané heslo.
- Témata týkající se různých operací:
  - Telemetrie: `$iothub/telemetry` místo `devices/{Client Id}/messages/events` ,
  - Příkazy: `$iothub/commands` místo `devices/{Client Id}/messages/devicebound` ,
  - Nahlášená zdvojená Oprava: `$iothub/twin/patch/reported` místo `$iothub/twin/PATCH/properties/reported` ,
  - Počet změněných požadovaných stavů oznamujících vláken: `$iothub/twin/patch/desired` místo `$iothub/twin/PATCH/properties/desired` .
- Předplatné pro téma odpovědi na požadavky typu klient-server se nevyžaduje.
- Vlastnosti uživatele se používají místo vlastností kódování v segmentu název tématu.
- názvy vlastností jsou zadány v konvenci pojmenování "spojovník-Case" místo zkratek se speciální předponou. Uživatelem definované vlastnosti teď místo toho vyžadují předponu. Instance je například `$.mid` nyní `message-id` , zatímco se `myProperty1` stala `@myProperty1` .
- Vlastnost dat korelace se používá ke korelaci požadavků a zpráv odpovědí pro operace požadavku a odpovědi místo `$rid` vlastnosti kódované v tématu.
- `iothub-connection-auth-method` vlastnost již není označena pro události telemetrie.
- Příkazy C2D se nevyprázdní v absenci předplatného ze zařízení. Zůstanou zařazené do fronty až do přihlášení k odběru nebo vypršení platnosti zařízení.

## <a name="examples"></a>Příklady

### <a name="send-telemetry"></a>Odeslání telemetrie

Message:

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 31
    Topic: $iothub/telemetry
    @myProperty1: My String Value # optional
    creation-time: 1600987195320 # optional
    @ No_Rules-ForUser-PROPERTIES: Any UTF-8 string value # optional
    Payload: <data>
```

Potvrzení

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 0
```

Alternativní potvrzení (omezení):

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 151
    status: 0501
```

---

### <a name="send-get-twins-state"></a>Odeslat stav vlákna získat zdvojení

Požadavek:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
    Correlation_Data: 0x01 0xFA
    Payload: <empty>
```

Odpověď (úspěch):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: <twin/desired state>
```

Odpověď (nepovoluje se):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    status: 0102
    reason: Operation not allowed for `B2` SKU
    Payload: <empty>
```

---

### <a name="handle-direct-method-call"></a>Zpracovat přímé volání metody

Požadavek:

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/abc
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

Odpověď (úspěch):

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    response-code: 200 # user defined response code
    Payload: <data>
```

> [!NOTE]
> `status` není nastavené – jedná se o odpověď po úspěchu.

Nedostupná odpověď zařízení:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    status: 0603
```

---

### <a name="error-while-using-qos-0-part-1"></a>Chyba při používání technologie QoS 0, část 1

Požadavek:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/gett # misspelled topic name - server won't recognize it as Request-Response interaction
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

Odpověď:

```yaml
<- DISCONNECT
    Reason_Code: 144
    reason: "Unsupported topic: `$iothub/twin/gett`"
```

---

### <a name="error-while-using-qos-0-part-2"></a>Chyba při používání technologie QoS 0, část 2

Požadavek:

```yaml
-> PUBLISH # missing Correlation Data
    QoS: 0
    Topic: $iothub/twin/get
    Payload: <data>
```

Odpověď:

```yaml
<- DISCONNECT
    Reason_Code: 131
    status: 0100
    reason: "`Correlation Data` property is missing"
```
## <a name="next-steps"></a>Další kroky

- Referenční informace o rozhraní API pro MQTT 5 Preview najdete v [referenčních informacích k rozhraní API pro IoT Hub datové roviny MQTT 5](iot-hub-mqtt-5-reference.md).
- Postup při sledování ukázky v jazyce C# najdete v tématu [úložiště ukázek GitHubu](https://github.com/Azure-Samples/iot-hub-mqtt-5-preview-samples-csharp).