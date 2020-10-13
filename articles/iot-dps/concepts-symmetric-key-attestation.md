---
title: Azure IoT Hub Device Provisioning Service – ověření identity symetrického klíče
description: Tento článek obsahuje koncepční přehled ověřování symetrického klíče pomocí služby IoT Device Provisioning (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: devx-track-csharp
ms.openlocfilehash: 994c2c3124d6822f047af942268ad7a401d5a976
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531555"
---
# <a name="symmetric-key-attestation"></a>Osvědčení symetrického klíče

Tento článek popisuje proces ověření identity při použití symetrických klíčů se službou Device Provisioning. 

Symetrický ověření identity je jednoduchý přístup k ověřování zařízení pomocí instance služby Device Provisioning. Tato metoda ověření identity představuje prostředí "Hello World" pro vývojáře, kteří jsou noví v zřizování zařízení, nebo nemají přísné požadavky na zabezpečení. Ověření zařízení pomocí [čipu TPM](concepts-tpm-attestation.md) nebo [certifikátu X. 509](concepts-x509-attestation.md) je bezpečnější a mělo by se používat pro přísnější požadavky na zabezpečení.

Zápisy symetrického klíče také poskytují skvělý způsob, jak používat starší verze zařízení s omezeným zabezpečením, pro spouštění do cloudu prostřednictvím Azure IoT. Další informace o ověření symetrického klíče pomocí starších zařízení najdete v tématu [použití symetrických klíčů se staršími zařízeními](how-to-legacy-device-symm-key.md).


## <a name="symmetric-key-creation"></a>Vytvoření symetrického klíče

Ve výchozím nastavení služba Device Provisioning vytvoří nové symetrické klíče s výchozí délkou 64 bajtů, když se nové registrace uloží s povolenou možností **automaticky generovat klíče** .

![Automaticky generovat symetrické klíče](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

Zakázáním této možnosti můžete také poskytnout vlastní symetrické klíče pro registraci. Když zadáte vlastní symetrický klíč, musí mít klíče délku klíče mezi 16 bajty a 64 bajtů. V platném formátu base64 musí být také poskytnuty symetrické klíče.



## <a name="detailed-attestation-process"></a>Podrobný proces ověření identity

Ověření identity symetrického klíče se službou Device Provisioning se provádí pomocí stejných [tokenů zabezpečení](../iot-hub/iot-hub-devguide-security.md#security-token-structure) , které centra IoT podporuje k identifikaci zařízení. Tyto tokeny zabezpečení jsou [tokeny sdíleného přístupového podpisu (SAS)](../service-bus-messaging/service-bus-sas.md). 

Tokeny SAS mají *signaturu* s hodnotou hash, která je vytvořena pomocí symetrického klíče. Služba Device Provisioning znovu vytvoří podpis, který ověří, jestli je token zabezpečení prezentovaný během ověření platný, nebo ne.

Tokeny SAS mají následující formát:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Tady jsou komponenty každého tokenu:

| Hodnota | Popis |
| --- | --- |
| označení |Řetězec pro podpis HMAC-SHA256. Pro jednotlivé registrace se tento podpis vytvoří pomocí symetrického klíče (primárního nebo sekundárního) k provedení hodnoty hash. Pro skupiny registrací se k provedení hodnoty hash používá klíč odvozený z klíče skupiny zápisu. Hodnota hash se provádí ve zprávě formuláře: `URL-encoded-resourceURI + "\n" + expiry` . **Důležité**: klíč musí být před použitím pro výpočet HMAC-SHA256 dekódovat z formátu base64. Výsledek signatury musí být také kódovaný pomocí adresy URL. |
| resourceUri |Identifikátor URI koncového bodu registrace, ke kterému má být přístup s tímto tokenem, počínaje ID oboru pro instanci služby Device Provisioning. Například `{Scope ID}/registrations/{Registration ID}`. |
| vypršení platnosti |Řetězce UTF8 po dobu v sekundách od epocha 00:00:00 UTC dne 1. ledna 1970. |
| {URL-Encoded-resourceURI} |Malá adresa URL – kódování identifikátoru URI pro malý případ prostředku |
| PolicyName |Název zásad sdíleného přístupu, na který tento token odkazuje Název zásady, který se používá při zřizování s symetrickým ověřením identity klíče, se **registruje**. |

Když se zařízení potvrdí pomocí individuální registrace, zařízení použije symetrický klíč definovaný v položce individuální registrace k vytvoření signatury s hodnotou hash pro token SAS.

Příklady kódu, které vytvářejí tokeny SAS, najdete v tématu [tokeny zabezpečení](../iot-hub/iot-hub-devguide-security.md#security-token-structure).

Vytváření tokenů zabezpečení pro ověřování symetrického klíče je podporováno sadou SDK Azure IoT C. Příklad použití sady Azure IoT C SDK k ověření pomocí individuální registrace najdete v tématu [zřízení simulovaného zařízení s symetrickými klíči](quick-create-simulated-device-symm-key.md).


## <a name="group-enrollments"></a>Registrace skupin

Symetrické klíče pro registrace skupin se při zřizování nepoužívají přímo v zařízeních. Místo toho zařízení, která patří do zřizování skupiny registrací, použijte odvozený klíč zařízení. 

Nejprve je definováno jedinečné ID registrace pro každé zařízení, které se ověřuje pomocí skupiny pro registraci. Platnými znaky ID registrace jsou malá písmena a spojovníky (-). ID registrace by mělo být něco jedinečného, které identifikuje zařízení. Starší verze zařízení například nemusí podporovat mnoho funkcí zabezpečení. Starší verze zařízení může mít k dispozici pouze adresu MAC nebo sériové číslo pro jednoznačné identifikaci daného zařízení. V takovém případě ID registrace se může skládat z adresy MAC a sériového čísla, které je podobné následujícímu:

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Tento přesný příklad se používá v článku [jak zřídit starší verze zařízení pomocí symetrických klíčů](how-to-legacy-device-symm-key.md) .

Po definování ID registrace pro zařízení se symetrický klíč pro skupinu registrací používá k výpočtu hodnoty hash [SHA256 HMAC](https://wikipedia.org/wiki/HMAC) ID registrace, která vytvoří odvozený klíč zařízení. Hodnoty hash ID registrace lze provést s následujícím kódem jazyka C#:

```csharp
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

```csharp
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

Výsledný klíč zařízení se pak použije k vygenerování tokenu SAS, který se použije k ověření identity. Každé zařízení ve skupině registrací se vyžaduje k ověření pomocí tokenu zabezpečení generovaného z jedinečného odvozeného klíče. Symetrický klíč skupiny registrací nelze použít přímo pro ověření identity.

#### <a name="installation-of-the-derived-device-key"></a>Instalace odvozeného klíče zařízení

Klíče zařízení se v ideálním případě odvozují a nainstalují v továrně. Tato metoda zaručuje, že klíč skupiny není nikdy zahrnutý v žádném softwaru nasazeném do zařízení. Když je zařízení přiřazená adresa MAC nebo sériové číslo, může se klíč odvodit a vložit do zařízení, ale výrobce si ho bude moct Uložit.

Vezměte v úvahu následující diagram, který zobrazuje tabulku klíčů zařízení vygenerovaných v továrně pomocí algoritmu hash pro každé ID registrace zařízení pomocí klíče pro registraci skupiny (**K**). 

![Klíče zařízení přiřazené z továrny](./media/concepts-symmetric-key-attestation/key-diversification.png)

Identita každého zařízení je reprezentovaná ID registrace a odvozeným klíčem zařízení, který je nainstalovaný v továrně. Klíč zařízení se nikdy nekopíruje do jiného umístění a klíč skupiny se na zařízení nikdy neukládá.

Pokud v továrně nejsou nainstalované klíče zařízení, musí se k bezpečnému uložení identity zařízení použít modul hardwarového [zabezpečení HSM](concepts-service.md#hardware-security-module) .

## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámeni s ověřením symetrického klíče, Projděte si následující články, kde najdete další informace:

* [Rychlý start: Zřízení simulovaného zařízení se symetrickými klíči](quick-create-simulated-device-symm-key.md)
* [Přečtěte si o konceptech zřizování](about-iot-dps.md#provisioning-process)
* [Začínáme s automatickým zřizováním](./quick-setup-auto-provision.md) 
