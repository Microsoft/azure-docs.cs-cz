---
title: 'Kurz: Migrace z Map Google do Map Azure | Mapy Microsoft Azure'
description: Návod, jak migrovat z Map Google do Microsoft Azure Maps. Pokyny vás provede, jak přepnout na Azure Maps API a SDKs.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 7b73923b7fc32ae83bfc8405d074835c02031a63
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77913697"
---
# <a name="migrate-from-google-maps-to-azure-maps"></a>Migrace z Map Google do Azure Maps

Tento kurz poskytuje přehled o tom, jak migrovat webové, mobilní a serverové aplikace z Map Google na platformu Microsoft Azure Maps. Tento kurz zahrnuje srovnávací ukázky kódu, návrhy migrace a osvědčené postupy pro migraci do Azure Maps.

## <a name="azure-maps-platform-overview"></a>Přehled platformy Azure Maps

Azure Maps poskytuje vývojářům ze všech průmyslových odvětví výkonné geoprostorové funkce. Funkce jsou plné pravidelně aktualizovaných mapových dat, která poskytují geografický kontext pro webové a mobilní aplikace. Azure Maps má sadu rest API kompatibilní s rozhraním API Azure One. Rozhraní REST API nabízejí vykreslování map, vyhledávání, směrování, provoz, časová pásma, geolokace, geofencing, mapová data, počasí, mobilitu a prostorové operace. Operace jsou doprovázeny webovými sadami SDK i sadami Android, které usnadňují vývoj, jsou flexibilní a přenosné na různých platformách.

## <a name="high-level-platform-comparison"></a>Porovnání platformy na vysoké úrovni

Tabulka obsahuje podrobný seznam funkcí Azure Maps, které odpovídají funkcím Map Google. Tento seznam nezobrazuje všechny funkce Azure Maps. Mezi další funkce Azure Maps patří: usnadnění, geofencing, isochrony, prostorové operace, přímý přístup k dlaždicím mapy, dávkové služby a porovnání pokrytí dat (to znamená pokrytí snímků).

| Funkce Mapy Google         | Podpora Azure Maps                     |
|-----------------------------|:--------------------------------------:|
| Sada Web SDK                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | Plánováno                                |
| API služby REST           | ✓                                      |
| Směry (směrování)        | ✓                                      |
| Matice vzdálenosti             | ✓                                      |
| Zvýšení                   | Plánováno                                |
| Geokódování (vpřed/vzad) | ✓                                      |
| Geografická poloha                 | Není dostupné.                                    |
| Nejbližší silnice               | ✓                                      |
| Hledání míst               | ✓                                      |
| Podrobnosti o místech              | Není k dispozici – webové stránky & telefonní číslo k dispozici |
| Místa Fotografie               | Není dostupné.                                    |
| Umístit automatické dokončování          | ✓                                      |
| Přichytit k silnici                | ✓                                      |
| Rychlostní limity                | ✓                                      |
| Statické mapy                 | ✓                                      |
| Statický street view          | Není dostupné.                                    |
| Časové pásmo                   | ✓                                      |
| Vložené rozhraní API map           | Není dostupné.                                    |
| Adresy URL map                    | Není dostupné.                                    |

Mapy Google poskytují základní ověřování založené na klíčích. Azure Maps poskytuje základní ověřování založené na klíčích i ověřování azure active directory. Ověřování Azure Active Directory poskytuje více funkcí zabezpečení ve srovnání se základním ověřováním na základě klíče.

## <a name="licensing-considerations"></a>Aspekty licencování

Při migraci do Map Azure z Map Google zvažte následující body týkající se licencování.

- Azure Maps poplatky za použití interaktivní mapy, která je založena na počtu načtených mapových dlaždic. Na druhou stranu, Mapy Google účtují poplatky za načtení ovládacího prvku mapy. V interaktivních sadách Azure Maps SDK se dlaždice map automaticky ukládají do mezipaměti, aby se snížily náklady na vývoj. Jedna transakce Azure Maps se vygeneruje pro každých 15 mapových dlaždic, které se načtou. Interaktivní sady Azure Maps SDK používají dlaždice o velikosti 512 pixelů a v průměru vygenerují jednu nebo méně transakcí na zobrazení stránky.
- Často je nákladově efektivnější nahradit statické mapové obrázky z webových služeb Map Google sadou Azure Maps Web SDK. Web Ovásada Azure Maps Web SDK používá dlaždice mapy. Pokud uživatel posouvá a přibližuje mapu, služba často generuje pouze zlomek transakce na zatížení mapy. Webová sada Azure Maps s sadou Azure Maps má v případě potřeby možnosti pro zakázání posouvání a zvětšování. Webová sada Azure Maps navíc poskytuje mnohem více možností vizualizace než webová služba statické mapy.
- Azure Maps umožňují ukládání dat z jeho platformy v Azure. Také data mohou být uložena v mezipaměti jinde po dobu až šesti měsíců podle [podmínek použití](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46).

Tady jsou některé související prostředky pro Azure Maps:

- [Cenová stránka Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Cenová kalkulačka Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Podmínky používání Map Azure](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (zahrnuté ve smluvních podmínkách služeb Microsoft Online Services)
- [Výběr správné cenové úrovně v Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Navrhovaný plán migrace

Následuje plán migrace na vysoké úrovni.

1. Udělejte si inventuru sad SDK a služeb Map Google, které vaše aplikace používá. Ověřte, zda Azure Maps poskytuje alternativní sady SDK a služby.
2. Pokud ještě nemáte, vytvořte předplatné Azure [https://azure.com](https://azure.com)na .
3. Vytvořte účet Azure Maps ([dokumentaci](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)) a ověřovací klíč nebo Azure Active Directory ([dokumentace](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)).
4. Migrujte kód aplikace.
5. Otestujte migrose.
6. Nasaďte migrojedou aplikaci do produkčního prostředí.

## <a name="azure-maps-technical-resources"></a>Technické prostředky Azure Maps

Tady je seznam užitečných technických prostředků pro Azure Maps.

- Přehled:[https://azure.com/maps](https://azure.com/maps)
- Dokumentace:[https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Ukázky kódu sady Web SDK:[https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Vývojářfóra:[https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- Videa:[https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Blog:[https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Tech Blog:[https://aka.ms/AzureMapsTechBlog](https://aka.ms/AzureMapsTechBlog)
- Zpětná vazba map Azure (UserVoice):[https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)
- [Poznámkový blok Azure Maps Jupyter] [https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook]

## <a name="migration-support"></a>Podpora migrace

Vývojáři mohou hledat podporu migrace prostřednictvím [fór](https://aka.ms/AzureMapsForums) nebo prostřednictvím jedné z mnoha možností podpory Azure:[https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>Další kroky

Přečtěte si podrobnosti o migraci aplikace Mapy Google pomocí těchto článků:

> [!div class="nextstepaction"]
> [Migrace webové aplikace](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Migrace aplikace pro Android](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [Migrace webové služby](migrate-from-google-maps-web-services.md)
