---
title: Import dat do indexu vyhledávání pomocí portálu Azure Portal
titleSuffix: Azure Cognitive Search
description: Zjistěte, jak pomocí Průvodce importem dat na webu Azure Portal procházet data Azure z Cosmos DB, úložiště objektů blob, úložiště tabulek, DATABÁZE SQL a SQL Serveru na virtuálních počítačích Azure.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0ed2bd7f1e03d8d5fa11f7e76010d087605f0fe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460694"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>Průvodce importem dat pro Azure Cognitive Search

Portál Azure poskytuje průvodce **importem dat** na řídicím panelu Azure Cognitive Search pro vytváření prototypů a načítání indexu. Tento článek popisuje výhody a omezení použití průvodce, vstupy a výstupy a některé informace o použití. Praktické pokyny při procházení průvodce pomocí předdefinovaných ukázkových dat najdete v [tématu Vytvoření indexu Azure Cognitive Search pomocí rychlého startu portálu Azure.](search-get-started-portal.md)

Mezi operace, které tento průvodce provádí, patří:

1 – Připojení k podporovanému zdroji dat Azure.

2 - Vytvořte schéma indexu, odvozené vzorkováním zdrojových dat.

3 - Volitelně přidejte obohacení AI k extrahování nebo generování obsahu a struktury.

4 - Spusťte průvodce pro vytváření objektů, import dat, nastavení plánu a dalších možností konfigurace.

Průvodce vyveze několik objektů, které jsou uloženy do vyhledávací služby, ke kterým můžete přistupovat programově nebo v jiných nástrojích.

## <a name="advantages-and-limitations"></a>Výhody a omezení

Před napsáním libovolného kódu můžete použít průvodce pro vytváření prototypů a testování proof-of-concept. Průvodce se připojí k externím zdrojům dat, navzorkuje data k vytvoření počátečního indexu a potom importuje data jako dokumenty JSON do indexu v Azure Cognitive Search. 

Vzorkování je proces, při kterém je odvozeno schéma indexu a má určitá omezení. Po vytvoření zdroje dat průvodce vybere ukázku dokumentů, která rozhodne, které sloupce jsou součástí zdroje dat. Ne všechny soubory jsou čteny, protože to může potenciálně trvat hodiny pro velmi velké zdroje dat. Vzhledem k výběru dokumentů, zdrojová metadata, například název pole nebo typ, se používá k vytvoření kolekce polí ve schématu indexu. V závislosti na složitosti zdrojových dat může být nutné upravit počáteční schéma pro přesnost nebo rozšířit jeho úplnost. Změny můžete provést vřádku na stránce definice indexu.

Celkově jsou výhody použití průvodce jasné: pokud jsou splněny požadavky, můžete prototypovat dotazovatelný index během několika minut. Některé složitosti indexování, jako je například poskytování dat jako dokumenty JSON, jsou zpracovány průvodcem.

Známá omezení jsou shrnuta takto:

+ Průvodce nepodporuje iteraci nebo opakované použití. Každý průchod průvodcem vytvoří novou konfiguraci indexu, sady dovedností a indexeru. V průvodci lze zachovat a znovu použít pouze zdroje dat. Chcete-li upravit nebo upřesnit jiné objekty, je nutné použít rozhraní REST API nebo .NET SDK k načtení a úpravě struktur.

+ Zdrojový obsah musí být umístěn v podporovaném zdroji dat Azure.

+ Vzorkování je přes podmnožinu zdrojových dat. U velkých zdrojů dat je možné, že průvodce pole přehlédne. Možná budete muset rozšířit schéma nebo opravit odvozené datové typy, pokud vzorkování je nedostatečná.

+ Obohacení umělou ai, jak je vystaveno na portálu, je omezeno na několik vestavěných dovedností. 

+ [Úložiště znalostí](knowledge-store-concept-intro.md), které může vytvořit průvodce, je omezeno na několik výchozích projekcí. Pokud chcete uložit obohacené dokumenty vytvořené průvodcem, kontejner objektů blob a tabulky jsou dodávány s výchozími názvy a strukturou.

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>Vstup zdroje dat

Průvodce **importem dat** se připojí k externímu zdroji dat pomocí interní logiky poskytované indexery Azure Cognitive Search, které jsou vybaveny k ukázkovému zdroji, čtení metadat, bezva dokumentů pro čtení obsahu a struktury a serializaci obsahu jako JSON pro následný import do Azure Cognitive Search.

Importovat lze pouze z jedné tabulky, zobrazení databáze nebo ekvivalentní datové struktury, ale struktura může obsahovat hierarchické nebo vnořené dílčí struktury. Další informace naleznete v [tématu Jak modelovat složité typy](search-howto-complex-data-types.md).

Před spuštěním průvodce byste měli vytvořit tuto jednu tabulku nebo zobrazení a musí obsahovat obsah. Ze zřejmých důvodů nemá smysl spouštět Průvodce **importem dat** na prázdný zdroj dat.

|  Výběr | Popis |
| ---------- | ----------- |
| **Stávající zdroj dat** |Pokud již máte indexery definované ve vyhledávací službě, můžete mít existující definici zdroje dat, kterou můžete znovu použít. V Azure Cognitive Search objekty zdroje dat se používají pouze indexery. Objekt zdroje dat můžete vytvořit programově nebo pomocí Průvodce **importem dat** a podle potřeby je znovu použít.|
| **ukázky**| Azure Cognitive Search poskytuje dva předdefinované ukázkové zdroje dat, které se používají v kurzech a rychlých startech: databáze SQL s nemovitostmi a databáze Hotels hostovaná v Cosmos DB. Projděte si na základě ukázky Hotely najdete v tématu Vytvoření indexu na rychlém startu [portálu Azure.](search-get-started-portal.md) |
| [**Azure SQL Database**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |Název služby, přihlašovací údaje uživatele s oprávněním ke čtení a název databáze můžete zadat na této stránce nebo přes připojovací řetězec technologie ADO.NET. Chcete-li zobrazit nebo přizpůsobit vlastnosti, zvolte možnost připojovacího řetězce. <br/><br/>Na této stránce je třeba určit tabulku nebo zobrazení poskytující sadu řádků. Tato možnost se zobrazí po úspěšném připojení v podobě rozevíracího seznamu, ze kterého můžete vybírat.|
| **SQL Server na virtuálním počítači Azure** |Zadejte plně kvalifikovaný název služby, ID uživatele a heslo a databázi jako připojovací řetězec. Abyste mohli použít tento zdroj dat, je třeba mít v místním úložišti dříve nainstalovaný certifikát šifrující připojení. Pokyny najdete v tématu [připojení virtuálního počítače SQL k Azure Cognitive Search](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>Na této stránce je třeba určit tabulku nebo zobrazení poskytující sadu řádků. Tato možnost se zobrazí po úspěšném připojení v podobě rozevíracího seznamu, ze kterého můžete vybírat. |
| [**Azure Cosmos DB**](search-howto-index-cosmosdb.md)|Požadavky zahrnují účet, databázi a kolekci. Všechny dokumenty v kolekci budou zahrnuty v indexu. Můžete definovat dotaz pro sloučení nebo filtrování sady řádků nebo ponechat dotaz prázdný. V tomto průvodci není vyžadován dotaz.|
| [**Úložiště objektů blob Azure**](search-howto-indexing-azure-blob-storage.md) |Požadavky zahrnují účet úložiště a kontejner. Pokud se názvy objektů blob řídí zásadami virtuálního pojmenovávání pro účely seskupování, můžete volitelně zadat část názvu obsahující virtuální adresář jako složku v kontejneru. Další informace najdete v tématu [Indexování služby Blob Storage](search-howto-indexing-azure-blob-storage.md). |
| [**Azure Table Storage**](search-howto-indexing-azure-tables.md) |Požadavky zahrnují účet úložiště a název tabulky. Volitelně můžete zadat dotaz pro načtení podmnožiny tabulek. Další informace najdete v tématu [Indexování služby Table Storage](search-howto-indexing-azure-tables.md). |

## <a name="wizard-output"></a>Výstup průvodce

Na pozadí průvodce vytvoří, nakonfiguruje a vyvolá následující objekty. Po spuštění průvodce najdete jeho výstup na stránkách portálu. Stránka Přehled služby obsahuje seznamy indexů, indexerů, zdrojů dat a dovedností. Definice indexu lze zobrazit v plném JSON na portálu. Pro jiné definice můžete použít [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/) získat konkrétní objekty.

| Objekt | Popis | 
|--------|-------------|
| [Zdroj dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Zachová informace o připojení ke zdrojovým datům, včetně pověření. Objekt zdroje dat se používá výhradně s indexery. | 
| [Index](https://docs.microsoft.com/rest/api/searchservice/create-index) | Fyzická datová struktura používaná pro fulltextové vyhledávání a další dotazy. | 
| [Skillset](https://docs.microsoft.com/rest/api/searchservice/create-skillset) | Kompletní sada pokynů pro manipulaci, transformaci a tvarování obsahu, včetně analýzy a extrahování informací z obrazových souborů. S výjimkou velmi jednoduché a omezené struktury obsahuje odkaz na prostředek služby Cognitive Services, který poskytuje obohacení. Volitelně může také obsahovat definici úložiště znalostí.  | 
| [Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer)  | Objekt konfigurace určující zdroj dat, cílový index, volitelný skillset, volitelný plán a volitelná nastavení konfigurace pro chybové předání a kódování base-64. |


## <a name="how-to-start-the-wizard"></a>Jak spustit průvodce

Průvodce importem dat je spuštěn z panelu příkazů na stránce Přehled služby.

1. Na [webu Azure Portal](https://portal.azure.com)otevřete stránku vyhledávací služby z řídicího panelu nebo [vyhledejte službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v seznamu služeb.

2. Na stránce přehled služby v horní části klikněte na **Importovat data**.

   ![Příkaz Importovat data na portálu](./media/search-import-data-portal/import-data-cmd2.png "Spuštění Průvodce importem dat")

Můžete také spustit **import dat** z jiných služeb Azure, včetně Azure Cosmos DB, Azure SQL Database a úložiště objektů blob Azure. Vyhledejte **přidat Azure Cognitive Search** v levém navigačním podokně na stránce s přehledem služby.

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>Úprava nebo dokončení schématu indexu v průvodci

Průvodce vygeneruje neúplný index, který bude naplněn dokumenty získanými ze vstupního zdroje dat. Pro funkční index, ujistěte se, že máte následující prvky definované.

1. Je seznam polí dokončen? Přidejte nová pole, která vzorkování zmeškané a odeberte všechny, které nepřidávají hodnotu do prostředí vyhledávání nebo které nebudou použity ve [výrazu filtru](search-query-odata-filter.md) nebo [v ykórování profilu](index-add-scoring-profiles.md).

1. Je datový typ vhodný pro příchozí data? Azure Cognitive Search podporuje [datové typy datového modelu entity (EDM).](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) Pro data Azure SQL je [mapování grafu,](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping) který stanoví ekvivalentní hodnoty. Další informace naleznete v [tématu Mapování polí a transformace](search-indexer-field-mappings.md).

1. Máte jedno pole, které může sloužit jako *klíč?* Toto pole musí být Soubor Emm.string a musí jednoznačně identifikovat dokument. U relačních dat může být mapována na primární klíč. Pro objekty BLOB může `metadata-storage-path`být . Pokud hodnoty polí obsahují mezery nebo pomlčky, musíte nastavit volbu **Kódovací klíč Base-64** v kroku **Vytvořit indexer** v části **Upřesnit možnosti**, abyste potlačili kontrolu ověření těchto znaků.

1. Nastavte atributy k určení, jak se toto pole používá v indexu. 

   Udělejte si čas s tímto krokem, protože atributy určují fyzické vyjádření polí v indexu. Pokud chcete změnit atributy později, i programově, budete téměř vždy muset přetáhnout a znovu sestavit index. Základní atributy, jako je **searchable** a **Retrievable** mají [zanedbatelný dopad na úložiště](search-what-is-an-index.md#index-size). Povolení filtrů a použití návrhovců zvyšuje požadavky na úložiště. 
   
   + **Prohledávatelné** umožňuje fulltextové vyhledávání. Každé pole používané v dotazech ve volném tvaru nebo ve výrazech dotazu musí mít tento atribut. Invertované indexy jsou vytvořeny pro každé pole, které označíte jako **prohledávatelné**.

   + **Vrátí pole** ve výsledcích hledání. Každé pole, které poskytuje obsah pro výsledky hledání, musí mít tento atribut. Nastavení tohoto pole nemá znatelně vliv na velikost indexu.

   + **Filtrovatelné** umožňuje odkazovat na pole ve výrazech filtru. Každé pole použité ve **$filter** výrazu musí mít tento atribut. Výrazy filtru jsou určeny pro přesné shody. Vzhledem k tomu, že textové řetězce zůstávají beze změny, je nutné další úložiště, aby se přizpůsobilo doslovný obsah.

   + **Facetable** umožňuje pole pro fazetované navigace. Pouze pole označená jako **filtrovatelná** mohou být označena jako **plocha**.

   + **Seřaditelné** umožňuje použití pole v řazení. Každé pole použité ve **$Orderby** výrazu musí mít tento atribut.

1. Potřebujete [lexikální analýzu?](search-lucene-query-architecture.md#stage-2-lexical-analysis) Pro pole Edm.string, která lze **prohledávat**, můžete nastavit **analyzátor,** pokud chcete indexování a dotazování s rozšířeným jazykem. 

   Výchozí hodnota je *Standardní Lucene,* ale můžete zvolit *microsoft angličtinu,* pokud jste chtěli použít analyzátor společnosti Microsoft pro pokročilé lexikální zpracování, jako je například řešení nepravidelných tvarů snažení a sloves. Na portálu lze zadat pouze analyzátory jazyka. Použití vlastního analyzátoru nebo nejazykového analyzátoru, jako je klíčové slovo, vzorek a tak dále, musí být provedeno programově. Další informace o analyzátorech naleznete [v tématu Přidání analyzátorů jazyků](search-language-support.md).

1. Potřebujete funkci dopředného psaní ve formě automatického dokončování nebo navrhovaných výsledků? Zaškrtnutím políčka **Sugester** povolíte [návrhy dotazů dopředného psaní a automatické dokončování](index-add-suggesters.md) u vybraných polí. Návrhy přidat k počtu tokenizovaných termínů v indexu, a tím spotřebovávají více úložiště.


## <a name="next-steps"></a>Další kroky

Nejlepší způsob, jak pochopit výhody a omezení průvodce, je projít. Následující rychlý start vás provede jednotlivými kroky.

> [!div class="nextstepaction"]
> [Vytvoření indexu Azure Cognitive Search pomocí portálu Azure](search-get-started-portal.md)