---
title: Nejčastější dotazy (FAQ) – Azure Search
description: Získejte odpovědi na běžné dotazy týkající se služby Microsoft Azure Search, vyhledávání služby hostované v cloudu v Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/03/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 9ea4e4ec78e5613758bd9e5ff7a4fbd3273208c6
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53312812"
---
# <a name="azure-search---frequently-asked-questions-faq"></a>Azure Search – nejčastější dotazy (FAQ)

 Najděte odpovědi na nejčastější dotazy o konceptech, kód a scénáře týkající se Azure Search.

## <a name="platform"></a>Platforma

### <a name="how-is-azure-search-different-from-full-text-search-in-my-dbms"></a>Čím se liší od fulltextové vyhledávání v mé DBMS Azure Search?

Služba Azure Search podporuje více zdrojů dat, [pro řadu jazyků pro jazykovou analýzu](https://docs.microsoft.com/rest/api/searchservice/language-support), [vlastní analýza datových výstupů zajímavé a neobvyklé](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search), Hledat pořadí ovládacích prvků prostřednictvím [bodování profily](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)a funkce činnost koncového uživatele, jako je typeahead, zvýrazňování shod a fasetová navigace. Zahrnuje také další funkce, jako je například synonym a bohaté možnosti dotazů syntaxe, ale ty nejsou obecně odlišení těchto funkcí.

### <a name="what-is-the-difference-between-azure-search-and-elasticsearch"></a>Jaký je rozdíl mezi Azure Search a Elasticsearch?

Při porovnávání vyhledávací technologie, zákazníci často požádat o specifikách různých na tom, jak si stojí Azure Search pomocí Elasticsearch. Zákazníci, kteří se rozhodnou Azure Search v Elasticsearch pro jejich hledání projektů aplikace obvykle provést, protože My jsme usnadnili klíčové úlohy nebo které potřebují integrovanou integrace s dalšími technologiemi Microsoftu:

+ Služba Azure Search je plně spravovaná Cloudová služba s 99,9 % Smlouvy o úrovni služeb (SLA), když zřízené dostatečná redundance (2 repliky pro čtení, 3 repliky pro čtení a zápis).
+ Společnosti Microsoft [přirozeného jazyka procesorů](https://docs.microsoft.com/rest/api/searchservice/language-support) nabízejí špičkových pro jazykovou analýzu.  
+ [Azure Search indexery](search-indexer-overview.md) může procházet celou řadu zdrojů dat Azure pro počáteční a přírůstkové indexování.
+ Pokud potřebujete rychlou odpověď na kolísání v dotazu nebo indexování svazků, můžete použít [ovládacích prvků posuvník](search-manage.md#scale-up-or-down) v Azure portal nebo spuštění [skript prostředí PowerShell](search-manage-powershell.md), přímo bez použití správy horizontálních oddílů.  
+ [Vyhodnocení funkce a optimalizace](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) poskytuje prostředky pro vliv na vyhledávání pořadí skóre nad rámec co může poskytnout pouze vyhledávací web.

### <a name="can-i-pause-azure-search-service-and-stop-billing"></a>Můžete pozastavit službu Azure Search a zastavit fakturaci?

Službu nelze pozastavit. Úložiště a výpočetní prostředky se přidělují pro výhradní použití při vytváření služby. Není možné uvolnit a získat tyto prostředky na vyžádání.

## <a name="indexing-operations"></a>Operace indexování

### <a name="backup-and-restore-or-download-and-move-indexes-or-index-snapshots"></a>Indexy zálohování a obnovení (nebo ke stažení a přesunutí) nebo snímky index?

Můžete sice [získat definici indexu](https://docs.microsoft.com/rest/api/searchservice/get-index) kdykoli, neexistuje žádný index extrakce, snímku nebo funkci obnovení zálohování pro stahování *vyplní* index do místního systému, které běží v cloudu nebo přesunutím do jiné služby Azure Search.

Indexy jsou vytvořena a naplněna kód, který napíšete a spustit pouze v Azure Search v cloudu. Obvykle zákazníky, kteří chtějí přesunout do jiné služby index udělat tak, že upravíte jejich kód pro použití nového koncového bodu a pak znovu spusťte indexování. Pokud chcete mít možnost pro vytvoření snímku nebo zálohování indexu, přetypování hlas [User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Můžete po odstranění obnovit své index nebo služby?

Ne, nemůžete obnovit indexy nebo služby. Při odstranění indexu Azure Search, operace je konečné, prostředek index nelze obnovit. Při odstranění služby Azure Search se trvale odstraní všechny indexy ve službě. Navíc pokud odstraníte skupinu prostředků Azure, která obsahuje jednu nebo více služeb Azure Search, všechny služby se trvale odstraní.  

Obnovení prostředky, jako jsou indexy, indexery, zdroje dat a dovednosti vyžaduje, můžete obnovit z kódu. V případě indexů musí přeindexování data z externích zdrojů. Z tohoto důvodu důrazně doporučujeme, abyste zachovali hlavní kopie nebo zálohu pro původní data v úložišti dat, jako je například Azure SQL Database nebo Cosmos DB.

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexershttpsdocsmicrosoftcomazuresearchsearch-howto-connecting-azure-sql-database-to-azure-search-using-indexers"></a>Můžete mi indexu z repliky databáze SQL (platí pro [indexování Azure SQL Database](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers))

Neexistují žádná omezení týkající se použití primární nebo sekundární repliky jako zdroj dat, při vytváření indexu úplně od začátku. Aktualizace indexu s přírůstkovou aktualizací (podle změněné záznamy) ale vyžaduje primární repliku. Tento požadavek pochází z SQL Database, která garantuje změňte sledování na pouze primární repliky. Pokud se pokusíte pomocí sekundární repliky úloh aktualizace indexu, není zaručeno, které získáte všechna data.

## <a name="search-operations"></a>Operace hledání

### <a name="can-i-search-across-multiple-indexes"></a>Můžete prohledávat více indexů?

Ne, tato operace není podporována. Hledání je vždy s rozsahem jednoho indexu.

### <a name="can-i-restrict-search-corpus-access-by-user-identity"></a>Můžete omezit přístup souhrnu hledání podle identity uživatele?

Můžete implementovat [filtry zabezpečení](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) s `search.in()` filtru. Filtr lze kombinovat s [služby správy identit, jako je Azure Active prodloužil platnost](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) oříznout výsledky hledání na základě definované členství ve skupině uživatelů.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Proč existují nula odpovídá na podmínky, které znáte, platit?

Nejběžnější případ není vědět, že každý typ dotazu podporuje různé vyhledávací chování a úrovně lingvistické analýzy. Fulltextové vyhledávání, což je převládající pracovního vytížení, zahrnuje fázi analýzy jazyka, který porušuje podmínky dolů kořenové formuláře. Tento aspekt analýze dotazu širší net přetypování nad možných shod, protože tokenizovaná termín shoduje s větší množství variant.

Zástupný znak, přibližných shod a dotazy na regulární výraz, ale neanalyzují jako regulární dotazy termín nebo frázi a může způsobit špatné odvolání, pokud dotaz se neshoduje s analyzované formě slovo v indexu vyhledávání. Další informace o analýze dotazu a analýzy, najdete v tématu [dotazování architektura](https://docs.microsoft.com/azure/search/search-lucene-query-architecture).

### <a name="my-wildcard-searches-are-slow"></a>Vyhledávání se zástupnými znaky jsou pomalé.

Většina zástupný znak vyhledávacích dotazů, stejně jako předpona přibližných shod a regulárního výrazu, jsou zapsány interně s odpovídajícími podmínky v indexu vyhledávání. Tato další zpracování skenování indexu vyhledávání přidá na latenci. Další různé vyhledávací dotazy, jako je třeba `a*` například, která jsou tak pravděpodobnou příčinou, aby se povolilo mnoho podmínek může být velmi pomalé. Výkonné vyhledávání pomocí zástupných znaků, zvažte možnost definice [vlastní analyzátor](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search).

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Proč je pořadí hledání konstantní nebo rovna skóre 1.0 pro každý položek?

Ve výchozím nastavení, jsou hodnocené podle výsledků hledání [statistické vlastnosti odpovídající podmínky](search-lucene-query-architecture.md#stage-4-scoring)a seřazené sady nejvyšších po nejnižší ve výsledku. Nicméně některé typy (zástupný znak, předpony, regulární výraz) dotazů vždy měli přispívat konstantní score celkové skóre dokumentu. Toto chování je záměrné. Azure Search vynucuje konstantní score umožňující nalezených položek prostřednictvím rozšíření dotazu mají být zahrnuty ve výsledcích, aniž by to ovlivnilo hodnocení.

Předpokládejme například, že vytváří odpovídajících položek na "prohlídky", "tourettes" a "tourmaline" vstup "tour *" v hledání pomocí zástupných znaků. Vzhledem k povaze tyto výsledky, neexistuje žádný způsob, jak rozumně odvodit podmínky, které jsou větší hodnotu než jiné. Z tohoto důvodu jsme ignorovat termín frekvence, při vyhodnocování výsledky v dotazech typy zástupných znaků, předpony a regulární výraz. Na základě částečné zadání výsledky hledání jsou uvedeny konstantní score, aby Posun směrem k potenciálně neočekávané shody.

## <a name="design-patterns"></a>Způsoby návrhu

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Co je nejlepším řešením pro vyhledávání lokalizovaných?

Většina zákazníků zvolte vyhrazených polí v kolekci, pokud jde o podporuje různá národní prostředí (jazyky) ve stejném indexu. Pole specifické pro národní prostředí umožňují přiřadit odpovídající analyzátor. Například přiřazování analyzátoru francouzštiny Microsoft pole obsahující francouzské řetězce. Taky usnadňují filtrování. Pokud víte, že dotaz je zahájeno na stránce fr-fr, můžete omezit rozsah výsledků vyhledávání na toto pole. Nebo vytvořte [bodovací profil](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) poskytnout další relativní váhu pole. Služba Azure Search podporuje víc než [50 jazykové analyzátory](https://docs.microsoft.com/azure/search/search-language-support) lze vybírat.

## <a name="next-steps"></a>Další postup

Je vaše otázky týkající se chybějící funkce nebo funkce? Na žádost o funkci [webu User Voice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Další informace najdete v tématech

 [StackOverflow: Služba Azure Search](https://stackoverflow.com/questions/tagged/azure-search)   
 [Jak funguje fulltextové vyhledávání ve službě Azure Search](search-lucene-query-architecture.md)  
 [Co je Azure Search?](search-what-is-azure-search.md)
