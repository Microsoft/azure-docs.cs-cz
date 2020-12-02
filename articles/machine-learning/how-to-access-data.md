---
title: Připojení ke službám úložiště v Azure
titleSuffix: Azure Machine Learning
description: Naučte se používat úložiště dat k zabezpečenému připojení ke službám Azure Storage během školení pomocí Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 11/03/2020
ms.custom: how-to, contperfq1, devx-track-python, data4ml
ms.openlocfilehash: c85989dc02bddd136c56a81993df7a507a84d3b4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460285"
---
# <a name="connect-to-storage-services-on-azure"></a>Připojení ke službám úložiště v Azure

V tomto článku se dozvíte, jak se **připojit ke službám úložiště v Azure prostřednictvím Azure Machine Learning úložiště dat**. DataStore se bezpečně připojují ke službě Azure Storage bez nutnosti zadat přihlašovací údaje pro ověřování a integritu původního zdroje dat. Ukládají informace o připojení, například ID předplatného a autorizaci tokenu v [Key Vault](https://azure.microsoft.com/services/key-vault/) přidružené k pracovnímu prostoru, takže můžete bezpečně přistupovat k úložišti, aniž byste je museli zakódovat ve svých skriptech. K vytvoření a registraci úložišť dat můžete použít [sadu SDK Azure Machine Learning Python](#python) nebo [Azure Machine Learning Studio](how-to-connect-data-ui.md) .

Pokud dáváte přednost vytváření a správě úložiště dat pomocí rozšíření Azure Machine Learning VS Code; Další informace najdete v tématu [Průvodce správou prostředků vs Code](how-to-manage-resources-vscode.md#datastores) .

Z [těchto řešení Azure Storage](#matrix)můžete vytvořit úložiště dat. **Pro Nepodporovaná řešení úložišť** a za účelem úspory nákladů na výstup dat během experimentů ml [přesuňte data](#move) do podporovaného řešení úložiště Azure.  

Informace o tom, kde je úložiště dat vhodné v rámci celkového pracovního postupu pro přístup k datům v Azure Machine Learning, najdete v článku [zabezpečený přístup k datům](concept-data.md#data-workflow) .

## <a name="prerequisites"></a>Předpoklady

Budete potřebovat:
- Předplatné Azure. Pokud ještě předplatné Azure nemáte, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

- Účet úložiště Azure s [podporovaným typem úložiště](#matrix).

- [Sada SDK Azure Machine Learning pro Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)nebo přístup k [Azure Machine Learning Studiu](https://ml.azure.com/).

- Pracovní prostor služby Azure Machine Learning.
  
  Buď [Vytvořte pracovní prostor Azure Machine Learning](how-to-manage-workspace.md) nebo použijte existující sadu pomocí sady Python SDK. 

    Importujte `Workspace` třídu a a `Datastore` načtěte ze souboru informace o svém předplatném `config.json` pomocí funkce `from_config()` . Ve výchozím nastavení vyhledá soubor JSON v aktuálním adresáři, ale můžete také zadat parametr cesty, který bude odkazovat na soubor pomocí `from_config(path="your/file/path")` .

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

    Když vytvoříte pracovní prostor, kontejner objektů BLOB v Azure a sdílená složka Azure se automaticky zaregistrují jako úložiště dat do pracovního prostoru. Jsou pojmenované `workspaceblobstore` a `workspacefilestore` v uvedeném pořadí. `workspaceblobstore`Slouží k ukládání artefaktů pracovního prostoru a protokolů experimentování ve službě Machine Learning. Je také nastaven jako **výchozí úložiště dat** a nelze jej odstranit z pracovního prostoru. `workspacefilestore`Slouží k ukládání poznámkových bloků a skriptů R autorizovaných pomocí [výpočetní instance](./concept-compute-instance.md#accessing-files).
    
    > [!NOTE]
    > Azure Machine Learning Designer vytvoří úložiště dat s názvem **azureml_globaldatasets** automaticky, když otevřete ukázku na domovské stránce návrháře. Toto úložiště dat obsahuje jenom ukázkové datové sady. Nepoužívejte **prosím toto** úložiště dat pro přístup k důvěrným datům.

<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Podporované typy služby úložiště dat

Úložiště dat v současné době podporují ukládání informací o připojení do služby úložiště uvedené v následující matici.

| &nbsp;Typ úložiště | &nbsp;Typ ověřování | [Azure &nbsp; Machine &nbsp; Learning Studio](https://ml.azure.com/) | [Sada SDK pro službu Azure &nbsp; Machine &nbsp; Learning &nbsp; Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) |  [Rozhraní příkazového &nbsp; řádku Azure Machine &nbsp; Learning](reference-azure-machine-learning-cli.md) | [&nbsp; &nbsp; &nbsp; Rozhraní REST API služby Azure Machine Learning](/rest/api/azureml/) | VS Code
---|---|---|---|---|---|---
[&nbsp;Úložiště objektů BLOB v Azure &nbsp;](../storage/blobs/storage-blobs-overview.md)| Klíč účtu <br> Token SAS | ✓ | ✓ | ✓ |✓ |✓
[&nbsp; &nbsp; Sdílená složka Azure](../storage/files/storage-files-introduction.md)| Klíč účtu <br> Token SAS | ✓ | ✓ | ✓ |✓|✓
[Azure &nbsp; Data Lake &nbsp; Storage – &nbsp; 1. generace](../data-lake-store/index.yml)| Instanční objekt| ✓ | ✓ | ✓ |✓|
[&nbsp;Úložiště Azure Data Lake – &nbsp; 2. generace &nbsp;](../storage/blobs/data-lake-storage-introduction.md)| Instanční objekt| ✓ | ✓ | ✓ |✓|
[&nbsp;Databáze SQL &nbsp; Azure](../azure-sql/database/sql-database-paas-overview.md)| Ověřování SQL <br>Instanční objekt| ✓ | ✓ | ✓ |✓|
[&nbsp;PostgreSQL Azure](../postgresql/overview.md) | Ověřování SQL| ✓ | ✓ | ✓ |✓|
[Azure &nbsp; Database &nbsp; for &nbsp; MySQL](../mysql/overview.md) | Ověřování SQL|  | ✓* | ✓* |✓*|
[Systém souborů datacihly &nbsp; &nbsp;](/azure/databricks/data/databricks-file-system)| Žádné ověřování | | ✓** | ✓ ** |✓** |

\*MySQL se podporuje jenom pro [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?preserve-view=true&view=azure-ml-py) kanálu.<br />
\*\*Datacihly se podporují jenom pro [DatabricksStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?preserve-view=true&view=azure-ml-py) kanálu.


### <a name="storage-guidance"></a>Pokyny k ukládání

Pro [kontejner objektů blob Azure](../storage/blobs/storage-blobs-introduction.md)doporučujeme vytvořit úložiště dat. Úložiště úrovně Standard a Premium jsou k dispozici pro objekty blob. I když je Premium Storage dražší, jejich rychlejší přenosová rychlost může zlepšit rychlost vašich školicích běhů, zejména pokud budete vlakovat s velkou datovou sadou. Informace o nákladech na účty úložiště najdete v [cenové kalkulačkě Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) je postavená na službě Azure Blob Storage a určená pro analýzy velkých objemů dat v podniku. Základní část Data Lake Storage Gen2 je přidání [hierarchického oboru názvů](../storage/blobs/data-lake-storage-namespace.md) do úložiště objektů BLOB. Hierarchický obor názvů uspořádává objekty/soubory do hierarchie adresářů pro efektivní přístup k datům.

## <a name="storage-access-and-permissions"></a>Přístup k úložišti a oprávnění

Aby bylo zajištěno zabezpečené připojení ke službě Azure Storage, Azure Machine Learning vyžaduje, abyste měli oprávnění pro přístup k odpovídajícímu kontejneru úložiště dat. Tento přístup závisí na přihlašovacích údajích používaných k registraci úložiště dat. 

### <a name="virtual-network"></a>Virtuální síť 

Pokud je váš účet úložiště dat ve **virtuální síti**, jsou potřeba další kroky konfigurace, abyste zajistili, že Azure Machine Learning má přístup k vašim datům. Pokud chcete zajistit, aby se při vytváření a registraci úložiště dat používaly příslušné kroky konfigurace, přečtěte si téma [použití Azure Machine Learning studia ve službě Azure Virtual Network](how-to-enable-studio-virtual-network.md) .  

### <a name="access-validation"></a>Ověření přístupu

V **rámci počátečního procesu vytváření a registrace úložiště dat** Azure Machine Learning automaticky ověřuje, zda existuje základní služba úložiště a uživatel zadal objekt zabezpečení (uživatelské jméno, instanční objekt nebo token SAS), který má přístup k zadanému úložišti.

**Po vytvoření úložiště dat** se toto ověřování provede jenom u metod, které vyžadují přístup k podkladové kontejneru úložiště, **ne** při každém načtení objektů úložiště dat. K ověření dojde například v případě, že chcete stáhnout soubory z úložiště dat. Pokud ale chcete pouze změnit výchozí úložiště dat, pak k ověření nedojde.

Pokud chcete ověřit přístup k základní službě úložiště, můžete v odpovídající `register_azure_*()` metodě typu úložiště dat, který chcete vytvořit, zadat buď klíč účtu, tokeny sdíleného přístupu (SAS) nebo instanční objekt. V [matici typ úložiště](#matrix) jsou uvedené podporované typy ověřování, které odpovídají jednotlivým typům úložiště dat.

Klíč účtu, token SAS a informace o instančním objektu najdete na svém [Azure Portal](https://portal.azure.com).

* Pokud plánujete použít klíč účtu nebo token SAS pro ověřování, v levém podokně vyberte **účty úložiště** a zvolte účet úložiště, který chcete zaregistrovat. 
  * Stránka **Přehled** poskytuje informace, jako je název účtu, kontejner a název sdílené složky. 
      1. Klíče účtu najdete v části **přístupové klíče** v podokně **Nastavení** . 
      1. V případě tokenů SAS přejděte na **sdílené přístupové podpisy** v podokně **Nastavení** .

* Pokud máte v úmyslu použít instanční objekt k ověřování, otevřete **Registrace aplikací** a vyberte aplikaci, kterou chcete použít. 
    * Jeho odpovídající stránka **přehledu** bude obsahovat požadované informace, jako je ID TENANTA a ID klienta.

> [!IMPORTANT]
> * Pokud potřebujete změnit přístupové klíče pro účet Azure Storage (klíč účtu nebo token SAS), nezapomeňte synchronizovat nové přihlašovací údaje s vaším pracovním prostorem a úložiště dat, která jsou k němu připojená. Přečtěte si, jak [synchronizovat aktualizované přihlašovací údaje](how-to-change-storage-access-key.md). 
### <a name="permissions"></a>Oprávnění

V případě kontejneru objektů blob Azure a Azure Data Lake úložiště Gen 2 se ujistěte, že přihlašovací údaje mají přístup ke **čtečce dat objektů BLOB úložiště** . Přečtěte si další informace o [čtečce dat objektů BLOB úložiště](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Výchozí token SAS účtu nemá žádná oprávnění. 
* Pro **přístup ke čtení** dat musí mít přihlašovací údaje pro ověřování minimální oprávnění seznam a čtení pro kontejnery a objekty. 

* Pro **přístup k zápisu** dat se vyžadují taky oprávnění zapisovat a přidat.

<a name="python"></a>

## <a name="create-and-register-datastores"></a>Vytvoření a registrace úložišť dat

Když zaregistrujete řešení Azure Storage jako úložiště dat, automaticky vytvoříte a zaregistrujete toto úložiště dat do konkrétního pracovního prostoru. Pokyny k scénářům virtuální sítě najdete v části věnované [oprávněním pro přístup & k úložišti](#storage-access-and-permissions) a kde najít požadovaná ověřovací pověření. 

V této části jsou příklady, jak vytvořit a zaregistrovat úložiště dat pomocí sady Python SDK pro následující typy úložišť. Parametry uvedené v těchto příkladech jsou **požadované parametry** pro vytvoření a registraci úložiště dat.

* [Kontejner objektů blob Azure](#azure-blob-container)
* [Sdílená složka Azure](#azure-file-share)
* [Azure Data Lake Storage generace 2](#azure-data-lake-storage-generation-2)

 Chcete-li vytvořit úložiště dat pro jiné podporované služby úložiště, přečtěte si [referenční dokumentaci pro příslušné `register_azure_*` metody](/python/api/azureml-core/azureml.core.datastore.datastore?preserve-view=true&view=azure-ml-py#&preserve-view=truemethods).

Pokud dáváte přednost prostředí s nízkým kódem, přečtěte si téma [připojení k datům pomocí Azure Machine Learning studia](how-to-connect-data-ui.md).
>[!IMPORTANT]
> Pokud zrušíte registraci a znovu zaregistrujete úložiště dat se stejným názvem, ale Azure Key Vault pro váš pracovní prostor nemusí mít povolené obnovitelné odstranění. Ve výchozím nastavení je obnovitelné odstranění povoleno pro instanci trezoru klíčů vytvořenou v pracovním prostoru, ale nemusí být povolená, pokud jste použili existující Trezor klíčů nebo máte pracovní prostor vytvořený před 15. října 2020. Informace o tom, jak povolit obnovitelné odstranění, najdete v tématu [Zapnutí obnovitelného odstranění pro existující Trezor klíčů]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault).

> [!NOTE]
> Název úložiště dat by měl obsahovat jenom malá písmena, číslice a podtržítka. 

### <a name="azure-blob-container"></a>Kontejner objektů blob Azure

Pokud chcete zaregistrovat kontejner objektů blob Azure jako úložiště dat, použijte [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

Následující kód vytvoří a zaregistruje `blob_datastore_name` úložiště dat do `ws` pracovního prostoru. Toto úložiště dat přistupuje k `my-container-name` kontejneru objektů BLOB v `my-account-name` účtu úložiště pomocí poskytnutého přístupového klíče účtu. Pokyny k scénářům virtuální sítě najdete v části věnované [oprávněním pro přístup & k úložišti](#storage-access-and-permissions) a kde najít požadovaná ověřovací pověření. 

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

### <a name="azure-file-share"></a>Sdílená složka Azure

Pokud chcete zaregistrovat sdílenou složku Azure jako úložiště dat, použijte [`register_azure_file_share()`](/python/api/azureml-core/azureml.core.datastore%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) . 

Následující kód vytvoří a zaregistruje `file_datastore_name` úložiště dat do `ws` pracovního prostoru. Toto úložiště dat přistupuje ke `my-fileshare-name` sdílené složce v `my-account-name` účtu úložiště pomocí poskytnutého přístupového klíče účtu. Pokyny k scénářům virtuální sítě najdete v části věnované [oprávněním pro přístup & k úložišti](#storage-access-and-permissions) a kde najít požadovaná ověřovací pověření. 

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

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage generace 2

Pro úložiště dat Azure Data Lake Storage generace 2 (ADLS Gen 2) použijte [register_azure_data_lake_gen2 ()](/python/api/azureml-core/azureml.core.datastore.datastore?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) k registraci úložiště dat přihlašovacích údajů připojeného k úložišti Azure datalake Gen 2 s [oprávněními instančního objektu](../active-directory/develop/howto-create-service-principal-portal.md).  

Aby bylo možné použít instanční objekt, musíte [aplikaci zaregistrovat](../active-directory/develop/app-objects-and-service-principals.md) a udělit přístup k datům instančního objektu prostřednictvím řízení přístupu na základě role Azure (Azure RBAC) nebo seznamů řízení přístupu (ACL). Přečtěte si další informace o [nastavení řízení přístupu pro adls Gen 2](../storage/blobs/data-lake-storage-access-control-model.md). 

Následující kód vytvoří a zaregistruje `adlsgen2_datastore_name` úložiště dat do `ws` pracovního prostoru. Toto úložiště dat přistupuje k systému souborů `test` v `account_name` účtu úložiště pomocí zadaných přihlašovacích údajů instančního objektu. Pokyny k scénářům virtuální sítě najdete v části věnované [oprávněním pro přístup & k úložišti](#storage-access-and-permissions) a kde najít požadovaná ověřovací pověření. 

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

<a name="arm"></a>

## <a name="create-datastores-using-azure-resource-manager"></a>Vytvoření úložiště dat pomocí Azure Resource Manager

[https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-datastore-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/)K vytvoření úložiště dat je možné použít několik šablon.

Informace o použití těchto šablon naleznete v tématu [použití šablony Azure Resource Manager k vytvoření pracovního prostoru pro Azure Machine Learning](how-to-create-workspace-template.md).

<a name="train"></a>
## <a name="use-data-in-your-datastores"></a>Použití dat v úložišti dat

Po vytvoření úložiště dat [vytvořte Azure Machine Learning datovou sadu](how-to-create-register-datasets.md) pro interakci s daty. Datové sady zabalí vaše data do laxně vytvářená vyhodnoceného spotřebního objektu pro úlohy strojového učení, jako je například školení. Také poskytují možnost [stahovat nebo připojovat](how-to-train-with-datasets.md#mount-vs-download) soubory libovolného formátu ze služeb Azure Storage, jako je Azure Blob Storage a adls Gen 2. Můžete je také použít k načtení tabulkových dat do datového rámce PANDAS nebo Spark.

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Získat úložiště dat z vašeho pracovního prostoru

Chcete-li získat konkrétní úložiště dat registrované v aktuálním pracovním prostoru, použijte [`get()`](/python/api/azureml-core/azureml.core.datastore%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-workspace--datastore-name-) statickou metodu pro `Datastore` třídu:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Chcete-li získat seznam úložišť dat zaregistrovaných v daném pracovním prostoru, můžete použít [`datastores`](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedatastores) vlastnost v objektu pracovního prostoru:

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
Výchozí úložiště dat můžete také změnit pomocí následujícího kódu. Tato možnost je podporována pouze prostřednictvím sady SDK. 

```Python
 ws.set_default_datastore(new_default_datastore)
```

## <a name="access-data-during-scoring"></a>Přístup k datům během bodování

Azure Machine Learning poskytuje několik způsobů, jak používat vaše modely pro bodování. Některé z těchto metod neposkytují přístup k úložiště dat. Následující tabulka vám pomůže pochopit, které metody umožňují přístup k úložišti dat během bodování:

| Metoda | Přístup k úložišti dat | Popis |
| ----- | :-----: | ----- |
| [Dávkové předpovědi](./tutorial-pipeline-batch-scoring-classification.md) | ✔ | Provádějte asynchronní předpovědi velké množství dat. |
| [Webová služba](how-to-deploy-and-where.md) | &nbsp; | Nasaďte modely jako webovou službu. |
| [Modul Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Nasaďte modely pro IoT Edge zařízení. |

V situacích, kdy sada SDK neposkytuje přístup k úložiště dat, může být možné vytvořit vlastní kód pomocí příslušné sady Azure SDK pro přístup k datům. Například [sada SDK Azure Storage pro Python](https://github.com/Azure/azure-storage-python) je Klientská knihovna, kterou můžete použít pro přístup k datům uloženým v objektech blob nebo souborech.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Přesun dat na podporovaná řešení Azure Storage

Azure Machine Learning podporuje přístup k datům ze služby Azure Blob Storage, souborů Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database a Azure Database for PostgreSQL. Pokud používáte nepodporované úložiště, doporučujeme přesunout vaše data na podporovaná řešení úložiště Azure pomocí [Azure Data Factory a těchto kroků](../data-factory/quickstart-create-data-factory-copy-data-tool.md). Přesunutí dat do podporovaného úložiště vám umožní ušetřit náklady na výstup dat během experimentů strojového učení. 

Azure Data Factory poskytuje efektivní a odolný přenos dat s více než 80 předem sestavenými konektory bez dalších nákladů. Mezi tyto konektory patří datové služby Azure, místní zdroje dat, Amazon S3 a RedShift a Google BigQuery.

## <a name="next-steps"></a>Další kroky

* [Vytvoření datové sady služby Azure Machine Learning](how-to-create-register-datasets.md)
* [Učení modelu](how-to-set-up-training-targets.md)
* [Nasazení modelu](how-to-deploy-and-where.md)