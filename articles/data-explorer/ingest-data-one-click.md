---
title: Ingestování dat do Azure Průzkumník dat pomocí přijímání jedním kliknutím
description: Přečtěte si, jak přijímat (načítat) data do Azure Průzkumník dat jednoduše pomocí přijímání jedním kliknutím.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: b1ce2d9efe44021b4e3191739bd2f922e34c44cb
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69520046"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Ingestování dat do Azure Průzkumník dat pomocí přijímání jedním kliknutím

V tomto článku se dozvíte, jak pomocí ingestování jedním kliknutím rychle ingestovat novou tabulku ve formátech JSON nebo CSV z úložiště do Azure Průzkumník dat. Po ingestování dat můžete tabulku upravit a spustit dotazy pomocí webového uživatelského rozhraní.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* Přihlaste se k [aplikaci](https://dataexplorer.azure.com/).
* Vytvoření [clusteru a databáze Azure Průzkumník dat](create-cluster-database-portal.md)
* Zdroj dat v Azure Storage.

## <a name="ingest-new-data"></a>Přijímání nových dat

1. Klikněte pravým tlačítkem na *název databáze* a vyberte možnost ingestovat **Nová data (Preview)** .

    ![výběr jednoho kliknutí na přijímání ve webovém uživatelském rozhraní](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. V okně ingestování **dat (Preview)** na kartě **zdroj** dokončete **Podrobnosti projektu**:

    * Zadejte **název nové tabulky**. 
    * **Z úložiště**vyberte **typ** > přijímání.
    * Zadejte **odkaz na úložiště** přidat adresu URL do úložiště. Použijte adresu URL SAS objektu BLOB pro privátní účty úložiště. 
    * Vybrat **Upravit schéma**
 
    ![Podrobnosti o zdroji pro ingestování na jednom](media/ingest-data-one-click/one-click-ingestion-source.png) 

1. Na kartě **schéma** vyberte **Formát dat** z rozevíracího > **JSON** nebo **CSV**. 
   
   Pokud vyberte **CSV**:
    * Zaškrtnutím políčka **Ignorovat titulek** můžete ignorovat řádek záhlaví souboru CSV.    
    * **Mapování názvu** se nastaví automaticky, ale dá se upravit.

    ![schéma formátu CSV pro přijímání zpráv o jednom kliknutím. png](media/ingest-data-one-click/one-click-csv-format.png)

   Pokud vyberete **JSON**:
    * Vybrat **úrovně JSON**: 1-10 z rozevíracího seznamu. Úrovně v souboru JSON jsou zobrazeny v tabulce vpravo dole. 
    * **Mapování názvu** se nastaví automaticky, ale dá se upravit.

    ![schéma formátu JSON pro ingestování jedním kliknutím](media/ingest-data-one-click/one-click-json-format.png)  

1. V **editoru**vyberte v pravé části a otevřete Editor. V editoru můžete zobrazit a zkopírovat automatické dotazy vygenerované z vašich vstupů. 

1.  V tabulce vpravo dole: 
    * Vybrat v pravé části sloupce pro **přejmenování sloupce**, **odstranění sloupce**, **řazení vzestupně**nebo řazení sestupně
    * Dvakrát klikněte na název sloupce, který chcete upravit.
    * Vyberte ikonu vlevo od názvu sloupce a změňte datový typ. 

1. Vyberte **Spustit** ingestování pro vytvoření tabulky, vytvoření mapování a ingestování dat.
 
## <a name="query-data"></a>Dotazování dat

1. V okně ingestování **dat** budou všechny tři kroky označeny zelenou značkou zaškrtnutí, pokud se přijímání dat úspěšně dokončilo. 
 
    ![dokončení příjmu dat jedním kliknutím](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Výběrem **V** otevřete dotaz. Chcete-li upravit dotaz, zkopírujte do webového uživatelského rozhraní.

1. Nabídka na pravé straně obsahuje **rychlé dotazy** a **nástroje**. 

    * **Rychlé dotazy** obsahují odkazy na webové uživatelské rozhraní s příklady dotazů.
    * **Nástroje** obsahují odkaz na webové uživatelské rozhraní s **příkazy drop** , které umožňují řešit problémy spuštěním příslušného `.drop` příkazu.

    > [!TIP]
    > Data mohou být ztracena `.drop` pomocí příkazů. Používejte je pečlivě.

## <a name="next-steps"></a>Další postup

* [Dotazování na data ve službě Azure Průzkumník dat Web UI](web-query-data.md)
* [Zápis dotazů pro Azure Průzkumník dat pomocí dotazovacího jazyka Kusto](write-queries.md)
