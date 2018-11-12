---
title: Chránit obsah pomocí služby Azure Media Services | Dokumentace Microsoftu
description: Tento článek přináší přehled ochrany obsahu pomocí služby Media Services.
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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 1c7454aead07c728d55ff2c309cca83a792aac88
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238345"
---
# <a name="content-protection-overview"></a>Přehled ochrany obsahu

Azure Media Services můžete použít k zabezpečení médií od okamžiku opuštění počítače přes úložiště, zpracování a dodání. Pomocí služby Media Services můžete doručovat na vyžádání a živého obsahu dynamicky šifrován Advanced Encryption Standard (AES-128) nebo některý z systémy tři hlavní digital rights management (DRM): Apple FairPlay, Microsoft PlayReady a Google Widevine. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM (PlayReady, Widevine a FairPlay) licence autorizovaným klientům. 

Následující obrázek ukazuje pracovní postup služby Media Services content protection: 

![Ochrana obsahu](./media/content-protection/content-protection.png)

&#42;*podporuje dynamické šifrování AES-128 "nezašifrovaný klíč", CBCS a CENC. Podrobnosti najdete v tématu Přehled funkcí pro podporu [tady](#streaming-protocols-and-encryption-types).*

Tento článek vysvětluje koncepty a terminologie relevantní pro pochopení ochrany obsahu pomocí služby Media Services. Tento článek obsahuje také [nejčastější dotazy k](#faq) části a poskytuje odkazy na články, které ukazují, jak chránit obsah. 

## <a name="main-components-of-the-content-protection-system"></a>Hlavní součásti systému Ochrana obsahu

Pro úspěšné dokončení návrhu "content protection" systému nebo aplikace, budete muset plně přehledu o rozsahu úsilí. Následující seznam obsahuje přehled o tři části, které je třeba k implementaci. 

1. Azure Media Services kódu
  
  * Šablony licencí PlayReady, Widevine a FairPlay. Šablony umožňují nakonfigurovat práva a oprávnění pro každou používané technologiemi DRM
  * Povolení doručování licencí, určíte logiku kontroly autorizace na základě deklarací identity do tokenů JWT
  * Symetrické klíče, streamování protokolů a odpovídající technologiemi DRM použije, definující šifrování DRM

  > [!NOTE]
  > Každý prostředek můžete zašifrovat i pomocí několika typů šifrování (AES-128, PlayReady, Widevine, FairPlay). V článku [Typy streamovacích protokolů a šifrování](#streaming-protocols-and-encryption-types) se dozvíte, jaké kombinace dávají smysl.
  
  Následující články popisují kroky pro šifrování obsahu pomocí AES a DRM: 
  
  * [Ochrana s využitím šifrování AES](protect-with-aes128.md)
  * [Ochrana s využitím DRM](protect-with-drm.md)

2. Přehrávač klientem DRM nebo AES. Přehrávač videa aplikaci založenou na přehrávač SDK (nativní nebo založené na prohlížeči) musí splňovat následující požadavky:
  * Přehrávač SDK podporuje potřebné klienti DRM
  * Přehrávač SDK podporuje požadované protokoly datových proudů: protokol Smooth, DASH nebo HLS
  * Přehrávač SDK musí být schopna zpracovávat předání tokenů JWT token v žádosti o získání licence
  
    Přehrávač můžete vytvořit pomocí [rozhraní API služby Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). Použít [rozhraní API služby Azure Media Player ProtectionInfo](http://amp.azure.net/libs/amp/latest/docs/) zadat technologii DRM, která má používat na různých platformách DRM.

    Pro testování AES nebo šifrování CENC (Widevine a/nebo technologií PlayReady) zašifrovaný obsah, můžete použít [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html). Ujistěte se, že klikněte na "Pokročilé možnosti" a zkontrolujte možnosti šifrování.

    Pokud chcete testovat FairPlay zašifrovaný obsah, použijte [tento test přehrávač](https://aka.ms/amtest). Přehrávač podporuje Widevine, PlayReady, a technologiemi FairPlay DRM a standardu AES-128 s nezašifrovaným klíčem. Musíte zvolit správný prohlížeče k testování různých technologiemi DRM: Chrome/Opera/Firefox pro MS Edge/11 pro PlayReady, Widevine, Safari v systému macOS pro FairPlay.

3. Zabezpečení služby tokenů (STS), která vydává JSON Web Token (JWT) jako přístupový token pro přístup k prostředkům back-endu. AMS služeb doručování licencí můžete použít jako back-endový prostředek. Služby STS musí definovat následující:

  * Vystavitel a cílové skupiny (nebo rozsah)
  * Deklarace identity, které jsou závislé na obchodních požadavcích v ochrana obsahu
  * Symetrický, nebo asymetrický ověření pro ověření podpisu
  * Podpora výměny klíčů (v případě potřeby)

    Můžete použít [tento nástroj STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) testu službu tokenů zabezpečení, která podporuje všechny 3 typy ověřovací klíč: symetrické, asymetrické, nebo AAD s výměny klíčů. 

> [!NOTE]
> Důrazně doporučujeme se můžete soustředit a plně otestovat každou část (popsaných výše) před přesunutím do další části. K otestování "content protection" systému, použijte nástroje uvedené v seznamu výše.  

## <a name="streaming-protocols-and-encryption-types"></a>Typy šifrování a streamování protokolů

Media Services můžete doručovat obsah zašifrovaný dynamicky pomocí nezašifrovaného klíče AES a DRM šifrování pomocí PlayReady, Widevine a FairPlay. V současné době můžete šifrovat formáty HTTP Live Streaming (HLS), MPEG DASH a Smooth Streaming. Všechny protokoly, které podporuje následující způsoby šifrování:

|Protocol (Protokol)|Formát kontejneru|Schéma šifrování|
|---|---|---|---|
|MPEG-DASH|Vše|AES|
||CSF(fmp4) |Šifrování CENC (Widevine + PlayReady) |
||CMAF(fmp4)|Šifrování CENC (Widevine + PlayReady)|
|HLS|Vše|AES|
||MPG2 TS |CBCS (Fairplay) |
||MPG2 TS |Šifrování CENC (PlayReady) |
||CMAF(fmp4) |Šifrování CENC (PlayReady) |
|Technologie Smooth Streaming|fMP4|AES|
||fMP4 | Šifrování CENC (PlayReady) |

## <a name="dynamic-encryption"></a>Dynamické šifrování

V Media Services v3 klíč obsahu je přiřazena StreamingLocator (viz [v tomto příkladu](protect-with-aes128.md)). Pokud používáte doručení klíče služby Media Services, můžete automaticky generovat klíče k obsahu. Klíč obsahu by měl generovat sami Pokud jste se pomocí vlastní doručení klíče služby, nebo pokud potřebujete zpracovávat scénáře vysoké dostupnosti, kde je potřeba mít stejné klíče k obsahu ve dvou datových centrech.

Datový proud je žádost přehrávač, Media Services využívá se zadaným klíčem k dynamické šifrování obsahu pomocí nezašifrovaného klíče AES a DRM šifrování. Přehrávač dešifrovat datového proudu, vyžaduje klíč z doručení klíče služby Media Services nebo doručení klíče, který jste zadali. Rozhodování o tom, zda je uživatel oprávnění k získání klíče, služba vyhodnocuje obsahu klíče zásadami, které jste zadali pro klíč.

## <a name="aes-128-clear-key-vs-drm"></a>Vs nezašifrovaného klíče AES-128. DRM

Zákazníci často zajímat, zda by měl používat šifrování AES nebo systému DRM. Hlavní rozdíl mezi těmito dvěma systémy je, že se šifrováním AES klíč obsahu přenášena na klienty v nezašifrované podobě ("v nezašifrované podobě"). V důsledku toho klíč používaný k šifrování obsahu lze zobrazit v síťového trasování na straně klienta ve formátu prostého textu. Šifrování nezašifrovaného klíče AES-128 je vhodný pro případy použití, ve kterém je prohlížeč důvěryhodné strany (například šifrování firemních videí distribuované v rámci společnosti prohlížení zaměstnanci).

PlayReady, Widevine a FairPlay AES-128 všechny poskytovat vyšší úroveň šifrování ve srovnání s nezašifrovaným klíčem. Klíč obsahu se přenášejí v zašifrovaném formátu. Kromě toho se v zabezpečeném prostředí na úrovni operačního systému, kde je obtížnější uživatel se zlými úmysly k útoku na zpracovává dešifrování. DRM se doporučuje pro případy použití, kdy prohlížeč pravděpodobně nebude důvěryhodná strana a vyžadujete nejvyšší úroveň zabezpečení.

## <a name="storage-side-encryption"></a>Šifrování na straně úložiště

K ochraně vašich prostředků v klidovém stavu, prostředky by se měla šifrovat pomocí šifrování na straně úložiště. Následující tabulka ukazuje, jak funguje šifrování na straně úložiště v Media Services v3:

|Možnost šifrování|Popis|Media Services v3|
|---|---|---|---|
|Šifrování úložiště služby Media Services| Šifrování AES-256, klíče spravované službou Media Services|Nepodporuje<sup>(1)</sup>|
|[Šifrování služby Storage pro neaktivní uložená Data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Šifrování na straně serveru nabízených službou Azure Storage, klíče spravované službou Azure nebo podle zákazníka|Podporováno|
|[Šifrování na straně klienta úložiště](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Šifrování na straně klienta nabízené službou Azure storage, klíče spravované zákazníkem ve službě Key Vault|Nepodporuje se|

<sup>1</sup> v Media Services v3 šifrování úložiště (šifrování AES-256) je pouze podporována pro zpětné kompatibility při vaše prostředky se vytvořily pomocí Media Services v2. To znamená v3 spolupracuje s existující úložiště šifrované prostředky, ale nebude umožňovat vytváření nových.

## <a name="licenses-and-keys-delivery-service"></a>Službu doručování licencí a klíčů

Služba Media Services poskytuje doručení klíče služby pro doručování licencí DRM (PlayReady, Widevine, FairPlay) a klíče AES autorizovaným klientům. Rozhraní REST API nebo klientskou knihovnu služby Media Services můžete použít ke konfiguraci zásad ověřování a ověřování licencí a klíčů.

## <a name="control-content-access"></a>Přístup k obsahu ovládacího prvku

Můžete řídit, kdo má přístup k vašemu obsahu tím, že nakonfigurujete zásady obsahu klíčů. Služba Media Services podporuje více způsobů ověřování uživatelů, kteří žádají o klíč. Musíte nakonfigurovat obsahu klíče zásad. Klient (přehrávač) musí zásady splňovat, než tento klíč se dá doručit do klienta. Může mít obsahu klíče zásad **otevřete** nebo **token** omezení. 

Pomocí omezení tokenem obsahu klíče zásad pouze na klienta, který představuje jednoduchý webový token (SWT) nebo platný JSON Web Token (JWT) v žádosti o správu klíčů/licencí přijde klíč obsahu. Tento token musí být vystavené služby tokenů zabezpečení (STS). Můžete použít Azure Active Directory jako služba STS nebo nasadit vlastní službu STS. Služba tokenů zabezpečení musí být nakonfigurovaný k vytvoření tokenu podepsán zadaný klíč a vydávání deklarací identity, které jste zadali v konfiguraci omezení s tokenem. Doručení klíče služby Media Services vrátí klientovi požadovaný klíčů/licencí, pokud je token platný a deklarace identity v tokenu odpovídají těm nakonfigurovaný pro klíčů/licencí.

Když konfigurujete zásady omezení tokenem, musíte zadat primární ověřovací klíč, vydavatele a parametry cílovou skupinu. Primární ověřovací klíč obsahuje klíč, který byl token podepsán pomocí. Vystavitel je služba tokenů zabezpečení, který vydá token. Cílové skupiny, někdy označuje jako obor, by měl popisovat záměr tokenu nebo prostředek token, který autorizuje přístup k. Služba Media Services doručení klíče ověří, že tyto hodnoty v tokenu odpovídají hodnotám v šabloně.

## <a name="a-idfaqfrequently-asked-questions"></a><a id="faq"/>Nejčastější dotazy

### <a name="question"></a>Otázka

Jak implementovat několika variant DRM (PlayReady, Widevine a FairPlay) systému pomocí Azure Media Services (AMS) v3 a také AMS službu doručování klíčů/licencí?

### <a name="answer"></a>Odpověď

Scénář začátku do konce, najdete v článku [následující ukázka kódu](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs). 

Příklad ukazuje postup:

1. Vytvoření a konfigurace ContentKeyPolicies.

  Ukázka obsahuje funkce, které konfigurace [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md), a [FairPlay](fairplay-license-overview.md) licence.

    ```
    ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
    ```

2. Vytvoření StreamingLocator, nakonfigurovaný tak, aby Streamovat prostředek šifrované. 

  Můžete například nastavit StreamingLocator.StreamingPolicyName zásady "Predefined_MultiDrmCencStreaming". Tato zásada udává, že chcete vygenerovat a nastavit v lokátoru dva symetrické klíče (obálku a CENC). Tím dojde k nastavení obálky a šifrování PlayReady a Widevine (klíč se doručí klientovi pro přehrávání na základě nakonfigurovaných licencí DRM). Pokud zároveň chcete svůj stream zašifrovat pomocí CBCS (FairPlay), použijte zásadu Predefined_MultiDrmStreaming.

3. Vytvořte token testu.

  **GetTokenAsync** metoda ukazuje, jak vytvořit testovací token.
  
4. Sestavte adresu URL streamování.

  **GetDASHStreamingUrlAsync** metoda ukazuje, jak sestavit adresu URL streamování. V tomto případě adresy URL datové proudy **DASH** obsah.

### <a name="question"></a>Otázka

Jak a kde se získat token JWT před jeho použitím žádosti o licenci nebo klíč?

### <a name="answer"></a>Odpověď

1. Pro produkční prostředí musíte mít zabezpečení Token služby (STS) (webová služba) který vystaví token JWT na vyžádání protokolu HTTPS. Pro testování, můžete použít kód zobrazený v **GetTokenAsync** metody definované v [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Přehrávač, bude nutné vytvořit žádost, po ověření uživatele, na službu STS pro takový token a přiřaďte ji jako hodnotu tokenu. Můžete použít [rozhraní API služby Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/).

* Příkladem s symetrické i asymetrické klíč služby tokenů zabezpečení, najdete v tématu [ http://aka.ms/jwt ](https://aka.ms/jwt). 
* Příklad přehrávač založené na Azure Media Player pomocí těchto tokenu JWT, naleznete v tématu [ http://aka.ms/amtest ](https://aka.ms/amtest) (expand na token vstup odkaz "player_settings").

### <a name="question"></a>Otázka

Jak autorizujete požadavky na datový proud videa s využitím šifrování AES

### <a name="answer"></a>Odpověď

Správný přístup je využívat služby tokenů zabezpečení (zabezpečení Token Service):

Služba tokenů zabezpečení v závislosti na profil uživatele, přidejte různé deklarace (například "Premium User", "Základní uživatel", "Uživatele bezplatné zkušební verze"). S jinou deklarací v token JWT uživatel uvidí jiný obsah. Samozřejmě pro jiný obsah nebo prostředek, bude mít ContentKeyPolicyRestriction odpovídající RequiredClaims.

Licence/klíč API služby Azure Media Services použijte ke konfiguraci doručování a šifruje vaše prostředky (jak je znázorněno v [této ukázce](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs).

## <a name="next-steps"></a>Další postup

Projděte si následující články:

  * [Ochrana s využitím šifrování AES](protect-with-aes128.md)
  * [Ochrana s využitím DRM](protect-with-drm.md)

Další informace najdete v [návrhu systému s více variantami drm ochrany obsahu pomocí řízení přístupu](design-multi-drm-system-with-access-control.md)


