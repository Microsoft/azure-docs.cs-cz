---
title: Ochrana obsahu pomocí Media Services dynamického šifrování – Azure | Microsoft Docs
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
ms.date: 07/17/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 5d31e4a523fdedf9907e33c70638f07a08461ed1
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310302"
---
# <a name="content-protection-with-dynamic-encryption"></a>Ochrana obsahu s dynamickým šifrováním

Azure Media Services můžete použít k zabezpečení médií od okamžiku opuštění počítače přes úložiště, zpracování a dodání. Pomocí Media Services můžete doručovat živý obsah na vyžádání a dynamicky šifrovaný pomocí standard AES (Advanced Encryption Standard) (AES-128) nebo kteréhokoli ze tří hlavních systémů DRM (Správa digitálních práv): Microsoft PlayReady, Google Widevine a Apple FairPlay. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM (PlayReady, Widevine a FairPlay) licence autorizovaným klientům. 

V Media Services V3 je klíč obsahu přidružený k lokátoru streamování (viz [Tento příklad](protect-with-aes128.md)). Pokud používáte službu doručování klíčů Media Services, můžete nechat Azure Media Services vygenerovat klíč obsahu. Klíč obsahu byste měli vygenerovat sami, pokud používáte vlastní službu pro doručování klíčů, nebo pokud potřebujete zpracovat scénář s vysokou dostupností, ke kterému potřebujete stejný klíč obsahu ve dvou datových centrech.

Datový proud je žádost přehrávač, Media Services využívá se zadaným klíčem k dynamické šifrování obsahu pomocí nezašifrovaného klíče AES a DRM šifrování. Přehrávač dešifrovat datového proudu, vyžaduje klíč z doručení klíče služby Media Services nebo doručení klíče, který jste zadali. Rozhodování o tom, zda je uživatel oprávnění k získání klíče, služba vyhodnocuje obsahu klíče zásadami, které jste zadali pro klíč.

Rozhraní REST API nebo klientskou knihovnu služby Media Services můžete použít ke konfiguraci zásad ověřování a ověřování licencí a klíčů.

Následující obrázek ukazuje pracovní postup služby Media Services content protection: 

![Ochrana obsahu](./media/content-protection/content-protection.svg)

&#42;*podporuje dynamické šifrování AES-128 "nezašifrovaný klíč", CBCS a CENC. Podrobnosti najdete v tématu Přehled funkcí pro podporu [tady](#streaming-protocols-and-encryption-types).*

Tento článek vysvětluje koncepty a terminologie relevantní pro pochopení ochrany obsahu pomocí služby Media Services.

## <a name="main-components-of-a-content-protection-system"></a>Hlavní součásti systému ochrany obsahu

Pro úspěšné dokončení návrhu "content protection" systému nebo aplikace, budete muset plně přehledu o rozsahu úsilí. Následující seznam obsahuje přehled o tři části, které je třeba k implementaci. 

1. Azure Media Services kódu
  
   Ukázka [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) vám ukáže, jak implementovat systém s více DRM pomocí Media Services V3 pomocí .NET. Také ukazuje, jak používat Media Services licencování a služby doručování klíčů. Každý prostředek můžete zašifrovat i pomocí několika typů šifrování (AES-128, PlayReady, Widevine, FairPlay). V článku [Typy streamovacích protokolů a šifrování](#streaming-protocols-and-encryption-types) se dozvíte, jaké kombinace dávají smysl.
  
   Příklad ukazuje postup:

   1. Vytvořte a nakonfigurujte [zásady klíče obsahu](content-key-policy-concept.md). Vytvoříte zásadu pro **klíč obsahu** , která umožní nakonfigurovat způsob doručení klíče obsahu (který poskytuje zabezpečený přístup k vašim prostředkům) koncovým klientům.    

      * Definujte autorizaci pro doručování licencí a určete logiku kontroly autorizace na základě deklarací identity v tokenu JWT.
      * Nakonfigurujte licence [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md)a [Fairplay](fairplay-license-overview.md) . Šablony vám umožní nakonfigurovat práva a oprávnění pro jednotlivé použité několikanásobnou.

        ```
        ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
        ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
        ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
        ```
   2. Vytvořte [Lokátor streamování](streaming-locators-concept.md) , který je nakonfigurovaný pro streamování šifrovaného prostředku. 
  
      **Lokátor streamování** musí být přidružený k [zásadě streamování](streaming-policy-concept.md). V tomto příkladu nastavíme StreamingLocator. StreamingPolicyName na zásadu "Predefined_MultiDrmCencStreaming". Používají se šifrování PlayReady a Widevine, klíč se doručuje klientovi pro přehrávání na základě konfigurovaných licencí DRM. Pokud zároveň chcete svůj stream zašifrovat pomocí CBCS (FairPlay), použijte zásadu Predefined_MultiDrmStreaming.
      
      Lokátor streamování je také přidružen k **zásadám klíče obsahu** , které byly definovány.
    
   3. Vytvořte token testu.

      **GetTokenAsync** metoda ukazuje, jak vytvořit testovací token.
   4. Sestavte adresu URL streamování.

      **GetDASHStreamingUrlAsync** metoda ukazuje, jak sestavit adresu URL streamování. V tomto případě adresy URL datové proudy **DASH** obsah.

2. Přehrávač klientem DRM nebo AES. Přehrávač videa aplikaci založenou na přehrávač SDK (nativní nebo založené na prohlížeči) musí splňovat následující požadavky:
   * Přehrávač SDK podporuje potřebné klienti DRM
   * Sada Player SDK podporuje požadované protokoly streamování: Hladké, PŘERUŠOVANé a/nebo HLS
   * Přehrávač SDK musí být schopna zpracovávat předání tokenů JWT token v žádosti o získání licence
  
     Přehrávač můžete vytvořit pomocí [rozhraní API služby Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/). Použít [rozhraní API služby Azure Media Player ProtectionInfo](https://amp.azure.net/libs/amp/latest/docs/) zadat technologii DRM, která má používat na různých platformách DRM.

     Pro testování AES nebo šifrování CENC (Widevine a/nebo technologií PlayReady) zašifrovaný obsah, můžete použít [Azure Media Player](https://aka.ms/azuremediaplayer). Ujistěte se, že klikněte na "Pokročilé možnosti" a zkontrolujte možnosti šifrování.

     Pokud chcete testovat FairPlay zašifrovaný obsah, použijte [tento test přehrávač](https://aka.ms/amtest). Přehrávač podporuje Widevine, PlayReady, a technologiemi FairPlay DRM a standardu AES-128 s nezašifrovaným klíčem. 
    
     Musíte zvolit správný prohlížeč pro otestování různých několikanásobnou: Chrome/Opera/Firefox pro Widevine, Microsoft Edge/IE11 pro PlayReady, Safari v macOS pro FairPlay.

3. Zabezpečení služby tokenů (STS), která vydává JSON Web Token (JWT) jako přístupový token pro přístup k prostředkům back-endu. AMS služeb doručování licencí můžete použít jako back-endový prostředek. Služby STS musí definovat následující:

   * Vystavitel a cílové skupiny (nebo rozsah)
   * Deklarace identity, které jsou závislé na obchodních požadavcích v ochrana obsahu
   * Symetrický, nebo asymetrický ověření pro ověření podpisu
   * Podpora výměny klíčů (v případě potřeby)

     [Tento nástroj STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) můžete použít k otestování služby STS, která podporuje všechny 3 typy ověřovacího klíče: symetrický, asymetrický nebo Azure AD s použitím klíčového přechodu. 

> [!NOTE]
> Důrazně doporučujeme se můžete soustředit a plně otestovat každou část (popsaných výše) před přesunutím do další části. K otestování "content protection" systému, použijte nástroje uvedené v seznamu výše.  

## <a name="streaming-protocols-and-encryption-types"></a>Typy šifrování a streamování protokolů

Media Services můžete doručovat obsah zašifrovaný dynamicky pomocí nezašifrovaného klíče AES a DRM šifrování pomocí PlayReady, Widevine a FairPlay. V současné době můžete šifrovat formáty HTTP Live Streaming (HLS), MPEG DASH a Smooth Streaming. Všechny protokoly, které podporuje následující způsoby šifrování:

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

* iOS V11 nebo novější 
* iPhone 8 nebo novější
* MacOS High Sierra s Intel 7 gen CPU

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
|Microsoft Edge, IE 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="control-content-access"></a>Přístup k obsahu ovládacího prvku

Můžete řídit, kdo má přístup k vašemu obsahu tím, že nakonfigurujete zásady obsahu klíčů. Služba Media Services podporuje více způsobů autorizace uživatelů, kteří žádají o klíč. Musíte nakonfigurovat obsahu klíče zásad. Klient (přehrávač) musí zásady splňovat, než tento klíč se dá doručit do klienta. Může mít obsahu klíče zásad **otevřete** nebo **token** omezení. 

Pomocí omezení tokenem obsahu klíče zásad pouze na klienta, který představuje jednoduchý webový token (SWT) nebo platný JSON Web Token (JWT) v žádosti o správu klíčů/licencí přijde klíč obsahu. Tento token musí být vystavené služby tokenů zabezpečení (STS). Můžete použít Azure Active Directory jako služba STS nebo nasadit vlastní službu STS. Služba tokenů zabezpečení musí být nakonfigurovaný k vytvoření tokenu podepsán zadaný klíč a vydávání deklarací identity, které jste zadali v konfiguraci omezení s tokenem. Doručení klíče služby Media Services vrátí klientovi požadovaný klíčů/licencí, pokud je token platný a deklarace identity v tokenu odpovídají těm nakonfigurovaný pro klíčů/licencí.

Když konfigurujete zásady omezení tokenem, musíte zadat primární ověřovací klíč, vydavatele a parametry cílovou skupinu. Primární ověřovací klíč obsahuje klíč, který byl token podepsán pomocí. Vystavitel je služba tokenů zabezpečení, který vydá token. Cílové skupiny, někdy označuje jako obor, by měl popisovat záměr tokenu nebo prostředek token, který autorizuje přístup k. Služba Media Services doručení klíče ověří, že tyto hodnoty v tokenu odpovídají hodnotám v šabloně.

Zákazníci často používají vlastní STS k zahrnutí vlastních deklarací identity do tokenu k výběru mezi různými ContentKeyPolicyOptionsy s různými licenčními parametry DRM (licence k předplatnému oproti licenci na pronájem) nebo zahrnutím deklarace identity představující klíč obsahu. identifikátor klíče, ke kterému token uděluje přístup

### <a name="token-replay-prevention"></a>Prevence opětovného přehrání tokenu

Funkce *prevence* opětovného přehrání tokenu umožňuje Media Services zákazníkům nastavit limit, kolikrát se dá stejný token použít k vyžádání klíče nebo licence. Zákazník může přidat deklaraci identity typu `urn:microsoft:azure:mediaservices:maxuses` v tokenu, kde hodnota je počet, kolikrát je možné token použít k získání licence nebo klíče. Všechny následné požadavky se stejným tokenem na doručení klíče vrátí neautorizovanou odpověď. Podívejte se, jak přidat deklaraci identity v [ukázce DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Požadavky

* Zákazníci musí mít kontrolu nad generováním tokenu. Deklarace identity musí být umístěna do samotného tokenu.
* Při použití této funkce jsou požadavky s tokeny, jejichž čas vypršení platnosti je více než jedna hodina od doby přijetí žádosti, odmítnuty s neoprávněnou odpovědí.
* Tokeny se jednoznačně identifikují podle jejich signatury. Jakékoli změny datové části (například aktualizace na čas vypršení platnosti nebo deklarace identity) mění signaturu tokenu a počítají se jako nový token, který předá doručení klíče.
* Přehrávání se nezdařilo, pokud token `maxuses` překročil hodnotu nastavenou zákazníkem.
* Tato funkce se dá použít pro veškerý stávající chráněný obsah (musí se změnit jenom vydaný token).
* Tato funkce funguje s tokenem JWT i SWT.

## <a name="custom-key-and-license-acquisition-url"></a>Adresa URL vlastního klíče a získání licence

Následující šablony použijte, pokud chcete zadat jiný klíč a službu pro doručování licencí (ne Media Services). Dvě umístěná pole v šablonách jsou zde, takže můžete sdílet zásady streamování napříč mnoha prostředky namísto vytváření zásad streamování na jeden prostředek. 

* EnvelopeEncryption. CustomKeyAcquisitionUrlTemplate – šablona pro adresu URL vlastní služby, která doručuje klíče koncovým uživatelům. Není vyžadováno při použití Azure Media Services pro vydávání klíčů. Šablona podporuje nahraditelné tokeny, které služba aktualizuje za běhu s hodnotou specifickou pro požadavek.  Aktuálně podporované hodnoty tokenu jsou {AlternativeMediaId}, které se nahradí hodnotou StreamingLocatorId. AlternativeMediaId a {ContentKeyId}, která je nahrazena hodnotou identifikátoru požadovaného klíče.
* StreamingPolicyPlayReadyConfiguration. CustomLicenseAcquisitionUrlTemplate – šablona pro adresu URL vlastní služby, která poskytuje licence přehrávačům koncových uživatelů. Nepožadováno při použití Azure Media Services k vystavování licencí. Šablona podporuje nahraditelné tokeny, které služba aktualizuje za běhu s hodnotou specifickou pro požadavek. Aktuálně podporované hodnoty tokenu jsou {AlternativeMediaId}, které se nahradí hodnotou StreamingLocatorId. AlternativeMediaId a {ContentKeyId}, která je nahrazena hodnotou identifikátoru požadovaného klíče. 
* StreamingPolicyWidevineConfiguration. CustomLicenseAcquisitionUrlTemplate – totéž jako výše, pouze pro Widevine. 
* StreamingPolicyFairPlayConfiguration. CustomLicenseAcquisitionUrlTemplate – totéž jako výše, pouze pro FairPlay.  

Příklad:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

Má hodnotu požadovaného klíče a lze jej použít, `AlternativeMediaId` Pokud chcete vytvořit mapování požadavku na entitu na straně. `ContentKeyId` `AlternativeMediaId` Můžete například použít k usnadnění vyhledávání oprávnění.

Příklady REST, které používají vlastní klíče a adresy URL pro získání licencí, najdete v tématu [zásady streamování – vytvořit](https://docs.microsoft.com/rest/api/media/streamingpolicies/create) .

## <a name="troubleshoot"></a>Řešení potíží

Pokud se zobrazí `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` chyba, ujistěte se, že zadáváte vhodné zásady streamování.

Pokud se zobrazí chyby, které končí `_NOT_SPECIFIED_IN_URL`, ujistěte se, že jste v adrese URL zadali formát šifrování. Například, `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Viz [protokoly streamování a typy šifrování](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="next-steps"></a>Další postup

* [Ochrana s využitím šifrování AES](protect-with-aes128.md)
* [Ochrana s využitím DRM](protect-with-drm.md)
* [Návrh sady multi-DRM pro ochranu obsahu pomocí řízení přístupu](design-multi-drm-system-with-access-control.md)
* [Šifrování na straně úložiště](storage-account-concept.md#storage-side-encryption)
* [Nejčastější dotazy](frequently-asked-questions.md)

