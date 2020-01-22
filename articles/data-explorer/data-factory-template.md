---
title: Hromadné kopírování z databáze do Azure Průzkumník dat pomocí šablony Azure Data Factory
description: V tomto článku se naučíte používat šablonu Azure Data Factory pro hromadné kopírování z databáze do Azure Průzkumník dat
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 884f4e956b37c2def6c25d0acdf20f15eddf7767
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293551"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>Hromadné kopírování z databáze do Azure Průzkumník dat pomocí šablony Azure Data Factory 

Azure Průzkumník dat je rychlá, plně spravovaná služba pro analýzu dat. Nabízí analýzu v reálném čase u velkých objemů dat, která se streamují z mnoha zdrojů, jako jsou aplikace, weby a zařízení IoT. 

Pokud chcete kopírovat data z databáze v Oracle serveru, Netezza, Teradata nebo SQL Server do Azure Průzkumník dat, musíte načíst obrovský objem dat z více tabulek. Data musí být obvykle rozdělená do oddílů v každé tabulce, takže můžete načíst řádky s více vlákny paralelně z jedné tabulky. Tento článek popisuje šablonu, která se má použít v těchto scénářích.

[Šablony Azure Data Factory](/azure/data-factory/solution-templates-introduction) jsou předdefinované Data Factory kanály. Tyto šablony vám pomohou rychle začít s Data Factory a zkrátit dobu vývoje v projektech Integration data. 

*Hromadnou kopii z databáze můžete vytvořit do šablony Azure Průzkumník dat* pomocí aktivit *vyhledávání* a *foreach* . Pro rychlejší kopírování dat můžete použít šablonu k vytvoření mnoha kanálů na databázi nebo pro každou tabulku. 

> [!IMPORTANT]
> Nezapomeňte použít nástroj, který je vhodný pro množství dat, která chcete kopírovat.
> * K kopírování velkých objemů dat z databází, jako je SQL Server a Google BigQuery, do Azure Průzkumník dat použijte šablonu *hromadného kopírování z databáze do azure Průzkumník dat* . 
> * Pomocí [*nástroje Data Factory kopírování dat*](data-factory-load-data.md) můžete zkopírovat pár tabulek s malým nebo středním objemem dat do služby Azure Průzkumník dat. 

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Cluster a databáze Azure Průzkumník dat](create-cluster-database-portal.md).
* [Vytvořte datovou továrnu](data-factory-load-data.md#create-a-data-factory).
* Zdroj dat v databázi.

## <a name="create-controltabledataset"></a>Vytvořit ControlTableDataset

*ControlTableDataset* označuje, jaká data se zkopírují ze zdroje do cíle v kanálu. Počet řádků označuje celkový počet kanálů potřebných ke zkopírování dat. ControlTableDataset byste měli definovat jako součást zdrojové databáze.

Příklad formátu zdrojové tabulky SQL Server je zobrazen v následujícím kódu:
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```

Prvky kódu jsou popsány v následující tabulce:

|Vlastnost  |Popis  | Příklad:
|---------|---------| ---------|
|PartitionId   |  Pořadí kopírování | 1\. místo  |  
|SourceQuery   |  Dotaz, který určuje, která data budou kopírována během běhu kanálu | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  Název cílové tabulky | MyAdxTable       |  

Pokud je vaše ControlTableDataset v jiném formátu, vytvořte si pro svůj formát srovnatelný ControlTableDataset.

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>Použití hromadného kopírování z databáze do služby Azure Průzkumník dat Template

1. V podokně **Začínáme** vyberte **vytvořit kanál ze šablony** a otevřete tak podokno **Galerie šablon** .

    ![Podokno Azure Data Factory "začít znovu"](media/data-factory-template/adf-get-started.png)

1. Vyberte šablonu **hromadného kopírování z databáze do služby Azure Průzkumník dat** .
 
    ![Šablona "hromadné kopírování z databáze do Azure Průzkumník dat"](media/data-factory-template/pipeline-from-template.png)

1.  V podokně **hromadné kopírování z databáze do Azure Průzkumník dat** v části **uživatelské vstupy**určete své datové sady pomocí následujícího postupu: 

    a. V rozevíracím seznamu **ControlTableDataset** vyberte propojenou službu s řídicí tabulkou, která určuje, jaká data se zkopírují ze zdroje do cílového umístění a kam se umístí do cílového umístění. 

    b. V rozevíracím seznamu **SourceDataset** vyberte propojenou službu ke zdrojové databázi. 

    c. V rozevíracím seznamu **AzureDataExplorerTable** vyberte tabulku Azure Průzkumník dat. Pokud datová sada neexistuje, [vytvořte propojenou službu Azure Průzkumník dat](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) a přidejte datovou sadu.

    d. Vyberte **Použít tuto šablonu**.

    ![Podokno hromadné kopírování z databáze do Azure Průzkumník dat](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Vyberte oblast na plátně mimo aktivity pro přístup k kanálu šablony. Vyberte kartu **parametry** a zadejte parametry tabulky, včetně **názvu** (název řídicí tabulky) a **výchozí hodnoty** (názvy sloupců).

    ![Parametry kanálu](media/data-factory-template/pipeline-parameters.png)

1.  V části **vyhledávání**vyberte **GetPartitionList** a zobrazte výchozí nastavení. Dotaz je automaticky vytvořen.
1.  Vyberte aktivitu příkazu, **ForEachPartition**, vyberte kartu **Nastavení** a proveďte následující kroky:

    a. Do pole **počet dávek** zadejte číslo od 1 do 50. Tento výběr určuje počet kanálů, které běží paralelně, dokud nedosáhnete počtu *ControlTableDataset* řádků. 

    b. Chcete-li zajistit, aby byly dávky kanálu spouštěny paralelně *, nezaškrtávejte políčko* **sekvenční** .

    ![Nastavení ForEachPartition](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > Osvědčeným postupem je souběžné spouštění řady kanálů, aby bylo možné data rychleji kopírovat. Chcete-li zvýšit efektivitu, vytvořte oddíly dat ve zdrojové tabulce a přidělte jeden oddíl na kanál podle data a tabulky.

1. Vyberte možnost **ověřit vše** , pokud chcete ověřit kanál Azure Data Factory a pak zobrazte výsledek v podokně **výstup ověření kanálu** .

    ![Ověřit kanály šablon](media/data-factory-template/validate-template-pipelines.png)

1. V případě potřeby vyberte **ladit**a pak vyberte **Přidat Trigger** a spusťte tak kanál.

    ![Tlačítka ladit a spustit kanál](media/data-factory-template/trigger-run-of-pipeline.png)    

Šablonu teď můžete použít k efektivnímu kopírování velkých objemů dat z databází a tabulek.

## <a name="next-steps"></a>Další kroky

* Naučte se, jak [Kopírovat data do Azure Průzkumník dat pomocí Azure Data Factory](data-factory-load-data.md).
* Přečtěte si informace o [konektoru Azure Průzkumník dat](/azure/data-factory/connector-azure-data-explorer) v Azure Data Factory.
* Přečtěte si o dotazech na [Azure Průzkumník dat](/azure/data-explorer/web-query-data) pro dotazování na data.






