---
title: Ingestování dat do Azure Průzkumník dat pomocí přijímání jedním kliknutím
description: Přečtěte si, jak přijímat (načítat) data do Azure Průzkumník dat jednoduše pomocí přijímání jedním kliknutím.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: c4ee4ed81cd4cc443a8f412462a5a7f204c91898
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688191"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Ingestování dat do Azure Průzkumník dat pomocí přijímání jedním kliknutím

V tomto článku se dozvíte, jak používat příjem jedním kliknutím pro rychlou příjem nové tabulky ve formátech JSON nebo CSV z úložiště nebo místního souboru do existující tabulky nebo do nové tabulky v Azure Průzkumník dat. Pomocí intuitivního průvodce a během několika minut se data ingestují, můžete tabulku upravit a spouštět dotazy pomocí webového uživatelského rozhraní.

## <a name="prerequisites"></a>Předpoklady

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* Přihlaste se k [aplikaci](https://dataexplorer.azure.com/).
* Vytvoření [clusteru a databáze Azure Průzkumník dat](create-cluster-database-portal.md)
* Přihlaste se k [webovému uživatelskému rozhraní](https://dataexplorer.azure.com/) a [přidejte připojení ke clusteru](/azure/data-explorer/web-query-data#add-clusters) .
* Zdroj dat v Azure Storage.

## <a name="ingest-new-data"></a>Přijímání nových dat

1. Klikněte pravým tlačítkem myši na řádek *databáze* nebo *tabulky* v levé nabídce webového uživatelského rozhraní a vyberte možnost ingestovat **Nová data (Preview)** .

    ![výběr jednoho kliknutí na přijímání ve webovém uživatelském rozhraní](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. V okně ingestovat **Nová data (Preview)** na kartě **zdroj** dokončete **Podrobnosti projektu**:

    * **Tabulka**: Vyberte existující název tabulky z rozevíracího seznamu nebo vyberte **vytvořit novou** a vytvořte novou tabulku.
    * **Z úložiště** nebo **ze souboru**vyberte **typ** příjmu > .
        * Pokud jste vybrali možnost **úložiště**, zadejte **odkaz na úložiště** a přidejte tak adresu URL do úložiště. Použijte [adresu URL SAS objektu BLOB](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) pro privátní účty úložiště. 
        * Pokud jste vybrali možnost **ze souboru**, vyberte **Procházet** a přetáhněte ho do pole.
    * Vyberte **Upravit schéma** pro zobrazení a úpravu konfigurace sloupce tabulky.
 
    ![Podrobnosti o zdroji pro ingestování na jednom](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > Pokud na řádku *tabulky* vyberete možnost ingestovat **Nová data (Preview)** , zobrazí se v **podrobnostech o projektu**název vybrané tabulky.

1. Pokud jste vybrali existující tabulku, otevře se okno **sloupce mapy** , ve kterém se namapuje sloupce zdrojových dat na cílové sloupce tabulky. 
    * Sloupec **vynechat** použijte k odebrání cílového sloupce z tabulky. 
    * Nový **sloupec** použijte k přidání nového sloupce do tabulky. 

    ![Okno sloupce mapy](media/ingest-data-one-click/one-click-map-columns-window.png)

1. Na kartě **schéma** :

    * Z rozevíracího seznamu vyberte **typ komprese** > **uncompressed** nebo **gzip**.
    * Vyberte **Formát dat** z rozevíracího seznamu > **JSON**, **CSV**, **TSV**, **SCSV**, **SOHSV**, **TSVE**nebo **PSV**. 
        * Když vyberete formát **JSON** , vyberte **úrovně JSON**: 1-10. Úrovně ovlivňují znázornění dat sloupce tabulky. 
        * Pokud vyberete jiný formát než JSON: zaškrtněte políčko **zahrnout názvy sloupců** , aby se ignoroval řádek záhlaví souboru.    
    * **Mapování názvu** se nastaví automaticky, ale dá se upravit.
    * Pokud jste vybrali existující tabulku, můžete vybrat tlačítko **mapovat sloupce** a otevřít okno **sloupce mapy** .

    ![schéma formátu CSV pro přijímání zpráv o jednom kliknutím. png](media/ingest-data-one-click/one-click-csv-format.png)

1. V **editoru** **výběrem tlačítka v pravé** straně otevřete Editor. V editoru můžete zobrazit a zkopírovat automatické dotazy vygenerované z vašich vstupů. 

1.  V tabulce: 
    * Kliknutím pravým tlačítkem myši na nová záhlaví sloupců můžete **změnit datový typ**, **Přejmenovat sloupec**, **Odstranit sloupec**, **Seřadit vzestupně**nebo **Seřadit sestupně**. V existujících sloupcích je k dispozici pouze řazení dat. 
    * Dvakrát klikněte na název nového sloupce, který chcete upravit.

1. Vyberte **Spustit** ingestování pro vytvoření tabulky, vytvoření mapování a ingestování dat.

    ![schéma formátu JSON pro ingestování jedním kliknutím](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>Dotazování dat

1. V okně ingestování **dat** budou všechny tři kroky označeny zelenou značkou zaškrtnutí, pokud se přijímání dat úspěšně dokončilo. 
 
    ![dokončení příjmu dat jedním kliknutím](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Výběrem **V** otevřete dotaz. Chcete-li upravit dotaz, zkopírujte do webového uživatelského rozhraní.

1. Nabídka na pravé straně obsahuje **rychlé dotazy** a **nástroje**. 

    * **Rychlé dotazy** obsahují odkazy na webové uživatelské rozhraní s příklady dotazů.
    * **Nástroje** obsahují odkaz na webové uživatelské rozhraní s **příkazy drop** , které umožňují řešit problémy spuštěním příslušného `.drop` příkazu.

    > [!TIP]
    > Data mohou být ztracena pomocí `.drop`ch příkazů. Používejte je pečlivě.

## <a name="next-steps"></a>Další kroky

* [Dotazování na data ve službě Azure Průzkumník dat Web UI](web-query-data.md)
* [Zápis dotazů pro Azure Průzkumník dat pomocí dotazovacího jazyka Kusto](write-queries.md)
