---
title: Mediální služby a licenční podpora Apple FairPlay – Azure | Dokumenty společnosti Microsoft
description: Toto téma poskytuje přehled licenčních požadavků a konfigurace Apple FairPlay.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6d4b7ba842d08723b90a4f2491d9e79e68dd932e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60733568"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Konfigurace a licenční požadavky pro Apple FairPlay 

Azure Media Services umožňuje šifrovat obsah HLS pomocí **Apple FairPlay** (AES-128 CBC). Media Services také poskytuje službu pro doručování licencí FairPlay. Když se hráč pokusí přehrát váš obsah chráněný fairplayem, je do doručovací služby licence odeslána žádost o získání licence. Pokud licenční služba žádost schválí, vydá licenci, která je odeslána klientovi a slouží k dešifrování a přehrání zadaného obsahu.

Media Services také poskytuje rozhraní API, která můžete použít ke konfiguraci licencí FairPlay. Toto téma popisuje licenční požadavky FairPlay a ukazuje, jak můžete nakonfigurovat licenci **FairPlay** pomocí rozhraní API Mediálních služeb. 

## <a name="requirements"></a>Požadavky

Při používání mediálních služeb k šifrování obsahu HLS pomocí **Apple FairPlay** a k doručování licencí FairPlay je nutné následující:

* Zaregistrujte se [pomocí vývojového programu Apple](https://developer.apple.com/).
* Společnost Apple vyžaduje, aby vlastník obsahu získal [balíček nasazení](https://developer.apple.com/contact/fps/). Uveďte, že jste již implementovali modul zabezpečení klíčů (KSM) s mediálními službami a že požadujete konečný balíček FPS. V konečném balíčku FPS jsou pokyny pro generování certifikace a získání tajného klíče aplikace (ASK). Ke konfiguraci FairPlay používáte ASK.
* Na straně doručování klíčů/licencí služby Media Services je nutné nastavit následující věci:

    * **Certifikát aplikace (AC):** Jedná se o soubor .pfx, který obsahuje soukromý klíč. Tento soubor vytvoříte a zašifrujete heslem. Soubor .pfx by měl být ve formátu Base64.

        Následující kroky popisují, jak generovat soubor certifikátu .pfx pro FairPlay:

        1. Nainstalujte OpenSSL z https://slproweb.com/products/Win32OpenSSL.html.

            Přejděte do složky, kde je certifikát FairPlay a další soubory dodané společností Apple.
        2. V příkazovém řádku spusťte následující příkaz. Tím se převede soubor CER na soubor PEM.

            "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
        3. V příkazovém řádku spusťte následující příkaz. Tím se soubor PEM převede na soubor Pfx se soukromým klíčem. Heslo pro soubor .pfx je pak požádán OpenSSL.

            "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
            
    * **Heslo certifikátu aplikace**: Heslo pro vytvoření souboru .pfx.
    * **ASK**: Tento klíč je přijat při generování certifikace pomocí portálu Apple Developer. Každý vývojový tým obdrží jedinečný ASK. Uložte kopii ask a uložte ji na bezpečném místě. Ask je třeba nakonfigurovat jako FairPlayAsk s Media Services.
    
* Na straně klienta FPS musí být nastaveny následující věci:

  * **Certifikát aplikace (AC)**: Jedná se o soubor CER/.der, který obsahuje veřejný klíč, který operační systém používá k šifrování některých datových částek. Media Services potřebuje vědět o tom, protože je vyžadovánpřehrávačem. Služba doručování klíčů ji dešifruje pomocí odpovídajícího soukromého klíče.

* Chcete-li přehrát šifrovaný stream FairPlay, získejte nejprve skutečný ASK a poté vygenerujte skutečný certifikát. Tento proces vytváří všechny tři části:

  * Soubor .der
  * Soubor .pfx
  * heslo pro .pfx

## <a name="fairplay-and-player-apps"></a>FairPlay a aplikace pro hráče

Když je váš obsah šifrován pomocí **Apple FairPlay**, jednotlivé ukázky videa a zvuku jsou šifrovány pomocí režimu **AES-128 CBC.** **FairPlay Streaming** (FPS) je integrován do operačních systémů zařízení s nativní podporou v systémech iOS a Apple TV. Safari na OS X umožňuje FPS pomocí podpory rozhraní Encrypted Media Extensions (EME).

Azure Media Player také podporuje přehrávání FairPlay. Další informace naleznete v [dokumentaci k programu Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html).

Pomocí sady iOS SDK můžete vyvíjet vlastní aplikace přehrávače. Abyste mohli přehrávat obsah FairPlay, musíte implementovat protokol výměny licencí. Tento protokol není společností Apple určen. Je na každé aplikaci, jak odeslat žádosti o doručení klíčů. Služba doručování klíčů Media Services FairPlay očekává, že SPC přijde jako postová zpráva s kódem www-form-url v následujícím formuláři:

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>Příklad konfigurace FairPlay .NET

K konfiguraci licencí FairPlay můžete použít rozhraní API media services. Když se hráč pokusí přehrát váš obsah chráněný fairplayem, je odeslána žádost do služby doručování licencí, aby byla licence získáním. Pokud licenční služba žádost schválí, vydá licenci. Odešle se klientovi a slouží k dešifrování a přehrání zadaného obsahu.

> [!NOTE]
> Obvykle byste chtěli nakonfigurovat možnosti zásad FairPlay pouze jednou, protože budete mít pouze jednu sadu certifikace a ASK.

Následující příklad používá ke konfiguraci licence sady [Media Services .NET SDK.](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models?view=azure-dotnet)

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
        RentalDuration = 2249
    };

    return fairPlayConfiguration;
}
```

## <a name="next-steps"></a>Další kroky

Podívejte se, jak [chránit pomocí DRM](protect-with-drm.md)
