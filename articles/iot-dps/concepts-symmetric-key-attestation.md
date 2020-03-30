---
title: Služba zřizování zařízení služby Azure IoT Hub – atestace symetrických klíčů
description: Tento článek obsahuje koncepční přehled testování symetrických klíčů pomocí služby IoT Device Provisioning Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 0e3d343c0a68dd527e4e8e8d23e5b3843a216a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271509"
---
# <a name="symmetric-key-attestation"></a>Osvědčení symetrického klíče

Tento článek popisuje proces ověření identity při použití symetrických klíčů se službou device provisioning service. 

Symetrické atestace klíče je jednoduchý přístup k ověřování zařízení s instancí služby Device Provisioning Service. Tato metoda atestace představuje prostředí "Hello world" pro vývojáře, kteří jsou nové pro zřizování zařízení nebo nemají přísné požadavky na zabezpečení. Atestace zařízení pomocí [čipu TPM](concepts-tpm-attestation.md) nebo [certifikátu X.509](concepts-security.md#x509-certificates) je bezpečnější a měla by být použita pro přísnější požadavky na zabezpečení.

Symetrické registrace klíčů také poskytují skvělý způsob, jak starší zařízení s omezenou funkčností zabezpečení zavést do cloudu prostřednictvím Azure IoT. Další informace o symetrickém atestaci klíčů se staršími zařízeními najdete v tématu [Použití symetrických klíčů se staršími zařízeními](how-to-legacy-device-symm-key.md).


## <a name="symmetric-key-creation"></a>Vytvoření symetrického klíče

Ve výchozím nastavení služba Zřizování zařízení vytvoří nové symetrické klíče s výchozí délkou 32 bajtů, když jsou uloženy nové registrace s povolenou možností **Automaticky generovat klíče.**

![Automatické generování symetrických klíčů](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

Můžete také zadat vlastní symetrické klíče pro zápisy zakázáním této možnosti. Při zadávání vlastní symetrické klíče, klíče musí mít délku klíče mezi 16 bajtů a 64 bajtů. Symetrické klíče musí být také k dispozici v platném formátu Base64.



## <a name="detailed-attestation-process"></a>Podrobný proces atestace

Symetrické atestace klíče se službou Device Provisioning Service se provádí pomocí [stejných tokenů zabezpečení podporovaných](../iot-hub/iot-hub-devguide-security.md#security-token-structure) službou IoT huby k identifikaci zařízení. Tyto tokeny zabezpečení jsou [tokeny sdíleného přístupového podpisu (SAS).](../service-bus-messaging/service-bus-sas.md) 

Tokeny SAS mají zakódovaný *podpis,* který je vytvořen pomocí symetrického klíče. Podpis je znovu vytvořen službou device provisioning service k ověření, zda je token zabezpečení prezentovaný během ověřování autentický nebo ne.

Tokeny SAS mají následující formulář:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Zde jsou součásti každého tokenu:

| Hodnota | Popis |
| --- | --- |
| {podpis} |Řetězec podpisu HMAC-SHA256. Pro jednotlivé zápisy je tento podpis vytvořen pomocí symetrického klíče (primární ho nebo sekundární) k provedení algoritmu hash. Pro skupiny zápisu se klíč odvozený od klíče skupiny pro zápis používá k provedení hash. Hash se provádí na zprávu formuláře: `URL-encoded-resourceURI + "\n" + expiry`. **Důležité:** Klíč musí být dekódován z base64 před použitím k provedení výpočtu HMAC-SHA256. Výsledek podpisu musí být také zakódován adresou URL. |
| {resourceURI} |Identifikátor URI koncového bodu registrace, který je přístupný pomocí tohoto tokenu, počínaje ID oboru pro instanci služby zřizování zařízení. Například `{Scope ID}/registrations/{Registration ID}`. |
| {expirace} |Řetězce UTF8 pro počet sekund od epochy 00:00:00 UTC dne 1. |
| {URL kódovaný zdrojURI} |Malé url kódování identifikátoru URI |
| {policyName} |Název zásady sdíleného přístupu, na které odkazuje tento token. Název zásady používaný při zřizování s atestací symetrických klíčů je **registrace**. |

Když zařízení potvrzuje individuální registraci, zařízení používá symetrický klíč definovaný v jednotlivé položce registrace k vytvoření zahasovaného podpisu pro token SAS.

Příklady kódu, které vytvářejí token SAS, naleznete v [tématu Tokeny zabezpečení](../iot-hub/iot-hub-devguide-security.md#security-token-structure).

Vytváření tokenů zabezpečení pro symetrické atestace klíčů je podporováno sadou Azure IoT C SDK. Příklad pomocí sady Azure IoT C SDK k potvrzení individuální registrace najdete v [tématu Zřízení simulované zařízení s symetrickými klíči](quick-create-simulated-device-symm-key.md).


## <a name="group-enrollments"></a>Registrace skupiny

Symetrické klíče pro skupinové registrace nejsou používány přímo zařízeními při zřizování. Místo toho zařízení, která patří do skupiny registrace zřízení pomocí klíče odvozeného zařízení. 

Za prvé, jedinečné ID registrace je definována pro každé zařízení, které potvrzuje se skupinou registrace. Platné znaky pro ID registrace jsou malá alfanumerická a pomlčka ('-'). Toto ID registrace by mělo být něco jedinečného, co identifikuje zařízení. Starší zařízení například nemusí podporovat mnoho funkcí zabezpečení. Starší zařízení může mít k dispozici pouze adresu MAC nebo sériové číslo, které toto zařízení jednoznačně identifikuje. V takovém případě může být ID registrace složeno z adresy MAC a sériového čísla podobného následujícímu:

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Tento přesný příklad se používá v jak [zřídit starší zařízení pomocí symetrických klíčů](how-to-legacy-device-symm-key.md) článku.

Jakmile je pro zařízení definováno ID registrace, symetrický klíč pro skupinu zápisu se používá k výpočtu hash [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) id registrace k vytvoření odvozeného klíče zařízení. Hash ID registrace lze provést pomocí následujícího kódu Jazyka C#:

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

Výsledný klíč zařízení se pak používá ke generování tokenu SAS, který se má použít pro atestaci. Každé zařízení ve skupině registrace je nutné osvědčit pomocí tokenu zabezpečení generovaného z jedinečného odvozeného klíče. Symetrický klíč skupiny zápisu nelze použít přímo pro atestaci.

#### <a name="installation-of-the-derived-device-key"></a>Instalace odvozeného klíče zařízení

V ideálním případě jsou klíče zařízení odvozeny a nainstalovány ve výrobě. Tato metoda zaručuje, že klíč skupiny není nikdy součástí žádného softwaru nasazeného do zařízení. Pokud je zařízení přiřazena adresa MAC nebo sériové číslo, klíč může být odvozen a vložen do zařízení, nicméně výrobce se rozhodne jej uložit.

Vezměte v úvahu následující diagram, který zobrazuje tabulku klíčů zařízení generovaných ve výrobě pomocí hash každé ID registrace zařízení pomocí klíče pro registraci skupiny (**K**). 

![Klíče zařízení přiřazené z výroby](./media/concepts-symmetric-key-attestation/key-diversification.png)

Identita každého zařízení je reprezentována ID registrace a odvozeným klíčem zařízení, který je nainstalován ve výrobě. Klíč zařízení se nikdy nezkopíruje do jiného umístění a klíč skupiny se nikdy neuloží do zařízení.

Pokud klíče zařízení nejsou nainstalovány ve výrobě, modul [hardwarového zabezpečení hardwarového zabezpečení](concepts-security.md#hardware-security-module) modulu by měl být použit k bezpečnému uložení identity zařízení.

## <a name="next-steps"></a>Další kroky

Nyní, když máte pochopení symetrického klíče ověření, podívejte se na následující články se dozvíte více:

* [Rychlý start: Zřízení simulovaného zařízení se symetrickými klíči](quick-create-simulated-device-symm-key.md)
* [Další informace o konceptech v automatickém zřizování](./concepts-auto-provisioning.md)
* [Začínáme používat automatické zřizování](./quick-setup-auto-provision.md) 
