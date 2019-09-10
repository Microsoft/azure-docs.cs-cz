---
title: Použití šablony Azure Data Factory pro hromadné kopírování z databáze do Azure Průzkumník dat
description: V tomto tématu se naučíte používat šablonu Azure Data Factory pro hromadné kopírování z databáze do Azure Průzkumník dat
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 5a6aebd276ef8658da9ca763be7da5c38a9c772a
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873432"
---
# <a name="use-azure-data-factory-template-for-bulk-copy-from-database-to-azure-data-explorer"></a>Použití šablony Azure Data Factory pro hromadné kopírování z databáze do Azure Průzkumník dat

Azure Průzkumník dat je rychlá a plně spravovaná služba analýzy dat pro analýzu velkých objemů datových proudů dat z mnoha zdrojů, jako jsou aplikace, weby a zařízení IoT, a to v reálném čase. Azure Data Factory je plně spravovaná cloudová služba pro integraci dat. Pomocí služby Azure Data Factory můžete naplnit databázi Azure Průzkumník dat daty ze stávajícího systému a ušetřit čas při sestavování analytických řešení. 

[Šablony Azure Data Factory](/azure/data-factory/solution-templates-introduction) jsou předdefinované Azure Data Factory kanály, které umožňují rychle začít pracovat s Data Factory a zkrátit dobu vývoje pro sestavování projektů pro integraci dat. Šablona **hromadného kopírování z databáze do služby Azure Průzkumník dat** je vytvořena pomocí aktivit **vyhledávání** a **foreach** . Pomocí šablony můžete vytvořit mnoho kanálů na databázi nebo tabulku pro rychlejší kopírování dat. 

> [!IMPORTANT]
> * K kopírování velkých objemů dat z databází, jako je SQL Server a Google BigQuery, do Azure Průzkumník dat použijte šablonu **hromadného kopírování z databáze do azure Průzkumník dat** . 
> * Pomocí [Nástroje pro kopírování](data-factory-load-data.md) můžete zkopírovat pár tabulek s malým nebo středním objemem dat do Azure Průzkumník dat. 

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Cluster a databáze Azure Průzkumník dat](create-cluster-database-portal.md)
* [Vytvoření datové továrny](data-factory-load-data.md#create-a-data-factory)
* Zdroj dat v databázi

## <a name="create-controltabledataset"></a>Vytvořit ControlTableDataset

**ControlTableDataset** určuje, jaká data se zkopírují ze zdroje do cíle v kanálu. Počet řádků označuje celkový počet kanálů potřebných ke zkopírování dat. **ControlTableDataset** by měl být definován jako součást zdrojové databáze.

Příklad SQL Server formátu zdrojové tabulky:
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```
    
|Vlastnost  |Popis  | Příklad
|---------|---------| ---------|
|Oddílu   |   Kopírovat pořadí | 1  |  
|SourceQuery   |   dotaz, který označuje, která data se zkopírují během běhu kanálu | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  název cílové tabulky | MyAdxTable       |  

Pokud je vaše **ControlTableDataset** v jiném formátu, vytvořte si pro svůj formát srovnatelný **ControlTableDataset** .

## <a name="use-bulk-copy-from-database-to-azure-data-explorer-template"></a>Použití hromadného kopírování z databáze do Azure Průzkumník dat Template

1. Na stránce **Začínáme** vyberte dlaždici **vytvořit kanál ze šablony** nebo vyberte ikonu tužky (karta**Autor** ) na pravé > **+**  >  **kanálu ze šablony** a otevřete galerii šablon.

    ![Azure Data Factory začít](media/data-factory-template/adf-get-started.png)

1. Vyberte šablonu **hromadného kopírování z databáze do Azure Průzkumník dat**.
 
    ![Vybrat kanál ze šablony](media/data-factory-template/pipeline-from-template.png)

1.  V okně **hromadné kopírování z databáze do Azure Průzkumník dat** v rozevíracím seznamu vyberte existující datové sady. 

    * **ControlTableDataset** – propojená služba s řídicí tabulkou, která indikuje, jaká data se zkopírují ze zdroje do cíle a kam se umístí do cílového umístění. 
    * **SourceDataset** – propojená služba ke zdrojové databázi. 
    * **AzureDataExplorerTable** – tabulka Průzkumník dat Azure Pokud datová sada neexistuje, [vytvořte propojenou službu Azure Průzkumník dat](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) a přidejte datovou sadu.
    * Vyberte **použít tuto šablonu**.

    ![Konfigurace hromadné kopie šablony Azure Průzkumník dat](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Vyberte oblast na plátně mimo aktivity pro přístup k kanálu šablony. Vyberte **parametry** pro zadání parametrů tabulky včetně **názvu** (název řídicí tabulky) a **výchozí hodnoty** (názvy sloupců).

    ![Parametry kanálu](media/data-factory-template/pipeline-parameters.png)

1.  Pokud chcete zobrazit výchozí nastavení, vyberte aktivita vyhledávání **GetPartitionList**. Dotaz je automaticky vytvořen.
1.  Vyberte možnost aktivita příkazu **ForEachPartition**, vyberte **Nastavení** .
    * **Počet dávek**: Vyberte číslo z 1-50. Tento výběr určuje počet kanálů, které jsou spuštěny paralelně, dokud není dosaženo **ControlTableDataset** počtu řádků. 
    * Nevybírejte **sekvenční** políčko, aby se dávky kanálu spouštěly paralelně.

    ![Nastavení ForEachPartition](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > Osvědčeným postupem je paralelní spouštění řady kanálů, takže data je možné zkopírovat rychleji. Umožňuje rozdělit data ve zdrojové tabulce a přidělit oddíl na kanál podle data a tabulky, aby se zvýšila efektivita.

1. Vyberte **ověřit vše** a ověřte kanál ADF. Viz **výstup ověření kanálu**.

    ![Ověřit kanály šablon](media/data-factory-template/validate-template-pipelines.png)

1. V případě potřeby vyberte **ladění**a potom **přidejte aktivační událost** pro spuštění kanálu.

    ![Spustit kanál](media/data-factory-template/trigger-run-of-pipeline.png)    


Nyní můžete použít šablonu **hromadného kopírování z databáze do služby Azure Průzkumník dat** k efektivnímu kopírování velkých objemů dat z databází a tabulek.

## <a name="next-steps"></a>Další postup

* Přečtěte si informace o postupu [kopírování dat do Azure Průzkumník dat pomocí Azure Data Factory](data-factory-load-data.md).

* Přečtěte si informace o [konektoru Azure Průzkumník dat](/azure/data-factory/connector-azure-data-explorer) v Azure Data Factory.

* Přečtěte si o dotazech na [Azure Průzkumník dat](/azure/data-explorer/web-query-data) pro dotazování na data.






