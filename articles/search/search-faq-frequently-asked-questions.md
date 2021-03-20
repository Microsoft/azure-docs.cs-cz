---
title: Nejčastější dotazy
titleSuffix: Azure Cognitive Search
description: Získejte odpovědi na běžné otázky týkající se služby Microsoft Azure Kognitivní hledání, cloudové vyhledávací služby na Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: 9d6acdcf9487b2d1a5964d4ec686cd23666275b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88923088"
---
# <a name="azure-cognitive-search---frequently-asked-questions-faq"></a>Nejčastější dotazy k Azure Kognitivní hledáníu (FAQ)

 Získejte odpovědi na nejčastější dotazy týkající se konceptů, kódu a scénářů souvisejících se službou Azure Kognitivní hledání.

## <a name="platform"></a>Platforma

### <a name="how-is-azure-cognitive-search-different-from-full-text-search-in-my-dbms"></a>Jak se Azure Kognitivní hledání liší od fulltextového vyhledávání v mém systému DBMS?

Azure Kognitivní hledání podporuje více zdrojů dat, [jazykovou analýzu pro spoustu jazyků](/rest/api/searchservice/language-support), [vlastní analýzu zajímavých a neobvyklých datových vstupů](/rest/api/searchservice/custom-analyzers-in-azure-search), vyhledávání kontrolních mechanismů pomocí [profilů vyhodnocování](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)a funkce uživatelského prostředí, jako je typeahead, zvýrazňování přístupů a hodnocená navigace. Zahrnuje taky další funkce, jako jsou synonyma a bohatou syntaxi dotazů, ale tyto funkce obecně nejsou rozdílné.

### <a name="can-i-pause-azure-cognitive-search-service-and-stop-billing"></a>Můžu pozastavit službu Azure Kognitivní hledání a zastavit vyúčtování?

Službu nelze pozastavit. Výpočetní prostředky a prostředky úložiště se přidělují pro vaše výhradní použití při vytváření služby. Tyto prostředky není možné vydávat a znovu získat na vyžádání.

## <a name="indexing-operations"></a>Operace indexování

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>Přesunout, zálohovat a obnovit indexy nebo snímky indexu?

Během fáze vývoje můžete chtít přesunout index mezi vyhledávacími službami. Můžete například použít základní nebo bezplatnou cenovou úroveň pro vývoj indexu a pak ho přesunout do úrovně Standard nebo vyšší pro použití v produkčním prostředí. 

Nebo můžete chtít zálohovat snímek indexu do souborů, které je možné použít k pozdějšímu obnovení. 

Všechny tyto akce můžete provádět pomocí ukázkového kódu **zálohování index-Restore** v tomto [úložišti ukázkových operací Azure kognitivní hledání .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples). 

[Definici indexu](/rest/api/searchservice/get-index) můžete také kdykoli získat pomocí REST API kognitivní hledání Azure.

V tuto chvíli není v Azure Portal žádná integrovaná funkce extrakce, snímku nebo obnovení indexu. Zvažujeme ale přidání funkce zálohování a obnovení v budoucí verzi. Pokud chcete zobrazit podporu pro tuto funkci, přetypování hlasu na [uživatelský hlas](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Můžu po odstranění index nebo službu obnovit?

Ne. Pokud index nebo službu Azure Kognitivní hledání odstraníte, nebude možné ji obnovit. Když odstraníte službu Azure Kognitivní hledání, všechny indexy ve službě se trvale odstraní. Pokud odstraníte skupinu prostředků Azure, která obsahuje jednu nebo víc služeb Azure Kognitivní hledání, všechny služby se trvale odstraní.  

Opětovné vytváření prostředků, jako jsou indexy, indexery, zdroje dat a dovednosti, vyžadují, abyste je znovu vytvořili z kódu. 

Chcete-li znovu vytvořit index, je nutné znovu indexovat data z externích zdrojů. Z tohoto důvodu doporučujeme zachovat hlavní kopii nebo zálohu původních dat v jiném úložišti dat, například Azure SQL Database nebo Cosmos DB.

Alternativně můžete použít vzorový kód **index-Backup-Restore** v tomto [úložišti Azure kognitivní hledání .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples) k zálohování definice indexu a snímku indexu do řady souborů JSON. Později můžete v případě potřeby použít nástroj a soubory k obnovení indexu.  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexers"></a>Je možné indexovat z SQL Database repliky (platí pro [Azure SQL Database indexery](./search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md))

Při vytváření indexu od začátku neexistují žádná omezení použití primárních nebo sekundárních replik jako zdroje dat. Aktualizace indexu pomocí přírůstkových aktualizací (na základě změněných záznamů) ale vyžaduje primární repliku. Tento požadavek pochází z SQL Database, který garantuje sledování změn pouze u primárních replik. Pokud se pokusíte použít sekundární repliky pro úlohu aktualizace indexu, není vám zaručeno, abyste získali všechna data.

## <a name="search-operations"></a>Operace hledání

### <a name="can-i-search-across-multiple-indexes"></a>Je možné hledat v několika indexech?

Ne, tato operace není podporována. Hledání je vždy vymezeno na jeden index.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>Můžu omezit přístup k indexu vyhledávání pomocí identity uživatele?

[Filtry zabezpečení](./search-security-trimming-for-azure-search.md) můžete implementovat pomocí `search.in()` filtru. Filtr je dobře vytvořen pomocí [služeb správy identit, jako je například Azure Active Directory (AAD)](./search-security-trimming-for-azure-search-with-aad.md) , aby se výsledky hledání vyčistí podle definovaného členství ve skupině uživatelů.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Proč neexistují žádné shody s podmínkami, které je třeba mít na platnosti?

Nejběžnější případ není vědomí, že každý typ dotazu podporuje různá chování vyhledávání a úrovně lingvistických analýz. Fulltextové vyhledávání, které je převládající úlohou, zahrnuje fázi analýzy jazyka, která je rozdělena na kořenové formuláře. Tento aspekt analýzy dotazů převezme širší síť nad možnými shodami, protože pojem s tokeny odpovídá většímu počtu variant.

Výrazy se zástupnými znaky, přibližné a regulární výrazy se ale neanalyzují jako běžné podmínky nebo fráze a můžou vést k špatnému odvolání, pokud dotaz neodpovídá analyzovanému formuláři slova v indexu vyhledávání. Další informace o analýze a analýze dotazů najdete v tématu [Architektura dotazů](./search-lucene-query-architecture.md).

### <a name="my-wildcard-searches-are-slow"></a>Hledání pomocí zástupného znaku je pomalé.

Většina vyhledávacích dotazů se zástupnými znaky, jako je například předpona, přibližná a regulární výraz, se přepíší interně se shodnými podmínkami v indexu vyhledávání. Toto dodatečné zpracování prohledávání indexu vyhledávání přičítá k latenci. Další, podobně široké vyhledávací dotazy, `a*` například které je vhodné přepsat mnoha výrazy, mohou být velmi pomalé. Pro provádění zástupných hledání se zástupnými znaky zvažte definování [vlastního analyzátoru](/rest/api/searchservice/custom-analyzers-in-azure-search).

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Proč je u každé z přístupů v pořadí hledání konstantní nebo stejné skóre 1,0?

Ve výchozím nastavení se výsledky hledání vyhodnotí na základě [statistických vlastností vyhovujících podmínek](search-lucene-query-architecture.md#stage-4-scoring)a v sadě výsledků jsou seřazené na nejvyšší úrovni. Některé typy dotazů (zástupný znak, prefix, regulární výraz) ale vždy přispívat k celkovému skóre dokumentu jako konstantní skóre. Toto chování je záměrné. Azure Kognitivní hledání ukládá konstantní skóre, které umožňuje zahrnout shody nalezené prostřednictvím rozšíření dotazu do výsledků, aniž by to ovlivnilo hodnocení.

Předpokládejme například, že zadání "Tour *" v hledání se zástupnými znaky se shoduje s "prohlídky", "Tourettes" a "Tourmaline". Vzhledem k povaze těchto výsledků neexistuje způsob, jak rozumně odvodit, které výrazy jsou užitečnější než jiné. Z tohoto důvodu budeme ignorovat pojem četnosti při vyhodnocování výsledků v dotazech typů zástupného znaku, předpony a regulárního výrazu. Výsledky hledání na základě částečného vstupu mají za následek konstantní skóre, aby se předešlo posunu k potenciálně neočekávaným shodám.

## <a name="skillset-operations"></a>Operace dovednosti

### <a name="are-there-any-tips-or-tricks-to-reduce-cognitive-services-charges-on-ingestion"></a>Existují nějaké tipy nebo triky pro snížení poplatků za službu pro zpracování dat?

Je srozumitelnější, že nechcete spouštět integrované dovednosti nebo vlastní dovednosti více než je nezbytně nutné, zejména pokud pracujete s miliony dokumentů ke zpracování. V takovém případě jsme přidali možnosti "přírůstkové obohacení" do dovednosti provádění. V podstatě můžete zadat umístění mezipaměti (připojovací řetězec úložiště objektů BLOB), které se použije k uložení výstupu "zprostředkujících" kroků obohacení.  To umožňuje, aby kanál obohacení byl inteligentní a mohl použít pouze obohacení, který je nezbytný při úpravě dovednosti. Tím se přirozeně ušetří i čas indexování, protože kanál bude efektivnější.

Další informace o [přírůstkovém obohacení](cognitive-search-incremental-indexing-conceptual.md)

## <a name="design-patterns"></a>Způsoby návrhu

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Jaký je nejlepší přístup k implementaci lokalizovaného vyhledávání?

Většina zákazníků zvolí vyhrazená pole na kolekci, když přichází na podporu různých národních prostředí (jazyků) ve stejném indexu. Pole specifická pro národní prostředí umožňují přiřadit odpovídající analyzátor. Například přiřazení Microsoft francouzsky Analyzer k poli obsahujícímu francouzské řetězce. Také usnadňuje filtrování. Pokud víte, že se dotaz spouští na stránce fr-FR, můžete výsledky hledání omezit na toto pole. Případně můžete vytvořit [profil vyhodnocování](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) , aby pole poskytovalo více relativních vah. Azure Kognitivní hledání podporuje přes [analyzátory jazyka 50](./search-language-support.md) , ze kterých si můžete vybrat.

## <a name="next-steps"></a>Další kroky

Máte dotaz ohledně chybějící funkce nebo funkce? Vyžádejte si funkci na webu [User Voice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Viz také

 [StackOverflow: Azure Kognitivní hledání](https://stackoverflow.com/questions/tagged/azure-search)   
 [Jak funguje fulltextové vyhledávání ve službě Azure Cognitive Search](search-lucene-query-architecture.md)  
 [Co je Azure Cognitive Search?](search-what-is-azure-search.md)