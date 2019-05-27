---
title: Přístup k datům v úložišť / objektů blob pro školení
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak používat úložiště pro přístup k úložišti objektů blob data během cvičení se službou Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 02/25/2019
ms.custom: seodec18
ms.openlocfilehash: 15118535578419f9e1230c5b2fcfd0d7c42257ea
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65908998"
---
# <a name="access-data-from-your-datastores"></a>Přístup k datům z vašich úložišť

 Ve službě Azure Machine Learning jsou úložišť výpočetní mechanismů, bez ohledu na umístění úložiště s přístupem nevyžaduje změny zdrojového kódu. Zda zapisovat školení kódu bude fungovat jako parametr cestu nebo zadejte úložiště dat přímo do odhaduje, zkontrolujte pracovní postupy Azure Machine Learning umístění úložiště dat jsou k dispozici a k dispozici výpočetní kontext.

Tento návod ukazuje příklady z následujících úloh:
* [Vyberte úložiště dat](#access)
* [Získání dat](#get)
* [Nahrávání a stahování dat do úložišť](#up-and-down)
* [Přístup k úložišti dat během cvičení](#train)

## <a name="prerequisites"></a>Požadavky

Chcete-li použít úložiště, musíte nejprve [pracovní prostor](concept-workspace.md).

Začněte tím, že buď [vytváří se nový pracovní prostor](setup-create-workspace.md#sdk) nebo některý z existujících načítání:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>Vyberte úložiště dat

Můžete použít výchozí úložiště dat nebo funkce přineste si vlastní.

### <a name="use-the-default-datastore-in-your-workspace"></a>Použít výchozí úložiště dat ve vašem pracovním prostoru

 Každý pracovní prostor má registrovaný, výchozí úložiště dat, které můžete okamžitě použít.

Chcete-li získat výchozí pracovním prostoru úložiště dat:

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>Zaregistrujte své vlastní úložiště dat s pracovním prostorem

Pokud máte existující služby Azure Storage, můžete ho zaregistrovat jako úložiště dat ve svém pracovním prostoru.   Všechny metody registrace jsou na [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) třídy a mít formulář register_azure_ *. 

Následující příklady ukazují registraci kontejneru objektů Blob v Azure nebo sdílené složky Azure jako datového úložiště.

+ Pro **úložiště kontejner objektů Blob v Azure**, použijte [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ Pro **úložiště dat sdílené složky Azure File**, použijte [ `register_azure_file_share()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). Příklad: 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           file_share_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>Najít & definovat úložišť

Zadané úložiště dat zaregistrovaný do aktuálního pracovního prostoru získáte pomocí [ `get()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) :

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Pokud chcete získat seznam všech úložišť v daném pracovním prostoru, použijte tento kód:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

Chcete-li definovat různé výchozí úložiště pro aktuálního pracovního prostoru, použijte [ `set_default_datastore()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-):

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Nahrávání a stahování dat
[ `upload()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) a [ `download()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) metod popsaných v následujících příkladech jsou specifické pro a pracovat stejně jako u [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) a [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) třídy.

### <a name="upload"></a>Nahrávání

 Nahrajte do adresáře nebo jednotlivé soubory do úložiště dat pomocí sady Python SDK.

K nahrání do úložiště dat do adresáře `ds`:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```

`target_path` Určuje umístění sdílené složky (nebo kontejneru objektů blob) k nahrání. Použije se výchozí `None`, v takovém případě získá nahrát data do kořenového adresáře. `overwrite=True` přepíše všechny existující data v `target_path`.

Nebo můžete nahrát seznam jednotlivých souborů s úložištěm prostřednictvím úložiště dat `upload_files()` metody.

### <a name="download"></a>Ke stažení
Podobně stahování dat z úložiště dat do vašeho místního systému souborů.

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```

`target_path` je umístění pro stahování dat do místního adresáře. Chcete-li zadat cestu ke složce ve sdílené složky (nebo kontejneru objektů blob) ke stažení, zadejte cestu tak, `prefix`. Pokud `prefix` je `None`, se stáhne veškerý obsah sdílené složky (nebo kontejneru objektů blob).

<a name="train"></a>
## <a name="access-datastores-during-training"></a>Úložišť přístup během cvičení

Po provedení vaše úložiště dat dostupné v cílové výpočetní prostředí, můžete předáním jednoduše cestu k němu jako parametr v cvičný skript přístup během tréninkových spuštění (například školení nebo ověřovací data).

Následující tabulce jsou uvedeny [ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) metody, které informují cílové výpočetní prostředí, jak používat úložiště během spuštění.

způsob, jak|Metoda|Popis|
----|-----|--------
Připojit| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Slouží k připojení úložiště v cílové výpočetní prostředí.
Ke stažení|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Použijte ke stažení obsahu z vašeho úložiště dat do umístění určeného proměnnou `path_on_compute`. <br> Pro kontext školení, spusťte tento soubor ke stažení se provede před spuštění.
Nahrávání|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Slouží k nahrávání souboru z umístění, které určuje `path_on_compute` do vašeho úložiště. <br> Pro kontext spuštění školení odesílání dochází po spuštění.

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Chcete-li odkazovat na konkrétní složky nebo souboru v vaše úložiště dat a zpřístupnit ji na cílové výpočetní prostředí, použijte úložiště dat [ `path()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) funkce.

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> Žádné `ds` nebo `ds.path` objektu se překládá na název proměnné prostředí ve formátu `"$AZUREML_DATAREFERENCE_XXXX"` jehož hodnota představuje cestu k připojení a stahování na cílové výpočetní prostředky. Cesta úložiště dat na cílové výpočetní nemusí být stejný jako cesta provedení pro cvičný skript.

### <a name="compute-context-and-datastore-type-matrix"></a>Výpočetní kontext a úložiště dat typu matice

Následující matice zobrazí funkce technologie přístup dostupných dat pro různými výpočetními scénáři kontextu a úložiště. Pojem "Kanál" v této matici představuje možnost používat úložiště jako vstup nebo výstup v [Azure Machine Learning kanály](https://docs.microsoft.com/azure/machine-learning/service/concept-ml-pipelines).

||Místní výpočty|Azure Machine Learning Compute|Přenos dat|Databricks|HDInsight|Azure Batch|Azure DataLake Analytics|Virtuální počítače|
-|--|-----------|----------|---------|-----|--------------|---------|---------|
|AzureBlobDatastore|[`as_download()`] [`as_upload()`]|[`as_mount()`]<br> [`as_download()`] [`as_upload()`] <br> Kanál|Kanál|Kanál|[`as_download()`] <br> [`as_upload()`]|Kanál||[`as_download()`] <br> [`as_upload()`]|
|AzureFileDatastore|[`as_download()`] [`as_upload()`]|[`as_mount()`]<br> [`as_download()`] [`as_upload()`] Kanálu |||[`as_download()`] [`as_upload()`]|||[`as_download()`] [`as_upload()`]|
|AzureDataLakeDatastore|||Kanál|Kanál|||Kanál||
|AzureDataLakeGen2Datastore|||Kanál||||||
|AzureDataPostgresSqlDatastore|||Kanál||||||
|AzureSqlDatabaseDataDatastore|||Kanál||||||


> [!NOTE]
> Můžou existovat scénáře, ve kterých vysoce iterativní velkých objemů dat procesy jsou spuštěny rychleji pomocí [`as_download()`] místo [`as_mount()`]; to může být ověřen experimentálně.

### <a name="examples"></a>Příklady 

Následující příklady kódu, které jsou specifické pro [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) třídy pro přístup k vaší úložiště dat během cvičení.

Tento kód vytvoří odhadu pomocí skriptu školení `train.py`, z uvedených zdrojový adresář pomocí parametrů definovaných v `script_params`, všechny na cílové zadaný výpočetní prostředí.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Můžete také předat seznam úložišť do konstruktoru Estimator `inputs` parametr připojování nebo zkopírovat do a z vašeho úložiště dat. Tento příklad kódu:
* Stáhne veškerý obsah v úložišti dat `ds1` do cílového výpočetního prostředí před cvičný skript `train.py` spuštění
* Soubory ke stažení složce `'./foo'` v úložišti dat `ds2` do cílového výpočetního prostředí před `train.py` spuštění
* Nahraje soubor `'./bar.pkl'` z cílového výpočetního prostředí až po úložiště `ds3` po spuštění skriptu

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="next-steps"></a>Další postup

* [Trénování modelu](how-to-train-ml-models.md)

* [Nasazení modelu](how-to-deploy-and-where.md)
