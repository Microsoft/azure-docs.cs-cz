---
title: Zpráva k vydání verze služby Media Services | Dokumentace Microsoftu
description: Zpráva k vydání verze služby Media Services
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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: ae2697c96435ec18ae7645520d438c41837f036f
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53016857"
---
# <a name="azure-media-services-release-notes"></a>Poznámky k verzi Azure Media Services
Tyto poznámky k verzi pro Azure Media Services shrnují změny z předchozích verzí a známých problémů.

> [!NOTE]
> Chceme slyšet od našich zákazníků, takže se můžeme zaměřit na opravu problémů, které se vás týkají. Pokud chcete nahlásit problém nebo položit dotazy, odešlete příspěvek ve [Fórum MSDN Azure Media Services].
> 
> 

## <a name="a-idissuescurrently-known-issues"></a><a id="issues"/>Aktuálně známé problémy
### <a name="a-idgeneralissuesmedia-services-general-issues"></a><a id="general_issues"/>Obecné problémy Media Services

| Problém | Popis |
| --- | --- |
| Několik běžných hlaviček protokolu HTTP nejsou k dispozici v rozhraní REST API. |Pokud vyvíjíte aplikace služby Media Services pomocí rozhraní REST API, zjistíte, že některé společné pole hlavičky protokolu HTTP (včetně CLIENT-REQUEST-ID, REQUEST-ID a RETURN-CLIENT-REQUEST-ID) nejsou podporovány. Záhlaví bude přidána v budoucí aktualizaci. |
| Procento kódování není povoleno. |Služba Media Services využívá hodnoty vlastnosti IAssetFile.Name při vytváření adres URL pro streamování obsahu (například `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters`). Z tohoto důvodu není povoleno kódování procent. Hodnota vlastnosti Název nesmí obsahovat žádný z následujících [procent kódování – vyhrazené znaky](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Kromě toho může existovat pouze jeden "." pro příponu názvu souboru. |
| Metoda ListBlobs, který je součástí Azure Storage SDK verze 3.x selže. |Služba Media Services generuje na základě adres URL SAS [2012-02-12](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) verze. Pokud chcete k použití sady SDK úložiště pro výpis objektů BLOB v kontejneru objektů blob, použijte [CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) metodu, která je součástí úložiště sady SDK verze 2.x. |
| Media Services omezování mechanismus omezí využití prostředků pro aplikace, které usnadňují nadměrné požadavky na službu. Služba může vrátit – služba není dostupná"503 stavový kód HTTP. |Další informace naleznete v popisu 503 stavového kódu protokolu HTTP v [kódy chyb služby Media Services](media-services-encoding-error-codes.md). |
| Při dotazování entity stanovený limit 1 000 entity se vrátí najednou, protože ostatní veřejné verze 2 omezuje výsledky dotazu do 1 000 výsledky. |Použití přeskočit a převzít (.NET) a hlavních (REST), jak je popsáno v [v tomto příkladu .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) a [v tomto příkladu rozhraní REST API](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Někteří klienti můžou pocházet napříč problém při opakovaném značky v manifestu technologie Smooth Streaming. |Další informace najdete v tématu [v této části](media-services-deliver-content-overview.md#known-issues). |
| Objekty Media Services .NET SDK nelze serializovat a díky tomu nebudou fungovat s mezipamětí Azure pro Redis. |Pokud se pokusíte k serializaci objektu SDK AssetCollection se přidá do mezipaměti Azure pro Redis, je vyvolána výjimka. |


## <a name="a-idrestversionhistoryrest-api-version-history"></a><a id="rest_version_history"/>Historie verzí rozhraní REST API
Informace o historii verzí rozhraní API REST služby Media Services, najdete v článku [Referenční informace k Azure Media Services REST API].

## <a name="october-2018"></a>Říjen 2018

### <a name="cmaf-support"></a>Podpora CMAF

Podpora šifrování CMAF a "cbcs" (iOS 11 +) Apple HLS a MPEG-DASH přehrávačů, které podporují CMAF.

### <a name="web-vtt-thumbnail-sprites"></a>Web VTT miniatur prvky, které budou

Služba Media Services teď můžete generovat webové VTT miniatur prvky, které budou pomocí našich rozhraní API v2. Další informace najdete v tématu [generování miniatur sprite](generate-thumbnail-sprite.md).

## <a name="july-2018"></a>Červenec 2018

V nejnovější verzi služby existují dílčí formátování změny chybové zprávy vrácené službou, pokud se úloha nezdaří, s ohledem na tom, jak ho je rozdělený do dvou nebo více řádků.

## <a name="may-2018"></a>Květen 2018 

Od 12. května 2018, živé kanály budou nadále podporu přenosového streamu RTP/MPEG-2 protokol ingestování. Migrujte prosím z RTP/MPEG-2 na RTMP nebo MP4 s fragmentací (Smooth Streaming) ingestovací protokoly.

## <a name="october-2017-release"></a>Verze z října 2017
> [!IMPORTANT] 
> Služba Media Services je ukončení podpory pro Azure Access Control Service ověřovací klíče. 22. června 2018 můžete už k ověření Media Services back-endu prostřednictvím kódu pomocí klíčů Access Control Service. Je potřeba aktualizovat kód Refaktorovat pro použití služby Azure Active Directory (Azure AD) za [ověřování Azure AD na základě](media-services-use-aad-auth-to-access-ams-api.md). Podívejte se na upozornění o této změně na webu Azure Portal.

### <a name="updates-for-october-2017"></a>Aktualizace. října 2017
#### <a name="sdks"></a>Sady SDK
* Sady .NET SDK byla aktualizována o podporu ověřování Azure AD. Podpora pro ověřování Access Control Service je odebraný z nejnovější sadu .NET SDK na Nuget.org poskytuje rychlejší migrace do služby Azure AD. 
* Sada JAVA SDK byla aktualizována o podporu ověřování Azure AD. Sada Java SDK byla přidaná podpora pro ověřování Azure AD. Informace o tom, jak pomocí služby Media Services Java SDK najdete v tématu [začátek práce s klientskou sadou Java SDK pro Azure Media Services](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Kódování souborů
* Kodér úrovně Premium teď můžete použít ke kódování obsahu videa H.265 vysoce účinné, kódování videa kodek (HEVC). Pokud se rozhodnete H.265 přes jiné kodeky, jako je například H.264 neexistuje žádné zpoplatněné. Informace o HEVC patentové licence najdete v tématu [podmínky Online služeb](https://azure.microsoft.com/support/legal/).
* Zdroj videa, která jsou zakódovány videa kodek H.265 (HEVC), jako jsou videa, které jsou zachyceny pomocí iOS11 nebo GoPro Hero 6 teď můžete kodér úrovně Premium nebo kodér úrovně Standard ke kódování těchto videa. Informace o patentové licence najdete v tématu [podmínky Online služeb](https://azure.microsoft.com/support/legal/).
* Pro obsah, který obsahuje více zvukové stopy jazyka musí být hodnoty jazyka správně označeny podle odpovídající specifikace formátu souboru (například ISO MP4). Pak můžete použít kodér úrovně Standard zakódovat obsah pro streamování. Výsledná Lokátor streamování jsou uvedeny dostupné jazyky zvuku.
* Kodér úrovně Standard teď podporuje dvě nové přednastavení pouze se zvukem systému, "AAC zvuku" a "AAC vhodné kvality zvuku." Obě vytvořit stereo advanced zvuk kódování výstupu (AAC) na přenosové rychlosti 128 kb/s a 192 kb/s, v uvedeném pořadí.
* Kodér úrovně Premium teď podporuje formáty souborů QuickTime/MOV jako vstup. Kodek videa musí být jedna z [Apple ProRes typy uvedené v tomto článku Githubu věnovaném](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats). Zvuk musí být buď AAC nebo pulzní modulace kódu (PCM). Kodér úrovně Premium nepodporuje například DVC/DVCPro video QuickTime/MOV souborů je obalen jako vstup. Kodér úrovně Standard podporuje tyto video kodeků.
* V kodérů byly provedeny následující opravy:

    * Nyní mohou odesílat úlohy s využitím vstupní asset. Po dokončení těchto úloh můžete upravit assetu (například přidat, odstranit nebo přejmenovat soubory v rámci asset) a odesílat další úlohy.
    * Zlepšení kvality JPEG miniatury vytvářených kodér úrovně Standard.
    * Kodér úrovně Standard zpracovává vstupní metadata a lépe ve videích velmi krátká doba trvání generování miniatur.
    * Vylepšení dekodéru H.264 používaných pro kodér úrovně Standard odstranění některých výjimečných artefakty. 

#### <a name="media-analytics"></a>Media Analytics
Obecnou dostupnost služby Azure Media Redactor: tento procesor médií provádí anonymizaci rozmazáním tváří vybraných jedinců a je ideální pro použití v veřejného scénáře a sdělovací. 

Přehled na tento nový procesor, naleznete v tématu [tento příspěvek na blogu](https://azure.microsoft.com/blog/azure-media-redactor/). Informace o dokumentaci a nastaveních najdete v tématu [zákona o svobodném přístupu tváří pomocí Azure Media Analytics](media-services-face-redaction.md).



## <a name="june-2017-release"></a>Verze z června 2017

Media Services teď podporují [ověřování Azure AD na základě](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> V současné době služba Media Services podporuje model ověřování Access Control Service. Ověření služby Řízení přístupu se přestanou používat 1. června 2018. Doporučujeme, abyste na model ověřování Azure AD migrovali co nejdříve.

## <a name="march-2017-release"></a>Verze. března 2017

Teď můžete použít kodér úrovně Standard pro [automaticky generovat přenosových](media-services-autogen-bitrate-ladder-with-mes.md) tak, že zadáte řetězec "Adaptivní streamování" přednastavení, když vytvoříte úlohu kódování. Ke kódování videa pro streamování pomocí služby Media Services, použijte přednastavení "Adaptivní streamování". Chcete-li přizpůsobit kódování předvolby pro váš konkrétní scénář, můžete začít s [Tato přednastavení](media-services-mes-presets-overview.md).

Teď můžete použít kodéru Media Encoder Standard nebo pracovní postup kodéru Media Encoder Premium pro [vytvořit úlohu kódování, která generuje bloky fMP4](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>Vydaná verze. února 2017

Od 1. dubna 2017, libovolný záznam úlohy ve vašem účtu, který je starší než 90 dní automaticky odstraní, spolu s jeho související záznamy. I v případě, že celkový počet záznamů je nižší než maximální kvóta dojde k odstranění. Informace o úlohách/úkolech archivovat, můžete použít kód popsaný v [spravovat prostředky a související entity pomocí sady Media Services .NET SDK](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>Verze. ledna 2017

Koncový bod streamování ve službě Media Services reprezentuje službu streamování, která může doručovat obsah přímo do klientské aplikace přehrávače nebo do sítě pro doručování obsahu (CDN) k další distribuci. Služba Media Services také poskytuje bezproblémovou integraci Azure Content Delivery Network. Výstupní datový proud ze služby StreamingEndpoint může být v živém datovém proudu, video na vyžádání nebo progresivní stahování asset ve vašem účtu Media Services. Každý účet Media Services obsahuje výchozí koncový bod streamování. Další koncové body streamování se dají vytvořit v rámci účtu. 

Existují dvě verze koncové body, streamování 1.0 a 2.0. Od 10. ledna 2017, zahrnují všechny nově vytvořené účty Media Services verze 2.0 výchozí koncový bod streamování. Další koncové body streamování, které přidáte k tomuto účtu jsou také verze 2.0. Tato změna neovlivní existující účty. Stávající koncové body streamování jsou verze 1.0 a je možné upgradovat na verzi 2.0. Existují chování, fakturace a změny funkcí díky této změně. Další informace najdete v [přehledu koncových bodů streamování](media-services-streaming-endpoints-overview.md).

Od verze 2.15, Media Services přidat následující vlastnosti k entitě koncového bodu streamování:

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* StreamingEndpointVersion 

Další informace o těchto vlastnostech najdete v tématu [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>Verzi z prosince 2016

 Nyní můžete Media Services pro přístup k datům telemetrie/metriky pro jeho služeb. Můžete používat aktuální verzi Media Services k shromažďování telemetrických dat pro živý kanál, koncový bod streamování a archivaci entity. Další informace najdete v tématu [Media Services telemetrie](media-services-telemetry-overview.md).

## <a name="a-idjulychanges16july-2016-release"></a><a id="july_changes16"/>Verze z července 2016
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Aktualizace souboru manifestu (*. Správci služeb sítě Internet) generovaných úlohy kódování
Když do kodéru Media Encoder Standard a Media Encoder Premium kategorie se odešle úlohu kódování, vygeneruje úlohu kódování [datových proudů souboru manifestu](media-services-deliver-content-overview.md) (* .ism) v prostředku výstupu. V nejnovější verzi služby byl aktualizován syntaxe tohoto datového proudu souboru manifestu.

> [!NOTE]
> Syntaxe datových proudů souboru manifest (.ism) je rezervovaná pro interní použití. Je v budoucích vydáních může být. Upravit nebo manipulovat s obsahem tohoto souboru.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Nový klient manifest (*. Soubor ISMC) se v prostředku výstupu vygeneruje, když jeden nebo více souborů MP4 výstupy úlohu kódování
Od nejnovější verze služby po dokončení úlohu kódování, který generuje jeden nebo více souborů MP4 výstupního prostředku obsahuje soubor manifestu (*.ismc) streamování klienta. Soubor .ismc pomáhá zlepšit výkon dynamické streamování. 

> [!NOTE]
> Syntaxe soubor manifestu (.ismc) klienta je rezervovaná pro interní použití. Je v budoucích vydáních může být. Upravit nebo manipulovat s obsahem tohoto souboru.
> 
> 

Další informace najdete v tématu [tento blog](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/).

### <a name="known-issues"></a>Známé problémy
Někteří klienti můžou pocházet napříč problém při opakovaném značky v manifestu technologie Smooth Streaming. Další informace najdete v tématu [v této části](media-services-deliver-content-overview.md#known-issues).

## <a id="apr_changes16"></a>Verze. dubna 2016
### <a name="media-analytics"></a>Media Analytics
 Media Services Media Analytics zavedla pro výkonných možností videa. Další informace najdete v tématu [přehled analýz mediálních služeb služeb](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (ve verzi preview)
Media Services teď můžete použít k dynamické šifrování vašeho HTTP Live Streaming (HLS) obsahu technologií Apple FairPlay. Službu doručování licencí Media Services můžete použít také k distribuci licencí FairPlay pro klienty. Další informace najdete v tématu "Používání Azure Media Services k streamování obsahu HLS chráněného technologií Apple FairPlay."

## <a id="feb_changes16"></a>Verze. února 2016
Nejnovější verze služby Media Services SDK pro .NET (3.5.3) obsahuje opravy chyb souvisejících s Google Widevine. Nebylo možné znovu použít AssetDeliveryPolicy pro několik prostředků, které jsou šifrované pomocí Widevine. V rámci této opravy chyb, následující vlastnost byla přidána do sady SDK: WidevineBaseLicenseAcquisitionUrl.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a id="jan_changes_16"></a>Verze. ledna 2016
Abyste snížili nejasnosti s názvy kodér byly přejmenovány jednotek rezervovaných pro kódování.

Basic, Standard a Premium jednotek rezervovaných pro kódování byly přejmenovány na S1, S2, a S3 rezervované jednotky, v uvedeném pořadí. Zákazníci, kteří dnes používají základní jednotky rezervované pro kódování najdete v článku S1 jako popisek na webu Azure Portal (a na faktuře je uvedena). Zákazníci, kteří používají Standard a Premium najdete v článku popisky S2 a S3, v uvedeném pořadí. 

## <a id="dec_changes_15"></a>Verzi z prosince 2015

### <a name="media-encoder-deprecation-announcement"></a>Oznámení o zastarání Media Encoder

 Media Encoder bude zastaralé, počínaje přibližně 12 měsíců od vydání kodéru Media Encoder Standard.

### <a name="azure-sdk-for-php"></a>Sada Azure SDK for PHP
Tým Azure SDK publikované novou verzi sady [sady Azure SDK pro jazyk PHP](http://github.com/Azure/azure-sdk-for-php) balíček, který obsahuje aktualizace a nové funkce pro Media Services. Zejména, Media Services SDK pro jazyk PHP teď podporuje nejnovější [ochrana obsahu](media-services-content-protection-overview.md) funkce. Tyto funkce jsou dynamické šifrování pomocí standardu AES a DRM (PlayReady a Widevine) s a bez omezení tokenu. Také podporuje škálování [jednotky kódování](media-services-dotnet-encoding-units.md).

Další informace naleznete v tématu:

* Následující [ukázky kódu](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) dozvíte, jak rychle začít:
  * **vodworkflow_aes.php**: PHP tento soubor ukazuje, jak používat dynamické šifrování AES-128 a služba doručování klíčů. Je založen na ukázku .NET je vysvětleno v [dynamického šifrování pomocí AES-128 a služba doručování klíčů](media-services-protect-with-aes128.md).
  * **vodworkflow_aes.php**: PHP tento soubor ukazuje způsob použití dynamického šifrování PlayReady a službou doručování licencí. Je založen na ukázku .NET je vysvětleno v [běžného dynamického šifrování pomocí PlayReady nebo Widevine](media-services-protect-with-playready-widevine.md).
  * **scale_encoding_units.php**: PHP tento soubor popisuje postup při škálování jednotek rezervovaných pro kódování.

## <a id="nov_changes_15"></a>Verzi z listopadu 2015
 Media Services teď nabízejí službu doručování licencí Widevine v cloudu. Další informace najdete v tématu [tento blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Viz také [v tomto kurzu](media-services-protect-with-playready-widevine.md) a [úložiště GitHub](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Služeb doručování licencí Widevine poskytované službou Media Services jsou ve verzi preview. Další informace najdete v tématu [tento blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a id="oct_changes_15"></a>Verze. října 2015
Služba Media Services je teď v provozu v následujících datových centrech: Brazílie – Jih, Indie – Západ, Indie – jih a Indie – střed. Teď můžete na webu Azure portal [vytvoření účtů Media Service](media-services-portal-create-account.md) a provádění různých úloh popsaných v [webové stránce dokumentace k Media Services](https://azure.microsoft.com/documentation/services/media-services/). Live Encoding není povolena v těchto datových centrech. Ne všechny typy jednotky rezervované pro kódování nejsou dále, k dispozici v těchto datových centrech.

* Brazílie – jih: Dostupné jsou jenom Standard a Basic jednotky rezervované pro kódování.
* Indie – Západ, Indie – jih a Indie – střed: pouze základní jednotky rezervované pro kódování jsou k dispozici.

## <a id="september_changes_15"></a>Verze. září 2015
Media Services teď nabízejí možnost chránit videa na vyžádání a živé streamování s technologiemi DRM modulární Widevine. Při doručování licencí Widevine můžete použít následující partneři služeb doručování:
* [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) 
* [EZDRM](http://ezdrm.com/) 
* [castLabs](http://castlabs.com/company/partners/azure/) 

Další informace najdete v tématu [tento blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
Konfiguraci zásady AssetDeliveryConfiguration na používání technologie Widevine můžete provést pomocí sady [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (počínaje verzí 3.5.1) nebo rozhraní REST API. 
* Služba Media Services přidali podporu pro Apple ProRes videa. Teď můžete nahrát QuickTime zdrojové soubory videa, které používají Apple ProRes nebo jiných kodeky. Další informace najdete v tématu [tento blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Nyní můžete kodéru Media Encoder Standard provedete klipů tak pro živé extrakce archivu. Další informace najdete v tématu [tento blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Byly provedeny následující aktualizace filtrování: 
  
  * Můžete teď pomocí formátu Apple HLS s filtrem pouze se zvukem. Tuto aktualizaci můžete použít k odebrání pouze se zvukem sledovat tak, že zadáte (jenom zvuk = false) v adrese URL.
  * Když definujete filtry pro vaše prostředky, teď můžete zkombinovat více (až tři) filtry v jedné adresy URL.
    
    Další informace najdete v tématu [tento blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).
* Media Services teď podporují I rámce při HLS verze 4. Můžu snímků podporu optimalizuje operace přechod na libovolný krok a vzad. Ve výchozím nastavení zahrnují všechny výstupy HLS verze 4 stop můžu snímků (EXT-X-I-FRAME-STREAM-INF).
Další informace najdete v tématu [tento blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a id="august_changes_15"></a>Verzi ze srpna 2015
* Media Services SDK pro jazyk Java verze 0.8.0 a nové ukázky jsou teď k dispozici. Další informace naleznete v tématu:
    
* Azure Media Player byl aktualizován s podporou více zvukový datový proud. Další informace najdete v tématu [tento příspěvek na blogu](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a id="july_changes_15"></a>Verze z července 2015
* Byl oznámili všeobecnou dostupnost služby Media Encoder Standard. Další informace najdete v tématu [tento příspěvek na blogu](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Media Encoder Standard používá přednastavení, jak je popsáno v [v této části](https://go.microsoft.com/fwlink/?LinkId=618336). Pokud používáte přednastavený kontext pro 4 kB kóduje, získání typu Premium, vyhrazené jednotky. Další informace najdete v tématu [škálování kódování](media-services-scale-media-processing-overview.md).
* Pomocí Media Services a na Windows Media Player byly použity za provozu v reálném čase titulky. Další informace najdete v tématu [tento příspěvek na blogu](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Aktualizace Media Services .NET SDK
Media Services .NET SDK je teď verze 3.4.0.0. Byly provedeny následující aktualizace: 

* Podpora byla implementována pro živý archív. Nelze stáhnout asset, který obsahuje živý archív.
* Podpora byla implementována pro dynamické filtry.
* Funkce byla implementována tak, aby uživatelé mohli kontejner úložiště při jejich umožňuje odstranit prostředek.
* Opravy chyb byly provedeny související zásady v kanálech opakování.
* Pracovní postup kodéru Media Encoder Premium bylo povoleno.

## <a id="june_changes_15"></a>Verze z června 2015
### <a name="media-services-net-sdk-updates"></a>Aktualizace Media Services .NET SDK
Media Services .NET SDK je teď verze 3.3.0.0. Byly provedeny následující aktualizace: 

* Přidali jsme podporu specifikace zjišťování OpenId Connect.
* Přidala se podpora pro zpracování výměny klíčů na straně zprostředkovatele identity.

Pokud používáte zprostředkovatele identity, která zveřejňuje dokument zjišťování OpenID Connect (jako Azure AD, Googlu a Salesforce provádět), můžete dát pokyn, Media Services získat podpisové klíče pro ověření webových tokenů JSON (Jwt) od specifikace zjišťování OpenID Connect. 

Další informace najdete v tématu [webového klíče JSON pomocí z specifikace zjišťování OpenID Connect pro práci s ověřování tokenů JWT ve službě Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a id="may_changes_15"></a>Verze květen 2015
Následující nové funkce byly oznámili:

* [Živé kódování pomocí Media Services ve verzi preview](media-services-manage-live-encoder-enabled-channels.md)
* [Dynamický manifest](media-services-dynamic-manifest-overview.md)
* [Ve verzi preview procesor médií Azure Media Hyperlapse](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

## <a id="april_changes_15"></a>Vydání duben 2015
### <a name="general-media-services-updates"></a>Aktualizace obecných Media Services
* [Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) jsme představili.
* Počínaje Media Services REST 2.10, kanály, které jsou nakonfigurovány pro ingestování protokolu pro zasílání zpráv v reálném čase (RTMP) se vytvoří s primárním a sekundárním ingestované adresy URL. Další informace najdete v tématu [ingestu kanálu konfigurace](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Azure Media Indexer byl aktualizován.
* Přidala se podpora španělštiny.
* Přidala se nová konfigurace pro formát XML.

Další informace najdete v tématu [tento blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Aktualizace Media Services .NET SDK
Media Services .NET SDK je teď verze 3.2.0.0. Byly provedeny následující aktualizace:

* Zásadní změna: TokenRestrictionTemplate.Issuer a TokenRestrictionTemplate.Audience byly změněny bude typu řetězec.
* Byly provedeny aktualizace vztahující se k vytvoření vlastní zásady.
* Opravy chyb byly provedeny týkajících se nahrávání a stahování souborů.
* Třída MediaServicesCredentials nyní přijímá koncové body ovládací prvek primární a sekundární přístupu k ověřování na základě.

## <a id="march_changes_15"></a>Verze. března 2015
### <a name="general-media-services-updates"></a>Aktualizace obecných Media Services
* Služba Media Services teď poskytuje integraci sítě Content Delivery Network. Pro podporu integrace, byla přidána vlastnost CdnEnabled StreamingEndpoint. CdnEnabled je možné pomocí rozhraní REST API od verze 2.9. Další informace najdete v tématu [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). CdnEnabled jde použít s počínaje verzí 3.1.0.2 sady .NET SDK. Další informace najdete v tématu [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx).
* Pracovní postup kodéru Media Encoder Premium jsme představili. Další informace najdete v tématu [Úvod do Premium encoding v Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a id="february_changes_15"></a>Verze. února 2015
### <a name="general-media-services-updates"></a>Aktualizace obecných Media Services
Rozhraní REST API pro Media Services je teď verze 2.9. Od této verze, můžete povolit integraci sítě Content Delivery Network se koncové body streamování. Další informace najdete v tématu [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a id="january_changes_15"></a>Verze leden 2015
### <a name="general-media-services-updates"></a>Aktualizace obecných Media Services
Byl jsme oznámili obecnou dostupnost služby content protection v případě dynamického šifrování. Další informace najdete v tématu [Media Services zlepšuje zabezpečení datových proudů s obecnou dostupnost technologie DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Aktualizace Media Services .NET SDK
Media Services .NET SDK je teď verze 3.1.0.1.

Tato verze je označena jako výchozí konstruktor Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate jako zastaralé. Typ TokenType nový konstruktor přijímá jako argument.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>Verzi z prosince 2014
### <a name="general-media-services-updates"></a>Aktualizace obecných Media Services
* Některé aktualizace a nové funkce byly přidány do Media Indexer. Další informace najdete v tématu [zpráva k vydání verze 1.1.6.7 Azure Media Indexer](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Nové rozhraní REST API byla přidána, můžete použít k aktualizaci jednotky rezervované pro kódování. Další informace najdete v tématu [EncodingReservedUnitType s využitím REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* Přidala se podpora CORS pro službu doručování klíčů.
* Vylepšení výkonu byly provedeny na dotazy týkající se možnosti zásad autorizace.
* V datovém centru Čína [dodací adresu URL klíče](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) je teď na zákazníka (stejně jako v jiných datových centrech).
* Doba trvání cílové automaticky HLS se přidal. Při živém streamování HLS je vždy zabalený dynamicky. Ve výchozím nastavení Media Services automaticky vypočítá segmentu balení poměr HLS (FragmentsPerSegment), který je na základě intervalu klíčových snímků (KeyFrameInterval). Tato metoda se také označuje jako skupinu obrázků (GOP), který se poslal kodér služby live Encoding. Další informace najdete v tématu [práce pomocí služby Media Services živě Streamovat](https://msdn.microsoft.com/library/azure/dn783466.aspx).

### <a name="media-services-net-sdk-updates"></a>Aktualizace Media Services .NET SDK
[Media Services .NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) je teď verze 3.1.0.0. Byly provedeny následující aktualizace:

* Závislosti sady .NET SDK byl upgradován na rozhraní .NET Framework 4.5.
* Byla přidána nová rozhraní API, které můžete použít k aktualizaci jednotky rezervované pro kódování. Další informace najdete v tématu [aktualizace vyhrazená typ jednotky a jednotky rezervované pro kódování .NET pomocí zvýšení](media-services-dotnet-encoding-units.md).
* Přidala se podpora JWT pro ověřování pomocí tokenu. Další informace najdete v tématu [ověření tokenu JWT v Media Services a dynamického šifrování](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* Relativní posunutí pro BeginDate a ExpirationDate v šabloně licence PlayReady byly přidány.

## <a id="november_changes_14"></a>Verzi z listopadu 2014
* Media Services teď můžete použít k ingestujte živý obsah Smooth Streaming (fMP4) připojení přes protokol SSL. K ingestování přes protokol SSL, nezapomeňte aktualizovat adresu URL ingestování na protokol HTTPS. Služba Media Services v současné době nepodporuje SSL s použitím vlastních domén. Další informace o živém streamování najdete v tématu [pracovat s Azure Media Services Live Streaming](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* V současné době nelze ingestovat živý stream RTMP připojení přes protokol SSL.
* Pouze v případě, že po 10. září 2014 byl vytvořen koncový bod streamování, ze kterého můžete doručovat obsah můžete Streamovat přes protokol SSL. Pokud vaše adresy URL pro streamování jsou založené na koncových bodech streamování vytvořené po 10. září 2014, adresa URL obsahuje "streaming.mediaservices.windows.net" (nový formát). Adresy URL pro streamování, které obsahují "origin.mediaservices.windows.net" (starý formát) nepodporují SSL. Pokud vaše adresa URL používá starý formát a chcete Streamovat přes protokol SSL, [vytvořit nový koncový bod streamování](media-services-portal-manage-streaming-endpoints.md). Streamovat obsah pomocí protokolu SSL, pomocí adresy URL na základě nového koncového bodu streamování.

## <a id="october_changes_14"></a>Verze. října 2014
### <a id="new_encoder_release"></a>Kodér Media Services verze
 Jsme představili novou verzi sady médií služby Azure Media Encoderu. Pomocí nejnovější kodéru Media Encoder, platíte jenom za výstupní GB. V opačném případě nový kodér je funkce, které jsou kompatibilní s předchozím kodér. Další informace najdete v tématu [podrobnosti o cenách za Media Services].

### <a id="oct_sdk"></a>Media Services .NET SDK
Media Services SDK pro .NET rozšíření je teď verze 2.0.0.3.

Media Services SDK pro .NET je teď verze 3.0.0.8. Byly provedeny následující aktualizace:

* Refaktoring bylo implementováno v třídy zásady opakování.
* Řetězec uživatelského agenta byl přidán do hlavičky požadavků HTTP.
* Byl přidán krok sestavení obnovení NuGet.
* Scénář testy, které jsme opravili používat x509 certifikát z úložiště.
* Nastavení ověření byly přidány pro aktualizace kanálu a datových proudů end.

### <a name="new-github-repository-to-host-media-services-samples"></a>Nové úložiště GitHub na ukázky hostitele služby Media Services
Ukázky jsou v [úložišti GitHub s ukázkami Media Services](https://github.com/Azure/Azure-Media-Services-Samples).

## <a id="september_changes_14"></a>Verze. září 2014
Metadata Media Services REST jsou teď verze 2.7. Další informace o nejnovějších aktualizacích REST, najdete v článku [referenční dokumentace rozhraní API REST služby Media Services](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Media Services SDK pro .NET je teď verze 3.0.0.7

### <a id="sept_14_breaking_changes"></a>Rozbíjející změny v
* Počátek se přejmenoval na [StreamingEndpoint].
* Byla provedena změna výchozího chování při použití na webu Azure portal má být zakódován a potom publikovat soubory MP4.

### <a id="sept_14_GA_changes"></a>Nové funkce/scénáře, které jsou součástí všeobecně dostupné verze
* Procesor médií Media Indexer byl zaveden. Další informace najdete v tématu [indexování mediálních souborů pomocí Media Indexer](https://msdn.microsoft.com/library/azure/dn783455.aspx).
* Můžete použít [StreamingEndpoint] entitu, kterou chcete přidat názvy vlastních domén (hostitel).
  
    Jako název koncového bodu streamování služby Media Services použít vlastní název domény, přidejte názvy vlastního hostitele pro koncový bod streamování. Pomocí .NET SDK nebo rozhraní REST API služby Media Services můžete přidat vlastní hostitele.
  
    Platí následující aspekty:
  
  * Musíte mít vlastnictví vlastního názvu domény.
  * Vlastní název domény musí být ověřeny službou Media Services. Pro ověření domény, vytvořte záznam CName mapující MediaServicesAccountId nadřazenou doménu ověřit mediaservices-dns zónu DNS.
  * Musíte vytvořit jiný záznam CName, který mapuje název vlastního hostitele (například sports.contoso.com) na médiu služby StreamingEndpoint název hostitele (například amstest.streaming.mediaservices.windows.net).

    Další informace najdete v tématu CustomHostNames vlastnost [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx) článku.

### <a id="sept_14_preview_changes"></a>Nové funkce a scénáře, které jsou součástí verze public preview
* Živé streamování ve verzi preview. Další informace najdete v tématu [práce pomocí služby Media Services živě Streamovat](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* Služba doručování klíčů. Další informace najdete v tématu [dynamického šifrování pomocí AES-128 a služba doručování klíčů](https://msdn.microsoft.com/library/azure/dn783457.aspx).
* Dynamické šifrování AES. Další informace najdete v tématu [dynamického šifrování pomocí AES-128 a služba doručování klíčů](https://msdn.microsoft.com/library/azure/dn783457.aspx).
* Službu doručování licencí PlayReady. 
* Dynamického šifrování PlayReady. 
* Šablona licencování Media Services PlayReady. Další informace najdete v tématu [Přehled šablon licencování Media Services PlayReady].
* Prostředky úložiště šifrované Stream. Další informace najdete v tématu [Stream obsahu zašifrovaného úložiště](https://msdn.microsoft.com/library/azure/dn783451.aspx).

## <a id="august_changes_14"></a>Verze. srpna 2014
Při kódování prostředku výstupního prostředku je vytvořen po dokončení úlohy kódování. Až do této verze kodér Media Services vytváří metadata o výstupní assety. Od této verze, kodér vytvoří metadata o vstupní prostředky. Další informace najdete v tématu [vstupní metadata] a [Výstupní metadata].

## <a id="july_changes_14"></a>Verze. července 2014
Pro Azure Media Services Packageru a Encryptoru byly provedeny následující opravy:

* Když k assetu živý archív se přenášejí na protokol HLS, přehrává zvuk pouze: Tento problém byl vyřešen, a teď můžete přehrát zvuk a video.
* Pokud prostředek je zabalená do HLS a AES 128-bit šifrování obálky, zabalené datové proudy není přehrávání na zařízeních s Androidem: Tato chyba byla opravena a zabalené stream přehrávání na zařízeních s Androidem, které podporují HLS.

## <a id="may_changes_14"></a>Verze. května 2014
### <a id="may_14_changes"></a>Aktualizace obecných Media Services
Teď můžete použít [dynamické balení] do datového proudu HLS verze 3. Datový proud HLS verze 3, přidat do cesty pro Lokátor původu v následujícím formátu: * .ism/manifest(format=m3u8-aapl-v3). Další informace najdete v tématu [Toto fórum](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3).

Dynamické balení nyní také podporuje doručování HLS (verze 3 a verze 4) se šifrováním pomocí technologie PlayReady podle technologie Smooth Streaming staticky zašifrovaný pomocí technologie PlayReady. Informace o tom, jak šifrování, technologie Smooth Streaming pomocí technologie PlayReady, naleznete v tématu [chránit technologie Smooth Streaming pomocí technologie PlayReady](https://msdn.microsoft.com/library/azure/dn189154.aspx).

### <a name="may_14_donnet_changes"></a>Aktualizace Media Services .NET SDK
Media Services .NET SDK je teď verze 3.0.0.5. Byly provedeny následující aktualizace:

* Při nahrávání a stahování mediálních materiálů, je lepší rychlosti a odolnosti.
* Vylepšili jsme ve zpracování výjimek logiku a přechodným opakování: 
  
  * Výjimky, které jsou způsobeny, když dotaz, uložte změny a odeslání nebo stažení souborů byly vylepšeny logiky přechodná chyba zjišťování a zkuste to znovu. 
  * Pokud se zobrazí web výjimky (například během token požadavku Access Control Service), závažné chyby nezdaří rychleji nyní.

Další informace najdete v tématu [Logika opakování v sadě Media Services SDK pro .NET].

## <a id="april_changes_14"></a>Kodér vydání duben 2014
### <a name="april_14_enocer_changes"></a>Kodér Media Services aktualizace
* Přidala se podpora pro ingestování AVI, soubory, které jsou vytvořeny pomocí editoru nelineárních EDIUS travní Valley. V tomto procesu video lehce komprimovaný pomocí tráva Valley HQ a HQX kodek. Další informace najdete v tématu [Tráva Valley oznamuje EDIUS 7 vysílání datového proudu prostřednictvím cloudu].
*  Chcete-li určit zásady vytváření názvů pro soubory vytvořené jazykem kodér Media Services přidala se podpora. Další informace najdete v tématu [kodér Media Services ovládací prvek výstupní názvy souborů](https://msdn.microsoft.com/library/azure/dn303341.aspx).
*  Byla přidána podpora pro videa nebo zvukový překrytí. Další informace najdete v tématu [vytvořit překrytí](https://msdn.microsoft.com/library/azure/dn640496.aspx).
*  Přidala se podpora Pokud chcete spojit dohromady více segmentů videa. Další informace najdete v tématu [spojit videa segmenty](https://msdn.microsoft.com/library/azure/dn640504.aspx).
* Chyba byla opravena, který se vztahuje k překódování soubory MP4 rychlostmi kde zvuk kódovaný pomocí MPEG-1 zvuku vrstvy 3 (označované také jako MP3).

## <a id="jan_feb_changes_14"></a>Uvolní ledna/únor 2014
### <a name="jan_fab_14_donnet_changes"></a>Media Services .NET SDK 3.0.0.1, 3.0.0.2 a 3.0.0.3
Změny v 3.0.0.1 a 3.0.0.2 patří:

* Které jsme opravili problémy týkající se využití dotazů LINQ s příkazy OrderBy.
* Testování řešení v [GitHub] rozděleny do jednotkové testy a testy založené na scénářích.

Další informace o změnách najdete v tématu [Media Services .NET SDK 3.0.0.1 a 3.0.0.2 uvolní](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html).

Ve verzi 3.0.0.3 byly provedeny následující změny:

* Chcete-li použít verzi 3.0.3.0 se upgradovala závislosti Azure storage.
* Zpětná kompatibilita problém byl vyřešen 3.0. *.* uvolní.

## <a id="december_changes_13"></a>Verze 2013 dne
### <a name="dec_13_donnet_changes"></a>Media Services .NET SDK 3.0.0.0
> [!NOTE]
> Verze 3.0.x.x nejsou zpětně kompatibilní s verzemi 2.4.x.x.
> 
> 

Nejnovější verzi sady Media Services SDK je teď 3.0.0.0. Můžete stáhnout z NuGet nejnovější balíček nebo získat bitů z [GitHub].

Počínaje verzí 3.0.0.0 Media Services SDK, můžete opakovaně použít [Azure AD Access Control Service](https://msdn.microsoft.com/library/hh147631.aspx) tokeny. Další informace najdete v části "Opětovné použití Access Control Service tokeny" v [připojení ke službám Media Services pomocí sady Media Services SDK pro .NET](https://msdn.microsoft.com/library/azure/jj129571.aspx).

### <a name="dec_13_donnet_ext_changes"></a>Media Services .NET SDK rozšíření 2.0.0.0
 Media Services .NET SDK rozšíření představují sadu metod rozšíření a pomocných funkcí, které zjednoduší kódování a usnadňují vývoj pomocí služby Media Services. Můžete získat nejnovější součásti z [Media Services .NET SDK rozšíření](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev).

## <a id="november_changes_13"></a>Verzi z listopadu 2013
### <a name="nov_13_donnet_changes"></a>Změny Media Services .NET SDK
Od této verze, Media Services SDK pro .NET popisovače přechodných chyb chyby, které mohou nastat při volání REST API služby Media Services vrstvu.

## <a id="august_changes_13"></a>Verzi ze srpna 2013
### <a name="aug_13_powershell_changes"></a>Rutiny Powershellu služby Media součástí sady Azure SDK tools
Následující rutiny Powershellu Media Services jsou teď součástí [sady Azure SDK tools](https://github.com/Azure/azure-sdk-tools):

* Get-AzureMediaServices 

    Příklad: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Příklad: `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`
* New-AzureMediaServicesKey 
  
    Příklad: `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Příklad: `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`

## <a id="june_changes_13"></a>Verze z června 2013
### <a name="june_13_general_changes"></a>Změny služby Media Services
Následující změny uvedené v této části jsou zahrnuté v červnu 2013 Media Services verze aktualizace:

* Umožňuje propojit více účtů úložiště pro účet služby Media Services. 
    * StorageAccount
    * Asset.StorageAccountName a Asset.StorageAccount
* Možnost aktualizovat Job.Priority. 
* Oznámení související entity a vlastnosti: 
    * JobNotificationSubscription
    * Elementu NotificationEndPoint
    * Úloha
* Asset.Uri 
* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Změny Media Services .NET SDK
Tyto změny jsou zahrnuty v červnu 2013 Media Services SDK verze. Nejnovější sadu Media Services SDK je k dispozici na Githubu.

* Od verze 2.3.0.0, Media Services SDK podporuje propojování více úložiště účtů k účtu Media Services. Tuto funkci podporovat následující rozhraní API:
  
    * Typ IStorageAccount
    * Vlastnost Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts
    * Účet úložiště vlastností
    * Vlastnost StorageAccountName
  
    Další informace najdete v tématu [Media Services spravovat prostředky ve více účtech úložiště](https://msdn.microsoft.com/library/azure/dn271889.aspx).
* Rozhraní API související s oznámení. Počínaje verzí 2.2.0.0, může naslouchat oznámení Azure Queue storage. Další informace najdete v tématu [zpracování Media Services úlohy oznámení](https://msdn.microsoft.com/library/azure/dn261241.aspx).
  
    * Vlastnost Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions
    * Typ Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint
    * Typ Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription
    * Typ Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection
    * Typ Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType
* Závislost na klienta úložiště SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll)
* Závislost na protokolu OData 5.5 (Microsoft.Data.OData.dll)

## <a id="december_changes_12"></a>Verzi z prosince 2012
### <a name="dec_12_dotnet_changes"></a>Změny Media Services .NET SDK
* IntelliSense: Chybí IntelliSense dokumentace byla přidána pro mnoho typů.
* Microsoft.Practices.TransientFaultHandling.Core: Byla chyba opravena kde SDK stále má závislost na starou verzi tohoto sestavení. Sada SDK nyní odkazuje na 5.1.1209.1 verzi tohoto sestavení.

Řeší problémy zjištěné v listopadu 2012 SDK:

* IAsset.Locators.Count: Tento počet je nyní správně hlášené na nová rozhraní IAsset po odstranění se všechny lokátory.
* IAssetFile.ContentFileSize: Toto je nyní správně nastavit hodnotu po nahrání IAssetFile.Upload(filepath).
* IAssetFile.ContentFileSize: Tuto vlastnost můžete nyní nastavit při vytváření souboru prostředků. To byla dříve jen pro čtení.
* IAssetFile.Upload(filepath): Byla chyba opravena kde byla tato metoda synchronní nahrávání vyvolání následující chybu při více soubory byly odeslány do assetu. Došlo k chybě "serveru se nepodařilo ověřit žádost. Ujistěte se, že hodnota hlavičky autorizace je vytvořen správně včetně podpis. "
* IAssetFile.UploadAsync: Byla chyba opravena, který omezený souběžná nahrávání souborů, které mají pět souborů.
* IAssetFile.UploadProgressChanged: Tato událost se teď poskytují prostřednictvím sady SDK.
* IAssetFile.DownloadAsync (string, BlobTransferClient ILocator, CancellationToken): Toto přetížení metody je nyní k dispozici.
* IAssetFile.DownloadAsync: Byla chyba opravena, který omezený souběžných stahování souborů, které mají pět souborů.
* IAssetFile.Delete(): Byla chyba opravena kde volání delete může vyvolat výjimku, pokud žádný soubor byl nahrán pro IAssetFile.
* Úlohy: Byla chyba opravena kde řetězení "Obsah ve formátu MP4 do funkce Smooth Streams úlohy" s "Ochrany úlohami PlayReady" pomocí šablony projektu nevytvořili žádné úlohy vůbec.
* EncryptionUtils.GetCertificateFromStore(): Tato metoda už vyvolá výjimka nulového odkazu, protože došlo k chybě při hledání certifikát založený na problémy s konfigurací certifikátu.

## <a id="november_changes_12"></a>Listopad 2012 release
Změny uvedené v této části jsou zahrnuté v listopadu 2012 (verze 2.0.0.0) aktualizace sady SDK. Tyto změny mohou vyžadovat libovolný kód napsaný pro verzi sady SDK upravena nebo přepsán ve verzi preview z června 2012.

* Prostředky
  
    * Je IAsset.Create(assetName) *pouze* funkce vytváření prostředků. IAsset.Create už nahraje soubory jako součást volání metody. Použití IAssetFile pro nahrávání.
    * Metoda IAsset.Publish a hodnota výčtu AssetState.Publish byly odebrány ze sady SDK služby. Veškerý kód, který závisí na této hodnotě musí být přepsán.
* FileInfo
  
    * Tato třída byl odebrán a nahrazen IAssetFile.
  
* IAssetFiles
  
    * IAssetFile nahradí FileInfo a má různé chování. Ho Pokud chcete použít, vytvořte instanci objektu IAssetFiles, za nímž následuje nahrávání souborů pomocí sady Media Services SDK nebo sady SDK služby Storage. Je možné použít následující IAssetFile.Upload přetížení:
  
        * IAssetFile.Upload(filePath): Této synchronní metody blokuje vlákno a My ho doporučujeme pouze v případě, že můžete nahrát jeden soubor.
        * IAssetFile.UploadAsync (CestakSouboru, blobTransferClient, Lokátor, cancellationToken): Tato asynchronní metoda je mechanismus upřednostňované nahrávání. 
    
            Známý problém: Pokud používáte token zrušení, se zruší nahrávání. Úlohy může mít mnoho stavů zrušení. Musí správně zachytila a zpracovala výjimky.
* Lokátory
  
    * Specifické pro původ verze byly odebrány. Kontext, který specifické pro SAS. Zastaralé nebo odebrané podle všeobecné dostupnosti budou označeny Locators.CreateSasLocator (asset, accessPolicy). V části "Lokátory" v části "Nové funkce" aktualizované chování.

## <a id="june_changes_12"></a>Ve verzi preview z června 2012
Následující funkce byla v listopadu verzi sady SDK:

* Odstranění entit
  
    * Odstraní IAsset IAssetFile, ILocator, IAccessPolicy a IContentKey objekty na úrovni objektu, tedy IObject.Delete(), nemusíte mít delete v kolekci, tedy cloudMediaContext.ObjCollection.Delete(objInstance) se nyní.
* Lokátory
  
    * Lokátory teď musí vytvořit pomocí metody CreateLocator. Hodnoty výčtu LocatorType.SAS nebo LocatorType.OnDemandOrigin musí použít jako argument pro konkrétní typ lokátoru, že který chcete vytvořit.
    * Byly přidány nové vlastnosti pro lokátory, aby bylo snazší získat použitelné identifikátory URI pro váš obsah. Tato změna návrhu lokátory poskytuje větší flexibilitu pro budoucí rozšíření třetích stran a zvyšuje díky jednoduchosti použití pro média klientské aplikace.
* Asynchronní metoda podpory
  
    * Přidala se asynchronní podpora pro všechny metody.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Fórum MSDN Azure Media Services]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Referenční informace k Azure Media Services REST API]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[Podrobnosti o cenách za Media Services]: http://azure.microsoft.com/pricing/details/media-services/
[Vstupní metadata]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Výstupní metadata]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Deliver content]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Index media files with the Azure Media Indexer]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Work with Media Services live streaming]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Use AES-128 dynamic encryption and the key delivery service]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Use PlayReady dynamic encryption and the license delivery service]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Přehled šablon licencování Media Services PlayReady]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Stream storage-encrypted content]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://portal.azure.com
[Dynamické balení]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Logika opakování v sadě Media Services SDK pro .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Tráva Valley oznamuje EDIUS 7 vysílání datového proudu prostřednictvím cloudu]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Create overlays]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Stitch video segments]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: http://msdn.microsoft.com/library/hh147631.aspx
[Connect to Media Services with the Media Services SDK for .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Handle Media Services job notifications]: http://msdn.microsoft.com/library/azure/dn261241.aspx

