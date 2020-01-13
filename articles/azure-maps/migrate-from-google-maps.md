---
title: 'Kurz: migrace z Google Maps na Azure Maps | Mapy Microsoft Azure'
description: Kurz migrace z Google Maps na Microsoft Azure Maps Průvodce vás seznámí s postupem, jak přepnout na Azure Maps rozhraní API a sady SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 0e841b1f386d45ddb4af8598855d8e739750307e
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910746"
---
# <a name="migrate-from-google-maps-to-azure-maps"></a>Migrace z Google Maps na Azure Maps

V tomto kurzu najdete informace o tom, jak migrovat webové a mobilní aplikace a aplikace založené na serveru z Google Maps na platformu Microsoft Azure Maps. Tento kurz obsahuje srovnávací ukázky kódu, návrhy migrace a osvědčené postupy pro migraci na Azure Maps.

## <a name="azure-maps-platform-overview"></a>Přehled platformy Azure Maps

Azure Maps poskytuje vývojářům ze všech průmyslových odvětví výkonné geoprostorové funkce, které jsou zabaleny s pravidelnými aktualizovanými daty mapy a poskytují geografickou souvislost pro webové a mobilní aplikace. Azure Maps má sadu rozhraní REST API, která je kompatibilní s rozhraním Azure One API pro mapy, vyhledávání, směrování, provoz, časová pásma, geografickou polohu, monitorování geografických zón, mapování dat, počasí, mobilitu a prostorových operací, které spolu s nimi nabízí web i sady Android SDK, které usnadňují vývoj, flexibilní a Přenosné napříč různými platformami.

## <a name="high-level-platform-comparison"></a>Porovnání platforem vysoké úrovně

Následující tabulka obsahuje seznam funkcí Google Maps na nejvyšší úrovni a relativní podporu pro tyto funkce v Azure Maps. Tento seznam neobsahuje další funkce Azure Maps, jako je usnadnění přístupu, rozhraní API pro monitorování geografických zón, izochronů, prostorové operace, přímý mapový přístup dlaždic, služby Batch a porovnání pokrytí dat (tj. pokrytí snímků).

| Funkce mapy Google         | Podpora Azure Maps                     |
|-----------------------------|:--------------------------------------:|
| Sada Web SDK                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | Plánováno                                |
| Rozhraní API služby REST           | ✓                                      |
| Směry (směrování)        | ✓                                      |
| Matice vzdáleností             | ✓                                      |
| Zvýšení oprávnění                   | Plánováno                                |
| Geografické kódování (přesměrné/obrácené) | ✓                                      |
| Geografická poloha                 | Nevztahuje se                                    |
| Hledání míst               | ✓                                      |
| Podrobnosti o místech              | Není k dispozici – web & telefonní číslo. |
| Místo fotek               | Nevztahuje se                                    |
| Umístit automatické dokončování          | ✓                                      |
| Statické mapy                 | ✓                                      |
| Statické zobrazení ulice          | Nevztahuje se                                    |
| Časové pásmo                   | ✓                                      |
| Rozhraní API pro mapování Embedded           | Nevztahuje se                                    |
| Mapování adres URL                    | Nevztahuje se                                    |

Google Maps poskytuje základní ověřování založené na klíčích. Azure Maps poskytuje základní ověřování založené na klíčích i vysoce zabezpečené ověřování Azure Active Directory.

## <a name="licensing-considerations"></a>Požadavky na licencování

Při migraci na Azure Maps z Google Maps by se měly brát v úvahu tyto body s ohledem na licencování.

- Azure Maps poplatky za použití interaktivních map na základě počtu načtených dlaždic mapy, zatímco Google Maps účtuje za načítání mapového ovládacího prvku. V interaktivních sadách Azure Maps SDK se dlaždice map automaticky ukládají do mezipaměti, aby se snížily náklady pro vývojáře. Jedna Azure Mapsová transakce je vygenerována pro každých 15 dlaždic map, které jsou načteny. Interaktivní Azure Maps sady SDK používají dlaždice 512-pixel a v průměru generuje jednu nebo méně transakcí na zobrazení stránky.
- K nahrazení statických imagí map z webových služeb Google Maps pomocí Azure Maps Web SDK je často mnohem cenově výhodnější, protože se používá dlaždice mapy a pokud uživatel neposouvá a nezvětšuje mapu, často generuje jenom zlomek transakce na zatížení mapy. Sada Azure Maps Web SDK obsahuje možnosti pro vypnutí posouvání a zvětšování. Azure Maps webová sada SDK navíc nabízí spoustu dalších možností vizualizace než webová služba statické mapy.
- Azure Maps umožňuje ukládat data z své platformy do Azure. Dá se taky ukládat do mezipaměti jinde až po dobu šesti měsíců, a to podle [podmínek použití](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46).

Tady je několik souvisejících prostředků pro Azure Maps:

- [Stránka s cenami Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Cenová kalkulačka Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Azure Maps podmínky použití](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (zahrnuté do podmínek služby Microsoft Online Services)
- [Výběr správné cenové úrovně v Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Navrhovaný plán migrace

Následuje plán migrace na vysoké úrovni.

1. Seznamte se s tím, jaké sady SDK a služby Google Maps vaše aplikace používá, a ověřte, že Azure Maps poskytuje alternativní sady SDK a služby, na které můžete migrovat.
2. Vytvořte si předplatné Azure (pokud ho ještě nemáte) na [https://azure.com](https://azure.com).
3. Vytvořte účet Azure Maps ([dokumentace](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)) a ověřovací klíč nebo Azure Active Directory ([dokumentace](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)).
4. Migrujte kód aplikace.
5. Otestujte migrovaná aplikace.
6. Nasaďte migrovaná aplikace do produkčního prostředí.

## <a name="azure-maps-technical-resources"></a>Azure Maps technické prostředky

Tady je seznam užitečných technických prostředků pro Azure Maps.

- Přehled: [https://azure.com/maps](https://azure.com/maps)
- Dokumentace: [https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Ukázky kódu web SDK: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Vývojářské fóra: [https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- Videa: [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Blog: [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Azure Maps Feedback (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)

## <a name="migration-support"></a>Podpora migrace

Vývojáři můžou vyhledat podporu migrace prostřednictvím [fór](https://aka.ms/AzureMapsForums) nebo pomocí jedné z mnoha možností podpory Azure: [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>Další kroky

Naučte se migrovat aplikaci pro Google Maps pomocí těchto článků:

> [!div class="nextstepaction"]
> [Migrace webové aplikace](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Migrace aplikace pro Android](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [Migrace webové služby](migrate-from-google-maps-web-services.md)
