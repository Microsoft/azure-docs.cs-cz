---
title: Vytváření opakovaně použitelných prostředí ML
titleSuffix: Azure Machine Learning
description: Vytvářejte a spravujte prostředí pro školení a nasazení modelů. Spravujte balíčky Pythonu a další nastavení pro prostředí.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: f733e29fc5fbce764fef9a713747d6793d2ebd43
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489314"
---
# <a name="create-and-manage-reusable-environments-for-training-and-deployment-with-azure-machine-learning"></a>Vytvářejte a spravujte opakovaně použitelná prostředí pro školení a nasazení pomocí Azure Machine Learning.
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak vytvářet a spravovat Azure Machine Learning [prostředí](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) , abyste mohli sledovat a reprodukuje závislosti softwaru projektů při jejich vývojech.

Správa závislostí softwaru je běžným úkolem pro vývojáře. Chcete mít jistotu, že sestavení jsou reprodukovatelná bez velkého množství ruční konfigurace softwaru. Díky řešení pro místní vývoj, jako je PIP a Conda, poskytuje třída prostředí Azure Machine Learning řešení pro vývoj místních i distribuovaných cloudů.

Příklady v tomto článku ukazují, jak:

* Vytvoření prostředí a určení závislostí balíčku
* Načtení a aktualizace prostředí
* Použití prostředí pro školení
* Použití prostředí pro nasazení webové služby

## <a name="what-are-environments"></a>Co jsou prostředí

Prostředí určují balíčky Pythonu, proměnné prostředí a nastavení softwaru kolem vašich školicích a vyhodnocovacích skriptů a časy spuštění (Python, Spark nebo Docker). Jsou spravované a se správou verzí v rámci vašeho pracovního prostoru Azure Machine Learning umožňují reprodukovatelné pracovní postupy, které lze auditovat a přenosné strojové učení napříč různými výpočetními cíli.

K vývoji školicího skriptu můžete použít objekt prostředí v místním výpočetním prostředí, použít stejné prostředí v Azure Machine Learning výpočetním prostředí pro modelově škálovatelné školení a dokonce model nasadit do stejného prostředí.

Níže vidíte, že stejný objekt prostředí lze použít jak v konfiguraci spuštění pro školení, tak v konfiguraci nasazení pro nasazení webových služeb.

![Diagram prostředí v pracovním postupu Machine Learning](./media/how-to-use-environments/ml-environment.png)

### <a name="types-of-environments"></a>Typy prostředí

Prostředí se můžou v podstatě rozdělit do tří **kategorií: řízená**, **uživatelsky spravovaná** a **spravovaná systémem**.

Dodaná prostředí jsou poskytována Azure Machine Learning a jsou ve výchozím nastavení k dispozici ve vašem pracovním prostoru. Obsahují kolekce balíčků a nastavení Pythonu, které vám pomůžou začít s různými architekturami strojového učení. 

V případě prostředí spravovaného uživatelem zodpovídáte za nastavení vašeho prostředí a instalaci každého balíčku, který je potřebný pro školicí skript na výpočetním cíli. Conda nebude kontrolovat vaše prostředí ani instalovat cokoli za vás. 

Prostředí spravovaná systémem se používají, když chcete, aby [conda](https://conda.io/docs/) spravovat prostředí Pythonu a závislosti skriptů za vás. Služba ve výchozím nastavení předpokládá tento typ prostředí kvůli jeho užitečnosti pro vzdálené výpočetní cíle, které se nedají ručně konfigurovat.

## <a name="prerequisites"></a>Předpoklady

* Sada Azure Machine Learning SDK pro Python je [nainstalována](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* [Pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).


## <a name="create-an-environment"></a>Vytvoření prostředí

Existuje několik způsobů, jak vytvořit prostředí pro vaše experimenty.

### <a name="use-curated-environment"></a>Použití spravovaného prostředí

Můžete vybrat jedno z uspořádaného prostředí a začít s nástrojem. 

* Prostředí __AzureML-Minimal__ obsahuje minimální sadu balíčků pro povolení sledování spuštění a nahrávání assetů. Můžete ho použít jako výchozí bod pro vlastní prostředí.

* Prostředí __AzureML-tutorial__ obsahuje běžné balíčky pro datové vědy, jako jsou Scikit-učení, PANDAS a matplotlib a větší sada balíčků AzureML-SDK.

Pořízené prostředí jsou zajištěna pomocí imagí Docker uložených v mezipaměti, což snižuje náklady na přípravu za běhu.

Použijte metodu __Environment. Get__ k výběru jednoho ze spravovaných prostředí:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Seznamovaná prostředí a jejich balíčky můžete zobrazit pomocí následujícího kódu:
```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Nespouštějte vlastní název prostředí s předponou _AzureML_ . Je vyhrazený pro prostředí, která jsou v něm uspořádaná.

### <a name="instantiate-an-environment-object"></a>Vytvoření instance objektu prostředí

Chcete-li ručně vytvořit prostředí, importujte třídu prostředí ze sady SDK a vytvořte instanci objektu prostředí s následujícím kódem.

```python
from azureml.core import Environment
Environment(name="myenv")
```

### <a name="conda-and-pip-specification-files"></a>Soubory specifikace conda a PIP

Můžete také vytvořit prostředí ze specifikace conda nebo souboru požadavků PIP.
Použijte metodu [from_conda_specification ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) nebo [from_pip_requirements ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) a zahrňte název vašeho prostředí a cestu k souboru požadovaného souboru do argumentu metoda.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

#From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="existing-conda-environment"></a>Existující prostředí conda

Pokud máte v místním počítači existující prostředí Conda, služba nabízí řešení pro vytvoření objektu prostředí z něj. Tímto způsobem můžete použít místní interaktivní prostředí na vzdáleném spuštění.

Následující kód vytvoří objekt prostředí ze stávajícího prostředí conda `mycondaenv` s metodou [from_existing_conda_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) .

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="automatically-create-environments"></a>Automatické vytváření prostředí

Automatické vytvoření prostředí tím, že odešlete školicí běh pomocí metody Submit (). Při odeslání školicího programu může sestavení nového prostředí trvat několik minut, a to v závislosti na velikosti požadovaných závislostí. 

Pokud před odesláním spuštění neurčíte prostředí v konfiguraci spuštění, vytvoří se pro vás výchozí prostředí.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attaches training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Shows each step of run 
run.wait_for_completion(show_output=True)
```

Podobně pokud použijete objekt [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) pro školení, můžete instanci Estimator odeslat přímo jako běh bez nutnosti určení prostředí. Objekt `Estimator` již zapouzdřuje prostředí a cíl výpočtů.


## <a name="add-packages-to-an-environment"></a>Přidání balíčků do prostředí

Přidejte balíčky do prostředí pomocí souborů Conda, PIP nebo soukromých kol. Zadejte každou závislost balíčku pomocí [třídy CondaDependency](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)a přidejte ji do PythonSection prostředí.

### <a name="conda-and-pip-packages"></a>Balíčky conda a PIP

Pokud je balíček dostupný v úložišti balíčků Conda, doporučuje se použít instalaci conda přes PIP. Důvodem je, že conda balíčky typicky docházejí s předem vytvořenými binárními soubory, které zajistí spolehlivější instalaci.

Následující příklad přidá `scikit-learn`, konkrétně 0.21.3 verze a `pillow` balíček do prostředí, `myenv` s [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) a [`add_pip_package()`mi](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) metodami, v uvedeném pořadí.

```python
from azureml.core import Environment
from azureml.core.environment import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs scikit-learn version 0.21.3 conda package
conda_dep.add_conda_package("scikit-learn==0.21.3")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

### <a name="private-wheel-files"></a>Soubory soukromých kol

Soubory privátního kolečka PIP můžete použít tak, že je nejdřív nahrajete do úložiště pracovního prostoru pomocí statické metody [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) a pak zachytíte adresu URL úložiště a předáte adresu URL do metody `add_pip_package()`.

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Správa prostředí

Spravujte prostředí, abyste je mohli aktualizovat, sledovat a opakovaně používat napříč výpočetními cíli a dalšími uživateli pracovního prostoru.

### <a name="register-environments"></a>Registrovat prostředí

Prostředí se do vašeho pracovního prostoru automaticky zaregistruje, když odešlete spuštění nebo nasazení webové služby. Prostředí můžete také manuálně zaregistrovat pomocí metody [Register ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) . Tato operace zpřístupňuje prostředí do entity, která je sledována a je v cloudu, a může být sdílena mezi uživateli pracovního prostoru.

Následující kód registruje prostředí, `myenv`, do pracovního prostoru `ws`.

```python
myenv.register(workspace=ws)
```

Při prvním použití v rámci školení nebo nasazení je prostředí zaregistrované v pracovním prostoru, sestavené a nasazené na výpočetním cíli. Služba ukládá do mezipaměti prostředí. Použití prostředí s mezipamětí trvá mnohem kratší dobu než používání nové služby nebo aktualizace, kterou jste aktualizovali.

### <a name="get-existing-environments"></a>Získat existující prostředí

Třída prostředí nabízí metody, které umožňují načíst existující prostředí v pracovním prostoru podle názvu, jako seznam nebo konkrétní školicí běh pro účely řešení potíží nebo auditování a také reprodukovatelnosti.

#### <a name="view-list-of-environments"></a>Zobrazit seznam prostředí

Zobrazte prostředí v pracovním prostoru pomocí [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-)a pak vyberte jedno pro opakované použití.

#### <a name="get-environment-by-name"></a>Získat prostředí podle názvu

Konkrétní prostředí můžete také získat podle názvu a verze.
Následující kód používá metodu [Get ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) pro načtení verze `1` prostředí, `myenv` v pracovním prostoru `ws`.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="training-run-specific-environment"></a>Konkrétní prostředí pro školicí běh

Chcete-li získat prostředí používané pro konkrétní spuštění po dokončení školení, použijte metodu [get_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) ve třídě run.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Aktualizace existujícího prostředí

Pokud provedete změny ve stávajícím prostředí, jako je například přidání balíčku Pythonu, vytvoří se nová verze prostředí, když buď odešlete Run, model nasadíte, nebo ručně zaregistrujete prostředí. Správa verzí umožňuje zobrazit změny v prostředí v průběhu času.

Pokud chcete aktualizovat verzi balíčku Python existujícího prostředí, zadejte přesné číslo verze tohoto balíčku. V opačném případě bude Azure Machine Learning znovu použít stávající prostředí s verzemi balíčků z, když bylo prostředí vytvořeno.

### <a name="debug-the-image-build"></a>Ladění sestavení image

V tomto příkladu se používá metoda [Build ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-) pro ruční vytvoření prostředí jako image Docker a monitoruje výstupní protokoly ze sestavení imagí pomocí [wait_for_completion ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-). Sestavená bitová kopie se pak zobrazí v pracovním prostoru Azure Container Registry, což je užitečné pro ladění.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="docker-and-environments"></a>Docker a prostředí

 Azure Machine Learning [DockerSection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) třídy `Environments` umožňuje přizpůsobení a řízení podrobností o hostovaném operačním systému, ve kterém se vaše školení spouští.

Když `enable` Docker, služba vytvoří image Docker a vytvoří prostředí Python s vašimi specifikacemi v rámci tohoto kontejneru Docker. To poskytuje další izolaci a reprodukovatelnost pro vaše školicí běhy.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Po vytvoření se obrázek Docker zobrazí v Azure Container Registry, který je k pracovnímu prostoru přidružen, ve výchozím nastavení.  Název úložiště má formu *\<\>UUID azureml_* . Součást jedinečného identifikátoru (*UUID*) odpovídá hodnotě hash vypočítané z konfigurace prostředí. Díky tomu může služba určit, jestli image odpovídající danému prostředí už existuje pro opakované použití.

Služba navíc automaticky používá jednu z [základních imagí](https://github.com/Azure/AzureML-Containers)založených na Ubuntu Linux a nainstaluje zadané balíčky Pythonu. Základní image má verze procesoru a GPU. Azure Machine Learning automaticky zjišťuje, která verze se má použít.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

> [!NOTE]
> Pokud zadáte `environment.python.user_managed_dependencies=False` při použití vlastní image Docker, služba sestaví prostředí conda v imagi a spustí běh v tomto prostředí namísto použití knihoven Pythonu, které jste mohli nainstalovat na základní image. Nastavte parametr na hodnotu `True`, aby se používaly vlastní nainstalované balíčky.

## <a name="using-environments-for-training"></a>Použití prostředí pro školení

Chcete-li odeslat školicí běh, je třeba zkombinovat prostředí, [cíl výpočtů](concept-compute-target.md) a školicí skript Pythonu do konfigurace spuštění; Obálkový objekt použitý pro odeslání spuštění.

Když odešlete školicí běh, může vytváření nového prostředí trvat několik minut, a to v závislosti na velikosti požadovaných závislostí. Prostředí ukládá služba do mezipaměti, takže pokud definice prostředí zůstane beze změny, bude úplná doba instalace platit jenom jednou.

Následující příklad spuštění místního skriptu ukazuje, kde byste jako objekt obálky použili [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) .

```python
from azureml.core import Environment, ScriptRunConfig, Experiment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.run_config.target = "local"

# Attach environment to run config
runconfig.run_config.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> Pokud chcete zakázat historii spuštění nebo spustit snímky, použijte nastavení v části `ScriptRunConfig.run_config.history`.

Pokud v konfiguraci spuštění nezadáte prostředí, služba při odeslání vašeho spuštění vytvoří výchozí prostředí.

### <a name="train-with-an-estimator"></a>Výuka s Estimator

Pokud používáte [Estimator](how-to-train-ml-models.md) pro školení, můžete jednoduše odeslat instanci Estimator přímo, protože už zapouzdřuje prostředí a cíl výpočtů.

Následující kód používá Estimator pro školení s jedním uzlem, které se spouští ve vzdáleném výpočetním prostředí pro model scikit a předpokládá, že dříve vytvořený objekt cíle služby COMPUTE `compute_target` a objekt úložiště dat `ds`.

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])

# Submit the run 
run = experiment.submit(sk_est)
```

## <a name="using-environments-for-web-service-deployment"></a>Použití prostředí pro nasazení webové služby

Prostředí můžete použít při nasazování modelu jako webové služby. To umožňuje reprodukovatelně připojenému pracovnímu postupu, ve kterém můžete model prosazovat, testovat a nasadit pomocí přesně stejných knihoven v rámci vašich školicích a odvozených výpočtů.

Chcete-li nasadit webovou službu, kombinování prostředí, výpočetního skriptu, vyhodnocování a zaregistrovaného modelu do objektu nasazení, [Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-). Přečtěte si další informace o [nasazení webových služeb](how-to-deploy-and-where.md).

V tomto příkladu Předpokládejme, že jste dokončili školicí běh a chcete tento model nasadit do služby Azure Container instance (ACI). Při sestavování webové služby jsou soubory modelů a bodování připojené k imagi a do image se přidá zásobník odvození Azure Machine Learning.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Ukázkové poznámkové bloky

Tento [ukázkový Poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) se rozbalí na základě konceptů a metod popsaných v tomto článku.

## <a name="next-steps"></a>Další kroky

* [Kurz: výuka modelu](tutorial-train-models-with-aml.md) používá ke školení modelu spravovaný výpočetní cíl.
* Jakmile budete mít školený model, zjistěte, [jak a kde nasadit modely](how-to-deploy-and-where.md).
* Podívejte se na referenční informace o sadě SDK [třídy prostředí](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) .
