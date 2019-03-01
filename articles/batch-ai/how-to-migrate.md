---
title: Migrace ze služby Batch AI do služby Azure Machine Learning
description: Zjistěte, jak migrovat do služby Azure Machine Learning pro AMLcompute a způsobu mapování kódu pro kód ve službě Azure Machine Learning.
ms.service: batch-ai
services: batch-ai
ms.topic: overview
ms.author: jmartens
author: j-martens
ms.date: 2/28/2019
ms.openlocfilehash: 5bc01ad6811cc5661d74770ce36281c9acba8912
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2019
ms.locfileid: "57196262"
---
# <a name="migrating-from-batch-ai-to-azure-machine-learning-service"></a>Migrace ze služby Batch AI do služby Azure Machine Learning

**Služba Azure Batch AI je vyřazení z provozu v březnu.** Ve velkém měřítku trénování a vyhodnocování funkcí služby Batch AI jsou teď dostupné v [služby Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md), která se stala všeobecně dostupná na 4. prosince 2018.

Spolu s mnoha jiných strojového učení zahrnuje službu Azure Machine Learning založené na cloudu spravované cílové výpočetní prostředí pro školení, nasazování a vyhodnocování modelů strojového učení. Tato cílového výpočetního prostředí se nazývá [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Spuštění migrace a jeho použití Dnes](#migrate). Můžete pracovat se službou Azure Machine Learning prostřednictvím jeho [sady SDK pro Python](../machine-learning/service/quickstart-create-workspace-with-python.md), rozhraní příkazového řádku a [webu Azure portal](../machine-learning/service/quickstart-get-started.md).

Upgrade z verze Preview služby Batch AI ve službě GA'ed Azure Machine Learning poskytuje lepší základními pojmy, které se snadněji používá jako jsou odhady a úložišť. Zaručuje taky všeobecné dostupnosti služeb Azure na úrovni smlouvy o úrovni služeb a zákaznické podpory.

Služba Azure Machine Learning, které službu také přináší nové funkce, jako automatické machine learning, hyperparametrů a ML kanály, které jsou užitečné v nejvíce rozsáhlé úlohy AI. Schopnost nasadit trénovaného modelu bez nutnosti přepínat do samostatné služby pomáhá dokončit datové vědy smyčky od přípravy dat (s použitím sady SDK pro Data Prep) k operacionalizaci a sledování modelů.

## <a name="start-migrating"></a>Zahájení migrace
Vyhnete se tak přerušením pro vaše aplikace a využívat nejnovější funkce, proveďte následující kroky před 31. března 2019:

1. Vytvořit pracovní prostor služby Azure Machine Learning a začít:
    + [Rychlý start postavená na Pythonu](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [Azure portal podle rychlého startu](../machine-learning/service/quickstart-get-started.md)

1. Nainstalujte [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) a [sady SDK pro přípravu dat](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install). 

1. Nastavení [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute) k tréninku modelu.

1. Aktualizujte skripty, aby pomocí Azure Machine Learning Compute. Následující části vysvětlují, jak společný kód použijete pro služby Batch AI mapy kódu pro Azure Machine Learning. 


## <a name="create-workspaces"></a>Vytváření pracovních prostorů
Koncept inicializace pracovního prostoru v Azure Batch AI pomocí configuration.json mapuje podobně pomocí konfiguračního souboru ve službě Azure Machine Learning.

Pro **služby Batch AI**, nebyla tímto způsobem:

```python
sys.path.append('../../..')
import utilities as utils

cfg = utils.config.Configuration('../../configuration.json')
client = utils.config.create_batchai_client(cfg)

utils.config.create_resource_group(cfg)
_ = client.workspaces.create(cfg.resource_group, cfg.workspace, cfg.location).result()
```

**Služba Azure Machine Learning**, vyzkoušejte:

```python
from azureml.core.workspace import Workspace

ws = Workspace.from_config()
print('Workspace name: ' + ws.name, 
      'Azure region: ' + ws.location, 
      'Subscription id: ' + ws.subscription_id, 
      'Resource group: ' + ws.resource_group, sep = '\n')
```

Kromě toho můžete také vytvořit pracovní prostor přímo tak, že zadáte parametry konfigurace, jako jsou

```python
from azureml.core import Workspace
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

Další informace o třídě pracovní prostor Azure Machine Learning v [referenční dokumentaci k sadě SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py).


## <a name="create-compute-clusters"></a>Vytvořte výpočetní clustery
Azure Machine Learning podporuje více cílových výpočetních prostředí, z nichž některé jsou spravovány službou a ostatní, které lze připojit k vašemu pracovnímu prostoru (např.) HDInsight cluster nebo vzdáleném virtuálním počítači. Další informace o různých [cílových výpočetních prostředí](../machine-learning/service/how-to-set-up-training-targets.md). Konceptu vytváření Azure Batch AI výpočetní cluster mapuje se na vytváření AmlCompute clusteru ve službě Azure Machine Learning. Vytvoření Amlcompute trvá v konfiguraci výpočtů, který je podobný jak předávat parametry v Azure Batch AI. Je jedna věc, kterou si uvědomit, že automatické škálování na ve výchozím nastavení je ve vašem clusteru AmlCompute vzhledem k tomu je vypnuto ve výchozím nastavení v Azure Batch AI.

Pro **služby Batch AI**, nebyla tímto způsobem:

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

Pro **služby Azure Machine Learning**, vyzkoušejte:

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

Další informace o třídě AMLCompute v [referenční dokumentaci k sadě SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py). Mějte na paměti, že v konfiguraci vyšší pouze vm_size a max_nodes jsou povinné a jsou určené pro pokročilé nastavení clusteru s podporou pouze zbývající vlastnosti, jako jsou virtuální sítě.

## <a name="monitor-status-of-your-cluster"></a>Sledování stavu clusteru
Toto je jednodušší ve službě Azure Machine Learning jak vidíte níže.

Pro **služby Batch AI**, nebyla tímto způsobem:

```python
cluster = client.clusters.get(cfg.resource_group, cfg.workspace, cluster_name)
utils.cluster.print_cluster_status(cluster)
```

Pro **služby Azure Machine Learning**, vyzkoušejte:

```python
gpu_cluster.get_status().serialize()
```

## <a name="get-reference-to-a-storage-account"></a>Získat odkaz na účet úložiště
Koncept úložiště dat jako objektů blob, získá zjednodušenou objekt úložiště dat pomocí služby Azure Machine Learning. Ve výchozím nastavení pracovního prostoru služby Azure Machine Learning, vytvoří účet úložiště, ale vlastní úložiště lze také připojit jako součást vytváření pracovního prostoru. 

Pro **služby Batch AI**, nebyla tímto způsobem:

```python
azure_blob_container_name = 'batchaisample'
blob_service = BlockBlobService(cfg.storage_account_name, cfg.storage_account_key)
blob_service.create_container(azure_blob_container_name, fail_on_exist=False)
```

Pro **služby Azure Machine Learning**, vyzkoušejte:

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)
```

Další informace o registraci dalších účtů úložiště, nebo získání odkazu na jiného registrovaného úložiště dat v [dokumentace ke službě Azure Machine Learning](../machine-learning/service/how-to-access-data.md#access).


## <a name="download-and-upload-data"></a>Stažení a nahrání dat 
Pomocí těchto služeb můžete nahrát data do účtu úložiště snadno pomocí odkazu na úložiště dat výše. Pro Azure Batch AI jsme také nasadit cvičný skript jako součást Sdílení souborů, i když se zobrazí, jak můžete zadat jako součást konfigurace úlohy v případě služby Azure Machine Learning.

Pro **služby Batch AI**, nebyla tímto způsobem:

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


Pro **služby Azure Machine Learning**, vyzkoušejte:

```python
import os
import urllib
os.makedirs('./data', exist_ok=True)
download_url = 'https://s3.amazonaws.com/img-datasets/mnist.npz'
urllib.request.urlretrieve(download_url, filename='data/mnist.npz')

ds.upload(src_dir='data', target_path='mnist_dataset', overwrite=True, show_progress=True)

path_on_datastore = ' mnist_dataset/mnist.npz' ds_data = ds.path(path_on_datastore) print(ds_data)
```

## <a name="create-experiments"></a>Vytváření experimentů
Jak je uvedeno výše služby Azure Machine Learning zahrnuje koncept podobný Azure Batch AI experimentu. Každý experimentu potom může mít jednotlivé testy, podobně jako jak máme úlohy v Azure Batch AI. Služba Azure Machine Learning umožňuje mít hierarchii pod každým nadřazeným spustit pro spuštění jednotlivých podřízených.

Pro **služby Batch AI**, nebyla tímto způsobem:

```python
experiment_name = 'tensorflow_experiment'
experiment = client.experiments.create(cfg.resource_group, cfg.workspace, experiment_name).result()
```

Pro **služby Azure Machine Learning**, vyzkoušejte:

```python
from azureml.core import Experiment

experiment_name = 'tensorflow_experiment'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="submit-jobs"></a>Odesílání úloh
Jakmile vytvoříte experiment, máte několik různých způsobů odeslání spuštění. V tomto příkladu jsme se pokoušíte vytvořit pomocí TensorFlow modelu obsáhlého learningu a bude k tomu použít službu Azure Machine Learning odhad. [Estimator](../machine-learning/service/how-to-train-ml-models.md) je jednoduše funkce obálky na základní konfigurace spuštění, usnadňuje odeslat spuštění, který je aktuálně podporovaná Pytorch a TensorFlow jenom pro. Prostřednictvím koncept úložišť, zobrazí se také jak snadno se stane pro zadání cesty připojení 

Pro **služby Batch AI**, nebyla tímto způsobem:

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

Odeslání úlohy v Azure Batch AI je prostřednictvím funkce vytvořit.

```python
job_name = datetime.utcnow().strftime('tf_%m_%d_%Y_%H%M%S')
job = client.jobs.create(cfg.resource_group, cfg.workspace, experiment_name, job_name, parameters).result()
print('Created Job {0} in Experiment {1}'.format(job.name, experiment.name))
```

Úplné informace pro tento fragment kódu školení (včetně mnist_replica.py soubor, který jsme měli nahráli do sdílené složky výše) najdete v [úložišti na githubu ukázky poznámkového bloku Azure Batch AI](https://github.com/Azure/BatchAI/tree/2238607d5a028a0c5e037168aefca7d7bb165d5c/recipes/TensorFlow/TensorFlow-GPU-Distributed).

Pro **služby Azure Machine Learning**, vyzkoušejte:

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

Úplné informace pro tento fragment kódu školení (včetně souboru tf_mnist_replica.py) najdete v [úložiště github Poznámkový blok ukázky služby Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/distributed-tensorflow-with-parameter-server). Úložiště dat, samotného je možné připojit buď v jednotlivých uzlech, nebo trénovacích dat si můžete stáhnout na samotný uzel. Další informace o odkazování na úložiště dat ve vaší odhaduje se [dokumentace ke službě Azure Machine Learning](../machine-learning/service/how-to-access-data.md#access). 

Odesílání spustit ve službě Azure Machine Learning service je pomocí funkce Odeslat.

```python
run = experiment.submit(estimator)
print(run)
```

Neexistuje jiný způsob určení parametrů spuštění, pomocí spouštění config – užitečné zejména pro definování vlastní školení prostředí. Další podrobnosti najdete v tomto [ukázkový AmlCompute Poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-amlcompute/train-on-amlcompute.ipynb). 

## <a name="monitor-runs"></a>Monitorování spuštění
Jakmile odešlete spuštění, můžete buď počkat na její dokončení nebo monitorování ve službě Azure Machine Learning pomocí úhledné Jupyter pomůcky, které můžete vyvolat přímo v kódu. Také si můžete vyžádat kontextu jakékoli předchozí spuštění ve smyčce prostřednictvím různých experimenty v pracovním prostoru a jednotlivých běží v rámci každého pokusu.

Pro **služby Batch AI**, nebyla tímto způsobem:

```python
utils.job.wait_for_job_completion(client, cfg.resource_group, cfg.workspace, 
                                  experiment_name, job_name, cluster_name, 'stdouterr', 'stdout-wk-0.txt')

files = client.jobs.list_output_files(cfg.resource_group, cfg.workspace, experiment_name, job_name,
                                      models.JobsListOutputFilesOptions(outputdirectoryid='stdouterr')) 
for f in list(files):
    print(f.name, f.download_url or 'directory')
```


Pro **služby Azure Machine Learning**, vyzkoušejte:

```python
run.wait_for_completion(show_output=True)

from azureml.widgets import RunDetails
RunDetails(run).show()
```

Zde je snímek jak by widgetu načíst v poznámkovém bloku pro vyhledávání v reálném čase vaše protokoly: ![Monitorování diagram widgetu](./media/overview-what-happened-batch-ai/monitor.png)



## <a name="edit-clusters"></a>Upravit clustery
Odstranění clusteru je jednoduché. Kromě toho služby Azure Machine Learning vám také umožní aktualizovat cluster z v rámci poznámkového bloku, v případě, že chcete škálovat tak, aby vyšší počet uzlů, či navyšte čas nečinnosti wait dříve, než škálování clusteru. Jsme Nepovolovat, můžete změnit velikost virtuálního počítače samotného clusteru, protože vyžaduje nové nasazení efektivně v back-endu.

Pro **služby Batch AI**, nebyla tímto způsobem:
```python
_ = client.clusters.delete(cfg.resource_group, cfg.workspace, cluster_name)
```

Pro **služby Azure Machine Learning**, vyzkoušejte:

```python
gpu_cluster.delete()

gpu_cluster.update(min_nodes=2, max_nodes=4, idle_seconds_before_scaledown=600)
```

## <a name="get-support"></a>Získat podporu

Batch AI je plánované vyřadit z provozu 31. března a už blokuje nová předplatná v registraci na službu, pokud není na seznamu povolených vyvoláním výjimky prostřednictvím podpory.  Kontaktujte nás na adrese [Azure Batch AI školení ve verzi Preview](mailto:AzureBatchAITrainingPreview@service.microsoft.com) jakékoli dotazy nebo pokud máte nějakou zpětnou vazbu, jak migrovat do služby Azure Machine Learning.

Služba Azure Machine Learning je obecně dostupné služby. To znamená, že obsahuje potvrzené smlouva SLA a můžete vybírat z různých plánech podpory.

Ceny za použití infrastruktury Azure prostřednictvím služby Azure Batch AI nebo ve službě Azure Machine Learning by neměl lišit v závislosti na, účtujeme jenom cena za spotřebované výpočetní prostředky v obou případech. Další informace najdete v tématu [cenové kalkulačky](https://azure.microsoft.com/pricing/details/machine-learning-service/).

Zobrazit na dostupnost podle oblastí mezi těmito dvěma službami [webu Azure portal](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all).


## <a name="next-steps"></a>Další postup

+ Přečtěte si [Přehled služby Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Konfigurace cílové výpočetní prostředí k tréninku modelu](../machine-learning/service/how-to-set-up-training-targets.md) službou Azure Machine Learning.

+ Zkontrolujte [plány Azure do budoucna](https://azure.microsoft.com/updates/) a získejte informace o další aktualizace služby Azure.
