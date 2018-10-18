---
title: Azure Media Services a Apple FairPlay licenční podpory | Dokumentace Microsoftu
description: Toto téma s přehledem Apple FairPlay licenční požadavky a konfigurace.
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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 19f382de3ffe11253005f5fa2874ee817abaeed3
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376750"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Apple FairPlay licenční požadavky a konfigurace 

Azure Media Services umožňuje šifrování obsahu HLS pomocí **Apple FairPlay** (AES-128 CBC). Služba Media Services také poskytuje službu k doručování licencí FairPlay. Když hráč pokusí k přehrávání obsahu s ochranou FairPlay, žádost přijde na službu doručování licencí licenci. Pokud licenční služby schválí, problémy s licencí, které se odešle klientovi a slouží k dešifrování a přehrát zadaný obsah.

Služba Media Services také poskytuje rozhraní API, která vám umožní nakonfigurujte své licence FairPlay. Toto téma popisuje požadavky na licence FairPlay a ukazuje, jak nakonfigurovat **FairPlay** licence pomocí rozhraní API Sercies média. 

## <a name="requirements"></a>Požadavky

Vyžadují se následující věci při použití služby Media Services k šifrování obsahu HLS pomocí **Apple FairPlay** a používat k doručování licencí FairPlay Media Services:

* Zaregistrovat s [programu pro vývoj Apple](https://developer.apple.com/).
* Apple vyžaduje k získání vlastníka obsahu [balíček pro nasazení](https://developer.apple.com/contact/fps/). Stav již implementováno klíč zabezpečení modulu (KSM) pomocí služby Media Services, a že žádáte finálním balíčku snímků za Sekundu. Existují pokyny ve finálním balíčku snímků za Sekundu pro generování certifikace a získat klíč tajný klíč aplikace (AKS). Konfigurace FairPlay použijete dotaz.
* Na straně doručování klíčů/licencí Media Services musí být nastaveny následující věci:

    * **Certifikát aplikace (AC)**: Toto je soubor .pfx, který obsahuje privátní klíč. Tento soubor vytvořte a šifrovat pomocí hesla. Skupiny soubor .pfx, být ve formátu Base64.

        Následující kroky popisují, jak generovat soubor certifikátu .pfx pro FairPlay:

        1. Nainstalujte OpenSSL z https://slproweb.com/products/Win32OpenSSL.html.

            Přejděte do složky, kde jsou FairPlay certifikát a dalších souborů od společnosti Apple.
        2. V příkazovém řádku spusťte následující příkaz. Tento soubor .cer převede na soubor .pem.

            "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
        3. V příkazovém řádku spusťte následující příkaz. Soubor .pem tím převedete do souboru .pfx s privátním klíčem. Heslo pro soubor .pfx se zobrazí výzva, pomocí OpenSSL.

            "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12-export - out FairPlay out.pfx-inkey privatekey.pem – v file:privatekey-pem-pass.txt - passin FairPlay out.pem
            
    * **Heslo certifikátu aplikace**: heslo pro vytvoření souboru .pfx.
    * **Požádejte**: Tento klíč je dostanete při generování certifikace prostřednictvím portálu pro vývojáře Apple. Jednotlivé vývojové týmy obdrží jedinečný položit dotaz. Uložit kopii zadání a uložte ho na bezpečném místě. Musíte nakonfigurovat položit dotaz jako FairPlayAsk pomocí služby Media Services.
    
* Snímků za Sekundu na straně klienta je nutné nastavit následující věci:

  * **Certifikát aplikace (AC)**: Toto je.cer/.der soubor, který obsahuje veřejný klíč, který operační systém používá k šifrování některé datové části. Služba Media Services je potřeba vědět, protože je vyžadováno přehrávač. Služba doručování klíčů dešifruje ji pomocí odpovídajícího soukromého klíče.

* Pro přehrávání šifrovaného datového proudu FairPlay, získat první skutečné položit dotaz a pak vygenerovat skutečný certifikát. Tento proces vytvoří všechny tři části:

  * souboru .der
  * soubor .pfx
  * heslo pro soubor .pfx

## <a name="fairplay-and-player-apps"></a>Aplikace pro FairPlay a player

Pokud je váš obsah zašifrovaný pomocí **Apple FairPlay**, jednotlivé video a audiostreamů ukázky jsou šifrované pomocí **AES-128 CBC** režimu. **FairPlay Streaming** (FPS) je integrované do operačních systémů zařízení s nativní podporou v Iosu a Apple TV. Safari v systému OS X umožňuje snímků za Sekundu s využitím podpory rozhraní rozšíření eme (Encrypted Media Extensions).

Azure Media Player podporuje také FairPlay přehrávání. Další informace najdete v tématu [dokumentace ke službě Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html).

Aplikace přehrávače můžete vyvíjet s použitím sady iOS SDK. Aby bylo možné k přehrávání obsahu s FairPlay, budete muset implementovat protokol exchange licence. Tento protokol není zadán společností Apple. Je až po každé aplikaci, jak odesílat žádosti o doručení klíče. Doručení klíče služby Media Services FairPlay očekává, že certifikát SPC pocházet jako zpráva www-form-url kódovaného příspěvku. v následujícím tvaru:

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>Příklad .NET konfigurace FairPlay

Rozhraní API služby Media Services můžete použít ke konfiguraci licencí FairPlay. Když hráč se snaží přehrávání obsahu s ochranou FairPlay, žádost přijde na službu doručování licencí k získání licence. Pokud licenční služby schválí, služba vydá licence. Je odeslat klientovi a slouží k dešifrování a přehrát zadaný obsah.

> [!NOTE]
> Obvykle byste to nakonfigurovat možnosti zásad FairPlay pouze jednou, protože budete používat jen jednu sadu certifikace a nazvanou ZEPTEJTE se.

Následující příklad používá [Media Services .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models?view=azure-dotnet) konfigurace licence.

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

## <a name="next-steps"></a>Další postup

Podívejte se na tom, jak [chránit pomocí DRM](protect-with-drm.md)