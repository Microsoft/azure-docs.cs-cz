---
title: Chránit obsah pomocí služby Azure Media Services | Microsoft Docs
description: Tento článek poskytuje přehled toho Ochrana obsahu pomocí služby Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2018
ms.author: juliako
ms.openlocfilehash: 28c10d7c478ea7a9b1d1bfa91da79452eba3a4b6
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132854"
---
# <a name="content-protection-overview"></a>Přehled ochrany obsahu

Azure Media Services můžete použít k zabezpečení médiu od okamžiku, kdy by poté počítač prostřednictvím úložiště, zpracování a doručení. Pomocí služby Media Services, abyste mohli zajistit obsah za provozu a na vyžádání dynamicky šifrován Advanced Encryption Standard (AES-128) nebo jakýkoli systém tři hlavní digitální rights management (DRM): Microsoft PlayReady, Google Widevine a Apple FairPlay. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM (PlayReady, Widevine a FairPlay) licence autorizovaným klientům. 

Následující obrázek ukazuje pracovní postup služby Media Services ochrana obsahu: 

![Ochrana obsahu](./media/content-protection/content-protection.png)

&#42;*podporuje dynamické šifrování AES-128 "nezašifrovaný klíč", CBCS a CENC. Podrobnosti najdete v tématu na matici podpory [zde](#streaming-protocols-and-encryption-types).*

Tento článek vysvětluje principy a terminologií, které jsou relevantní pro pochopení ochrana obsahu pomocí služby Media Services. Tento článek také poskytuje odkazy na články, které popisují postup chránit obsah. 

## <a name="main-components-of-the-content-protection-system"></a>Hlavní součásti systému, ochrana obsahu

Úspěšné dokončení návrhu "ochrana obsahu" systému nebo aplikace, budete muset plně uvědomit, jaký obor úsilí. Následující seznam obsahuje přehled tří částí, které by bylo nutné implementovat. 

1. Azure Media Services kódu
  
  * Šablony licence PlayReady, Widevine nebo FairPlay. Šablony umožňují nakonfigurovat práva a oprávnění pro každou z použité technologiemi DRM
  * Ověření doručení licence, zadání logiku kontroly autorizace na základě deklarací v JWT
  * Obsahu klíče, protokolů datových proudů a odpovídající technologiemi DRM použije, definující DRM šifrování

  > [!NOTE]
  > Každý prostředek s více typy šifrování (AES-128, PlayReady, Widevine, FairPlay) můžete šifrovat. V tématu [streamování protokoly a typy šifrování](#streaming-protocols-and-encryption-types), pokud chcete zobrazit, co má smysl kombinovat.
  
  V následujícím článku zobrazit kroky pro šifrování obsahu pomocí standardu AES: [chránit pomocí šifrování AES](protect-with-aes128.md)
 
2. Přehrávač s AES nebo DRM s klientem. Přehrávání videa aplikace, podle přehrávač SDK (nativní nebo založené na prohlížeči), musí splňovat následující požadavky:
  * Přehrávač SDK podporuje potřebné DRM klientů
  * Přehrávač SDK podporuje požadované protokoly streamování: protokol Smooth, DASH nebo HLS
  * Přehrávač SDK musí být schopna zpracovávat předávání JWT token v žádosti o získání licence
  
    Přehrávač můžete vytvořit pomocí [rozhraní API služby Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). Použít [rozhraní API služby Azure Media Player ProtectionInfo](http://amp.azure.net/libs/amp/latest/docs/) k určení technologii, která DRM používat na různých platformách DRM.

    Pro testování AES nebo CENC (Widevine + PlayReady) Šifrovat obsah, můžete použít [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html). Ujistěte se, klikněte na "Rozšířené možnosti" a zkontrolujte AES a poskytují token.

    Pokud chcete testovat obsah FairPlay šifrovat, použijte [tento test player](http://aka.ms/amtest). Přehrávač podporuje Widevine, technologie PlayReady, a FairPlay technologiemi DRM, jakož i AES-128 zrušte šifrovacího klíče. Je třeba vybrat správné prohlížeče pro testování různých technologiemi DRM: Chrome nebo Opera nebo Firefox Widevine, MS Edge/IE11 pro PlayReady, Safari na maOS pro FairPlay.

3. Zabezpečte tokenu služby (STS), která vydává JSON Web Token (JWT) jako přístupový token pro přístup k prostředkům back-end. Služeb doručování licence AMS můžete použít jako prostředek back-end. Služby tokenů zabezpečení má zadat následující:

  * Vystavitel a cílové skupiny (nebo oboru)
  * Deklarace identity, které jsou závislé na obchodní požadavky v ochrana obsahu
  * Symetrický, nebo asymetrický ověření pro ověření podpisu
  * Podpora výměny klíčů (v případě potřeby)

    Můžete použít [tento nástroj STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) na test tokenů zabezpečení, která podporuje všechny 3 typy ověření klíče: symetrický, asymetrické, nebo AAD s výměny klíčů. 

> [!NOTE]
> Důrazně doporučujeme zaměřit a plně otestovat každou část (popsané výše) před přesunutím do další části. K otestování systému "ochrana obsahu", použijte nástroje uvedený v seznamu výše.  

## <a name="streaming-protocols-and-encryption-types"></a>Protokoly streamování a typy šifrování

Služba Media Services můžete použít k doručení obsahu dynamicky šifrovat pomocí nezašifrovaného klíče AES nebo DRM šifrování pomocí PlayReady a Widevine, FairPlay. V současné době můžete šifrovat formáty HTTP Live Streaming (HLS), MPEG DASH a technologie Smooth Streaming. Každý protokol podporuje následující metody šifrování:

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

Ve službě Media Services v3 klíč obsahu souvisí s StreamingLocator (viz [v tomto příkladu](protect-with-aes128.md)). Pokud používáte službu Media Services doručení klíče, by měl automaticky generovat klíče obsahu. Klíč k obsahu měl generovat sami Pokud používáte můžete vlastní doručení klíče služby, nebo pokud je potřeba zpracovat scénáři vysokou dostupnost, kde je potřeba mít stejný klíč k obsahu ve dvou Datacenter.

Datový proud je žádost přehrávač, Media Services používá k zadanému klíči dynamicky šifrovat pomocí standardu AES nezašifrovaný klíč nebo DRM šifrování vašeho obsahu. Přehrávač dešifrovat datový proud, požadavků klíč z doručení klíče služby Media Services nebo službu doručení klíče, kterou jste zadali. Při rozhodování, zda je uživatel oprávnění k získání klíče, služba vyhodnocuje zásady autorizace, které jste zadali pro klíč.

## <a name="aes-128-clear-key-vs-drm"></a>Zrušte klíče vs AES-128. DRM

Zákazníci často zajímat, jestli by měl použít systém DRM nebo šifrování AES. Hlavní rozdíl mezi dvěma systémy je, že s šifrováním AES klíč obsahu přenášená do klienta v nezašifrované podobě ("v Vymazat"). V důsledku toho klíč používaný k šifrování obsahu lze zobrazit v trasování v síti na straně klienta v prostém textu. Zrušte klíče šifrování AES-128 je vhodná pro použití případech, kdy prohlížeč je důvěryhodná strana (například šifrování podnikové videa distribuované v rámci společnosti prohlížení zaměstnanci).

Technologie PlayReady, Widevine a FairPlay všechny poskytovat vyšší úroveň porovnání šifrování AES-128 zrušte šifrovacího klíče. Klíč obsahu se přenášejí v zašifrovaném formátu. Kromě toho se v zabezpečeném prostředí na úrovni operačního systému, kde je pro uživatel se zlými úmysly použijí pro útok obtížnější zpracovává dešifrování. DRM se doporučuje pro použití případy, kdy prohlížeč nemusí být důvěryhodná strana a vyžadujete nejvyšší úroveň zabezpečení.

## <a name="storage-side-encryption"></a>Šifrování na straně úložiště

Pokud chcete ochránit vaše prostředky v klidovém stavu, prostředky by měla šifrovat pomocí šifrování na straně úložiště. Následující tabulka ukazuje, jak funguje šifrování na straně úložiště ve službě Media Services v3:

|Možnost šifrování|Popis|Media Services v3|
|---|---|---|---|
|Šifrování úložiště služby Media Services| AES 256 šifrování klíče spravované službou Media Services|Nepodporuje<sup>(1)</sup>|
|[Šifrování služby úložiště pro Data v klidovém stavu](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Šifrování na straně serveru, které nabízí Azure Storage, klíč spravovat Azure nebo zákazníka|Podporováno|
|[Šifrování na straně klienta úložiště](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Šifrování na straně klienta nabízené úložiště Azure, klíč spravovaný zákazníkem v Key Vault|Nepodporuje se|

<sup>1</sup> v3 Media Services, úložiště šifrování (AES 256 šifrování) je pouze podporována z důvodů zpětné kompatibility při vaše prostředky, které se vytvořily s v2 Media Services. Znamená v3 funguje s existující úložiště šifrovaný prostředky, ale neumožní vytváření nových databází.

## <a name="licenses-and-keys-delivery-service"></a>Službu doručování licencí a klíče

Služba Media Services poskytuje doručení klíče službu k doručování licencí DRM (PlayReady, Widevine, FairPlay) a klíčů AES autorizovaným klientům. Rozhraní REST API nebo klientskou knihovnu služby Media Services můžete použít ke konfiguraci zásad autorizace a ověření licencí a klíčů.

## <a name="control-content-access"></a>Přístup k obsahu ovládacího prvku

Můžete řídit, kdo má přístup k vašemu obsahu konfigurací zásad obsahu klíče. Služba Media Services podporuje více způsobů ověřování uživatelů, kteří žádají o klíč. Musíte nakonfigurovat obsahu klíče zásady. Klient (přehrávač) musí splňovat zásady, než klíč se dá doručit do klienta. Může mít zásad obsahu klíče **otevřete** nebo **tokenu** omezení. 

Omezený token obsahu klíče zásadu klíč k obsahu je odeslán pouze klienta, který představuje platný JSON Web Token (JWT) nebo jednoduchého webového tokenu (SWT) v žádosti o klíč nebo licenci. Tento token musí být vydaný služby tokenů zabezpečení (STS). Můžete použít jako služby tokenů zabezpečení Azure Active Directory nebo nasadit vlastní službu tokenů zabezpečení. Služba tokenů zabezpečení musí být nakonfigurované vytvořit token podepsané zadaný klíč a vystavování deklarací identity, které jste zadali v nastavení omezení s tokenem. Pokud token je platný a deklarace identity v tokenu shodují s těmi, nakonfigurované pro klíč nebo licence doručení klíče služby Media Services vrátí požadovaný klíč nebo licence klientovi.

Když konfigurujete zásady omezení tokenem, musíte zadat ověření primární klíč, vystavitele a cílová skupina parametry. Ověření primární klíč obsahuje klíč, který byl podepsaný token. Vystavitel je zabezpečený tokenu služba, která vydá token. Cílovou skupinu, které se někdy označuje jako obor, popisuje záměr tokenu nebo prostředek token povolí přístup k. Služba Media Services doručení klíče ověří, jestli tyto hodnoty v tokenu shodují s hodnotami v šabloně.

## <a name="streaming-urls"></a>Adresy URL pro streamování

Pokud váš asset byla zašifrována pomocí více než jeden DRM, použijte značku šifrování v adresu URL streamování: (formát = 'm3u8-aapl' šifrování = 'xxx').

Platí následující aspekty:

* Typ šifrování nemusí být zadaného v adrese URL, pokud jenom jeden šifrování byla použita pro daný prostředek.
* Typ šifrování je malá a velká písmena.
* Určit lze následující typy šifrování:
  * **šifrování cenc**: pro PlayReady nebo Widevine (common encryption)
  * **cbcs-aapl**: pro FairPlay (šifrování AES CBC)
  * **CBC**: šifrování AES pro obálky

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží

Použijte následující informace o odstraňování potíží pro pomoc s potížích s implementací.

* Adresa URL musí končit vystavitele "/". Cílová skupina musí být ID player aplikace klienta. Navíc přidat "/" na konci URL vystavitele.

  ```
  <add key="ida:audience" value="[Application Client ID GUID]" />
  <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />
  ```

* Přidejte oprávnění k aplikaci ve službě Azure AD na **konfigurace** aplikace. Oprávnění se vyžadují pro každou aplikaci, místní i nasazené verzi.
* Při nastavování dynamické šifrování CENC ochrany, použijte správný vystavitele.

  ```
  <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
  ```

  Následující nefunguje:

  ```
  <add key="ida:issuer" value="https://username.onmicrosoft.com/" />
  ```

  Identifikátor GUID je ID klienta Azure AD. Identifikátor GUID naleznete v **koncové body** místní nabídky na portálu Azure.

* Členství ve skupině udělit deklarací oprávnění. Ujistěte se, že toto je v souboru manifestu aplikace Azure AD: 

    "groupMembershipClaims": "Vše" (výchozí hodnota je null)

## <a name="next-steps"></a>Další postup

[Chránit pomocí šifrování AES](protect-with-aes128.md)
