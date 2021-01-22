---
title: Nejčastější dotazy k častým dotazům služby Microsoft Azure Maps (Preview)
description: Najděte odpověď na časté otázky týkající se Azure Maps dat a funkcí služby počasí (Preview).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9c8e971b4fda313ffede58455dd6d057d6848ce4
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678125"
---
# <a name="azure-maps-weather-services-preview-frequently-asked-questions-faq"></a>Nejčastější dotazy Azure Maps počasí Services (Preview)

> [!IMPORTANT]
> Služba Azure Maps počasí jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek obsahuje odpovědi na běžné dotazy týkající se Azure Maps dat a funkcí [služby počasí](/rest/api/maps/weather) . Jsou pokrytá následující témata:

* Zdroje dat a datové modely
* Pokrytí a dostupnost služeb počasí
* Frekvence aktualizace dat
* Vývoj s využitím sad Azure Maps SDK
* Možnosti pro vizualizaci dat počasí, včetně integrace služby Microsoft Power BI

## <a name="data-sources-and-data-models"></a>Zdroje dat a datové modely

**Jak Azure Maps zdrojová data počasí?**

Azure Maps je postavená na spolupráci špičkových partnerů pro mobilitu a polohu, včetně AccuWeather, který poskytuje základní data o počasí. Pokud si chcete přečíst oznámení o Azure Maps "spolupráci s AccuWeather, přečtěte si informace o [deště nebo září: Azure Maps povětrnostní služby, které vám poskytnou přehled o vašem podniku](https://azure.microsoft.com/blog/rain-or-shine-azure-maps-weather-services-will-bring-insights-to-your-enterprise/).

AccuWeather má v reálném čase k dispozici informace o počasí a prostředí, které jsou dostupné kdekoli na světě, a to z velké části kvůli jejich partnerství s řadou státních státních povětrnostních agentur a dalšími proprietárními opatřeními. Seznam těchto základních informací je uvedený níže.

* Veřejně dostupné výsledky globálních povrchů z vládních úřadů
* Proprietární datové sady pro pozorování povrchu od vlád a soukromých společností
* Paprsková data s vysokým rozlišením pro více než 40 zemí nebo oblastí
* Nejlepší data o globálních bleskech v reálném čase
* Upozornění na počasí vydané vládou pro více než 60 zemí/oblastí a území
* Satelitní data z mikrostacionárních povětrnostních družicí, která pokrývají celý svět
* Přes 150 číselných modelů prognóz, včetně interních, proprietárních modelování, modelů pro státní správu, jako je GFS (Global Odhading System), a jedinečné downscaled modely poskytované soukromými společnostmi
* Pozorování kvality ovzduší
* Pozorování z oddělení dopravy

Desítky tisíců povrchových pozorování spolu s dalšími daty jsou součástí vytváření a ovlivňujících aktuální podmínky, které jsou uživatelům k dispozici. To zahrnuje nejen volně dostupné standardní datové sady, ale také jedinečné pozorování získaná od národních meteorologických služeb v mnoha zemích nebo oblastech, včetně Indie, Brazílie a Kanady a dalších přístupných vstupů. Tyto jedinečné datové sady zvyšují prostorové a dočasné rozlišení aktuálních dat podmínek pro naše uživatele. 

Tyto datové sady jsou v reálném čase přezkoumány, aby se zajistila přesnost systému digitálního odhadování, která využívá vlastní algoritmy uměle AccuWeather pro průběžnou úpravu předpovědí a zajišťuje tak, že vždy zahrnou nejnovější data a tím maximalizuje jejich nepřetržitou přesnost.

**Jaké modely vytvářejí data předpovědi počasí?**

Pro formulaci globálních prognóz se využívá řada pomocných systémů předpovědi počasí. Více než 150 číselných modelů prognóz se používá každý den, externí i interní datové sady. To zahrnuje i modely pro státní správu, jako je ECMWF Evropské centra a systém GFS (Global FORECAST System). Kromě toho AccuWeather zahrnuje proprietární modely s vysokým rozlišením, které snížit předpověď na konkrétní umístění a strategické regionální domény pro předpověď počasí s větší přesností. AccuWeather jedinečné algoritmy pro prolnutí a vážení byly vyvinuty za posledních několik desetiletí. Tyto algoritmy optimálně využívají nejrůznější vstupy prognóz k poskytování vysoce přesných předpovědí.

## <a name="weather-services-preview-coverage-and-availability"></a>Pokrytí a dostupnost služeb počasí (ve verzi Preview)

**Jaký druh pokrytí můžu očekávat u různých zemí nebo oblastí?**

Pokrytí služeb se liší podle země nebo oblasti. Všechny funkce nejsou k dispozici v každé zemi nebo oblasti. Další informace najdete v [dokumentaci k pokrytí](./weather-coverage.md).

## <a name="data-update-frequency"></a>Frekvence aktualizace dat

**Jak často se data aktuální podmínky aktualizují?**

Data aktuální podmínky se přibližně aktualizují nejméně jednou za hodinu, ale je možné je aktualizovat častěji pomocí rychle se měnících podmínek – třeba při velkých teplotních změnách, změnách podmínek nebe, změnách srážek atd. Většina pozorovacích stanic po celém světě sestavuje mnoho časů za hodinu, protože se mění podmínky. Několik oblastí se ale pořád aktualizuje jenom jednou, dvakrát nebo čtyřikrát za hodinu v naplánovaných intervalech.  

Azure Maps ukládá do mezipaměti aktuální podmínky dat až po dobu 10 minut, které vám pomůžou zachytit četnost aktualizací dat téměř v reálném čase. Pokud chcete zjistit, kdy vyprší platnost odpovědi v mezipaměti, a vyhnout se zobrazování zastaralých dat, můžete použít informace hlavičky Expires v hlavičce HTTP odpovědi Azure Maps API.

**Jak často jsou data o každodenním a hodinovém plánování aktualizována?**

Data o denní a hodinové prognóze se aktualizují několikrát denně, protože se obdrží aktualizovaná pozorování.  Pokud je například překročena odhadovaná vysoká/nízká teplota, naše data prognózy se upraví při příštím cyklu aktualizace. Tato situace může nastat v různých intervalech, ale obvykle probíhá do jedné hodiny. Řada náhlých povětrnostních podmínek může způsobit změnu dat předpovědi. Například při horké letní odpoledne se může izolovaný Thunderstorm náhle vymezit a přináší tak těžké cloudové pokrytí a deště. Izolovaná úroveň může efektivně uvolnit teplotu o až 10 stupňů. Tato nová hodnota teploty bude mít vliv na hodinové a denní prognózy pro zbytek dne a jako takový bude aktualizována v našich datových sadách.

Rozhraní API pro Azure Maps prognózy jsou ukládána do mezipaměti až po dobu 30 minut. Pokud chcete zjistit, kdy vyprší platnost odpovědi v mezipaměti, a vyhnout se zobrazování zastaralých dat, můžete použít informace hlavičky Expires v hlavičce HTTP odpovědi Azure Maps API. V závislosti na konkrétním případu použití produktu a UŽIVATELSKÉM rozhraní (uživatelské rozhraní) doporučujeme aktualizace podle potřeby aktualizovat.

## <a name="developing-with-azure-maps-sdks"></a>Vývoj s využitím sad Azure Maps SDK

**Podporuje Azure Maps Web SDK integraci služeb počasí (Preview)?**

Sada Azure Maps Web SDK poskytuje modul služeb. Modul služby je pomocná knihovna usnadňující používání služby Azure Maps REST ve webových nebo Node.jsch aplikacích. pomocí JavaScriptu nebo TypeScript. Informace o tom, jak začít, najdete v naší [dokumentaci](./how-to-use-services-module.md).

**Podporuje Azure Maps Android SDK nativně podporu integrace s povětrnostními službami (Preview)?**

Azure Maps sady Android SDK podporují vrstvy dlaždic Mercator, které mohou mít zápis x/y/přiblížení, zápis ve formátu Quad Key nebo EPSG 3857.

Plánujeme vytvořit modul služeb pro Java/Android podobně jako modul Web SDK. Modul služeb Android usnadňuje přístup ke všem službám Azure Maps v aplikaci Java nebo Android.  

## <a name="data-visualizations"></a>Vizualizace dat  

**Azure Maps Power BI vizuální podporu Azure Maps počasí?**

Yes. Pokud se chcete dozvědět, jak migrovat paprskové a infračervené satelitní dlaždice na vizuál Microsoft Power BI, přečtěte si téma [Přidání vrstvy dlaždic do Power BI vizuálu](./power-bi-visual-add-tile-layer.md). 

**Návody interpretovat barvy používané pro paprskové a satelitní dlaždice?**

Článek Azure Maps [počasí pro předpověď počasí](./weather-services-concepts.md#radar-and-satellite-imagery-color-scale) obsahuje průvodce, který vám pomůže interpretovat barvy používané pro paprskové a satelitní dlaždice. Tento článek se zabývá barevnými ukázkami a HEXADECIMÁLNÍmi kódy barev.
 
**Můžu vytvářet paprskové a satelitní animace dlaždic?**

Yes. Kromě paprskových a satelitních Azure Maps dlaždic v reálném čase si zákazníci můžou vyžádat předchozí a budoucí dlaždice a vylepšit vizualizace dat pomocí překryvů map. To lze provést přímo voláním metody [Get map v2 API](/rest/api/maps/renderv2/getmaptilepreview) nebo vyžádáním dlaždic prostřednictvím Azure Maps Web SDK. Paprskové dlaždice jsou k dispozici po dobu až 1,5 hodin v minulosti a až 2 hodiny v budoucnosti. Dlaždice a jsou k dispozici v intervalech 5 minut. Infračervené dlaždice jsou k dispozici po dobu až 3 hodin v minulosti a jsou dostupné v intervalu 10 minut. Další informace najdete v [Ukázka kódu](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Animated%20tile%20layer)animace Open Source dlaždice s počasí.  

**Nabízíte ikony pro různé povětrnostní podmínky?**

Yes. [Tady](./weather-services-concepts.md#weather-icons)můžete najít ikony a jejich příslušné kódy. Všimněte si, že pouze některá rozhraní API služby počasí (verze Preview), jako je například  [načíst aktuální podmínky rozhraní API](/rest/api/maps/weather/getcurrentconditionspreview), vrátí *iconCode* v odpovědi. Další informace naleznete v aktuální ukázce WeatherConditions open source [Code](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Get%20current%20weather%20at%20a%20location).

## <a name="next-steps"></a>Další kroky

Pokud se vám časté otázky nedotazují, můžete nás kontaktovat prostřednictvím následujících kanálů (v pořadí eskalace):

* Část s poznámkami tohoto článku.
* [MSFT Q&stránku pro Azure Maps](/answers/topics/azure-maps.html).
* podpora Microsoftu. Chcete-li vytvořit novou žádost o podporu, v [Azure Portal](https://portal.azure.com/)na kartě Help klikněte na tlačítko **pomoc** a podpora a pak vyberte **Nová žádost o podporu**.
* [Azure Maps UserVoice](https://feedback.azure.com/forums/909172-azure-maps) k odeslání žádostí o funkce.

Naučte se, jak vyžádat data počasí v reálném čase a předpovědi s využitím Azure Maps povětrnostních služeb (Preview):
> [!div class="nextstepaction"]
> [Vyžádat data o počasí v reálném čase ](how-to-request-weather-data.md)

Koncepty Azure Maps počasí Services (Preview):
> [!div class="nextstepaction"]
> [Koncepce služby Weather](weather-coverage.md)

Prozkoumejte dokumentaci k rozhraní API služby Azure Maps počasí Services (Preview):

> [!div class="nextstepaction"]
> [Služba Azure Maps Weather](/rest/api/maps/weather)