---
title: Principy zabezpečení Azure IoT Hub | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – jak řídit přístup k IoT Hubu pro aplikace pro zařízení a back-endové aplikace. Obsahuje informace o tokenech zabezpečení a podporu certifikátů X.509.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: b7f9ac7e6e7049a3b744151bc9cb05115fbac935
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729216"
---
# <a name="control-access-to-iot-hub"></a>Řízení přístupu k IoT Hubu

Tento článek popisuje možnosti zabezpečení centra IoT. IoT Hub používá *oprávnění* k udělení přístupu ke každému koncovému bodu centra IoT. Oprávnění omezují přístup k centru IoT na základě funkcí.

Tento článek představuje:

* Různá oprávnění, která můžete udělit zařízení nebo back-endové aplikaci pro přístup k centru IoT.
* Proces ověřování a tokeny, které používá k ověření oprávnění.
* Jak obor pověření omezit přístup k určité prostředky.
* Podpora služby IoT Hub pro certifikáty X.509.
* Vlastní mechanismy ověřování zařízení, které používají existující registry identit zařízení nebo schémata ověřování.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Musíte mít příslušná oprávnění pro přístup k libovolné koncové body centra IoT Hub. Zařízení musí například obsahovat token obsahující pověření zabezpečení spolu s každou zprávou, kterou odešle do služby IoT Hub.

## <a name="access-control-and-permissions"></a>Řízení přístupu a oprávnění

[Oprávnění](#iot-hub-permissions) můžete udělit následujícími způsoby:

* **Zásady sdíleného přístupu na úrovni centra IoT**. Zásady sdíleného přístupu mohou udělit libovolnou kombinaci [oprávnění](#iot-hub-permissions). Zásady můžete definovat na [portálu Azure](https://portal.azure.com), programově pomocí [api REST prostředků služby IoT Hub](/rest/api/iothub/iothubresource)nebo pomocí cli zásad [az iot hub.](/cli/azure/iot/hub/policy?view=azure-cli-latest) Nově vytvořené centrum IoT hub má následující výchozí zásady:
  
  | Zásady sdíleného přístupu | Oprávnění |
  | -------------------- | ----------- |
  | iothubowner | Všechna oprávnění |
  | služba | **Oprávnění ServiceConnect** |
  | zařízení | **Oprávnění DeviceConnect** |
  | registryRead | **Oprávnění RegistryRead** |
  | registryReadWrite | **Oprávnění RegistryRead** a **RegistryWrite** |

* **Pověření zabezpečení pro zařízení**. Každé centrum IoT Hub obsahuje [registr identit](iot-hub-devguide-identity-registry.md) Pro každé zařízení v tomto registru identit můžete nakonfigurovat pověření zabezpečení, která udělují oprávnění **DeviceConnect** s rozsahem odpovídajících koncových bodů zařízení.

Například v typickém řešení IoT:

* Součást správy zařízení používá zásadu *registryReadWrite.*
* Komponenta procesoru událostí používá zásady *služby.*
* Součást obchodní logiky zařízení za běhu používá zásady *služby.*
* Jednotlivá zařízení se připojují pomocí přihlašovacích údajů uložených v registru identit služby IoT hub.

> [!NOTE]
> Podrobné informace naleznete v [tématu oprávnění.](#iot-hub-permissions)

## <a name="authentication"></a>Ověřování

Azure IoT Hub udělí přístup koncovým bodům, když ověří token podle zásad sdíleného přístupu a bezpečnostních přihlašovacích údajů v registru identit.

Pověření zabezpečení, jako jsou například symetrické klíče, nejsou nikdy odesílány po drátě.

> [!NOTE]
> Poskytovatel prostředků Centra Azure IoT Hub je zabezpečený prostřednictvím vašeho předplatného Azure, stejně jako všichni poskytovatelé ve [Správci prostředků Azure](../azure-resource-manager/management/overview.md).

Další informace o tom, jak vytvářet a používat tokeny zabezpečení, naleznete v [tématu tokeny zabezpečení centra IoT](iot-hub-devguide-security.md#security-tokens).

### <a name="protocol-specifics"></a>Specifika protokolu

Každý podporovaný protokol, například MQTT, AMQP a HTTPS, přenáší tokeny různými způsoby.

Při použití MQTT, connect paket má deviceId jako `{iothubhostname}/{deviceId}` ClientId, v poli Uživatelské jméno a token SAS v poli Heslo. `{iothubhostname}`by měl být úplný Název Ccentra IoT hubu (například contoso.azure-devices.net).

Při použití [protokolu AMQP](https://www.amqp.org/)podporuje služba IoT Hub [zabezpečení SASL PLAIN](https://tools.ietf.org/html/rfc4616) a [AMQP .](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc)

Pokud používáte zabezpečení založené na deklaracích identity AMQP, standard určuje způsob přenosu těchto tokenů.

Pro SASL PLAIN může být **uživatelské jméno:**

* `{policyName}@sas.root.{iothubName}`pokud používáte tokeny na úrovni centra IoT.
* `{deviceId}@sas.{iothubname}`pokud používáte tokeny s rozsahem zařízení.

V obou případech obsahuje pole heslo token, jak je popsáno v [tokenech zabezpečení centra IoT Hub](iot-hub-devguide-security.md#security-tokens).

Protokol HTTPS implementuje ověřování zahrnutím platného tokenu do hlavičky žádosti o **autorizaci.**

#### <a name="example"></a>Příklad

Uživatelské jméno (DeviceId rozlišuje malá a velká písmena):`iothubname.azure-devices.net/DeviceId`

Heslo (Můžete vygenerovat token SAS s příkazem rozšíření příkazu CLI [az iot hub generate-sas-token](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token)nebo [Nástroje Azure IoT pro kód Visual Studia):](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> Sady [Azure IoT SDK](iot-hub-devguide-sdks.md) automaticky generují tokeny při připojování ke službě. V některých případech sady Azure IoT SDK nepodporují všechny protokoly nebo všechny metody ověřování.

### <a name="special-considerations-for-sasl-plain"></a>Zvláštní aspekty pro SASL PLAIN

Při použití SASL PLAIN s AMQP, klient připojení k centru IoT můžete použít jeden token pro každé připojení TCP. Po vypršení platnosti tokenu připojení TCP odpojí od služby a aktivuje opětovné připojení. Toto chování, i když není problematické pro back-endovou aplikaci, je škodlivé pro aplikaci zařízení z následujících důvodů:

* Brány se obvykle připojují jménem mnoha zařízení. Při použití SASL PLAIN, musí vytvořit odlišné připojení TCP pro každé zařízení připojení k centru IoT. Tento scénář výrazně zvyšuje spotřebu energie a síťových prostředků a zvyšuje latenci každého připojení zařízení.

* Zařízení s omezenými prostředky jsou nepříznivě ovlivněna zvýšeným využíváním prostředků k opětovnému připojení po každém vypršení platnosti tokenu.

## <a name="scope-iot-hub-level-credentials"></a>Pověření na úrovni centra IoT oboru

Zásady zabezpečení na úrovni centra IoT můžete obor vytvořením tokenů s identifikátorem URI s omezeným přístupem. Například koncový bod pro odesílání zpráv typu device-cloud ze zařízení je **/devices/{deviceId}/messages/events**. Můžete také použít zásady sdíleného přístupu na úrovni služby IoT hub s oprávněními **DeviceConnect** k podepsání tokenu, jehož resourceURI je **/devices/{deviceId}**. Tento přístup vytvoří token, který je použitelný pouze pro odesílání zpráv jménem **deviceDeviceId**.

Tento mechanismus je podobný [zásadám vydavatele centra událostí](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab)a umožňuje implementovat vlastní metody ověřování.

## <a name="security-tokens"></a>Tokeny zabezpečení

Služba IoT Hub používá tokeny zabezpečení k ověřování zařízení a služeb, aby se zabránilo odesílání klíčů v síti. Tokeny zabezpečení jsou navíc omezeny časovou platností a rozsahem. [Sady Azure IoT SDK](iot-hub-devguide-sdks.md) automaticky generují tokeny bez nutnosti jakékoli speciální konfigurace. Některé scénáře vyžadují, abyste přímo vygenerovali a používali tokeny zabezpečení. Tyto scénáře zahrnují:

* Přímé použití povrchů MQTT, AMQP nebo HTTPS.

* Implementace vzoru služby tokenu, jak je vysvětleno v [ověřování vlastního zařízení](iot-hub-devguide-security.md#custom-device-and-module-authentication).

IoT Hub také umožňuje zařízení k ověření pomocí Služby IoT Hub pomocí [certifikátů X.509](iot-hub-devguide-security.md#supported-x509-certificates).

### <a name="security-token-structure"></a>Struktura tokenů zabezpečení

Tokeny zabezpečení slouží k udělení časově ohraničeného přístupu k zařízením a službám ke konkrétním funkcím v centru IoT Hub. Chcete-li získat autorizaci pro připojení k službě IoT Hub, musí zařízení a služby odesílat tokeny zabezpečení podepsané sdíleným přístupem nebo symetrickým klíčem. Tyto klíče jsou uloženy s identitou zařízení v registru identit.

Token podepsaný sdíleným přístupovým klíčem uděluje přístup ke všem funkcím přidruženým k oprávněním zásad sdíleného přístupu. Token podepsaný symetrickým klíčem identity zařízení uděluje oprávnění **DeviceConnect** pouze pro přidruženou identitu zařízení.

Token zabezpečení má následující formát:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Zde jsou očekávané hodnoty:

| Hodnota | Popis |
| --- | --- |
| {podpis} |Řetězec podpisu HMAC-SHA256 formuláře: `{URL-encoded-resourceURI} + "\n" + expiry`. **Důležité:** Klíč je dekódován z base64 a používá se jako klíč k provedení výpočtu HMAC-SHA256. |
| {resourceURI} |Uri předpona (podle segmentu) koncových bodů, které jsou přístupné pomocí tohoto tokenu, počínaje názvem hostitele centra IoT hub (bez protokolu). Například `myHub.azure-devices.net/devices/device1`. |
| {expirace} |Řetězce UTF8 pro počet sekund od epochy 00:00:00 UTC dne 1. |
| {URL kódovaný zdrojURI} |Malé url kódování identifikátoru URI |
| {policyName} |Název zásady sdíleného přístupu, na které odkazuje tento token. Chybí, pokud token odkazuje na pověření registru zařízení. |

**Poznámka k předponě**: Předpona IDENTIFIKÁTORURI je vypočítána podle segmentu a nikoli podle znaku. Například `/a/b` je předpona pro, `/a/b/c` ale ne pro `/a/bc`.

Následující fragment node.js zobrazuje funkci s názvem **generateSasToken,** která vypočítá `resourceUri, signingKey, policyName, expiresInMins`token ze vstupů . V dalších částech podrobně, jak inicializovat různé vstupy pro různé případy použití tokenu.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', Buffer.from(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
};
```

Pro srovnání, ekvivalentní kód Pythonu pro generování tokenu zabezpečení je:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import parse
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((parse.quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key.encode('utf-8'), sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + parse.urlencode(rawtoken)
```

Níže jsou uvedeny pokyny k instalaci pro požadavky.

[!INCLUDE [Iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]


Funkce v c# generovat token zabezpečení je:

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
> Vzhledem k tomu, že čas platnost tokenu je ověřena na počítačích IoT Hub, drift na hodiny počítače, který generuje token musí být minimální.

### <a name="use-sas-tokens-in-a-device-app"></a>Použití tokenů SAS v aplikaci pro zařízení

Existují dva způsoby, jak získat oprávnění **DeviceConnect** pomocí služby IoT Hub s tokeny zabezpečení: použijte [symetrický klíč zařízení z registru identit](#use-a-symmetric-key-in-the-identity-registry)nebo použijte sdílený [přístupový klíč](#use-a-shared-access-policy).

Mějte na paměti, že všechny funkce přístupné ze `/devices/{deviceId}`zařízení jsou vystaveny návrhu na koncových bodech s předponou .

> [!IMPORTANT]
> Jediný způsob, jak služba IoT Hub ověřuje konkrétní zařízení, je použití symetrického klíče identity zařízení. V případech, kdy se pro přístup k funkcím zařízení používá zásady sdíleného přístupu, musí řešení považovat komponentu vydávající token zabezpečení za důvěryhodnou dílčí součást.

Koncové body směřující k zařízení jsou (bez ohledu na protokol):

| Koncový bod | Funkce |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Odesílejte zprávy mezi zařízeními a cloudy. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Příjem zpráv z cloudu na zařízení. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Použití symetrického klíče v registru identit

Při použití symetrického klíče identity zařízení ke generování`skn`tokenu je vynechán prvek policyName ( tokenu.

Například token vytvořený pro přístup ke všem funkcím zařízení by měl mít následující parametry:

* identifikátor URI `{IoT hub name}.azure-devices.net/devices/{device id}`prostředků: ,
* podpisový klíč: jakýkoli symetrický klíč pro identitu, `{device id}`
* žádný název zásady,
* dobu expirace.

Příkladem použití předchozí funkce Node.js by bylo:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Výsledkem, který uděluje přístup ke všem funkcím pro device1, by bylo:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Je možné generovat token SAS s příkazem rozšíření příkazu CLI [az iot hub generate-sas-token](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token)nebo [Nástroje Azure IoT pro kód Visual Studio](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

### <a name="use-a-shared-access-policy"></a>Použití zásad sdíleného přístupu

Při vytváření tokenu ze zásady sdíleného přístupu nastavte `skn` pole na název zásady. Tato zásada musí udělit oprávnění **DeviceConnect.**

Dva hlavní scénáře pro použití zásad sdíleného přístupu pro přístup k funkcím zařízení jsou:

* [brány protokolu o cloudu](iot-hub-devguide-endpoints.md),
* [tokenové služby](iot-hub-devguide-security.md#custom-device-and-module-authentication) používané k implementaci vlastních schémat ověřování.

Vzhledem k tomu, že zásady sdíleného přístupu mohou potenciálně udělit přístup k připojení jako jakékoli zařízení, je důležité použít při vytváření tokenů zabezpečení správný identifikátor URI prostředků. Toto nastavení je důležité zejména pro služby tokenů, které mají obor token u konkrétního zařízení pomocí identifikátoru URI prostředku. Tento bod je méně relevantní pro brány protokolu, protože již zprostředkují provoz pro všechna zařízení.

Jako příklad by služba tokenů používající předem vytvořenou zásadu sdíleného přístupu nazvanou **zařízení** vytvořila token s následujícími parametry:

* identifikátor URI `{IoT hub name}.azure-devices.net/devices/{device id}`prostředků: ,
* podpisový klíč: jeden z `device` klíčů politiky,
* název zásady: `device`,
* dobu expirace.

Příkladem použití předchozí funkce Node.js by bylo:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Výsledkem, který uděluje přístup ke všem funkcím pro device1, by bylo:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Brána protokolu může použít stejný token pro všechna `myhub.azure-devices.net/devices`zařízení, která jednoduše nastaví identifikátor URI prostředku na .

### <a name="use-security-tokens-from-service-components"></a>Použití tokenů zabezpečení z komponent služby

Součásti služby mohou generovat tokeny zabezpečení pouze pomocí zásad sdíleného přístupu, které udělují příslušná oprávnění, jak bylo vysvětleno dříve.

Zde jsou funkce služby vystavené na koncových bodech:

| Koncový bod | Funkce |
| --- | --- |
| `{iot hub host name}/devices` |Vytvářejte, aktualizujte, načítajte a odstraňujte identity zařízení. |
| `{iot hub host name}/messages/events` |Příjem zpráv mezi zařízeními a cloudy. |
| `{iot hub host name}/servicebound/feedback` |Získejte zpětnou vazbu pro zprávy mezi cloudy. |
| `{iot hub host name}/devicebound` |Odesílejte zprávy z cloudu na zařízení. |

Jako příklad by služba generující pomocí předem vytvořené zásady sdíleného přístupu s názvem **registryRead** vytvořila token s následujícími parametry:

* identifikátor URI `{IoT hub name}.azure-devices.net/devices`prostředků: ,
* podpisový klíč: jeden z `registryRead` klíčů politiky,
* název zásady: `registryRead`,
* dobu expirace.

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Výsledkem, který by udělit přístup ke čtení všech identit zařízení, by bylo:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Podporované certifikáty X.509

K ověření zařízení pomocí služby IoT Hub můžete použít libovolný certifikát X.509 tak, že do služby Azure IoT Hub nahrajete kryptografický otisk certifikátu nebo certifikační autoritu (CA). Ověřování pomocí kryptografických otisků certifikátu ověří, zda prezentovaný kryptografický otisk odpovídá nakonfigurovanému kryptografickému otisku. Ověřování pomocí certifikační autority ověří řetěz certifikátů. V obou směrech tls handshake vyžaduje, aby zařízení mít platný certifikát a soukromý klíč. Podrobnosti naleznete ve specifikaci TLS, například: [RFC 5246 - Protokol TLS (Transport Layer Security) verze 1.2](https://tools.ietf.org/html/rfc5246/).

Mezi podporované certifikáty patří:

* **Existující certifikát X.509**. Zařízení již může mít certifikát X.509 přidružený. Zařízení můžete použít tento certifikát k ověření pomocí služby IoT Hub. Pracuje s kryptografickým otiskem nebo ověřováním certifikační autority. 

* **Certifikát X.509 podepsaný certifikační autoritou**. Chcete-li identifikovat zařízení a ověřit ho pomocí služby IoT Hub, můžete použít certifikát X.509 generovaný a podepsaný certifikačníautoritou (CA). Pracuje s kryptografickým otiskem nebo ověřováním certifikační autority.

* **Certifikát X-509 s vlastním generováním a vlastním podpisem**. Výrobce zařízení nebo interní nasazovač může tyto certifikáty vygenerovat a uložit do zařízení odpovídající soukromý klíč (a certifikát). Pro tento účel můžete použít nástroje, jako je [OpenSSL](https://www.openssl.org/) a [Windows SelfSignedCertificate.](/powershell/module/pkiclient/new-selfsignedcertificate) Funguje pouze s ověřováním kryptografickým otiskem. 

Zařízení může pro ověřování používat certifikát X.509 nebo token zabezpečení, ale ne obojí.

Další informace o ověřování pomocí certifikační autority naleznete v [tématu Device Authentication using X.509 CA Certificates](iot-hub-x509ca-overview.md).

### <a name="register-an-x509-certificate-for-a-device"></a>Registrace certifikátu X.509 pro zařízení

Sada [Azure IoT Service SDK for C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) (verze 1.0.8+) podporuje registraci zařízení, které používá certifikát X.509 pro ověřování. Další api, jako je například import nebo export zařízení, také podporují certifikáty X.509.

Ke konfiguraci certifikátů X.509 pro zařízení můžete také použít příkaz rozšíření rozhraní PŘÍKAZU rozhraní [příkazu az iot hub.](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)

### <a name="c-support"></a>C\# Podpora

Třída **RegistryManager** poskytuje programový způsob registrace zařízení. Zejména **AddDeviceAsync** a **UpdateDeviceAsync** metody umožňují zaregistrovat a aktualizovat zařízení v registru identit služby IoT Hub. Tyto dvě metody trvat **Device** instance jako vstup. Třída **Device** obsahuje vlastnost **Authentication,** která umožňuje zadat primární a sekundární kryptografické otisky certifikátu X.509. Kryptografický otisk představuje hash SHA256 certifikátu X.509 (uložený pomocí binárního kódování DER). Máte možnost zadat primární kryptografický otisk nebo sekundární kryptografický otisk nebo obojí. Primární a sekundární kryptografické otisky jsou podporovány pro zpracování scénářů přechodu certifikátu.

Zde je ukázkový fragment kódu C\# pro registraci zařízení pomocí kryptografického otisku certifikátu X.509:

```csharp
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "B4172AB44C28F3B9E117648C6F7294978A00CDCBA34A46A1B8588B3F7D82C4F1"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Použití certifikátu X.509 během operací za běhu

Sada [Azure IoT device SDK for .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device) (verze 1.0.11+) podporuje použití certifikátů X.509.

### <a name="c-support"></a>C\# Podpora

Třída **DeviceAuthenticationWithX509Certificate** podporuje vytváření instancí **DeviceClient** pomocí certifikátu X.509. Certifikát X.509 musí být ve formátu PFX (označovaný také jako PKCS #12), který obsahuje soukromý klíč.

Zde je ukázkový fragment kódu:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Vlastní ověřování zařízení a modulu

[Registr identit služby](iot-hub-devguide-identity-registry.md) IoT Hub můžete použít ke konfiguraci pověření zabezpečení pro zařízení nebo modul a řízení přístupu pomocí [tokenů](iot-hub-devguide-security.md#security-tokens). Pokud řešení IoT již má vlastní registr identit nebo schéma ověřování, zvažte vytvoření *služby tokenů* pro integraci této infrastruktury s službou IoT Hub. Tímto způsobem můžete ve svém řešení použít další funkce IoT.

Služba tokenů je vlastní cloudová služba. Používá *zásady sdíleného přístupu služby* IoT Hub s oprávněními **DeviceConnect** nebo **ModuleConnect** k vytváření *tokenů* *s rozsahem zařízení* nebo modulů. Tyto tokeny umožňují zařízení a modul pro připojení k centru IoT.

![Kroky vzoru služby tokenu](./media/iot-hub-devguide-security/tokenservice.png)

Zde jsou hlavní kroky vzoru služby token:

1. Vytvořte zásady sdíleného přístupu služby IoT Hub s oprávněními **DeviceConnect** nebo **ModuleConnect** pro vaše služby IoT hub. Tuto zásadu můžete vytvořit na [webu Azure Portal](https://portal.azure.com) nebo programově. Služba tokenů používá tuto zásadu k podepsání tokenů, které vytvoří.

2. Když zařízení nebo modul potřebuje přístup k centru IoT, požaduje podepsaný token ze služby tokenu. Zařízení se může ověřit pomocí vlastního registru identity/schématu ověřování a určit identitu zařízení nebo modulu, kterou služba tokenu používá k vytvoření tokenu.

3. Služba tokenu vrátí token. Token je vytvořen `/devices/{deviceId}` pomocí `/devices/{deviceId}/module/{moduleId}` `resourceURI`nebo `deviceId` jako , s jako `moduleId` zařízení, které je ověřeno nebo jako modul, který je ověřován. Služba tokenu používá zásady sdíleného přístupu k vytvoření tokenu.

4. Zařízení/modul používá token přímo s centrem IoT.

> [!NOTE]
> Třídu .NET [SharedAccessSignatureBuilder](https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx) nebo třídu Java [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) můžete použít k vytvoření tokenu ve službě tokenů.

Služba tokenu můžete nastavit vypršení platnosti tokenu podle potřeby. Když vyprší platnost tokenu, služba IoT hub přeruší připojení zařízení/modulu. Potom zařízení/modul musí požádat o nový token ze služby tokenu. Krátká doba vypršení platnosti zvyšuje zatížení zařízení nebo modulu i služby tokenů.

Aby se zařízení nebo modul mohl připojit k rozbočovači, musíte ho stále přidávat do registru identit služby IoT Hub , i když používá token a není klíčem k připojení. Proto můžete nadále používat řízení přístupu pro zařízení nebo modul povolením nebo zakázáním identit zařízení nebo modulu v [registru identit](iot-hub-devguide-identity-registry.md). Tento přístup zmírňuje rizika používání tokenů s dlouhou dobu vypršení platnosti.

### <a name="comparison-with-a-custom-gateway"></a>Porovnání s vlastní bránou

Vzor služby tokenu je doporučený způsob implementace vlastního registru identity/schématu ověřování pomocí služby IoT Hub. Tento vzor se doporučuje, protože služba IoT Hub nadále zpracovává většinu provozu řešení. Pokud je však vlastní schéma ověřování tak propojeno s protokolem, můžete ke zpracování veškerého provozu vyžadovat *vlastní bránu.* Příkladem takového scénáře je použití [zabezpečení transportní vrstvy (TLS) a předsdílené klíče (PSKs)](https://tools.ietf.org/html/rfc4279). Další informace naleznete v článku [brány protokolu.](iot-hub-protocol-gateway.md)

## <a name="reference-topics"></a>Referenční témata:

Následující referenční témata poskytují další informace o řízení přístupu k centru IoT hub.

## <a name="iot-hub-permissions"></a>Oprávnění centra IoT Hub

V následující tabulce jsou uvedena oprávnění, která můžete použít k řízení přístupu k centru IoT Hub.

| Oprávnění | Poznámky |
| --- | --- |
| **RegistryRead** |Uděluje přístup pro čtení do registru identit. Další informace naleznete v [registru identity](iot-hub-devguide-identity-registry.md). <br/>Toto oprávnění používají back-endové cloudové služby. |
| **RegistryReadWrite** |Uděluje přístup pro čtení a zápis do registru identit. Další informace naleznete v [registru identity](iot-hub-devguide-identity-registry.md). <br/>Toto oprávnění používají back-endové cloudové služby. |
| **ServiceConnect** |Uděluje přístup ke komunikaci cloudové služby a monitorování koncových bodů. <br/>Uděluje oprávnění k přijímání zpráv mezi zařízeními, odesílání zpráv z cloudu na zařízení a načítání odpovídajících potvrzení o doručení. <br/>Uděluje oprávnění k načtení potvrzení o doručení pro odeslání souborů. <br/>Uděluje oprávnění k přístupu dvojčat k aktualizaci značek a požadovaných vlastností, načtení ohlášených vlastností a spuštění dotazů. <br/>Toto oprávnění používají back-endové cloudové služby. |
| **Připojení zařízení** |Uděluje přístup ke koncovým bodům směřujícím k zařízení. <br/>Uděluje oprávnění k odesílání zpráv mezi zařízeními a přijímání zpráv z cloudu na zařízení. <br/>Uděluje oprávnění k nahrávání souborů ze zařízení. <br/>Uděluje oprávnění k přijímání oznámení o požadovaných vlastnostech dvojčete zařízení a aktualizace vlastností ohlášených dvojčete zařízení. <br/>Uděluje oprávnění k nahrávání souborů. <br/>Toto oprávnění používají zařízení. |

## <a name="additional-reference-material"></a>Doplňkový referenční materiál

Mezi další referenční témata v průvodci vývojáři služby IoT Hub patří:

* [Koncové body ioT hubu](iot-hub-devguide-endpoints.md) popisují různé koncové body, které každý ioT hub zveřejňuje pro operace za běhu a správy.

* [Omezení a kvóty](iot-hub-devguide-quotas-throttling.md) popisuje kvóty a omezení chování, které platí pro službu IoT Hub.

* [Sady SDK zařízení a služeb Azure IoT](iot-hub-devguide-sdks.md) uvádí různé sady SDK, které můžete použít při vývoji aplikací pro zařízení i služeb, které interagují s službou IoT Hub.

* [Dotazovací jazyk IoT Hub](iot-hub-devguide-query-language.md) popisuje dotazovací jazyk, který můžete použít k načtení informací z ioT hubu o dvojčatech a úlohách vašeho zařízení.

* [Podpora Služby IoT Hub MQTT](iot-hub-mqtt-support.md) poskytuje další informace o podpoře služby IoT Hub pro protokol MQTT.

* [RFC 5246 - Protokol TLS (Transport Layer Security) verze 1.2](https://tools.ietf.org/html/rfc5246/) poskytuje další informace o ověřování TLS.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili řídit přístup k IoT Hubu, vás můžou zajímat následující témata s průvodcem pro vývojáře služby IoT Hub:

* [Synchronizace stavu a konfigurací pomocí dvojčat zařízení](iot-hub-devguide-device-twins.md)
* [Vyvolání přímé metody na zařízení](iot-hub-devguide-direct-methods.md)
* [Plánování úloh na několika zařízeních](iot-hub-devguide-jobs.md)

Pokud byste chtěli vyzkoušet některé koncepty popsané v tomto článku, podívejte se na následující kurzy ioT Hub:

* [Začínáme s Azure IoT Hub](quickstart-send-telemetry-node.md)
* [Jak odesílat zprávy z cloudu na zařízení pomocí služby IoT Hub](iot-hub-csharp-csharp-c2d.md)
* [Jak zpracovat zprávy služby IoT Hub mezi zařízeními a cloudy](tutorial-routing.md)
