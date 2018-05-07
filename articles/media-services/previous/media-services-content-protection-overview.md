---
title: "Chránit obsah pomocí služby Azure Media Services | Microsoft Docs"
description: "Tento článek poskytuje přehled toho Ochrana obsahu pomocí služby Media Services."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 13447fd9193374d80ed5c2e6af8543f11b95e709
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
---
# <a name="content-protection-overview"></a>Přehled ochrany obsahu
 Azure Media Services můžete použít k zabezpečení médiu od okamžiku, kdy by poté počítač prostřednictvím úložiště, zpracování a doručení. Pomocí služby Media Services, abyste mohli zajistit obsah za provozu a na vyžádání dynamicky šifrován Advanced Encryption Standard (AES-128) nebo jakýkoli systém tři hlavní digitální rights management (DRM): Microsoft PlayReady, Google Widevine a Apple FairPlay. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM (PlayReady, Widevine a FairPlay) licence autorizovaným klientům. 

Následující obrázek ukazuje pracovní postup služby Media Services ochrana obsahu: 

![Ochrana technologií PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Tento článek vysvětluje principy a terminologií, které jsou relevantní pro pochopení ochrana obsahu pomocí služby Media Services. Tento článek také poskytuje odkazy na články, které popisují postup chránit obsah. 

## <a name="dynamic-encryption"></a>Dynamické šifrování
 Služba Media Services můžete použít k doručení obsahu dynamicky šifrovat pomocí nezašifrovaného klíče AES nebo DRM šifrování pomocí PlayReady a Widevine, FairPlay. V současné době můžete šifrovat formáty HTTP Live Streaming (HLS), MPEG DASH a technologie Smooth Streaming. Šifrování na progresivní stahování není podporováno. Každá metoda šifrování podporuje následující protokoly pro streamování:

- AES: MPEG-DASH, Smooth Streaming a HLS
- PlayReady: MPEG-DASH, Smooth Streaming a HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

K šifrování prostředek, musíte přidružit asset šifrovací klíč obsahu a taky konfigurace zásad autorizace pro klíč. Obsahu klíčů může být zadaný nebo automaticky generuje služba Media Services.

Také musíte nakonfigurovat zásady doručení assetu. Pokud chcete k vysílání datového proudu asset šifrovat úložiště, zajistěte, aby k určení, jak chcete poskytovaným konfigurace zásad doručení assetu.

Datový proud je žádost přehrávač, Media Services používá k zadanému klíči dynamicky šifrovat pomocí standardu AES nezašifrovaný klíč nebo DRM šifrování vašeho obsahu. Přehrávač dešifrovat datový proud, vyžaduje klíč z doručení klíče služby Media Services. Při rozhodování, zda je uživatel oprávnění k získání klíče, služba vyhodnocuje zásady autorizace, které jste zadali pro klíč.

## <a name="aes-128-clear-key-vs-drm"></a>Zrušte klíče vs AES-128. DRM
Zákazníci často zajímat, jestli by měl použít systém DRM nebo šifrování AES. Hlavní rozdíl mezi dvěma systémy je, že s šifrováním AES klíč obsahu přenášená do klienta v nezašifrované podobě ("v Vymazat"). V důsledku toho klíč používaný k šifrování obsahu lze zobrazit v trasování v síti na straně klienta v prostém textu. Zrušte klíče šifrování AES-128 je vhodná pro použití případech, kdy prohlížeč je důvěryhodná strana (například šifrování podnikové videa distribuované v rámci společnosti prohlížení zaměstnanci).

Technologie PlayReady, Widevine a FairPlay všechny poskytovat vyšší úroveň porovnání šifrování AES-128 zrušte šifrovacího klíče. Klíč obsahu se přenášejí v zašifrovaném formátu. Kromě toho se v zabezpečeném prostředí na úrovni operačního systému, kde je pro uživatel se zlými úmysly použijí pro útok obtížnější zpracovává dešifrování. DRM se doporučuje pro použití případy, kdy prohlížeč nemusí být důvěryhodná strana a vyžadujete nejvyšší úroveň zabezpečení.

## <a name="storage-encryption"></a>Šifrování úložiště
Šifrování úložiště můžete použít k zašifrování obsahu místně pomocí šifrování AES 256 bitů. Potom můžete nahrát jej do úložiště Azure, kde je uložený v zašifrované podobě. Prostředky chráněné pomocí šifrování úložiště jsou automaticky bez šifrování a jejich umísťování v systému souborů EFS před kódování. Prostředky jsou volitelně znovu zašifrovat před jejich odesláním zpět v podobě nového výstupního prostředku. Případem primárního použití šifrování úložiště je, když chcete zabezpečit soubory vysoce kvalitními vstupními médii pomocí silného šifrování v klidovém stavu na disku.

K poskytování asset šifrovat úložiště, musíte nakonfigurovat zásady doručení assetu, aby služba Media Services věděla způsob doručení obsahu. Před asset Streamovat, streamování serveru dešifruje a datové proudy svůj obsah pomocí zadaného doručování zásad (například AES, běžným šifrováním nebo žádné šifrování).

## <a name="types-of-encryption"></a>Typy šifrování
PlayReady a Widevine využívat běžné šifrování (AES PEV.cenu režim). FairPlay využívá šifrování AES-režimu CBC. Zrušte klíče šifrování AES-128 využívá šifrování obálku.

## <a name="licenses-and-keys-delivery-service"></a>Službu doručování licencí a klíče
Služba Media Services poskytuje doručení klíče službu k doručování licencí DRM (PlayReady, Widevine, FairPlay) a klíčů AES autorizovaným klientům. Můžete použít [portálu Azure](media-services-portal-protect-content.md), REST API nebo sady Media Services SDK pro .NET, ke konfiguraci zásad autorizace a ověření licencí a klíčů.

## <a name="control-content-access"></a>Přístup k obsahu ovládacího prvku
Můžete řídit, kdo má přístup k vašemu obsahu tak, že nakonfigurujete zásady autorizace klíče obsahu. Zásady autorizace klíče obsahu podporuje buď otevřené nebo s tokenem omezení.

### <a name="open-authorization"></a>Otevřete autorizace
Zásad otevřete autorizace je odeslán klíč obsahu libovolnému klientovi (bez omezení).

### <a name="token-authorization"></a>Token autorizace
Zásadu omezený tokenu autorizace klíče obsahu je odeslán pouze klienta, který představuje platný JSON Web Token (JWT) nebo jednoduchého webového tokenu (SWT) v žádosti o klíč nebo licenci. Tento token musí být vydaný služby tokenů zabezpečení (STS). Můžete použít jako služby tokenů zabezpečení Azure Active Directory nebo nasadit vlastní službu tokenů zabezpečení. Služba tokenů zabezpečení musí být nakonfigurované vytvořit token podepsané zadaný klíč a vystavování deklarací identity, které jste zadali v nastavení omezení s tokenem. Pokud token je platný a deklarace identity v tokenu shodují s těmi, nakonfigurované pro klíč nebo licence doručení klíče služby Media Services vrátí požadovaný klíč nebo licence klientovi.

Když konfigurujete zásady omezení tokenem, musíte zadat ověření primární klíč, vystavitele a cílová skupina parametry. Ověření primární klíč obsahuje klíč, který byl podepsaný token. Vystavitel je zabezpečený tokenu služba, která vydá token. Cílovou skupinu, které se někdy označuje jako obor, popisuje záměr tokenu nebo prostředek token povolí přístup k. Služba Media Services doručení klíče ověří, jestli tyto hodnoty v tokenu shodují s hodnotami v šabloně.

## <a name="streaming-urls"></a>Adresy URL pro streamování
Pokud váš asset byla zašifrována pomocí více než jeden DRM, použijte značku šifrování v adresu URL streamování: (formát = 'm3u8-aapl' šifrování = 'xxx').

Platí následující aspekty:

* Lze zadat více než jeden typ šifrování.
* Typ šifrování nemusí být zadaného v adrese URL, pokud jenom jeden šifrování byla použita pro daný prostředek.
* Typ šifrování je malá a velká písmena.
* Určit lze následující typy šifrování:
  * **šifrování cenc**: pro PlayReady nebo Widevine (common encryption)
  * **cbcs-aapl**: pro FairPlay (šifrování AES CBC)
  * **CBC**: šifrování AES pro obálky

## <a name="next-steps"></a>Další postup
Následující články popisují další kroky můžete začít pracovat s ochrana obsahu:

* [Chránit pomocí šifrování úložiště](media-services-rest-storage-encryption.md)
* [Chránit pomocí šifrování AES](media-services-protect-with-aes128.md)
* [Chránit pomocí PlayReady nebo Widevine](media-services-protect-with-playready-widevine.md)
* [Chránit pomocí FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Související odkazy

* [Vysvětlení Azure ceny doručování licencí Media Services PlayReady](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)
* [Ladění pro šifrované datový proud AES ve službě Azure Media Services](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)
* [Ověření pomocí tokenu JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Aplikace založené na Azure Media Services OWIN MVC integrovat Azure Active Directory a omezit klíče doručování obsahu na základě deklarací JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
