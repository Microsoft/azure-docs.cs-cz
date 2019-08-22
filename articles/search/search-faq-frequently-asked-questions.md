---
title: Nejčastější dotazy – Azure Search
description: Získejte odpovědi na běžné otázky týkající se Microsoft Azure Search Service, cloudové vyhledávací služby na Microsoft Azure.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/03/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d4aae2f2ef9ccbc645647125682d999c11c99ab6
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649834"
---
# <a name="azure-search---frequently-asked-questions-faq"></a>Nejčastější dotazy k Azure Search (FAQ)

 Získejte odpovědi na nejčastější dotazy týkající se konceptů, kódu a scénářů souvisejících s Azure Search.

## <a name="platform"></a>Platforma

### <a name="how-is-azure-search-different-from-full-text-search-in-my-dbms"></a>Jak se Azure Search liší od fulltextového vyhledávání v mém systému DBMS?

Azure Search podporuje více zdrojů dat, [jazykovou analýzu pro řadu jazyků](https://docs.microsoft.com/rest/api/searchservice/language-support), [vlastní analýzu zajímavých a neobvyklých datových vstupů](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search), hledání kontrolních mechanismů pomocí [profilů vyhodnocování](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)a funkcí uživatelského prostředí, jako je například typeahead, zvýrazňování přístupů a omezující navigace. Zahrnuje taky další funkce, jako jsou synonyma a bohatou syntaxi dotazů, ale tyto funkce obecně nejsou rozdílné.

### <a name="what-is-the-difference-between-azure-search-and-elasticsearch"></a>Jaký je rozdíl mezi Azure Search a Elasticsearch?

Při porovnávání vyhledávacích technologií si zákazníci často žádají o konkrétní informace o tom, jak Azure Search porovnává s Elasticsearch. Zákazníci, kteří si vyberou Azure Search přes Elasticsearch pro své projekty vyhledávací aplikace, obvykle tak, že jsme vytvořili klíčovou úlohu, která je jednodušší nebo potřebují integrovanou integraci s dalšími technologiemi Microsoftu:

+ Azure Search je plně spravovaná cloudová služba s 99,9% smlouvou o úrovni služeb (SLA), když je zajištěna dostatečná redundance (2 repliky pro přístup pro čtení, 3 repliky pro čtení a zápis).
+ Procesory [přirozeného jazyka](https://docs.microsoft.com/rest/api/searchservice/language-support) Microsoftu nabízejí špičkovou jazykovou analýzu.  
+ [Azure Search indexery](search-indexer-overview.md) můžou procházet nejrůznější zdroje dat Azure pro počáteční a přírůstkové indexování.
+ Pokud potřebujete rychlou odezvu na kolísání v dotazech nebo při indexování svazků, můžete použít [ovládací prvky posuvníku](search-manage.md#scale-up-or-down) v Azure Portal nebo spustit [skript PowerShellu](search-manage-powershell.md)a obejít správu horizontálních oddílů přímo.  
+ [Funkce bodování a optimalizace](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) poskytují prostředky pro vliv na výsledky hledání, a to nad rámec toho, co samotný vyhledávací stroj může poskytnout.

### <a name="can-i-pause-azure-search-service-and-stop-billing"></a>Můžu pozastavit službu Azure Search a zastavit účtování?

Službu nelze pozastavit. Výpočetní prostředky a prostředky úložiště se přidělují pro vaše výhradní použití při vytváření služby. Tyto prostředky není možné vydávat a znovu získat na vyžádání.

## <a name="indexing-operations"></a>Operace indexování

### <a name="backup-and-restore-or-download-and-move-indexes-or-index-snapshots"></a>Zálohovat a obnovovat (nebo stahovat a přesouvat) indexy nebo snímky indexu?

I když můžete [získat definici indexu](https://docs.microsoft.com/rest/api/searchservice/get-index) kdykoli, není k dispozici žádná funkce extrakce, snímku nebo obnovení indexu pro stažení naplněné indexu spuštěného v cloudu do místního systému nebo jeho přesun do jiné služby Azure Search.

Indexy jsou sestaveny a vyplněny z kódu, který zapisujete a spouštíte pouze v Azure Search v cloudu. Zákazníci, kteří chtějí přesunout index do jiné služby, to obvykle udělá úpravou kódu, aby používali nový koncový bod, a pak znovu spustí indexování. Pokud chcete mít možnost pořídit snímek nebo zálohovat index, přetypujte hlas na [uživatelský hlas](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Můžu po odstranění index nebo službu obnovit?

Ne, indexy nebo služby nemůžete obnovit. Odstraníte-li index Azure Search, operace je finální a index nelze obnovit. Když odstraníte Azure Search službu, všechny indexy ve službě se trvale odstraní. Pokud odstraníte skupinu prostředků Azure, která obsahuje jednu nebo více služeb Azure Search, všechny služby se trvale odstraní.  

Obnovení prostředků, jako jsou indexy, indexery, zdroje dat a dovednosti, vyžaduje, abyste je znovu vytvořili z kódu. V případě indexů je nutné znovu indexovat data z externích zdrojů. Z tohoto důvodu se důrazně doporučuje, abyste zachovali hlavní kopii nebo zálohu původních dat v jiném úložišti dat, například Azure SQL Database nebo Cosmos DB.

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexershttpsdocsmicrosoftcomazuresearchsearch-howto-connecting-azure-sql-database-to-azure-search-using-indexers"></a>Je možné indexovat z replik služby SQL Database (platí pro [Azure SQL Database indexery](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers))

Při vytváření indexu od začátku neexistují žádná omezení použití primárních nebo sekundárních replik jako zdroje dat. Aktualizace indexu pomocí přírůstkových aktualizací (na základě změněných záznamů) ale vyžaduje primární repliku. Tento požadavek pochází z SQL Database, který garantuje sledování změn pouze u primárních replik. Pokud se pokusíte použít sekundární repliky pro úlohu aktualizace indexu, není vám zaručeno, abyste získali všechna data.

## <a name="search-operations"></a>Operace hledání

### <a name="can-i-search-across-multiple-indexes"></a>Je možné hledat v několika indexech?

Ne, tato operace není podporována. Hledání je vždy vymezeno na jeden index.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>Můžu omezit přístup k indexu vyhledávání pomocí identity uživatele?

[Filtry zabezpečení](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) můžete implementovat pomocí `search.in()` filtru. Filtr je dobře vytvořen pomocí [služeb správy identit, jako je například Azure Active Directory (AAD)](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) , aby se výsledky hledání vyčistí podle definovaného členství ve skupině uživatelů.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Proč neexistují žádné shody s podmínkami, které je třeba mít na platnosti?

Nejběžnější případ není vědomí, že každý typ dotazu podporuje různá chování vyhledávání a úrovně lingvistických analýz. Fulltextové vyhledávání, což je převládající úloha, zahrnuje fázi analýzy jazyka, která je rozdělena na kořenové formuláře. Tento aspekt analýzy dotazů převezme širší síť nad možnými shodami, protože pojem s tokeny odpovídá většímu počtu variant.

Výrazy se zástupnými znaky, přibližné a regulární výrazy se ale neanalyzují jako běžné podmínky nebo fráze a můžou vést k špatnému odvolání, pokud dotaz neodpovídá analyzovanému formuláři slova v indexu vyhledávání. Další informace o analýze a analýze dotazů najdete v tématu [Architektura dotazů](https://docs.microsoft.com/azure/search/search-lucene-query-architecture).

### <a name="my-wildcard-searches-are-slow"></a>Hledání pomocí zástupného znaku je pomalé.

Většina vyhledávacích dotazů se zástupnými znaky, jako je například předpona, přibližná a regulární výraz, se přepíší interně se shodnými podmínkami v indexu vyhledávání. Toto dodatečné zpracování prohledávání indexu vyhledávání přičítá k latenci. Další, podobně `a*` široké vyhledávací dotazy, například které je vhodné přepsat mnoha výrazy, mohou být velmi pomalé. Pro provádění zástupných hledání se zástupnými znaky zvažte definování [vlastního analyzátoru](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search).

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Proč je u každé z přístupů v pořadí hledání konstantní nebo stejné skóre 1,0?

Ve výchozím nastavení se výsledky hledání vyhodnotí na základě [statistických vlastností vyhovujících podmínek](search-lucene-query-architecture.md#stage-4-scoring)a v sadě výsledků jsou seřazené na nejvyšší úrovni. Některé typy dotazů (zástupný znak, prefix, regulární výraz) ale vždy přispívat k celkovému skóre dokumentu jako konstantní skóre. Toto chování je záměrné. Azure Search ukládá konstantní skóre, které umožňuje, aby se shody nalezené prostřednictvím rozšíření dotazu zahrnuly do výsledků, aniž by to ovlivnilo hodnocení.

Předpokládejme například, že zadání "Tour *" v hledání se zástupnými znaky se shoduje s "prohlídky", "Tourettes" a "Tourmaline". Vzhledem k povaze těchto výsledků neexistuje způsob, jak rozumně odvodit, které výrazy jsou užitečnější než jiné. Z tohoto důvodu budeme ignorovat pojem četnosti při vyhodnocování výsledků v dotazech typů zástupného znaku, předpony a regulárního výrazu. Výsledky hledání na základě částečného vstupu mají za následek konstantní skóre, aby se předešlo posunu k potenciálně neočekávaným shodám.

## <a name="design-patterns"></a>Způsoby návrhu

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Jaký je nejlepší přístup k implementaci lokalizovaného vyhledávání?

Většina zákazníků zvolí vyhrazená pole na kolekci, když přichází na podporu různých národních prostředí (jazyků) ve stejném indexu. Pole specifická pro národní prostředí umožňují přiřadit odpovídající analyzátor. Například přiřazení Microsoft francouzsky Analyzer k poli obsahujícímu francouzské řetězce. Také usnadňuje filtrování. Pokud víte, že se dotaz spouští na stránce fr-FR, můžete výsledky hledání omezit na toto pole. Případně můžete vytvořit [profil vyhodnocování](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) , aby pole poskytovalo více relativních vah. Azure Search podporuje [analyzátory jazyka 50](https://docs.microsoft.com/azure/search/search-language-support) , ze kterých si můžete vybrat.

## <a name="next-steps"></a>Další postup

Máte dotaz ohledně chybějící funkce nebo funkce? Vyžádejte si funkci na webu [User Voice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Viz také:

 [StackOverflow Azure Search](https://stackoverflow.com/questions/tagged/azure-search)   
 [Jak funguje úplné hledání textu v Azure Search](search-lucene-query-architecture.md)  
 [Co je Azure Search?](search-what-is-azure-search.md)
