---
title: Data SQL serveru do SQL Azure s Azure Data Factory - vědecké zpracování týmových dat
description: Nastavte kanál ADF, která vytvoří dvě aktivity migrace dat, které každý den společně přesun dat mezi databází v místním prostředí i v cloudu.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8f696f1c6c414cd9db082e79e0f34c56156e1ee0
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722488"
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Přesun dat z místních SQL serveru do SQL Azure s Azure Data Factory

V tomto článku se dozvíte, jak přesunout data z místní databáze SQL Server do databáze SQL Azure prostřednictvím služby Azure Blob Storage pomocí Azure Data Factory (ADF): Tato metoda je podporovaným starším přístupem, který má výhody replikované pracovní kopie, ale [doporučujeme podívat se na stránku migrace dat](https://datamigration.microsoft.com/scenario/sql-to-azuresqldb?step=1), kde najdete nejnovější možnosti.

Tabulka, která shrnuje různé možnosti přesunutí dat na Azure SQL Database, najdete v tématu [přesun dat do Azure SQL Database Azure Machine Learning](move-sql-azure.md).

## <a name="intro"></a>Úvod: co je ADF a kdy by se měla použít k migraci dat?
Azure Data Factory je služba pro integraci plně spravované cloudové data, která orchestruje a automatizuje přesouvání a transformaci dat. Klíčovým konceptem sady modelu ADF je kanál. Kanál je logické seskupení aktivit, z nichž každý definuje akce provést na data obsažená v datových sadách. Propojené služby slouží k definování informace potřebné pro připojení k prostředkům datové služby Data Factory.

Data pomocí ADF se může skládat stávající služby zpracování dat do datové kanály, které jsou vysoce dostupné a spravovaná v cloudu. Tyto datové kanály můžete naplánovat ingestování, Příprava, transformace, analýzy a publikovat data a ADF spravuje a orchestruje komplexních datových a zpracování závislostí. Řešení může být rychle sestavíte a nasadíte v cloudu, připojení stále většího počtu místních a cloudových zdrojů dat.

Zvažte použití ADF:

* Když data musí být neustále migrovat v hybridní scénář, který přistupuje k i v místním a cloudovým prostředkům
* Když data potřebují transformace nebo když se k ní přidají obchodní logika při migraci.

ADF umožňuje pro plánování a monitorování úloh pomocí jednoduchých skriptů JSON, které spravují pohybu dat v pravidelných intervalech. ADF má také další funkce, jako třeba podporu pro složité operace. Další informace o ADF najdete v dokumentaci na adrese [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="scenario"></a>Scénář
Nastavíme kanál ADF, který lze kombinovat dvěma aktivitami data migrace. Společně přesouvá data na denní bázi mezi místními SQL Database a Azure SQL Database v cloudu. Jsou dvě aktivity:

* kopírování dat z místní databáze systému SQL Server do účtu služby Azure Blob Storage
* kopírování dat z účtu služby Azure Blob Storage do služby Azure SQL Database.

> [!NOTE]
> Výše uvedené kroky se přizpůsobují z podrobnějšího kurzu, který poskytuje tým ADF: [kopírování dat z místní SQL serverové databáze do Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal/) odkazy na relevantní části tohoto tématu jsou uvedené v případě potřeby.
>
>

## <a name="prereqs"></a>Požadavky
Tento kurz předpokládá, že máte:

* **Předplatné Azure**. Pokud předplatné nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* **Účet služby Azure Storage**. Pro ukládání dat v tomto kurzu použijete účet úložiště Azure. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md). Po vytvoření účtu úložiště je třeba získat klíč účtu, který se používá pro přístup k účtu. Viz [Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md).
* Přístup k **Azure SQL Database**. Pokud musíte nastavit Azure SQL Database, téma [Začínáme s Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) poskytuje informace o tom, jak zřídit novou instanci Azure SQL Database.
* Instalace a konfigurace **Azure PowerShell** lokálně. Pokyny najdete v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/overview).

> [!NOTE]
> Tento postup používá [Azure Portal](https://portal.azure.com/).
>
>

## <a name="upload-data"></a>Nahrajte data do místních SQL Server
K předvedení procesu migrace používáme [datovou sadu NYC taxislužby](https://chriswhong.com/open-data/foil_nyc_taxi/) . Datová sada taxislužby NYC je k dispozici, jak je uvedeno v tomto příspěvku, v datech Azure Blob Storage [NYC taxislužby](https://www.andresmh.com/nyctaxitrips/). Data obsahují dva soubory, trip_data.csv soubor, který obsahuje podrobnosti o jízdách, a trip_far.csv soubor, který obsahuje podrobné informace o tarif placené pro každou cestu. Ukázka a popis těchto souborů jsou k dispozici v [popisu datové sady NYC taxislužby TRIPS](sql-walkthrough.md#dataset).

Můžete přizpůsobit postup uvedený tady na sadu vlastních dat nebo postupujte podle pokynů, jak je popsáno s použitím datové sady NYC taxislužby. Pokud chcete nahrát datovou sadu taxislužby NYC do místní databáze SQL Server, postupujte podle pokynů uvedených v [hromadném importu dat do databáze SQL Server](sql-walkthrough.md#dbload). Tyto pokyny jsou určené pro SQL Server na virtuálním počítači Azure, ale postup pro jeho odeslání do místního SQL serveru je stejný.

## <a name="create-adf"></a>Vytvoření Azure Data Factory
K dispozici jsou pokyny pro vytvoření nové Azure Data Factory a skupiny prostředků v [Azure Portal](https://portal.azure.com/) [Vytvoření Azure Data Factory](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory). Pojmenujte novou instanci ADF *adfdsp* a pojmenujte skupinu prostředků, kterou vytvořila *adfdsprg*.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Instalace a konfigurace modulu Runtime integrace pro Azure Data Factory
Integration Runtime je infrastruktura pro integraci dat spravovaná zákazníky, kterou používá Azure Data Factory k poskytování možností integrace dat napříč různými síťovými prostředími. Tento modul runtime byl dříve se označovaly jako "Brána správy dat".

Pokud chcete nastavit, [postupujte podle pokynů pro vytvoření kanálu](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal#create-a-pipeline) .

## <a name="adflinkedservices"></a>Vytvoření propojených služeb pro připojení k datovým prostředkům
Propojená služba definuje informace potřebné pro připojení ke zdroji dat služby Azure Data Factory. Máme tři prostředky v tomto scénáři, které jsou v případě propojené služby:

1. Na místním SQL serveru
2. Azure Blob Storage
3. Azure SQL Database

Podrobný postup pro vytváření propojených služeb je k dispozici v části [Vytvoření propojených služeb](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).


## <a name="adf-tables"></a>Definování a vytvoření tabulek pro určení způsobu přístupu k datovým sadám
Vytváření tabulek, které určují strukturu, umístění a dostupnost datových sad pomocí následujících postupů založených na skriptech. Soubory JSON se používají k definování tabulky. Další informace o struktuře těchto souborů naleznete v tématu [datové sady](../../data-factory/concepts-datasets-linked-services.md).

> [!NOTE]
> Před spuštěním rutiny [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) byste měli spustit rutinu `Add-AzureAccount` a ověřit tak, že pro provádění příkazu bylo vybrané správné předplatné Azure. Dokumentaci k této rutině najdete v tématu [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-3.7.0).
>
>

Definice založenými na JSON v tabulkách nepoužívejte následující názvy:

* **název tabulky** v místním SQL serveru je *nyctaxi_data*
* **název kontejneru** v účtu Azure Blob Storage je *ContainerName* .

Tři tabulky definice, které jsou potřebné pro tento kanál ADF:

1. [Místní tabulka SQL](#adf-table-onprem-sql)
2. [Tabulka objektů BLOB](#adf-table-blob-store)
3. [SQL Azure tabulka](#adf-table-azure-sql)

> [!NOTE]
> Tyto postupy pomocí Azure Powershellu můžete definovat a vytvořit aktivity ADF. Ale tyto úkoly můžete provést také pomocí webu Azure portal. Podrobnosti najdete v tématu [vytvoření datových sad](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

### <a name="adf-table-onprem-sql"></a>Místní tabulka SQL
Definice tabulky pro místní systém SQL Server je zadán v následujícím souboru JSON:

```json
{
    "name": "OnPremSQLTable",
    "properties":
    {
        "location":
        {
            "type": "OnPremisesSqlServerTableLocation",
            "tableName": "nyctaxi_data",
            "linkedServiceName": "adfonpremsql"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1,
            "waitOnExternal":
            {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Názvy sloupců sem nebyly zahrnuty. Na názvy sloupců můžete vybrat jejich výběr tak, že je sem zahrnete (podrobnosti najdete v tématu [dokumentace k ADF](../../data-factory/copy-activity-overview.md) .

Zkopírujte definici JSON tabulky do souboru s názvem *onpremtabledef. JSON* a uložte ho do známého umístění (tady se předpokládá, že bude *C:\temp\onpremtabledef.JSON*). Vytvoření tabulky ve službě ADF pomocí následující rutiny Azure Powershellu:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="adf-table-blob-store"></a>Tabulka objektů BLOB
Definice tabulky pro výstupní umístění objektu blob je v následujícím (mapuje přijatých dat z místních do objektu blob Azure):

```json
{
    "name": "OutputBlobTable",
    "properties":
    {
        "location":
        {
            "type": "AzureBlobLocation",
            "folderPath": "containername",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            },
            "linkedServiceName": "adfds"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Zkopírujte definici JSON tabulky do souboru s názvem *bloboutputtabledef. JSON* a uložte ho do známého umístění (tady se předpokládá, že bude *C:\temp\bloboutputtabledef.JSON*). Vytvoření tabulky ve službě ADF pomocí následující rutiny Azure Powershellu:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json

### <a name="adf-table-azure-sql"></a>SQL Azure tabulka
Definice tabulky SQL Azure výstupu v následujícím (toto schéma mapování dat pocházejících z objektu blob):

```json
{
    "name": "OutputSQLAzureTable",
    "properties":
    {
        "structure":
        [
            { "name": "column1", "type": "String"},
            { "name": "column2", "type": "String"}
        ],
        "location":
        {
            "type": "AzureSqlTableLocation",
            "tableName": "your_db_name",
            "linkedServiceName": "adfdssqlazure_linked_servicename"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Zkopírujte definici JSON tabulky do souboru s názvem *AzureSqlTable. JSON* a uložte ho do známého umístění (tady se předpokládá, že bude *C:\temp\AzureSqlTable.JSON*). Vytvoření tabulky ve službě ADF pomocí následující rutiny Azure Powershellu:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json


## <a name="adf-pipeline"></a>Definování a vytvoření kanálu
Zadejte aktivity, které patří do tohoto kanálu a vytvoříte kanál pomocí následujících postupů založených na skriptech. Soubor JSON se používá k definování vlastností kanálu.

* Skript předpokládá, že **název kanálu** je *AMLDSProcessPipeline*.
* Všimněte si také, že nastavení periodicita kanálu pro spuštění na každý den a použijte výchozí dobu provádění úlohy (standard UTC 00: 00).

> [!NOTE]
> Následující postupy používají prostředí Azure PowerShell k definování a vytvoření kanálu ADF. Ale tuto úlohu lze provést také pomocí webu Azure portal. Podrobnosti najdete v tématu [vytvoření kanálu](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

Pomocí výše uvedené tabulce definice, definice kanálu ADF určena následujícím způsobem:

```json
{
    "name": "AMLDSProcessPipeline",
    "properties":
    {
        "description" : "This pipeline has one Copy activity that copies data from an on-premises SQL to Azure blob",
        "activities":
        [
            {
                "name": "CopyFromSQLtoBlob",
                "description": "Copy data from on-premises SQL server to blob",
                "type": "CopyActivity",
                "inputs": [ {"name": "OnPremSQLTable"} ],
                "outputs": [ {"name": "OutputBlobTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from nyctaxi_data"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            },
            {
                "name": "CopyFromBlobtoSQLAzure",
                "description": "Push data to Sql Azure",
                "type": "CopyActivity",
                "inputs": [ {"name": "OutputBlobTable"} ],
                "outputs": [ {"name": "OutputSQLAzureTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "BlobSource"
                    },
                    "sink":
                    {
                        "type": "SqlSink",
                        "WriteBatchTimeout": "00:5:00",
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 2,
                    "timeout": "02:00:00"
                }
            }
        ]
    }
}
```

Zkopírujte tuto definici JSON kanálu do souboru s názvem *pipelinedef. JSON* a uložte ho do známého umístění (tady se předpokládá *C:\temp\pipelinedef.JSON*). Vytvoření kanálu ve službě ADF pomocí následující rutiny Azure Powershellu:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="adf-pipeline-start"></a>Spuštění kanálu
Kanálu se teď dají spouštět pomocí následujícího příkazu:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

Hodnoty parametrů *StartDate* a *EndDate* je třeba nahradit skutečnými daty, mezi kterými chcete spustit kanál.

Jakmile se spustí kanál byste měli vidět data zobrazí v kontejneru vybrané pro tento objekt blob, souborů za den.

Nevyužili jsme funkci, kterou poskytuje ADF pro přírůstková data. Další informace o tom, jak provést tuto a další funkce, které poskytuje ADF, najdete v [dokumentaci k ADF](https://azure.microsoft.com/services/data-factory/).
