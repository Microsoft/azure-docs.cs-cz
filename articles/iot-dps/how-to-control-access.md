---
title: Zabezpečení koncových bodů v IoT Device Provisioning Service | Dokumentace Microsoftu
description: Principy – řízení přístupu k IoT Device Provisioning Service pro back endové aplikace. Obsahuje informace o tokeny zabezpečení.
author: wesmc7777
manager: timlt
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: wesmc
ms.openlocfilehash: b4776ef3589d994fff692e450d252c491c20f7b2
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522862"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Řízení přístupu k Azure IoT Hub Device Provisioning Service

Tento článek popisuje možnosti pro zabezpečení služby zřizování zařízení IoT. Zřizovací služba používá *oprávnění* udělit přístup pro každý koncový bod. Oprávnění omezují přístup k instanci služby závislosti na funkcích.

Tento článek popisuje:

* Jiná oprávnění, abyste udělili do back endové aplikace pro přístup k vaší službě zřizování.
* V procesu ověřování a tokenů použije k ověření oprávnění.

### <a name="when-to-use"></a>Kdy je použít

Musí mít příslušná oprávnění pro přístup k jakémukoli zřizování koncových bodů služby. Back endové aplikace musí obsahovat třeba token obsahující zabezpečovací přihlašovací údaje spolu s každou zprávu, kterou odesílá do služby.

## <a name="access-control-and-permissions"></a>Řízení přístupu a oprávnění

Můžete udělit [oprávnění](#device-provisioning-service-permissions) následujícími způsoby:

* **Sdílené zásady přístupu autorizace**. Zásady sdíleného přístupu udělit libovolnou kombinaci [oprávnění](#device-provisioning-service-permissions). Můžete definovat zásady v [webu Azure portal][lnk-management-portal], nebo prostřednictvím kódu programu pomocí [zařízení zřizování služby REST API][lnk-resource-provider-apis]. Nově vytvořenou službu zřizování má následující výchozí zásady:

  * **provisioningserviceowner**: zásada se všechna oprávnění.

> [!NOTE]
> Zobrazit [oprávnění](#device-provisioning-service-permissions) podrobné informace.

## <a name="authentication"></a>Authentication

Azure IoT Hub Device Provisioning Service uděluje přístup ke koncovým bodům pomocí ověření tokenu proti zásady sdíleného přístupu. Zabezpečovací přihlašovací údaje, jako jsou například symetrického klíče, se nikdy neodesílá přenosu.

> [!NOTE]
> Poskytovatele prostředků služby Device Provisioning zabezpečuje svým předplatným Azure, jako jsou všichni poskytovatelé v [Azure Resource Manageru][lnk-azure-resource-manager].

Další informace o tom, jak vytvořit a použít tokeny zabezpečení najdete v další části.

Je jediný podporovaný protokol HTTP a implementuje ověřování zahrnutím platný token v **autorizace** hlavičky žádosti.

#### <a name="example"></a>Příklad:
`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`

> [!NOTE]
> [Azure zřizování služby SDK pro zařízení IoT] [ lnk-sdks] automaticky generovat tokeny při připojování ke službě.

## <a name="security-tokens"></a>Tokeny zabezpečení
Služby Device Provisioning Service používá k ověření služby se odesílání klíče na lince tokeny zabezpečení. Kromě toho mají omezenou dobu platnosti a rozsahu tokenů zabezpečení. [Azure IoT zřizování služby sady SDK pro zařízení] [ lnk-sdks] automaticky generovat tokeny nevyžaduje žádnou zvláštní konfiguraci. Některé scénáře vyžadují vytvoření a použití tokenů zabezpečení přímo. Tyto scénáře zahrnují přímému použití povrchu HTTP.

### <a name="security-token-structure"></a>Struktura tokenu zabezpečení

Použití tokenů zabezpečení pro udělení přístupu časově služby k určité funkce IoT Device Provisioning Service. Získat autorizaci k připojení ke službě zřizování, musíte odeslat služby tokenů zabezpečení, které jsou podepsané pomocí sdíleného přístupového nebo symetrický klíč.

Token podepsán sdíleného přístupového klíče uděluje přístup ke všem funkcím, které jsou přidružené k oprávnění zásad sdíleného přístupu. 

Token zabezpečení má následující formát:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Tady jsou očekávané hodnoty:

| Hodnota | Popis |
| --- | --- |
| {podpis} |Řetězec podpisu HMAC SHA256 formuláře: `{URL-encoded-resourceURI} + "\n" + expiry`. **Důležité**: klíč je dekódovat z formátu base64 a použít jako klíč pro provádění výpočtů HMAC SHA256.|
| {expiry} |Řetězce UTF8 pro počet sekund od 00:00:00 UTC epocha na 1. ledna 1970. |
| {Adresu URL-encoded-resourceURI} | Nižší malá a velká kódování URL z identifikátoru URI prostředku malými písmeny. Předponu identifikátoru URI (podle segmentů) koncových bodů, které lze přistupovat pomocí tohoto tokenu, počínaje název hostitele IoT Device Provisioning Service (žádný protokol). Například, `mydps.azure-devices-provisioning.net`. |
| {policyName} |Název zásad sdíleného přístupu, na který odkazuje tento token. |

**Poznámka: na předponě**: identifikátor URI předponu je vypočítán segmentu a ne znak. Například `/a/b` je předpona pro `/a/b/c` , ale nikoli pro `/a/bc`.

Následující fragment kódu Node.js ukazuje funkci s názvem **generateSasToken** , který počítá token z vstupy `resourceUri, signingKey, policyName, expiresInMins`. Následující části podrobně popisují, jak inicializovat různé vstupy pro případy použití v odlišných tokenu.

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

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

Porovnání je ekvivalentní kód Python a vygenerovat token zabezpečení:

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
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> Protože doba platnosti tokenu je ověřen na počítačích IoT Device Provisioning Service, musí být minimální posun hodin na počítač, který generuje token.


### <a name="use-security-tokens-from-service-components"></a>Použití tokenů zabezpečení ze služby komponent

Součásti služby můžou generovat jenom tokeny zabezpečení pomocí udělení příslušných oprávnění, jak bylo popsáno dříve zásady sdíleného přístupu.

Zde jsou funkce služby, které jsou zveřejněné na koncové body:

| Koncový bod | Funkce |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Poskytuje operace registrace zařízení ve službě Device Provisioning Service. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Poskytuje operace pro správu skupin pro registraci zařízení. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Poskytuje operace pro načtení a správu stav registrací zařízení. |


Jako příklad service vygenerované pomocí předem vytvořené na úrovni shared zásadu přístupu **enrollmentread** by vytvořit token s následujícími parametry:

* identifikátor URI prostředku: `{mydps}.azure-devices-provisioning.net`,
* podpisový klíč: jeden z klíčů `enrollmentread` zásad,
* Název zásady: `enrollmentread`,
* kdykoli vypršení platnosti.

![Vytvořit zásady sdíleného přístupu pro instanci služby Device Provisioning na portálu][img-add-shared-access-policy]

```nodejs
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Výsledek, který může poskytnout přístup ke čtení všechny záznamy o zápisu, by byl:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Referenční témata:

V následujících tématech vám poskytnout další informace o řízení přístupu ke službě IoT Device Provisioning Service.

## <a name="device-provisioning-service-permissions"></a>Oprávnění služby zřizování zařízení

V následující tabulce jsou uvedeny oprávnění, která můžete použít k řízení přístupu ke službě IoT Device Provisioning Service.

| Oprávnění | Poznámky |
| --- | --- |
| **ServiceConfig** |Uděluje oprávnění k provádění změn konfigurace služeb. <br/>Toto oprávnění je použít cloudové back endové služby. |
| **EnrollmentRead** |Uděluje přístup pro čtení k registraci zařízení a skupin registrací. <br/>Toto oprávnění je použít cloudové back endové služby. |
| **EnrollmentWrite** |Uděluje oprávnění k zápisu na registraci zařízení a skupin registrací. <br/>Toto oprávnění je použít cloudové back endové služby. |
| **RegistrationStatusRead** |Uděluje přístup pro čtení k stav registrace zařízení. <br/>Toto oprávnění je použít cloudové back endové služby. |
| **RegistrationStatusWrite**  |Odstranit uděluje přístup ke stavu registrace zařízení. <br/>Toto oprávnění je použít cloudové back endové služby. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iot-dps/
