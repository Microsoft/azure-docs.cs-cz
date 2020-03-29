---
title: Data SQL Serveru do SQL Azure s Azure Data Factory – proces vědecké analýzy týmových dat
description: Nastavte kanál ADF, který obsahuje dvě aktivity migrace dat, které společně přesouvají data denně mezi místními databázemi a v cloudu.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722488"
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Přesunutí dat z místního serveru SQL do SQL Azure pomocí Azure Data Factory

Tento článek ukazuje, jak přesunout data z místní databáze SQL Serveru do databáze SQL Azure prostřednictvím azure blob storage pomocí Azure Data Factory (ADF): Tato metoda je podporovaná starší verze přístupu, který má výhody replikované pracovní kopie, i když [doporučujeme podívat se na naší stránce migrace dat pro nejnovější možnosti](https://datamigration.microsoft.com/scenario/sql-to-azuresqldb?step=1).

Tabulka, která shrnuje různé možnosti pro přesun dat do databáze Azure SQL, najdete v [tématu Přesun dat do Azure SQL Database for Azure Machine Learning](move-sql-azure.md).

## <a name="introduction-what-is-adf-and-when-should-it-be-used-to-migrate-data"></a><a name="intro"></a>Úvod: Co je ADF a kdy by měl být použit k migraci dat?
Azure Data Factory je plně spravovaná cloudová služba pro integraci dat, která organizuje a automatizuje pohyb a transformaci dat. Klíčovým konceptem v modelu ADF je kanál. Kanál je logické seskupení aktivit, z nichž každá definuje akce, které mají být v datech obsažených v datových sadách. Propojené služby se používají k definování informací potřebných pro data factory pro připojení k datovým prostředkům.

S ADF, stávající služby zpracování dat lze skládat do datových kanálů, které jsou vysoce dostupné a spravované v cloudu. Tyto datové kanály lze naplánovat na ingestování, přípravu, transformaci, analýzu a publikování dat a ADF spravuje a orchestruje komplexní data a závislosti zpracování. Řešení lze rychle sestavit a nasadit v cloudu a propojit tak rostoucí počet místních a cloudových zdrojů dat.

Zvažte použití ADF:

* když je třeba data průběžně migrovat v hybridním scénáři, který přistupuje k místním i cloudovým prostředkům
* když data potřebují transformace nebo do nich při migraci přidá obchodní logiku.

ADF umožňuje plánování a sledování úloh pomocí jednoduchých skriptů JSON, které řídí pohyb dat v pravidelných intervalech. ADF má také další funkce, jako je například podpora složitých operací. Další informace o adf, najdete v dokumentaci k [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="the-scenario"></a><a name="scenario"></a>Scénář
Vytvořili jsme kanál ADF, který skládá dvě aktivity migrace dat. Společně přesouvají data denně mezi místní databází SQL a databází Azure SQL v cloudu. Jedná se o tyto dvě činnosti:

* kopírování dat z místní databáze serveru SQL Server do účtu úložiště objektů blob Azure
* zkopírujte data z účtu Azure Blob Storage do databáze Azure SQL Database.

> [!NOTE]
> Kroky uvedené zde byly upraveny z podrobnější kurz poskytované týmem ADF: [Kopírování dat z místní databáze SQL Serveru do úložiště objektů Blob Azure](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal/) Odkazy na příslušné části tohoto tématu jsou k dispozici v případě potřeby.
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Požadavky
Tento kurz předpokládá, že máte:

* Předplatné **Azure**. Pokud nemáte předplatné, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* **Účet úložiště Azure**. Účet úložiště Azure použít pro ukládání dat v tomto kurzu. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md). Po vytvoření účtu úložiště je třeba získat klíč účtu, který se používá pro přístup k účtu. Viz [Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md).
* Přístup k **databázi Azure SQL**. Pokud musíte nastavit Azure SQL Database, téma [Začínáme s Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) obsahuje informace o tom, jak zřídit novou instanci Azure SQL Database.
* Nainstalované a nakonfigurované **Azure PowerShell** místně. Pokyny najdete v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).

> [!NOTE]
> Tento postup používá [portál Azure](https://portal.azure.com/).
>
>

## <a name="upload-the-data-to-your-on-premises-sql-server"></a><a name="upload-data"></a>Nahrání dat na místní SQL Server
K předvedení procesu migrace používáme [datovou sadu NYC Taxi.](https://chriswhong.com/open-data/foil_nyc_taxi/) Datová sada NYC Taxi je k dispozici, jak je uvedeno v tomto příspěvku, na azure blob úložiště [NYC Taxi Data](https://www.andresmh.com/nyctaxitrips/). Data mají dva soubory, soubor trip_data.csv, který obsahuje podrobnosti o cestě, a soubor trip_far.csv, který obsahuje podrobnosti o jízdném zaplacené za každou cestu. Ukázka a popis těchto souborů jsou uvedeny v [NYC Taxi Výlety Dataset Popis](sql-walkthrough.md#dataset).

Můžete buď upravit postup zde k dispozici sadu vlastních dat nebo postupujte podle pokynů popsaných pomocí datové sady NYC Taxi. Chcete-li nahrát datovou sadu NYC Taxi do místní databáze serveru SQL Server, postupujte podle postupu popsaného v části [Hromadná import dat do databáze serveru SQL Server](sql-walkthrough.md#dbload). Tyto pokyny jsou pro SQL Server na virtuálním počítači Azure, ale postup pro nahrávání do místního SQL Serveru je stejný.

## <a name="create-an-azure-data-factory"></a><a name="create-adf"></a>Vytvoření Azure Data Factory
Pokyny pro vytvoření nové Azure Data Factory a skupiny prostředků na [webu Azure Portal](https://portal.azure.com/) jsou k dispozici vytvořit Azure Data [Factory](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory). Pojmenujte novou instanci *ADF adfdsp* a pojmenujte skupinu prostředků *vytvořenou adfdsprg*.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Instalace a konfigurace runtime integrace Azure Data Factory
Integrační runtime je infrastruktura integrace dat spravovaná zákazníkem, kterou používá Azure Data Factory k poskytování možností integrace dat v různých síťových prostředích. Tento runtime byl dříve nazýván "Brána pro správu dat".

Chcete-li nastavit, [postupujte podle pokynů pro vytvoření kanálu](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal#create-a-pipeline)

## <a name="create-linked-services-to-connect-to-the-data-resources"></a><a name="adflinkedservices"></a>Vytvoření propojených služeb pro připojení k datovým prostředkům
Propojená služba definuje informace potřebné pro Azure Data Factory pro připojení k datovému prostředku. V tomto scénáři máme tři zdroje, pro které jsou potřeba propojené služby:

1. Místní SQL Server
2. Azure Blob Storage
3. Azure SQL Database

Postup krok za krokem pro vytváření propojených služeb je k dispozici v části [Vytvořit propojené služby](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).


## <a name="define-and-create-tables-to-specify-how-to-access-the-datasets"></a><a name="adf-tables"></a>Definování a vytváření tabulek pro určení způsobu přístupu k datovým souborům
Vytvořte tabulky, které určují strukturu, umístění a dostupnost datových sad pomocí následujících postupů založených na skriptech. JSON soubory se používají k definování tabulek. Další informace o struktuře těchto souborů naleznete v tématu [Datasets](../../data-factory/concepts-datasets-linked-services.md).

> [!NOTE]
> Měli byste `Add-AzureAccount` spustit rutinu před spuštěním rutiny [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) k potvrzení, že je vybráno správné předplatné Azure pro spuštění příkazu. Dokumentaci k této rutině najdete v [tématu Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-3.7.0).
>
>

Definice založené na JSON v tabulkách používají následující názvy:

* **Název tabulky** v místním serveru SQL je *nyctaxi_data*
* **Název kontejneru** v účtu úložiště objektů blob Azure je *název kontejneru*

Pro tento kanál ADF jsou potřeba tři definice tabulek:

1. [Místní tabulka SQL](#adf-table-onprem-sql)
2. [Tabulka objektů blob](#adf-table-blob-store)
3. [Tabulka SQL Azure](#adf-table-azure-sql)

> [!NOTE]
> Tyto postupy používají Azure PowerShell k definování a vytvoření aktivit adf. Ale tyto úkoly lze provést také pomocí portálu Azure. Podrobnosti naleznete v [tématu Vytváření datových sad](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

### <a name="sql-on-premises-table"></a><a name="adf-table-onprem-sql"></a>Místní tabulka SQL
Definice tabulky pro místní SQL Server je určena v následujícím souboru JSON:

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

Názvy sloupců zde nebyly zahrnuty. Názvy sloupců můžete podvýběrem zahrnout sem (podrobnosti naleznete v [tématu dokumentace ADF.](../../data-factory/copy-activity-overview.md)

Zkopírujte definici tabulky JSON do souboru s názvem *onpremtabledef.json* a uložte jej do známého umístění (zde se předpokládá, že je *C:\temp\onpremtabledef.json*). Vytvořte tabulku v podavač apodak ování pomocí následující rutiny Prostředí Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="blob-table"></a><a name="adf-table-blob-store"></a>Tabulka objektů blob
Definice pro tabulku pro umístění výstupního objektu blob je následující (to mapuje ingestovaná data z místního objektu blob Azure):

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

Zkopírujte definici tabulky JSON do souboru s názvem *bloboutputtabledef.json* a uložte ji do známého umístění (zde se předpokládá, že je *C:\temp\bloboutputtabledef.json*). Vytvořte tabulku v podavač apodak ování pomocí následující rutiny Prostředí Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json

### <a name="sql-azure-table"></a><a name="adf-table-azure-sql"></a>Tabulka SQL Azure
Definice pro tabulku pro výstup SQL Azure je v následujícím (toto schéma mapuje data pocházející z objektu blob):

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

Zkopírujte definici tabulky JSON do souboru s názvem *Soubor AzureSqlTable.json* a uložte jej do známého umístění (zde se předpokládá, že je *C:\temp\AzureSqlTable.json).* Vytvořte tabulku v podavač apodak ování pomocí následující rutiny Prostředí Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json


## <a name="define-and-create-the-pipeline"></a><a name="adf-pipeline"></a>Definování a vytvoření kanálu
Zadejte aktivity, které patří do kanálu a vytvořte kanál s následujícími postupy založenými na skriptu. K definování vlastností kanálu se používá soubor JSON.

* Skript předpokládá, že **název kanálu** je *AMLDSProcessPipeline*.
* Všimněte si také, že jsme nastavili periodicitu kanálu, který má být prováděn denně a použít výchozí čas spuštění úlohy (12 hodin UTC).

> [!NOTE]
> Následující postupy používají Azure PowerShell k definování a vytvoření kanálu ADF. Ale tento úkol lze provést také pomocí portálu Azure. Podrobnosti naleznete v [tématu Vytvoření kanálu](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

Pomocí definic tabulek uvedených dříve je definice kanálu pro podavač ADF určen takto:

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

Zkopírujte tuto definici kanálu JSON do souboru s názvem *pipelinedef.json* a uložte jej do známého umístění (zde se předpokládá, že je *C:\temp\pipelinedef.json).* Vytvořte kanál v podavač apodak s následující rutinou Prostředí Azure PowerShell:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="start-the-pipeline"></a><a name="adf-pipeline-start"></a>Spuštění kanálu
Kanál lze nyní spustit pomocí následujícího příkazu:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

Hodnoty *parametru počáteční ho a* *koncového data* musí být nahrazeny skutečnými daty, mezi kterými má být kanál spuštěn.

Po spuštění kanálu byste měli být schopni zobrazit data v kontejneru vybraném pro objekt blob, jeden soubor za den.

Nevyužili jsme funkce poskytované adf k datům potrubí postupně. Další informace o tom, jak to provést, a další funkce poskytované adf, naleznete v [dokumentaci ADF](https://azure.microsoft.com/services/data-factory/).
