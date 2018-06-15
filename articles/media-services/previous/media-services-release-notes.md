---
title: Poznámky k verzi Media Services | Microsoft Docs
description: Poznámky k verzi Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 3ca2d7af-1cf0-45fa-9585-3b73f3ee057d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 10/18/2017
ms.author: juliako
ms.openlocfilehash: 62f5ae4a9152d3f1b1641cc69136bf472527ba2a
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942407"
---
# <a name="azure-media-services-release-notes"></a>Poznámky k verzi Azure Media Services
Tyto poznámky k verzi pro Azure Media Services shrnout změny z předchozích verzí a známé problémy.

> [!NOTE]
> Chceme slyšet od našich zákazníků, takže zaměříme na řešení problémů, které vám. Chcete-li nahlásit problém nebo klást otázky, odeslat příspěvek v [MSDN fóru služby Azure Media Services].
> 
> 

## <a name="a-idissuescurrently-known-issues"></a><a id="issues"/>Aktuálně známé problémy
### <a name="a-idgeneralissuesmedia-services-general-issues"></a><a id="general_issues"/>Obecné problémy Media Services

| Problém | Popis |
| --- | --- |
| Několik běžných hlaviček protokolu HTTP nejsou k dispozici v rozhraní REST API. |Pokud vyvíjíte aplikace Media Services pomocí rozhraní REST API, zjistíte, že některé běžné pole hlavičky protokolu HTTP (včetně CLIENT-REQUEST-ID, ID žádosti a vrátit-CLIENT-REQUEST-ID) nejsou podporovány. Hlavičky bude přidána v budoucí aktualizaci. |
| Kódování v procentech není povoleno. |Služba Media Services použije hodnotu vlastnosti IAssetFile.Name při sestavování adresy URL pro streamování obsah (například http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters). Z tohoto důvodu není povoleno kódování v procentech. Hodnota vlastnosti Název nemůže mít žádné z následujících [procent kódování vyhrazené znaky](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Navíc může být jen jedna "." pro příponu názvu souboru. |
| ListBlobs metoda, která je součástí selže 3.x verze SDK úložiště Azure. |Služba Media Services vygeneruje SAS adresy URL na základě [2012-02-12](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) verze. Pokud chcete použít sady SDK úložiště do seznamu objektů BLOB v kontejneru objektů blob, použijte [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) metoda, která je součástí verze sady SDK úložiště 2.x. |
| Media Services omezení mechanismus omezuje využití prostředků pro aplikace, které provést nadměrné požadavky na službu. Služba může vrátit "Služba není k dispozici" 503 stavový kód HTTP. |Další informace najdete v tématu Popis 503 stavový kód HTTP v [kódy chyb Media Services](media-services-encoding-error-codes.md). |
| Když dotazujete entity, omezení 1 000 entit se vrátí najednou, protože veřejné REST verze 2 omezí výsledky dotazu a 1000 výsledky. |Pomocí přeskočit a trvat (.NET) / top (REST), jak je popsáno v [v tomto příkladu .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) a [v tomto příkladu REST API](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Někteří klienti můžete setkat při opakovaném značky problém v manifestu technologie Smooth Streaming. |Další informace najdete v tématu [v této části](media-services-deliver-content-overview.md#known-issues). |
| Objekty sady Media Services .NET SDK nelze serializovat a v důsledku nefungují s Azure Redis Cache. |Pokud se pokusíte serializovat objekt SDK AssetCollection, který chcete přidat do Azure Redis Cache, je vyvolána výjimka. |


## <a name="a-idrestversionhistoryrest-api-version-history"></a><a id="rest_version_history"/>Historie verzí rozhraní REST API
Informace o historii verze Media Services REST API najdete v tématu [REST API služby Azure Media Services odkaz].

## <a name="may-2018"></a>2018 může 

Od 12 může 2018 za provozu kanály se už podporu datový proud RTP/MPEG-2 transport ingestování. Proveďte prosím migraci z RTP/MPEG-2 RTMP nebo fragmentovaných MP4 (technologie Smooth Streaming) ingestování protokoly.

## <a name="october-2017-release"></a>Říjen 2017 verze
> [!IMPORTANT] 
> Služba Media Services je místo začne podporu služby Řízení přístupu Azure ověřovací klíče. Na 22. června 2018 můžete už ověřit pomocí back end Media Services prostřednictvím kódu s použitím klíče služby Řízení přístupu. Je nutné aktualizovat vaše kódu pro použití služby Azure Active Directory (Azure AD) za [Azure ověřování na základě AD](media-services-use-aad-auth-to-access-ams-api.md). Podívejte se na pro upozornění o této změně na portálu Azure.

### <a name="updates-for-october-2017"></a>Aktualizace pro říjen 2017
#### <a name="sdks"></a>Sady SDK
* .NET SDK byl aktualizován pro podporu ověřování Azure AD. Podpora pro ověřování služby Řízení přístupu je odebraný z nejnovější sady .NET SDK v Nuget.org podporovat rychlejší migrace do služby Azure AD. 
* Sady JAVA SDK byl aktualizován pro podporu ověřování Azure AD. Podpora pro ověřování Azure AD byl přidán do sady Java SDK. Informace o tom, jak pomocí sady Java SDK služby Media Services najdete v tématu [Začínáme s klientem nástroje Java SDK pro Azure Media Services](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Na základě souborů kódování
* Teď můžete použít kodér úrovně Premium zakódovat svůj obsah na video vysoce účinné H.265 kódování kodek videa (HEVC). Pokud se rozhodnete H.265 přes jiné kodeky, jako je například H.264 neexistuje žádný cenovou vliv. Informace o licencích patentová HEVC najdete v tématu [Online služby podmínky](https://azure.microsoft.com/support/legal/).
* Pro zdroj video, které je zakódovaných pomocí kodek videa H.265 (HEVC), jako je například video zachyceny pomocí iOS11 nebo GoPro nejdůležitější 6 teď můžete kodér úrovně Premium nebo standardní kodér ke kódování těchto videa. Informace o licencích patentová najdete v tématu [Online služby podmínky](https://azure.microsoft.com/support/legal/).
* Pro obsah, který obsahuje více jazyk zvukových stop musí být hodnoty jazyk správně označeny podle odpovídající specifikaci formátu souboru (například ISO MP4). Pak můžete použít standardní kodér ke kódování obsahu pro streamování. Výsledná Lokátor streamování jsou uvedeny dostupné audio jazyky.
* Standardní kodér teď podporuje dva nové přednastavení systému jen zvuk, "AAC zvuk" a "AAC dobrý kvalitu zvuku." Jak vytvořit stereofonním systémem advanced zvuk kódování výstupu (AAC) v přenosové rychlosti 128 kb/s a 192 kb/s, v uvedeném pořadí.
* Kodér úrovně Premium teď podporuje formáty souborů MOV QuickTime nebo jako vstup. Kodek videa musí mít jednu z [Apple ProRes typy uvedené v tomto článku Githubu](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats). Zvukovém souboru musí být buď AAC nebo pulzní Modulační kódu (PCM). Kodér Premium nepodporuje například DVC/DVCPro video uzavřen do soubory QuickTime/MOV jako vstup. Standardní kodér podporuje tyto kodeky videa.
* V kodéry byly provedeny následující opravy chyb:

    * Teď můžete odeslat úlohy pomocí vstupní prostředek. Po dokončení těchto úloh můžete upravit asset (například přidat, odstranit nebo přejmenovat soubory v rámci asset) a odesílání dalších úloh.
    * Zlepšení kvality JPEG miniatur vyprodukované kodér úrovně Standard.
    * Standardní Kodér zpracovává vstupní metadata a miniatur generování lepší ve velmi krátká doba trvání videa.
    * Vylepšení H.264 dekodér použít ve standardní kodér eliminovat určité výjimečných artefakty. 

#### <a name="media-analytics"></a>Media Analytics
Obecné dostupnosti Redactor média Azure: tento procesor médií provádí anonymization, a to stírá řezy vybrané osob a je ideální pro použití v veřejné scénáře zabezpečení a média zprávy. 

Přehled na tuto novou procesoru, najdete v tématu [tomto příspěvku na blogu](https://azure.microsoft.com/blog/azure-media-redactor/). Informace v dokumentaci a nastavení najdete v tématu [redigovat řezy s Azure Media Analytics](media-services-face-redaction.md).



## <a name="june-2017-release"></a>2017 června verze

Služba Media Services nyní podporuje [Azure ověřování na základě AD](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> V současné době Media Services podporuje model ověřování služby Řízení přístupu. Ověření služby Řízení přístupu bude na 1. června 2018 zastaralá. Doporučujeme, abyste na model ověřování Azure AD migrovali co nejdříve.

## <a name="march-2017-release"></a>2017 března verze

Teď můžete použít standardní kodéru pro [automaticky generovat žebříku přenosovou rychlostí](media-services-autogen-bitrate-ladder-with-mes.md) zadáním "Adaptivní streamování" přednastavení řetězec při vytvoření kódování úlohy. Ke kódování video pro streamování pomocí služby Media Services, použijte přednastavení "Adaptivní datové proudy". Chcete-li přizpůsobit pro konkrétní scénář přednastavení kódování, můžete začít s [Tato přednastavení](media-services-mes-presets-overview.md).

Teď můžete použít Media Encoder Standard nebo Media Encoder Premium pracovní postup [vytvořit kódování úkol, který generuje fMP4 bloky](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>2017 února verze

Od 1. dubna 2017 záznam všechny úlohy ve vašem účtu, který je starší než 90 dní automaticky odstraní, společně s jeho záznamy související úlohy. Odstranění dojde i v případě, že celkový počet záznamů je nižší než maximální kvótu. K archivaci informace úloh, můžete použít kód popsané v [spravovat prostředky a entit v relaci pomocí .NET SDK služby Media Services](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>Ledna 2017 verze

Ve službě Media Services představuje koncový bod streamování streamování služba, která může poskytnout obsah přímo do klientské aplikace player nebo k síti pro doručování obsahu (CDN) pro další distribuci. Služba Media Services také poskytuje bezproblémovou integraci Azure Content Delivery Network. Výstupní datový proud z StreamingEndpoint služba může být živý datový proud, video na vyžádání nebo progresivní stahování asset ve vašem účtu Media Services. Každý účet Media Services obsahuje výchozí koncový bod streamování. V rámci účtu se dají vytvořit další koncové body streamování. 

Existují dvě verze koncové body, streamování 1.0 a 2.0. Od 10. ledna 2017, zahrnují všechny nově vytvořené účty služby Media Services verze 2.0 výchozí koncový bod streamování. Další streamování koncové body, které přidáte k tomuto účtu jsou také verze 2.0. Tato změna nemá vliv existujících účtů. Stávající koncové body streamování jsou verze 1.0 a lze upgradovat na verzi 2.0. Existují chování, fakturace a změny funkce v této změně. Další informace najdete v [přehledu koncových bodů streamování](media-services-streaming-endpoints-overview.md).

Počínaje verzí 2.15, Media Services přidána následující vlastnosti streamování entitě koncový bod:

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* StreamingEndpointVersion 

Další informace o těchto vlastnostech najdete v tématu [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>Release – prosinec 2016

 Nyní můžete Media Services pro přístup k datům telemetrie/metriky pro jeho služby. Můžete použít aktuální verzi Media Services k shromažďování telemetrických dat pro živého kanálu koncový bod streamování a archivaci entity. Další informace najdete v tématu [Media Services telemetrie](media-services-telemetry-overview.md).

## <a name="a-idjulychanges16july-2016-release"></a><a id="july_changes16"/>Verze července 2016
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Aktualizace souboru manifestu (*. ISM) generované úlohy kódování
Při odeslání kódování úloh pro Media Encoder Standard nebo Premium kodér médií, generuje úlohu kódování [streamování souboru manifestu](media-services-deliver-content-overview.md) (* .ism) v výstupní asset. Pomocí nejnovější verze služby byl aktualizován syntaxe tohoto streamování souboru manifestu.

> [!NOTE]
> Syntaxe streamování soubor manifestu (.ism) je vyhrazená pro interní použití. Je může v budoucích verzích změnit. Změnit nebo upravit obsah tohoto souboru.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Nový klient manifest (*. Soubor ISMC) se generuje ve výstupní asset při kódování úloh výstupy jeden nebo více souborů MP4
Počínaje nejnovější verze služby po dokončení kódování úloh, který generuje jeden nebo více souborů MP4, výstupní asset obsahuje soubor manifestu (*.ismc) streamování klienta. Soubor .ismc pomáhá zlepšit výkon dynamické streamování. 

> [!NOTE]
> Syntaxe souboru manifestu (.ismc) klienta je vyhrazená pro interní použití. Je může v budoucích verzích změnit. Změnit nebo upravit obsah tohoto souboru.
> 
> 

Další informace najdete v tématu [tomto blogu](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/).

### <a name="known-issues"></a>Známé problémy
Někteří klienti můžete setkat při opakovaném značky problém v manifestu technologie Smooth Streaming. Další informace najdete v tématu [v této části](media-services-deliver-content-overview.md#known-issues).

## <a id="apr_changes16"></a>Verze. dubna 2016
### <a name="media-analytics"></a>Media Analytics
 Služba Media Services zavedli Media Analytics výkonné video intelligence. Další informace najdete v tématu [Media Services Analytics přehled](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (preview)
Nyní můžete Media Services dynamicky šifrovat vaší HTTP Live Streaming (HLS) obsahu s Apple FairPlay. Službu doručování licencí Media Services můžete použít také k poskytování licence FairPlay klientům. Další informace najdete v části "Použití Azure Media Services Streamovat HLS obsah chráněný pomocí Apple FairPlay."

## <a id="feb_changes16"></a>Verze. února 2016
Nejnovější verzi sady Media Services SDK pro platformu .NET (3.5.3) obsahuje oprava chyb souvisejících s Google Widevine. Nebylo možné znovu použít AssetDeliveryPolicy pro více prostředků, které jsou šifrované pomocí Widevine. V rámci této opravy chyb následující vlastnost byla přidána do sady SDK: WidevineBaseLicenseAcquisitionUrl.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a id="jan_changes_16"></a>Release – leden 2016
Jednotky rezervované pro kódování byly přejmenovány ke snížení záměně se kodér názvy.

Basic, Standard a Premium kódování vyhrazené jednotky byly přejmenovány na S1, S2, a jednotky rezervované pro S3, v uvedeném pořadí. Zákazníci, kteří používají základní kódování jednotek rezervovaných Dnes najdete S1 jako štítek na portálu Azure (a v kusovníku). Zákazníci, kteří používají Standard a Premium najdete v části štítky S2 a S3, v uvedeném pořadí. 

## <a id="dec_changes_15"></a>Release – prosinec 2015

### <a name="media-encoder-deprecation-announcement"></a>Kodér médií vyřazení oznámení

 Kodér médií přestanou počínaje přibližně po dobu 12 měsíců od verze nástroje Media Encoder Standard.

### <a name="azure-sdk-for-php"></a>Sada Azure SDK for PHP
Tým služby Azure SDK publikovaná novou verzi [Azure SDK pro jazyk PHP](http://github.com/Azure/azure-sdk-for-php) balíček, který obsahuje aktualizací a nových funkcí pro Media Services. Na konkrétní sadu Media Services SDK pro jazyk PHP teď podporuje nejnovější [obsahu ochrany](media-services-content-protection-overview.md) funkce. Tyto funkce jsou dynamické šifrování AES a DRM (PlayReady a Widevine) a bez omezení tokenu. Mimoto podporuje i škálování [kódování jednotky](media-services-dotnet-encoding-units.md).

Další informace naleznete v tématu:

* [Sady Media Services SDK pro jazyk PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/) blogu.
* Následující [ukázky kódu jsou](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) umožňují rychle začít:
  * **vodworkflow_aes.php**: Tento PHP souboru ukazuje, jak používat dynamické šifrování AES-128 a službu doručení klíče. Je založena na rozhraní .NET vzorku vysvětlené v [dynamického šifrování pomocí standardu AES-128 a službu doručení klíče](media-services-protect-with-aes128.md).
  * **vodworkflow_aes.php**: Tento PHP souboru ukazuje způsob použití dynamického šifrování PlayReady a službu doručování licencí. Je založena na rozhraní .NET vzorku vysvětlené v [použití PlayReady nebo Widevine běžného dynamického šifrování](media-services-protect-with-playready-widevine.md).
  * **scale_encoding_units.php**: Tento PHP souboru ukazuje, jak škálování jednotek rezervovaných pro kódování.

## <a id="nov_changes_15"></a>Verze. listopadu 2015.
 Služba Media Services nyní nabízí službu doručování licencí Widevine v cloudu. Další informace najdete v tématu [tomto blogu](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Další informace naleznete v [v tomto kurzu](media-services-protect-with-playready-widevine.md) a [úložiště GitHub](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Služeb doručování licence Widevine poskytované Media Services jsou ve verzi preview. Další informace najdete v tématu [tomto blogu](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a id="oct_changes_15"></a>Release – říjen 2015
Služba Media Services je nyní za provozu v následujících datových centrech: Brazílie – Jih, Indie – Západ, Indie – jih a Indie – střed. Nyní můžete portál Azure [vytvoření účtů Media Service](media-services-portal-create-account.md) a provádění různých úloh popsaných v [webová stránka dokumentaci Media Services](https://azure.microsoft.com/documentation/services/media-services/). Za provozu kódování není povoleno v těchto datových centrech. Ne všechny typy jednotky rezervované pro kódování jsou navíc k dispozici v těchto datových centrech.

* Brazílie – jih: Standard a Basic jednotky rezervované pro kódování, které jsou k dispozici pouze.
* Indie – Západ, Indie – jih a Indie – střed: pouze základní jednotky rezervované pro kódování jsou k dispozici.

## <a id="september_changes_15"></a>Release – září 2015
Služba Media Services nyní nabízí možnost chránit obou video na vyžádání a živé datové proudy s modulární DRM s technologií Widevine. Můžete použít následující partneři služeb doručování doručit licence na Widevine:
* [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) 
* [EZDRM](http://ezdrm.com/) 
* [castLabs](http://castlabs.com/company/partners/azure/) 

Další informace najdete v tématu [tomto blogu](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
Konfiguraci zásady AssetDeliveryConfiguration na používání technologie Widevine můžete provést pomocí sady [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (počínaje verzí 3.5.1) nebo rozhraní REST API. 
* Služba Media Services přidaná podpora pro Apple ProRes videa. Teď můžete nahrát QuickTime zdrojových souborech videa využívající Apple ProRes nebo jiných kodeky. Další informace najdete v tématu [tomto blogu](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Nyní můžete Media Encoder Standard udělat extrakce archivu subclipping a za provozu. Další informace najdete v tématu [tomto blogu](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Byly provedeny následující aktualizace filtrování: 
  
  * Nyní můžete formátu Apple HLS filtr pouze. Tuto aktualizaci můžete použít k odebrání pouze sledovat zadáním (pouze = false) v adrese URL.
  * Když definujete filtry pro vaše prostředky, teď můžete kombinovat více (až tři) filtry v jednu adresu URL.
    
    Další informace najdete v tématu [tomto blogu](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).
* Služba Media Services nyní podporuje I rámce v HLS verze 4. Podpora I rámce optimalizuje operace rychlé převíjení vpřed a zpět. Ve výchozím nastavení zahrnují všechny výstupy HLS verze 4 seznamu stop I rámce (EXT-X-I-FRAME-STREAM-INF).
Další informace najdete v tématu [tomto blogu](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a id="august_changes_15"></a>Release – srpen 2015
* Sady Media Services SDK pro jazyk Java verze 0.8.0 verze a nové ukázky jsou nyní k dispozici. Další informace naleznete v tématu:
  
  * [Tento příspěvek blogu](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
  * [Ukázky úložiště Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
* Azure Media Player byl aktualizován s podporou více zvukový stream. Další informace najdete v tématu [tomto příspěvku na blogu](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a id="july_changes_15"></a>Verze července 2015
* Byl oznámila všeobecné zpřístupnění Media Encoder Standard. Další informace najdete v tématu [tomto příspěvku na blogu](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Media Encoder Standard používá přednastavení, jak je popsáno v [v této části](http://go.microsoft.com/fwlink/?LinkId=618336). Pokud používáte jedno z přednastavení pro 4 kB kóduje, získat typ jednotka Premium vyhrazena. Další informace najdete v tématu [škálování kódování](media-services-scale-media-processing-overview.md).
* Za provozu v reálném čase titulky měla použít s Media Services a Media Player. Další informace najdete v tématu [tomto příspěvku na blogu](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Aktualizace sady Media Services .NET SDK
.NET SDK služby Media Services je nyní verze 3.4.0.0. Byly provedeny následující aktualizace: 

* Podpora byla implementována pro archiv za provozu. Nelze stáhnout asset, který obsahuje za provozu archivu.
* Podpora byla implementována pro dynamické filtry.
* Funkce byla implementována tak, aby uživatelé můžete ponechat kontejner úložiště, když se odstranit prostředek.
* Opravy chyb byly provedeny související opakujte zásad v kanály.
* Bylo povoleno Media Encoder Premium pracovního postupu.

## <a id="june_changes_15"></a>Release – červen 2015
### <a name="media-services-net-sdk-updates"></a>Aktualizace sady Media Services .NET SDK
.NET SDK služby Media Services je nyní verze 3.3.0.0. Byly provedeny následující aktualizace: 

* Byla přidána podpora pro zjišťování specifikace OpenId Connect.
* Byla přidána podpora pro zpracování výměny klíče na straně zprostředkovatele identity.

Pokud používáte poskytovatele identity, která zveřejňuje dokument zjišťování OpenID Connect (jako Azure AD, Google a Salesforce provádět), můžete určit, aby Media Services k získání podpisové klíče pro ověření webových tokenů JSON (Jwt) ze zjišťování specifikace OpenID Connect. 

Další informace najdete v tématu [JSON použijte web klíče ze zjišťování specifikace OpenID Connect pro práci s JWT ověřování ve službě Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a id="may_changes_15"></a>Release – květen 2015
Byly oznámeno následující nové funkce:

* [Náhled kódování v reálném čase pomocí služby Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Dynamické manifestu](media-services-dynamic-manifest-overview.md)
* [Náhled procesor médií Azure Media Hyperlapse](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

## <a id="april_changes_15"></a>Vydání duben 2015
### <a name="general-media-services-updates"></a>Aktualizace obecné Media Services
* [Přehrávač médií](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) byl oznámeno.
* Počínaje 2.10 média služby REST, kanály, které jsou nakonfigurovány na ingestování protokol pro zasílání zpráv v reálném čase (RTMP) jsou vytvořeny pomocí primární a sekundární ingestovaných adres URL. Další informace najdete v tématu [kanál ingestování konfigurace](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Azure Media Indexer byl aktualizován.
* Přidala se podpora pro španělštinu.
* Přidala se nová konfigurace pro formát XML.

Další informace najdete v tématu [tomto blogu](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Aktualizace sady Media Services .NET SDK
.NET SDK služby Media Services je nyní verze 3.2.0.0. Byly provedeny následující aktualizace:

* Narušující změny: TokenRestrictionTemplate.Issuer a TokenRestrictionTemplate.Audience byly změněny na být typu string.
* Byly provedeny týkající se vytváření zásady opakování vlastní aktualizace.
* Opravy chyb byly provedeny týkající se nahrávání a stahování souborů.
* Třída MediaServicesCredentials nyní přijme koncové body řízení primární a sekundární přístup k ověřování na základě.

## <a id="march_changes_15"></a>Verze března 2015
### <a name="general-media-services-updates"></a>Aktualizace obecné Media Services
* Služba Media Services nyní poskytuje integraci se Content Delivery Network. Pro podporu integrace, byla přidána vlastnost CdnEnabled StreamingEndpoint. CdnEnabled lze použít s počínaje verzí 2.9 rozhraní REST API. Další informace najdete v tématu [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). CdnEnabled lze pomocí .NET SDK, počínaje verzí 3.1.0.2. Další informace najdete v tématu [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx).
* Pracovní postup Media Encoder Premium se oznámila. Další informace najdete v tématu [představení Premium kódování ve službě Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a id="february_changes_15"></a>Release – únor 2015
### <a name="general-media-services-updates"></a>Aktualizace obecné Media Services
Media Services REST API je nyní verze 2.9. Od této verze, můžete povolit integraci Content Delivery Network s koncové body streamování. Další informace najdete v tématu [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a id="january_changes_15"></a>Release – leden 2015
### <a name="general-media-services-updates"></a>Aktualizace obecné Media Services
Byl oznámila všeobecné zpřístupnění obsahu ochrany v případě dynamického šifrování. Další informace najdete v tématu [Media Services zvyšuje zabezpečení streamování s obecné dostupnosti technologie DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Aktualizace sady Media Services .NET SDK
.NET SDK služby Media Services je nyní verze 3.1.0.1.

Tato verze označena výchozí konstruktor Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate jako zastaralé. Nový konstruktor trvá TokenType jako argument.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>Verze z prosince 2014
### <a name="general-media-services-updates"></a>Aktualizace obecné Media Services
* Některé aktualizace a nové funkce byly přidány do Media Indexer. Další informace najdete v tématu [poznámky k verzi Azure Media Indexer verze 1.1.6.7](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Nové rozhraní API REST byl přidán, můžete použít k aktualizaci jednotky rezervované pro kódování. Další informace najdete v tématu [EncodingReservedUnitType se zbytkem](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* Byla přidána podpora CORS služby doručení klíče.
* Vylepšení výkonu byly provedeny na dotazy týkající se možnosti zásad autorizace.
* V datovém centru Číně [klíče doručení URL](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) je nyní každého zákazníka (stejně jako v jiných datových centrech).
* Doba trvání cíl automaticky HLS byla přidána. Při provádění živé vysílání datového proudu, HLS je vždy zabalené dynamicky. Ve výchozím nastavení služba Media Services automaticky vypočítá segment balení poměr HLS (FragmentsPerSegment), který je na základě intervalu klíčový snímek (KeyFrameInterval). Tato metoda se také označuje jako skupinu obrázků (GOP) přijaté z kodéru za provozu. Další informace najdete v tématu [pracovní pomocí služby Media Services živě Streamovat](http://msdn.microsoft.com/library/azure/dn783466.aspx).

### <a name="media-services-net-sdk-updates"></a>Aktualizace sady Media Services .NET SDK
[Sady Media Services .NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) je nyní verze 3.1.0.0. Byly provedeny následující aktualizace:

* Závislost .NET SDK se upgradoval na rozhraní .NET Framework 4.5.
* Bylo přidáno nové rozhraní API, které můžete použít k aktualizaci jednotky rezervované pro kódování. Další informace najdete v tématu [aktualizace vyhrazená typ jednotky a zvýšení jednotky rezervované pro kódování pomocí rozhraní .NET](media-services-dotnet-encoding-units.md).
* Přidala se podpora JWT pro ověření tokenu. Další informace najdete v tématu [ověření pomocí tokenu JWT ve službě Media Services a dynamickým šifrováním](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* Relativní posunutí pro BeginDate a Datumvypršení platnosti v šabloně licence PlayReady byly přidány.

## <a id="november_changes_14"></a>Verze v listopadu 2014
* Nyní můžete Media Services ingestovat živý obsah Smooth Streaming (fMP4) připojení přes protokol SSL. K ingestování přes protokol SSL, nezapomeňte aktualizovat adresu URL ingestování protokolem HTTPS. Služba Media Services v současné době nepodporuje SSL vlastní domény. Další informace o živé streamování najdete v tématu [fungovat s Azure Media Services živým streamováním](http://msdn.microsoft.com/library/azure/dn783466.aspx).
* V současné době nelze ingestování RTMP živý datový proud připojení přes protokol SSL.
* Proudy přes protokol SSL pouze v případě, že koncový bod streamování, ze kterého doručení obsahu byl vytvořen po 10. září 2014. Pokud vaše streamování adresy URL jsou založené na koncových bodů streamování vytvořený po 10. září 2014, adresa URL obsahuje "streaming.mediaservices.windows.net" (nový formát). Streamování adresy URL, které obsahují "origin.mediaservices.windows.net" (starý formát) nepodporují SSL. Pokud je adresa URL v původním formátu a chcete Streamovat přes protokol SSL, [vytvořit nový koncový bod streamování](media-services-portal-manage-streaming-endpoints.md). Pokud chcete Streamovat obsah přes protokol SSL, použijte adresy URL založené na nový koncový bod streamování.

## <a id="october_changes_14"></a>Release – říjen 2014
### <a id="new_encoder_release"></a>Kodér médií Services verze
 Nové verze média služby Azure Media Encoder byl oznámila. S nejnovější Media Encoder vám účtovat pouze pro výstup GB. Nové kodér, jinak hodnota je funkce, které jsou kompatibilní s předchozí kodér. Další informace najdete v tématu [ceny služby Azure Media Services].

### <a id="oct_sdk"></a>Služba Media Services .NET SDK
Media Services SDK pro .NET rozšíření je nyní verze 2.0.0.3.

Media Services SDK pro .NET je nyní verze 3.0.0.8. Byly provedeny následující aktualizace:

* Refaktoring byl implementován ve třídách zásady opakování.
* Řetězec uživatelského agenta byla přidána do hlavičky požadavku HTTP.
* Krok sestavení NuGet obnovení byla přidána.
* Scénář testy jsme vyřešili používat x509 certifikát z úložiště.
* Nastavení ověřování byly přidány pro když kanálu a datových proudů koncové aktualizace.

### <a name="new-github-repository-to-host-media-services-samples"></a>Nové úložiště GitHub pro hostitele ukázky Media Services
Ukázky jsou v [úložiště GitHub ukázky Media Services](https://github.com/Azure/Azure-Media-Services-Samples).

## <a id="september_changes_14"></a>Verze září 2014
Metadata Media Services REST je nyní verze 2.7. Další informace o nejnovější aktualizace REST, najdete v článku [referenční dokumentace rozhraní API REST služby média](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Media Services SDK pro .NET je nyní verze 3.0.0.7

### <a id="sept_14_breaking_changes"></a>Nejnovější změny
* Původ byla přejmenována na [StreamingEndpoint].
* Byla provedena změna výchozího chování při použití portálu Azure ke kódování a pak publikovat soubory MP4.

### <a id="sept_14_GA_changes"></a>Nové funkce nebo scénáře, které jsou součástí na vydání obecné dostupnosti
* Procesor médií Media Indexer byla zavedena. Další informace najdete v tématu [indexu mediálních souborů pomocí Media Indexer](http://msdn.microsoft.com/library/azure/dn783455.aspx).
* Můžete použít [StreamingEndpoint] entity přidat názvy vlastních domén (hostitel).
  
    Jako název koncového bodu streamování Media Services použít vlastní název domény, přidejte vlastní hostitel názvy do koncového bodu streamování. Pro přidání vlastního hostitele názvy použijte rozhraní API služby REST média nebo .NET SDK.
  
    Platí následující aspekty:
  
  * Musíte mít vlastnictví vlastní název domény.
  * Vlastnictví název domény musí být ověřený službou Media Services. Chcete-li ověřit doménu, vytvořte záznam CName, který se mapuje MediaServicesAccountId nadřazené domény k ověření mediaservices-dns zóny DNS.
  * Musíte vytvořit jiný CName, který mapuje název vlastního hostitele (například sports.contoso.com) na název hostitele vašeho Media Services StreamingEndpoint (například amstest.streaming.mediaservices.windows.net).

    Další informace najdete v tématu vlastnost v CustomHostNames [StreamingEndpoint](http://msdn.microsoft.com/library/azure/dn783468.aspx) článku.

### <a id="sept_14_preview_changes"></a>Nové funkce nebo scénáře, které jsou součástí verze public preview
* Živé streamování preview. Další informace najdete v tématu [pracovní pomocí služby Media Services živě Streamovat](http://msdn.microsoft.com/library/azure/dn783466.aspx).
* Služba doručení klíče. Další informace najdete v tématu [dynamického šifrování pomocí standardu AES-128 a službu doručení klíče](http://msdn.microsoft.com/library/azure/dn783457.aspx).
* Dynamické šifrování AES. Další informace najdete v tématu [dynamického šifrování pomocí standardu AES-128 a službu doručení klíče](http://msdn.microsoft.com/library/azure/dn783457.aspx).
* Službu doručování licencí PlayReady. 
* Dynamického šifrování PlayReady. 
* Šablona licence Media Services PlayReady. Další informace najdete v tématu [přehled šablonu licence Media Services PlayReady].
* Prostředky šifrovat úložiště datového proudu. Další informace najdete v tématu [Stream obsahu zašifrovaného úložiště](http://msdn.microsoft.com/library/azure/dn783451.aspx).

## <a id="august_changes_14"></a>Verze 2014 srpen
Při kódování prostředek, prostředek výstup vytváří až po dokončení úlohy kódování. Dokud nebude tato verze kodéru Media Services vytváří metadata o prostředcích výstup. Od této verze, kodér vytvoří metadata o vstupní prostředky. Další informace najdete v tématu [vstupu metadata] a [výstup metadat].

## <a id="july_changes_14"></a>Release – červenec 2014
Pro Azure Media Services Balíčkovač a modul pro šifrování byly provedeny následující opravy chyb:

* Při provozu archivu asset se přenesou do HLS, pouze zvuk přehrává: Tento problém byl opraven a nyní můžete přehrát zvuk a video.
* Pokud prostředek je zabalené do obálky šifrovací HLS a AES 128 bitů, zabalené datové proudy Nepřehrávat zpět na zařízeních s Androidem: Tato chyba byla opravena a zabalené datový proud přehrává na zařízeních s Androidem, které podporují HLS.

## <a id="may_changes_14"></a>Verze může 2014
### <a id="may_14_changes"></a>Aktualizace obecné Media Services
Teď můžete použít [dynamické balení] do datového proudu HLS verze 3. Do datového proudu HLS verze 3, přidejte do cesty Lokátor původ v následujícím formátu: * .ism/manifest(format=m3u8-aapl-v3). Další informace najdete v tématu [tomto blogu](http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/).

Dynamické balení teď také podporuje doručování HLS (verze 3 a verze 4) šifrovat pomocí PlayReady podle staticky šifrovat pomocí PlayReady technologie Smooth Streaming. Informace o tom, jak šifrování, technologie Smooth Streaming s technologií PlayReady najdete v tématu [chránit technologie Smooth Streaming s technologií PlayReady](http://msdn.microsoft.com/library/azure/dn189154.aspx).

### <a name="may_14_donnet_changes"></a>Aktualizace sady Media Services .NET SDK
.NET SDK služby Media Services je nyní verze 3.0.0.5. Byly provedeny následující aktualizace:

* Rychlost a pružnost je lepší použít při nahrávání a stahování datových zdrojů médií.
* Vylepšení byly provedeny v opakování logiku a přechodná výjimek: 
  
  * Pro výjimky, které jsou dojde, pokud dotaz, uložit změny a odeslání nebo stažení souborů byly vylepšené logiku přechodná chyba zjišťování a zkuste to znovu. 
  * Když získáte webové výjimky (například během token požadavku služby Řízení přístupu), závažné chyby nezdaří rychlejší teď.

Další informace najdete v tématu [opakujte logiky sady Media Services SDK pro .NET].

## <a id="april_changes_14"></a>Kodér vydání duben 2014
### <a name="april_14_enocer_changes"></a>Aktualizace služby kodér médií
* Přidala se podpora pro načítání souborů AVI soubory, které jsou vytvořeny pomocí editoru nelineární tráva Valley EDIUS. V tomto procesu se na video lehkým zkomprimuje s použitím kodeků tráva Valley Ústředí/HQX. Další informace najdete v tématu [tráva Valley ohlášen EDIUS 7 vysílání datového proudu prostřednictvím cloudu].
*  Chcete-li určit zásady vytváření názvů pro soubory vytvořené pomocí kodéru Media Services přidala se podpora. Další informace najdete v tématu [řízení Media Services Encoder výstup názvy souborů](http://msdn.microsoft.com/library/azure/dn303341.aspx).
*  Překryvy video nebo zvuk byla přidána podpora. Další informace najdete v tématu [vytvořit překryvy](http://msdn.microsoft.com/library/azure/dn640496.aspx).
*  Přidala se podpora více video segmentů ke spojení společně. Další informace najdete v tématu [spojení video segmenty](http://msdn.microsoft.com/library/azure/dn640504.aspx).
* Chyba byla opravena vztahující se k překódování soubory MP4 s rychlostmi kde kódované zvukovému záznamu s MPEG-1 zvuk vrstvy 3 (také označované jako MP3).

## <a id="jan_feb_changes_14"></a>Uvolní leden/února 2014
### <a name="jan_fab_14_donnet_changes"></a>Media Services .NET SDK 3.0.0.1, 3.0.0.2 a 3.0.0.3
Změny v 3.0.0.1 a 3.0.0.2 patří:

* Problémy související s použití dotazů LINQ s příkazy OrderBy jsme vyřešili.
* Testování řešení v [Githubu] byly rozdělit do jednotkové testování a testy na základě scénáře.

Další informace o změnách, najdete v tématu [uvolní sadu Media Services .NET SDK 3.0.0.1 a 3.0.0.2](http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/).

Ve verzi 3.0.0.3 byly provedeny následující změny:

* Úložiště Azure závislosti byly upgradovány na verzi 3.0.3.0 použít.
* 3.0 došlo k problému na zpětné kompatibility. *.* uvolní.

## <a id="december_changes_13"></a>Verze prosinci 2013
### <a name="dec_13_donnet_changes"></a>Služba Media Services .NET SDK 3.0.0.0
> [!NOTE]
> Verze 3.0.x.x nejsou zpětně kompatibilní s verzemi 2.4.x.x.
> 
> 

Nejnovější verzi sady Media Services SDK je nyní 3.0.0.0. Můžete stáhnout nejnovější balíček z NuGet nebo získat službu bits z [Githubu].

Od verze sady Media Services SDK verze 3.0.0.0, můžete opakovaně použít [služby Řízení přístupu Azure AD](http://msdn.microsoft.com/library/hh147631.aspx) tokeny. Další informace najdete v části "Tokeny služby Řízení přístupu opakované použití" v [připojení ke službám Media Services pomocí sady Media Services SDK pro .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

### <a name="dec_13_donnet_ext_changes"></a>Rozšíření sady Media Services .NET SDK 2.0.0.0
 Rozšíření sady Media Services .NET SDK je sada metod rozšíření a pomocných funkcí, které zjednoduší kódování a usnadní vývoj pomocí služby Media Services. Můžete získat nejnovější bits z [rozšíření sady Media Services .NET SDK](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev).

## <a id="november_changes_13"></a>Verze v listopadu 2013
### <a name="nov_13_donnet_changes"></a>Změny sady Media Services .NET SDK
Od této verze sady Media Services SDK pro .NET obslužné rutiny přechodná chyba chyby, které mohou nastat při volání vrstvě Media Services REST API.

## <a id="august_changes_13"></a>Release – srpen 2013
### <a name="aug_13_powershell_changes"></a>Rutiny prostředí PowerShell služby média, které jsou součástí sady Azure SDK tools
Následující rutiny prostředí PowerShell Media Services jsou teď součástí [Azure SDK tools](https://github.com/Azure/azure-sdk-tools):

* Get-AzureMediaServices 

    Příklad: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Příklad: `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`
* New-AzureMediaServicesKey 
  
    Příklad: `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Příklad: `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`

## <a id="june_changes_13"></a>Release – červen 2013
### <a name="june_13_general_changes"></a>Změny Media Services
Následující změny v této části jsou aktualizace součástí vydání červen 2013 Media Services:

* Možnost propojit víc účtů úložiště na účet Media Service. 
    * StorageAccount
    * Asset.StorageAccountName a Asset.StorageAccount
* Umožňuje aktualizovat Job.Priority. 
* Oznámení související entity a vlastnosti: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * Úloha
* Asset.Uri 
* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Změny sady Media Services .NET SDK
Následující změny jsou zahrnuty v červen 2013 uvolní sada SDK služby Media Services. Nejnovější sady Media Services SDK je dostupná na Githubu.

* Počínaje verzí 2.3.0.0, sada SDK služby Media Services podporuje, propojování více úložiště účtů k účtu Media Services. Tuto funkci podporovat následující rozhraní API:
  
    * Typ IStorageAccount
    * Vlastnost Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts
    * Vlastnost StorageAccount
    * Vlastnost StorageAccountName
  
    Další informace najdete v tématu [prostředky spravovat Media Services napříč více účtů úložiště](http://msdn.microsoft.com/library/azure/dn271889.aspx).
* Rozhraní API související s oznámení. Počínaje verzí 2.2.0.0, můžete poslouchat Azure Queue storage oznámení. Další informace najdete v tématu [zpracování Media Services úlohy oznámení](http://msdn.microsoft.com/library/azure/dn261241.aspx).
  
    * Vlastnost Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions
    * Typ Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint
    * Typ Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription
    * Typ Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection
    * Typ Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType
* Závislost na úložiště klienta SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll)
* Závislost na protokolu OData 5.5 (Microsoft.Data.OData.dll)

## <a id="december_changes_12"></a>Verze 2012 prosinec
### <a name="dec_12_dotnet_changes"></a>Změny sady Media Services .NET SDK
* IntelliSense: Chybí IntelliSense dokumentace přidala pro mnoho typů.
* Microsoft.Practices.TransientFaultHandling.Core: Problém byl opraven kde sady SDK stále byl závislý na původní verzi tohoto sestavení. Sada SDK nyní odkazuje 5.1.1209.1 verzi tohoto sestavení.

Opravy pro problémy zjištěné v listopad 2012 SDK:

* IAsset.Locators.Count: Tento počet je nyní správně hlášené na nové rozhraní IAsset po odstranění všech lokátory se.
* IAssetFile.ContentFileSize: Tato hodnota je nyní správně nastavená po nahrávaný podle IAssetFile.Upload(filepath).
* IAssetFile.ContentFileSize: Tuto vlastnost lze nyní nastavit při vytváření souboru asset. Protože byl dříve jen pro čtení.
* IAssetFile.Upload(filepath): Problém byl opraven kde byla tato metoda synchronní nahrávání vyvolání k následující chybě při více souborů byly odeslání pro daný prostředek. Došlo k chybě "serveru se nepodařilo ověřit žádost. Ujistěte se, že hodnotu hlavičky autorizace je vytvořen. správně včetně podpis. "
* IAssetFile.UploadAsync: Problém byl opraven omezenou souběžných odesílání souborů, které se pět souborů.
* IAssetFile.UploadProgressChanged: Tato událost se teď poskytují prostřednictvím sady SDK.
* IAssetFile.DownloadAsync (string, BlobTransferClient, ILocator, CancellationToken): Toto přetížení metody je nyní k dispozici.
* IAssetFile.DownloadAsync: Problém byl opraven omezenou souběžných stahování souborů do pět souborů.
* IAssetFile.Delete(): Problém byl opraven kde volání odstranění může vyvolat výjimku pro IAssetFile nebyl nahrán žádný soubor.
* Úlohy: Problém byl opraven kde řetězení "MP4 funkce Smooth Streams úloha" s "Ochrany úloha PlayReady" pomocí šablony úlohy nevytvořili žádné úlohy vůbec.
* EncryptionUtils.GetCertificateFromStore(): Tato metoda vyvolá už výjimka odkazu s hodnotou null z důvodu chyby při hledání certifikát založený na problémy s konfigurací certifikátu.

## <a id="november_changes_12"></a>Release – listopad 2012
Změny v této části jsou aktualizace součástí listopad 2012 (verze 2.0.0.0) sady SDK. Tyto změny mohou vyžadovat žádné kód napsaný v verzi Preview. června 2012 upravena nebo přepsaná verzi sady SDK.

* Prostředky
  
    * Je IAsset.Create(assetName) *pouze* asset vytvoření funkce. IAsset.Create už ukládání souborů v rámci volání metody. Použijte IAssetFile pro odesílání.
    * Metoda IAsset.Publish a hodnota výčtu AssetState.Publish byly odebrány ze sady SDK služby. Je nutné přepsat kód, který závisí na této hodnotě.
* FileInfo
  
    * Tato třída byla odebrána a nahrazena IAssetFile.
  
* IAssetFiles
  
    * IAssetFile nahrazuje FileInfo a s jiným chováním. Pokud chcete použít, vytvořte instanci objektu IAssetFiles, za nímž následuje nahrání souboru buď pomocí sady Media Services SDK nebo sady SDK úložiště. Můžete použít následující přetížení IAssetFile.Upload:
  
        * IAssetFile.Upload(filePath): Tato metoda synchronní blokuje vlákno, a doporučujeme ho jenom v případě, že nahrát jeden soubor.
        * IAssetFile.UploadAsync (cesta k souboru, blobTransferClient, Lokátor, cancellationToken): Tato asynchronní metoda je způsob upřednostňované odeslání. 
    
            Známého problému: Pokud použijete token zrušení, odesílání zrušeno. Úlohy mohou mít mnoho stavů zrušení. Musíte správně catch a zpracování výjimek.
* Lokátory
  
    * Nebyly odebrány specifické pro původní verze. Kontext specifické pro SAS. Locators.CreateSasLocator (asset, accessPolicy) budou označeny nepoužívané nebo odebraných podle obecné dostupnosti. Najdete v části "Lokátory" v části "Nových funkcí" aktualizované chování.

## <a id="june_changes_12"></a>Verze preview června 2012
Následující funkce byla nová v listopadu verzi sady SDK:

* Odstraňování entit
  
    * IAsset, IAssetFile, ILocator, IAccessPolicy a IContentKey objekty jsou nyní odstranit na úrovni objektu, který je IObject.Delete(), místo aby odstranění v kolekci, který je cloudMediaContext.ObjCollection.Delete(objInstance).
* Lokátory
  
    * Lokátory nyní je nutné vytvořit pomocí metody CreateLocator. LocatorType.SAS nebo LocatorType.OnDemandOrigin hodnoty výčtu musí použít jako argument pro konkrétní typ lokátoru, že chcete vytvořit.
    * Nové vlastnosti byly přidány do lokátory, aby bylo snazší získat použitelné identifikátory URI pro obsah. Tato změna návrhu lokátorů poskytují větší flexibilitu pro budoucí rozšíření třetích stran a zvyšuje snadné použití média klientské aplikace.
* Podpora asynchronní metody
  
    * Přidala se asynchronní podpora pro všechny metody.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[MSDN fóru služby Azure Media Services]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[REST API služby Azure Media Services odkaz]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[ceny služby Azure Media Services]: http://azure.microsoft.com/pricing/details/media-services/
[vstupu metadata]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[výstup metadat]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Deliver content]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Index media files with the Azure Media Indexer]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Work with Media Services live streaming]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Use AES-128 dynamic encryption and the key delivery service]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Use PlayReady dynamic encryption and the license delivery service]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[přehled šablonu licence Media Services PlayReady]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Stream storage-encrypted content]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://portal.azure.com
[Dynamické balení]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[opakujte logiky sady Media Services SDK pro .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[tráva Valley ohlášen EDIUS 7 vysílání datového proudu prostřednictvím cloudu]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Create overlays]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Stitch video segments]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: http://msdn.microsoft.com/library/hh147631.aspx
[Connect to Media Services with the Media Services SDK for .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[Githubu]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Handle Media Services job notifications]: http://msdn.microsoft.com/library/azure/dn261241.aspx

