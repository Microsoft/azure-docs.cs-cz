---
title: Azure Cosmos DB zásadám indexování
description: Zjistěte, jak funguje indexování ve službě Azure Cosmos DB. Zjistěte, jak konfigurovat a měnit zásady indexování pro automatické indexování a vyšší výkon.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: 2153f0a16df9e79b3f5324ce19880e2708855196
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847996"
---
# <a name="indexing-policy-in-azure-cosmos-db"></a>Zásady indexování ve službě Azure Cosmos DB

Můžete přepsat výchozí zásady na kontejner služby Azure Cosmos indexování nakonfigurováním následující parametry:

* **Zahrnout nebo vyloučit položky a cesty z indexu**: můžete vyloučit nebo zahrnout konkrétní položky v indexu při vložení nebo nahrazení položek v rámci kontejneru. Můžete také zahrnout nebo vyloučit určité cesty/vlastnosti indexovaných napříč kontejnery. Cesty může obsahovat jeden vzor zástupných znaků, například *.

* **Konfigurovat typy index**: kromě pro oblast cesty indexované, můžete přidat další typy indexy, jako prostorových.

* **Konfigurace režimů index**: pomocí zásady indexování v kontejneru můžete nakonfigurovat různé režimy indexování například *konzistentní* nebo *žádný*.

## <a name="indexing-modes"></a>Indexování režimy 

Azure Cosmos DB podporuje dva režimy indexování, které můžete konfigurovat v kontejneru Azure Cosmos. Můžete nakonfigurovat následující dva režimy indexování prostřednictvím zásady indexování: 

* **Konzistentní**: Pokud zásady kontejneru Azure Cosmos je nastavena na konzistentní, dotazy na konkrétní kontejner postupujte podle stejné úrovně konzistence, jako je určeno pro čtení bod (například silná, ohraničená odolnost, relace, nebo konečné). 

  Index se aktualizuje synchronně při aktualizaci položky. Například insert, replace, aktualizace a operace odstranění na položku způsobí aktualizace indexu. Konzistentní indexování podporuje konzistentních dotazů za cenu vliv na propustnost zápisu. Snížení propustnosti zápisu závisí "cesty součástí indexování" a "úrovně konzistence." Konzistentní indexování režimu rychle slouží k zápisu a dotazování okamžitě úlohy.

* **Žádný**: kontejner, který nemá žádný index režim nemá žádný index s ním spojená. To se běžně používá, pokud je databáze Azure Cosmos použít jako úložiště klíč / hodnota a položky jsou přístupné jenom podle jejich ID vlastnosti.

  > [!NOTE]
  > Konfigurace režimu indexování jako žádná má vedlejší účinek vyřadit všechny stávající indexy. Tuto možnost používejte, pokud vaše vzorce přístupu vyžaduje ID nebo odkaz na sebe sama pouze.

Úrovně konzistence dotazu se udržuje podobné standardních operací čtení. Databáze Azure Cosmos vrátí chybu, když odešlete dotaz na kontejner, který nemá žádná indexování režimu. Dotazy můžete spustit jako kontroly prostřednictvím explicitního **x-ms-documentdb-enable kontroly** záhlaví v rozhraní REST API nebo **EnableScanInQuery** vyžádat možnost pomocí sady .NET SDK. Některé dotazu funkce, jako je klauzule ORDER BY není momentálně podporované s **EnableScanInQuery**, protože se stanoví odpovídající index.

## <a name="modifying-the-indexing-policy"></a>Úprava zásady indexování

Ve službě Azure Cosmos DB můžete kdykoli aktualizovat zásady indexování kontejneru. Změna indexování zásady v kontejneru Azure Cosmos může vést ke změně tvaru index. Tato změna ovlivní cesty, které můžete indexovat, jejich přesnosti a modelu konzistence indexu samotný. Změna zásady indexování efektivně vyžaduje transformaci starého indexu do nového indexu.

### <a name="index-transformations"></a>Index transformace

Všechny transformace indexu jsou prováděny online. Položek indexovaných za starou zásadu se efektivně transformují na nové zásady bez ovlivnění dostupnosti zápisu nebo ke kontejneru zřízené propustnosti. Konzistence čtení a zápisu operace, které se provádějí pomocí rozhraní REST API, SDK, nebo z uložené procedury a triggery se nevztahuje při transformaci indexu.

Změna zásady indexování je asynchronní operace a čas k dokončení operace závisí na počtu položek, zřízenou propustnost a velikosti položky. Probíhá Reindexace, dotaz nemusí vrátit všechny odpovídající výsledky, v případě dotazů pomocí indexu, pro který je právě upravuje a dotazy nebudou nalezeny žádné chyby nebo selhání. Probíhá Reindexace, dotazy jsou konzistentní bez ohledu na to indexování konfiguraci režimu. Po indexu transformace se dokončí, budete i nadále konzistentní výsledky. To platí pro dotazy vydané rozhraní, jako je například rozhraní REST API, SDK, nebo uložených procedur a aktivačních událostí. Index je provedena transformace asynchronně na pozadí na replikách pomocí náhradních prostředků, které jsou k dispozici pro konkrétní repliky.

Všechny transformace index probíhají na místě. Azure Cosmos DB nespravuje dvě kopie index. Žádné další místo na disku tak je vyžaduje nebo spotřebovanými v kontejnery, když dojde k transformaci indexu.

Pokud změníte zásady indexování, změny se použijí na přesunout z původní index na nový index primárně vycházejí indexování konfigurace režimu. Indexování konfigurace režimu hrají hlavní roli ve srovnání s další vlastnosti, jako je například zahrnutý/vyloučený cesty, index typy a přesnost.

Pokud staré a nové zásady indexování **konzistentní** indexování, databáze Azure Cosmos provede transformaci indexu online. Nelze použít jiné indexování změnu zásad, který má konzistentní indexování režimu, zatímco probíhá transformace. Při přesunu do žádné indexování režimu index se okamžitě ukončí. Přechod na hodnotu None je užitečné, když chcete zrušit transformaci v průběhu a začít pracovat s různé zásady indexování.

Pro transformace index úspěšně dokončit, ujistěte se, že má kontejner dostatečně velký prostor úložiště. Pokud kontejner dosáhne kvóta úložiště, se pozastaví transformace indexu. Transformace indexu se automaticky obnoví, pokud úložiště je k dispozici místo, například když odstraníte některé položky.

## <a name="modifying-the-indexing-policy---examples"></a>Úprava zásady indexování – příklady

Následují nejběžnější případy použití, které aktualizovat zásady indexování:

* Pokud chcete mít konzistentní výsledky při normálním provozu, ale vrátit zpět k **žádný** indexování režimu při importu dat hromadně.

* Pokud chcete začít používat funkce indexování na aktuální kontejnery Azure Cosmos. Můžete například použít geoprostorové dotazy, které vyžaduje typ prostorového indexu nebo ORDER BY / řetězec dotazy na rozsah, které vyžadují index typu rozsah řetězec.

* Pokud chcete ručně vybrat vlastnosti, které mají indexovat a změnit v čase, chcete-li upravit pro vaše úlohy.

* Pokud chcete ladit indexování přesnosti pro zlepšení výkonu dotazů nebo ke snížení spotřebovaného úložiště.

## <a name="next-steps"></a>Další postup

Další informace o indexování v následujících článcích:

* [Indexování – přehled](index-overview.md)
* [Index typy](index-types.md)
* [Index cesty](index-paths.md)
* [Jak spravovat zásady indexování](how-to-manage-indexing-policy.md)
