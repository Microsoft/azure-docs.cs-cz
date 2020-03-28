---
title: 'Kurz: Vytvoření modelu strojového učení pomocí R'
titleSuffix: Azure Machine Learning
description: V tomto kurzu použijete Azure Machine Learning R SDK k vytvoření logistického regresního modelu, který předpovídá pravděpodobnost úmrtí při autonehodě.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: sgilley
author: revodavid
ms.author: davidsmi
ms.date: 02/07/2020
ms.openlocfilehash: 72488ba339399c526e882ffd11c41410a0b011ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80159076"
---
# <a name="tutorial-use-r-to-create-a-machine-learning-model"></a>Kurz: Vytvoření modelu strojového učení pomocí R
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto kurzu použijete Azure Machine Learning R SDK k vytvoření logistického regresního modelu, který předpovídá pravděpodobnost úmrtí při autonehodě. Uvidíte, jak cloudové prostředky Azure Machine Learning pracovat s R poskytnout škálovatelné prostředí pro školení a nasazení modelu.  

V tomto kurzu provedete následující úlohy:
> [!div class="checklist"]
> * Vytvoření pracovního prostoru Azure Machine Learning
> * Klonování složky poznámkového bloku se soubory potřebnými ke spuštění tohoto kurzu do pracovního prostoru
> * Otevření rstudia z pracovního prostoru
> * Načtení dat a příprava na školení
> * Nahrání dat do úložiště dat, aby byla k dispozici pro vzdálené školení
> * Vytvoření výpočetního prostředku pro vzdálené trénování modelu
> * Trénování `caret` modelu k předvídání pravděpodobnosti úmrtí
> * Nasazení koncového bodu předpovědi
> * Otestujte model od R

Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.


## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Pracovní prostor Azure Machine Learning je základní prostředek v cloudu, který používáte k experimentování, trénování a nasazování modelů strojového učení. Vazby předplatného Azure a skupiny prostředků na snadno spotřebované objektve službě. 

Pracovní prostor vytvoříte prostřednictvím portálu Azure, webové konzoly pro správu prostředků Azure. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Poznamenejte si pracovní **prostor** a **předplatné**. Budete je potřebovat, abyste zajistili vytvoření experimentu na správném místě. 


## <a name="clone-a-notebook-folder"></a><a name="azure"></a>Klonování složky poznámkového bloku

Tento příklad používá cloudový notebook ový server ve vašem pracovním prostoru pro prostředí bez instalace a předkonfigurované. Pokud dáváte přednost kontrole nad prostředím, balíčky a závislostmi, použijte [vlastní prostředí.](https://azure.github.io/azureml-sdk-for-r/articles/installation.html)

Můžete dokončit následující experiment set-up a spustit kroky v Azure Machine Learning studio, konsolidované rozhraní, které zahrnuje nástroje strojového učení k provádění scénářů datové vědy pro odborníky datové vědy všech úrovní dovedností.

1. Přihlaste se do [studia Azure Machine Learning Studio](https://ml.azure.com/).

1. Vyberte předplatné a pracovní prostor, který jste vytvořili.

1. Vlevo vyberte **Poznámkové bloky.**

1. Otevřete složku **Ukázky.**

1. Otevřete složku **R.**

1. Otevřete složku s číslem verze.  Toto číslo představuje aktuální verzi sady R SDK.

1. Vyberte **"..."** vpravo od složky **vinět** a pak vyberte **Klonovat**.

    ![Složka Klonování](media/tutorial-1st-r-experiment/clone-folder.png)

1. Zobrazí se seznam složek zobrazující každého uživatele, který přistupuje k pracovnímu prostoru.  Vyberte složku a naklonujte složku **vinět.**

## <a name="a-nameopenopen-rstudio"></a><a name="open">Otevřít RStudio

Použijte RStudio na výpočetní instanci nebo virtuální počítač poznámkového bloku ke spuštění tohoto kurzu.  

1. Vlevo vyberte **Vypočítat.**

1. Přidejte výpočetní prostředek, pokud ještě neexistuje.

1. Po spuštění výpočetního výkonu použijte odkaz **RStudio** k otevření RStudio.

1. V RStudio, vaše *viněty* složka je několik úrovní dolů od *uživatelů* v části **Soubory** v pravém dolním bodě.  V *části viněty*vyberte složku *train-and-deploy-to-aci* a vyhledejte soubory potřebné v tomto kurzu.

> [!Important]
> Zbytek tohoto článku obsahuje stejný obsah, který vidíte v *train-and-deploy-to-aci. Rmd* soubor. Pokud máte zkušenosti s RMarkdown, neváhejte použít kód z tohoto souboru.  Nebo můžete zkopírovat/vložit fragmenty kódu odtud, nebo z tohoto článku do skriptu R nebo příkazového řádku.  


## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí
Nastavení pro vývojovou práci v tomto kurzu obsahuje následující akce:

* Instalace požadovaných balíčků
* Připojení k pracovnímu prostoru, aby vaše instance výpočetních prostředků mohla komunikovat se vzdálenými prostředky
* Vytvoření experimentu pro sledování vašich běhů
* Vytvoření vzdáleného výpočetního cíle, který se použije pro školení

### <a name="install-required-packages"></a>Instalace požadovaných balíčků
Tento kurz předpokládá, že už máte nainstalovanou sadu Azure ML SDK. Pokračujte a importujte balíček **azuremlsdk.**

```R
library(azuremlsdk)
```

Trénovací a`accidents.R` bodovací skripty ( a `accident_predict.R`) mají některé další závislosti. Pokud máte v plánu na spuštění těchto skriptů místně, ujistěte se, že máte tyto požadované balíčky také.

### <a name="load-your-workspace"></a>Načtení pracovního prostoru
Vytvořte instanci objektu pracovního prostoru z existujícího pracovního prostoru. Následující kód načte podrobnosti pracovního prostoru ze souboru **config.json.** Pracovní prostor můžete také [`get_workspace()`](https://azure.github.io/azureml-sdk-for-r/reference/get_workspace.html)načíst pomocí aplikace .

```R
ws <- load_workspace_from_config()
```

### <a name="create-an-experiment"></a>Vytvoření experimentu
Experiment Azure ML sleduje seskupení spuštění, obvykle ze stejného školicího skriptu. Vytvořte experiment pro sledování běhů pro trénování modelu stříšky na datech o nehodách.

```R
experiment_name <- "accident-logreg"
exp <- experiment(ws, experiment_name)
```

### <a name="create-a-compute-target"></a>Vytvoření výpočetního cíle
Pomocí Azure Machine Learning Compute (AmlCompute), spravované služby, mohou datoví vědci trénovat modely strojového učení v clusterech virtuálních počítačů Azure. Mezi příklady patří virtuální zařízení s podporou GPU. V tomto kurzu vytvoříte cluster AmlCompute s jedním uzlem jako tréninkové prostředí. Níže uvedený kód vytvoří výpočetní cluster pro vás, pokud ještě neexistuje ve vašem pracovním prostoru.

Možná budete muset počkat několik minut pro výpočetní cluster, který má být zřízena, pokud ještě neexistuje.

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
Tento výukový program používá data z amerického [Národního úřadu pro bezpečnost silničního provozu](https://cdan.nhtsa.gov/tsftables/tsfar.htm) (díky [Mary C. Meyer a Tremika Finney](https://www.stat.colostate.edu/~meyer/airbags.htm)).
Tato datová sada obsahuje data z více než 25 000 dopravních nehod v USA s proměnnými, které můžete použít k předvídání pravděpodobnosti úmrtí. Nejprve importujte data do R a transformujte je do nového datového rámce `accidents` pro analýzu a exportujte je do souboru. `Rdata`

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

### <a name="upload-data-to-the-datastore"></a>Nahrání dat do úložiště dat
Nahrajte data do cloudu, aby k nim mělo přístup vaše vzdálené tréninkové prostředí. Každý pracovní prostor Azure Machine Learning je dodáván s výchozím úložištěm dat, které ukládá informace o připojení do kontejneru objektů blob Azure, který se zřává v účtu úložiště připojeném k pracovnímu prostoru. Následující kód nahraje údaje o nehodách, které jste vytvořili výše, do tohoto úložiště dat.

```R
ds <- get_default_datastore(ws)

target_path <- "accidentdata"
upload_files_to_datastore(ds,
                          list("./accidents.Rd"),
                          target_path = target_path,
                          overwrite = TRUE)
```


## <a name="train-a-model"></a>Učení modelu

V tomto kurzu vejde logistické regresní model na nahraná data pomocí vzdáleného výpočetního clusteru. Chcete-li odeslat úlohu, musíte:

* Příprava školicího skriptu
* Vytvoření estimátoru
* Odeslání úlohy

### <a name="prepare-the-training-script"></a>Příprava školicího skriptu
Ve stejném `accidents.R` adresáři jako v tomto kurzu byl zadán školicí skript s názvem. Všimněte si následující ch podrobností **uvnitř školicího skriptu,** které byly provedeny k využití Azure Machine Learning pro školení:

* Trénovací skript `-d` trvá argument najít adresář, který obsahuje trénovací data. Když definujete a odešlete úlohu později, přejdete na úložiště dat pro tento argument. Azure ML připojí složku úložiště do vzdáleného clusteru pro úlohu školení.
* Trénovací skript zaznamenává konečnou přesnost jako metriku pro `log_metric_to_run()`záznam spuštění v Azure ML pomocí . Sada Azure ML SDK poskytuje sadu protokolování api pro protokolování různých metrik během spuštění školení. Tyto metriky jsou zaznamenány a trvalé v záznamu spuštění experimentu. Metriky pak lze přistupovat kdykoli nebo zobrazit na stránce podrobnosti spuštění ve [studiu](https://ml.azure.com). Viz [odkaz](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-training-experimentation) na úplnou sadu `log_*()`metod protokolování .
* Školicí skript uloží model do adresáře s názvem **výstupy**. Složka `./outputs` obdrží zvláštní zacházení azure ML. Během školení se `./outputs` soubory zapsané do služby automaticky nahrají do vašeho spuštěného záznamu službou Azure ML a zatrvají jako artefakty. Uložením trénovaného modelu do `./outputs`aplikace budete mít přístup k souboru modelu a načíst jej i po skončení spuštění a již nebudete mít přístup k prostředí vzdáleného školení.

### <a name="create-an-estimator"></a>Vytvoření estimátoru

Odhad Azure ML zapouzdřuje informace o konfiguraci spuštění potřebné pro spuštění trénovacího skriptu na výpočetní masce. Azure ML běží se spouštějí jako kontejnerizované úlohy na zadaný výpočetní cíl. Ve výchozím nastavení bude image Dockeru vytvořená pro vaši tréninkovou úlohu zahrnovat R, sadu SDK Azure ML a sadu běžně používaných balíčků R. Zde naleznete úplný seznam výchozích balíčků.

Chcete-li vytvořit odhad, definujte:

* Adresář, který obsahuje skripty potřebné`source_directory`pro školení ( ). Všechny soubory v tomto adresáři jsou odeslány do uzlů clusteru pro spuštění. Adresář musí obsahovat trénovací skript a všechny další požadované skripty.
* Školicí skript, který`entry_script`bude proveden ( ).
* Cíl výpočetních`compute_target`prostředků ( ), v tomto případě cluster AmlCompute, který jste vytvořili dříve.
* Parametry požadované z tréninkového`script_params`skriptu ( ). Azure ML spustí trénovací skript jako `Rscript`skript příkazového řádku s . V tomto kurzu zadáte jeden argument do skriptu, datový adresář `ds$path(target_path)`montážní bod, který můžete přistupovat s .
* Všechny závislosti prostředí potřebné pro školení. Výchozí bitová kopie Dockeru vytvořená`caret`pro `e1071`školení `optparse`již obsahuje tři balíčky ( , , a ) potřebné ve skriptu školení.  Takže nemusíte zadávat další informace. Pokud používáte balíčky R, které nejsou zahrnuty ve `cran_packages` výchozím nastavení, použijte parametr odhadu k přidání dalších balíčků CRAN. Úplný [`estimator()`](https://azure.github.io/azureml-sdk-for-r/reference/estimator.html) soubor konfigurovatelných možností naleznete v odkazu.

```R
est <- estimator(source_directory = ".",
                 entry_script = "accidents.R",
                 script_params = list("--data_folder" = ds$path(target_path)),
                 compute_target = compute_target
                 )
```

### <a name="submit-the-job-on-the-remote-cluster"></a>Odeslání úlohy ve vzdáleném clusteru

Nakonec odešlete úlohu, která bude spuštěna v clusteru. `submit_experiment()`vrátí objekt Run, který pak použijete k rozhraní s run. Celkově první běh trvá **asi 10 minut**. Ale pro pozdější spuštění je stejná image Dockeru znovu použita, pokud se nezmění závislosti skriptu.  V tomto případě je bitová kopie uložena do mezipaměti a doba spuštění kontejneru je mnohem rychlejší.

```R
run <- submit_experiment(exp, est)
```

Podrobnosti o spuštění si můžete prohlédnout v prohlížeči RStudio Viewer. Kliknutím na "Webové zobrazení" odkaz k dispozici vás přenese do studia Azure Machine Learning, kde můžete sledovat spuštění v uživatelském okně.

```R
view_run_details(run)
```

Model školení se děje v pozadí. Počkejte, až model dokončil školení před spuštěním další kód.

```R
wait_for_run_completion(run, show_output = TRUE)
```

Vy – a kolegové s přístupem k pracovnímu prostoru – můžete odeslat více experimentů paralelně a Azure ML převezme plánování úloh na výpočetním clusteru. Můžete dokonce nakonfigurovat cluster tak, aby automaticky škálovat až na více uzlů a škálování zpět, když nejsou žádné další výpočetní úlohy ve frontě. Tato konfigurace je nákladově efektivní způsob, jak týmy sdílet výpočetní prostředky.

## <a name="retrieve-training-results"></a>Načtení výsledků školení
Po dokončení školení modelu můžete získat přístup k artefaktům vaší úlohy, které byly zachovány do záznamu spuštění, včetně všech protokolovaných metrik a konečného trénovaného modelu.

### <a name="get-the-logged-metrics"></a>Získání protokolovaných metrik
V trénovacím skriptu `accidents.R`jste zaznamenali metriku z modelu: přesnost předpovědi v trénovacích datech. Metriky můžete zobrazit ve [studiu](https://ml.azure.com)nebo je extrahovat do místní relace jako seznam R následujícím způsobem:

```R
metrics <- get_run_metrics(run)
metrics
```

Pokud jste spouštěli více experimentů (například pomocí různých proměnných, algoritmů nebo hyperparamerů), můžete pomocí metrik z každého spuštění porovnat a vybrat model, který budete používat v produkčním prostředí.

### <a name="get-the-trained-model"></a>Získejte trénovaný model
Můžete načíst trénovaný model a podívat se na výsledky v místní relaci R. Následující kód stáhne obsah `./outputs` adresáře, který obsahuje soubor modelu.

```R
download_files_from_run(run, prefix="outputs/")
accident_model <- readRDS("outputs/model.rds")
summary(accident_model)
```

Vidíte některé faktory, které přispívají ke zvýšení odhadované pravděpodobnosti úmrtí:

* vyšší rychlost nárazu 
* muž řidič
* starší cestující
* Osobní

Nižší pravděpodobnost úmrtí se zobrazí s:

* přítomnost airbagů
* přítomnost bezpečnostních pásů
* čelní kolize 

Rok výroby vozidla nemá významný vliv.

Tento model můžete použít k nové předpovědi:

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

S vaším modelem můžete předpovědět nebezpečí smrti kolizí. Azure ML použijte k nasazení modelu jako služby předpovědi. V tomto kurzu nasadíte webovou službu v [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/) (ACI).

### <a name="register-the-model"></a>Registrace modelu

Nejprve zaregistrujte model, který jste [`register_model()`](https://azure.github.io/azureml-sdk-for-r/reference/register_model.html)stáhli do pracovního prostoru, pomocí aplikace . Registrovaný model může být libovolná kolekce souborů, ale v tomto případě je dostačující objekt modelu R. Azure ML použije registrovaný model pro nasazení.

```R
model <- register_model(ws, 
                        model_path = "outputs/model.rds", 
                        model_name = "accidents_model",
                        description = "Predict probablity of auto accident")
```

### <a name="define-the-inference-dependencies"></a>Definování závislostí odvození
Chcete-li vytvořit webovou službu pro váš model,`entry_script`musíte nejprve vytvořit bodovací skript ( ), skript Jazyka R, který bude mít jako vstupní hodnoty proměnných (ve formátu JSON) a výstup předpověď z modelu. Pro účely tohoto kurzu použijte `accident_predict.R`zadaný soubor hodnocení . Bodovací skript musí `init()` obsahovat metodu, která načte model a vrátí funkci, která používá model k vytvoření předpovědi založené na vstupních datech. Další [podrobnosti](https://azure.github.io/azureml-sdk-for-r/reference/inference_config.html#details) naleznete v dokumentaci.

Dále definujte **prostředí** Azure ML pro závislosti balíčků skriptu. V prostředí zadáte balíčky R (z CRAN nebo jinde), které jsou potřebné pro spuštění skriptu. Můžete také zadat hodnoty proměnných prostředí, které skript může odkazovat na změnu jeho chování. Ve výchozím nastavení Azure ML vytvoří stejnou výchozí bitovou kopii Dockeru, která se používá s odhadem pro školení. Vzhledem k tomu, že kurz nemá žádné zvláštní požadavky, vytvořte prostředí bez zvláštních atributů.

```R
r_env <- r_environment(name = "basic_env")
```

Pokud chcete místo toho použít vlastní image Dockeru `custom_docker_image` pro nasazení, zadejte parametr. Viz [`r_environment()`](https://azure.github.io/azureml-sdk-for-r/reference/r_environment.html) odkaz na úplnou sadu konfigurovatelných možností pro definování prostředí.

Nyní máte vše, co potřebujete k vytvoření konference **odvození** pro zapouzdření vašeho bodovacího skriptu a závislostí prostředí.

```R
inference_config <- inference_config(
  entry_script = "accident_predict.R",
  environment = r_env)
```

### <a name="deploy-to-aci"></a>Nasazení do ACI
V tomto kurzu nasadíte službu aci. Tento kód zřizován jeden kontejner reagovat na příchozí požadavky, který je vhodný pro testování a lehké zatížení. Další [`aci_webservice_deployment_config()`](https://azure.github.io/azureml-sdk-for-r/reference/aci_webservice_deployment_config.html) konfigurovatelné možnosti naleznete. (Pro nasazení v produkčním prostředí můžete také [nasadit do služby Azure Kubernetes Service](https://azure.github.io/azureml-sdk-for-r/articles/deploy-to-aks/deploy-to-aks.html).)

``` R
aci_config <- aci_webservice_deployment_config(cpu_cores = 1, memory_gb = 0.5)
```

Nyní nasadíte model jako webovou službu. Nasazení **může trvat několik minut**. 

```R
aci_service <- deploy_model(ws, 
                            'accident-pred', 
                            list(model), 
                            inference_config, 
                            aci_config)

wait_for_deployment(aci_service, show_output = TRUE)
```

## <a name="test-the-deployed-service"></a>Testování nasazené služby

Nyní, když je váš model nasazen jako služba, můžete [`invoke_webservice()`](https://azure.github.io/azureml-sdk-for-r/reference/invoke_webservice.html)otestovat službu z R pomocí .  Zadejte novou sadu dat předpovědět z, převést na JSON a odeslat do služby.

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

Můžete také získat koncový bod HTTP webové služby, který přijímá volání klienta REST. Tento koncový bod můžete sdílet s kýmkoli, kdo chce otestovat webovou službu nebo ji integrovat do aplikace.

```R
aci_service$scoring_uri
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte prostředky, jakmile je již nepotřebujete. Neodstraňujte žádný prostředek, který chcete stále používat. 

Odstranit webovou službu:
```R
delete_webservice(aci_service)
```

Odstranit registrovaný model:
```R
delete_model(model)
```

Odstranění výpočetního clusteru:
```R
delete_compute(compute)
```

### <a name="delete-everything"></a>Odstranit vše

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Skupinu prostředků můžete také zachovat, ale odstranit jeden pracovní prostor. Zobrazte vlastnosti pracovního prostoru a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

* Teď, když jste dokončili svůj první experiment Azure Machine Learning v R, další informace o [Azure Machine Learning SDK pro R](https://azure.github.io/azureml-sdk-for-r/index.html).

* Další informace o Azure Machine Learning s R na příklady v jiných *složek viněty.*
