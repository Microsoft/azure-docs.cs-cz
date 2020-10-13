---
title: Nejčastější dotazy k Azure Media Services V3 | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure Media Services V3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: d34b5aaaa12a3d296f92e0d7be34ae76931d8506
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89268481"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Nejčastější dotazy k Media Services V3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure Media Services V3.

## <a name="general"></a>Obecné

### <a name="what-are-the-azure-portal-limitations-for-media-services-v3"></a>Jaká jsou omezení Azure Portal Media Services V3?

[Azure Portal](https://portal.azure.com/) můžete použít ke správě událostí V3 Live, zobrazení zdrojů a úloh v3, získání informací o přístupu k rozhraním API a k šifrování obsahu. <br/>Pro všechny ostatní úlohy správy (například ke správě transformací a úloh nebo analýze obsahu V3) použijte [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref)nebo jednu z podporovaných [sad SDK](media-services-apis-overview.md#sdks).

Pokud se vaše video dřív nahrálo na účet Media Services pomocí rozhraní Media Services V3 API nebo se obsah vygeneroval na základě živého výstupu, neuvidíte v Azure Portal tlačítka **kódování**, **Analýza**ani **šifrování** . K provedení těchto úloh použijte rozhraní API Media Services V3.  

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Jaké role Azure můžou provádět akce s Azure Media Services prostředky? 

Přečtěte si téma [řízení přístupu na základě role (RBAC) pro účty Media Services](rbac-overview.md).

### <a name="how-do-i-stream-to-apple-ios-devices"></a>Návody Stream do zařízení Apple iOS?

Ujistěte se, že máte **(Format = M3U8-AAPL)** na konci vaší cesty (po **/manifest** části adresy URL), aby server zdroje dat pro streamování vrátil HTTP Live Streaming (HLS) pro spotřebu na nativních zařízeních Apple iOS. Podrobnosti najdete v tématu [doručování obsahu](dynamic-packaging-overview.md).

### <a name="how-do-i-configure-media-reserved-units"></a>Návody nakonfigurovat rezervované jednotky médií?

Pro analýzy zvuku a úlohy analýzy videí, které se spouštějí Media Services V3 nebo Video Indexer, doporučujeme zřídit účet s 10 rezervovanými jednotkami médií (MRUs). Pokud potřebujete více než 10 S3 MRUs, otevřete lístek podpory pomocí [Azure Portal](https://portal.azure.com/).

Podrobnosti najdete v tématu [škálování zpracování médií](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Jaká je doporučená metoda pro zpracování videí?

Pomocí [transformací](/rest/api/media/transforms) můžete nakonfigurovat běžné úlohy pro kódování a analýzu videí. Každá transformace popisuje recept nebo pracovní postup úloh pro zpracování vašich videosouborů nebo zvukových souborů. [Úloha](/rest/api/media/jobs) je skutečný požadavek na Media Services, jak použít transformaci na vstupní video nebo zvukový obsah. Po vytvoření transformace můžete odesílat úlohy pomocí rozhraní API Media Services nebo kterékoli z publikovaných sad SDK. Další informace najdete v tématu [Transformace a úlohy](transforms-jobs-concept.md).

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>Video se nahrálo, zakódoval a publikovalo. Proč se video při pokusu o streamování nebude přehrávat?

Jedním z nejběžnějších důvodů je, že nemáte koncový bod streamování, ze kterého se snažíte přejít do běžícího stavu.

### <a name="how-does-pagination-work"></a>Jak funguje stránkování?

Při použití stránkování byste měli vždy použít další odkaz k zobrazení výčtu kolekce a nezáleží na konkrétní velikosti stránky. Podrobnosti a příklady najdete v tématu [filtrování, řazení, stránkování](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Jaké funkce ještě nejsou v Azure Media Services V3 k dispozici?

Podrobnosti najdete v tématu [mezery funkcí v souvislosti s rozhraními API v2](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Jaký je proces přesunutí účtu Media Services mezi předplatnými?  

Podrobnosti najdete v tématu [přesun Media Services účtu mezi předplatnými](media-services-account-concept.md).

## <a name="live-streaming"></a>Živé streamování 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>Návody zastavit živý stream po dokončení vysílání?

K tomuto přístupu se můžete přizpůsobovat ze strany klienta nebo na straně serveru.

#### <a name="client-side"></a>Strana klienta

Vaše webová aplikace by měla uživatele vyzvat, pokud chce ukončit všesměrové vysílání, protože zavírá prohlížeč. Toto je událost prohlížeče, kterou může webová aplikace zpracovat.

#### <a name="server-side"></a>Strana serveru

Můžete monitorovat živé události tím, že se přihlásíte k odběru Azure Event Gridch událostí. Další informace najdete v tématu [schéma událostí EventGrid](media-services-event-schemas.md#live-event-types).

Máte tyto možnosti:

* [Přihlaste](reacting-to-media-services-events.md) se k odběru událostí [Microsoft. Media. LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) na úrovni datového proudu a sledujte, že v průběhu chvilky nejsou žádné znovu navázány, aby se zastavila a odstranila živá událost.
* [Přihlaste](reacting-to-media-services-events.md) se k odběru událostí [prezenčního signálu](media-services-event-schemas.md#liveeventingestheartbeat) na úrovni sledování. Pokud se u všech stop přestanou příchozí přenosové rychlosti na 0 nebo když se poslední časové razítko nezvyšuje, můžete živou událost bezpečně vypnout. Události prezenčního signálu přicházejí v každé 20 sekundách pro každou stopu, takže se může jednat o bitovou podrobnou bitovou kopii.

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>V živém streamu Návody vkládat rozlomení, videa a obrazové položky?

Media Services V3 Live Encoding ještě nepodporuje vkládání videa nebo obrazových obrázků v živém streamu. 

Pro přepnutí zdrojového videa můžete použít [živý místní kodér](recommended-on-premises-live-encoders.md) . Řada aplikací nabízí možnost přepnout zdroje, včetně Wirecast streamování, přepínač studia (v iOS) a OBS studia (bezplatná aplikace).

## <a name="content-protection"></a>Ochrana obsahu

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>Mám použít šifrované šifrování klíče AES-128 nebo systém DRM?

Zákazníci často chtějí, aby používali šifrování AES nebo systém DRM. Hlavním rozdílem mezi těmito dvěma systémy je to, že při šifrování AES se klíč obsahu přenáší klientovi přes protokol TLS, aby se klíč zašifroval při přenosu, ale bez dalšího šifrování ("v jasném stavu"). V důsledku toho je klíč, který se používá k dešifrování obsahu, přístupný klientskému přehrávači a může se zobrazit v síťovém trasování na klientovi v prostém textu. Šifrování standardu AES-128 je vhodné pro případy použití, kde se jedná o důvěryhodnou osobu (například k šifrování firemních videí distribuovaných v rámci společnosti, které mají zaměstnanci prohlížet).

Systémy DRM, jako je PlayReady, Widevine a FairPlay, poskytují další úroveň šifrování na klíč, který se používá k dešifrování obsahu v porovnání s jasným klíčem AES-128. Klíč obsahu je zašifrovaný na klíč chráněný modulem runtime DRM kromě jakéhokoli šifrování na úrovni přenosu poskytovaného protokolem TLS. Šifrování se navíc zpracovává v zabezpečeném prostředí na úrovni operačního systému, kde je obtížnější útok na uživatele se zlými úmysly. Pro případy použití doporučujeme použít DRM, kdy prohlížeč nemusí být důvěryhodnou stranou a potřebujete nejvyšší úroveň zabezpečení.

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>Návody zobrazit video jenom uživatelům, kteří mají určité oprávnění, bez použití Azure AD?

Nemusíte používat žádného konkrétního poskytovatele tokenu, jako je Azure Active Directory (Azure AD). Pomocí asymetrického šifrování klíčů můžete vytvořit vlastního zprostředkovatele [JWT](https://jwt.io/) (označovaný jako služba zabezpečeného tokenu nebo STS). Ve vlastní službě STS můžete přidat deklarace identity založené na obchodní logice.

Ujistěte se, že Vystavitel, skupina a deklarace identity se přesně shodují mezi tím, co je v tokenu JWT, a `ContentKeyPolicyRestriction` hodnotou použitou v `ContentKeyPolicy` .

Další informace najdete v tématu [Ochrana obsahu pomocí Media Services dynamického šifrování](content-protection-overview.md).

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>Jak a kde získá token JWT před jeho použitím k vyžádání licence nebo klíče?

V případě produkčního prostředí musíte mít službu zabezpečeného tokenu (tj. webovou službu), která vydává token JWT po požadavku HTTPS. Pro test můžete použít kód zobrazený v `GetTokenAsync` metodě definované v [program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).

Hráč vytvoří požadavek, po ověření uživatele, na službu STS pro takový token a přiřadí ho jako hodnotu tokenu. Můžete použít [rozhraní Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/).

Příklad spuštění služby STS s symetrickým klíčem nebo asymetrickým klíčem naleznete v tématu [Nástroj JWT](https://aka.ms/jwt). Příklad přehrávače, který je založený na Azure Media Player pomocí takového tokenu JWT, najdete v tématu [Azure Media test Tool](https://aka.ms/amtest). (Pokud chcete zobrazit vstup tokenu, rozbalte odkaz **player_settings** .)

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>Návody autorizovat požadavky na streamování videí pomocí šifrování AES?

Správným přístupem je použití služby Secure token Service. V závislosti na profilu uživatele přidejte v rámci služby STS různé deklarace identity (například "Premium User", "Basic User", "User bezplatnou zkušební verzi"). S různými deklaracemi v tokenu JWT může uživatel zobrazit různý obsah. Pro různé obsahy nebo prostředky `ContentKeyPolicyRestriction` bude mít odpovídající `RequiredClaims` hodnotu.

Použijte rozhraní API pro Azure Media Services ke konfiguraci poskytování licencí/klíčů a šifrování prostředků (jak je znázorněno v [této ukázce](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Další informace naleznete v tématech:

- [Přehled ochrany obsahu](content-protection-overview.md)
- [Návrh systému ochrany obsahu s více variantami DRM s využitím řízení přístupu](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>Mám použít protokol HTTP nebo HTTPS?
Aplikace aktéra ASP.NET MVC musí podporovat následující:

* Ověřování uživatelů prostřednictvím služby Azure AD, která je v rámci protokolu HTTPS.
* Výměna JWT mezi klientem a službou Azure AD, která je v protokolu HTTPS.
* Získání licence DRM klientem, který musí být v rámci protokolu HTTPS, pokud je doručování licencí poskytované Media Services. Sada produktů PlayReady nevyžaduje pro doručování licencí protokol HTTPS. Pokud je váš licenční server PlayReady mimo Media Services, můžete použít buď protokol HTTP, nebo HTTPS.

Aplikace přehrávače ASP.NET používá jako osvědčený postup protokol HTTPS, takže Media Player se nachází na stránce s protokolem HTTPS. Protokol HTTP je ale upřednostňovaný pro streamování, takže je potřeba zvážit tyto problémy se smíšeným obsahem:

* Prohlížeč nepovoluje smíšený obsah. Moduly plug-in, jako je Silverlight a modul plug-in OSMF pro zajištění hladkého a PŘERUŠOVANého připojení, to umožňují. Smíšený obsah je bezpečnostní riziko z důvodu hrozby, která umožňuje vložit škodlivý kód JavaScriptu, který může ohrozit zákaznická data. V prohlížečích je tato funkce ve výchozím nastavení zablokovaná. Jediným způsobem, jak se tento problém obejít, je na straně serveru (Origin) povolit všechny domény (bez ohledu na protokol HTTPS nebo HTTP). Tato možnost není pravděpodobně dobrý nápad.
* Vyhněte se Smíšenému obsahu. Aplikace přehrávače i Media Player by měly používat protokol HTTP nebo HTTPS. Když hrajete smíšený obsah, technologie Silverlight tech vyžaduje vymazání upozornění na smíšený obsah. Technik Flash zpracovává smíšený obsah bez upozornění na smíšený obsah.
* Pokud byl koncový bod streamování vytvořen před srpna 2014, nebude podporovat protokol HTTPS. V takovém případě vytvořte a použijte nový koncový bod streamování pro protokol HTTPS.

### <a name="what-about-live-streaming"></a>Co je živé streamování?

Můžete použít naprosto stejný návrh a implementaci, abyste chránili živé streamování v Media Services tím, že provedete Asset přidružený k programu jako prostředek VOD. Pokud chcete zajistit ochranu pomocí živého obsahu s více technologiemi DRM, použijte stejné nastavení nebo zpracování na Asset, jako by šlo o VOD Asset ještě předtím, než provedete přidružení assetu s živým výstupem.

### <a name="what-about-license-servers-outside-media-services"></a>Co jsou licenční servery mimo Media Services?

Zákazníci se často investovali do farmy licenčních serverů buď ve vlastním datovém centru, nebo v jednom hostovaném poskytovateli služeb DRM. Díky Media Services ochraně obsahu můžete pracovat v hybridním režimu. Obsah je možné hostovat a dynamicky chránit v Media Services a při doručování licencí DRM servery mimo Media Services. V takovém případě zvažte následující změny:

* Služba tokenů zabezpečení musí vydávat tokeny, které jsou přijatelné a můžou je ověřit farmou licenčního serveru. Například licenční servery Widevine, které poskytuje Axinom, vyžadují konkrétní token JWT, který obsahuje zprávu nároku. K vystavení takového tokenu JWT potřebujete službu STS. 
* V Media Services už nemusíte konfigurovat službu doručování licencí. Při konfiguraci nástroje musíte zadat adresy URL pro získání licence (pro PlayReady, Widevine a FairPlay) `ContentKeyPolicy` .

> [!NOTE]
> Widevine je služba poskytovaná společností Google a podléhá podmínkám služby a zásad ochrany osobních údajů Google.

## <a name="media-services-v2-vs-v3"></a>Media Services V2 vs. v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Můžu Azure Portal použít ke správě prostředků V3?

V současné době můžete použít [Azure Portal](https://portal.azure.com/) k těmto akcím:

* Spravujte [živé události](live-events-outputs-concept.md) v Media Services V3. 
* Umožňuje zobrazit (Nespravovat) [prostředky](assets-concept.md)v3. 
* [Získejte informace o přístupu k rozhraním API](./access-api-howto.md). 

Pro všechny ostatní úlohy správy (například [transformace a úlohy](transforms-jobs-concept.md) a [Ochrana obsahu](content-protection-overview.md)) použijte [REST API](/rest/api/media/), [Azure CLI](https://aka.ms/ams-v3-cli-ref)nebo jednu z podporovaných [sad SDK](media-services-apis-overview.md#sdks).

### <a name="is-there-an-assetfile-concept-in-v3"></a>Je v v3 koncept AssetFile?

`AssetFile`Koncept se odebral z rozhraní Media Services API a odděluje Media Services od závislosti sady SDK úložiště. Nyní Azure Storage, nikoli Media Services, uchovává informace, které patří do sady Storage SDK. 

Další informace najdete v tématu [migrace na Media Services V3](media-services-v2-vs-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Kde se nacházelo šifrování úložiště na straně klienta?

Teď doporučujeme použít šifrování úložiště na straně serveru (což je ve výchozím nastavení zapnuté). Další informace najdete v tématu [šifrování služby Azure Storage pro](../../storage/common/storage-service-encryption.md)neaktivní neaktivní data.

## <a name="offline-streaming"></a>Offline streamování

### <a name="fairplay-streaming-for-ios"></a>FairPlay streaming pro iOS

Následující nejčastější dotazy poskytují pomoc s řešením potíží s offline FairPlay streamingem pro iOS.

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Proč se v režimu offline jenom zvuk hraje, ale ne video?

Zdá se, že se jedná o návrh ukázkové aplikace. Když je v režimu offline k dispozici alternativní zvuková stopa (což je případ pro HLS), v režimu offline se jako výchozí použije alternativní zvuková stopa v systému iOS 10 i iOS 11. Pokud chcete toto chování pro offline režim přechodu na více snímků, odeberte alternativní zvukovou stopu z datového proudu. Chcete-li to provést na Media Services, přidejte Dynamický manifest filtru **pouze zvuk = false**. Jinými slovy, HLS adresa URL končí řetězcem **. ISM/manifest (Format = M3U8-AAPL, audio-Only = false)**. 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>Proč I po přidání zvuku = false zůstane zvuk pouze bez videa v režimu offline?

V závislosti na návrhu klíče mezipaměti pro Content Delivery Network se může obsah ukládat do mezipaměti. Vyprázdnit mezipaměť.

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>Podporuje se kromě iOS 10 offline režim přechodu do režimu FPS v iOS 11?

Ano. Režim offline režimu FPS je podporován pro iOS 10 a iOS 11.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>Proč v sadě SDK serveru pro FPS nejde najít dokument "offline přehrávání pomocí FairPlay streaming a HTTP Live Streaming"?

Vzhledem k tomu, že sada FPS Server SDK verze 4, tento dokument se sloučil do Průvodce programováním pro FairPlay streaming.

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>Jaká je struktura souborů ke stažení/offline na zařízeních s iOS?

Stažená struktura souborů na zařízení se systémem iOS vypadá jako na následujícím snímku obrazovky. `_keys`Složka uchovává stažené licence na FPS a jeden soubor úložiště pro každého hostitele licenční služby. `.movpkg`Složka obsahuje zvuk a video obsah. 

První složka s názvem, který končí spojovníkem následovaným číslem, obsahuje obsah videa. Číselná hodnota je šířka pásma ve špičce pro verze videa. Druhá složka s názvem, který končí čárkou následovanou 0, obsahuje zvukový obsah. Třetí složka s názvem `Data` obsahuje hlavní seznam skladeb obsahu FPS. Nakonec boot.xml poskytuje úplný popis `.movpkg` obsahu složky. 

![Struktura offline souborů pro ukázkovou aplikaci FairPlay iOS](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Zde je ukázkový soubor boot.xml:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

### <a name="widevine-streaming-for-android"></a>Streamování Widevine pro Android

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>Jak mohu doručovat trvalé licence (offline) pro některé klienty/uživatele a netrvalé licence (offline zakázáno) pro ostatní? Musím duplikovat obsah a používat samostatné klíče obsahu?

Vzhledem k tomu, že Media Services V3 umožňuje assetu mít více `StreamingLocator` instancí, můžete mít:

* Jedna `ContentKeyPolicy` instance s `license_type = "persistent"` `ContentKeyPolicyRestriction` deklarací identity na `"persistent"` a `StreamingLocator` .
* Další `ContentKeyPolicy` instance s `license_type="nonpersistent"` `ContentKeyPolicyRestriction` deklarací identity on `"nonpersistent` a `StreamingLocator` .
* Dvě `StreamingLocator` instance, které mají různé `ContentKey` hodnoty.

V závislosti na obchodní logice vlastní služby STS se v tokenu JWT vydávají různé deklarace identity. U tokenu lze získat pouze odpovídající licenci a přehrát lze pouze odpovídající adresu URL.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Jaké je mapování mezi Widevine a Media Services úrovní zabezpečení DRM?

Přehled architektury DRM společnosti Google "Widevine" definuje tři úrovně zabezpečení. [Dokumentace Azure Media Services v šabloně licence Widevine](widevine-license-template-overview.md) ale popisuje pět úrovní zabezpečení (požadavky na odolnost klienta pro přehrávání). V této části se dozvíte, jak se mapují úrovně zabezpečení.

Obě sady úrovní zabezpečení jsou definované Google Widevine. Rozdíl je v úrovni využití: architektura nebo rozhraní API. V rozhraní API Widevine se používá pět úrovní zabezpečení. `content_key_specs`Objekt, který obsahuje `security_level` , je rekonstruován a předán službě Widevine Global Delivery Service pomocí licenční služby Azure Media Services Widevine. Následující tabulka ukazuje mapování mezi dvěma sadami úrovní zabezpečení.

| **Úrovně zabezpečení definované v architektuře Widevine** |**Úrovně zabezpečení používané v rozhraní Widevine API**|
|---|---| 
| **Úroveň zabezpečení 1**: zpracování, kryptografie a řízení obsahu se provádí v prostředí pro důvěryhodné spouštění (TEE). V některých implementačních modelech se může zpracování zabezpečení provádět v různých čipy.|**security_level = 5**: šifrování, dekódování a veškerá manipulace s médii (komprimovaná a nekomprimovaná) musí být zpracovávány v Tee s hardwarovým zálohováním.<br/><br/>**security_level = 4**: šifrování a dekódování obsahu je třeba provést v rámci Tee se zálohovaným hardwarem.|
**Úroveň zabezpečení 2**: kryptografie (ale ne zpracování videa) se provádí v rámci Tee. Dešifrované vyrovnávací paměti jsou vráceny do aplikační domény a zpracovávány prostřednictvím samostatného grafického hardwaru nebo softwaru. Na úrovni 2 se však kryptografické informace stále zpracovávají pouze v rámci TEE.| **security_level = 3**: klíčový materiál a kryptografické operace je nutné provést v rámci Tee s hardwarovým zálohováním. |
| **Úroveň zabezpečení 3**: na zařízení není žádné Tee. K ochraně kryptografických informací a dešifrovaného obsahu v hostitelském operačním systému je možné učinit vhodná opatření. Implementace úrovně 3 může zahrnovat také hardwarový kryptografický modul, který zvyšuje jenom výkon, ne zabezpečení. | **security_level = 2**: je vyžadováno šifrování softwaru a vypsaný dekodér.<br/><br/>**security_level = 1**: je vyžadováno šifrování na základě softwarového bílého boxu.|

#### <a name="why-does-content-download-take-so-long"></a>Proč stahování obsahu trvá tak dlouho?

Existují dva způsoby, jak zvýšit rychlost stahování:

* Povolte síť pro doručování obsahu, aby se uživatelé lépe vypnuli a místo počátečního/streamového koncového bodu pro stažení obsahu. Pokud uživatel narazí na koncový bod streamování, každý segment HLS nebo fragment SPOJOVNÍKu se dynamicky zabalí a zašifruje. I když je tato latence v milisekundách pro každý segment nebo fragment, pokud máte video s hodinovým videem, kumulovaná latence může být velká a způsobit delší stahování.
* Poskytněte uživatelům možnost selektivně stahovat vrstvy kvality videa a zvukové stopy místo veškerého obsahu. V offline režimu neexistuje žádný bod ke stažení všech vrstev kvality. Toho můžete dosáhnout dvěma způsoby:

  * Řízená klientem: aplikace přehrávače se automaticky vybere nebo vybere uživatel, vrstvu kvality videa a zvukové stopy, které se mají stáhnout.
  * Řízená služba: pomocí funkce dynamického manifestu v Azure Media Services vytvoříte filtr (globální), který omezuje HLS seznam skladeb nebo POMLČKy na jednu vrstvu kvality videa a vybrané zvukové stopy. Pak se adresa URL pro stažení prezentovaná uživatelům zahrne do tohoto filtru.

## <a name="next-steps"></a>Další kroky

[Přehled Media Services V3](media-services-overview.md)
