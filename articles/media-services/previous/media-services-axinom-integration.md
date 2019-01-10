---
title: Doručování licencí Widevine pro Azure Media Services pomocí Axinomu | Dokumentace Microsoftu
description: Tento článek popisuje, jak můžete pomocí Azure Media Services (AMS) k zajištění datového proudu, která je dynamicky šifrovat pomocí AMS pomocí technologie PlayReady a Widevine technologiemi DRM. Licence PlayReady pochází ze serveru pro správu licencí Media Services PlayReady a licencování Widevine se doručí Axinom licenční server.
services: media-services
documentationcenter: ''
author: willzhan
manager: cfowler
editor: ''
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: willzhan;Mingfeiy;rajputam;Juliako
ms.openlocfilehash: d269818e82261c51b63379bb41f69efdc21de18a
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191254"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Distribuce licencí Widevine pro Azure Media Services pomocí Axinomu
> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Přehled
Azure Media Services (AMS) přidala dynamická ochrany Google Widevine (viz [Mingfei na blogu](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) podrobnosti). Navíc Azure Media Player (AMP) má také přidanou podporu Widevine (viz [AMP dokumentu](http://amp.azure.net/libs/amp/latest/docs/) podrobnosti). Toto je hlavní splnění ve streamování DASH obsah chráněný šifrování CENC s více-native variant DRM (PlayReady a Widevine) pro moderní prohlížeče vybavené MSE a EME.

Počínaje Media Services .NET SDK verze 3.5.2, Media Services umožňuje konfigurovat šablonu licence Widevine a získání licencí Widevine. Můžete také použít následující partneři AMS při distribuci licencí Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

Tento článek popisuje, jak tak integrují a testují spravuje Axinom serveru pro správu licencí Widevine. Konkrétně zahrnuje:  

* Konfigurace běžného dynamického šifrování pomocí několika variant DRM (PlayReady a Widevine) pomocí adresy URL pro získání odpovídající licence;
* Chcete-li splnit požadavky na licenční server; generování tokenu JWT
* Vývoj aplikace v Azure Media Player, která zpracovává získání licence pomocí ověřování tokenu JWT;

Celý systém a tok klíče k obsahu, klíč ID, počáteční hodnota klíče, JTW token a jeho deklarace identity může být nejlepší popsal následující diagram:

![DASH a šifrování CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Content Protection
Pro konfiguraci dynamické ochrany a zásad doručení klíče, podrobnosti najdete na blogu od Mingfei: [Konfigurace balení Widevine pomocí služby Azure Media Services](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Můžete nakonfigurovat dynamické ochranu CENC s více technologiemi DRM pro streamování, pokud máte obě z následujících DASH:

1. Ochrana technologií PlayReady pro Microsoft Edge a IE11, která by mohla mít omezení autorizační token. Zásady omezení tokenem musí být doplněny tokenem vydaným podle zabezpečení Token služby (STS), jako je Azure Active Directory;
2. Widevine ochrany pro Chrome, se vyžadují ověřování pomocí tokenu s tokenem vydaným službou tokenů zabezpečení jiného. 

Zobrazit [generování tokenů JWT](media-services-axinom-integration.md#jwt-token-generation) části Proč nelze použít Azure Active Directory jako služba STS na Axinom Widevine licenčního serveru.

### <a name="considerations"></a>Požadavky
1. Je nutné použít Axinom zadaná počáteční hodnota klíče (8888000000000000000000000000000000000000) a vygenerovaný nebo vybrané klíče ID k vygenerování klíče k obsahu pro konfiguraci služby doručení klíče. Všechny licence obsahující obsahu klíče založené na stejné klíče jádro, který je platný pro testování a produkci vydá Axinom licenční server.
2. URL pro získání licence Widevine pro testování: [ https://drm-widevine-licensing.axtest.net/AcquireLicense ](https://drm-widevine-licensing.axtest.net/AcquireLicense). HTTP a HTTS jsou povoleny.

## <a name="azure-media-player-preparation"></a>Příprava Azure Media Player
AMP v1.4.0 podporuje přehrávání obsahu AMS, která je dynamicky zabalený pomocí technologie PlayReady a Widevine DRM.
Pokud serveru pro správu licencí Widevine nevyžaduje ověření tokenu, neexistuje žádné další, že musíte udělat testování DASH obsah chráněný pomocí Widevine. Příklad, tým AMP poskytuje jednoduchý [ukázka](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html), kde můžete sledovat, jak funguje v Microsoft Edge a IE11 pomocí technologie PlayReady a Chrome s technologií Widevine.
Server licence Widevine poskytovaný platformou Axinom vyžaduje ověřování pomocí tokenu JWT. JWT token musí odeslat požadavek na licenční prostřednictvím HTTP hlavičce "X-AxDRM-Message". K tomuto účelu budete muset přidat následující javascript na webové stránce hostování AMP před nastavením zdroje:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

Zbývající část kódu AMP je standardní rozhraní API AMP stejně jako v dokumentu AMP [tady](http://amp.azure.net/libs/amp/latest/docs/).

Výše uvedené jazyka javascript pro vlastní autorizační hlavičky. nastavení je stále krátkodobé přístup před vydáním oficiální dlouhodobý přístup v knihovně AMP.

## <a name="jwt-token-generation"></a>Generování tokenů JWT
Server licence Widevine Axinomu pro testování vyžaduje ověřování pomocí tokenu JWT. Kromě toho jedna z deklarací identity v tokenu JWT je komplexní objekt typu místo primitivní datový typ.

Bohužel Azure AD můžete pouze vystavovat tokeny JWT s primitivními typy. Obdobně rozhraní .NET Framework API (System.IdentityModel.Tokens.SecurityTokenHandler a JwtPayload) pouze umožňuje zadat typ komplexní objekt jako deklarace identity. Deklarace identity jsou stále serializovat jako datový typ string. Proto jsme nemohou použít žádný z nich pro vygenerování tokenu JWT pro žádosti o licenci Widevine.

Jan Sheehan [balíček JWT NuGet](https://www.nuget.org/packages/JWT) splňuje potřeby, takže jsme se chystáte použít tento balíček NuGet.

Níže je kód pro generování tokenů JWT token s potřebné deklarace identity podle požadavků serveru pro správu licencí Axinom Widevine pro testování:

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

Serveru pro správu licencí Widevine Axinomu

    <add key="ax:laurl" value="http://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

### <a name="considerations"></a>Požadavky
1. I když službu doručování licencí AMS PlayReady vyžaduje "nosiče =" předcházející ověřovací token, serveru pro správu licencí Axinom Widevine nepoužije.
2. Klíč Axinom komunikace se používá jako podpisový klíč. Klíč je hexadecimální řetězec však musí být zpracována jako řadu bajtů není řetězec při kódování. Toho lze dosáhnout metodou ConvertHexStringToByteArray.

## <a name="retrieving-key-id"></a>Načítání ID klíče
Mohli jste si všimnout, že v kódu pro generování token JWT token, key ID je povinné. Token JWT token musí být připraveno před načítání AMP player klíče musí ID se má načíst, aby bylo možné generovat JWT token.

Samozřejmě existují několika způsoby, jak získat blokování klíč ID. Například může ukládat jeden klíč ID spolu s metadata obsahu v databázi. Nebo můžete načíst klíč ID ze souboru MPD pomlčka (popis prezentace média). Následující kód je k tomu.

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
        objXmlNamespaceManager.AddNamespace("xsi",  "http://www.w3.org/2001/XMLSchema-instance");
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
Uveďte nejnovější Widevine podpory v Azure Media Services Content Protection a Azure Media Player nejsme schopni implementovat streamování DASH + více-native variant DRM (PlayReady a Widevine) pomocí obou PlayReady licenční služby AMS a Widevine licencí Server z Axinom pro následující moderní prohlížeče:

* Chrome
* Microsoft Edge ve Windows 10
* IE 11 na Windows 8.1 a Windows 10
* (Desktop) Firefox a Safari v systému Mac (ne iOS) jsou také podporovány prostřednictvím programu Silverlight a stejnou adresu URL pomocí Azure Media Player

Ve zkrácené řešení využitím Axinomu Widevine licenčního serveru se vyžadují následující parametry. S výjimkou klíč ID, zbývající parametry jsou k dispozici v Axinom na základě svých nastavení serveru Widevine.

| Parametr | Jak se používá |
| --- | --- |
| ID klíče komunikace |Musí být zahrnut jako hodnota deklarace identity "com_key_id" v tokenu JWT (viz [to](media-services-axinom-integration.md#jwt-token-generation) části). |
| Klíč komunikace |Musíte použít jako podpisový klíč tokenu JWT (naleznete v tématu [to](media-services-axinom-integration.md#jwt-token-generation) části). |
| Počáteční hodnota klíče |Použije k vygenerování klíče k obsahu s obsahem, daný klíč ID (naleznete v tématu [to](media-services-axinom-integration.md#content-protection) části). |
| URL pro získání licence Widevine |Musí být použita v konfigurace zásad doručení assetu pro datové proudy DASH (viz [to](media-services-axinom-integration.md#content-protection) části). |
| ID klíče obsahu |Musí být zahrnut jako součást hodnoty deklarace identity nárok zpráva tokenu JWT (viz [to](media-services-axinom-integration.md#jwt-token-generation) části). |

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Potvrzení
Rádi bychom se na vědomí následující osob, které přispívají k vytvoření tohoto dokumentu: Kristjan Jõgi Axinom Mingfei Jan a Amitu Rajput.

