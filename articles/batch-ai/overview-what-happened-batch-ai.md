---
title: Co se děje s Azure Batch AI? | Dokumenty Microsoft
description: Další informace o tom, co se děje na Azure Batch AI a službou Azure Machine Learning compute možnost.
services: batch-ai
author: garyericson
ms.service: batch-ai
ms.topic: overview
ms.date: 2/14/2019
ms.author: garye
ms.openlocfilehash: 44c0eec97f63897173ecde170ec4ed926db8bcaa
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342809"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Co se děje s Azure Batch AI?

**Služba Azure Batch AI je vyřazení z provozu v březnu.** Ve velkém měřítku trénování a vyhodnocování funkcí služby Batch AI jsou teď dostupné v [služby Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md), která se stala všeobecně dostupná na 4. prosince 2018.

Spolu s mnoha jiných strojového učení zahrnuje službu Azure Machine Learning založené na cloudu spravované cílové výpočetní prostředí pro školení, nasazování a vyhodnocování modelů strojového učení. Tato cílového výpočetního prostředí se nazývá [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Spuštění migrace a jeho použití Dnes](#migrate). Můžete pracovat se službou Azure Machine Learning prostřednictvím jeho [sady SDK pro Python](../machine-learning/service/quickstart-create-workspace-with-python.md), rozhraní příkazového řádku a [webu Azure portal](../machine-learning/service/quickstart-get-started.md).

## <a name="support-timeline"></a>Časový plán podpory

| Datum | Podrobnosti o podpoře služby batch AI |
| ---- |-----------------|
| Prosinec&nbsp;14&#x2c;&nbsp;2018| Můžete použít existující předplatných Azure Batch AI jako před. Ale žádný **nová předplatná** se provádějí jsou možné a žádné nové investice.|
| March&nbsp;31&#x2c;&nbsp;2019 | Po tomto datu se stávající předplatná služby Batch AI přestane fungovat. |

## <a name="how-does-azure-machine-learning-service-compare"></a>Porovnání služby Azure Machine Learning
Je Cloudová služba, který použijete k natrénování, nasazení, automatizaci a správu modelů strojového učení, vše na široké měřítko, které cloud poskytuje. Získejte přehled, [služby Azure Machine Learning v tomto přehledu](../machine-learning/service/overview-what-is-azure-ml.md).
 

Životní cyklus vývoje typické model zahrnuje přípravy dat, školení a experimentování a fáze nasazení. Tento cyklus koncového lze orchestrované s využitím kanálů Machine Learning.

![Vývojový diagram](./media/overview-what-happened-batch-ai/lifecycle.png)


[Další informace o fungování služby a jeho hlavní koncepty](../machine-learning/service/concept-azure-machine-learning-architecture.md). Mnoho popsaných konceptů v pracovním postupu trénování modelu jsou podobné existující koncepty služby Batch AI. 

Tady je konkrétně, jak byste měli uvažovat o jejich mapování:
 
|Služba batch AI|  Služba Azure Machine Learning|
|:--:|:---:|
|Pracovní prostor|Pracovní prostor|
|Cluster|   Výpočetní prostředky typu `AmlCompute`|
|Souborové servery|DataStores|
|Experimenty|Experimenty|
|Úlohy|Spustí (umožňuje vnořené spuštění)|
 
Tady je jiný pohled stejné tabulky, které pomůžou vám vizualizace další věci:
 
**Batch AI hierarchie**
![vývojový diagram](./media/overview-what-happened-batch-ai/batchai-heirarchy.png) 
 
**Azure Machine Learning service hierarchie**
![vývojový diagram](./media/overview-what-happened-batch-ai/azure-machine-learning-service-heirarchy.png) 

### <a name="platform-capabilities"></a>Funkce platformy
Azure Machine Learning, které služba přináší sadu skvělé nové funkce, včetně komplexní školení -> sada pro nasazení, můžete použít při vývoji AI, aniž byste museli spravovat veškeré prostředky Azure. Tato tabulka obsahuje porovnání podpora funkce k trénování mezi těmito dvěma službami.

|Funkce|BatchAI služby|Služba Azure Machine Learning|
|-------|:-------:|:-------:|
|Volba velikosti virtuálního počítače |CPU/GPU    |CPU/GPU. Také podporuje pro odvozování FPGA|
|Připravený Cluster AI (ovladače, Docker, atd.)|  Ano |Ano|
|Příprava uzlu| Ano|    Ne|
|Řada operačního systému podle výběru   |Částečné    |Ne|
|Vyhrazené a LowPriority virtuálních počítačů  |Ano    |Ano|
|Automatické škálování   |Ano    |Ano (ve výchozím nastavení)|
|Čekací doba pro automatické škálování  |Ne |Ano|
|SSH    |Ano|   Ano|
|Připojování na úrovni clusteru |Ano (FileShares, objekty BLOB, systém souborů NFS, vlastní)   |Ano (připojit nebo stažení vašeho úložiště dat)|
|Distribuované trénování|  Ano |Ano|
|Režim spuštění úlohy|    Virtuální počítač nebo kontejneru|    Kontejner|
|Vlastní Image kontejneru|    Ano |Ano|
|Všechny sady Toolkit    |Ano    |Ano (Python spuštění skriptu)|
|JobPreparation|    Ano |Zatím ne|
|Připojování na úrovni úlohy |Ano (FileShares, objekty BLOB, systém souborů NFS, vlastní)   |Ano (FileShares, objekty BLOB)|
|Monitorování úloh     |prostřednictvím GetJob|    prostřednictvím historie spuštění (podrobnější informace, modul runtime vlastní tak, aby nabízel další metriky)|
|Získat protokoly úlohy a soubory/modelů |   prostřednictvím ListFiles a rozhraní API úložiště  |prostřednictvím služby artefaktu|
 |Podpora pro Tensorboard   |Ne|    Ano|
|Kvóty úrovně řady virtuálních počítačů |Ano    |Ano (s předchozím kapacitu přenesena)|
 
Kromě v předchozí tabulce jsou funkce ve službě Azure Machine Learning, které byly v BatchAI tradičně nejsou podporovány.

|Funkce|BatchAI služby|Služba Azure Machine Learning|
|-------|:-------:|:-------:|
|Příprava prostředí    |Ne |Ano (Conda přípravě a nahrávání do služby ACR)|
|Hyperparametrů  |Ne|    Ano|
|Správa modelů   |Ne |Ano|
|Operacionalizace/nasazení| Ne  |Prostřednictvím AKS a ACI|
|Příprava dat   |Ne |Ano|
|Cílových výpočetních prostředí    |Virtuální počítače Azure  |Místní BatchAI (jako AmlCompute), DataBricks, HDInsight|
|Automatizované Machine Learning |Ne|    Ano|
|Kanály  |Ne |Ano|
|Dávkové vyhodnocování  |Ano    |Ano|
|Podpora portálu nebo rozhraní příkazového řádku|    Ano |Ano|


### <a name="programming-interfaces"></a>Rozhraní pro programování

Tato tabulka představuje různé programovací rozhraní pro každou službu k dispozici.
    
|Funkce|BatchAI služby|Služba Azure Machine Learning|
|-------|:-------:|:-------:|
|Sada SDK    |Java, C#, Python, Node.js   |Python (spuštění na základě konfigurace a odhad na základě běžných architektur)|
|Rozhraní příkazového řádku    |Ano    |Zatím ne|
|portál Azure   |Ano    |Ano (s výjimkou odeslání úlohy)|
|REST API   |Ano    |Ano ale distribuované napříč mikroslužeb|




## <a name="why-migrate"></a>Proč migrovat?

Upgrade ze služby ve verzi Preview BatchAI ke službě GA'ed Azure Machine Learning poskytuje lepší základními pojmy, které se snadněji používá jako jsou odhady a úložišť. Zaručuje taky všeobecné dostupnosti služeb Azure na úrovni smlouvy o úrovni služeb a zákaznické podpory.

Služba Azure Machine Learning také přináší nové funkce, jako automatické strojové učení, Hyperparametrů a ML kanály, které jsou užitečné v nejvíce rozsáhlé úlohy AI. Uvést do provozu trénovaného modelu bez nutnosti přepínat do samostatné služby pomáhá dokončit datové vědy smyčky od přípravy dat (s použitím sady SDK pro Data Prep) k operacionalizaci a sledování modelů.

<a name="migrate"></a>

## <a name="how-do-i-migrate"></a>Jak provést migraci?

Než budete postupovat podle kroků v této příručce, které pomáhají příkazů mapování mezi těmito dvěma službami, doporučujeme, že můžete věnovat nějaký čas získávání znáte službu Azure Machine Learning prostřednictvím jeho dokumentaci] (.. / machine-learning/service/overview-what-is-azure-ml.md) včetně [výukového programu v jazyce Python](../machine-learning/service/tutorial-train-models-with-aml.md).

Vyhnete se tak přerušením pro vaše aplikace a využívat nejnovější funkce, proveďte následující kroky před 31. března 2019:

1. Vytvořit pracovní prostor služby Azure Machine Learning a začít:
    + [Rychlý start postavená na Pythonu](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [Azure portal podle rychlého startu](../machine-learning/service/quickstart-get-started.md)

1. Nastavení [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute) k tréninku modelu.

1. Aktualizujte skripty, aby pomocí Azure Machine Learning Compute.


### <a name="sdk"></a>Sada SDK

Aktuální sady SDK podporují ve službě Azure Machine Learning service je prostřednictvím několika sadách SDK pro Python. Hlavní sada SDK se aktualizuje přibližně každé dva týdny a je možné nainstalovat z PyPi pomocí následujícího příkazu:

```python
pip install --upgrade azureml-sdk[notebooks]
```

Nastavení prostředí a nainstalujte sadu SDK používat [pokyny rychlý start](../machine-learning/service/quickstart-create-workspace-with-python.md#install-the-sdk)

Po otevření poznámkového bloku jupyter s jádra odkazující na prostředí conda relevantní, tady je způsob mapování příkazů v obou službách:


### <a name="create-a-workspace"></a>Vytvoření pracovního prostoru
Koncept inicializace pracovního prostoru configuration.json v BatchAI mapuje podobně pomocí souboru konfigurace v Azure ML.

Pro služby Batch AI máte to Hotovo tímto způsobem:

```python
sys.path.append('../../..')
import utilities as utils

cfg = utils.config.Configuration('../../configuration.json')
client = utils.config.create_batchai_client(cfg)

utils.config.create_resource_group(cfg)
_ = client.workspaces.create(cfg.resource_group, cfg.workspace, cfg.location).result()
```

Služba Azure Machine Learning:

```python
from azureml.core.workspace import Workspace

ws = Workspace.from_config()
print('Workspace name: ' + ws.name, 
      'Azure region: ' + ws.location, 
      'Subscription id: ' + ws.subscription_id, 
      'Resource group: ' + ws.resource_group, sep = '\n')
```

Kromě toho můžete také vytvořit pracovní prostor přímo tak, že určíte, že parametry konfigurace, jako je z azureml.core importovat pracovní prostor

```python
# Create the workspace using the specified parameters
ws = Workspace.create(name = workspace_name,
                      subscription_id = subscription_id,
                      resource_group = resource_group, 
                      location = workspace_region,
                      create_resource_group = True,
                      exist_ok = True)
ws.get_details()

# write the details of the workspace to a configuration file to the notebook library
ws.write_config()
```

Tady je podrobnější dokumentaci k třídě AML prostoru pomocí příslušné funkce.


#### <a name="create-a-compute-cluster"></a>Vytvořte výpočetní cluster
Azure Machine Learning podporuje více cílových výpočetních prostředí, z nichž některé jsou spravovány službou a ostatní, které lze připojit k vašemu pracovnímu prostoru (např.) HDInsight cluster nebo vzdáleném virtuálním počítači, další informace najdete tady). Vytváření BatchAI koncept výpočetní cluster mapuje se na vytváření AmlCompute clusteru v Azure ML. Vytvoření Amlcompute trvá v konfiguraci výpočtů, který je podobný jak předávat parametry v BatchAI. Je jedna věc, kterou si uvědomit, že automatické škálování na ve výchozím nastavení je ve vašem clusteru AmlCompute vzhledem k tomu je vypnuto ve výchozím nastavení v BatchAI.

Pro služby Batch AI máte to Hotovo tímto způsobem:

```python
nodes_count = 2
cluster_name = 'nc6'

parameters = models.ClusterCreateParameters(
    vm_size='STANDARD_NC6',
    scale_settings=models.ScaleSettings(
        manual=models.ManualScaleSettings(target_node_count=nodes_count)
    ),
    user_account_settings=models.UserAccountSettings(
        admin_user_name=cfg.admin,
        admin_user_password=cfg.admin_password or None,
        admin_user_ssh_public_key=cfg.admin_ssh_key or None,
    )
)
_ = client.clusters.create(cfg.resource_group, cfg.workspace, cluster_name, parameters).result()
```

Pro službu Azure Machine Learning vyzkoušejte:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
gpu_cluster_name = "nc6"

# Verify that cluster does not exist already
try:
    gpu_cluster = ComputeTarget(workspace=ws, name=gpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           vm_priority='lowpriority',
                                                           min_nodes=1,
                                                           max_nodes=2,
                                                           idle_seconds_before_scaledown='300',
                                                           vnet_resourcegroup_name='<my-resource-group>',
                                                           vnet_name='<my-vnet-name>',
                                                           subnet_name='<my-subnet-name>')
    gpu_cluster = ComputeTarget.create(ws, gpu_cluster_name, compute_config)

gpu_cluster.wait_for_completion(show_output=True)
```

Tady je podrobnější dokumentaci k třídě AmlCompute pomocí příslušné funkce. Všimněte si, že v konfiguraci vyšší pouze vm_size a max_nodes jsou povinné a zbývající vlastnosti, jako jsou virtuální sítě jsou určeny pro pokročilou konfiguraci.


### <a name="monitoring-status-of-your-cluster"></a>Sledování stavu clusteru
Toto je jednodušší v Azure ML jak je uvedeno níže.

Pro služby Batch AI máte to Hotovo tímto způsobem:

```python
cluster = client.clusters.get(cfg.resource_group, cfg.workspace, cluster_name)
utils.cluster.print_cluster_status(cluster)
```

Pro službu Azure Machine Learning vyzkoušejte:

```python
gpu_cluster.get_status().serialize()
```

#### <a name="getting-reference-to-a-storage-account"></a>Získání odkazu na účet úložiště
Koncept úložiště dat jako objektů blob, získá zjednodušená v Azure ML pomocí objektu úložiště. Ve výchozím nastavení pracovního prostoru Azure ML vytvoří účet úložiště, ale vlastní úložiště lze také připojit jako součást vytváření pracovního prostoru. 

Pro služby Batch AI máte to Hotovo tímto způsobem:

```python
azure_blob_container_name = 'batchaisample'
blob_service = BlockBlobService(cfg.storage_account_name, cfg.storage_account_key)
blob_service.create_container(azure_blob_container_name, fail_on_exist=False)
```

Pro službu Azure Machine Learning vyzkoušejte:

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)
```

Další informace o registraci dalších účtů úložiště, nebo získání odkazu na jiného registrovaného úložiště najdete tady.


#### <a name="downloading-and-uploading-data"></a>Stahování a nahrávání dat 
Pomocí těchto služeb můžete nahrát data do účtu úložiště snadno pomocí odkazu na úložiště dat výše. BatchAI jsme také nasadit cvičný skript jako součást Sdílení souborů, i když se zobrazí, jak můžete zadat jako součást konfigurace úlohy v případě Azure ML.

Pro služby Batch AI máte to Hotovo tímto způsobem:

```python
mnist_dataset_directory = 'mnist_dataset'
utils.dataset.download_and_upload_mnist_dataset_to_blob(
    blob_service, azure_blob_container_name, mnist_dataset_directory)

script_directory = 'tensorflow_samples'
script_to_deploy = 'mnist_replica.py'

blob_service.create_blob_from_path(azure_blob_container_name,
                                   script_directory + '/' + script_to_deploy, 
                                   script_to_deploy)
```


Pro službu Azure Machine Learning vyzkoušejte:

```python
import os
import urllib
os.makedirs('./data', exist_ok=True)
download_url = 'https://s3.amazonaws.com/img-datasets/mnist.npz'
urllib.request.urlretrieve(download_url, filename='data/mnist.npz')

ds.upload(src_dir='data', target_path='mnist_dataset', overwrite=True, show_progress=True)

path_on_datastore = ' mnist_dataset/mnist.npz' ds_data = ds.path(path_on_datastore) print(ds_data)
```

#### <a name="create-an-experiment"></a>Vytvoření experimentu
Jak je uvedeno výše má Azure ML koncept podobný BatchAI experimentu. Každý experimentu potom může mít jednotlivé testy, podobně jako jak máme úlohy BatchAI. Služba Azure ML umožňuje mít hierarchii pod každým nadřazeným spustit pro spuštění jednotlivých podřízených.

Pro služby Batch AI máte to Hotovo tímto způsobem:

```python
experiment_name = 'tensorflow_experiment'
experiment = client.experiments.create(cfg.resource_group, cfg.workspace, experiment_name).result()
```

Pro službu Azure Machine Learning vyzkoušejte:

```python
from azureml.core import Experiment

experiment_name = 'tensorflow_experiment'
experiment = Experiment(ws, name=experiment_name)
```


#### <a name="submit-a-job"></a>Odeslání úlohy
Jakmile vytvoříte experiment, máte několik různých způsobů odeslání spuštění. V tomto příkladu jsme se pokoušíte vytvořit pomocí TensorFlow modelu obsáhlého learningu a pomocí Azure ML Estimator udělat. Odhadu je jednoduše funkce obálky na základní konfigurace spuštění, usnadňuje odeslat spuštění, který je aktuálně podporovaná Pytorch a TensorFlow jenom pro. Prostřednictvím koncept úložišť, zobrazí se také jak snadno se stane pro zadání cesty připojení 

Pro služby Batch AI máte to Hotovo tímto způsobem:

```python
azure_file_share = 'afs'
azure_blob = 'bfs'
args_fmt = '--job_name={0} --num_gpus=1 --train_steps 10000 --checkpoint_dir=$AZ_BATCHAI_OUTPUT_MODEL --log_dir=$AZ_BATCHAI_OUTPUT_TENSORBOARD --data_dir=$AZ_BATCHAI_INPUT_DATASET --ps_hosts=$AZ_BATCHAI_PS_HOSTS --worker_hosts=$AZ_BATCHAI_WORKER_HOSTS --task_index=$AZ_BATCHAI_TASK_INDEX'

parameters = models.JobCreateParameters(
     cluster=models.ResourceId(id=cluster.id),
     node_count=2,
     input_directories=[
        models.InputDirectory(
            id='SCRIPT',
            path='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}/{1}'.format(azure_blob, script_directory)),
        models.InputDirectory(
            id='DATASET',
            path='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}/{1}'.format(azure_blob, mnist_dataset_directory))],
     std_out_err_path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
     output_directories=[
        models.OutputDirectory(
            id='MODEL',
            path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
            path_suffix='Models'),
        models.OutputDirectory(
            id='TENSORBOARD',
            path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
            path_suffix='Logs')
     ],
     mount_volumes=models.MountVolumes(
            azure_file_shares=[
                models.AzureFileShareReference(
                    account_name=cfg.storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=cfg.storage_account_key),
                    azure_file_url='https://{0}.file.core.windows.net/{1}'.format(
                        cfg.storage_account_name, azure_file_share_name),
                    relative_mount_path=azure_file_share)
            ],
            azure_blob_file_systems=[
                models.AzureBlobFileSystemReference(
                    account_name=cfg.storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=cfg.storage_account_key),
                    container_name=azure_blob_container_name,
                    relative_mount_path=azure_blob)
            ]
        ),
     container_settings=models.ContainerSettings(
         image_source_registry=models.ImageSourceRegistry(image='tensorflow/tensorflow:1.8.0-gpu')),
     tensor_flow_settings=models.TensorFlowSettings(
         parameter_server_count=1,
         worker_count=nodes_count,
         python_script_file_path='$AZ_BATCHAI_INPUT_SCRIPT/'+ script_to_deploy,
         master_command_line_args=args_fmt.format('worker'),
         worker_command_line_args=args_fmt.format('worker'),
         parameter_server_command_line_args=args_fmt.format('ps'),
     )
)
```

Odeslání úlohy sám o sobě v BatchAI představuje prostřednictvím funkce vytvořit.

```python
job_name = datetime.utcnow().strftime('tf_%m_%d_%Y_%H%M%S')
job = client.jobs.create(cfg.resource_group, cfg.workspace, experiment_name, job_name, parameters).result()
print('Created Job {0} in Experiment {1}'.format(job.name, experiment.name))
```

Úplné informace pro tento fragment kódu školení (včetně mnist_replica.py soubor, který jsme měli nahráli do sdílené složky výše) najdete v BatchAI ukázka Poznámkový blok úložiště github se tady.

Pro službu Azure Machine Learning vyzkoušejte:

```python
from azureml.train.dnn import TensorFlow

script_params={
    '--num_gpus': 1,
    '--train_steps': 500,
    '--input_data': ds_data.as_mount()

}

estimator = TensorFlow(source_directory=project_folder,
                       compute_target=gpu_cluster,
                       script_params=script_params,
                       entry_script='tf_mnist_replica.py',
                       node_count=2,
                       worker_count=2,
                       parameter_server_count=1,   
                       distributed_backend='ps',
                       use_gpu=True)
```

Úplné informace pro tento fragment kódu školení (včetně souboru tf_mnist_replica.py) najdete v Azure ML ukázka Poznámkový blok úložiště github se tady. Úložiště dat, samotného je možné připojit buď v jednotlivých uzlech, nebo trénovacích dat si můžete stáhnout na samotný uzel. Tady jsou další podrobnosti o odkazování na úložiště dat do vašeho odhadu. 

Odesílání spustit v Azure ML je pomocí funkce Odeslat.

```python
run = experiment.submit(estimator)
print(run)
```

Neexistuje jiný způsob určení parametrů spuštění, pomocí spouštění config – užitečné zejména pro definování vlastní školení prostředí. Další podrobnosti najdete v tomto příkladu poznámkového bloku v tomto poli. 

#### <a name="monitor-your-run"></a>Monitorování spuštění
Jakmile odešlete spustit, můžete buď počkejte na její dokončení nebo monitorování v Azure ML pomocí úhledné Jupyter pomůcky, které můžete vyvolat přímo v kódu. Také si můžete vyžádat kontextu jakékoli předchozí spuštění ve smyčce prostřednictvím různých experimenty v pracovním prostoru a jednotlivých běží v rámci každého pokusu.

Pro služby Batch AI máte to Hotovo tímto způsobem:

```python
utils.job.wait_for_job_completion(client, cfg.resource_group, cfg.workspace, 
                                  experiment_name, job_name, cluster_name, 'stdouterr', 'stdout-wk-0.txt')

files = client.jobs.list_output_files(cfg.resource_group, cfg.workspace, experiment_name, job_name,
                                      models.JobsListOutputFilesOptions(outputdirectoryid='stdouterr')) 
for f in list(files):
    print(f.name, f.download_url or 'directory')
```


Pro službu Azure Machine Learning vyzkoušejte:

```python
run.wait_for_completion(show_output=True)

from azureml.widgets import RunDetails
RunDetails(run).show()
```

Zde je snímek jak by widgetu načíst v poznámkovém bloku pro vyhledávání v reálném čase vaše protokoly: ![Monitorování diagram widgetu](./media/overview-what-happened-batch-ai/monitor.png)



#### <a name="delete-a-cluster"></a>Odstranění clusteru
Odstranění clusteru je jednoduché. Kromě toho Azure ML umožňuje aktualizovat cluster z v rámci poznámkového bloku, v případě, že chcete škálovat tak, aby vyšší počet uzlů, či navyšte čas nečinnosti wait dříve, než škálování clusteru. Jsme Nepovolovat, můžete změnit velikost virtuálního počítače samotného clusteru, protože vyžaduje nové nasazení efektivně v back-endu.

Pro služby Batch AI máte to Hotovo tímto způsobem:
```python
_ = client.clusters.delete(cfg.resource_group, cfg.workspace, cluster_name)
```

Pro službu Azure Machine Learning vyzkoušejte:

```python
gpu_cluster.delete()

gpu_cluster.update(min_nodes=2, max_nodes=4, idle_seconds_before_scaledown=600)
```

## <a name="support"></a>Podpora

BatchAI plánované vyřadit z provozu 31. března a už blokuje nová předplatná v registraci na službu, pokud není na seznamu povolených vyvoláním výjimky prostřednictvím podpory.  Azure Batch AI školení ve verzi Preview, kontaktujte AzureBatchAITrainingPreview@service.microsoft.com jakékoli dotazy nebo pokud máte nějakou zpětnou vazbu, jak migrovat do služby Azure Machine Learning.

Služba Azure Machine Learning je obecně dostupné služby. To znamená, že jde o potvrzené smlouva SLA a různých plánech podporuje lze vybírat.

Ceny za použití infrastruktury Azure BatchAI služby nebo pomocí služby Azure Machine Learning by neměl lišit v závislosti na, účtujeme jenom cena za spotřebované výpočetní prostředky v obou případech. Další informace najdete v tématu [cenové kalkulačky](https://azure.microsoft.com/pricing/details/machine-learning-service/).

Dostupnost podle oblastí mezi těmito dvěma službami najdete tady: https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai, služby machine learning a oblasti = all.


## <a name="next-steps"></a>Další postup

+ Přečtěte si [Přehled služby Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Konfigurace cílové výpočetní prostředí k tréninku modelu](../machine-learning/service/how-to-set-up-training-targets.md) službou Azure Machine Learning.

+ Zkontrolujte [plány Azure do budoucna](https://azure.microsoft.com/updates/) a získejte informace o další aktualizace služby Azure.
