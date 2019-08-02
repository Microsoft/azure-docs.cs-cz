---
title: Chraňte svůj obsah pomocí Media Services dynamického šifrování – Azure | Microsoft Docs
description: Tento článek poskytuje přehled ochrany obsahu s dynamickým šifrováním. Také mluví o protokolech streamování a typech šifrování.
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
ms.date: 07/25/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: a928640aa6d56f0a39011a2cabcf979b4d907a46
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561471"
---
# <a name="protect-your-content-by-using-media-services-dynamic-encryption"></a>Chraňte svůj obsah pomocí Media Services dynamického šifrování

Azure Media Services můžete použít k lepšímu zabezpečení médií před tím, než počítač opustí úložiště, zpracování a doručování. Pomocí Media Services můžete doručovat živý obsah na vyžádání a dynamicky šifrovaný pomocí standard AES (Advanced Encryption Standard) (AES-128) nebo kteréhokoli ze tří hlavních systémů DRM (Správa digitálních práv): Microsoft PlayReady, Google Widevine a Apple FairPlay. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM (PlayReady, Widevine a FairPlay) licence autorizovaným klientům.  

V Media Services V3 je klíč obsahu přidružený k lokátoru streamování (viz [Tento příklad](protect-with-aes128.md)). Pokud používáte službu doručování klíčů Media Services, můžete nechat Azure Media Services vygenerovat klíč obsahu. Klíč obsahu byste měli vygenerovat sami, pokud používáte vlastní službu pro doručování klíčů, nebo pokud potřebujete zpracovat scénář s vysokou dostupností, ke kterému potřebujete stejný klíč obsahu ve dvou datových centrech.

Datový proud je žádost přehrávač, Media Services využívá se zadaným klíčem k dynamické šifrování obsahu pomocí nezašifrovaného klíče AES a DRM šifrování. Přehrávač dešifrovat datového proudu, vyžaduje klíč z doručení klíče služby Media Services nebo doručení klíče, který jste zadali. Rozhodování o tom, zda je uživatel oprávnění k získání klíče, služba vyhodnocuje obsahu klíče zásadami, které jste zadali pro klíč.

Rozhraní REST API nebo klientskou knihovnu služby Media Services můžete použít ke konfiguraci zásad ověřování a ověřování licencí a klíčů.

Následující obrázek znázorňuje pracovní postup ochrany Media Services obsahu:

![Pracovní postup pro Media Services ochranu obsahu](./media/content-protection/content-protection.svg)
  
&#42;*Dynamické šifrování podporuje AES-128 Clear Key, CBCS a CENC. Podrobnosti najdete v tématu [matice podpory](#streaming-protocols-and-encryption-types).*

Tento článek vysvětluje koncepty a terminologii, které vám pomůžou pochopit ochranu obsahu pomocí Media Services.

## <a name="main-components-of-a-content-protection-system"></a>Hlavní součásti systému ochrany obsahu

K úspěšnému dokončení systému ochrany obsahu je potřeba plně porozumět rozsahu úsilí. Následující části poskytují přehled tří částí, které byste měli implementovat. 

> [!NOTE]
> Předtím, než přejdete k další části, důrazně doporučujeme, abyste si před přechodem na další část provedli všechny části v následujících oddílech. Chcete-li otestovat systém ochrany obsahu, použijte nástroje, které jsou uvedeny v částech.

### <a name="media-services-code"></a>Kód Media Services
  
[Ukázka DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) vám ukáže, jak implementovat systém s více DRM pomocí Media Services V3 pomocí .NET. Také ukazuje, jak používat Media Services License/Key Delivery Service.   
  
Každý prostředek můžete zašifrovat i pomocí několika typů šifrování (AES-128, PlayReady, Widevine, FairPlay). Informace o tom, co je vhodné kombinovat, najdete v tématu [protokoly streamování a typy šifrování](#streaming-protocols-and-encryption-types).

Příklad ukazuje postup:

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
  
   Lokátor streamování musí být přidružený k [zásadě streamování](streaming-policy-concept.md). V tomto příkladu jsme nastavili `StreamingLocator.StreamingPolicyName` zásady "Predefined_MultiDrmCencStreaming". 
      
   Šifry PlayReady a Widevine se aplikují a klíč se doručí klientovi pro přehrávání na základě konfigurovaných licencí DRM. Pokud chcete datový proud zašifrovat pomocí CBCS (FairPlay), použijte zásadu "Predefined_MultiDrmStreaming".

   Lokátor streamování je také přidružen k zásadě klíče obsahu, kterou jste definovali.
3. Vytvořte token testu.

   `GetTokenAsync` Metoda ukazuje, jak vytvořit testovací token.
4. Sestavte adresu URL streamování.

   `GetDASHStreamingUrlAsync` Metoda ukazuje, jak vytvořit adresu URL streamování. V tomto případě adresa URL streamuje obsah POMLČKy.

### <a name="player-with-an-aes-or-drm-client"></a>Přehrávač s klientem AES nebo DRM 

Přehrávač videa aplikaci založenou na přehrávač SDK (nativní nebo založené na prohlížeči) musí splňovat následující požadavky:

* Sada Player SDK podporuje potřebné klienty DRM.
* Sada Player SDK podporuje požadované protokoly streamování: Hladké, PŘERUŠOVANé a/nebo HLS.
* Sada Player SDK dokáže zvládnout předávání tokenu JWT v žádosti o získání licence.

Přehrávač můžete vytvořit pomocí [rozhraní API služby Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/). Použít [rozhraní API služby Azure Media Player ProtectionInfo](https://amp.azure.net/libs/amp/latest/docs/) zadat technologii DRM, která má používat na různých platformách DRM.

Pro testování AES nebo šifrování CENC (Widevine a/nebo technologií PlayReady) zašifrovaný obsah, můžete použít [Azure Media Player](https://aka.ms/azuremediaplayer). Ujistěte se, že jste vybrali možnost **Pokročilé možnosti** a zkontrolujete možnosti šifrování.

Pokud chcete testovat FairPlay zašifrovaný obsah, použijte [tento test přehrávač](https://aka.ms/amtest). Přehrávač podporuje Widevine, PlayReady a FairPlay několikanásobnou společně s šifrováním pomocí šifrovaného klíče AES-128. 

Vyberte si správný prohlížeč pro otestování různých několikanásobnou:

* Chrome, Opera nebo Firefox pro Widevine
* Microsoft Edge nebo Internet Explorer 11 pro PlayReady
* Safari v macOS pro FairPlay

### <a name="security-token-service"></a>Služba tokenů zabezpečení

Služba tokenů zabezpečení (STS) vydává token JWT jako přístupový token pro přístup k prostředkům back-endu. Jako prostředek back-endu můžete použít službu Azure Media Services License/Key Delivery Service. Služby STS musí definovat následující:

* Vystavitel a cílové skupiny (nebo rozsah)
* Deklarace identity, které jsou závislé na obchodních požadavcích v ochrana obsahu
* Symetrický, nebo asymetrický ověření pro ověření podpisu
* Podpora výměny klíčů (v případě potřeby)

[Tento nástroj STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) můžete použít k otestování služby STS. Podporuje všechny tři typy ověřovacích klíčů: symetrický, asymetrický nebo Azure Active Directory (Azure AD) s použitím klíčového přechodu. 

## <a name="streaming-protocols-and-encryption-types"></a>Typy šifrování a streamování protokolů

Media Services můžete doručovat obsah zašifrovaný dynamicky pomocí nezašifrovaného klíče AES a DRM šifrování pomocí PlayReady, Widevine a FairPlay. V současné době můžete šifrovat formáty HTTP Live Streaming (HLS), MPEG DASH a Smooth Streaming. Každý protokol podporuje následující metody šifrování.

### <a name="hls"></a>HLS

Protokol HLS podporuje následující formáty kontejnerů a schémata šifrování.

|Formát kontejneru|Schéma šifrování|Příklad adresy URL|
|---|---|---|
|Vše|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2 TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2 TS |Šifrování CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |Šifrování CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + FairPlay (včetně HEVC/H. 265) se podporuje na následujících zařízeních:

* iOS 11 nebo novější 
* iPhone 8 nebo novější
* MacOS High Sierra s PROCESORem Intel 7 Generation

### <a name="mpeg-dash"></a>MPEG-DASH

Protokol MPEG-SPOJOVNÍK podporuje následující formáty kontejneru a schémata šifrování.

|Formát kontejneru|Schéma šifrování|Příklady adres URL
|---|---|---|
|Vše|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |Šifrování CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|Šifrování CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Technologie Smooth Streaming

Protokol Smooth Streaming podporuje následující formáty kontejneru a schémata šifrování.

|Protocol|Formát kontejneru|Schéma šifrování|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | Šifrování CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Prohlížeče

Běžné prohlížeče podporují následující klienty DRM:

|Browser|Šifrování|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>Řízení přístupu k obsahu

Můžete řídit, kdo má přístup k vašemu obsahu tím, že nakonfigurujete zásady obsahu klíčů. Služba Media Services podporuje více způsobů autorizace uživatelů, kteří žádají o klíč. Musíte nakonfigurovat obsahu klíče zásad. Klient (přehrávač) musí zásady splňovat, než tento klíč se dá doručit do klienta. Může mít obsahu klíče zásad *otevřete* nebo *token* omezení. 

Zásada pro klíč obsahu s omezeným přístupem se dá použít, když chcete licenci vydávat nikomu bez autorizace. Například pokud vaše tržby jsou založené na službě AD a nikoli na základě předplatného.  

Pomocí zásad klíčového obsahu omezeného tokenu se klíč obsahu pošle jenom klientovi, který prezentuje platný token JWT, nebo jednoduchý webový token (SWT) v žádosti o licenci nebo klíč. Tento token musí být vydán službou STS. 

Můžete použít Azure AD jako STS nebo nasadit vlastní STS. Služba tokenů zabezpečení musí být nakonfigurovaný k vytvoření tokenu podepsán zadaný klíč a vydávání deklarací identity, které jste zadali v konfiguraci omezení s tokenem. Služba Media Services License/Key Delivery Services vrátí požadovanou licenci nebo klíč klientovi, pokud existují obě tyto podmínky:

* Token je platný. 
* Deklarace identity v tokenu se shodují s požadavky nakonfigurovanými pro licenci nebo klíč.

Když konfigurujete zásady s omezením tokenu, musíte zadat primární ověřovací klíč, Vystavitel a parametry cílové skupiny. Primární ověřovací klíč obsahuje klíč, který byl token podepsán pomocí. Vystavitel je STS, který vydává token. Cílová skupina, někdy označovaná jako Scope, popisuje účel tokenu nebo prostředku, ke kterému token opravňuje přístup. Služba Media Services License/Key Delivery Services ověřuje, že se tyto hodnoty v tokenu shodují s hodnotami v šabloně.

### <a name="token-replay-prevention"></a>Prevence opětovného přehrání tokenu

Funkce *prevence* opětovného přehrání tokenu umožňuje Media Services zákazníkům nastavit limit, kolikrát se dá stejný token použít k vyžádání klíče nebo licence. Zákazník může přidat deklaraci identity typu `urn:microsoft:azure:mediaservices:maxuses` v tokenu, kde hodnota je počet, kolikrát je možné token použít k získání licence nebo klíče. Všechny následné požadavky se stejným tokenem na doručení klíče vrátí neautorizovanou odpověď. Podívejte se, jak přidat deklaraci identity v [ukázce DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Požadavky

* Zákazníci musí mít kontrolu nad generováním tokenu. Deklarace identity musí být umístěna do samotného tokenu.
* Při použití této funkce jsou požadavky s tokeny, jejichž čas vypršení platnosti je více než jedna hodina od doby přijetí žádosti, odmítnuty s neoprávněnou odpovědí.
* Tokeny se jednoznačně identifikují podle jejich signatury. Jakékoli změny datové části (například aktualizace na čas vypršení platnosti nebo deklarace identity) mění signaturu tokenu a počítají se jako nový token, který předá doručení klíče.
* Přehrávání se nezdařilo, pokud token `maxuses` překročil hodnotu nastavenou zákazníkem.
* Tato funkce se dá použít pro veškerý stávající chráněný obsah (musí se změnit jenom vydaný token).
* Tato funkce funguje s tokenem JWT i SWT.

## <a name="using-a-custom-sts"></a>Použití vlastní služby STS

Zákazník se může rozhodnout použít vlastní STS k poskytování tokenů. Mezi důvody patří:

* Zprostředkovatel identity (IDP) používaný zákazníkem nepodporuje službu STS. V takovém případě vlastních služeb STS, může být možnost.
* Zákazník může být nutné flexibilní nebo užší ovládací prvek integrovat službu tokenů zabezpečení zákazníka odběratele fakturačním systémem. 

   Například operátor služby [OTT](https://en.wikipedia.org/wiki/Over-the-top_media_services) může nabízet několik balíčků předplatitelů, jako jsou Premium, Basic a sport. Operátor může být vhodné tak, aby odpovídaly deklarací identity v tokenu s balíčkem předplatitele, tak, že jsou k dispozici pouze obsah v určitém balíčku. V takovém případě vlastních služeb STS nabízí potřebné flexibility a kontroly.
* Pokud chcete do tokenu zahrnout vlastní deklarace identity, které se mají vybrat mezi různými ContentKeyPolicyOptions s různými licenčními parametry DRM (licence k předplatnému a licence k pronájmu).
* Chcete-li zahrnout deklaraci identity představující identifikátor klíče obsahu klíče, ke kterému token uděluje přístup.

Při použití vlastních služeb STS musí udělali dvě změny:

* Když nakonfigurujete službu doručování licencí pro určitý prostředek, musíte zadat klíč zabezpečení použijí k ověření pomocí vlastních služeb STS místo platnost aktuálního klíče ze služby Azure AD.
* Při vygenerování tokenu JTW klíč zabezpečení je zadán místo aktuální X509 privátní klíč certifikátu ve službě Azure AD.

Existují dva typy bezpečnostních klíčů:

* Symetrický klíč: Stejný klíč se používá ke generování a ověření tokenu JWT.
* Asymetrický klíč: V certifikátu x509 se pomocí privátního klíče pro šifrování, vygenerování tokenu JWT a s veřejným klíčem k ověření tokenu používá pár klíčů veřejného privátního klíče.

Pokud používáte rozhraní .NET Framework a jazyka C# jako svou vývojovou platformu, X509 certifikát používaný pro asymetrické bezpečnostní klíč musí mít klíč délku aspoň 2048. Jde o požadavek třídy System.IdentityModel.Tokens.X509AsymmetricSecurityKey v rozhraní .NET Framework. V opačném případě je vyvolána následující výjimka: IDX10630: Hodnota System. IdentityModel. Tokens. X509AsymmetricSecurityKey pro podpis nemůže být menší než 2048 bitů.

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
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: Stejné jako předchozí šablona, pouze pro Widevine. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: Stejné jako předchozí šablona, pouze pro FairPlay.  

Příklad:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId`má hodnotu požadovaného klíče. Můžete použít `AlternativeMediaId` , pokud chcete mapovat požadavek na entitu na straně. `AlternativeMediaId` Můžete například použít k usnadnění vyhledávání oprávnění.

 Příklady REST, které používají vlastní licence nebo adresy URL pro získání klíčů, najdete v tématu [zásady streamování – vytvořit](https://docs.microsoft.com/rest/api/media/streamingpolicies/create).

## <a name="troubleshoot"></a>Řešení potíží

Pokud se zobrazí `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` chyba, ujistěte se, že zadáváte vhodné zásady streamování.

Pokud se zobrazí chyby, které končí `_NOT_SPECIFIED_IN_URL`, ujistěte se, že jste v adrese URL zadali formát šifrování. Příklad: `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Viz [protokoly streamování a typy šifrování](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="next-steps"></a>Další postup

* [Ochrana s využitím šifrování AES](protect-with-aes128.md)
* [Ochrana s využitím DRM](protect-with-drm.md)
* [Návrh sady multi-DRM pro ochranu obsahu pomocí řízení přístupu](design-multi-drm-system-with-access-control.md)
* [Šifrování na straně úložiště](storage-account-concept.md#storage-side-encryption)
* [Nejčastější dotazy](frequently-asked-questions.md)

