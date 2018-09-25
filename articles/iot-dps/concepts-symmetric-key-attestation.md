---
title: Azure IoT Hub zařízení služby zřizování – ověření identity symetrického klíče
description: Tento článek obsahuje koncepční přehled symetrického klíče ověření pomocí služby IoT Device Provisioning.
author: wesmc7777
ms.author: wesmc
ms.date: 08/18/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 80828876ffe8b58697cfaacad4991354ac070730
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971786"
---
# <a name="symmetric-key-attestation"></a>Ověření identity symetrického klíče

Tento článek popisuje postup ověření identity, při použití symetrického klíče ve službě Device Provisioning Service. 

Symetrické klíče ověření je jednoduchý přístup k ověřování zařízení s instancí služby Device Provisioning. Tato metoda ověření představuje "Hello world" prostředí pro vývojáře, kteří začínají s zřizování zařízení, nebo nemáte vysokými nároky na zabezpečení. Zařízení pomocí ověření [TPM](concepts-tpm-attestation.md) nebo [certifikát X.509](concepts-security.md#x509-certificates) je informace zabezpečení a byste měli použít pro přísnější požadavky na zabezpečení.

Symetrické klíče registrace také poskytnout skvělý způsob, jak pro zařízení se starší verze, funkce omezeného zabezpečení k navázání připojení ke cloudu pomocí Azure IoT. Další informace o ověření identity symetrického klíče pomocí starší verze zařízení najdete v tématu [použití symetrické klíče s starší zařízení](how-to-legacy-device-symm-key.md).


## <a name="symmetric-key-creation"></a>Vytvoření symetrického klíče

Ve výchozím nastavení, služby Device Provisioning Service vytvoří nová symetrické klíče s délkou výchozí 32 bajtů při ukládání nové registrace s **automaticky generovat klíče** povolenou možnost.

![Automaticky vygenerovat symetrické klíče](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

Zakázáním této možnosti můžete zadat také vlastní symetrické klíče pro registraci. Při zadávání symetrického klíče, klíče musí mít délku klíče mezi 16 bajtů a 64 bajtů. Symetrické klíče musí také zadat v platném formátu Base64.



## <a name="detailed-attestation-process"></a>Podrobné ověření procesu

Ověření identity symetrického klíče ve službě Device Provisioning Service se provádí pomocí stejných [tokeny zabezpečení](../iot-hub/iot-hub-devguide-security.md#security-token-structure) centra IoT hub pro účely identifikace zařízení podporovány. Tyto tokeny zabezpečení jsou [tokeny sdíleného přístupového podpisu (SAS)](../service-bus-messaging/service-bus-sas.md). 

Mít tokeny SAS hodnoty hash *podpis* , který je vytvořený pomocí symetrického klíče. Podpis je opětovně vytvořit pomocí služby Device Provisioning můžete ověřit, zda token zabezpečení, které se zobrazí během ověření identity je platná, nebo ne.

Tokeny SAS mají následující podobu:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Tady jsou součástí každý token:

| Hodnota | Popis |
| --- | --- |
| {podpis} |Řetězec podpisu HMAC SHA256. Pro jednotlivé registrace tento podpis je vytvořen pomocí symetrického klíče (primární nebo sekundární) k provedení-the-hash. Pro skupiny registrací klíčem odvozeným od klíč registrace skupiny slouží k provádění-the-hash. Hodnota hash se provádí na zprávu ve formátu: `URL-encoded-resourceURI + "\n" + expiry`. **Důležité**: klíč musí dekódovat z formátu base64. před použitím pro provádění výpočtů HMAC SHA256. Výsledek podpis musí být také kódovaná adresou URL. |
| {resourceURI} |Identifikátor URI koncového bodu registrace, který je přístupný s tímto tokenem od ID oboru pro instanci služby Device Provisioning. Například `{Scope ID}/registrations/{Registration ID}`. |
| {expiry} |Řetězce UTF8 pro počet sekund od 00:00:00 UTC epocha na 1. ledna 1970. |
| {Adresu URL-encoded-resourceURI} |Nižší malá a velká kódování URL z identifikátoru URI prostředku malými písmeny |
| {policyName} |Název zásad sdíleného přístupu, na který odkazuje tento token. Název zásady použitý při zřizování s symetrického klíče ověření je **registrace**. |

Když je zařízení potvrzení s jednotlivou registraci, zařízení použije k vytvoření hodnoty hash podpisu tokenu SAS symetrický klíč definovaný v položku jednotlivé registrace.

Příklady kódu, které vytvoříte SAS token, naleznete v tématu [tokeny zabezpečení](../iot-hub/iot-hub-devguide-security.md#security-token-structure).

Vytváření tokenů zabezpečení pro symetrické klíče ověření je podporováno v sadě Azure IoT C SDK. Příklad použití Azure IoT C SDK ověřit s jednotlivou registraci, najdete v části [zřízení simulovaného zařízení pomocí symetrických klíčů](quick-create-simulated-device-symm-key.md).


## <a name="group-enrollments"></a>Skupinové registrace

Symetrické klíče pro skupinové registrace nepoužívají přímo na zařízeních při zřizování. Místo toho skupiny zařízení, která patří registraci zřizování pomocí klíče odvozené zařízení. 

Nejprve jedinečným registračním ID, je definován pro každé zařízení potvrzení s skupině pro registraci. Platné znaky pro ID registrace jsou malé alfanumerické znaky a pomlčky ("-"). Toto ID registrace musí být jedinečný něco, co se identifikují zařízení. Například starší zařízení nemusí podporovat mnoho funkcí zabezpečení. Starší verze zařízení může mít jenom adresu MAC nebo sériové číslo, které jsou k dispozici k jednoznačné identifikaci zařízení. V takovém případě ID registrace může skládat ze adresu MAC a sériové číslo podobný následujícímu:

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

V tomto příkladu přesně je používán [jak zřídit starší zařízení pomocí symetrických klíčů](how-to-legacy-device-symm-key.md) článku.

Po definování ID registrace pro zařízení, symetrický klíč pro skupiny registrací se používá k výpočtu [HMAC SHA256](https://wikipedia.org/wiki/HMAC) hash ID registrace k vytvoření klíče odvozené zařízení. Algoritmus hash ID registrace, můžete to provést pomocí následujícího kódu jazyka C#:

```C#
using System; 
using System.Security.Cryptography; 
using System.Text;  

public static class Utils 
{ 
    public static string ComputeDerivedSymmetricKey(byte[] masterKey, string registrationId) 
    { 
        using (var hmac = new HMACSHA256(masterKey)) 
        { 
            return Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(registrationId))); 
        } 
    } 
} 
```

```C#
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

Výsledným klíčem zařízení se použije k vygenerování tokenu SAS, který má být použit pro ověření identity. Každé zařízení ve skupině pro registraci je potřeba ověřit pomocí tokenu zabezpečení vygenerovaná jedinečný odvozeného klíče. Registrace skupiny symetrický klíč nelze použít přímo pro ověření identity.

#### <a name="installation-of-the-derived-device-key"></a>Instalace klíče odvozené zařízení

V ideálním případě jsou klíče zařízení odvozen a nainstalován v objektu pro vytváření. Tato metoda zaručuje, že klíč skupiny je součástí nikdy veškerý software nasazený do zařízení. Když zařízení přiřadí adresu MAC nebo sériové číslo, klíč odvozené a vloženy do zařízení, ale možnost výrobce jej uložte.

Vezměte v úvahu následující diagram, který zobrazuje tabulku zařízení klíče v továrně generují pomocí hashování každé ID registrace zařízení s klíčem registrace skupiny (**K**). 

![Klíče zařízení přiřazené od objektu factory](./media/concepts-symmetric-key-attestation/key-diversification.png)

Identita každé zařízení je reprezentován ID registrace a odvozené zařízení klíč, který je nainstalován na objekt pro vytváření. Klíč zařízení nikdy zkopírován do jiného umístění a skupiny klíč se nikdy neukládají na zařízení.

Pokud zařízení klíče nejsou nainstalovány v objektu pro vytváření, [modulu hardwarového zabezpečení HSM](concepts-security.md#hardware-security-module) by měla sloužit k bezpečnému ukládání identitu zařízení.

## <a name="next-steps"></a>Další postup

Teď, když máte představu o ověření identity symetrického klíče, přečtěte si další informace v následujících článcích:

* [Rychlý start: Zřízení simulovaného zařízení pomocí symetrických klíčů](quick-create-simulated-device-symm-key.md)
* [Další informace o konceptech automatického zřizování](./concepts-auto-provisioning.md)
* [Začínáme s využitím automatického zřizování](./quick-setup-auto-provision.md) 
