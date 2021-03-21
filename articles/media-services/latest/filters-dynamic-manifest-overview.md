---
title: Filtrování manifestů pomocí dynamického balíčku
description: Naučte se vytvářet filtry pomocí dynamického balíčku pro filtrování a selektivní streamování manifestů.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 3ffdb41752630e0e5e22303ff58ecd798595a890
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897658"
---
# <a name="filter-your-manifests-using-dynamic-packager"></a>Filtrování manifestů pomocí dynamického balíčku

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Při doručování obsahu streamování s adaptivní přenosovou rychlostí do zařízení někdy potřebujete publikovat více verzí manifestu, aby se mohly zaměřit konkrétní možnosti zařízení nebo dostupná šířka pásma sítě. [Dynamický balíček](dynamic-packaging-overview.md) vám umožní určit filtry, které mohou vyfiltrovat konkrétní kodeky, rozlišení, přenosové rychlosti a kombinace zvukového stopu průběžně. Toto filtrování odstraní nutnost vytvářet více kopií. Jednoduše potřebujete publikovat novou adresu URL s konkrétní sadou filtrů nakonfigurovaných na vaše cílová zařízení (iOS, Android, byznysu nebo prohlížeče) a možnosti sítě (scénáře s vysokou šířkou pásma, mobilním nebo malou šířkou pásma). V takovém případě můžou klienti manipulovat s datovým proudem vašeho obsahu prostřednictvím řetězce dotazu (zadáním dostupných [filtrů assetů nebo filtrů účtu](filters-concept.md)) a pomocí filtrů streamovat konkrétní oddíly streamu.

Některé scénáře doručování vyžadují, abyste se ujistili, že zákazník nemá přístup ke konkrétním stopám. Například je možné, že nechcete publikovat manifest, který obsahuje HD stopy, do konkrétní vrstvy odběratele. Nebo možná budete chtít odebrat konkrétní stopy s adaptivní přenosovou rychlostí (ABR), abyste snížili náklady na doručování na konkrétní zařízení, které by nebylo přínosem dalších stop. V takovém případě můžete přidružit seznam předem vytvořených filtrů ke [lokátoru streamování](streaming-locators-concept.md) při vytváření. Klienti pak nemohou manipulovat s tím, jak je obsah streamovaná, protože je definovaný **lokátorem streamování**.

Filtrování můžete kombinovat zadáním [filtrů na lokátoru streamování](filters-concept.md#associating-filters-with-streaming-locator) a dalších filtrů specifických pro zařízení, které klient ZADÁ v adrese URL. Tato kombinace je užitečná k omezení dalších stop, jako jsou metadata nebo datové proudy událostí, zvukové jazyky nebo popisné zvukové stopy.

Tato možnost zadat různé filtry v datovém proudu poskytuje výkonné řešení pro manipulaci s **dynamickým manifestem** , které cílí na více scénářů použití v případě vašich cílových zařízení. Toto téma vysvětluje koncepty související s **dynamickými manifesty** a obsahuje příklady scénářů, ve kterých můžete použít tuto funkci.

> [!NOTE]
> Dynamické manifesty nemění Asset a výchozí manifest pro daný prostředek.

## <a name="overview-of-manifests"></a>Přehled manifestů

Azure Media Services podporuje protokoly HLS, MPEG POMLČKy a Smooth Streaming. V rámci [dynamického balení](dynamic-packaging-overview.md)se dynamicky generují manifesty klienta streamování (HLS hlavní seznam testů, pomlčka multimediální prezentace [MPD] a Smooth Streaming) na základě selektor formátu v adrese URL. Další informace najdete v tématu Delivery Protocols ( [běžné pracovní postupy na vyžádání](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery)).

### <a name="get-and-examine-manifest-files"></a>Získání a kontrola souborů manifestu

Určíte seznam stavů filtru sledování, které závisí na tom, které stopy datového proudu (živé nebo video na vyžádání [VOD]) by měly být součástí dynamicky vytvořeného manifestu. Chcete-li získat a prohlédnout vlastnosti stop, je třeba nejprve načíst manifest Smooth Streaming.

Kurz [nahrávání, kódování a streamování souborů pomocí .NET](stream-files-tutorial-with-api.md#get-streaming-urls) vám ukáže, jak vytvořit adresy URL streamování pomocí .NET. Pokud aplikaci spouštíte, jedna z adres URL ukazuje na manifest Smooth Streaming: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest` .<br/> Zkopírujte a vložte adresu URL do panelu Adresa v prohlížeči. Soubor se stáhne. Můžete ho otevřít v libovolném textovém editoru.

Příklad REST najdete v tématu [nahrání, kódování a streamování souborů pomocí REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Monitorování přenosové rychlosti streamu videa

Ke sledování přenosové rychlosti streamu videa můžete použít [stránku Azure Media Player demo](https://aka.ms/azuremediaplayer) . Ukázková stránka zobrazuje diagnostické informace na kartě **Diagnostika** :

![Diagnostika Azure Media Player][amp_diagnostics]

### <a name="examples-urls-with-filters-in-query-string"></a>Příklady: adresy URL s filtry v řetězci dotazu

Můžete použít filtry na protokoly streamování ABR: HLS, MPEG-POMLČKa a Smooth Streaming. V následující tabulce jsou uvedeny některé příklady adres URL s filtry:

|Protokol|Příklad|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Technologie Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Filtrování verzí

Můžete si vybrat, že se má Asset zakódovat do několika profilů kódování (H. 264, H. 264 High, AACL, AACH, Dolby Digital Plus) a více přenosů kvality. Ale ne všechna klientská zařízení budou podporovat všechny profily a přenosové rychlosti vašeho prostředku. Například starší zařízení s Androidem podporují pouze H. 264 standardní hodnoty + AACL. Posílání vyšších přenosů do zařízení, které nedokáže získat výhody pro odpad a využití šířky pásma a zařízení. Takové zařízení musí dekódovat všechny dané informace, a to pouze pro horizontální navýšení kapacity pro zobrazení.

Pomocí dynamického manifestu můžete vytvořit profily zařízení (například mobilní zařízení, konzolu nebo HD/SD) a zahrnout stopy a kvality, které chcete být součástí každého profilu. Nazývá se filtrování verzí. V následujícím diagramu je znázorněn příklad.

![Příklad filtrování verzí s dynamickým manifestem][renditions2]

V následujícím příkladu byl kodér použit ke kódování Mezzanine prostředku do sedmi verzí rychlostmi videa ISO (od 180p do 1080p). Kódovaný prostředek se dá [dynamicky balit](dynamic-packaging-overview.md) do libovolného z následujících protokolů streamování: HLS, MPEG pomlčka a hladký.

V horní části následujícího diagramu se zobrazuje manifest HLS pro Asset bez filtrů. (Obsahuje všechny sedm verzí.)  V levém dolním rohu se v diagramu zobrazí manifest HLS, na který se použil filtr s názvem "OTT". Filtr "OTT" Určuje odstranění všech přenosů pod 1 MB/s, takže v odpovědi byly odstraněny dolní dvě úrovně kvality. V pravém dolním rohu diagram zobrazuje manifest HLS, na který se použil filtr s názvem "mobilní". Filtr "mobilní" Určuje odebrání verzí, u kterých je rozlišení větší než 720p, takže dvě verze 1080p byly odstraněny.

![Filtrování verzí s dynamickým manifestem][renditions1]

## <a name="removing-language-tracks"></a>Odebírají se stopy jazyka.
Vaše prostředky můžou zahrnovat několik zvukových jazyků, jako je angličtina, španělština, francouzština a tak dále. Sada SDK přehrávače obvykle spravuje výběr výchozí zvukové stopy a dostupné zvukové stopy na výběr uživatele.

Vývoj takových sad SDK pro hráče je náročný, protože vyžaduje různé implementace napříč platformami přehrávače pro konkrétní zařízení. Na některých platformách jsou taky rozhraní API přehrávače omezená a neobsahují funkci výběru zvuku, kde uživatelé nemůžou vybrat nebo změnit výchozí zvukovou stopu. Pomocí filtrů assetů můžete chování řídit vytvořením filtrů, které obsahují jenom požadované zvukové jazyky.

![Filtrování jazykových běhů s dynamickým manifestem][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>Oříznutí začátku assetu

Ve většině událostí živého streamování operátory spouští některé testy před skutečnou událostí. Například mohou zahrnovat jako tuto hodnotu SLAT před začátkem události: "program začne za chvíli."

Pokud je program archivován, data testu a SLAT jsou také archivována a součástí prezentace. Tyto informace by však neměly být zobrazeny klientům. S dynamickým manifestem můžete vytvořit filtr času spuštění a odebrat nežádoucí data z manifestu.

![Oříznutí začátku assetu s dynamickým manifestem][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Vytváření dílčích klipů (zobrazení) z živého archivu

Spousta živých událostí je dlouhodobě spuštěná a živý archiv může zahrnovat víc událostí. Po skončení živé události budou všesměrové vysílání chtít přerušit živý archiv do sekvence spuštění a zastavení logického programu.

Tyto virtuální programy můžete publikovat samostatně bez následného zpracování živého archivu a nevytvářejte samostatné prostředky (což nezíská výhodu z existujících fragmentů uložených v mezipaměti v sítě CDN). Příklady takových virtuálních programů jsou čtvrtiny fotbalu nebo basketbalový hry, innings v baseballu nebo jednotlivé události jakéhokoli sportovního programu.

S dynamickým manifestem můžete vytvářet filtry pomocí počátečního/koncového času a vytvářet virtuální zobrazení v horní části živého archivu.

![Filtr dílčích klipů s dynamickým manifestem][subclip_filter]

Tady je filtrovaný prostředek:

![Filtrovaný prostředek s dynamickým manifestem][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>Úprava okna prezentace (DVR)

V současné době Azure Media Services nabízí kruhový archiv, ve kterém je možné nastavit dobu trvání mezi 1 minutou až 25 hodinami. Filtrování manifestu lze použít k vytvoření okna postupného DVR v horní části archivu, aniž byste museli odstraňovat média. Existuje řada scénářů, ve kterých mají všesměrová vysílání poskytnout omezené okno DVR pro přesun s živým okrajem a zároveň zachovat větší archivační interval. Všesměrové vysílání může chtít použít data z okna zaregistrování k zvýraznění klipů nebo může chtít pro různá zařízení poskytnout různá okna se systémem DVR. Například většina mobilních zařízení nezpracovává velká okna ve službě DVR (pro mobilní zařízení a jednu hodinu pro klienty pro stolní počítače můžete mít dvě minuty).

![Okno DVR s dynamickým manifestem][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Úprava LiveBackoff (živá pozice)

Filtrování manifestu lze použít k odebrání několika sekund z živého okraje živého programu. Filtrování umožňuje vysílání sledovat prezentaci v bodu publikace Preview a vytvářet body vkládání reklamy, než čtenáři obdrží datový proud (zálohovaný 30 sekund). Všesměrové vysílání pak tyto reklamy můžou do svých klientských rozhraní nabízet včas, aby je mohli přijímat a zpracovávat před příležitostí k reklamě.

Kromě podpory inzerce je možné pomocí živého Back-off nastavit umístění čtenářů, aby se klienti při posunu a dosáhli živého okraje, ale můžou z tohoto serveru získat fragmenty. Klienti tak nebudou mít chybu HTTP 404 nebo 412.

![Filtr pro živé zálohování s dynamickým manifestem][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Kombinování více pravidel v jednom filtru

V jednom filtru můžete zkombinovat více pravidel filtrování. Můžete například definovat "pravidlo rozsahu" pro odebrání SLAT z živého archivu a také odfiltrovat dostupné přenosové rychlosti. Při použití více pravidel filtrování je konečným výsledkem průnik všech pravidel.

![Vícenásobná pravidla filtrování s dynamickým manifestem][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Kombinování více filtrů (složení filtru)

V jedné adrese URL můžete také kombinovat více filtrů. Následující scénář ukazuje, proč byste mohli chtít kombinovat filtry:

1. Potřebujete filtrovat kvality videa pro mobilní zařízení, jako je Android nebo iPad (abyste omezili kvality videa). Chcete-li odebrat nechtěné kvality, vytvořte filtr účtu vhodný pro profily zařízení. Filtry účtu můžete použít pro všechny vaše prostředky v rámci stejného Media Services účtu bez dalšího přidružení.
1. Také chcete zkrátit čas zahájení a ukončení assetu. Chcete-li provést ořezávání, vytvoříte filtr assetu a nastavíte počáteční/koncový čas.
1. Chcete kombinovat oba tyto filtry. Bez kombinace byste museli do filtru oříznutí přidat filtrování kvality, což by mohlo ztížit použití filtru.

Chcete-li kombinovat filtry, nastavte názvy filtrů na adresu URL manifestu nebo seznamu stop ve formátu odděleném středníkem. Předpokládejme, že máte filtr s názvem *MyMobileDevice* , který filtruje kvality a máte další pojmenovaný *MyStartTime* pro nastavení konkrétního počátečního času. Můžete zkombinovat až tři filtry.

Další informace najdete v [tomto blogovém příspěvku](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="considerations-and-limitations"></a>Důležité informace a omezení

- Pro filtr VOD by se neměly nastavit hodnoty pro **forceEndTimestamp**, **presentationWindowDuration** a **liveBackoffDuration** . Používají se jenom pro scénáře živého filtru.
- Dynamický manifest funguje v hranicích skupinu GOP (klíčové snímky), takže ořezávání má skupinu GOPou přesnost.
- Pro účty a filtry assetů můžete použít stejný název filtru. Filtry prostředků mají vyšší prioritu a přepíšou filtry účtu.
- Pokud filtr aktualizujete, může trvat až 2 minuty, než koncový bod streamování aktualizuje pravidla. Pokud jste použili filtry pro poskytování obsahu (a ukládáte obsah do mezipaměti v proxy serverech a v mezipamětech CDN), může aktualizace těchto filtrů způsobit selhání přehrávače. Doporučujeme, abyste po aktualizaci filtru vymazali mezipaměť. Pokud tato možnost není možná, zvažte použití jiného filtru.
- Zákazníci potřebují stáhnout manifest ručně a analyzovat přesné časové razítko a časové měřítko času spuštění.

    - Chcete-li určit vlastnosti stop v assetu, [Získejte a zkontrolujte soubor manifestu](#get-and-examine-manifest-files).
    - Vzorec pro nastavení vlastností časového razítka filtru assetu je: <br/>startTimestamp = &lt; čas zahájení v manifestu &gt;  +   &lt; očekával čas spuštění filtru v sekundách &gt; * Časová osa

## <a name="next-steps"></a>Další kroky

Následující články ukazují, jak vytvořit filtry prostřednictvím kódu programu:  

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
