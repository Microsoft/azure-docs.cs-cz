---
title: Chraňte svůj obsah pomocí Azure Media Services | Microsoft Docs
description: Tento článek poskytuje přehled ochrany obsahu pomocí Azure Media Services V2.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: b0d71a7b010e91776a28330cfc32278c7060aab6
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901256"
---
# <a name="content-protection-overview"></a>Přehled ochrany obsahu 

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Projděte si nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-from-v2-to-v3.md) .

Azure Media Services můžete použít k zabezpečení médií od okamžiku opuštění počítače přes úložiště, zpracování a dodání. Pomocí služby Media Services můžete doručovat na vyžádání a živého obsahu dynamicky šifrován Advanced Encryption Standard (AES-128) nebo některý z systémy tři hlavní digital rights management (DRM): Apple FairPlay, Microsoft PlayReady a Google Widevine. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM (PlayReady, Widevine a FairPlay) licence autorizovaným klientům. 

Následující obrázek ukazuje pracovní postup služby Media Services content protection: 

![Ochrana technologií PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Tento článek vysvětluje koncepty a terminologie relevantní pro pochopení ochrany obsahu pomocí služby Media Services. Článek obsahuje také odkazy na články, které popisují, jak chránit obsah. 

## <a name="dynamic-encryption"></a>Dynamické šifrování
 Media Services můžete doručovat obsah zašifrovaný dynamicky pomocí nezašifrovaného klíče AES a DRM šifrování pomocí PlayReady, Widevine a FairPlay. V současné době můžete šifrovat formáty HTTP Live Streaming (HLS), MPEG DASH a Smooth Streaming. Šifrování při progresivním stahování se nepodporuje. Každá metoda šifrování podporuje následující protokoly streamování:

- AES: MPEG-POMLČKa, Smooth Streaming a HLS
- PlayReady: MPEG-POMLČKa, Smooth Streaming a HLS
- Widevine: MPEG-POMLČKa
- FairPlay: HLS

K šifrování assetu je potřeba k assetu přidružit šifrovací klíč obsahu a taky nakonfigurovat zásady autorizace pro tento klíč. Klíče obsahu lze určit nebo automaticky vygenerovat pomocí Media Services.

Také je potřeba nakonfigurovat zásady doručování prostředků. Pokud chcete streamovat zašifrovaný prostředek, ujistěte se, že jste určili, jak ho chcete doručit konfigurací zásady doručení assetu.

Datový proud je žádost přehrávač, Media Services využívá se zadaným klíčem k dynamické šifrování obsahu pomocí nezašifrovaného klíče AES a DRM šifrování. Aby byl datový proud dešifrován, přehrávač si vyžádá klíč od Media Services služby doručování klíčů. Aby bylo možné rozhodnout, jestli uživatel má oprávnění k získání klíče, vyhodnotí zásady autorizace, které jste zadali pro tento klíč.

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 Vymazat klíč a DRM
Zákazníci často zajímat, zda by měl používat šifrování AES nebo systému DRM. Hlavní rozdíl mezi těmito dvěma systémy je, že se šifrováním AES klíč obsahu přenášena na klienty v nezašifrované podobě ("v nezašifrované podobě"). V důsledku toho klíč používaný k šifrování obsahu lze zobrazit v síťového trasování na straně klienta ve formátu prostého textu. Šifrování nezašifrovaného klíče AES-128 je vhodný pro případy použití, ve kterém je prohlížeč důvěryhodné strany (například šifrování firemních videí distribuované v rámci společnosti prohlížení zaměstnanci).

PlayReady, Widevine a FairPlay AES-128 všechny poskytovat vyšší úroveň šifrování ve srovnání s nezašifrovaným klíčem. Klíč obsahu se přenášejí v zašifrovaném formátu. Kromě toho se v zabezpečeném prostředí na úrovni operačního systému, kde je obtížnější uživatel se zlými úmysly k útoku na zpracovává dešifrování. DRM se doporučuje pro případy použití, kdy prohlížeč pravděpodobně nebude důvěryhodná strana a vyžadujete nejvyšší úroveň zabezpečení.

## <a name="storage-encryption"></a>Šifrování úložiště
Šifrování úložiště můžete použít k místnímu šifrování nešifrovaného obsahu pomocí šifrování AES 256-bit Encryption. Pak ho můžete odeslat do Azure Storage, kde je uložený zašifrovaný v klidovém stavu. Prostředky chráněné pomocí šifrování úložiště se před kódováním automaticky nešifrují a umístí do systému souborů EFS. Prostředky se volitelně znovu šifrují před odesláním zpět jako nového výstupního prostředku. Primárním případem použití šifrování úložiště je, že chcete zabezpečit vysoce kvalitní vstupní mediální soubory se silným šifrováním v klidovém provozu na disku.

Aby bylo možné doručovat prostředky zašifrované v úložišti, musíte nakonfigurovat zásady doručení assetu tak, aby Media Services vědět, jak chcete obsah doručovat. Předtím, než bude možné Asset streamovat, server streamování dešifruje a streamuje svůj obsah pomocí zadaných zásad doručení (například AES, běžné šifrování nebo bez šifrování).

## <a name="types-of-encryption"></a>Typy šifrování
PlayReady a Widevine využívají běžné šifrování (režim AES pro AES). FairPlay využívá šifrování AES CBC-Mode. AES-128 vymazání klíče šifrování používá šifrování obálek.

## <a name="licenses-and-keys-delivery-service"></a>Službu doručování licencí a klíčů
Služba Media Services poskytuje doručení klíče služby pro doručování licencí DRM (PlayReady, Widevine, FairPlay) a klíče AES autorizovaným klientům. Můžete použít [Azure Portal](media-services-portal-protect-content.md), REST API nebo sadu Media Services SDK pro .NET ke konfiguraci zásad autorizace a ověřování pro vaše licence a klíče.

## <a name="control-content-access"></a>Přístup k obsahu ovládacího prvku
Konfigurací zásad autorizace klíče obsahu můžete určit, kdo má přístup k vašemu obsahu. Zásada autorizace klíče obsahu podporuje buď omezení Open, nebo token.

### <a name="open-authorization"></a>Otevřít autorizaci
V případě otevřené zásady autorizace se klíč obsahu pošle libovolnému klientovi (bez omezení).

### <a name="token-authorization"></a>Autorizace tokenu
Pomocí zásad autorizace s omezeným tokenem se klíč obsahu pošle jenom klientovi, který v žádosti o klíč nebo licenci prezentuje platný JSON Web Token (JWT) nebo jednoduchý webový token (SWT). Tento token musí být vystavené služby tokenů zabezpečení (STS). Můžete použít Azure Active Directory jako služba STS nebo nasadit vlastní službu STS. Služba tokenů zabezpečení musí být nakonfigurovaný k vytvoření tokenu podepsán zadaný klíč a vydávání deklarací identity, které jste zadali v konfiguraci omezení s tokenem. Doručení klíče služby Media Services vrátí klientovi požadovaný klíčů/licencí, pokud je token platný a deklarace identity v tokenu odpovídají těm nakonfigurovaný pro klíčů/licencí.

Když konfigurujete zásady omezení tokenem, musíte zadat primární ověřovací klíč, vydavatele a parametry cílovou skupinu. Primární ověřovací klíč obsahuje klíč, který byl token podepsán pomocí. Vystavitel je služba tokenů zabezpečení, který vydá token. Cílové skupiny, někdy označuje jako obor, by měl popisovat záměr tokenu nebo prostředek token, který autorizuje přístup k. Služba Media Services doručení klíče ověří, že tyto hodnoty v tokenu odpovídají hodnotám v šabloně.

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

## <a name="next-steps"></a>Další kroky
Následující články popisují další kroky, které vám pomohou začít s ochranou obsahu:

* [Ochrana pomocí šifrování úložiště](media-services-rest-storage-encryption.md)
* [Ochrana s využitím šifrování AES](media-services-protect-with-aes128.md)
* [Ochrana pomocí PlayReady nebo Widevine](media-services-protect-with-playready-widevine.md)
* [Ochrana pomocí FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Související odkazy

* [Ověřování tokenu JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integrace Azure Media Services OWIN aplikace založené na MVC s Azure Active Directory a omezení doručení klíče obsahu na základě deklarací JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
