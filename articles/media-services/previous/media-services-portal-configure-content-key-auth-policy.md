---
title: Konfigurace zásad autorizace klíče obsahu pomocí webu Azure portal | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat zásadu autorizace pro klíč k obsahu.
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
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: 7dd056042b841e54c18ee1a667c44cfa11d77a61
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56164596"
---
# <a name="configure-a-content-key-authorization-policy"></a>Konfigurace zásad autorizace klíče obsahu
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Přehled
 Azure Media Services můžete doručovat datové proudy MPEG-DASH, Smooth Streaming a HTTP Live Streaming (HLS) chráněné pomocí šifrování AES (Advanced Standard) pomocí 128bitového šifrování klíče nebo [PlayReady digital rights management (DRM)](https://www.microsoft.com/playready/overview/). Díky Media Services můžete také doručovat datové proudy DASH šifrované pomocí Widevine DRM. Technologie PlayReady i Widevine jsou šifrované podle specifikace Common Encryption (ISO/IEC CENC 23001-7).

Služba Media Services také poskytuje služba doručování klíčů/licencí ze kterých mohou klienti získat klíče AES nebo PlayReady/Widevine licence k přehrávání šifrovaného obsahu.

Tento článek ukazuje, jak pomocí webu Azure portal nakonfigurovat zásady autorizace klíče obsahu. Klíč můžete později použít k dynamické šifrování obsahu. V současné době můžete šifrovat, HLS, MPEG-DASH a Smooth Streaming formátů. Nelze zašifrovat progresivní stahování.

Když hráč vyžádá datový proud, který je nastavena na dynamicky šifrovat, Media Services využívá nakonfigurovaný klíč k dynamické šifrování obsahu pomocí šifrování AES a DRM. K dešifrování streamu si přehrávač vyžádá klíč ze služby doručování klíčů. Pokud chcete zjistit, zda je uživatel oprávnění k získání klíče, služba vyhodnocuje zásady autorizace, které jste zadali pro klíč.

Pokud budete chtít mít více klíčů obsahu nebo chcete zadat adresu URL služby doručování klíčů/licencí než služba Media Services doručení klíče, použijte sadu Media Services .NET SDK nebo rozhraní REST API. Další informace naleznete v tématu:

* [Konfigurace zásad autorizace klíče obsahu s využitím Media Services .NET SDK](media-services-dotnet-configure-content-key-auth-policy.md)
* [Konfigurace zásad autorizace klíče obsahu pomocí rozhraní REST API pro Media Services](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Určité předpoklady
* Po vytvoření účtu Media Services se do vašeho účtu přidá výchozí koncový bod streamování ve stavu Zastaveno. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí být koncový bod streamování ve stavu "Spuštěno". 
* Váš asset musí obsahovat sadu s adaptivní přenosovou rychlostí soubory MP4 rychlostmi nebo soubory technologie Smooth Streaming s adaptivní přenosovou rychlostí. Další informace najdete v tématu [kódování prostředku](media-services-encode-asset.md).
* Služba doručování klíčů ukládá do mezipaměti ContentKeyAuthorizationPolicy a její související objekty (Možnosti zásad a omezení) na 15 minut. Můžete vytvořit ContentKeyAuthorizationPolicy a zadat omezení s tokenem, otestovat a pak aktualizujte zásady na otevřené omezení. Tento proces trvá přibližně 15 minut před zásad přepínače otevřít verzi.
* Media Services koncový bod streamování nastaví hodnotu hlavičky CORS Access-Control-Allow-Origin v předběžné odpověď jako zástupný znak "\*". Tato hodnota se pracuje s většina přehrávače, včetně Azure Media Player, Roku a JWPlayer a dalších. Však některé přehrávačích souborem dash.js nefungují, protože v režimu pověření nastavte na "zahrnutí" XMLHttpRequest v jejich souborem dash.js nepovoluje zástupný znak "\*" jako hodnota Access-Control-Allow-Origin. Jako alternativní řešení pro toto omezení v souborem dash.js pokud hostujete vašeho klienta z jedné domény, Media Services můžete zadat tuto doménu v hlavičce odpovědi výstupem. Potřebujete pomoc otevřete lístek podpory prostřednictvím webu Azure portal.

## <a name="configure-the-key-authorization-policy"></a>Nakonfigurujte zásady autorizace pro klíč
Pokud chcete nakonfigurovat zásady autorizace pro klíč, vyberte **CONTENT PROTECTION** stránky.

Služba Media Services podporuje více způsobů, jak ověřovat uživatele, kteří žádají o klíč. Zásady autorizace klíče obsahu může mít otevřený, token nebo povolení omezení IP adres. (IP je nakonfigurovat pomocí .NET SDK nebo REST.)

### <a name="open-restriction"></a>Otevřít omezení
Otevřít omezení znamená, že systém poskytuje všem uživatelům, kteří vytvoří klíče požadavek klíč. Toto omezení může být užitečná pro účely testování.

![Funkce OpenPolicy][open_policy]

### <a name="token-restriction"></a>Omezení s tokenem
Zvolte zásady omezení tokenem, vyberte **TOKEN** tlačítko.

Zásady omezení tokenem musí být doplněny tokenem vydaným službou služby tokenů zabezpečení (STS). Služba Media Services podporuje tokeny ve jednoduchý webový token ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) a formátů JSON Web Token (JWT). Další informace najdete v tématu [ověřování tokenů JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Služba Media Services neposkytuje službu tokenů zabezpečení. Můžete vytvořit vlastní službu STS vystavovat tokeny. Služba tokenů zabezpečení musí být nakonfigurovaný k vytvoření tokenu podepsán zadaný klíč a vydávání deklarací identity, které jste zadali v konfiguraci omezení s tokenem. Pokud je token platný a deklarace identity v tokenu odpovídají těm nakonfigurované pro symetrický klíč, doručení klíče služby Media Services vrátí klientovi šifrovací klíč.

Když konfigurujete zásady omezení tokenem, musíte zadat primární ověřovací klíč, vydavatele a parametry cílovou skupinu. Primární ověřovací klíč obsahuje klíč, který byl token podepsán pomocí. Vystavitel je služba tokenů zabezpečení, které vydá token. Cílová skupina (říká se jim oboru) by měl popisovat záměr tokenu nebo prostředek token, který autorizuje přístup k. Služba Media Services doručení klíče ověří, že tyto hodnoty v tokenu odpovídají hodnotám v šabloně.

### <a name="playready"></a>PlayReady
Pokud budete chránit obsah pomocí technologie PlayReady, jednou z věcí, které je třeba zadat v zásadách autorizace je řetězec XML, který definuje šablona licencování PlayReady. Ve výchozím nastavení je nastavit následující zásady:

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

Můžete vybrat **importovat soubor xml zásad** tlačítko a zadejte jiný kód XML, který odpovídá schématu XML, které jsou definovány v [Přehled šablon licencování Media Services PlayReady](media-services-playready-license-template-overview.md).

## <a name="next-steps"></a>Další postup
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

