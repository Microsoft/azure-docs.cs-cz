---
title: Hromadná kopírování z databáze do Průzkumníka dat Azure pomocí šablony Azure Data Factory
description: V tomto článku se naučíte používat šablonu Azure Data Factory ke velkoobjemové kopírování z databáze do Průzkumníka dat Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 884f4e956b37c2def6c25d0acdf20f15eddf7767
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293551"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>Hromadná kopírování z databáze do Průzkumníka dat Azure pomocí šablony Azure Data Factory 

Azure Data Explorer je rychlá, plně spravovaná služba analýzy dat. Nabízí analýzu v reálném čase na velkých objemech dat, která streamují z mnoha zdrojů, jako jsou aplikace, weby a zařízení IoT. 

Chcete-li zkopírovat data z databáze v Oracle Server, Netezza, Teradata nebo SQL Server do Průzkumníka dat Azure, musíte načíst obrovské množství dat z více tabulek. Obvykle musí být data rozdělena do oddílů v každé tabulce, takže můžete načíst řádky s více vlákny paralelně z jedné tabulky. Tento článek popisuje šablonu, která se má použít v těchto scénářích.

[Šablony Azure Data Factory](/azure/data-factory/solution-templates-introduction) jsou předdefinované kanály Datové továrny. Tyto šablony vám pomohou rychle začít s aplikací Data Factory a zkrátit dobu vývoje projektů integrace dat. 

*Hromadnou kopii z databáze do* šablony Průzkumníka dat Azure vytvoříte pomocí *aktivit Vyhledávání* a *ProKaždý.* Pro rychlejší kopírování dat můžete pomocí šablony vytvořit mnoho kanálů na databázi nebo tabulku. 

> [!IMPORTANT]
> Ujistěte se, že používáte nástroj, který je vhodný pro množství dat, které chcete zkopírovat.
> * Pomocí šablony *Hromadné kopírování z databáze do Průzkumníka dat Azure* zkopírujte velké množství dat z databází, jako je SQL server a Google BigQuery, do Průzkumníka dat Azure. 
> * Pomocí [*nástroje Data Factory Copy Data Tool*](data-factory-load-data.md) zkopírujte několik tabulek s malým nebo středním množstvím dat do Průzkumníka dat Azure. 

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Cluster a databáze Průzkumníka dat Azure](create-cluster-database-portal.md).
* [Vytvořte datovou továrnu](data-factory-load-data.md#create-a-data-factory).
* Zdroj dat v databázi.

## <a name="create-controltabledataset"></a>Vytvořit sadu ControlTableDataset

*ControlTableDataset* označuje, jaká data budou zkopírována ze zdroje do cíle v kanálu. Počet řádků označuje celkový počet kanálů, které jsou potřebné ke kopírování dat. Sadu ControlTableDataset byste měli definovat jako součást zdrojové databáze.

Příklad formátu zdrojové tabulky serveru SQL Server je uveden v následujícím kódu:
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```

Prvky kódu jsou popsány v následující tabulce:

|Vlastnost  |Popis  | Příklad
|---------|---------| ---------|
|Partitionid   |  Pořadí kopírování | 1  |  
|SourceQuery   |  Dotaz, který označuje, která data budou zkopírována během běhu kanálu | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  Název cílové tabulky | MyAdxTable       |  

Pokud je vaše ControlTableDataset v jiném formátu, vytvořte pro svůj formát srovnatelnou sadu ControlTableDataset.

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>Použití šablony Hromadné kopírování z databáze do Průzkumníka dat Azure

1. V podokně **Začínáme** vyberte **Vytvořit kanál z předlohy** a otevřete podokno Galerie **šablon.**

    ![Podokno Azure Data Factory "Začínáme"](media/data-factory-template/adf-get-started.png)

1. Vyberte **šablonu Hromadné kopírování z databáze do Průzkumníka dat Azure.**
 
    ![Šablona "Hromadná kopie z databáze do Průzkumníka dat Azure"](media/data-factory-template/pipeline-from-template.png)

1.  V podokně **Hromadná kopie z databáze do Průzkumníka dat Azure** v části **Uživatelské vstupy**zadejte datové sady následujícím způsobem: 

    a. V rozevíracím seznamu **ControlTableDataset** vyberte propojenou službu do řídicí tabulky, která označuje, jaká data jsou zkopírována ze zdroje do cíle a kam budou umístěna do cíle. 

    b. V rozevíracím seznamu **SourceDataset** vyberte propojenou službu se zdrojovou databází. 

    c. V rozevíracím seznamu **AzureDataExplorerTable** vyberte tabulku Průzkumník a dat Azure. Pokud datová sada neexistuje, [vytvořte propojenou službu Azure Data Explorer](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) a přidejte datovou sadu.

    d. Vyberte **Použít tuto šablonu**.

    ![Podokno Hromadné kopírování z databáze do Průzkumníka dat Azure](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Vyberte oblast na plátně mimo aktivity, abyste měli přístup k kanálu šablony. Vyberte kartu **Parametry,** chcete-li zadat parametry tabulky, včetně **name** (název řídicí tabulky) a **výchozí hodnoty** (názvy sloupců).

    ![Parametry kanálu](media/data-factory-template/pipeline-parameters.png)

1.  V části **Vyhledávání**vyberte **Možnost GetPartitionList,** chcete-li zobrazit výchozí nastavení. Dotaz je vytvořen automaticky.
1.  Vyberte aktivitu příkazu **ForEachPartition**, vyberte kartu **Nastavení** a proveďte následující kroky:

    a. Do pole **Počet dávek** zadejte číslo od 1 do 50. Tento výběr určuje počet kanálů, které běží paralelně, dokud není dosaženo počtu řádků *ControlTableDataset.* 

    b. Chcete-li zajistit, aby dávky *do not* kanálu běžely paralelně, nezaškrtávejte políčko **Sekvenční.**

    ![ForEachPartition nastavení](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > Osvědčeným postupem je spouštět mnoho kanálů paralelně, aby bylo možné data zkopírovat rychleji. Chcete-li zvýšit efektivitu, rozdělte data ve zdrojové tabulce a přidělte jeden oddíl na kanál podle data a tabulky.

1. Vyberte **Ověřit vše,** chcete-li ověřit kanál Azure Data Factory, a pak výsledek zobrazte v podokně **Výstup ověření kanálu.**

    ![Ověřit kanály šablon](media/data-factory-template/validate-template-pipelines.png)

1. V případě potřeby vyberte **Ladění**a pak vyberte **Přidat aktivační událost** pro spuštění kanálu.

    ![Tlačítka "Ladění" a "Spustit kanál"](media/data-factory-template/trigger-run-of-pipeline.png)    

Nyní můžete šablonu použít k efektivnímu kopírování velkého množství dat z databází a tabulek.

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak [zkopírovat data do Azure Data Exploreru pomocí Azure Data Factory](data-factory-load-data.md).
* Přečtěte si o [konektoru Azure Data Explorer](/azure/data-factory/connector-azure-data-explorer) v Azure Data Factory.
* Přečtěte si o [dotazech Azure Data Explorer](/azure/data-explorer/web-query-data) pro dotazování dat.






