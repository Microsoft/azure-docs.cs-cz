---
title: Chraňte svůj obsah pomocí Azure Media Services | Microsoft Docs
description: Tento článek poskytuje přehled ochrany obsahu pomocí Azure Media Services V2.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: edffa2dddd0ec877a4b825a69a76fb158928c89f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016759"
---
# <a name="content-protection-overview"></a>Přehled ochrany obsahu

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-v-2-v-3-migration-introduction.md) .

Pomocí Azure Media Services můžete zabezpečit vaše média od okamžiku, kdy počítač opustí úložiště, zpracování a doručování. Pomocí Media Services můžete doručovat živý obsah na vyžádání a dynamicky šifrovaný pomocí standard AES (Advanced Encryption Standard) (AES-128) nebo kteréhokoli ze tří hlavních systémů DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine a Apple FairPlay. Media Services taky poskytuje službu pro doručování klíčů AES a licencí DRM (PlayReady, Widevine a FairPlay) autorizovaným klientům. 

Následující obrázek znázorňuje Media Services pracovní postup ochrany obsahu: 

![Ochrana technologií PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Tento článek vysvětluje koncepty a terminologii relevantní pro porozumění ochraně obsahu pomocí Media Services. Článek obsahuje také odkazy na články, které popisují, jak chránit obsah. 

## <a name="dynamic-encryption"></a>Dynamické šifrování

Pomocí Media Services můžete doručovat obsah dynamicky šifrovaný pomocí šifrovaného klíče AES nebo šifrování DRM pomocí PlayReady, Widevine nebo FairPlay. Pokud je obsah zašifrovaný pomocí nezašifrovaného klíče AES a pošle se přes HTTPS, není nejasný, dokud nedosáhne klienta. 

Každá metoda šifrování podporuje následující protokoly streamování:
 
- AES: MPEG-POMLČKa, Smooth Streaming a HLS
- PlayReady: MPEG-POMLČKa, Smooth Streaming a HLS
- Widevine: MPEG-POMLČKa
- FairPlay: HLS

Šifrování při progresivním stahování se nepodporuje. 

K šifrování assetu je potřeba k assetu přidružit šifrovací klíč obsahu a taky nakonfigurovat zásady autorizace pro tento klíč. Klíče obsahu lze určit nebo automaticky vygenerovat pomocí Media Services.

Také je potřeba nakonfigurovat zásady doručování prostředků. Pokud chcete streamovat zašifrovaný prostředek, ujistěte se, že jste určili, jak ho chcete doručit konfigurací zásady doručení assetu.

Když hráč vyžádá datový proud, Media Services použije zadaný klíč k dynamickému šifrování obsahu pomocí nezašifrovaného klíče AES nebo šifrování DRM. Aby byl datový proud dešifrován, přehrávač si vyžádá klíč od Media Services služby doručování klíčů. Aby bylo možné rozhodnout, jestli uživatel má oprávnění k získání klíče, vyhodnotí zásady autorizace, které jste zadali pro tento klíč.

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 Vymazat klíč a DRM
Zákazníci často chtějí, aby používali šifrování AES nebo systém DRM. Hlavním rozdílem mezi těmito dvěma systémy je to, že při šifrování AES se klíč obsahu přenáší klientovi v nešifrovaném formátu (v nejasném případě). V důsledku toho se klíč, který se používá k šifrování obsahu, dá zobrazit v síťovém trasování na klientovi v prostém textu. Šifrování standardu AES-128 je vhodné pro případy použití, kde se jedná o důvěryhodnou osobu (například k šifrování firemních videí distribuovaných v rámci společnosti, které mají zaměstnanci prohlížet).

PlayReady, Widevine a FairPlay poskytují vyšší úroveň šifrování v porovnání s šifrovaným šifrováním AES-128. Klíč obsahu se přenáší v zašifrovaném formátu. Šifrování se navíc zpracovává v zabezpečeném prostředí na úrovni operačního systému, kde je obtížnější útok na uživatele se zlými úmysly. Správa systému DRM se doporučuje pro případy použití, kdy prohlížeč nepředstavuje důvěryhodnou stranu a vyžadujete nejvyšší úroveň zabezpečení.

## <a name="storage-encryption"></a>Šifrování úložiště
Šifrování úložiště můžete použít k místnímu šifrování nešifrovaného obsahu pomocí šifrování AES 256-bit Encryption. Pak ho můžete odeslat do Azure Storage, kde je uložený zašifrovaný v klidovém stavu. Prostředky chráněné pomocí šifrování úložiště se před kódováním automaticky nešifrují a umístí do systému souborů EFS. Prostředky se volitelně znovu šifrují před odesláním zpět jako nového výstupního prostředku. Primárním případem použití šifrování úložiště je, že chcete zabezpečit vysoce kvalitní vstupní mediální soubory se silným šifrováním v klidovém provozu na disku.

Aby bylo možné doručovat prostředky zašifrované v úložišti, musíte nakonfigurovat zásady doručení assetu tak, aby Media Services vědět, jak chcete obsah doručovat. Předtím, než bude možné Asset streamovat, server streamování dešifruje a streamuje svůj obsah pomocí zadaných zásad doručení (například AES, běžné šifrování nebo bez šifrování).

## <a name="types-of-encryption"></a>Typy šifrování
PlayReady a Widevine využívají běžné šifrování (režim AES pro AES). FairPlay využívá šifrování AES CBC-Mode. AES-128 vymazání klíče šifrování používá šifrování obálek.

## <a name="licenses-and-keys-delivery-service"></a>Služba doručování licencí a klíčů
Media Services poskytuje službu pro doručování klíčů pro doručování licencí DRM (PlayReady, Widevine, FairPlay) a klíčů AES autorizovaným klientům. Můžete použít [Azure Portal](media-services-portal-protect-content.md), REST API nebo sadu Media Services SDK pro .NET ke konfiguraci zásad autorizace a ověřování pro vaše licence a klíče.

## <a name="control-content-access"></a>Řízení přístupu k obsahu
Konfigurací zásad autorizace klíče obsahu můžete určit, kdo má přístup k vašemu obsahu. Zásada autorizace klíče obsahu podporuje buď omezení Open, nebo token.

### <a name="open-authorization"></a>Otevřít autorizaci
V případě otevřené zásady autorizace se klíč obsahu pošle libovolnému klientovi (bez omezení).

### <a name="token-authorization"></a>Autorizace tokenu
Pomocí zásad autorizace s omezeným tokenem se klíč obsahu pošle jenom klientovi, který v žádosti o klíč nebo licenci prezentuje platný JSON Web Token (JWT) nebo jednoduchý webový token (SWT). Tento token musí být vydaný službou tokenu zabezpečení (STS). Můžete použít Azure Active Directory jako STS nebo nasadit vlastní STS. Služba STS musí být nakonfigurovaná tak, aby vytvořila token podepsaný pomocí zadaného klíče a vydávala deklarace identity, které jste zadali v konfiguraci omezení tokenu. Služba doručování Media Services Key vrátí požadovaný klíč nebo licenci klientovi, pokud je token platný a deklarace identity v tokenu se shodují s hodnotami nakonfigurovanými pro klíč nebo licenci.

Když konfigurujete zásady omezeného tokenu, musíte zadat primární ověřovací klíč, Vystavitel a parametry cílové skupiny. Primární ověřovací klíč obsahuje klíč, pomocí kterého byl token podepsán. Vystavitel je služba tokenů zabezpečení, která vydává token. Cílová skupina, někdy označovaná jako Scope, popisuje účel tokenu nebo prostředku, ke kterému token opravňuje přístup. Služba doručení Media Services Key ověřuje, že se tyto hodnoty v tokenu shodují s hodnotami v šabloně.

### <a name="token-replay-prevention"></a>Prevence opětovného přehrání tokenu

Funkce *Prevence opětovného přehrání tokenu* umožňuje Media Services zákazníkům nastavit limit, kolikrát se dá stejný token použít k vyžádání klíče nebo licence. Zákazník může přidat deklaraci identity typu `urn:microsoft:azure:mediaservices:maxuses` v tokenu, kde hodnota je počet, kolikrát je možné token použít k získání licence nebo klíče. Všechny následné požadavky se stejným tokenem na doručení klíče vrátí neautorizovanou odpověď. Podívejte se, jak přidat deklaraci identity v [ukázce DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Požadavky

* Zákazníci musí mít kontrolu nad generováním tokenu. Deklarace identity musí být umístěna do samotného tokenu.
* Při použití této funkce jsou požadavky s tokeny, jejichž čas vypršení platnosti je více než jedna hodina od doby přijetí žádosti, odmítnuty s neoprávněnou odpovědí.
* Tokeny se jednoznačně identifikují podle jejich signatury. Jakékoli změny v datové části (například aktualizace na čas vypršení platnosti nebo deklarace identity) mění signaturu tokenu a počítají se jako nový token, který nepřijde do výše uvedeného klíče.
* Přehrávání se nezdařilo, pokud token překročil `maxuses` hodnotu nastavenou zákazníkem.
* Tato funkce se dá použít pro veškerý stávající chráněný obsah (musí se změnit jenom vydaný token).
* Tato funkce funguje s tokenem JWT i SWT.

## <a name="streaming-urls"></a>Adresy URL streamování
Pokud je váš Asset zašifrovaný s více než jedním DRM, použijte šifrovací značku v adrese URL streamování: (Format = ' 3u8-AAPL ', Encryption = ' xxx ').

Platí následující důležité informace:

* Nelze zadat více než jeden typ šifrování.
* V případě, že se k assetu použilo jenom jedno šifrování, nemusí se v adrese URL zadávat typ šifrování.
* Typ šifrování rozlišuje velká a malá písmena.
* Lze zadat následující typy šifrování:

  * **CENC**: pro PlayReady nebo Widevine (běžné šifrování)
  * **cbcs-AAPL**: pro Fairplay (šifrování AES CBC)
  * **CBC**: pro šifrování obálek AES

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="next-steps"></a>Další kroky
Následující články popisují další kroky, které vám pomohou začít s ochranou obsahu:

* [Ochrana pomocí šifrování úložiště](media-services-rest-storage-encryption.md)
* [Ochrana pomocí šifrování AES](media-services-protect-with-aes128.md)
* [Ochrana pomocí PlayReady nebo Widevine](media-services-protect-with-playready-widevine.md)
* [Ochrana pomocí FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Související odkazy

* [Ověřování tokenu JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integrace Azure Media Services OWIN aplikace založené na MVC s Azure Active Directory a omezení doručení klíče obsahu na základě deklarací JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
