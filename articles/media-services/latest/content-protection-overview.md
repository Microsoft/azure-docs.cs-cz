---
title: Chraňte svůj obsah pomocí dynamického šifrování Media Services V3
titleSuffix: Azure Media Services
description: Přečtěte si o ochraně obsahu s dynamickým šifrováním, protokoly streamování a typy šifrování v Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 5d6530cf7b8d8611ff23a3517112cb0aa7442d6d
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91595997"
---
# <a name="protect-your-content-with-media-services-dynamic-encryption"></a>Ochrana obsahu pomocí Media Services dynamického šifrování

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Pomocí Azure Media Services můžete lépe zabezpečit vaše média v době, kdy počítač opustí, a to prostřednictvím úložiště, zpracování a doručování. Pomocí Media Services můžete doručovat živý obsah na vyžádání a dynamicky šifrovaný pomocí standard AES (Advanced Encryption Standard) (AES-128) nebo kteréhokoli ze tří hlavních systémů DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine a Apple FairPlay. Media Services taky poskytuje službu pro doručování klíčů AES a licencí DRM (PlayReady, Widevine a FairPlay) autorizovaným klientům. Pokud je obsah zašifrovaný pomocí nezašifrovaného klíče AES a pošle se přes HTTPS, není nejasný, dokud nedosáhne klienta.

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

V Media Services V3 je klíč obsahu přidružený k lokátoru streamování (viz [Tento příklad](protect-with-aes128.md)). Pokud používáte službu doručování klíčů Media Services, můžete nechat Azure Media Services vygenerovat klíč obsahu. Klíč obsahu by se měl vygenerovat sami, pokud používáte vlastní službu pro doručování klíčů, nebo pokud potřebujete, aby byl scénář s vysokou dostupností, kdy potřebujete stejný klíč obsahu ve dvou datových centrech.

Když hráč vyžádá datový proud, Media Services použije zadaný klíč k dynamickému šifrování obsahu pomocí nezašifrovaného klíče AES nebo šifrování DRM. Aby se datový proud dešifroval, přehrávač požádá o klíč od Media Services služby doručování klíčů nebo služby pro doručování klíčů, kterou jste zadali. Pokud chcete zjistit, jestli má uživatel autorizaci získat klíč, služba vyhodnotí zásadu klíče obsahu, kterou jste zadali pro tento klíč.

Pomocí REST API nebo Media Services klientské knihovny můžete nakonfigurovat zásady ověřování a ověřování pro vaše licence a klíče.

Následující obrázek znázorňuje pracovní postup ochrany Media Services obsahu:

![Pracovní postup pro Media Services ochranu obsahu](./media/content-protection/content-protection.svg)
  
&#42; *dynamické šifrování podporuje AES-128 Clear Key, CBCS a CENC. Podrobnosti najdete v tématu [matice podpory](#streaming-protocols-and-encryption-types).*

Tento článek vysvětluje koncepty a terminologii, které vám pomůžou pochopit ochranu obsahu pomocí Media Services.

## <a name="main-components-of-a-content-protection-system"></a>Hlavní součásti systému ochrany obsahu

K úspěšnému dokončení systému ochrany obsahu je potřeba plně porozumět rozsahu úsilí. Následující části poskytují přehled tří částí, které je třeba implementovat.

> [!NOTE]
> Předtím, než přejdete k další části, důrazně doporučujeme, abyste si před přechodem na další část provedli všechny části v následujících oddílech. Chcete-li otestovat systém ochrany obsahu, použijte nástroje, které jsou uvedeny v částech.

### <a name="media-services-code"></a>Kód Media Services
  
[Ukázka DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) vám ukáže, jak implementovat systém s více DRM pomocí Media Services V3 pomocí .NET. Také ukazuje, jak používat Media Services License/Key Delivery Service.
  
Každý prostředek můžete zašifrovat i pomocí několika typů šifrování (AES-128, PlayReady, Widevine, FairPlay). Informace o tom, co je vhodné kombinovat, najdete v tématu [protokoly streamování a typy šifrování](#streaming-protocols-and-encryption-types).

Příklad ukazuje, jak:

1. Vytvořte a nakonfigurujte [zásady klíče obsahu](content-key-policy-concept.md).

   Vytvoříte zásadu pro klíč obsahu ke konfiguraci způsobu doručení klíče obsahu (který poskytuje zabezpečený přístup k vašim prostředkům) koncovým klientům:  

   * Definujte autorizaci pro doručování licencí. Zadejte logiku kontroly autorizace na základě deklarací identity v JSON Web Token (JWT).
   * Nakonfigurujte licence [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md)a [Fairplay](fairplay-license-overview.md) . Šablony vám umožní nakonfigurovat práva a oprávnění pro jednotlivé několikanásobnou.

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```

2. Vytvořte [Lokátor streamování](streaming-locators-concept.md) , který je nakonfigurovaný pro streamování šifrovaného prostředku.
  
   Lokátor streamování musí být přidružený k [zásadě streamování](streaming-policy-concept.md). V tomto příkladu nastavíme `StreamingLocator.StreamingPolicyName` zásadu "Predefined_MultiDrmCencStreaming".

   Šifry PlayReady a Widevine se aplikují a klíč se doručí klientovi pro přehrávání na základě konfigurovaných licencí DRM. Pokud chcete datový proud zašifrovat pomocí CBCS (FairPlay), použijte zásadu "Predefined_MultiDrmStreaming".

   Lokátor streamování je také přidružen k zásadě klíče obsahu, kterou jste definovali.

3. Vytvořte testovací token.

   `GetTokenAsync`Metoda ukazuje, jak vytvořit testovací token.
4. Sestavte adresu URL streamování.

   `GetDASHStreamingUrlAsync`Metoda ukazuje, jak vytvořit adresu URL streamování. V tomto případě adresa URL streamuje obsah POMLČKy.

### <a name="player-with-an-aes-or-drm-client"></a>Přehrávač s klientem AES nebo DRM

Aplikace přehrávače videa založená na sadě hráčů SDK (buď v nativním režimu, nebo v prohlížeči) musí splňovat následující požadavky:

* Sada Player SDK podporuje potřebné klienty DRM.
* Sada Player SDK podporuje požadované protokoly streamování: hladké, PŘERUŠOVANé a/nebo HTTP Live Streaming (HLS).
* Sada Player SDK dokáže zvládnout předávání tokenu JWT v žádosti o získání licence.

Přehrávač můžete vytvořit pomocí [rozhraní Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/). Pomocí [rozhraní Azure Media Player ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) určete, která technologie DRM se má používat na různých platformách DRM.

Pro testování šifrování AES nebo CENC (Widevine nebo PlayReady) můžete použít [Azure Media Player](https://aka.ms/azuremediaplayer). Ujistěte se, že jste vybrali možnost **Pokročilé možnosti** a zkontrolujete možnosti šifrování.

Pokud chcete testovat FairPlay zašifrovaný obsah, použijte [Tento testovací přehrávač](https://aka.ms/amtest). Přehrávač podporuje Widevine, PlayReady a FairPlay několikanásobnou společně s šifrováním pomocí šifrovaného klíče AES-128.

Vyberte si správný prohlížeč pro otestování různých několikanásobnou:

* Chrome, Opera nebo Firefox pro Widevine.
* Microsoft Edge nebo Internet Explorer 11 pro PlayReady
* Safari v macOS pro FairPlay.

### <a name="security-token-service"></a>Služba tokenů zabezpečení

Služba tokenů zabezpečení (STS) vydává token JWT jako přístupový token pro přístup k prostředkům back-endu. Jako prostředek back-endu můžete použít službu Azure Media Services License/Key Delivery Service. Služba STS musí definovat následující věci:

* Vystavitel a cílová skupina (nebo obor).
* Deklarace identity, které jsou závislé na obchodních požadavcích v ochraně obsahu.
* Symetrický nebo asymetrický ověření pro ověření podpisu.
* Podpora pro výměnu klíčů (v případě potřeby).

[Tento nástroj STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) můžete použít k otestování služby STS. Podporuje všechny tři typy ověřovacích klíčů: symetrický, asymetrický nebo Azure Active Directory (Azure AD) s použitím klíčového přechodu.

## <a name="streaming-protocols-and-encryption-types"></a>Typy šifrování a protokoly streamování

Pomocí Media Services můžete doručovat obsah dynamicky šifrovaný pomocí šifrovaného klíče AES nebo šifrování DRM pomocí PlayReady, Widevine nebo FairPlay. V současné době můžete šifrovat formáty HLS, MPEG POMLČKy a Smooth Streaming. Každý protokol podporuje následující metody šifrování.

### <a name="hls"></a>HLS

Protokol HLS podporuje následující formáty kontejnerů a schémata šifrování:

|Formát kontejneru|Schéma šifrování|Příklad adresy URL|
|---|---|---|
|Vše|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2 – TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2 – TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + FairPlay (včetně HEVC/H. 265) se podporuje na následujících zařízeních:

* iOS 11 nebo novější.
* iPhone 8 nebo novější.
* macOS vysoký Sierra s PROCESORem Intel 7 pro generace.

### <a name="mpeg-dash"></a>MPEG-POMLČKA

Protokol MPEG-SPOJOVNÍK podporuje následující formáty kontejneru a schémata šifrování:

|Formát kontejneru|Schéma šifrování|Příklady adres URL
|---|---|---|
|Vše|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF (FMP4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Technologie Smooth Streaming

Protokol Smooth Streaming podporuje následující formáty kontejneru a schémata šifrování.

|Protokol|Formát kontejneru|Schéma šifrování|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|
|fMP4 | PIFF 1,1 (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=piff)`|

> [!NOTE]
> Podpora PIFF 1,1 je poskytována jako zpětně kompatibilní řešení pro inteligentní TV (Samsung, LG), které implementovalo úvodní verzi programu Common Encryption. Doporučuje se používat jenom formát PIFF, pokud je to potřeba pro podporu inteligentních televizorů legacey Samsung nebo LG dodaných mezi 2009-2015, které podporují verzi PIFF 1,1 šifrování PlayReady. 

### <a name="browsers"></a>Browsers

Běžné prohlížeče podporují následující klienty DRM:

|Prohlížeč|Šifrování|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>Řízení přístupu k obsahu

Konfigurací zásad klíče obsahu můžete určit, kdo má přístup k vašemu obsahu. Služba Media Services podporuje více způsobů autorizace uživatelů, kteří žádají o klíč. Klient (přehrávač) musí splnit zásadu, aby bylo možné klíč doručit klientovi. Zásady klíčů obsahu můžou mít omezení *Open* nebo *token* .

Zásada pro klíč obsahu s omezeným přístupem se dá použít, když chcete licenci vydávat nikomu bez autorizace. Například pokud vaše tržby jsou založené na službě AD a nikoli na základě předplatného.  

Pomocí zásad klíčového obsahu omezeného tokenu se klíč obsahu pošle jenom klientovi, který prezentuje platný token JWT, nebo jednoduchý webový token (SWT) v žádosti o licenci nebo klíč. Tento token musí být vydán službou STS.

Můžete použít Azure AD jako STS nebo nasadit [vlastní STS](#using-a-custom-sts). Služba STS musí být nakonfigurovaná tak, aby vytvořila token podepsaný pomocí zadaného klíče a vydávala deklarace identity, které jste zadali v konfiguraci omezení tokenu. Služba Media Services License/Key Delivery Services vrátí požadovanou licenci nebo klíč klientovi, pokud existují obě tyto podmínky:

* Token je platný.
* Deklarace identity v tokenu se shodují s požadavky nakonfigurovanými pro licenci nebo klíč.

Když konfigurujete zásady s omezením tokenu, musíte zadat primární ověřovací klíč, Vystavitel a parametry cílové skupiny. Primární ověřovací klíč obsahuje klíč, pomocí kterého byl token podepsán. Vystavitel je STS, který vydává token. Cílová skupina, někdy označovaná jako Scope, popisuje účel tokenu nebo prostředku, ke kterému token opravňuje přístup. Služba Media Services License/Key Delivery Services ověřuje, že se tyto hodnoty v tokenu shodují s hodnotami v šabloně.

### <a name="token-replay-prevention"></a>Prevence opětovného přehrání tokenu

Funkce *Prevence opětovného přehrání tokenu* umožňuje Media Services zákazníkům nastavit limit, kolikrát se dá stejný token použít k vyžádání klíče nebo licence. Zákazník může přidat deklaraci identity typu `urn:microsoft:azure:mediaservices:maxuses` v tokenu, kde hodnota je počet, kolikrát je možné token použít k získání licence nebo klíče. Všechny následné požadavky se stejným tokenem na doručení klíče vrátí neautorizovanou odpověď. Podívejte se, jak přidat deklaraci identity v [ukázce DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Požadavky

* Zákazníci musí mít kontrolu nad generováním tokenu. Deklarace identity musí být umístěna do samotného tokenu.
* Při použití této funkce jsou požadavky s tokeny, jejichž čas vypršení platnosti je více než jedna hodina od doby přijetí žádosti, odmítnuty s neoprávněnou odpovědí.
* Tokeny se jednoznačně identifikují podle jejich signatury. Jakékoli změny v datové části (například aktualizace na čas vypršení platnosti nebo deklarace identity) mění signaturu tokenu a počítají se jako nový token, který nepřijde do výše uvedeného klíče.
* Přehrávání se nezdařilo, pokud token překročil `maxuses` hodnotu nastavenou zákazníkem.
* Tato funkce se dá použít pro veškerý stávající chráněný obsah (musí se změnit jenom vydaný token).
* Tato funkce funguje s tokenem JWT i SWT.

## <a name="using-a-custom-sts"></a>Použití vlastní služby STS

Zákazník se může rozhodnout použít vlastní STS k poskytování tokenů. Důvody zahrnují:

* Zprostředkovatel identity (IDP) používaný zákazníkem nepodporuje službu STS. V takovém případě může být vlastní STS možnost.
* Zákazník může potřebovat flexibilní nebo užší řízení pro integraci služby STS se zákaznickým fakturačním systémem zákazníka.

   Například operátor služby [OTT](https://en.wikipedia.org/wiki/Over-the-top_media_services) může nabízet několik balíčků předplatitelů, jako jsou Premium, Basic a sport. Operátor může chtít vyhledat deklarace identity v tokenu s balíčkem předplatitele, aby byl k dispozici pouze obsah v určitém balíčku. V takovém případě vlastní STS poskytuje potřebnou flexibilitu a kontrolu.

* Pokud chcete do tokenu zahrnout vlastní deklarace identity, které se mají vybrat mezi různými ContentKeyPolicyOptions s různými licenčními parametry DRM (licence k předplatnému a licence k pronájmu).
* Chcete-li zahrnout deklaraci identity představující identifikátor klíče obsahu klíče, ke kterému token uděluje přístup.

Pokud používáte vlastní STS, je třeba provést dvě změny:

* Při konfiguraci služby doručování licencí pro Asset musíte zadat klíč zabezpečení, který se používá pro ověření vlastní službou STS místo aktuálního klíče ze služby Azure AD.
* Když se vygeneruje token JTW, místo privátního klíče aktuálního certifikátu x509 ve službě Azure AD se zadá bezpečnostní klíč.

Existují dva typy klíčů zabezpečení:

* Symetrický klíč: stejný klíč se používá ke generování a ověření tokenu JWT.
* Asymetrická klávesa: v certifikátu x509 se používá privátní klíč privátního klíče pro šifrování/vygenerování JWT a s veřejným klíčem k ověření tokenu.

Použijete-li jako vývojovou platformu .NET Framework/C#, certifikát x509 použitý pro asymetrický bezpečnostní klíč musí mít délku klíče alespoň 2048. Tato délka klíče je požadavkem třídy System. IdentityModel. Tokens. X509AsymmetricSecurityKey v .NET Framework. V opačném případě je vyvolána následující výjimka: IDX10630: ' System. IdentityModel. Tokens. X509AsymmetricSecurityKey ' pro Signing nemůže být menší než ' 2048 ' bitů.

## <a name="custom-key-and-license-acquisition-url"></a>Adresa URL vlastního klíče a získání licence

Následující šablony použijte, pokud chcete zadat jinou službu pro doručování licencí nebo klíčů (ne Media Services). Dvě umístěná pole v šablonách jsou zde, takže můžete sdílet zásady streamování napříč mnoha prostředky namísto vytváření zásad streamování na jeden prostředek. 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`: Šablona pro adresu URL vlastní služby, která poskytuje klíče přehrávačům koncových uživatelů. Tento požadavek není nutný, pokud používáte Azure Media Services pro vydávání klíčů. 

   Šablona podporuje nahraditelné tokeny, které služba aktualizuje za běhu s hodnotou specifickou pro požadavek.  Aktuálně podporované hodnoty tokenu jsou:
   * `{AlternativeMediaId}`, který je nahrazen hodnotou StreamingLocatorId. AlternativeMediaId.
   * `{ContentKeyId}`, který je nahrazen hodnotou identifikátoru požadovaného klíče.
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`: Šablona pro adresu URL vlastní služby, která poskytuje licence přehrávačům koncových uživatelů. Tento požadavek není nutný, pokud používáte Azure Media Services pro vystavování licencí.

   Šablona podporuje nahraditelné tokeny, které služba aktualizuje za běhu s hodnotou specifickou pro požadavek. Aktuálně podporované hodnoty tokenu jsou:  
   * `{AlternativeMediaId}`, který je nahrazen hodnotou StreamingLocatorId. AlternativeMediaId.
   * `{ContentKeyId}`, který je nahrazen hodnotou identifikátoru požadovaného klíče. 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: Totéž jako předchozí šablona, pouze pro Widevine. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: Totéž jako předchozí šablona, pouze pro FairPlay.  

Například:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId` má hodnotu požadovaného klíče. Můžete použít, `AlternativeMediaId` Pokud chcete mapovat požadavek na entitu na straně. `AlternativeMediaId`Můžete například použít k usnadnění vyhledávání oprávnění.

Příklady REST, které používají vlastní licence nebo adresy URL pro získání klíčů, najdete v tématu [zásady streamování – vytvořit](/rest/api/media/streamingpolicies/create).

> [!NOTE]
> Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="troubleshoot"></a>Odstraňování potíží

Pokud se zobrazí `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` Chyba, ujistěte se, že zadáváte vhodné zásady streamování.

Pokud se zobrazí chyby, které končí, ujistěte se, `_NOT_SPECIFIED_IN_URL` že jste v adrese URL zadali formát šifrování. Příklad: `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Viz [protokoly streamování a typy šifrování](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="next-steps"></a>Další kroky

* [Ochrana pomocí šifrování AES](protect-with-aes128.md)
* [Ochrana pomocí DRM](protect-with-drm.md)
* [Návrh sady multi-DRM pro ochranu obsahu pomocí řízení přístupu](design-multi-drm-system-with-access-control.md)
* [Šifrování na straně úložiště](storage-account-concept.md#storage-side-encryption)
* [Nejčastější dotazy](frequently-asked-questions.md)
* [Obslužná rutina webových tokenů JSON](/dotnet/framework/security/json-web-token-handler)
