---
title: Konfigurace zásad autorizace klíče obsahu pomocí portálu Azure | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak nakonfigurovat zásady autorizace pro klíč obsahu.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 8580bafd4d68ef6567b09fefcaa01c682ae2cafe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74968786"
---
# <a name="configure-a-content-key-authorization-policy"></a>Konfigurace zásad autorizace klíče obsahu
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Přehled
 Pomocí mediálních služeb Azure můžete doručovat datové proudy MPEG-DASH, Smooth Streaming a HTTP Live Streaming (HLS) chráněné standardem Advanced Encryption Standard (AES) pomocí 128bitových šifrovacích klíčů nebo [správy digitálních práv PlayReady (DRM).](https://www.microsoft.com/playready/overview/) Pomocí služby Media Services můžete také dodávat datové proudy DASH šifrované pomocí widevine DRM. Technologie PlayReady i Widevine jsou šifrované podle specifikace Common Encryption (ISO/IEC CENC 23001-7).

Služba Media Services také poskytuje službu doručování klíčů a licencí, ze které mohou klienti získat klíče AES nebo licence PlayReady/Widevine k přehrávání šifrovaného obsahu.

Tento článek ukazuje, jak pomocí portálu Azure nakonfigurovat zásady autorizace klíče obsahu. Klíč lze později použít k dynamickému šifrování obsahu. V současné době můžete šifrovat formáty HLS, MPEG-DASH a Smooth Streaming. Nelze šifrovat progresivní stahování.

Když přehrávač požaduje datový proud, který je nastaven na dynamicky šifrovaný, používá služba Media Services nakonfigurovaný klíč k dynamickému šifrování obsahu pomocí šifrování AES nebo DRM. K dešifrování streamu si přehrávač vyžádá klíč ze služby doručování klíčů. Chcete-li zjistit, zda je uživatel oprávněn získat klíč, služba vyhodnotí zásady autorizace, které jste zadali pro klíč.

Pokud plánujete mít více klíčů obsahu nebo chcete zadat jinou adresu URL služby doručování klíčů nebo licencí než službu doručování klíčů Mediální služby, použijte rozhraní API media services .NET SDK nebo REST. Další informace naleznete v tématu:

* [Konfigurace zásad autorizace klíče obsahu pomocí sady Media Services .NET SDK](media-services-dotnet-configure-content-key-auth-policy.md)
* [Konfigurace zásad autorizace klíče obsahu pomocí rozhraní REST API služby Media Services](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Platí některé aspekty
* Po vytvoření účtu Media Services se do vašeho účtu přidá výchozí koncový bod streamování ve stavu Zastaveno. Chcete-li spustit streamování obsahu a využít výhod dynamického balení a dynamického šifrování, musí být koncový bod streamování ve stavu Spuštěno. 
* Datový zdroj musí obsahovat sadu souborů MP4 s adaptivním datovým tokem nebo adaptivní datové rychlosti Plynulé streamování. Další informace naleznete [v tématu Kódování datového zdroje](media-services-encode-asset.md).
* Služba doručování klíčů ukládá do mezipaměti ContentKeyAuthorizationPolicy a jeho související objekty (možnosti zásad a omezení) po dobu 15 minut. Můžete vytvořit ContentKeyAuthorizationPolicy a určit použití omezení tokenu, otestovat a potom aktualizovat zásady na otevřené omezení. Tento proces trvá přibližně 15 minut, než se zásada přepne na otevřenou verzi.
* Koncový bod streamování mediální služby nastaví hodnotu hlavičky CORS Access-Control-Allow-Origin\*v odpovědi kontroly před výstupem jako zástupný znak " ". Tato hodnota funguje dobře s většinou hráčů, včetně Azure Media Player, Roku a JWPlayer a další. Někteří hráči, kteří používají soubor dash.js, však nefungují, protože s režimem pověření nastaveným na "zahrnout", XMLHttpRequest v jejich dash.js nepovoluje zástupný znak "\*" jako hodnotu Access-Control-Allow-Origin. Jako řešení tohoto omezení v dash.js, pokud hostujete klienta z jedné domény, media services můžete určit, že doména v záhlaví odpovědi kontroly před výstupem. Pro pomoc otevřete lístek podpory prostřednictvím portálu Azure.

## <a name="configure-the-key-authorization-policy"></a>Konfigurace zásad autorizace klíčů
Chcete-li nakonfigurovat zásady autorizace klíče, vyberte stránku **OCHRANA OBSAHU.**

Služba Media Services podporuje několik způsobů ověřování uživatelů, kteří požadují klíčové klíče. Zásady autorizace klíče obsahu mohou mít omezení autorizace otevření, tokenu nebo IP adresy. (IP lze nakonfigurovat pomocí rest nebo .NET SDK.)

### <a name="open-restriction"></a>Otevřené omezení
Otevřené omezení znamená, že systém doručí klíč každému, kdo požádá o klíč. Toto omezení může být užitečné pro účely testování.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Omezení tokenu
Chcete-li zvolit zásadu s omezeným přístupem tokenu, vyberte tlačítko **TOKEN.**

Zásady omezené token musí být doprovázentoken vydaný službou tokenu zabezpečení (STS). Media Services podporuje tokeny ve formátu jednoduchého webového tokenu ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) a JSON Web Token (JWT). Další informace naleznete v tématu [JWT authentication](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Mediální služby neposkytují STS. Můžete vytvořit vlastní STS vydávat tokeny. Služba STS musí být nakonfigurována tak, aby vytvořila token podepsaný zadaným klíčem a vydala deklarace identity, které jste zadali v konfiguraci omezení tokenu. Pokud je token platný a deklarace identity v tokenu odpovídají deklaracím nakonfigurovaným pro klíč obsahu, vrátí služba doručování klíčů Mediální služby šifrovací klíč klientovi.

Při konfiguraci zásad s omezeným přístupem tokenu je nutné zadat primární ověřovací klíč, vystavitel a parametry publika. Primární ověřovací klíč obsahuje klíč, se kterým byl token podepsán. Vystavitel je STS, který vydává token. Cílová skupina (někdy označovaná jako obor) popisuje záměr tokenu nebo prostředku, ke kterému token autorizuje přístup. Služba doručování klíčů Mediální služby ověří, zda tyto hodnoty v tokenu odpovídají hodnotám v šabloně.

### <a name="playready"></a>PlayReady
Při ochraně obsahu pomocí služby PlayReady je jednou z věcí, které je třeba zadat v zásadách autorizace, řetězec XML, který definuje šablonu licence PlayReady. Ve výchozím nastavení jsou nastaveny následující zásady:

    <PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
          <LicenseTemplates>
            <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
              <ContentKey i:type="ContentEncryptionKeyFromHeader" />
              <LicenseType>Nonpersistent</LicenseType>
              <PlayRight>
                <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
              </PlayRight>
            </PlayReadyLicenseTemplate>
          </LicenseTemplates>
        </PlayReadyLicenseResponseTemplate>

Můžete vybrat tlačítko **XML zásad importu** a poskytnout jiný kód XML, který odpovídá schématu XML definovanému v [přehledu šablony licence PlayReady služby Media Services](media-services-playready-license-template-overview.md).

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="next-steps"></a>Další kroky
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

