---
title: Nejčastější dotazy
titleSuffix: Azure Cognitive Search
description: Získejte odpovědi na časté otázky týkající se služby Microsoft Azure Cognitive Search, služby vyhledávání hostované v cloudu v Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fee74cb6ec5acd5fa0f171eab9769a833f04ad66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792915"
---
# <a name="azure-cognitive-search---frequently-asked-questions-faq"></a>Azure Cognitive Search – nejčastější dotazy (nejčastější dotazy)

 Najděte odpovědi na nejčastější dotazy týkající se konceptů, kódu a scénářů souvisejících s Azure Cognitive Search.

## <a name="platform"></a>Platforma

### <a name="how-is-azure-cognitive-search-different-from-full-text-search-in-my-dbms"></a>Jak se Azure Cognitive Search liší od fulltextového vyhledávání v mém DBMS?

Azure Cognitive Search podporuje více zdrojů dat, [jazykové analýzy pro mnoho jazyků](https://docs.microsoft.com/rest/api/searchservice/language-support), vlastní [analýzy pro zajímavé a neobvyklé datové vstupy](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search), řízení pořadí vyhledávání prostřednictvím [vyhodnocování profily](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)a uživatelské prostředí funkce, jako je dopředné písmo, zvýraznění přístupů a fazetované navigace. Obsahuje také další funkce, jako jsou synonyma a syntaxe bohatého dotazu, ale obecně se nejedná o rozlišovací funkce.

### <a name="what-is-the-difference-between-azure-cognitive-search-and-elasticsearch"></a>Jaký je rozdíl mezi Azure Cognitive Search a Elasticsearch?

Při porovnávání vyhledávacích technologií se zákazníci často ptají na podrobnosti o tom, jak azure kognitivní vyhledávání porovnává s elasticsearch. Zákazníci, kteří pro své projekty vyhledávacích aplikací zvolili azure kognitivní vyhledávání přes elastické vyhledávání, to obvykle dělají, protože jsme usnadnili klíčový úkol nebo potřebují integrovanou integraci s jinými technologiemi Microsoftu:

+ Azure Cognitive Search je plně spravovaná cloudová služba s 99,9% smlouvami o úrovni služeb (SLA) při zřízení s dostatečnou redundancí (2 repliky pro přístup pro čtení, tři repliky pro čtení a zápis).
+ [Procesory microsoft Natural language](https://docs.microsoft.com/rest/api/searchservice/language-support) nabízejí špičkovou jazykovou analýzu.  
+ [Indexery Azure Cognitive Search](search-indexer-overview.md) můžou procházet různé zdroje dat Azure pro počáteční a přírůstkové indexování.
+ Pokud potřebujete rychlou reakci na výkyvy v dotazu nebo indexování svazků, můžete použít [posuvník ovládací prvky](search-manage.md#scale-up-or-down) na portálu Azure nebo spustit [skript PowerShell](search-manage-powershell.md), vynechání správu oddílů přímo.  
+ [Bodování a ladění funkce](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) poskytují prostředky pro ovlivňování skóre vyhledávání hodnosti nad rámec toho, co vyhledávač sám může poskytnout.

### <a name="can-i-pause-azure-cognitive-search-service-and-stop-billing"></a>Můžu pozastavit službu Azure Cognitive Search a zastavit fakturaci?

Službu nelze pozastavit. Výpočetní prostředky a prostředky úložiště jsou přiděleny pro výhradní použití při vytvoření služby. Není možné uvolnit a získat zpět tyto prostředky na vyžádání.

## <a name="indexing-operations"></a>Operace indexování

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>Přesunutí, zálohování a obnovení indexů nebo snímků indexu?

Během fáze vývoje můžete chtít přesunout index mezi vyhledávacími službami. Můžete například použít základní nebo bezplatnou cenovou úroveň k vývoji indexu a pak ji chcete přesunout na standardní nebo vyšší úroveň pro produkční použití. 

Nebo můžete chtít zálohovat snímek indexu na soubory, které lze později obnovit. 

Všechny tyto věci můžete provést pomocí ukázkového kódu **obnovení obnovení indexu** v tomto [ukázkovém úložiště Azure Cognitive Search .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples). 

[Definici indexu](https://docs.microsoft.com/rest/api/searchservice/get-index) můžete také získat kdykoli pomocí rozhraní REST rozhraní AZURE Cognitive Search.

V současné době neexistuje žádná integrovaná funkce extrakce indexu, snímek nebo funkce obnovení zálohování na webu Azure Portal. Zvažujeme však přidání funkce zálohování a obnovení v budoucí verzi. Chcete-li zobrazit podporu této funkce, hlasujte pro [user voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Mohu obnovit svůj index nebo službu po jeho odstranění?

Ne, pokud odstraníte index nebo službu Azure Cognitive Search, nelze ji obnovit. Když odstraníte službu Azure Cognitive Search, všechny indexy ve službě se trvale odstraní. Pokud odstraníte skupinu prostředků Azure, která obsahuje jednu nebo více služeb Azure Cognitive Search, všechny služby se trvale odstraní.  

Opětovné vytvoření prostředků, jako jsou indexy, indexery, zdroje dat a dovednosti, vyžaduje, abyste je znovu vytvořili z kódu. 

Chcete-li znovu vytvořit index, je nutné znovu indexovat data z externích zdrojů. Z tohoto důvodu se doporučuje zachovat hlavní kopii nebo zálohu původních dat v jiném úložišti dat, jako je například Azure SQL Database nebo Cosmos DB.

Jako alternativu můžete použít ukázkový kód **obnovení zálohy indexu** v tomto [úložiště ukázky Azure Cognitive Search .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples) k zálohování definice indexu a snímku indexu na řadu souborů JSON. Později můžete použít nástroj a soubory k obnovení indexu, v případě potřeby.  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexers"></a>Můžu indexovat z replik databáze SQL (Platí pro [indexátory azure SQL database)](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers)

Neexistují žádná omezení týkající se použití primárních nebo sekundárních replik jako zdroje dat při vytváření indexu od začátku. Aktualizace indexu s přírůstkovými aktualizacemi (na základě změněných záznamů) však vyžaduje primární repliku. Tento požadavek pochází z databáze SQL, která zaručuje sledování změn pouze na primární repliky. Pokud se pokusíte použít sekundární repliky pro úlohu aktualizace indexu, neexistuje žádná záruka, že získáte všechna data.

## <a name="search-operations"></a>Vyhledávací operace

### <a name="can-i-search-across-multiple-indexes"></a>Mohu vyhledávat ve více indexech?

Ne, tato operace není podporována. Hledání je vždy vymezeno na jeden index.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>Mohu omezit přístup k indexu vyhledávání podle identity uživatele?

[Filtry zabezpečení](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) můžete `search.in()` implementovat pomocí filtru. Filtr se dobře skládá se [službami správy identit, jako je Azure Active Directory (AAD),](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) aby se zkrátily výsledky hledání na základě definovaného členství ve skupině uživatelů.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Proč jsou nulové shody za podmínek, o kterých vím, že jsou platné?

Nejběžnější případ je nevědět, že každý typ dotazu podporuje různé chování vyhledávání a úrovně jazykových analýz. Fulltextové vyhledávání, což je převládající pracovní vytížení, zahrnuje fázi jazykové analýzy, která rozděluje termíny na kořenové formuláře. Tento aspekt analýzy dotazu vrhá širší síť přes možné shody, protože tokenizovaný termín odpovídá většímu počtu variant.

Zástupné, fuzzy a regulární dotazy však nejsou analyzovány jako běžné dotazy termínnebo fráze a může vést k špatné odvolání, pokud dotaz neodpovídá analyzované podobě slova v indexu vyhledávání. Další informace o analýze dotazů a analýze naleznete v tématu [architektura dotazů](https://docs.microsoft.com/azure/search/search-lucene-query-architecture).

### <a name="my-wildcard-searches-are-slow"></a>Hledání zástupných symbolů je pomalé.

Většina vyhledávacích dotazů se zástupnými symboly, jako je předpona, přibližné a regulární výrazy, jsou přepsány interně s odpovídajícími termíny v indexu vyhledávání. Toto další zpracování prohledávání indexu vyhledávání zvyšuje latenci. Kromě toho mohou být `a*` rozsáhlé vyhledávací dotazy, jako například, které budou pravděpodobně přepsány s mnoha termíny, velmi pomalé. Při hledání se zástupnými symboly provádějícími symboly zvažte definování [vlastního analyzátoru](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search).

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Proč je pořadí vyhledávání konstantní nebo stejné skóre 1,0 pro každý hit?

Ve výchozím nastavení jsou výsledky hledání hodnoceny na základě [statistických vlastností odpovídajících termínů](search-lucene-query-architecture.md#stage-4-scoring)a v sadě výsledků jsou seřazeny od nejvyšší ho diody až nízké. Některé typy dotazů (zástupný znak, předpona, regulární výraz) však vždy přispívají konstantnískóre celkové skóre dokumentu. Toto chování je záměrné. Azure Cognitive Search ukládá konstantní skóre povolit shody nalezené prostřednictvím rozšíření dotazu, které mají být zahrnuty do výsledků, aniž by to ovlivnilo pořadí.

Předpokládejme například, že vstup "tour*" ve vyhledávání zástupných symbolů vytváří shody na "tours", "tourettes" a "tourmaline". Vzhledem k povaze těchto výsledků neexistuje způsob, jak rozumně vyvodit, které podmínky jsou cennější než jiné. Z tohoto důvodu ignorujeme četnost termínů při vyhodnocování výsledků v dotazech typů zástupných, prefix a regulární výraz. Výsledky hledání založené na částečném vstupu jsou uvedeny konstantní skóre, aby se zabránilo zaujatost vůči potenciálně neočekávané shody.

## <a name="design-patterns"></a>Způsoby návrhu

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Jaký je nejlepší přístup k implementaci lokalizovaného vyhledávání?

Většina zákazníků volí vyhrazená pole před kolekcí, pokud jde o podporu různých národních prostředí (jazyků) ve stejném indexu. Pole specifická pro národní prostředí umožňují přiřadit vhodný analyzátor. Například přiřazení aplikace Microsoft French Analyzer k poli obsahujícímu francouzské řetězce. Zjednodušuje také filtrování. Pokud víte, že dotaz je iniciován na fr-fr stránce, můžete omezit výsledky hledání na toto pole. Nebo vytvořte [profil hodnocení,](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) který pole poskytne větší relativní váhu. Azure Cognitive Search podporuje více než [50 analyzátorů jazyků,](https://docs.microsoft.com/azure/search/search-language-support) ze kterých si můžete vybrat.

## <a name="next-steps"></a>Další kroky

Je vaše otázka týkající se chybějící funkce nebo funkce? Vyžádejte si tuto funkci na [webovém serveru User Voice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Viz také

 [StackOverflow: Azure kognitivní vyhledávání](https://stackoverflow.com/questions/tagged/azure-search)   
 [Jak funguje fulltextové vyhledávání ve službě Azure Cognitive Search](search-lucene-query-architecture.md)  
 [Co je kognitivní hledání Azure?](search-what-is-azure-search.md)
