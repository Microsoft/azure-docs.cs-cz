---
title: Filtrování manifestů pomocí dynamického balení
titleSuffix: Azure Media Services
description: Zjistěte, jak vytvořit filtry pomocí dynamického balíčku k filtrování a selektivnímu streamování manifestů.
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
ms.openlocfilehash: cd955f97a2f26543f799d95b7dc0b1de235333c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186211"
---
# <a name="filter-your-manifests-using-dynamic-packager"></a>Filtrování manifestů pomocí dynamického balení

Když do zařízení dodáváte obsah s adaptivním datovým tokem, někdy je třeba publikovat více verzí manifestu, aby bylo možné cílit na konkrétní možnosti zařízení nebo dostupnou šířku pásma sítě. [Dynamický balíček](dynamic-packaging-overview.md) umožňuje určit filtry, které mohou průběžně odfiltrovat konkrétní kodeky, rozlišení, přenosové rychlosti a kombinace zvukových stop. Toto filtrování odstraní potřebu vytvořit více kopií. Stačí publikovat novou adresu URL s konkrétní sadou filtrů nakonfigurovaných pro vaše cílová zařízení (iOS, Android, SmartTV nebo prohlížeče) a síťovými možnostmi (scénáře s vysokou šířkou pásma, mobilní nebo s malou šířkou pásma). V takovém případě mohou klienti manipulovat s datovým proudem obsahu prostřednictvím řetězce dotazu (zadáním dostupných [filtrů datových zdrojů nebo filtrů účtů)](filters-concept.md)a používat filtry k vysílání datových proudů určitých částí datového proudu.

Některé scénáře doručení vyžadují, abyste se ujistili, že zákazník nemá přístup ke konkrétním stopy. Například možná nechcete publikovat manifest, který obsahuje hd stopy na konkrétní úroveň odběratele. Nebo možná budete chtít odebrat konkrétní adaptivní datový tok (ABR) stopy snížit náklady na doručení na konkrétní zařízení, které by neměly prospěch z dalších stop. V takovém případě můžete při vytváření přidružit seznam předem vytvořených filtrů k [lokátoru streamování.](streaming-locators-concept.md) Klienti pak nemohou manipulovat s tím, jak je obsah streamován, protože je definován **lokátorem streamování**.

Filtrování můžete kombinovat pomocí zadání [filtrů v lokátoru streamování](filters-concept.md#associating-filters-with-streaming-locator) + dalšífiltry specifické pro zařízení, které klient určí v adrese URL. Tato kombinace je užitečná k omezení dalších stop, jako jsou metadata nebo datové proudy událostí, jazyky zvuku nebo popisné zvukové stopy.

Tato možnost zadat různé filtry ve vašem datovém proudu poskytuje výkonné řešení manipulace **dynamického manifestu** pro cílení více scénářů použití pro vaše cílová zařízení. Toto téma vysvětluje koncepty související s **dynamickými manifesty** a uvádí příklady scénářů, ve kterých můžete tuto funkci použít.

> [!NOTE]
> Dynamické manifesty nemění datový zdroj a výchozí manifest pro tento datový zdroj.

## <a name="overview-of-manifests"></a>Přehled manifestů

Azure Media Services podporuje protokoly HLS, MPEG DASH a Smooth Streaming. Jako součást [dynamického balení](dynamic-packaging-overview.md)jsou manifesty streamovaného klienta (HLS Master Playlist, DASH Media Presentation Description [MPD] a Smooth Streaming) dynamicky generovány na základě voliče formátu v adrese URL. Další informace naleznete v protokolech doručování v [běžném pracovním postupu na vyžádání](dynamic-packaging-overview.md#delivery-protocols).

### <a name="get-and-examine-manifest-files"></a>Získání a prozkoumání souborů manifestu

Zadáte seznam podmínek vlastností sledování filtru na základě toho, které stopy vašeho datového proudu (živé nebo video na vyžádání [VOD]) by měly být zahrnuty do dynamicky vytvořeného manifestu. Chcete-li získat a prozkoumat vlastnosti stop, musíte nejprve načíst manifest Vyhlazení datových proudů.

Kurz [Nahrávání, kódování a streamování souborů pomocí rozhraní .NET ukazuje,](stream-files-tutorial-with-api.md#get-streaming-urls) jak vytvářet adresy URL streamování pomocí rozhraní .NET. Pokud aplikaci spustíte, jedna z adres URL odkazuje `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`na manifest Plynulé streamování: .<br/> Zkopírujte a vložte adresu URL do adresního řádku prohlížeče. Soubor bude stažen. Můžete jej otevřít v libovolném textovém editoru.

Příklad REST najdete v [tématu Nahrávání, kódování a streamování souborů pomocí REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Sledování datového toku datového proudu videa

Demo [stránku přehrávače Azure Media Player](https://aka.ms/azuremediaplayer) můžete použít ke sledování datového toku datového proudu videa. Na stránce ukázky se zobrazují diagnostické informace na kartě **Diagnostika:**

![Diagnostika přehrávače médií Azure][amp_diagnostics]

### <a name="examples-urls-with-filters-in-query-string"></a>Příklady: adresy URL s filtry v řetězci dotazu

Filtry můžete použít pro streamovací protokoly ABR: HLS, MPEG-DASH a Smooth Streaming. V následující tabulce jsou uvedeny některé příklady adres URL s filtry:

|Protocol (Protokol)|Příklad|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Technologie Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Filtrování interpretací

Můžete se rozhodnout kódovat datový zdroj do více profilů kódování (H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) a více kvalitních datových toky. Ne všechna klientská zařízení však budou podporovat všechny profily a datové sazby vašeho datového zdroje. Například starší zařízení Android podporují pouze H.264 Baseline + AACL. Odesílání vyšších datových toků do zařízení, které nemůže získat výhody, ztrácí šířku pásma a výpočty zařízení. Takové zařízení musí dekódovat všechny dané informace, pouze proto, aby je zmenšeno pro zobrazení.

Pomocí dynamického manifestu můžete vytvářet profily zařízení (například mobilní zařízení, konzole nebo HD/SD) a zahrnout stopy a vlastnosti, které chcete být součástí každého profilu. Tomu se říká filtrování interpretací. Následující diagram znázorňuje jeho příklad.

![Příklad filtrování interpretací pomocí dynamického manifestu][renditions2]

V následujícím příkladu byl k zakódování mezaninového datového zdroje použit kodér do sedmi interpretací videa ISO MP4 (od 180p do 1080p). Kódovaný datový zdroj lze [dynamicky zabalit](dynamic-packaging-overview.md) do některého z následujících protokolů streamování: HLS, MPEG DASH a Smooth.

V horní části následujícího diagramu je zobrazen manifest HLS pro datový zdroj bez filtrů. (Obsahuje všech sedm interpretací.)  V levém dolním rohu diagramu znázorňuje manifest HLS, na který byl použit filtr s názvem "ott". Filtr "ott" určuje odstranění všech přenosových rychlostí pod 1 Mb/s, takže dolní dvě úrovně kvality byly v odpovědi odstraněny. V pravém dolním dolním obrázku diagram udává manifest HLS, na který byl použit filtr s názvem "mobile". Filtr "mobilní" určuje odstranění interpretací, kde je rozlišení větší než 720p, takže dvě interpretace 1080p byly odstraněny.

![Filtrování interpretací pomocí dynamického manifestu][renditions1]

## <a name="removing-language-tracks"></a>Odebrání jazykových stop
Vaše datové zdroje mohou zahrnovat více zvukových jazyků, jako je angličtina, španělština, francouzština a tak dále. Sada Player SDK obvykle spravuje výchozí výběr zvukových stop a dostupné zvukové stopy podle výběru uživatele.

Vývoj takových sad SDK přehrávače je náročný, protože vyžaduje různé implementace v rámci přehrávače specifické pro zařízení. Na některých platformách jsou také uživatelská api omezená a nezahrnují funkci výběru zvuku, ve které uživatelé nemohou vybrat nebo změnit výchozí zvukovou stopu. Pomocí filtrů datových zdrojů můžete řídit chování vytvořením filtrů, které obsahují pouze požadované jazyky zvuku.

![Filtrování jazykových stop pomocí dynamického manifestu][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>Oříznutí začátku datového zdroje

Ve většině živých přenosových událostí operátory spustit některé testy před skutečnou událost. Mohou například před začátkem akce obsahovat takový břidlicový štít: "Program začne na okamžik."

Pokud je program archivován, jsou data testu a břidlice také archivována a zahrnuta do prezentace. Tyto informace by se však neměly zobrazovat klientům. Pomocí dynamického manifestu můžete vytvořit filtr času zahájení a odebrat nežádoucí data z manifestu.

![Oříznutí začátku datového zdroje pomocí dynamického manifestu][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Vytváření dílčích klipů (zobrazení) z živého archivu

Mnoho živých událostí je dlouho spuštěno a živý archiv může zahrnovat více událostí. Po ukončení živé události mohou provozovatelé vysílání chtít rozdělit živý archiv na logické sekvence spuštění a zastavení programu.

Tyto virtuální programy můžete publikovat samostatně bez následného zpracování živého archivu a bez vytváření samostatných datových zdrojů (což nevyužívá výhod existujících fragmentů v mezipaměti v cdn). Příklady takových virtuálních programů jsou čtvrtiny fotbalového nebo basketbalového zápasu, směn v baseballu nebo jednotlivých událostí jakéhokoli sportovního programu.

Pomocí dynamického manifestu můžete vytvářet filtry pomocí počátečních a koncových časů a vytvářet virtuální zobrazení v horní části živého archivu.

![Filtr podklipu s dynamickým manifestem][subclip_filter]

Tady je filtrovaný datový zdroj:

![Filtrovaný datový zdroj s dynamickým manifestem][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>Nastavení okna prezentace (DVR)

V současné době Azure Media Services nabízí kruhový archiv, kde lze nakonfigurovat dobu trvání mezi 1 minuta - 25 hodin. Filtrování manifestu lze použít k vytvoření postupného okna DVR nad horní částí archivu bez odstranění média. Existuje mnoho scénářů, kdy provozovatelé vysílání chtějí poskytnout omezené okno DVR pro pohyb s živým okrajem a současně zachovat větší okno archivace. Provozovatel vysílání může chtít použít data, která jsou mimo okno DVR, ke zvýraznění klipů, nebo může chtít poskytnout různá okna DVR pro různá zařízení. Například většina mobilních zařízení nezpracovává velká okna DVR (můžete mít 2minutové okno DVR pro mobilní zařízení a jednu hodinu pro klienty pro stolní počítače).

![Okno DVR s dynamickým manifestem][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Nastavení LiveBackoff (živá poloha)

Filtrování manifestu lze použít k odstranění několika sekund z živého okraje živého programu. Filtrování umožňuje vysílatelům sledovat prezentaci v bodě publikace náhledu a vytvářet body vložení reklamy před tím, než diváci obdrží datový proud (zálohováno o 30 sekund). Provozovatelé vysílání pak mohou tyto reklamy zasouvat do svých klientských rámců včas, aby mohli přijímat a zpracovávat informace před příležitostí k inzerci.

Kromě podpory reklamy lze nastavení živého back-off použít k nastavení polohy diváků tak, aby při zákaznících drift a hit živé hrany, mohou stále získat fragmenty ze serveru. Tímto způsobem klienti nedostanou chybu HTTP 404 nebo 412.

![Filtr pro živé back-off s dynamickým manifestem][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Kombinace více pravidel v jednom filtru

V jednom filtru můžete kombinovat více pravidel filtrování. Můžete například definovat "pravidlo rozsahu" pro odstranění břidlic z živého archivu a také odfiltrovat dostupné přenosové rychlosti. Při použití více pravidel filtrování je konečným výsledkem průsečík všech pravidel.

![Více pravidel filtrování s dynamickým manifestem][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Kombinace více filtrů (složení filtru)

Můžete také kombinovat více filtrů v jedné adrese URL. Následující scénář ukazuje, proč můžete chtít kombinovat filtry:

1. Je třeba filtrovat vlastnosti videa pro mobilní zařízení, jako je Android nebo iPad (aby se omezily vlastnosti videa). Chcete-li odstranit nežádoucí vlastnosti, vytvoříte filtr účtu vhodný pro profily zařízení. Filtry účtů můžete použít pro všechny své datové zdroje pod stejným účtem Mediálních služeb bez dalšího přidružení.
1. Chcete také zkrátit počáteční a koncový čas datového zdroje. Chcete-li provést oříznutí, vytvoříte filtr majetku a nastavíte čas zahájení a ukončení.
1. Chcete kombinovat oba tyto filtry. Bez kombinace byste museli do filtru oříznutí přidat filtrování kvality, což by ztížilo použití filtru.

Chcete-li kombinovat filtry, nastavte názvy filtrů na adresu URL manifestu nebo seznamu stop ve formátu odděleném středníkem. Předpokládejme, že máte filtr s názvem *MyMobileDevice,* který filtruje vlastnosti a máte jiný s názvem *MyStartTime* nastavit konkrétní čas zahájení. Můžete kombinovat až tři filtry.

Další informace naleznete v [tomto příspěvku blogu](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="considerations-and-limitations"></a>Důležité informace a omezení

- Hodnoty **forceEndTimestamp**, **presentationWindowDuration**a **liveBackoffDuration** by neměly být nastaveny pro filtr VOD. Používají se pouze pro scénáře živého filtru.
- Dynamický manifest funguje v hranicích GOP (klíčové snímky), takže oříznutí má přesnost GOP.
- Pro filtry účtů a majetku můžete použít stejný název filtru. Filtry majetku mají vyšší prioritu a přepíší filtry účtů.
- Pokud aktualizujete filtr, může trvat až 2 minuty, než koncový bod streamování aktualizuje pravidla. Pokud jste k zobrazení obsahu použili filtry (a obsah jste uvázali do mezipaměti proxy a cdn), může aktualizace těchto filtrů způsobit selhání přehrávače. Doporučujeme vymazat mezipaměť po aktualizaci filtru. Pokud tato možnost není možná, zvažte použití jiného filtru.
- Zákazníci musí ručně stáhnout manifest a analyzovat přesné počáteční časové razítko a časové měřítko.

    - Chcete-li určit vlastnosti stop v datovém zdroji, [získejte a zkontrolujte soubor manifestu](#get-and-examine-manifest-files).
    - Vzorec pro nastavení vlastností časového razítka filtru majetku je: <br/>startTimestamp &lt;= počáteční čas&gt; +  &lt;v očekávaném&gt; čase spuštění filtru v sekundách * časová osa

## <a name="next-steps"></a>Další kroky

Následující články ukazují, jak programově vytvářet filtry:  

- [Vytváření filtrů pomocí rest API](filters-dynamic-manifest-rest-howto.md)
- [Vytváření filtrů pomocí .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Vytváření filtrů pomocí cli](filters-dynamic-manifest-cli-howto.md)

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
