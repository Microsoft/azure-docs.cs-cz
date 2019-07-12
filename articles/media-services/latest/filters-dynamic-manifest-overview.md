---
title: Filtry a Azure Media Services dynamických manifestů | Dokumentace Microsoftu
description: Toto téma popisuje, jak vytvářet filtry, takže klient může používat na určité části datový proud stream. Služba Media Services vytvoří dynamických manifestů k selektivní Streamovat.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: ee0200f7c007b437a27b8e9d0f36becc13b8f611
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835818"
---
# <a name="pre-filtering-manifests-by-using-dynamic-packager"></a>Předem filtrování manifesty pomocí dynamické Packager

Pokud budete posílat adaptivní přenosové rychlosti streamování obsahu do zařízení, je často potřeba publikování několika verzí manifestu funkce pro cílové konkrétní zařízení nebo dostupnou šířku pásma sítě. [Dynamické Packager](dynamic-packaging-overview.md) můžete zadat filtry, které můžete vyfiltrovat konkrétní kodeky, zvuk, rozlišení a přenosových rychlostí sledovat kombinace na průběžné odstraňují potřebu vytvářet více kopií. Stačí jednoduše publikujte novou adresu URL s konkrétní sadou filtrů nakonfigurovaný tak, aby vaše cílová zařízení (iOS, Android, celosvětového nebo prohlížeče) a možnosti sítě (scénáře velkou šířkou pásma, mobilní nebo s malou šířkou pásma). V takovém případě klienti můžete pracovat s streamování vašeho obsahu pomocí řetězce dotazu (zadáním dostupné [Asset filtry nebo účet filtry](filters-concept.md)) a použít filtry na určité části datový proud stream.

Některé scénáře doručování vyžadují, aby zákazník je nejde získat přístup ke konkrétní stopy. Například možná chcete publikovat manifestu, který obsahuje HD, High Density stopy na úroveň konkrétních odběratele. Nebo můžete chtít odebrat konkrétní s adaptivní přenosovou rychlostí (ABR) sleduje, abyste snížili náklady na doručování na konkrétní zařízení, které by těžit z další stopy. V tomto případě bylo možné přidružit k seznam předem vytvořené filtrů s vaší [Lokátor streamování](streaming-locators-concept.md) při vytvoření. V takovém případě klienti nemůže pracovat s jak Streamovat obsah, je definován **Lokátor streamování**.

Filtrování pomocí zadání můžete kombinovat [filtry pro Lokátor streamování](filters-concept.md#associating-filters-with-streaming-locator) + další zařízení specifické filtry, které váš klient určuje v adrese URL. To může být užitečné pro omezení dalších sleduje například metadata nebo událostí datových proudů, zvuku jazyků nebo popisný zvukové stopy. 

Tato schopnost určit různé filtry na datový proud, poskytuje výkonný **dynamické Manifest** manipulaci s řešení cílit na více scénářů použití pro cílová zařízení. Toto téma vysvětluje koncepty související s **dynamických manifestů** a uvádí příklady scénářů, ve kterých může být vhodné tuto funkci používat.

> [!NOTE]
> Dynamický manifest neměňte assetu a výchozí manifest pro tento prostředek. 
> 

##  <a name="overview-of-manifests"></a>Přehled manifestů

Azure Media Services podporuje protokoly HLS, MPEG DASH a Smooth Streaming. Jako součást [dynamické balení](dynamic-packaging-overview.md), streamování manifesty klientů (seznam stop hlavní HLS, DASH média prezentace popis [MPD] a technologie Smooth Streaming) jsou dynamicky generován a základě modulu pro výběr formátu v adrese URL. Zobrazit protokoly doručování v [běžným pracovním postupem na vyžádání](dynamic-packaging-overview.md#delivery-protocols). 

### <a name="get-and-examine-manifest-files"></a>Získání a zkontrolovat soubory manifestu

Můžete zadat seznam filtru sledovat vlastnost podmínek založených na která sleduje váš datový proud (za provozu nebo video na vyžádání [VOD]) by měl být součástí dynamicky generovaný manifest. Pokud chcete získat a podívejte se na vlastnosti drah, musíte nejdřív načíst manifest technologie Smooth Streaming.

[Nahrávání, kódování a streamování souborů s využitím .NET](stream-files-tutorial-with-api.md#get-streaming-urls) kurzu se dozvíte, jak sestavit adresy URL pro streamování s využitím .NET. Pokud spustíte aplikaci, jedna z adres URL odkazuje na manifest technologie Smooth Streaming: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/> Zkopírujte a vložte adresu URL do adresního řádku prohlížeče. Soubor se stáhne. Lze jej otevřít v textovém editoru podle vašeho výběru.

Příklad REST, naleznete v tématu [nahrávání, kódování a streamování souborů s využitím REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Monitorování s přenosovou rychlostí streamu videa

Můžete použít [stránky ukázku Azure Media Player](http://aka.ms/azuremediaplayer) monitorování přenosové rychlosti datový proud videa. Ukázka stránka zobrazuje diagnostické informace na **diagnostiky** kartu:

![Diagnostika Azure Media Player][amp_diagnostics]
 
### <a name="examples-urls-with-filters-in-query-string"></a>Příklady: Adresy URL s filtry v řetězci dotazu

Nastavte filtry pro ABR streamovací protokoly: HLS, MPEG-DASH a Smooth Streaming. V následující tabulce jsou uvedeny příklady adresy URL s filtry:

|Protocol|Příklad|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Technologie Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Interpretace filtrování

Je možné zakódovat asset více kódování profilů (směrného H.264 plánu, H.264 vysoké, AACL, AACH, Dolby digitální Plus) a více přenosových rychlostí kvality. Profily a přenosových rychlostí všech prostředku se ale nepodporují všechna zařízení klienta. Například starší zařízení se systémem Android podporovat jen H.264 směrného plánu + AACL. Odesílání vyšší přenosových rychlostí na zařízení, který nemůže získat výhody plýtvá výpočtu šířky pásma a zařízení. Taková zařízení musíte dekódovat všechny zadané informace, jenom na vertikálně snížit kapacitu pro zobrazení.

Díky dynamický Manifest, můžete vytvořit profily zařízení (jako jsou mobilní zařízení, konzoly nebo HD, High Density/SD) a zahrnuje sleduje a vlastnosti, které mají být součástí každý profil. Interpretace filtrování, která je volána. Následující diagram ukazuje příklad ho.

![Příklad interpretace filtrování][renditions2]

V následujícím příkladu byl použit pro kodér má kódovat mezzanine asset do sedmi interpretace video soubory MP4 rychlostmi ISO (z 180p 1080p). Může být zakódovanému assetu [dynamicky zabalené](dynamic-packaging-overview.md) do některé z následujících protokolů streamování: HLS, MPEG DASH a Smooth. 

Horní následující diagram ukazuje, že že HLS manifest pro prostředek s žádné filtry. (Obsahuje všechny sedm interpretace.)  V levé dolní diagram znázorňuje manifest HLS, do které byl použit filtr s názvem "ott". Filtr "ott" Určuje odebrání všech přenosových rychlostí nižší než 1 MB/s, takže dolní dvě úrovně kvality se odstraní v odpovědi. V pravém dolním diagram znázorňuje manifest HLS, do které byl použit filtr s názvem "mobilní". "Mobilní" filtr určuje odebrání interpretace kde rozlišení je větší než 720p, aby dvě 1080 p interpretace byla odebrána.

![Interpretace filtrování][renditions1]

## <a name="removing-language-tracks"></a>Odebrání jazyka sleduje
Vaše prostředky mohou zahrnovat více zvuku jazyků, jako je angličtina, španělština, francouzština, atd. Obvykle správci Player SDK výchozí výběr zvukové stopy a k dispozici zvuk sleduje podle výběru uživatele.

Vývoj těchto sad SDK Player je snadné, protože vyžaduje jedná o rozdílné implementace na přehrávačů konkrétní zařízení. Na některých platformách také Player rozhraní API jsou omezené a neobsahují zvukový výběr funkce, kde uživatelé nemohou vybrat nebo změnit výchozí zvukové stopy. S filtry asset můžete řídit chování tak, že vytvoříte filtry, které obsahují pouze požadované zvuku jazyky.

![Filtrování jazyk stop][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>Ořízne počáteční prostředek

Ve většině živě streamovaných událostí operátoři spustit některé testy před skutečné události. Například může patří mezi ně břidlicová takto před začátkem události: "Programu Zbývá: okamžik." 

Pokud program je archivace, testování a slatu data jsou také archivovat a zahrnuty v prezentaci. Tyto informace však by neměly být uváděny klientům. S dynamický Manifest můžete vytvořit filtr času spuštění a odebrat nežádoucí data z manifestu.

![Oříznutí start][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Vytváření z živý archív subclips (zobrazení)

Mnoho živé události jsou spuštěna po dlouhou dobu a živý archív může zahrnovat více událostí. Až živá událost skončí, provozovatele vysílání může být vhodné rozdělit živý archív do spuštění programu logické a ukončení sekvence. 

Tyto virtuální aplikace můžete publikovat samostatně bez příspěvek zpracování živý archív a nevytváří samostatné prostředky (který nezíská výhodou existující fragmenty v mezipaměti CDN). Příklady těchto virtuálních aplikací jsou čtvrtiny football nebo Basketbalový hru, innings v baseballu nebo jednotlivé události každého programu sportu.

S dynamický Manifest můžete vytvořit filtry pomocí počátečním/koncovém času a vytvořit virtuální zobrazení v horní části živý archív. 

![Filtr dílčího klipu][subclip_filter]

Tady je filtrovaná prostředku:

![Lyžování][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>Úprava okna prezentace (DVR)

V současné době Azure Media Services nabízí cyklické archivu, kde můžete nakonfigurovat doba trvání mezi 5 minutami a 25 hodin. Manifestu filtrování je možné vytvořit postupné okna DVR v horní části archivu, bez odstranění média. Existuje mnoho scénářů, kde chcete poskytnout omezené okna DVR přesunout pomocí živých edge a současně zachovat větší archivační okno vysílání. Provozovatel vysílání vhodné využít data, která je mimo okno DVR zvýrazněte klipy nebo může být vhodné poskytnout jiný záznam ze systému windows pro různá zařízení. Většina mobilních zařízení například nezpracovávají záznam ze systému windows (okna DVR – 2minutová pro mobilní zařízení a 1 hod. můžete mít klienty klasické pracovní plochy).

![Okna DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Úprava LiveBackoff (živé pozice)

Filtrování manifestu slouží k odebrání za provozu edge živou program několik sekund. Filtrování umožňuje provozovatele vysílání a podívejte se na prezentaci v bodě publikování ve verzi preview vytvořit oznámení o inzerovaném programu body vložení předtím, než čtenáři příjem datového proudu (se opírá o 30 sekund). Vysílání pak můžete odeslat těchto oznámení o inzerovaných programech do jejich klientská rozhraní v čase pro ně pro příjem a zpracování informací před příležitost oznámení o inzerovaném programu.

Kromě podpory oznámení o inzerovaném programu je možné nastavení živé regresní upravit umístění prohlížeče tak, aby když se klienti odchylek a přístupů na hraničních zařízeních za provozu, můžete stále dostanou fragmenty ze serveru. Tímto způsobem, klienti nebudou získat chybu HTTP 404 nebo 412 chyby.

![Filtr pro živé regresní][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Sloučení více pravidel v jednom filtru

Můžete zkombinovat více pravidel filtrování v jeden filtr. Jako příklad lze definovat pravidlo"rozsah" odebrání živý archív slaty a také vyfiltrovat dostupné přenosových rychlostí. Pokud chcete aplikovat více pravidel filtrování, konečný výsledek je průsečík všechna pravidla.

![Více pravidel filtrování][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Kombinace více filtrů (složení filtru)

Můžete také kombinovat několik filtrů v jedné adresy URL. Následující příklad ukazuje, proč můžete chtít filtry zkombinovat:

1. Potřebujete filtrovat vaše video kvality pro mobilní zařízení, třeba na Android nebo iPad (aby bylo možné omezit videa kvality). Pokud chcete odebrat nežádoucí kvality, vytvoříte filtr účet vhodný pro profily zařízení. Filtry účtů můžete použít pro všechny vaše prostředky v rámci stejného účtu Media Services bez dalšího přidružení.
1. Chcete oříznout počáteční a koncový čas prostředku. Za tím účelem vytvoříte filtr asset a nastavte počáteční/koncový čas. 
1. Chcete obě tyto filtry zkombinovat. Bez kombinaci je třeba přidat kvalitní filtrování filtrování ořezávání, což by mohlo způsobit nepoužitelnost použití filtru obtížnější.


Zkombinovat filtry, je nutné nastavit filtr názvů do manifestu/stop adresy URL ve formátu oddělené středníky. Předpokládejme, že máte filtru s názvem *MyMobileDevice* , která filtruje kvality a druhou s názvem máte *MyStartTime* nastavit konkrétní čas spuštění. Můžete kombinovat až tři filtry. 

Další informace najdete v tématu [tento příspěvek na blogu](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="considerations-and-limitations"></a>Požadavky a omezení

- Hodnoty pro **forceEndTimestamp**, **presentationWindowDuration**, a **liveBackoffDuration** by se neměla nastavovat pro filtr videa na Vyžádání. Slouží pouze pro scénáře živé filtru. 
-  Dynamický manifest funguje v hranicích GOP (klíčových snímků), takže ořezávání má GOP přesnost.
-  Můžete použít stejný název filtru pro účet a asset filtry. Asset filtry mají vyšší prioritu a přepíše filtry účtů.
- Pokud aktualizujete filtr, může trvat až 2 minuty, než koncový bod streamování se aktualizovat pravidla. Pokud jste použili filtry pro obsluhu obsah (Uložit do mezipaměti obsah v proxy a CDN ukládá do mezipaměti), aktualizuje se tyto filtry může způsobit selhání přehrávače. Doporučujeme, abyste vymazání mezipaměti po aktualizaci filtr. Pokud tato možnost není možné, zvažte použití jiného filtru.
- Zákazníci musí ručně stáhněte manifest a analyzovat start přesné časové razítko a časové měřítko.
    
    - Chcete-li zjistit vlastnosti v prostředku, která sleduje [získat a zkontrolujte soubor manifestu](#get-and-examine-manifest-files).
    - Vzorce můžete nastavit vlastnosti časového razítka filtru asset je: <br/>startTimestamp = &lt;čas spuštění v manifestu&gt; +  &lt;očekávaný čas zahájení filtru v řádu sekund&gt; * časové osy

## <a name="next-steps"></a>Další kroky

Následující články popisují, jak programově vytvářet filtry:  

- [Vytváření filtrů pomocí rozhraní REST API](filters-dynamic-manifest-rest-howto.md)
- [Vytváření filtrů pomocí .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Vytváření filtrů pomocí rozhraní příkazového řádku](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
