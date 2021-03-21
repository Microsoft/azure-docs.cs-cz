---
title: 'Kurz: migrace z map Bing na Azure Maps | Mapy Microsoft Azure'
description: V tomto kurzu se naučíte migrovat z map Bingu na Microsoft Azure Maps. Průvodce vás seznámí s postupem, jak přepnout na Azure Maps rozhraní API a sady SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 9bd0516889733a666bf15668cffd124dcc468f3e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100388953"
---
# <a name="tutorial-migrate-from-bing-maps-to-azure-maps"></a>Kurz: migrace z map Bingu na Azure Maps

V této příručce najdete informace o tom, jak migrovat webové a mobilní aplikace a aplikace založené na serveru z mapy Bing na Azure Mapsovou platformu. Tato příručka obsahuje srovnávací ukázky kódu, návrhy migrace a osvědčené postupy pro migraci na Azure Maps. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Porovnání vysoké úrovně pro ekvivalentní funkce map Bing dostupné v Azure Maps.
> * Jaké jsou rozdíly v licencích, které je potřeba vzít v úvahu.
> * Postup plánování migrace.
> * Kde najít technické prostředky a podporu.

## <a name="prerequisites"></a>Předpoklady

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).
2. [Vytvořit účet Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného. Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](how-to-manage-authentication.md).

## <a name="azure-maps-platform-overview"></a>Přehled platformy Azure Maps

Azure Maps poskytuje vývojářům ze všech průmyslových odvětví výkonné geoprostorové funkce, které jsou k dispozici pro zajištění geografického kontextu pro webové a mobilní aplikace. Azure Maps je sada rozhraní REST API, která je kompatibilní s rozhraním Azure One API pro mapy, vyhledávání, směrování, provoz, časová pásma, monitorování geografických zón, mapování dat, data o počasí a mnoho dalších služeb, které spolu s nimi podporují webové i sady Android SDK, a usnadňují tak vývoj a flexibilitu v různých platformách. [Azure Maps je také k dispozici v Power BI](power-bi-visual-getting-started.md).

## <a name="high-level-platform-comparison"></a>Porovnání platforem vysoké úrovně

Následující tabulka obsahuje seznam funkcí mapy Bing na nejvyšší úrovni a relativní podporu pro tyto funkce v Azure Maps. Tento seznam neobsahuje další funkce Azure Maps, jako je usnadnění přístupu, rozhraní API pro monitorování geografických zón, přenosové služby, prostorové operace, přístup k dlaždici map a služby Batch.

| Funkce mapy Bing                     | Podpora Azure Maps |
|---------------------------------------|:------------------:|
| Sada Web SDK                               | ✓                  |
| Android SDK                           | ✓                  |
| iOS SDK                               | Plánováno            |
| SADA UWP SDK                               | –                 |
| SADA WPF SDK                               | –                 |
| Rozhraní API služby REST                     | ✓                  |
| Automatické návrhy                           | ✓                  |
| Pokyny (včetně nákladní auto)          | ✓                  |
| Matice vzdáleností                       | ✓                  |
| Zvyšovat                            | ✓ (Preview)        |
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

Mapy Bing poskytují základní ověřování založené na klíčích. Azure Maps poskytuje základní ověřování založené na klíčových a vysoce zabezpečených Azure Active Directorych ověřování.

## <a name="licensing-considerations"></a>Požadavky na licencování

Při migraci na Azure Maps z map Bing by se měly brát v úvahu následující informace týkající se licencování.

* Azure Maps poplatky za použití interaktivních map na základě počtu načtených dlaždic map, zatímco mapy Bing se účtují za načítání mapového ovládacího prvku (relace). Aby se snížily náklady pro vývojáře, Azure Maps automaticky ukládá do mezipaměti dlaždice map. Jedna Azure Mapsová transakce je vygenerována pro každých 15 dlaždic map, které jsou načteny. Interaktivní Azure Maps sady SDK používají dlaždice 512-pixel a v průměru generuje jednu nebo méně transakcí na zobrazení stránky.

* Azure Maps umožňuje ukládat data z své platformy do Azure. Dá se taky ukládat do mezipaměti jinde až po dobu šesti měsíců, a to podle [podmínek použití](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

Zde jsou některé prostředky týkající se licencování pro Azure Maps:

-   [Stránka s cenami Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
-   [Cenová kalkulačka Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
-   [Azure Maps podmínky použití](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (zahrnuté do podmínek služby Microsoft Online Services)
-   [Výběr správné cenové úrovně v Azure Maps](./choose-pricing-tier.md)

## <a name="suggested-migration-plan"></a>Navrhovaný plán migrace

Tady je příklad plánu migrace na vysoké úrovni.

1.  Seznamte se s využitím sad SDK a služeb Bing Maps, které vaše aplikace používá, a ověřte, že Azure Maps poskytuje alternativní sady SDK a služby, na které můžete migrovat.
2.  Vytvořte si předplatné Azure (pokud ho ještě nemáte) na adrese <https://azure.com> .
3.  Vytvořte účet Azure Maps ([dokumentace](./how-to-manage-account-keys.md)) a ověřovací klíč nebo Azure Active Directory ([dokumentace](./how-to-manage-authentication.md)).
4.  Migrujte kód aplikace.
5.  Otestujte migrovaná aplikace.
6.  Nasaďte migrovaná aplikace do produkčního prostředí.

## <a name="create-an-azure-maps-account"></a>Vytvoření účtu Azure Maps

Pokud chcete vytvořit účet Azure Maps a získat přístup k platformě Azure Maps, postupujte podle těchto kroků:

1. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).
2. Přihlaste se na [Azure Portal](https://portal.azure.com/).
3. Vytvořte [účet Azure Maps](./how-to-manage-account-keys.md).
4. [Získejte Azure Maps klíč předplatného](./how-to-manage-authentication.md#view-authentication-details) nebo nastavte Azure Active Directory ověřování pro rozšířené zabezpečení.

## <a name="azure-maps-technical-resources"></a>Azure Maps technické prostředky

Tady je seznam užitečných technických prostředků pro Azure Maps.

* Přehled: <https://azure.com/maps>
* Nápovědě <https://aka.ms/AzureMapsDocs>
* Ukázky kódu pro web SDK: <https://aka.ms/AzureMapsSamples>
* Fóra pro vývojáře: <https://aka.ms/AzureMapsForums>
* Videa <https://aka.ms/AzureMapsVideos>
* Webový <https://aka.ms/AzureMapsBlog>
* Azure Maps váš názor (UserVoice): <https://aka.ms/AzureMapsFeedback>

## <a name="migration-support"></a>Podpora migrace

Vývojáři můžou vyhledat podporu migrace prostřednictvím [fór](/answers/topics/azure-maps.html) nebo pomocí jedné z mnoha možností podpory Azure: <https://azure.microsoft.com/support/options/>

## <a name="new-terminology"></a>Nová terminologie

Následující seznam obsahuje běžné výrazy služby mapy Bing a jejich odpovídající Azure Mapsé výrazy.

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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejsou k dispozici žádné prostředky, které vyžadují vyčištění.

## <a name="next-steps"></a>Další kroky

Přečtěte si podrobnosti o migraci aplikace Bing Maps pomocí těchto článků:

> [!div class="nextstepaction"]
> [Migrace webové aplikace](migrate-from-bing-maps-web-app.md)
