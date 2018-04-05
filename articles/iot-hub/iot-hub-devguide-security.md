---
title: Porozumět zabezpečení služby Azure IoT Hub | Microsoft Docs
description: Příručka vývojáře – řízení přístupu ke službě IoT Hub pro aplikace pro zařízení a back-end aplikace. Obsahuje informace o tokeny zabezpečení a podporu pro certifikáty X.509.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 45631e70-865b-4e06-bb1d-aae1175a52ba
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/12/2018
ms.author: dobett
ms.openlocfilehash: c410db9a7255a039ab9b41ae39f2fe1018719f8f
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="control-access-to-iot-hub"></a>Řízení přístupu k IoT Hubu

Tento článek popisuje možnosti pro zabezpečení služby IoT hub. IoT Hub používá *oprávnění* k udělení přístupu k každý koncový bod centra IoT. Oprávnění omezit přístup do služby IoT hub, v závislosti na funkcích.

Tento článek představuje:

* Jiná oprávnění, že můžete udělit do zařízení nebo back-end aplikace pro přístup k službě IoT hub.
* Proces ověřování a tokenů se používá k ověření oprávnění.
* Postup určení oboru přihlašovací údaje k omezení přístupu ke konkrétním prostředkům.
* Podpora služby IoT Hub pro certifikáty X.509.
* Mechanismy ověřování vlastní zařízení, které používají existující registrech identity zařízení nebo schémat ověřování.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Musí mít příslušná oprávnění pro přístup k libovolnému koncové body centra IoT. Zařízení musí obsahovat třeba token obsahující zabezpečovací pověření společně s každou zprávu, kterou odešle do služby IoT Hub.

## <a name="access-control-and-permissions"></a>Řízení přístupu a oprávnění

Můžete udělit [oprávnění](#iot-hub-permissions) následujícími způsoby:

* **IoT hub úrovni sdílených zásad přístupu**. Zásady sdíleného přístupu můžete udělit libovolnou kombinaci [oprávnění](#iot-hub-permissions). Můžete definovat zásady v [portál Azure][lnk-management-portal], nebo programově pomocí [zprostředkovatele prostředků služby IoT Hub rozhraní REST API][lnk-resource-provider-apis]. Nově vytvořený IoT hub má následující výchozích zásad:

  * **iothubowner**: zásada se všechna oprávnění.
  * **Služba**: zásada se **ServiceConnect** oprávnění.
  * **zařízení**: zásada se **DeviceConnect** oprávnění.
  * **registryRead**: zásada se **RegistryRead** oprávnění.
  * **registryReadWrite**: zásada se **RegistryRead** a RegistryWrite oprávnění.
  * **Podle zařízení zabezpečovací pověření**. Každé centrum IoT obsahuje [registru identit][lnk-identity-registry]. Pro každé zařízení v registru této identity můžete nakonfigurovat zabezpečovací pověření, která udělují **DeviceConnect** oprávnění obor ke koncovým bodům odpovídající zařízení.

Například v typického řešení IoT:

* Komponenty správy zařízení používá *registryReadWrite* zásad.
* Součástí procesoru událostí se používá *služby* zásad.
* Používá komponentu spuštění zařízení obchodní logiku *služby* zásad.
* Jednotlivých zařízení se připojují přes přihlašovací údaje uložené v registru identit služby IoT hub.

> [!NOTE]
> V tématu [oprávnění](#iot-hub-permissions) podrobné informace.

## <a name="authentication"></a>Authentication

Azure IoT Hub uděluje přístup ke koncovým bodům kontrolou token proti zásady sdíleného přístupu a identit registru zabezpečovací pověření.

Zabezpečovací přihlašovací údaje, jako jsou symetrického klíče, se nikdy odeslány prostřednictvím sítě.

> [!NOTE]
> Poskytovatel prostředků Azure IoT Hub je zabezpečená vašeho předplatného Azure, jsou všechny zprostředkovatele v [Azure Resource Manager][lnk-azure-resource-manager].

Další informace o tom, jak vytvořit a používat tokeny zabezpečení najdete v tématu [tokeny zabezpečení služby IoT Hub][lnk-sas-tokens].

### <a name="protocol-specifics"></a>Protokol podrobností

Každý podporovaný protokol, například MQTT AMQP nebo HTTPS, je určena k přenosu tokeny různými způsoby.

Při použití MQTT, paket CONNECT má deviceId jako ClientId, `{iothubhostname}/{deviceId}` v poli uživatelské jméno a do pole pro heslo tokenu SAS. `{iothubhostname}` by měla být úplná CName služby IoT hub (například contoso.azure-devices.net).

Při použití [AMQP][lnk-amqp], podporuje IoT Hub [SASL prostý] [ lnk-sasl-plain] a [AMQP deklarace identity – zabezpečení na základě-] [ lnk-cbs].

Pokud používáte AMQP deklarace identity – zabezpečení na základě-, standardní Určuje, jak přenést tyto tokeny.

Pro SASL prostý **uživatelské jméno** může být:

* `{policyName}@sas.root.{iothubName}` Pokud se používá tokeny úrovni centra IoT.
* `{deviceId}@sas.{iothubname}` Pokud používáte zařízení obor tokeny.

V obou případech pole pro heslo obsahuje token, jak je popsáno v [tokeny zabezpečení služby IoT Hub][lnk-sas-tokens].

HTTPS implementuje ověřování zahrnutím platný token v **autorizace** hlavičky žádosti.

#### <a name="example"></a>Příklad:

Uživatelské jméno (DeviceId je malá a velká písmena): `iothubname.azure-devices.net/DeviceId`

Heslo (Generovat SAS token s [explorer zařízení] [ lnk-device-explorer] nástroj): `SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> [SDK služby Azure IoT] [ lnk-sdks] automaticky generovat tokeny při připojování ke službě. V některých případech nepodporují SDK služby Azure IoT všechny protokoly nebo všechny metody ověřování.

### <a name="special-considerations-for-sasl-plain"></a>Zvláštní upozornění pro SASL prostý

Při použití SASL prostý s AMQP, klient připojení do služby IoT hub můžete použít jeden token pro každé připojení TCP. Když vyprší platnost tokenu, připojení TCP odpojení od služby a aktivuje opětovné připojení. Toto chování, když není problematické pro back-end aplikačním je poškození pro aplikace na zařízení z následujících důvodů:

* Brány obvykle jménem mnoho zařízení připojit. Pokud používáte SASL prostý, mají vytvořit odlišné připojení protokolu TCP pro každé zařízení připojení do služby IoT hub. Tento scénář také výrazně zvyšuje spotřeby energie a síťové prostředky a latence každé připojení zařízení.
* Zařízení s omezenými zdroji jsou nevyhovělo vyšší využití prostředků se znovu připojit po každém vypršení platnosti tokenu.

## <a name="scope-iot-hub-level-credentials"></a>Přihlašovací údaje úrovni centra IoT oboru

Můžete určit obor zásad zabezpečení na úrovni centra IoT tak, že vytvoříte tokeny pomocí identifikátoru URI prostředku s omezeným přístupem. Koncový bod k odesílání zpráv typu zařízení cloud ze zařízení, je třeba **/devices/ {deviceId} / zprávy/události**. Můžete taky zásady sdíleného přístupu úrovně rozbočovače IoT s **DeviceConnect** oprávnění pro přihlášení token je jejichž resourceURI **/devices/ {deviceId}**. Tento postup vytvoří token, který lze použít k odeslání zprávy jménem zařízení pouze **deviceId**.

Tento mechanismus je podobná [zásad vydavatele Event Hubs][lnk-event-hubs-publisher-policy]a umožňuje implementovat vlastní metody ověřování.

## <a name="security-tokens"></a>Tokeny zabezpečení

IoT Hub používá tokeny zabezpečení k ověřování zařízení a služby se odesílání klíče v drátové síti. Kromě toho mají omezenou dobu platnosti a obor tokeny zabezpečení. [Sady SDK služby Azure IoT] [ lnk-sdks] automaticky generovat tokeny bez nutnosti žádnou zvláštní konfiguraci. Některé scénáře vyžadují, abyste generovat a používat přímo tokeny zabezpečení. Mezi tyto scénáře patří:

* Přímé použití ploch MQTT, AMQP nebo HTTPS.
* Implementace vzoru služba tokenu, jak je popsáno v [ověřování zařízení vlastní][lnk-custom-auth].

Centrum IoT také umožňuje zařízení k ověření službou IoT Hub pomocí [certifikáty X.509][lnk-x509].

### <a name="security-token-structure"></a>Struktura tokenu zabezpečení

Pomocí tokenů zabezpečení udělte přístup k zařízením s ohraničenou čas a služeb na určité funkce IoT hub. Získat autorizaci k připojení ke službě IoT Hub, zařízení a služby, musí poslat tokeny zabezpečení, které jsou podepsané sdíleného přístupu nebo symetrického klíče. Tyto klíče jsou uloženy s identitu zařízení v registru identit.

Token podepsán sdílený přístupový klíč uděluje přístup k všechny funkce související s oprávněními zásady sdíleného přístupu. Podepsaný token pomocí identity zařízení symetrického klíče pouze uděluje **DeviceConnect** oprávnění pro identitu související zařízení.

Token zabezpečení má následující formát:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Zde jsou očekávaných hodnot:

| Hodnota | Popis |
| --- | --- |
| {podpis} |Řetězec podpisu HMAC SHA256 ve tvaru: `{URL-encoded-resourceURI} + "\n" + expiry`. **Důležité**: klíč je dekódovat z formátu base64 a použít jako klíč, aby prováděly výpočty HMAC SHA256. |
| {resourceURI} |Předpony identifikátoru URI (podle segmentu) koncových bodů, které lze přistupovat pomocí tohoto tokenu, počínaje název hostitele služby IoT hub (žádné protocol). Například `myHub.azure-devices.net/devices/device1`. |
| {expiry} |Řetězce UTF8 pro počet sekund od 00:00:00 UTC epoch na 1. ledna pod hodnotou 1970. |
| {Adresu URL-kódovaný resourceURI} |Nižší případ kódování URL prostředku malá identifikátor URI |
| {policyName} |Název zásady sdíleného přístupu, na který odkazuje tento token. Chybějící Pokud token odkazuje na přihlašovací údaje registru zařízení. |

**Poznámka: na předponě**: předpony identifikátoru URI se počítá podle segmentu a ne serverem znak. Například `/a/b` předpona pro `/a/b/c` , ale ne pro `/a/bc`.

Následující fragment kódu Node.js ukazuje funkci s názvem **generateSasToken** , vypočítá token zabezpečení ze vstupních údajů `resourceUri, signingKey, policyName, expiresInMins`. V dalších oddílech jsou upřesněny postupy k chybě při inicializaci jiné vstupy pro případy použití v odlišných tokenu.

```nodejs
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct autorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
};
```

Jako porovnání je ekvivalentní kód Python pro vygenerování tokenu zabezpečení:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

Funkce v jazyce C# pro vygenerování tokenu zabezpečení jsou:

```csharp
using System;
using System.Globalization;
using System.Net;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;

public static string generateSasToken(string resourceUri, string key, string policyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = WebUtility.UrlEncode(resourceUri) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}", WebUtility.UrlEncode(resourceUri), WebUtility.UrlEncode(signature), expiry);

    if (!String.IsNullOrEmpty(policyName))
    {
        token += "&skn=" + policyName;
    }

    return token;
}

```


> [!NOTE]
> Vzhledem k tomu, že doba platnosti tokenu se ověří na počítačích IoT Hub, musí být minimální odlišily v hodinách na počítač, který generuje token.

### <a name="use-sas-tokens-in-a-device-app"></a>Použití tokeny SAS v aplikaci pro zařízení

Existují dva způsoby, jak získat **DeviceConnect** oprávnění službou IoT Hub s tokeny zabezpečení: pomocí [zařízení symetrického klíče z registru identit](#use-a-symmetric-key-in-the-identity-registry), nebo použijte [sdílený přístupový klíč](#use-a-shared-access-policy).

Mějte na paměti, že všechny funkce, které jsou přístupné ze zařízení je zveřejněný prostřednictvím návrhu na koncové body s předponou `/devices/{deviceId}`.

> [!IMPORTANT]
> Jediným způsobem, že IoT Hub ověřuje určité zařízení používá symetrický klíč identity zařízení. V případech, pokud zásady sdíleného přístupu slouží k přístupu funkce zařízení, musí řešení zvažte komponentu vydání tokenu zabezpečení důvěryhodné komponentu.

Zařízení přístupem koncových bodů jsou (bez ohledu na protokol):

| Koncový bod | Funkce |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Odesílání zpráv typu zařízení cloud. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Příjem zpráv typu cloud zařízení. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Použít symetrického klíče v registru identit

Při použití identitu zařízení symetrický klíč pro vygenerování tokenu, policyName (`skn`) element tokenu je vynechán.

Například pro přístup ke všem funkcím zařízení vytvoří token musí mít následující parametry:

* identifikátor URI prostředku: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* podpisového klíče: pro všechny symetrický klíč `{device id}` identitu,
* žádný název zásady
* kdykoli vypršení platnosti.

Příkladem pomocí funkce předchozí Node.js může být:

```nodejs
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Výsledek, který uděluje přístup ke všem funkcím pro device1, bude:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Je možné vytvořit token SAS pomocí .NET [explorer zařízení] [ lnk-device-explorer] nástroje nebo a platformy, na základě Python [IoT rozšíření pro Azure CLI 2.0] [ lnk-IoT-extension-CLI-2.0] nástroj příkazového řádku.

### <a name="use-a-shared-access-policy"></a>Použijte zásady sdíleného přístupu

Při vytváření tokenu z zásady sdíleného přístupu, nastavení `skn` pole na název zásady. Tato zásada musí udělit **DeviceConnect** oprávnění.

Jsou dva základní scénáře pro přístup k zařízení funkce pomocí zásady sdíleného přístupu:

* [cloudové brány protokolu][lnk-endpoints],
* [token služby] [ lnk-custom-auth] použít k implementaci schémat vlastní ověřování.

Vzhledem k tomu, že zásady sdíleného přístupu může potenciálně udělit přístup k připojení jako jakékoli zařízení, je důležité použít správný identifikátor URI, při vytváření tokenů zabezpečení. Toto nastavení je obzvláště důležité pro token služby, které mají k určení rozsahu token k určitému zařízení pomocí identifikátoru URI prostředku. Tento bod je méně relevantní pro protokol brány jako jejich jsou již jehož prostřednictvím je umožněn přenos pro všechna zařízení.

Jako příklad tokenu služby pomocí předem vytvořené sdílené zásady přístupu volat **zařízení** by vytvořit token s následujícími parametry:

* identifikátor URI prostředku: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* podpisový klíč: jeden z klíčů `device` zásad
* Název zásady: `device`,
* kdykoli vypršení platnosti.

Příkladem pomocí funkce předchozí Node.js může být:

```nodejs
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Výsledek, který uděluje přístup ke všem funkcím pro device1, bude:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Brána protokolu pro všechna zařízení, jednoduše nastavení identifikátoru URI prostředku použít stejný token do `myhub.azure-devices.net/devices`.

### <a name="use-security-tokens-from-service-components"></a>Použít tokeny zabezpečení ze součásti služby

Součásti služby můžete vygenerovat pouze tokeny zabezpečení pomocí udělení příslušných oprávnění, jak je popsáno dříve zásady sdíleného přístupu.

Tady je funkce služby, které jsou zveřejněné na koncové body:

| Koncový bod | Funkce |
| --- | --- |
| `{iot hub host name}/devices` |Vytvoření, aktualizace, získání a odstranění identit zařízení. |
| `{iot hub host name}/messages/events` |Příjem zpráv typu zařízení cloud. |
| `{iot hub host name}/servicebound/feedback` |Přijímat zpětnou vazbu pro zprávy typu cloud zařízení. |
| `{iot hub host name}/devicebound` |Odesílání zpráv typu cloud zařízení. |

Jako příklad služby generování použití předem vytvořené sdílené zásady přístupu volat **registryRead** by vytvořit token s následujícími parametry:

* identifikátor URI prostředku: `{IoT hub name}.azure-devices.net/devices`,
* podpisový klíč: jeden z klíčů `registryRead` zásad
* Název zásady: `registryRead`,
* kdykoli vypršení platnosti.

```nodejs
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Výsledek, který by udělit přístup ke čtení všech identit zařízení, bude:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Podporované certifikáty X.509

K ověření zařízení IoT Hub tím, že nahrajete kryptografický otisk certifikátu nebo certifikační autoritu (CA) do služby Azure IoT Hub můžete použít libovolný certifikát X.509. Ověřování pomocí kryptografické otisky certifikátu pouze ověřuje, že vidění kryptografický otisk shoduje má nakonfigurovaný kryptografický otisk. Ověřování pomocí certifikační autority ověří řetěz certifikátů. 

Podporované certifikáty patří:

* **Existující certifikát X.509**. Zařízení již pravděpodobně certifikát X.509 s ním spojená. Zařízení můžete použít tento certifikát k ověření službou IoT Hub. Funguje s kryptografickým otiskem nebo ověřování certifikační Autority. 
* **Certifikační Autority podepsané certifikát X.509**. K identifikaci zařízení a ověřování službou IoT Hub, můžete certifikát X.509, generovány a podepsaný pomocí certifikační autority (CA). Funguje s kryptografickým otiskem nebo ověřování certifikační Autority.
* **A samoobslužné generované a X-509 certifikátu podepsaného svým držitelem**. Výrobce zařízení nebo interní nástroje pro nasazení můžete generovat tyto certifikáty a uložení odpovídající privátní klíč (a certifikátu) na zařízení. Můžete použít nástroje, jako [OpenSSL] [ lnk-openssl] a [Windows SelfSignedCertificate] [ lnk-selfsigned] nástroj pro tento účel. Pracuje pouze s kryptografickým otiskem ověřování. 

Zařízení může použít certifikát X.509 nebo token zabezpečení pro ověřování, ale ne obojí.

Další informace o ověřování pomocí certifikační autority najdete v tématu [koncepční přehled o certifikáty certifikační Autority X.509](iot-hub-x509ca-concept.md).

### <a name="register-an-x509-certificate-for-a-device"></a>Registrovat certifikát X.509 pro zařízení.

[Sady SDK služby Azure IoT pro jazyk C#][lnk-service-sdk] (verze 1.0.8+) podporuje registraci zařízení, které používá certifikátu X.509. certifikát pro ověřování. Jiná rozhraní API, jako je například importu a exportu zařízení také podporují certifikáty X.509.

### <a name="c-support"></a>C\# podpory

**RegistryManager** třída poskytuje programový způsob, jak registrovat zařízení. Konkrétně **AddDeviceAsync** a **UpdateDeviceAsync** metody umožňují zaregistrovat a aktualizujte zařízení v registru identit služby IoT Hub. Tyto dvě metody přijímají **zařízení** instance jako vstup. **Zařízení** třída zahrnuje **ověřování** vlastnost, která vám umožní určit primární a sekundární X.509 kryptografické otisky certifikátu. Kryptografický otisk představuje hodnotu hash SHA-1 (uložené pomocí binární kódování DER) X.509 certifikátu. Máte možnost určení primární kryptografický otisk nebo sekundární kryptografický otisk nebo obojí. Primární a sekundární kryptografické otisky jsou podporovány pro zpracování scénáře výměnu certifikátů.

Zde je ukázka C\# fragment kódu registrovat zařízení pomocí kryptografický otisk certifikátu X.509:

```csharp
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "921BC9694ADEB8929D4F7FE4B9A3A6DE58B0790B"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Použít certifikát X.509 během spuštění operací

[Zařízení Azure IoT SDK pro .NET][lnk-client-sdk] (verze 1.0.11+) podporuje použití certifikátů X.509.

### <a name="c-support"></a>C\# podpory

Třída **DeviceAuthenticationWithX509Certificate** podporuje vytváření **DeviceClient** instance, používá certifikát X.509. Certifikát X.509 musí být ve formátu PFX (také nazývané PKCS #12), který obsahuje soukromý klíč.

Zde je fragment kódu ukázka:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-authentication"></a>Ověřování vlastní zařízení

Můžete použít Centrum IoT [registru identit] [ lnk-identity-registry] ke konfiguraci zařízení zabezpečovací pověření a přistupovat pomocí ovládacího prvku [tokeny][lnk-sas-tokens]. Pokud řešení IoT už má vlastní identitu registru nebo ověřování schématu, zvažte vytvoření *token služby* integrovat do této infrastruktury službou IoT Hub. Tímto způsobem můžete další funkce IoT ve vašem řešení.

Token služby je vlastní Cloudová služba. Používá služby IoT Hub *sdílené zásady přístupu* s **DeviceConnect** oprávnění k vytvoření *obor zařízení* tokeny. Tyto tokeny povolit zařízení pro připojení do služby IoT hub.

![Kroky vzoru služby tokenů][img-tokenservice]

Zde jsou hlavní kroky vzoru tokenu služby:

1. Vytvoření služby IoT Hub sdílených zásad přístupu s **DeviceConnect** oprávnění pro službu IoT hub. Můžete vytvořit v tyto zásady [portál Azure] [ lnk-management-portal] nebo prostřednictvím kódu programu. Službu token tuto zásadu používá k podepisování tokenů, který vytváří.
1. Pokud zařízení potřebuje přístup k službě IoT hub, vyžádá podepsaný token ze služby tokenu. Zařízení můžete ověřit se schématem registru nebo ověření vaše vlastní identity k určení identity zařízení, která služba token používá k vytvoření tohoto tokenu.
1. Služba tokenu vrátí token. Token je vytvořená pomocí `/devices/{deviceId}` jako `resourceURI`, s `deviceId` jako ověřovaného zařízení. Služba tokenu použije k vytvoření tokenu zásady sdíleného přístupu.
1. Zařízení na základě tokenu přímo službou IoT hub.

> [!NOTE]
> Můžete použít třídu .NET [SharedAccessSignatureBuilder] [ lnk-dotnet-sas] nebo třída Java [IotHubServiceSasToken] [ lnk-java-sas] k vytvoření tokenu ve vaší Služba tokenu.

Služba tokenu můžete podle potřeby nastavit vypršení platnosti tokenu. Když vyprší platnost tokenu, servery služby IoT hub připojení zařízení. Potom zařízení musíte požádat o nový token od služby tokenů. Čas vypršení platnosti krátké zvyšuje zatížení zařízení a služby tokenů.

U zařízení pro připojení do vašeho centra, musí stále ho přidáte do registru identit služby IoT Hub – i když zařízení používá token a klíč zařízení pro připojení. Proto můžete nadále používat řízení přístupu podle zařízení povolením nebo zakázáním identit zařízení ve [registru identit][lnk-identity-registry]. Tento přístup snižuje rizika tokeny pomocí vypršení platnosti dlouhou dobu.

### <a name="comparison-with-a-custom-gateway"></a>Porovnání s vlastní bránu

Vzor token služby je doporučeným způsobem, jak implementovat vlastní identitu schéma registru nebo ověřování službou IoT Hub. Tento vzor je doporučená, protože nadále zpracovávat většina dat řešení IoT Hub. Ale pokud schéma vlastní ověřování tak vzájemně propojeny s protokolem, možná budete potřebovat *vlastní brány* zpracovat veškerý provoz. Příkladem takové situaci je pomocí[zabezpečení TLS (Transport Layer) a předsdílených klíčů (PSKs)][lnk-tls-psk]. Další informace najdete v tématu [brány protokolu] [ lnk-protocols] článku.

## <a name="reference-topics"></a>Témata odkazů:

Následující referenční témata poskytují další informace o řízení přístupu ke službě IoT hub.

## <a name="iot-hub-permissions"></a>IoT Hub oprávnění

Následující tabulka uvádí oprávnění, která můžete použít k řízení přístupu ke službě IoT hub.

| Oprávnění | Poznámky |
| --- | --- |
| **RegistryRead** |Uděluje přístup do registru identit pro čtení. Další informace najdete v tématu [registru identit][lnk-identity-registry]. <br/>Toto oprávnění je používán back-end cloudové služby. |
| **RegistryReadWrite** |Uděluje přístup čtení a zápisu do registru identit. Další informace najdete v tématu [registru identit][lnk-identity-registry]. <br/>Toto oprávnění je používán back-end cloudové služby. |
| **ServiceConnect** |Uděluje přístup do cloudu komunikace a sledování koncových bodů služby přístupem. <br/>Udělí oprávnění přijímat zprávy typu zařízení cloud, odesílání zpráv typu cloud zařízení a načíst odpovídající potvrzování doručení. <br/>Uděluje oprávnění k načtení potvrzení o doručení pro soubor odešle. <br/>Uděluje oprávnění k přístupu k dvojčata zařízení, které chcete aktualizovat požadované vlastnosti a značky, načíst hlášené vlastnosti a spouštět dotazy. <br/>Toto oprávnění je používán back-end cloudové služby. |
| **DeviceConnect** |Uděluje přístup k zařízení přístupem koncových bodů. <br/>Uděluje oprávnění k odesílání zpráv typu zařízení cloud a příjem zpráv typu cloud zařízení. <br/>Uděluje oprávnění k provedení nahrávání souborů ze zařízení. <br/>Uděluje oprávnění k přijímání oznámení vlastnost twin požadovaného zařízení a aktualizaci dvojče zařízení hlášené vlastnosti. <br/>Uděluje oprávnění k provedení soubor odešle. <br/>Toto oprávnění je používán zařízení. |

## <a name="additional-reference-material"></a>Odkaz na další materiály

Další témata referenční příručka vývojáře IoT Hub patří:

* [Koncové body centra IoT] [ lnk-endpoints] popisuje různé koncových bodů, které každý IoT hub zpřístupní pro spuštění a management operace.
* [Omezování a kvóty] [ lnk-quotas] popisuje kvóty a omezení chování, které se vztahují ke službě IoT Hub.
* [Azure IoT zařízení a služby sady SDK] [ lnk-sdks] uvádí různé jazykové sady SDK můžete použít při vývoji aplikace zařízení a služby, které interakci s centrem IoT.
* [IoT Hub dotazovací jazyk] [ lnk-query] popisuje dotazovací jazyk, můžete použít k načtení informací ze služby IoT Hub o úlohách a dvojčata zařízení.
* [Podpora IoT Hub MQTT] [ lnk-devguide-mqtt] poskytuje další informace o podpoře služby IoT Hub pro protokol MQTT.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili řízení přístupu služby IoT Hub, možná by vás také zajímat v následujících tématech Příručka vývojáře IoT Hub:

* [Pomocí dvojčata zařízení synchronizovat stavu a konfigurace][lnk-devguide-device-twins]
* [Volání metody přímé na zařízení][lnk-devguide-directmethods]
* [Plánování úloh na několika zařízeních][lnk-devguide-jobs]

Pokud chcete vyzkoušet některé konceptů popsaných v tomto článku, najdete v následujících kurzech IoT Hub:

* [Začínáme s Azure IoT Hub][lnk-getstarted-tutorial]
* [Odesílání zpráv typu cloud zařízení s centrem IoT][lnk-c2d-tutorial]
* [Postupy zpracování zpráv typu zařízení cloud IoT Hub][lnk-d2c-tutorial]

<!-- links and images -->

[img-tokenservice]: ./media/iot-hub-devguide-security/tokenservice.png
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-openssl]: https://www.openssl.org/
[lnk-selfsigned]: https://technet.microsoft.com/library/hh848633

[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-sas-tokens]: iot-hub-devguide-security.md#security-tokens
[lnk-amqp]: https://www.amqp.org/
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-management-portal]: https://portal.azure.com
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.auth._iot_hub_service_sas_token
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-custom-auth]: iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: iot-hub-devguide-security.md#supported-x509-certificates
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer
[lnk-IoT-extension-CLI-2.0]: https://github.com/Azure/azure-iot-cli-extension

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
