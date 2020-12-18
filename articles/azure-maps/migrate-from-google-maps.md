---
title: Kurz – migrace z Google Maps na Azure Maps | Mapy Microsoft Azure
description: Kurz migrace z Google Maps na Microsoft Azure Maps Průvodce vás seznámí s postupem, jak přepnout na Azure Maps rozhraní API a sady SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 09/23/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: decf289614179718d5c3424f6d4482a5ce2c43e1
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680719"
---
# <a name="tutorial-migrate-from-google-maps-to-azure-maps"></a>Kurz: migrace z Google Maps na Azure Maps

Tento článek poskytuje přehledy o tom, jak migrovat webové a mobilní aplikace a aplikace založené na serveru z Google Maps na platformu Microsoft Azure Maps. Tento kurz obsahuje srovnávací ukázky kódu, návrhy migrace a osvědčené postupy pro migraci na Azure Maps. V tomto kurzu se dozvíte:

> [!div class="checklist"]
> * Porovnání na vysoké úrovni pro ekvivalentní funkce Google Maps dostupné v Azure Maps.
> * Jaké jsou rozdíly v licencích, které je potřeba vzít v úvahu.
> * Postup plánování migrace.
> * Kde najít technické prostředky a podporu.

## <a name="prerequisites"></a>Požadavky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).
2. [Vytvořit účet Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného. Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](how-to-manage-authentication.md).

## <a name="azure-maps-platform-overview"></a>Přehled platformy Azure Maps

Azure Maps poskytuje vývojářům ze všech odvětví výkonné geoprostorové možnosti. Možnosti jsou zabaleny s pravidelnými aktualizovanými daty mapy a poskytují geografickou souvislost pro webové a mobilní aplikace. Azure Maps má sadu rozhraní REST API, která je kompatibilní s rozhraním Azure One API. Rozhraní REST API nabízí mapy pro vykreslování, vyhledávání, směrování, provoz, časová pásma, geografickou polohu, monitorování geografických zón, mapování dat, počasí, mobilitu a prostorové operace. K operacím jsou připojeny webové i sady Android SDK, které usnadňují vývoj, flexibilní a přenosné prostředí napříč různými platformami.

## <a name="high-level-platform-comparison"></a>Porovnání platforem vysoké úrovně

Tabulka poskytuje seznam Azure Mapsch funkcí, které odpovídají funkcím služby Google Maps, na nejvyšší úrovni. V tomto seznamu se nezobrazují všechny funkce Azure Maps. Mezi další Azure Maps funkce patří: usnadnění přístupu, monitorování geografických zón, izochronů, prostorové operace, přímý přístup dlaždic map, služby Batch a porovnání pokrytí dat (tj. pokrytí snímků).

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
| Geografická poloha                 | –                                    |
| Nejbližší silnice               | ✓                                      |
| Hledání míst               | ✓                                      |
| Podrobnosti o místech              | Není k dispozici – web & telefonní číslo. |
| Místo fotek               | –                                    |
| Umístit automatické dokončování          | ✓                                      |
| Přichycení k cestám                | ✓                                      |
| Omezení rychlosti                | ✓                                      |
| Statické mapy                 | ✓                                      |
| Statické zobrazení ulice          | –                                    |
| Časové pásmo                   | ✓                                      |
| Rozhraní API pro mapování Embedded           | –                                    |
| Mapování adres URL                    | –                                    |

Google Maps poskytuje základní ověřování založené na klíčích. Azure Maps poskytuje jak základní ověřování založené na klíčích, tak ověřování Azure Active Directory. Ověřování Azure Active Directory poskytuje více funkcí zabezpečení oproti základnímu ověřování založenému na klíčích.

## <a name="licensing-considerations"></a>Požadavky na licencování

Při migraci na Azure Maps z webu Google Maps zvažte následující body týkající se licencování.

* Azure Maps poplatky za použití interaktivních map, které jsou založené na počtu načtených dlaždic mapy. Na druhé straně se za načtení mapového ovládacího prvku účtují služby Google Maps. V interaktivních sadách Azure Maps SDK se dlaždice map automaticky ukládají do mezipaměti, aby se snížily náklady na vývoj. Jedna Azure Mapsová transakce je vygenerována pro každých 15 dlaždic map, které jsou načteny. Interaktivní Azure Maps sady SDK používají dlaždice 512-pixel a v průměru vygenerují jednu nebo méně transakcí na zobrazení stránky.
* Často platí, že pokud chcete nahradit statické obrázky map z webových služeb Google Maps pomocí Azure Maps webové sady SDK, je jejich cena efektivnější. Sada Azure Maps Web SDK používá dlaždice mapy. Pokud uživatel neposouvá a nezvětšuje mapu, služba často generuje pouze zlomek transakce na jedno načtení mapy. Sada Azure Maps Web SDK obsahuje možnosti pro vypnutí posouvání a přiblížení (v případě potřeby). Sada SDK Azure Maps Web navíc nabízí spoustu dalších možností vizualizace než webová služba statické mapy.
* Azure Maps umožňuje ukládat data z své platformy do Azure. Data je také možné ukládat do mezipaměti jinde po dobu až šesti měsíců podle [podmínek použití](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46).

Tady je několik souvisejících prostředků pro Azure Maps:

* [Stránka s cenami Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
* [Cenová kalkulačka Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
* [Azure Maps podmínky použití](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (zahrnuté do podmínek služby Microsoft Online Services)
* [Výběr správné cenové úrovně v Azure Maps](./choose-pricing-tier.md)

## <a name="suggested-migration-plan"></a>Navrhovaný plán migrace

Následuje plán migrace na vysoké úrovni.

1. Využijte inventarizaci sad SDK a služeb Google Maps, které vaše aplikace používá. Ověřte, že Azure Maps poskytuje alternativní sady SDK a služby.
2. Pokud ho ještě nemáte, vytvořte si předplatné Azure na adrese [https://azure.com](https://azure.com) .
3. Vytvořte účet Azure Maps ([dokumentace](./how-to-manage-account-keys.md)) a ověřovací klíč nebo Azure Active Directory ([dokumentace](./how-to-manage-authentication.md)).
4. Migrujte kód aplikace.
5. Otestujte migrovaná aplikace.
6. Nasaďte migrovaná aplikace do produkčního prostředí.

## <a name="create-an-azure-maps-account"></a>Vytvoření účtu Azure Maps

Pokud chcete vytvořit účet Azure Maps a získat přístup k platformě Azure Maps, postupujte podle těchto kroků:

1. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).
2. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
3. Vytvořte [účet Azure Maps](./how-to-manage-account-keys.md). 
4. [Získejte Azure Maps klíč předplatného](./how-to-manage-authentication.md#view-authentication-details) nebo nastavte Azure Active Directory ověřování pro rozšířené zabezpečení.

## <a name="azure-maps-technical-resources"></a>Azure Maps technické prostředky

Tady je seznam užitečných technických prostředků pro Azure Maps.

- Přehled [https://azure.com/maps](https://azure.com/maps)
- Nápovědě [https://aka.ms/AzureMapsDocs](./index.yml)
- Ukázky kódu pro web SDK: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Fóra pro vývojáře: [https://aka.ms/AzureMapsForums](/answers/topics/azure-maps.html)
- Videa [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Webový [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Technický blog: [https://aka.ms/AzureMapsTechBlog](https://aka.ms/AzureMapsTechBlog)
- Azure Maps váš názor (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)
- [Azure Maps Jupyter Notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)

## <a name="migration-support"></a>Podpora migrace

Vývojáři můžou vyhledat podporu migrace prostřednictvím [fór](/answers/topics/azure-maps.html) nebo pomocí jedné z mnoha možností podpory Azure: [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nenašly se žádné prostředky, které by se vyčistily.

## <a name="next-steps"></a>Další kroky

Přečtěte si podrobnosti o migraci aplikace Google Maps pomocí těchto článků:

> [!div class="nextstepaction"]
> [Migrace webové aplikace](migrate-from-google-maps-web-app.md)
