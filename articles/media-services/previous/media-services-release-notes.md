---
title: Poznámky k verzi Azure Media Services | Dokumenty společnosti Microsoft
description: Tento článek popisuje poznámky k verzi Microsoft Azure Media Services v2.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2019
ms.author: juliako
ms.openlocfilehash: a3893c8d19c89b639e0584f203cbcd1adf7e2dee
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474846"
---
# <a name="azure-media-services-release-notes"></a>Poznámky k verzi Azure Media Services

Tyto poznámky k verzi pro Azure Media Services shrnují změny z předchozích verzí a známých problémů.

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

Chceme slyšet od našich zákazníků, abychom se mohli soustředit na řešení problémů, které se vás týkají. Pokud chcete nahlásit problém nebo položit otázky, odešlete příspěvek na [fóru MSDN služby Azure Media Services]. 

## <a name="known-issues"></a><a id="issues"/>Známé problémy
### <a name="media-services-general-issues"></a><a id="general_issues"/>Obecné problémy mediálních služeb

| Problém | Popis |
| --- | --- |
| Několik běžných záhlaví protokolu HTTP není k dispozici v rozhraní REST API. |Pokud vyvíjíte aplikace Media Services pomocí rozhraní REST API, zjistíte, že některá běžná pole záhlaví PROTOKOLU HTTP (včetně CLIENT-REQUEST-ID, REQUEST-ID a RETURN-CLIENT-REQUEST-ID) nejsou podporována. Záhlaví budou přidána v budoucí aktualizaci. |
| Procento kódování není povoleno. |Služba Media Services používá hodnotu vlastnosti IAssetFile.Name při vytváření adres `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters`URL pro streamovaný obsah (například). Z tohoto důvodu není povoleno kódování procentuální chod. Hodnota Name vlastnost nemůže mít žádné z následujících [procent-kódování vyhrazené znaky:](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)!*'();:@&=+$,/?%#[]". Také může existovat pouze jeden "." pro příponu názvu souboru. |
| ListBlobs metoda, která je součástí sady Azure Storage SDK verze 3.x selže. |Služba Media Services generuje adresy URL SAS na základě verze [2012-02-12.](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) Pokud chcete pomocí sady Storage SDK vypsat objekty BLOB v kontejneru objektů blob, použijte metodu [CloudBlobContainer.ListBlobs,](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs) která je součástí sady Storage SDK verze 2.x. |
| Mechanismus omezení mediálních služeb omezuje využití prostředků pro aplikace, které poskytují nadměrné požadavky na službu. Služba může vrátit stavový kód HTTP "Služba není k dispozici". |Další informace naleznete v popisu stavového kódu 503 HTTP v [kódech chyb služby Media Services](media-services-encoding-error-codes.md). |
| Při dotazování entit je současně vrácen limit 1 000 entit, protože veřejná verze REST 2 omezuje výsledky dotazu na 1 000 výsledků. |Použijte Přeskočit a vzít (.NET)/top (REST), jak je popsáno v [tomto příkladu .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) a [v tomto příkladu rozhraní REST API](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Někteří klienti mohou narazit na problém s opakovanou značkou v manifestu Plynulé streamování. |Další informace naleznete v [této části](media-services-deliver-content-overview.md#known-issues). |
| Objekty sady Media Services .NET SDK nelze serializovat a v důsledku toho nefungují s azure cache pro Redis. |Pokud se pokusíte serializovat objekt SDK AssetCollection a přidat ho do mezipaměti Azure pro Redis, je vyvolána výjimka. |
|Rozhraní REST API odpoví chybovou zprávou "Filtr nemůže být přístupný touto verzí rozhraní REST Api" při pokusu o získání filtru na úrovni majetku nebo účtu.|Filtr byl vytvořen nebo upraven s novější verzí rozhraní API, než se používá k pokusu o získání filtru. K tomu může dojít, pokud jsou dvě verze rozhraní API používány kódem nebo nástroji používanými zákazníkem.  Nejlepším řešením je zde upgrade kódu nebo nástroje pro použití novější nebo dvě verze rozhraní API.|

## <a name="rest-api-version-history"></a><a id="rest_version_history"/>Historie verzí rozhraní REST API
Informace o historii verzí rozhraní API rozhraní MEDIA Services REST najdete v [tématu odkaz na rozhraní REST api služby Azure Media Services].

## <a name="february-2020"></a>Únor 2020

Některé procesory analytických médií budou vyřazeny. Data vyřazení naleznete v tématu [starších součástí.](legacy-components.md)

## <a name="september-2019"></a>Září 2019

### <a name="deprecation-of-media-processors"></a>Vyřazení mediálních procesorů

Oznamujeme vyřazení Azure *Media Indexer* a *Azure Media Indexer 2 Preview*. [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) nahradí tyto starší mediální procesory.

Data vyřazení naleznete v tomto tématu [starších součástí.](legacy-components.md)

Viz taky [migrace z Azure Media Indexer a Azure Media Indexer 2 do Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Srpen 2019

### <a name="deprecation-of-media-processors"></a>Vyřazení mediálních procesorů

Oznamujeme vyřazení mediálních procesorů *Windows Azure Media Encoder* (WAME) a *Azure Media Encoder* (AME). Data vyřazení naleznete v tomto tématu [starších součástí.](legacy-components.md)

Podrobnosti naleznete [v tématech Migrace wame do standardu kodéru médií](https://go.microsoft.com/fwlink/?LinkId=2101334) a migrace [ame do standardu kodéru médií](https://go.microsoft.com/fwlink/?LinkId=2101335).

## <a name="march-2019"></a>Březen 2019

Funkce Media Hyperlapse Preview služby Azure Media Services byla zastaralá.

## <a name="december-2018"></a>Prosinec 2018

Funkce Media Hyperlapse Preview služby Azure Media Services bude brzy vyřazena. prosince 2018 již mediální služby nebudou provádět změny ani vylepšování technologie Media Hyperlapse. 29. března 2019 bude v důchodu a již nebude k dispozici.

## <a name="october-2018"></a>Říjen 2018

### <a name="cmaf-support"></a>Podpora pro CMAF

Cmaf a 'cbcs' podpora šifrování pro Apple HLS (iOS 11 +) a MPEG-DASH přehrávače, které podporují CMAF.

### <a name="web-vtt-thumbnail-sprites"></a>Miniatury webových vtt miniatur

Nyní můžete pomocí mediálních služeb generovat miniatury webových virtuálních obrazovek pomocí našich virtuálních api. Další informace naleznete [v tématu Generování miniaturpohybci](generate-thumbnail-sprite.md).

## <a name="july-2018"></a>Červenec 2018

V nejnovější verzi služby dochází k menším změnám formátování chybových zpráv vrácených službou při selhání úlohy, s ohledem na to, jak je rozdělena na dva nebo více řádků.

## <a name="may-2018"></a>Květen 2018 

května 2018 již živé kanály nebudou podporovat protokol ingestestování datového proudu rtp/MPEG-2. Migrujte z protokolů s ingestováním RTP/MPEG-2 do RTMP nebo fragmentovaných protokolů s ingestováním MP4 (Smooth Streaming).

## <a name="october-2017-release"></a>Verze z října 2017
> [!IMPORTANT] 
> Služba Media Services zavrhuje podporu ověřovacích klíčů služby Azure Access Control Service. 22. června 2018 již nelze ověřit pomocí back-endu Mediálních služeb pomocí kódu pomocí klíčů služby Řízení přístupu. Musíte aktualizovat svůj kód používat Azure Active Directory (Azure AD) na základě [ověřování azure ad](media-services-use-aad-auth-to-access-ams-api.md). Sledujte upozornění na tuto změnu na webu Azure Portal.

### <a name="updates-for-october-2017"></a>Aktualizace za říjen 2017
#### <a name="sdks"></a>Sady SDK
* Sada .NET SDK byla aktualizována tak, aby podporovala ověřování Azure AD. Podpora ověřování služby řízení přístupu byla odebrána z nejnovější sady .NET SDK v Nuget.org, aby se podpořila rychlejší migrace do služby Azure AD. 
* Sada JAVA SDK byla aktualizována tak, aby podporovala ověřování Azure AD. Do sady Java SDK byla přidána podpora pro ověřování azure ad. Informace o tom, jak používat sadku Java SDK se službami Media Services, najdete [v tématu Začínáme s sadou SDK klienta Java pro mediální služby Azure](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Kódování založené na souborech
* Nyní můžete pomocí prémiového enkodéru kódovat obsah do videokatu H.265 s vysoce účinným kódováním videa (HEVC). Pokud zvolíte H.265 před jinými kodeky, například H.264, nebude mít žádný vliv na ceny. Informace o patentových licencích HEVC naleznete [v podmínkách online služeb](https://azure.microsoft.com/support/legal/).
* U zdrojového videa, které je kódováno videokodekem H.265 (HEVC), například video zachycené pomocí iOS11 nebo GoPro Hero 6, můžete nyní k kódování těchto videí použít buď prémiový kodér, nebo standardní kodér. Informace o patentových licencích naleznete [v podmínkách online služeb](https://azure.microsoft.com/support/legal/).
* U obsahu, který obsahuje více jazykových zvukových stop, musí být jazykové hodnoty správně označeny podle odpovídající specifikace formátu souboru (například ISO MP4). Potom můžete použít standardní kodér ke kódování obsahu pro streamování. Výsledný vyhledávač streamování uvádí dostupné jazyky zvuku.
* Standardní kodér nyní podporuje dvě nová přednastavení systému pouze pro zvuk, "AAC Audio" a "AAC Good Quality Audio". Oba produkují stereo pokročilé audio kódování (AAC) výstup, při přenosových rychlostech 128 kb/s a 192 kb/s.
* Prémiový kodér nyní podporuje formáty souborů QuickTime/MOV jako vstup. Kodek videa musí být jedním z [typů Apple ProRes uvedených v tomto článku GitHubu](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats). Zvuk musí být buď AAC nebo pulzní kód modulace (PCM). Premium Encoder nepodporuje, například DVC/DVCPro video zabalené v QuickTime /MOV soubory jako vstup. Standardní kodér podporuje tyto video kodeky.
* V kodérech byly provedeny následující opravy chyb:

    * Nyní můžete odesílat úlohy pomocí vstupního majetku. Po dokončení těchto úloh můžete datový zdroj upravit (například přidat, odstranit nebo přejmenovat soubory v rámci datového zdroje) a odeslat další úlohy.
    * Zlepšila se kvalita miniatur JPEG vytvořených standardním kodérem.
    * Standardní kodér zpracovává vstupní metadata a generování miniatur lépe ve velmi krátkých videích.
    * Vylepšení dekodéru H.264 použitého ve standardním kodéru eliminují některé vzácné artefakty. 

#### <a name="media-analytics"></a>Media Analytics
Obecná dostupnost editoru médií Azure: Tento mediální procesor provádí anonymizaci rozmazáním tváří vybraných jednotlivců a je ideální pro použití ve scénářích veřejné bezpečnosti a zpravodajských médií. 

Přehled tohoto nového procesoru najdete v [tomto příspěvku blogu](https://azure.microsoft.com/blog/azure-media-redactor/). Informace o dokumentaci a nastavení najdete v tématu [React tváře s Azure Media Analytics](media-services-face-redaction.md).



## <a name="june-2017-release"></a>Vydání z června 2017

Mediální služby teď podporují [ověřování na základě Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> V současné době služba Media Services podporuje model ověřování služby Řízení přístupu. 1. června 2018 bude oprávnění služby Access Control Service zastaralé. Doporučujeme, abyste na model ověřování Azure AD migrovali co nejdříve.

## <a name="march-2017-release"></a>Vydání z března 2017

Nyní můžete použít standardní kodér k [automatickému generování žebříku s přenosovou rychlostí](media-services-autogen-bitrate-ladder-with-mes.md) zadáním přednastaveného řetězce "Adaptivní streamování" při vytváření úlohy kódování. Chcete-li kódovat video pro streamování pomocí mediálních služeb, použijte přednastavení Adaptivní streamování. Chcete-li přizpůsobit přednastavení kódování pro konkrétní scénář, můžete začít s [těmito přednastaveními](media-services-mes-presets-overview.md).

Nyní můžete pomocí standardního programu Media Encoder standardu nebo programu Media Encoder Premium [vytvořit úlohu kódování, která generuje bloky fMP4](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>Vydání z února 2017

dubna 2017 se automaticky odstraní všechny záznamy o úloze ve vašem účtu starší než 90 dní spolu s přidruženými záznamy úkolů. K odstranění dochází i v případě, že celkový počet záznamů je nižší než maximální kvóta. Chcete-li archivovat informace o úloze/úloze, můžete pomocí kódu popsaného v části [Správa datových zdrojů a souvisejících entit pomocí sady Media Services .NET SDK](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>Vydání z ledna 2017

Ve službě Media Services představuje koncový bod streamování službu streamování, která může doručovat obsah přímo do aplikace klientského přehrávače nebo do sítě pro doručování obsahu (CDN) pro další distribuci. Mediální služby také poskytují bezproblémovou integraci sítě doručování obsahu Azure. Odchozí datový proud ze služby StreamingEndpoint může být živý přenos, video na vyžádání nebo postupné stahování vašeho datového zdroje v účtu Mediálních služeb. Každý účet služby Media Services obsahuje výchozí koncový bod streamování. Další koncové body streamování lze vytvořit pod účtem. 

Existují dvě verze koncových bodů streamování, 1.0 a 2.0. ledna 2017 všechny nově vytvořené účty Služby Media Services obsahují výchozí koncový bod streamování verze 2.0. Další koncové body streamování, které přidáte do tohoto účtu, jsou také verze 2.0. Tato změna nemá vliv na existující účty. Existující koncové body streamování jsou verze 1.0 a lze je upgradovat na verzi 2.0. S touto změnou dochází ke změnám chování, fakturace a funkcí. Další informace najdete v [přehledu koncových bodů streamování](media-services-streaming-endpoints-overview.md).

Počínaje verzí 2.15 přidala služba Media Services do entity koncového bodu streamování následující vlastnosti:

* Zprostředkovatel cdn 
* Profil Cdn
* FreeTrialEndTime 
* Streamovací endpointversion 

Další informace o těchto vlastnostech naleznete v tématu [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>Vydání z prosince 2016

 Nyní můžete použít Media Services pro přístup k telemetrických dat nebo metriky pro své služby. Aktuální verzi služby Media Services můžete použít ke shromažďování telemetrických dat pro živý kanál, koncový bod streamování a archivní entity. Další informace naleznete v tématu [Telemetrie mediálních služeb](media-services-telemetry-overview.md).

## <a name="july-2016-release"></a><a id="july_changes16"/>Vydání z července 2016
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Aktualizace souboru manifestu (*. ISM) generované úlohami kódování
Při odeslání úlohy kódování do standardu media encoder nebo media encoder Premium, úloha kódování generuje [soubor manifestu streamování](media-services-deliver-content-overview.md) (*.ism) ve výstupním datovém zdroji. V nejnovější verzi služby byla aktualizována syntaxe tohoto souboru manifestu streamování.

> [!NOTE]
> Syntaxe souboru manifestu streamování (.ism) je vyhrazena pro interní použití. To se může změnit v budoucích verzích. Obsah tohoto souboru neupravujte ani s ním nemanipulujte.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Nový manifest klienta (*. ISMC) je generován ve výstupním datovém zdroji, když úloha kódování vyprodukuje jeden nebo více souborů MP4
Počínaje nejnovější verzí služby po dokončení úlohy kódování, která generuje jeden nebo více souborů MP4, obsahuje výstupní datový zdroj také soubor manifestu klienta streamování (*.ismc). Soubor ISMC pomáhá zlepšit výkon dynamického streamování. 

> [!NOTE]
> Syntaxe souboru manifestu klienta (.ismc) je vyhrazena pro interní použití. To se může změnit v budoucích verzích. Obsah tohoto souboru neupravujte ani s ním nemanipulujte.
> 
> 

Další informace naleznete v [tomto blogu](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/).

### <a name="known-issues"></a>Známé problémy
Někteří klienti mohou narazit na problém s opakovanou značkou v manifestu Plynulé streamování. Další informace naleznete v [této části](media-services-deliver-content-overview.md#known-issues).

## <a name="april-2016-release"></a><a id="apr_changes16"></a>Vydání z dubna 2016
### <a name="media-analytics"></a>Media Analytics
 Společnost Media Services představila službu Media Analytics pro výkonnou video inteligenci. Další informace naleznete v tématu [Přehled analýzy mediálních služeb](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (náhled)
Nyní můžete pomocí mediálních služeb dynamicky šifrovat obsah http živého streamování (HLS) pomocí Apple FairPlay. Můžete také použít službu doručování licencí Media Services k doručování licencí FairPlay klientům. Další informace najdete v tématu "Pomocí Mediálních služeb Azure můžete streamovat obsah HLS chráněný pomocí Apple FairPlay.".

## <a name="february-2016-release"></a><a id="feb_changes16"></a>Vydání z února 2016
Nejnovější verze sady Media Services SDK pro rozhraní .NET (3.5.3) obsahuje opravu chyb související se službou Google Widevine. Nebylo možné znovu použít AssetDeliveryPolicy pro více prostředků šifrovaných pomocí Widevine. Jako součást této opravy chyby byla do sady SDK přidána následující vlastnost: WidevineBaseLicenseAcquisitionUrl.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a name="january-2016-release"></a><a id="jan_changes_16"></a>Vydání z ledna 2016
Rezervované jednotky kódování byly přejmenovány, aby se snížily nejasnosti s názvy kodéru.

Rezervované jednotky kódování Basic, Standard a Premium byly přejmenovány na rezervované jednotky S1, S2 a S3. Zákazníci, kteří dnes používají rezervované jednotky základního kódování, vidí S1 jako popisek na webu Azure Portal (a na vyúčtování). Zákazníci, kteří používají standard a premium, vidí štítky S2 a S3. 

## <a name="december-2015-release"></a><a id="dec_changes_15"></a>Vydání z prosince 2015

### <a name="media-encoder-deprecation-announcement"></a>Oznámení o vyřazení kodéru médií

 Kodéru médií bude zastaralé přibližně za 12 měsíců od vydání standardu Media Encoder Standard.

### <a name="azure-sdk-for-php"></a>Azure SDK pro PHP
Tým sady Azure SDK publikoval novou verzi balíčku [Azure SDK pro PHP,](https://github.com/Azure/azure-sdk-for-php) který obsahuje aktualizace a nové funkce pro mediální služby. Zejména sada Media Services SDK pro PHP nyní podporuje nejnovější funkce [ochrany obsahu.](media-services-content-protection-overview.md) Tyto funkce jsou dynamické šifrování s AES a DRM (PlayReady a Widevine) s a bez omezení tokenu. Podporuje také škálování [kódovacích jednotek](media-services-dotnet-encoding-units.md).

Další informace naleznete v tématu:

* Následující [ukázky kódu](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) vám pomohou rychle začít:
  * **vodworkflow_aes.php**: Tento soubor PHP ukazuje, jak používat AES-128 dynamické šifrování a služby doručování klíčů. Je založen na ukázce .NET vysvětlené v [použití dynamického šifrování AES-128 a služby doručování klíčů](media-services-protect-with-aes128.md).
  * **vodworkflow_aes.php**: Tento soubor PHP ukazuje, jak používat PlayReady dynamické šifrování a službu doručování licencí. Je založen na ukázce .NET vysvětlené v [use PlayReady a/nebo Widevine dynamické společné šifrování](media-services-protect-with-playready-widevine.md).
  * **scale_encoding_units.php**: Tento soubor PHP ukazuje, jak škálovat kódování rezervovaných jednotek.

## <a name="november-2015-release"></a><a id="nov_changes_15"></a>Vydání z listopadu 2015
 Služba Media Services nyní nabízí službu doručování licencí Widevine v cloudu. Další informace naleznete v [tomto blogu](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Viz [také tento návod](media-services-protect-with-playready-widevine.md) a úložiště [GitHub](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Služby doručování licencí Widevine poskytované službou Media Services jsou ve verzi Preview. Další informace naleznete v [tomto blogu](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a name="october-2015-release"></a><a id="oct_changes_15"></a>Vydání z října 2015
Mediální služby jsou nyní aktivní v následujících datových centrech: Brazílie – jih, Indie – západ, Indie – jih a Indie – střed. Nyní můžete pomocí portálu Azure [vytvářet účty služby Media Service](media-services-portal-create-account.md) a provádět různé úkoly popsané na [webové stránce dokumentace Mediálních služeb](https://azure.microsoft.com/documentation/services/media-services/). Živé kódování není v těchto datových centrech povoleno. Dále nejsou v těchto datových centrech k dispozici všechny typy rezervovaných jednotek kódování.

* Brazílie – jih: K dispozici jsou pouze rezervované jednotky kódování standardu a základního kódování.
* Indie – západ, Indie – jih a Indie – střed: K dispozici jsou pouze rezervované jednotky základního kódování.

## <a name="september-2015-release"></a><a id="september_changes_15"></a>Vydání ze září 2015
Mediální služby nyní nabízí možnost chránit video na vyžádání i živé přenosy pomocí modulární technologie DRM Widevine. K poskytování licencí Widevine můžete použít následující partnery poskytující ch služeb:
* [Axinom](https://www.axinom.com) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

Další informace naleznete v [tomto blogu](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
Konfiguraci zásady AssetDeliveryConfiguration na používání technologie Widevine můžete provést pomocí sady [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (počínaje verzí 3.5.1) nebo rozhraní REST API. 
* Mediální služby přidaly podporu pro videa Apple ProRes. Nyní můžete nahrát soubory zdrojových videí QuickTime, které používají Apple ProRes nebo jiné kodeky. Další informace naleznete v [tomto blogu](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Nyní můžete použít Standard kodéru médií k subclipping a extrakci živého archivu. Další informace naleznete v [tomto blogu](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Byly provedeny následující aktualizace filtrování: 
  
  * Nyní můžete použít formát Apple HLS s filtrem pouze pro zvuk. Tuto aktualizaci můžete použít k odebrání zvukové stopy zadáním (audio-only=false) v adrese URL.
  * Když definujete filtry pro své datové zdroje, můžete nyní kombinovat více (až tři) filtry v jedné adrese URL.
    
    Další informace naleznete v [tomto blogu](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).
* Mediální služby nyní podporují I-rámce v HLS verze 4. Podpora i-frame optimalizuje operace rychlého převíjení vpřed a vzad. Ve výchozím nastavení všechny výstupy HLS verze 4 zahrnují seznam stop I-frame (EXT-X-I-FRAME-STREAM-INF).
Další informace naleznete v [tomto blogu](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="august-2015-release"></a><a id="august_changes_15"></a>Vydání ze srpna 2015
* Sada Media Services SDK pro verzi jazyka Java verze 0.8.0 a nové ukázky jsou nyní k dispozici. Další informace naleznete v tématu:
    
* Azure Media Player byl aktualizován s podporou více zvukových datových proudů. Další informace naleznete v [tomto příspěvku blogu](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a name="july-2015-release"></a><a id="july_changes_15"></a>Vydání z července 2015
* Byla oznámena obecná dostupnost standardu Media Encoder Standard. Další informace naleznete v [tomto příspěvku blogu](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Standard Media Encoder Standard používá přednastavení, jak je popsáno v [této části](https://go.microsoft.com/fwlink/?LinkId=618336). Když použijete přednastavení pro kódování 4K, získejte typ rezervované jednotky Premium. Další informace naleznete v tématu [Škálování kódování](media-services-scale-media-processing-overview.md).
* Živé titulky v reálném čase byly použity se službami Media Services a media playerem. Další informace naleznete v [tomto příspěvku blogu](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Aktualizace sady Media Services .NET SDK
Sada Media Services .NET SDK je nyní verze 3.4.0.0. Byly provedeny následující aktualizace: 

* Podpora byla implementována pro živý archiv. Nelze stáhnout datový zdroj, který obsahuje živý archiv.
* Podpora byla implementována pro dynamické filtry.
* Funkce byla implementována tak, aby uživatelé mohli zachovat kontejner úložiště při odstraňování datového zdroje.
* Opravy chyb byly provedeny v souvislosti se zásadami opakování v kanálech.
* Pracovní postup Premium pro připojení k médiím byl povolen.

## <a name="june-2015-release"></a><a id="june_changes_15"></a>Vydání z června 2015
### <a name="media-services-net-sdk-updates"></a>Aktualizace sady Media Services .NET SDK
Sada Media Services .NET SDK je nyní verze 3.3.0.0. Byly provedeny následující aktualizace: 

* Byla přidána podpora pro specifikaci OpenId Connect.
* Byla přidána podpora pro zpracování klíče rollover na straně zprostředkovatele identity.

Pokud používáte zprostředkovatele identity, který zveřejňuje dokument zjišťování OpenID Connect (jako Azure AD, Google a Salesforce), můžete dát pokyn, aby Media Services získali podpisové klíče pro ověření Webových tokenů JSON (JWT) ze specifikace zjišťování OpenID Connect. 

Další informace naleznete [v tématu Použití webových klíčů JSON ze specifikace zjišťování OpenID Connect pro práci s ověřováním JWT ve službě Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a name="may-2015-release"></a><a id="may_changes_15"></a>Vydání z května 2015
Byly oznámeny následující nové funkce:

* [Náhled živého kódování pomocí mediálních služeb](media-services-manage-live-encoder-enabled-channels.md)
* [Dynamický manifest](media-services-dynamic-manifest-overview.md)

## <a name="april-2015-release"></a><a id="april_changes_15"></a>Vydání z dubna 2015
### <a name="general-media-services-updates"></a>Obecné aktualizace mediálních služeb
* [Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) byl oznámen.
* Počínaje media services REST 2.10 jsou kanály, které jsou nakonfigurovány pro ingestování protokolu RTMP (Real-Time Messaging Protocol), vytvořeny s primárními a sekundárními adresami URL ingestování. Další informace naleznete v tématu [Konfigurace ingestování kanálu](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Azure Media Indexer byl aktualizován.
* Byla přidána podpora španělského jazyka.
* Byla přidána nová konfigurace formátu XML.

Další informace naleznete v [tomto blogu](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Aktualizace sady Media Services .NET SDK
Sada Media Services .NET SDK je nyní verze 3.2.0.0. Byly provedeny následující aktualizace:

* Změna porušení: TokenRestrictionTemplate.Issuer a TokenRestrictionTemplate.Audience byly změněny na typ řetězce.
* Aktualizace byly provedeny v souvislosti s vytvářením vlastních zásad opakování.
* Opravy chyb byly provedeny v souvislosti s nahráváním a stahováním souborů.
* Třída MediaServicesCredentials nyní přijímá primární a sekundární koncové body řízení přístupu k ověření.

## <a name="march-2015-release"></a><a id="march_changes_15"></a>Vydání z března 2015
### <a name="general-media-services-updates"></a>Obecné aktualizace mediálních služeb
* Mediální služby nyní poskytují integraci sítě pro doručování obsahu. Pro podporu integrace byla vlastnost CdnEnabled přidána do objektu StreamingEndpoint. CdnEnabled lze použít s rest API počínaje verzí 2.9. Další informace naleznete v tématu [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). CdnEnabled lze použít s sadou .NET SDK začínající verzí 3.1.0.2. Další informace naleznete v tématu [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx).
* Byl oznámen prémiový pracovní postup Media Encoder. Další informace najdete [v tématu Zavedení kódování Premium ve službě Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a name="february-2015-release"></a><a id="february_changes_15"></a>Vydání z února 2015
### <a name="general-media-services-updates"></a>Obecné aktualizace mediálních služeb
Rozhraní REST API mediálních služeb je nyní verze 2.9. Počínaje touto verzí můžete povolit integraci sítě doručování obsahu s koncovými body streamování. Další informace naleznete v tématu [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a name="january-2015-release"></a><a id="january_changes_15"></a>Vydání z ledna 2015
### <a name="general-media-services-updates"></a>Obecné aktualizace mediálních služeb
Byla oznámena obecná dostupnost ochrany obsahu pomocí dynamického šifrování. Další informace naleznete v [tématu Media Services enhances streaming security with general availability of DRM technology](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Aktualizace sady Media Services .NET SDK
Sada Media Services .NET SDK je nyní verze 3.1.0.1.

Tato verze označila výchozí konstruktor Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate jako zastaralý. Nový konstruktor bere TokenType jako argument.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a name="december-2014-release"></a><a id="december_changes_14"></a>Vydání z prosince 2014
### <a name="general-media-services-updates"></a>Obecné aktualizace mediálních služeb
* Do programu Media Indexer byly přidány některé aktualizace a nové funkce. Další informace naleznete v [tématu Azure Media Indexer verze 1.1.6.7 poznámky k verzi](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Bylo přidáno nové rozhraní REST API, které můžete použít k aktualizaci rezervovaných jednotek kódování. Další informace naleznete v [tématu EncodingReservedUnitType with REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* Pro službu doručování klíčů byla přidána podpora CORS.
* Vylepšení výkonu byla provedena při dotazování možností zásad autorizace.
* V čínském datovém centru je nyní [adresa URL klíčového doručení](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) na zákazníka (stejně jako v jiných datových centrech).
* Byla přidána doba trvání automatického cíle HLS. Při živém streamování je HLS vždy dynamicky zabaleno. Ve výchozím nastavení media services automaticky vypočítá poměr balení segmentu HLS (FragmentsPerSegment) na základě intervalu klíčových snímků (KeyFrameInterval). Tato metoda se také označuje jako skupina obrázků (GOP), která je přijata z živého kodéru. Další informace naleznete v [tématu Práce s mediálními službami živé vysílání](https://msdn.microsoft.com/library/azure/dn783466.aspx).

### <a name="media-services-net-sdk-updates"></a>Aktualizace sady Media Services .NET SDK
Sada [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) je nyní verze 3.1.0.0. Byly provedeny následující aktualizace:

* Závislost sady .NET SDK byla upgradována na rozhraní .NET 4.5 Framework.
* Bylo přidáno nové rozhraní API, které můžete použít k aktualizaci rezervovaných jednotek kódování. Další informace naleznete v [tématu Aktualizace typu rezervované jednotky a zvýšení kódování rezervovaných jednotek pomocí rozhraní .NET](media-services-dotnet-encoding-units.md).
* Byla přidána podpora JWT pro ověřování tokenů. Další informace naleznete v tématu [Ověřování tokenů JWT ve službě Media Services a dynamické šifrování](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* Byly přidány relativní posuny pro BeginDate a ExpirationDate v šabloně licence PlayReady.

## <a name="november-2014-release"></a><a id="november_changes_14"></a>Vydání z listopadu 2014
* Nyní můžete pomocí služby Media Services ingestovat živý obsah plynulého streamování (fMP4) prostředněk připojení TLS. Chcete-li ingestovat přes TLS, nezapomeňte aktualizovat ingestující adresu URL na HTTPS. V současné době media services nepodporuje TLS s vlastními doménami. Další informace o živém streamování najdete v [tématu Práce s Azure Media Services Live Streaming](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* V současné době nelze ingestovat rtmp živého datového proudu přes připojení TLS.
* Streamovat přes TLS můžete pouze v případě, že koncový bod streamování, ze kterého doručujete obsah byl vytvořen po 10 září 2014. Pokud jsou vaše adresy URL streamování založeny na koncových bodech streamování vytvořených po 1 streaming.mediaservices.windows.net0. Streamované adresy URL, které obsahují "origin.mediaservices.windows.net" (starý formát), nepodporují TLS. Pokud je vaše adresa URL ve starém formátu a chcete streamovat přes TLS, [vytvořte nový koncový bod streamování](media-services-portal-manage-streaming-endpoints.md). Chcete-li streamovat obsah přes TLS, použijte adresy URL založené na novém koncovém bodu streamování.

### <a name="media-services-net-sdk"></a><a id="oct_sdk"></a>Sada Media Services .NET SDK
Sada Media Services SDK pro rozšíření .NET je nyní verze 2.0.0.3.

Sada Media Services SDK pro rozhraní .NET je nyní verze 3.0.0.8. Byly provedeny následující aktualizace:

* Refaktoring byl implementován ve třídách zásad opakování.
* Řetězec uživatelského agenta byl přidán do hlavičky požadavku HTTP.
* Byl přidán krok sestavení obnovení NuGet.
* Testy scénářů byly opraveny tak, aby používaly certifikát x509 z úložiště.
* Nastavení ověření byly přidány při aktualizaci kanálu a datového proudu konce.

### <a name="new-github-repository-to-host-media-services-samples"></a>Nové úložiště GitHub pro hostování ukázek Mediálních služeb
Ukázky jsou v [ukázkách mediálních služeb úložiště GitHub](https://github.com/Azure/Azure-Media-Services-Samples).

## <a name="september-2014-release"></a><a id="september_changes_14"></a>Vydání ze září 2014
Metadata REST služby Media Services je nyní verze 2.7. Další informace o nejnovějších aktualizacích rest naleznete v [odkazu rozhraní REST API mediálních služeb](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Sada Media Services SDK pro rozhraní .NET je nyní verze 3.0.0.7

### <a name="breaking-changes"></a><a id="sept_14_breaking_changes"></a>Změny způsobující chyby
* Origin byl přejmenován na [StreamingEndpoint].
* Změna byla provedena ve výchozím chování při použití portálu Azure ke kódování a publikování souborů MP4.

### <a name="new-featuresscenarios-that-are-part-of-the-general-availability-release"></a><a id="sept_14_GA_changes"></a>Nové funkce/scénáře, které jsou součástí verze obecné dostupnosti
* Byl zaveden mediální procesor Media Indexer. Další informace naleznete [v tématu Index media files with the Media Indexer](https://msdn.microsoft.com/library/azure/dn783455.aspx).
* K přidání vlastních názvů domén (hostitelů) můžete použít entitu [StreamingEndpoint.]
  
    Chcete-li jako název koncového bodu streamování služby Media Services použít vlastní název domény, přidejte do koncového bodu streamování vlastní názvy hostitelů. Pomocí rozhraní API REST mediálních služeb nebo sady .NET SDK přidejte vlastní názvy hostitelů.
  
    Platí následující důležité informace:
  
  * Musíte mít vlastnictví vlastního názvu domény.
  * Vlastnictví názvu domény musí být ověřeno službou Media Services. Chcete-li doménu ověřit, vytvořte název CName, který mapuje nadřazenou doménu MediaServicesAccountId a ověřuje zónu DNS mediaservices-dns.
  * Musíte vytvořit jiný CName, který mapuje vlastní název hostitele (například sports.contoso.com) na název hostitele Media Services StreamingEndpoint (například amstest.streaming.mediaservices.windows.net).

    Další informace naleznete v vlastnosti CustomHostNames v článku [StreamingEndpoint.](https://msdn.microsoft.com/library/azure/dn783468.aspx)

### <a name="new-featuresscenarios-that-are-part-of-the-public-preview-release"></a><a id="sept_14_preview_changes"></a>Nové funkce/scénáře, které jsou součástí verze Public Preview
* Náhled živého streamování. Další informace naleznete v [tématu Práce s mediálními službami živé vysílání](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* Služba doručování klíčů. Další informace naleznete [v tématu Použití dynamického šifrování AES-128 a služby doručování klíčů](https://msdn.microsoft.com/library/azure/dn783457.aspx).
* Dynamické šifrování AES. Další informace naleznete [v tématu Použití dynamického šifrování AES-128 a služby doručování klíčů](https://msdn.microsoft.com/library/azure/dn783457.aspx).
* Služba doručování licencí PlayReady. 
* Dynamické šifrování PlayReady. 
* Šablona licence PlayReady služby Media Services. Další informace naleznete v [přehledu šablony licence PlayReady služby Media Services].
* Streamujte prostředky šifrované úložištěm. Další informace naleznete v tématu [Stream storage-encrypted content](https://msdn.microsoft.com/library/azure/dn783451.aspx).

## <a name="august-2014-release"></a><a id="august_changes_14"></a>Vydání ze srpna 2014
Když kódujete datový zdroj, výstupní datový zdroj se vytvoří po dokončení úlohy kódování. Až do této verze, Media Services Encoder vyrábí metadata o výstupních datových zdrojů. Počínaje touto verzí kodér také vytváří metadata o vstupních datových zdrojů. Další informace naleznete [v tématu Vstupní metadata] a [výstupní metadata].

## <a name="july-2014-release"></a><a id="july_changes_14"></a>Vydání z července 2014
Pro služby Azure Media Services Packager a Encryptor byly provedeny následující opravy chyb:

* Když je živý archivní prostředek přenášen do HLS, přehrává se pouze zvuk: Tento problém byl opraven a nyní se může přehrávat zvuk i video.
* Když je datový zdroj zabalen do šifrování 128bitové obálky HLS a AES, zabalené datové proudy se nepřehrávají na zařízeních android: Tato chyba byla opravena a zabalený datový proud se přehrává na zařízeních android, která podporují HLS.

## <a name="may-2014-release"></a><a id="may_changes_14"></a>Vydání z května 2014
### <a name="general-media-services-updates"></a><a id="may_14_changes"></a>Obecné aktualizace mediálních služeb
Nyní můžete použít [dynamické balení] pro streamování HLS verze 3. Chcete-li streamovat HLS verze 3, přidejte do cesty původu lokátoru následující formát: * .ism/manifest(format=m3u8-aapl-v3). Další informace naleznete na [tomto fóru](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3).

Dynamické balení nyní také podporuje doručování HLS (verze 3 a verze 4) šifrované s PlayReady založené na plynulém streamování staticky šifrované s PlayReady. Informace o šifrování plynulého streamování pomocí služby PlayReady naleznete v [tématu Ochrana plynulého streamování pomocí služby PlayReady](https://msdn.microsoft.com/library/azure/dn189154.aspx).

### <a name="media-services-net-sdk-updates"></a><a name="may_14_donnet_changes"></a>Aktualizace sady Media Services .NET SDK
Sada Media Services .NET SDK je nyní verze 3.0.0.5. Byly provedeny následující aktualizace:

* Rychlost a odolnost jsou lepší, když nahrajete a stáhnete mediální prostředky.
* Vylepšení byla provedena v logice opakování a zpracování přechodných výjimek: 
  
  * Přechodová chyba detekce a opakování logiky byly vylepšeny pro výjimky, které jsou způsobeny při dotazování, ukládání změn a nahrávání nebo stahování souborů. 
  * Když získáte výjimky z webu (například během požadavku tokenu služby řízení přístupu), závažné chyby se nezdaří rychleji.

Další informace naleznete [v tématu Logika opakování v sadě Media Services SDK pro rozhraní .NET].

## <a name="januaryfebruary-2014-releases"></a><a id="jan_feb_changes_14"></a>Leden / únor 2014 zprávy
### <a name="media-services-net-sdk-3001-3002-and-3003"></a><a name="jan_fab_14_donnet_changes"></a>Mediální služby .NET SDK 3.0.0.1, 3.0.0.2 a 3.0.0.3
Změny v 3.0.0.1 a 3.0.0.2 zahrnují:

* Byly opraveny problémy související s používáním dotazů LINQ s příkazy OrderBy.
* Testovací řešení v [GitHubu] byla rozdělena na testy založené na jednotkách a testy založené na scénářích.

Další informace o změnách naleznete ve [verzích Media Services .NET SDK 3.0.0.1 a 3.0.0.2](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html).

Ve verzi 3.0.0.3 byly provedeny následující změny:

* Závislosti úložiště Azure byly upgradovány na verzi 3.0.3.0.
* Problém se zpětnou kompatibilitou byl opraven pro 3.0. *.* Uvolní.

## <a name="december-2013-release"></a><a id="december_changes_13"></a>Vydání z prosince 2013
### <a name="media-services-net-sdk-3000"></a><a name="dec_13_donnet_changes"></a>Mediální služby .NET SDK 3.0.0.0
> [!NOTE]
> Verze 3.0.x.x nejsou zpětně kompatibilní s verzemi 2.4.x.x.
> 
> 

Nejnovější verze sady Media Services SDK je nyní 3.0.0.0. Můžete si stáhnout nejnovější balíček z NuGet nebo získat bity z [GitHubu].

Počínaje sadou Media Services SDK verze 3.0.0.0 můžete znovu použít tokeny [služby Azure AD Access Control Service.](https://msdn.microsoft.com/library/hh147631.aspx) Další informace naleznete v části Opakované použití tokenů služby Řízení přístupu v [části Připojení k mediálním službám pomocí sady Media Services SDK pro rozhraní .NET](https://msdn.microsoft.com/library/azure/jj129571.aspx).

### <a name="media-services-net-sdk-extensions-2000"></a><a name="dec_13_donnet_ext_changes"></a>Rozšíření sady Media Services .NET SDK 2.0.0.0
 Rozšíření sady Media Services .NET SDK jsou sadou rozšiřujících metod a pomocných funkcí, které zjednodušují váš kód a usnadňují vývoj pomocí mediálních služeb. Nejnovější bity můžete získat z [rozšíření sady Media Services .NET SDK](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev).

## <a name="november-2013-release"></a><a id="november_changes_13"></a>Vydání z listopadu 2013
### <a name="media-services-net-sdk-changes"></a><a name="nov_13_donnet_changes"></a>Změny sady Media Services .NET SDK
Počínaje touto verzí zpracovává sada Media Services SDK pro rozhraní .NET přechodné chyby, ke kterým může dojít při volání do vrstvy rozhraní REST API mediálních služeb.

## <a name="august-2013-release"></a><a id="august_changes_13"></a>Vydání ze srpna 2013
### <a name="media-services-powershell-cmdlets-included-in-azure-sdk-tools"></a><a name="aug_13_powershell_changes"></a>Rutiny prostředí PowerShell pro mediální služby zahrnuté v nástrojích sady Azure SDK
Následující rutiny prostředí PowerShell pro mediální služby jsou teď součástí [nástrojů sady Azure SDK](https://github.com/Azure/azure-sdk-tools):

* Get-AzureMediaServices 

    Příklad: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Příklad: `New-AzureMediaServicesAccount -Name "MediaAccountName" -Location "Region" -StorageAccountName "StorageAccountName"`
* New-AzureMediaServicesKey 
  
    Příklad: `New-AzureMediaServicesKey -Name "MediaAccountName" -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Příklad: `Remove-AzureMediaServicesAccount -Name "MediaAccountName" -Force`

## <a name="june-2013-release"></a><a id="june_changes_13"></a>Vydání z června 2013
### <a name="media-services-changes"></a><a name="june_13_general_changes"></a>Změny mediálních služeb
Následující změny uvedené v této části jsou aktualizace zahrnuty v červnu 2013 Media Services verze:

* Možnost propojit více účtů úložiště s účtem mediální služby. 
    * StorageAccount
    * Asset.StorageAccountName a Asset.StorageAccount
* Možnost aktualizovat Job.Priority. 
* Entity a vlastnosti související s oznámením: 
    * JobNotificationSubscription
    * OznámeníEndPoint
    * Úloha
* Asset.Uri 
* Locator.Name 

### <a name="media-services-net-sdk-changes"></a><a name="june_13_dotnet_changes"></a>Změny sady Media Services .NET SDK
Následující změny jsou zahrnuty ve verzích sady Media Services SDK z června 2013. Nejnovější sada Media Services SDK je k dispozici na GitHubu.

* Počínaje verzí 2.3.0.0 podporuje sada Media Services SDK propojení více účtů úložiště s účtem Mediálních služeb. Následující api podporují tuto funkci:
  
    * Typ účtu IStorage
    * Vlastnost Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts
    * Vlastnost StorageAccount
    * Vlastnost StorageAccountName
  
      Další informace naleznete v [tématu Správa datových zdrojů mediálních služeb napříč více účty úložiště](https://msdn.microsoft.com/library/azure/dn271889.aspx).
* Api související s oznámením. Počínaje verzí 2.2.0.0 můžete poslouchat oznámení o úložišti služby Azure Queue. Další informace naleznete v [tématu Handle Media Services oznámení o úlohách](https://msdn.microsoft.com/library/azure/dn261241.aspx).
  
    * Vlastnost Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions
    * Typ Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint
    * Typ Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription
    * Typ Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection
    * Typ Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType
* Závislost na klientovi úložiště SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll)
* Závislost na OData 5.5 (Microsoft.Data.OData.dll)

## <a name="december-2012-release"></a><a id="december_changes_12"></a>Vydání z prosince 2012
### <a name="media-services-net-sdk-changes"></a><a name="dec_12_dotnet_changes"></a>Změny sady Media Services .NET SDK
* Technologie IntelliSense: Pro mnoho typů byla přidána chybějící dokumentace technologie IntelliSense.
* Microsoft.Practices.TransientFaultHandling.Core: Byl opraven problém, kdy sada SDK stále měla závislost na staré verzi tohoto sestavení. Sada SDK nyní odkazuje na verzi 5.1.1209.1 tohoto sestavení.

Opravy problémů nalezených v sdk z listopadu 2012:

* IAsset.Locators.Count: Tento počet je nyní správně hlášen na nových rozhraních IAsset po odstranění všech lokátorů.
* IAssetFile.ContentFileSize: Tato hodnota je nyní správně nastavena po nahrání iAssetFile.Upload(filepath).
* IAssetFile.ContentFileSize: Tuto vlastnost lze nyní nastavit při vytváření souboru datového zdroje. To bylo dříve jen pro čtení.
* IAssetFile.Upload(filepath): Byl opraven problém, kdy tato metoda synchronního nahrávání vyvolával následující chybu, když bylo do datového zdroje nahráno více souborů. Chyba byla "Server se nepodařilo ověřit požadavek. Ujistěte se, že hodnota hlavičky Autorizace je vytvořena správně včetně podpisu."
* IAssetFile.UploadAsync: Byl opraven problém, který omezil současné nahrávání souborů na pět souborů.
* IAssetFile.UploadProgressChanged: Tato událost je nyní poskytována sadou SDK.
* IAssetFile.DownloadAsync(řetězec, BlobTransferClient, ILocator, CancellationToken): Tato metoda přetížení je nyní k dispozici.
* IAssetFile.DownloadAsync: Byl opraven problém, který omezil současné stahování souborů na pět souborů.
* IAssetFile.Delete(): Byl opraven problém, kdy volání odstranění může vyvolat výjimku, pokud pro soubor IAssetFile nebyl nahrán žádný soubor.
* Úlohy: Byl opraven problém, kdy zřetězení úlohy "MP4 na hladké datové proudy" pomocí úlohy ochrany PlayReady pomocí šablony úlohy nevytvořilo vůbec žádné úkoly.
* EncryptionUtils.GetCertificateFromStore(): Tato metoda již nevyvolá výjimku nulového odkazu z důvodu selhání při hledání certifikátu na základě problémů s konfigurací certifikátu.

## <a name="november-2012-release"></a><a id="november_changes_12"></a>Vydání z listopadu 2012
Změny uvedené v této části byly aktualizace zahrnuty v listopadu 2012 (verze 2.0.0.0) SDK. Tyto změny mohou vyžadovat, aby byl jakýkoli kód napsaný pro verzi sady SDK preview z června 2012 upraven nebo přepsán.

* Prostředky
  
    * IAsset.Create(assetName) je *jediná* funkce vytváření majetku. IAsset.Create již nenahrává soubory jako součást volání metody. Pro nahrávání použijte soubor IAssetFile.
    * Metoda IAsset.Publish a hodnota výčtu AssetState.Publish byly odebrány ze sady SDK sady Services. Jakýkoli kód, který závisí na této hodnotě, musí být přepsán.
* Fileinfo
  
    * Tato třída byla odebrána a nahrazena souborem IAssetFile.
  
* Soubory iAsset
  
    * Soubor IAssetFile nahrazuje FileInfo a má jiné chování. Chcete-li jej použít, vytvořte instanci objektu IAssetFiles následovaným nahráním souboru pomocí sady Media Services SDK nebo sady Storage SDK. Lze použít následující přetížení iAssetFile.Upload:
  
        * IAssetFile.Upload(filePath): Tato synchronní metoda blokuje vlákno a doporučujeme ji pouze při nahrání jednoho souboru.
        * IAssetFile.UploadAsync(filePath, blobTransferClient, lokátor, cancellationToken): Tato asynchronní metoda je upřednostňovaným mechanismem nahrávání. 
    
            Známá chyba: Pokud použijete token zrušení, nahrávání se zruší. Úkoly mohou mít mnoho stavů zrušení. Musíte správně zachytit a zpracovat výjimky.
* Lokátory
  
    * Verze specifické pro původ byly odebrány. Kontext specifický pro SAS. Locators.CreateSasLocator (prostředek, accessPolicy) budou označeny zastaralé nebo odebrány podle obecné dostupnosti. Aktualizované chování naleznete v části Lokátory v části "Nové funkce".

## <a name="june-2012-preview-release"></a><a id="june_changes_12"></a>Vydání preview z června 2012
Následující funkce byla novinkou v listopadové verzi sady SDK:

* Odstranění entit
  
    * Objekty IAsset, IAssetFile, ILocator, IAccessPolicy a IContentKey jsou nyní odstraněny na úrovni objektu, to znamená IObject.Delete(), namísto vyžadování odstranění v kolekci, to znamená cloudMediaContext.ObjCollection.Delete(objInstance).
* Lokátory
  
    * Lokátory nyní musí být vytvořeny pomocí Metody CreateLocator. Musí použít hodnoty výčtu LocatorType.SAS nebo LocatorType.OnDemandOrigin jako argument pro konkrétní typ lokátoru, který chcete vytvořit.
    * Do lokátorů byly přidány nové vlastnosti, které usnadňují získání použitelných identifikátorů URI pro váš obsah. Tento redesign lokátorů poskytuje větší flexibilitu pro budoucí rozšiřitelnost třetích stran a zvyšuje snadnost použití pro aplikace mediálních klientů.
* Podpora asynchronní metody
  
    * Ke všem metodám byla přidána asynchronní podpora.

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Fórum MSDN služby Azure Media Services]: https://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Odkaz na rozhraní REST služby Azure Media Services]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services pricing details]: https://azure.microsoft.com/pricing/details/media-services/
[Vstupní metadata]: https://msdn.microsoft.com/library/azure/dn783120.aspx
[Výstupní metadata]: https://msdn.microsoft.com/library/azure/dn783217.aspx
[Deliver content]: https://msdn.microsoft.com/library/azure/hh973618.aspx
[Index media files with the Azure Media Indexer]: https://msdn.microsoft.com/library/azure/dn783455.aspx
[Datový proudEndpoint]: https://msdn.microsoft.com/library/azure/dn783468.aspx
[Work with Media Services live streaming]: https://msdn.microsoft.com/library/azure/dn783466.aspx
[Use AES-128 dynamic encryption and the key delivery service]: https://msdn.microsoft.com/library/azure/dn783457.aspx
[Use PlayReady dynamic encryption and the license delivery service]: https://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: https://azure.microsoft.com/services/preview/
[Přehled šablony licence PlayReady služby Media Services]: https://msdn.microsoft.com/library/azure/dn783459.aspx
[Stream storage-encrypted content]: https://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://portal.azure.com
[Dynamické balení]: https://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: https://msdn.microsoft.com/library/azure/dn189154.aspx
[Logika opakování v sadě Media Services SDK pro rozhraní .NET]: https://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley announces EDIUS 7 streaming through the cloud]: https://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: https://msdn.microsoft.com/library/azure/dn303341.aspx
[Create overlays]: https://msdn.microsoft.com/library/azure/dn640496.aspx
[Stitch video segments]: https://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: https://msdn.microsoft.com/library/hh147631.aspx
[Connect to Media Services with the Media Services SDK for .NET]: https://msdn.microsoft.com/library/azure/jj129571.aspx
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHubu]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: https://msdn.microsoft.com/library/azure/dn271889.aspx
[Handle Media Services job notifications]: https://msdn.microsoft.com/library/azure/dn261241.aspx

