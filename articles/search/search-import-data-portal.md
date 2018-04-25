---
title: Import dat do služby Azure Search na portálu | Dokumentace Microsoftu
description: Použijte Průvodce importem dat do služby Azure Search na webu Azure Portal k procházení dat Azure z databáze NoSQL Azure Cosmos DB, ze služby Blob Storage, Table Storage, SQL Database a SQL Serveru na virtuálních počítačích Azure.
author: HeidiSteen
manager: cgronlun
tags: Azure Portal
services: search
ms.service: search
ms.topic: quickstart
ms.date: 05/01/2017
ms.author: heidist
ms.openlocfilehash: ee27b63a5df658ff5d575f0599dadd1cbafd3c18
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="import-data-to-azure-search-using-the-portal"></a>Import dat do služby Azure Search pomocí portálu
Azure Portal poskytuje v řídicím panelu služby Azure Search průvodce **Importem dat** pro načítání dat do indexu. 

  ![Import dat na panelu příkazů][1]

Průvodce interně konfiguruje a vyvolává *indexer*, čímž automatizuje několik kroků procesu indexování: 

* Připojení k externímu zdroji dat ve stejném předplatném Azure
* Generování upravitelného schématu indexu na základě struktury zdrojových dat
* Načtení dokumentů JSON do indexu s použitím sady řádků získané ze zdroje dat

Tento pracovní postup můžete vyzkoušet s použitím ukázkových dat ve službě Azure Cosmos DB. Pokyny najdete v tématu [Začínáme se službou Azure Search na webu Azure Portal](search-get-started-portal.md).

> [!NOTE]
> Z řídicího panelu služby Azure Cosmos DB můžete spustit průvodce **Importem dat** a zjednodušit tak indexování zdroje dat. Začněte tak, že v levém navigačním panelu přejdete do **Collections** (Kolekce) > **Add Azure Search** (Přidat službu Azure Search).

## <a name="data-sources-supported-by-the-import-data-wizard"></a>Zdroje dat podporované Průvodcem importem dat
Průvodce importem dat podporuje následující zdroje dat: 

* Azure SQL Database
* Relační data systému SQL Server na virtuálním počítači Azure
* Azure Cosmos DB
* Azure Blob Storage
* Azure Table Storage

Plochá datová sada je požadovaný vstup. Importovat můžete pouze z jedné tabulky, jednoho zobrazení databáze nebo ekvivalentní datové struktury. Tuto datovou strukturu byste měli vytvořit před spuštěním průvodce.

## <a name="connect-to-your-data"></a>Připojení k datům
1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) a otevřete řídicí panel služby. Kliknutím na **Všechny služby** na panelu odkazů můžete vyhledávat stávající „vyhledávací služby“ v rámci aktuálního předplatného. 
2. Kliknutím na **Import dat** na panelu příkazů otevřete vysouvací okno Import dat.  
3. Klikněte na **Připojit k datům** a zadejte definici zdroje dat používanou indexerem. V případě zdrojů dat v rámci předplatného průvodce obvykle může rozpoznat a přečíst informace o připojení, čímž minimalizuje celkové požadavky na konfiguraci.

|  |  |
| --- | --- |
| **Stávající zdroj dat** |Pokud již ve vyhledávací službě máte definované indexery, můžete pro další import vybrat stávající definici zdroje dat. |
| **Azure SQL Database** |Název služby, přihlašovací údaje uživatele s oprávněním ke čtení a název databáze můžete zadat na této stránce nebo přes připojovací řetězec technologie ADO.NET. Chcete-li zobrazit nebo přizpůsobit vlastnosti, zvolte možnost připojovacího řetězce. <br/><br/>Na této stránce je třeba určit tabulku nebo zobrazení poskytující sadu řádků. Tato možnost se zobrazí po úspěšném připojení v podobě rozevíracího seznamu, ze kterého můžete vybírat. |
| **SQL Server na virtuálním počítači Azure** |Zadejte plně kvalifikovaný název služby, ID a heslo uživatele a databázi jako připojovací řetězec. Abyste mohli použít tento zdroj dat, je třeba mít v místním úložišti dříve nainstalovaný certifikát šifrující připojení. Pokyny najdete v tématu [Připojení virtuálního počítače SQL ke službě Azure Search](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>Na této stránce je třeba určit tabulku nebo zobrazení poskytující sadu řádků. Tato možnost se zobrazí po úspěšném připojení v podobě rozevíracího seznamu, ze kterého můžete vybírat. |
| **Azure Cosmos DB** |Požadavky zahrnují účet, databázi a kolekci. Všechny dokumenty v kolekci budou zahrnuty v indexu. Můžete definovat dotaz, který zploští nebo vyfiltruje sadu řádků nebo který rozpozná změněné dokumenty pro následné operace aktualizace dat. |
| **Azure Blob Storage** |Požadavky zahrnují účet úložiště a kontejner. Pokud se názvy objektů blob řídí zásadami virtuálního pojmenovávání pro účely seskupování, můžete volitelně zadat část názvu obsahující virtuální adresář jako složku v kontejneru. Další informace najdete v tématu [Indexování služby Blob Storage](search-howto-indexing-azure-blob-storage.md). |
| **Azure Table Storage** |Požadavky zahrnují účet úložiště a název tabulky. Volitelně můžete zadat dotaz pro načtení podmnožiny tabulek. Další informace najdete v tématu [Indexování služby Table Storage](search-howto-indexing-azure-tables.md). |

## <a name="customize-target-index"></a>Přizpůsobení cílového indexu
Předběžný index je obvykle odvozen z datové sady. Přidejte, upravte nebo odstraňte pole a dokončete schéma. Případně nastavte atributy na úrovni pole k určení jeho chování při následném vyhledávání.

1. V části **Přizpůsobit cílový index** zadejte název a **Klíč** sloužící k jedinečné identifikaci každého dokumentu. Hodnota Klíč musí být řetězec. Pokud hodnoty polí obsahují mezery nebo pomlčky, ujistěte se, že v části **Import dat** nastavíte rozšířené možnosti, které pro tyto znaky potlačí kontrolu platnosti.
2. Zkontrolujte a zrevidujte zbývající pole. Název a typ pole jsou obvykle předvyplněné. Datový typ můžete měnit, dokud se nevytvoří index. Pozdější změna bude vyžadovat opětovné sestavení.
3. Pro každé pole nastavte atribut indexu:
   
   * Retrievable (Zobrazitelné) – vrátí pole ve výsledcích vyhledávání.
   * Filterable (Filtrovatelné) – umožňuje na pole odkazovat ve výrazech filtru.
   * Sortable (Seřaditelné) – umožňuje použití pole při řazení.
   * Facetable (Kategorizovatelné) – povoluje použití pole pro fasetovou navigaci.
   * Searchable (Prohledávatelné) – umožňuje fulltextové vyhledávání.
4. Chcete-li určit analyzátor jazyka na úrovni pole, klikněte na kartu **Analyzátor**. V této chvíli lze určit pouze analyzátory jazyka. Použití vlastního analyzátoru nebo nejazykového analyzátoru, jako například analyzátoru klíčových slov, vzoru a dalších, bude vyžadovat psaní kódu.
   
   * Kliknutím na **Prohledávatelné** určíte fulltextové vyhledávání pole a povolíte rozevírací seznam Analyzátor.
   * Vyberte analyzátor, který chcete. Podrobnosti naleznete v oddílu [Vytvoření indexu pro vícejazyčné dokumenty](search-language-support.md).
5. Kliknutím na **Našeptávač** povolíte pro vybraná pole našeptávání dotazů.

## <a name="import-your-data"></a>Import dat
1. V části **Import dat** zadejte název indexeru. Připomínáme, že indexer je výsledkem Průvodce importem dat. Pokud jej později budete chtít zobrazit nebo upravit, místo opětovného spuštění průvodce jej vyberete z portálu. 
2. Zadejte plán založený na časovém pásmu oblasti, ve které je služba zřízená.
3. Nastavením rozšířených možností můžete zadat prahové hodnoty, podle kterých se určí, zda může indexování pokračovat v případě zrušení dokumentu. Případně můžete určit, zda pole **Klíč** mohou obsahovat mezery a lomítka.  
4. Kliknutím na **OK** vytvořte index a zahajte import dat.

Indexování můžete monitorovat na portálu. S načítáním dokumentů se bude zvyšovat počet dokumentů u indexu, který jste nadefinovali. Stránce portálu někdy trvá několik minut, než získá nejnovější aktualizace.

Index je připraven k dotazování, jakmile jsou načteny všechny dokumenty.

## <a name="query-an-index-using-search-explorer"></a>Dotazování indexu pomocí Průzkumník služby Search

Portál zahrnuje **Průzkumníka služby Search**, kterého můžete použít k zadávání dotazů na index, aniž byste museli programovat. [Průzkumníka služby Search](search-explorer.md) můžete použít na jakýkoli index.

Funkce vyhledávání je založena na výchozím nastavení, jako je [jednoduchá syntaxe](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) a výchozí [parametr dotazu searchMode(https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Podrobné výsledky jsou vráceny ve formátu JSON, abyste si mohli prohlédnout celý dokument.

## <a name="edit-an-existing-indexer"></a>Úprava existujícího indexeru
Jak jsme uvedli, průvodce importem dat vytvoří **indexer**, který můžete na portálu upravit jako samostatnou konstrukci.

Dvojím kliknutím na dlaždici Indexer v řídícím panelu služby vysuňte seznam všech indexerů vytvořených pro vaše předplatné. Dvakrát klikněte na indexery, které chcete spustit, upravit nebo odstranit. Index můžete nahradit jiným existujícím indexem, změnit zdroj dat a nastavit možnosti prahových hodnot chybu během indexování.

## <a name="edit-an-existing-index"></a>Úprava existujícího indexu
Průvodce vytvořil také **index**. Strukturální aktualizace indexu ve službě Azure Search vyžadují opětovné sestavení daného indexu. Opětovné sestavení zahrnuje odstranění indexu, opětovné vytvoření indexu s použitím zrevidovaného schématu s požadovanými změnami a načtení dat. Strukturální aktualizace zahrnují změnu datového typu a přejmenování nebo odstranění pole.

Úpravy, které nevyžadují opětovné sestavení zahrnují přidání nového pole, změnu vyhodnocování profilů, změna navrhovatelů nebo změnu analyzátorů jazyka. Další informace naleznete v [aktualizaci indexu](https://msdn.microsoft.com/library/azure/dn800964.aspx).


## <a name="next-steps"></a>Další kroky
Další informace o indexerech najdete na těchto odkazech:

* [Indexování služby Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Indexování služby Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Indexování služby Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Indexování služby Table Storage](search-howto-indexing-azure-tables.md)

<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png

