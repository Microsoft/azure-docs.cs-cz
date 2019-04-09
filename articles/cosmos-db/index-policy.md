---
title: Azure Cosmos DB zásadám indexování
description: Zjistěte, jak funguje indexování ve službě Azure Cosmos DB. Zjistěte, jak konfigurovat a měnit zásady indexování pro automatické indexování a vyšší výkon.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: 6998db1679e67f8ac4bf7c81ea9373c66a9618ee
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278560"
---
# <a name="index-policy-in-azure-cosmos-db"></a>Zásady indexu ve službě Azure Cosmos DB

Můžete přepsat výchozí zásady na kontejner služby Azure Cosmos indexování nakonfigurováním následující parametry:

* **Zahrnout nebo vyloučit položky a cesty z indexu**: Můžete vyloučit nebo zahrnout konkrétní položky v indexu, při vložení nebo nahrazení položek v rámci kontejneru. Můžete také zahrnout nebo vyloučit určité cesty/vlastnosti indexovaných napříč kontejnery. Cesty může obsahovat jeden vzor zástupných znaků, například *.

* **Konfigurovat typy index**: Kromě toho rozsahu indexované cesty, můžete přidat jiné typy indexů, například prostorových.

* **Konfigurace režimů index**: Pomocí zásady indexování v kontejneru můžete nakonfigurovat různé režimy indexování například *konzistentní* nebo *žádný*.

## <a name="indexing-modes"></a>Indexování režimy

Azure Cosmos DB podporuje dva režimy indexování, které můžete konfigurovat v kontejneru Azure Cosmos prostřednictvím zásady indexování:

* **Konzistentní**: Pokud zásady kontejneru Azure Cosmos je nastavená na *konzistentní*, dotazy na konkrétní kontejner postupujte podle stejné úrovně konzistence, jako je určeno pro čtení bod (například silná, ohraničená odolnost, relace, nebo konečné). 

  Index se aktualizuje synchronně při aktualizaci položky. Například insert, replace, aktualizace a operace odstranění na položku způsobí aktualizace indexu. Konzistentní indexování podporuje konzistentních dotazů za cenu vliv na propustnost zápisu. Snížení propustnosti zápisu, závisí na "cesty zahrnuty v indexu" a "úrovně konzistence." Konzistentní indexování režim je navržen tak, abyste měli přehled o všech aktualizacích index a okamžitě poskytovat dotazy.

* **Žádný**: Kontejner, který nemá žádný index režim nemá žádný index s ním spojená. To se běžně používá, pokud je databáze Azure Cosmos použít jako úložiště klíč / hodnota a položky jsou přístupné jenom podle jejich ID vlastnosti.

  > [!NOTE]
  > Konfigurace režimu indexování jako *žádný* má vedlejší účinek vyřadit všechny stávající indexy. Tuto možnost používejte, pokud vaše vzorce přístupu vyžaduje ID nebo odkaz na sebe sama pouze.

Úrovně konzistence dotazu se udržuje podobné standardních operací čtení. Databáze Azure Cosmos vrátí chybu, když odešlete dotaz na kontejner, který má *žádný* indexování režimu. Dotazy můžete spustit jako kontroly prostřednictvím explicitního **x-ms-documentdb-enable kontroly** záhlaví v rozhraní REST API nebo **EnableScanInQuery** vyžádat možnost pomocí sady .NET SDK. Některé dotazu funkce, jako je klauzule ORDER BY není momentálně podporované s **EnableScanInQuery**, protože se stanoví odpovídající index.

## <a name="modifying-the-indexing-policy"></a>Úprava zásady indexování

Ve službě Azure Cosmos DB můžete kdykoli aktualizovat zásady indexování kontejneru. Změna zásady indexování v kontejneru Azure Cosmos může vést ke změně tvaru index. Tato změna ovlivní cesty, které můžete indexovat, jejich přesnosti a modelu konzistence indexu samotný. Změna zásady indexování efektivně vyžaduje transformaci starého indexu do nového indexu.

### <a name="index-transformations"></a>Index transformace

Všechny transformace indexu jsou prováděny online. Položek indexovaných za starou zásadu se efektivně transformují na nové zásady bez ovlivnění dostupnosti zápisu nebo ke kontejneru zřízené propustnosti. Konzistence čtení a zápisu operace, které se provádějí pomocí rozhraní REST API, SDK, nebo pomocí uložených procedur a aktivačních událostí nemá vliv při transformaci indexu.

Změna zásady indexování je asynchronní operace a čas k dokončení operace závisí na počtu položek, zřízená propustnost a velikost položek. Probíhá vytvoření nového indexu, dotaz nemusí vrátit všechny odpovídající výsledky, v případě dotazů pomocí indexu, pro který je právě upravuje a dotazy nebudou nalezeny žádné chyby nebo selhání. Probíhá vytvoření nového indexu, dotazy jsou konzistentní bez ohledu na to indexování konfiguraci režimu. Po indexu transformace se dokončí, budete i nadále konzistentní výsledky. To platí pro dotazy vydané rozhraní, jako je například rozhraní REST API, SDK, nebo uložených procedur a aktivačních událostí. Transformace indexu se provádí asynchronně, na pozadí na replikách pomocí náhradních prostředků, které jsou k dispozici pro konkrétní repliky.

Všechny transformace index probíhají na místě. Azure Cosmos DB nespravuje dvě kopie index. Žádné další místo na disku tak je vyžaduje nebo spotřebovanými v kontejnery, když dojde k transformaci indexu.

Po změně zásady indexování, změny se použijí u přesunout z původní index na nový index a primárně vycházejí indexování konfigurace režimu. Indexování konfigurace režimu hrají hlavní roli ve srovnání s další vlastnosti, jako je například zahrnutý/vyloučený cesty, index typy a přesnost.

Pokud staré a nové zásady indexování **konzistentní** indexování, databáze Azure Cosmos provede transformaci indexu online. Nelze použít jiné indexování změnu zásad, který má konzistentní indexování režimu, zatímco probíhá transformace. Při přesunu do žádné indexování režimu index se okamžitě ukončí. Přechod na hodnotu None je užitečné, když chcete zrušit transformaci v průběhu a začít pracovat s různé zásady indexování.

## <a name="modifying-the-indexing-policy---examples"></a>Úprava zásady indexování – příklady

Následující jsou nejběžnější případy použití, pokud chcete aktualizovat zásady indexování:

* Pokud chcete mít konzistentní výsledky při normálním provozu, ale vrátit zpět **žádný** indexování režimu při importu dat hromadně.

* Pokud chcete začít používat funkce indexování na aktuální kontejnery Azure Cosmos. Můžete například použít geoprostorové dotazy, které vyžaduje typ prostorového indexu nebo ORDER BY / řetězec dotazy na rozsah, které vyžadují index typu rozsah řetězec.

* Pokud chcete ručně vybrat vlastnosti, které mají indexovat a změnit v čase, chcete-li upravit pro vaše úlohy.

* Pokud chcete ladit indexování přesnosti pro zlepšení výkonu dotazů nebo ke snížení spotřebovaného úložiště.

## <a name="next-steps"></a>Další postup

Další informace o indexování v následujících článcích:

* [Indexování – přehled](index-overview.md)
* [Typy indexů](index-types.md)
* [Cesty indexů](index-paths.md)
* [Jak spravovat zásady indexování](how-to-manage-indexing-policy.md)
