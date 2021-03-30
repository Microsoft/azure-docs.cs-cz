---
title: Import dat do indexu vyhledávání pomocí Azure Portal
titleSuffix: Azure Cognitive Search
description: Naučte se používat Průvodce importem dat v Azure Portal k procházení dat Azure z Cosmos DB, úložiště objektů blob, úložiště tabulek, SQL Database, spravované instance SQL a SQL Server na virtuálních počítačích Azure.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7cff009d5d1e187e8d0330fadca530b57b3e3d21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88935207"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>Průvodce importem dat pro Azure Kognitivní hledání

Azure Portal poskytuje průvodce **importem dat** na řídicím panelu Azure kognitivní hledání pro vytváření prototypů a načítání indexu. Tento článek se věnuje výhodám a omezením při používání průvodce, vstupů a výstupů a některých informací o využití. Praktické pokyny k procházení průvodce pomocí integrovaných ukázkových dat najdete v tématu [vytvoření indexu Azure kognitivní hledání pomocí Azure Portal](search-get-started-portal.md) rychlého startu.

Operace, které tento průvodce provede, zahrnují:

1 – Připojte se k podporovanému zdroji dat Azure.

2 – Vytvořte schéma indexu odvozeného ze zdrojových dat vzorkování.

3 – Volitelně můžete přidat rozšíření AI pro extrakci nebo generování obsahu a struktury.

4 – spusťte Průvodce pro vytváření objektů, import dat, nastavení plánu a další možnosti konfigurace.

Průvodce vypíše počet objektů, které jsou uloženy do vaší vyhledávací služby, ke kterým máte přístup programově nebo v jiných nástrojích.

## <a name="advantages-and-limitations"></a>Výhody a omezení

Než začnete psát kód, můžete použít Průvodce pro testování prototypů a testování konceptu. Průvodce se připojuje k externím zdrojům dat, vzorkuje data pro vytvoření počátečního indexu a pak data importuje jako dokumenty JSON do indexu v Azure Kognitivní hledání. 

Vzorkování je proces, při kterém je schéma indexu odvozeno a má určitá omezení. Když se vytvoří zdroj dat, Průvodce vybere ukázku dokumentů a určí, které sloupce jsou součástí zdroje dat. Ne všechny soubory jsou čteny, protože by to mohlo trvat i hodiny u velmi rozsáhlých zdrojů dat. Při výběru dokumentů se zdrojová metadata, jako je název pole nebo typ, používají k vytvoření kolekce polí ve schématu indexu. V závislosti na složitosti zdrojových dat může být nutné upravit počáteční schéma pro přesnost nebo ho pro úplnosti zvětšit. Změny můžete provést na stránce definice indexu.

Obecně platí, že výhody použití Průvodce jsou jasné: Pokud jsou splněné požadavky, můžete prototypovat index Queryable během několika minut. Některé ze složitých indexů, jako jsou například poskytování dat jako dokumentů JSON, jsou zpracovávány průvodcem.

Známá omezení jsou shrnuta takto:

+ Průvodce nepodporuje iteraci ani opakované použití. Každý průchod průvodcem vytvoří nový index, dovednosti a konfiguraci indexeru. V průvodci můžete zachovat a znovu použít jenom zdroje dat. Chcete-li upravit nebo Upřesnit další objekty, je nutné použít rozhraní REST API nebo sadu .NET SDK pro načtení a úpravu struktury.

+ Zdrojový obsah se musí nacházet v podporovaném zdroji dat Azure.

+ Vzorkování je nad podmnožinou zdrojových dat. U rozsáhlých zdrojů dat je možné, že Průvodce neobdrží pole. Pokud vzorkování nestačí, možná budete muset schéma zvětšit nebo opravit odvozené datové typy.

+ Rozšíření AI, jak je zveřejněné na portálu, je omezené na několik integrovaných dovedností. 

+ [Znalostní báze](knowledge-store-concept-intro.md), který může průvodce vytvořit, je omezen na několik výchozích projekce. Pokud chcete uložit obohacené dokumenty vytvořené průvodcem, jsou v kontejneru objektů BLOB a v tabulkách výchozí názvy a struktura.

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>Vstup zdroje dat

Průvodce **importem dat** se připojuje k externímu zdroji dat pomocí interní logiky poskytované indexovací službou Azure kognitivní hledání indexery, které jsou vybavené pro vzorkování zdrojového kódu, čtení metadat, rozlomení dokumentů pro čtení obsahu a struktury a serializace obsahu jako JSON pro následné importy do Azure kognitivní hledání.

Importovat můžete pouze z jedné tabulky, zobrazení databáze nebo ekvivalentní datové struktury, ale struktura může zahrnovat hierarchické nebo vnořené podstruktury. Další informace najdete v tématu [modelování komplexních typů](search-howto-complex-data-types.md).

Tuto jednu tabulku nebo zobrazení byste měli vytvořit před spuštěním průvodce a musí obsahovat obsah. Z zjevné důvodů nemá smysl spustit průvodce **importem dat** v prázdném zdroji dat.

|  Výběr | Popis |
| ---------- | ----------- |
| **Stávající zdroj dat** |Pokud ve vyhledávací službě již máte definovány indexery, můžete mít existující definici zdroje dat, kterou můžete znovu použít. V Azure Kognitivní hledání objekty zdroje dat používají pouze indexery. Objekt zdroje dat můžete vytvořit programově nebo prostřednictvím průvodce **importem dat** a podle potřeby je znovu použít.|
| **ukázky**| Azure Kognitivní hledání poskytuje dva předdefinované ukázkové zdroje dat, které se používají v kurzech a rychlých startech: v reálném čase databáze SQL a databáze hotelů hostované na Cosmos DB. Návod na základě ukázky hotelů najdete v tématu [vytvoření indexu v Azure Portal](search-get-started-portal.md) rychlý Start. |
| [**Azure SQL Database nebo spravovaná instance SQL**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |Název služby, přihlašovací údaje uživatele s oprávněním ke čtení a název databáze můžete zadat na této stránce nebo přes připojovací řetězec technologie ADO.NET. Chcete-li zobrazit nebo přizpůsobit vlastnosti, zvolte možnost připojovacího řetězce. <br/><br/>Na této stránce je třeba určit tabulku nebo zobrazení poskytující sadu řádků. Tato možnost se zobrazí po úspěšném připojení v podobě rozevíracího seznamu, ze kterého můžete vybírat.|
| **SQL Server na virtuálním počítači Azure** |Zadejte plně kvalifikovaný název služby, ID uživatele a heslo a databázi jako připojovací řetězec. Abyste mohli použít tento zdroj dat, je třeba mít v místním úložišti dříve nainstalovaný certifikát šifrující připojení. Pokyny najdete v tématu [připojení k virtuálnímu počítači SQL do Azure kognitivní hledání](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>Na této stránce je třeba určit tabulku nebo zobrazení poskytující sadu řádků. Tato možnost se zobrazí po úspěšném připojení v podobě rozevíracího seznamu, ze kterého můžete vybírat. |
| [**Azure Cosmos DB**](search-howto-index-cosmosdb.md)|Požadavky zahrnují účet, databázi a kolekci. Všechny dokumenty v kolekci budou zahrnuty v indexu. Můžete definovat dotaz pro sloučení nebo filtrování sady řádků nebo nechat dotaz prázdný. V tomto průvodci není vyžadován dotaz.|
| [**Blob Storage Azure**](search-howto-indexing-azure-blob-storage.md) |Požadavky zahrnují účet úložiště a kontejner. Pokud se názvy objektů blob řídí zásadami virtuálního pojmenovávání pro účely seskupování, můžete volitelně zadat část názvu obsahující virtuální adresář jako složku v kontejneru. Další informace najdete v tématu [Indexování služby Blob Storage](search-howto-indexing-azure-blob-storage.md). |
| [**Table Storage Azure**](search-howto-indexing-azure-tables.md) |Požadavky zahrnují účet úložiště a název tabulky. Volitelně můžete zadat dotaz pro načtení podmnožiny tabulek. Další informace najdete v tématu [Indexování služby Table Storage](search-howto-indexing-azure-tables.md). |

## <a name="wizard-output"></a>Výstup Průvodce

Na pozadí Průvodce vytvoří, nakonfiguruje a vyvolá následující objekty. Po spuštění Průvodce můžete jeho výstup najít na stránkách portálu. Stránka přehled vaší služby obsahuje seznam indexů, indexerů, zdrojů dat a dovednosti. Definice indexů se dají zobrazit v plném formátu JSON na portálu. Pro jiné definice můžete použít [REST API](/rest/api/searchservice/) k získání konkrétních objektů.

| Objekt | Popis | 
|--------|-------------|
| [Zdroj dat](/rest/api/searchservice/create-data-source)  | Uchovává informace o připojení ke zdrojovým datům, včetně přihlašovacích údajů. Objekt zdroje dat se používá výhradně s indexery. | 
| [Index](/rest/api/searchservice/create-index) | Fyzická datová struktura, která se používá pro fulltextové vyhledávání a další dotazy. | 
| [Dovednosti](/rest/api/searchservice/create-skillset) | Kompletní sada instrukcí pro práci, transformaci a tvarování obsahu, včetně analýzy a extrakce informací ze souborů obrázků. S výjimkou jednoduchých a omezených struktur obsahuje odkaz na prostředek Cognitive Services, který poskytuje obohacení. Volitelně může také obsahovat definici znalostní báze Knowledge Store.  | 
| [Indexer](/rest/api/searchservice/create-indexer)  | Objekt konfigurace určující zdroj dat, cílový index, volitelný dovednosti, volitelný plán a volitelná nastavení konfigurace pro chyby a kódování Base-64. |


## <a name="how-to-start-the-wizard"></a>Jak spustit Průvodce

Průvodce importem dat je spuštěn z panelu příkazů na stránce Přehled služby.

1. V [Azure Portal](https://portal.azure.com)otevřete stránku vyhledávací služby z řídicího panelu nebo [vyhledejte službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v seznamu služby.

2. Na stránce Přehled služby v horní části klikněte na **importovat data**.

   ![Příkaz Importovat data na portálu](./media/search-import-data-portal/import-data-cmd2.png "Spuštění Průvodce importem dat")

Můžete také spustit **Import dat** z jiných služeb Azure, včetně Azure Cosmos DB, Azure SQL Database, spravované instance SQL a Azure Blob Storage. V levém navigačním podokně na stránce Přehled služby vyhledejte **Přidat Azure kognitivní hledání** .

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>Postup úpravy nebo dokončení schématu indexu v Průvodci

Průvodce vygeneruje nekompletní index, který se naplní dokumenty získanými ze vstupního zdroje dat. Pro funkční index se ujistěte, že máte definovány následující prvky.

1. Je seznam polí dokončený? Přidejte nová pole, která vzorkování nenalezlo, a odeberte všechny, které neumožňují přidat hodnotu do vyhledávacího prostředí, nebo které nebudou použity ve [výrazu filtru](search-query-odata-filter.md) nebo [profilu bodování](index-add-scoring-profiles.md).

1. Je datový typ vhodný pro příchozí data? Azure Kognitivní hledání podporuje [datové typy EDM (Entity Data Model)](/rest/api/searchservice/supported-data-types). Pro data SQL Azure je k dispozici [mapování grafu](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping) , které stanovuje ekvivalentní hodnoty. Další informace najdete v tématu [mapování polí a transformace](search-indexer-field-mappings.md).

1. Máte jedno pole, které může sloužit jako *klíč*? Toto pole musí být EDM. String a musí jednoznačně identifikovat dokument. V případě relačních dat může být namapována na primární klíč. U objektů BLOB může být `metadata-storage-path` . Pokud hodnoty polí obsahují mezery nebo pomlčky, je nutné nastavit možnost **klíč kódování Base-64** v kroku **Vytvoření indexeru** v části **Upřesnit možnosti** pro potlačení kontroly ověření pro tyto znaky.

1. Nastavte atributy pro určení způsobu použití tohoto pole v indexu. 

   Využijte tento krok v čase, protože atributy určují fyzický výraz polí v indexu. Pokud chcete změnit atributy později, dokonce i programově, je nutné index vyřadit a znovu sestavit. Základní atributy, jako **prohledávatelné** **a** získatelné, mají [zanedbatelný dopad na úložiště](search-what-is-an-index.md#index-size). Povolení filtrů a použití modulu pro návrhy zvyšují požadavky na úložiště. 
   
   + **Prohledávání** umožňuje fulltextové vyhledávání. Každé pole, které se používá v bezplatných dotazech formuláře nebo ve výrazech dotazů, musí mít tento atribut. Obrácené indexy se vytvoří pro každé pole, které označíte jako **prohledávatelné**.

   + K **disvratně** se vrátí pole ve výsledcích hledání. Každé pole, které poskytuje obsah pro výsledky hledání, musí mít tento atribut. Nastavení tohoto pole nemá výrazný vliv na velikost indexu.

   + **Filtrovatelné** umožňuje, aby pole bylo odkazováno ve výrazech filtru. Každé pole, které je použito ve výrazu **$Filter**  , musí mít tento atribut. Výrazy filtru jsou pro přesné shody. Vzhledem k tomu, že textové řetězce zůstávají beze změny, je nutné pro přizpůsobení doslovného obsahu vyžadovat další úložiště.

   + **Ploška** umožňuje pole pro omezující navigaci. Pouze pole označená jako **filtrovaná** mohou být označena jako **ploška**.

   + Možnost **řazení umožňuje,** aby pole bylo použito při řazení. Každé pole, které je použito ve výrazu **$OrderBy** , musí mít tento atribut.

1. Potřebujete [lexikální analýzu](search-lucene-query-architecture.md#stage-2-lexical-analysis)? U polí s řetězci EDM. String, která lze **Prohledávat**, můžete nastavit **analyzátor** , pokud chcete jazykově rozšířené indexování a dotazování. 

   Výchozí hodnota je *standardní Lucene* , ale pokud jste chtěli použít analyzátor od Microsoftu pro rozšířené lexikální zpracování, jako je například řešení nepravidelných podstatných jmen a operací, můžete zvolit *Microsoft English* . Na portálu lze zadat pouze analyzátory jazyka. Použití vlastního analyzátoru nebo nejazykového analyzátoru, jako je klíčové slovo, vzor a tak dále, musí být provedeno programově. Další informace o analyzátorech najdete v tématu [Přidání analyzátorů jazyka](search-language-support.md).

1. Potřebujete funkci typeahead ve formě automatického dokončování nebo navrhovaných výsledků? Zaškrtněte políčko **pro povolení** [návrhů dotazů typeahead a automatické dokončování](index-add-suggesters.md) pro vybraná pole. Moduly pro návrhy se přidávají do počtu vydaných tokenů v indexu, a proto spotřebovávají větší úložiště.


## <a name="next-steps"></a>Další kroky

Nejlepším způsobem, jak porozumět výhodám a omezením průvodce, je projít si ho. Následující rychlý Start vás provede jednotlivými kroky.

> [!div class="nextstepaction"]
> [Vytvoření indexu služby Azure Kognitivní hledání pomocí Azure Portal](search-get-started-portal.md)