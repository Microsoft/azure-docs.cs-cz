---
title: Ochrana obsahu pomocí dynamického šifrování Služby Media Services v3
titleSuffix: Azure Media Services
description: Další informace o ochraně obsahu pomocí dynamického šifrování, protokolů streamování a typů šifrování ve službě Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: c1c9440f7ec70cea98f270f04c3030c800dd0fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461108"
---
# <a name="protect-your-content-with-media-services-dynamic-encryption"></a>Ochrana obsahu pomocí dynamického šifrování mediálních služeb

Pomocí Mediálních služeb Azure můžete zabezpečit média od okamžiku, kdy opustí váš počítač až po úložiště, zpracování a doručování. Pomocí služby Media Services můžete poskytovat obsah živého i na vyžádání šifrovaný dynamicky pomocí advanced encryption standardu (AES-128) nebo některého ze tří hlavních systémů správy digitálních práv (DRM): Microsoft PlayReady, Google Widevine a Apple FairPlay. Mediální služby také poskytují službu pro doručování klíčů AES a licencí DRM (PlayReady, Widevine a FairPlay) oprávněným klientům. Pokud je obsah zašifrován pomocí jasného klíče AES a je odeslán přes protokol HTTPS, není jasný, dokud se nedostane ke klientovi. 

Ve službě Media Services v3 je klíč obsahu spojen s lokátorem streamování (viz [tento příklad).](protect-with-aes128.md) Pokud používáte službu doručování klíčů Mediální služby, můžete nechat Azure Media Services generovat klíč obsahu za vás. Klíč obsahu by měl být generován sami, pokud používáte vlastní službu doručování klíčů, nebo pokud potřebujete zpracovat scénář s vysokou dostupností, kde potřebujete mít stejný klíč obsahu ve dvou datových centrech.

Pokud je datový proud požadován přehrávačem, služba Media Services používá zadaný klíč k dynamickému šifrování obsahu pomocí jasného klíče AES nebo šifrování DRM. Chcete-li dešifrovat datový proud, přehrávač požaduje klíč ze služby doručování klíčů Služby Media Services nebo služby doručování klíčů, které jste zadali. Chcete-li rozhodnout, zda je uživatel oprávněn získat klíč, služba vyhodnotí zásady klíče obsahu, které jste zadali pro klíč.

Pomocí rozhraní REST API nebo klientské knihovny Služby Media Services můžete nakonfigurovat zásady autorizace a ověřování pro licence a klíče.

Následující obrázek znázorňuje pracovní postup ochrany obsahu mediálních služeb:

![Pracovní postup pro ochranu obsahu mediálních služeb](./media/content-protection/content-protection.svg)
  
&#42; *dynamické šifrování podporuje aes-128 vymazat klíč, CBCS a CENC. Podrobnosti naleznete v [matici podpory](#streaming-protocols-and-encryption-types).*

Tento článek vysvětluje koncepty a terminologii, které vám pomohou porozumět ochraně obsahu pomocí služby Media Services.

## <a name="main-components-of-a-content-protection-system"></a>Hlavní součásti systému ochrany obsahu

Chcete-li úspěšně dokončit systém ochrany obsahu, musíte plně pochopit rozsah úsilí. V následujících částech je uveden přehled tří částí, které je třeba implementovat.

> [!NOTE]
> Důrazně doporučujeme, abyste se před přechodem na další díl zaměřili a plně otestovali každou část v následujících částech. Chcete-li otestovat systém ochrany obsahu, použijte nástroje uvedené v sekcích.

### <a name="media-services-code"></a>Kód mediálních služeb
  
[Ukázka DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) ukazuje, jak implementovat systém více DRM pomocí služby Media Services v3 pomocí rozhraní .NET. Ukazuje také, jak používat licenci/službu doručování klíčů mediálních služeb.
  
Každý prostředek můžete zašifrovat i pomocí několika typů šifrování (AES-128, PlayReady, Widevine, FairPlay). Informace o tom, co má smysl kombinovat, najdete v [tématu Protokoly datových proudů a typy šifrování](#streaming-protocols-and-encryption-types).

Příklad ukazuje, jak:

1. Vytvořte a nakonfigurujte [zásady klíče obsahu](content-key-policy-concept.md).

   Vytvoříte zásady klíče obsahu pro konfiguraci způsobu doručování klíče obsahu (který poskytuje zabezpečený přístup k vašim prostředkům koncovým klientům):  

   * Definujte autorizaci dodání licence. Zadejte logiku kontroly autorizace na základě deklarací identity ve webovém tokenu JSON (JWT).
   * Konfigurace licencí [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md)a/nebo [FairPlay.](fairplay-license-overview.md) Šablony umožňují konfigurovat práva a oprávnění pro každý z DRM.

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```

2. Vytvořte [lokátor streamování,](streaming-locators-concept.md) který je nakonfigurovaný pro streamování šifrovaného datového zdroje.
  
   Lokátor streamování musí být přidružen k [zásadám streamování](streaming-policy-concept.md). V příkladu jsme `StreamingLocator.StreamingPolicyName` nastavili zásady "Predefined_MultiDrmCencStreaming".

   Jsou použity šifrování PlayReady a Widevine a klíč je doručen klientovi přehrávání na základě nakonfigurovaných licencí DRM. Pokud chcete také šifrovat svůj stream pomocí CBCS (FairPlay), použijte zásady "Predefined_MultiDrmStreaming".

   Lokátor streamování je také přidružen k zásadám klíče obsahu, které jste definovali.

3. Vytvořte testovací token.

   Metoda `GetTokenAsync` ukazuje, jak vytvořit testovací token.
4. Vytvořte adresu URL streamování.

   Metoda `GetDASHStreamingUrlAsync` ukazuje, jak vytvořit adresu URL streamování. V tomto případě adresa URL streamuje obsah DASH.

### <a name="player-with-an-aes-or-drm-client"></a>Přehrávač s klientem AES nebo DRM

Aplikace přehrávače videa založená na sadě SDK přehrávače (nativní nebo na bázi prohlížeče) musí splňovat následující požadavky:

* Sada SDK přehrávače podporuje potřebné klienty DRM.
* Sada SDK přehrávače podporuje požadované protokoly streamování: Smooth, DASH a/nebo HTTP Live Streaming (HLS).
* Sada SDK přehrávače zvládne předání tokenu JWT v žádosti o získání licence.

Přehrávač můžete vytvořit pomocí [rozhraní Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/). Pomocí [rozhraní Azure Media Player ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) určete, kterou technologii DRM použít na různých platformách DRM.

Pro testování šifrovaného obsahu AES nebo CENC (Widevine a/nebo PlayReady) můžete použít [Azure Media Player](https://aka.ms/azuremediaplayer). Ujistěte se, že jste vybrali **možnosti Upřesnit** a zkontrolujte možnosti šifrování.

Chcete-li otestovat šifrovaný obsah FairPlay, použijte [tento testovací přehrávač](https://aka.ms/amtest). Přehrávač podporuje Widevine, PlayReady a FairPlay DRM, spolu s AES-128 jasné šifrování klíče.

Vyberte si správný prohlížeč pro testování různých DRM:

* Chrome, Opera nebo Firefox pro Widevine.
* Microsoft Edge nebo Internet Explorer 11 pro PlayReady.
* Safari na macOS pro FairPlay.

### <a name="security-token-service"></a>Služba tokenů zabezpečení

Služba tokenů zabezpečení (STS) vydává JWT jako přístupový token pro přístup k back-endovým prostředkům. Jako back-endový prostředek můžete použít službu doručování licencí a klíčů Azure Media Services. STS musí definovat následující věci:

* Vydavatel a publikum (nebo rozsah).
* Deklarace identity, které jsou závislé na obchodních požadavcích v ochraně obsahu.
* Symetrické nebo asymetrické ověření pro ověření podpisu.
* Podpora převrácení klíče (v případě potřeby).

Tento [nástroj STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) můžete použít k testování STS. Podporuje všechny tři typy ověřovacích klíčů: symetrické, asymetrické nebo Azure Active Directory (Azure AD) s přechodem na klíče.

## <a name="streaming-protocols-and-encryption-types"></a>Typy šifrování a protokoly streamování

Pomocí mediálních služeb můžete pomocí služby Media Services dodávat obsah šifrovaný dynamicky pomocí jasného klíče AES nebo šifrování DRM pomocí služeb PlayReady, Widevine nebo FairPlay. V současné době můžete šifrovat formáty HLS, MPEG DASH a Smooth Streaming. Každý protokol podporuje následující metody šifrování.

### <a name="hls"></a>HLS

Protokol HLS podporuje následující formáty kontejnerů a šifrovací schémata:

|Formát kontejneru|Schéma šifrování|Příklad adresy URL|
|---|---|---|
|Všechny|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (Fairplay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF (fmp4) |CBCS (Fairplay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF (fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + FairPlay (včetně HEVC/H.265) je podporován na následujících zařízeních:

* iOS 11 nebo novější.
* iPhone 8 nebo novější.
* MacOS High Sierra s procesorem Intel 7th Generation.

### <a name="mpeg-dash"></a>MPEG-POMLČKA

Protokol MPEG-DASH podporuje následující formáty kontejnerů a šifrovací schémata:

|Formát kontejneru|Schéma šifrování|Příklady adres URL
|---|---|---|
|Všechny|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF (fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Technologie Smooth Streaming

Protokol Smooth Streaming podporuje následující formáty kontejnerů a šifrovací schémata.

|Protocol (Protokol)|Formát kontejneru|Schéma šifrování|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

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

Konfigurací zásad klíče obsahu můžete určit, kdo má k vašemu obsahu přístup. Služba Media Services podporuje více způsobů autorizace uživatelů, kteří žádají o klíč. Klient (hráč) musí splnit zásady před klíč může být dodáno klientovi. Zásady klíče obsahu mohou mít *omezení otevření* nebo *tokenu.*

Zásady klíče obsahu s otevřeným omezeným přístupem mohou být použity, pokud chcete vydat licenci komukoli bez autorizace. Pokud jsou například vaše tržby založeny na reklamách a nejsou založeny na předplatném.  

Se zásadami klíče obsahu s omezeným tokenem je klíč obsahu odeslán pouze klientovi, který v žádosti o licenci/klíč představuje platný token JWT nebo jednoduchý webový token (SWT). Tento token musí být vydán STS.

Azure AD můžete použít jako STS nebo nasadit [vlastní STS](#using-a-custom-sts). Služba STS musí být nakonfigurována tak, aby vytvořila token podepsaný zadaným klíčem a vydala deklarace identity, které jste zadali v konfiguraci omezení tokenu. Služba doručování licencí a klíčů služby Media Services vrátí klientovi požadovanou licenci nebo klíč, pokud existují obě tyto podmínky:

* Token je platný.
* Deklarace identity v tokenu odpovídají deklaracím nakonfigurovaným pro licenci nebo klíč.

Při konfiguraci zásad s omezeným přístupem tokenu je nutné zadat primární ověřovací klíč, vystavitel a parametry publika. Primární ověřovací klíč obsahuje klíč, se kterým byl token podepsán. Vystavitel je STS, který vydává token. Cílová skupina, někdy volaná obor, popisuje záměr tokenu nebo prostředku, ke kterému token autorizuje přístup. Služba doručování licencí a klíčů služby Media Services ověří, zda tyto hodnoty v tokenu odpovídají hodnotám v šabloně.

### <a name="token-replay-prevention"></a>Prevence opětovného přehrání tokenu

Funkce *Prevence přehrání tokenu* umožňuje zákazníkům mediálních služeb nastavit limit, kolikrát lze stejný token použít k vyžádání klíče nebo licence. Zákazník může přidat deklaraci `urn:microsoft:azure:mediaservices:maxuses` typu do tokenu, kde hodnota je počet, kolikrát token lze použít k získání licence nebo klíče. Všechny následné požadavky se stejným tokenem doručení klíče vrátí neoprávněnou odpověď. Podívejte se, jak přidat deklaraci do [vzorku DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Požadavky

* Zákazníci musí mít kontrolu nad generování tokenu. Deklarace musí být umístěna do samotného tokenu.
* Při použití této funkce jsou požadavky s tokeny, jejichž doba vypršení platnosti je vzdálena více než jednu hodinu od okamžiku přijetí požadavku, odmítnuty s neoprávněnou odpovědí.
* Tokeny jsou jednoznačně identifikovány jejich podpisem. Jakákoli změna datové části (například aktualizace na dobu vypršení platnosti nebo deklarace identity) změní podpis tokenu a bude se počítat jako nový token, na který se doručení klíče dařilo předtím nesetkalo.
* Přehrávání se nezdaří, pokud `maxuses` token překročil hodnotu nastavenou zákazníkem.
* Tuto funkci lze použít pro veškerý existující chráněný obsah (je třeba změnit pouze vydaný token).
* Tato funkce funguje jak s JWT, tak s SWT.

## <a name="using-a-custom-sts"></a>Použití vlastního STS

Zákazník se může rozhodnout použít vlastní STS k poskytování tokenů. Důvody zahrnují:

* Zprostředkovatel identity (IDP) používaný zákazníkem nepodporuje STS. V tomto případě vlastní STS může být možnost.
* Zákazník může potřebovat flexibilnější nebo přísnější kontrolu pro integraci STS s fakturačním systémem odběratele zákazníka.

   Operátor služby [OTT](https://en.wikipedia.org/wiki/Over-the-top_media_services) může například nabízet více balíčků předplatitelů, jako je například premium, basic a sports. Operátor může chtít porovnat deklarace identity v tokenu s balíčkem odběratele tak, aby byly k dispozici pouze obsah v určitém balíčku. V tomto případě vlastní STS poskytuje potřebnou flexibilitu a kontrolu.

* Chcete-li zahrnout vlastní deklarace identity do tokenu pro výběr mezi různými ContentKeyPolicyOptions s různými parametry licence DRM (předplacená licence versus licence k pronájmu).
* Chcete-li zahrnout deklaraci představující identifikátor klíče obsahu klíče klíče, ke kterému token uděluje přístup.

Při použití vlastní STS, musí být provedeny dvě změny:

* Při konfiguraci služby doručování licencí pro datový zdroj, musíte zadat klíč zabezpečení, který se používá pro ověření vlastní STS namísto aktuálníklíč z Azure AD.
* Při generování tokenu JTW je určen klíč zabezpečení namísto privátního klíče aktuálního certifikátu X509 ve službě Azure AD.

Existují dva typy klíčů zabezpečení:

* Symetrický klíč: Stejný klíč se používá ke generování a ověření JWT.
* Asymetrický klíč: Pár veřejného a soukromého klíče v certifikátu X509 se používá s privátním klíčem k šifrování/generování JWT a s veřejným klíčem k ověření tokenu.

Pokud používáte rozhraní .NET Framework/C# jako vývojovou platformu, certifikát X509 použitý pro asymetrický klíč zabezpečení musí mít délku klíče alespoň 2048. Tato délka klíče je požadavkem třídy System.IdentityModel.Tokens.X509AsymmetricSecuritySecurityKey v rozhraní .NET Framework. Jinak je vyvolána následující výjimka: IDX10630: System.IdentityModel.Tokens.X509AsymmetrSecuritySecurityKey' pro podepisování nemůže být menší než '2048' bity.

## <a name="custom-key-and-license-acquisition-url"></a>Adresa URL pro vlastní klíč a získání licence

Pokud chcete zadat jinou službu doručování licencí a klíčů (nikoli služby Media Services), použijte následující šablony. Dvě nahraditelná pole v šablonách jsou k dispozici, takže můžete sdílet zásady streamování mezi mnoha datovými zdroji namísto vytváření zásad streamování na datový zdroj. 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`: Šablona pro adresu URL vlastní služby, která poskytuje klíče koncovým uživatelům. Není vyžadováno, když používáte Azure Media Services pro vydávání klíčů. 

   Šablona podporuje nahraditelné tokeny, které bude služba aktualizovat za běhu s hodnotou specifickou pro požadavek.  Aktuálně podporované hodnoty tokenů jsou:
   * `{AlternativeMediaId}`, který je nahrazen hodnotou StreamingLocatorId.AlternativeMediaId.
   * `{ContentKeyId}`, který je nahrazen hodnotou identifikátoru požadovaného klíče.
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`: Šablona pro adresu URL vlastní služby, která poskytuje licence koncovým uživatelům. Není vyžadováno, když používáte Azure Media Services pro vydávání licencí.

   Šablona podporuje nahraditelné tokeny, které bude služba aktualizovat za běhu s hodnotou specifickou pro požadavek. Aktuálně podporované hodnoty tokenů jsou:  
   * `{AlternativeMediaId}`, který je nahrazen hodnotou StreamingLocatorId.AlternativeMediaId.
   * `{ContentKeyId}`, který je nahrazen hodnotou identifikátoru požadovaného klíče. 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: Stejné jako předchozí šablony, pouze pro Widevine. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: Stejné jako předchozí šablony, pouze pro FairPlay.  

Například:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId`má hodnotu požadovaného klíče. Můžete použít, `AlternativeMediaId` pokud chcete mapovat požadavek na entitu na vaší straně. Můžete například `AlternativeMediaId` vyhledat oprávnění.

Příklady REST, které používají adresy URL vlastní licence nebo pořízení klíče, naleznete v [tématu Zásady streamování – Vytvoření](https://docs.microsoft.com/rest/api/media/streamingpolicies/create).

> [!NOTE]
> Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="troubleshoot"></a>Řešení potíží

Pokud se `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` zobrazí chyba, ujistěte se, že zadáte příslušné zásady streamování.

Pokud se vám našly chyby, které končí , `_NOT_SPECIFIED_IN_URL`ujistěte se, že jste v adrese URL zadali formát šifrování. Příklad: `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Viz [Protokoly datových proudů a typy šifrování](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Ptejte se, podávejte zpětnou vazbu, získejte aktualizace

Podívejte se na článek [komunity Mediálních služeb Azure](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, poskytovat zpětnou vazbu a získat aktualizace o mediálních službách.

## <a name="next-steps"></a>Další kroky

* [Ochrana pomocí šifrování AES](protect-with-aes128.md)
* [Ochrana pomocí DRM](protect-with-drm.md)
* [Navrhněte systém ochrany obsahu s více DRM s řízením přístupu](design-multi-drm-system-with-access-control.md)
* [Šifrování na straně úložiště](storage-account-concept.md#storage-side-encryption)
* [Nejčastější dotazy](frequently-asked-questions.md)
* [Obslužná rutina webových tokenů JSON](https://docs.microsoft.com/dotnet/framework/security/json-web-token-handler)
