---
title: Pojmy Azure Media Services | Microsoft Docs
description: Tento článek poskytuje stručný přehled Microsoft Azure Media Services konceptů a odkazy na další články, které vám pomají podrobnosti.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: fbe387b3f86dfd10cbc4e80a02ee2528e43c4b67
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016878"
---
# <a name="azure-media-services-concepts"></a>Azure Media Services koncepty

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-v-2-v-3-migration-introduction.md) .

Toto téma poskytuje přehled nejdůležitějších Media Services konceptů.

## <a name="assets-and-storage"></a><a name="assets"></a>Prostředky a úložiště
### <a name="assets"></a>Prostředky
[Asset](/rest/api/media/operations/asset) obsahuje digitální soubory (včetně videa, zvuku, obrázků, kolekcí miniatur, textových stop a skrytých souborů titulků) a metadat těchto souborů. Po nahrání digitálních souborů do assetu se dají použít v pracovních postupech pro kódování a streamování Media Services.

Asset se namapuje na kontejner objektů BLOB v účtu Azure Storage a soubory v prostředku se ukládají jako objekty blob bloku v tomto kontejneru. Azure Media Services nepodporuje objekty blob stránky.

Při rozhodování o tom, jaký mediální obsah se má nahrát a uložit v assetu, platí následující požadavky:

* Prostředek by měl obsahovat jenom jednu jedinečnou instanci mediálního obsahu. Například jedna úprava TELEVIZNÍho dílu, videa nebo reklamy.
* Asset by neměl obsahovat více verzí nebo úprav audiovizuálního souboru. Jedním z příkladů nesprávného využití Assetu by se při pokusu o uložení více než jednoho TELEVIZNÍho dílu, inzerce nebo několika úhlů kamery z jedné výroby v rámci assetu. Uložení více verzí nebo úprav audiovizuálního souboru v prostředku může vést k potížím při odesílání úloh kódování, streamování a zabezpečení doručení assetu později v pracovním postupu.  

### <a name="asset-file"></a>Soubor assetu
[AssetFile](/rest/api/media/operations/assetfile) představuje skutečný videosoubor nebo zvukový soubor, který je uložený v kontejneru objektů BLOB. Soubor prostředků je vždy přidružen k assetu a Asset může obsahovat jeden nebo více souborů. Úloha Media Services Encoder se nezdařila v případě, že objekt souboru prostředků není přidružen k digitálnímu souboru v kontejneru objektů BLOB.

Instance **AssetFile** a skutečný mediální soubor jsou dva odlišné objekty. Instance AssetFile obsahuje metadata o souboru média, zatímco soubor média obsahuje skutečný mediální obsah.

Neměli byste se pokoušet změnit obsah kontejnerů objektů BLOB generovaných Media Services bez použití rozhraní API služby Media Service.

### <a name="asset-encryption-options"></a>Možnosti šifrování prostředků
V závislosti na typu obsahu, který chcete nahrát, ukládat a doručovat, Media Services poskytuje různé možnosti šifrování, ze kterých si můžete vybrat.

>[!NOTE]
>Nepoužívá se žádné šifrování. Toto je výchozí hodnota. Když použijete tuto možnost, váš obsah se nechrání během přenosu nebo v klidovém úložišti.

Pokud plánujete doručení MP4 pomocí postupného stahování, použijte tuto možnost k nahrání obsahu.

**StorageEncrypted** – tuto možnost použijte k místnímu šifrování nešifrovaného obsahu pomocí šifrování AES 256 a pak ho nahrajte do Azure Storage tam, kde je uložený zašifrovaný v klidovém stavu. Prostředky chráněné s šifrováním úložiště se před kódováním automaticky nešifrují a ukládají do systému souborů EFS a volitelně se znovu zašifrují předtím, než se znovu nahraje jako nový výstupní prostředek. Primárním případem použití šifrování úložiště je, že chcete zabezpečit vysoce kvalitní vstupní mediální soubory se silným šifrováním v klidovém provozu na disku. 

Aby bylo možné doručovat šifrovaný prostředek úložiště, je nutné nakonfigurovat zásady doručení assetu, aby Media Services ví, jak chcete obsah doručovat. Předtím, než bude možné Asset streamovat, server streamování odstraní šifrování úložiště a streamuje obsah pomocí zadaných zásad doručování (například AES, PlayReady nebo bez šifrování). 

**CommonEncryptionProtected** – tuto možnost použijte, pokud chcete šifrovat (nebo nahrávat již zašifrovaný) obsah pomocí Common Encryption nebo PlayReady DRM (například Smooth Streaming chráněná pomocí technologie PlayReady DRM).

**EnvelopeEncryptionProtected** – tuto možnost použijte, pokud chcete chránit (nebo odeslat již chráněný) http Live Streaming (HLS) šifrovaný pomocí standard AES (Advanced Encryption Standard) (AES). Pokud odesíláte HLS již zašifrované pomocí AES, musí být zašifrováno pomocí nástroje pro transformaci.

### <a name="access-policy"></a>Zásady přístupu
[AccessPolicy](/rest/api/media/operations/accesspolicy) definuje oprávnění (například čtení, zápis a výpis) a dobu trvání přístupu k assetu. Obvykle byste předávali objekt AccessPolicy do lokátoru, který by pak měl být použit pro přístup k souborům obsaženým v prostředku.

>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Pokud vždy používáte stejné dny / přístupová oprávnění, například zásady pro lokátory, které mají zůstat na místě po dlouhou dobu (zásady bez odeslání), měli byste použít stejné ID zásad. Další informace najdete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) tématu.

### <a name="blob-container"></a>Kontejner objektů blob
Kontejner objektů BLOB poskytuje seskupení sady objektů BLOB. Kontejnery objektů BLOB se používají v Media Services jako hraniční bod pro řízení přístupu a Lokátory sdíleného přístupového podpisu (SAS) u prostředků. Účet Azure Storage může obsahovat neomezený počet kontejnerů objektů BLOB. Kontejner můžete pojmout neomezený počet objektů blob.

>[!NOTE]
> Neměli byste se pokoušet změnit obsah kontejnerů objektů BLOB generovaných Media Services bez použití rozhraní API služby Media Service.
> 
> 

### <a name="locators"></a><a name="locators"></a>Lokátory
[Lokátor](/rest/api/media/operations/locator)s poskytuje vstupní bod pro přístup k souborům obsaženým v prostředku. Zásady přístupu slouží k definování oprávnění a doby trvání, ke kterým má klient přístup k danému prostředku. Lokátory můžou mít k zásadě přístupu relaci n:n, takže různé Lokátory můžou poskytovat různé časy spuštění a typy připojení různým klientům, zatímco všichni používají stejná oprávnění a nastavení doby trvání. vzhledem k omezení zásad sdíleného přístupu nastaveným službami Azure Storage ale nemůžete najednou mít k danému prostředku přidružené více než pět jedinečných lokátorů. 

Media Services podporuje dva typy lokátorů: OnDemandOrigin Lokátory, které se používají ke streamování médií (například MPEG POMLČKy, HLS nebo Smooth Streaming) nebo postupně stahovat Lokátory adres URL médií a SAS, které slouží k nahrávání nebo stahování mediálních souborů to\from Azure Storage. 

>[!NOTE]
>Oprávnění list (AccessPermissions. list) by se nemělo používat při vytváření lokátoru OnDemandOrigin. 

### <a name="storage-account"></a>Účet úložiště
Veškerý přístup k Azure Storage se provádí prostřednictvím účtu úložiště. Účet Media Service může přidružit jeden nebo více účtů úložiště. Účet může obsahovat neomezený počet kontejnerů, pokud je jeho celková velikost pod 500TBem na účet úložiště.  Media Services poskytuje nástroje na úrovni sady SDK, které vám umožní spravovat více účtů úložiště a vyrovnávat zatížení při nahrávání prostředků do těchto účtů na základě metrik nebo náhodné distribuce. Další informace najdete v tématu práce s [Azure Storage](/previous-versions/azure/dn767951(v=azure.100)). 

## <a name="jobs-and-tasks"></a>Úlohy a úkoly
[Úloha](/rest/api/media/operations/job) se obvykle používá ke zpracování jedné prezentace audio/video (například indexování nebo kódování). Pokud zpracováváte více videí, vytvořte úlohu pro kódování každého videa.

Úloha obsahuje metadata o zpracování, která se mají provést. Každá úloha obsahuje jednu nebo více [úloh](/rest/api/media/operations/task), které určují úlohu atomické zpracování, její vstupní prostředky, výstupní prostředky, procesor médií a jeho přidružená nastavení. Úlohy v rámci úlohy lze zřetězit společně, kde výstupní prostředek jedné úlohy je uveden jako vstupní Asset pro další úkol. V tomto způsobu může jedna úloha obsahovat veškeré zpracování potřebné pro prezentaci multimédií.

## <a name="encoding"></a><a id="encoding"></a>Encoding
Azure Media Services poskytuje více možností pro kódování médií v cloudu.

Když začnete s Media Services, je důležité pochopit rozdíl mezi kodeky a formáty souborů.
Kodeky jsou software, který implementuje algoritmy komprese/dekomprese, zatímco formáty souborů jsou kontejnery, které obsahují komprimované video.

Media Services poskytuje dynamické balení, které vám umožní doručovat obsah MP4 s adaptivní přenosovou rychlostí nebo Smooth Streaming kódovaný obsah ve formátech streamování podporovaných Media Services (MPEG POMLČKa, HLS, Smooth Streaming), aniž byste je museli znovu zabalit do těchto formátů streamování.

Pokud chcete využít výhod [dynamického balení](media-services-dynamic-packaging-overview.md), musíte soubor Mezzanine (zdrojový soubor) kódovat do sady souborů MP4 s adaptivní přenosovou rychlostí nebo s adaptivní přenosovou rychlostí Smooth Streaming soubory a mít aspoň jeden koncový bod streamování Standard nebo Premium ve stavu spuštěno.

Media Services podporuje následující kodér na vyžádání, který je popsaný v tomto článku:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)

Informace o tomto podporovaném kodéru naleznete v tématu [Encoder](media-services-encode-asset.md).

## <a name="live-streaming"></a>Živé streamování
V Azure Media Services kanál představuje kanál pro zpracování obsahu živého streamování. Kanál přijímá živé vstupní proudy jedním ze dvou způsobů:

* On-premises Live Encoder posílá do kanálu RTMP s více přenosovými rychlostmi nebo Smooth Streaming (fragmentovaný MP4). Můžete použít následující živé kodéry, které mají výstup s více přenosovými rychlostmi Smooth Streaming: MediaExcel, Ateme, představovat komunikaci, Envivio, Cisco a prvky. Následující kodéry pro výstup v reálném čase: Kodér Adobe Flash Live Encoder, [Wirecast](media-services-configure-wirecast-live-encoder.md), Teradek, Haivision Encoders. Ingestované datové proudy procházejí kanálem bez dalšího překódování a kódování. Služba Media Services doručí datový proud zákazníkům na vyžádání.
* Datový proud s jednou přenosovou rychlostí (v jednom z následujících formátů: RTMP nebo Smooth Streaming (fragmentovaný MP4)) se pošle do kanálu, který má povolené kódování v reálném čase pomocí Media Services. Kanál potom provede kódování v reálném čase pro příchozí datový proud s jednou přenosovou rychlostí v reálném čase na datový proud videa s více přenosovými rychlostmi (adaptivní). Služba Media Services doručí datový proud zákazníkům na vyžádání.

### <a name="channel"></a>Kanál
V Media Services [kanál](/rest/api/media/operations/channel)s zodpovídá za zpracování obsahu živého streamování. Kanál poskytuje vstupní koncový bod (adresa URL pro příjem), který pak poskytnete pro živý překódování. Kanál přijímá živé vstupní proudy z živého překódování a zpřístupňuje je pro streamování prostřednictvím jednoho nebo více starají. Kanály také poskytují koncový bod verze Preview (adresa URL náhledu), který používáte k zobrazení náhledu a ověření datového proudu před dalším zpracováním a doručením.

Při vytváření kanálu můžete získat adresu URL pro ingestování a adresu URL náhledu. Aby se tyto adresy URL dostaly, kanál se nemusí nacházet ve stavu spuštěno. Až budete připraveni začít nahrávat data z živého přesílaného do kanálu, je nutné spustit kanál. Jakmile živý převedený kód začne ingestovat data, můžete zobrazit náhled streamu.

Každý účet Media Services může obsahovat více kanálů, více programů a několik starají. V závislosti na potřebách šířky pásma a zabezpečení může být služba StreamingEndpoint vyhrazená pro jeden nebo více kanálů. Libovolný StreamingEndpoint může vyžádat z libovolného kanálu.

### <a name="program-event"></a>Program (událost)
[Program (událost)](/rest/api/media/operations/program) umožňuje řídit publikování a ukládání segmentů v živém datovém proudu. Kanály spravují programy (události). Vztah kanálu a programu je podobný tradičnímu médiu, kde kanál obsahuje konstantní datový proud obsahu a program je vymezen na určitou událost s časovým limitem na daném kanálu.
Nastavením vlastnosti **ArchiveWindowLength** můžete zadat počet hodin, po které chcete zachovávat zaznamenaný obsah pro program. Tuto hodnotu můžete nastavit v rozmezí od 5 minut po 25 hodin.

ArchiveWindowLength také určuje maximální dobu, po kterou mohou klienti vyhledávat v čase z aktuální živé pozice. Programy můžou běžet po určenou dobu a obsah, který se do délky okna nevejde, bude vždy zahozen. Hodnota této vlastnosti také určuje, jak dlouho můžou růst manifesty klientů.

Každý program (událost) je přidružen k Assetu. Pro publikování programu musíte vytvořit Lokátor pro přidružený Asset. Tento lokátor vám umožní sestavit adresu URL pro streamování, kterou potom poskytnete svým klientům.

Kanál podporuje až tři současně spuštěné programy, takže si můžete vytvořit několik archivů stejného příchozího datového proudu. To vám umožní podle potřeby publikovat a archivovat různé části události. Požadavek vaší firmy může být například takový, že chcete archivovat 6 hodin programu, ale vysílat jenom posledních 10 minut. K tomu potřebujete vytvořit dva současně spuštěné programy. Jeden program nastavíte, aby archivoval 6 hodin události, ale tento program nebudete publikovat. Druhý program nastavíte, aby archivoval 10 minut a tento program budete publikovat.

Další informace naleznete v tématu:

* [Práce s kanály, které jsou povolené k provádění Live Encoding s Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Práce s kanály, které přijímají živé datové proudy s více přenosovými rychlostmi z místních kodérů](media-services-live-streaming-with-onprem-encoders.md)
* [Kvóty a omezení](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>Ochrana obsahu
### <a name="dynamic-encryption"></a>Dynamické šifrování
Azure Media Services vám umožní zabezpečit vaše média od okamžiku, kdy počítač opustí vaše počítače, a to prostřednictvím úložiště, zpracování a doručování. Media Services vám umožní doručovat obsah dynamicky šifrovaný pomocí standard AES (Advanced Encryption Standard) (AES) (pomocí 128 šifrovacích klíčů) a běžné šifrování (CENC) pomocí technologie PlayReady nebo Widevine DRM. Media Services taky poskytuje službu pro doručování klíčů AES a licencí PlayReady do autorizovaných klientů.

V současné době můžete šifrovat následující formáty streamování: HLS, MPEG POMLČKa a Smooth Streaming. Progresivní stahování nelze zašifrovat.

Pokud chcete, aby Media Services šifrování assetu, musíte k assetu přidružit šifrovací klíč (CommonEncryption nebo EnvelopeEncryption) a také nakonfigurovat zásady autorizace pro tento klíč.

Pokud chcete streamovat šifrovaný prostředek úložiště, musíte nakonfigurovat zásady doručení assetu, aby bylo možné určit, jak chcete Asset doručovat.

Když hráč vyžádá datový proud, Media Services použije zadaný klíč k dynamickému šifrování obsahu pomocí šifrování obálky (s AES) nebo běžné šifrování (s PlayReady nebo Widevine). Aby byl datový proud dešifrován, přehrávač si vyžádá klíč ze služby doručování klíčů. Aby bylo možné rozhodnout, jestli uživatel má oprávnění k získání klíče, vyhodnotí zásady autorizace, které jste zadali pro tento klíč.

### <a name="token-restriction"></a>Omezení tokenu
Zásady autorizace klíče obsahu můžou mít jedno nebo víc autorizačních omezení: otevřená, omezení tokenu nebo omezení IP adres. Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Služba Media Services podporuje tokeny ve  formátu jednoduchých webových tokenů (SWT) a webových tokenů JSON (JWT). Media Services neposkytuje služby zabezpečeného tokenu. Můžete vytvořit vlastní STS. Služba STS musí být nakonfigurovaná tak, aby vytvořila token podepsaný pomocí zadaného klíče a vydávala deklarace identity, které jste zadali v konfiguraci omezení tokenu. Služba doručování Media Services Key vrátí požadovaný klíč (nebo licenci) klientovi, pokud je token platný a deklarace identity v tokenu se shodují s hodnotami nakonfigurovanými pro klíč (nebo licenci).

Při konfiguraci zásad s omezeným tokenem je nutné zadat primární ověřovací klíč, parametry vystavitele a cílové skupiny. Primární ověřovací klíč obsahuje klíč, se kterým byl token podepsaný, vydavatel je služba zabezpečeného tokenu, která token vydává. Cílová skupina (někdy označovaná jako obor) popisuje účel tokenu nebo prostředku, ke kterému token opravňuje přístup. Služba doručení Media Services Key ověřuje, že se tyto hodnoty v tokenu shodují s hodnotami v šabloně.

Další informace najdete v následujících článcích:
- [Přehled ochrany obsahu](media-services-content-protection-overview.md)
- [Ochrana pomocí AES-128](media-services-protect-with-aes128.md)
- [Ochrana pomocí PlayReady/Widevine](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Doručování
### <a name="dynamic-packaging"></a><a name="dynamic_packaging"></a>Dynamické balení
Při práci s Media Services se doporučuje kódování souborů Mezzanine zakódovat do sady MP4 s adaptivní přenosovou rychlostí a pak ji převést do požadovaného formátu pomocí [dynamického balení](media-services-dynamic-packaging-overview.md).

### <a name="streaming-endpoint"></a>Koncový bod streamování
StreamingEndpoint představuje službu streamování, která může doručovat obsah přímo do klientské aplikace přehrávače nebo do Content Delivery Network (CDN) pro další distribuci (Azure Media Services nyní poskytuje integraci Azure CDN.) Odchozí datový proud ze služby streamování koncového bodu streamování může být živý datový proud nebo prostředek videa na vyžádání v účtu Media Services. Zákazníci Media Services si podle svých potřeb zvolí buď koncový bod **Standard**, nebo jeden nebo několik koncových bodů streamování **Premium**. Koncový bod streamování Standard je vhodný pro většinu úloh streamování. 

Koncový bod streamování Standard je vhodný pro většinu streamovacích úloh. Standardní koncové body streamování nabízí flexibilitu při doručování obsahu prakticky do všech zařízení prostřednictvím dynamického balení do HLS, MPEG-POMLČKy a Smooth Streaming a také dynamického šifrování pro Microsoft PlayReady, Google Widevine, Apple Fairplay a AES128.  Také se škálují z velmi malých na velmi velké cílové skupiny s tisíci souběžnými prohlížeči prostřednictvím Integrace Azure CDN. Pokud máte pokročilé úlohy nebo vaše požadavky na kapacitu streamování nevyhovují standardním datovým proudům koncového bodu streamování nebo chcete řídit kapacitu služby StreamingEndpoint, aby se zpracovávala rostoucí nároky na šířku pásma, doporučujeme přidělit jednotky škálování (označované taky jako jednotky streamování Premium).

Doporučuje se použít dynamické balení a/nebo dynamické šifrování.

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 

Další informace najdete v [tomto](media-services-portal-manage-streaming-endpoints.md) tématu.

Ve výchozím nastavení můžete mít v účtu Media Services až dva koncové body streamování. Pokud si chcete vyžádat vyšší limit, přečtěte si téma [kvóty a omezení](media-services-quotas-and-limitations.md).

Fakturuje se vám jenom v případě, že vaše StreamingEndpoint je ve spuštěném stavu.

### <a name="asset-delivery-policy"></a>Zásady doručení assetu
Jeden z kroků v pracovním postupu pro doručování obsahu Media Services konfiguruje [zásady doručování pro prostředky](/rest/api/media/operations/assetdeliverypolicy), které chcete zasílat datovým proudem. Zásada doručení assetu oznamuje Media Services, jak chcete, aby se Asset doručil: do kterého by měl být váš Asset dynamicky zabalen (například MPEG POMLČKa, HLS, Smooth Streaming nebo vše), ať už chcete, nebo ne, chcete-li prostředek dynamicky zašifrovat a jak (obálky nebo běžné šifrování).

Pokud máte zašifrovaný prostředek úložiště, pak před odesláním assetu do datového proudu Server streamování odstraní šifrování úložiště a streamuje obsah pomocí zadaných zásad doručování. Pokud třeba chcete zajistit, aby se Asset zašifroval pomocí šifrovacího klíče standard AES (Advanced Encryption Standard) (AES), nastavte typ zásad na DynamicEnvelopeEncryption. Pokud chcete odstranit šifrování úložiště a streamovat prostředek v nejasném případě, nastavte typ zásady na NoDynamicEncryption.

### <a name="progressive-download"></a>Progresivní stahování
Progresivní stahování vám umožní začít přehrávat média před stažením celého souboru. Soubor MP4 můžete postupně stáhnout pouze v průběhu.

>[!NOTE]
>Pokud chcete, aby byly k dispozici pro progresivní stahování, je nutné dešifrovat šifrované prostředky.

Pokud chcete uživatelům poskytnout adresy URL progresivního stahování, musíte nejdřív vytvořit Lokátor OnDemandOrigin. Vytvoření lokátoru vám poskytne základní cestu k assetu. Pak musíte připojit název souboru MP4. Například:

`http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

### <a name="streaming-urls"></a>Adresy URL streamování
Streamování obsahu do klientů. Pokud chcete uživatelům poskytnout adresy URL streamování, musíte nejdřív vytvořit Lokátor OnDemandOrigin. Vytvořením lokátoru získáte základní cestu k assetu, který obsahuje obsah, který chcete streamovat. Abyste ale mohli streamovat tento obsah, musíte tuto cestu dál upravit. Pokud chcete vytvořit úplnou adresu URL souboru manifestu streamování, musíte zřetězit hodnotu cesty lokátoru a název souboru manifestu (filename. ISM). Pak v cestě lokátoru přidejte/manifest a odpovídající formát (v případě potřeby).

Svůj obsah můžete streamovat i přes připojení TLS. Pokud to chcete provést, ujistěte se, že vaše adresy URL streamování začínají na HTTPS. AMS v současné době nepodporuje TLS s vlastními doménami.  

>[!NOTE]
>Streamování přes TLS můžete odeslat jenom v případě, že koncový bod streamování, ze kterého dodáváte obsah, byl vytvořen po 10. září 2014. Pokud jsou vaše adresy URL streamování založené na koncových bodech streamování vytvořených po 10. září, adresa URL obsahuje "streaming.mediaservices.windows.net" (nový formát). Adresy URL streamování obsahující "origin.mediaservices.windows.net" (starý formát) nepodporují protokol TLS. Pokud je vaše adresa URL ve starém formátu a chcete být schopná streamovat přes TLS, vytvořte nový koncový bod streamování. K streamování obsahu přes TLS použijte adresy URL vytvořené na základě nového koncového bodu streamování.

Následující seznam popisuje různé formáty streamování a nabízí příklady:

* Technologie Smooth Streaming

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

http: \/ /testendpoint-testaccount.Streaming.MediaServices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest

* MPEG DASH

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http: \/ /testendpoint-testaccount.Streaming.MediaServices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (Format = MPD-Time-CSF)

* Apple HTTP Live Streaming (HLS) v4

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http: \/ /testendpoint-testaccount.Streaming.MediaServices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (Format = M3U8-AAPL)

* Apple HTTP Live Streaming (HLS) V3

{streamování názvu koncového bodu – název účtu Media Services}. streamování. MediaServices. Windows. NET/{Locator ID}/{filename}.ism/Manifest (Format = M3U8-AAPL-V3)

http: \/ /testendpoint-testaccount.Streaming.MediaServices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (Format = M3U8-AAPL-V3)

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
