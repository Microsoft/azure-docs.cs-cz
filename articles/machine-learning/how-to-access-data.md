---
title: Přístup k datům ve službě Azure Storage Services
titleSuffix: Azure Machine Learning
description: Naučte se používat úložiště dat k zabezpečenému připojení ke službám Azure Storage během školení pomocí Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: keli19
author: likebupt
ms.reviewer: nibaccam
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: 5527d58f140074279d6b9feac13803f29eb0bda5
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255775"
---
# <a name="access-data-in-azure-storage-services"></a>Přístup k datům ve službě Azure Storage
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak snadno získat přístup k datům ve službě Azure Storage Services přes Azure Machine Learning úložiště dat. Úložiště dat slouží k ukládání informací o připojení, jako je ID předplatného a autorizace tokenu. Když použijete úložiště dat, budete mít přístup k úložišti, aniž byste museli v skriptech zakódovat informace o připojení. 

Z [těchto řešení Azure Storage](#matrix)můžete vytvořit úložiště dat. Pro Nepodporovaná řešení úložišť a za účelem úspory nákladů na výstup dat během experimentů machine learningu doporučujeme [přesunout data](#move) na podporovaná Azure Storage řešení. 

## <a name="prerequisites"></a>Předpoklady
Budete potřebovat:
- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

- Účet služby Azure Storage s [kontejnerem objektů blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) nebo [sdílenou složkou Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- [Sada SDK Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)nebo přístup k [Azure Machine Learning Studiu](https://ml.azure.com/).

- Pracovní prostor služby Azure Machine Learning.
  
  Buď [Vytvořte pracovní prostor Azure Machine Learning](how-to-manage-workspace.md) nebo použijte existující sadu pomocí sady Python SDK. Importujte třídu `Workspace` a `Datastore` a načtěte informace o předplatném ze souboru `config.json` pomocí `from_config()`funkce. Ve výchozím nastavení vyhledá soubor JSON v aktuálním adresáři, ale můžete také zadat parametr cesty, který bude odkazovat na soubor pomocí `from_config(path="your/file/path")`.

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Podporované typy služby úložiště dat

Úložiště dat v současné době podporují ukládání informací o připojení do služby úložiště uvedené v následující matici. V tuto chvíli není Azure Data Warehouse podporovaný. 

| Typ&nbsp;úložiště | Typ&nbsp;ověřování | [Azure&nbsp;Machine –&nbsp;Learning Studio](https://ml.azure.com/) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Rozhraní příkazového řádku Azure&nbsp;Machine&nbsp;Learning](reference-azure-machine-learning-cli.md) | [Rozhraní REST API pro Azure&nbsp;Machine&nbsp;Learning&nbsp;](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Úložiště Azure&nbsp;BLOB&nbsp;](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Klíč účtu <br> Token SAS | ✓ | ✓ | ✓ |✓
[Soubor Azure&nbsp;&nbsp;sdílená složka](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Klíč účtu <br> Token SAS | ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;úložiště – obecné&nbsp;1](https://docs.microsoft.com/azure/data-lake-store/)| Instanční objekt| ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;Storage gen&nbsp;2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Instanční objekt| ✓ | ✓ | ✓ |✓
Databáze&nbsp;Azure&nbsp;SQL| Ověřování pomocí SQL <br>Instanční objekt| ✓ | ✓ | ✓ |✓
PostgreSQL Azure&nbsp; | Ověřování pomocí SQL| ✓ | ✓ | ✓ |✓
&nbsp;databáze služby Azure&nbsp;pro&nbsp;MySQL | Ověřování pomocí SQL|  | ✓* | ✓* |✓*
Datacihly&nbsp;systém souborů&nbsp;| Bez ověřování | | ✓** | ✓ ** |✓** 

*MySQL se podporuje jenom pro [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py)kanálu. <br> \** Datacihly se podporují jenom pro [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) kanálu.

### <a name="storage-guidance"></a>Pokyny k ukládání

Pro kontejner objektů blob Azure doporučujeme vytvořit úložiště dat.  
Úložiště úrovně Standard a Premium jsou k dispozici pro objekty blob. I když je Premium Storage dražší, jejich rychlejší přenosová rychlost může zlepšit rychlost vašich školicích běhů, zejména pokud budete vlakovat s velkou datovou sadou. Informace o nákladech na účty úložiště najdete v [cenové kalkulačkě Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

Když vytvoříte pracovní prostor, kontejner objektů BLOB v Azure a sdílená složka Azure se automaticky zaregistrují do pracovního prostoru. Jsou pojmenovány `workspaceblobstore` a `workspacefilestore`v uvedeném pořadí. Ukládají informace o připojení pro kontejner objektů BLOB a sdílenou složku, která je zřízená v účtu úložiště připojeném k pracovnímu prostoru. Kontejner `workspaceblobstore` je nastaven jako výchozí úložiště dat.

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Vytvoření a registrace úložišť dat

Když zaregistrujete řešení Azure Storage jako úložiště dat, automaticky vytvoříte a zaregistrujete toto úložiště dat do konkrétního pracovního prostoru. Úložiště dat můžete vytvořit a zaregistrovat v pracovním prostoru pomocí sady Python SDK nebo Azure Machine Learning studia.

>[!IMPORTANT]
> V rámci aktuálního procesu vytvoření a registrace úložiště dat Azure Machine Learning ověří, že uživatel zadaný jako objekt zabezpečení (uživatelské jméno, instanční objekt nebo token SAS) má přístup k příslušné službě úložiště. 
<br><br>
U Azure Data Lake Storageových úložišť Gen 1 a 2 však toto ověření proběhne později, pokud jsou volány metody přístupu k datům, jako je [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) nebo [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) . 

### <a name="python-sdk"></a>Python SDK

Všechny metody registru jsou na třídě [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) a mají `register_azure_*`formuláře.

Informace, které potřebujete k naplnění metody `register()`, najdete v [Azure Portal](https://portal.azure.com).
V levém podokně vyberte **účty úložiště** a zvolte účet úložiště, který chcete zaregistrovat. Stránka **Přehled** poskytuje informace, jako je název účtu, kontejner a název sdílené složky. 

* U položek ověřování, jako je klíč účtu nebo token SAS, v podokně **Nastavení** přejít na **klíče účtu** . 

* V případě položek instančního objektu, jako je ID tenanta a ID klienta, přejdete na **Registrace aplikací** a vyberte, kterou aplikaci chcete použít. Příslušné stránky s **přehledem** budou obsahovat tyto položky.

> [!IMPORTANT]
> Pokud je váš účet úložiště ve virtuální síti, podporuje se jenom vytváření objektů blob, sdílení souborů, ADLS a ADLS Gen 2 pro úložiště dat **prostřednictvím sady SDK** . Pokud chcete vašemu pracovnímu prostoru udělit přístup k vašemu účtu úložiště, nastavte parametr `grant_workspace_access` na `True`.

Následující příklady ukazují, jak zaregistrovat kontejner objektů blob Azure, sdílenou složku Azure a Azure Data Lake Storage generaci 2 jako úložiště dat. Další služby úložiště najdete v [referenční dokumentaci pro metody `register_azure_*`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods).

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

#### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage generace 2

Pro úložiště dat Azure Data Lake Storage generace 2 (ADLS Gen 2) použijte [register_azure_data_lake_gen2 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) k registraci úložiště dat přihlašovacích údajů připojeného k úložišti Azure datalake Gen 2 s [oprávněními instančního objektu](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). Aby bylo možné využívat instanční objekt, potřebujete [zaregistrovat svoji aplikaci](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) a nastavit přiřazení rolí pro čtenáře a přístup k datům. Přečtěte si další informace o [nastavení řízení přístupu pro adls Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Následující kód vytvoří a zaregistruje úložiště dat `adlsgen2_datastore_name` do pracovního prostoru `ws`. Toto úložiště dat přistupuje k `test` systému souborů v účtu úložiště `account_name` pomocí zadaných přihlašovacích údajů instančního objektu.

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio 

Vytvořte nové úložiště dat v několika krocích v Azure Machine Learning Studiu:

> [!IMPORTANT]
> Pokud je váš účet úložiště ve virtuální síti, podporuje se jenom vytváření úložiště dat [prostřednictvím sady SDK](#python-sdk) . 

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com/).
1. V levém podokně v části **Spravovat**vyberte **úložiště** .
1. Vyberte **+ nové úložiště dat**.
1. Vyplňte formulář pro nové úložiště dat. Formulář se inteligentně aktualizuje na základě vašich výběrů pro Azure Storage typ a typ ověřování.
  
Můžete najít informace, které potřebujete k naplnění formuláře na [Azure Portal](https://portal.azure.com). V levém podokně vyberte **účty úložiště** a zvolte účet úložiště, který chcete zaregistrovat. Stránka **Přehled** poskytuje informace, jako je název účtu, kontejner a název sdílené složky. 

* U položek ověřování, jako je klíč účtu nebo token SAS, v podokně **Nastavení** přejít na **klíče účtu** . 

* V případě položek instančního objektu, jako je ID tenanta a ID klienta, přejdete na **Registrace aplikací** a vyberte, kterou aplikaci chcete použít. Příslušné stránky s **přehledem** budou obsahovat tyto položky. 

Následující příklad ukazuje, jak formulář vypadá při vytváření úložiště dat objektů BLOB v Azure: 
    
![Formulář pro nové úložiště dat](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Získat úložiště dat z vašeho pracovního prostoru

> [!IMPORTANT]
> Azure Machine Learning Designer (Preview) vytvoří úložiště dat s názvem **azureml_globaldatasets** automaticky, když otevřete ukázku na domovské stránce návrháře. Toto úložiště dat obsahuje jenom ukázkové datové sady. Nepoužívejte **prosím toto** úložiště dat pro přístup k důvěrným datům.
> ![automaticky vytvořené úložiště dat pro ukázkové datové sady návrháře](media/how-to-access-data/datastore-designer-sample.png)

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

### <a name="upload"></a>Odeslat

Nahrát buď adresář nebo jednotlivé soubory do úložiště dat pomocí sady Python SDK:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

Parametr `target_path` určuje umístění sdílené složky (nebo kontejneru objektů BLOB), který se má nahrát. Výchozí hodnota je `None`, takže data se nahrají do kořenového adresáře. Pokud `overwrite=True`, všechna existující data v `target_path` se přepíší.

Můžete také nahrát seznam jednotlivých souborů do úložiště dat prostřednictvím metody `upload_files()`.

### <a name="download"></a>Ke stažení

Stáhněte data z úložiště dat do místního systému souborů:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Parametr `target_path` je umístění místního adresáře, do kterého se mají data stahovat. Chcete-li zadat cestu ke složce ve sdílené složce (nebo kontejneru objektů BLOB) ke stažení, zadejte tuto cestu k `prefix`. Pokud je `prefix` `None`, stáhne se veškerý obsah sdílené složky (nebo kontejneru objektů BLOB).

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Přístup k datům během školení

Pokud chcete pracovat s daty v úložišti dat nebo zabalit data do spotřebního objektu pro úlohy strojového učení, jako je například školení, [vytvořte Azure Machine Learning datovou sadu](how-to-create-register-datasets.md). Datové sady poskytují funkce, které načítají tabulková data do datového rámce PANDAS nebo Spark. Datové sady také poskytují možnost stahovat nebo připojovat soubory libovolného formátu z služby Azure Blob Storage, souborů Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database a Azure Database for PostgreSQL. [Přečtěte si další informace o tom, jak naučit s datovými sadami](how-to-train-with-datasets.md).

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
| [Předpověď dávky](how-to-use-parallel-run-step.md) | ✔ | Provádějte asynchronní předpovědi velké množství dat. |
| [Webová služba](how-to-deploy-and-where.md) | &nbsp; | Nasaďte modely jako webovou službu. |
| [Modul Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Nasaďte modely pro IoT Edge zařízení. |

V situacích, kdy sada SDK neposkytuje přístup k úložiště dat, může být možné vytvořit vlastní kód pomocí příslušné sady Azure SDK pro přístup k datům. Například [sada SDK Azure Storage pro Python](https://github.com/Azure/azure-storage-python) je Klientská knihovna, kterou můžete použít pro přístup k datům uloženým v objektech blob nebo souborech.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Přesun dat na podporovaná Azure Storage řešení

Azure Machine Learning podporuje přístup k datům ze služby Azure Blob Storage, souborů Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database a Azure Database for PostgreSQL. Pokud používáte nepodporované úložiště, doporučujeme přesunout vaše data na podporovaná Azure Storage řešení pomocí [Azure Data Factory a těchto kroků](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool). Přesunutí dat do podporovaného úložiště vám umožní ušetřit náklady na výstup dat během experimentů strojového učení. 

Azure Data Factory poskytuje efektivní a odolný přenos dat s více než 80 předem sestavenými konektory bez dalších nákladů. Mezi tyto konektory patří datové služby Azure, místní zdroje dat, Amazon S3 a RedShift a Google BigQuery.

## <a name="next-steps"></a>Další kroky

* [Vytvoření datové sady služby Azure Machine Learning](how-to-create-register-datasets.md)
* [Výuka modelu](how-to-train-ml-models.md)
* [Nasazení modelu](how-to-deploy-and-where.md)
