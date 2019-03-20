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
ms.openlocfilehash: e6e1b304b90b37c93bed22bcb720a646680ee083
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58223610"
---
# <a name="access-data-from-your-datastores"></a>Přístup k datům z vašich úložišť

Úložiště umožňují interakci s a přistupovat ke svým datům, ať už jsou spuštěné kódu místně, na výpočetním clusteru, nebo na virtuálním počítači. V tomto článku přečtěte si, že jsou k dispozici pracovní postupy Azure Machine Learning, které zajistí vaše úložiště a k dispozici k výpočetním kontextu.

Tento návod ukazuje příklady pro následující úlohy:
* [Vyberte úložiště dat](#access)
* [Získání dat](#get)
* [Nahrávání a stahování dat do úložišť](#up-and-down)
* [Přístup k úložišti dat během cvičení](#train)

## <a name="prerequisites"></a>Požadavky

Použití úložiště, je nutné [pracovní prostor](concept-azure-machine-learning-architecture.md#workspace) první. 

Začněte tím, že buď [vytváří se nový pracovní prostor](quickstart-create-workspace-with-python.md) nebo některý z existujících načítání:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

Nebo, [postupovat podle tohoto rychlého startu Python](quickstart-create-workspace-with-python.md) k použití sady SDK k vytvoření pracovního prostoru a můžete začít.

<a name="access"></a>

## <a name="choose-a-datastore"></a>Vyberte úložiště dat

Můžete použít výchozí úložiště dat nebo funkce přineste si vlastní.

### <a name="use-the-default-datastore-in-your-workspace"></a>Použít výchozí úložiště dat ve vašem pracovním prostoru

Není nutné vytvořit nebo nakonfigurovat účet úložiště, protože každý pracovní prostor má výchozí úložiště. Můžete použít, který úložiště dat ihned protože je už zaregistrovaný v pracovním prostoru. 

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
                                           container_name='your file share name',
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

### <a name="upload"></a>Odeslat

 Nahrajte do adresáře nebo jednotlivé soubory do úložiště dat pomocí sady Python SDK.

K nahrání do úložiště dat do adresáře `ds`:

```Python
import azureml.data
from azureml.data import AzureFileDatastore, AzureBlobDatastore

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

Po provedení vaše úložiště k dispozici na vzdálené výpočetní prostředky, můžete předáním jednoduše cestu k němu jako parametr v cvičný skript přístup během tréninkových spuštění (například školení nebo ověřovací data).

V následující tabulce jsou uvedeny běžné [ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) metody, zpřístupníte úložišť na vzdálené výpočetní prostředky.

# #

způsob, jak|Metoda|Popis
----|-----|--------
Připojení| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Pomocí připojte úložiště dat na vzdálené výpočetní prostředky. Výchozí režim úložiště.
Ke stažení|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Stáhněte si pomocí dat z umístění, které určuje `path_on_compute` na vaše úložiště dat pro vzdálené výpočetní prostředky.
Odeslat|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Slouží k nahrávání dat do kořenového adresáře vašeho úložiště dat z umístění, které určuje `path_on_compute`.

```Python
import azureml.data
from azureml.data import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Chcete-li odkazovat na konkrétní složky nebo souboru v vaše úložiště dat, použijte úložiště dat [ `path()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) funkce.

```Python
#download the contents of the `./bar` directory from the datastore to the remote compute
ds.path('./bar').as_download()
```



> [!NOTE]
> Žádné `ds` nebo `ds.path` objektu se překládá na název proměnné prostředí ve formátu `"$AZUREML_DATAREFERENCE_XXXX"` jehož hodnota představuje cestu k připojení a stahování na vzdálené výpočetní prostředky. Cesta úložiště dat na vzdálený výpočetní nemusí být stejný jako cesta provedení pro cvičný skript.

### <a name="examples"></a>Příklady 

Ukazují následující příklady specifické pro [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) třídy pro přístup k vaší úložiště dat během cvičení.


```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py')
```

Protože `as_mount()` je výchozím režimem pro úložiště dat, můžete také přímo předat `ds` k `'--data_dir'` argument.

Nebo v seznamu úložišť předat konstruktoru Estimator `inputs` parametr připojování nebo zkopírovat do a z vašeho úložiště dat. Tento příklad kódu:
* Stáhne veškerý obsah v úložišti dat `ds1` na vzdálený výpočetní před cvičný skript `train.py` spuštění
* Soubory ke stažení složce `'./foo'` v úložišti dat `ds2` na vzdálený výpočetní před `train.py` spuštění
* Nahraje soubor `'./bar.pkl'` ze vzdálené výpočetní až úložiště `ds3` po spuštění skriptu

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```


## <a name="next-steps"></a>Další postup

* [Trénování modelu](how-to-train-ml-models.md)

* [Nasazení modelu](how-to-deploy-and-where.md)
