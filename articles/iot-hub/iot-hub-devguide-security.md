---
title: Vysvětlení zabezpečení Azure IoT Hub | Microsoft Docs
description: Příručka pro vývojáře – jak řídit přístup k IoT Hub pro aplikace zařízení a back-endové aplikace. Obsahuje informace o tokenech zabezpečení a podpoře certifikátů X. 509.
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
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Operations'
- devx-track-js
- devx-track-csharp
- devx-track-azurecli
ms.openlocfilehash: 8627681d843d15658882529424375486a4cdb1b9
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94845165"
---
# <a name="control-access-to-iot-hub"></a>Řízení přístupu k IoT Hubu

Tento článek popisuje možnosti zabezpečení služby IoT Hub. IoT Hub používá *oprávnění* k udělení přístupu ke každému koncovému bodu služby IoT Hub. Oprávnění omezují přístup ke službě IoT Hub na základě funkčnosti.

Tento článek obsahuje následující informace:

* Různá oprávnění, která můžete udělit k zařízení nebo back-endové aplikaci pro přístup ke službě IoT Hub.
* Proces ověřování a tokeny, které používá k ověření oprávnění.
* Jak nastavit obor pověření pro omezení přístupu ke konkrétním prostředkům.
* IoT Hub podporu pro certifikáty X. 509.
* Vlastní mechanismy ověřování zařízení, které používají existující Registry identity zařízení nebo schémata ověřování.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Musíte mít příslušná oprávnění pro přístup ke všem koncovým bodům IoT Hub. Zařízení například musí zahrnovat token obsahující zabezpečovací přihlašovací údaje spolu s každou zprávou, kterou pošle IoT Hub.

## <a name="access-control-and-permissions"></a>Řízení přístupu a oprávnění

[Oprávnění](#iot-hub-permissions) můžete udělit následujícími způsoby:

* **Zásady sdíleného přístupu na úrovni IoT Hub**. Zásady sdíleného přístupu můžou udělit libovolnou kombinaci [oprávnění](#iot-hub-permissions). Můžete definovat zásady v [Azure Portal](https://portal.azure.com)programově pomocí [rozhraní API REST IoT Hub prostředků](/rest/api/iothub/iothubresource)nebo pomocí rozhraní příkazového řádku [AZ IoT Hub Policy](/cli/azure/iot/hub/policy) . Nově vytvořené centrum IoT má následující výchozí zásady:
  
  | Zásady sdíleného přístupu | Oprávnění |
  | -------------------- | ----------- |
  | iothubowner | Všechna oprávnění |
  | service | **ServiceConnect** oprávnění |
  | zařízení | **DeviceConnect** oprávnění |
  | registryRead | **RegistryRead** oprávnění |
  | registryReadWrite | **RegistryRead** a **RegistryWrite** oprávnění |

* **Zabezpečovací přihlašovací údaje pro jednotlivá zařízení**. Každý IoT Hub obsahuje [registr identit](iot-hub-devguide-identity-registry.md) pro každé zařízení v tomto registru identity, můžete nakonfigurovat přihlašovací údaje zabezpečení, které udělí **DeviceConnect** oprávnění vymezená na odpovídající koncové body zařízení.

Například v typickém řešení IoT:

* Komponenta správy zařízení používá zásady *registryReadWrite* .
* Komponenta procesoru událostí používá zásady *služby* .
* Komponenta Business Logic pro zařízení runtime používá zásady *služby* .
* Jednotlivá zařízení se připojují pomocí přihlašovacích údajů uložených v registru identit ve službě IoT Hub.

> [!NOTE]
> Podrobné informace najdete v tématu [oprávnění](#iot-hub-permissions) .

## <a name="authentication"></a>Ověřování

Azure IoT Hub udělí přístup koncovým bodům, když ověří token podle zásad sdíleného přístupu a bezpečnostních přihlašovacích údajů v registru identit.

Přihlašovací údaje zabezpečení, jako jsou například symetrické klíče, se nikdy neodesílají přes síťový kabel.

> [!NOTE]
> Poskytovatel prostředků Azure IoT Hub je zabezpečený prostřednictvím vašeho předplatného Azure, stejně jako všichni poskytovatelé v [Azure Resource Manager](../azure-resource-manager/management/overview.md).

Další informace o tom, jak vytvářet a používat tokeny zabezpečení, najdete v tématu [IoT Hub tokeny zabezpečení](iot-hub-devguide-security.md#security-tokens).

### <a name="protocol-specifics"></a>Konkrétní protokol

Každý podporovaný protokol, například MQTT, AMQP a HTTPS, přesměruje tokeny různými způsoby.

Při použití MQTT má paket CONNECT deviceId jako ClientId, `{iothubhostname}/{deviceId}` v poli UserName (uživatelské jméno) a token SAS v poli heslo. `{iothubhostname}` mělo by se jednat o úplný záznam CName služby IoT Hub (například contoso.azure-devices.net).

Při použití [AMQP](https://www.amqp.org/)podporuje IoT Hub [SASL Plain](https://tools.ietf.org/html/rfc4616) a [AMQP a zabezpečení založené na deklaracích identity](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc).

Pokud používáte AMQP zabezpečení založené na deklaracích identity, Standard určuje, jak se mají tyto tokeny přenést.

Pro SASL PLAIN **uživatelské jméno** může být:

* `{policyName}@sas.root.{iothubName}` Pokud používáte tokeny na úrovni IoT Hub.
* `{deviceId}@sas.{iothubname}` Pokud používáte tokeny v oboru zařízení.

V obou případech pole heslo obsahuje token, jak je popsáno v [IoT Hub tokeny zabezpečení](iot-hub-devguide-security.md#security-tokens).

Protokol HTTPS implementuje ověřování zahrnutím platného tokenu do hlavičky **autorizační** žádosti.

#### <a name="example"></a>Příklad

Username (DeviceId rozlišuje velká a malá písmena): `iothubname.azure-devices.net/DeviceId`

Heslo (můžete vygenerovat token SAS pomocí příkazu CLI Extension. [AZ IoT Hub Generate-SAS-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token)nebo [Azure iot Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)):

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> Sady [SDK Azure IoT](iot-hub-devguide-sdks.md) automaticky generují tokeny při připojování ke službě. V některých případech sady SDK Azure IoT nepodporují všechny protokoly ani metody ověřování.

### <a name="special-considerations-for-sasl-plain"></a>Zvláštní důležité důvody pro SASL PLAIN

Při použití SASL PLAIN s AMQP může klient připojující se ke službě IoT Hub použít pro každé připojení TCP jeden token. Po vypršení platnosti tokenu se připojení TCP odpojí od služby a aktivuje se opětovné připojení. Toto chování, ale ne problematické pro back-endové aplikace, je škodlivé pro aplikace zařízení z následujících důvodů:

* Brány se obvykle připojují za mnoho zařízení. Při použití SASL PLAIN musí vytvořit samostatné připojení TCP pro každé zařízení připojující se ke službě IoT Hub. Tento scénář významně zvyšuje spotřebu napájení a síťových prostředků a zvyšuje latenci každého připojení zařízení.

* U zařízení, která jsou omezená na prostředky, se zvýší využití prostředků, které se po vypršení platnosti tokenu znovu připojí.

## <a name="scope-iot-hub-level-credentials"></a>Rozsah přihlašovacích údajů na úrovni IoT Hub

Vytvářením tokenů s omezeným identifikátorem URI prostředků můžete nastavit rozsah zásad zabezpečení na úrovni centra IoT. Například koncový bod pro posílání zpráv typu zařízení-Cloud ze zařízení je **/Devices/{deviceId}/Messages/Events**. Zásady sdíleného přístupu na úrovni služby IoT Hub můžete použít také k podepsání tokenu, jehož resourceURI **DeviceConnect** je **/Devices/{deviceId}**. Tento přístup vytvoří token, který je možné použít jenom pro posílání zpráv jménem zařízení **deviceId**.

Tento mechanismus je podobný [zásadě Event Hubs vydavateli](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab)a umožňuje implementovat vlastní metody ověřování.

## <a name="security-tokens"></a>Tokeny zabezpečení

IoT Hub používá k ověřování zařízení a služeb tokeny zabezpečení, aby nedocházelo k posílání klíčů na lince. Tokeny zabezpečení jsou navíc omezené v době platnosti a rozsahu. Sady [SDK Azure IoT](iot-hub-devguide-sdks.md) automaticky generují tokeny bez nutnosti jakékoli speciální konfigurace. Některé scénáře vyžadují, abyste přímo vygenerovali a použili tokeny zabezpečení. Mezi takové scénáře patří:

* Přímé použití ploch MQTT, AMQP nebo HTTPS.

* Implementace vzoru služby tokenu, jak je vysvětleno v tématu [vlastní ověřování zařízení](iot-hub-devguide-security.md#custom-device-and-module-authentication).

IoT Hub taky umožňuje zařízením ověřování pomocí [certifikátů X. 509](iot-hub-devguide-security.md#supported-x509-certificates)pomocí IoT Hub.

### <a name="security-token-structure"></a>Struktura tokenu zabezpečení

Tokeny zabezpečení slouží k udělení časově vázaného přístupu k zařízením a službám k určitým funkcím v IoT Hub. Chcete-li získat autorizaci pro připojení k IoT Hub, musí zařízení a služby odesílat tokeny zabezpečení podepsané buď pomocí sdíleného přístupu, nebo symetrického klíče. Tyto klíče jsou uložené s identitou zařízení v registru identit.

Token podepsaný pomocí sdíleného přístupového klíče uděluje přístup ke všem funkcím přidruženým k oprávněním zásad sdíleného přístupu. Token podepsaný symetrickým klíčem identity zařízení uděluje pouze oprávnění **DeviceConnect** pro přidruženou identitu zařízení.

Token zabezpečení má následující formát:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Tady jsou očekávané hodnoty:

| Hodnota | Popis |
| --- | --- |
| označení |Řetězec pro podpis HMAC-SHA256 ve formátu: `{URL-encoded-resourceURI} + "\n" + expiry` . **Důležité**: klíč se dekóduje z formátu Base64 a používá se jako klíč k provedení výpočtu HMAC-SHA256. |
| resourceUri |Předpona URI (podle segmentu) koncových bodů, ke kterým se dá dostat s tímto tokenem, počínaje názvem hostitele centra IoT (bez protokolu). Například `myHub.azure-devices.net/devices/device1`. |
| vypršení platnosti |Řetězce UTF8 po dobu v sekundách od epocha 00:00:00 UTC dne 1. ledna 1970. |
| {URL-Encoded-resourceURI} |Malá adresa URL – kódování identifikátoru URI pro malý případ prostředku |
| PolicyName |Název zásad sdíleného přístupu, na který tento token odkazuje Chybí, pokud token odkazuje na přihlašovací údaje registru zařízení. |

**Poznámka k předponě**: PŘEDPONa identifikátoru URI je vypočítána segmentem a nikoli znakem. Například `/a/b` je prefix pro `/a/b/c` , ale ne pro `/a/bc` .

Následující fragment Node.js ukazuje funkci nazvanou **generateSasToken** , která vypočítá token ze vstupů `resourceUri, signingKey, policyName, expiresInMins` . Další části podrobně popisují, jak inicializovat různé vstupy pro různé případy použití tokenu.

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

Jako srovnání je ekvivalentní kód Pythonu pro vygenerování tokenu zabezpečení:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import parse
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((parse.quote_plus(uri)), int(ttl))
    print(sign_key)
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

Funkce v jazyce C# pro vygenerování tokenu zabezpečení je:

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

Pro Java:
```java
    public static String generateSasToken(String resourceUri, String key) throws Exception {
        // Token will expire in one hour
        var expiry = Instant.now().getEpochSecond() + 3600;

        String stringToSign = URLEncoder.encode(resourceUri, StandardCharsets.UTF_8) + "\n" + expiry;
        byte[] decodedKey = Base64.getDecoder().decode(key);

        Mac sha256HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secretKey = new SecretKeySpec(decodedKey, "HmacSHA256");
        sha256HMAC.init(secretKey);
        Base64.Encoder encoder = Base64.getEncoder();

        String signature = new String(encoder.encode(
            sha256HMAC.doFinal(stringToSign.getBytes(StandardCharsets.UTF_8))), StandardCharsets.UTF_8);

        String token = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, StandardCharsets.UTF_8)
                + "&sig=" + URLEncoder.encode(signature, StandardCharsets.UTF_8.name()) + "&se=" + expiry;
            
        return token;
    }
```


> [!NOTE]
> Vzhledem k tomu, že je doba platnosti tokenu ověřená na IoT Hubch počítačích, musí být posun na hodinách počítače, který generuje token, minimální.

### <a name="use-sas-tokens-in-a-device-app"></a>Použití tokenů SAS v aplikaci zařízení

Existují dva způsoby, jak pomocí IoT Hub s tokeny zabezpečení získat oprávnění **DeviceConnect** : použijte [symetrický klíč zařízení z registru identity](#use-a-symmetric-key-in-the-identity-registry)nebo použijte [sdílený přístupový klíč](#use-a-shared-access-policy).

Mějte na paměti, že všechny funkce dostupné ze zařízení jsou zpřístupněné návrhem u koncových bodů s předponou `/devices/{deviceId}` .

> [!IMPORTANT]
> Jediný způsob, jak IoT Hub ověřit konkrétní zařízení, používá symetrický klíč identity zařízení. V případech, kdy se k přístupu k funkcím zařízení používá zásada sdíleného přístupu, musí řešení brát v úvahu součást, která vydává token zabezpečení jako důvěryhodnou podsoučást.

Koncové body směřující k zařízení jsou (bez ohledu na protokol):

| Koncový bod | Funkce |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Posílání zpráv ze zařízení do cloudu. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Příjem zpráv z cloudu do zařízení. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Použití symetrického klíče v registru identit

Pokud k vygenerování tokenu použijete symetrický klíč identity zařízení, bude element Policy ( `skn` ) tohoto tokenu vynechán.

Například token vytvořený pro přístup ke všem funkcím zařízení by měl mít následující parametry:

* identifikátor URI prostředku: `{IoT hub name}.azure-devices.net/devices/{device id}` ,
* podpisový klíč: libovolný symetrický klíč pro `{device id}` identitu,
* žádný název zásad,
* čas vypršení platnosti.

Příkladem použití předchozí funkce Node.js by byl:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Výsledek, který uděluje přístup ke všem funkcím pro zařízení1, by byl:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Je možné vygenerovat token SAS pomocí příkazu CLI rozšíření [AZ IoT Hub Generate-SAS-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token)nebo [Azure iot Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

### <a name="use-a-shared-access-policy"></a>Použití zásad sdíleného přístupu

Při vytváření tokenu ze zásad sdíleného přístupu nastavte `skn` pole na název zásady. Tato zásada musí udělit oprávnění **DeviceConnect** .

Existují dva hlavní scénáře použití zásad sdíleného přístupu pro přístup k funkcím zařízení:

* [brány cloudového protokolu](iot-hub-devguide-endpoints.md),
* [služby tokenů](iot-hub-devguide-security.md#custom-device-and-module-authentication) používané k implementaci vlastních schémat ověřování.

Vzhledem k tomu, že zásady sdíleného přístupu můžou potenciálně udělit přístup pro připojení k libovolnému zařízení, je důležité při vytváření tokenů zabezpečení použít správný identifikátor URI prostředku. Toto nastavení je zvláště důležité pro služby tokenů, které musí být v oboru tokenu na konkrétní zařízení pomocí identifikátoru URI prostředku. Tento bod je míň důležitý pro brány protokolu, protože už Mediating provoz pro všechna zařízení.

Například služba tokenů pomocí předem vytvořených zásad sdíleného přístupu označovaného jako **zařízení** vytvoří token s následujícími parametry:

* identifikátor URI prostředku: `{IoT hub name}.azure-devices.net/devices/{device id}` ,
* podpisový klíč: jeden z klíčů `device` zásady,
* Název zásady: `device` ,
* čas vypršení platnosti.

Příkladem použití předchozí funkce Node.js by byl:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Výsledek, který uděluje přístup ke všem funkcím pro zařízení1, by byl:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Brána protokolu může použít stejný token pro všechna zařízení pouhým nastavením identifikátoru URI prostředku na `myhub.azure-devices.net/devices` .

### <a name="use-security-tokens-from-service-components"></a>Použití tokenů zabezpečení z komponent služby

Součásti služby mohou generovat pouze tokeny zabezpečení pomocí zásad sdíleného přístupu udělujících příslušná oprávnění, která jsou vysvětlena dříve.

Tady jsou funkce služby vystavené na koncových bodech:

| Koncový bod | Funkce |
| --- | --- |
| `{iot hub host name}/devices` |Vytváření, aktualizace, načítání a odstraňování identit zařízení. |
| `{iot hub host name}/messages/events` |Příjem zpráv ze zařízení do cloudu. |
| `{iot hub host name}/servicebound/feedback` |Dostanou zpětnou vazbu na zprávy z cloudu na zařízení. |
| `{iot hub host name}/devicebound` |Posílání zpráv z cloudu na zařízení. |

Například služba, která vygenerovala pomocí předem vytvořených zásad sdíleného přístupu s názvem **registryRead** , vytvoří token s následujícími parametry:

* identifikátor URI prostředku: `{IoT hub name}.azure-devices.net/devices` ,
* podpisový klíč: jeden z klíčů `registryRead` zásady,
* Název zásady: `registryRead` ,
* čas vypršení platnosti.

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Výsledek, který by udělil přístup pro čtení všech identit zařízení, by byl:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Podporované certifikáty X. 509

K ověření zařízení s IoT Hub můžete použít libovolný certifikát X. 509 tak, že nahrajete kryptografický otisk certifikátu nebo certifikační autoritu (CA) do Azure IoT Hub. Ověřování pomocí kryptografických otisků certifikátů ověřuje, že zobrazený kryptografický otisk odpovídá nakonfigurovanému kryptografickému otisku. Ověřování pomocí certifikační autority ověří řetěz certifikátů. V obou případech musí TLS handshake vyžadovat, aby zařízení mělo platný certifikát a soukromý klíč. Podrobnosti najdete v tématu Specifikace TLS, například: [RFC 5246 – protokol TLS (Transport Layer Security) verze 1,2](https://tools.ietf.org/html/rfc5246/).

Mezi podporované certifikáty patří:

* **Existující certifikát X. 509**. K zařízení již může být přidružen certifikát X. 509. Zařízení může tento certifikát použít k ověření pomocí IoT Hub. Funguje buď s kryptografickým otiskem, nebo s ověřováním CA. 

* **Certifikát X. 509 podepsaný certifikační autoritou**. Pokud chcete identifikovat zařízení a ověřit ho pomocí IoT Hub, můžete použít certifikát X. 509 generovaný a podepsaný certifikační autoritou (CA). Funguje buď s kryptografickým otiskem, nebo s ověřováním CA.

* **Samostatně generovaný certifikát X-509 podepsaný svým držitelem**. Výrobce zařízení nebo interní nástroj pro nasazení může tyto certifikáty vygenerovat a uložit odpovídající privátní klíč (a certifikát) na zařízení. K tomuto účelu můžete použít nástroje, jako je [OpenSSL](https://www.openssl.org/) a nástroj [Windows SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) Utility. Funguje pouze s ověřováním pomocí kryptografického otisku.

Zařízení může buď použít certifikát X. 509, nebo token zabezpečení pro ověřování, ale ne obojí. Při ověřování pomocí certifikátu X. 509 se ujistěte, že máte zavedenou strategii pro zpracování změny certifikátu, když vyprší platnost existujícího certifikátu.

Pro zařízení, která používají ověřování CA X. 509, se nepodporuje následující funkce:

* Protokol HTTPS, MQTT přes objekty WebSockets a AMQP přes protokoly WebSockets.
* Nahrávání souborů (všechny protokoly).

Další informace o ověřování pomocí certifikační autority najdete v tématu [ověřování zařízení pomocí certifikátů certifikační autority X. 509](iot-hub-x509ca-overview.md). Informace o tom, jak nahrát a ověřit certifikační autoritu ve službě IoT Hub, najdete v tématu [nastavení zabezpečení X. 509 ve službě Azure IoT Hub](iot-hub-security-x509-get-started.md).

### <a name="register-an-x509-certificate-for-a-device"></a>Registrace certifikátu X. 509 pro zařízení

[Sada Azure IoT Service SDK pro C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) (verze 1.0.8 +) podporuje registraci zařízení, které pro ověřování používá certifikát X. 509. Další rozhraní API, jako je například import a export zařízení, podporují také certifikáty X. 509.

Můžete také použít příkaz rozšíření CLI [AZ IoT Hub Device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) ke konfiguraci certifikátů X. 509 pro zařízení.

### <a name="c-support"></a>Podpora jazyka C \#

Třída **RegistryManager** poskytuje programový způsob registrace zařízení. Konkrétně metody **AddDeviceAsync** a **UpdateDeviceAsync** umožňují registraci a aktualizaci zařízení v registru IoT Hub identity. Tyto dvě metody jako vstup přebírají instanci **zařízení** . Třída **zařízení** zahrnuje vlastnost **ověřování** , která umožňuje určit primární a sekundární kryptografické otisky certifikátů X. 509. Kryptografický otisk představuje SHA256 hodnotu hash certifikátu X. 509 (uložený pomocí binárního kódování DER). Máte možnost určit primární kryptografický otisk nebo sekundární kryptografický otisk nebo obojí. U primárních a sekundárních kryptografických otisků se podporuje zpracování scénářů přechodu certifikátů.

Tady je ukázkový \# fragment kódu jazyka C k registraci zařízení pomocí kryptografického otisku certifikátu X. 509:

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

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Použití certifikátu X. 509 během operací run-time

[Sada SDK pro zařízení Azure IoT pro .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device) (verze 1.0.11 +) podporuje použití certifikátů X. 509.

### <a name="c-support"></a>Podpora jazyka C \#

Třída **DeviceAuthenticationWithX509Certificate** podporuje vytváření instancí **DeviceClient** pomocí certifikátu X. 509. Certifikát X. 509 musí být ve formátu PFX (označovaném také jako PKCS #12), který obsahuje privátní klíč.

Zde je ukázka fragmentu kódu:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Vlastní ověřování zařízení a modulů

IoT Hub [registru identit](iot-hub-devguide-identity-registry.md) můžete použít ke konfiguraci přihlašovacích údajů zabezpečení podle zařízení/modulu a přístupového řízení přístupu pomocí [tokenů](iot-hub-devguide-security.md#security-tokens). Pokud řešení IoT již obsahuje vlastní registr identit nebo schéma ověřování, zvažte vytvoření *služby tokenů* pro integraci této infrastruktury s IoT Hub. Tímto způsobem můžete ve vašem řešení použít další funkce IoT.

Služba tokenů je vlastní cloudová služba. Používá *zásady sdíleného přístupu* IoT Hub s oprávněním **DeviceConnect** nebo **ModuleConnect** k vytváření tokenů v *oboru zařízení* nebo *modulu* . Tyto tokeny umožňují zařízení a modul připojit se ke službě IoT Hub.

![Kroky vzoru služby tokenu](./media/iot-hub-devguide-security/tokenservice.png)

Tady jsou hlavní kroky vzoru služby tokenu:

1. Vytvořte zásadu sdíleného přístupu IoT Hub s oprávněním **DeviceConnect** nebo **ModuleConnect** pro Centrum IoT. Tuto zásadu můžete vytvořit v [Azure Portal](https://portal.azure.com) nebo programově. Služba tokenů používá tuto zásadu k podepsání tokenů, které vytvoří.

2. Když zařízení nebo modul potřebuje přístup k centru IoT, vyžádá si podepsaný token ze služby tokenů. Zařízení se může ověřit pomocí vlastního schématu registru nebo ověřování identity, aby bylo možné zjistit identitu zařízení/modulu, kterou služba tokenů používá k vytvoření tokenu.

3. Služba tokenů vrací token. Token se vytvoří pomocí `/devices/{deviceId}` nebo `/devices/{deviceId}/module/{moduleId}` jako `resourceURI` s `deviceId` ověřováním zařízení nebo jako modul, který se `moduleId` ověřuje. Služba tokenů používá zásady sdíleného přístupu k vytvoření tokenu.

4. Zařízení/modul používá token přímo ve službě IoT Hub.

> [!NOTE]
> K vytvoření tokenu ve službě tokenů můžete použít třídu .NET [SharedAccessSignatureBuilder](/dotnet/api/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder) nebo [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) třídy Java.

Služba tokenů může nastavit vypršení platnosti tokenu podle potřeby. Po vypršení platnosti tokenu Server IoT Hub naruší připojení zařízení nebo modulu. Zařízení/modul pak musí od služby tokenu požádat o nový token. Krátká doba vypršení platnosti zvyšuje zatížení zařízení i modulu i služby tokenů.

Aby se zařízení nebo modul připojoval k vašemu rozbočovači, musíte ho dál přidat do registru IoT Hub identity, i když používá token, a ne klíč pro připojení. Díky tomu můžete v [registru identit](iot-hub-devguide-identity-registry.md)povolit nebo zakázat identity zařízení/modulu i nadále pomocí řízení přístupu pro jednotlivé zařízení/moduly. Tento přístup snižuje riziko používání tokenů s dlouhou dobou vypršení platnosti.

### <a name="comparison-with-a-custom-gateway"></a>Porovnání s vlastní bránou

Vzor služby tokenu je doporučeným způsobem implementace vlastního schématu registru nebo ověřování identity pomocí IoT Hub. Tento vzor je doporučený, protože IoT Hub nadále zpracovává většinu přenosů řešení. Pokud je však vlastní schéma ověřování propojeno s protokolem, můžete vyžadovat *vlastní bránu* pro zpracování všech přenosů. Příkladem takového scénáře je použití protokolu [TLS (Transport Layer Security) a předem sdílených klíčů (PSKs)](https://tools.ietf.org/html/rfc4279). Další informace najdete v článku o [bráně protokolu](iot-hub-protocol-gateway.md) .

## <a name="reference-topics"></a>Referenční témata:

Následující referenční témata vám poskytnou další informace o řízení přístupu ke službě IoT Hub.

## <a name="iot-hub-permissions"></a>IoT Hub oprávnění

Následující tabulka uvádí oprávnění, která můžete použít k řízení přístupu ke službě IoT Hub.

| Oprávnění | Poznámky |
| --- | --- |
| **RegistryRead** |Udělí přístup pro čtení k registru identit. Další informace najdete v části [registr identit](iot-hub-devguide-identity-registry.md). <br/>Toto oprávnění využívají back-endové cloudové služby. |
| **RegistryReadWrite** |Udělí registru identity přístup pro čtení a zápis. Další informace najdete v části [registr identit](iot-hub-devguide-identity-registry.md). <br/>Toto oprávnění využívají back-endové cloudové služby. |
| **ServiceConnect** |Uděluje přístup k koncovým bodům pro komunikaci a monitorování cloudových služeb. <br/>Uděluje oprávnění přijímat zprávy ze zařízení do cloudu, odesílat zprávy z cloudu na zařízení a načítat odpovídající potvrzení o doručení. <br/>Uděluje oprávnění k načtení potvrzení o doručení pro nahrávání souborů. <br/>Udělí oprávnění pro přístup k nevlákenám k aktualizaci značek a požadovaných vlastností, načtení hlášených vlastností a spuštění dotazů. <br/>Toto oprávnění využívají back-endové cloudové služby. |
| **DeviceConnect** |Udělí přístup k koncovým bodům orientovaným na zařízení. <br/>Uděluje oprávnění odesílat zprávy ze zařízení do cloudu a přijímat zprávy z cloudu na zařízení. <br/>Uděluje oprávnění k provádění nahrávání souborů ze zařízení. <br/>Uděluje oprávnění přijímat oznámení o vyplňování požadovaných vlastností zařízení a aktualizovat nahlášené vlastnosti zařízení. <br/>Uděluje oprávnění k provádění nahrávání souborů. <br/>Tato oprávnění používají zařízení. |

## <a name="additional-reference-material"></a>Další referenční materiály

Další referenční témata v IoT Hub příručce pro vývojáře zahrnují:

* [IoT Hub koncové body](iot-hub-devguide-endpoints.md) popisují různé koncové body, které jednotlivé služby IoT Hub zpřístupňují pro operace run-time a Management.

* [Omezení a kvóty](iot-hub-devguide-quotas-throttling.md) popisují chování kvót a omezení, která se vztahují na službu IoT Hub.

* Sady [SDK pro zařízení a služby Azure IoT](iot-hub-devguide-sdks.md) obsahují různé jazykové sady SDK, které můžete použít při vývoji aplikací pro zařízení i služby, které komunikují s IoT Hub.

* Dotazovací jazyk [IoT Hub](iot-hub-devguide-query-language.md) popisuje dotazovací jazyk, který můžete použít k načtení informací z IoT Hub o nečinnosti zařízení a úlohách.

* [Podpora IoT Hub MQTT](iot-hub-mqtt-support.md) poskytuje další informace o podpoře IoT Hub pro protokol MQTT.

* [RFC 5246 – protokol TLS (Transport Layer Security) verze 1,2](https://tools.ietf.org/html/rfc5246/) poskytuje další informace o ověřování TLS.

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s řízením přístupu IoT Hub, může vás zajímat následující témata IoT Hub příručka pro vývojáře:

* [Pro synchronizaci stavu a konfigurací použít vlákna zařízení](iot-hub-devguide-device-twins.md)
* [Vyvolání přímé metody v zařízení](iot-hub-devguide-direct-methods.md)
* [Plánování úloh na několika zařízeních](iot-hub-devguide-jobs.md)

Pokud si přejete vyzkoušet některé z konceptů popsaných v tomto článku, přečtěte si následující IoT Hub kurzy:

* [Začínáme s Azure IoT Hub](quickstart-send-telemetry-node.md)
* [Postup posílání zpráv z cloudu na zařízení pomocí IoT Hub](iot-hub-csharp-csharp-c2d.md)
* [Jak zpracovávat IoT Hub zpráv ze zařízení do cloudu](tutorial-routing.md)
