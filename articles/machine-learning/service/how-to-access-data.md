---
title: Přístup k datům ve službě Azure Storage
titleSuffix: Azure Machine Learning
description: Naučte se používat úložiště dat pro přístup ke službám Azure Storage během školení pomocí Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: ee6ab1ada540f4f664e6782a1fffc63cc7df95e4
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928588"
---
# <a name="access-data-in-azure-storage-services"></a>Přístup k datům ve službě Azure Storage
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak snadno získat přístup k datům ve službě Azure Storage prostřednictvím Azure Machine Learning úložiště dat. Úložiště dat slouží k ukládání informací o připojení, jako je ID předplatného a autorizace tokenu. Pomocí úložiště dat získáte přístup k úložišti, aniž byste museli zakódovat informace o připojení ve svých skriptech. Z těchto [Řešení Azure Storage](#matrix)můžete vytvořit úložiště dat. Pro Nepodporovaná řešení úložišť a za účelem úspory nákladů na výstup při experimentech strojového učení doporučujeme přesunout vaše data do našich podporovaných řešení Azure Storage. Přečtěte si, [Jak přesunout data](#move). 

Tento postup ukazuje příklady následujících úloh:
* Registrovat úložiště dat
* Získat úložiště dat z pracovního prostoru
* Nahrávání a stahování dat pomocí datových úložišť
* Přístup k datům během školení
* Přesun dat do služby Azure Storage

## <a name="prerequisites"></a>Předpoklady
Budete potřebovat
- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

- Účet služby Azure Storage s [kontejnerem objektů blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) nebo [sdílenou složkou Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- [Sada SDK Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)nebo přístup k [Azure Machine Learning Studiu](https://ml.azure.com/).

- Pracovní prostor služby Azure Machine Learning. 
    - Buď [Vytvořte pracovní prostor Azure Machine Learning](how-to-manage-workspace.md) , nebo použijte existující s použitím sady Python SDK.

        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore
        
        ws = Workspace.from_config()
        ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Vytvoření a registrace úložišť dat

Když zaregistrujete řešení úložiště Azure jako úložiště dat, vytvoříte toto úložiště automaticky v konkrétním pracovním prostoru. Můžete vytvořit a zaregistrovat úložiště dat v pracovním prostoru pomocí sady Python SDK nebo Azure Machine Learning studia.

### <a name="using-the-python-sdk"></a>Používání sady Python SDK

Všechny metody registru jsou na třídě [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) a mají formulář register_azure_ *.

Informace, které potřebujete k naplnění metody Register (), najdete v [Azure Machine Learning Studiu](https://ml.azure.com) a v těchto krocích.

1. V levém podokně vyberte **účty úložiště** a zvolte účet úložiště, který chcete zaregistrovat. 
2. Stránka **Přehled** poskytuje informace, jako je název účtu a kontejner nebo název sdílené složky. 
3. Pro informace o ověřování, jako je klíč účtu nebo token SAS, přejděte na **klíče účtu** v podokně **Nastavení** na levé straně. 

>VÝZNAMNÁ Pokud je váš účet úložiště ve virtuální síti, podporuje se jenom vytváření úložiště Azure Blob. Nastavte parametr `grant_workspace_access` `True` a udělte vašemu pracovnímu prostoru přístup k vašemu účtu úložiště.

Následující příklady ukazují, jak zaregistrovat kontejner objektů blob Azure nebo sdílenou složku Azure jako úložiště dat.

+ Pro **úložiště dat kontejneru objektů BLOB v Azure**použijte [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)

    Následující kód vytvoří a zaregistruje úložiště dat `my_datastore`do pracovního prostoru `ws`. Toto úložiště dat přistupuje k kontejneru objektů blob Azure `my_blob_container`v účtu úložiště Azure `my_storage_account` pomocí zadaného klíče účtu.

    ```Python
       datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                          datastore_name='my_datastore', 
                                                          container_name='my_blob_container',
                                                          account_name='my_storage_account', 
                                                          account_key='your storage account key',
                                                          create_if_not_exists=True)
    ```

+ Pro **úložiště dat sdílené složky Azure**použijte [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

    Následující kód vytvoří a zaregistruje úložiště dat `my_datastore`do pracovního prostoru `ws`. Toto úložiště DataStore přistupuje ke sdílené složce Azure `my_file_share`v účtu úložiště Azure `my_storage_account` pomocí zadaného klíče účtu.

    ```Python
       datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                      datastore_name='my_datastore', 
                                                      file_share_name='my_file_share',
                                                      account_name='my_storage account', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
    ```

####  <a name="storage-guidance"></a>Pokyny k ukládání

Doporučujeme kontejner Azure Blob. Úložiště úrovně Standard a Premium jsou k dispozici pro objekty blob. I když je výhodnější, doporučujeme Premium Storage z důvodu rychlejších přenosů propustnosti, které můžou zlepšit rychlost vašich školicích běhů, zejména pokud budete vlakem s velkou datovou sadou. Informace o nákladech na účet úložiště najdete v [cenové kalkulačkě Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) .

### <a name="using-azure-machine-learning-studio"></a>Používání Azure Machine Learning studia 

V několika krocích v Azure Machine Learning Studiu vytvořte nové úložiště dat.

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com/).
1. V levém podokně v části **Spravovat**vyberte **úložiště dat** .
1. Vyberte **+ nové úložiště dat**.
1. Dokončete formulář nového úložiště dat. Formulář se inteligentně aktualizuje na základě výběru typu úložiště Azure a typu ověřování.
  
Informace, které potřebujete k naplnění formuláře, najdete prostřednictvím [Azure Portal](https://portal.azure.com). V levém podokně vyberte **účty úložiště** a zvolte účet úložiště, který chcete zaregistrovat. Stránka **Přehled** poskytuje informace, jako je název účtu a kontejner nebo název sdílené složky. U položek ověřování, jako je klíč účtu nebo token SAS, přejděte v podokně **Nastavení** na levé straně na **klíče účtu** .

Následující příklad ukazuje, jak formulář vypadá jako při vytváření úložiště dat Azure Blob. 
    
 ![Nové úložiště dat](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Získat úložiště dat z vašeho pracovního prostoru

Chcete-li získat konkrétní úložiště dat registrované v aktuálním pracovním prostoru, použijte metodu [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) static na třídě úložiště dat:

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Chcete-li získat seznam úložišť dat zaregistrovaných v daném pracovním prostoru, můžete použít vlastnost [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) v objektu pracovního prostoru:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Když vytvoříte pracovní prostor, kontejner objektů BLOB v Azure a sdílená složka Azure se automaticky zaregistrují do pracovního prostoru s názvem `workspaceblobstore` a `workspacefilestore` v uvedeném pořadí. V těchto úložištích se ukládají informace o připojení kontejneru objektů BLOB a sdílené složky zřízené v účtu úložiště připojeném k pracovnímu prostoru. `workspaceblobstore` je nastaven jako výchozí úložiště dat.

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
Metody [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) a [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) popsané v následujících příkladech jsou specifické pro a pracují stejně jako třídy [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) a [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) .

### <a name="upload"></a>Nahrávání

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

Parametr `target_path` určuje umístění sdílené složky (nebo kontejneru objektů BLOB), který se má nahrát. Použije se výchozí `None`, v takovém případě získá nahrát data do kořenového adresáře. V opačném případě `overwrite=True` všechna existující data v `target_path` přepsána.

Nebo do úložiště dat nahrajte seznam jednotlivých souborů prostřednictvím metody `upload_files()`.

### <a name="download"></a>Ke stažení

Podobně stahování dat z úložiště dat do vašeho místního systému souborů.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Parametr `target_path` je umístění místního adresáře, do kterého se mají data stahovat. Chcete-li zadat cestu ke složce ve sdílené složky (nebo kontejneru objektů blob) ke stažení, zadejte cestu tak, `prefix`. Pokud `prefix` je `None`, se stáhne veškerý obsah sdílené složky (nebo kontejneru objektů blob).

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Přístup k datům během školení

> [!IMPORTANT]
> Použití [Azure Machine Learning datových sad](how-to-create-register-datasets.md) je nově doporučený způsob, jak získat přístup k vašim datům při školení. Datové sady poskytují funkce, které načítají tabulková data do PANDAS nebo Spark dataframe a mají možnost stahovat nebo připojovat soubory libovolného formátu z objektů blob Azure, souborů Azure, Azure Data Lake 1. generace Azure Data Lake Gen 2, Azure SQL, Azure PostgreSQL. Přečtěte si další informace o [tom, jak naučit s datovými sadami](how-to-train-with-datasets.md).

Následující tabulka uvádí metody, které oznamují výpočetnímu cíli, jak používat úložiště dat během spuštění. 

Podobně|Metoda|Popis|
----|-----|--------
Připojení| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Slouží k připojení úložiště dat na cílovém výpočetním cíli. Po připojení jsou všechny soubory vašeho úložiště dat zpřístupněny pro váš cíl výpočtů.
Ke stažení|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Použijte ke stažení obsahu úložiště dat do umístění určeného `path_on_compute`. <br><br> K tomuto stažení dojde před spuštěním.
Nahrávání|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Slouží k nahrání souboru z umístění určeného `path_on_compute` do úložiště dat. <br><br> K odeslání dojde po spuštění.

K odkazování na konkrétní složku nebo soubor v úložišti dat a jejich zpřístupnění v cíli výpočtů použijte metodu [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) úložiště dat.

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Libovolný zadaný `datastore` nebo `datastore.path` objekt překládá na název proměnné prostředí ve formátu `"$AZUREML_DATAREFERENCE_XXXX"`, jehož hodnota představuje cestu pro připojení nebo stažení cílového Compute. Cesta úložiště dat na cílovém výpočetním prostředí nemusí být stejná jako cesta spuštění pro skript školení.

### <a name="examples"></a>Příklady 

Následující příklady kódu jsou specifické pro třídu [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) pro přístup k datům během školení.

`script_params` je slovník obsahující parametry do entry_script. Použijte ho k předání do úložiště dat a popište, jak budou data dostupná na výpočetním cíli. Další informace získáte v našem uceleném [kurzu](tutorial-train-models-with-aml.md).

```Python
from azureml.train.estimator import Estimator

# notice '/' is in front, this indicates the absolute path
script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Můžete také předat seznam úložišť dat do konstruktoru Estimator `inputs` parametr pro připojení nebo kopírování dat do nebo z úložiště dat. Tento příklad kódu:
* Před spuštěním školicího skriptu `train.py` stáhne veškerý obsah v `datastore1` do výpočetního cíle.
* Před spuštěním `train.py` stáhne složku `'./foo'` `datastore2` do cílového výpočetního prostředí.
* Nahraje soubor `'./bar.pkl'` z cíle služby COMPUTE do `datastore3` po spuštění skriptu.

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>Matice COMPUTE a úložiště dat

Úložiště dat v současné době podporují ukládání informací o připojení do služby úložiště uvedené v následující matici. Tato matice zobrazuje dostupné funkce přístupu k datům pro různé výpočetní cíle a scénáře úložiště dat. Další informace o [výpočetních cílech pro Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Služby Compute|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Místní|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|Nevztahuje se         |Nevztahuje se                                                                         |
| Azure Machine Learning Compute |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|Nevztahuje se         |Nevztahuje se                                                                         |
| Virtuální počítače               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Nevztahuje se         |Nevztahuje se                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Nevztahuje se         |Nevztahuje se                                                                         |
| Přenos dat                  |[Kanály&nbsp;ML](concept-ml-pipelines.md)                                               |Nevztahuje se                                           |[Kanály&nbsp;ML](concept-ml-pipelines.md)            |[Kanály&nbsp;ML](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[Kanály&nbsp;ML](concept-ml-pipelines.md)                                              |Nevztahuje se                                           |[Kanály&nbsp;ML](concept-ml-pipelines.md)             |Nevztahuje se                                                                         |
| Azure Batch                    |[Kanály&nbsp;ML](concept-ml-pipelines.md)                                               |Nevztahuje se                                           |Nevztahuje se         |Nevztahuje se                                                                         |
| Azure DataLake Analytics       |Nevztahuje se                                           |Nevztahuje se                                           |[Kanály&nbsp;ML](concept-ml-pipelines.md)             |Nevztahuje se                                                                         |

> [!NOTE]
> Můžou nastat scénáře, ve kterých se vysoce iterační procesy s velkými objemy dat spouštějí rychleji pomocí `as_download()` místo `as_mount()`; To se dá ověřit experimentálně.

### <a name="accessing-source-code-during-training"></a>Přístup ke zdrojovému kódu během školení

Azure Blob Storage má vyšší propustnost než sdílenou složku Azure a bude se škálovat na velký počet spuštěných paralelně. Z tohoto důvodu doporučujeme nakonfigurovat vaše běhy na používání úložiště objektů BLOB pro přenos souborů zdrojového kódu.

Následující příklad kódu určuje v konfiguraci spuštění, které úložiště dat objektu BLOB se má použít pro přenosy zdrojového kódu.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Přístup k datům během bodování

Azure Machine Learning poskytuje několik způsobů, jak používat vaše modely pro bodování. Některé z těchto metod neposkytují přístup k úložišti dat. Následující tabulka vám pomůže pochopit, které metody umožňují přístup k úložišti dat během bodování:

| Metoda | Přístup k úložišti dat | Popis |
| ----- | :-----: | ----- |
| [Předpověď dávky](how-to-run-batch-predictions.md) | ✔ | Provádějte asynchronní předpovědi velké množství dat. |
| [Webová služba](how-to-deploy-and-where.md) | &nbsp; | Nasadit modely jako webovou službu. |
| [Modul IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Nasaďte modely do IoT Edgech zařízení. |

V situacích, kdy sada SDK neposkytuje přístup k úložiště dat, může být možné vytvořit vlastní kód pomocí příslušné sady Azure SDK pro přístup k datům. Například [sada SDK Azure Storage pro Python](https://github.com/Azure/azure-storage-python) je Klientská knihovna, kterou můžete použít pro přístup k datům uloženým v objektech blob nebo souborech.

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>Přesun dat na podporovaná řešení Azure Storage

Azure Machine Learning podporuje přístup k datům z objektů blob Azure, Azure File, Azure Data Lake Gen 1, Azure Data Lake Gen 2, Azure SQL a Azure PostgreSQL. V případě nepodporovaného úložiště ušetříte náklady na výstup dat během experimentů strojového učení, doporučujeme přesunout data do našich podporovaných řešení Azure Storage pomocí Azure Data Factory. Azure Data Factory poskytuje efektivní a odolný přenos dat s více než 80 předem sestavenými konektory, včetně datových služeb Azure, místních zdrojů dat, Amazon S3 a RedShift a Google BigQuery – bez dalších poplatků. [Postupujte podle podrobných pokynů a přesuňte data pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

## <a name="next-steps"></a>Další kroky

* [Trénování modelu](how-to-train-ml-models.md).

* [Nasazení modelu](how-to-deploy-and-where.md).
