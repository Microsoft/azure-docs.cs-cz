---
title: 'Kurz: použití R k vytvoření modelu Machine Learning (Preview)'
titleSuffix: Azure Machine Learning
description: V tomto kurzu použijete sadu Azure Machine Learning R SDK k vytvoření modelu logistické regrese, který předpovídá pravděpodobnost závažnosti při havárii auta.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: sgilley
author: revodavid
ms.author: davidsmi
ms.date: 02/07/2020
ms.openlocfilehash: 0e94288b49cd57b59c126c95ca507477f1c56946
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321522"
---
# <a name="tutorial-use-r-to-create-a-machine-learning-model-preview"></a>Kurz: použití R k vytvoření modelu Machine Learning (Preview)


> [!IMPORTANT]
> Sada Azure Machine Learning R SDK je aktuálně ve verzi Public Preview.
> Verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V tomto kurzu použijete Azure Machine Learning R SDK (Preview) k vytvoření modelu logistické regrese, který předpovídá pravděpodobnost závažnosti při havárii auta. Uvidíte, jak Azure Machine Learning prostředky cloudu fungují s R, abyste zajistili škálovatelné prostředí pro školení a nasazení modelu.  

V tomto kurzu provedete následující úlohy:
> [!div class="checklist"]
> * Vytvoření pracovního prostoru Azure Machine Learning
> * Otevření RStudio z pracovního prostoru
> * Naklonujte https://github.com/Azure/azureml-sdk-for-r soubory nezbytné ke spuštění tohoto kurzu do svého pracovního prostoru.
> * Načtení dat a příprava na školení
> * Nahrajte data do úložiště dat, aby bylo dostupné pro vzdálené školení.
> * Vytvoření výpočetního prostředku pro vzdálenou výuku modelu
> * Výuka `caret` modelu pro předpověď pravděpodobnosti závažnosti
> * Nasazení koncového bodu předpovědi
> * Testování modelu z R

Pokud ještě nemáte předplatné Azure, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes


## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Azure Machine Learning pracovní prostor je základní prostředek v cloudu, který používáte k experimentování, výuce a nasazování modelů strojového učení. Přijedná se o vaše předplatné Azure a skupinu prostředků k snadno spotřebovanému objektu ve službě. 

Existuje mnoho způsobů, jak [vytvořit pracovní prostor](how-to-manage-workspace.md). V tomto kurzu vytvoříte pracovní prostor prostřednictvím Azure Portal, webové konzoly pro správu prostředků Azure. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Poznamenejte si svůj **pracovní prostor** a **předplatné**. Budete je potřebovat, abyste se ujistili, že vytvoříte experiment na správném místě. 


## <a name="open-rstudio"></a><a name="open"></a>Otevřít RStudio

V tomto příkladu se v pracovním prostoru používá výpočetní instance pro prostředí bez instalace a předem nakonfigurované prostředí. Použijte [vlastní prostředí](https://azure.github.io/azureml-sdk-for-r/articles/installation.html) , pokud chcete mít kontrolu nad prostředím, balíčky a závislostmi na vašem počítači.

Ke spuštění tohoto kurzu použijte RStudio na instanci výpočetní služby Azure ML.  

1. Na levé straně vyberte **COMPUTE** .

1. Pokud jeden z nich ještě neexistuje, přidejte výpočetní prostředek.

1. Po spuštění výpočetní služby použijte odkaz **RStudio** k otevření RStudio.


## <a name="clone-the-sample-vignettes"></a><a name="azure"></a>Naklonujte vzorový Vignettes 

Naklonujte https://github.com/Azure/azureml-sdk-for-r úložiště GitHub pro kopii souborů Vignette, které budete spouštět v tomto kurzu.

1. V RStudio přejděte do adresáře, kam chcete klonovat úložiště, na kartu terminálu a na disk CD.

1. Spusťte `git clone https://github.com/Azure/azureml-sdk-for-r` v terminálu a naklonujte úložiště.

1. V RStudio přejděte do složky *Vignettes* naklonované složky *AzureML-SDK-for-r* .  V části *Vignettes* vyberte možnost *výuka a nasazení-First-model. RMD* soubor k vyhledání Vignette používané v tomto kurzu. Další soubory používané pro Vignette jsou umístěné v podsložce s *výukou a nasazením prvního modelu* . Po otevření Vignette nastavte pracovní adresář na umístění souboru prostřednictvím **relace > nastavte pracovní adresář > na umístění zdrojového souboru**. 

> [!Important]
> Zbývající část tohoto článku obsahuje stejný obsah, jak vidíte v tématu *výuka a nasazení-First-model. Soubor RMD* Pokud máte zkušenosti s RMarkdown, můžete použít kód z tohoto souboru.  Nebo můžete zkopírovat nebo vložit fragmenty kódu z nebo z tohoto článku do skriptu R nebo příkazového řádku. 


## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí
Nastavení pro vývojovou práci v tomto kurzu zahrnuje následující akce:

* Instalace požadovaných balíčků
* Připojte se k pracovnímu prostoru, aby vaše výpočetní instance mohla komunikovat se vzdálenými prostředky.
* Vytvoření experimentu ke sledování vašich běhů
* Vytvoření vzdáleného výpočetního cíle pro použití pro školení

### <a name="install-required-packages"></a>Instalace požadovaných balíčků
Instance COMPUTE už má nainstalovanou nejnovější verzi R SDK z CRAN. Pokud chcete místo toho nainstalovat vývojovou verzi z GitHubu, použijte následující příkaz:
    
```R
remotes::install_github('https://github.com/Azure/azureml-sdk-for-r')
azuremlsdk::install_azureml()
```

> [!WARNING]
> Pokud se během procesu instalace zobrazí výzva " `Would you like to install Miniconda? [Y/n]:` ", Odpovězte prosím na "", `n` protože výpočetní instance už je Anaconda nainstalovaná a instalace Miniconda není potřeba.

Nyní pokračujte a naimportujte balíček **azuremlsdk** .

```R
library(azuremlsdk)
```

Skripty pro školení a bodování ( `accidents.R` a `accident_predict.R` ) mají nějaké další závislosti. Pokud plánujete spouštět tyto skripty místně, ujistěte se, že máte také tyto požadované balíčky.

### <a name="load-your-workspace"></a>Načtení pracovního prostoru
Vytvořte instanci objektu pracovního prostoru z existujícího pracovního prostoru. Následující kód načte podrobnosti pracovního prostoru z **config.jsv** souboru. Pracovní prostor můžete také načíst pomocí [`get_workspace()`](https://azure.github.io/azureml-sdk-for-r/reference/get_workspace.html) .

```R
ws <- load_workspace_from_config()
```

### <a name="create-an-experiment"></a>Vytvoření experimentu
Experiment Azure ML sleduje seskupení spuštění, obvykle ze stejného školicího skriptu. Vytvořte experiment pro sledování spuštění pro školení modelu blikajících dat o nehodách.

```R
experiment_name <- "accident-logreg"
exp <- experiment(ws, experiment_name)
```

### <a name="create-a-compute-target"></a>Vytvořit cíl výpočtů
Pomocí Azure Machine Learning COMPUTE (AmlCompute) spravovaná služba mohou vědečtí data v clusterech virtuálních počítačů Azure naučit modely strojového učení. Mezi příklady patří virtuální počítače s podporou GPU. V tomto kurzu vytvoříte cluster AmlCompute s jedním uzlem jako školicí prostředí. Následující kód vytvoří výpočetní cluster pro vás, pokud ještě neexistuje v pracovním prostoru.

Možná budete muset několik minut počkat, než se výpočetní cluster zřídí, pokud ještě neexistuje.

```R
cluster_name <- "rcluster"
compute_target <- get_compute(ws, cluster_name = cluster_name)
if (is.null(compute_target)) {
  vm_size <- "STANDARD_D2_V2" 
  compute_target <- create_aml_compute(workspace = ws,
                                       cluster_name = cluster_name,
                                       vm_size = vm_size,
                                       max_nodes = 1)
}

wait_for_provisioning_completion(compute_target)
```

## <a name="prepare-data-for-training"></a>Příprava dat pro školení
V tomto kurzu se používá data z [státní správy zabezpečení provozu](https://cdan.nhtsa.gov/tsftables/tsfar.htm) USA (s poděkováním [Marie C. Meyer a Tremika Finney](https://www.stat.colostate.edu/~meyer/airbags.htm)).
Tato datová sada zahrnuje data z více než 25 000 chyb auta v USA s proměnnými, které můžete použít k předpovědi pravděpodobnosti závažnosti. Nejprve importujte data do jazyka R a Transformujte je do nového datového rámce `accidents` pro analýzu a exportujte je do `Rdata` souboru.

```R
nassCDS <- read.csv("nassCDS.csv", 
                     colClasses=c("factor","numeric","factor",
                                  "factor","factor","numeric",
                                  "factor","numeric","numeric",
                                  "numeric","character","character",
                                  "numeric","numeric","character"))
accidents <- na.omit(nassCDS[,c("dead","dvcat","seatbelt","frontal","sex","ageOFocc","yearVeh","airbag","occRole")])
accidents$frontal <- factor(accidents$frontal, labels=c("notfrontal","frontal"))
accidents$occRole <- factor(accidents$occRole)
accidents$dvcat <- ordered(accidents$dvcat, 
                          levels=c("1-9km/h","10-24","25-39","40-54","55+"))

saveRDS(accidents, file="accidents.Rd")
```

### <a name="upload-data-to-the-datastore"></a>Nahrajte data do úložiště dat.
Nahrajte data do cloudu, aby k nim měli přístup vaše vzdálené školicí prostředí. Každý Azure Machine Learning pracovní prostor obsahuje výchozí úložiště dat, které ukládá informace o připojení do kontejneru objektů blob Azure zřízeného v účtu úložiště připojeném k pracovnímu prostoru. Následující kód odešle data o nehodách, která jste vytvořili výše, do tohoto úložiště dat.

```R
ds <- get_default_datastore(ws)

target_path <- "accidentdata"
upload_files_to_datastore(ds,
                          list("./accidents.Rd"),
                          target_path = target_path,
                          overwrite = TRUE)
```


## <a name="train-a-model"></a>Učení modelu

Pro tento kurz si nahráli model logistické regrese pro nahraná data pomocí vzdáleného výpočetního clusteru. K odeslání úlohy potřebujete:

* Příprava školicího skriptu
* Vytvoření estimátoru
* Odeslání úlohy

### <a name="prepare-the-training-script"></a>Příprava školicího skriptu
`accidents.R`Byl k dispozici školicí skript pro vás v adresáři *výuka a nasazení-First-model* . Všimněte si následujících podrobností v **školicím skriptu** , které byly provedeny k využití Azure Machine Learning pro školení:

* Školicí skript přebírá argument `-d` pro vyhledání adresáře, který obsahuje školicí data. Při pozdějším definování a odeslání úlohy odkazujete na úložiště dat pro tento argument. Služba Azure ML připojí složku úložiště ke vzdálenému clusteru pro úlohu školení.
* Školicí skript zaznamená konečnou přesnost jako metriku pro záznam spuštění v Azure ML pomocí `log_metric_to_run()` . Sada Azure ML SDK poskytuje sadu protokolovacích rozhraní API pro protokolování různých metrik během školicích běhů. Tyto metriky se zaznamenávají a ukládají v záznamu experimentálního spuštění. Metriky je pak možné kdykoli otevřít nebo zobrazit na stránce Podrobnosti o spuštění v nástroji [Studio](https://ml.azure.com). Úplnou sadu metod protokolování najdete v [referenčních informacích](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-training-experimentation) `log_*()` .
* Školicí skript uloží model do adresáře s názvem **výstupy**. `./outputs`Složka přijímá zvláštní zacházení pomocí Azure ml. Během školení se soubory zapsané do `./outputs` automaticky odešlou do záznamu o spuštění pomocí Azure ml a trvale se uloží jako artefakty. Uložením školicího modelu do nástroje budete `./outputs` moci získat přístup k souboru modelu a načíst ho i po skončení běhu a už nebudete mít přístup ke vzdálenému školicímu prostředí.

### <a name="create-an-estimator"></a>Vytvoření estimátoru

Azure ML Estimator zapouzdřuje informace o konfiguraci spuštění potřebné ke spuštění školicího skriptu na cílovém výpočetním cíli. Spuštění Azure ML se spouští jako kontejnerové úlohy na určeném cíli výpočtu. Ve výchozím nastavení bude image Docker sestavená pro vaši výukovou úlohu zahrnovat R, sadu Azure ML SDK a sadu běžně používaných balíčků R. Úplný seznam výchozích balíčků, které jsou zde zahrnuty, najdete v části.

Chcete-li vytvořit Estimator, zadejte:

* Adresář, který obsahuje skripty potřebné pro školení ( `source_directory` ). Všechny soubory v tomto adresáři se nahrají na uzly, které se mají vykoná. Adresář musí obsahovat školicí skript a požadované další skripty.
* Školicí skript, který se spustí ( `entry_script` ).
* Výpočetní cíl ( `compute_target` ), v tomto případě cluster AmlCompute, který jste vytvořili dříve.
* Parametry požadované ze školicího skriptu ( `script_params` ). Azure ML spustí školicí skript jako skript příkazového řádku s `Rscript` . V tomto kurzu zadáte jeden argument skriptu, bod připojení k datovému adresáři, ke kterému máte přístup `ds$path(target_path)` .
* Jakékoli závislosti prostředí požadované pro školení. Výchozí image Docker vytvořená pro školení už obsahuje tři balíčky ( `caret` , a `e1071` `optparse` ) potřebné ve školicím skriptu.  Takže nemusíte zadávat další informace. Pokud používáte balíčky R, které nejsou ve výchozím nastavení zahrnuty, `cran_packages` přidejte další balíčky Cran pomocí parametru Estimator. [`estimator()`](https://azure.github.io/azureml-sdk-for-r/reference/estimator.html)Úplnou sadu konfigurovatelných možností najdete v referenčních informacích.

```R
est <- estimator(source_directory = "train-and-deploy-first-model",
                 entry_script = "accidents.R",
                 script_params = list("--data_folder" = ds$path(target_path)),
                 compute_target = compute_target
                 )
```

### <a name="submit-the-job-on-the-remote-cluster"></a>Odešlete úlohu ve vzdáleném clusteru.

Nakonec odešlete úlohu ke spuštění v clusteru. `submit_experiment()` Vrátí objekt Run, který pak použijete k rozhraní v běhu. V celkovém případě trvá první spuštění **přibližně 10 minut**. Ale pro pozdější spuštění se stejná image Docker použije znovu, dokud se závislosti skriptů nezmění.  V tomto případě je obrázek uložen do mezipaměti a čas spuštění kontejneru je mnohem rychlejší.

```R
run <- submit_experiment(exp, est)
```

Podrobnosti o spuštění můžete zobrazit v prohlížeči RStudio. Když kliknete na odkaz "webové zobrazení", přejdete do Azure Machine Learning studia, kde můžete monitorovat spuštění v uživatelském rozhraní.

```R
view_run_details(run)
```

K výuce modelů dochází na pozadí. Počkejte, dokud model nedokončí školení, než spustíte více kódu.

```R
wait_for_run_completion(run, show_output = TRUE)
```

Vy a kolegové, kteří mají přístup k pracovnímu prostoru, můžou odeslat více experimentů paralelně a Azure ML bude pořizovat plánování úloh ve výpočetním clusteru. Cluster můžete dokonce nakonfigurovat tak, aby se automaticky nastavil na více uzlů, a škálovat zpátky, pokud ve frontě nejsou žádné další výpočetní úlohy. Tato konfigurace představuje nákladově efektivní způsob, jak týmy sdílet výpočetní prostředky.

## <a name="retrieve-training-results"></a>Načíst výsledky školení
Jakmile váš model dokončí školení, můžete získat přístup k artefaktům vaší úlohy, které byly trvale uloženy na záznam spuštění, včetně všech protokolovaných metrik a konečného školicího modelu.

### <a name="get-the-logged-metrics"></a>Získání protokolovaných metrik
Ve školicím skriptu `accidents.R` jste zaznamenali metriku z modelu: Přesnost předpovědi ve školicích datech. Metriky můžete zobrazit v [studiu](https://ml.azure.com)nebo je extrahovat do místní relace jako seznam R, a to takto:

```R
metrics <- get_run_metrics(run)
metrics
```

Pokud jste spustili více experimentů (například používáním odlišných proměnných, algoritmů nebo parametrů), můžete použít metriky z každého spuštění k porovnání a zvolit model, který budete používat v produkčním prostředí.

### <a name="get-the-trained-model"></a>Získat trained model
Můžete načíst trained model a podívat se na výsledky v místní relaci jazyka R. Následující kód stáhne obsah `./outputs` adresáře, který bude obsahovat soubor modelu.

```R
download_files_from_run(run, prefix="outputs/")
accident_model <- readRDS("outputs/model.rds")
summary(accident_model)
```

Vidíte některé faktory, které přispívají ke zvýšení odhadované pravděpodobnosti smrti:

* vyšší rychlost dopadu 
* ovladač samčího pohlaví
* starší cestující
* ro

Vidíte menší pravděpodobnost smrti:

* přítomnost airbags
* seatbelts přítomnosti
* přední kolize 

Rok pro vozidlo z výroby nemá významný vliv.

Pomocí tohoto modelu můžete vytvořit nové předpovědi:

```R
newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=16,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

## predicted probability of death for these variables, as a percentage
as.numeric(predict(accident_model,newdata, type="response")*100)
```

## <a name="deploy-as-a-web-service"></a>Nasazení jako webové služby

Pomocí modelu můžete předpovědět nebezpečí smrti proti kolizi. Použijte Azure ML k nasazení modelu jako předpovědi služby. V tomto kurzu nasadíte webovou službu v [Azure Container Instances](../container-instances/index.yml) (ACI).

### <a name="register-the-model"></a>Registrace modelu

Nejprve zaregistrujte model, který jste stáhli do pracovního prostoru, pomocí [`register_model()`](https://azure.github.io/azureml-sdk-for-r/reference/register_model.html) . Registrovaný model může být libovolná kolekce souborů, ale v tomto případě je objekt modelu R dostačující. Azure ML použije registrovaný model pro nasazení.

```R
model <- register_model(ws, 
                        model_path = "outputs/model.rds", 
                        model_name = "accidents_model",
                        description = "Predict probablity of auto accident")
```

### <a name="define-the-inference-dependencies"></a>Definovat závislosti odvození
Pokud chcete vytvořit webovou službu pro váš model, musíte nejdřív vytvořit hodnoticí skript ( `entry_script` ), skript R, který bude přebírat jako hodnoty vstupních proměnných (ve formátu JSON), a vyhodnotit předpovědi z modelu. Pro tento kurz použijte poskytnutý soubor hodnocení `accident_predict.R` . Skript bodování musí obsahovat `init()` metodu, která načte váš model a vrátí funkci, která používá model k vytvoření předpovědi založené na vstupních datech. Další podrobnosti najdete v [dokumentaci](https://azure.github.io/azureml-sdk-for-r/reference/inference_config.html#details) .

Dále definujte **prostředí** Azure ml pro závislosti balíčku vašeho skriptu. V prostředí zadáte balíčky R (od CRAN nebo jinde), které jsou potřeba ke spuštění skriptu. Můžete také zadat hodnoty proměnných prostředí, na které může skript odkazovat, aby bylo možné změnit jeho chování. Ve výchozím nastavení vytvoří Azure ML stejnou výchozí image Docker, která se používá s Estimator pro školení. Vzhledem k tomu, že kurz nemá žádné zvláštní požadavky, vytvořte prostředí bez speciálních atributů.

```R
r_env <- r_environment(name = "basic_env")
```

Pokud místo toho chcete použít vlastní image Docker pro nasazení, zadejte `custom_docker_image` parametr. [`r_environment()`](https://azure.github.io/azureml-sdk-for-r/reference/r_environment.html)Úplnou sadu konfigurovatelných možností pro definování prostředí najdete v referenčních informacích.

Nyní máte všechno, co potřebujete k vytvoření **Konfigurace odvození** pro zapouzdření skriptu bodování a závislostí prostředí.

```R
inference_config <- inference_config(
  entry_script = "accident_predict.R",
  source_directory = "train-and-deploy-first-model",
  environment = r_env)
```

### <a name="deploy-to-aci"></a>Nasazení do ACI
V tomto kurzu nasadíte službu, která bude ACI. Tento kód zřídí jeden kontejner, který reaguje na příchozí požadavky, který je vhodný pro testování a světelnou zátěž. [`aci_webservice_deployment_config()`](https://azure.github.io/azureml-sdk-for-r/reference/aci_webservice_deployment_config.html)Další konfigurovatelné možnosti najdete v tématu. (Pro nasazení v produkčním měřítku můžete také [nasadit do služby Azure Kubernetes](https://azure.github.io/azureml-sdk-for-r/articles/deploy-to-aks.html).)

``` R
aci_config <- aci_webservice_deployment_config(cpu_cores = 1, memory_gb = 0.5)
```

Nyní model nasadíte jako webovou službu. Nasazení **může trvat několik minut**. 

```R
aci_service <- deploy_model(ws, 
                            'accident-pred', 
                            list(model), 
                            inference_config, 
                            aci_config)

wait_for_deployment(aci_service, show_output = TRUE)
```

## <a name="test-the-deployed-service"></a>Otestování nasazené služby

Teď, když je model nasazený jako služba, můžete otestovat službu z R pomocí [`invoke_webservice()`](https://azure.github.io/azureml-sdk-for-r/reference/invoke_webservice.html) .  Poskytněte novou sadu dat pro předpověď, převeďte je na JSON a odešlete ji do služby.

```R
library(jsonlite)

newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=22,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

prob <- invoke_webservice(aci_service, toJSON(newdata))
prob
```

Můžete také získat koncový bod HTTP webové služby, který přijímá volání klientů REST. Tento koncový bod můžete sdílet s kýmkoli, kdo chce Testovat webovou službu nebo ji integrovat do aplikace.

```R
aci_service$scoring_uri
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte prostředky, jakmile je už nebudete potřebovat. Neodstraňujte žádný prostředek, který plánujete dál používat. 

Odstraňte webovou službu:
```R
delete_webservice(aci_service)
```

Odstraňte registrovaný model:
```R
delete_model(model)
```

Odstraňte výpočetní cluster:
```R
delete_compute(compute)
```

### <a name="delete-everything"></a>Odstranit vše

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Můžete také zachovat skupinu prostředků, ale odstranit jeden pracovní prostor. Zobrazte vlastnosti pracovního prostoru a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

* Teď, když jste dokončili první Azure Machine Learning experimentovat v R, se dozvíte víc o [Azure Machine Learning SDK pro R](https://azure.github.io/azureml-sdk-for-r/index.html).

* Přečtěte si další informace o Azure Machine Learning s R z příkladů v ostatních složkách *Vignettes* .