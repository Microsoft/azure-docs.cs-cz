---
title: Přesun dat z místního serveru SQL do SQL Azure s Azure Data Factory | Microsoft Docs
description: Nastavte kanál ADF, která vytvoří dvě aktivity migrace dat, které společně přesun dat na každý den mezi databází na místě a v cloudu.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 36837c83-2015-48be-b850-c4346aa5ae8a
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: e9f6de3d4f4f731c2e727889bef1aef129cb00bf
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838097"
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Přesun dat z místního serveru SQL do SQL Azure s Azure Data Factory
Toto téma ukazuje, jak pro přesun dat z databáze serveru SQL místní databázi SQL Azure přes Azure Blob Storage pomocí Azure Data Factory (ADF).

Tabulka, která shrnuje různé možnosti pro přesun dat do Azure SQL Database, najdete v části [přesun dat do Azure SQL Database pro Azure Machine Learning](move-sql-azure.md).

## <a name="intro"></a>Úvod: Co je ADF a pokud by ho použít k migraci dat?
Azure Data Factory je plně spravovaná Cloudová datová integrační služba, která orchestruje a automatizuje přesouvání a transformaci dat. Klíče koncept v modelu ADF je kanálu. Kanál je logické seskupení aktivit, z nichž každý definuje akce lze provádět na data obsažená v datových sadách. Propojené služby se používá k definování informace potřebné pro vytváření dat pro připojení k datové prostředky.

S ADF může být složené stávající služby zpracování dat do datových kanálů, které jsou vysoce dostupné a spravovaných v cloudu. Tyto kanály dat může být naplánovaná ingestování, Příprava, transformace, analyzovat a publikovat data a ADF spravuje a orchestruje komplexní dat a zpracování závislosti. Řešení můžete rychle vytvořené a nasazené v cloudu, připojení stále více využívají místní a zdroje dat v cloudu.

Zvažte použití ADF:

* Když dat je třeba průběžně migrovat v hybridním scénáři, který přistupuje k i místní a cloudové prostředky
* Pokud data je zpracován, nebo musí být změněna nebo mít obchodní logiky přidána při migraci.

ADF umožňuje na plánování a sledování úloh pomocí jednoduché skripty JSON, které spravují přesun dat v pravidelných intervalech. ADF má také další funkce, například podporu pro komplexní operace. Další informace o ADF, naleznete v dokumentaci na [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="scenario"></a>Tento scénář
Nemůžeme nastavit kanál ADF, která vytvoří dvě aktivity migrace dat. Společně se přesouvat data na každý den mezi místní databázi SQL a Azure SQL Database v cloudu. Jsou dvě aktivity:

* kopírování dat z databáze místní SQL Server k účtu Azure Blob Storage
* kopírování dat z účtu úložiště objektů Blob Azure do Azure SQL Database.

> [!NOTE]
> Postupy v tomto poli byly upraveny, z podrobnější kurzu poskytovaných týmem ADF: [přesun dat mezi místní zdroje a cloudu s Brána pro správu dat](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md) jsou odkazy na příslušné části tohoto tématu k dispozici v případě nutnosti.
>
>

## <a name="prereqs"></a>Požadavky
Tento kurz předpokládá, že máte:

* **Předplatné**. Pokud předplatné nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* **Účtu úložiště Azure**. Používáte účet úložiště Azure pro ukládání dat v tomto kurzu. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-create-storage-account.md#create-a-storage-account). Po vytvoření účtu úložiště je třeba získat klíč účtu, který se používá pro přístup k účtu. V tématu [Správa přístupových klíčů úložiště](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* Přístup **databáze Azure SQL**. Pokud je potřeba nastavit Azure SQL Database, tématu [Začínáme se službou Microsoft Azure SQL Database ](../../sql-database/sql-database-get-started.md) poskytuje informace o tom, jak zřídit novou instanci třídy Azure SQL Database.
* Nainstalovaný a nakonfigurovaný **prostředí Azure PowerShell** místně. Pokyny najdete v tématu [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

> [!NOTE]
> Tento postup používá [portál Azure](https://portal.azure.com/).
>
>

## <a name="upload-data"></a> Nahrát data do SQL serveru na místě
Používáme [datovou sadu NYC taxíkem](http://chriswhong.com/open-data/foil_nyc_taxi/) k předvedení proces migrace. Je NYC taxíkem datová sada k dispozici, jak je uvedeno v tomto příspěvku v úložišti objektů blob v Azure [NYC taxíkem Data](http://www.andresmh.com/nyctaxitrips/). Data má dva soubory, trip_data.csv souboru, který obsahuje podrobnosti o cestě, a trip_far.csv soubor, který obsahuje podrobnosti o tarif placené pro každou cestu. Ukázka a popis tyto soubory jsou uvedeny v [NYC taxíkem služebních cest datovou sadu popis](sql-walkthrough.md#dataset).

Můžete přizpůsobit postup uvedený v tomto poli na sadu svoje vlastní data, nebo postupujte podle kroků, jak je popsáno pomocí NYC taxíkem datovou sadu. Datová sada NYC taxíkem nahrát do místní databáze systému SQL Server, postupujte podle pokynů uvedených v [hromadně importovat Data do databáze serveru SQL](sql-walkthrough.md#dbload). Tyto pokyny jsou pro systém SQL Server na virtuální počítač Azure, ale postup pro odesílání na místní SQL Server je stejný.

## <a name="create-adf"></a> Vytvoření služby Azure Data Factory
Pokyny pro vytvoření nové Azure Data Factory a skupiny prostředků v [portál Azure](https://portal.azure.com/) jsou k dispozici [vytvoření služby Azure Data Factory](../../data-factory/v1/data-factory-build-your-first-pipeline-using-editor.md#create-a-data-factory). Název nové instance ADF *adfdsp* a pojmenujte vytvoření skupiny prostředků *adfdsprg*.

## <a name="install-and-configure-up-the-data-management-gateway"></a>Instalace a konfigurace se Brána pro správu dat
Povolit kanály v objektu pro vytváření dat Azure pro práci s SQL serveru místní, musíte ji přidat k objektu pro vytváření dat jako propojené služby. K vytvoření propojené služby SQL serveru místní, musíte:

* Stáhněte a nainstalujte Brána pro správu dat na místním počítači.
* Nakonfigurujte propojené služby pro místní zdroje dat pro použití brány.

Brána pro správu dat serializuje a deserializuje zdroj a jímka data v počítači, který je hostitelem.

Pokyny k instalaci a informace o Brána pro správu dat najdete v tématu [přesun dat mezi místní zdroje a cloudu s Brána pro správu dat](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md)

## <a name="adflinkedservices"></a>Vytvoření propojených služeb pro připojení ke zdrojům dat
Propojená služba definuje informace potřebné pro vytváření dat Azure pro připojení k prostředku data. Máme tři zdroje v tomto scénáři, pro které jsou potřeba propojené služby:

1. Místní SQL Server
2. Azure Blob Storage
3. Databáze SQL Azure

Podrobný postup pro vytvoření propojené služby je k dispozici v [vytvoření propojených služeb](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-linked-services).


## <a name="adf-tables"></a>Definovat a vytvářet tabulky určete, jak pro přístup k datové sady
Vytváření tabulek, které určují strukturu, umístění a dostupnost datové sady pomocí následujících postupů založených na skriptech. Soubory JSON se používají k definování tabulky. Další informace o struktuře těchto souborů najdete v tématu [datové sady](../../data-factory/v1/data-factory-create-datasets.md).

> [!NOTE]
> Musí provést `Add-AzureAccount` rutiny před provedením [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) rutiny a potvrďte, že je vybraný právo předplatné pro provedení příkazu. Dokumentaci této rutiny najdete v tématu [Add-AzureAccount](/powershell/module/azure/add-azureaccount?view=azuresmps-3.7.0).
>
>

Na základě JSON definice v tabulkách použijte tyto názvy:

* **název tabulky** v místním systému SQL server je *nyctaxi_data*
* **název kontejneru** ve službě Azure Blob Storage je účet *containername*  

Tři definice tabulek jsou potřeba pro tento kanál ADF:

1. [Místní tabulky SQL](#adf-table-onprem-sql)
2. [Tabulka objektů BLOB ](#adf-table-blob-store)
3. [SQL Azure Table](#adf-table-azure-sql)

> [!NOTE]
> Tyto postupy pomocí prostředí Azure PowerShell můžete definovat a vytvořit aktivity ADF. Ale tyto úkoly lze provést také pomocí portálu Azure. Podrobnosti najdete v tématu [vytvoření datových sad](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-datasets).
>
>

### <a name="adf-table-onprem-sql"></a>Místní tabulky SQL
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

Názvy sloupců sem nebyly zahrnuty. Můžete se na názvy sloupců zahrnutím je zde dílčí vyberte (podrobnosti najdete [ADF dokumentaci](../../data-factory/v1/data-factory-data-movement-activities.md) tématu.

Kopírování názvem definici JSON tabulky do souboru *onpremtabledef.json* souboru a uložte ho do vhodného umístění (zde předpokládá, že *C:\temp\onpremtabledef.json*). Vytvořte v tabulce v ADF pomocí následující rutiny prostředí Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="adf-table-blob-store"></a>Tabulka objektů BLOB
Definice tabulky pro výstupní umístění objektu blob je v následujícím (mapuje ingestovaný data z místně do objektu blob Azure):

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

Kopírování názvem definici JSON tabulky do souboru *bloboutputtabledef.json* souboru a uložte ho do vhodného umístění (zde předpokládá, že *C:\temp\bloboutputtabledef.json*). Vytvořte v tabulce v ADF pomocí následující rutiny prostředí Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

### <a name="adf-table-azure-sql"></a>SQL Azure Table
Definice tabulky SQL Azure výstupu v následujícím (toto schéma mapuje dat pocházejících z objektu blob):

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

Kopírování názvem definici JSON tabulky do souboru *AzureSqlTable.json* souboru a uložte ho do vhodného umístění (zde předpokládá, že *C:\temp\AzureSqlTable.json*). Vytvořte v tabulce v ADF pomocí následující rutiny prostředí Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  


## <a name="adf-pipeline"></a>Definovat a vytvořit kanál
Zadejte aktivity, které patří do tohoto kanálu a vytvoření kanálu pomocí následujících postupů založených na skriptech. Soubor JSON se používá k definování vlastností, kanálu.

* Skript předpokládá, že **název kanálu** je *AMLDSProcessPipeline*.
* Všimněte si také, že nastaví se periodicity kanálu, který má být provedeny na každý den a použít výchozí doba provádění úlohy (12: 00 UTC).

> [!NOTE]
> Následující postupy použijte prostředí Azure PowerShell definovat a vytvořit kanál ADF. Ale tento úkol můžete udělat taky pomocí portálu Azure. Podrobnosti najdete v tématu [vytvořit kanál](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-pipeline).
>
>

Pomocí definice tabulek, které jsou uvedeny dříve, definici kanálu pro ADF určen takto:

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

Kopírování volat tuto definici JSON kanálu do souboru *pipelinedef.json* souboru a uložte ho do vhodného umístění (zde předpokládá, že *C:\temp\pipelinedef.json*). Vytvoření kanálu v ADF pomocí následující rutiny prostředí Azure PowerShell:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="adf-pipeline-start"></a>Spuštění kanálu
Kanál můžete spustit nyní pomocí následujícího příkazu:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

*Počátečním* a *koncovým datem* parametr hodnoty se musí nahradit skutečnými daty, mezi kterými chcete kanál ke spuštění.

Po kanálu provede, byste měli vidět data z kontejneru vybraného pro tento objekt blob, jeden soubor za den zobrazí.

Všimněte si, že jsme nebyly využít funkce poskytované službou ADF kanálu data postupně. Další informace o tom, jak udělat toto a další funkce poskytované verzí ADF, najdete v článku [ADF dokumentaci](https://azure.microsoft.com/services/data-factory/).
