---
title: Nakonfigurujte zásady autorizace klíče obsahu pomocí portálu Azure | Microsoft Docs
description: Zjistěte, jak nakonfigurovat zásady autorizace pro klíč obsahu.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: juliako
ms.openlocfilehash: db0117b0b4ddee002fc69d71e78eca2b9008e4f6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="configure-a-content-key-authorization-policy"></a>Nakonfigurujte zásady autorizace klíče obsahu
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Přehled
 Azure Media Services můžete doručovat datové proudy MPEG-DASH, technologie Smooth Streaming a HTTP Live Streaming (HLS) chráněné pomocí Standard AES (Advanced Encryption) pomocí klíčů 128bitové šifrování nebo [PlayReady správy digitálních práv (DRM)](https://www.microsoft.com/playready/overview/). Pomocí služby Media Services můžete také doručovat datové proudy DASH šifrované pomocí Widevine DRM. Technologie PlayReady i Widevine jsou šifrované podle specifikace Common Encryption (ISO/IEC CENC 23001-7).

Media Services také poskytuje službu doručování klíč nebo licenci, ze kterého klienti získat klíče AES nebo licence PlayReady nebo Widevine přehrávání šifrovaný obsah.

Tento článek ukazuje, jak pomocí portálu Azure a nakonfigurujte zásady autorizace klíče obsahu. Klíč lze později dynamicky šifrovat obsah. V současné době můžete šifrovat formáty technologie Smooth Streaming, MPEG DASH a HLS. Nelze zašifrovat progresivní stahování.

Když přehrávač vyžádá datový proud, který je nastaven na dynamicky šifrovat, používá služba Media Services nakonfigurovaný klíč dynamicky šifrovat obsah pomocí šifrování AES nebo DRM. Přehrávač dešifrovat datový proud, požadavků klíč ze služby doručení klíče. Pokud chcete zjistit, zda je uživatel autorizovaný k získání klíče, služba vyhodnocuje zásady autorizace, které jste zadali pro klíč.

Pokud budete chtít mít několik klíčů obsah nebo chcete zadat adresu URL služby doručení klíč nebo licenci než doručení klíče služby Media Services, pomocí sady Media Services .NET SDK nebo REST API. Další informace naleznete v tématu:

* [Nakonfigurujte zásady autorizace klíče obsahu pomocí sady Media Services .NET SDK](media-services-dotnet-configure-content-key-auth-policy.md)
* [Nakonfigurujte zásady autorizace klíče obsahu pomocí Media Services REST API](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Splněny určité předpoklady
* Po vytvoření účtu Media Services se do vašeho účtu přidá výchozí koncový bod streamování ve stavu Zastaveno. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamické šifrování, koncový bod streamování musí být ve stavu "Spuštění". 
* Váš asset musí obsahovat sadu s adaptivní přenosovou rychlostí soubory MP4 s rychlostmi nebo soubory technologie Smooth Streaming s adaptivní přenosovou rychlostí. Další informace najdete v tématu [kódovat asset](media-services-encode-asset.md).
* Službu doručení klíče ukládá do mezipaměti ContentKeyAuthorizationPolicy a související objekty (Možnosti zásad a omezení) pro 15 minut. Můžete vytvořit ContentKeyAuthorizationPolicy a zadejte token omezení použijte, otestovat ji a aktualizujte zásady otevřete omezení. Tento proces trvá přibližně 15 minut před přepínače zásad otevřete verzi.
* Služba Media Services koncový bod streamování nastaví hodnotu hlavičky CORS Access-Control-Allow-Origin v předběžné odpovědi jako zástupný znak "\*". Tato hodnota se pracuje s většině přehrávačů, včetně Azure Media Player, Roku a JWPlayer a dalších. Však některé přehrávače, které používají dash.js nefungují, protože v režimu přihlašovací údaje nastavit na "zahrnout" XMLHttpRequest v jejich dash.js nepovoluje zástupného "\*" jako hodnotu Access-Control-Allow-Origin. Jako řešení pro toto omezení v dash.js pokud hostujete vašeho klienta z jedné domény, služba Media Services můžete zadat tuto doménu v hlavičce předběžných odpovědí. O pomoc otevřete lístek podpory prostřednictvím portálu Azure.

## <a name="configure-the-key-authorization-policy"></a>Nakonfigurujte zásady autorizace pro klíč
Konfigurace zásad autorizace pro klíč, vyberte **ochrana obsahu** stránky.

Služba Media Services podporuje více způsobů ověřování uživatelů, kteří žádají o klíč. Zásady autorizace klíče obsahu může mít otevřete, token nebo autorizace omezení IP adres. (IP můžete nakonfigurovat pomocí .NET SDK nebo REST.)

### <a name="open-restriction"></a>Otevřete omezení
Otevřete omezení znamená, že systém přináší klíč každý, kdo požádá klíče. Toto omezení může být užitečná pro účely testování.

![Funkce OpenPolicy][open_policy]

### <a name="token-restriction"></a>Omezení s tokenem
Chcete-li vybrat zásady omezení tokenem, vyberte **TOKENU** tlačítko.

Zásady omezení tokenem musí být doplněny tokenem vydaným pomocí služby tokenů zabezpečení (STS). Služba Media Services podporuje tokeny ve jednoduchého webového tokenu ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) a formátů JSON Web Token (JWT). Další informace najdete v tématu [ověřování tokenů JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Služba Media Services neposkytuje službu tokenů zabezpečení. Můžete vytvořit vlastní službu tokenů zabezpečení pro vydávání tokenů. Služba tokenů zabezpečení musí být nakonfigurované vytvořit token podepsané zadaný klíč a vystavování deklarací identity, které jste zadali v nastavení omezení s tokenem. Pokud token je platný a deklarace identity v tokenu shodují s těmi, nakonfigurované pro klíč k obsahu, vrátí doručení klíče služby Media Services šifrovací klíč klienta.

Když konfigurujete zásady omezený token, musíte zadat ověření primární klíč, vystavitele a cílová skupina parametry. Ověření primární klíč obsahuje klíč, který byl podepsaný token. Vystavitel je služba tokenů zabezpečení, které vydá token. Cílová skupina (někdy nazývané oboru) popisuje záměr tokenu nebo prostředek token povolí přístup k. Služba Media Services doručení klíče ověří, jestli tyto hodnoty v tokenu shodují s hodnotami v šabloně.

### <a name="playready"></a>PlayReady
Při ochraně svůj obsah pomocí PlayReady jednu z akcí, které je třeba zadat ve vaší zásady autorizace je řetězec XML, který definuje šablona licence PlayReady. Ve výchozím nastavení je nastavit tyto zásady:

    <PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
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

Můžete vybrat **importovat zásady xml** tlačítko a zadejte jiný kód XML, který odpovídá schématu XML, které jsou definované v [přehled šablonu licence Media Services PlayReady](media-services-playready-license-template-overview.md).

## <a name="next-steps"></a>Další postup
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

