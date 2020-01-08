---
title: Přístup k datům ve službě Azure Storage Services
titleSuffix: Azure Machine Learning
description: Naučte se používat úložiště dat k zabezpečenému připojení ke službám Azure Storage během školení pomocí Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: ylxiong
author: YLXiong1125
ms.reviewer: nibaccam
ms.date: 12/10/2019
ms.custom: seodec18
ms.openlocfilehash: ac6ef6341013ca13d5a9f27be8897365c1c2155d
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540942"
---
# <a name="access-data-in-azure-storage-services"></a>Přístup k datům ve službě Azure Storage
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak snadno získat přístup k datům ve službě Azure Storage Services přes Azure Machine Learning úložiště dat. Úložiště dat slouží k ukládání informací o připojení, jako je ID předplatného a autorizace tokenu. Když použijete úložiště dat, budete mít přístup k úložišti, aniž byste museli v skriptech zakódovat informace o připojení. 

Z [těchto řešení Azure Storage](#matrix)můžete vytvořit úložiště dat. Pro Nepodporovaná řešení úložišť a za účelem úspory nákladů na výstup dat během experimentů machine learningu doporučujeme [přesunout data](#move) na podporovaná Azure Storage řešení. 

## <a name="prerequisites"></a>Požadavky
Budete potřebovat:
- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

- Účet služby Azure Storage s [kontejnerem objektů blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) nebo [sdílenou složkou Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- [Sada SDK Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)nebo přístup k [Azure Machine Learning Studiu](https://ml.azure.com/).

- Pracovní prostor služby Azure Machine Learning.
  
  Buď [Vytvořte pracovní prostor Azure Machine Learning](how-to-manage-workspace.md) nebo použijte existující pomocí sady Python SDK:

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Vytvoření a registrace úložišť dat

Když zaregistrujete řešení Azure Storage jako úložiště dat, automaticky vytvoříte toto úložiště dat v určitém pracovním prostoru. Úložiště dat můžete vytvořit a zaregistrovat v pracovním prostoru pomocí sady Python SDK nebo Azure Machine Learning studia.

### <a name="python-sdk"></a>Python SDK

Všechny metody registru jsou na třídě [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) a mají `register_azure_*`formuláře.

Informace, které potřebujete k naplnění `register()` metody, získáte pomocí [Azure Portal](https://portal.azure.com):

1. V levém podokně vyberte **účty úložiště** a zvolte účet úložiště, který chcete zaregistrovat. 
2. Informace, jako je název účtu, kontejner a název sdílené složky, najdete na stránce **Přehled** . Pro informace o ověřování, jako je klíč účtu nebo token SAS, přejděte v podokně **Nastavení** na **přístupové klíče** . 

> [!IMPORTANT]
> Pokud je váš účet úložiště ve virtuální síti, podporuje se jenom vytváření úložiště dat objektů BLOB v Azure. Pokud chcete vašemu pracovnímu prostoru udělit přístup k vašemu účtu úložiště, nastavte parametr `grant_workspace_access` na `True`.

Následující příklady ukazují, jak zaregistrovat kontejner objektů blob Azure, sdílenou složku Azure a data Azure SQL jako úložiště dat.

#### <a name="blob-container"></a>Kontejner objektů blob

K registraci kontejneru objektů blob Azure jako úložiště dat použijte [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

Následující kód vytvoří a zaregistruje úložiště dat `blob_datastore_name` do pracovního prostoru `ws`. Toto úložiště dat přistupuje k `my-container-name` kontejneru objektů BLOB v účtu úložiště `my-account-name` pomocí zadaného klíče účtu.

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

#### <a name="file-share"></a>Sdílená složka

Pokud chcete zaregistrovat sdílenou složku Azure jako úložiště dat, použijte [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

Následující kód vytvoří a zaregistruje úložiště dat `file_datastore_name` do pracovního prostoru `ws`. Toto úložiště DataStore přistupuje ke sdílené složce `my-fileshare-name` v účtu úložiště `my-account-name` pomocí zadaného klíče účtu.

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                 datastore_name=file_datastore_name, 
                                                 file_share_name=file_share_name, 
                                                 account_name=account_name,
                                                 account_key=account_key)
```

#### <a name="sql-data"></a>Data SQL

Pro úložiště dat Azure SQL použijte [register_azure_sql_database ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-sql-database-workspace--datastore-name--server-name--database-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--endpoint-none--overwrite-false--username-none--password-none-) k registraci úložiště dat přihlašovacích údajů připojeného ke službě Azure SQL Database s ověřováním SQL nebo s oprávněními instančního objektu. 

Postup při registraci pomocí ověřování SQL:

```python
sql_datastore_name="azsqlsdksql"
server_name=os.getenv("SQL_SERVERNAME", "<my-server-name>") # Name of the Azure SQL server
database_name=os.getenv("SQL_DATBASENAME", "<my-database-name>") # Name of the Azure SQL database
username=os.getenv("SQL_USER_NAME", "<my-sql-user-name>") # Username of the database user to access the database
password=os.getenv("SQL_USER_PASSWORD", "<my-sql-user-password>") # Password of the database user to access the database

sql_datastore = Datastore.register_azure_sql_database(workspace=ws,
                                                  datastore_name=sql_datastore_name,
                                                  server_name=server_name,
                                                  database_name=database_name,
                                                  username=username,
                                                  password=password)

```

Postup při registraci pomocí instančního objektu:

```python 
sql_datastore_name="azsqlsdksp"
server_name=os.getenv("SQL_SERVERNAME", "<my-server-name>") # Name of the SQL server
database_name=os.getenv("SQL_DATBASENAME", "<my-database-name>") # Name of the SQL database
client_id=os.getenv("SQL_CLIENTNAME", "<my-client-id>") # Client ID of the service principal with permissions to access the database
client_secret=os.getenv("SQL_CLIENTSECRET", "<my-client-secret>") # Secret of the service principal
tenant_id=os.getenv("SQL_TENANTID", "<my-tenant-id>") # Tenant ID of the service principal

sql_datastore = Datastore.register_azure_sql_database(workspace=ws,
                                                      datastore_name=sql_datastore_name,
                                                      server_name=server_name,
                                                      database_name=database_name,
                                                      client_id=client_id,
                                                      client_secret=client_secret,
                                                      tenant_id=tenant_id)
```

#### <a name="storage-guidance"></a>Pokyny k ukládání

Doporučujeme kontejner objektů blob Azure. Úložiště úrovně Standard a Premium jsou k dispozici pro objekty blob. I když je Premium Storage dražší, jejich rychlejší přenosová rychlost může zlepšit rychlost vašich školicích běhů, zejména pokud budete vlakovat s velkou datovou sadou. Informace o nákladech na účty úložiště najdete v [cenové kalkulačkě Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio 

Vytvořte nové úložiště dat v několika krocích v Azure Machine Learning Studiu:

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com/).
1. V levém podokně v části **Spravovat**vyberte **úložiště** .
1. Vyberte **+ nové úložiště dat**.
1. Vyplňte formulář pro nové úložiště dat. Formulář se inteligentně aktualizuje na základě vašich výběrů pro Azure Storage typ a typ ověřování.
  
Můžete najít informace, které potřebujete k naplnění formuláře na [Azure Portal](https://portal.azure.com). V levém podokně vyberte **účty úložiště** a zvolte účet úložiště, který chcete zaregistrovat. Stránka **Přehled** poskytuje informace, jako je název účtu, kontejner a název sdílené složky. U položek ověřování, jako je klíč účtu nebo token SAS, v podokně **Nastavení** přejít na **klíče účtu** .

Následující příklad ukazuje, jak formulář vypadá při vytváření úložiště dat objektů BLOB v Azure: 
    
![Formulář pro nové úložiště dat](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Získat úložiště dat z vašeho pracovního prostoru

Chcete-li získat konkrétní úložiště dat registrované v aktuálním pracovním prostoru, použijte metodu [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) static na `Datastore` třídě:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Chcete-li získat seznam úložišť dat zaregistrovaných v daném pracovním prostoru, můžete použít vlastnost [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) v objektu pracovního prostoru:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Když vytvoříte pracovní prostor, kontejner objektů BLOB v Azure a sdílená složka Azure se automaticky zaregistrují do pracovního prostoru. Jsou pojmenovány `workspaceblobstore` a `workspacefilestore`v uvedeném pořadí. Ukládají informace o připojení pro kontejner objektů BLOB a sdílenou složku, která je zřízená v účtu úložiště připojeném k pracovnímu prostoru. Kontejner `workspaceblobstore` je nastaven jako výchozí úložiště dat.

Pokud chcete získat výchozí úložiště dat v pracovním prostoru, použijte tento řádek:

```Python
datastore = ws.get_default_datastore()
```

Pro definování jiného výchozího úložiště dat pro aktuální pracovní prostor použijte metodu [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) v objektu pracovního prostoru:

```Python
# Define the default datastore for the current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Nahrávání a stahování dat

Metody [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) a [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) popsané v následujících příkladech jsou specifické pro a pracují stejně jako třídy [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) a [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) .

### <a name="upload"></a>Nahrávání

Nahrát buď adresář nebo jednotlivé soubory do úložiště dat pomocí sady Python SDK:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

Parametr `target_path` určuje umístění sdílené složky (nebo kontejneru objektů BLOB), který se má nahrát. Výchozí hodnota je `None`, takže data se nahrají do kořenového adresáře. Pokud `overwrite=True`, všechna existující data v `target_path` se přepíší.

Můžete také nahrát seznam jednotlivých souborů do úložiště dat prostřednictvím metody `upload_files()`.

### <a name="download"></a>Stáhnout

Stáhněte data z úložiště dat do místního systému souborů:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Parametr `target_path` je umístění místního adresáře, do kterého se mají data stahovat. Chcete-li zadat cestu ke složce ve sdílené složky (nebo kontejneru objektů blob) ke stažení, zadejte cestu tak, `prefix`. Pokud je `prefix` `None`, stáhne se veškerý obsah sdílené složky (nebo kontejneru objektů BLOB).

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Přístup k datům během školení

> [!IMPORTANT]
> Teď doporučujeme použít [Azure Machine Learning datové sady](how-to-create-register-datasets.md) pro přístup k vašim datům při výuce. Datové sady poskytují funkce, které načítají tabulková data do datového rámce PANDAS nebo Spark. Datové sady také poskytují možnost stahovat nebo připojovat soubory libovolného formátu z služby Azure Blob Storage, souborů Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database a Azure Database for PostgreSQL. [Přečtěte si další informace o tom, jak naučit s datovými sadami](how-to-train-with-datasets.md).

V následující tabulce jsou uvedené metody, které oznamují výpočetnímu cíli, jak používat úložiště dat během spuštění: 

Podobně|Metoda|Popis|
----|-----|--------
Připojení| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Slouží k připojení úložiště dat na cílovém výpočetním cíli. Když je úložiště dat připojené, zpřístupní se všechny soubory vašeho úložiště dat pro váš cíl výpočtů.
Stáhnout|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Použijte ke stažení obsahu úložiště dat do umístění určeného `path_on_compute`. <br><br> K tomuto stažení dojde před spuštěním.
Nahrávání|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Slouží k nahrání souboru z umístění určeného `path_on_compute` do úložiště dat. <br><br> K odeslání dojde po spuštění.

Chcete-li odkazovat na konkrétní složku nebo soubor v úložišti dat a zpřístupnit ho v cílovém výpočetním prostředí, použijte metodu [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) úložiště dat:

```Python
# To mount the full contents in your storage to the compute target
datastore.as_mount()

# To download the contents of only the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Libovolný zadaný `datastore` nebo objekt `datastore.path` se přeloží na název proměnné prostředí `"$AZUREML_DATAREFERENCE_XXXX"`formátu. Hodnota tohoto názvu představuje cestu k připojení nebo ke stažení na cílovém výpočetním cíli. Cesta úložiště dat na cílovém výpočetním cíli se nemusí shodovat s cestou spuštění pro skript školení.

### <a name="examples"></a>Příklady 

Pro přístup k datům během školení doporučujeme použít třídu [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) . 

Proměnná `script_params` je slovník, který obsahuje parametry pro `entry_script`. Použijte ho k předání do úložiště dat a popište, jak budou data dostupná na výpočetním cíli. Další informace najdete v tomto [uceleném kurzu](tutorial-train-models-with-aml.md).

```Python
from azureml.train.estimator import Estimator

# Notice that '/' is in front, which indicates the absolute path
script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Můžete také předat seznam úložišť dat do parametru `inputs` `Estimator` konstruktoru, abyste mohli připojit nebo zkopírovat data do nebo do úložiště dat. Tento příklad kódu:
* Před spuštěním skriptu `train.py` pro školení stáhne veškerý obsah v `datastore1` do cílového výpočetního prostředí.
* Před spuštěním `train.py` stáhne složku `'./foo'` v `datastore2` do cílového výpočetního prostředí.
* Po spuštění skriptu nahraje soubor `'./bar.pkl'` z cíle služby COMPUTE do `datastore3`.

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
Pokud dáváte přednost použití `RunConfig`ho objektu pro školení, je nutné nastavit objekt [`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) . 

Následující kód ukazuje, jak pracovat s objektem `DataReference` ve odhadovém kanálu. Úplný příklad najdete v [tomto poznámkovém bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-estimatorstep.ipynb).

```Python
from azureml.core import Datastore
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

def_blob_store = Datastore(ws, "workspaceblobstore")

input_data = DataReference(
       datastore=def_blob_store,
       data_reference_name="input_data",
       path_on_datastore="20newsgroups/20news.pkl")

output = PipelineData("output", datastore=def_blob_store)
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>Matice COMPUTE a úložiště dat

Úložiště dat v současné době podporují ukládání informací o připojení do služby úložiště uvedené v následující matici. Tato matice zobrazuje dostupné funkce pro přístup k datům pro různé výpočetní cíle a scénáře úložiště dat. Další [informace o výpočetních cílech pro Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Služby Compute|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Místní|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|Nevztahuje se         |Nevztahuje se                                                                         |
| Azure Machine Learning compute |[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download (](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [Machine Learning kanály](concept-ml-pipelines.md)|[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download (](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [Machine Learning kanály](concept-ml-pipelines.md)|Nevztahuje se         |Nevztahuje se                                                                         |
| Virtuální počítače               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Nevztahuje se         |Nevztahuje se                                                                         |
| Azure HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Nevztahuje se         |Nevztahuje se                                                                         |
| Přenos dat                  |[Machine Learning kanály](concept-ml-pipelines.md)                                               |Nevztahuje se                                           |[Machine Learning kanály](concept-ml-pipelines.md)            |[Machine Learning kanály](concept-ml-pipelines.md)                                                                            |
| Azure Databricks                     |[Machine Learning kanály](concept-ml-pipelines.md)                                              |Nevztahuje se                                           |[Machine Learning kanály](concept-ml-pipelines.md)             |Nevztahuje se                                                                         |
| Azure Batch                    |[Machine Learning kanály](concept-ml-pipelines.md)                                               |Nevztahuje se                                           |Nevztahuje se         |Nevztahuje se                                                                         |
| Azure Data Lake Analytics       |Nevztahuje se                                           |Nevztahuje se                                           |[Machine Learning kanály](concept-ml-pipelines.md)             |Nevztahuje se                                                                         |

> [!NOTE]
> Můžou nastat scénáře, ve kterých se vysoce iterační procesy s velkými objemy dat spouštějí rychleji pomocí `as_download()` místo `as_mount()`. Můžete to experimentně ověřit.

### <a name="accessing-source-code-during-training"></a>Přístup ke zdrojovému kódu během školení

Azure Blob Storage má vyšší propustnost než sdílenou složku Azure a bude se škálovat na velký počet spuštěných paralelně. Z tohoto důvodu doporučujeme nakonfigurovat vaše běhy na používání úložiště objektů BLOB pro přenos souborů zdrojového kódu.

Následující příklad kódu určuje v konfiguraci spuštění, které úložiště dat objektu BLOB se má použít pro přenosy zdrojového kódu:

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Přístup k datům během bodování

Azure Machine Learning poskytuje několik způsobů, jak používat vaše modely pro bodování. Některé z těchto metod neposkytují přístup k úložiště dat. Následující tabulka vám pomůže pochopit, které metody umožňují přístup k úložišti dat během bodování:

| Metoda | Přístup k úložišti dat | Popis |
| ----- | :-----: | ----- |
| [Předpověď dávky](how-to-run-batch-predictions.md) | ✔ | Provádějte asynchronní předpovědi velké množství dat. |
| [Webová služba](how-to-deploy-and-where.md) | &nbsp; | Nasaďte modely jako webovou službu. |
| [Modul Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Nasaďte modely pro IoT Edge zařízení. |

V situacích, kdy sada SDK neposkytuje přístup k úložiště dat, může být možné vytvořit vlastní kód pomocí příslušné sady Azure SDK pro přístup k datům. Například [sada SDK Azure Storage pro Python](https://github.com/Azure/azure-storage-python) je Klientská knihovna, kterou můžete použít pro přístup k datům uloženým v objektech blob nebo souborech.

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>Přesun dat na podporovaná Azure Storage řešení

Azure Machine Learning podporuje přístup k datům ze služby Azure Blob Storage, souborů Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database a Azure Database for PostgreSQL. Pokud používáte nepodporované úložiště, doporučujeme přesunout vaše data na podporovaná Azure Storage řešení pomocí Azure Data Factory. Přesunutí dat do podporovaného úložiště vám umožní ušetřit náklady na výstup dat během experimentů strojového učení. 

Azure Data Factory poskytuje efektivní a odolný přenos dat s více než 80 předem sestavenými konektory bez dalších nákladů. Mezi tyto konektory patří datové služby Azure, místní zdroje dat, Amazon S3 a RedShift a Google BigQuery. [Postupujte podle podrobných pokynů pro přesun dat pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

## <a name="next-steps"></a>Další kroky

* [Trénování modelu](how-to-train-ml-models.md)
* [Nasazení modelu](how-to-deploy-and-where.md)
