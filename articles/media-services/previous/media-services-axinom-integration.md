---
title: Použití axinomu k doručování licencí Widevine do služby Azure Media Services | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak můžete pomocí Azure Media Services (AMS) dodat datový proud, který je dynamicky šifrována AMS s PlayReady a Widevine DRM. Licence PlayReady pochází z licenčního serveru Media Services PlayReady a licence Widevine je dodávána licenčním serverem Axinom.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: Mingfeiy;rajputam;Juliako
ms.openlocfilehash: 2ec3276b9b02c29b80d46e5fd31298c909857182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197160"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Distribuce licencí Widevine pro Azure Media Services pomocí Axinomu 
> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Přehled
Azure Media Services (AMS) přidal google widevine dynamickou ochranu (podrobnosti najdete na [blogu Mingfei).](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) Kromě toho Azure Media Player (AMP) také přidal podporu Widevine (podrobnosti viz [dokument AMP).](https://amp.azure.net/libs/amp/latest/docs/) To je významný úspěch v streamování obsahu DASH chráněného CENC s multi-nativní-DRM (PlayReady a Widevine) na moderních prohlížečích vybavených MSE a EME.

Počínaje sadou Media Services .NET SDK verze 3.5.2 umožňuje služba Media Services konfigurovat šablonu licence Widevine a získat licence Widevine. Licence Widevine vám také mohou doručit následující partneři AMS : [Axinom](https://www.axinom.com), [EZDRM](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/).

Tento článek popisuje, jak integrovat a testovat licenční server Widevine spravovaný společností Axinom. Konkrétně se vztahuje na:  

* Konfigurace dynamického společného šifrování s multi-DRM (PlayReady a Widevine) s odpovídajícími adresy URL získávání licencí;
* Generování tokenu JWT za účelem splnění požadavků licenčního serveru;
* Vývoj aplikace Azure Media Player, která zpracovává získávání licencí pomocí ověřování tokenů JWT;

Celý systém a tok klíče obsahu, ID klíče, osiva klíče, tokenu JTW a jeho deklarací lze nejlépe popsat pomocí následujícího diagramu:

![DASH a CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Content Protection
Informace o konfiguraci dynamické ochrany a zásad doručování klíčů najdete v blogu Mingfei: [Jak nakonfigurovat balení Widevine pomocí mediálních služeb Azure](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Dynamickou ochranu CENC můžete nakonfigurovat pomocí multi-DRM pro streamování DASH, které má obě následující:

1. PlayReady ochrana pro Microsoft Edge a IE11, které by mohly mít omezení autorizace tokenu. Zásady s omezeným přístupem tokenu musí být doprovázeny tokenem vydaným službou Secure Token Service (STS), jako je například služba Azure Active Directory;
2. Widevine ochrana pro Chrome, může vyžadovat ověření tokenu s tokenem vydaným jiným STS. 

Informace o tom, proč službu Azure Active Directory nelze použít jako službu STS pro licenční server Widevine společnosti Axinom, najdete v části [Generování tokenů](media-services-axinom-integration.md#jwt-token-generation) JWT.

### <a name="considerations"></a>Požadavky
1. Je nutné použít osiva klíče axinom (8888000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000 Licenční server Axinom vydává všechny licence obsahující klíče obsahu založené na stejném osivu klíče, které je platné jak pro testování, tak pro produkční prostředí.
2. Adresa URL pro získání licence [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense)Widevine pro testování: . Http a HTTS jsou povoleny.

## <a name="azure-media-player-preparation"></a>Příprava přehrávače médií Azure
AMP v1.4.0 podporuje přehrávání obsahu AMS, který je dynamicky zabalen s playready a widevine DRM.
Pokud licenční server Widevine nevyžaduje ověřování tokenů, není nic dalšího, co musíte udělat pro testování obsahu DASH chráněného Widevine. Například tým AMP poskytuje jednoduchou [ukázku](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html), kde můžete vidět, že funguje v Microsoft Edge a IE11 s PlayReady a Chrome s Widevine.
Licenční server Widevine poskytovaný společností Axinom vyžaduje ověřování tokenů JWT. Token JWT musí být odeslán s žádostí o licenci prostřednictvím hlavičky HTTP "X-AxDRM-Message". Pro tento účel je třeba přidat následující javascript na webové stránce hosting AMP před nastavením zdroje:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

Zbytek kódu AMP je standardní AMP API jako v dokumentu AMP [zde](https://amp.azure.net/libs/amp/latest/docs/).

Výše uvedený javascript pro nastavení vlastní autorizační hlavičky je stále krátkodobý přístup před oficiálním dlouhodobým přístupem v AMP je propuštěn.

## <a name="jwt-token-generation"></a>Generování tokenů JWT
Licenční server Axinom Widevine pro testování vyžaduje ověřování tokenů JWT. Kromě toho jeden z deklarací v tokenu JWT je typu komplexní objekt namísto primitivní datový typ.

Bohužel Azure AD můžete vydat pouze tokeny JWT s primitivní typy. Podobně rozhraní .NET Framework API (System.IdentityModel.Tokens.SecurityTokenHandler a JwtPayload) umožňuje pouze zadávat komplexní typ objektu jako deklarace identity. Deklarace identity jsou však stále serializovány jako řetězec. Proto nemůžeme použít některý z těchto dvou pro generování tokenu JWT pro požadavek licence Widevine.

[Balíček JWT NuGet](https://www.nuget.org/packages/JWT) společnosti John Sheehan splňuje potřeby, takže budeme používat tento balíček NuGet.

Níže je kód pro generování Token JWT s potřebnými deklaracemi, jak je požadováno Axinom Widevine licenční server pro testování:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.IdentityModel.Tokens;
    using System.IdentityModel.Protocols.WSTrust;
    using System.Security.Claims;

    namespace OpenIdConnectWeb.Utils
    {
        public class JwtUtils
        {
            //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
            public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
            {
                byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.

                var payload = new Dictionary<string, object>()
                             {
                                 { "version", 1 },
                                 { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                                 { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
                             };

                string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);

                return token;
            }

            //convert hex string to byte[]
            public static byte[] ConvertHexStringToByteArray(string hexString)
            {
                if (hexString.Length % 2 != 0)
                {
                    throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
                }

                byte[] HexAsBytes = new byte[hexString.Length / 2];
                for (int index = 0; index < HexAsBytes.Length; index++)
                {
                    string byteValue = hexString.Substring(index * 2, 2);
                    HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
                }

                return HexAsBytes;
            }

        }  

    }  

Licenční server Axinom Widevine

    <add key="ax:laurl" value="https://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

### <a name="considerations"></a>Požadavky
1. I když služba doručování licencí AMS PlayReady vyžaduje "Bearer=" před ověřovacítoken, licenční server Axinom Widevine jej nepoužívá.
2. Komunikační klíč Axinom se používá jako podpisový klíč. Klíč je šestnáctkový řetězec, ale musí být považován za řadu bajtů není řetězec při kódování. Toho je dosaženo metodou ConvertHexStringToByteArray.

## <a name="retrieving-key-id"></a>Načítání ID klíče
Možná jste si všimli, že v kódu pro generování tokenu JWT je vyžadováno ID klíče. Vzhledem k tomu, že token JWT musí být připraven před načtením přehrávače AMP, musí být načteno ID klíče, aby bylo možné generovat token JWT.

Samozřejmě existuje několik způsobů, jak získat klíč ID. Například jeden může ukládat ID klíče spolu s metadaty obsahu v databázi. Nebo můžete načíst ID klíče ze souboru DASH MPD (Media Presentation Description). Níže uvedený kód je pro druhé.

    //get key_id from DASH MPD
    public static string GetKeyID(string dashUrl)
    {
        if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
        {
            dashUrl += "(format=mpd-time-csf)";
        }

        XPathDocument objXPathDocument = new XPathDocument(dashUrl);
        XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
        XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
        objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
        objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
        objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
        objXmlNamespaceManager.AddNamespace("xsi",  "https://www.w3.org/2001/XMLSchema-instance");
        objXmlNamespaceManager.PushScope();

        XPathNodeIterator objXPathNodeIterator;
        objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);

        string key_id = string.Empty;
        if (objXPathNodeIterator.MoveNext())
        {
            key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
        }

        return key_id;
    }

## <a name="summary"></a>Souhrn

S nejnovějším přírůstkem podpory Widevine v azure media services content protection a Azure Media Player jsme schopni implementovat streamování DASH + Multi-native-DRM (PlayReady + Widevine) s licenční službou PlayReady v AMS i widevine licenci server od společnosti Axinom pro následující moderní prohlížeče:

* Chrome
* Microsoft Edge ve Windows 10
* IE 11 ve Windows 8.1 a Windows 10
* Firefox (Desktop) i Safari na Macu (ne iOS) jsou také podporovány přes Silverlight a stejnou adresu URL s Azure Media Player

Následující parametry jsou vyžadovány v mini-řešení využívající axinom Widevine licenční server. S výjimkou ID klíče jsou zbývající parametry poskytovány společností Axinom na základě nastavení serveru Widevine.

| Parametr | Jak se používá |
| --- | --- |
| ID komunikačního klíče |Musí být zahrnuta jako hodnota deklarace "com_key_id" v Token JWT (viz [tato](media-services-axinom-integration.md#jwt-token-generation) část). |
| Komunikační klíč |Musí být použit jako podpisový klíč tokenu JWT (viz [tato](media-services-axinom-integration.md#jwt-token-generation) část). |
| Klíčové osivo |Musí být použit ke generování klíče obsahu s libovolným ID klíče obsahu (viz [tato](media-services-axinom-integration.md#content-protection) část). |
| Adresa URL akvizice licence Widevine |Musí být použit při konfiguraci zásad doručování datových zdrojů pro streamování DASH (viz [tato](media-services-axinom-integration.md#content-protection) část). |
| ID klíče obsahu |Musí být zahrnuty jako součást hodnoty nároku nároku na zprávu o jwt token (viz [tato](media-services-axinom-integration.md#jwt-token-generation) část). |

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Poděkování
Rádi bychom ocenili následující lidi, kteří přispěli k vytvoření tohoto dokumentu: Kristjan Jõgi z Axinomu, Mingfei Yan a Amit Rajput.

