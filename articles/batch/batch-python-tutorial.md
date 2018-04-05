---
title: Kurz – použití sady Azure Batch SDK pro Python | Dokumentace Microsoftu
description: Informace o základních konceptech služby Azure Batch a vytvoření jednoduchého řešení pomocí Pythonu
services: batch
documentationcenter: python
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 42cae157-d43d-47f8-88f5-486ccfd334f4
ms.service: batch
ms.devlang: python
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b620ff7d49b3e2712393de6124e31ff417220db2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="get-started-with-the-batch-sdk-for-python"></a>Začínáme se sadou SDK služby Batch pro Python

> [!div class="op_single_selector"]
> * [.NET](batch-dotnet-get-started.md)
> * [Python](batch-python-tutorial.md)
> * [Node.js](batch-nodejs-get-started.md)
>
>

V tomto článku probereme malou aplikaci Batch napsanou v Pythonu a vy se seznámíte se základními informacemi o službě [Azure Batch][azure_batch] a klientovi [Batch Python][py_azure_sdk]. Podíváme se, jak dva ukázkové skripty využívají službu Batch ke zpracování paralelní úlohy na linuxových virtuálních počítačích v cloudu, a také, jak tyto počítače komunikují se službou [Azure Storage](../storage/common/storage-introduction.md) při přípravě a načítání souborů. Seznámíte se s běžným pracovním postupem aplikací Batch a získáte základní přehled o součástech služby Batch, například o úlohách, úkolech, fondech a výpočetních uzlech.

![Pracovní postup řešení Batch (Basic)][11]<br/>

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte praktické znalosti Pythonu a umíte do jisté míry pracovat s Linuxem. Předpokládá také, že dokážete splnit požadavky na vytvoření účtů Azure, služby Batch a služby Storage, které jsou uvedeny níže.

### <a name="accounts"></a>Účty
* **Účet Azure**: Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet Azure][azure_free_account].
* **Účet Batch**: Po pořízení předplatného Azure si [vytvořte účet Azure Batch](batch-account-create-portal.md).
* **Účet Storage**: Viz část [Vytvoření účtu úložiště](../storage/common/storage-create-storage-account.md#create-a-storage-account) v článku [Informace o účtech Azure Storage](../storage/common/storage-create-storage-account.md).

### <a name="code-sample"></a>Ukázka kódu
[Ukázka kódu][github_article_samples] Pythonu pro tento kurz je jednou z mnoha ukázek kódu Batch, které najdete v úložišti na GitHubu [azure-batch-samples][github_samples]. Všechny ukázky můžete stáhnout kliknutím na **Klonovat nebo stáhnout > Stáhnout ZIP** na domovské stránce úložiště, nebo kliknutím na přímý odkaz ke stažení [azure-batch-samples-master.zip][github_samples_zip]. Po extrahování obsahu souboru ZIP najdete oba skripty pro tento kurzu v adresáři `article_samples`:

`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_client.py`<br/>
`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_task.py`

### <a name="python-environment"></a>Prostředí Python
Abyste mohli spustit ukázkový skript *python_tutorial_client.py* na místní pracovní stanici, budete potřebovat **překladač Pythonu**, který je kompatibilní s verzí **2.7** nebo **3.3+**. Skript byl otestován v Linuxu i Windows.

### <a name="cryptography-dependencies"></a>závislosti kryptografie
Je nutné nainstalovat závislosti pro knihovnu [kryptografie][crypto], které vyžadují balíčky Pythonu `azure-batch` a `azure-storage`. Proveďte jednu z následujících operací, které jsou vhodné pro vaši platformu, nebo si přečtěte podrobnosti o [instalaci kryptografie][crypto_install], kde najdete další informace:

* Ubuntu

    `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython-dev python-dev`
* CentOS

    `yum update && yum install -y gcc openssl-devel libffi-devel python-devel`
* SLES/OpenSUSE

    `zypper ref && zypper -n in libopenssl-dev libffi48-devel python-devel`
* Windows

    `pip install cryptography`

> [!NOTE]
> Pokud instalujete Python 3.3+ na Linuxu, použijte pro závislosti Pythonu ekvivalenty python3. Například na Ubuntu: `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython3-dev python3-dev`
>
>

### <a name="azure-packages"></a>Balíčky Azure
Následně nainstalujte balíčky Pythonu pro **Azure Batch** a **Azure Storage**. Oba balíčky můžete nainstalovat pomocí funkce **pip** a souboru *requirements.txt*, které najdete tady:

`/azure-batch-samples/Python/Batch/requirements.txt`

Zadejte následující příkaz **pip** pro instalaci balíčků Batch a Storage:

`pip install -r requirements.txt`

Nebo můžete balíčky Pythonu [azure-batch][pypi_batch] a [azure-storage][pypi_storage] nainstalovat ručně:

`pip install azure-batch`<br/>
`pip install azure-storage`

> [!TIP]
> Pokud používáte neprivilegovaný účet, může být potřeba, abyste k příkazům přidali předponu `sudo`. Například, `sudo pip install -r requirements.txt`. Další informace o instalaci balíčků Pythonu najdete v článku [Instalace balíčků][pypi_install] na webu python.org.
>
>

## <a name="batch-python-tutorial-code-sample"></a>Ukázka kódu Pythonu pro službu Batch
Ukázka kódu Pythonu pro službu Batch se skládá ze dvou skriptů Pythonu a několika datových souborů.

* **python_tutorial_client.py**: Komunikuje se službou Batch a se službou Storage při spouštění paralelní úlohy na výpočetních uzlech (virtuálních počítačích). Skript *python_tutorial_client.py* se spouští na místní pracovní stanici.
* **python_tutorial_task.py**: Skript, který běží na výpočetních uzlech v Azure a provádí samotnou práci. Skript *python_tutorial_task.py* v tomto příkladu analyzuje text v souboru staženém ze služby Azure Storage (vstupní soubor). Potom vytvoří textový soubor (výstupní soubor), který obsahuje seznam nejčastějších tří slov, která se zobrazují ve vstupním souboru. Po vytvoření výstupního souboru skript *python_tutorial_task.py* odešle soubor do služby Azure Storage. Soubor tak bude dostupný pro stažení do klientského skriptu, který běží na pracovní stanici. Skript *python_tutorial_task.py* běží paralelně v několika výpočetních uzlech v rámci služby Batch.
* **./data/taskdata\*.txt**: Tyto tři textové soubory zajišťují vstup pro úkoly, které jsou spouštěné na výpočetních uzlech.

Následující diagram znázorňuje primární operace, které provádí klientský skript a skript úkolu. Tento základní pracovní postup je typický pro mnoho výpočetních řešení, která jsou vytvořená pomocí služby Batch. I když nepředvádí všechny funkce, které jsou ve službě Batch dostupné, téměř každý scénář Batch bude obsahovat části tohoto pracovního postupu.

![Ukázkový pracovní postup služby Batch][8]<br/>

[**Krok 1.**](#step-1-create-storage-containers) Ve službě Azure Blob Storage vytvořte **kontejnery** .<br/>
[**Krok 2.**](#step-2-upload-task-script-and-data-files) Odešlete skript úkolu a vstupní soubory do kontejneru.<br/>
[**Krok 3.**](#step-3-create-batch-pool) Vytvořte **fond** Batch.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** Když se uzly připojí k fondu, fond **StartTask** stáhne skript úkolu (python_tutorial_task.py).<br/>
[**Krok 4.**](#step-4-create-batch-job) Vytvořte **úlohu** Batch.<br/>
[**Krok 5.**](#step-5-add-tasks-to-job) Přidejte do úlohy **úkoly**.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Úkoly jsou naplánované, aby se spustily na uzlech.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Každý úkol stáhne svoje vstupní data ze služby Azure Storage a potom zahájí spuštění.<br/>
[**Krok 6.**](#step-6-monitor-tasks) Sledujte úkoly.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Úkoly při dokončení odesílají svoje výstupní data do služby Azure Storage.<br/>
[**Krok 7.**](#step-7-download-task-output) Stáhněte si výstup úkolu ze služby Storage.

Jak jsme už zmínili, ne každé řešení Batch provede právě tyto kroky a může jich dokonce obsahovat mnohem víc, ale tato ukázka představuje procesy, které běžně bývají v řešení Batch.

## <a name="prepare-client-script"></a>Příprava klientského skriptu
Před spuštěním ukázky přidejte do skriptu *python_tutorial_client.py* přihlašovací údaje k účtu Batch a účtu Storage. Pokud jste to ještě neudělali, otevřete soubor ve svém oblíbeném editoru a aktualizujte následující řádky pomocí svých přihlašovacích údajů.

```python
# Update the Batch and Storage account credential strings below with the values
# unique to your accounts. These are used when constructing connection strings
# for the Batch and Storage client objects.

# Batch account credentials
BATCH_ACCOUNT_NAME = ""
BATCH_ACCOUNT_KEY = ""
BATCH_ACCOUNT_URL = ""

# Storage account credentials
STORAGE_ACCOUNT_NAME = ""
STORAGE_ACCOUNT_KEY = ""
```

Přihlašovací údaje k účtu Batch a k účtu služby Storage najdete v okně účtu každé služby na webu [Azure Portal][azure_portal]:

![Přihlašovací údaje služby Batch na portálu][9]
![Přihlašovací údaje služby Storage na portálu][10]<br/>

V následujících částech budeme analyzovat kroky, které skripty používají ke zpracování úloh ve službě Batch. Doporučujeme, abyste během procházení zbytku článku průběžně nahlíželi do skriptů v editoru.

Přejděte do následujícího řádku ve skriptu **python_tutorial_client.py** a začněte krokem 1:

```python
if __name__ == '__main__':
```

## <a name="step-1-create-storage-containers"></a>Krok 1: Vytvoření kontejnerů služby Storage
![Vytvoření kontejnerů ve službě Azure Storage][1]
<br/>

Batch obsahuje vestavěnou podporu pro komunikaci se službou Azure Storage. Kontejnery v účtu Storage poskytnou soubory, které potřebují úkoly spuštěné v účtu Batch. Kontejnery také poskytují místo pro ukládání výstupních dat, která úkoly vytvářejí. Skript *python_tutorial_client.py* nejdřív vytvoří tři kontejnery ve službě [Azure Blob Storage](../storage/common/storage-introduction.md#blob-storage):

* **aplikace**: Tento kontejner bude ukládat skript Pythonu spuštěný úkoly, *python_tutorial_task.py*.
* **input**: Datové soubory ke zpracování budou úkoly stahovat z kontejneru *input*.
* **output**: Když úkoly dokončí zpracování vstupního souboru, odešlou výsledky do kontejneru *output*.

K práci s účtem služby Storage a k vytvoření kontejnerů používáme balíček [azure-storage][pypi_storage], abychom vytvořili objekt [BlockBlobService][py_blockblobservice] – klienta objektů blob. Potom pomocí klienta objektů blob vytvoříme tři kontejnery v účtu Storage.

```python
import azure.storage.blob as azureblob

# Create the blob client, for use in obtaining references to
# blob storage containers and uploading files to containers.
blob_client = azureblob.BlockBlobService(
    account_name=STORAGE_ACCOUNT_NAME,
    account_key=STORAGE_ACCOUNT_KEY)

# Use the blob client to create the containers in Azure Storage if they
# don't yet exist.
APP_CONTAINER_NAME = 'application'
INPUT_CONTAINER_NAME = 'input'
OUTPUT_CONTAINER_NAME = 'output'
blob_client.create_container(APP_CONTAINER_NAME, fail_on_exist=False)
blob_client.create_container(INPUT_CONTAINER_NAME, fail_on_exist=False)
blob_client.create_container(OUTPUT_CONTAINER_NAME, fail_on_exist=False)
```

Po vytvoření kontejnerů může aplikace začít odesílat soubory, které budou úkoly používat.

> [!TIP]
> Článek [Použití služby Azure Blob Storage z Pythonu](../storage/blobs/storage-python-how-to-use-blob-storage.md) nabízí pěkný přehled o práci s kontejnery a objekty blob ve službě Azure Storage. Když začnete pracovat se službou Batch, je určitě na místě si ten článek přečíst.
>
>

## <a name="step-2-upload-task-script-and-data-files"></a>Krok 2: Odeslání skriptu úkolu a datových souborů
![Odeslání aplikačních a vstupních (datových) souborů úkolů do kontejnerů][2]
<br/>

Během operace odesílání souborů skript *python_tutorial_client.py* nejdřív definuje kolekce cest k souborům **aplikace** a **vstup**, které jsou v místním počítači. Potom tyto soubory odešle do kontejnerů, které jste vytvořili v předchozím kroku.

```python
# Paths to the task script. This script will be executed by the tasks that
# run on the compute nodes.
application_file_paths = [os.path.realpath('python_tutorial_task.py')]

# The collection of data files that are to be processed by the tasks.
input_file_paths = [os.path.realpath('./data/taskdata1.txt'),
                    os.path.realpath('./data/taskdata2.txt'),
                    os.path.realpath('./data/taskdata3.txt')]

# Upload the application script to Azure Storage. This is the script that
# will process the data files, and is executed by each of the tasks on the
# compute nodes.
application_files = [
    upload_file_to_container(blob_client, APP_CONTAINER_NAME, file_path)
    for file_path in application_file_paths]

# Upload the data files. This is the data that will be processed by each of
# the tasks executed on the compute nodes in the pool.
input_files = [
    upload_file_to_container(blob_client, INPUT_CONTAINER_NAME, file_path)
    for file_path in input_file_paths]
```

Když používáte obsah seznamu, bude se pro každý soubor v kolekci volat funkce `upload_file_to_container` a zaplní se dvě kolekce [ResourceFile][py_resource_file]. Funkce `upload_file_to_container` se zobrazí níže:

```python
def upload_file_to_container(block_blob_client, container_name, path):
    """
    Uploads a local file to an Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param str container_name: The name of the Azure Blob storage container.
    :param str file_path: The local path to the file.
    :rtype: `azure.batch.models.ResourceFile`
    :return: A ResourceFile initialized with a SAS URL appropriate for Batch
    tasks.
    """

    import datetime
    import azure.storage.blob as azureblob
    import azure.batch.models as batchmodels

    blob_name = os.path.basename(path)

    print('Uploading file {} to container [{}]...'.format(path,
                                                          container_name))

    block_blob_client.create_blob_from_path(container_name,
                                            blob_name,
                                            file_path)

    sas_token = block_blob_client.generate_blob_shared_access_signature(
        container_name,
        blob_name,
        permission=azureblob.BlobPermissions.READ,
        expiry=datetime.datetime.utcnow() + datetime.timedelta(hours=2))

    sas_url = block_blob_client.make_blob_url(container_name,
                                              blob_name,
                                              sas_token=sas_token)

    return batchmodels.ResourceFile(file_path=blob_name,
                                    blob_source=sas_url)
```

### <a name="resourcefiles"></a>ResourceFiles
[ResourceFile][py_resource_file] poskytuje úkolům v Batch adresu URL k souboru ve službě Azure Storage, který se před spuštěním úkolu stáhne do výpočetního uzlu. Vlastnost [ResourceFile][py_resource_file].**blob_source** určuje úplnou adresu URL souboru, protože existuje ve službě Azure Storage. Adresa URL může obsahovat také sdílený přístupový podpis (SAS), který zajišťuje zabezpečený přístup k souboru. Většina typů úkolů ve službě Batch obsahuje vlastnost *ResourceFiles* včetně:

* [CloudTask][py_task]
* [StartTask][py_starttask]
* [JobPreparationTask][py_jobpreptask]
* [JobReleaseTask][py_jobreltask]

Ukázka nepoužívá typy úloh JobPreparationTask nebo JobReleaseTask, ale můžete si o nich přečíst v článku [Spouštění úkolů přípravy a dokončení úlohy na výpočetních uzlech Azure Batch](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Sdílený přístupový podpis (SAS)
Sdílené přístupové podpisy jsou řetězce, které zajišťují zabezpečený přístup ke kontejnerům a objektům blob ve službě Azure Storage. Skript *python_tutorial_client.py* používá sdílené přístupové podpisy objektu blob i kontejneru a ukazuje, jak můžete tyto řetězce sdíleného přístupového podpisu získat ze služby Storage.

* **Sdílené přístupové podpisy objektů blob**: StartTask fondu používá sdílené přístupové podpisy objektů blob při stahování skriptu úkolu a vstupních datových souborů ze služby Storage (viz [krok 3](#step-3-create-batch-pool) níže). Funkce `upload_file_to_container` v skriptu *python_tutorial_client.py* obsahuje kód, který získá sdílený přístupový podpis jednotlivých objektů blob. Provede to voláním [BlockBlobService.make_blob_url][py_make_blob_url] v modulu služby Storage.
* **Sdílený přístupový podpis kontejneru**: Když každý úkol dokončí svojí práci ve výpočetním uzlu, odešle svůj výstupní soubor do kontejneru *výstupního* kontejneru ve službě Azure Storage. Aby to mohl udělat, skript *python_tutorial_task.py* použije sdílený přístupový podpis kontejneru, který nabízí oprávnění k zápisu do kontejneru. Funkce `get_container_sas_token` ve skriptu *python_tutorial_client.py* získá sdílený přístupový podpis kontejneru, který se potom předá do úkolů jako argument příkazového řádku. Krok 5 [Přidání úkolů do úlohy](#step-5-add-tasks-to-job) popisuje použití sdíleného přístupového podpisu kontejneru.

> [!TIP]
> Přečtěte si dvoudílný článek, který pojednává o sdíleném přístupovém podpisu [Část 1: Vysvětlení modelu sdíleného přístupového podpisu (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) a [Část 2: Vytvoření a používání sdíleného přístupového podpisu (SAS) se službou objektů blob](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md). Dozvíte se další informace o zajišťování bezpečného přístupu k datům v účtu Storage.
>
>

## <a name="step-3-create-batch-pool"></a>Krok 3: Vytvoření fondu služby Batch
![Vytvořte fond Batch.][3]
<br/>

**Fond** Batch je kolekce výpočetních uzlů (virtuálních počítačů), na kterých služba Batch provádí úkoly z úlohy.

Po odeslání skriptu úkolu a datových souborů do účtu Storage zahájí skript *python_tutorial_client.py* pomocí modulu Batch Python komunikaci se službou Batch. Aby to mohl provést, vytvoří se [BatchServiceClient][py_batchserviceclient]:

```python
# Create a Batch service client. We'll now be interacting with the Batch
# service in addition to Storage.
credentials = batchauth.SharedKeyCredentials(BATCH_ACCOUNT_NAME,
                                             BATCH_ACCOUNT_KEY)

batch_client = batch.BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL)
```

Na účtu Batch potom pomocí volání `create_pool` vytvoří fond výpočetních uzlů.

```python
def create_pool(batch_service_client, pool_id,
                resource_files, publisher, offer, sku):
    """
    Creates a pool of compute nodes with the specified OS settings.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str pool_id: An ID for the new pool.
    :param list resource_files: A collection of resource files for the pool's
    start task.
    :param str publisher: Marketplace image publisher
    :param str offer: Marketplace image offer
    :param str sku: Marketplace image sku
    """
    print('Creating pool [{}]...'.format(pool_id))

    # Create a new pool of Linux compute nodes using an Azure Virtual Machines
    # Marketplace image. For more information about creating pools of Linux
    # nodes, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/

    # Specify the commands for the pool's start task. The start task is run
    # on each node as it joins the pool, and when it's rebooted or re-imaged.
    # We use the start task to prep the node for running our task script.
    task_commands = [
        # Copy the python_tutorial_task.py script to the "shared" directory
        # that all tasks that run on the node have access to.
        'cp -r $AZ_BATCH_TASK_WORKING_DIR/* $AZ_BATCH_NODE_SHARED_DIR',
        # Install pip and the dependencies for cryptography
        'apt-get update',
        'apt-get -y install python-pip',
        'apt-get -y install build-essential libssl-dev libffi-dev python-dev',
        # Install the azure-storage module so that the task script can access
        # Azure Blob storage
        'pip install azure-storage']

    # Get the node agent SKU and image reference for the virtual machine
    # configuration.
    # For more information about the virtual machine configuration, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/
    sku_to_use, image_ref_to_use = \
        common.helpers.select_latest_verified_vm_image_with_node_agent_sku(
            batch_service_client, publisher, offer, sku)

    new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            node_agent_sku_id=sku_to_use),
        vm_size=_POOL_VM_SIZE,
        target_dedicated=_POOL_NODE_COUNT,
        start_task=batch.models.StartTask(
            command_line=
            common.helpers.wrap_commands_in_shell('linux', task_commands),
            run_elevated=True,
            wait_for_success=True,
            resource_files=resource_files),
        )

    try:
        batch_service_client.pool.add(new_pool)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Při vytváření fondu můžete definovat [PoolAddParameter][py_pooladdparam], který určuje několik vlastností fondu:

* **ID** fondu (*id* – povinné)<p/>Stejně jako u většiny entit ve službě Batch musí mít nový fond v rámci účtu Batch jedinečné ID. Váš kód bude na tento fond odkazovat pomocí jeho ID, podle kterého tento fond můžete také identifikovat na webu [Azure Portal][azure_portal].
* **Počet výpočetních uzlů** (*target_dedicated* – povinné)<p/>Tato vlastnost určuje, kolik virtuálních počítačů má být ve fondu nasazeno. Je důležité, abyste si všimli, že všechny účty Batch mají výchozí **kvótu**, která omezuje počet **jader** (a tedy výpočetních uzlů) na účtu Batch. Výchozí kvóty a pokyny pro [navýšení kvóty](batch-quota-limit.md#increase-a-quota) (například maximální počet jader na účtu Batch) najdete v článku [Kvóty a omezení služby Azure Batch](batch-quota-limit.md). Možná vás někdy napadne otázka, proč váš fond nedosahuje víc než X uzlů. příčinou může být tato kvóta na jádra.
* **Operační systém** uzlů (*virtual_machine_configuration***nebo***cloud_service_configuration* – povinné)<p/>Ve skriptu *python_tutorial_client.py* vytvoříme fond linuxových uzlů pomocí [VirtualMachineConfiguration][py_vm_config]. Funkce `select_latest_verified_vm_image_with_node_agent_sku` v `common.helpers` zjednodušuje práci s imagemi z [Azure Virtual Machines Marketplace][vm_marketplace]. Další informace o používání imagí z Marketplace najdete v tématu [Zřízení linuxových výpočetních uzlů ve fondech Azure Batch](batch-linux-nodes.md).
* **Velikost výpočetních uzlů** (*vm_size* – povinné)<p/>Vzhledem k tomu, že zadáváme linuxové uzly pro naší [VirtualMachineConfiguration][py_vm_config], zadáme velikost virtuálního počítače (v této ukázce `STANDARD_A1`) podle článku [Velikosti virtuálních počítačů v Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Další informace opět najdete v článku [Zřízení linuxových výpočetních uzlů ve fondech Azure Batch](batch-linux-nodes.md) 
* **Spustit úkol** (*start_task* – nepovinné)<p/>Spolu s výše uvedenými fyzickými vlastnostmi uzlu můžete určit také [StartTask][py_starttask] fondu (nepovinné). StartTask se spustí na každém uzlu, když se takový uzel připojí k fondu, a taky pokaždé, když se uzel restartuje. StartTask je zvláště užitečný pro přípravu výpočetních uzlů k provádění úkolů, například k instalaci aplikací, které budou vaše úkoly spouštět.<p/>V této ukázkové aplikaci StartTask zkopíruje soubory, které stáhne ze služby Storage (které je určené vlastností **resource_files** ze StartTask) z *pracovního adresáře* StartTask do *sdíleného* adresáře, ke kterému mají přístup všechny úkoly spuštěné v takovém uzlu. V podstatě zkopíruje soubor `python_tutorial_task.py` do sdíleného adresáře v každém uzlu v okamžiku, kdy se uzel připojí k fondu, aby každý úkol spuštěný v uzlu měl k tomuto souboru přístup.

Můžete si povšimnout volání pomocné funkce `wrap_commands_in_shell`. Tato funkce vezme kolekci samostatných příkazů a vytvoří jeden příkazový řádek, který odpovídá vlastnosti příkazového řádku úkolu.

Ve výše uvedeném fragmentu kódu je také zajímavé použití dvou proměnných prostředí ve vlastnosti **command_line** v StartTask: `AZ_BATCH_TASK_WORKING_DIR` a `AZ_BATCH_NODE_SHARED_DIR`. Každý výpočetní uzel v rámci fondu Batch je automaticky nakonfigurovaný pomocí řady proměnných prostředí, které se týkají služby Batch. Jakýkoli proces spuštěný úkolem má přístup k těmto proměnným prostředí.

> [!TIP]
> Další informace o proměnných prostředí, které jsou dostupné na výpočetní uzlech ve fondu Batch, a také informace o pracovních adresářích úkolu najdete v částech **Nastavení prostředí pro úkoly** a **Soubory a adresáře** v článku [Přehled funkcí Azure Batch](batch-api-basics.md).
>
>

## <a name="step-4-create-batch-job"></a>Krok 4: Vytvoření úlohy Batch
![Vytvoření úlohy Batch][4]<br/>

**Úloha** Batch je kolekcí úkolů a je přidružená k fondu výpočetních uzlů. Úkoly v úloze se spustit na přidružených výpočetních uzlech fondu.

Úlohu můžete použít nejen k uspořádání a sledování úkolů v souvisejících úlohách, ale také k nastavení určitých omezení – například maximálního runtime úlohy (a při rozšíření i pro její úkoly) a také priority úloh ve vztahu k dalším úlohám na účtu Batch. V tomto příkladu je úloha přidružená jenom k fondu, který byl vytvořen v kroku 3. Žádné další vlastnosti se nekonfigurují.

Všechny úlohy Batch jsou přidružené ke konkrétnímu fondu. Toto přidružení označuje uzly, na kterých se úkoly úlohy spustí. Fond určíte použitím vlastnosti [PoolInformation][py_poolinfo], jak znázorňuje následující fragment kódu.

```python
def create_job(batch_service_client, job_id, pool_id):
    """
    Creates a job with the specified ID, associated with the specified pool.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID for the job.
    :param str pool_id: The ID for the pool.
    """
    print('Creating job [{}]...'.format(job_id))

    job = batch.models.JobAddParameter(
        job_id,
        batch.models.PoolInformation(pool_id=pool_id))

    try:
        batch_service_client.job.add(job)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Po vytvoření úlohy budou přidány úkoly, které budou provádět práci.

## <a name="step-5-add-tasks-to-job"></a>Krok 5: Přidání úkolů do úlohy
![Přidání úkolů do úlohy][5]<br/>
*(1) Úkoly jsou přidány do úlohy, (2) úkoly jsou naplánovány ke spuštění na uzlech a (3) úkoly stahují datové soubory ke zpracování.*

**Úkoly** Batch jsou jednotlivé jednotky práce, které se spouští na výpočetních uzlech. Úkol má příkazový řádek a spouští skripty nebo spustitelné soubory, které jste v takovém příkazovém řádku určili.

Aby mohly skutečně provést nějakou práci, musí úkoly nejprve přidat do úlohy. Každý [CloudTask][py_task] je nakonfigurovaný pomocí vlastnosti příkazového řádku a [ResourceFiles][py_resource_file] (stejně jako u StartTask fondu), kterou si úkol stáhne do uzlu předtím, než se jeho příkazový řádek automaticky spustí. Každý úkol v ukázce zpracovává jenom jeden soubor. Proto jeho kolekce ResourceFiles obsahuje jen jeden prvek.

```python
def add_tasks(batch_service_client, job_id, input_files,
              output_container_name, output_container_sas_token):
    """
    Adds a task for each input file in the collection to the specified job.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID of the job to which to add the tasks.
    :param list input_files: A collection of input files. One task will be
     created for each input file.
    :param output_container_name: The ID of an Azure Blob storage container to
    which the tasks will upload their results.
    :param output_container_sas_token: A SAS token granting write access to
    the specified Azure Blob storage container.
    """

    print('Adding {} tasks to job [{}]...'.format(len(input_files), job_id))

    tasks = list()

    for input_file in input_files:

        command = ['python $AZ_BATCH_NODE_SHARED_DIR/python_tutorial_task.py '
                   '--filepath {} --numwords {} --storageaccount {} '
                   '--storagecontainer {} --sastoken "{}"'.format(
                    input_file.file_path,
                    '3',
                    _STORAGE_ACCOUNT_NAME,
                    output_container_name,
                    output_container_sas_token)]

        tasks.append(batch.models.TaskAddParameter(
                'topNtask{}'.format(input_files.index(input_file)),
                wrap_commands_in_shell('linux', command),
                resource_files=[input_file]
                )
        )

    batch_service_client.task.add_collection(job_id, tasks)
```

> [!IMPORTANT]
> Když přistupují k proměnným prostředí, například k `$AZ_BATCH_NODE_SHARED_DIR`, nebo když spouští aplikaci, která se nedá najít na `PATH` uzlu, musí příkazové řádky úkolu vyvolat prostředí explicitně, například pomocí `/bin/sh -c MyTaskApplication $MY_ENV_VAR`. Tento požadavek není nutný, pokud vaše úkoly spouští aplikace v `PATH` uzlu a neodkazují na žádné proměnné prostředí.
>
>

Ve smyčce `for` ve výše uvedeném fragmentu kódu můžete vidět, že příkazový řádek úkolu je vytvořený pomocí pěti argumentů příkazového řádku, které se předávají do skriptu *python_tutorial_task.py*:

1. **filepath**: Jedná se o místní cestu k souboru, protože soubor existuje na uzlu. Když byl objekt ResourceFile v `upload_file_to_container` ve výše uvedeném kroku 2 vytvořený, použil se pro tuto vlastnost název souboru (parametr `file_path` v konstruktoru ResourceFile). To znamená, že soubor můžete najít ve stejném adresáři na uzlu jako skript *python_tutorial_task.py*.
2. **numwords**: *N* nejčastějších slov, která musí být zapsaná do výstupního souboru.
3. **storageaccount**: Název účtu Storage, který vlastní kontejner, do kterého se musí odesílat výstup úkolu.
4. **storagecontainer**: Název kontejneru Storage, do kterého se musí odesílat výstupní soubory.
5. **sastoken**: Sdílený přístupový podpis (SAS), který zajišťuje oprávnění k zápisu do **výstupního** kontejneru ve službě Azure Storage. Skript *python_tutorial_task.py* používá tento sdílený přístupový podpis při vytváření svého odkazu BlockBlobService. Tím je zajištěné oprávnění k zápisu do kontejneru bez potřeby přístupového klíče k účtu úložiště.

```python
# NOTE: Taken from python_tutorial_task.py

# Create the blob client using the container's SAS token.
# This allows us to create a client that provides write
# access only to the container.
blob_client = azureblob.BlockBlobService(account_name=args.storageaccount,
                                         sas_token=args.sastoken)
```

## <a name="step-6-monitor-tasks"></a>Krok 6: Sledování úkolů
![Sledujte úkoly.][6]<br/>
*Skript (1) sleduje stav dokončení úkolů a (2) úkoly odesílají výsledná data do služby Azure Storage.*

Pokud úkoly přidáte do úlohy, budou automaticky zařazeny do fronty a bude naplánováno jejich spuštění na výpočetních uzlech ve fondu, který je k úloze přidružený. Na základě vámi zadaných nastavení služba Batch zpracuje veškeré řazení úkolů do fronty, plánování úkolů, opakované spouštění a další povinnosti spojené se správou úkolů místo vás.

Ke sledování provádění úkolů existuje mnoho přístupů. Funkce `wait_for_tasks_to_complete` ve skriptu *python_tutorial_client.py* nabízí jednoduchý příklad sledování úkolů a jejich určitého stavu, v tomto případě stav [dokončeno][py_taskstate].

```python
def wait_for_tasks_to_complete(batch_service_client, job_id, timeout):
    """
    Returns when all tasks in the specified job reach the Completed state.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The id of the job whose tasks should be to monitored.
    :param timedelta timeout: The duration to wait for task completion. If all
    tasks in the specified job do not reach Completed state within this time
    period, an exception will be raised.
    """
    timeout_expiration = datetime.datetime.now() + timeout

    print("Monitoring all tasks for 'Completed' state, timeout in {}..."
          .format(timeout), end='')

    while datetime.datetime.now() < timeout_expiration:
        print('.', end='')
        sys.stdout.flush()
        tasks = batch_service_client.task.list(job_id)

        incomplete_tasks = [task for task in tasks if
                            task.state != batchmodels.TaskState.completed]
        if not incomplete_tasks:
            print()
            return True
        else:
            time.sleep(1)

    print()
    raise RuntimeError("ERROR: Tasks did not reach 'Completed' state within "
                       "timeout period of " + str(timeout))
```

## <a name="step-7-download-task-output"></a>Krok 7: Stažení výstupu úkolu
![Stažení výstupu úkolu ze služby Storage][7]<br/>

Po dokončení úlohy můžete ze služby Azure Storage stáhnout výstup úkolů. Provádí se to voláním `download_blobs_from_container` ve skriptu *python_tutorial_client.py*:

```python
def download_blobs_from_container(block_blob_client,
                                  container_name, directory_path):
    """
    Downloads all blobs from the specified Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param container_name: The Azure Blob storage container from which to
     download files.
    :param directory_path: The local directory to which to download the files.
    """
    print('Downloading all files from container [{}]...'.format(
        container_name))

    container_blobs = block_blob_client.list_blobs(container_name)

    for blob in container_blobs.items:
        destination_file_path = os.path.join(directory_path, blob.name)

        block_blob_client.get_blob_to_path(container_name,
                                           blob.name,
                                           destination_file_path)

        print('  Downloaded blob [{}] from container [{}] to {}'.format(
            blob.name,
            container_name,
            destination_file_path))

    print('  Download complete!')
```

> [!NOTE]
> Volání `download_blobs_from_container` ve skriptu *python_tutorial_client.py* určuje, že soubory mají být stažené do vašeho domovského adresáře. Umístění výstupu můžete podle libosti změnit.
>
>

## <a name="step-8-delete-containers"></a>Krok 8: Odstranění kontejnerů
Vzhledem k tomu, že musíte platit za data, která si necháváte ve službě Azure Storage, doporučujeme odebrat všechny objekty blob, které už pro úlohy Batch nepotřebujete. Ve skriptu *python_tutorial_client.py* se to provádí pomocí tří volání [BlockBlobService.delete_container][py_delete_container]:

```python
# Clean up storage resources
print('Deleting containers...')
blob_client.delete_container(app_container_name)
blob_client.delete_container(input_container_name)
blob_client.delete_container(output_container_name)
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Krok 9: Odstranění úlohy a fondu
V posledním kroku budete vyzváni k odstranění úlohy a fondu, které vytvořil skript *python_tutorial_client.py*. I když se vám neúčtují poplatky za úlohy a úkoly samotné, *účtují* se vám poplatky za výpočetní uzly. Proto doporučujeme, abyste uzly přidělovali, jen když je to potřeba. Odstraňování nepoužívaných fondů by mělo být součástí vašeho standardního procesu údržby.

[JobOperations][py_job] a [PoolOperations][py_pool] z BatchServiceClient mají odpovídající metody odstranění, které se volají, pokud potvrdíte odstranění:

```python
# Clean up Batch resources (if the user so chooses).
if query_yes_no('Delete job?') == 'yes':
    batch_client.job.delete(_JOB_ID)

if query_yes_no('Delete pool?') == 'yes':
    batch_client.pool.delete(_POOL_ID)
```

> [!IMPORTANT]
> Pamatujte, že se vám účtují poplatky za výpočetní prostředky, takže odstranění nepoužívaných fondů vám ušetří náklady. Musíme ale upozornit, že odstraněním fondu odstraníte všechny výpočetní uzly v takovém fondu a veškerá data na uzlech budou po odstranění fondu ztracená.
>
>

## <a name="run-the-sample-script"></a>Spuštění ukázkového skriptu
Při spuštění skriptu *python_tutorial_client.py* z [ukázky kódu][github_article_samples] pro tento kurz bude výstup konzoly podobný následujícímu. Zatímco se vytvářejí a spouští výpočetní uzly fondu a provádí se příkazy ve spouštěcím úkolu fondu, uvidíte pozastavení na `Monitoring all tasks for 'Completed' state, timeout in 0:20:00...`. Ke sledování fondu, výpočetních uzlů, úlohy a úkolů během a po spuštění použijte [Azure Portal][azure_portal]. K zobrazení prostředků služby Storage (kontejnerů a objektů blob), které vytvořila aplikace, použijte [Azure Portal][azure_portal] nebo [Microsoft Azure Storage Explorer][storage_explorer].

> [!TIP]
> Z adresáře `azure-batch-samples/Python/Batch/article_samples` spusťte skript *python_tutorial_client.py*. Protože pro import modulu `common.helpers` používá relativní cestu, může se při spuštění mimo tento adresář zobrazit chyba `ImportError: No module named 'common'`.
>
>

Typická doba provádění je **přibližně 5-7 minut**, pokud ukázku spustíte ve výchozí konfiguraci.

```
Sample start: 2016-05-20 22:47:10

Uploading file /home/user/py_tutorial/python_tutorial_task.py to container [application]...
Uploading file /home/user/py_tutorial/data/taskdata1.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata2.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata3.txt to container [input]...
Creating pool [PythonTutorialPool]...
Creating job [PythonTutorialJob]...
Adding 3 tasks to job [PythonTutorialJob]...
Monitoring all tasks for 'Completed' state, timeout in 0:20:00..........................................................................
  Success! All tasks reached the 'Completed' state within the specified timeout period.
Downloading all files from container [output]...
  Downloaded blob [taskdata1_OUTPUT.txt] from container [output] to /home/user/taskdata1_OUTPUT.txt
  Downloaded blob [taskdata2_OUTPUT.txt] from container [output] to /home/user/taskdata2_OUTPUT.txt
  Downloaded blob [taskdata3_OUTPUT.txt] from container [output] to /home/user/taskdata3_OUTPUT.txt
  Download complete!
Deleting containers...

Sample end: 2016-05-20 22:53:12
Elapsed time: 0:06:02

Delete job? [Y/n]
Delete pool? [Y/n]

Press ENTER to exit...
```

## <a name="next-steps"></a>Další kroky
Nebojte se provést ve skriptech *python_tutorial_client.py* a *python_tutorial_task.py* změny a experimentovat s různými výpočetními scénáři. Zkuste například do skriptu *python_tutorial_task.py* přidat prodlevu provádění, abyste mohli simulovat dlouhotrvající úkoly a sledovat je na portálu. Zkuste přidat další úkoly nebo upravit počet výpočetních uzlů. Přidejte logiku pro kontrolu a povolení použití existujícího fondu, abyste urychlili dobu spouštění.

Teď, když jste se seznámili se základním pracovním postupem řešení Batch, je čas proniknout do dalších funkcí služby Batch.

* Přečtěte si článek [Přehled funkcí Azure Batch](batch-api-basics.md), který doporučujeme všem novým uživatelům služby.
* Začněte u dalších článků o vývoji pro Batch, které najdete v [Postupu výuky pro Batch][batch_learning_path] v části **Podrobný popis vývoje**.
* Podívejte se na různé implementace zpracování úlohy „N nejčastějších slov“ a použijte k tomu Batch v ukázce [TopNWords][github_topnwords].

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[crypto]: https://cryptography.io/en/latest/
[crypto_install]: https://cryptography.io/en/latest/installation/
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[github_article_samples]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/article_samples

[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build

[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_batchserviceclient]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html#azure.batch.BatchServiceClient
[py_blockblobservice]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.blockblobservice.html#azure.storage.blob.blockblobservice.BlockBlobService
[py_cloudtask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_cs_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudServiceConfiguration
[py_delete_container]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.delete_container
[py_gen_blob_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_blob_shared_access_signature
[py_gen_container_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_container_shared_access_signature
[py_image_ref]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_job]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.JobOperations
[py_jobpreptask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobPreparationTask
[py_jobreltask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobReleaseTask
[py_list_skus]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[py_make_blob_url]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.make_blob_url
[py_pool]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.PoolOperations
[py_pooladdparam]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolAddParameter
[py_poolinfo]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolInformation
[py_resource_file]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ResourceFile
[py_samples_github]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_task]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_taskstate]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.TaskState
[py_vm_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.VirtualMachineConfiguration
[pypi_batch]: https://pypi.python.org/pypi/azure-batch
[pypi_storage]: https://pypi.python.org/pypi/azure-storage
[pypi_install]: https://packaging.python.org/installing/
[storage_explorer]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-python-tutorial/batch_workflow_01_sm.png "Vytvoření kontejnerů ve službě Azure Storage"
[2]: ./media/batch-python-tutorial/batch_workflow_02_sm.png "Odeslání aplikačních a vstupních (datových) souborů úkolů do kontejnerů"
[3]: ./media/batch-python-tutorial/batch_workflow_03_sm.png "Vytvoření fondu Batch"
[4]: ./media/batch-python-tutorial/batch_workflow_04_sm.png "Vytvoření úlohy Batch"
[5]: ./media/batch-python-tutorial/batch_workflow_05_sm.png "Přidání úkolů do úlohy"
[6]: ./media/batch-python-tutorial/batch_workflow_06_sm.png "Sledování úkolů"
[7]: ./media/batch-python-tutorial/batch_workflow_07_sm.png "Stažení výstupu úkolu ze služby Storage"
[8]: ./media/batch-python-tutorial/batch_workflow_sm.png "Pracovní postup řešení Batch (úplný diagram)"
[9]: ./media/batch-python-tutorial/credentials_batch_sm.png "Přihlašovací údaje Batch na portálu"
[10]: ./media/batch-python-tutorial/credentials_storage_sm.png "Přihlašovací údaje služby Storage na portálu"
[11]: ./media/batch-python-tutorial/batch_workflow_minimal_sm.png "Pracovní postup řešení Batch (minimální diagram)"
