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
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 410867ae034309db0bb013ae22f90e8489aa463e
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886107"
---
# <a name="access-data-from-your-datastores"></a>Přístup k datům z vašich úložišť
V tomto článku se naučíte různými způsoby pro přístup k a interagování s daty v Azure Machine Learning pracovních postupů prostřednictvím úložišť.

Tento návod ukazuje příklady pro následující úlohy: 
* Vytváření a přístup k úložišti dat přístupném
* [Nahrávání a stahování dat do úložišť](#upload-and-download-data)
* [Přístup k úložišti dat pro školení](#access-datastores-for-training)

<a name="access"></a>

## <a name="create-and-access-a-datastore"></a>Vytváření a přístup k úložišti dat přístupném
Chcete-li použít úložiště, musíte nejprve [pracovní prostor](concept-azure-machine-learning-architecture.md#workspace). Začněte tím, že buď [vytváří se nový pracovní prostor](quickstart-create-workspace-with-python.md) nebo některý z existujících načítání:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>Použít výchozí úložiště dat
Není nutné vytvořit nebo nakonfigurovat účet úložiště.  Každý pracovní prostor má výchozí úložiště dat, které chcete začít používat okamžitě.

Chcete-li získat výchozí pracovním prostoru úložiště dat:
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>Registrace datového úložiště
Pokud máte existující služby Azure Storage, můžete ho zaregistrovat jako úložiště dat ve svém pracovním prostoru.  Všechny metody registrace jsou na [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) třídy a mít formulář register_azure_ *. Následující příklady ukazují registraci sdílené složky Azure a služby kontejneru objektů Blob Azure jako datového úložiště.

#### <a name="azure-blob-container-datastore"></a>Kontejner úložiště objektů Blob v Azure
Chcete-li zaregistrovat Azure kontejner objektů Blob úložiště dat, použijte [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-:)

```Python
ds = Datastore.register_azure_blob_container(workspace=ws, 
                                             datastore_name='your datastore name', 
                                             container_name='your azure blob container name',
                                             account_name='your storage account name', 
                                             account_key='your storage account key',
                                             create_if_not_exists=True)
```

#### <a name="azure-file-share-datastore"></a>Úložiště sdílené složky Azure File
Pokud chcete zaregistrovat úložišti dat sdílené složky Azure, použijte [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)

```Python
ds = Datastore.register_azure_file_share(workspace=ws, 
                                         datastore_name='your datastore name', 
                                         container_name='your file share name',
                                         account_name='your storage account name', 
                                         account_key='your storage account key',
                                         create_if_not_exists=True)
```

### <a name="get-an-existing-datastore"></a>Získat existující úložiště dat
[ `get()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) Metoda dotazy na již zaregistrované úložiště dat podle názvu:

```Python
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Můžete také získat všechna úložiště dat pro pracovní prostor:

```Python
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

Pro usnadnění práce [ `set_default_datastore()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) nastaví výchozí úložiště dat pro váš pracovní prostor úložiště, podle toho, která zvolíte:

```Python
ws.set_default_datastore('your datastore name')
```

## <a name="upload-and-download-data"></a>Nahrávání a stahování dat
### <a name="upload"></a>Odeslat
Nahrajte do adresáře nebo jednotlivé soubory do úložiště dat pomocí sady Python SDK.

K nahrání do úložiště dat do adresáře `ds`:

```Python
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

## <a name="access-datastores-for-training"></a>Přístup úložišť pro školení
Dostanete během cvičení spuštění (například pro ověření nebo trénovací data) na cílové vzdálené výpočetní prostředí pomocí sady Python SDK pomocí datového úložiště [ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) třídy.

Zpřístupnit vaše úložiště dat na vzdálený výpočetní dvě podporované způsoby:
* **Připojení**  

    * [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), zadáte v tomto režimu připojení, úložiště získá připojené za vás na vzdálené výpočetní prostředky.

    ```Python
    import azureml.data
    from azureml.data import DataReference

    ds.as_mount()
    ```

* **Stažení nebo nahrávání.**  
    * [`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), stáhne data z umístění, které určuje `path_on_compute` na vaše úložiště dat pro vzdálené výpočetní prostředky.

    * [`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), odesílá data do kořenového adresáře vašeho úložiště dat z umístění, které určuje `path_on_compute`

    ```Python
    ds.as_download(path_on_compute='your path on compute')
    ds.as_upload(path_on_compute='yourfilename')
    ```   

Chcete-li odkazovat na konkrétní složky nebo souboru v vaše úložiště dat, použijte úložiště dat [ `path()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) funkce.

```Python
#download the contents of the `./bar` directory from the datastore 
ds.path('./bar').as_download()
```
Žádné `ds` nebo `ds.path` objektu se překládá na název proměnné prostředí ve formátu `"$AZUREML_DATAREFERENCE_XXXX"` jehož hodnota představuje cestu k připojení a stahování na vzdálené výpočetní prostředky. Cesta úložiště dat na vzdálený výpočetní nemusí být stejný jako cesta provedení skriptu.

Pro přístup k vaší úložiště dat během cvičení, předejte ho do trénovací skript jako argument příkazového řádku prostřednictvím `script_params` z [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) třídy:

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
`as_mount()` je výchozí režim úložiště dat, abyste mohli předat také přímo `ds` k `'--data_dir'` argument.

Nebo v seznamu úložišť předat konstruktoru Estimator `inputs` parametr připojování nebo zkopírovat do a z vašeho úložiště dat

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

V předchozím příkladu kódu:

* Stáhne veškerý obsah v úložišti dat `ds1` na vzdálený výpočetní před cvičný skript `train.py` spuštění

* Soubory ke stažení složce `'./foo'` v úložišti dat `ds2` na vzdálený výpočetní před `train.py` spuštění

* Nahraje soubor `'./bar.pkl'` ze vzdálené výpočetní až úložiště `d3` po spuštění skriptu

## <a name="next-steps"></a>Další postup

* [Trénování modelu](how-to-train-ml-models.md)
* [Nasazení modelu](how-to-deploy-and-where.md)

