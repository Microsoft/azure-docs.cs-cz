---
title: SQL Server dat pro SQL Database pomocí procesu vědeckého zpracování dat Azure Data Factory – tým
description: Nastavte kanál ADF, který bude vytvářet dvě aktivity migrace dat, které společně přesouvá data mezi databázemi místně a v cloudu.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/03/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 02fd6c1d4cbd1c2db287a38e086045042b5f220a
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93309533"
---
# <a name="move-data-from-a-sql-server-database-to-sql-database-with-azure-data-factory"></a>Přesunout data z databáze SQL Server do SQL Database pomocí Azure Data Factory

V tomto článku se dozvíte, jak přesunout data z databáze SQL Server do Azure SQL Database prostřednictvím služby Azure Blob Storage pomocí Azure Data Factory (ADF): Tato metoda je podporovaným starším přístupem, který má výhody replikované pracovní kopie, ale [doporučujeme podívat se na naši stránku migrace dat](https://datamigration.microsoft.com/scenario/sql-to-azuresqldb?step=1), kde najdete nejnovější možnosti.

Tabulka, která shrnuje různé možnosti přesunutí dat na Azure SQL Database, najdete v tématu [přesun dat do Azure SQL Database Azure Machine Learning](move-sql-azure.md).

## <a name="introduction-what-is-adf-and-when-should-it-be-used-to-migrate-data"></a><a name="intro"></a>Úvod: co je ADF a kdy by se měla použít k migraci dat?
Azure Data Factory je plně spravovaná cloudová služba pro integraci dat, která orchestruje a automatizuje přesun a transformaci dat. Klíčovým konceptem modelu ADF je kanál. Kanál je logické seskupení aktivit, z nichž každá definuje akce, které se mají provést na datech obsažených v datových sadách. Propojené služby se používají k definování informací potřebných pro připojení k datovým prostředkům Data Factory.

Pomocí ADF můžete existující služby zpracování dat sestavit do datových kanálů, které jsou vysoce dostupné a spravované v cloudu. Tyto datové kanály je možné naplánovat na ingestování, přípravu, transformaci, analýzu a publikování dat a ADF spravuje a orchestruje složitá data a závislosti zpracování. Řešení je možné rychle sestavit a nasadit v cloudu a připojit rostoucí počet místních a cloudových zdrojů dat.

Zvažte použití ADF:

* Když je potřeba data průběžně migrovat v hybridním scénáři, který přistupuje k místním i cloudovým prostředkům
* Když data potřebují transformace nebo když se k ní přidají obchodní logika při migraci.

ADF umožňuje plánovat a monitorovat úlohy pomocí jednoduchých skriptů JSON, které pravidelně spravují pohyb dat. ADF má také další možnosti, jako je podpora složitých operací. Další informace o ADF najdete v dokumentaci na adrese [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="the-scenario"></a><a name="scenario"></a>Scénář
Nastavili jsme kanál ADF, který bude vytvářet dvě aktivity migrace dat. Společně přesouvá data každý den mezi SQL Server databáze a Azure SQL Database. Tyto dvě aktivity jsou:

* Kopírování dat z SQL Server databáze do účtu Azure Blob Storage
* Zkopírujte data z účtu Azure Blob Storage do Azure SQL Database.

> [!NOTE]
> Popsané kroky byly přizpůsobené z podrobnějšího kurzu poskytnutého týmem ADF: [kopírování dat z databáze SQL Server do Azure Blob Storage](../../data-factory/tutorial-hybrid-copy-portal.md) odkazy na relevantní části tohoto tématu jsou uvedené v případě potřeby.
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Předpoklady
V tomto kurzu se předpokládá, že máte následující:

* **Předplatné Azure** Pokud nemáte předplatné, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* **Účet služby Azure Storage**. K ukládání dat v tomto kurzu použijete účet služby Azure Storage. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md). Po vytvoření účtu úložiště je třeba získat klíč účtu, který se používá pro přístup k účtu. Viz [Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md).
* Přístup k **Azure SQL Database**. Pokud musíte nastavit Azure SQL Database, téma [Začínáme s Microsoft Azure SQL Database](../../azure-sql/database/single-database-create-quickstart.md) poskytuje informace o tom, jak zřídit novou instanci Azure SQL Database.
* Instalace a konfigurace **Azure PowerShell** lokálně. Pokyny najdete v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/).

> [!NOTE]
> Tento postup používá [Azure Portal](https://portal.azure.com/).
>
>

## <a name="upload-the-data-to-your-sql-server-instance"></a><a name="upload-data"></a> Nahrajte data do instance SQL Server.
K předvedení procesu migrace používáme [datovou sadu NYC taxislužby](https://chriswhong.com/open-data/foil_nyc_taxi/) . Datová sada taxislužby NYC je k dispozici, jak je uvedeno v tomto příspěvku, v datech Azure Blob Storage [NYC taxislužby](https://www.andresmh.com/nyctaxitrips/). Data obsahují dva soubory, trip_data.csv soubor, který obsahuje podrobnosti o cestě, a soubor trip_far.csv, který obsahuje podrobnosti o tarifu placeného pro každou cestu. Ukázka a popis těchto souborů jsou k dispozici v [popisu datové sady NYC taxislužby TRIPS](sql-walkthrough.md#dataset).

Můžete buď upravit proceduru, která je zde uvedena, do sady vlastních dat nebo postupovat podle kroků popsaných v datové sadě taxislužby NYC. Pokud chcete nahrát datovou sadu taxislužby NYC do databáze SQL Server, postupujte podle pokynů uvedených v [hromadném importu dat do SQL Server databáze](sql-walkthrough.md#dbload).

## <a name="create-an-azure-data-factory"></a><a name="create-adf"></a> Vytvoření Azure Data Factory
K dispozici jsou pokyny pro vytvoření nové Azure Data Factory a skupiny prostředků v [Azure Portal](https://portal.azure.com/) [Vytvoření Azure Data Factory](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory). Pojmenujte novou instanci ADF *adfdsp* a pojmenujte skupinu prostředků, kterou vytvořila *adfdsprg*.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Instalace a konfigurace Azure Data Factory Integration Runtime
Integration Runtime je infrastruktura pro integraci dat spravovaná zákazníky, kterou používá Azure Data Factory k poskytování možností integrace dat napříč různými síťovými prostředími. Tento modul runtime se dřív nazýval "Správa dat Gateway".

Pokud chcete nastavit, [postupujte podle pokynů pro vytvoření kanálu](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline) .

## <a name="create-linked-services-to-connect-to-the-data-resources"></a><a name="adflinkedservices"></a>Vytvoření propojených služeb pro připojení k datovým prostředkům
Propojená služba definuje informace potřebné pro připojení k datovému zdroji Azure Data Factory. V tomto scénáři máme tři prostředky, pro které jsou potřeba propojené služby:

1. Místní SQL Server
2. Azure Blob Storage
3. Azure SQL Database

Podrobný postup pro vytváření propojených služeb je k dispozici v části [Vytvoření propojených služeb](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).


## <a name="define-and-create-tables-to-specify-how-to-access-the-datasets"></a><a name="adf-tables"></a>Definování a vytvoření tabulek pro určení způsobu přístupu k datovým sadám
Pomocí následujících postupů založených na skriptech vytvořte tabulky, které určují strukturu, umístění a dostupnost datových sad. Soubory JSON se používají k definování tabulek. Další informace o struktuře těchto souborů naleznete v tématu [datové sady](../../data-factory/concepts-datasets-linked-services.md).

> [!NOTE]
> `Add-AzureAccount`Před spuštěním rutiny [New-AzureDataFactoryTable](/previous-versions/azure/dn835096(v=azure.100)) byste měli spustit rutinu, abyste zkontrolovali, jestli je pro provedení příkazu vybraný správné předplatné Azure. Dokumentaci k této rutině najdete v tématu [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount?view=azuresmps-3.7.0).
>
>

Definice založené na formátu JSON v tabulkách používají následující názvy:

* **název tabulky** v SQL Server je *nyctaxi_data*
* **název kontejneru** v účtu Azure Blob Storage je *ContainerName* .

Pro tento kanál ADF jsou potřeba tři definice tabulek:

1. [Místní tabulka SQL](#adf-table-onprem-sql)
2. [Tabulka objektů BLOB](#adf-table-blob-store)
3. [SQL Azure tabulka](#adf-table-azure-sql)

> [!NOTE]
> Tyto postupy používají Azure PowerShell k definování a vytvoření aktivit ADF. Tyto úlohy je ale možné provádět také pomocí Azure Portal. Podrobnosti najdete v tématu [vytvoření datových sad](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

### <a name="sql-on-premises-table"></a><a name="adf-table-onprem-sql"></a>Místní tabulka SQL
Definice tabulky pro SQL Server je určena v následujícím souboru JSON:

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

Tady nejsou uvedené názvy sloupců. Na názvy sloupců můžete vybrat jejich výběr tak, že je sem zahrnete (podrobnosti najdete v tématu [dokumentace k ADF](../../data-factory/copy-activity-overview.md) .

Zkopírujte definici JSON tabulky do souboru s názvem *onpremtabledef.jsv* souboru a uložte je do známého umístění (tady se předpokládá, že *C:\temp\onpremtabledef.jszapnutá* ). Vytvořte tabulku v ADF pomocí následující rutiny Azure PowerShell:

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json
```


### <a name="blob-table"></a><a name="adf-table-blob-store"></a>Tabulka objektů BLOB
Definice tabulky pro výstupní umístění objektu BLOB je následující (mapuje ingestovaná data z místního prostředí do objektu blob Azure):

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

Zkopírujte definici JSON tabulky do souboru s názvem *bloboutputtabledef.jsv* souboru a uložte je do známého umístění (tady se předpokládá, že *C:\temp\bloboutputtabledef.jszapnutá* ). Vytvořte tabulku v ADF pomocí následující rutiny Azure PowerShell:

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json
```

### <a name="sql-azure-table"></a><a name="adf-table-azure-sql"></a>SQL Azure tabulka
Definice tabulky pro výstup SQL Azure je následující (Toto schéma mapuje data přicházející z objektu BLOB):

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

Zkopírujte definici JSON tabulky do souboru s názvem *AzureSqlTable.jsv* souboru a uložte je do známého umístění (tady se předpokládá, že *C:\temp\AzureSqlTable.jszapnutá* ). Vytvořte tabulku v ADF pomocí následující rutiny Azure PowerShell:

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json
```


## <a name="define-and-create-the-pipeline"></a><a name="adf-pipeline"></a>Definování a vytvoření kanálu
Zadejte aktivity, které patří do kanálu, a vytvořte kanál s následujícími procedurami založenými na skriptech. K definování vlastností kanálu se používá soubor JSON.

* Skript předpokládá, že **název kanálu** je *AMLDSProcessPipeline*.
* Všimněte si také, že jsme nastavili periodicitu kanálu, který se má spustit každý den a použít výchozí čas spuštění úlohy (12 UTC).

> [!NOTE]
> Následující postupy používají Azure PowerShell k definování a vytvoření kanálu ADF. Tuto úlohu ale můžete také dosáhnout pomocí Azure Portal. Podrobnosti najdete v tématu [vytvoření kanálu](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

Pomocí výše uvedených definic tabulek je definice kanálu pro ADF uvedená níže:

```json
{
    "name": "AMLDSProcessPipeline",
    "properties":
    {
        "description" : "This pipeline has two activities: the first one copies data from SQL Server to Azure Blob, and the second one copies from Azure Blob to Azure Database Table",
        "activities":
        [
            {
                "name": "CopyFromSQLtoBlob",
                "description": "Copy data from SQL Server to blob",
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

Zkopírujte tuto definici JSON kanálu do souboru s názvem *pipelinedef.jsv* souboru a uložte ho do známého umístění (tady se předpokládá *C:\temp\pipelinedef.jszapnuto* ). Vytvořte kanál v ADF pomocí následující rutiny Azure PowerShell:

```azurepowershell
New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json
```


## <a name="start-the-pipeline"></a><a name="adf-pipeline-start"></a>Spuštění kanálu
Kanál se teď dá spustit pomocí následujícího příkazu:

```azurepowershell
Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline
```

Hodnoty parametrů *StartDate* a *EndDate* je třeba nahradit skutečnými daty, mezi kterými chcete spustit kanál.

Po spuštění kanálu byste měli být schopni zobrazit data v kontejneru vybraném pro objekt blob, jeden soubor za den.

Nevyužili jsme funkci, kterou poskytuje ADF pro přírůstková data. Další informace o tom, jak provést tuto a další funkce, které poskytuje ADF, najdete v [dokumentaci k ADF](https://azure.microsoft.com/services/data-factory/).