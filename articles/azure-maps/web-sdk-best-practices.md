---
title: Osvědčené postupy pro Azure Maps Web SDK | Mapy Microsoft Azure
description: Přečtěte si tipy & triky pro optimalizaci použití Azure Maps webové sady SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 3/22/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: ba351053c876c31d945ec7e4127a5caebd6a71ce
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104877859"
---
# <a name="azure-maps-web-sdk-best-practices"></a>Osvědčené postupy pro Azure Maps Web SDK

Tento dokument se zaměřuje na osvědčené postupy pro Azure Maps Web SDK, ale mnoho osvědčených postupů a optimalizací je možné použít pro všechny ostatní sady SDK Azure Maps.

Sada Azure Maps Web SDK poskytuje výkonné plátno pro vykreslování velkých prostorových dat mnoha různými způsoby. V některých případech existuje více způsobů, jak data sestavovat stejným způsobem, ale v závislosti na velikosti datové sady a požadované funkci může být jedna metoda vyšší než jiné. V tomto článku se vysvětlují osvědčené postupy a tipy a triky pro maximalizaci výkonu a vytváření hladkého uživatelského prostředí.

Obecně platí, že pokud chcete zlepšit výkon mapy, hledejte způsob, jak snížit počet vrstev a zdrojů, a složitost datových sad a stylů vykreslování, které jsou používány.

## <a name="security-basics"></a>Základy zabezpečení

Jedinou důležitou součástí vaší aplikace je zabezpečení. Pokud vaše aplikace nezabezpečuje hackera, může ruin jakoukoli aplikaci bez ohledu na to, jak to může být uživatelské prostředí. Zde jsou některé tipy, jak zajistit zabezpečení Azure Maps aplikace. Při používání Azure si proložte seznam nástrojů zabezpečení, které máte k dispozici. [Úvod do zabezpečení Azure](https://docs.microsoft.com/azure/security/fundamentals/overview)najdete v tomto dokumentu.

> [!IMPORTANT]
> Azure Maps poskytuje dvě metody ověřování.
> * Ověřování na základě klíče předplatného
> * Azure Active Directory ověřování používá Azure Active Directory ve všech produkčních aplikacích.
> Ověřování založené na klíčích předplatného je jednoduché a to, co většina používaných platforem mapování používá jako metodu pro měření využití platformy pro účely fakturace. Nejedná se ale o zabezpečenou formu ověřování a měla by se používat jenom místně při vývoji aplikací. Některé platformy poskytují možnost omezit, které IP adresy nebo odkazující HTTP jsou v žádostech, ale tyto informace mohou být snadno falešné. Pokud použijete klíče předplatného, nezapomeňte [je pravidelně střídat](how-to-manage-authentication.md#manage-and-rotate-shared-keys).
> Azure Active Directory je podniková služba identity, která má velký výběr funkcí a nastavení zabezpečení pro nejrůznější scénáře aplikací. Microsoft doporučuje, aby všechny provozní aplikace používaly Azure Maps Azure Active Directory k ověřování.
> Přečtěte si další informace o [správě ověřování v Azure Maps](how-to-manage-authentication.md) v tomto dokumentu.

### <a name="secure-your-private-data"></a>Zabezpečení privátních dat

Když se data přidají do Azure Maps interaktivních mapových sad SDK, vykreslí se místně na zařízení koncového uživatele a z jakéhokoli důvodu se z něj nikdy nepošle zpět na Internet.

Pokud vaše aplikace načítá data, která by neměla být veřejně přístupná, ujistěte se, že jsou data uložená v zabezpečeném umístění, jsou přístupná zabezpečeným způsobem a že je samotná aplikace uzamčená a dostupná jenom pro požadované uživatele. Pokud se některý z těchto kroků přeskočí, může mít neoprávněná osoba přístup k těmto datům. Azure Active Directory vám může pomoct s tímto zámkem.

V tomto kurzu najdete [Postup přidání ověřování do webové aplikace běžící na Azure App Service](https://docs.microsoft.com/azure/app-service/scenario-secure-app-authentication-app-service)

### <a name="use-the-latest-versions-of-azure-maps"></a>Použijte nejnovější verze nástroje Azure Maps

Sady Azure Maps SDK procházejí pravidelným testováním zabezpečení spolu s dalšími externími knihovnami závislostí, které mohou sady SDK používat. Jakékoli známé potíže se zabezpečením jsou vyřešeny včas a vydány do produkčního prostředí. Pokud vaše aplikace odkazuje na nejnovější hlavní verzi hostované verze Azure Maps Web SDK, automaticky získá všechny aktualizace dílčí verze, které budou obsahovat opravy související se zabezpečením.

Pokud Azure Maps webovou sadu SDK sami hostuje pomocí modulu NPM, nezapomeňte použít symbol stříšky (^) pro v kombinaci s číslem verze balíčku Azure Maps NPM v `package.json` souboru tak, aby se vždy odkazovalo na nejnovější dílčí verzi.

```json
"dependencies": {
  "azure-maps-control": "^2.0.30"
}
```

## <a name="optimize-initial-map-load"></a>Optimalizovat počáteční načtení mapy

Po načtení webové stránky se jedna z prvních věcí, kterou chcete provést, začne vykreslovat co nejdříve, takže uživatel nebude mít na prázdné obrazovce hvězda.

### <a name="watch-the-maps-ready-event"></a>Sledujte událost Maps Ready.

Podobně, pokud se mapa zpočátku načítá často, je žádoucí načíst data co nejrychleji, takže uživatel nehledá prázdnou mapu. Vzhledem k tomu, že mapa načte prostředky asynchronně, musíte počkat, dokud nebude mapa připravena k interakci s předtím, než se pokusíte vykreslit vlastní data. Existují dvě události, které můžete počkat na `load` událost a `ready` událost. Událost Load se spustí poté, co mapa dokončí zcela načtení počátečního zobrazení mapy a všechny dlaždice mapy budou načteny. Událost připravená se aktivuje, když minimální prostředky mapy potřebné ke spuštění interakce s mapou. Přizpůsobená událost se často může vyvolávat v polovině doby zátěže, takže vám umožní začít načítat data do mapy dřív.

### <a name="lazy-load-the-azure-maps-web-sdk"></a>Opožděné načtení Azure Maps webové sady SDK

Pokud mapa není potřeba hned, převeďte Azure Maps webovou sadu SDK, dokud ji nepotřebujete. Tím se zpoždění načítání souborů JavaScriptu a CSS, které používá Azure Maps Web SDK, dokud nebudete potřebovat. Běžným scénářem, kdy k tomu dojde, je, že mapa je načtena na kartě nebo v panelu s plovoucím panelem, který není zobrazen při načítání stránky.
Následující ukázka kódu ukazuje, jak zpozdit načítání Azure Maps webové sady SDK, dokud se nestiskne tlačítko.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Přeopožděné načtení mapy" src="https://codepen.io/azuremaps/embed/vYEeyOv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/vYEeyOv'>opožděně načítat mapu</a> pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-a-placeholder-for-the-map"></a>Přidat zástupný symbol pro mapu

Pokud se mapa v rámci vaší aplikace trvá, než se nahraje v důsledku omezení sítě nebo jiných priorit, zvažte přidání malého obrázku na pozadí do mapy `div` jako zástupný symbol pro mapu. Tím se při načítání naplní void mapa `div` .

### <a name="set-initial-map-style-and-camera-options-on-initialization"></a>Nastavit styl počáteční mapy a možnosti kamery při inicializaci

Aplikace často chtějí načítat mapu do konkrétního umístění nebo stylu. Vývojáři někdy budou čekat, dokud se mapa nenačte (nebo počká na `ready` událost), a pak použijte `setCemer` funkce nebo `setStyle` mapy. Tato akce bude často trvat déle, než se načte do požadovaného počátečního zobrazení mapy, protože mnoho prostředků se ve výchozím nastavení načítá ještě předtím, než se načtou prostředky potřebné pro požadované zobrazení mapy. Lepším přístupem je při inicializaci předat v mapě požadované možnosti a styl mapy.

## <a name="optimize-data-sources"></a>Optimalizace zdrojů dat

Webová sada SDK má dva zdroje dat,

* **Zdroj** geografického formátu JSON: známo jako `DataSource` Třída spravuje data nezpracovaného umístění ve formátu geografických JSON místně. Vhodný pro malé až střední datové sady (nahoru z stovek tisíců funkcí).
* **Zdroj vektorové dlaždice**: známo ve `VectorTileSource` třídě, načte data formátovaná jako vektorové dlaždice pro aktuální zobrazení mapy na základě systému mapy dláždění. Ideální pro velké až obrovský datové sady (miliony nebo miliardy funkcí).

### <a name="use-tile-based-solutions-for-large-datasets"></a>Použití řešení pro velké datové sady na základě dlaždic

Pokud pracujete s většími datovými sadami obsahujícími miliony funkcí, doporučeným způsobem pro dosažení optimálního výkonu je vystavení dat pomocí řešení na straně serveru, jako je například vektorová nebo rastrová dlaždice obrázku.  
Vektorové dlaždice jsou optimalizované tak, aby načetly jenom data, která jsou v zobrazení geometrií, která jsou oříznutá na oblast fokusu dlaždice a zobecněná tak, aby odpovídala rozlišení mapy pro úroveň přiblížení dlaždice.

[Platforma Azure Maps Creator](creator-indoor-maps.md) poskytuje možnost načíst data ve formátu Vektorové dlaždice. Jiné formáty dat můžou [na této stránce](https://github.com/mapbox/awesome-vector-tiles)používat nástroje, jako je [Tippecanoe](https://github.com/mapbox/tippecanoe) nebo jeden z mnoha seznamů prostředků.

Je také možné vytvořit vlastní službu, která vykresluje datové sady jako dlaždice rastrových obrázků na straně serveru a načítá data pomocí třídy TileLayer v sadě map SDK. To poskytuje výjimečný výkon, protože mapa potřebuje jenom načítat a spravovat několik desítek imagí. Existují však určitá omezení s použitím rastrových dlaždic, protože nezpracovaná data nejsou místně k dispozici. Sekundární služba se často vyžaduje k napájení libovolného typu interakce, například zjistit, na jaký tvar uživatel kliknul. Kromě toho je velikost souboru rastrové dlaždice často větší než komprimovaná vektorová dlaždice, která obsahuje generalizované a optimalizované úrovně přiblížení geometrií.

Další informace o zdrojích dat najdete v dokumentu [Vytvoření zdroje dat](create-data-source-web-sdk.md) .

### <a name="combine-multiple-datasets-into-a-single-vector-tile-source"></a>Kombinování více datových sad do jednoho zdroje vektorové dlaždice

Čím méně zdrojů dat mapa musí spravovat, tím rychlejší může zpracovat všechny funkce, které se mají zobrazit. Konkrétně Pokud je součástí zdrojů dlaždic, kombinace dvou zdrojů vektorových dlaždic rozkládá počet požadavků HTTP na načtení dlaždic na polovinu a celkové množství dat by bylo mírně menší, protože je k dispozici pouze jedna hlavička souboru.

Kombinování více datových sad v jednom zdroji vektorové dlaždice je možné dosáhnout pomocí nástroje, jako je [Tippecanoe](https://github.com/mapbox/tippecanoe). Sady dat lze zkombinovat do jedné kolekce funkcí nebo rozdělit do samostatných vrstev v rámci vektorové dlaždice označované jako zdrojové vrstvy. Při připojování zdroje vektorové dlaždice k vrstvě vykreslování byste měli zadat zdrojovou vrstvu obsahující data, která chcete vykreslit pomocí vrstvy.

### <a name="reduce-the-number-of-canvas-refreshes-due-to-data-updates"></a>Snižte počet aktualizací plátna z důvodu aktualizací dat.

Existuje několik způsobů, jak `DataSource` lze přidat nebo aktualizovat data ve třídě. Níže jsou uvedené různé metody a některé okolnosti, které vám pomohou zajistit dobrý výkon.

* Funkci zdroje dat `add` lze použít k přidání jedné nebo více funkcí do zdroje dat. Pokaždé, když se tato funkce volá, aktivuje se aktualizace plátna mapy. Pokud přidáváte mnoho funkcí, Zkombinujte je do kolekce Array nebo functions a předá je do této funkce jednou, nikoli prostřednictvím smyčky přes datovou sadu a zavoláním této funkce pro každou funkci.
* Funkci zdroje dat `setShapes` lze použít k přepsání všech tvarů ve zdroji dat. V digestoři kombinuje zdroje dat `clear` a `add` funkce společně a provede jedno obnovení mapy plátna místo dvou, což je mnohem rychlejší. Nezapomeňte použít tento postup, pokud chcete aktualizovat všechna data ve zdroji dat.
* Funkci zdroje dat `importDataFromUrl` lze použít k načtení souboru. JSON pomocí adresy URL do zdroje dat. Data se po stažení předávají do funkce zdroje dat `add` . Pokud je soubor. JSON hostovaný v jiné doméně, ujistěte se, že druhá doména podporuje požadavky mezi doménami (CORs). Pokud to nebere v úvahu kopírování dat do místního souboru ve vaší doméně nebo vytvoření proxy služby s povoleným CORs. Pokud je soubor velký, zvažte jeho převod na zdroj vektorové dlaždice.
* Pokud jsou funkce zabaleny `Shape` třídou, `addProperty` funkce, `setCoordinates` a v `setProperties` prvku Shape budou aktivovat aktualizaci ve zdroji dat a v aktualizaci plátna mapy. Všechny funkce vrácené zdroji dat `getShapes` a `getShapeById` funkcemi jsou automaticky zabaleny `Shape` třídou. Pokud chcete aktualizovat několik tvarů, je rychlejší je převést na JSON pomocí funkce zdroje dat `toJson` , úpravou geometrické aplikace a následným předáním těchto dat do funkce zdroje dat `setShapes` .

### <a name="avoid-calling-the-data-sources-clear-function-unnecessarily"></a>Vyhněte se nevolání funkce zdroje dat Clear

Voláním funkce Clear `DataSource` třídy dojde k aktualizaci plátna mapy. Pokud `clear` je funkce volána víckrát v řádku, může dojít k prodlevě, zatímco mapa čeká na výskyt každé aktualizace.

Běžným scénářem, kdy se tato často objevuje v aplikacích, je, že když aplikace vymaže zdroj dat, stáhne nová data, vymaže zdroj dat znovu a pak přidá nová data do zdroje dat. V závislosti na požadovaném uživatelském prostředí by byly lepší tyto alternativy.

* Před stažením nových dat vymažte data a pak nová data předejte do zdrojů dat nebo do `add` `setShapes` funkce. Pokud se jedná o jedinou datovou sadu na mapě, mapa bude při stahování nových dat prázdná.
* Stáhněte si nová data a předejte je do funkce zdrojů dat `setShapes` . Tato akce nahradí všechna data na mapě.

### <a name="remove-unused-features-and-properties"></a>Odebrat nepoužívané funkce a vlastnosti

Pokud vaše datová sada obsahuje funkce, které se ve vaší aplikaci nepoužívají, odeberte je. Podobně odeberte všechny vlastnosti pro funkce, které nepotřebujete. Má několik výhod:

* Zmenší množství dat, která se mají stáhnout.
* Snižuje počet funkcí, které se při vykreslování dat musí cyklicky procházet.
* Může někdy zjednodušit nebo odebrat výrazy řízené daty a filtry, což znamená, že méně zpracování vyžaduje v době vykreslování.

Pokud mají některé funkce hodně vlastností nebo obsahu, je mnohem více náročných na to, co se přidá ke zdroji dat, pouze k tomu, aby bylo nutné pro vykreslování, a mít samostatnou metodu nebo službu pro získání další vlastnosti nebo obsahu v případě potřeby. Například pokud máte jednoduché mapování se zobrazením umístění na mapě při kliknutí na svazek podrobného obsahu, zobrazí se. Pokud chcete použít styly řízené daty k přizpůsobení způsobu vykreslování umístění na mapě, načtěte pouze vlastnosti potřebné do zdroje dat. Pokud chcete zobrazit podrobný obsah, pomocí ID funkce načtěte další obsah samostatně. Pokud je obsah uložen na straně serveru, může být služba použita k asynchronnímu načtení služby, což by významně snížilo množství dat, která je nutné stáhnout při počátečním načtení mapy.

Kromě toho je možné omezit počet významných číslic v souřadnicích funkcí a také významně snižovat velikost dat. Není neobvyklé, že souřadnice obsahují 12 nebo více desetinných míst; Nicméně šest desetinných míst má přesnost přibližně 0,1 měřičů, což je často přesnější než poloha, kterou reprezentuje souřadnice (při práci s malými daty umístění, jako je například rozložení interiéru sestavení se doporučuje šest desetinných míst). Více než šest desetinných míst by nejspíš nevedlo k žádnému rozdílu ve způsobu, jakým se data vykreslují, a vyžadují, aby uživatel stáhl více dat, aby se nepřidala žádná výhoda.

Tady je seznam [užitečných nástrojů pro práci s daty v](https://github.com/tmcw/awesome-geojson)angličtině.

### <a name="use-a-separate-data-source-for-rapidly-changing-data"></a>Použití samostatného zdroje dat pro rychlé změny dat

Někdy je potřeba rychle aktualizovat data na mapě pro věci, jako je třeba zobrazení živých aktualizací dat streamování nebo animace funkcí. Při aktualizaci zdroje dat projde modul vykreslování a vykreslí všechny funkce ve zdroji dat. Oddělení statických dat od rychlé změny dat do různých zdrojů dat může výrazně snížit počet funkcí, které jsou znovu vykresleny při každé aktualizaci zdroje dat, a zvýšit celkový výkon.

Pokud používáte vektorové dlaždice s živými daty, můžete snadno podporovat aktualizace tak, že použijete `expires` hlavičku odpovědi. Ve výchozím nastavení všechny vektorové dlaždice nebo vrstva rastrových dlaždic automaticky znovu načítají dlaždice po `expires` datu. Tok přenosů a dlaždice incidentů v mapě tuto funkci používají k zajištění, aby se na mapě zobrazovala data o přenosech dat v reálném čase. Tuto funkci můžete zakázat nastavením `refreshExpiredTiles` možnosti Služba Maps na `false` .

### <a name="adjust-the-buffer-and-tolerance-options-in-geojson-data-sources"></a>Úprava možností vyrovnávací paměti a tolerance v datových zdrojích s neplatnými JSON

`DataSource`Třída převede nezpracovaná data umístění na vektorové dlaždice místní pro průběžné vykreslování. Tyto místní vektorové dlaždice vystřihují nezpracovaná data na hranice oblasti dlaždice s bitovou pamětí, aby bylo zajištěno hladké vykreslování mezi dlaždicemi. `buffer`Čím menší je možnost, méně překrývajících se dat je uloženo v místních vektorových dlaždicích a lepší výkon, ale čím větší je změna vykreslování artefaktů. Zkuste tuto možnost vylepšit, abyste získali správnou kombinaci výkonu s minimálními artefakty vykreslování.

`DataSource`Třída má také `tolerance` možnost, která se používá s algoritmem Douglas-Peucker pro zjednodušení při omezení rozlišení geometrií pro účely vykreslování. Zvýšení této hodnoty tolerance omezí rozlišení geometrií a zároveň vylepší výkon. Sestavte tuto možnost, abyste získali správnou kombinaci rozlišení geometrie a výkonu pro datovou sadu.

### <a name="set-the-max-zoom-option-of-geojson-data-sources"></a>Nastavte možnost maximální přiblížení zdrojů dat typu injson.

`DataSource`Třída převede nezpracovaná data umístění na vektorové dlaždice místní pro průběžné vykreslování. Ve výchozím nastavení to provede, dokud úroveň přiblížení až 18 nedosáhne přiblížení, bude vzorkovat data z dlaždic vygenerovaných pro úroveň přiblížení 18. To je vhodné pro většinu datových sad, které potřebují vysoké rozlišení při přiblížení na tyto úrovně. Pokud však pracujete se sadami dat, které jsou pravděpodobně zobrazovány při přiblížení více, například při zobrazení mnohoúhelníků stavu nebo provincií, nastavení `minZoom` Možnosti zdroje dat na menší hodnotu, jako je například `12` snížení množství výpočtu, vytvoření místní dlaždice, k němuž dojde a paměti používané zdrojem dat a zvýšení výkonu.

### <a name="minimize-geojson-response"></a>Minimalizovat odpověď typu injson

Při načítání dat z horizontálního formátu JSON ze serveru buď prostřednictvím služby, nebo načtením plochého souboru se ujistěte, že data jsou minimalizovaná, aby se odstranily nepotřebné znaky a velikost stahovaných souborů je větší, než je potřeba.

### <a name="access-raw-geojson-using-a-url"></a>Přístup k nezpracovanému hrubému formátu JSON pomocí adresy URL

Je možné ukládat objekty ve formátu JSON vložené dovnitř do JavaScriptu, ale budou používat spoustu paměti, protože její kopie budou uloženy napříč proměnnou, kterou jste vytvořili pro tento objekt, a instancí zdroje dat, která ho spravuje v rámci samostatného webového pracovního procesu. Vystavte svůj nepřímý formát JSON pro aplikaci pomocí adresy URL a zdroj dat načte jednu kopii dat přímo do webového pracovního procesu datových zdrojů.  

## <a name="optimize-rendering-layers"></a>Optimalizace vykreslování vrstev

Azure Maps poskytuje několik různých vrstev pro vykreslování dat na mapě. K dispozici je mnoho optimalizací, které můžete využít k přizpůsobení těchto vrstev vašemu scénáři při zvyšování funkčních výsledků a celkové činnosti uživatelů.

### <a name="create-layers-once-and-reuse-them"></a>Vytvořit vrstvy jednou a znovu použít

Azure Maps webové sadě SDK se rozhodnete, že budou řízeny daty. Data přecházejí do zdrojů dat, které jsou následně připojeny k vrstvám vykreslování. Chcete-li změnit data na mapě, aktualizujte data ve zdroji dat nebo změňte možnosti stylu ve vrstvě. To je často mnohem rychlejší než odebrání a pak opětovné vytvoření vrstev vždy, když dojde ke změně.

### <a name="consider-bubble-layer-over-symbol-layer"></a>Zvážit vrstvu bublin přes symbolovou vrstvu

Bublinová vrstva vykresluje body jako kruhy na mapě a může snadno mít jejich poloměr a barvu ve stylu pomocí výrazu založeného na datech. Vzhledem k tomu, že kruh je jednoduchý tvar pro vykreslování WebGL, vykreslovací modul bude moci vykreslovat mnohem rychleji než symbolovou vrstvu, která musí načíst a vykreslit obrázek. Rozdíl v výkonu těchto dvou vrstev vykreslování je patrné při vykreslování desítky tisíc bodů.

### <a name="use-html-markers-and-popups-sparingly"></a>Používejte k omezenému používání značek a překryvných oken HTML

Na rozdíl od většiny vrstev ve webovém ovládacím prvku Azure Maps, který používá WebGL pro vykreslování, značky HTML a automaticky otevíraná okna používají tradiční prvky DOM pro vykreslování. V takovém případě více značek HTML a automaticky otevíraných oken přidalo stránku, další prvky modelu DOM jsou. Výkon se může snížit po přidání několika stovek značek HTML nebo automaticky otevíraných oken. U větších datových sad zvažte, jak vaše data zařadí do clusteru, nebo použijte symbol nebo bublinovou vrstvu. V případě překryvných oken je běžné strategii vytvořit jednu automaticky otevíraná okna a znovu ji použít tak, že aktualizuje její obsah a polohu, jak je znázorněno v následujícím příkladu:

<br/>

<iframe height='500' scrolling='no' title='Opakované použití automaticky otevíraného okna s více PIN kódy' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Přečtěte si <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>automaticky otevírané okno s více kolíky</a> pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

V takovém případě, pokud máte pouze několik bodů vykreslování na mapě, může být vhodnější jednoduchost značek HTML. V případě potřeby lze značky HTML také snadno přetahovat.

### <a name="combine-layers"></a>Kombinovat vrstvy

Mapa dokáže vykreslovat stovky vrstev, ale víc vrstev je, tím déle bude trvat vykreslování scény. Jednou z strategií pro snížení počtu vrstev je kombinování vrstev, které mají podobné styly nebo které lze stylovat pomocí stylů založených na [datech](data-driven-style-expressions-web-sdk.md).

Zvažte například sadu dat, kde všechny funkce mají `isHealthy` vlastnost, která může mít hodnotu `true` nebo `false` . Pokud vytváříte bublinovou vrstvu, která vykresluje různé barevné bubliny na základě této vlastnosti, existuje několik způsobů, jak to provést, jak je uvedeno níže, z nejméně výkonného provedení.

* Rozdělit data na dva zdroje dat na základě `isHealthy` hodnoty a připojit bublinovou vrstvu s pevně zakódovanými možnostmi barev do každého zdroje dat.
* Všechna data vložte do jednoho zdroje dat a vytvořte dvě bublinové vrstvy s pevně kódovanou volbou barvy a filtr na základě `isHealthy` Vlastnosti. 
* Vložte všechna data do jednoho zdroje dat, vytvořte jednu bublinovou vrstvu s `case` výrazem stylu pro možnost Barva na základě `isHealthy` Vlastnosti. Zde je příklad kódu, který ukazuje to.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Get the 'isHealthy' property from the feature.
        ['get', 'isHealthy'],

        //If true, make the color 'green'. 
        'green',

        //If false, make the color red.
        'red'
    ]
});
```

### <a name="create-smooth-symbol-layer-animations"></a>Vytváření hladkých animací vrstev symbolů

Ve výchozím nastavení jsou v vrstvách symbolů povolené detekce kolizí. Tato detekce kolizí má za cíl zajistit, aby se nepřekrývaly žádné dva symboly. Možnosti ikony a textu vrstvy symbolů mají dvě možnosti.

* `allowOverlap` -Určuje, zda bude symbol viditelný, pokud koliduje s jinými symboly.
* `ignorePlacement` -Určuje, zda mohou ostatní symboly kolidovat se symbolem.

Obě tyto možnosti jsou standardně nastavené na `false` . Při animování symbolu se v každém snímku animace spustí Výpočty kolizí, což může zpomalit animaci a zobrazit méně kapaliny. Pokud chcete animaci vyhladit, nastavte tyto možnosti na `true` .

Následující ukázka kódu představuje jednoduchý způsob, jak animovat vrstvu symbolů.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Animace vrstvy symbolů" src="https://codepen.io/azuremaps/embed/oNgGzRd?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/oNgGzRd'>animaci vrstvy symbolů</a> pera podle Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="specify-zoom-level-range"></a>Zadat rozsah úrovně přiblížení

Pokud vaše data splňují jedno z následujících kritérií, nezapomeňte zadat minimální a maximální úroveň přiblížení vrstvy, aby ji vykreslovací modul mohl přeskočit mimo rozsah úrovně přiblížení.

* Pokud data pocházejí ze zdroje vektorové dlaždice, často jsou zdrojové vrstvy pro různé datové typy dostupné jenom přes rozsah úrovní přiblížení.
* Pokud používáte dlaždicovou vrstvu, která nemá dlaždice pro všechny úrovně přiblížení 0 až 24 a chcete, aby se vygenerovala jenom na úrovních, které obsahuje dlaždice, a nepokusí se vyplňovat chybějící dlaždice pomocí dlaždic z dalších úrovní přiblížení.
* Pokud chcete pouze vykreslit vrstvu na určitých úrovních přiblížení.
Všechny vrstvy mají `minZoom` možnost a, `maxZoom` kde se vrstva vykreslí při každé z těchto úrovní přiblížení na základě této logiky ` maxZoom > zoom >= minZoom` .

**Příklad**

```javascript
//Only render this layer between zoom levels 1 and 9. 
var layer = new atlas.layer.BubbleLayer(dataSource, null, {
    minZoom: 1,
    maxZoom: 10
});
```

### <a name="specify-tile-layer-bounds-and-source-zoom-range"></a>Zadat hranice vrstvy dlaždice a rozsah zvětšení zdroje

Ve výchozím nastavení vrstvy dlaždic načítají dlaždice po celém světě. Pokud však služba dlaždice obsahuje pouze dlaždice pro určitou oblast, mapa se pokusí načíst dlaždice mimo tuto oblast. V takovém případě se vytvoří požadavek na každou dlaždici a počká na odpověď, která může blokovat jiné požadavky na mapě, a tím zpomaluje vykreslování dalších vrstev. Pokud zadáte hranice vrstvy dlaždic, bude mapa vyžadovat pouze dlaždice, které jsou v tomto ohraničujícím poli. Pokud je vrstva dlaždice k dispozici pouze mezi určitými úrovněmi přiblížení, určete minimální a maximální zvětšení zdroje ze stejného důvodu.

**Příklad**

```javascript
var tileLayer = new atlas.layer.TileLayer({
    tileUrl: 'myTileServer/{z}/{y}/{x}.png',
    bounds: [-101.065, 14.01, -80.538, 35.176],
    minSourceZoom: 1,
    maxSourceZoom: 10
});
```

### <a name="use-a-blank-map-style-when-base-map-not-visible"></a>Použít prázdný styl mapy, pokud není základní mapa viditelná

Pokud je vrstva překryta na mapě, která zcela pokrývá základní mapu, zvažte nastavení stylu mapy na `blank` nebo `blank_accessible` , aby základní mapa nebyla vykreslena. Běžným scénářem je, že při překrytí celé zeměkoule na dlaždici nemá žádná průhlednost ani průhledná oblast nad základní mapou.

### <a name="smoothly-animate-image-or-tile-layers"></a>Plynule animovat vrstvy obrázků nebo dlaždic

Pokud chcete animovat přes řadu obrázků nebo dlaždic vrstev na mapě. Je často rychlejší vytvořit vrstvu pro každou vrstvu obrázku nebo dlaždice a změnit neprůhlednost než aktualizovat zdroj jedné vrstvy na každém snímku animace. Skrytím vrstvy nastavením opacity na nulu a zobrazením nové vrstvy nastavením průhlednosti na hodnotu větší než nula je mnohem rychlejší než aktualizace zdroje ve vrstvě. Alternativně lze viditelnost vrstev přepínat, ale nezapomeňte nastavit dobu trvání slábnutí vrstvy na hodnotu nula. v opačném případě bude při zobrazení vrstvy animována vrstva, která způsobí, že předchozí vrstva bude skryta, než bude nová vrstva viditelná.

### <a name="tweak-symbol-layer-collision-detection-logic"></a>Vylepšení logiky detekce kolizí vrstev symbolů

Vrstva symbolů má dvě možnosti, které existují pro ikonu i text s názvem `allowOverlap` a `ignorePlacement` . Tyto dvě možnosti určují, zda může být ikona nebo text symbolu překrytý nebo překrytý. Pokud jsou nastaveny na `false` , vrstva symbolu provede výpočty při vykreslování každého bodu, aby bylo vidět, zda koliduje s jakýmkoli jiným již vykresleným symbolem ve vrstvě, a pokud k tomu dojde, nebude vykreslovat kolidující symbol. To je dobré při snižování zbytečných informací na mapě a omezení počtu vykreslených objektů. Nastavením těchto možností na `false` bude tato logika detekce kolizí vynechána a všechny symboly budou vykresleny na mapě. Úpravou této možnosti získáte nejlepší možnou kombinaci výkonu a uživatelského prostředí.

### <a name="cluster-large-point-data-sets"></a>Sady dat clusterových velkých bodů

Při práci s velkými sadami datových bodů se můžete setkat s tím, že při vykreslování na určitých úrovních přiblížení se řada bodů překrývá a jsou pouze částečně viditelné, pokud jsou vůbec. Clustering je proces seskupení bodů, které jsou blízko sebe a představují je jako jeden clusterovaný bod. Jak uživatel přiblíží mapu, rozdělují se clustery do jejich jednotlivých bodů. To může významně snížit množství dat, která je nutné vykreslit, zajistit, aby mapa nedocházelo k méně zbytečným a zvýšila výkon. `DataSource`Třída obsahuje možnosti pro místní data clusteringu. Kromě toho mnoho nástrojů, které generují vektorové dlaždice, má také možnosti clusteringu.

Navíc zvyšte velikost poloměru clusteru, aby se zvýšil výkon. Čím větší je clusterový poloměr, tím méně seskupených bodů je sledování a vykreslování.
Další informace najdete v [dokumentu s datovým bodem clusteringu](clustering-point-data-web-sdk.md) .

### <a name="use-weighted-clustered-heat-maps"></a>Použít vážené clusterované Heat mapy

Vrstva Heat mapy dokáže snadno vykreslovat desítky tisíců datových bodů. U větších datových sad zvažte povolení clusteringu na zdroji dat a použití malého poloměru clusteru a vlastnost clustery použijte `point_count` jako váhu pro mapu Height. Pokud je velikost poloměru clusteru jenom v několika pixelech, bude ve vykreslené Heat mapě málo vizuálních rozdílů. Použití většího poloměru clusteru zvýší výkon, ale může snížit rozlišení vykreslené Heat mapy.

```javascript
var layer = new atlas.layer.HeatMapLayer(source, null, {
   weight: ['get', 'point_count']
});
```

Další informace najdete v části [clusteringu a Heat mapy v tomto dokumentu](clustering-point-data-web-sdk.md #clustering-and-the-heat-maps-layer) .

### <a name="keep-image-resources-small"></a>Zachování malých a obrazových prostředků

Obrázky lze přidat do Sprite obrázku mapy pro ikony vykreslování v symbolové vrstvě nebo vzorech v mnohoúhelníkové vrstvě. Udržujte tyto obrázky malé, abyste minimalizovali množství dat, která se mají stáhnout, a množství místa, které se zabírají v pohyblivém obrazovém symbolu mapy. Při použití vrstvy symbolu, která škáluje ikonu pomocí `size` Možnosti, použijte obrázek, který má maximální velikost, kterou váš plán má zobrazit na mapě a žádný větší. Tím se zajistí, že se ikona vykreslí s vysokým rozlišením při minimalizaci prostředků, které používá. Kromě toho může být SVG také použit jako menší formát souboru pro jednoduché obrázky ikon.

## <a name="optimize-expressions"></a>Výrazy optimalizace

[Výrazy stylu řízené daty](data-driven-style-expressions-web-sdk.md) poskytují značnou flexibilitu a výkon pro filtrování a stylování dat na mapě. Existuje mnoho způsobů, jak lze optimalizovat výrazy. Tady je několik tipů.

### <a name="reduce-the-complexity-of-filters"></a>Zmenšení složitosti filtrů

Filtr vychází ze všech dat ve zdroji dat a kontroluje, zda každý filtr odpovídá logice ve filtru. Pokud se filtry stanou komplexní, může to způsobit problémy s výkonem. Mezi možné postupy, které je potřeba vyřešit, patří následující:

* Pokud používáte vektorové dlaždice, rozdělte data do různých zdrojových vrstev.
* Pokud používáte `DataSource` třídu, předělte tato data do samostatných zdrojů dat. Zkuste vyrovnávat počet zdrojů dat se složitou složitostí filtru. Příliš mnoho zdrojů dat může způsobit problémy s výkonem, takže možná budete muset provést nějaké testování, abyste zjistili, co nejlépe vyhovuje vašemu scénáři.
* Při použití složitého filtru na vrstvě zvažte použití více vrstev s výrazy stylu pro snížení složitosti filtru. Vyhněte se vytváření svazků vrstev se styly pevně zakódované, pokud se výrazy stylu dají použít jako velký počet vrstev, může také dojít k problémům s výkonem.

### <a name="make-sure-expressions-dont-produce-errors"></a>Zajistěte, aby výrazy nepřinesly chyby.

Výrazy se často používají ke generování kódu k provádění výpočtů nebo logických operací v době vykreslování. Stejně jako u kódu ve zbývající části aplikace se ujistěte, že výpočty a logické smysly nejsou náchylné k chybám. Chyby ve výrazech způsobí problémy při vyhodnocování výrazu, což může vést k nižším problémům s výkonem a vykreslováním.

Jedna běžná chyba, kterou je třeba zajímat, je mít výraz, který se spoléhá na vlastnost funkce, která nemusí existovat na všech funkcích. Například následující kód používá výraz pro nastavení vlastnosti Color v bublinové vrstvě na `myColor` vlastnost funkce.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: ['get', 'myColor']
});
```

Výše uvedený kód bude fungovat, pokud všechny funkce ve zdroji dat mají `myColor` vlastnost a hodnota této vlastnosti je barva. To může být problém, pokud máte plnou kontrolu nad daty ve zdroji dat a víte, že některé funkce budou mít v vlastnosti platnou barvu `myColor` . Aby byl tento kód v bezpečí před chybami, `case` lze výraz použít spolu s `has` výrazem pro kontrolu, zda má funkce `myColor` vlastnost. V takovém případě se `to-color` výraz typu může použít k pokusu o převod hodnoty této vlastnosti na barvu. Pokud je barva neplatná, lze použít záložní barvu. Následující kód ukazuje, jak to provést, a nastavení záložní barvy na zelenou.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Check to see if the feature has a 'myColor' property.
        ['has', 'myColor'],

        //If true, try validating that 'myColor' value is a color, or fallback to 'green'. 
        ['to-color', ['get', 'myColor'], 'green'],

        //If false, return a fallback value.
        'green'
    ]
});
```

### <a name="order-boolean-expressions-from-most-specific-to-least-specific"></a>Seřazení logických výrazů od nejpřesnější po nejméně konkrétní

Při použití logických výrazů, které obsahují více podmíněných testů, seřazení podmíněných testů od nejpřesnější po nejméně konkrétní. První podmínkou je, že by měla snížit množství dat, ke kterým se má druhá podmínka testovat, čímž se sníží celkový počet podmíněných testů, které je potřeba provést.

### <a name="simplify-expressions"></a>Zjednodušit výrazy

Výrazy můžou být výkonné a někdy složité. Čím jednodušší výraz je, tím rychleji se vyhodnotí. Například pokud je potřeba jednoduché porovnání, podobný výraz `['==', ['get', 'category'], 'restaurant']` by byl lepší než použití výrazu shody jako `['match', ['get', 'category'], 'restaurant', true, false]` . V takovém případě, pokud je zaškrtnutá vlastnost logická hodnota, `get` výraz by byl dokonce jednodušší `['get','isRestaurant']` .

## <a name="web-sdk-troubleshooting"></a>Řešení potíží s web SDK

Následuje několik tipů pro ladění některých běžných problémů, ke kterým došlo při vývoji pomocí Azure Maps Web SDK.

**Proč se mapa nezobrazuje při načítání webového ovládacího prvku?**

Postupujte následovně:

* Ujistěte se, že jste přidali přidané možnosti ověřování na mapu. Pokud to nepřidáte, mapa se načte s prázdným plátnem, protože nemůže získat přístup k základním datům mapy bez ověření a 401 chyby se zobrazí na kartě síť v vývojářských nástrojích prohlížeče.
* Ujistěte se, že máte připojení k Internetu.
* Podívejte se na konzolu chyby vývojářských nástrojů v prohlížeči. Některé chyby můžou způsobit, že se mapa nebude vykreslovat. Ladit aplikaci.
* Ujistěte se, že používáte [podporovaný prohlížeč](supported-browsers.md).

**Všechna moje data se zobrazují na druhé straně světa, co se na nich právě používá?**
Souřadnice, označované také jako pozice, se v sadách Azure Maps SDK zarovnají formátu geoprostorového standardního oboru `[longitude, latitude]` . Stejný formát je také způsob, jakým jsou definovány souřadnice ve schématu biojson; základní data formátovaná v sadě SDK pro Azure Maps. Pokud jsou vaše data na protější straně světa, je nejpravděpodobnější, že se hodnoty Zeměpisná délka a zeměpisná šířka obrátí v informacích o souřadnicích nebo pozicích.

**Proč se značky HTML zobrazují na nesprávném místě v ovládacím prvku Web?**

Co byste měli kontrolovat:

* Pokud používáte vlastní obsah pro značku, ujistěte se, `anchor` že `pixelOffset` jsou možnosti a správné. Ve výchozím nastavení je dolní střed obsahu zarovnán s pozicí na mapě.
* Ujistěte se, že soubor CSS pro Azure Maps byl načten.
* Zkontrolujte element modelu DOM značky HTML a podívejte se, zda se některé šablony CSS z vaší aplikace připojily k této značce a ovlivňují její polohu.

**Proč jsou ikony nebo text v vrstvě symbolů zobrazeny na nesprávném místě?**
Ověřte, zda `anchor` `offset` jsou možnosti a správně nakonfigurovány tak, aby byly zarovnány s částí obrázku nebo textu, který chcete zarovnat s souřadnicí na mapě.
Pokud je symbol zastaralý pouze při otočení mapy, zaškrtněte `rotationAlignment` možnost. Ve výchozím nastavení se symboly otočí pomocí zobrazení mapy tak, aby se uživateli zobrazovala vzhůru. V závislosti na vašem scénáři ale může být žádoucí zamknout symbol na orientaci mapy. K tomu můžete nastavit `rotationAlignment` možnost `’map’` .
Pokud je symbol na místě pouze v případě, že je mapa zapnutá nebo nakloněná, zaškrtněte `pitchAlignment` možnost. Ve výchozím nastavení symboly zachová v zobrazení mapy, protože rozvržení je nakloněné nebo nakloněné. V závislosti na vašem scénáři ale může být žádoucí zamknout symbol na rozteč mapy. K tomu můžete nastavit `pitchAlignment` možnost `’map’` .

**Proč se na mapě nezobrazují žádná data?**

Co byste měli kontrolovat:

* Podívejte se na konzolu v vývojářských nástrojích prohlížeče pro chyby.
* Zajistěte, aby byl zdroj dat vytvořen a přidán do mapy a aby byl zdroj dat připojen k vrstvě vykreslování, která byla také přidána do mapy.
* Přidejte body přerušení do kódu a Projděte si je, abyste zajistili, že se data přidávají do zdroje dat a že se k mapě přidávají zdroje dat a vrstvy, aniž by došlo k chybám.
* Zkuste ze své vrstvy vykreslování odebrat výrazy řízené daty. Je možné, že jedna z nich může mít chybu, která je příčinou problému.

**Můžu použít Azure Maps Web SDK v izolovaném režimu IFRAME?**

Ano. Všimněte si, že [Safari obsahuje chybu](https://bugs.webkit.org/show_bug.cgi?id=170075) , která brání prvkům IFRAME z izolovaného prostoru (sandbox) ve spouštění webových pracovních procesů, což je požadavek Azure Maps Web SDK. Řešením je přidání `"allow-same-origin"` značky do vlastnosti izolovaného prostoru (sandbox) prvku IFRAME.

## <a name="get-support"></a>Získání podpory

Níže jsou uvedené různé způsoby, jak získat podporu pro Azure Maps v závislosti na vašem problému.

**Návody ohlásit problém s daty nebo problém s adresou?**

Azure Maps má nástroj pro zasílání zpětné vazby dat, kde se dají problémy s daty nahlásit a sledovat. [https://feedback.azuremaps.com/](https://feedback.azuremaps.com/) Každý odeslaný problém vygeneruje jedinečnou adresu URL, kterou můžete použít ke sledování průběhu problému s daty. Čas potřebný k vyřešení problému s daty se liší v závislosti na typu problému a na tom, jak snadné je ověřit, zda je změna správná. Po dokončení aktualizace uvidí služba vykreslování aktualizaci v týdenní aktualizaci. další služby, jako je například geografické kódování a směrování, uvidí aktualizaci v rámci měsíční aktualizace. Podrobné pokyny k nahlášení problému s daty jsou uvedené v tomto [dokumentu](how-to-use-feedback-tool.md).

**Návody ohlásit chybu ve službě nebo rozhraní API?**

https://azure.com/support

**Kde získám technickou nápovědu pro Azure Maps?**

Pokud se jedná o Azure Maps vizuál v Power BI: https://powerbi.microsoft.com/support/ pro všechny ostatní Azure Maps služby: https://azure.com/support nebo pro fóra pro vývojáře: https://docs.microsoft.com/answers/topics/azure-maps.html

**Návody vytvořit žádost o funkci?**

Vytvoření žádosti o funkce na webu našeho hlasu uživatele: https://feedback.azure.com/forums/909172-azure-maps

## <a name="next-steps"></a>Další kroky

V následujících článcích najdete další tipy ke zlepšení uživatelského prostředí aplikace.

> [!div class="nextstepaction"]
> [Zpřístupnění aplikace](map-accessibility.md)

Přečtěte si další informace o terminologii používané Azure Maps a geoprostorovém odvětví.

> [!div class="nextstepaction"]
> [Glosář služby Azure Maps](glossary.md)
