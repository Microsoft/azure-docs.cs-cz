---
title: Chraňte svůj obsah pomocí Mediálních služeb Azure | Dokumenty společnosti Microsoft
description: Tento článek poskytuje přehled ochrany obsahu pomocí Azure Media Services v2.
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
ms.openlocfilehash: 88e0e1c18722fd86e79fc1fa7722b59b3cb8966a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460955"
---
# <a name="content-protection-overview"></a>Přehled ochrany obsahu 

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

Azure Media Services můžete použít k zabezpečení médií od okamžiku, kdy opustí váš počítač prostřednictvím úložiště, zpracování a doručení. Pomocí služby Media Services můžete poskytovat obsah živého i na vyžádání šifrovaný dynamicky pomocí advanced encryption standardu (AES-128) nebo některého ze tří hlavních systémů správy digitálních práv (DRM): Microsoft PlayReady, Google Widevine a Apple FairPlay. Mediální služby také poskytují službu pro doručování klíčů AES a licencí DRM (PlayReady, Widevine a FairPlay) oprávněným klientům. 

Následující obrázek znázorňuje pracovní postup ochrany obsahu mediálních služeb: 

![Ochrana technologií PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Tento článek vysvětluje koncepty a terminologii relevantní pro pochopení ochrany obsahu pomocí služby Media Services. Článek také obsahuje odkazy na články, které popisují, jak chránit obsah. 

## <a name="dynamic-encryption"></a>Dynamické šifrování

Pomocí mediálních služeb můžete pomocí služby Media Services dodávat obsah šifrovaný dynamicky pomocí jasného klíče AES nebo šifrování DRM pomocí služeb PlayReady, Widevine nebo FairPlay. Pokud je obsah zašifrován pomocí jasného klíče AES a je odeslán přes protokol HTTPS, není jasný, dokud se nedostane ke klientovi. 

Každá metoda šifrování podporuje následující protokoly streamování:
 
- AES: MPEG-DASH, hladké streamování a HLS
- PlayReady: MPEG-DASH, hladké streamování a HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

Šifrování při postupném stahování není podporováno. 

Chcete-li zašifrovat datový zdroj, musíte k datovému zdroji přidružit klíč obsahu šifrování a také nakonfigurovat zásady autorizace pro klíč. Klíče obsahu lze zadat nebo automaticky generovat službou Media Services.

Je také nutné nakonfigurovat zásady poskytování datového zdroje. Pokud chcete streamovat datový zdroj šifrovaný úložištěm, nezapomeňte určit, jak ho chcete doručit, a to konfigurací zásad pro doručování datových zdrojů.

Pokud je datový proud požadován přehrávačem, služba Media Services používá zadaný klíč k dynamickému šifrování obsahu pomocí jasného klíče AES nebo šifrování DRM. Chcete-li dešifrovat datový proud, přehrávač požaduje klíč ze služby doručování klíčů Služby Media Services. Chcete-li rozhodnout, zda je uživatel oprávněn získat klíč, služba vyhodnotí zásady autorizace, které jste zadali pro klíč.

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 jasný klíč vs. DRM
Zákazníci si často kladou otázku, zda by měli používat šifrování AES nebo systém DRM. Hlavní rozdíl mezi těmito dvěma systémy spoážit v tom, že při šifrování AES je klíč obsahu přenášen klientovi v nešifrovaném formátu ("v jasném"). V důsledku toho lze klíč použitý k šifrování obsahu zobrazit v síťovém trasování na straně klienta ve formátu prostého textu. Šifrování jasného klíče AES-128 je vhodné pro případy použití, kdy je divák důvěryhodnou stranou (například šifrování firemních videí distribuovaných v rámci společnosti, které mají zaměstnanci zobrazit).

PlayReady, Widevine a FairPlay poskytují vyšší úroveň šifrování ve srovnání s AES-128 clear key encryption. Klíč obsahu je přenášen v šifrovaném formátu. Kromě toho je dešifrování zpracováno v zabezpečeném prostředí na úrovni operačního systému, kde je pro uživatele se zlými úmysly obtížnější zaútočit. Drm se doporučuje pro případy použití, kdy prohlížeč nemusí být důvěryhodnou stranou a vyžadujete nejvyšší úroveň zabezpečení.

## <a name="storage-encryption"></a>Šifrování úložiště
Šifrování úložiště můžete použít k šifrování jasného obsahu místně pomocí 256bitového šifrování AES. Pak ho můžete nahrát do Služby Azure Storage, kde je uložena zašifrovaná v klidovém stavu. Prostředky chráněné šifrováním úložiště jsou automaticky nezašifrovány a umístěny do šifrovaného systému souborů před kódováním. Datové zdroje jsou volitelně znovu zašifrovány před odesláním zpět jako nový výstupní prostředek. Primární případ použití pro šifrování úložiště je, když chcete zabezpečit vysoce kvalitní vstupní mediální soubory se silným šifrováním v klidovém stavu na disku.

Chcete-li dodat prostředek šifrovaný úložištěm, je nutné nakonfigurovat zásady doručování datového zdroje tak, aby mediální služby věděly, jak chcete doručovat svůj obsah. Před datovým proudem server pro streamování dešifruje a streamuje váš obsah pomocí zadaných zásad doručení (například AES, běžné šifrování nebo žádné šifrování).

## <a name="types-of-encryption"></a>Typy šifrování
PlayReady a Widevine využívají běžné šifrování (režim AES CTR). FairPlay využívá šifrování v režimu AES CBC. AES-128 šifrování jasného klíče využívá šifrování obálek.

## <a name="licenses-and-keys-delivery-service"></a>Služby doručování licencí a klíčů
Media Services poskytuje službu doručování klíčů pro doručování licencí DRM (PlayReady, Widevine, FairPlay) a klíčů AES autorizovaným klientům. [K konfiguraci](media-services-portal-protect-content.md)zásad autorizace a ověřování pro vaše licence a klíče můžete použít portál Azure , rozhraní REST API nebo sadku SDK media services pro rozhraní .NET.

## <a name="control-content-access"></a>Řízení přístupu k obsahu
Konfigurací zásad autorizace klíče obsahu můžete určit, kdo má přístup k vašemu obsahu. Zásady autorizace klíče obsahu podporují omezení otevření nebo tokenu.

### <a name="open-authorization"></a>Otevřít autorizaci
S otevřenou zásadou autorizace je klíč obsahu odeslán libovolnému klientovi (bez omezení).

### <a name="token-authorization"></a>Autorizace tokenu
Se zásadami autorizace s omezeným tokenem je klíč obsahu odeslán pouze klientovi, který v žádosti o klíč/licenci představuje platný webový token JSON (JWT) nebo jednoduchý webový token (SWT). Tento token musí být vydán službou tokenů zabezpečení (STS). Azure Active Directory můžete použít jako službu STS nebo nasadit vlastní službu STS. Služba STS musí být nakonfigurována tak, aby vytvořila token podepsaný zadaným klíčem a vydala deklarace identity, které jste zadali v konfiguraci omezení tokenu. Služba doručování klíčů mediálních služeb vrátí požadovaný klíč/licenci klientovi, pokud je token platný a deklarace identity v tokenu odpovídají deklaracím nakonfigurovaným pro klíč/licenci.

Při konfiguraci zásad s omezeným přístupem tokenu je nutné zadat primární ověřovací klíč, vystavitel a parametry publika. Primární ověřovací klíč obsahuje klíč, se kterým byl token podepsán. Vystavitel je služba zabezpečeného tokenu, která vydává token. Cílová skupina, někdy volaná obor, popisuje záměr tokenu nebo prostředku, ke kterému token autorizuje přístup. Služba doručování klíčů Mediální služby ověří, zda tyto hodnoty v tokenu odpovídají hodnotám v šabloně.

### <a name="token-replay-prevention"></a>Prevence opětovného přehrání tokenu

Funkce *Prevence přehrání tokenu* umožňuje zákazníkům mediálních služeb nastavit limit, kolikrát lze stejný token použít k vyžádání klíče nebo licence. Zákazník může přidat deklaraci `urn:microsoft:azure:mediaservices:maxuses` typu do tokenu, kde hodnota je počet, kolikrát token lze použít k získání licence nebo klíče. Všechny následné požadavky se stejným tokenem doručení klíče vrátí neoprávněnou odpověď. Podívejte se, jak přidat deklaraci do [vzorku DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Požadavky

* Zákazníci musí mít kontrolu nad generování tokenu. Deklarace musí být umístěna do samotného tokenu.
* Při použití této funkce jsou požadavky s tokeny, jejichž doba vypršení platnosti je vzdálena více než jednu hodinu od okamžiku přijetí požadavku, odmítnuty s neoprávněnou odpovědí.
* Tokeny jsou jednoznačně identifikovány jejich podpisem. Jakákoli změna datové části (například aktualizace na dobu vypršení platnosti nebo deklarace identity) změní podpis tokenu a bude se počítat jako nový token, na který se doručení klíče dařilo předtím nesetkalo.
* Přehrávání se nezdaří, pokud `maxuses` token překročil hodnotu nastavenou zákazníkem.
* Tuto funkci lze použít pro veškerý existující chráněný obsah (je třeba změnit pouze vydaný token).
* Tato funkce funguje jak s JWT, tak s SWT.

## <a name="streaming-urls"></a>Datové adresy URL pro streamování
Pokud byl váš prostředek zašifrován pomocí více než jednoho DRM, použijte v adrese URL streamování šifrovací značku: (format='m3u8-aapl', encryption='xxx').

Platí následující důležité informace:

* Nelze zadat více než jeden typ šifrování.
* Typ šifrování nemusí být zadán v adrese URL, pokud bylo na prostředek použito pouze jedno šifrování.
* Typ šifrování nerozlišuje malá a velká písmena.
* Lze zadat následující typy šifrování:

  * **cenc**: Pro PlayReady nebo Widevine (společné šifrování)
  * **cbcs-aapl**: Pro FairPlay (AES CBC šifrování)
  * **cbc**: Pro šifrování obálky AES

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="next-steps"></a>Další kroky
Následující články popisují další kroky, které vám pomohou začít s ochranou obsahu:

* [Ochrana pomocí šifrování úložiště](media-services-rest-storage-encryption.md)
* [Ochrana pomocí šifrování AES](media-services-protect-with-aes128.md)
* [Chraňte pomocí PlayReady a/nebo Widevine](media-services-protect-with-playready-widevine.md)
* [Chraňte s FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Související odkazy

* [Ověřování tokenů JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integrace aplikace založená na Azure Media Services OWIN MVC s Azure Active Directory a omezení doručování klíčů obsahu na základě deklarací JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
