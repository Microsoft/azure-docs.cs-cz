---
title: Data SQL serveru do SQL Azure s Azure Data Factory - vědecké zpracování týmových dat
description: Nastavte kanál ADF, která vytvoří dvě aktivity migrace dat, které každý den společně přesun dat mezi databází v místním prostředí i v cloudu.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 87aa1c30bb567c6820e2d9ecacfc3f8cd2338339
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53137765"
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Přesun dat z místních SQL serveru do SQL Azure s Azure Data Factory

Tento článek ukazuje, jak pro přesun dat z databáze v místním SQL serveru do databáze SQL Azure prostřednictvím služby Azure Blob Storage pomocí Azure Data Factory (ADF).

Tabulka, která shrnuje různé možnosti pro přesun dat do služby Azure SQL Database, najdete v části [přesun dat do služby Azure SQL Database pro Azure Machine Learning](move-sql-azure.md).

## <a name="intro"></a>Představení: Co je ADF a kdy je vhodné ji používat k migraci dat?
Azure Data Factory je služba pro integraci plně spravované cloudové data, která orchestruje a automatizuje přesouvání a transformaci dat. Klíčovým konceptem sady modelu ADF je kanál. Kanál je logické seskupení aktivit, z nichž každý definuje akce provést na data obsažená v datových sadách. Propojené služby slouží k definování informace potřebné pro připojení k prostředkům datové služby Data Factory.

Data pomocí ADF se může skládat stávající služby zpracování dat do datové kanály, které jsou vysoce dostupné a spravovaná v cloudu. Tyto datové kanály můžete naplánovat ingestování, Příprava, transformace, analýzy a publikovat data a ADF spravuje a orchestruje komplexních datových a zpracování závislostí. Řešení může být rychle sestavíte a nasadíte v cloudu, připojení stále většího počtu místních a cloudových zdrojů dat.

Zvažte použití ADF:

* Když data musí být neustále migrovat v hybridní scénář, který přistupuje k i v místním a cloudovým prostředkům
* Když data je zpracováván jako transakce nebo se musí změnit nebo mít obchodní logiky do ní přidat při migraci.

ADF umožňuje pro plánování a monitorování úloh pomocí jednoduchých skriptů JSON, které spravují pohybu dat v pravidelných intervalech. ADF má také další funkce, jako třeba podporu pro složité operace. Další informace o ADF, naleznete v dokumentaci na [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="scenario"></a>Tento scénář
Nastavíme kanál ADF, který lze kombinovat dvěma aktivitami data migrace. Společně se přesun dat každý den mezi službou v místním SQL database a Azure SQL Database v cloudu. Jsou dvě aktivity:

* kopírování dat z místní databáze systému SQL Server do účtu služby Azure Blob Storage
* kopírování dat z účtu služby Azure Blob Storage do služby Azure SQL Database.

> [!NOTE]
> Postup je znázorněno zde byly upraveny z podrobnější kurzu poskytované týmem ADF: [kopírování dat z místní databáze systému SQL Server do Azure Blob storage](https://docs.microsoft.com/en-us/azure/data-factory/tutorial-hybrid-copy-portal/) odkazy na relevantní části tohoto tématu jsou k dispozici při je to vhodné.
>
>

## <a name="prereqs"></a>Požadavky
Tento kurz předpokládá, že máte:

* **Předplatného Azure**. Pokud předplatné nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* **Účtu služby Azure storage**. Pro ukládání dat v tomto kurzu použijete účet úložiště Azure. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-quickstart-create-account.md). Po vytvoření účtu úložiště je třeba získat klíč účtu, který se používá pro přístup k účtu. Zobrazit [Správa přístupových klíčů úložiště](../../storage/common/storage-account-manage.md#access-keys).
* Přístup **Azure SQL Database**. Pokud musíte nastavit Azure SQL Database, tématu [Začínáme s Microsoft Azure SQL Database ](../../sql-database/sql-database-get-started.md) poskytuje informace o tom, jak zřídit novou instanci služby Azure SQL Database.
* Nainstalovaný a nakonfigurovaný **prostředí Azure PowerShell** místně. Pokyny najdete v tématu [instalace a konfigurace Azure Powershellu](/powershell/azure/overview).

> [!NOTE]
> Tento postup používá [webu Azure portal](https://portal.azure.com/).
>
>

## <a name="upload-data"></a> Nahrání dat do SQL serveru v místním
Používáme [datovou sadu NYC taxislužby](http://chriswhong.com/open-data/foil_nyc_taxi/) k předvedení proces migrace. Jak je uvedeno v tomto příspěvku v úložišti objektů blob v Azure je k dispozici, datová sada NYC taxislužby [Data taxislužby města NYC](http://www.andresmh.com/nyctaxitrips/). Data obsahují dva soubory, trip_data.csv soubor, který obsahuje podrobnosti o jízdách, a trip_far.csv soubor, který obsahuje podrobné informace o tarif placené pro každou cestu. Ukázka a popis tyto soubory jsou k dispozici v [NYC taxislužby zkracuje dobu odezvy datovou sadu popis](sql-walkthrough.md#dataset).

Můžete přizpůsobit postup uvedený tady na sadu vlastních dat nebo postupujte podle pokynů, jak je popsáno s použitím datové sady NYC taxislužby. K odeslání NYC taxislužby datovou sadu do místní databáze SQL serveru, postupujte podle postupu uvedeného v [hromadného importu dat do databáze SQL serveru](sql-walkthrough.md#dbload). Tyto pokyny jsou určené pro SQL Server na virtuálním počítači Azure, ale postup pro jeho odeslání do místního SQL serveru je stejný.

## <a name="create-adf"></a> Vytvoření služby Azure Data Factory
Pokyny pro vytvoření nové datové továrny Azure a skupiny prostředků [webu Azure portal](https://portal.azure.com/) jsou k dispozici [vytvořte datovou továrnu Azure](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory). Pojmenujte novou instanci ADF *adfdsp* a název skupiny prostředků, vytvoří *adfdsprg*.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Instalace a konfigurace modulu Runtime integrace pro Azure Data Factory 
Prostředí Integration Runtime je infrastruktura integrace spravovaných dat zákazníka službou Azure Data Factory používá k poskytování možnosti integrace dat napříč různými síťovými prostředími. Tento modul runtime byl dříve se označovaly jako "Brána správy dat". 

Chcete-li nastavit, [postupujte podle instrutions pro vytvoření kanálu](https://docs.microsoft.com/en-us/azure/data-factory/tutorial-hybrid-copy-portal#create-a-pipeline)

## <a name="adflinkedservices"></a>Vytvoření propojené služby pro připojení k datovým prostředkům
Propojená služba definuje informace potřebné pro připojení ke zdroji dat služby Azure Data Factory. Máme tři prostředky v tomto scénáři, které jsou v případě propojené služby:

1. Na místním SQL serveru
2. Azure Blob Storage
3. Databáze SQL Azure

Podrobný postup pro vytvoření propojené služby je součástí [vytvoříte propojené služby,](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).


## <a name="adf-tables"></a>Definování a vytvoření tabulky a určíte, jak získat přístup k datové sady
Vytváření tabulek, které určují strukturu, umístění a dostupnost datových sad pomocí následujících postupů založených na skriptech. Soubory JSON se používají k definování tabulky. Další informace o struktuře těchto souborů naleznete v tématu [datových sad](../../data-factory/concepts-datasets-linked-services.md).

> [!NOTE]
> By měl spustit `Add-AzureAccount` rutina před spuštěním [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) rutiny a potvrďte, že je pro spuštění příkazu vybrané předplatné přímo Azure. Dokumentace k této rutiny naleznete v tématu [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-3.7.0).
>
>

Definice založenými na JSON v tabulkách nepoužívejte následující názvy:

* **název tabulky** v místní SQL server je *nyctaxi_data*
* **název kontejneru** ve službě Azure Blob Storage je účet *containername*  

Tři tabulky definice, které jsou potřebné pro tento kanál ADF:

1. [On-premises tabulky SQL](#adf-table-onprem-sql)
2. [Tabulka objektů BLOB ](#adf-table-blob-store)
3. [SQL Azure Table](#adf-table-azure-sql)

> [!NOTE]
> Tyto postupy pomocí Azure Powershellu můžete definovat a vytvořit aktivity ADF. Ale tyto úkoly můžete provést také pomocí webu Azure portal. Podrobnosti najdete v tématu [vytvoření datových sad](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

### <a name="adf-table-onprem-sql"></a>On-premises tabulky SQL
Definice tabulky pro místní systém SQL Server je zadán v následujícím souboru JSON:

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

Názvy sloupců sem nebyly zahrnuty. Můžete zvolit dílčí na názvy sloupců uvedete tady (podrobnosti najdete [dokumentace ke službě ADF](../../data-factory/copy-activity-overview.md) tématu.

Zkopírujte definici JSON tabulky do souboru volá *onpremtabledef.json* soubor a uložte do vhodného umístění (zde předpokládá se, že *C:\temp\onpremtabledef.json*). Vytvoření tabulky ve službě ADF pomocí následující rutiny Azure Powershellu:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="adf-table-blob-store"></a>Tabulka objektů BLOB
Definice tabulky pro výstupní umístění objektu blob je v následujícím (mapuje přijatých dat z místních do objektu blob Azure):

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

Zkopírujte definici JSON tabulky do souboru volá *bloboutputtabledef.json* soubor a uložte do vhodného umístění (zde předpokládá se, že *C:\temp\bloboutputtabledef.json*). Vytvoření tabulky ve službě ADF pomocí následující rutiny Azure Powershellu:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

### <a name="adf-table-azure-sql"></a>SQL Azure Table
Definice tabulky SQL Azure výstupu v následujícím (toto schéma mapování dat pocházejících z objektu blob):

    {
        "name": "OutputSQLAzureTable",
        "properties":
        {
            "structure":
            [
                { "name": "column1", type": "String"},
                { "name": "column2", type": "String"}                
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

Zkopírujte definici JSON tabulky do souboru volá *AzureSqlTable.json* soubor a uložte do vhodného umístění (zde předpokládá se, že *C:\temp\AzureSqlTable.json*). Vytvoření tabulky ve službě ADF pomocí následující rutiny Azure Powershellu:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  


## <a name="adf-pipeline"></a>Definovat a vytvořit kanál
Zadejte aktivity, které patří do tohoto kanálu a vytvoříte kanál pomocí následujících postupů založených na skriptech. Soubor JSON se používá k definování vlastností kanálu.

* Skript předpokládá, že **názvu kanálu** je *AMLDSProcessPipeline*.
* Všimněte si také, že nastavení periodicita kanálu pro spuštění na každý den a použijte výchozí dobu provádění úlohy (standard UTC 00: 00).

> [!NOTE]
> Následující postupy používají prostředí Azure PowerShell k definování a vytvoření kanálu ADF. Ale tuto úlohu lze provést také pomocí webu Azure portal. Podrobnosti najdete v tématu [vytvořit kanál](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

Pomocí výše uvedené tabulce definice, definice kanálu ADF určena následujícím způsobem:

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

Kopírování volat tuto definici JSON kanálu do souboru *pipelinedef.json* soubor a uložte do vhodného umístění (zde předpokládá se, že *C:\temp\pipelinedef.json*). Vytvoření kanálu ve službě ADF pomocí následující rutiny Azure Powershellu:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="adf-pipeline-start"></a>Spuštění kanálu
Kanálu se teď dají spouštět pomocí následujícího příkazu:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

*Startdate* a *enddate* hodnoty parametrů, třeba nahradit skutečnými daty, mezi kterými chcete kanál spustit.

Jakmile se spustí kanál byste měli vidět data zobrazí v kontejneru vybrané pro tento objekt blob, souborů za den.

Všimněte si, že jsme ještě využít funkce poskytované službou ADF kanálu dat přírůstkově. Další informace o tom, jak provést toto a další funkce poskytované ADF, najdete v článku [dokumentace ke službě ADF](https://azure.microsoft.com/services/data-factory/).
