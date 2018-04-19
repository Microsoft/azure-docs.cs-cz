---
title: Azure Machine Learning experimentování služby konfigurační soubory
description: Tento dokument podrobně popisuje nastavení konfigurace služby Azure ML experimenty.
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ms.openlocfilehash: 8ea7e7453ba49b17560b7c2147dd6c71c6c1a7a0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="azure-machine-learning-experimentation-service-configuration-files"></a>Azure Machine Learning experimentování služby konfigurační soubory

Když spustíte skript v nástroji Azure Machine Learning (Azure ML) Workbench, soubory v řídí chování provádění **aml_config** složky. Tato složka je pod složku kořenového adresáře projektu. Je důležité si uvědomit, obsah těchto souborů k dosažení požadované výsledek pro vaše provádění optimální způsobem.

Toto jsou příslušné soubory v této složce:
- conda_dependencies.yml
- spark_dependencies.yml
- výpočetní cílové soubory
    - \<výpočetní cílová > .compute
- Spusťte konfigurační soubory
    - \<Spustit název konfigurace > .runconfig

>[!NOTE]
>Obvykle výpočetní cílový soubor a spusťte konfigurační soubor pro každou výpočetní cíl, který vytvoříte. Můžete však tyto soubory vytvořit nezávisle a mít odkazující na stejný cíl výpočetní více spuštění konfigurační soubory.

## <a name="condadependenciesyml"></a>conda_dependencies.yml
Tento soubor je [conda prostředí soubor](https://conda.io/docs/using/envs.html#create-environment-file-by-hand) který určuje verzi modulu runtime jazyka Python a balíčky, které váš kód závisí na. Když Azure ML Workbench spustí skript v kontejner Docker nebo clusteru HDInsight, vytvoří [conda prostředí](https://conda.io/docs/using/envs.html) vašeho skriptu ke spuštění na. 

V tomto souboru zadejte balíčky Python, které potřebuje váš skript pro spuštění. Služba Azure ML experimentování vytvoří prostředí conda podle vašeho seznamu závislostí. Balíčky uvedené v tomto poli musí být dostupný modul provádění prostřednictvím kanálů, jako:

* [continuum.io](https://anaconda.org/conda-forge/repo)
* [PyPI](https://pypi.python.org/pypi)
* veřejně přístupném koncovém bodu (URL)
* nebo místní cesta.
* Další dostupný modul provádění

>[!NOTE]
>Při spuštění v clusteru HDInsight, vytvoří Azure ML Workbench conda prostředí pro vaše konkrétní spustit. To umožňuje různým uživatelům spustit v prostředí různých python ve stejném clusteru.  

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

Azure ML Workbench používá stejné prostředí conda bez opětovného stejně dlouho jako **conda_dependencies.yml** zůstává stejná. Pokud změníte svoje závislosti ho bude znovu sestavit prostředí.

>[!NOTE]
>Pokud cílíte vůči _místní_ výpočetní kontextu, **conda_dependencies.yml** soubor **není** použít. Závislosti balíčků pro místní prostředí Azure ML Workbench Python je potřeba nainstalovat ručně.

## <a name="sparkdependenciesyml"></a>spark_dependencies.yml
Tento soubor Určuje název aplikace Spark při odesílání skript PySpark a Spark balíčky, které je potřeba nainstalovat. Můžete také zadat veřejného úložiště Maven, jakož i Spark balíčky, které najdete v těchto Maven úložiště.

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
>Ladění parametry, jako je například velikost pracovního procesu a jader clusteru by měli přejít do části "konfigurace" v souboru spark_dependecies.yml 

>[!NOTE]
>Pokud jsou spouštění skriptu v prostředí Python, *spark_dependencies.yml* soubor je ignorován. Používá se pouze v případě, že používáte proti Spark (buď na Docker nebo clusteru HDInsight).

## <a name="run-configuration"></a>Spuštění nástroje Konfigurace
Můžete zadat konkrétní konfiguraci spuštění, budete potřebovat soubor .compute a soubor .runconfig. Tyto jsou obvykle generovány pomocí rozhraní příkazového řádku příkaz. Můžete také klonovat těch, které jsou ukončení, je přejmenujte a upravit je.

```azurecli
# create a compute target pointing to a VM via SSH
$ az ml computetarget attach remotedocker -n <compute target name> -a <IP address or FQDN of VM> -u <username> -w <password>

# create a compute context pointing to an HDI cluster head-node via SSH
$ az ml computetarget attach cluster -n <compute target name> -a <IP address or FQDN of HDI cluster> -u <username> -w <password> 
```

Tento příkaz vytvoří dvojici soubory podle zadaným cílem výpočetní. Řekněme, že jste s názvem cílových výpočetní _foo_. Tento příkaz generuje _foo.compute_ a _foo.runconfig_ ve vaší **aml_config** složky.

>[!NOTE]
> _místní_ nebo _docker_ názvy pro spuštění konfigurační soubory, které jsou libovolný. Azure ML Workbench přidá že tyto dva spustit konfigurace při vytváření prázdného projektu pro usnadnění vaší práce. Můžete přejmenovat "<run configuration name>.runconfig" soubory, které jsou součástí šablony projektu, nebo vytvořit nové s libovolný název.

### <a name="compute-target-namecompute"></a>\<výpočetní cílová > .compute
_\<výpočetní cílová > .compute_ soubor Určuje připojení a informace o konfiguraci pro výpočetní cíl. Je to seznam dvojic název hodnota. Podporovaná nastavení jsou následující:

**typ**: typ výpočetním prostředí. Podporované hodnoty jsou:
  - místní
  - Vzdálené
  - Docker
  - remotedocker
  - Cluster

**baseDockerImage**: bitové kopie Docker Python nebo PySpark skript spuštěn. Výchozí hodnota je _microsoft/mmlspark:plus-0.7.91_. Také podporujeme jeden jiného obrázku: _microsoft/mmlspark:plus-gpu-0.7.91_, který umožňuje GPU přístup k počítači hostitele (Pokud je k dispozici GPU).

**Adresa**: IP adresa nebo plně kvalifikovaný název domény (plně kvalifikovaný název domény) virtuálního počítače nebo HDInsight hlavního uzlu clusteru.

**uživatelské jméno**: SSH uživatelské jméno pro přístup k virtuálnímu počítači nebo z hlavního uzlu HDInsight.

**heslo**: zašifrované heslo pro připojení SSH.

**sharedVolumes**: Příznak signál, že modul pro spuštění by měl používat Docker sdíleného svazku funkce pro odeslání souborů projektu a zpět. Má tento příznak zapnutý urychlit provádění vzhledem k tomu, že Docker přístup projekty přímo, aniž by bylo nutné je zkopírovat. Je vhodné nastavit _false_ Pokud modulu Docker běží v systému Windows od svazku sdílení Docker v systému Windows, může být nestabilním stavu. Nastavte ji na _true_ Pokud je spuštěn v systému macOS nebo Linux.

**nvidiaDocker**: Tento příznak, pokud je nastavena na _true_, informuje službu Azure ML experimentování na používání _nvidia docker_ příkaz oproti běžné _docker_příkaz ke spuštění bitové kopie Docker. _Nvidia docker_ modul umožňuje kontejner Docker hardware GPU přístup. Pokud budete chtít spuštění GPU kontejner Docker je požadované nastavení. Podporuje pouze Linux hostitele _nvidia docker_. Například se systémem Linux DSVM v Azure dodává s _nvidia docker_. _NVIDIA docker_ od teď není podporován v systému Windows.

**nativeSharedDirectory**: Tato vlastnost určuje základní adresář (třeba: _~/.azureml/share/_) uložení souborů Chcete-li sdílet běží na stejný cíl výpočty. Pokud toto nastavení se používá při spuštění na kontejner Docker _sharedVolumes_ musí být nastavena na hodnotu true. Provádění, jinak selže.

**userManagedEnvironment**: Tato vlastnost určuje, zda je tento cíl výpočetní přímo spravuje uživatele nebo spravovat pomocí služby experimenty.  

**pythonLocation**: Tato vlastnost určuje umístění na cílovém výpočetní použít ke spuštění programu uživatele modulu runtime jazyka python. 

### <a name="run-configuration-namerunconfig"></a>\<Spustit název konfigurace > .runconfig
_\<Spustit název konfigurace > .runconfig_ určuje Azure ML experimentovat chování při spuštění. Můžete nakonfigurovat chování při spuštění například sledování historie spouštění nebo co výpočetní cíle použít společně s mnohé další. Názvy spuštění konfigurační soubory, které slouží k naplnění rozevíracího seznamu kontext spuštění v Azure ML Workbench desktopová aplikace.

**ArgumentVector**: v této části Určuje skript, který chcete spustit v rámci provedení tohoto a parametry pro skript. Například pokud máte následující fragment kódu ve vašem "<run configuration name>.runconfig" soubor 

```
 "ArgumentVector":[
  - "myscript.py"
  - 234
  - "-v" 
 ] 
```
_"az ml experimentu odeslat foo.runconfig"_ automaticky spustí příkaz s _myscript.py_ souboru předávání v 234 jako parametr a nastaví--podrobné příznak.

**Cíl**: Tento parametr je název _.compute_ souboru, který _runconfig_ souboru odkazy. Obecně odkazuje _foo.compute_ soubor, ale můžete upravit ji tak, aby odkazoval na různé výpočetní cíl.

**Proměnné prostředí**: v této části umožňuje uživatelům nastavit proměnné prostředí v rámci jejich spuštění. Uživatele můžete zadat proměnné prostředí pomocí dvojice název hodnota v následujícím formátu:
```
EnvironmentVariables:
  "EXAMPLE_ENV_VAR1": "Example Value1"
  "EXAMPLE_ENV_VAR2": "Example Value2"
```

Tyto proměnné prostředí jsou přístupné v kódu uživatele. Například tento kód Python vytiskne proměnnou prostředí s názvem "EXAMPLE_ENV_VAR"
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

**Framework**: Tato vlastnost určuje, pokud má Azure ML Workbench spusťte relaci Spark pro spuštění skriptu. Výchozí hodnota je _PySpark_. Nastavte ji na _Python_ Pokud používáte PySpark kód, který pomůže spustit úlohu s menší nároky na rychlejší.

**CondaDependenciesFile**: Tato vlastnost odkazuje na soubor, který určuje závislosti prostředí conda v *aml_config* složky. Pokud nastavena na _null_, odkazuje na výchozí hodnoty **conda_dependencies.yml** souboru.

**SparkDependenciesFile**: Tato vlastnost odkazuje na soubor, který určuje závislostí Spark v **aml_config** složky. Je nastaven na hodnotu _null_ ve výchozím nastavení a jeho ukazuje na výchozí **spark_dependencies.yml** souboru.

**PrepareEnvironment**: Tato vlastnost, pokud je nastavena na _true_, informuje službu experimentování Příprava prostředí conda podle conda závislosti zadaný jako součást počáteční spustit. Tato vlastnost je platná pouze v případě, že spustíte proti Docker prostředí. Toto nastavení nemá žádný vliv, pokud používáte systém proti _místní_ prostředí. 

**TrackedRun**: Tento příznak signály službu experimentování, zda chcete sledovat spustit v Azure ML Workbench spustit historie infrastruktury. Výchozí hodnota je _true_. 

**UseSampling**: _UseSampling_ Určuje, jestli active ukázkových datových sad pro zdroje dat se používá pro spuštění. Pokud nastavena na _false_, zdroje dat ingestování a úplná data načíst z úložiště dat použít. Pokud nastavena na _true_, aktivní vzorky se používají. Uživatelé mohou používat **DataSourceSettings** k určení, které konkrétní ukázkových datových sad má použít, pokud chtějí přepsat active vzorku. 

**DataSourceSettings**: nastavení zdroje dat určuje tento konfigurační oddíl. V této části určuje uživatele, které existující vzorek dat pro určitý zdroj dat se používá jako součást spuštění. 

Následující nastavení konfigurace určuje, že tento ukázkový s názvem "MySample" se používá pro zdroj dat s názvem "MyDataSource"
```
DataSourceSettings:
    MyDataSource.dsource:
    Sampling:
    Sample: MySample
```

**DataSourceSubstitutions**: nahrazení zdroje dat lze použít, pokud chce uživatel přejít z jeden zdroj dat na jiný beze změny svůj kód. Například mohou uživatelé přepínat ze souboru vzorků dolů, místní původní, větší datovou sadu, můžete změnit odkaz na zdroj dat uložené v objektu Blob Azure. Pokud se používá nahrazení, Azure ML Workbench se spustí zdroje dat a dat příprava balíčků odkazující na zdroj dat substitute.

Následující příklad nahradí "mylocal.datasource" odkazy v Azure ML zdroje dat a dat příprava balíčků "myremote.dsource". 
 
```
DataSourceSubstitutions:
    mylocal.dsource: myremote.dsource
```

Podle výše nahrazení, následující ukázka kódu nyní čte z "myremote.dsource" místo "mylocal.dsource" bez uživatelé změnit svůj kód.
```
df = datasource.load_datasource('mylocal.dsource')
```
## <a name="next-steps"></a>Další postup
Další informace o [konfigurace služby experimentování](experimentation-service-configuration.md).
