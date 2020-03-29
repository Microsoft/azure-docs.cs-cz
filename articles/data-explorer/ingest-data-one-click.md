---
title: Ingestování dat do Azure Data Exploreru pomocí jedním kliknutím
description: Přečtěte si, jak ingestovat (načíst) data do Azure Data Exploreru jednoduše pomocí ingestování jedním kliknutím.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 4a53f7e68501ce7f9b19dea0822d3896ec241fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444555"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Ingestování dat do Azure Data Exploreru pomocí jedním kliknutím

Tento článek ukazuje, jak používat požití jedním kliknutím pro rychlé ingestování nové tabulky ve formátech JSON nebo CSV. Data mohou být ingestována z úložiště nebo místního souboru do existující tabulky nebo nové tabulky. Použijte intuitivní průvodce jedním kliknutím a vaše data se schytnou během několika minut. Potom můžete upravit tabulku a spouštět dotazy pomocí webového uživatelského rozhraní Průzkumníka dat Azure.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* Přihlaste se [k aplikaci](https://dataexplorer.azure.com/).
* [Vytvořte cluster a databázi Průzkumníka dat Azure](create-cluster-database-portal.md).
* Přihlaste se k [webovému uživatelskému uživatelskému uživatelskému](https://dataexplorer.azure.com/) [uživatelskému panelu a přidejte připojení ke clusteru](/azure/data-explorer/web-query-data#add-clusters).

## <a name="ingest-new-data"></a>Ingestování nových dat

1. Klepněte pravým tlačítkem myši na *databázi* nebo řádek *tabulky* v levé nabídce webového uživatelského prostředí a vyberte **příkaz Ingestovat nová data (náhled).**

    ![Výběr ingestování jedním kliknutím v uživatelském uživatelském počítači webu](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. V okně **Ingestovat nová data (náhled)** vyberte kartu **Zdroj** a vyplňte **podrobnosti projektu**:

    * V **nabídce Tabulka**vyberte existující název tabulky nebo vyberte **Vytvořit nový,** abyste vytvořili novou tabulku.
    * V případě **typu Požití**vyberte buď **z úložiště,** nebo **ze souboru**.
      * Pokud jste vybrali **z úložiště**, přidejte adresu URL výběrem **možnosti Propojit s úložištěm.** Pro účty privátního úložiště použijte [adresu URL objektu Blob SAS.](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) 
      * Pokud jste vybrali **ze souboru**, vyberte **Procházet** a přetáhněte soubor do pole.
    * Vyberte **Upravit schéma,** chcete-li zobrazit a upravit konfiguraci sloupce tabulky.
 
    ![Podrobnosti o zdroji požití jedním kliknutím](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > Pokud na řádku *tabulky* vyberete **Možnost Ingestovat nová data (náhled),** zobrazí se vybraný název tabulky v **podrobnostech projektu**.

1. Pokud jste vybrali existující tabulku, otevře se okno **Sloupce mapy** pro mapování sloupců zdrojových dat na cílové sloupce tabulky. 
    * Pomocí **sloupce Vynechat** od tabulky odeberte cílový sloupec.
    * Pomocí **nového sloupce** můžete do tabulky přidat nový sloupec.

    ![Okno Sloupce mapy](media/ingest-data-one-click/one-click-map-columns-window.png)

1. Na kartě **Schéma:**

    * V rozevírací nabídce vyberte **Typ komprese** a pak vyberte **Nekomprimovaný** nebo **GZip**.
    * V rozevírací nabídce vyberte **Formát dat** a pak vyberte **JSON**, **CSV**, **TSV**, **SCSV**, **SOHSV**, **TSVE**nebo **PSV**. 
        * Když vyberete formát **JSON,** musíte také vybrat **úrovně JSON**, od 1 do 10. Úrovně ovlivňují zobrazení dat sloupce tabulky. 
        * Pokud vyberete jiný formát než JSON, musíte zaškrtnout políčko **Zahrnout názvy sloupců,** abyste ignorovali řádek záhlaví souboru.
    * **Název mapování** je nastaven automaticky, ale lze jej upravovat.
    * Pokud jste vybrali existující tabulku, můžete vybrat **Možnost Mapovat sloupce** a otevřít okno **Sloupce mapy.**

    ![Schéma formátu CSV s jedním kliknutím](media/ingest-data-one-click/one-click-csv-format.png)

1. Nad podoknem **Editor** vyberte tlačítko **v** pro otevření editoru. V editoru můžete zobrazit a zkopírovat automatické dotazy generované z vašich vstupů. 

1. V tabulce: 
    * Klepněte pravým tlačítkem myši na nová záhlaví **sloupců: Změnit datový typ**, **Přejmenovat sloupec**, Odstranit **sloupec**, **Seřadit vzestupně**nebo **Seřadit sestupně**. V existujících sloupcích je k dispozici pouze řazení dat. 
    * Poklikejte na nový název sloupce, který chcete upravit.

1. Vyberte **Zahájit požití,** chcete-li vytvořit tabulku a mapování a zahájit přijímdat data.

    ![Schéma formátu JSON jedním kliknutím](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>Dotazování dat

1. V okně **dokončení ingestování dat** budou všechny tři kroky označeny zelenými značkami zaškrtnutí, pokud je požití dat úspěšně dokončeno.
 
    ![Požití dat jedním kliknutím dokončeno](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Kliknutím na tlačítko **v** otevřete dotaz. Zkopírujte do webového uživatelského panelu a upravte dotaz.

1. Nabídka vpravo obsahuje **rychlé dotazy** a **možnosti nástroje.** 

    * **Rychlé dotazy** obsahuje odkazy na webové uživatelské uživatelské tlačítko s ukázkovými dotazy.
    * **Nástroje** obsahují odkaz na **příkazy drop** v uživatelském okně webu, které `.drop` umožňují řešit problémy spuštěním příslušných příkazů.

    > [!TIP]
    > Při použití `.drop` příkazů může dojít ke ztrátě dat. Používejte je opatrně.

## <a name="next-steps"></a>Další kroky

* [Dotazovat se na data ve webovém uživatelském rozhraní Průzkumníka dat Azure](web-query-data.md)
* [Psaní dotazů pro Azure Data Explorer pomocí kusto dotazovacího jazyka](write-queries.md)
