---
title: Ingestování dat do Azure Průzkumník dat pomocí přijímání jedním kliknutím
description: Přečtěte si, jak přijímat (načítat) data do Azure Průzkumník dat jednoduše pomocí přijímání jedním kliknutím.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 4a53f7e68501ce7f9b19dea0822d3896ec241fb8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444555"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Ingestování dat do Azure Průzkumník dat pomocí přijímání jedním kliknutím

V tomto článku se dozvíte, jak používat přijímání jedním kliknutím pro rychlou příjem nové tabulky ve formátech JSON nebo CSV. Data je možné ingestovat z úložiště nebo místního souboru do existující tabulky nebo do nové tabulky. Použijte intuitivního průvodce jedním kliknutím a vaše data se ingestují během několika minut. Pak můžete tabulku upravit a spustit dotazy pomocí webového uživatelského rozhraní Azure Průzkumník dat.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* Přihlaste se k [aplikaci](https://dataexplorer.azure.com/).
* Vytvořte [cluster a databázi Azure Průzkumník dat](create-cluster-database-portal.md).
* Přihlaste se k [webovému uživatelskému rozhraní](https://dataexplorer.azure.com/) a [přidejte připojení ke clusteru](/azure/data-explorer/web-query-data#add-clusters).

## <a name="ingest-new-data"></a>Přijímání nových dat

1. Klikněte pravým tlačítkem myši na řádek *databáze* nebo *tabulky* v nabídce vlevo webového uživatelského rozhraní a vyberte možnost ingestovat **Nová data (Preview)** .

    ![Výběr přijímání jedním kliknutím ve webovém uživatelském rozhraní](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. V okně ingestovat **Nová data (Preview)** vyberte kartu **zdroj** a vyplňte **Podrobnosti projektu**:

    * V poli **tabulka**vyberte existující název tabulky z rozevírací nabídky nebo vyberte **vytvořit novou** a vytvořte novou tabulku.
    * Jako **typ**příjmu vyberte buď **ze služby Storage** , nebo **ze souboru**.
      * Pokud jste vybrali možnost **úložiště**, vyberte **odkaz na úložiště** a přidejte adresu URL. Použijte [adresu URL SAS objektu BLOB](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) pro privátní účty úložiště. 
      * Pokud jste vybrali možnost **ze souboru**, vyberte **Procházet** a přetáhněte ho do pole.
    * Vyberte **Upravit schéma** pro zobrazení a úpravu konfigurace sloupce tabulky.
 
    ![Podrobnosti zdroje ingestování jedním kliknutím](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > Pokud na řádku *tabulky* vyberete možnost ingestovat **Nová data (Preview)** , zobrazí se v **podrobnostech o projektu**název vybrané tabulky.

1. Pokud jste vybrali existující tabulku, otevře se okno **sloupce mapy** , ve kterém se namapuje sloupce zdrojových dat na cílové sloupce tabulky. 
    * Sloupec **vynechat** použijte k odebrání cílového sloupce z tabulky.
    * Nový **sloupec** použijte k přidání nového sloupce do tabulky.

    ![Okno sloupce mapy](media/ingest-data-one-click/one-click-map-columns-window.png)

1. Na kartě **schéma** :

    * V rozevírací nabídce vyberte **typ komprese** a pak vyberte možnost **uncompressed** nebo **gzip**.
    * V rozevírací nabídce vyberte **Formát dat** a pak vyberte **JSON**, **CSV**, **TSV**, **SCSV**, **SOHSV**, **TSVE**nebo **PSV**. 
        * Když vyberete formát **JSON** , musíte taky vybrat **úrovně JSON**, od 1 do 10. Úrovně ovlivňují znázornění dat sloupce tabulky. 
        * Pokud vyberete jiný formát než JSON, musíte zaškrtnout políčko **zahrnout názvy sloupců** , chcete-li ignorovat řádek záhlaví souboru.
    * **Mapování názvu** se nastaví automaticky, ale dá se upravit.
    * Pokud jste vybrali existující tabulku, můžete vybrat **sloupce mapy** a otevřít okno **sloupce mapy** .

    ![Schéma formátu CSV pro přijímání jedním kliknutím](media/ingest-data-one-click/one-click-csv-format.png)

1. Nad oknem **editoru** otevřete Editor kliknutím na tlačítko **v** . V editoru můžete zobrazit a zkopírovat automatické dotazy vygenerované z vašich vstupů. 

1. V tabulce: 
    * Kliknutím pravým tlačítkem myši na nová záhlaví sloupců můžete **změnit datový typ**, **Přejmenovat sloupec**, **Odstranit sloupec**, **Seřadit vzestupně**nebo **Seřadit sestupně**. U stávajících sloupců je k dispozici pouze řazení dat. 
    * Dvakrát klikněte na název nového sloupce, který chcete upravit.

1. Pokud chcete vytvořit tabulku a mapování a zahájit příjem dat, vyberte **Spustit** ingestování.

    ![Schéma formátu JSON pro přijímání jedním kliknutím](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>Dotazování dat

1. V okně ingestování **dat** budou všechny tři kroky označeny zelenými značkami zaškrtnutí, pokud se příjem dat úspěšně dokončí.
 
    ![Doplňování dat jedním kliknutím dokončeno](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Kliknutím na tlačítko **v** otevřete dotaz. Chcete-li upravit dotaz, zkopírujte do webového uživatelského rozhraní.

1. Nabídka na pravé straně obsahuje možnosti **rychlých dotazů** a **nástrojů** . 

    * **Rychlé dotazy** obsahují odkazy na webové uživatelské rozhraní s příklady dotazů.
    * **Nástroje** obsahují odkaz na **vyřazení příkazů** ve webovém uživatelském rozhraní, které vám umožní řešit problémy spuštěním příslušných `.drop` příkazů.

    > [!TIP]
    > Když použijete příkazy `.drop`, může dojít ke ztrátě dat. Používejte je pečlivě.

## <a name="next-steps"></a>Další kroky

* [Dotazování na data ve službě Azure Průzkumník dat Web UI](web-query-data.md)
* [Zápis dotazů pro Azure Průzkumník dat pomocí dotazovacího jazyka Kusto](write-queries.md)
