---
title: Konfigurace služby experimentování ve službě Azure Machine Learning | Dokumentace Microsoftu
description: Tento článek obsahuje přehled služby experimentování ve službě Azure Machine Learning s pokyny o tom, jak ho nakonfigurovat.
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
ROBOTS: NOINDEX
ms.openlocfilehash: 3c5084e548bbb72fa38aae8b60aa46fb4d462dca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990344"
---
# <a name="configuring-azure-machine-learning-experimentation-service"></a>Konfigurace služby experimentování ve službě Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

## <a name="overview"></a>Přehled
Služba experimentování ve službě Azure Machine Learning umožňuje datovým vědcům ke spouštění jejich experimenty Azure Machine Learning spuštění pomocí funkce pro správu. Poskytuje rozhraní pro agilní experimentování s využitím rychlé iterace. Azure Machine Learning Workbench můžete začít s místní spuštění na vašem počítači a také snadno cestu pro škálování i horizontální navyšování kapacity do jiných prostředí, jako jsou vzdálené virtuální počítače datové vědy s GPU nebo clustery HDInsight se systémem Spark.

Služba experimentování ve službě je vytvořená pro poskytování izolované, reprodukovatelné a konzistentní spouštění své experimenty. Pomáhá spravovat cílových výpočetních prostředí, spouštěcí prostředí, a konfiguracích spuštění. Pomocí Azure Machine Learning Workbench spuštění a možnosti spuštění správy, můžete snadno přesouvat mezi různými prostředími. 

V projektu aplikace Workbench můžete spustit skript Python nebo PySpark, místně nebo ve velkém měřítku v cloudu. 

Skripty můžete spustit na: 

* Prostředí Pythonu (3.5.2) v místním počítači nainstalována aplikace Workbench
* Prostředí Conda Python uvnitř kontejneru Docker v místním počítači
* V prostředí Python, která vlastníte a spravovat na vzdáleném počítači s Linuxem
* Prostředí Conda Python uvnitř kontejneru Docker ve vzdáleném počítači s Linuxem. Například () [DSVM založeného na Ubuntu v Azure]https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* [HDInsight Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) v Azure

>[!IMPORTANT]
>Služba experimentování ve službě Azure Machine Learning aktuálně podporuje Python verze 3.5.2 a Spark 2.1.11 jako verze modulu runtime Pythonu a Spark, v uvedeném pořadí. 


### <a name="key-concepts-in-experimentation-service"></a>Klíčové koncepty služby experimentování ve službě
Je důležité pochopit následující konceptech v Azure Machine Learning spuštění experimentu. V následujících oddílech si probereme, jak používat tyto koncepty podrobně. 

#### <a name="compute-target"></a>Cílové výpočetní prostředí
A _cílové výpočetní prostředí_ Určuje, kde ke spuštění programu jako je pracovní plocha, vzdálenou Docker na virtuálním počítači nebo clusteru. Cílové výpočetní prostředí musí být adresovatelný a je přístupný. Aplikace Workbench dává možnost vytvářet cílových výpočetních prostředí a spravovat je pomocí aplikace Workbench a rozhraní příkazového řádku. 

_připojit az ml computetarget_ příkaz v rozhraní příkazového řádku vám umožní vytvořit cílové výpočetní prostředí, který vám pomůže v spuštěních.

Cílových podporovaných výpočetních prostředí jsou:
* Místní prostředí Pythonu (3.5.2) v počítači nainstalována aplikace Workbench.
* Místní Dockeru ve vašem počítači
* Uživatel spravován, prostředí Pythonu na vzdálenou virtuální počítače s Linux Ubuntu. Například [DSVM založeného na Ubuntu v Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* Vzdálené Dockeru na virtuální počítače s Linux Ubuntu. Například [DSVM založeného na Ubuntu v Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* [HDInsight pro Spark cluster](https://azure.microsoft.com/services/hdinsight/apache-spark/) v Azure

Služba experimentování ve službě aktuálně podporuje Python verze 3.5.2 2.1.11 jako Pythonu ve Sparku a Spark verze modulu runtime, v uvedeném pořadí. 

>[!IMPORTANT]
> Běžící virtuální počítače s Windows jsou Dockeru **není** podporovány jako vzdálený výpočetní cíle.

#### <a name="execution-environment"></a>Spuštění prostředí
_Prostředí pro spouštění_ definuje konfiguraci běhu a závislosti nutné ke spuštění programu v aplikaci Workbench.

Můžete spravovat prostředí pro místní spuštění pomocí své oblíbené nástroje a Správce balíčků, pokud pracujete v aplikaci Workbench výchozí modul runtime. 

Conda se používá ke správě místního Dockeru a vzdálené spuštění Docker, jakož i spuštění na základě HDInsight. Pro tyto cílových výpočetních prostředí, konfiguraci spuštění prostředí je spravována prostřednictvím **Conda_dependencies.yml** a **Spark_dependencies.yml soubory**. Tyto soubory jsou v **aml_config** složky v projektu.

**Jsou podporované moduly runtime pro spouštěcí prostředí:**
* Python verze 3.5.2
* Spark 2.1.11

### <a name="run-configuration"></a>Konfigurace spuštění
Kromě cíl a spuštění prostředí compute, Azure Machine Learning poskytuje rozhraní k definování a změnit *konfigurace spuštění*. Různé spuštění experimentu může vyžadovat jinou konfiguraci jako součást iterativní experimentování. Může být otevřeného rozsahy různých parametrů, pomocí různých zdrojů dat a optimalizace parametrů spark. Služba experimentování ve službě poskytuje rozhraní pro správu spusťte konfigurací.

Spuštění _az ml computetarget připojit_ příkaz vytvoří dva soubory v vaše **aml_config** složku ve vašem projektu: ".compute" a ".runconfig" po Tato konvence: _< your_ computetarget_name > .compute_ a _< your_computetarget_name > .runconfig_. Je automaticky vytvořen soubor .runconfig pro usnadnění práce při vytváření cílového výpočetního prostředí. Můžete vytvářet a spravovat další spuštění konfigurace pomocí _az ml runconfigurations_ příkaz v rozhraní příkazového řádku. Můžete také vytvořit a upravit v systému souborů.

Konfigurace spuštění v aplikaci Workbench můžete také zadat proměnné prostředí. Můžete zadat proměnné prostředí a jejich použití ve vašem kódu tak, že přidáte následující části v souboru .runconfig. 

```
EnvironmentVariables:
    "EXAMPLE_ENV_VAR1": "Example Value1"
    "EXAMPLE_ENV_VAR2": "Example Value2"
```

Tyto proměnné prostředí je přístupný ve vašem kódu. Například tento fragment kódu phyton vytiskne proměnnou prostředí s názvem "EXAMPLE_ENV_VAR1"
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

_**Následující obrázek znázorňuje základní tok pro první běh experimentu.**_
![](media/experimentation-service-configuration/experiment-execution-flow.png)

## <a name="experiment-execution-scenarios"></a>Scénáře spuštění experimentu
V této části jsme Ponořte se do spuštění scénáře a seznamte se s Azure Machine Learning vykonávání experimenty, konkrétně spuštění experimentu místně, na vzdáleném virtuálním počítači a v clusteru HDInsight. Tato část je návod, od vytváření cílového výpočetního prostředí k provádění své experimenty.

>[!NOTE]
>Pro zbývající část tohoto článku používáme příkazů rozhraní příkazového řádku (rozhraní příkazového řádku) k zobrazení, koncepty a funkce. Funkce je zde popsáno, lze také z aplikace Workbench.

## <a name="launching-the-cli"></a>Spuštění rozhraní příkazového řádku
Snadný způsob, jak spusťte rozhraní příkazového řádku je otevření projektu v aplikaci Workbench a přejdete do **soubor--> otevřít příkazový řádek**.

![](media/experimentation-service-configuration/opening-cli.png)

Tento příkaz spustí okno terminálu, ve kterém můžete zadat příkazy, které spouštění skriptů v aktuální složce projektu. Toto okno terminálu se nakonfigurují prostředí Python verze 3.5.2, která je nainstalovaná aplikace Workbench.

>[!NOTE]
> Při spuštění žádný _az ml_ příkazu z příkazového okna, je potřeba ověřovány proti Azure. Rozhraní příkazového řádku používá mezipaměti nezávislé ověřování desktopové aplikace a tak přihlášení do aplikace Workbench neznamená, že jste ověřeni ve svém prostředí rozhraní příkazového řádku. Chcete-li ověřit, použijte následující postup. Ověřovací token do místní mezipaměti pro určitou dobu, stačí tento postup opakujte, když vyprší platnost tokenu. Když vyprší platnost tokenu nebo pokud dochází k chybám při ověřování, spusťte následující příkazy:

```
# to authenticate 
$ az login

# to list subscriptions
$ az account list -o table

# to set current subscription to a particular subscription ID 
$ az account set -s <subscription_id>

# to verify your current Azure subscription
$ az account show
```

>[!NOTE] 
>Při spuštění _az ml_ příkaz v rámci složky projektu, ujistěte se, že projekt patří k účtu experimentování ve službě Azure Machine Learning na _aktuální_ předplatného Azure. Jinak může dojít k chybám spouštění.


## <a name="running-scripts-and-experiments"></a>Spouštění skriptů a experimenty
Pomocí aplikace Workbench, můžete spustit váš Python a skriptů PySpark v různých výpočetních cílů pomocí _odeslání experimentu ml az_ příkazu. Tento příkaz vyžaduje definici konfigurace spuštění. 

Aplikace Workbench vytvoří odpovídající soubor runconfig při vytvoření cílové výpočetní prostředí, ale můžete vytvořit další spuštění konfigurace pomocí _az ml runconfiguration vytvořit_ příkazu. Můžete také ručně upravte soubory konfigurace spuštění.

Konfigurace spuštění se zobrazí jako součást experiment spustit prostředí v aplikaci Workbench. 

>[!NOTE]
>Další informace o spuštění konfiguračního souboru v [odkaz Konfigurace spuštění experimentu](experimentation-service-configuration-reference.md) oddílu.

## <a name="running-a-script-locally-on-workbench-installed-runtime"></a>Skriptu místně v aplikaci Workbench nainstalovat modul runtime
Aplikace Workbench umožňuje spouštět skripty přímo proti modulu runtime aplikace Workbench nainstalovat Python verze 3.5.2. Tato výchozí modul runtime je nainstalován v době instalace aplikace Workbench a zahrnuje knihovny Azure Machine Learning a závislosti. Výsledky spuštění a artefakty pro místní spuštění jsou stále uloženy ve službě historie spuštění v cloudu.

Na rozdíl od spuštění dockeru, tato konfigurace je _není_ spravuje systém Conda. Budete muset ručně zřízení závislosti balíčků pro vaše místní prostředí Python aplikace Workbench.

Spuštěním následujícího příkazu spusťte skript místně v aplikaci Workbench nainstalovat Python prostředí. 

```
$az ml experiment submit -c local myscript.py
```

Cestu k výchozí prostředí Pythonu najdete tak, že zadáte následující příkaz v okně rozhraní příkazového řádku aplikace Workbench.
```
$ conda env list
```

>[!NOTE]
>Spuštění prostředí PySpark místně přímo proti místní Spark je aktuálně **není** podporována. Aplikace Workbench podporuje spuštění na místní Docker skriptů PySpark. Azure Machine Learning základní image Dockeru obsahuje Spark 2.1.11 předinstalovaným. 

_**Přehled o místní spuštění skriptu Pythonu:**_
![](media/experimentation-service-configuration/local-native-run.png)

## <a name="running-a-script-on-local-docker"></a>Spuštění skriptu na místní Dockeru
Vaše projekty lze také spustit v kontejneru Dockeru na místním počítači pomocí služby experimentování ve službě. Aplikace Workbench poskytuje základní image Dockeru, která se dodává s knihovnami Azure Machine Learning a také jako usnadňují Spark místní spuštění Spark 2.1.11 modulu runtime. Docker musí být spuštěna na místním počítači.

Pro spuštění v místním Docker váš skript Python nebo PySpark, můžete spustit následující příkazy v rozhraní příkazového řádku.

```
$az ml experiment submit -c docker myscript.py
```
nebo
```
az ml experiment submit --run-configuration docker myscript.py
```

Pomocí základní image Dockeru Azure Machine Learning je připraveno prostředí pro spuštění na místní Dockeru. Aplikace Workbench stáhne tuto bitovou kopii při spuštění pro první čas a Překryvné prvky s balíčky zadané v souboru conda_dependencies.yml. Tato operace zpomaluje počáteční, ale následné spuštění se zkopírují podstatně rychleji díky opětovné použití v mezipaměti vrstvy aplikace Workbench. 

>[!IMPORTANT]
>Je potřeba spustit _experimentu ml az Příprava - c docker_ příkaz nejprve k přípravě image Dockeru pro první spuštění. Můžete také nastavit **PrepareEnvironment** parametr na hodnotu true v souboru docker.runconfig. Tato akce automaticky připraví vaše prostředí jako součást spuštění provádění.  

>[!NOTE]
>Pokud skript PySpark běžící na Sparku, používá se také spark_dependencies.yml kromě conda_dependencies.yml.

Spouštění vašich skriptů na image Dockeru poskytuje následující výhody:

1. Zajišťuje, že váš skript můžete spolehlivě provést v dalším spuštění prostředí. Spuštění v kontejneru Dockeru pomáhá zjistit a vyhnout se všechny místní odkazy, které může mít vliv na přenositelnost. 

2. Umožňuje rychle testování kódu na moduly runtime a rozhraní, které jsou komplexní instalace a konfigurace, jako je Apache Spark, aniž byste museli nainstalovat sami.


_**Přehled o místní spuštění Docker pro skript Pythonu:**_
![](media/experimentation-service-configuration/local-docker-run.png)

## <a name="running-a-script-on-a-remote-docker"></a>Spuštění skriptu na vzdálené Dockeru
V některých případech nemusí být prostředkům dostupným v místním počítači dost informací k trénování požadovaný model. V takovém případě služba experimentování umožňuje snadný způsob, jak spouštět skripty Python nebo PySpark výkonnější virtuální počítače pomocí vzdálené spuštění Docker. 

Vzdálený počítač by měl splňovat následující požadavky:
* Vzdáleném virtuálním počítači musí běžet Linux Ubuntu a by měly být přístupné přes SSH. 
* Vzdáleném virtuálním počítači je potřeba mít Docker s.

>[!IMPORTANT]
> Docker je spuštěné virtuální počítače s Windows **není** podporovány jako vzdálený výpočetní cíle


Následující příkaz slouží k vytvoření kontextové cílové výpočetní a spuštění konfigurace pro vzdálené spuštění založené na Dockeru.

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```

Jakmile nakonfigurujete cílové výpočetní prostředí, můžete skript spustit následující příkaz.
```
$ az ml experiment submit -c remotevm myscript.py
```
>[!NOTE]
>Mějte na paměti tohoto spuštění prostředí je nakonfigurované použití specifikace v conda_dependencies.yml. spark_dependencies.yml se také používá-li v souboru .runconfig zadána PySpark framework. 

Proces konstrukce Dockeru pro vzdálené virtuální počítače je přesně stejný jako proces pro místní Dockeru běží, takže byste měli očekávat podobné možnosti jako spuštění.

>[!TIP]
>Pokud budete chtít vyhnout vytvořením image Dockeru pro vaše první práce s uvedenou latenci, slouží následující příkaz k přípravě cílového výpočetního prostředí před spuštěním skriptu. Příprava experimentu ml az - c remotedocker

_**Přehled o vzdáleném virtuálním počítači provádění skriptu Pythonu:**_
![](media/experimentation-service-configuration/remote-vm-run.png)

## <a name="running-a-script-on-a-remote-vm-targeting-user-managed-environments"></a>Spuštění skriptu na vzdáleném virtuálním počítači cílení na uživatele, spravovat prostředí
Služba experimentování ve službě podporuje také skriptu v prostředí Python vlastněných uživateli uvnitř vzdáleného virtuálního počítače se systémem Ubuntu. To umožňuje spravovat vlastní prostředí pro provádění a stále využít možnosti Azure Machine Learning. 

Postupujte podle následujících kroků pro spuštění skriptu ve svém vlastním prostředí.
* Příprava prostředí Pythonu ve vzdáleném virtuálním počítači se systémem Ubuntu nebo DSVM instalace závislostí.
* Požadavky Azure Machine Learning pomocí následujícího příkazu nainstalujte.

```
pip install -I --index-url https://azuremldownloads.azureedge.net/python-repository/preview --extra-index-url https://pypi.python.org/simple azureml-requirements
```

>[!TIP]
>V některých případech budete muset spustit tento příkaz v režimu sudo v závislosti na vaše oprávnění. 
```
sudo pip install -I --index-url https://azuremldownloads.azureedge.net/python-repository/preview --extra-index-url https://pypi.python.org/simple azureml-requirements
```
 
* Použijte následující příkaz k vytvoření definice cílového výpočetní prostředky a konfigurace spuštění pro spuštění spravovaného uživatele na vzdálené spuštění virtuálního počítače.
```
az ml computetarget attach remote --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```
>[!NOTE]
>Tím nastavíte parametr "userManagedEnvironment" v konfiguračním souboru .compute na hodnotu true.

* Nastavit umístění vašeho prostředí Python runtime spustitelného souboru v souboru .compute. By měla odkazovat na úplnou cestu jazyce python spustitelný soubor. 
```
pythonLocation: python3
```

Jakmile nakonfigurujete cílové výpočetní prostředí, můžete skript spustit následující příkaz.
```
$ az ml experiment submit -c remotevm myscript.py
```

>[!NOTE]
> Pokud spouštíte na DSVM, by měl použít následující příkazy

Pokud chcete spustit přímo v prostředí vaší DSVM globální python, spusťte tento příkaz.
```
sudo /anaconda/envs/py35/bin/pip install <package>
```


## <a name="running-a-script-on-an-hdinsight-cluster"></a>Spuštění skriptu na clusteru služby HDInsight
HDInsight je oblíbená platforma pro analýzy velkých objemů dat, podpora Apache Spark. Aplikace Workbench umožňuje experimentování velkých objemů dat pomocí clusterů HDInsight Spark. 

>[!NOTE]
>Cluster HDInsight musí jako primární úložiště používat Azure Blob. Použití úložiště Azure Data Lake se ještě nepodporuje.

Můžete vytvořit cílové výpočetní prostředí a spuštění nástroje Konfigurace pro cluster HDInsight Spark pomocí následujícího příkazu:

```
$ az ml computetarget attach cluster --name "myhdi" --address "<FQDN or IP address>" --username "sshuser" --password "sshpassword"  
```

>[!NOTE]
>Pokud používáte plně kvalifikovaný název domény místo IP adresy a má název vašeho clusteru Hdinsight Spark _foo_, je koncový bod SSH na uzel ovladače s názvem _foo-ssh.azurehdinsight.net_. Nezapomeňte **-ssh** přípony v názvu serveru při používání plně kvalifikovaný název domény pro _--adresu_ parametru.


Jakmile budete mít výpočetní kontext, spustíte následující příkaz pro spuštění vašeho skriptu PySpark.

```
$ az ml experiment submit -c myhdi myscript.py
```

Aplikace Workbench připraví a spravuje prostředí pro spuštění v clusteru HDInsight pomocí Conda. Je konfigurace spravovaná službou _conda_dependencies.yml_ a _spark_dependencies.yml_ konfigurační soubory. 

Pokud chcete spouštět experimenty v tomto režimu potřebujete přístup SSH ke clusteru HDInsight. 

>[!NOTE]
>Podporované konfigurace je že spuštěné Linux (Ubuntu pomocí Pythonu/PySpark 3.5.2 a Sparku 2.1.11) clustery HDInsight Spark.

_**Přehled služby HDInsight na základě spuštění skriptu PySpark**_
![](media/experimentation-service-configuration/hdinsight-run.png)


## <a name="running-a-script-on-gpu"></a>Spuštění skriptu na GPU
Pro spouštění vašich skriptů v GPU, lze postupujte podle pokynů v tomto článku:[použití GPU ve službě Azure Machine Learning](how-to-use-gpu.md)

## <a name="using-ssh-key-based-authentication-for-creating-and-using-compute-targets"></a>Použití ověřování pomocí klíče SSH pro vytváření a používání cílových výpočetních prostředí
Azure Machine Learning Workbench umožňuje vytváření a používání cílových výpočetních prostředí pomocí ověřování založeného na klíč SSH kromě schéma pomocí uživatelského jména a hesla. Při použití remotedocker nebo v clusteru jako vaše cílové výpočetní prostředí, můžete použít tuto funkci. Při použití tohoto režimu v aplikaci Workbench vytvoří pár veřejného a privátního klíče a oznamuje ho zpátky veřejný klíč. Veřejný klíč se připojí k ~/.ssh/authorized_keys soubory pro své uživatelské jméno. Azure Machine Learning Workbench použije ssh ověřování pro přístup k a provádění na tento cílové výpočetní prostředí na základě klíče. Protože privátní klíč pro cílové výpočetní prostředí je uložen v úložišti klíčů pracovního prostoru, ostatní uživatelé pracovního prostoru použít cílové výpočetní prostředí stejným způsobem jako tím, že poskytuje pro vytvoření cílového výpočetního prostředí zadané uživatelské jméno.  

Provedením následujících kroků k použití této funkce. 

- Vytvořte cílové výpočetní prostředí pomocí jednoho z následujících příkazů.

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
```
nebo
```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" -k
```
- Připojte veřejný klíč vygenerovaná aplikace Workbench a soubor ~/.ssh/authorized_keys na cílové připojené výpočetní prostředí. 

>[!IMPORTANT]
>Je nutné se přihlásit na cílové výpočetní prostředí pomocí stejného uživatelského jména, které jste použili k vytvoření cílové výpočetní prostředí. 

- Teď můžete připravit a použití cílové výpočetní prostředí pomocí ověřování pomocí klíče SSH.

```
az ml experiment prepare -c remotevm
```

## <a name="next-steps"></a>Další postup
* [Vytvořte a nainstalujte Azure Machine Learning](quickstart-installation.md)
* [Správa modelů](model-management-overview.md)
