---
title: Přístup k datům ve službě Azure Storage
titleSuffix: Azure Machine Learning service
description: Naučte se používat úložiště dat pro přístup ke službám Azure Storage během školení pomocí služby Azure Machine Learning Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 08/2/2019
ms.custom: seodec18
ms.openlocfilehash: 7b800a7ef38624dbe89a61dd04e2bd97b02066bb
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70191925"
---
# <a name="access-data-in-azure-storage-services"></a>Přístup k datům ve službě Azure Storage

 V tomto článku se dozvíte, jak snadno získat přístup k datům ve službě Azure Storage prostřednictvím Azure Machine Learning úložiště dat. Úložiště dat slouží k ukládání informací o připojení, jako je ID předplatného a autorizace tokenu, pro přístup k úložišti, aniž by bylo nutné zakódovat tyto informace ve skriptech.

Tento postup ukazuje příklady následujících úloh:
* [Registrovat úložiště dat](#access)
* [Získat úložiště dat z pracovního prostoru](#get)
* [Nahrávání a stahování dat pomocí datových úložišť](#up-and-down)
* [Přístup k datům během školení](#train)

## <a name="prerequisites"></a>Požadavky

Chcete-li použít úložiště, musíte nejprve [pracovní prostor](concept-workspace.md).

Začněte tím, že buď [vytváří se nový pracovní prostor](how-to-manage-workspace.md) nebo některý z existujících načítání:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="register-datastores"></a>Registrovat úložiště dat

Všechny metody registru jsou ve [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) třídě a mají tvar register_azure_ *.

Následující příklady ukazují, jak zaregistrovat kontejner objektů blob Azure nebo sdílenou složku Azure jako úložiště dat.

+ Pro **úložiště dat kontejneru objektů BLOB v Azure**použijte[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                      datastore_name='your datastore name', 
                                                      container_name='your azure blob container name',
                                                      account_name='your storage account name', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
  ```

+ Pro **úložiště dat sdílené složky Azure**použijte [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). Příklad: 
  ```Python
  datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                  datastore_name='your datastore name', 
                                                  file_share_name='your file share name',
                                                  account_name='your storage account name', 
                                                  account_key='your storage account key',
                                                  create_if_not_exists=True)
  ```

####  <a name="storage-guidance"></a>Pokyny k ukládání

Doporučujeme kontejner Azure Blob. Úložiště úrovně Standard a Premium jsou k dispozici pro objekty blob. I když je výhodnější, doporučujeme Premium Storage z důvodu rychlejších přenosů propustnosti, které můžou zlepšit rychlost vašich školicích běhů, zejména pokud budete vlakem s velkou datovou sadou. Informace o nákladech na účet úložiště najdete v [cenové kalkulačkě Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) .

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Získat úložiště dat z vašeho pracovního prostoru

Chcete-li získat konkrétní úložiště dat registrované v aktuálním pracovním prostoru, použijte [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) statickou metodu pro třídu úložiště dat:

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Chcete-li získat seznam úložišť dat zaregistrovaných v daném pracovním prostoru, můžete použít `datastores` vlastnost v objektu pracovního prostoru:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Když vytvoříte pracovní prostor, kontejner objektů blob Azure a sdílená složka Azure se zaregistrují do pracovního prostoru `workspaceblobstore` s `workspacefilestore` názvem a v uvedeném pořadí. Ukládají informace o připojení kontejneru objektů BLOB a sdílené složky zřízené v účtu úložiště připojeném k pracovnímu prostoru. `workspaceblobstore` Je nastaven jako výchozí úložiště dat.

Chcete-li získat výchozí pracovním prostoru úložiště dat:

```Python
datastore = ws.get_default_datastore()
```

Pro definování jiného výchozího úložiště dat pro aktuální pracovní prostor použijte [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) metodu v objektu pracovního prostoru:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Nahrání & stažení dat
Metody [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) a [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) popsané v následujících příkladech jsou specifické pro a pracují stejně jako třídy [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) a [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) .

### <a name="upload"></a>Odeslat

 Nahrajte do adresáře nebo jednotlivé soubory do úložiště dat pomocí sady Python SDK.

K nahrání do úložiště dat do adresáře `datastore`:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

`target_path` Parametr určuje umístění ve sdílené složce (nebo kontejneru objektů BLOB), které se má nahrát. Použije se výchozí `None`, v takovém případě získá nahrát data do kořenového adresáře. Když `overwrite=True` budou všechna existující data `target_path` v přepsána.

Nebo nahrajte pomocí `upload_files()` metody seznam jednotlivých souborů do úložiště dat.

### <a name="download"></a>Ke stažení

Podobně stahování dat z úložiště dat do vašeho místního systému souborů.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

`target_path` Parametr je umístěním místního adresáře, do kterého se mají data stahovat. Chcete-li zadat cestu ke složce ve sdílené složky (nebo kontejneru objektů blob) ke stažení, zadejte cestu tak, `prefix`. Pokud `prefix` je `None`, se stáhne veškerý obsah sdílené složky (nebo kontejneru objektů blob).

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Přístup k datům během školení

Pro přístup k datům během školení si můžete stáhnout nebo připojit data ze služeb Azure Storage do cíle výpočetní služby prostřednictvím úložiště dat.

Následující tabulka uvádí metody, které oznamují výpočetnímu cíli, jak používat úložiště dat během spuštění. 

Podobně|Metoda|Popis|
----|-----|--------
Připojit| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Slouží k připojení úložiště dat na cílovém výpočetním cíli.
Ke stažení|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Použijte ke stažení obsahu úložiště dat do umístění určeného parametrem `path_on_compute`. <br> K tomuto stažení dojde před spuštěním.
Odeslat|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Slouží k nahrání souboru z umístění určeného `path_on_compute` do úložiště dat. <br> K odeslání dojde po spuštění.

Chcete-li odkazovat na konkrétní složku nebo soubor v úložišti dat a zpřístupnit ho v cílovém výpočetním prostředí, použijte metodu úložiště dat [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) .

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Libovolný `datastore` objekt `datastore.path` nebo se přeloží na název proměnné prostředí ve formátu `"$AZUREML_DATAREFERENCE_XXXX"`, jehož hodnota představuje cestu pro připojení nebo stažení cílového Compute. Cesta úložiště dat na cílovém výpočetním prostředí nemusí být stejná jako cesta spuštění pro skript školení.

### <a name="examples"></a>Příklady 

Následující příklady kódu jsou specifické [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) pro třídu pro přístup k datům během školení. 

`script_params`je slovník obsahující parametry pro entry_script. Můžete ho použít k předání do úložiště dat a popisuje, jak mají být data dostupná na výpočetním cíli. Další informace získáte v našem uceleném [kurzu](tutorial-train-models-with-aml.md).

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Můžete také předat seznam úložišť dat do parametru konstruktoru `inputs` Estimator a připojit nebo zkopírovat data do nebo z úložiště dat. Tento příklad kódu:
* Před spuštěním školicího skriptu `datastore1` `train.py` stáhne veškerý obsah do cílového výpočetního prostředí.
* Stáhne složku `'./foo'` do `datastore2` cílového výpočetního prostředí před `train.py` spuštěním.
* Nahraje soubor `'./bar.pkl'` z výpočetního cíle `datastore3` do po spuštění skriptu.

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```

### <a name="compute-and-datastore-matrix"></a>Matice COMPUTE a úložiště dat

Úložiště dat v současné době podporují ukládání informací o připojení do služby úložiště uvedené v následující matici. Tato matice zobrazuje dostupné funkce přístupu k datům pro různé výpočetní cíle a scénáře úložiště dat. Další informace o [výpočetních cílech pro Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Compute|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Místní|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|Není k dispozici         |Není k dispozici                                                                         |
| Azure Machine Learning Compute |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|Není k dispozici         |Není k dispozici                                                                         |
| Virtuální počítače               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Není k dispozici         |Není k dispozici                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Není k dispozici         |Není k dispozici                                                                         |
| Přenos dat                  |[Kanály&nbsp;ml](concept-ml-pipelines.md)                                               |Není k dispozici                                           |[Kanály&nbsp;ml](concept-ml-pipelines.md)            |[Kanály&nbsp;ml](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[Kanály&nbsp;ml](concept-ml-pipelines.md)                                              |Není k dispozici                                           |[Kanály&nbsp;ml](concept-ml-pipelines.md)             |Není k dispozici                                                                         |
| Azure Batch                    |[Kanály&nbsp;ml](concept-ml-pipelines.md)                                               |Není k dispozici                                           |Není k dispozici         |Není k dispozici                                                                         |
| Azure DataLake Analytics       |Není k dispozici                                           |Není k dispozici                                           |[Kanály&nbsp;ml](concept-ml-pipelines.md)             |Není k dispozici                                                                         |

> [!NOTE]
> Můžou nastat scénáře, ve kterých se vysoce iterační procesy s velkými objemy dat `as_download()` spouští rychleji `as_mount()`pomocí místo toho, aby je bylo možné ověřit experimentálně.

### <a name="accessing-source-code-during-training"></a>Přístup ke zdrojovému kódu během školení

Azure Blob Storage má vyšší propustnost než sdílenou složku Azure a bude se škálovat na velký počet spuštěných paralelně. Z tohoto důvodu doporučujeme nakonfigurovat vaše běhy na používání úložiště objektů BLOB pro přenos souborů zdrojového kódu.

Následující příklad kódu určuje v konfiguraci spuštění, které úložiště dat objektu BLOB se má použít pro přenosy zdrojového kódu.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Přístup k datům během bodování

Služba Azure Machine Learning poskytuje několik způsobů, jak používat vaše modely pro bodování. Některé z těchto metod neposkytují přístup k úložišti dat. Následující tabulka vám pomůže pochopit, které metody umožňují přístup k úložišti dat během bodování:

| Metoda | Přístup k úložišti dat | Popis |
| ----- | :-----: | ----- |
| [Předpověď dávky](how-to-run-batch-predictions.md) | ✔ | Provádějte asynchronní předpovědi velké množství dat. |
| [Webová služba](how-to-deploy-and-where.md) | &nbsp; | Nasadit modely jako webovou službu. |
| [Modul IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Nasaďte modely do IoT Edgech zařízení. |

V situacích, kdy sada SDK neposkytuje přístup k úložiště dat, může být možné vytvořit vlastní kód pomocí příslušné sady Azure SDK pro přístup k datům. Například použití [sady SDK Azure Storage pro Python](https://github.com/Azure/azure-storage-python) pro přístup k datům uloženým v objektech blob.


## <a name="next-steps"></a>Další postup

* [Trénování modelu](how-to-train-ml-models.md)

* [Nasazení modelu](how-to-deploy-and-where.md)
