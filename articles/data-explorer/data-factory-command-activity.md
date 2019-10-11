---
title: Použití příkazů řízení Azure Průzkumník dat v Azure Data Factory
description: V tomto tématu použijte příkazy pro řízení Průzkumník dat Azure v Azure Data Factory
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: 20da2d54ea54674656b2c1006d094c63133baf79
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264487"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>Použití aktivity Azure Data Factory příkazu pro spuštění příkazů Azure Průzkumník dat Control

[Azure Data Factory](/azure/data-factory/) (ADF) je cloudová služba pro integraci dat, která umožňuje provádět kombinaci aktivit s daty. Pomocí ADF můžete vytvářet pracovní postupy řízené daty pro orchestraci a automatizaci přesunu dat a transformaci dat. Aktivita **příkazu azure Průzkumník dat** v Azure Data Factory umožňuje spouštět [příkazy řízení služby Azure Průzkumník dat](/azure/kusto/concepts/#control-commands) v rámci pracovního postupu ADF. V tomto článku se naučíte, jak vytvořit kanál s aktivitou vyhledávání a aktivitou ForEach, která obsahuje aktivitu příkazu Azure Průzkumník dat.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Cluster a databáze Azure Průzkumník dat](create-cluster-database-portal.md)
* Zdroj dat.
* [Datová továrna](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>Vytvořit nový kanál

1. Vyberte nástroj pro **tvorbu** tužky. 
1. Vytvořte nový kanál výběrem **+** a pak v rozevíracím seznamu vyberte **kanál** .

   ![vytvořit nový kanál](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>Vytvoření aktivity vyhledávání

[Aktivita vyhledávání](/azure/data-factory/control-flow-lookup-activity) může načíst datovou sadu z libovolného zdroje dat podporovaného Azure Data Factory. Výstup aktivity vyhledávání lze použít v ForEach nebo jiné aktivitě.

1. V podokně **aktivity** v části **Obecné**vyberte aktivitu **vyhledávání** . Přetáhněte ho na hlavní plátno na pravé straně.
 
    ![vybrat aktivitu vyhledávání](media/data-factory-command-activity/select-activity.png)

1. Plátno teď obsahuje aktivitu vyhledávání, kterou jste vytvořili. Pomocí karet pod plátnem můžete změnit všechny relevantní parametry. V části **Obecné**přejmenujte aktivitu. 

    ![Upravit aktivitu vyhledávání](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > Kliknutím na prázdnou oblast plátna zobrazíte vlastnosti kanálu. K přejmenování kanálu použijte kartu **Obecné** . Náš kanál se nazývá *kanál-4-docs*.

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>Vytvoření datové sady Azure Průzkumník dat v aktivitě vyhledávání

1. V **Nastavení**vyberte předem vytvořenou **zdrojovou datovou sadu**Azure Průzkumník dat nebo vyberte **+ Nová** a vytvořte novou datovou sadu.
 
    ![Přidat datovou sadu v nastavení vyhledávání](media/data-factory-command-activity/lookup-settings.png)

1. V okně **Nová datová sada** vyberte datovou sadu **Azure Průzkumník dat (Kusto)** . Chcete-li přidat novou datovou sadu, vyberte **pokračovat** .

   ![vybrat novou datovou sadu](media/data-factory-command-activity/select-new-dataset.png) 

1. Nové parametry datové sady Azure Průzkumník dat jsou v **Nastavení**viditelné. Chcete-li aktualizovat parametry, vyberte možnost **Upravit**.

    ![nastavení vyhledávání s datovou sadou Azure Průzkumník dat](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. Otevře se nová karta **AzureDataExplorerTable** na hlavním plátně. 
    * Vyberte **Obecné** a upravte název datové sady. 
    * Vyberte **připojení** , chcete-li upravit vlastnosti datové sady. 
    * Vyberte **propojenou službu** z rozevíracího seznamu nebo vyberte **+ Nová** a vytvořte novou propojenou službu.

    ![Upravit vlastnosti datové sady Azure Průzkumník dat](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. Při vytváření nové propojené služby se otevře stránka **Nová propojená služba (Azure Průzkumník dat)** :

    ![Nová propojená služba ADX](media/data-factory-command-activity/adx-new-linked-service.png)

   * Vyberte **název** propojené služby Azure Průzkumník dat. V případě potřeby přidejte **Popis** .
   * V možnosti **připojit prostřednictvím prostředí Integration runtime**v případě potřeby změňte aktuální nastavení. 
   * V části **metoda výběru účtu** vyberte svůj cluster pomocí jedné ze dvou metod: 
        * Vyberte přepínač **z předplatného Azure** a vyberte svůj účet **předplatného Azure** . Pak vyberte svůj **cluster**. Poznámka: rozevírací seznam bude zobrazovat jenom clustery, které patří k uživateli.
        * Místo toho vyberte **zadat ručně** přepínač a zadejte **koncový bod** (URL clusteru).
    * Zadejte **tenanta**.
    * Zadejte **ID instančního objektu**. ID objektu zabezpečení musí mít odpovídající oprávnění podle úrovně oprávnění požadované použitým příkazem.
    * Vyberte tlačítko **klíč instančního objektu** a zadejte **klíč instančního objektu služby**.
    * Z rozevírací nabídky vyberte svou **databázi** . Případně můžete vybrat zaškrtávací políčko **Upravit** a zadat název databáze.
    * Vyberte **Test připojení** a otestujte připojení propojené služby, které jste vytvořili. Pokud se můžete připojit k instalaci, **zobrazí se zelený symbol zaškrtnutí.**
    * Vytvoření propojené služby dokončíte kliknutím na **Dokončit** .

1. Jakmile nanastavíte propojenou službu, v **AzureDataExplorerTable** **připojení** >  přidejte název **tabulky** . Vyberte **Náhled dat**, abyste se ujistili, že se data zobrazují správně.

   Vaše datová sada je teď připravená a můžete pokračovat v úpravách kanálu.

### <a name="add-a-query-to-your-lookup-activity"></a>Přidat dotaz do aktivity vyhledávání

1. V**Nastavení** **kanál-4-docs** >  přidejte do textového pole **dotazu** dotaz, například:

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. V případě potřeby změňte **časový limit dotazu** nebo pouze vlastnosti **zkrácení** a **první řádek** . V tomto toku zachováme výchozí **časový limit dotazu** a zrušíte zaškrtnutí těchto políček. 

    ![Konečné nastavení aktivity vyhledávání](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>Vytvoření aktivity for-each 

Aktivita [for-each](/azure/data-factory/control-flow-for-each-activity) se používá k iteraci v kolekci a provádění zadaných aktivit ve smyčce. 

1. Nyní do kanálu přidáte aktivitu for-each. Tato aktivita zpracuje data vrácená z aktivity vyhledávání. 
    * V podokně **aktivity** v části **iterace & Podmíněné podmínky**vyberte aktivitu **foreach** a přetáhněte ji na plátno.
    * Nakreslete čáru mezi výstupem aktivity vyhledávání a vstupem aktivity ForEach na plátně, abyste je připojili.

        ![Aktivita ForEach](media/data-factory-command-activity/for-each-activity.png)

1.  Vyberte aktivitu ForEach na plátně. Na kartě **Nastavení** níže:
    * Zaškrtněte políčko **sekvenční** pro sekvenční zpracování výsledků vyhledávání nebo ho nechte nezaškrtnuté, chcete-li vytvořit paralelní zpracování.
    * Nastavte **počet dávek**.
    * V části **položky**zadejte následující odkaz na výstupní hodnotu: *@activity (' Lookup1 '). Output. Value*

       ![Nastavení aktivity ForEach](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>Vytvoření aktivity příkazu Azure Průzkumník dat v rámci aktivity ForEach

1. Poklikejte na aktivitu ForEach na plátně a otevřete ji na novém plátně a určete aktivity v rámci ForEach.
1. V podokně **aktivity** v části **Azure Průzkumník dat**vyberte aktivitu **příkazu Azure Průzkumník dat** a přetáhněte ji na plátno.

    ![Aktivita příkazu Azure Průzkumník dat](media/data-factory-command-activity/adx-command-activity.png)

1.  Na kartě **připojení** vyberte dříve vytvořenou propojenou službu.

    ![karta připojení aktivity příkazu v Průzkumníku dat Azure](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. Na kartě **příkaz** zadejte následující příkaz:

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    **Příkaz** dá službě Azure Průzkumník dat pokyn Exportovat výsledky daného dotazu do úložiště objektů BLOB v komprimovaném formátu. Spouští se asynchronně (pomocí modifikátoru Async).
    Dotaz řeší sloupec databáze každého řádku ve výsledku hledání aktivity vyhledávání. **Časový limit příkazu** může být ponechán beze změny.

    ![aktivita příkazu](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > Aktivita příkazu má následující omezení:
    > * Omezení velikosti: 1 MB velikosti odpovědi
    > * Časový limit: 20 minut (výchozí), 1 hodina (maximum).
    > * V případě potřeby můžete k výsledku připojit dotaz pomocí [AdminThenQuery](/azure/kusto/management/index#combining-queries-and-control-commands)a snížit tak výslednou velikost a čas.

1.  Kanál je teď připravený. Kliknutím na název kanálu se můžete vrátit k hlavnímu Zobrazení kanálu.

    ![Kanál příkazů Azure Průzkumník dat](media/data-factory-command-activity/adx-command-pipeline.png)

1. Před publikováním kanálu vyberte **ladit** . Průběh kanálu lze monitorovat na kartě **výstup** .

    ![výstup aktivity příkazů v Průzkumníku dat Azure](media/data-factory-command-activity/command-activity-output.png)

1. Můžete **publikovat vše** a potom **Přidat aktivační událost** pro spuštění kanálu. 

## <a name="control-command-outputs"></a>Výstupy řídicích příkazů

Struktura výstupu aktivity příkazu je popsána níže. Tento výstup může použít další aktivita v kanálu.

### <a name="returned-value-of-a-non-async-control-command"></a>Vrácená hodnota řídicího příkazu, který není asynchronní

V neasynchronním řídicím příkazu je struktura vrácené hodnoty podobná struktuře výsledku vyhledávací aktivity. Pole `count` označuje počet vrácených záznamů. Pole s pevným polem `value` obsahuje seznam záznamů. 

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
 
### <a name="returned-value-of-an-async-control-command"></a>Vrácená hodnota příkazu asynchronního řízení

V příkazu asynchronního řízení se aktivita dotazuje tabulky operací na pozadí, dokud není asynchronní operace dokončena nebo vypršel časový limit. Proto Vrácená hodnota bude obsahovat výsledek `.show operations OperationId` pro danou vlastnost **OperationId** . Zkontrolujte hodnoty vlastností **stav** a **stav** , abyste ověřili úspěšné dokončení operace.

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

* Přečtěte si informace o tom, jak [Kopírovat data do Azure Průzkumník dat pomocí Azure Data Factory](data-factory-load-data.md).
* Přečtěte si o použití [šablony Azure Data Factory pro hromadné kopírování z databáze do Azure Průzkumník dat](data-factory-template.md).
