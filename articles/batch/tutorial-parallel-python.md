---
title: Spuštění paralelní úlohy – Azure Batch Python
description: Kurz – Zpracování multimediálních souborů pomocí aplikace ffmpeg ve službě Azure Batch s využitím klientské knihovny Batch Python
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: python
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 6bbaa9693bb8d8e54e78f1e83617449cd013ad48
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47158730"
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-python-api"></a>Kurz: Spuštění paralelní úlohy pomocí služby Azure Batch s využitím rozhraní Python API

Azure Batch umožňuje efektivně spouštět v Azure rozsáhlé paralelní dávkové úlohy a úlohy vysokovýkonného výpočetního prostředí (HPC). Tento kurz vás provede příkladem spuštění paralelní úlohy pomocí služby Batch v Pythonu. Seznámíte se s běžným pracovním postupem aplikace Batch a způsobem práce s prostředky služby Batch a Storage prostřednictvím kódu programu. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Ověření pomocí účtů Batch a Storage
> * Nahrání vstupních souborů do služby Storage
> * Vytvoření fondu výpočetních uzlů pro spouštění aplikace
> * Vytvoření úlohy a úkolů pro zpracování vstupních souborů
> * Monitorování provádění úkolů
> * Načtení výstupních souborů

V tomto kurzu pomocí open source nástroje [ffmpeg](http://ffmpeg.org/) paralelně převedete multimediální soubory MP4 do formátu MP3. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* [Python verze 2.7 nebo 3.3 nebo novější](https://www.python.org/downloads/)

* Správce balíčků [pip](https://pip.pypa.io/en/stable/installing/)

* Účet Azure Batch a propojený účet Azure Storage. Informace o vytvoření těchto účtů prostřednictvím [webu Azure Portal](quick-create-portal.md) nebo [rozhraní Azure CLI](quick-create-cli.md) najdete v rychlém startu služby Batch.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)] 

## <a name="download-and-run-the-sample"></a>Stažení a spuštění ukázky

### <a name="download-the-sample"></a>Stažení ukázky

[Stáhněte nebo naklonujte ukázkovou aplikaci](https://github.com/Azure-Samples/batch-python-ffmpeg-tutorial) z GitHubu. K naklonování úložiště ukázkové aplikace pomocí klienta Git použijte následující příkaz:

```
git clone https://github.com/Azure-Samples/batch-python-ffmpeg-tutorial.git
```

Přejděte do adresáře, který obsahuje soubor `batch_python_tutorial_ffmpeg.py`.

Ve prostředí Pythonu nainstalujte vyžadované balíčky pomocí `pip`.

```bash
pip install -r requirements.txt
```

Otevřete soubor `batch_python_tutorial_ffmpeg.py`. Aktualizujte řetězce přihlašovacích údajů účtu Batch a účtu úložiště s jedinečnými hodnotami pro vaše účty. Příklad:


```Python
_BATCH_ACCOUNT_NAME = 'mybatchaccount'
_BATCH_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ=='
_BATCH_ACCOUNT_URL = 'https://mybatchaccount.mybatchregion.batch.azure.com'
_STORAGE_ACCOUNT_NAME = 'mystorageaccount'
_STORAGE_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=='
```

### <a name="run-the-app"></a>Spuštění aplikace

Spuštění skriptu:

```
python batch_python_tutorial_ffmpeg.py
```

Po spuštění ukázkové aplikace vypadá výstup konzoly zhruba následovně. Během provádění dojde k pozastavení na řádku `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` a mezitím se spustí výpočetní uzly fondu. 
   
```
Sample start: 12/12/2017 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [LinuxFFmpegPool]...
Creating job [LinuxFFmpegJob]...
Adding 5 tasks to job [LinuxFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]....

Sample end: 12/12/2017 3:29:36 PM
Elapsed time: 00:09:14.3418742
```

Fond, výpočetní uzly, úlohy a úkoly můžete sledovat ve svém účtu Batch na webu Azure Portal. Pokud například chcete zobrazit heat mapu výpočetních uzlů ve fondu, klikněte na **Fondy** > *LinuxFFmpegPool*.

Když jsou úkoly spuštěné, heat mapa vypadá přibližně takto:

![Heat mapa fondu](./media/tutorial-parallel-python/pool.png)

Když aplikaci spouštíte v její výchozí konfiguraci, je obvyklá doba provádění přibližně **5 minut**. Vytvoření fondu trvá nejdéle. 

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>Kontrola kódu

Následující části ukázkovou aplikaci rozdělují do kroků, které aplikace provádí při zpracování úloh ve službě Batch. Při čtení zbývajících částí tohoto článku nahlížejte do kódu Pythonu, protože tady nezvládneme probrat každý jednotlivý řádek kódu.

### <a name="authenticate-blob-and-batch-clients"></a>Ověřování klientů objektů blob a služby Batch

K práci s účtem úložiště aplikace používá balíček [azure-storage-blob](https://pypi.python.org/pypi/azure-storage-blob) k vytvoření objektu [BlockBlobService](/python/api/azure.storage.blob.blockblobservice.blockblobservice).

```python
blob_client = azureblob.BlockBlobService(
    account_name=_STORAGE_ACCOUNT_NAME,
    account_key=_STORAGE_ACCOUNT_KEY)
```

K vytváření a správě fondů, úloh a úkolů ve službě Batch aplikace vytvoří objekt [BatchServiceClient](/python/api/azure.batch.batchserviceclient). Klient služby Batch v ukázce používá ověření pomocí sdíleného klíče. Pro účely ověřování jednotlivých uživatelů nebo bezobslužných aplikací služba Batch podporuje také ověřování prostřednictvím [Azure Active Directory](batch-aad-auth.md).

```python
credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
    _BATCH_ACCOUNT_KEY)

batch_client = batch.BatchServiceClient(
    credentials,
    base_url=_BATCH_ACCOUNT_URL)
```

### <a name="upload-input-files"></a>Nahrání vstupních souborů

Aplikace používá odkaz `blob_client` k vytvoření kontejneru úložiště pro vstup souborů MP4 a kontejner pro výstup úlohy. Potom zavolá funkci `upload_file_to_container` pro odeslání souborů MP4 z místního adresáře `InputFiles` do kontejneru. Soubory v úložišti jsou definované jako objekty [ResourceFile](/python/api/azure.batch.models.resourcefile) služby Batch, které může služba Batch později stáhnout do výpočetních uzlů.

```python
blob_client.create_container(input_container_name, fail_on_exist=False)
blob_client.create_container(output_container_name, fail_on_exist=False)
input_file_paths = []
    
for folder, subs, files in os.walk(os.path.join(sys.path[0],'./InputFiles/')):
    for filename in files:
        if filename.endswith(".mp4"):
            input_file_paths.append(os.path.abspath(os.path.join(folder, filename)))

# Upload the input files. This is the collection of files that are to be processed by the tasks. 
input_files = [
    upload_file_to_container(blob_client, input_container_name, file_path)
    for file_path in input_file_paths]
```

### <a name="create-a-pool-of-compute-nodes"></a>Vytvořte fond výpočetních uzlů.

Na účtu Batch potom příklad pomocí volání `create_pool` vytvoří fond výpočetních uzlů. Tato definovaná funkce používá třídu služby Batch [PoolAddParameter](/python/api/azure.batch.models.pooladdparameter) k nastavení počtu uzlů, velikosti virtuálního počítače a konfigurace fondu. Tady objekt [VirtualMachineConfiguration](/python/api/azure.batch.models.virtualmachineconfiguration) určuje odkaz [ImageReference](/python/api/azure.batch.models.imagereference) na image Ubuntu Server 16.04 LTS publikovanou v Azure Marketplace. Batch podporuje širokou škálu imagí virtuálních počítačů v Azure Marketplace, ale i vlastní image virtuálních počítačů.

Počet uzlů a velikost virtuálních počítačů jsou definované konstanty. Batch podporuje vyhrazené uzly a [uzly s nízkou prioritou](batch-low-pri-vms.md) a ve svých fondech můžete použít oba typy. Vyhrazené uzly jsou rezervované pro váš fond. Uzly s nízkou prioritou pocházejí z přebytečné kapacity virtuálních počítačů v Azure a nabízejí se za nižší cenu. Pokud Azure nemá dostatek kapacity, uzly s nízkou prioritou budou nedostupné. Ukázka ve výchozím nastavení vytvoří fond obsahující pouze 5 uzlů s nízkou prioritou ve velikosti *Standard_A1_v2*. 

Spolu s fyzickými vlastnostmi uzlu tato konfigurace fondu také zahrnuje objekt [StartTask](/python/api/azure.batch.models.starttask). StartTask se spustí na každém uzlu, když se takový uzel připojí k fondu, a taky pokaždé, když se uzel restartuje. V tomto příkladě StartTask spouští příkazy prostředí Bash pro instalaci balíčku ffmpeg a závislostí na uzlech.

Metoda [pool.add](/python/api/azure.batch.operations.pooloperations#azure_batch_operations_PoolOperations_add) odešle fond do služby Batch.

```python
new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04-LTS",
            version="latest"
            ),
        node_agent_sku_id="batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_DEDICATED_POOL_NODE_COUNT,
    target_low_priority_nodes=_LOW_PRIORITY_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install -y ffmpeg\"",
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(new_pool)
```

### <a name="create-a-job"></a>Vytvoření úlohy

Úloha služby Batch určí fond, ve kterém se budou spouštět úkoly, a volitelná nastavení, jako je priorita a plán práce. Ukázka vytvoří úlohu zavoláním metody `create_job`. Tato definovaná funkce používá třídu [JobAddParameter](/python/api/azure.batch.models.jobaddparameter) k vytvoření úlohy ve vašem fondu. Metoda [job.add](/python/api/azure.batch.operations.joboperations#azure_batch_operations_JobOperations_add) odešle fond do služby Batch. Na začátku úloha neobsahuje žádné úkoly.

```python
job = batch.models.JobAddParameter(
    id=job_id,
    pool_info=batch.models.PoolInformation(pool_id=pool_id))

batch_service_client.job.add(job)
```

### <a name="create-tasks"></a>Vytvoření úkolů

Aplikace vytvoří úkoly pro úlohu pomocí volání `add_tasks`. Tato definovaná funkce vytvoří seznam objektů úlohy pomocí třídy [TaskAddParameter](/python/api/azure.batch.models.taskaddparameter). Každá úloha spouští ffmpeg pro zpracování vstupního objektu `resource_files` pomocí parametru `command_line`. Aplikace ffmpeg se na každý uzel nainstalovala dříve při vytváření fondu. Tady příkazový řádek spouští aplikaci ffmpeg kvůli převodu jednotlivých vstupních souborů MP4 (video) na soubory MP3 (zvuk).

Ukázka po spuštění příkazového řádku vytvoří pro soubor MP3 objekt [OutputFile](/python/api/azure.batch.models.outputfile). Výstupní soubory všech úkolů (v tomto případě jednoho) se pomocí vlastnosti `output_files` nahrají do kontejneru v propojeném účtu úložiště.

Potom aplikace přidá úkoly do úlohy pomocí metody [task.add_collection](/python/api/azure.batch.operations.taskoperations#azure_batch_operations_TaskOperations_add_collection) a ta je zařadí do fronty ke spuštění ve výpočetních uzlech. 

```python
tasks = list()

for idx, input_file in enumerate(input_files): 
    input_file_path=input_file.file_path
    output_file_path="".join((input_file_path).split('.')[:-1]) + '.mp3'
    command = "/bin/bash -c \"ffmpeg -i {} {} \"".format(input_file_path, output_file_path)
    tasks.append(batch.models.TaskAddParameter(
        id='Task{}'.format(idx),
        command_line=command,
        resource_files=[input_file],
        output_files=[batchmodels.OutputFile(
            file_pattern=output_file_path,
            destination=batchmodels.OutputFileDestination(
                container=batchmodels.OutputFileBlobContainerDestination(
                    container_url=output_container_sas_url)),
            upload_options=batchmodels.OutputFileUploadOptions(
                upload_condition=batchmodels.OutputFileUploadCondition.task_success))]
        )
    )
batch_service_client.task.add_collection(job_id, tasks)
```    

### <a name="monitor-tasks"></a>Sledování úkolů

Když služba Batch přidá do úlohy úkoly, automaticky je zařadí do fronty a naplánuje jejich spuštění ve výpočetních uzlech v přidruženém fondu. Na základě vámi zadaných nastavení služba Batch zpracuje veškeré řazení úkolů do fronty, plánování úkolů, opakované spouštění a další povinnosti spojené se správou úkolů. 

Ke sledování provádění úkolů existuje mnoho přístupů. Funkce `wait_for_tasks_to_complete` v tomto příkladu používá objekt [TaskState](/python/api/azure.batch.models.taskstate) k monitorování úloh pro konkrétní stav (v tomto případě dokončený stav) v rámci časového limitu.

```python
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
...
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Aplikace po spuštění úkolů automaticky odstraní kontejner vstupního úložiště, který vytvořila, a dá vám možnost odstranit fond a úlohu služby Batch. Třídy [JobOperations](/python/api/azure.batch.operations.joboperations) a [PoolOperations](/python/api/azure.batch.operations.pooloperations) v BatchClient obsahují metody pro odstranění, které se zavolají, pokud potvrdíte odstranění. I když se vám neúčtují poplatky za úlohy a úlohy samotné, účtují se vám poplatky za výpočetní uzly. Proto doporučujeme, abyste fondy přidělovali, jen když je to potřeba. Při odstranění fondu se odstraní veškeré výstupy úkolů v uzlech. Vstupní a výstupní soubory však zůstanou v účtu úložiště.

Pokud už je nepotřebujete, odstraňte skupinu prostředků, účet Batch a účet úložiště. Na webu Azure Portal to provedete tak, že vyberete skupinu prostředků účtu Batch a kliknete na **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili tyto postupy:

> [!div class="checklist"]
> * Ověření pomocí účtů Batch a Storage
> * Nahrání vstupních souborů do služby Storage
> * Vytvoření fondu výpočetních uzlů pro spouštění aplikace
> * Vytvoření úlohy a úkolů pro zpracování vstupních souborů
> * Monitorování provádění úkolů
> * Načtení výstupních souborů

Další příklady použití rozhraní Python API k plánování a zpracování úloh služby Batch najdete v ukázkách na GitHubu.

> [!div class="nextstepaction"]
> [Ukázky Batch Pythonu](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch)

