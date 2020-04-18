---
title: Koncepty mediálních služeb Azure | Dokumenty společnosti Microsoft
description: Tento článek poskytuje stručný přehled konceptů Microsoft Azure Media Services a odkazy na další články pro podrobnosti.
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
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 44d6776a367a5cb1eeb86c955f083598d417aba6
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641668"
---
# <a name="azure-media-services-concepts"></a>Koncepty Mediálních služeb Azure 

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

Toto téma poskytuje přehled nejdůležitějších konceptů mediálních služeb.

## <a name="assets-and-storage"></a><a id="assets"/>Majetek a úložiště
### <a name="assets"></a>Prostředky
[Asset](https://docs.microsoft.com/rest/api/media/operations/asset) obsahuje digitální soubory (včetně videa, zvuku, obrázků, miniatur, textových stop a souborů skrytých titulků) a metadata o těchto souborech. Po nahrání digitálních souborů do datového zdroje mohou být použity v pracovních postupech kódování a streamování mediálních služeb.

Prostředek je mapován na kontejner objektů blob v účtu Azure Storage a soubory v prostředku jsou uloženy jako objekty BLOB bloku v tomto kontejneru. Objekty BLOB stránky nejsou podporovány službou Azure Media Services.

Při rozhodování o tom, jaký mediální obsah chcete do datového zdroje nahrát a uložit, platí následující:

* Datový zdroj by měl obsahovat pouze jednu jedinečnou instanci mediálního obsahu. Například jedna úprava televizní epizody, filmu nebo reklamy.
* Prostředek by neměl obsahovat více interpretací nebo úprav audiovizuálního souboru. Jedním z příkladů nesprávného použití datového zdroje by se pokus ukládat více než jednu televizní epizodu, reklamu nebo více úhlů kamery z jedné produkce uvnitř datového zdroje. Uložení více interpretací nebo úprav audiovizuálního souboru do datového zdroje může mít za následek potíže s odesláním úloh kódování, streamováním a zabezpečením doručení datového zdroje později v pracovním postupu.  

### <a name="asset-file"></a>Soubor datového zdroje
[AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) představuje skutečný video nebo zvukový soubor, který je uložen v kontejneru objektů blob. Soubor datového zdroje je vždy přidružen k datovému zdroji a datový zdroj může obsahovat jeden nebo více souborů. Úloha kodéru mediálních služeb se nezdaří, pokud objekt souboru datového zdroje není přidružen k digitálnímu souboru v kontejneru objektů blob.

Instance **AssetFile** a skutečný mediální soubor jsou dva odlišné objekty. Instance AssetFile obsahuje metadata o mediálním souboru, zatímco mediální soubor obsahuje skutečný mediální obsah.

Neměli byste se pokoušet změnit obsah kontejnerů objektů blob, které byly generovány službou Media Services bez použití souborů API služby Media Service.

### <a name="asset-encryption-options"></a>Možnosti šifrování datových zdrojů
V závislosti na typu obsahu, který chcete nahrát, uložit a doručit, poskytuje služba Media Services různé možnosti šifrování, ze kterých si můžete vybrat.

>[!NOTE]
>Není použito žádné šifrování. Toto je výchozí hodnota. Při použití této možnosti není váš obsah chráněn při přenosu nebo v klidovém stavu.

Pokud plánujete dodat MP4 pomocí postupného stahování, použijte tuto možnost k nahrání obsahu.

**StorageEncrypted** – Tuto možnost použijte k šifrování vymazat obsah místně pomocí AES 256 bitové šifrování a pak jej nahrát do Služby Azure Storage, kde je uloženš šifrované v klidovém stavu. Prostředky chráněné šifrováním úložiště jsou automaticky nezašifrovány a umístěny do šifrovaného systému souborů před kódováním a volitelně znovu zašifrovány před odesláním zpět jako nový výstupní prostředek. Primární případ použití pro šifrování úložiště je, když chcete zabezpečit vysoce kvalitní vstupní mediální soubory se silným šifrováním v klidovém stavu na disku. 

Chcete-li dodat úložiště šifrovaný datový zdroj, musíte nakonfigurovat zásady doručování datového zdroje tak, aby mediální služby věděly, jak chcete doručovat svůj obsah. Před datovým proudem datový proud server datových proudů odebere šifrování úložiště a streamuje váš obsah pomocí zadaných zásad doručení (například AES, PlayReady nebo žádné šifrování). 

**CommonEncryptionProtected** - Tuto možnost použijte, pokud chcete šifrovat (nebo nahrát již šifrovaný) obsah pomocí společného šifrování nebo playready DRM (například vyhlazené streamování chráněné pomocí služby PlayReady DRM).

**EnvelopeEncryptionProtected** – Tuto možnost použijte, pokud chcete chránit (nebo nahrát již chráněné) HTTP Live Streaming (HLS) šifrované s Advanced Encryption Standard (AES). Pokud nahráváte HLS již zašifrované pomocí AES, musí být zašifrována Správcem transformace.

### <a name="access-policy"></a>Zásady přístupu
[AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) definuje oprávnění (jako je čtení, zápis a seznam) a dobu trvání přístupu k prostředku. Objekt AccessPolicy byste obvykle předali lokátoru, který by pak byl použit pro přístup k souborům obsaženým v datovém zdroji.

>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Pokud vždy používáte stejné dny / přístupová oprávnění, například zásady pro lokátory, které mají zůstat na místě po dlouhou dobu (zásady bez odeslání), měli byste použít stejné ID zásad. Další informace najdete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) tématu.

### <a name="blob-container"></a>Kontejner objektů blob
Kontejner objektů blob poskytuje seskupení sady objektů BLOB. Kontejnery objektů blob se používají ve službě Media Services jako hraniční bod pro řízení přístupu a lokátory sdíleného přístupového podpisu (SAS) na datových prostředcích. Účet Azure Storage může obsahovat neomezený počet kontejnerů objektů blob. Kontejner můžete pojmout neomezený počet objektů blob.

>[!NOTE]
> Neměli byste se pokoušet změnit obsah kontejnerů objektů blob, které byly generovány službou Media Services bez použití souborů API služby Media Service.
> 
> 

### <a name="locators"></a><a id="locators"/>Lokátory
[Lokátor](https://docs.microsoft.com/rest/api/media/operations/locator)s poskytují vstupní bod pro přístup k souborům obsaženým v datovém zdroji. Zásady přístupu se používají k definování oprávnění a doby trvání, po kterou má klient přístup k danému prostředku. Lokátory mohou mít vztah mnoho ku jedné se zásadami přístupu, takže různé lokátory mohou různým klientům poskytovat různé časy spuštění a typy připojení, zatímco všechny používají stejné nastavení oprávnění a doby trvání. z důvodu omezení zásad sdíleného přístupu nastaveného službami úložiště Azure však nemůžete mít více než pět jedinečných lokátorů přidružených k danému prostředku najednou. 

Media Services podporuje dva typy lokátorů: OnDemandOrigin lokátory, používané k streamování médií (například MPEG DASH, HLS nebo plynulé streamování) nebo postupně stahovat média a SAS URL lokátory, slouží k nahrávání nebo stahování mediálních souborů do \z úložiště Azure. 

>[!NOTE]
>Oprávnění seznamu (AccessPermissions.List) by neměla být použita při vytváření lokátoru OnDemandOrigin. 

### <a name="storage-account"></a>Účet úložiště
Veškerý přístup k Azure Storage se provádí prostřednictvím účtu úložiště. Účet mediální služby lze přidružit k jednomu nebo více účtům úložiště. Účet může obsahovat neomezený počet kontejnerů, pokud je jejich celková velikost menší než 500 TB na účet úložiště.  Služba Media Services poskytuje nástroje na úrovni sady SDK, které umožňují spravovat více účtů úložiště a vyrovnávání zatížení při odesílání prostředků během nahrávání na tyto účty na základě metrik nebo náhodné distribuce. Další informace najdete v tématu Práce s [Azure Storage](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

## <a name="jobs-and-tasks"></a>Pracovní místa a úkoly
[Úloha](https://docs.microsoft.com/rest/api/media/operations/job) se obvykle používá ke zpracování (například index nebo kódování) jedné audio/video prezentace. Pokud zpracováváte více videí, vytvořte úlohu pro každé video, které má být zakódováno.

Úloha obsahuje metadata o zpracování, které má být provedeno. Každá úloha obsahuje jednu nebo více [úloh,](https://docs.microsoft.com/rest/api/media/operations/task)které určují úlohu atomového zpracování, její vstupní datové zdroje, výstupní datové zdroje, mediální procesor a jeho přidružená nastavení. Úkoly v rámci úlohy lze zřetězit dohromady, kde je výstupní majetek jednoho úkolu uveden jako vstupní majetek k dalšímu úkolu. Tímto způsobem může jedna úloha obsahovat veškeré zpracování nezbytné pro mediální prezentaci.

## <a name="encoding"></a><a id="encoding"></a>Kódování
Azure Media Services poskytuje několik možností pro kódování médií v cloudu.

Při spuštění s Media Services, je důležité pochopit rozdíl mezi kodeky a formáty souborů.
Kodeky jsou software, který implementuje kompresní/dekompresní algoritmy, zatímco formáty souborů jsou kontejnery, které drží komprimované video.

Media Services poskytuje dynamické balení, které vám umožní dodat adaptivní datový tok MP4 nebo smooth streaming kódovaný obsah ve formátech streamování podporovaných Media Services (MPEG DASH, HLS, Smooth Streaming), aniž byste museli znovu zabalit do těchto formátů streamování.

Chcete-li využít [výhod dynamického balení](media-services-dynamic-packaging-overview.md), musíte kódovat mezipatro (zdrojový) soubor do sady souborů MP4 s adaptivní množení nebo adaptivního datového toku Smooth Streaming a mít alespoň jeden standardní nebo prémiový koncový bod streamování v spuštěném stavu.

Služba Media Services podporuje následující kodéry na vyžádání, které jsou popsány v tomto článku:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Pracovní postup kodéru Media Encoder Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Informace o podporovaných kododrech naleznete v tématu [Kodéry](media-services-encode-asset.md).

## <a name="live-streaming"></a>Živé streamování
Ve službě Azure Media Services kanál představuje kanál pro zpracování živého streamování obsahu. Kanál přijímá živé vstupní proudy jedním ze dvou způsobů:

* Místní živý kodér odesílá do kanálu vícebitové rtmp nebo plynulé streamování (fragmentované MP4). Můžete použít následující živé kodéry, které vypracovává plynulé streamování s více přenosovými rychlostmi: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco a Elemental. Následující živé kodéry výstup RTMP: Adobe Flash Live Encoder, [Telestream Wirecast](media-services-configure-wirecast-live-encoder.md), Teradek, Haivision kodéry. Ingestované datové proudy procházejí kanály bez dalšího překódování a kódování. Služba Media Services doručí datový proud zákazníkům na vyžádání.
* Jeden datový proud datového toku (v jednom z následujících formátů: RTMP nebo plynulé streamování (fragmentované MP4)) je odeslán do kanálu, který je povolen pro živé kódování pomocí služby Media Services. Kanál potom provede kódování v reálném čase pro příchozí datový proud s jednou přenosovou rychlostí v reálném čase na datový proud videa s více přenosovými rychlostmi (adaptivní). Služba Media Services doručí datový proud zákazníkům na vyžádání.

### <a name="channel"></a>Channel
Ve službě Media Services jsou [kanály](https://docs.microsoft.com/rest/api/media/operations/channel)s odpovědné za zpracování živého streamování obsahu. Kanál poskytuje vstupní koncový bod (ingestovat URL), který pak poskytnout živé transkodéru. Kanál přijímá živé vstupní proudy z živého transkodéru a zpřístupňuje je pro streamování prostřednictvím jednoho nebo více StreamingEndpoints. Kanály také poskytují koncový bod náhledu (adresu URL náhledu), který používáte k zobrazení náhledu a ověření datového proudu před dalším zpracováním a doručením.

Při vytváření kanálu můžete získat adresu URL ingestování a adresu URL náhledu. Chcete-li získat tyto adresy URL, kanál nemusí být ve stavu spuštění. Když jste připraveni začít tlačit data z živého transkodéru do kanálu, kanál musí být spuštěn. Jakmile živý transkodér začne příjem dat, můžete zobrazit náhled datového proudu.

Každý účet media services může obsahovat více kanálů, více programů a více StreamingEndpoints. V závislosti na šířce pásma a potřebách zabezpečení mohou být služby StreamingEndpoint vyhrazeny pro jeden nebo více kanálů. Všechny StreamingEndpoint můžete vytáhnout z libovolného kanálu.

### <a name="program-event"></a>Program (událost)
[Program (událost)](https://docs.microsoft.com/rest/api/media/operations/program) umožňuje řídit publikování a ukládání segmentů v živém přenosu. Kanály spravují programy (události). Vztah Kanálu a programu je podobný tradičním médiím, kde kanál má konstantní datový proud obsahu a program je vymezen na určitou časovou událost v tomto kanálu.
Počet hodin, po které chcete zachovat zaznamenaný obsah programu, můžete určit nastavením vlastnosti **ArchiveWindowLength.** Tuto hodnotu můžete nastavit v rozmezí od 5 minut po 25 hodin.

ArchiveWindowLength také určuje maximální množství času, které mohou klienti hledat zpět v čase z aktuální živé pozice. Programy můžou běžet po určenou dobu a obsah, který se do délky okna nevejde, bude vždy zahozen. Hodnota této vlastnosti také určuje, jak dlouho můžou růst manifesty klientů.

Každý program (událost) je přidružen k datovému zdroji. Chcete-li publikovat program, musíte vytvořit lokátor pro přidružený datový zdroj. Tento lokátor vám umožní sestavit adresu URL pro streamování, kterou potom poskytnete svým klientům.

Kanál podporuje až tři současně spuštěné programy, takže si můžete vytvořit několik archivů stejného příchozího datového proudu. To vám umožní podle potřeby publikovat a archivovat různé části události. Požadavek vaší firmy může být například takový, že chcete archivovat 6 hodin programu, ale vysílat jenom posledních 10 minut. K tomu potřebujete vytvořit dva současně spuštěné programy. Jeden program nastavíte, aby archivoval 6 hodin události, ale tento program nebudete publikovat. Druhý program nastavíte, aby archivoval 10 minut a tento program budete publikovat.

Další informace naleznete v tématu:

* [Práce s kanály, které mají povoleno živé kódování pomocí Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Práce s kanály, které přijímají živé datové proudy s více přenosovými rychlostmi z místních kodérů](media-services-live-streaming-with-onprem-encoders.md)
* [Kvóty a omezení](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>Ochrana obsahu
### <a name="dynamic-encryption"></a>Dynamické šifrování
Azure Media Services umožňuje zabezpečit média od okamžiku, kdy opustí váš počítač prostřednictvím úložiště, zpracování a doručení. Mediální služby umožňují dodávat obsah šifrovaný dynamicky pomocí standardu Advanced Encryption Standard (AES) (pomocí 128bitových šifrovacích klíčů) a běžného šifrování (CENC) pomocí služby PlayReady a/nebo Widevine DRM. Služba Media Services také poskytuje službu pro doručování klíčů AES a licencí PlayReady autorizovaným klientům.

V současné době můžete šifrovat následující formáty streamování: HLS, MPEG DASH a Smooth Streaming. Progresivní stahování nelze šifrovat.

Pokud chcete, aby mediální služby šifrovat prostředek, musíte přidružit šifrovací klíč (CommonEncryption nebo EnvelopeEncryption) s datovým zdrojem a také nakonfigurovat zásady autorizace pro klíč.

Pokud chcete streamovat zašifrovaný datový zdroj úložiště, musíte nakonfigurovat zásady doručování datového zdroje, abyste určili, jak chcete svůj datový zdroj doručit.

Když je datový proud požadován přehrávačem, služba Media Services používá zadaný klíč k dynamickému šifrování obsahu pomocí šifrování obálky (pomocí AES) nebo běžného šifrování (pomocí služby PlayReady nebo Widevine). Chcete-li dešifrovat datový proud, přehrávač bude požadovat klíč ze služby doručování klíčů. Chcete-li rozhodnout, zda je uživatel oprávněn získat klíč, služba vyhodnotí zásady autorizace, které jste zadali pro klíč.

### <a name="token-restriction"></a>Omezení tokenu
Zásady autorizace klíče obsahu mohou mít jedno nebo více omezení autorizace: otevřené, omezení tokenu nebo omezení IP adresy. Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Služba Media Services podporuje tokeny ve  formátu jednoduchých webových tokenů (SWT) a webových tokenů JSON (JWT). Mediální služby neposkytují služby zabezpečeného tokenu. Můžete vytvořit vlastní STS. Služba STS musí být nakonfigurována tak, aby vytvořila token podepsaný zadaným klíčem a vydala deklarace identity, které jste zadali v konfiguraci omezení tokenu. Služba doručování klíčů mediálních služeb vrátí požadovaný klíč (nebo licenci) klientovi, pokud je token platný a deklarace identity v tokenu odpovídají deklaracím nakonfigurovaným pro klíč (nebo licenci).

Při konfiguraci zásad s omezeným přístupem tokenu je nutné zadat primární ověřovací klíč, parametry vystavittele a publika. Primární ověřovací klíč obsahuje klíč, se kterým byl token podepsán, vystavitel je služba zabezpečeného tokenu, která vydává token. Cílová skupina (někdy označovaná jako obor) popisuje záměr tokenu nebo prostředku, ke kterému token autorizuje přístup. Služba doručování klíčů Mediální služby ověří, zda tyto hodnoty v tokenu odpovídají hodnotám v šabloně.

Další informace najdete v těchto článcích:
- [Ochrana přehledu obsahu](media-services-content-protection-overview.md)
- [Chraňte pomocí AES-128](media-services-protect-with-aes128.md)
- [Chraňte pomocí PlayReady/Widevine](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Poskytování
### <a name="dynamic-packaging"></a><a id="dynamic_packaging"/>Dynamické balení
Při práci se službou Media Services se doporučuje zakódovat soubory mezaninové ho diod do sady MP4 s adaptivní množí datový tok a poté ji převést na požadovaný formát pomocí [dynamického balení](media-services-dynamic-packaging-overview.md).

### <a name="streaming-endpoint"></a>Koncový bod streamování
StreamingEndpoint představuje službu streamování, která může doručovat obsah přímo do aplikace přehrávače klienta nebo do sítě pro doručování obsahu (CDN) pro další distribuci (Azure Media Services nyní poskytuje integraci Azure CDN.) Odchozí datový proud ze služby koncového bodu streamování může být živý datový proud nebo datový zdroj na vyžádání ve vašem účtu Mediálních služeb. Zákazníci Media Services si podle svých potřeb zvolí buď koncový bod **Standard**, nebo jeden nebo několik koncových bodů streamování **Premium**. Standardní koncový bod streamování je vhodný pro většinu úloh streamování. 

Koncový bod streamování Standard je vhodný pro většinu streamovacích úloh. Standardní koncové body pro streamování nabízejí flexibilitu pro doručování obsahu prakticky do každého zařízení prostřednictvím dynamického balení do hls, MPEG-DASH a plynulého streamování, stejně jako dynamické šifrování pro Microsoft PlayReady, Google Widevine, Apple Fairplay a AES128.  Také škálovat z velmi malé až velmi velké publikum s tisíci souběžných diváků prostřednictvím integrace Azure CDN. Pokud máte rozšířené zatížení nebo vaše požadavky na kapacitu streamování neodpovídají standardnícíle propustnosti koncového bodu streamování nebo chcete řídit kapacitu služby StreamingEndpoint pro zpracování rostoucích potřeb šířky pásma, doporučujeme přidělit jednotky škálování (označované také jako jednotky streamování premium).

Doporučujese používat dynamické balení a/nebo dynamické šifrování.

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 

Další informace najdete v [tomto](media-services-portal-manage-streaming-endpoints.md) tématu.

Ve výchozím nastavení můžete mít v účtu Media Services až 2 koncové body streamování. Chcete-li požádat o vyšší limit, naleznete [v tématu Kvóty a omezení](media-services-quotas-and-limitations.md).

Bude se vám účtovat pouze v případě, že je váš StreamingEndpoint ve spuštěném stavu.

### <a name="asset-delivery-policy"></a>Zásady poskytování majetku
Jedním z kroků v pracovním postupu doručování obsahu služby Media Services je konfigurace [zásad doručování pro datové zdroje,](https://docs.microsoft.com/rest/api/media/operations/assetdeliverypolicy)které chcete streamovat. Zásady doručování datových zdrojů sdělují službě Media Services, jak chcete, aby byl váš datový zdroj dodán: do kterého streamovacího protokolu by měl být váš datový zdroj dynamicky zabalen (například MPEG DASH, HLS, Plynulé streamování nebo všechny), zda chcete dynamicky šifrovat váš datový zdroj a jak (obálka nebo běžné šifrování).

Pokud máte úložiště šifrovaný datový zdroj, před datovým proudem, server streamování odebere šifrování úložiště a streamuje obsah pomocí zadaných zásad doručení. Chcete-li například doručit datový prostředek šifrovaný pomocí šifrovacího klíče Advanced Encryption Standard (AES), nastavte typ zásady na DynamicEnvelopeEncryption. Chcete-li odebrat šifrování úložiště a streamovat datový zdroj v přehledné, nastavte typ zásady na NoDynamicEncryption.

### <a name="progressive-download"></a>Postupné stahování
Postupné stahování umožňuje začít přehrávat média před stažením celého souboru. Soubor MP4 lze stahovat pouze postupně.

>[!NOTE]
>Pokud chcete, aby byly šifrované datové zdroje k dispozici pro postupné stahování, je nutné je dešifrovat.

Chcete-li uživatelům poskytnout adresy URL postupného stahování, musíte nejprve vytvořit lokátor OnDemandOrigin. Vytvořenílokátoru, vám dává základní cestu k majetku. Poté je třeba připojit název souboru MP4. Příklad:

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### <a name="streaming-urls"></a>Datové adresy URL pro streamování
Streamování obsahu klientům. Chcete-li uživatelům poskytnout streamované adresy URL, musíte nejprve vytvořit lokátor OnDemandOrigin. Vytvořenílokátoru, poskytuje základní cestu k datovému zdroji, který obsahuje obsah, který chcete streamovat. Chcete-li však tento obsah streamovat, je třeba tuto cestu dále upravit. Chcete-li vytvořit úplnou adresu URL souboru manifestu streamování, musíte zřetězit hodnotu Path lokátoru a název souboru manifestu (filename.ism). Potom připojit /Manifest a vhodný formát (v případě potřeby) na cestu lokátoru.

Můžete také streamovat obsah přes připojení TLS. Chcete-li to provést, ujistěte se, že vaše streamované adresy URL začínají protokolem HTTPS. V současné době AMS nepodporuje TLS s vlastními doménami.  

>[!NOTE]
>Streamovat přes TLS lze pouze v případě, že koncový bod streamování, ze kterého doručujete obsah byl vytvořen po 10 září 2014. Pokud jsou vaše adresy URL datových proudů založeny na koncových bodech streamování vytvořených po 1 streaming.mediaservices.windows.net0. Datové adresy URL datových proudů, které obsahují "origin.mediaservices.windows.net" (starý formát), nepodporují tls. Pokud je vaše adresa URL ve starém formátu a chcete mít možnost datového proudu přes TLS, vytvořte nový koncový bod streamování. Pomocí adres URL vytvořených na základě nového koncového bodu streamování můžete streamovat obsah přes TLS.

Následující seznam popisuje různé formáty datových proudů a uvádí příklady:

* Technologie Smooth Streaming

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

* MPEG DASH

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

* Apple HTTP Živé vysílání (HLS) V4

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

* Apple HTTP Živé vysílání (HLS) V3

{název_mediálního serveru koncového bodu název účtu}.streaming.mediaservices.windows.net/{locator ID}/{název_souboru}.ism/Manifest(format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

