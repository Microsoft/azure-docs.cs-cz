---
title: Media Services podporu licencí Apple FairPlay
description: Toto téma poskytuje přehled licenčních požadavků a konfigurací Apple FairPlay.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 5a00ba8000f33e8e50b1a5d01f40a68a7fe2442d
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956187"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Konfigurace a licenční požadavky pro Apple FairPlay

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services umožňuje šifrování obsahu HLS pomocí **Apple Fairplay** (AES-128 CBC). Media Services taky poskytuje službu pro doručování licencí FairPlay. Když se hráč pokusí přehrát obsah chráněný FairPlay, pošle se do služby doručování licencí požadavek, aby získal licenci. Pokud licenční služba žádost schválí, vydá licenci, která je odeslána klientovi a slouží k dešifrování a přehrání zadaného obsahu.

Media Services taky poskytuje rozhraní API, která můžete použít ke konfiguraci licencí FairPlay. Toto téma popisuje licenční požadavky FairPlay a ukazuje, jak můžete konfigurovat licenci **Fairplay** pomocí rozhraní Media Services API. 

## <a name="requirements"></a>Požadavky

Při použití Media Services k šifrování obsahu HLS pomocí nástroje **Apple Fairplay** a použití Media Services k doručování licencí Fairplay postupujte následovně:

* Zaregistrujte se pomocí [vývojového programu Apple](https://developer.apple.com/).
* Apple vyžaduje, aby vlastník obsahu získal [balíček pro nasazení](https://developer.apple.com/contact/fps/). Stav, kdy už jste implementovali modul zabezpečení klíčů (KSM) s Media Services a že požadujete finální balíček pro FPS. K dispozici jsou pokyny v posledním balíčku FPS pro vygenerování certifikace a získání tajného klíče aplikace (ASK). Pro konfiguraci FairPlay použijte dotaz.
* Na straně Media Services Key/License Delivery musí být nastavené následující věci:

    * **Certifikát aplikace (AC)**: Jedná se o soubor. pfx, který obsahuje privátní klíč. Vytvořte tento soubor a Zašifrujte ho heslem. Soubor. pfx by měl být ve formátu base64.

        Následující postup popisuje, jak vygenerovat soubor certifikátu. pfx pro FairPlay:

        1. Nainstalujte OpenSSL z https://slproweb.com/products/Win32OpenSSL.html .

            Přejít do složky, kde je certifikát FairPlay a další soubory dodávané společností Apple.
        2. V příkazovém řádku spusťte následující příkaz. Tím se soubor. cer převede na soubor. pem.

            "C:\OpenSSL-Win32\bin\openssl.exe" x509-informující der-in FairPlay. cer-out FairPlay-out. pem
        3. V příkazovém řádku spusťte následující příkaz. Tím se soubor. pem převede na soubor. pfx s privátním klíčem. Heslo pro soubor. pfx pak vyzve OpenSSL.

            "C:\OpenSSL-Win32\bin\openssl.exe" PKCS12-export FairPlay-out. pfx-INKEY PrivateKey. pem-in FairPlay-out. pem-Passin file:privatekey-pem-pass.txt
            
    * **Heslo certifikátu aplikace**: heslo pro vytvoření souboru. pfx.
    * **Dotaz**: Tento klíč se obdrží, když vygenerujete certifikaci pomocí portálu pro vývojáře Apple. Každý vývojový tým obdrží jedinečný dotaz. Uložte kopii žádosti a uložte ji na bezpečném místě. U Media Services musíte nakonfigurovat ASK jako FairPlayAsk.
    
* Následující akce musí být nastaveny na straně klienta FPS:

  * **Certifikát aplikace (AC)**: Jedná se o soubor. cer/. der obsahující veřejný klíč, který operační systém používá k šifrování některých datových částí. Media Services musí o tom znát, protože ho vyžaduje hráč. Služba doručování klíčů dešifruje pomocí odpovídajícího privátního klíče.

* Pokud chcete přehrát FairPlay zašifrovaný Stream, Získejte nejdřív skutečný dotaz a pak vygenerujte skutečný certifikát. Tento proces vytvoří všechny tři části:

  * soubor. der
  * soubor. pfx
  * heslo pro soubor. pfx
  
> [!NOTE]
> Azure Media Services nekontroluje datum vypršení platnosti certifikátu během balení nebo doručování klíčů. Po vypršení platnosti certifikátu bude fungovat i nadále.

## <a name="fairplay-and-player-apps"></a>Aplikace FairPlay a přehrávače

Když je váš obsah zašifrovaný pomocí **Apple Fairplay**, jednotlivé video a zvukové vzorky se šifrují pomocí režimu **AES-128 CBC** . **Fairplay streamování** (FPS) je integrované do operačních systémů zařízení s nativní podporou pro iOS a Apple TV. Safari v OS X umožňuje použití snímků na základě podpory rozhraní EME (Encrypted Media Extensions).

Azure Media Player podporuje také přehrávání FairPlay. Další informace najdete v [dokumentaci Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html).

Můžete vyvíjet vlastní aplikace přehrávače pomocí sady iOS SDK. Aby bylo možné přehrávat FairPlay obsah, je nutné implementovat protokol License Exchange. Tento protokol není specifikován společností Apple. Jak odesílat požadavky na doručení klíčů, je k diskaždé aplikaci. Služba doručování klíčů Media Services FairPlay očekává, že se SPC přidělí jako webová zpráva zakódovaná v URL formátu www v následujícím tvaru:

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>Příklad konfigurace rozhraní .NET FairPlay

K nakonfigurování licencí FairPlay můžete použít rozhraní API pro Media Services. Když se hráč pokusí přehrát obsah chráněný FairPlay, pošle se mu žádost o licenci. Pokud licenční služba žádost schválí, služba vydá licenci. Odesílá se klientovi a používá se k dešifrování a přehrávání zadaného obsahu.

> [!NOTE]
> Obvykle byste chtěli nakonfigurovat možnosti zásad FairPlay jenom jednou, protože budete mít jenom jednu sadu certifikace a dotaz.

Následující příklad používá ke konfiguraci licence [sadu Media Services .NET SDK](/dotnet/api/microsoft.azure.management.media.models?view=azure-dotnet) .

```csharp
private static ContentKeyPolicyFairPlayConfiguration ConfigureFairPlayPolicyOptions()
{

    string askHex = "";
    string FairPlayPfxPassword = "";

    var appCert = new X509Certificate2("FairPlayPfxPath", FairPlayPfxPassword, X509KeyStorageFlags.Exportable);

    byte[] askBytes = Enumerable
        .Range(0, askHex.Length)
        .Where(x => x % 2 == 0)
        .Select(x => Convert.ToByte(askHex.Substring(x, 2), 16))
        .ToArray();

    ContentKeyPolicyFairPlayConfiguration fairPlayConfiguration =
    new ContentKeyPolicyFairPlayConfiguration
    {
        Ask = askBytes,
        FairPlayPfx =
                Convert.ToBase64String(appCert.Export(X509ContentType.Pfx, FairPlayPfxPassword)),
        FairPlayPfxPassword = FairPlayPfxPassword,
        RentalAndLeaseKeyType =
                ContentKeyPolicyFairPlayRentalAndLeaseKeyType
                .PersistentUnlimited,
        RentalDuration = 2249 // in seconds
    };

    return fairPlayConfiguration;
}
```

## <a name="next-steps"></a>Další kroky

Podívejte se, jak [chránit pomocí DRM](protect-with-drm.md)
