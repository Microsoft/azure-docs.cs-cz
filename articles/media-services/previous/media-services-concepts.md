---
title: Koncepty služby Azure Media Services | Dokumentace Microsoftu
description: Toto téma poskytuje přehled konceptů Azure Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 413e005762ab557e0605f9b4e79a6fe5b45448b7
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993907"
---
# <a name="azure-media-services-concepts"></a>Koncepty služby Azure Media Services 

Toto téma poskytuje přehled o nejdůležitějších pojmů Media Services.

## <a name="a-idassetsassets-and-storage"></a><a id="assets"/>Prostředky a úložiště
### <a name="assets"></a>Prostředky
[Asset](https://docs.microsoft.com/rest/api/media/operations/asset) obsahuje digitální soubory (včetně video, zvuk, obrázky, kolekci miniatur, textové stopy a soubory s titulky) a metadata o těchto souborech. Jakmile jsou digitální soubory nahrát do assetu, může použít ve službě Media Services, kódování a streamování pracovních postupů.

Prostředek je namapovaná na kontejner objektů blob v účtu Azure Storage a soubory v prostředku se ukládají jako objekty BLOB bloku v tomto kontejneru. Objekty BLOB stránky nejsou podporovány službou Azure Media Services.

Při rozhodování o tom, jaké mediálního obsahu můžete nahrát a uložit do assetu, platí následující aspekty:

* Prostředek může obsahovat pouze jeden, jedinečné instanci mediálního obsahu. Například jedné úpravy epizodě TV, video nebo oznámení o inzerovaném programu.
* Prostředek nesmí obsahovat více interpretací nebo úpravy audiovizuálních souborů. Nesprávné využití prostředku jedním z příkladů by být pokusu o uložení více než jedné epizodě TV a oznámení o inzerovaném programu, více úhlů kamery z jednoho produkčního prostředí v prostředku. Ukládání více interpretací nebo úpravy audiovizuálních souborů do assetu může vést k odesílání úloh kódování, streamování a doručení assetu později v pracovním postupu zabezpečení mohou mít problémy.  

### <a name="asset-file"></a>Soubor assetu
[AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) představuje skutečný videa nebo zvukový soubor, který je uložený v kontejneru objektů blob. Soubor prostředků je vždy přidružena k assetu a prostředek může obsahovat jeden nebo více souborů. Kodér Media Services úloh selže, pokud objekt souboru prostředku není přidružen k digitálnímu souboru v kontejneru objektů blob.

**AssetFile** instance a samotný mediální soubor jsou dva různé objekty. AssetFile instance obsahuje metadata do souboru média, zatímco mediální soubor obsahuje skutečné mediálního obsahu.

By se neměly pokoušet ke změně obsahu kontejnery objektů blob, které byly generovány službou Media Services bez použití rozhraní API služby Media.

### <a name="asset-encryption-options"></a>Možnosti šifrování Asset
V závislosti na typu obsahu, který chcete nahrát, uložit a posílat služba Media Services poskytuje různé možnosti šifrování, které můžete vybírat.

>[!NOTE]
>Nepoužívá se žádné šifrování. Toto je výchozí hodnota. Při použití této možnosti není váš obsah chráněný během přenosu ani při umístění v úložišti.

Pokud chcete dodávat obsah ve formátu MP4 pomocí progresivního stahování, použijte tuto možnost Pokud chcete nahrát obsah.

**StorageEncrypted** – tuto možnost použijte k šifrování vašich nešifrovaného obsahu pomocí šifrování AES 256 bitů a nahrajte ho do služby Azure Storage kde jsou uložená v klidovém stavu zašifrovaná. Prostředky chráněné pomocí šifrování úložiště jsou automaticky bez šifrování umístěny do systému souborů EFS před kódováním a volitelně se znovu zašifrují před jejich odesláním zpět v podobě nového výstupního prostředku. Případem primárního použití šifrování úložiště je, pokud chcete zajistit vysoce kvalitní vstupními multimediálními soubory pomocí silného šifrování v klidovém stavu na disku. 

Aby bylo možné poskytovat šifrované prostředků úložiště, musíte nakonfigurovat zásady doručení assetu, Media Services ví, jak chcete dodávat váš obsah. Předtím, než můžete Streamovat prostředek, server streamování zruší šifrování úložiště a streamuje obsah pomocí zadaného doručování zásad (například AES, PlayReady nebo bez šifrování). 

**CommonEncryptionProtected** – tuto možnost použijte, pokud chcete zašifrovat (nebo nahrát už šifrovaný) obsah s běžným šifrováním nebo DRM PlayReady (například technologie Smooth Streaming chráněná pomocí PlayReady DRM).

**EnvelopeEncryptionProtected** – tuto možnost použijte, pokud chcete chránit (nebo nahrajete, již chráněné) HTTP Live Streaming (HLS) zašifrovaná pomocí šifrování AES (Advanced Standard). Pokud odesíláte HLS již šifrováním pomocí standardu AES, se musí být šifrována pomocí Správce transformací.

### <a name="access-policy"></a>Zásada přístupu
[AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) definuje oprávnění (jako jsou čtení, zápisu a seznamu) a doba trvání přístupu k prostředku. Obvykle by AccessPolicy objekt předat, která se pak použije pro přístup k souborům obsaženy v assetu lokátoru.

>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Pokud vždy používáte stejné dny / přístupová oprávnění, například zásady pro lokátory, které mají zůstat na místě po dlouhou dobu (zásady bez odeslání), měli byste použít stejné ID zásad. Další informace najdete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) tématu.

### <a name="blob-container"></a>Kontejner objektů BLOB
Kontejner objektů blob poskytuje seskupení sady objektů BLOB. Kontejnery objektů blob se používají ve službě Media Services jako hranic bod pro řízení přístupu a lokátory sdíleného přístupového podpisu (SAS) na prostředky. Účet úložiště Azure může obsahovat neomezený počet kontejnerů objektů blob. Kontejner můžete pojmout neomezený počet objektů blob.

>[!NOTE]
> By se neměly pokoušet ke změně obsahu kontejnery objektů blob, které byly generovány službou Media Services bez použití rozhraní API služby Media.
> 
> 

### <a name="a-idlocatorslocators"></a><a id="locators"/>Lokátory
[Lokátor](https://docs.microsoft.com/rest/api/media/operations/locator)s poskytují vstupní bod pro přístup k souborům obsaženy v assetu. Zásady přístupu slouží k definování oprávnění a dobu trvání, že má klient přístup k dané assetu. Lokátory může mít mnoho na jeden vztah s zásadu přístupu tak, aby různé lokátory poskytnout různé spuštění a typy připojení různých klientů přitom všechny stejné oprávnění a nastavení doby trvání; z důvodu omezení zásad sdíleného přístupu nastavit pomocí služby Azure storage, ale nemůže mít více než pět jedinečné lokátory přidružené k dané assetu najednou. 

Služba Media Services podporuje dva typy lokátorů: Ondemandorigin, používaný ke streamování médií (například MPEG DASH, HLS nebo technologie Smooth Streaming) nebo progresivně stahovat média a lokátory adresy URL SAS, používá k odeslání nebo stažení to\from soubory médií Azure storage. 

>[!NOTE]
>Seznam oprávnění (AccessPermissions.List) by neměl být použili při vytváření Lokátor OnDemandOrigin. 

### <a name="storage-account"></a>Účet úložiště
Veškerý přístup ke službě Azure Storage se provádí prostřednictvím účtu úložiště. Účet služby Media Services můžete přidružit jeden nebo více účtů úložiště. Účet může obsahovat neomezený počet kontejnerů, jejich celkové velikosti je v části 500TB na jeden účet úložiště.  Služba Media Services poskytuje úrovně nástrojů SDK, aby bylo možné spravovat více účtů úložiště a distribuci vašich prostředků během odesílání do těchto účtů podle metriky nebo náhodná distribuce Vyrovnávání zatížení. Další informace najdete v tématu práci s [služby Azure Storage](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

## <a name="jobs-and-tasks"></a>Úlohy a úkoly
A [úlohy](https://docs.microsoft.com/rest/api/media/operations/job) se obvykle používá k procesu (například indexování nebo kódování) jeden prezentace audio/video. Pokud zpracováváte několik videí, vytvoření úlohy pro každé video kódovaný.

Úloha obsahuje metadata o zpracování, která se má provést. Každá úloha obsahuje jednu nebo více [úloh](https://docs.microsoft.com/rest/api/media/operations/task)výstup s, určíte atomic zpracování úloh, jeho vstupní prostředky, prostředky, procesor médií a její související nastavení. Úkoly v rámci úlohy je možné zřetězit, kde je zadána výstupní asset jedné úlohy jako vstupní asset dalšího kroku. Tímto způsobem může obsahovat jednu úlohu všechny nezbytné pro prezentaci média zpracování.

## <a id="encoding"></a>Kódování
Azure Media Services nabízí několik možností pro kódování multimédií v cloudu.

Když začínáte se službou Media Services, je důležité pochopit rozdíl mezi formáty kodeky a soubor.
Kodeky jsou software, který implementuje algoritmy komprese nebo dekomprese, zatímco formáty souborů jsou kontejnery, které obsahují komprimované video.

Služba Media Services poskytuje dynamické balení, což vám umožní dodávat váš obsah s adaptivní přenosovou rychlostí kódováním MP4 nebo technologie Smooth Streaming ve formátech streamování podporovaných službou Media Services (MPEG DASH, HLS, technologie Smooth Streaming), aniž byste je museli znovu zabalit do těchto formátů streamování.

Abyste mohli využívat [dynamické balení](media-services-dynamic-packaging-overview.md), musíte zakódovat váš soubor mezzanine (zdrojový) do sady souborů MP4 nebo soubory technologie Smooth Streaming s adaptivní přenosovou rychlostí a mít aspoň jeden úrovně standard nebo premium koncový bod streamování bylo zahájeno stavu.

Služba Media Services podporuje následující kodéry na vyžádání, které jsou popsány v tomto článku:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Pracovní postup kodéru Media Encoder Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Informace o podporovaných kodérů najdete v tématu [kodérů](media-services-encode-asset.md).

## <a name="live-streaming"></a>Živé streamování
Ve službě Azure Media Services představuje kanál kanál pro zpracování obsahu živého streamování. Kanál obdrží živé vstupní datové proudy v jednom ze dvou způsobů:

* Místní kodér služby live Encoding odešle do kanálu s více přenosovými rychlostmi RTMP nebo technologie Smooth Streaming (fragmentovaný soubor MP4). Následující kodéry, které výstupu technologie Smooth Streaming s více přenosovými rychlostmi můžete použít: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco a Elemental. Následující kodéry výstupu RTMP: Adobe Flash Live Encoder, Telestream Wirecast, Teradek, Haivision a Transkodéry kodérů. Ingestované datové proudy prochází kanály bez kódování a překódování dalšího. Služba Media Services doručí datový proud zákazníkům na vyžádání.
* Datový proud s jednou přenosovou rychlostí (v jednom z následujících formátů: RTMP nebo technologie Smooth Streaming (fragmentovaný soubor MP4)) je odesílá do kanálu, který má povolené provádět živé kódování pomocí Media Services. Kanál potom provede kódování v reálném čase pro příchozí datový proud s jednou přenosovou rychlostí v reálném čase na datový proud videa s více přenosovými rychlostmi (adaptivní). Služba Media Services doručí datový proud zákazníkům na vyžádání.

### <a name="channel"></a>Kanál
Ve službě Media Services [kanál](https://docs.microsoft.com/rest/api/media/operations/channel)s zodpovídají za zpracování obsahu živého streamování. Kanál obsahuje vstupní koncový bod (adresa URL ingestu), pak poskytnete živé převaděč. Kanál obdrží živé vstupní datové proudy z živého převaděč a zpřístupňuje je prostřednictvím jednoho nebo více koncové body streamování. Kanály také poskytuje koncový bod ve verzi preview (adresa URL náhledu), který používáte k zobrazení náhledu a ověření datového proudu před dalším zpracováním a doručením.

Při vytváření kanálu můžete získat adresu URL ingestování a adresu URL ve verzi preview. Pokud chcete získat tyto adresy URL, nemá kanál ve spuštěném stavu. Pokud jste připravení začít zapisovat data z živého převaděč do kanálu, musí se spustit kanál. Po spuštění ingestovat data živého převaděč můžete zobrazit náhled datového proudu.

Každý účet Media Services může obsahovat několik kanálů, více programů a více koncové body streamování. V závislosti na požadavcích šířku pásma a zabezpečení služby StreamingEndpoint může být vyhrazen pro jeden nebo více kanálů. Žádné StreamingEndpoint můžete načítat jakémkoli jiném kanálu.

### <a name="program-event"></a>Program (událost)
A [programu (událost)](https://docs.microsoft.com/rest/api/media/operations/program) vám umožňuje řídit publikování a ukládání segmentů v živém datovém proudu. Kanály spravují programy (události). Vztah kanálů a programů je podobná tradičním médiím, kde kanál obsahuje nepřetržitý datový proud obsahu a program je vymezen na určité načasované události v tomto kanálu.
Můžete určit počet hodin, které chcete uchovávat zaznamenaný obsah programu nastavením **ArchiveWindowLength** vlastnost. Tuto hodnotu můžete nastavit v rozmezí od 5 minut po 25 hodin.

ArchiveWindowLength také určuje, že maximální množství času klientů můžete posunout zpátky v čase od aktuální živé pozice. Programy můžou běžet po určenou dobu a obsah, který se do délky okna nevejde, bude vždy zahozen. Hodnota této vlastnosti také určuje, jak dlouho můžou růst manifesty klientů.

Každý program (událost) je přidružena k Assetu. Program publikovat, musíte vytvořit lokátor pro přidružený asset. Tento lokátor vám umožní sestavit adresu URL pro streamování, kterou potom poskytnete svým klientům.

Kanál podporuje až tři současně spuštěné programy, takže si můžete vytvořit několik archivů stejného příchozího datového proudu. To vám umožní podle potřeby publikovat a archivovat různé části události. Požadavek vaší firmy může být například takový, že chcete archivovat 6 hodin programu, ale vysílat jenom posledních 10 minut. K tomu potřebujete vytvořit dva současně spuštěné programy. Jeden program nastavíte, aby archivoval 6 hodin události, ale tento program nebudete publikovat. Druhý program nastavíte, aby archivoval 10 minut a tento program budete publikovat.

Další informace naleznete v tématu:

* [Práce s kanály, které mají povolené kódování v reálném pomocí Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Práce s kanály, které přijímají živé datové proudy s více přenosovými rychlostmi z místních kodérů](media-services-live-streaming-with-onprem-encoders.md)
* [Kvóty a omezení](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>Ochrana obsahu
### <a name="dynamic-encryption"></a>Dynamické šifrování
Azure Media Services umožňuje zabezpečení médií od okamžiku opuštění počítače přes úložiště, zpracování a dodání. Služba Media Services vám umožní dodávat váš obsah dynamicky šifrován Standard AES (Advanced Encryption) (pomocí 128bitového šifrování klíče) a Standard common encryption (CENC) pomocí technologie PlayReady nebo Widevine DRM. Služba Media Services také poskytuje službu k doručování klíčů AES a PlayReady licence autorizovaným klientům.

V současné době můžete šifrovat formátech streamování: HLS, MPEG DASH a Smooth Streaming. Nelze zašifrovat progresivní stahování.

Pokud chcete pro Media Services k šifrování assetu, musíte přidružit asset šifrovacího klíče (CommonEncryption nebo EnvelopeEncryption) a také nakonfigurovat zásady autorizace pro klíč.

Pokud chcete Streamovat šifrované prostředků úložiště, musíte nakonfigurovat zásady doručení assetu aby bylo možné určit, jak chcete dodávat váš asset.

Datový proud je žádost přehrávač, Media Services používá k dynamické šifrování obsahu pomocí šifrování obálku (pomocí standardu AES) nebo používat standard common encryption (s technologií PlayReady nebo Widevine) se zadaným klíčem. Dešifrování datového proudu, přehrávače požádá o klíč ze služby doručení klíče. Rozhodování o tom, zda je uživatel oprávnění k získání klíče, služba vyhodnocuje zásady autorizace, které jste zadali pro klíč.

### <a name="token-restriction"></a>Omezení s tokenem
Zásady autorizace klíče obsahu může mít jednu nebo více omezení autorizace: otevřete, token omezení nebo omezení IP adres. Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Služba Media Services podporuje tokeny ve formátu jednoduchých webových tokenů (SWT) a formát JSON Web Token (JWT). Služba Media Services neposkytuje služby tokenu zabezpečení. Můžete vytvořit vlastní službu STS. Služba tokenů zabezpečení musí být nakonfigurovaný k vytvoření tokenu podepsán zadaný klíč a vydávání deklarací identity, které jste zadali v konfiguraci omezení s tokenem. Doručení klíče služby Media Services vrátí požadovaný klíč (nebo licencí) pro klienty, pokud je token platný a deklarace identity v tokenu shody jsou nakonfigurované pro klíče (nebo licencí).

Při konfiguraci token s omezením pomocí specifikátoru zásad, musíte zadat primární ověřovací klíč, vydavatele a parametry cílovou skupinu. Primární ověřovací klíč obsahuje klíč, který byl token podepsán pomocí, Vystavitel je služba tokenů zabezpečení, který vydá token. Cílová skupina (říká se jim oboru) by měl popisovat záměr tokenu nebo prostředek token, který autorizuje přístup k. Služba Media Services doručení klíče ověří, že tyto hodnoty v tokenu odpovídají hodnotám v šabloně.

Další informace najdete v následujících článcích:
- [Ochrana obsahu – přehled](media-services-content-protection-overview.md)
- [Ochrana pomocí AES-128](media-services-protect-with-aes128.md)
- [Ochrana pomocí PlayReady nebo Widevine](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Doručování
### <a name="a-iddynamicpackagingdynamic-packaging"></a><a id="dynamic_packaging"/>Dynamické balení
Při práci se službou Media Services, doporučuje se zakódovat váš soubor mezzanine soubory do sady MP4 adaptivní přenosové rychlosti a převeďte sady na požadovaný formát pomocí [dynamické balení](media-services-dynamic-packaging-overview.md).

### <a name="streaming-endpoint"></a>Koncový bod streamování
StreamingEndpoint reprezentuje službu streamování, která může doručovat obsah přímo do klientské aplikace přehrávače nebo do Content Delivery Network (CDN) k další distribuci (Azure Media Services teď poskytuje integraci Azure CDN.) Výstupní datový proud z datových proudů koncový bod služby může být živý stream nebo Asset videa na vyžádání ve vašem účtu Media Services. Zákazníci Media Services si podle svých potřeb zvolí buď koncový bod **Standard**, nebo jeden nebo několik koncových bodů streamování **Premium**. Koncový bod streamování Standard je vhodný pro většinu streamovacích úloh. 

Koncový bod streamování Standard je vhodný pro většinu streamovacích úloh. Standardní koncové body streamování nabízejí flexibilitu při doručování obsahu prakticky každé zařízení prostřednictvím dynamického balení do HLS, MPEG-DASH, Smooth Streaming i dynamické šifrování Microsoft PlayReady, Google Widevine, Apple Fairplay a AES128.  Jsou také škálovat z velmi malé na velmi velké cílové skupiny pomocí tisíce souběžných divákům prostřednictvím integrace Azure CDN. Pokud máte pokročilé úlohy nebo vaše kapacitní požadavky na velikost na standardních datových proudů cíle propustnost koncového bodu nebo chcete řádit kapacitu služby StreamingEndpoint pro zpracování, stále se rozšiřující potřebuje šířky pásma, doporučuje se přidělení jednotek škálování (označované také jako jednotky streamování premium).

Doporučuje se použít dynamické balení a dynamického šifrování.

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 

Další informace najdete v [tomto](media-services-portal-manage-streaming-endpoints.md) tématu.

Ve výchozím nastavení může mít až 2 koncové body streamování ve účtu Media Services. Vyšší omezení počtu požadavků, najdete v článku [kvóty a omezení](media-services-quotas-and-limitations.md).

Nebudete dostávat faktury, pokud je vaše StreamingEndpoint ve spuštěném stavu.

### <a name="asset-delivery-policy"></a>Zásady doručení assetu
Jedním z kroků v pracovním postupu doručování obsahu v Media Services konfiguruje [zásady doručování prostředků ](https://docs.microsoft.com/rest/api/media/operations/assetdeliverypolicy), kterou chcete Streamovat. Zásady doručení assetu říká Media Services způsob pro váš prostředek, který bude doručen: do datových proudů protokol, který by měl váš asset dynamicky balí (pro příklad, MPEG DASH, HLS, technologie Smooth Streaming nebo všechny), jestli chcete šifrovat dynamicky váš asset a jak (Obálka nebo používat standard common encryption).

Pokud máte úložiště šifrované asset, před Streamovat prostředek, server streamování zruší šifrování úložiště a streamuje obsah pomocí zadaného doručování zásad. Například dodávat váš asset zašifrovaný pomocí klíče pro šifrování Advanced Encryption (Standard AES), nastavte typ zásad DynamicEnvelopeEncryption. Pokud chcete odstranit úložiště šifrování a streamování majetku v nezašifrované podobě, nastavte typ zásad k NoDynamicEncryption.

### <a name="progressive-download"></a>Progresivní stahování
Progresivní stahování umožňuje spustit přehrávání médií předtím, než byly staženy celý soubor. Můžete pouze progresivně stahovat soubor MP4.

>[!NOTE]
>Šifrované prostředky musí dešifrovat, pokud chcete, aby pro ně k dispozici pro progresivní stahování.

Uživatelům poskytnout adresa URL progresivního stahování, nejprve musíte vytvořit lokátor OnDemandOrigin. Vytváření Lokátor, obsahuje základní cestu k assetu. Pak budete muset přidat název souboru MP4. Příklad:

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### <a name="streaming-urls"></a>Adresy URL pro streamování
Streamování vašeho obsahu pro klienty. Uživatelům poskytnout adresy URL pro streamování, nejprve musíte vytvořit lokátor OnDemandOrigin. Vytváření Lokátor, obsahuje základní cestu k prostředku, který obsahuje obsah, který chcete Streamovat. Však Streamovat tento obsah budete muset upravit další tuto cestu. Pokud chcete vytvořit úplnou adresu URL k souboru manifestu datových proudů, musí zřetězit hodnotu lokátoru cesty a manifest (filename.ism) název souboru. Potom připojí k lokátoru cesty volbu/manifest a příslušném formátu (v případě potřeby).

Také můžete Streamovat obsah pomocí připojení SSL. Chcete-li to provést, zkontrolujte, zda že vaší adresy URL pro streamování začínat HTTPS. V současné době nepodporuje AMS SSL s použitím vlastních domén.  

>[!NOTE]
>Můžete pouze Streamovat přes protokol SSL Pokud koncový bod streamování, ze kterého můžete doručovat obsah byl vytvořen po 10. září 2014. Pokud vaše adresy URL pro streamování jsou založené na koncových bodech streamování vytvořené po 10. září, adresa URL obsahuje "streaming.mediaservices.windows.net" (nový formát). Adresy URL pro streamování, které obsahují "origin.mediaservices.windows.net" (starý formát) nepodporují SSL. Pokud vaše adresa URL používá starý formát a chcete Streamovat přes protokol SSL, vytvořte nový koncový bod streamování. Použití adres URL založeno na nový koncový bod streamování pro streamování vašeho obsahu přes protokol SSL.

Následující seznam popisuje různé formáty streamování a uvádí příklady:

* Technologie Smooth Streaming

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

* MPEG DASH

{streamování koncový bod služby media název účtu name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

* Apple HTTP Live Streaming (HLS) V4

{streamování koncový bod služby media název účtu name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

* Apple HTTP Live Streaming (HLS) V3

{streamování koncový bod služby media název účtu name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

