---
title: Import dat do indexu vyhledávání pomocí Azure Portal-Azure Search
description: Naučte se používat Průvodce importem dat v Azure Portal k procházení dat Azure z Cosmos DB, úložiště objektů blob, úložiště tabulek, SQL Database a SQL Server na virtuálních počítačích Azure.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: e784cbf351bd062712e0fd66332799907a3bcae8
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648252"
---
# <a name="import-data-wizard-for-azure-search"></a>Průvodce importem dat pro Azure Search

Azure Portal poskytuje v řídicím panelu služby Azure Search průvodce **Importem dat** pro načítání dat do indexu. Na pozadí Průvodce nakonfiguruje a vyvolá *zdroj dat*, *index*a *indexer* – automatizují několik kroků procesu indexování: 

* Připojí se k externímu zdroji dat ve stejném předplatném Azure.
* Volitelně integruje optické rozpoznávání znaků nebo zpracování přirozeného jazyka pro extrakci textu z nestrukturovaných dat.
* Vygeneruje index na základě vzorkování dat a metadat externího zdroje dat.
* Prochází zdroj dat pro prohledávatelný obsah, serializaci a načítání dokumentů JSON do indexu.

Průvodce se nemůže připojit k předdefinovanému indexu nebo spustit existujícího indexeru, ale v průvodci můžete nakonfigurovat nový index nebo indexer pro podporu struktury a chování, které potřebujete.

Jste nováčky ve službě Azure Search? Krok v [rychlém startu: Import, indexování a dotazování pomocí nástrojů portálu](search-get-started-portal.md) k otestování importu a indexování pomocí nástroje pro **Import dat** a integrované sady ukázkových dat v reálném čase.

## <a name="start-importing-data"></a>Spustit import dat

Tato část vysvětluje, jak spustit průvodce a poskytuje podrobný přehled jednotlivých kroků.

1. V [Azure Portal](https://portal.azure.com)otevřete stránku vyhledávací služby z řídicího panelu nebo [vyhledejte službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v seznamu služby.

2. Na stránce Přehled služby v horní části klikněte na **importovat data**.

   ![Příkaz Importovat data na portálu](./media/search-import-data-portal/import-data-cmd2.png "Spuštění Průvodce importem dat")

   > [!NOTE]
   > Můžete spouštět **importovaná data** z jiných služeb Azure, včetně Azure Cosmos DB, Azure SQL Database a Azure Blob Storage. Vyhledejte **přidat Azure Search** v levém navigačním podokně na stránce Přehled služby.

3. Průvodce se otevře pro **připojení k vašim datům**, kde můžete zvolit externí zdroj dat, který chcete pro tento import použít. O tomto kroku se dozvíte několik věcí, proto si přečtěte část [vstupy zdroje dat](#data-source-inputs) , kde najdete další podrobnosti.

   ![Průvodce importem dat na portálu](./media/search-import-data-portal/import-data-wizard-startup.png "Průvodce importem dat pro Azure Search")

4. Dále **přidejte hledání rozpoznávání**, pokud chcete zahrnout optické rozpoznávání znaků (OCR) textu v souborech obrázků nebo analýzu textu přes nestrukturovaná data. Pro tuto úlohu jsou načteny algoritmy AI z Cognitive Services. Tento krok obsahuje dvě části:
  
   Nejprve [připojte prostředek Cognitive Services](cognitive-search-attach-cognitive-services.md) k Azure Search dovednosti.
  
   Potom vyberte, která rozšíření AI se mají zahrnout do dovednosti. Návod k ukázce najdete v tomto [rychlém](cognitive-search-quickstart-blob.md)startu.

   Pokud chcete jenom importovat data, přeskočte tento krok a přejděte přímo do definice indexu.

5. Dále je možné **přizpůsobit cílový index**, kde můžete přijmout nebo upravit schéma indexu uvedené v průvodci. Průvodce odvodí pole a datové typy pomocí vzorkování dat a čtení metadat z externího zdroje dat.

   U každého pole [zaškrtněte atributy indexu](#index-definition) , aby se povolilo konkrétní chování. Pokud nevyberete žádné atributy, váš index nebude možné použít. 

6. Dále je **vytvořen indexer**, který je produktem tohoto průvodce. Indexer je prohledávací modul, který extrahuje hledaná data a metadata z externího zdroje dat Azure. Když vyberete zdroj dat a připojíte dovednosti (volitelné) a index, nakonfigurujete indexer při procházení jednotlivých kroků průvodce.

   Dejte indexeru název a kliknutím na **Odeslat** zahajte proces importu. 

Indexování můžete na portálu sledovat kliknutím na indexer v seznamu **indexery** . S načítáním dokumentů se bude zvyšovat počet dokumentů u indexu, který jste nadefinovali. Stránce portálu někdy trvá několik minut, než získá nejnovější aktualizace.

Index je připravený k dotazování ihned po načtení prvního dokumentu. Pro tuto úlohu můžete použít [Průzkumníka služby Search](search-explorer.md) .

<a name="data-source-inputs"></a>

## <a name="data-source-inputs"></a>Vstupy zdroje dat

Průvodce **importem dat** vytvoří trvalý objekt zdroje dat, který určuje informace o připojení k externímu zdroji dat. Objekt zdroje dat se používá výhradně s [indexery](search-indexer-overview.md) a je možné ho vytvořit pro následující zdroje dat: 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Table Storage Azure](search-howto-indexing-azure-tables.md) (není podporováno pro kanály [hledání rozpoznávání](cognitive-search-concept-intro.md) )

Importovat můžete pouze z jedné tabulky, zobrazení databáze nebo ekvivalentní datové struktury, ale struktura může zahrnovat hierarchické nebo vnořené podstruktury. Další informace najdete v tématu [modelování komplexních typů](search-howto-complex-data-types.md).

Tuto strukturu dat byste měli vytvořit před spuštěním průvodce a musí obsahovat obsah. Nespouštějte Průvodce **importem dat** v prázdném zdroji dat.

|  Výběr | Popis |
| ---------- | ----------- |
| **Stávající zdroj dat** |Pokud již ve vyhledávací službě máte definované indexery, můžete pro další import vybrat stávající definici zdroje dat. V Azure Search objekty zdroje dat používají pouze indexery. Objekt zdroje dat můžete vytvořit programově nebo prostřednictvím průvodce **importem dat** .|
| **Ukázky**| Azure Search hostuje bezplatnou globální databázi SQL Azure, kterou můžete použít k získání informací o importu a dotazování požadavků v Azure Search. Další [informace najdete v tématu rychlý Start: Import, indexování a dotazování pomocí nástrojů](search-get-started-portal.md) portálu pro návod |
| **Azure SQL Database** |Název služby, přihlašovací údaje uživatele s oprávněním ke čtení a název databáze můžete zadat na této stránce nebo přes připojovací řetězec technologie ADO.NET. Chcete-li zobrazit nebo přizpůsobit vlastnosti, zvolte možnost připojovacího řetězce. <br/><br/>Na této stránce je třeba určit tabulku nebo zobrazení poskytující sadu řádků. Tato možnost se zobrazí po úspěšném připojení v podobě rozevíracího seznamu, ze kterého můžete vybírat. |
| **SQL Server na virtuálním počítači Azure** |Zadejte plně kvalifikovaný název služby, ID uživatele a heslo a databázi jako připojovací řetězec. Abyste mohli použít tento zdroj dat, je třeba mít v místním úložišti dříve nainstalovaný certifikát šifrující připojení. Pokyny najdete v tématu [Připojení virtuálního počítače SQL ke službě Azure Search](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>Na této stránce je třeba určit tabulku nebo zobrazení poskytující sadu řádků. Tato možnost se zobrazí po úspěšném připojení v podobě rozevíracího seznamu, ze kterého můžete vybírat. |
| **Databáze Cosmos** |Požadavky zahrnují účet, databázi a kolekci. Všechny dokumenty v kolekci budou zahrnuty v indexu. Můžete definovat dotaz pro sloučení nebo filtrování sady řádků nebo nechat dotaz prázdný. V tomto průvodci není vyžadován dotaz.|
| **Azure Blob Storage** |Požadavky zahrnují účet úložiště a kontejner. Pokud se názvy objektů blob řídí zásadami virtuálního pojmenovávání pro účely seskupování, můžete volitelně zadat část názvu obsahující virtuální adresář jako složku v kontejneru. Další informace najdete v tématu [Indexování služby Blob Storage](search-howto-indexing-azure-blob-storage.md). |
| **Azure Table Storage** |Požadavky zahrnují účet úložiště a název tabulky. Volitelně můžete zadat dotaz pro načtení podmnožiny tabulek. Další informace najdete v tématu [Indexování služby Table Storage](search-howto-indexing-azure-tables.md). |


<a name="index-definition"></a>

## <a name="index-attributes"></a>Atributy indexu

Průvodce **importem dat** vygeneruje index, který se naplní dokumenty získanými ze vstupního zdroje dat. 

Pro funkční index se ujistěte, že máte definovány následující prvky.

1. Jedno pole musí být označeno jako **klíč**, který slouží k jednoznačné identifikaci každého dokumentu. **Klíč** musí být *EDM. String*. 

   Pokud hodnoty polí obsahují mezery nebo pomlčky, je nutné nastavit možnost **klíč kódování Base-64** v kroku **Vytvoření indexeru** v části **Upřesnit možnosti**pro potlačení kontroly ověření pro tyto znaky.

1. Nastavte atributy indexu pro každé pole. Pokud nevyberete žádné atributy, index je v podstatě prázdný, s výjimkou požadovaného pole klíče. Vyberte alespoň jeden z těchto atributů pro každé pole.
   
   + K disvratně se vrátí pole ve výsledcích hledání. Každé pole, které poskytuje obsah pro výsledky hledání, musí mít tento atribut. Nastavení tohoto pole nemá výrazný vliv na velikost indexu.
   + **Filtrovatelné** umožňuje, aby pole bylo odkazováno ve výrazech filtru. Každé pole, které je použito ve výrazu **$Filter** , musí mít tento atribut. Výrazy filtru jsou pro přesné shody. Vzhledem k tomu, že textové řetězce zůstávají beze změny, je nutné pro přizpůsobení doslovného obsahu vyžadovat další úložiště.
   + **Prohledávání** umožňuje fulltextové vyhledávání. Každé pole, které se používá v bezplatných dotazech formuláře nebo ve výrazech dotazů, musí mít tento atribut. Obrácené indexy se vytvoří pro každé pole, které označíte jako **prohledávatelné**.

1. Volitelně můžete nastavit tento atribut podle potřeby:

   + Možnost řazení umožňuje, aby pole bylo použito při řazení. Každé pole, které je použito ve výrazu **$OrderBy** , musí mít tento atribut.
   + **Ploška** umožňuje pole pro omezující navigaci. Pouze pole označená jako **filtrovaná** mohou být označenajako ploška.

1. Nastavte **analyzátor** , pokud chcete jazykově rozšířené indexování a dotazování. Výchozí hodnota je *standardní Lucene* , ale pokud jste chtěli použít analyzátor od Microsoftu pro rozšířené lexikální zpracování, jako je například řešení nepravidelných podstatných jmen a operací, můžete zvolit *Microsoft English* .

   + Vyberte možnost prohledatelné, pokud chcete povolit seznam **analyzátorů** .
   + Vyberte analyzátor uvedený v seznamu. 
   
   V této chvíli lze určit pouze analyzátory jazyka. Použití vlastního analyzátoru nebo nejazykového analyzátoru, jako například analyzátoru klíčových slov, vzoru a dalších, bude vyžadovat psaní kódu. Další informace o analyzátorech najdete v tématu [vytvoření indexu pro dokumenty v několika jazycích](search-language-support.md).

1. Zaškrtněte políčko , pokud chcete pro vybraná pole Povolit návrhy dotazů typu dopředu.


## <a name="next-steps"></a>Další postup
Další informace o indexerech najdete na těchto odkazech:

* [Indexování služby Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Indexování služby Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Indexování služby Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Indexování služby Table Storage](search-howto-indexing-azure-tables.md)