---
title: Koncové body zabezpečení ve službě IoT Device Provisioning Service | Dokumenty společnosti Microsoft
description: Koncepty – jak řídit přístup ke službě IoT Device Provisioning Service (DPS) pro back-endové aplikace. Obsahuje informace o tokenech zabezpečení.
author: wesmc7777
manager: philmea
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: wesmc
ms.openlocfilehash: 2a7e0932d226b1533c039b8529c2c11de06cf525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79285146"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Řízení přístupu ke službě Azure IoT Hub Device Provisioning Service

Tento článek popisuje možnosti zabezpečení služby Zřizování zařízení IoT. Zřizovací služba používá *oprávnění* k udělení přístupu ke každému koncovému bodu. Oprávnění omezují přístup k instanci služby na základě funkcí.

Tento článek popisuje:

* Různá oprávnění, která můžete udělit back-endové aplikaci pro přístup ke službě zřizování.
* Proces ověřování a tokeny, které používá k ověření oprávnění.

### <a name="when-to-use"></a>Kdy je použít

Musíte mít příslušná oprávnění pro přístup k některé z koncových bodů zřizovací služby. Například back-endová aplikace musí obsahovat token obsahující pověření zabezpečení spolu s každou zprávou, kterou odešle službě.

## <a name="access-control-and-permissions"></a>Řízení přístupu a oprávnění

[Oprávnění](#device-provisioning-service-permissions) můžete udělit následujícími způsoby:

* **Zásady autorizace sdíleného přístupu**. Zásady sdíleného přístupu mohou udělit libovolnou kombinaci [oprávnění](#device-provisioning-service-permissions). Zásady můžete definovat na [webu Azure Portal][lnk-management-portal]nebo programově pomocí rozhraní REST API [služby zřizování zařízení][lnk-resource-provider-apis]. Nově vytvořená služba zřizování má následující výchozí zásady:

* **provisioningserviceowner**: Zásady se všemi oprávněními.

> [!NOTE]
> Podrobné informace naleznete v [tématu oprávnění.](#device-provisioning-service-permissions)

## <a name="authentication"></a>Ověřování

Služba zřizování zařízení služby Azure IoT Hub uděluje přístup ke koncovým bodům ověřením tokenu proti zásadám sdíleného přístupu. Pověření zabezpečení, jako jsou například symetrické klíče, nejsou nikdy odesílány po drátě.

> [!NOTE]
> Poskytovatel prostředků služby Device Provisioning Service je zabezpečený prostřednictvím vašeho předplatného Azure, stejně jako všichni poskytovatelé ve [Správci prostředků Azure][lnk-azure-resource-manager].

Další informace o tom, jak vytvořit a používat tokeny zabezpečení, naleznete v další části.

Protokol HTTP je jediný podporovaný protokol a implementuje ověřování zahrnutím platného tokenu do hlavičky požadavku **na autorizaci.**

#### <a name="example"></a>Příklad
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> Sady [SDK služby Azure IoT Device Provisioning][lnk-sdks] automaticky generují tokeny při připojování ke službě.

## <a name="security-tokens"></a>Tokeny zabezpečení

Služba device provisioning používá tokeny zabezpečení k ověřování služeb, aby se zabránilo odesílání klíčů na lince. Tokeny zabezpečení jsou navíc omezeny časovou platností a rozsahem. [Sady SDK služby Azure IoT Device Provisioning][lnk-sdks] automaticky generují tokeny bez nutnosti jakékoli speciální konfigurace. Některé scénáře vyžadují, abyste přímo vygenerovali a používali tokeny zabezpečení. Tyto scénáře zahrnují přímé použití povrchu HTTP.

### <a name="security-token-structure"></a>Struktura tokenů zabezpečení

Tokeny zabezpečení slouží k udělení časově ohraničeného přístupu pro služby ke konkrétním funkcím ve službě IoT Device Provisioning Service. Chcete-li získat autorizaci pro připojení ke službě zřizování, služby musí odesílat tokeny zabezpečení podepsané sdíleným přístupem nebo symetrickým klíčem.

Token podepsaný sdíleným přístupovým klíčem uděluje přístup ke všem funkcím přidruženým k oprávněním zásad sdíleného přístupu. 

Token zabezpečení má následující formát:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Zde jsou očekávané hodnoty:

| Hodnota | Popis |
| --- | --- |
| {podpis} |Řetězec podpisu HMAC-SHA256 formuláře: `{URL-encoded-resourceURI} + "\n" + expiry`. **Důležité:** Klíč je dekódován z base64 a používá se jako klíč k provedení výpočtu HMAC-SHA256.|
| {expirace} |Řetězce UTF8 pro počet sekund od epochy 00:00:00 UTC dne 1. |
| {URL kódovaný zdrojURI} | Malé url kódování identifikátoru URI. Uri předpona (podle segmentu) koncových bodů, které jsou přístupné pomocí tohoto tokenu, počínaje názvem hostitele služby IoT Device Provisioning Service (bez protokolu). Například, `mydps.azure-devices-provisioning.net`. |
| {policyName} |Název zásady sdíleného přístupu, na které odkazuje tento token. |

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
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

Pro srovnání, ekvivalentní kód Pythonu pro generování tokenu zabezpečení je:

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
> Vzhledem k tomu, že čas platnost tokenu je ověřena na počítačích služby zřizování zařízení IoT, drift na hodiny počítače, který generuje token musí být minimální.

### <a name="use-security-tokens-from-service-components"></a>Použití tokenů zabezpečení z komponent služby

Součásti služby mohou generovat tokeny zabezpečení pouze pomocí zásad sdíleného přístupu, které udělují příslušná oprávnění, jak bylo vysvětleno dříve.

Zde jsou funkce služby vystavené na koncových bodech:

| Koncový bod | Funkce |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Poskytuje operace registrace zařízení se službou Device Provisioning Service. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Poskytuje operace pro správu skupin registrace zařízení. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Poskytuje operace pro načítání a správu stavu registrací zařízení. |


Jako příklad by služba generovaná pomocí předem vytvořené zásady sdíleného přístupu s názvem **enrollmentread** vytvořila token s následujícími parametry:

* identifikátor URI `{mydps}.azure-devices-provisioning.net`prostředků: ,
* podpisový klíč: jeden z `enrollmentread` klíčů politiky,
* název zásady: `enrollmentread`,
* všechny expirační time.backn

![Vytvoření zásad sdíleného přístupu pro instanci služby Zřizování zařízení na portálu][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Výsledkem, který by uděluje přístup ke všem záznamům zápisu, by bylo:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Referenční témata:

Následující referenční témata poskytují další informace o řízení přístupu ke službě Zřizování zařízení IoT.

### <a name="device-provisioning-service-permissions"></a>Oprávnění služby Zřizování zařízení

V následující tabulce jsou uvedena oprávnění, která můžete použít k řízení přístupu ke službě Zřizování zařízení IoT.

| Oprávnění | Poznámky |
| --- | --- |
| **Nástroj ServiceConfig** |Uděluje přístup ke změně konfigurace služby. <br/>Toto oprávnění se používá back-endové cloudové služby. |
| **ZápisRead** |Uděluje přístup pro čtení k registracím zařízení a skupinám registrací. <br/>Toto oprávnění se používá back-endové cloudové služby. |
| **ZápisWrite** |Uděluje přístup pro zápis k registracím zařízení a skupinám registrací. <br/>Toto oprávnění se používá back-endové cloudové služby. |
| **RegistrationStatusRead** |Uděluje čtení přístup ke stavu registrace zařízení. <br/>Toto oprávnění se používá back-endové cloudové služby. |
| **RegistraceStatusWrite**  |Uděluje přístup k odstranění stavu registrace zařízení. <br/>Toto oprávnění se používá back-endové cloudové služby. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/management/overview.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iot-dps/
