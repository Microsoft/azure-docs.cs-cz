---
title: Připojení ke službám úložiště Azure
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí datových úložišť bezpečně připojit ke službám úložiště Azure během školení pomocí Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: ca892b5f360f523ee2b5ff875dfb0707136a5ab5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383446"
---
# <a name="connect-to-azure-storage-services"></a>Připojení ke službám úložiště Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak se připojit ke službám úložiště Azure prostřednictvím datových úložišť Azure Machine Learning. Datastores ukládat informace o připojení, jako je vaše ID předplatného a autorizace tokenu ve vašem [trezoru klíčů](https://azure.microsoft.com/services/key-vault/) přidružené k pracovníploše, takže můžete bezpečně přistupovat k úložišti, aniž byste je museli pevně kódovat ve skriptech.

Úložiště dat můžete vytvořit z [těchto řešení úložiště Azure](#matrix). Pro nepodporovaná řešení úložiště a pro uložení nákladů na odchozí přenos dat během experimentů strojového učení doporučujeme [přesunout data](#move) do podporovaných řešení úložiště Azure. 

## <a name="prerequisites"></a>Požadavky

Budete potřebovat:
- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree).

- Účet úložiště Azure s [kontejnerem objektů blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) nebo [sdílenou složkou Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- [Sada Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)nebo přístup k azure machine learning [studio](https://ml.azure.com/).

- Pracovní prostor služby Azure Machine Learning.
  
  Buď [vytvořte pracovní prostor Azure Machine Learning,](how-to-manage-workspace.md) nebo použijte existující pracovní prostor prostřednictvím sady Python SDK. Importujte `Workspace` `Datastore` třídu a a načtěte `config.json` informace `from_config()`o předplatném ze souboru pomocí funkce . Ve výchozím nastavení vyhledá soubor JSON v aktuálním adresáři, ale můžete také `from_config(path="your/file/path")`určit parametr cesty, který bude na soubor ukazovat pomocí .

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Podporované typy služeb ukládání dat

Úložiště dat aktuálně podporují ukládání informací o připojení ke službám úložiště uvedeným v následující matici.

| Typ&nbsp;úložiště | Typ&nbsp;ověřování | [Azure&nbsp;&nbsp;Machine Learning studio](https://ml.azure.com/) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Azure&nbsp;Machine&nbsp;Learning CLI](reference-azure-machine-learning-cli.md) | [Rozhraní&nbsp;&nbsp;API&nbsp; pro odpočinek v machine learningu Azure](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Úložiště&nbsp;objektů&nbsp;blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Klíč účtu <br> Token SAS | ✓ | ✓ | ✓ |✓
[Azure&nbsp;&nbsp;sdílení souborů](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Klíč účtu <br> Token SAS | ✓ | ✓ | ✓ |✓
[Úložiště&nbsp;datového jezera&nbsp;Azure Gen&nbsp;1](https://docs.microsoft.com/azure/data-lake-store/)| Instanční objekt| ✓ | ✓ | ✓ |✓
[Úložiště&nbsp;datového jezera&nbsp;Azure Gen&nbsp;2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Instanční objekt| ✓ | ✓ | ✓ |✓
[Databáze&nbsp;&nbsp;Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| Ověřování pomocí SQL <br>Instanční objekt| ✓ | ✓ | ✓ |✓
[Azure&nbsp;PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | Ověřování pomocí SQL| ✓ | ✓ | ✓ |✓
[Databáze&nbsp;&nbsp;Azure&nbsp;pro MySQL](https://docs.microsoft.com/azure/mysql/overview) | Ověřování pomocí SQL|  | ✓* | ✓* |✓*
[Systém souborů&nbsp;&nbsp;Databricks](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| Bez ověřování | | ✓** | ✓ ** |✓** 

*MySQL je podporováno pouze pro kanál [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). <br>
**Databricks je podporován pouze pro potrubí [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)

### <a name="storage-guidance"></a>Pokyny k ukládání

Doporučujeme vytvořit úložiště dat pro [kontejner objektů blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Standardní i prémiové úložiště je k dispozici pro objekty BLOB. Přestože je prémiové úložiště dražší, jeho rychlejší rychlost propustnosti může zvýšit rychlost tréninku, zejména pokud trénujete proti velké datové sadě. Informace o nákladech na účty úložiště najdete v [cenové kalkulačce Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) je postavené na azure blob storage a je navržené pro podnikové analýzy velkých objemů dat. Základní součástí Data Lake Storage Gen2 je přidání [hierarchického oboru názvů](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) do úložiště objektů Blob. Hierarchický obor názvů uspořádá objekty/soubory do hierarchie adresářů pro efektivní přístup k datům.

Když vytvoříte pracovní prostor, kontejner objektů blob Azure a sdílená složka Azure se automaticky zaregistrují do pracovního prostoru. Jsou pojmenovány `workspaceblobstore` `workspacefilestore`a , v uvedeném pořadí. `workspaceblobstore`slouží k ukládání artefaktů pracovního prostoru a protokolů experimentů strojového učení. `workspacefilestore`se používá k ukládání poznámkových bloků a skriptů R autorizovaných prostřednictvím [instance compute](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files). Kontejner `workspaceblobstore` je nastaven jako výchozí úložiště dat.

> [!IMPORTANT]
> Návrhář Azure Machine Learning (preview) vytvoří úložiště dat s názvem **azureml_globaldatasets** automaticky při otevření ukázky na domovské stránce návrháře. Toto úložiště dat obsahuje pouze ukázkové datové sady. **Nepoužívejte** prosím toto úložiště dat pro žádný důvěrný přístup k datům.
> ![Automaticky vytvořené úložiště dat pro ukázkové datové sady návrháře](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Vytvoření a registrace úložišť dat

Když zaregistrujete řešení úložiště Azure jako úložiště dat, automaticky vytvoříte a zaregistrujete toto úložiště dat do určitého pracovního prostoru. Úložiště dat můžete vytvořit a zaregistrovat do pracovního prostoru pomocí sady Python SDK nebo studia Azure Machine Learning.

>[!IMPORTANT]
> Jako součást počátečníúložiště dat vytvořit a zaregistrovat proces Azure Machine Learning ověří, že základní služba úložiště existuje a že uživatel za předpokladu, hlavní (uživatelské jméno, instanční objekt nebo token SAS) má přístup k tomuto úložišti. Pro Azure Data Lake Storage Gen 1 a 2 úložiště dat však [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) toto ověření se stane později, když metody přístupu k datům jako nebo jsou volány. 
<br><br>
Po vytvoření úložiště dat se toto ověření provádí pouze pro metody, které vyžadují přístup k podkladovému kontejneru úložiště, **nikoli** při každém načtení objektů úložiště dat. Například ověření se stane, pokud chcete stáhnout soubory z úložiště dat; ale pokud chcete změnit výchozí úložiště dat, pak se ověření nestane.

### <a name="python-sdk"></a>Python SDK

Všechny metody registru jsou [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) ve třídě `register_azure_*`a mají formulář .

Informace, které potřebujete k naplnění `register()` metody, najdete na [webu Azure Portal](https://portal.azure.com).
V levém podokně vyberte **Účty úložiště** a vyberte účet úložiště, který chcete zaregistrovat. Stránka **Přehled** obsahuje informace, jako je název účtu, kontejner a název sdílené složky. 

* Informace o položkách ověřování, jako je klíč účtu nebo token SAS, přejděte v podokně **Nastavení** na **přístupové klíče.** 

* Pokud chcete použít hlavní položky služby, jako je ID klienta a ID klienta, přejděte na **registrace aplikací** a vyberte, kterou aplikaci chcete použít. Jeho odpovídající **přehled** stránka bude obsahovat tyto položky.

> [!IMPORTANT]
> Pokud je váš účet úložiště ve virtuální síti, je podporováno pouze vytváření objektů Blob, sdílené složky, datových úložišť ADLS Gen 1 a ADLS Gen 2 **prostřednictvím sady SDK.** Chcete-li pracovnímu prostoru udělit přístup `grant_workspace_access` k `True`účtu úložiště, nastavte parametr na .

Následující příklady ukazují, jak zaregistrovat kontejner objektů blob Azure, sdílenou složku Azure a Azure Data Lake Storage Generation 2 jako úložiště dat. Další služby skladování naleznete v [referenční `register_azure_*` dokumentaci pro příslušné metody](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods).

#### <a name="blob-container"></a>Kontejner objektů blob

Chcete-li zaregistrovat kontejner objektů blob [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)Azure jako úložiště dat, použijte .

Následující kód vytvoří a `blob_datastore_name` zaregistruje úložiště `ws` dat do pracovního prostoru. Toto úložiště dat `my-container-name` přistupuje k kontejneru objektů blob v `my-account-name` účtu úložiště pomocí zadaný přístupový klíč účtu.

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

#### <a name="file-share"></a>Sdílená složka

Chcete-li zaregistrovat sdílenou složku [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)Azure jako úložiště dat, použijte . 

Následující kód vytvoří a `file_datastore_name` zaregistruje úložiště `ws` dat do pracovního prostoru. Toto úložiště dat `my-fileshare-name` přistupuje ke sdílené složce v `my-account-name` účtu úložiště pomocí zadaný klíč přístupu k účtu.

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```

#### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Generace 2

Pro úložiště dat Azure Data Lake Storage Generation 2 (ADLS Gen 2) použijte [register_azure_data_lake_gen2()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) k registraci úložiště dat přihlašovacích údajů připojeného k úložišti Azure DataLake Gen 2 s [oprávněními hlavního mocní ho s oprávněními hlavního nastavení služby](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). Chcete-li využít instanční objekt, musíte [zaregistrovat vaši žádost](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) a udělit instančnímu objektu správného přístupu k datům. Další informace o [nastavení řízení přístupu pro ADLS Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Následující kód vytvoří a `adlsgen2_datastore_name` zaregistruje úložiště `ws` dat do pracovního prostoru. Toto úložiště dat přistupuje k systému `test` souborů v `account_name` účtu úložiště pomocí pověření zadaný objekt zabezpečení služby.

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

Vytvořte nové úložiště dat v několika krocích ve studiu Azure Machine Learning:

> [!IMPORTANT]
> Pokud je váš účet úložiště ve virtuální síti, je podporováno pouze vytváření úložišť dat [prostřednictvím sady SDK.](#python-sdk) 

1. Přihlaste se do [studia Azure Machine Learning Studio](https://ml.azure.com/).
1. V yberte **Datastores** v levém podokně v části **Správa**.
1. Vyberte **+ Nové úložiště dat**.
1. Vyplňte formulář pro nové úložiště dat. Formulář se inteligentně aktualizuje na základě vašich výběrů pro typ úložiště Azure a typ ověřování.
  
Informace, které potřebujete k vyplnění formuláře, najdete na [webu Azure Portal](https://portal.azure.com). V levém podokně vyberte **Účty úložiště** a vyberte účet úložiště, který chcete zaregistrovat. Stránka **Přehled** obsahuje informace, jako je název účtu, kontejner a název sdílené složky. 

* Informace o položkách ověřování, jako je klíč účtu nebo token SAS, přejděte v podokně **Nastavení** na **přístupové klíče.** 

* Pokud chcete použít hlavní položky služby, jako je ID klienta a ID klienta, přejděte na **registrace aplikací** a vyberte, kterou aplikaci chcete použít. Jeho odpovídající **přehled** stránka bude obsahovat tyto položky. 

Následující příklad ukazuje, jak formulář vypadá při vytváření úložiště dat objektů blob Azure: 
    
![Formulář pro nové úložiště dat](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Získání datových úložišť z pracovního prostoru

Chcete-li získat konkrétní úložiště dat registrované v [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) aktuálním `Datastore` pracovním prostoru, použijte statickou metodu ve třídě:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Chcete-li získat seznam datových úložišť registrovaných v [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) daném pracovním prostoru, můžete použít vlastnost na objektu pracovního prostoru:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Chcete-li získat výchozí úložiště dat pracovního prostoru, použijte tento řádek:

```Python
datastore = ws.get_default_datastore()
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Nahrávání a stahování dat

A [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) metody popsané v následujících příkladech jsou specifické pro třídy [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) a [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) a fungují stejně pro je.

### <a name="upload"></a>Odeslat

Nahrajte adresář nebo jednotlivé soubory do úložiště dat pomocí sady Python SDK:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

Parametr `target_path` určuje umístění ve sdílené složce (nebo kontejneru objektů blob), které chcete nahrát. Je výchozí `None`na , takže data jsou odeslány do kořenového adresáře. Pokud `overwrite=True`jsou všechna `target_path` existující data na adrese přepsána.

Můžete také nahrát seznam jednotlivých souborů do `upload_files()` úložiště dat pomocí metody.

### <a name="download"></a>Stáhnout

Stáhněte data z úložiště dat do místního systému souborů:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Parametr `target_path` je umístění místního adresáře, do jehož stažení jsou data. Chcete-li určit cestu ke složce ve sdílené složce (nebo kontejneru objektů blob), kterou chcete stáhnout, zadejte tuto cestu k aplikaci `prefix`. Pokud `prefix` `None`je , veškerý obsah sdílené složky (nebo kontejner objektů blob) se stáhne.

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Přístup k datům během školení

Chcete-li pracovat s daty ve vašem úložišti dat nebo zabalit data do spotřebního objektu pro úlohy strojového učení, jako je školení, [vytvořte datovou sadu Azure Machine Learning](how-to-create-register-datasets.md). Datové sady poskytují funkce, které načítají tabulková data do pand nebo Spark DataFrame. Datové sady také poskytují možnost stahovat nebo připojovat soubory libovolného formátu z úložiště objektů Blob Azure, souborů Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database a Azure Database for PostgreSQL. [Další informace o trénování pomocí datových sad](how-to-train-with-datasets.md).

### <a name="accessing-source-code-during-training"></a>Přístup ke zdrojovému kódu během školení

Úložiště objektů blob Azure má vyšší rychlosti propustnosti než sdílená složka Azure a bude škálovat na velký počet úloh spuštěných paralelně. Z tohoto důvodu doporučujeme nakonfigurovat spuštění tak, aby se pro přenos souborů zdrojového kódu používalo úložiště objektů Blob.

Následující příklad kódu určuje v konfiguraci spuštění, které úložiště dat blob použít pro přenosy zdrojového kódu.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Přístup k datům během vyhodnocování

Azure Machine Learning poskytuje několik způsobů, jak použít vaše modely pro vyhodnocování. Některé z těchto metod neposkytují přístup k úložišti dat. V následující tabulce můžete pochopit, které metody umožňují přístup k úložištím dat během vyhodnocování:

| Metoda | Přístup k úložišti dat | Popis |
| ----- | :-----: | ----- |
| [Dávkové předpovědi](how-to-use-parallel-run-step.md) | ✔ | Proveďte předpovědi na velké množství dat asynchronně. |
| [Webová služba](how-to-deploy-and-where.md) | &nbsp; | Nasaďte modely jako webovou službu. |
| [Modul Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Nasazujte modely do zařízení IoT Edge. |

V situacích, kdy sada SDK neposkytuje přístup k úložištím dat, můžete vytvořit vlastní kód pomocí příslušné sady Azure SDK pro přístup k datům. Například [Sada Azure Storage SDK pro Python](https://github.com/Azure/azure-storage-python) je klientská knihovna, kterou můžete použít k přístupu k datům uloženým v objektech BLOB nebo souborech.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Přesunutí dat do podporovaných řešení úložišť Azure

Azure Machine Learning podporuje přístup k datům z úložiště objektů Blob Azure, souborů Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database a Azure Database for PostgreSQL. Pokud používáte nepodporované úložiště, doporučujeme přesunout data do podporovaných řešení úložiště Azure pomocí [Azure Data Factory a těmito kroky](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool). Přesunutí dat do podporovaného úložiště vám pomůže ušetřit náklady na odchozí přenos dat během experimentů strojového učení. 

Azure Data Factory poskytuje efektivní a odolný přenos dat s více než 80 předem předdefinovanými konektory bez dalších nákladů. Tyto konektory zahrnují datové služby Azure, místní zdroje dat, Amazon S3 a Redshift a Google BigQuery.

## <a name="next-steps"></a>Další kroky

* [Vytvoření datové sady Azure pro strojové učení](how-to-create-register-datasets.md)
* [Učení modelu](how-to-train-ml-models.md)
* [Nasazení modelu](how-to-deploy-and-where.md)
