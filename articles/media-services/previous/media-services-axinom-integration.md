---
title: Použití Axinom k doručování licencí Widevine Azure Media Services | Microsoft Docs
description: Tento článek popisuje, jak můžete pomocí Azure Media Services (AMS) doručovat datový proud, který je dynamicky zašifrovaný pomocí AMS, pomocí PlayReady i Widevine několikanásobnou. Licence PlayReady pochází z Media Services licenční server PlayReady a licence Widevine se doručují prostřednictvím licenčního serveru Axinom.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: willzhan
ms.reviewer: Mingfeiy;rajputam;Juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 532e982b02bb35074370db45fec8cc42a898c83e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015502"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Distribuce licencí Widevine pro Azure Media Services pomocí Axinomu

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Přehled
Azure Media Services (AMS) se přidala dynamická ochrana Google Widevine (podrobnosti najdete na [blogu Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) ). Kromě toho Azure Media Player (AMP) také přidal podporu Widevine (podrobnosti naleznete v [dokumentu amp](https://amp.azure.net/libs/amp/latest/docs/) ). Jedná se o zásadní úspěch při streamování obsahu, který je chráněný CENCem s využitím více nativních DRM (PlayReady a Widevine) v moderních prohlížečích vybavených pomocí programu MSE a EME.

Počínaje verzí Media Services .NET SDK verze 3.5.2 umožňuje Media Services nakonfigurovat šablonu licence Widevine a získat licence Widevine. Licence Widevine vám také mohou doručit následující partneři AMS : [Axinom](https://www.axinom.com), [EZDRM](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/).

Tento článek popisuje, jak integrovat a testovat licenční server Widevine spravovaný pomocí Axinom. Konkrétně to pokrývá:  

* Konfigurace dynamického Common Encryption s využitím více DRM (PlayReady a Widevine) s odpovídajícími adresami URL pro získání licence;
* Generování tokenu JWT za účelem splnění požadavků na licenční server;
* Vývoj aplikace Azure Media Player, která zpracovává získání licencí pomocí ověřování pomocí tokenu JWT;

Úplný systém a tok klíče obsahu, ID klíče, počátečního klíče, token JTW a jeho deklarace identity můžou být nejlépe popsané v následujícím diagramu:

![POMLČKy a CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Content Protection
Informace o konfiguraci dynamické ochrany a zásad doručování klíčů najdete na blogu Mingfei: [jak nakonfigurovat Widevine balení pomocí Azure Media Services](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Dynamickou ochranu CENC můžete nakonfigurovat pomocí více DRM pro PŘERUŠOVANé streamování s oběma následujícími možnostmi:

1. Ochrana PlayReady pro Microsoft Edge a IE11, která by mohla mít omezení autorizace tokenu. Zásady omezeného tokenu musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS), jako je například Azure Active Directory;
2. Widevine Protection pro Chrome, může vyžadovat ověření tokenu s tokenem vydaným jinou službou STS. 

Důvody, proč Azure Active Directory nelze použít jako STS pro licenční server Widevine pro Axinom, najdete v části [generování tokenu JWT](media-services-axinom-integration.md#jwt-token-generation) .

### <a name="considerations"></a>Požadavky
1. K vygenerování klíče obsahu pro konfiguraci služby doručování klíčů je nutné použít Axinom zadaného klíčového počátečního klíče (8888000000000000000000000000000000000000) a vygenerované nebo vybrané ID klíče. Axinom License Server vystavuje všechny licence obsahující klíče obsahu založené na stejném počátečním klíči, který je platný pro testování i pro produkční prostředí.
2. Adresa URL pro získání licence Widevine pro testování: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense) . Jsou povoleny protokoly HTTP a HTTS.

## <a name="azure-media-player-preparation"></a>Příprava Azure Media Player
AMP v 1.4.0 podporuje přehrávání obsahu AMS, který je dynamicky zabalený pomocí PlayReady i Widevine DRM.
Pokud Widevine License Server nevyžaduje ověření tokenu, nemusíte nic dalšího udělat, abyste otestovali obsah s POMLČKou chráněnou pomocí Widevine. Například tým AMP poskytuje jednoduchou [ukázku](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html), kde se můžete podívat, jak funguje v Microsoft Edge a IE11 s PlayReady a Chrome s Widevine.
Widevine License Server, který poskytuje Axinom, vyžaduje ověření tokenu JWT. Token JWT se musí odeslat pomocí žádosti o licenci prostřednictvím hlavičky HTTP "X-AxDRM-Message". Pro tento účel je nutné přidat následující JavaScript na webové stránce hostující AMP před nastavením zdroje:

```html
<script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>
```

Zbytek kódu AMP je standardní rozhraní AMP API jako [v dokumentu amp](https://amp.azure.net/libs/amp/latest/docs/).

Výše uvedený JavaScript pro nastavení vlastní autorizační hlavičky je stále krátkodobým přístupem před vydáním oficiálního dlouhodobého přístupu v AMP.

## <a name="jwt-token-generation"></a>Generování tokenu JWT
Licenční server Axinom Widevine pro testování vyžaduje ověření tokenu JWT. Kromě toho jedna z deklarací v tokenu JWT je komplexní typ objektu místo primitivního datového typu.

Služba Azure AD bohužel může vystavovat pouze tokeny JWT s primitivními typy. Podobně .NET Framework rozhraní API (System. IdentityModel. Tokens. SecurityTokenHandler a JwtPayload) umožňuje jako deklarace identity zadat jenom komplexní typ objektu. Deklarace identity jsou však stále serializovány jako řetězec. Proto nemůžeme pro vygenerování tokenu JWT pro žádost o licenci Widevine použít žádné z těchto dvou.

Sheehan [balíček NuGet pro JWT](https://www.nuget.org/packages/JWT) splňuje požadavky, takže budeme používat tento balíček NuGet.

Níže je uvedený kód pro vygenerování tokenu JWT s potřebnými deklaracemi, jak vyžaduje licenční server Axinom Widevine pro testování:

```csharp
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
```

Licenční server Axinom Widevine

```xml
<add key="ax:laurl" value="https://drm-widevine-licensing.axtest.net/AcquireLicense" />
<add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
<add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
<add key="ax:keyseed" value="8888000000000000000000000000000000000000" />
```

### <a name="considerations"></a>Požadavky
1. I když služba pro doručování licencí PlayReady v AMS vyžaduje "Bearer =" před ověřovacím tokenem, Axinom licenční server Widevine ho nepoužívá.
2. Axinom Communications Key se používá jako podpisový klíč. Klíč je řetězec v šestnáctkové soustavě, ale musí být zpracován jako série bajtů, nikoli String při kódování. Toho je dosaženo metodou ConvertHexStringToByteArray.

## <a name="retrieving-key-id"></a>Načítá se ID klíče.
Možná jste si všimli, že v kódu pro vygenerování tokenu JWT je vyžadováno ID klíče. Vzhledem k tomu, že je nutné token JWT před načtením programu AMP Player připravit, je nutné načíst ID klíče, aby bylo možné vygenerovat token JWT.

Je samozřejmě více způsobů, jak získat blokování ID klíče. Například jedna může ukládat ID klíče spolu s metadaty obsahu v databázi. Nebo můžete načíst ID klíče z POMLČKy MPD (s popisem prezentace multimédií). Níže uvedený kód je určen pro druhý.

```csharp
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
```

## <a name="summary"></a>Souhrn

S nejnovějším přidáním podpory Widevine do obou Azure Media Services Content Protection a Azure Media Player je možné implementovat streamování POMLČKy + multi-Native-DRM (PlayReady + Widevine) s licenční službou PlayReady v AMS i serverem Widevine License Server od Axinom pro následující moderní prohlížeče:

* Chrome
* Microsoft Edge ve Windows 10
* IE 11 v Windows 8.1 a Windows 10
* Aplikace Firefox (Desktop) i Safari na Macu (ne iOS) jsou podporované taky přes Silverlight a stejnou adresou URL s Azure Media Player

Následující parametry jsou vyžadovány ve zkráceném řešení, které využívá licenční server Axinom Widevine. S výjimkou ID klíče jsou zbývající parametry poskytovány pomocí Axinom na základě jejich nastavení serveru Widevine.

| Parametr | Jak se používá |
| --- | --- |
| ID komunikačního klíče |Musí být zahrnuté jako hodnota deklarace com_key_id v tokenu JWT (viz [Tato](media-services-axinom-integration.md#jwt-token-generation) část). |
| Komunikační klíč |Musí být použit jako podpisový klíč tokenu JWT (viz [Tato](media-services-axinom-integration.md#jwt-token-generation) část). |
| Klíčová semena |Musí se použít ke generování klíče obsahu s jakýmkoli daným ID klíče obsahu (viz  [Tato](media-services-axinom-integration.md#content-protection) část). |
| Adresa URL pro získání licence Widevine |Se musí použít při konfiguraci zásad doručení assetů pro PŘERUŠOVANé streamování (viz  [Tato](media-services-axinom-integration.md#content-protection) část). |
| ID klíče obsahu |Musí být zahrnuté jako součást hodnoty nároku na zprávu nároku tokenu JWT (viz v [této](media-services-axinom-integration.md#jwt-token-generation) části). |

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Poděkování
Chtěli bychom potvrdit následující lidi, kteří přispěli k vytváření tohoto dokumentu: Kristjan jõgi of Axinom, Mingfei Yan a Amit Rajput.

