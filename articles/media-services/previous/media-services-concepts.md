---
title: Koncepty Azure Media Services | Microsoft Docs
description: Toto téma nabízí přehled Azure Media Services konceptů
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: dcefc8bc-e2ea-4b38-a643-9010f4436fb5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: juliako
ms.openlocfilehash: cfc8ac74f626ada30dda72b58e4b4bc7f0d9e806
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="azure-media-services-concepts"></a>Koncepty Azure Media Services
Toto téma poskytuje přehled konceptů nejdůležitější Media Services.

## <a name="a-idassetsassets-and-storage"></a><a id="assets"/>Prostředky a úložiště
### <a name="assets"></a>Prostředky
[Asset](https://docs.microsoft.com/rest/api/media/operations/asset) obsahuje digitální soubory (včetně video, zvuk, obrázky, kolekci miniatur, textové stopy a soubory titulků) a metadata o těchto souborech. Po digitální soubory jsou odeslány do assetu, mohou být využívána ve službě Media Services kódování a vysílání datového proudu pracovních postupů.

Prostředek je namapovaná na kontejner objektů blob v účtu úložiště Azure a soubory v prostředku jsou uloženy jako objekty BLOB bloku v tomto kontejneru. Objekty BLOB stránky nejsou podporovány službou Azure Media Services.

Při rozhodování, jakou mediální obsah nahrát a uložit v prostředek, platí následující aspekty:

* Prostředek by měl obsahovat pouze jeden, jedinečné instance mediální obsah. Například jednom upravit nástroje televizního díl, videa nebo oznámení o inzerovaném programu.
* Prostředek nesmí obsahovat více interpretace nebo úpravy audiovizuálních souborů. Příkladem nesprávné použití prostředek by se pokusit o ukládání více než jeden díl TV, oznámení o inzerovaném programu nebo více úhlů z jediné provozní uvnitř prostředek. Ukládání více interpretace nebo úpravy audiovizuálních souborů v prostředek může mít za následek potíží při odesílání úlohy kódování, streamování a zabezpečení doručení assetu později v pracovním postupu.  

### <a name="asset-file"></a>Souboru assetu
[AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) představuje skutečný video nebo zvuk soubor, který je uložen v kontejneru objektů blob. Soubor asset je vždy přidružena k assetu a prostředek může obsahovat jeden nebo mnoho souborů. Media Services Encoder úloh selže, pokud objekt souboru asset není spojen s digitálnímu souboru v kontejneru objektů blob.

**AssetFile** instance a samotný mediální soubor jsou dva odlišné objekty. AssetFile instance obsahuje metadata o souboru média, zatímco souboru média obsahuje samotný mediální obsah.

Chcete-li změnit obsah kontejnery objektů blob, které byly vygenerovány Media Services bez použití rozhraní API služby Media byste neměli.

### <a name="asset-encryption-options"></a>Možnosti šifrování Asset
V závislosti na typu obsahu, který chcete nahrát, ukládání a poskytovat služba Media Services poskytuje různé možnosti šifrování, které lze vybírat.

>[!NOTE]
>Nepoužívá se žádné šifrování. Toto je výchozí hodnota. Při použití této možnosti není váš obsah chráněný během přenosu ani umístěná v úložišti.

Pokud chcete dodávat obsah ve formátu MP4 pomocí progresivního stahování, použijte tato možnost k nahrát obsah.

**StorageEncrypted** – tato možnost slouží k šifrování obsahu místně pomocí šifrování AES 256 bitů a nahrajte ho do Azure Storage kde bude uložený v zašifrované podobě. Prostředky chráněné pomocí šifrování úložiště jsou automaticky bez šifrování umístěny do systému souborů EFS před kódování a volitelně se znovu zašifrují před jejich odesláním zpět v podobě nového výstupního prostředku. Případem primárního použití šifrování úložiště je, když chcete zabezpečit vysoké kvality souborů vstupními médii pomocí silného šifrování v klidovém stavu na disku. 

Aby bylo možné poskytovat asset šifrované úložiště, musíte nakonfigurovat zásady doručení assetu, aby věděl Media Services může způsob doručení obsahu. Před asset Streamovat, server datových proudů odebere šifrování úložiště a datové proudy svůj obsah pomocí zadaného doručování zásad (například AES, PlayReady nebo žádné šifrování). 

**CommonEncryptionProtected** – tuto možnost použijte, pokud chcete zašifrovat (nebo nahrát už šifrovaný) obsah s běžným šifrováním nebo DRM s technologií PlayReady (například technologie Smooth Streaming chráněná pomocí DRM s technologií PlayReady).

**EnvelopeEncryptionProtected** – tuto možnost použijte, pokud chcete chránit (nebo odešlete, již chráněny) HTTP Live Streaming (HLS) zašifrovaná pomocí šifrování AES (Advanced Standard). Pokud odesíláte HLS již šifrováním pomocí standardu AES, se musí být šifrována pomocí Správce transformací.

### <a name="access-policy"></a>Zásada přístupu
[AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) definuje oprávnění (např. čtení, zápisu a seznamu) a doba trvání přístupu pro prostředek. Objekt AccessPolicy by obvykle předat lokátoru, která se pak použije pro přístup k souborům, které jsou obsaženy v assetu.

>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Pokud vždy používáte stejné dny / přístupová oprávnění, například zásady pro lokátory, které mají zůstat na místě po dlouhou dobu (zásady bez odeslání), měli byste použít stejné ID zásad. Další informace najdete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) tématu.

### <a name="blob-container"></a>Kontejner objektů BLOB
Kontejner objektů blob zajišťuje seskupení sady objektů BLOB. Kontejnery objektů blob se ve službě Media Services používají jako hranice bod pro řízení přístupu a lokátory sdíleného přístupového podpisu (SAS) na prostředky. Účet úložiště Azure může obsahovat neomezený počet kontejnerů objektů blob. Kontejner můžete pojmout neomezený počet objektů blob.

>[!NOTE]
> Chcete-li změnit obsah kontejnery objektů blob, které byly vygenerovány Media Services bez použití rozhraní API služby Media byste neměli.
> 
> 

### <a name="a-idlocatorslocators"></a><a id="locators"/>Lokátory
[Lokátor](https://docs.microsoft.com/rest/api/media/operations/locator)s vytvořit vstupní bod pro přístup k souborům obsaženým ve prostředek. Zásady přístupu se používá k definování oprávnění a doba trvání, že má klient přístup pro daný prostředek. Lokátory může mít mnoho relací s zásady přístupu, tak, že jiný lokátory poskytnete časy různou počáteční a typy připojení různých klientů při všech pomocí stejné oprávnění a nastavení doby trvání; z důvodu omezení zásady sdíleného přístupu nastavit pomocí služby Azure storage, ale nemůže mít více než pět jedinečný lokátory spojené s danou asset najednou. 

Služba Media Services podporuje dva typy lokátorů: ondemandorigin, používaný ke streamování médií (například MPEG DASH, HLS nebo technologie Smooth Streaming) nebo progresivně stahovat médií a SAS adresa URL, používá k odeslání nebo stažení média soubory to\from úložiště Azure. 

>[!NOTE]
>Seznam oprávnění (AccessPermissions.List) by neměla být použita při vytváření Lokátor OnDemandOrigin. 

### <a name="storage-account"></a>Účet úložiště
Veškerý přístup do služby Azure Storage se provádí prostřednictvím účtu úložiště. Účet Media Service můžete přidružit jeden nebo více účtů úložiště. Účet může obsahovat neomezený počet kontejnerů, tak dlouho, dokud jejich celková velikost je v části 500TB na účet úložiště.  Služba Media Services poskytuje SDK úrovně nástrojů a umožní vám ke správě více účtů úložiště a distribuci vaše prostředky během nahrávání do těchto účtů na základě metriky nebo náhodné distribuční Vyrovnávání zatížení. Další informace najdete v tématu práci s [Azure Storage](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

## <a name="jobs-and-tasks"></a>Úlohy a úkoly
A [úlohy](https://docs.microsoft.com/rest/api/media/operations/job) se obvykle používá k procesu (například index nebo kódování) jeden prezentace zvuku a videa. Při zpracování více videa, vytvořte úlohu pro každý video kódovaný.

Úloha obsahuje metadata týkající se zpracování provést. Každá úloha obsahuje jeden nebo více [úloh](https://docs.microsoft.com/rest/api/media/operations/task)s určeným atomic zpracování úloh, jeho vstupní prostředky výstupní prostředky, procesor médií a jeho přidružené nastavení. Úlohy v rámci úlohy dají se propojit, kde je zadána výstupní asset úkolů jako vstupní asset dalšího kroku. Tímto způsobem může obsahovat jednu úlohu všechny potřebné pro prezentaci média zpracování.

## <a id="encoding"></a>Kódování
Azure Media Services poskytuje několik možností kódování média v cloudu.

Při spouštění pomocí služby Media Services, je důležité si uvědomit rozdíl mezi formáty kodeků a souboru.
Kodeky jsou software, který implementuje kompresi nebo dekompresi algoritmy, zatímco formáty souborů jsou kontejnery, které obsahují komprimované video.

Služba Media Services poskytuje dynamické balení, což vám umožní dodávat váš obsah s adaptivní přenosovou rychlostí s kódováním MP4 nebo technologie Smooth Streaming ve formátech streamování podporovaných službou Media Services (MPEG DASH, HLS, technologie Smooth Streaming), aniž byste je museli znovu zabalit do těchto formátů streamování.

Chcete využít výhod [dynamické balení](media-services-dynamic-packaging-overview.md), budete muset zakódovat váš soubor mezzanine (zdrojový) do sady souborů MP4 nebo soubory technologie Smooth Streaming s adaptivní přenosovou rychlostí a mít alespoň jeden standard nebo premium koncový bod streamování v Spustit stavu.

Služba Media Services podporuje následující kodéry na vyžádání, které jsou popsané v tomto článku:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Pracovní postup kodéru Media Encoder Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Informace o podporovaných kodéry najdete v tématu [kodéry](media-services-encode-asset.md).

## <a name="live-streaming"></a>Živé streamování
Ve službě Azure Media Services kanál, který představuje kanálu pro zpracování obsahu živého streamování. Kanál, který obdrží živé vstupní datové proudy v jednom ze dvou způsobů:

* Místní kodér za provozu odešle více přenosovými rychlostmi RTMP nebo technologie Smooth Streaming (fragmentovaný soubor MP4) do kanálu. Můžete použít následující kodéry, které výstupu technologie Smooth Streaming více přenosovými rychlostmi: MediaExcel, Ateme, představte si komunikace, Envivio, Cisco a Elemental. Následující kodéry výstupu RTMP: Adobe Flash kodéru služby Live, Telestream Wirecast, Teradek, Haivision a čase kodérů. Ingestované datové proudy prochází kanály bez kódování a překódování žádné další. Služba Media Services doručí datový proud zákazníkům na vyžádání.
* Datový proud s jednou přenosovou rychlostí (v jednom z následujících formátů: RTP (MPEG-TS)), RTMP nebo technologie Smooth Streaming (fragmentovaný soubor MP4)) se odesílá do kanálu, který má povolené kódování v reálném čase pomocí služby Media Services provádět. Kanál potom provede kódování v reálném čase pro příchozí datový proud s jednou přenosovou rychlostí v reálném čase na datový proud videa s více přenosovými rychlostmi (adaptivní). Služba Media Services doručí datový proud zákazníkům na vyžádání.

### <a name="channel"></a>Kanál
Ve službě Media Services [kanál](https://docs.microsoft.com/rest/api/media/operations/channel)s jsou zodpovědná za zpracování obsahu živého streamování. Kanál, který obsahuje vstupní koncový bod (ingestovanou adresu URL), pak poskytnete za provozu převaděč. Kanál přijímá živé vstupní datové proudy ze za provozu převaděč a zpřístupní se k vysílání datového proudu prostřednictvím jeden nebo více koncové body streamování. Kanály taky zadat koncový bod preview (URL náhledu), který používáte pro zobrazení náhledu a ověřit před další zpracování a doručení datového proudu.

Když vytvoříte kanál, mohou získat adresu URL ingestování a URL náhledu. Pokud chcete získat tyto adresy URL, kanál nemá být ve stavu spuštěna. Pokud jste připravení začít s nabízet data z provozu převaděč do kanálu, je nutné spustit kanál. Jakmile se převaděč za provozu spustí příjem dat, můžete zobrazit náhled datového proudu.

Každý účet Media Services může obsahovat více typů komunikačních kanálů, více programů a více koncové body streamování. Podle potřeb šířky pásma a zabezpečení může být vyhrazený StreamingEndpoint služby pro jeden nebo více kanálů. Všechny StreamingEndpoint můžete načítat z jakékoli kanálu.

### <a name="program-event"></a>Program (událost)
A [programu (událost)](https://docs.microsoft.com/rest/api/media/operations/program) vám umožňuje řídit publikování a ukládání segmentů v živém datovém proudu. Kanály spravují programy (událostí). Vztah kanálů a programů je podobná tradičním médiím, kde kanál obsahuje nepřetržitý datový proud obsahu a program je vymezen na určité načasované události v tomto kanálu.
Můžete určit počet hodin, které chcete uchovávat zaznamenaný obsah programu nastavením **ArchiveWindowLength** vlastnost. Tuto hodnotu můžete nastavit v rozmezí od 5 minut po 25 hodin.

ArchiveWindowLength také určuje že maximální množství času klientů můžete prohledávat směrem zpět v čase od aktuální živé pozice. Programy můžou běžet po určenou dobu a obsah, který se do délky okna nevejde, bude vždy zahozen. Hodnota této vlastnosti také určuje, jak dlouho můžou růst manifesty klientů.

Každý program (událost) je přidružena k Assetu. Chcete-li publikovat program musíte vytvořit lokátor pro přidružený asset. Tento lokátor vám umožní sestavit adresu URL pro streamování, kterou potom poskytnete svým klientům.

Kanál podporuje až tři současně spuštěné programy, takže si můžete vytvořit několik archivů stejného příchozího datového proudu. To vám umožní podle potřeby publikovat a archivovat různé části události. Požadavek vaší firmy může být například takový, že chcete archivovat 6 hodin programu, ale vysílat jenom posledních 10 minut. K tomu potřebujete vytvořit dva současně spuštěné programy. Jeden program nastavíte, aby archivoval 6 hodin události, ale tento program nebudete publikovat. Druhý program nastavíte, aby archivoval 10 minut a tento program budete publikovat.

Další informace naleznete v tématu:

* [Práce s kanály, které mají povolené kódování v reálném službou Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Práce s kanály, které přijímají živé datové proudy s více přenosovými rychlostmi z místních kodérů](media-services-live-streaming-with-onprem-encoders.md)
* [Kvóty a omezení](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>Ochrana obsahu
### <a name="dynamic-encryption"></a>Dynamické šifrování
Azure Media Services umožňuje zabezpečit médiu od okamžiku, kdy by poté počítač prostřednictvím úložiště, zpracování a doručení. Služba Media Services vám umožní dodávat váš obsah dynamicky šifrován Standard AES (Advanced Encryption) (s použitím 128bitové šifrování klíče) a běžné šifrování (CENC) pomocí PlayReady nebo Widevine DRM. Služba Media Services také poskytuje službu k doručování klíčů AES a licence technologie PlayReady autorizovaným klientům.

V současné době můžete šifrovat formátech streamování: HLS, MPEG DASH a technologie Smooth Streaming. Nelze zašifrovat progresivní stahování.

Pokud chcete použít pro Media Services k šifrování prostředek, budete muset přidružit asset šifrovací klíč (CommonEncryption nebo EnvelopeEncryption) a taky nakonfigurovat zásady autorizace pro klíč.

Pokud chcete k vysílání datového proudu šifrované asset úložiště, musíte nakonfigurovat zásady doručení assetu a zadejte, jak chcete poskytovat asset.

Datový proud je žádost přehrávač, Media Services používá k zadanému klíči dynamicky šifrovat obsah pomocí obálky šifrování (pomocí standardu AES) nebo běžné šifrování (PlayReady nebo Widevine). K dešifrování datového proudu, bude přehrávač požadovat klíč ze služby doručení klíče. Při rozhodování, zda je uživatel oprávnění k získání klíče, služba vyhodnocuje zásady autorizace, které jste zadali pro klíč.

### <a name="token-restriction"></a>Omezení s tokenem
Zásady autorizace klíče obsahu může mít jeden nebo více omezení autorizace: otevřete, token omezení nebo omezení IP adres. Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Služba Media Services podporuje tokeny ve formátu jednoduché webové tokeny (SWT) a formátu JSON Web Token (JWT). Služba Media Services neposkytuje zabezpečení tokenu služby. Můžete vytvořit vlastní službu tokenů zabezpečení nebo využívat Microsoft Azure ACS problém tokeny. Služba tokenů zabezpečení musí být nakonfigurované vytvořit token podepsané zadaný klíč a vystavování deklarací identity, které jste zadali v nastavení omezení s tokenem. Služba Media Services doručení klíče vrátí požadovaný klíč (nebo licencí) do klienta, pokud je token platný a deklarace identity v tokenu shody ty nakonfigurované pro klíč (nebo licencí).

Při konfiguraci token omezený zásad, musíte zadat klíč primární ověřování, vystavitele a cílová skupina parametry. Ověření primární klíč obsahuje klíč, který byl podepsaný token, Vystavitel je zabezpečený tokenu služba, která vydá token. Cílová skupina (někdy nazývané oboru) popisuje záměr tokenu nebo prostředek token povolí přístup k. Služba Media Services doručení klíče ověří, jestli tyto hodnoty v tokenu shodují s hodnotami v šabloně.

Další informace najdete v následujících článcích:
- [Ochrana obsahu – přehled](media-services-content-protection-overview.md)
- [Chránit pomocí standardu AES-128](media-services-protect-with-aes128.md)
- [Chránit pomocí PlayReady nebo Widevine](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Doručování
### <a name="a-iddynamicpackagingdynamic-packaging"></a><a id="dynamic_packaging"/>Dynamické balení
Při práci se službou Media Services, doporučuje se zakódovat váš soubor mezzanine soubory do sady souborů MP4 adaptivní přenosovou rychlostí a pak sadu převést na požadovaný formát pomocí [dynamické balení](media-services-dynamic-packaging-overview.md).

### <a name="streaming-endpoint"></a>Koncový bod streamování
StreamingEndpoint představuje streamování služba, která může poskytnout obsah přímo k aplikaci player klienta nebo do obsah Delivery Network (CDN) pro další distribuci (Azure Media Services nyní poskytuje integraci Azure CDN.) Výstupní datový proud z datových proudů koncový bod služby může být živý datový proud nebo Asset videa na vyžádání ve vašem účtu Media Services. Zákazníci Media Services si podle svých potřeb zvolí buď koncový bod **Standard**, nebo jeden nebo několik koncových bodů streamování **Premium**. Koncový bod streamování Standard je vhodná pro většinu úloh streamování. 

Koncový bod streamování Standard je vhodný pro většinu streamovacích úloh. Standardní koncové body streamování nabízejí flexibilitu při doručování obsahu na téměř každé zařízení prostřednictvím dynamické balení do HLS, MPEG-DASH, technologie Smooth Streaming jakož a dynamického šifrování Microsoft PlayReady, Google Widevine, Apple Fairplay a ALGORITMU AES128 ZA POMOCI.  Také škálovat z velmi malé na velmi velké cílové skupiny s tisíci souběžných divákům prostřednictvím integrace Azure CDN. Pokud máte pokročilé úlohy streamování požadavků na kapacitu nehodí pro standardní streamování cíle propustnost koncového bodu nebo chcete řídit kapacita StreamingEndpoint služby pro zpracování narůstají musí šířky pásma, doporučuje se Přidělte jednotek škálování (také označované jako premium streamování jednotky).

Doporučujeme použít dynamické balení a/nebo dynamické šifrování.

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 

Další informace najdete v [tomto](media-services-portal-manage-streaming-endpoints.md) tématu.

Ve výchozím nastavení může mít až 2 koncové body v účtu Media Services streamování. Vyšší limit požadavku, najdete v tématu [kvóty a omezení](media-services-quotas-and-limitations.md).

Fakturuje se jenom, když vaše StreamingEndpoint je v běžícím stavu.

### <a name="asset-delivery-policy"></a>Zásady doručení assetu
Jeden z kroků v pracovním postupu doručování obsahu Media Services je konfigurace [zásady doručení pro prostředky ](https://docs.microsoft.com/rest/api/media/operations/assetdeliverypolicy), kterou chcete Streamovat. Zásady doručení assetu informuje Media Services, jak chcete použít pro váš asset, který bude doručen: do které protokol pro streamování by měl váš asset dynamicky zabalené (pro příklad, MPEG DASH, HLS, technologie Smooth Streaming nebo všechny), zda chcete dynamicky šifrovat. váš asset a jak (obálky nebo common encryption).

Pokud máte úložiště šifrované prostředek, než Streamovat asset, server datových proudů odebere šifrování úložiště a datové proudy svůj obsah pomocí zadaného doručování zásad. Například doručení asset šifrován Advanced Encryption (Standard AES) šifrovací klíč, nastavte typ zásad DynamicEnvelopeEncryption. Chcete-li odebrat šifrování úložiště stream asset v nešifrované podobě, nastavte typ zásad k NoDynamicEncryption.

### <a name="progressive-download"></a>Progresivní stahování
Progresivní stahování umožňuje spustit přehrávání média, než byl stažen celý soubor. Můžete pouze progresivně stáhnout soubor MP4.

>[!NOTE]
>Šifrované prostředky musí dešifrovat, pokud chcete pro ně k dispozici pro progresivní stahování.

Adresa URL progresivního stahování poskytují uživatelům, nejprve musíte vytvořit lokátor OnDemandOrigin. Vytvořením Lokátor, získáte základní cesta pro daný prostředek. Pak budete muset přidat název souboru MP4. Příklad:

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### <a name="streaming-urls"></a>Adresy URL pro streamování
Streamování svůj obsah do klientů. Chcete-li uživatelům poskytnout adresy URL pro streamování, nejprve musíte vytvořit lokátor OnDemandOrigin. Vytvořením Lokátor, získáte základní cesta pro daný prostředek, který obsahuje obsah, který chcete k vysílání datového proudu. Však být schopni stream tohoto obsahu musíte upravte tuto cestu další. Chcete-li vytvořit úplnou adresu URL k souboru manifestu streamování, musí řetězení hodnota Lokátor cesty a manifest (filename.ism) název souboru. Pak připojí k lokátoru cesty /Manifest a odpovídajícím formátu (v případě potřeby).

Můžete také Streamovat obsah pomocí připojení SSL. K tomu, ujistěte se, že vaše streamování adresy URL začínat HTTPS. V současné době nepodporuje AMS SSL s vlastní domény.  

>[!NOTE]
>Pouze proudy přes protokol SSL Pokud koncový bod streamování, ze kterého doručení obsahu byl vytvořen po 10. září 2014. Jsou-li u koncových bodů streamování vytvořený po 10 září adresám URL streamování, adresa URL obsahuje "streaming.mediaservices.windows.net" (nový formát). Streamování adresy URL, které obsahují "origin.mediaservices.windows.net" (starý formát) nepodporují SSL. Pokud je adresa URL v původním formátu a chcete Streamovat přes protokol SSL, vytvořte nový koncový bod streamování. Pomocí adresy URL na nový koncový bod streamování základě vytvořit Streamovat obsah přes protokol SSL.

Následující seznam popisuje různých formátech streamování a uvádí příklady:

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

