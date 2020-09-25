---
title: Koncové body zabezpečení ve službě IoT Device Provisioning | Microsoft Docs
description: Koncepty – jak řídit přístup k službě IoT Device Provisioning (DPS) pro back-endové aplikace. Obsahuje informace o tokenech zabezpečení.
author: wesmc7777
manager: philmea
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: wesmc
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 3a374dae89ddf22d33bc44cafd9db3b4b6a9e6fb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336816"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Řízení přístupu k Azure IoT Hub Device Provisioning Service

Tento článek popisuje možnosti zabezpečení služby IoT Device Provisioning. Služba zřizování používá *oprávnění* k udělení přístupu ke každému koncovému bodu. Oprávnění omezují přístup k instanci služby na základě funkčnosti.

Tento článek popisuje:

* Různá oprávnění, která můžete udělit aplikaci back-end pro přístup k vaší službě zřizování.
* Proces ověřování a tokeny, které používá k ověření oprávnění.

### <a name="when-to-use"></a>Kdy je použít

Musíte mít příslušná oprávnění pro přístup ke všem koncovým bodům služby zřizování. Například back-end aplikace musí zahrnovat token, který obsahuje zabezpečovací pověření, spolu s každou zprávou, kterou posílá službě.

## <a name="access-control-and-permissions"></a>Řízení přístupu a oprávnění

[Oprávnění](#device-provisioning-service-permissions) můžete udělit následujícími způsoby:

* **Zásady autorizace sdíleného přístupu**. Zásady sdíleného přístupu můžou udělit libovolnou kombinaci [oprávnění](#device-provisioning-service-permissions). V [Azure Portal][lnk-management-portal]můžete definovat zásady nebo programově pomocí [rozhraní REST API služby Device Provisioning][lnk-resource-provider-apis]. Nově vytvořená služba zřizování má následující výchozí zásady:

* **provisioningserviceowner**: zásada se všemi oprávněními.

> [!NOTE]
> Podrobné informace najdete v tématu [oprávnění](#device-provisioning-service-permissions) .

## <a name="authentication"></a>Authentication

Azure IoT Hub Device Provisioning Service uděluje přístup k koncovým bodům tím, že ověřuje token proti zásadám sdíleného přístupu. Přihlašovací údaje zabezpečení, jako jsou například symetrické klíče, se nikdy neodesílají přes síťový kabel.

> [!NOTE]
> Poskytovatel prostředků služby Device Provisioning je zabezpečený prostřednictvím vašeho předplatného Azure, stejně jako všichni poskytovatelé v [Azure Resource Manager][lnk-azure-resource-manager].

Další informace o tom, jak vytvořit a používat tokeny zabezpečení, najdete v další části.

Jediným podporovaným protokolem je HTTP a implementuje ověřování zahrnutím platného tokenu do hlavičky **autorizační** žádosti.

#### <a name="example"></a>Příklad
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> Sady [SDK služby Azure IoT Device Provisioning][lnk-sdks] automaticky generují tokeny při připojování ke službě.

## <a name="security-tokens"></a>Tokeny zabezpečení

Služba Device Provisioning používá k ověřování služeb tokeny zabezpečení, aby nedocházelo k posílání klíčů na lince. Tokeny zabezpečení jsou navíc omezené v době platnosti a rozsahu. Sady [SDK služby Device Provisioning v Azure IoT][lnk-sdks] automaticky generují tokeny bez nutnosti jakékoli speciální konfigurace. Některé scénáře vyžadují, abyste přímo vygenerovali a použili tokeny zabezpečení. Takové scénáře zahrnují přímé použití povrchu HTTP.

### <a name="security-token-structure"></a>Struktura tokenu zabezpečení

Tokeny zabezpečení slouží k udělení časově vázaného přístupu pro služby ke konkrétním funkcím ve službě IoT Device Provisioning. Aby bylo možné získat autorizaci k připojení ke službě zřizování, musí služby odesílat tokeny zabezpečení podepsané buď pomocí sdíleného přístupu, nebo symetrického klíče.

Token podepsaný pomocí sdíleného přístupového klíče uděluje přístup ke všem funkcím přidruženým k oprávněním zásad sdíleného přístupu. 

Token zabezpečení má následující formát:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Tady jsou očekávané hodnoty:

| Hodnota | Popis |
| --- | --- |
| označení |Řetězec pro podpis HMAC-SHA256 ve formátu: `{URL-encoded-resourceURI} + "\n" + expiry` . **Důležité**: klíč se dekóduje z formátu Base64 a používá se jako klíč k provedení výpočtu HMAC-SHA256.|
| vypršení platnosti |Řetězce UTF8 po dobu v sekundách od epocha 00:00:00 UTC dne 1. ledna 1970. |
| {URL-Encoded-resourceURI} | Malá adresa URL – kódování identifikátoru URI pro malý případ prostředku. Předpona URI (podle segmentu) koncových bodů, ke kterým se dá dostat s tímto tokenem, počínaje názvem hostitele služby IoT Device Provisioning (bez protokolu). Například, `mydps.azure-devices-provisioning.net`. |
| PolicyName |Název zásad sdíleného přístupu, na který tento token odkazuje |

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
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

Jako srovnání je ekvivalentní kód Pythonu pro vygenerování tokenu zabezpečení:

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
> Vzhledem k tomu, že se doba platnosti tokenu ověřuje na počítačích služby IoT Device Provisioning, musí být posun na hodinách počítače, který generuje token, minimální.

### <a name="use-security-tokens-from-service-components"></a>Použití tokenů zabezpečení z komponent služby

Součásti služby mohou generovat pouze tokeny zabezpečení pomocí zásad sdíleného přístupu udělujících příslušná oprávnění, která jsou vysvětlena dříve.

Tady jsou funkce služby vystavené na koncových bodech:

| Koncový bod | Funkce |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Poskytuje operace registrace zařízení ve službě Device Provisioning. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Poskytuje operace pro správu skupin registrace zařízení. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Poskytuje operace pro načtení a správu stavu registrací zařízení. |


Například služba vygenerovaná pomocí předem vytvořených zásad sdíleného přístupu s názvem **enrollmentread** by vytvořila token s následujícími parametry:

* identifikátor URI prostředku: `{mydps}.azure-devices-provisioning.net` ,
* podpisový klíč: jeden z klíčů `enrollmentread` zásady,
* Název zásady: `enrollmentread` ,
* jakýkoli čas vypršení platnosti. backn

![Vytvoření zásad sdíleného přístupu pro instanci služby Device Provisioning na portálu][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Výsledek, který by udělil přístup ke čtení všech záznamů zápisu, by byl:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Referenční témata:

Následující referenční témata obsahují další informace o řízení přístupu ke službě IoT Device Provisioning.

### <a name="device-provisioning-service-permissions"></a>Oprávnění služby Device Provisioning

Následující tabulka uvádí oprávnění, která můžete použít k řízení přístupu ke službě IoT Device Provisioning.

| Oprávnění | Poznámky |
| --- | --- |
| **ServiceConfig** |Udělí přístup ke změně konfigurací služby. <br/>Toto oprávnění používá cloudové služby back-end. |
| **EnrollmentRead** |Uděluje oprávnění ke čtení pro registrace zařízení a skupiny registrací. <br/>Toto oprávnění používá cloudové služby back-end. |
| **EnrollmentWrite** |Udělí přístup pro zápis zařízení a registrační skupiny. <br/>Toto oprávnění používá cloudové služby back-end. |
| **RegistrationStatusRead** |Udělí oprávnění ke čtení stavu registrace zařízení. <br/>Toto oprávnění používá cloudové služby back-end. |
| **RegistrationStatusWrite**  |Udělí odstranění přístup k stavu registrace zařízení. <br/>Toto oprávnění používá cloudové služby back-end. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/management/overview.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iot-dps/
