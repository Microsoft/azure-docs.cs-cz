---
title: 'Kurz: migrace z map Bing na Azure Maps | Mapy Microsoft Azure'
description: V tomto kurzu se naučíte migrovat z map Bingu na Microsoft Azure Maps. Průvodce vás seznámí s postupem, jak přepnout na Azure Maps rozhraní API a sady SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 643e49bdba76051c873ed549d5f6c21487f34056
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90108665"
---
# <a name="migrate-from-bing-maps-to-azure-maps"></a>Migrace z map Bingu na Azure Maps

V této příručce najdete informace o tom, jak migrovat webové a mobilní aplikace a aplikace založené na serveru z mapy Bing na Azure Mapsovou platformu. Tato příručka obsahuje srovnávací ukázky kódu, návrhy migrace a osvědčené postupy pro migraci na Azure Maps.

## <a name="azure-maps-platform-overview"></a>Přehled platformy Azure Maps

Azure Maps poskytuje vývojářům ze všech průmyslových odvětví výkonné geoprostorové funkce, které jsou k dispozici pro zajištění geografického kontextu pro webové a mobilní aplikace. Azure Maps je sada rozhraní REST API, která je kompatibilní s rozhraním Azure One API pro mapy, vyhledávání, směrování, provoz, časová pásma, monitorování geografických zón, mapování dat, data o počasí a mnoho dalších služeb, které spolu s nimi podporují webové i sady Android SDK, a usnadňují tak vývoj a flexibilitu v různých platformách. [Azure Maps je také k dispozici v Power BI](power-bi-visual-getting-started.md).

## <a name="high-level-platform-comparison"></a>Porovnání platforem vysoké úrovně

Následující tabulka obsahuje seznam funkcí mapy Bing na nejvyšší úrovni a relativní podporu pro tyto funkce v Azure Maps. Tento seznam neobsahuje další funkce Azure Maps, jako je usnadnění přístupu, rozhraní API pro monitorování geografických zón, přenosové služby, prostorové operace, přístup k dlaždici map a služby Batch.

| Funkce mapy Bing                     | Podpora Azure Maps |
|---------------------------------------|:------------------:|
| Sada Web SDK                               | ✓                  |
| Android SDK                           | ✓                  |
| iOS SDK                               | Plánováno            |
| SADA UWP SDK                               | Plánováno            |
| SADA WPF SDK                               | Plánováno            |
| Rozhraní API služby REST                     | ✓                  |
| Automatické návrhy                           | ✓                  |
| Pokyny (včetně nákladní auto)          | ✓                  |
| Matice vzdáleností                       | ✓                  |
| Zvyšovat                            | Plánováno            |
| Obrázek – statická mapa                  | ✓                  |
| Metadata z snímků                      | ✓                  |
| Izochronů                            | ✓                  |
| Místní přehledy                        | ✓                  |
| Místní hledání                          | ✓                  |
| Rozpoznávání polohy                  | ✓                  |
| Umístění (dopředného nebo zpětného geografického kódování) | ✓                  |
| Optimalizované trasy itineráře            | Plánováno            |
| Přichycení k cestám                         | ✓                  |
| Prostorové Data Services (SDS)           | Částečné            |
| Časové pásmo                             | ✓                  |
| Incidenty provozu                     | ✓                  |
| Mapy řízené konfigurací             | –                |

Mapy Bing poskytují základní ověřování založené na klíčích. Azure Maps poskytuje základní ověřování založené na klíčích i vysoce zabezpečené ověřování Azure Active Directory.

## <a name="licensing-considerations"></a>Požadavky na licencování

Při migraci na Azure Maps ze služby mapy Bing byste měli zvážit následující postup s ohledem na licencování.

-   Azure Maps poplatky za použití interaktivních map na základě počtu načtených dlaždic map, zatímco mapy Bing se účtují za načítání mapového ovládacího prvku (relace). Na Azure Maps jsou mapové dlaždice automaticky ukládány do mezipaměti, aby se snížily náklady pro vývojáře. Jedna Azure Mapsová transakce je vygenerována pro každých 15 dlaždic map, které jsou načteny. Interaktivní Azure Maps sady SDK používají dlaždice 512-pixel a v průměru generuje jednu nebo méně transakcí na zobrazení stránky.

-   Azure Maps umožňuje ukládat data z své platformy do Azure. Dá se taky ukládat do mezipaměti jinde až po dobu šesti měsíců, a to podle [podmínek použití](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

Tady je několik prostředků souvisejících s licencováním pro Azure Maps:

-   [Stránka s cenami Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
-   [Cenová kalkulačka Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
-   [Azure Maps podmínky použití](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (zahrnuté do podmínek služby Microsoft Online Services)
-   [Výběr správné cenové úrovně v Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Navrhovaný plán migrace

Následuje plán migrace na vysoké úrovni.

1.  Seznamte se s využitím sad SDK a služeb Bing Maps, které vaše aplikace používá, a ověřte, že Azure Maps poskytuje alternativní sady SDK a služby, na které můžete migrovat.
2.  Vytvořte si předplatné Azure (pokud ho ještě nemáte) na adrese <https://azure.com> .
3.  Vytvořte účet Azure Maps ([dokumentace](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)) a ověřovací klíč nebo Azure Active Directory ([dokumentace](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)).
4.  Migrujte kód aplikace.
5.  Otestujte migrovaná aplikace.
6.  Nasaďte migrovaná aplikace do produkčního prostředí.

## <a name="azure-maps-technical-resources"></a>Azure Maps technické prostředky

Tady je seznam užitečných technických prostředků pro Azure Maps.

-   Přehled: https://azure.com/maps
-   Nápovědě <https://aka.ms/AzureMapsDocs>
-   Ukázky kódu pro web SDK: <https://aka.ms/AzureMapsSamples>
-   Fóra pro vývojáře: <https://aka.ms/AzureMapsForums>
-   Videa <https://aka.ms/AzureMapsVideos>
-   Webový <https://aka.ms/AzureMapsBlog>
-   Azure Maps váš názor (UserVoice): <https://aka.ms/AzureMapsFeedback>

## <a name="migration-support"></a>Podpora migrace

Vývojáři můžou vyhledat podporu migrace prostřednictvím [fór](https://aka.ms/AzureMapsForums) nebo pomocí jedné z mnoha možností podpory Azure: <https://azure.microsoft.com/support/options/>

## <a name="new-terminology"></a>Nová terminologie 

Níže najdete seznam běžných termínů map Bingu, které jsou známé jiným termínem v Azure Maps.

| Termín mapy Bing                    | Azure Maps termín                                                |
|-----------------------------------|----------------------------------------------------------------|
| Musejí                            | Satelitní nebo letecké                                            |
| Pokyny                        | Může se taky označovat jako směrování.                             |
| Entity                          | Geometrií nebo funkce                                         |
| `EntityCollection`                | Zdroj dat nebo vrstva                                           |
| `Geopoint`                        | Pozice                                                       |
| `GeoXML`                          | Soubory XML v modulu prostorového vstupu/výstupu                             |
| Základní překrytí                    | Vrstva obrázku                                                    |
| Hybrid (v referenci na typ mapy) | Satelit s silnicemi                                           |
| Infobox                           | Překryvný                                                          |
| Umístění                          | Pozice                                                       |
| `LocationRect`                    | Ohraničovací rámeček                                                   |
| Typ mapování                          | Styl mapy                                                      |
| Navigační panel                    | Výběr stylu mapy, ovládací prvek přiblížení, ovládací prvek sklonu, ovládací prvek kompas |
| Ikonu                           | Bublinová vrstva, vrstva symbolu nebo značka HTML                      |

## <a name="next-steps"></a>Další kroky

Přečtěte si podrobnosti o migraci aplikace Bing Maps pomocí těchto článků:

> [!div class="nextstepaction"]
> [Migrace webové aplikace](migrate-from-bing-maps-web-app.md)

> [!div class="nextstepaction"]
> [Migrace webové služby](migrate-from-bing-maps-web-services.md)
