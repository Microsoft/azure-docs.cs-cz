---
title: Zabezpečení koncových bodů v služba zřizování zařízení IoT | Microsoft Docs
description: Principy – řízení přístupu ke službě zřizování zařízení IoT pro back-end aplikace. Obsahuje informace o tokeny zabezpečení.
author: dsk-2015
manager: timlt
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: dkshir
ms.openlocfilehash: e33f6b61f757c9d7f6a773141ad0deea363be2b7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629384"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Řízení přístupu k Azure IoT Hub zařízení zřizování služby

Tento článek popisuje možnosti pro zabezpečení služby zřizování zařízení IoT. Zřizování služby používá *oprávnění* k udělení přístupu k každý koncový bod. Oprávnění omezit přístup k instanci služby podle funkce.

Tento článek popisuje:

* Jiná oprávnění, že můžete udělit do back-end aplikace k přístupu ke službě zřizování.
* Proces ověřování a tokenů se používá k ověření oprávnění.

### <a name="when-to-use"></a>Kdy je použít

Musí mít příslušná oprávnění pro přístup k libovolnému zřizování služby na koncové body. Back-end aplikace musí obsahovat třeba token obsahující zabezpečovací pověření společně s každou zprávu, kterou odešle do služby.

## <a name="access-control-and-permissions"></a>Řízení přístupu a oprávnění

Můžete udělit [oprávnění](#device-provisioning-service-permissions) následujícími způsoby:

* **Sdílené zásady autorizace přístupu**. Zásady sdíleného přístupu můžete udělit libovolnou kombinaci [oprávnění](#device-provisioning-service-permissions). Můžete definovat zásady v [portál Azure][lnk-management-portal], nebo programově pomocí [zařízení zřizování služby REST API][lnk-resource-provider-apis]. Nově vytvořený zřizování služby má následující výchozí zásady:

  * **provisioningserviceowner**: zásada se všechna oprávnění.

> [!NOTE]
> V tématu [oprávnění](#device-provisioning-service-permissions) podrobné informace.

## <a name="authentication"></a>Authentication

Azure IoT Hub zařízení zřizování služby uděluje přístup ke koncovým bodům kontrolou token proti zásady sdíleného přístupu. Zabezpečovací přihlašovací údaje, jako jsou symetrického klíče, se nikdy odeslány prostřednictvím sítě.

> [!NOTE]
> Zprostředkovatel prostředků služby zřizování zařízení je zabezpečená vašeho předplatného Azure, jsou všechny zprostředkovatele v [Azure Resource Manager][lnk-azure-resource-manager].

Další informace o tom, jak vytvořit a používat tokeny zabezpečení najdete v další části.

Je pouze podporovaných protokolů HTTP a implementuje ověřování zahrnutím platný token v **autorizace** hlavičky žádosti.

#### <a name="example"></a>Příklad:
`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`

> [!NOTE]
> [Zařízení zřizování služby SDK služby Azure IoT] [ lnk-sdks] automaticky generovat tokeny při připojování ke službě.

## <a name="security-tokens"></a>Tokeny zabezpečení
Služba zřizování zařízení používá tokeny zabezpečení k ověření služby se odesílání klíče v drátové síti. Kromě toho mají omezenou dobu platnosti a obor tokeny zabezpečení. [Azure zřizování služby SDK pro zařízení IoT] [ lnk-sdks] automaticky generovat tokeny bez nutnosti žádnou zvláštní konfiguraci. Některé scénáře vyžadují, abyste generovat a používat přímo tokeny zabezpečení. Takových scénářů patří přímého použití povrchu HTTP.

### <a name="security-token-structure"></a>Struktura tokenu zabezpečení

Tokeny zabezpečení slouží k udělení přístupu ohraničenou čas služeb určitých funkcí ve službě zřizování zařízení IoT. Získat autorizaci k připojení ke službě zřizování, musí poslat služby tokenů zabezpečení, které jsou podepsané sdíleného přístupu nebo symetrického klíče.

Token podepsán sdílený přístupový klíč uděluje přístup k všechny funkce související s oprávněními zásady sdíleného přístupu. 

Token zabezpečení má následující formát:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Zde jsou očekávaných hodnot:

| Hodnota | Popis |
| --- | --- |
| {podpis} |Řetězec podpisu HMAC SHA256 ve tvaru: `{URL-encoded-resourceURI} + "\n" + expiry`. **Důležité**: klíč je dekódovat z formátu base64 a použít jako klíč, aby prováděly výpočty HMAC SHA256.|
| {vypršení platnosti} |Řetězce UTF8 pro počet sekund od 00:00:00 UTC epoch na 1. ledna pod hodnotou 1970. |
| {Adresu URL-kódovaný resourceURI} | Nižší případu, adresa URL kódování identifikátoru URI prostředku malá písmena. Předpony identifikátoru URI (podle segmentu) koncových bodů, které lze přistupovat pomocí tohoto tokenu, počínaje název hostitele služby IoT zařízení zřizování (žádné protocol). Například, `mydps.azure-devices-provisioning.net`. |
| {policyName} |Název zásady sdíleného přístupu, na který odkazuje tento token. |

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

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
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
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> Vzhledem k tomu, že doba platnosti tokenu se ověří na počítače, služby pro zřizování zařízení IoT, musí být minimální odlišily v hodinách na počítač, který generuje token.


### <a name="use-security-tokens-from-service-components"></a>Použít tokeny zabezpečení ze součásti služby

Součásti služby můžete vygenerovat pouze tokeny zabezpečení pomocí udělení příslušných oprávnění, jak je popsáno dříve zásady sdíleného přístupu.

Zde jsou funkce služby, které jsou zveřejněné na koncové body:

| Koncový bod | Funkce |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Poskytuje operace zápisu zařízení se službou zřizování zařízení. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Poskytuje operace pro správu skupin registrace zařízení. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Poskytuje operace pro načítání a správu stav registrace zařízení. |


Jako příklad služby generována pomocí předem vytvořené sdílené zásady přístupu volat **enrollmentread** by vytvořit token s následujícími parametry:

* identifikátor URI prostředku: `{mydps}.azure-devices-provisioning.net`,
* podpisový klíč: jeden z klíčů `enrollmentread` zásad
* Název zásady: `enrollmentread`,
* kdykoli vypršení platnosti.

![Vytvořit zásady sdíleného přístupu pro vaše instance distribučních bodů na portálu][img-add-shared-access-policy]

```nodejs
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Výsledek, který by udělit přístup ke čtení všechny záznamy o zápisu, bude:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Témata odkazů:

Následující referenční témata poskytují další informace o řízení přístupu ke službě zřizování zařízení IoT.

## <a name="device-provisioning-service-permissions"></a>Oprávnění služby zřizování zařízení

Následující tabulka uvádí oprávnění, která můžete použít k řízení přístupu ke službě zřizování zařízení IoT.

| Oprávnění | Poznámky |
| --- | --- |
| **ServiceConfig** |Uděluje oprávnění k provádění změn konfigurace služby. <br/>Toto oprávnění je používán back-end cloudové služby. |
| **EnrollmentRead** |Uděluje přístup pro čtení k registraci zařízení a registrace skupiny. <br/>Toto oprávnění je používán back-end cloudové služby. |
| **EnrollmentWrite** |Uděluje oprávnění k zápisu do registrace zařízení a registrace skupin. <br/>Toto oprávnění je používán back-end cloudové služby. |
| **RegistrationStatusRead** |Uděluje přístup pro čtení k stav registrace zařízení. <br/>Toto oprávnění je používán back-end cloudové služby. |
| **RegistrationStatusWrite**  |Odstranit uděluje přístup k stav registrace zařízení. <br/>Toto oprávnění je používán back-end cloudové služby. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iot-dps/
