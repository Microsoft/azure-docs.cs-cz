---
title: Nakonfigurujte zásady autorizace klíče obsahu pomocí Azure Portal | Microsoft Docs
description: Tento článek ukazuje, jak nakonfigurovat zásady autorizace pro klíč obsahu.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 68bd4bd2472e80a663294745368fb505767a230a
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008310"
---
# <a name="configure-a-content-key-authorization-policy"></a>Konfigurace zásad autorizace klíče obsahu

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Přehled
 Azure Media Services můžete použít k doručování datových proudů MPEG-POMLČKa, Smooth Streaming a HTTP Live Streaming (HLS) chráněných pomocí standard AES (Advanced Encryption Standard) (AES) pomocí 128 bitových šifrovacích klíčů nebo [technologie PlayReady DRM (Správa digitálních práv)](https://www.microsoft.com/playready/overview/). Pomocí Media Services můžete také doručovat PŘERUŠOVANé datové proudy šifrované pomocí Widevine DRM. Technologie PlayReady i Widevine jsou šifrované podle specifikace Common Encryption (ISO/IEC CENC 23001-7).

Media Services taky poskytuje službu pro doručování klíčů a licencí, ze které můžou klienti získat klíče AES nebo licence PlayReady/Widevine, aby mohli přehrávat zašifrovaný obsah.

Tento článek ukazuje, jak použít Azure Portal ke konfiguraci zásad autorizace klíče obsahu. Klíč lze později použít k dynamickému šifrování vašeho obsahu. V současné době můžete šifrovat formáty HLS, MPEG-SPOJOVNÍK a Smooth Streaming. Progresivní stahování nemůžete šifrovat.

Když hráč požádá o datový proud, který je nastaven na dynamický šifrování, Media Services používá nakonfigurovaný klíč k dynamickému šifrování vašeho obsahu pomocí šifrování AES nebo DRM. K dešifrování streamu si přehrávač vyžádá klíč ze služby doručování klíčů. Aby bylo možné zjistit, zda je uživatel autorizován pro získání klíče, služba vyhodnotí zásady autorizace, které jste zadali pro klíč.

Pokud máte v plánu používat více klíčů obsahu nebo chcete zadat jinou adresu URL služby pro doručování licencí, než je služba doručování klíčů Media Services, použijte rozhraní Media Services .NET SDK nebo rozhraní REST API. Další informace naleznete v tématu:

* [Konfigurace zásad autorizace klíče obsahu pomocí sady Media Services .NET SDK](media-services-dotnet-configure-content-key-auth-policy.md)
* [Konfigurace zásad autorizace klíče obsahu pomocí Media Services REST API](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Platí některé předpoklady.
* Po vytvoření účtu Media Services se do vašeho účtu přidá výchozí koncový bod streamování ve stavu Zastaveno. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí být koncový bod streamování ve stavu spuštěno. 
* Vaše Asset musí obsahovat sadu adaptivních přenosů rychlostmi nebo souborů s adaptivní přenosovou rychlostí Smooth Streaming. Další informace najdete v tématu [kódování assetu](media-services-encode-asset.md).
* Služba doručení klíčů ukládá do mezipaměti ContentKeyAuthorizationPolicy a související objekty (možnosti zásad a omezení) po dobu 15 minut. Můžete vytvořit ContentKeyAuthorizationPolicy a určit, že se má použít omezení tokenu, otestovat ho a pak aktualizovat zásadu na otevřené omezení. Tento proces trvá zhruba 15 minut, než se zásada přepne na otevřenou verzi.
* Koncový bod streamování Media Services nastaví hodnotu hlavičky Access-Control-Allow-Origin v odpovědi na kontrolu před výstupem jako zástupný znak " \* ". Tato hodnota dobře funguje u většiny hráčů, včetně Azure Media Player, roku a JWPlayer a dalších. Někteří hráči, kteří používají dash.js, ale nefungují, protože s režimem přihlašovacích údajů nastaveným na include, XMLHttpRequest v jejich dash.js nepovoluje zástupný znak \* "" jako hodnotu možnosti Access-Control-Allow-Origin. V případě tohoto omezení v dash.js se jedná o alternativní řešení, pokud klienta hostuje z jedné domény, Media Services může tuto doménu v hlavičce odpovědi na předběžné služby zadat. Pokud potřebujete pomoc, otevřete lístek podpory prostřednictvím Azure Portal.

## <a name="configure-the-key-authorization-policy"></a>Konfigurace zásad autorizace klíčů
Pokud chcete nakonfigurovat zásady autorizace klíčů, vyberte stránku **Content Protection** .

Media Services podporuje více způsobů ověřování uživatelů, kteří vytvářejí požadavky na klíč. Zásady autorizace klíče obsahu můžou mít omezení autorizace otevření, tokenu nebo protokolu IP. (IP se dá nakonfigurovat pomocí REST nebo sady .NET SDK.)

### <a name="open-restriction"></a>Otevřené omezení
Otevřené omezení znamená, že systém doručí klíč všem, kdo vytvoří klíčovou žádost. Toto omezení může být užitečné pro účely testování.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Omezení tokenu
Chcete-li zvolit zásadu omezeného tokenu, vyberte tlačítko **token** .

Zásady omezeného tokenu musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Media Services podporuje tokeny ve formátech jednoduchých webových tokenů ([SWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2)) a JSON web token (Jwt). Další informace najdete v tématu [ověřování JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Media Services neposkytuje službu STS. Můžete vytvořit vlastní STS pro vydávání tokenů. Služba STS musí být nakonfigurovaná tak, aby vytvořila token podepsaný pomocí zadaného klíče a vydávala deklarace identity, které jste zadali v konfiguraci omezení tokenu. Pokud je token platný a deklarace identity v tokenu odpovídají nastavením nakonfigurovaným pro klíč obsahu, služba doručování Media Services Key vrátí šifrovací klíč klientovi.

Když konfigurujete zásady s omezením tokenu, musíte zadat primární ověřovací klíč, Vystavitel a parametry cílové skupiny. Primární ověřovací klíč obsahuje klíč, pomocí kterého byl token podepsán. Vystavitel je STS, který vydává token. Cílová skupina (někdy označovaná jako obor) popisuje účel tokenu nebo prostředku, ke kterému token opravňuje přístup. Služba doručení Media Services Key ověřuje, že se tyto hodnoty v tokenu shodují s hodnotami v šabloně.

### <a name="playready"></a>PlayReady
Když chráníte obsah pomocí PlayReady, je jedním z věcí, které potřebujete zadat v zásadách autorizace, řetězec XML, který definuje šablonu licence PlayReady. Ve výchozím nastavení jsou nastaveny následující zásady:

```xml
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
```

Můžete vybrat tlačítko **Importovat zásady XML** a zadat jiný kód XML, který odpovídá schématu XML definovanému v [přehledu šablony licencí Media Services PlayReady](media-services-playready-license-template-overview.md).

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="next-steps"></a>Další kroky
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png
