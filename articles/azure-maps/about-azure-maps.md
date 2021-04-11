---
title: Přehled služby Microsoft Azure Maps
description: Seznamte se se službami a možnostmi v Microsoft Azure Maps a jejich použití ve svých aplikacích.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, references_regions
ms.openlocfilehash: fc70e6370f53848e9f3672611a3dfda685bcb9fe
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011772"
---
# <a name="what-is-azure-maps"></a>Co je služba Azure Maps?

Azure Maps je kolekce geoprostorových služeb a sad SDK, které používají data pro nové mapování k poskytování geografického kontextu pro webové a mobilní aplikace. Azure Maps poskytuje:

* Rozhraní REST API pro vykreslování vektorových a rastrových map v několika stylech a satelitních obrazových obrázcích.
* Creator Services (Preview) pro vytváření a vykreslování map na základě privátních dat vnitřní mapy.
* Služba Search vám umožní najít adresy, místa a body zájmu po celém světě.
* Různé možnosti směrování; například Point-to-Point, MultiPoint, optimalizace pro MultiPoint, isochrone, elektrické vozidlo, komerční vozidlo, ovlivněný provoz a směrování matrice.
* Zobrazení toku a incidenty přenosu pro aplikace, které vyžadují informace o provozu v reálném čase.
* Služby mobility (Preview) k vyžádání informací o veřejném přenosu, naplánujte trasy vymícháním různých režimů cestování a příchodů v reálném čase.
* Časové pásmo a služba geografického umístění (Preview).
* Zvýšení úrovně služeb (Preview) s modelem digitálního zvýšení oprávnění
* Služba geografických zón a mapování úložiště dat s informacemi o poloze hostované v Azure.
* Informace o poloze prostřednictvím geoprostorové analýzy.

Služba Azure Maps Services je navíc k dispozici prostřednictvím webové sady SDK a Android SDK. Tyto nástroje umožňují vývojářům rychle vyvíjet a škálovat řešení, která integrují informace o poloze do řešení Azure.

Můžete si zaregistrovat bezplatný [Azure Maps účet](https://azure.microsoft.com/services/azure-maps/) a začít vyvíjet.

Následující video vysvětluje Azure Maps do hloubky:

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny]

## <a name="map-controls"></a>Ovládací prvky mapy

### <a name="web-sdk"></a>Sada Web SDK

Sada Azure Maps Web SDK umožňuje přizpůsobit interaktivní mapy pomocí vlastního obsahu a snímků. Tuto interaktivní mapu můžete použít pro vaše webové i mobilní aplikace. Mapový ovládací prvek využívá WebGL, takže můžete vykreslit velké sady dat s vysokým výkonem. Můžete vyvíjet pomocí sady SDK pomocí JavaScriptu nebo TypeScript.

:::image type="content" source="./media/about-azure-maps/intro_web_map_control.png" alt-text="Ukázková mapa změny populace vytvořené pomocí Azure Maps Web SDK":::

### <a name="android-sdk"></a>Android SDK

K vytváření aplikací pro mobilní mapování použijte Azure Maps Android SDK.

:::image type="content" source="./media/about-azure-maps/android_sdk.png" border="false" alt-text="Příklady map na mobilním zařízení":::

## <a name="services-in-azure-maps"></a>Služby v rámci Azure Maps

Azure Maps sestávají z následujících služeb, které poskytují geografickou souvislost aplikacím Azure.

### <a name="data-service-preview"></a>Data Service (Preview)

Data jsou pro mapy naléhavá. Pomocí datové služby můžete nahrávat a ukládat geoprostorové data pro použití s prostorovou operací nebo kompozicí imagí.  Uvedení zákaznických dat blíže ke službě Azure Maps omezí latenci, zvýší produktivitu a vytvoří nové scénáře ve vašich aplikacích. Podrobnosti o této službě najdete v dokumentaci k [datové službě](/rest/api/maps/data).

### <a name="geolocation-service-preview"></a>Služba geografického umístění (Preview)

Použijte službu geografického umístění k zobrazení náhledu načteného kódu země/oblasti se dvěma písmeny pro IP adresu. Tato služba vám může přispět k vylepšení uživatelského prostředí tím, že poskytuje přizpůsobený obsah aplikace založený na geografickém umístění.

Další podrobnosti najdete v dokumentaci ke [službě geografického umístění](/rest/api/maps/geolocation).

### <a name="mobility-services-preview"></a>Služby mobility (Preview) 

Služby Azure Maps mobility zlepšují dobu vývoje aplikací s funkcemi veřejného přenosu, jako je například směrování Transitu a hledání okolního veřejného přenosu. Uživatelé můžou získat podrobné informace o zastavení, řádcích a plánech přenosu. Služba mobility taky umožňuje uživatelům načítat geometrií a line, výstrahy na zastávky, řádky a oblasti služeb a oznámení o doručení veřejných dat v reálném čase a upozornění služby. Služby mobility navíc poskytují možnosti směrování s možnostmi plánování Multimodal Trip. Plánování cest Multimodal zahrnuje možnosti procházení, cyklistice a veřejného přenosu, a to vše na jednu cestu. Uživatelé také mohou získat přístup k podrobným Multimodal krok za krokem cesty.

Další informace o této službě najdete v dokumentaci ke [službám mobility](/rest/api/maps/mobility).

### <a name="render-service"></a>Služba Render

[Služba vykreslování verze v2 (Preview)](/rest/api/maps/renderv2) zavádí novou verzi [rozhraní získat dlaždici mapy v2](/rest/api/maps/renderv2/getmaptilepreview). Rozhraní API pro dlaždici získat mapu verze 2 teď umožňuje zákazníkům požádat o Azure Mapsovou dlaždici, počasí a dlaždice map vytvořené pomocí Tvůrce Azure Maps. Doporučuje se použít novou dlaždici získat rozhraní API pro získání mapy.  

:::image type="content" source="./media/about-azure-maps/intro_map.png" border="false" alt-text="Příklad mapy ze služby vykreslení v2":::

Další podrobnosti najdete v dokumentaci ke [službě vykreslovat v2](/rest/api/maps/renderv2).

Další informace o službě vykreslování, která je v GA (Obecná dostupnost), najdete v [dokumentaci k vygenerování služby](/rest/api/maps/render)vydaných verzí v1.  

### <a name="route-service"></a>Služba Route

Služby směrování se dají použít k výpočtu předpokládaných časů doručení (ETAs) pro každou požadovanou trasu. Rozhraní API tras uvažují o faktorech, jako jsou informace o přenosech v reálném čase a historické údaje o provozu, jako je obvyklá rychlost provozu v požadovaném dni v týdnu a denní doba. Rozhraní API vrací nejkratší nebo nejrychlejší trasy, které jsou k dispozici více cílům v čase v pořadí nebo v optimalizovaném pořadí, na základě času nebo vzdálenosti. Služba umožňuje vývojářům vypočítat směry v různých režimech cestovních cest, jako je auto, nákladní kolo, kolo nebo procestování a elektrická vozidlo. Tato služba také zohledňuje vstupy, jako je čas odchodu, omezení váhy nebo přenos nebezpečných látek.

:::image type="content" source="./media/about-azure-maps/intro_route.png" border="false" alt-text="Příklad mapy ze služby směrování":::

Služba směrování nabízí rozšířené funkce sady, jako například:

* Dávkové zpracování více požadavků na směrování.
* Matice doby cesty a vzdálenosti mezi sadou zdrojů a míst určení.
* Hledání cest nebo vzdáleností, které mohou uživatelé cestovat na základě požadavků na čas nebo pohon.

Podrobnosti o možnostech směrování najdete v [dokumentaci ke službě Směrování](/rest/api/maps/route).

### <a name="search-service"></a>Služba Search

Vyhledávací služba pomáhá vývojářům vyhledávat adresy, místa, obchodní výpisy podle názvu nebo kategorie a dalších geografických informací. Služby také mohou v závislosti na Latitudes a zeměpisných délkách [přesměrovat](https://en.wikipedia.org/wiki/Reverse_geocoding) adresy zeměpisného kódu a meziulic.

:::image type="content" source="./media/about-azure-maps/intro_search.png" border="false" alt-text="Příklad hledání na mapě":::

Vyhledávací služba také poskytuje pokročilé funkce, jako například:

* Hledání v trase.
* Hledejte v širší oblasti.
* Dávkovat skupinu žádostí o hledání.
* Prohledejte čerpací stanice a data POI (Point of Interest) podle názvu značky.

Další informace o možnostech vyhledávání najdete v dokumentaci ke [službě Search Service](/rest/api/maps/search).

### <a name="spatial-service"></a>Služba Spatial

Prostorová služba rychle analyzuje informace o poloze, aby pomohla informovat zákazníky o probíhajících událostech v čase a prostoru. Umožňuje analýzu téměř v reálném čase a prediktivní modelování událostí.

Služba umožňuje zákazníkům zdokonalit své lokátory umístění pomocí knihovny běžných geoprostorovéch matematických výpočtů. Mezi běžné výpočty patří nejbližší bod, Skvělé kolečko a vyrovnávací paměti. Další informace o službě a různých funkcích si můžete přečíst v dokumentaci k [prostorové službě](/rest/api/maps/spatial).

### <a name="timezone-service"></a>Služba Timezone

Služba časového pásma umožňuje dotazovat se na aktuální, historické a budoucí informace o časovém pásmu. Jako vstup můžete použít páry Zeměpisná šířka a délka nebo [ID IANA](https://www.iana.org/) . Služba časového pásma také umožňuje:

* Převod ID časových pásem Microsoft Windows na časová pásma IANA.
* Načítají se posunutí časového pásma na čas UTC.
* Získání aktuálního času ve vybraném časovém pásmu.

Typická odpověď JSON pro dotaz na službu časového pásma vypadá jako v následující ukázce:

```JSON
{
  "Version": "2020a",
  "ReferenceUtcTimestamp": "2020-07-31T19:15:14.4570053Z",
  "TimeZones": [
    {
      "Id": "America/Los_Angeles",
      "Names": {
        "ISO6391LanguageCode": "en",
        "Generic": "Pacific Time",
        "Standard": "Pacific Standard Time",
        "Daylight": "Pacific Daylight Time"
      },
      "ReferenceTime": {
        "Tag": "PDT",
        "StandardOffset": "-08:00:00",
        "DaylightSavings": "01:00:00",
        "WallTime": "2020-07-31T12:15:14.4570053-07:00",
        "PosixTzValidYear": 2020,
        "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
      }
    }
  ]
}
```

Podrobnosti o této službě najdete v dokumentaci ke [službě pro časové pásmo](/rest/api/maps/timezone).

### <a name="traffic-service"></a>Služba Traffic

Přenosová služba je sada webových služeb, které můžou vývojáři použít pro webové nebo mobilní aplikace, které vyžadují informace o provozu. Služba nabízí dva datové typy:

* Tok přenosů: zaznamenané rychlosti v reálném čase a doby provozu pro všechny klíčové cesty v síti.
* Incidenty provozu: aktuální přehled o zaseknutí a incidentech provozu kolem sítě v provozu.

![Příklad mapy s informacemi o provozu](media/about-azure-maps/intro_traffic.png)

Další informace najdete v [dokumentaci služby Traffic Service](/rest/api/maps/traffic).

### <a name="weather-services-preview"></a>Počasí Services (Preview) 

Služba počasí nabízí rozhraní API, pomocí kterých můžou vývojáři načíst informace o počasí pro konkrétní umístění. Informace obsahují podrobnosti, jako je například datum a čas pozorování, stručný popis povětrnostních podmínek, ikona počasí, příznaky indikátoru měření, teplota a informace o rychlosti větru. Vrátí se také další podrobnosti, jako je RealFeel™ teploty a UV index.

Vývojáři můžou k načtení informací o počasí v rámci konkrétní trasy využít [rozhraní Get počasí společně s rozhraním API pro směrování](/rest/api/maps/weather/getweatheralongroute) . Služba také podporuje generování oznámení o počasí pro waypoints, která jsou ovlivněná riziky počasí, jako je například zahlcení nebo těžkých deště.

[Rozhraní API pro získání dlaždice získat mapu](/rest/api/maps/renderv2/getmaptilepreview) umožňuje vyžádat si předchozí, aktuální a budoucí paprskové a satelitní dlaždice.

![Příklad mapy s jedním z paprskových dlaždic počasí v reálném čase](media/about-azure-maps/intro_weather.png)

### <a name="maps-creator-service-preview"></a>Služba Maps Creator (Preview) 

Služba Maps Creator je sada webových služeb, které můžou vývojáři použít k vytváření aplikací s funkcemi mapy založenými na vnitřních datech mapy.

Tvůrce map nabízí tři základní služby:

* [Služba DataSet](/rest/api/maps/dataset) Pomocí služby DataSet Vytvořte datovou sadu z převedených dat balíčku vykreslování. Informace o požadavcích na sbalení balíčku najdete v tématu požadavky na balíčky pro vykreslování.

* [Převodní služba](/rest/api/maps/dataset). Pomocí převodní služby převeďte soubor návrhu DWG na data vykreslování balíčku pro mapy vnitřních souborů.

* [Služba TILESET](/rest/api/maps/tileset) Použijte službu TILESET k vytvoření vektorové reprezentace datové sady. Aplikace mohou používat TILESET k prezentaci vizuálního zobrazení na základě dlaždice datové sady.

* [Stavová služba funkcí](/rest/api/maps/featurestate). Použijte službu stavu funkce pro podporu stylů dynamické mapy. Styl dynamické mapy umožňuje aplikacím odrážet události v reálném čase na prostorech poskytovaných systémy IoT.

* [Služba WFS](/rest/api/maps/featurestate) Použijte službu WFS k dotazování na data mapy vnitřních dat. Služba WFS se řídí standardy [rozhraní API Open Geospatial Consortium](http://docs.opengeospatial.org/is/17-069r3/17-069r3.html) pro dotazování na jedinou datovou sadu.

### <a name="elevation-service-preview"></a>Služba zvýšení oprávnění (Preview)

Služba zvýšení oprávnění Azure Maps je webová služba, kterou můžou vývojáři použít k načtení dat zvýšení oprávnění odkudkoli na povrchu země.

Služba zvýšení oprávnění umožňuje načíst data zvýšení oprávnění ve dvou formátech:

* **Formát rastrového obrázku ve formátu TIFF** K načtení dat zvýšení úrovně ve formátu dlaždic použijte [vykreslení rozhraní vykreslování pro dlaždici pro vykreslení v2](/rest/api/maps/renderv2) .

* **Formát injson**. Použijte [rozhraní API pro zvýšení oprávnění](/rest/api/maps/elevation) k vyžádání dat zvýšení úrovně v rámci cest, v rámci definovaného ohraničujícího pole nebo v určitých souřadnicích. 

:::image type="content" source="./media/about-azure-maps/elevation.png" alt-text="Příklad mapy s daty zvýšení oprávnění":::


## <a name="programming-model"></a>Programovací model

Azure Maps je postavená na mobilitu a může vám pomáhat vyvíjet aplikace pro různé platformy. Používá programovací model, který je jazyk nezávislá a podporuje výstup JSON prostřednictvím [rozhraní REST API](/rest/api/maps/).

Azure Maps také nabízí pohodlný [mapový ovládací prvek JavaScriptu](/javascript/api/azure-maps-control) s jednoduchým programovacím modelem. Vývoj je rychlý a snadný pro webové i mobilní aplikace.





## <a name="power-bi-visual"></a>Vizuál Power BI

Azure Maps vizuál pro Power BI poskytuje bohatou sadu vizualizací dat pro prostorová data nad mapou. Odhaduje se, že více než 80% obchodních dat má kontext umístění. Azure Maps vizuál nabízí řešení bez kódu, které umožňuje získat přehled o tom, jak se tento kontext umístění týká a ovlivňuje vaše obchodní data.

:::image type="content" source="./media/about-azure-maps/intro-power-bi.png" border="false" alt-text="Power BI plochu s Azure Maps vizuálním zobrazením obchodních dat":::

Další informace najdete v tématu Začínáme s dokumentací k [aplikaci Azure Maps Power BI](power-bi-visual-getting-started.md) .

## <a name="usage"></a>Využití

Pokud chcete získat přístup k Azure Maps Services, přejděte na [Azure Portal](https://portal.azure.com) a vytvořte účet Azure Maps.

V Azure Maps se používá schéma ověřování založeného na klíčích. Při vytváření účtu se generují dva klíče. Pro ověření služby Azure Maps Services můžete použít kteroukoli z těchto kláves.

Poznámka: Azure Maps pro účely mapování funkcí a dotazů na umístění ("dotazy") od jiných výrobců TomTom. Dotazy nejsou propojeny s žádným zákazníkem nebo koncovým uživatelem, pokud jsou sdíleny pomocí TomTom a nelze je použít k identifikaci jednotlivců. Mobilita a povětrnostní služby, které zahrnují integraci s Moovit a AccuWeather, jsou momentálně ve [verzi Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Microsoft v současné době v procesu přidávání TomTom, Moovit a AccuWeather do seznamu subdodavatelů služeb Online Services.

## <a name="supported-regions"></a>Podporované oblasti

Služba Azure Maps Services je aktuálně dostupná kromě následujících zemí nebo oblastí:

* Čína
* Jižní Korea

Ověřte, jestli je umístění vaší aktuální IP adresy v podporované zemi nebo oblasti.

## <a name="next-steps"></a>Další kroky

Vyzkoušejte si ukázkovou aplikaci, která prezentuje Azure Maps:

[Rychlý Start: Vytvoření webové aplikace](quick-demo-map-app.md)

Udržujte si přehled o Azure Maps:

[Blog Azure Maps](https://azure.microsoft.com/blog/topics/azure-maps/)