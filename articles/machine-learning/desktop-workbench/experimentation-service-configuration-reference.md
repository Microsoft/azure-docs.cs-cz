---
title: Soubory Azure konfigurace služby experimentování ve službě Machine Learning
description: Tento dokument obsahuje podrobnosti o nastavení konfigurace služby experimentování ve službě Azure ML.
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ms.openlocfilehash: 43bee297b917143c9014b28049c6dfa28727b757
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644508"
---
# <a name="azure-machine-learning-experimentation-service-configuration-files"></a>Soubory Azure konfigurace služby experimentování ve službě Machine Learning

Při spuštění skriptu v aplikaci Workbench Azure Machine Learning (Azure ML), soubory v řídí chování provádění **aml_config** složky. Tato složka je ve složce kořenového adresáře projektu. Je důležité k porozumění obsahu těchto souborů tak, abyste dosáhli požadovaného výsledku pro spouštění optimální způsobem.

Toto jsou relevantní soubory v této složce:
- conda_dependencies.yml
- spark_dependencies.yml
- COMPUTE cílové soubory
    - \<Název cílové výpočetní > .compute
- Spusťte konfigurační soubory
    - \<Název konfigurace spuštění > .runconfig

>[!NOTE]
>Obvykle máte výpočetní cílový soubor a spusťte konfigurační soubor pro každé cílové výpočetní prostředí, které vytvoříte. Můžete však vytvořit tyto soubory nezávisle a mít více konfiguračních souborů běhu odkazující na stejné cílové výpočetní prostředí.

## <a name="condadependenciesyml"></a>conda_dependencies.yml
Tento soubor je [souboru prostředí conda](https://conda.io/docs/using/envs.html#create-environment-file-by-hand) , který určuje verzi modulu runtime Pythonu a balíčky, na kterých závisí váš kód. Když aplikaci Azure ML Workbench spustí skript z kontejneru Dockeru nebo clusteru HDInsight, vytvoří se [prostředí conda](https://conda.io/docs/using/envs.html) vašeho skriptu ke spuštění na. 

V tomto souboru zadáte balíčky Pythonu, které potřebuje váš skript pro spuštění. Služba experimentování ve službě Azure ML vytvoří prostředí conda podle seznamu závislostí. Balíčky uvedené v tomto poli musí být dostupný prostřednictvím modulu prostřednictvím kanálů, jako:

* [continuum.io](https://anaconda.org/conda-forge/repo)
* [PyPI](https://pypi.python.org/pypi)
* veřejně přístupném koncovém bodu (URL)
* nebo místní cesta k souboru
* Další dostupný prostřednictvím modulu

>[!NOTE]
>Při spuštění v clusteru HDInsight, vytvoří aplikaci Azure ML Workbench prostředí conda pro konkrétního spuštění. To umožňuje různým uživatelům spouštět v prostředích různých pythonu ve stejném clusteru.  

Tady je příklad typické **conda_dependencies.yml** souboru.
```yaml
name: project_environment
dependencies:
  # Python version
  - python=3.5.2
  
  # some conda packages
  - scikit-learn
  - cryptography
  
  # use pip to install some more packages
  - pip:
     # a package in PyPi
     - azure-storage
     
     # a package hosted in a public URL endpoint
     - https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl
     
     # a wheel file available locally on disk (this only works if you are executing against local Docker target)
     - C:\temp\my_private_python_pkg.whl
```

Azure ML Workbench používá stejné prostředí conda bez nutnosti opětovného sestavení až **conda_dependencies.yml** zůstává stejná. Pokud se změní závislostí ho znovu prostředí.

>[!NOTE]
>Pokud je cílem spuštění proti _místní_ výpočetního kontextu, **conda_dependencies.yml** soubor **není** použít. Závislosti balíčků pro vaše místní prostředí Azure ML Workbench Pythonu potřeba nainstalovat ručně.

## <a name="sparkdependenciesyml"></a>spark_dependencies.yml
Tento soubor Určuje název aplikace Spark, když odešlete skript PySpark a Spark balíčky, které je třeba nainstalovat. Můžete také zadat veřejného úložiště Maven, jakož i Spark balíčky, které najdete v těchto úložištích Maven.

Zde naleznete příklad:

```yaml
configuration:
  # Spark application name
  "spark.app.name": "ClassifyingIris"
  
repositories:
  # Maven repository hosted in Azure CDN
  - "https://mmlspark.azureedge.net/maven"
  
  # Maven repository hosted in spark-packages.org
  - "https://spark-packages.org/packages"
  
packages:
  # MMLSpark package hosted in the Azure CDN Maven
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.5"
    
  # spark-sklearn packaged hosted in the spark-packages.org Maven
  - group: "databricks"
    artifact: "spark-sklearn"
    version: "0.2.0"
```

>[!NOTE]
>Optimalizace parametrů, jako je například velikost pracovního procesu a počet jader clusteru by měly patřit do oddílu "konfigurace" v souboru spark_dependecies.yml 

>[!NOTE]
>Pokud spouštíte skript v prostředí Python *spark_dependencies.yml* soubor se ignoruje. Používá se pouze v případě, že se spouští Spark (buď v Dockeru nebo clusteru HDInsight).

## <a name="run-configuration"></a>Konfigurace spuštění
Pokud chcete zadat konkrétní konfigurace spuštění, budete potřebovat .compute soubor a soubor .runconfig. Tyto jsou obvykle generovány pomocí příkazu rozhraní příkazového řádku. Můžete také naklonovat ukončení těch, které jsou, je přejmenovat a upravovat.

```azurecli
# create a compute target pointing to a VM via SSH
$ az ml computetarget attach remotedocker -n <compute target name> -a <IP address or FQDN of VM> -u <username> -w <password>

# create a compute context pointing to an HDI cluster head-node via SSH
$ az ml computetarget attach cluster -n <compute target name> -a <IP address or FQDN of HDI cluster> -u <username> -w <password> 
```

Tento příkaz vytvoří dvojici souborů založené na cílové výpočetní prostředí, který je zadán. Řekněme, že je vaše cílové výpočetní prostředí s názvem _foo_. Tento příkaz vygeneruje _foo.compute_ a _foo.runconfig_ ve vašich **aml_config** složky.

>[!NOTE]
> _místní_ nebo _docker_ názvy jsou libovolné soubory konfigurace spuštění. Azure ML Workbench přidá že tyto dvě konfigurace spuštění, když vytvoříte prázdný projekt pro vaše pohodlí. Můžete přejmenovat "<run configuration name>.runconfig" soubory, které jsou součástí šablony projektu, nebo vytvořit nové s libovolný název.

### <a name="compute-target-namecompute"></a>\<Název cílové výpočetní > .compute
_\<Název cílové výpočetní > .compute_ soubor Určuje informace o připojení a konfigurace pro cílové výpočetní prostředí. Je to seznam dvojic název hodnota. Podporovaná nastavení jsou následující:

**typ**: typ výpočetní prostředí. Podporované hodnoty jsou:
  - místní
  - Vzdálený
  - Docker
  - remotedocker
  - Cluster

**baseDockerImage**: The Docker image sloužící ke spuštění skriptu Pythonu/PySpark. Výchozí hodnota je _microsoft/mmlspark:plus-0.7.91_. Podporujeme také jeden jiný obrázek: _microsoft/mmlspark:plus-gpu-0.7.91_, který poskytuje přístup GPU na hostitelském počítači (Pokud je k dispozici GPU).

**Adresa**: IP adresa nebo plně kvalifikovaný název domény (plně kvalifikovaný název domény) virtuálního počítače nebo HDInsight hlavní uzel clusteru.

**uživatelské jméno**: The uživatelské jméno SSH pro přístup k virtuálnímu počítači nebo hlavní uzel HDInsight.

**heslo**: šifrované heslo pro připojení SSH.

**sharedVolumes**: Příznak, který signalizuje, že prováděcí modul by měl použít Docker sdíleného svazku funkce k odeslání souborů projektu vpřed a zpět. Máte tento příznak zapnutý můžou urychlit provádění od Dockeru můžete přístup k projektům přímo bez nutnosti kopírovat. Doporučujeme nastavit _false_ Pokud modul Docker běží na Windows, od sdílení svazků pro Docker ve Windows může být nespolehlivá. Nastavte ho na _true_ Pokud běží v systému macOS nebo Linux.

**nvidiaDocker**: Tento příznak, pokud je nastavena na _true_, říká služby experimentování ve službě Azure ML k použití _nvidia docker_ příkazů, na rozdíl od standardní _docker_příkaz ke spuštění image Dockeru. _Nvidia docker_ modul umožňuje kontejneru Dockeru pro přístup k hardwaru GPU. Nastavení je povinné, pokud chcete spustit spouštění GPU v kontejneru Dockeru. Podporuje pouze hostiteli se systémem Linux _nvidia docker_. Například DSVM založených na Linuxu v Azure se dodává s _nvidia docker_. _NVIDIA docker_ v současné době se nepodporuje na Windows.

**nativeSharedDirectory**: Tato vlastnost určuje základní adresář (třeba: _~/.azureml/share/_) uložení souborů aby bylo možné sdílet mezi běží na stejném cílového výpočetního prostředí. Pokud toto nastavení se používá při spuštění v kontejneru Dockeru, _sharedVolumes_ musí být nastavena na hodnotu true. V opačném případě se nezdaří spuštění.

**userManagedEnvironment**: Tato vlastnost určuje, zda tento cílové výpočetní prostředí je spravovaný uživatel přímo nebo prostřednictvím služby experimentování ve službě.  

**pythonLocation**: Tato vlastnost určuje umístění modulu runtime pythonu se použije na cílové výpočetní prostředí ke spuštění programu uživatele. 

### <a name="run-configuration-namerunconfig"></a>\<Název konfigurace spuštění > .runconfig
_\<Název konfigurace spuštění > .runconfig_ určuje chování při spuštění experimentů Azure ML. Můžete nakonfigurovat chování při spuštění jako je sledování historie spuštění nebo co cílové použít spolu s mnoha dalších výpočetní prostředí. Názvy souborů konfigurace spuštění se používají k naplnění rozevíracího seznamu kontextu spuštění v desktopové aplikaci Azure ML Workbench.

**ArgumentVector**: Tato část určuje skript ke spuštění jako součást tohoto spuštění a parametry skriptu. Například, pokud máte následující fragment kódu ve vaší "<run configuration name>.runconfig" soubor 

```
 "ArgumentVector":[
  - "myscript.py"
  - 234
  - "-v" 
 ] 
```
_"az ml experiment odeslat foo.runconfig"_ automaticky spustí příkaz s _myscript.py_ souboru předávajícího 234 jako parametr a nastaví příznak--verbose.

**Cíl**: Tento parametr je název _.compute_ souboru, který _runconfig_ souboru odkazy. Obvykle odkazuje _foo.compute_ soubor, ale můžete upravit tak, aby odkazoval jiného cílového výpočetního prostředí.

**Proměnné prostředí**: Tato část umožňuje uživatelům nastavit proměnné prostředí jako součást jejich spuštění. Uživatele můžete zadat proměnné prostředí pomocí dvojice název hodnota v následujícím formátu:
```
EnvironmentVariables:
  "EXAMPLE_ENV_VAR1": "Example Value1"
  "EXAMPLE_ENV_VAR2": "Example Value2"
```

Tyto proměnné prostředí můžete přistupovat v kódu uživatele. Například tento kód Python vytiskne proměnnou prostředí s názvem "EXAMPLE_ENV_VAR"
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

**Rozhraní Framework**: Tato vlastnost určuje, pokud aplikaci Azure ML Workbench by měl spustit relaci Spark ke spuštění skriptu. Výchozí hodnota je _PySpark_. Nastavte ho na _Python_ Pokud neběží PySpark kód, který může pomoct spouštění rychlejší s menší nároky na úlohu.

**CondaDependenciesFile**: Tato vlastnost odkazuje na soubor, který určuje v prostředí závislosti systému conda *aml_config* složky. Pokud hodnotu _null_, odkazuje na výchozí hodnotu **conda_dependencies.yml** souboru.

**SparkDependenciesFile**: Tato vlastnost odkazuje na soubor, který určuje závislosti Sparku v **aml_config** složky. Je nastavený na _null_ ve výchozím nastavení a to ukazuje na výchozí **spark_dependencies.yml** souboru.

**PrepareEnvironment**: tuto vlastnost, pokud je nastavena na _true_, říká služba experimentování ve službě Příprava na základě závislostí systému conda, zadaný jako součást počáteční spustit prostředí conda. Tato vlastnost je platná pouze při spouštění ve prostředí pro Docker. Toto nastavení nemá žádný vliv, pokud se spouští _místní_ prostředí. 

**TrackedRun**: Tento příznak signály služby experimentování ve službě, jestli se mají sledovat spuštění v aplikaci Azure ML Workbench infrastruktury historie spuštění. Výchozí hodnota je _true_. 

**UseSampling**: _UseSampling_ Určuje, zda aktivní ukázkové datové sady pro zdroje dat se používají pro spuštění. Pokud nastavena na _false_, zdroje dat ingestování a úplná data načtená z úložiště dat používat. Pokud nastavit _true_, aktivní vzorky se používají. Uživatelé můžou použít **DataSourceSettings** k určení, jaké konkrétní ukázkové datové sady má použít, pokud se chcete přepsat aktivní vzorek. 

**DataSourceSettings**: nastavení zdroje dat určuje tento konfigurační oddíl. V této části uživatele určuje, které stávající ukázková data pro určitý zdroj dat se používá jako součást spuštění. 

Následující nastavení konfigurace určuje, že ukázky s názvem "MySample" se používá pro zdroj dat s názvem "MyDataSource"
```
DataSourceSettings:
    MyDataSource.dsource:
    Sampling:
    Sample: MySample
```

**DataSourceSubstitutions**: nahrazení zdroje dat lze použít, když chce uživatel přepínání z jednoho zdroje dat do jiného beze změny kódu. Například mohou uživatelé přepínat z vzorkovány dolů, místní soubor do původní, větší datové sady, tak, že změníte odkaz na zdroj dat uložených v Azure Blob. Když se používá k nahrazení, aplikaci Azure ML Workbench spustí zdroje dat a balíčků pro přípravu dat pomocí odkazu na zdroj dat náhradní.

Následující příklad nahradí "mylocal.datasource" odkazy ve zdroji dat Azure ML a balíčků pro přípravu dat "myremote.dsource". 
 
```
DataSourceSubstitutions:
    mylocal.dsource: myremote.dsource
```

Podle výše uvedených nahrazení, následující vzorový kód teď načteme "myremote.dsource" místo "mylocal.dsource" bez změny jejich kódu uživatelů.
```
df = datasource.load_datasource('mylocal.dsource')
```
## <a name="next-steps"></a>Další postup
Další informace o [konfigurace služby experimentování ve službě](experimentation-service-configuration.md).
