---
title: Použití úložiště ve službě Azure Machine Learning pro přístup k datům
description: Jak používat úložiště na úložiště dat během cvičení
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: ef183397cf7ca3f1f561f6951290b722df31e5ed
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52620196"
---
# <a name="how-to-access-data-during-training"></a>Jak získat přístup k datům během cvičení
Úložiště dat se používají pro přístup a interagování s daty v pracovních postupech Azure Machine Learning.

Úložiště ve službě Azure Machine Learning, je abstrakcí nad [služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction). Úložiště můžete odkazovat buď [objektů Blob v Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) kontejneru nebo [sdílené složky Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) jako základní úložiště. 

## <a name="create-a-datastore"></a>Vytvoření datového úložiště
Chcete-li použít úložiště, musíte nejprve [pracovní prostor](concept-azure-machine-learning-architecture.md#workspace). Začněte tím, že buď [vytváří se nový pracovní prostor](quickstart-create-workspace-with-python.md) nebo některý z existujících načítání:

```Python
import azureml.core
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>Použít výchozí úložiště dat
Není nutné vytvořit nebo nakonfigurovat účet úložiště.  Každý pracovní prostor má výchozí úložiště dat, které chcete začít používat okamžitě.

Chcete-li získat výchozí pracovním prostoru úložiště dat:
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>Registrace datového úložiště
Pokud máte existující služby Azure Storage, můžete ho zaregistrovat jako úložiště dat ve svém pracovním prostoru. Kontejneru objektů Blob v Azure nebo sdílené složky Azure můžete zaregistrovat jako datového úložiště. Všechny metody registrace jsou na `Datastore` třídy a mít formát _služba ._protokol `register_azure_*`.

#### <a name="azure-blob-container-datastore"></a>Kontejner úložiště objektů Blob v Azure
Postup pro registraci Azure kontejner objektů Blob úložiště dat:

```Python
ds = Datastore.register_azure_blob_container(workspace=ws, 
                                             datastore_name='your datastore name', 
                                             container_name='your azure blob container name',
                                             account_name='your storage account name', 
                                             account_key='your storage account key',
                                             create_if_not_exists=True)
```

#### <a name="azure-file-share-datastore"></a>Úložiště sdílené složky Azure File
Postup pro registraci úložišti dat sdílené složky Azure:

```Python
ds = Datastore.register_azure_file_share(workspace=ws, 
                                         datastore_name='your datastore name', 
                                         container_name='your file share name',
                                         account_name='your storage account name', 
                                         account_key='your storage account key',
                                         create_if_not_exists=True)
```

### <a name="get-an-existing-datastore"></a>Získat existující úložiště dat
Dotaz pro registrovaného úložiště dat podle názvu:
```Python
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Můžete také získat všechna úložiště dat pro pracovní prostor:
```Python
datastores = ws.datastores()
for name, ds in datastores.items(),
    print(name, ds.datastore_type)
```

Pro usnadnění práce nastavte jednu z registrovaných úložišť jako výchozí úložiště dat pro váš pracovní prostor:
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
Úložiště dat můžete zpřístupnit během cvičení spuštění (například pro ověření nebo trénovací data) na cílové vzdálené výpočetní prostředí pomocí sady Python SDK. 

Zpřístupnit vaše úložiště dat na vzdálený výpočetní dvě podporované způsoby:
* **Připojení**  
`ds.as_mount()`: zadáním tento režim připojení úložiště se ve vzdálené výpočetní připojit za vás. 
* **Stažení nebo nahrávání.**  
    * `ds.as_download(path_on_compute='your path on compute')` stáhne data z vašeho úložiště dat do vzdáleného výpočetních prostředků a umístění, které určuje `path_on_compute`.
    * `ds.as_upload(path_on_compute='yourfilename'` odešle data do úložiště.  Předpokládejme, že vytvoří cvičný skript `foo.pkl` soubor v aktuálním pracovním adresáři na vzdálené výpočetní prostředky. Tento soubor nahrajte do vašeho úložiště dat s `ds.as_upload(path_on_compute='./foo.pkl')` po skript vytvoří soubor. Se soubor nahraje do kořenového adresáře vašeho úložiště.
    
Chcete-li odkazovat na konkrétní složky nebo souboru v vaše úložiště dat, použijte úložiště dat **`path`** funkce. Například pro stahování obsahu `./bar` z úložiště do cílového výpočetního prostředí, použijte adresáře `ds.path('./bar').as_download()`.

Žádné `ds` nebo `ds.path` objektu se překládá na název proměnné prostředí ve formátu `"$AZUREML_DATAREFERENCE_XXXX"` jehož hodnota představuje cestu k připojení a stahování na vzdálené výpočetní prostředky. Cesta úložiště dat na vzdálený výpočetní nemusí být stejný jako cesta provedení skriptu.

Pro přístup k vaší úložiště dat během cvičení, předejte ho do trénovací skript jako argument příkazového řádku prostřednictvím `script_params`:

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
`as_mount()` je výchozí režim úložiště dat, abyste mohli předat také přímo pouze `ds` k `'--data_dir'` argument.

Nebo v seznamu úložišť předat konstruktoru Estimator `inputs` parametr připojování nebo zkopírovat do a z vašeho úložiště dat:

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```
Výše uvedený kód bude:
* Stáhnout veškerý obsah v úložišti dat `ds1` na vzdálený výpočetní před cvičný skript `train.py` spuštění
* Stáhněte složku `'./foo'` v úložišti dat `ds2` na vzdálený výpočetní před `train.py` spuštění
* Nahrajte soubor `'./bar.pkl'` ze vzdálené výpočetní až úložiště `d3` po spuštění skriptu

## <a name="next-steps"></a>Další postup
* [Trénování modelu](how-to-train-ml-models.md)
