---
title: Použití ovládacích příkazů Průzkumníka dat Azure v Azure Data Factory
description: V tomto tématu použijte příkazy ovládacího prvku Azure Data Explorer v Azure Data Factory
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: 20da2d54ea54674656b2c1006d094c63133baf79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72264487"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>Spuštění příkazů ovládacího prvku Azure Data Explorer pomocí aktivity příkazů Azure Data Factory

[Azure Data Factory](/azure/data-factory/) (ADF) je cloudová služba integrace dat, která umožňuje provádět kombinaci aktivit na datech. Pomocí adf můžete vytvářet pracovní postupy řízené daty pro orchestraci a automatizaci přesunu dat a transformace dat. Aktivita **příkazu Azure Data Explorer** v Azure Data Factory umožňuje spouštět [příkazy řízení Průzkumníka dat Azure](/azure/kusto/concepts/#control-commands) v rámci pracovního postupu ADF. Tento článek vás naučí, jak vytvořit kanál s vyhledávací aktivitou a aktivitou ForEach obsahující aktivitu příkazu Průzkumníka dat Azure.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Cluster a databáze Průzkumníka dat Azure](create-cluster-database-portal.md)
* Zdroj dat.
* [Továrna na data](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>Vytvoření nového kanálu

1. Vyberte nástroj **autora** tužky. 
1. Vytvořte nový kanál **+** výběrem a pak v rozevíracím seznamu vyberte **Pipeline.**

   ![vytvořit nový kanál](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>Vytvoření vyhledávací aktivity

[Vyhledávací aktivita](/azure/data-factory/control-flow-lookup-activity) můžete načíst datovou sadu z libovolného zdroje dat podporované Azure Data Factory. Výstup z aktivity vyhledávání lze použít v ForEach nebo jiné aktivity.

1. V podokně **Aktivity** vyberte v části **Obecné**aktivitu **vyhledávání.** Přetáhněte jej do hlavního plátna vpravo.
 
    ![vybrat vyhledávací aktivitu](media/data-factory-command-activity/select-activity.png)

1. Plátno nyní obsahuje vyhledávací aktivitu, kterou jste vytvořili. Pomocí karet pod plátnem můžete změnit všechny relevantní parametry. **Obecně platí**, přejmenujte aktivitu. 

    ![upravit vyhledávací aktivitu](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > Kliknutím na prázdnou oblast plátna zobrazíte vlastnosti kanálu. K přejmenování kanálu použijte kartu **Obecné.** Náš kanál se jmenuje *pipeline-4-docs*.

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>Vytvoření datové sady Průzkumníka dat Azure ve vyhledávací aktivitě

1. V **nastavení**vyberte předem vytvořenou **zdrojovou datovou sadu**Průzkumníka dat Azure nebo vyberte + **Nový** a vytvořte novou datovou sadu.
 
    ![přidání datové sady v nastavení vyhledávání](media/data-factory-command-activity/lookup-settings.png)

1. Vyberte datovou sadu **Průzkumníka dat Azure (Kusto)** z okna **Nová datová sada.** Výběrem **možnosti Pokračovat** přidejte novou datovou sadu.

   ![vybrat novou datovou sadu](media/data-factory-command-activity/select-new-dataset.png) 

1. Nové parametry datové sady Azure Data Explorer jsou viditelné v **nastavení**. Chcete-li parametry aktualizovat, vyberte **možnost Upravit**.

    ![nastavení vyhledávání pomocí datové sady Azure Data Explorer](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. Otevře se nová karta **AzureDataExplorerTable** na hlavním plátně. 
    * Vyberte **Obecné** a upravte název datové sady. 
    * Vyberte **Připojení,** chcete-li upravit vlastnosti datové sady. 
    * V rozevíracím souboru vyberte **službu Propojeno** nebo vyberte **+ Nový** a vytvořte novou propojenou službu.

    ![Úprava vlastností datové sady Průzkumníka dat Azure](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. Při vytváření nové propojené služby se otevře stránka **Nová propojená služba (Průzkumník dat Azure):**

    ![Nová propojená služba ADX](media/data-factory-command-activity/adx-new-linked-service.png)

   * Vyberte **Název** propojené služby Azure Data Explorer. V případě potřeby přidejte **popis.**
   * V **části Připojit pomocí integračního runtime**v případě potřeby změňte aktuální nastavení. 
   * V **metodě výběru účtu** vyberte cluster pomocí jedné ze dvou metod: 
        * Vyberte **přepínací** tlačítko Z předplatného Azure a vyberte účet **předplatného Azure.** Potom vyberte **cluster**. Všimněte si, že rozevírací seznam bude seznam pouze clustery, které patří uživateli.
        * Místo toho vyberte **Ručně** zadat přepínací tlačítko a zadejte **koncový bod** (adresu URL clusteru).
    * Zadejte **tenanta**.
    * Zadejte **ID instančního objektu**. ID objektu zabezpečení musí mít odpovídající oprávnění podle úrovně oprávnění vyžadované používaným příkazem.
    * Vyberte tlačítko **hlavního povinného servisu** a zadejte **hlavní klíč servisu**.
    * V rozevírací nabídce vyberte **databázi.** Případně zaškrtněte políčko **Upravit** a zadejte název databáze.
    * Vyberte **Testovat připojení,** chcete-li otestovat připojení propojené služby, které jste vytvořili. Pokud se můžete připojit k nastavení, zobrazí se zelená značka **Připojení jako úspěšné.**
    * Chcete-li dokončit vytvoření propojené služby, vyberte **možnost Dokončit.**

1. Po nastavení propojené služby v **azuredataexplorertable** > **připojení**, přidejte název **tabulky.** Vyberte **Náhled dat**, abyste se ujistili, že jsou data zobrazena správně.

   Vaše datová sada je nyní připravena a můžete pokračovat v úpravách kanálu.

### <a name="add-a-query-to-your-lookup-activity"></a>Přidání dotazu k vyhledávací aktivitě

1. V**nastavení** **pipeline-4-docs** > přidejte dotaz do textového pole **Dotaz,** například:

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. Podle potřeby změňte **časový limit dotazu** nebo **žádné zkrácení** a **pouze první řádek vlastnosti.** V tomto toku zachováme výchozí **časový limit dotazu** a zrušit zaškrtnutí políček. 

    ![Konečné nastavení vyhledávací aktivity](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>Vytvoření aktivity pro každý 

[For-Each](/azure/data-factory/control-flow-for-each-activity) aktivita se používá k iterátu přes kolekci a spustit zadané aktivity ve smyčce. 

1. Nyní přidáte aktivitu Pro každý do kanálu. Tato aktivita bude zpracovávat data vrácená z aktivity vyhledávání. 
    * V podokně **Aktivity** vyberte v části **Iterace & podmíněných podmínek**aktivitu **ForEach** a přetáhněte ji do plátna.
    * Nakreslete čáru mezi výstupem aktivity vyhledávání a vstupem aktivity ForEach na plátně, která je spojuje.

        ![Aktivita ForEach](media/data-factory-command-activity/for-each-activity.png)

1.  Vyberte aktivitu ForEach na plátně. Na kartě **Nastavení** níže:
    * Zaškrtněte políčko **Sekvenční** pro sekvenční zpracování výsledků vyhledávání nebo ponechat nezaškrtnuté vytvořit paralelní zpracování.
    * Nastavit **počet dávek**.
    * V **části Položky**uveďte následující odkaz na výstupní hodnotu: * @activity('Lookup1').output.value*

       ![Nastavení aktivity ForEach](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>Vytvoření aktivity příkazu Průzkumníka dat Azure v rámci aktivity ForEach

1. Poklepáním na aktivitu ForEach na plátně ji otevřete na novém plátně a určete aktivity v rámci foreach.
1. V podokně **Aktivity** vyberte v části **Průzkumník dat Azure**aktivitu **příkazu Průzkumníka dat Azure** a přetáhněte ji na plátno.

    ![Aktivita příkazu Azure Data Explorer](media/data-factory-command-activity/adx-command-activity.png)

1.  Na kartě **Připojení** vyberte stejnou propojenou službu, která byla dříve vytvořena.

    ![karta připojení k aktivitě příkazu azure data explorer](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. Na kartě **Příkaz** zadejte následující příkaz:

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    **Příkaz** instruuje Azure Data Explorer exportovat výsledky daného dotazu do úložiště objektů blob v komprimovaném formátu. Spouští se asynchronně (pomocí asynchronního modifikátoru).
    Dotaz řeší sloupec databáze každého řádku ve výsledku aktivity vyhledávání. **Časový čas příkazu** může zůstat beze změny.

    ![aktivita příkazu](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > Aktivita příkazu má následující omezení:
    > * Omezení velikosti: velikost odezvy 1 MB
    > * Časový limit: 20 minut (výchozí), 1 hodina (maximálně).
    > * V případě potřeby můžete připojit dotaz k výsledku pomocí [AdminThenQuery](/azure/kusto/management/index#combining-queries-and-control-commands), chcete-li snížit výslednou velikost/čas.

1.  Teď je potrubí připraveno. Kliknutím na název kanálu se můžete vrátit do zobrazení hlavního kanálu.

    ![Kanál příkazů Průzkumníka dat Azure](media/data-factory-command-activity/adx-command-pipeline.png)

1. Před publikováním kanálu vyberte **Ladění.** Průběh kanálu lze sledovat na kartě **Výstup.**

    ![výstup aktivity příkazu průzkumníka dat Azure](media/data-factory-command-activity/command-activity-output.png)

1. Můžete **publikovat vše** a pak **přidat aktivační událost** pro spuštění kanálu. 

## <a name="control-command-outputs"></a>Řídicí výstupy příkazů

Struktura výstupu aktivity příkazu je podrobně popsána níže. Tento výstup lze použít další aktivity v kanálu.

### <a name="returned-value-of-a-non-async-control-command"></a>Vrácená hodnota příkazu neasynchronního řízení

V příkazu řízení bez asynchronního je struktura vrácené hodnoty podobná struktuře výsledku aktivity vyhledávání. Toto `count` pole označuje počet vrácených záznamů. Pole s `value` pevným polem obsahuje seznam záznamů. 

```json
{ 
    "count": "2", 
    "value": [ 
        { 
            "ExtentId": "1b9977fe-e6cf-4cda-84f3-4a7c61f28ecd", 
            "ExtentSize": 1214.0, 
            "CompressedSize": 520.0 
        }, 
        { 
            "ExtentId": "b897f5a3-62b0-441d-95ca-bf7a88952974", 
            "ExtentSize": 1114.0, 
            "CompressedSize": 504.0 
        } 
    ] 
} 
```
 
### <a name="returned-value-of-an-async-control-command"></a>Vrácená hodnota příkazu asynchronního ovládacího prvku

V příkazu asynchronního ovládacího prvku aktivita dotazování tabulka operací na pozadí, dokud asynchronní operace je dokončena nebo časový čas. Proto vrácená hodnota bude obsahovat výsledek `.show operations OperationId` pro danou **OperationId** vlastnost. Zkontrolujte hodnoty **vlastností Stav** a **Stav,** chcete-li ověřit úspěšné dokončení operace.

```json
{ 
    "count": "1", 
    "value": [ 
        { 
            "OperationId": "910deeae-dd79-44a4-a3a2-087a90d4bb42", 
            "Operation": "TableSetOrAppend", 
            "NodeId": "", 
            "StartedOn": "2019-06-23T10:12:44.0371419Z", 
            "LastUpdatedOn": "2019-06-23T10:12:46.7871468Z", 
            "Duration": "00:00:02.7500049", 
            "State": "Completed", 
            "Status": "", 
            "RootActivityId": "f7c5aaaf-197b-4593-8ba0-e864c94c3c6f", 
            "ShouldRetry": false, 
            "Database": "MyDatabase", 
            "Principal": "<some principal id>", 
            "User": "<some User id>" 
        } 
    ] 
}
``` 

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [zkopírovat data do Azure Data Exploreru pomocí Azure Data Factory](data-factory-load-data.md).
* Přečtěte si o používání [šablony Azure Data Factory pro hromadné kopírování z databáze do Průzkumníka dat Azure](data-factory-template.md).
