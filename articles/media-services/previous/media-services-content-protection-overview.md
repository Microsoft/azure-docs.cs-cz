---
title: Chránit obsah pomocí služby Azure Media Services | Dokumentace Microsoftu
description: Tento článek přináší přehled ochrany obsahu pomocí služby Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2018
ms.author: juliako
ms.openlocfilehash: 63cf7633a2280682b3a3da7e8939e71e83ee8f3b
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342106"
---
# <a name="content-protection-overview"></a>Přehled ochrany obsahu
 Azure Media Services můžete použít k zabezpečení médií od okamžiku opuštění počítače přes úložiště, zpracování a dodání. Pomocí služby Media Services můžete doručovat na vyžádání a živého obsahu dynamicky šifrován Advanced Encryption Standard (AES-128) nebo některý z systémy tři hlavní digital rights management (DRM): Apple FairPlay, Microsoft PlayReady a Google Widevine. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM (PlayReady, Widevine a FairPlay) licence autorizovaným klientům. 

Následující obrázek ukazuje pracovní postup služby Media Services content protection: 

![Ochrana technologií PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Tento článek vysvětluje koncepty a terminologie relevantní pro pochopení ochrany obsahu pomocí služby Media Services. Článek taky obsahuje odkazy na články, které se zabývají k ochraně obsahu. 

## <a name="dynamic-encryption"></a>Dynamické šifrování
 Media Services můžete doručovat obsah zašifrovaný dynamicky pomocí nezašifrovaného klíče AES a DRM šifrování pomocí PlayReady, Widevine a FairPlay. V současné době můžete šifrovat formáty HTTP Live Streaming (HLS), MPEG DASH a Smooth Streaming. Šifrování na progresivní stahování se nepodporuje. Každá metoda šifrování se podporuje následující protokoly pro streamování:

- AES: MPEG-DASH, Smooth Streaming a HLS
- PlayReady: MPEG-DASH, Smooth Streaming a HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

K šifrování assetu, musíte přidružit asset šifrovací klíč obsahu a také konfigurace zásad autorizace pro klíč. Symetrické klíče můžete zadat nebo automaticky generuje služba Media Services.

Také musíte nakonfigurovat zásady doručení assetu. Pokud chcete Streamovat k šifrování úložiště assetu, ujistěte se, že k určení způsobu poskytovaným konfigurace zásad doručení assetu.

Datový proud je žádost přehrávač, Media Services využívá se zadaným klíčem k dynamické šifrování obsahu pomocí nezašifrovaného klíče AES a DRM šifrování. K dešifrování datového proudu, vyžaduje přehrávač klíč z doručení klíče služby Media Services. Rozhodování o tom, zda je uživatel oprávnění k získání klíče, služba vyhodnocuje zásady autorizace, které jste zadali pro klíč.

## <a name="aes-128-clear-key-vs-drm"></a>Vs nezašifrovaného klíče AES-128. DRM
Zákazníci často zajímat, zda by měl používat šifrování AES nebo systému DRM. Hlavní rozdíl mezi těmito dvěma systémy je, že se šifrováním AES klíč obsahu přenášena na klienty v nezašifrované podobě ("v nezašifrované podobě"). V důsledku toho klíč používaný k šifrování obsahu lze zobrazit v síťového trasování na straně klienta ve formátu prostého textu. Šifrování nezašifrovaného klíče AES-128 je vhodný pro případy použití, ve kterém je prohlížeč důvěryhodné strany (například šifrování firemních videí distribuované v rámci společnosti prohlížení zaměstnanci).

PlayReady, Widevine a FairPlay AES-128 všechny poskytovat vyšší úroveň šifrování ve srovnání s nezašifrovaným klíčem. Klíč obsahu se přenášejí v zašifrovaném formátu. Kromě toho se v zabezpečeném prostředí na úrovni operačního systému, kde je obtížnější uživatel se zlými úmysly k útoku na zpracovává dešifrování. DRM se doporučuje pro případy použití, kdy prohlížeč pravděpodobně nebude důvěryhodná strana a vyžadujete nejvyšší úroveň zabezpečení.

## <a name="storage-encryption"></a>Šifrování úložiště
Šifrování úložiště můžete použít k zašifrování obsahu místně s použitím šifrování AES 256 bitů. Potom můžete nahrát ho do služby Azure Storage, kde jsou uložená v klidovém stavu zašifrovaná. Prostředky chráněné pomocí šifrování úložiště jsou automaticky nešifrované a umístěné v systému souborů EFS před kódováním. Prostředky jsou volitelně znovu zašifrovat před odesláním zpět v podobě nového výstupního prostředku. Případem primárního použití šifrování úložiště je, pokud chcete zajistit vysoce kvalitními vstupními multimediálními soubory pomocí silného šifrování v klidovém stavu na disku.

K zajištění k šifrování úložiště assetu, musíte nakonfigurovat zásady doručení assetu tak, aby služba Media Services ví, jak chcete dodávat váš obsah. Předtím, než můžete Streamovat prostředek, server streamování dešifruje a streamování vašeho obsahu s využitím zadaného doručování zásad (například AES, používat standard common encryption nebo bez šifrování).

## <a name="types-of-encryption"></a>Typy šifrování
PlayReady a Widevine použít obecné šifrování (AES PEV.cenu režim). FairPlay využívá šifrování AES-CBC-režimu. Šifrování nezašifrovaného klíče AES-128 využívá šifrování obálky.

## <a name="licenses-and-keys-delivery-service"></a>Službu doručování licencí a klíčů
Služba Media Services poskytuje doručení klíče služby pro doručování licencí DRM (PlayReady, Widevine, FairPlay) a klíče AES autorizovaným klientům. Můžete použít [na webu Azure portal](media-services-portal-protect-content.md), rozhraní REST API nebo Media Services SDK pro .NET konfigurace zásad autorizace a ověřování licencí a klíčů.

## <a name="control-content-access"></a>Přístup k obsahu ovládacího prvku
Můžete řídit, kdo má přístup k vašemu obsahu tím, že nakonfigurujete zásady autorizace klíče obsahu. Zásady autorizace klíče obsahu podporuje omezení buď otevřené nebo s tokenem.

### <a name="open-authorization"></a>Otevřít autorizace
Pomocí zásad autorizace otevřít přijde klíč obsahu libovolnému klientovi (bez omezení).

### <a name="token-authorization"></a>Token autorizace
Zásady omezení tokenem autorizace klíče k obsahu odesláno pouze pro klienta, který představuje jednoduchý webový token (SWT) nebo platný JSON Web Token (JWT) v žádosti o správu klíčů/licencí. Tento token musí být vystavené služby tokenů zabezpečení (STS). Můžete použít Azure Active Directory jako služba STS nebo nasadit vlastní službu STS. Služba tokenů zabezpečení musí být nakonfigurovaný k vytvoření tokenu podepsán zadaný klíč a vydávání deklarací identity, které jste zadali v konfiguraci omezení s tokenem. Doručení klíče služby Media Services vrátí klientovi požadovaný klíčů/licencí, pokud je token platný a deklarace identity v tokenu odpovídají těm nakonfigurovaný pro klíčů/licencí.

Když konfigurujete zásady omezení tokenem, musíte zadat primární ověřovací klíč, vydavatele a parametry cílovou skupinu. Primární ověřovací klíč obsahuje klíč, který byl token podepsán pomocí. Vystavitel je služba tokenů zabezpečení, který vydá token. Cílové skupiny, někdy označuje jako obor, by měl popisovat záměr tokenu nebo prostředek token, který autorizuje přístup k. Služba Media Services doručení klíče ověří, že tyto hodnoty v tokenu odpovídají hodnotám v šabloně.

## <a name="streaming-urls"></a>Adresy URL pro streamování
Pokud váš asset byla zašifrována s více DRM, použijte značku šifrování v adrese URL streamování: (format = "m3u8-aapl" šifrování = "xxx").

Platí následující aspekty:

* Je možné zadat více než jeden typ šifrování.
* Typ šifrování nemusí být zadané v adrese URL, pokud pouze jeden šifrování byla použita k assetu.
* Typ šifrování se nerozlišují malá a velká písmena.
* Můžete zadat následující typy šifrování:
  * **šifrování cenc**: pro PlayReady nebo Widevine (Standard common encryption)
  * **cbcs-aapl**: pro FairPlay (AES CBC šifrování)
  * **CBC**: šifrování AES pro obálky

## <a name="next-steps"></a>Další postup
Následující články popisují kroků, které vám pomůžou začít s ochranou obsahu:

* [Chránit pomocí šifrování úložiště](media-services-rest-storage-encryption.md)
* [Ochrana s využitím šifrování AES](media-services-protect-with-aes128.md)
* [Ochrana díky PlayReady nebo Widevine](media-services-protect-with-playready-widevine.md)
* [Ochrana s využitím FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Související odkazy

* [Ověření tokenu JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integrace aplikace využívající architekturu MVC OWIN Azure Media Services se službou Azure Active Directory a omezit klíče doručování obsahu na základě deklarací identity tokenů JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
