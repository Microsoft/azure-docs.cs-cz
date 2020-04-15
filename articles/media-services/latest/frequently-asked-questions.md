---
title: Azure Media Services v3 nejčastější dotazy| Dokumenty společnosti Microsoft
description: Tento článek poskytuje odpovědi na nejčastější dotazy týkající se Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/07/2020
ms.author: juliako
ms.openlocfilehash: 713acbd098255af2869d7a462c9990f3d7e10bf1
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309187"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Media Services v3 nejčastější dotazy

Tento článek poskytuje odpovědi na nejčastější dotazy týkající se Azure Media Services v3.

## <a name="general"></a>Obecné

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Jaké role Azure můžou provádět akce s prostředky Azure Media Services? 

Viz [Řízení přístupu na základě rolí (RBAC) pro účty mediálních služeb](rbac-overview.md).

### <a name="how-do-i-stream-to-apple-ios-devices"></a>Jak se streamuje do zařízení Apple iOS?

Ujistěte se, že máte **(format=m3u8-aapl)** na konci cesty (po **/manifest** část URL) sdělit zdroj vysílání datového proudu server vrátit OBSAH HTTP Live Streaming (HLS) pro spotřebu na apple iOS nativní zařízení. Podrobnosti naleznete v [tématu Doručování obsahu](dynamic-packaging-overview.md).

### <a name="how-do-i-configure-media-reserved-units"></a>Jak nakonfiguruji rezervované jednotky médií?

U úloh analýzy zvuku a analýzy videa, které jsou spuštěny službou Media Services v3 nebo Video Indexer, doporučujeme zřídit účet s 10 rezervovanými jednotkami médií S3 (MRU). Pokud potřebujete více než 10 OBJEKTŮ MRU S3, otevřete lístek podpory pomocí [portálu Azure](https://portal.azure.com/).

Podrobnosti naleznete v tématu [Škálování zpracování médií](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Jaká je doporučená metoda zpracování videí?

Transformace slouží ke [konfiguraci běžných](https://docs.microsoft.com/rest/api/media/transforms) úloh pro kódování nebo analýzu videí. Každá transformace popisuje recept nebo pracovní postup úloh pro zpracování video souborů nebo zvukových souborů. [Úloha](https://docs.microsoft.com/rest/api/media/jobs) je skutečný požadavek na mediální služby použít transformace na vstupní video nebo zvukový obsah. Po vytvoření transformace můžete odeslat úlohy pomocí api mediálních služeb nebo libovolné z publikovaných sad SDK. Další informace najdete v tématu [Transformace a úlohy](transforms-jobs-concept.md).

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>Nahrál jsem, zakódoval a zveřejnil video. Proč se video nepřehrává, když se ho snažím streamovat?

Jedním z nejčastějších důvodů je, že nemáte koncový bod streamování, ze kterého se pokoušíte přehrát ve stavu Spuštěno.

### <a name="how-does-pagination-work"></a>Jak funguje stránkování?

Pokud používáte stránkování, měli byste vždy použít další odkaz k výčetkolekce a není závislá na konkrétní velikost stránky. Podrobnosti a příklady naleznete v [tématu Filtrování, řazení, stránkování](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Jaké funkce ještě nejsou dostupné ve službě Azure Media Services v3?

Podrobnosti naleznete v [tématu Mezery funkcí s ohledem na v2 API](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Jaký je proces přesouvání účtu mediálních služeb mezi předplatnými?  

Podrobnosti naleznete [v tématu Přesunutí účtu mediálních služeb mezi předplatnými](media-services-account-concept.md).

## <a name="live-streaming"></a>Živé streamování 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>Jak zastavím živý přenos po dokončení vysílání?

Můžete k němu přistupovat ze strany klienta nebo na straně serveru.

#### <a name="client-side"></a>Na straně klienta

Webová aplikace by měla uživatele vyzvat, pokud chce ukončit vysílání při zavírání prohlížeče. Jedná se o událost prohlížeče, kterou vaše webová aplikace zvládne.

#### <a name="server-side"></a>Strana serveru

Živé události můžete sledovat tak, že se přihlásíte k odběru událostí služby Azure Event Grid. Další informace naleznete v [tématu EventGrid schéma události](media-services-event-schemas.md#live-event-types).

Máte tyto možnosti:

* [Přihlaste se k odběru](reacting-to-media-services-events.md) událostí [Microsoft.Media.LiveEventEncoder](media-services-event-schemas.md#liveeventencoderdisconnected) na úrovni datového proudu Odpojené události a sledujte, že na chvíli nedojde k opětovnému připojení, abyste zastavili a odstranili vaši živou událost.
* [Přihlaste se k odběru](reacting-to-media-services-events.md) událostí [prezenčního signálu](media-services-event-schemas.md#liveeventingestheartbeat) na úrovni trati. Pokud všechny stopy mají příchozí přenosový tok klesá na 0 nebo poslední časové razítko se již zvyšuje, můžete bezpečně vypnout živé události. Srdeční tep události přicházejí v každých 20 sekund pro každou skladbu, takže to může být trochu verbose.

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>Jak vložím přestávky/videa a obrázkové břidlice během živého přenosu?

Živé kódování Media Services v3 ještě nepodporuje vkládání video nebo obrazových břidlic během živého přenosu. 

Zdrojové video můžete přepnout pomocí [živého místního kodéru.](recommended-on-premises-live-encoders.md) Mnoho aplikací poskytuje možnost přepínat zdroje, včetně Telestream Wirecast, Switcher Studio (na iOS) a OBS Studio (bezplatná aplikace).

## <a name="content-protection"></a>Ochrana obsahu

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>Mám použít šifrování clear key nebo systém DRM AES-128 clear key?

Zákazníci si často kladou otázku, zda by měli používat šifrování AES nebo systém DRM. Hlavní rozdíl mezi těmito dvěma systémy spoážit v tom, že pomocí šifrování AES je klíč obsahu přenášen klientovi přes TLS tak, aby byl klíč šifrován při přenosu, ale bez dalšího šifrování ("v jasném"). V důsledku toho je klíč, který se používá k dešifrování obsahu, přístupný hráči klienta a lze jej zobrazit v síťovém trasování na straně klienta ve formátu prostého textu. Šifrování jasného klíče AES-128 je vhodné pro případy použití, kdy je divák důvěryhodnou stranou (například šifrování firemních videí distribuovaných v rámci společnosti, které mají zaměstnanci zobrazit).

Systémy DRM jako PlayReady, Widevine a FairPlay poskytují další úroveň šifrování na klíči, který se používá k dešifrování obsahu, ve srovnání s jasným klíčem AES-128. Klíč obsahu je šifrován na klíč chráněný prostředím DRM kromě šifrování na úrovni přenosu poskytovaného protokolem TLS. Kromě toho je dešifrování zpracováno v zabezpečeném prostředí na úrovni operačního systému, kde je pro uživatele se zlými úmysly obtížnější zaútočit. Doporučujeme DRM pro případy použití, kdy prohlížeč nemusí být důvěryhodnou stranou a potřebujete nejvyšší úroveň zabezpečení.

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>Jak můžu zobrazit video jenom uživatelům, kteří mají konkrétní oprávnění, bez použití Azure AD?

Nemusíte používat žádné konkrétní zprostředkovatele tokenů, jako je Azure Active Directory (Azure AD). Můžete vytvořit vlastní poskytovatele [JWT](https://jwt.io/) (tzv Secure Token Service, nebo STS) pomocí asymetrického šifrování klíče. Ve vašem vlastním STS můžete přidat deklarace identity na základě obchodní logiky.

Ujistěte se, že vystavit, publikum a nároky všechny zápas přesně mezi `ContentKeyPolicyRestriction` co `ContentKeyPolicy`je v JWT a hodnota použitá v .

Další informace naleznete v [tématu Ochrana obsahu pomocí dynamického šifrování služby Media Services](content-protection-overview.md).

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>Jak a kde jsem získal token JWT před použitím k žádosti o licenci nebo klíč?

Pro produkční prostředí musíte mít službu Secure Token Service (tj. webovou službu), která vydává token JWT na požadavek HTTPS. Pro test můžete použít kód zobrazený v metodě `GetTokenAsync` definované v [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).

Hráč po ověření uživatele požádá o takový token a přiřadí jej jako hodnotu tokenu. Rozhraní Azure [Media Player API](https://amp.azure.net/libs/amp/latest/docs/)můžete použít .

Příklad spuštění STS s symetrickou klávesou nebo asymetrickou klávesou naleznete v [nástroji JWT](https://aka.ms/jwt). Příklad přehrávače založeného na Azure Media Playeru pomocí takového tokenu JWT najdete v [nástroji pro testování médií Azure](https://aka.ms/amtest). (Rozbalte **odkaz player_settings** a zobceme vstup tokenu.)

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>Jak povolím žádosti o streamování videí pomocí šifrování AES?

Správný přístup je použití služby Zabezpečené tokeny. V STS, v závislosti na profilu uživatele, přidejte různé deklarace identity (například "Premium User", "Základní uživatel", "Uživatel zkušební verze zdarma"). S různými deklaracemi v JWT může uživatel zobrazit jiný obsah. Pro různé obsahy `ContentKeyPolicyRestriction` nebo majetek, bude mít odpovídající `RequiredClaims` hodnotu.

Pomocí virtuálních zařízení Azure Media Services pro konfiguraci doručování licencí a klíčů a šifrování vašich prostředků (jak je znázorněno v [této ukázce).](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)

Další informace naleznete v tématu:

- [Přehled ochrany obsahu](content-protection-overview.md)
- [Návrh systému ochrany obsahu s více variantami DRM s využitím řízení přístupu](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>Mám použít HTTP nebo HTTPS?
Aplikace ASP.NET MVC player musí podporovat následující:

* Ověřování uživatelů prostřednictvím Azure AD, který je v části HTTPS.
* JWT exchange mezi klientem a Azure AD, který je v rámci protokolu HTTPS.
* Získání licence DRM klientem, které musí být v rámci protokolu HTTPS, pokud je doručení licence poskytováno službou Media Services. Sada produktů PlayReady nenařizuje protokol HTTPS pro doručování licencí. Pokud je licenční server PlayReady mimo službu Media Services, můžete použít protokol HTTP nebo HTTPS.

Aplikace ASP.NET přehrávačpoužívá jako osvědčený postup protokol HTTPS, takže media player je na stránce v části HTTPS. Protokol HTTP je však upřednostňován pro streamování, takže je třeba zvážit tyto problémy se smíšeným obsahem:

* Prohlížeč nepovoluje smíšený obsah. Ale plug-iny jako Silverlight a OSMF plug-in pro Smooth a DASH to umožňují. Smíšený obsah je bezpečnostní problém z důvodu hrozby schopnosti aplikovat škodlivý JavaScript, což může ohrozit zákaznická data. Prohlížeče tuto funkci ve výchozím nastavení blokují. Jediný způsob, jak to obejít, je na straně serveru (původu) povolením všech domén (bez ohledu na protokol HTTPS nebo HTTP). To asi není dobrý nápad jeden.
* Vyhněte se smíšenému obsahu. Aplikace přehrávače i přehrávač médií by měly používat protokol HTTP nebo HTTPS. Při přehrávání smíšeného obsahu vyžaduje technologie SilverlightSS vymazání upozornění na smíšený obsah. Technologie FlashSS zpracovává smíšený obsah bez upozornění na smíšený obsah.
* Pokud byl koncový bod streamování vytvořen před srpnem 2014, nebude podporovat protokol HTTPS. V takovém případě vytvořte a použijte nový koncový bod streamování pro protokol HTTPS.

### <a name="what-about-live-streaming"></a>A co živé vysílání?

Můžete použít přesně stejný návrh a implementaci k ochraně živého streamování ve službě Media Services tím, že s datovým zdrojem přidruženým k programu zacházíte jako s datovým zdrojem VOD. Chcete-li poskytnout ochranu živého obsahu s více DRM, použijte stejné nastavení nebo zpracování na datový zdroj, jako by se jednalo o prostředek VOD před přidružením datového zdroje k živému výstupu.

### <a name="what-about-license-servers-outside-media-services"></a>A co licenční servery mimo službu Media Services?

Zákazníci často investovali do licenční serverové farmy buď ve vlastním datovém centru, nebo do jedné hostované poskytovateli služeb DRM. S ochranou obsahu služby Media Services můžete pracovat v hybridním režimu. Obsah může být hostován a dynamicky chráněn ve službě Media Services, zatímco licence DRM jsou dodávány servery mimo službu Media Services. V takovém případě zvažte následující změny:

* SLUŽBA STS musí vydávat tokeny, které jsou přijatelné a mohou být ověřeny farmou licenčního serveru. Například licenční servery Widevine poskytované společností Axinom vyžadují konkrétní JWT, který obsahuje zprávu o nároku. Musíte mít STS vydat takový JWT. 
* Službu doručování licencí již není nutné konfigurovat ve službě Media Services. Při konfiguraci `ContentKeyPolicy`je třeba zadat adresy URL pro získání licence (pro PlayReady, Widevine a FairPlay).

> [!NOTE]
> Widevine je služba poskytovaná společností Google a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google.

## <a name="media-services-v2-vs-v3"></a>Mediální služby v2 vs. v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Můžu použít portál Azure ke správě prostředků v3?

V současné době můžete na [webu Azure Portal:](https://portal.azure.com/)

* Správa [živých událostí](live-events-outputs-concept.md) ve službě Media Services v3. 
* Zobrazit (nespravovat) [v3 datových zdrojů](assets-concept.md). 
* [Získejte informace o přístupu k api](access-api-portal.md). 

Pro všechny ostatní úlohy správy (například [transformace a úlohy](transforms-jobs-concept.md) a [ochrana obsahu](content-protection-overview.md)) použijte rozhraní [REST API](https://docs.microsoft.com/rest/api/media/), rozhraní [příkazového příkazu Azure](https://aka.ms/ams-v3-cli-ref)nebo jednu z podporovaných sad [SDK](media-services-apis-overview.md#sdks).

### <a name="is-there-an-assetfile-concept-in-v3"></a>Existuje koncept AssetFile ve v3?

Koncept `AssetFile` byl odebrán z rozhraní API mediálních služeb, aby oddělil mediální služby od závislosti sady Storage SDK. Azure Storage, ne Media Services, teď uchovává informace, které patří do sady Storage SDK. 

Další informace naleznete v [tématu Migrace do služby Media Services v3](media-services-v2-vs-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Kam se událo šifrování úložiště na straně klienta?

Nyní doporučujeme použít šifrování úložiště na straně serveru (které je ve výchozím nastavení zapnuto). Další informace najdete v tématu [Šifrování služby Azure Storage Service pro data v klidovém stavu](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="offline-streaming"></a>Offline streamování

### <a name="fairplay-streaming-for-ios"></a>Streamování fairplay pro iOS

Následující nejčastější dotazy poskytují pomoc při odstraňování problémů s offline streamováním FairPlay pro iOS.

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Proč se v režimu offline přehrává pouze zvuk, ale ne video?

Toto chování se zdá být záměrné ukázkové aplikace. Pokud je v režimu offline k dispozici alternativní zvuková stopa (což je případ HLS), iOS 10 i iOS 11 jsou ve výchozím nastavení na alternativní zvukovou stopu. Chcete-li kompenzovat toto chování pro režim offline FPS, odeberte alternativní zvukovou stopu z datového proudu. Chcete-li to provést ve službě Media Services, přidejte filtr dynamického manifestu **audio-only=false**. Jinými slovy, ADRESA URL HLS končí **.ism/manifest(format=m3u8-aapl,audio-only=false)**. 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>Proč se po přidání zvuku=false stále přehrává zvuk pouze bez videa v režimu offline?

V závislosti na návrhu klíče mezipaměti pro síť pro doručování obsahu může být obsah uložen do mezipaměti. Vyčistěte mezipaměť.

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>Je režim offline FPS podporován v systému iOS 11 kromě iOS 10?

Ano. Režim offline FPS je podporován pro iOS 10 a iOS 11.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>Proč nemohu najít dokument "Offline přehrávání s FairPlay Streaming a HTTP Live Streaming" v FPS Server SDK?

Od doby, kdy byla sada FPS Server SDK verze 4, byl tento dokument sloučen do průvodce programováním streamování hry FairPlay.

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>Co je stahovaná/offline struktura souborů na zařízeních se systémem iOS?

Struktura stažených souborů na iOS zařízení vypadá jako na následujícím snímku obrazovky. Složka `_keys` ukládá stažené licence FPS s jedním souborem úložiště pro každého hostitele licenční služby. Složka `.movpkg` ukládá zvukový obsah a videoobsah. 

První složka s názvem, který končí pomlčkou následovanou číslem, obsahuje videoobsah. Číselná hodnota je maximální šířka pásma interpretací videa. Druhá složka s názvem, který končí pomlčkou následovanou 0, obsahuje zvukový obsah. Třetí s `Data` názvem obsahuje hlavní seznam stop obsahu FPS. Nakonec soubor boot.xml poskytuje úplný `.movpkg` popis obsahu složky. 

![Offline struktura souborů pro ukázkovou aplikaci FairPlay iOS](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

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

### <a name="widevine-streaming-for-android"></a>Widevine streaming pro Android

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>Jak mohu dodat trvalé licence (offline povoleno) pro některé klienty / uživatele a netrvalé licence (offline zakázáno) pro ostatní? Musím duplikovat obsah a používat samostatné klíče obsahu?

Vzhledem k tomu, že služba `StreamingLocator` Media Services v3 umožňuje datovému zdroji mít více instancí, můžete mít:

* Jedna `ContentKeyPolicy` instance `license_type = "persistent"` `ContentKeyPolicyRestriction` s , `"persistent"`s `StreamingLocator`nárokem na , a jeho .
* Další `ContentKeyPolicy` instance `license_type="nonpersistent"` `ContentKeyPolicyRestriction` s , `"nonpersistent`s nárokem na ", a jeho `StreamingLocator`.
* Dvě `StreamingLocator` instance, které `ContentKey` mají různé hodnoty.

V závislosti na obchodní logiku vlastní STS, různé deklarace identity jsou vydány v tokenu JWT. S tokenem lze získat pouze odpovídající licenci a hrát pouze odpovídající ADRESU URL.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Jaké je mapování mezi úrovněmi zabezpečení DRM Widevine a Media Services?

Google "Widevine DRM Architektura Přehled" definuje tři úrovně zabezpečení. [Dokumentace k Mediální službě Azure na šabloně licence Widevine](widevine-license-template-overview.md) však popisuje pět úrovní zabezpečení (požadavky na odolnost klientů pro přehrávání). Tato část vysvětluje, jak se mapují úrovně zabezpečení.

Obě sady úrovní zabezpečení jsou definovány společností Google Widevine. Rozdíl je v úrovni využití: architektura nebo rozhraní API. Pět úrovní zabezpečení se používá v rozhraní WIDEVINE API. Objekt, `content_key_specs` který `security_level`obsahuje , je reserializován a předán službě globální doručení Widevine licenční službou Azure Media Services Widevine. V následující tabulce je uvedeno mapování mezi dvěma sadami úrovní zabezpečení.

| **Úrovně zabezpečení definované v architektuře Widevine** |**Úrovně zabezpečení používané v rozhraní Widevine API**|
|---|---| 
| **Úroveň zabezpečení 1**: Veškeré zpracování obsahu, kryptografie a řízení se provádějí v prostředí trusted execution environment (TEE). V některých modelech implementace může být zpracování zabezpečení prováděno v různých čipech.|**security_level=5**: S šifrováním, dekódováním a veškerým zpracováním média (komprimované a nekomprimované) musí být nakládáno v rámci tee podporovaného hardwarem.<br/><br/>**security_level=4**: Šifrování a dekódování obsahu musí být provedeno v rámci tee podporovaného hardwarem.|
**Úroveň zabezpečení 2**: Kryptografie (nikoli však zpracování videa) se provádí v rámci TEE. Dešifrované vyrovnávací paměti jsou vráceny do domény aplikace a zpracovány prostřednictvím samostatného grafického hardwaru nebo softwaru. Na úrovni 2 jsou však kryptografické informace stále zpracovávány pouze v rámci TEE.| **security_level=3**: Klíčové materiálové a kryptografické operace musí být prováděny v rámci TEE podporovaného hardwarem. |
| **Úroveň zabezpečení 3**: Na zařízení není tee. Mohou být přijata vhodná opatření k ochraně kryptografických informací a dešifrovaného obsahu v hostitelském operačním systému. Implementace úrovně 3 může také obsahovat hardwarový kryptografický modul, ale to zvyšuje pouze výkon, nikoli zabezpečení. | **security_level=2**: Je vyžadováno softwarové krypto a zamlžený dekodér.<br/><br/>**security_level=1**: Je vyžadováno softwarové šifrování bílé krabice.|

#### <a name="why-does-content-download-take-so-long"></a>Proč stahování obsahu trvá tak dlouho?

Rychlost stahování lze zlepšit dvěma způsoby:

* Povolte síť pro doručování obsahu, aby uživatelé s větší pravděpodobností dosáhli přístupového bodu namísto koncového bodu původu a streamování pro stahování obsahu. Pokud uživatel narazí na koncový bod streamování, každý segment HLS nebo DASH fragment je dynamicky zabalena a šifrována. I když je tato latence v milisekundovém měřítku pro každý segment nebo fragment, pokud máte hodinové video, nahromaděná latence může být velká a způsobit delší stahování.
* Dejte uživatelům možnost selektivně stahovat vrstvy kvality videa a zvukové stopy namísto veškerého obsahu. Pro režim offline nemá smysl stahovat všechny vrstvy kvality. Existují dva způsoby, jak toho dosáhnout:

  * Klient: Aplikace přehrávače automaticky vybere nebo uživatel vybere vrstvu kvality videa a zvukové stopy ke stažení.
  * Řízeno službou: Funkci dynamického manifestu ve službě Azure Media Services můžete použít k vytvoření (globálního) filtru, který omezuje seznam stop HLS nebo DASH MPD na jednu vrstvu kvality videa a vybrané zvukové stopy. Pak bude adresa URL pro stažení prezentovaná uživatelům obsahovat tento filtr.

## <a name="next-steps"></a>Další kroky

[Mediální služby v3 – přehled](media-services-overview.md)
