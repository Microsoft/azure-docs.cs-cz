---
title: Import dat do indexu vyhledávání pomocí webu Azure portal – Azure Search
description: Další informace o použití Průvodce importem dat na webu Azure Portal k procházení dat Azure z Cosmos DB, úložiště objektů Blob, table storage, SQL Database a SQL Server na virtuálních počítačích Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: ee1b2a40dbcbd53a758ac71f30401778ef07e872
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54229753"
---
# <a name="import-data-wizard-for-azure-search"></a>Průvodce importem dat pro službu Azure Search

Azure Portal poskytuje v řídicím panelu služby Azure Search průvodce **Importem dat** pro načítání dat do indexu. Na pozadí Průvodce konfiguruje a vyvolává *zdroj dat*, *index*, a *indexer* -čímž automatizuje několik kroků procesu indexování: 

* Připojení k externímu zdroji dat v rámci stejného předplatného Azure.
* Volitelně se integruje se zpracování optické rozpoznávání znaků nebo přirozený jazyk k extrakci textu z Nestrukturovaná data.
* Index založený na vzorkování dat a metadata externí zdroj dat generuje.
* Procházení zdroj dat pro prohledávatelný obsah, serializace a nahrávání dokumentů JSON do indexu.

Průvodce nemůže připojit k předdefinovaný index nebo spuštění existujícího indexeru, ale v rámci průvodce, můžete nakonfigurovat nového indexu nebo indexeru pro podporu struktury a chování, které potřebujete.

Jste nováčky ve službě Azure Search? Projít [rychlý start: Import, indexování a dotazování pomocí nástroje portálu](search-get-started-portal.md) mohli vyzkoušet importu a indexování s využitím **importovat data** a realestate integrovanou ukázkovou datovou sadu.

## <a name="start-importing-data"></a>Zahájení importu dat

Tato část vysvětluje, jak spustit průvodce a poskytuje základní přehled jednotlivých kroků.

1. V [webu Azure portal](https://portal.azure.com), otevřete stránku vyhledávací služby z řídicího panelu nebo [svou službu vyhledejte](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v seznamu služeb.

2. V horní části na stránce Přehled služby, klikněte na tlačítko **importovat data**.

   ![Příkaz pro import dat na portálu](./media/search-import-data-portal/import-data-cmd2.png "spusťte Průvodce importem dat")

   > [!NOTE]
   > Můžete spustit **importovat data** od ostatních služeb Azure, včetně služby Azure Cosmos DB, Azure SQL Database a Azure Blob storage. Vyhledejte **přidat Azure Search** v levém navigačním podokně na stránce Přehled služby.

3. Otevře se průvodce k **připojit ke svým datům**, kde můžete vybrat externímu zdroji dat použít pro import. Existuje několik věcí, které vědět o tomto kroku, takže nezapomeňte si přečíst [zdroje dat vstupy](#data-source-inputs) části Další podrobnosti.

   ![Průvodce importem dat portálu](./media/search-import-data-portal/import-data-wizard-startup.png "Průvodce importem dat pro službu Azure Search")

4. Dále je **kognitivního vyhledávání přidat**, v případě, že chcete zahrnout optické rozpoznávání znaků (OCR) textu do souborů obrázků a analýza textu Nestrukturovaná data. Algoritmy AI ze služeb Cognitive Services jsou získaná pro tuto úlohu. Existují dvě části k tomuto kroku:
  
   Nejprve je potřeba [připojit prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md) na dovednosti Azure Search.
  
   Za druhé zvolte který AI obohacení zahrnout zkušenostech. Názorný postup ukázku, najdete v tomto [rychlý Start](cognitive-search-quickstart-blob.md).

   Pokud chcete importovat data, tento krok přeskočit a přejít přímo na definici indexu.

5. Dále je **přizpůsobit cílový index**, kde můžete přijmout nebo změnit schéma indexu uvedené v průvodci. Průvodce odvodí vzorkování dat a čtení metadat z externího zdroje dat pole a datové typy.

   Pro každé pole [zkontrolujte atributy indexu](#index-definition) povolit konkrétní chování. Pokud nevyberete všechny atributy, nebude možné použít indexu. 

6. Dále je **vytvořit indexer**, což je produkt tohoto průvodce. Indexer je prohledávací modul, který extrahuje prohledávatelná data a metadata ze zdroje externích dat Azure. Vyberte zdroj dat a připojením dovednosti (volitelné) a indexu, jste byla konfigurujete indexer při procházení každý krok průvodce.

   Zadejte název indexeru a klikněte na tlačítko **odeslat** zahajte proces importu. 

Indexování na portálu kliknutím v indexeru můžete monitorovat **indexery** seznamu. S načítáním dokumentů se bude zvyšovat počet dokumentů u indexu, který jste nadefinovali. Stránce portálu někdy trvá několik minut, než získá nejnovější aktualizace.

Index je připraven k dotazování ihned poté, co je první dokument načten. Můžete použít [Průzkumníka služby Search](search-explorer.md) pro tuto úlohu.

<a name="data-source-inputs"></a>

## <a name="data-source-inputs"></a>Vstupy pro zdroj dat

**Importovat data** Průvodce vytvoří zdrojový objekt trvalých dat zadáte informace o připojení k externímu zdroji dat. Objekt zdroje dat se používá výhradně s [indexery](search-indexer-overview.md) a lze vytvořit pro následující zdroje dat: 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md) (není podporováno pro [kognitivního vyhledávání](cognitive-search-concept-intro.md) kanálů)

Plochá datová sada je požadovaný vstup. Importovat můžete pouze z jedné tabulky, jednoho zobrazení databáze nebo ekvivalentní datové struktury. Tuto datovou strukturu byste měli vytvořit před spuštěním průvodce.

|  Výběr | Popis |
| ---------- | ----------- |
| **Stávající zdroj dat** |Pokud již ve vyhledávací službě máte definované indexery, můžete pro další import vybrat stávající definici zdroje dat. Ve službě Azure Search objekty zdroje dat slouží pouze indexery. Můžete programově vytvořit objekt zdroje dat nebo prostřednictvím **importovat data** průvodce.|
| **Ukázky**| Služba Azure Search je hostitelem bezplatné veřejné databáze Azure SQL, můžete použít další informace o importu a dotaz požadavků ve službě Azure Search. Zobrazit [rychlý start: Import, indexování a dotazování pomocí nástroje portálu](search-get-started-portal.md) návod. |
| **Azure SQL Database** |Název služby, přihlašovací údaje uživatele s oprávněním ke čtení a název databáze můžete zadat na této stránce nebo přes připojovací řetězec technologie ADO.NET. Chcete-li zobrazit nebo přizpůsobit vlastnosti, zvolte možnost připojovacího řetězce. <br/><br/>Na této stránce je třeba určit tabulku nebo zobrazení poskytující sadu řádků. Tato možnost se zobrazí po úspěšném připojení v podobě rozevíracího seznamu, ze kterého můžete vybírat. |
| **SQL Server na virtuálním počítači Azure** |Zadejte plně kvalifikovaný název, ID uživatele a heslo a databázi jako připojovací řetězec. Abyste mohli použít tento zdroj dat, je třeba mít v místním úložišti dříve nainstalovaný certifikát šifrující připojení. Pokyny najdete v tématu [Připojení virtuálního počítače SQL ke službě Azure Search](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>Na této stránce je třeba určit tabulku nebo zobrazení poskytující sadu řádků. Tato možnost se zobrazí po úspěšném připojení v podobě rozevíracího seznamu, ze kterého můžete vybírat. |
| **Azure Cosmos DB** |Požadavky zahrnují účet, databázi a kolekci. Všechny dokumenty v kolekci budou zahrnuty v indexu. Můžete definovat dotaz, který zploští nebo vyfiltruje sadu řádků nebo který rozpozná změněné dokumenty pro následné operace aktualizace dat. |
| **Azure Blob Storage** |Požadavky zahrnují účet úložiště a kontejner. Pokud se názvy objektů blob řídí zásadami virtuálního pojmenovávání pro účely seskupování, můžete volitelně zadat část názvu obsahující virtuální adresář jako složku v kontejneru. Další informace najdete v tématu [Indexování služby Blob Storage](search-howto-indexing-azure-blob-storage.md). |
| **Azure Table Storage** |Požadavky zahrnují účet úložiště a název tabulky. Volitelně můžete zadat dotaz pro načtení podmnožiny tabulek. Další informace najdete v tématu [Indexování služby Table Storage](search-howto-indexing-azure-tables.md). |


<a name="index-definition"></a>

## <a name="index-attributes"></a>Atributy indexu

**Importovat data** Průvodce vytvoří index, který naplní se dokumenty získané z zdrojového vstupní data. 

Funkční indexu Ujistěte se, že máte následující prvky definice.

1. Jedno pole musí být označen jako **klíč**, který se používá k jedinečné identifikaci každého dokumentu. **Klíč** musí být *Edm.string*. 

   Pokud hodnoty polí obsahují mezery nebo pomlčky, je nutné nastavit **klíče kódování Base-64** možnost **vytvořit Indexer** krok v části **pokročilé možnosti**, můžete potlačit ověření pro tyto znaky.

1. Nastavte atribut indexu pro každé pole. Pokud vyberete možnost žádné atributy, je v podstatě prázdný, s výjimkou požadované pole klíče indexu. Minimálně vyberte jednu nebo více z těchto atributů pro každé pole.
   
   + **Retrievable** vrátí pole ve výsledcích hledání. Každé pole, která poskytuje obsah tak, aby výsledky hledání musí mít tento atribut. Nastavení tohoto pole to vliv nemá znatelně velikost indexu.
   + **Filterable** umožňuje pole odkazovat ve výrazech filtru. Každé pole použité v **$filter** výraz musí mít tento atribut. Filtrovací výrazy jsou určené pro přesné shody. Protože textové řetězce zůstanou beze změny, se vyžaduje další storage tak, aby vyhovovaly verbatim obsah.
   + **Prohledávání** umožňuje fulltextové vyhledávání. Každé pole použité v dotazech volného formátu nebo ve výrazech dotazů musí mít tento atribut. Obráceným indexy jsou vytvořené pro každé pole, které označíte jako **Searchable**.

1. Volitelně můžete nastavte tyto atribut, podle potřeby:

   + **Seřaditelné** umožňuje použít při řazení pole. Každé pole použité v **$Orderby** výraz musí mít tento atribut.
   + **Facetable** povoluje použití pole pro fasetovou navigaci. Pouze pole také označená jako **Filterable** může být označený jako **Facetable**.

1. Nastavení **analyzátor** Pokud chcete jazyk rozšířeného indexování a dotazování na ně. Výchozí hodnota je *standardní Lucene* ale můžete se rozhodnout *Microsoft English* Pokud byste chtěli použít analyzátor společnosti Microsoft pro pokročilé zpracování lexikální, jako jsou například řešení nestandardní podstatné jméno a příkaz formulářů.

   + Vyberte **prohledávatelné** povolit **analyzátor** seznamu.
   + Vyberte analyzátor uvedené v seznamu. 
   
   V této chvíli lze určit pouze analyzátory jazyka. Použití vlastního analyzátoru nebo nejazykového analyzátoru, jako například analyzátoru klíčových slov, vzoru a dalších, bude vyžadovat psaní kódu. Další informace o analyzátorech najdete v tématu [vytvoření indexu pro dokumenty v několika jazycích](search-language-support.md).

1. Vyberte **modulu pro návrhy** zaškrtávací políčko pro povolení našeptávání dotazů na vybraná pole.


## <a name="next-steps"></a>Další postup
Další informace o indexerech najdete na těchto odkazech:

* [Indexování služby Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Indexování služby Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Indexování služby Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Indexování služby Table Storage](search-howto-indexing-azure-tables.md)