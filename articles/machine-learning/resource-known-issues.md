---
title: Známé problémy & řešení potíží
titleSuffix: Azure Machine Learning
description: Získejte seznam známých problémů, řešení a řešení potíží pro Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d5525c02edb30eff0ee8971a382f2acb8f2e57ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455719"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Známé problémy a řešení potíží s Azure Machine Learning

Tento článek vám pomůže najít a opravit chyby nebo chyby při používání Azure Machine Learning.

## <a name="sdk-installation-issues"></a>Problémy s instalací sady SDK

**Chybová zpráva: Nelze odinstalovat "PyYAML"**

Azure Machine Learning SDK pro Python: PyYAML je projekt nainstalovaný distutils. Proto nemůžeme přesně určit, které soubory patří k němu, pokud je částečná odinstalace. Chcete-li pokračovat v instalaci sady SDK při ignorování této chyby, použijte:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**Chybová zpráva:`ERROR: No matching distribution found for azureml-dataprep-native`**

Distribuce Pythonu 3.7.4 aplikace Anaconda má chybu, která přeruší instalaci azureml-sdk. Tento problém je popsán v tomto [problému GitHub](https://github.com/ContinuumIO/anaconda-issues/issues/11195) To lze obejít vytvořením nového prostředí Conda pomocí tohoto příkazu:
```bash
conda create -n <env-name> python=3.7.3
```
Který vytvoří prostředí Conda pomocí Pythonu 3.7.3, který nemá problém s instalací v 3.7.4.

## <a name="training-and-experimentation-issues"></a>Problémy s školením a experimentací

### <a name="metric-document-is-too-large"></a>Dokument Metrika je příliš velký.
Azure Machine Learning má vnitřní omezení velikosti objektů metriky, které lze protokolovat najednou z trénovacího běhu. Pokud při protokolování metriky s hodnotou seznamu narazíte na chybu "Metrika je příliš velký", zkuste seznam rozdělit na menší bloky, například:

```python
run.log_list("my metric name", my_metric[:N])
run.log_list("my metric name", my_metric[N:])
```

Interně Azure ML zřetězí bloky se stejným názvem metriky do souvislého seznamu.

### <a name="moduleerrors-no-module-named"></a>ModuleErrors (bez pojmenování modulu)
Pokud běžíte do ModuleErrors při odesílání experimentů v Azure ML, znamená to, že školicí skript očekává balíček k instalaci, ale není přidán. Jakmile zadáte název balíčku, Azure ML nainstaluje balíček v prostředí používaném pro vaše školení. 

Pokud používáte Odhady k odesílání [experimentů,](concept-azure-machine-learning-architecture.md#estimators) můžete `pip_packages` zadat `conda_packages` název balíčku prostřednictvím nebo parametr v odhadu na základě zdroje, ze kterého zdroje chcete balíček nainstalovat. Můžete také zadat soubor yml se všemi vašimi závislostmi pomocí `conda_dependencies_file`nebo `pip_requirements_file` vypsat všechny vaše pip požadavky v souboru txt pomocí parametru. Pokud máte vlastní objekt prostředí Azure ML, který chcete přepsat výchozí bitovou kopii používanou `environment` odhadce, můžete určit, že prostředí prostřednictvím parametru konstruktoru odhadu.

Azure ML také poskytuje odhady specifické pro architekturu pro Tensorflow, PyTorch, Chainer a SKLearn. Pomocí těchto odhadů se ujistěte, že základní framework závislosti jsou nainstalovány vaším jménem v prostředí používaném pro školení. Máte možnost zadat další závislosti, jak je popsáno výše. 
 
Azure ML udržovány docker image a jejich obsah lze zobrazit v [kontejnerech AzureML](https://github.com/Azure/AzureML-Containers).
Závislosti specifické pro architekturu jsou uvedeny v příslušné rámcové dokumentaci - [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

> [!Note]
> Pokud si myslíte, že konkrétní balíček je natolik běžný, že se dá přidat do azure ml udržovaných bitových kopií a prostředí, vyvolávejte problém GitHubu v [kontejnerech AzureML](https://github.com/Azure/AzureML-Containers). 
 
### <a name="nameerror-name-not-defined-attributeerror-object-has-no-attribute"></a>NameError (Název není definován), AttributeError (Objekt nemá žádný atribut)
Tato výjimka by měla pocházet z vašich školicích skriptů. Můžete se podívat na soubory protokolu z webu Azure Portal získat další informace o konkrétní název není definován nebo chyba atribut. Ze sady SDK můžete `run.get_details()` použít k zobrazení chybové zprávy. To bude také seznam všech souborů protokolu generovaných pro váš běh. Prosím, ujistěte se, že se podívat na váš školicí skript a opravit chybu před resubmitting vašeho běhu. 

### <a name="horovod-has-been-shut-down"></a>Horovod byl uzavřen
Ve většině případů, pokud narazíte na "AbortedError: Horovod byl vypnut" tato výjimka znamená, že v jednom z procesů, které způsobily vypnutí Horovoda, byla základní výjimka. Každé pořadí v úloze MPI získá vlastní vyhrazený soubor protokolu v Azure ML. Tyto protokoly `70_driver_logs`jsou pojmenovány . V případě distribuované školení, názvy `_rank` protokolu jsou suffix s usnadnit rozlišení protokoly. Chcete-li najít přesnou chybu, která způsobila Horovod vypnout, projít všechny soubory protokolu a hledat `Traceback` na konci driver_log souborů. Jeden z těchto souborů vám poskytne skutečnou základní výjimku. 

### <a name="sr-iov-availability-on-ncv3-machines-in-amlcompute-for-distributed-training"></a>Dostupnost SR-IOV na počítačích NCv3 v AmlCompute pro distribuované školení
Azure Compute zavádí [upgrade SR-IOV](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku/) počítačů NCv3, který můžou zákazníci využít pomocí spravované výpočetní nabídky Azure ML (AmlCompute). Aktualizace umožní podporu celého zásobníku MPI a použití sítě Infiniband RDMA pro lepší výkon distribuovaného školení s více uzlovém programem, zejména pro hluboké učení.

Zobrazte [plán aktualizací](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku/) a zjistěte, kdy bude pro vaši oblast zavedena podpora.

### <a name="run-or-experiment-deletion"></a>Spuštění nebo experimentování odstranění
Experimenty lze archivovat pomocí metody [Experiment.archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) nebo z karty Experiment v klientovi studia Azure Machine Learning pomocí tlačítka "Experiment archivovat". Tato akce skryje experiment z dotazů a zobrazení seznamu, ale neodstraní jej.

Trvalé odstranění jednotlivých experimentů nebo spuštění není v současné době podporováno. Další informace o odstranění datových zdrojů pracovního prostoru naleznete v [tématu Export nebo odstranění dat pracovního prostoru služby Machine Learning](how-to-export-delete-data.md).

## <a name="azure-machine-learning-compute-issues"></a>Problémy s výpočetními prostředky Azure Machine Learning
Známé problémy s používáním Azure Machine Learning Compute (AmlCompute).

### <a name="trouble-creating-amlcompute"></a>Potíže s vytvářením AmlCompute

Existuje vzácná pravděpodobnost, že někteří uživatelé, kteří vytvořili svůj pracovní prostor Azure Machine Learning z portálu Azure před vydáním GA, nemusí být schopni vytvořit AmlCompute v tomto pracovním prostoru. Můžete buď vyvolat žádost o podporu proti službě nebo vytvořit nový pracovní prostor prostřednictvím portálu nebo sady SDK odblokovat sami okamžitě.

### <a name="outage-sr-iov-upgrade-to-ncv3-machines-in-amlcompute"></a>Výpadek: Upgrade SR-IOV na počítače NCv3 v AmlCompute

Azure Compute bude aktualizovat ncv3 SKU od začátku listopadu 2019 pro podporu všech implementací a verzí MPI a slovesRDMA pro virtuální počítače vybavené infiniBandem. To bude vyžadovat krátké prostoje - [přečtěte si více o upgradu SR-IOV](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku).

Jako zákazník spravované výpočetní nabídky Azure Machine Learning (AmlCompute) není nutné v tuto chvíli provádět žádné změny. Na základě [plánu aktualizace](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku) budete muset naplánovat krátkou přestávku ve vašem tréninku. Služba převezme odpovědnost za aktualizaci iobrazek virtuálních počítačí v uzlech clusteru a automaticky vertikálně navýšit kapacitu clusteru. Po dokončení upgradu můžete být schopni používat všechny ostatní distribuce MPI (jako OpenMPI s Pytorch) kromě získání vyšší šířky pásma InfiniBand, nižší latence a lepší výkon distribuovaných aplikací.

## <a name="azure-machine-learning-designer-issues"></a>Problémy s návrhářem Azure Machine Learning

Známé problémy s návrhářem.

### <a name="long-compute-preparation-time"></a>Dlouhá doba přípravy výpočetních prostředků

Vytvoření nového výpočetního nebo evokovat opuštění výpočetní trvá čas, může být několik minut nebo i déle. Tým pracuje na optimalizaci.


### <a name="cannot-run-an-experiment-only-contains-a-dataset"></a>Nelze spustit experiment obsahuje pouze datovou sadu 

Můžete chtít spustit experiment obsahuje pouze datovou sadu pro vizualizaci datové sady. Není však povoleno spustit experiment obsahuje pouze datovou sadu dnes. Tento problém aktivně řešíme.
 
Před opravou můžete datovou sadu připojit k libovolnému modulu transformace dat (Vybrat sloupce v datové sadě, upravit metadata, rozdělit data atd.) a spustit experiment. Potom můžete vytvořit vizualizaci datové sady. 

Níže uvedený obrázek ukazuje, jak: ![visulize-data](./media/resource-known-issues/aml-visualize-data.png)

## <a name="image-building-failure"></a>Selhání budovy obrázku

Selhání vytváření obrázků při nasazování webové služby. Řešení je přidat "pynacl==1.2.1" jako pip závislost do souboru Conda pro konfiguraci obrazu.

## <a name="deployment-failure"></a>Selhání nasazení

Pokud budete `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`dodržovat , změňte skladovou položku pro virtuální počítače používané ve vašem nasazení na ten, který má více paměti.

## <a name="fpgas"></a>FPGA

Modely nebude možné nasadit na FPGA, dokud nepožádáte o kvótu FPGA a nebudete schváleni. Chcete-li požádat o přístup, vyplňte formulář žádosti o kvótu:https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Automatizované strojové učení

Tentenzor Flow Automatizované strojové učení v současné době nepodporuje tenzorový tok verze 1.13. Instalace této verze způsobí, že závislosti balíčků přestanou fungovat. Pracujeme na vyřešení tohoto problému v budoucí verzi.

### <a name="experiment-charts"></a>Experimentální grafy

Binární klasifikační grafy (přesnost-odvolání, ROC, gain curve atd.) zobrazené v automatizovaných iterací experimentu ML nejsou vykreslování správně v uživatelském rozhraní od 4 / 12. Obrázky grafu jsou aktuálně zobrazeny inverzní výsledky, kde jsou zobrazeny výkonnější modely s nižšími výsledky. Usnesení se vyšetřuje.

## <a name="datasets-and-data-preparation"></a>Datové sady a příprava dat

Jedná se o známé problémy pro datové sady Azure Machine Learning.

### <a name="typeerror-filenotfound-no-such-file-or-directory"></a>TypeError: FileNotFound: Žádný takový soubor nebo adresář

K této chybě dochází, pokud cesta k souboru, kterou zadáte, není tam, kde je soubor umístěn. Musíte se ujistit, že způsob, jakým odkazujete na soubor, je konzistentní s tím, kde jste namontovali datovou sadu na váš výpočetní cíl. Chcete-li zajistit deterministický stav, doporučujeme použít abstraktní cestu při připojovat datovou sadu k výpočetnímu cíli. Například v následujícím kódu připojíme datovou sadu pod kořenem souborového `/tmp`systému výpočetního cíle . 

```python
# Note the leading / in '/tmp/dataset'
script_params = {
    '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
} 
```

Pokud nezahrnete úvodní lomítko,/, budete muset předponu pracovního `/mnt/batch/.../tmp/dataset` adresáře, například na výpočetním cíli, abyste označili, kam chcete datovou sadu připojit.

### <a name="fail-to-read-parquet-file-from-http-or-adls-gen-2"></a>Nepodaří číst parketní soubor z HTTP nebo ADLS Gen 2

Ve sadě AzureML DataPrep SDK verze 1.1.25 je známý problém, který způsobuje selhání při vytváření datové sady čtením parketových souborů z protokolu HTTP nebo ADLS Gen 2. To se `Cannot seek once reading started.`nezdaří s . Chcete-li tento problém `azureml-dataprep` vyřešit, upgradujte na verzi vyšší než 1.1.26 nebo downgrade na verzi nižší než 1.1.24.

```python
pip install --upgrade azureml-dataprep
```

### <a name="typeerror-mount-got-an-unexpected-keyword-argument-invocation_id"></a>TypeError: mount() dostal neočekávaný argument klíčového slova 'invocation_id'

K této chybě dochází, pokud máte `azureml-core` `azureml-dataprep`nekompatibilní verzi mezi a . Pokud se zobrazí tato `azureml-dataprep` chyba, upgrade balíčku na novější verzi (větší než nebo rovno 1.1.29).

```python
pip install --upgrade azureml-dataprep
```

## <a name="databricks"></a>Databricks

Databricks a Azure Machine Learning problémy.

### <a name="failure-when-installing-packages"></a>Selhání při instalaci balíčků

Instalace sady Azure Machine Learning SDK se nezdaří na Azure Databricks při instalaci dalšíbalíčky. Některé balíčky, `psutil`například , mohou způsobit konflikty. Chcete-li se vyhnout chybám při instalaci, nainstalujte balíčky zmrazením verze knihovny. Tento problém souvisí s Databricks a ne s Azure Machine Learning SDK. K tomuto problému může dojít i v jiných knihovnách. Příklad:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Případně můžete použít init skripty, pokud budete mít stále čelí problémům s instalací s knihovnami Pythonu. Tento přístup není oficiálně podporován. Další informace naleznete [v tématu Init skripty s rozsahem clusteru](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Zrušení automatického spuštění strojového učení

Když používáte funkce automatického strojového učení na Azure Databricks, chcete-li zrušit spuštění a spustit nový experiment, restartujte cluster Azure Databricks.

### <a name="10-iterations-for-automated-machine-learning"></a>>10 iterací pro automatizované strojové učení

V nastavení automatického strojového učení, pokud máte `show_output` více `False` než 10 iterací, nastavte při odeslání spuštění.

### <a name="widget-for-the-azure-machine-learning-sdk-and-automated-machine-learning"></a>Widget pro Azure Machine Learning SDK a automatizované strojové učení

Widget Azure Machine Learning SDK není v poznámkovém bloku Databricks podporovaný, protože poznámkové bloky nemůžou analyzovat widgety HTML. Widget můžete zobrazit na portálu pomocí tohoto kódu Pythonu v buňce poznámkového bloku Azure Databricks:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-cannot-import-name-timedelta-from-pandas_libstslibs"></a>Chyba importu: Nelze importovat název "Timedelta" z "pandas._libs.tslibs"

Pokud se při použití automatického strojového učení zobrazí tato chyba, spusťte v poznámkovém bloku dva následující řádky:
```
%sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
%sh /databricks/python/bin/pip install pandas==0.23.4
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Chyba importu: Žádný modul s názvem "pandas.core.indexes"

Pokud se při použití automatického strojového učení zobrazí tato chyba:

1. Spusťte tento příkaz a nainstalujte dva balíčky do clusteru Azure Databricks:

   ```bash
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Odpojte a znovu připojte cluster k poznámkovému bloku.

Pokud tyto kroky problém nevyřeší, zkuste cluster restartovat.

### <a name="failtosendfeather"></a>SelháníToSendFeather

Pokud se `FailToSendFeather` při čtení dat v clusteru Azure Databricks zobrazí chyba, podívejte se na následující řešení:

* Upgrade `azureml-sdk[automl]` balíčku na nejnovější verzi.
* Přidejte `azureml-dataprep` verzi 1.1.8 nebo vyšší.
* Přidejte `pyarrow` verzi 0.11 nebo vyšší.

## <a name="azure-portal"></a>portál Azure

Pokud přejdete přímo k zobrazení pracovního prostoru z odkazu sdílení z sady SDK nebo portálu, nebudete moci zobrazit normální stránku Přehled s informacemi o předplatném v rozšíření. Také nebudete moci přepnout do jiného pracovního prostoru. Pokud potřebujete zobrazit jiný pracovní prostor, je toto řešení přejít přímo do [centra Azure Machine Learning a](https://ml.azure.com) vyhledat název pracovního prostoru.

## <a name="diagnostic-logs"></a>Diagnostické protokoly

Někdy může být užitečné, pokud můžete poskytnout diagnostické informace při žádosti o pomoc. Pokud chcete zobrazit některé protokoly, navštivte [Azure Machine Learning studio](https://ml.azure.com) a přejděte do svého pracovního prostoru a vyberte **Workspace > Experiment > Spustit > protokoly**.  

> [!NOTE]
> Azure Machine Learning zaznamenává informace z různých zdrojů během školení, jako je například AutoML nebo kontejner Dockeru, který spouští trénovací úlohu. Mnohé z těchto protokolů nejsou dokumentovány. Pokud narazíte na problémy a obraťte se na podporu společnosti Microsoft, mohou být schopni používat tyto protokoly při řešení potíží.

## <a name="activity-logs"></a>Protokoly aktivit

Některé akce v pracovním prostoru Azure Machine Learning neprotokolují informace do __protokolu aktivit__. Například spuštění trénovacího běhu nebo registrace modelu.

Některé z těchto akcí se zobrazí v oblasti __Aktivity__ v pracovním prostoru, ale neoznačují, kdo aktivitu inicioval.

## <a name="resource-quotas"></a>Kvóty prostředků

Přečtěte si o [kvótách prostředků,](how-to-manage-quotas.md) se kterými se můžete setkat při práci s Azure Machine Learning.

## <a name="authentication-errors"></a>Chyby ověřování

Pokud provedete operaci správy výpočetního cíle ze vzdálené úlohy, obdržíte jednu z následujících chyb:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Například se zobrazí chyba, pokud se pokusíte vytvořit nebo připojit cíl výpočetní ch od ML kanálu, který je odeslán ke vzdálenému spuštění.

## <a name="overloaded-azurefile-storage"></a>Přetížené úložiště AzureFile

Pokud se zobrazí `Unable to upload project files to working directory in AzureFile because the storage is overloaded`chyba , použijte následující řešení.

Pokud používáte sdílenou složku pro jiné úlohy, jako je například přenos dat, doporučujeme použít objekty BLOB, aby byla sdílená složka souboru volně použita pro odesílání spuštění. Můžete také rozdělit zatížení mezi dva různé pracovní prostory.

## <a name="webservices-in-azure-kubernetes-service-failures"></a>Webservices v Azure Kubernetes service selhání

Mnoho selhání webové služby ve službě Azure Kubernetes lze ladit `kubectl`připojením ke clusteru pomocí . Pro cluster `kubeconfig.json` služeb Azure Kubernetes můžete získat spuštěním

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Aktualizace součástí Azure Machine Learning v clusteru AKS

Aktualizace součástí Azure Machine Learning nainstalovaných v clusteru služby Azure Kubernetes musí být použity ručně. 

Tyto aktualizace můžete použít odpojením clusteru z pracovního prostoru Azure Machine Learning a opětovným připojením clusteru k pracovnímu prostoru. Pokud je v clusteru povolenprotokol SSL, budete muset při opětovném připojení clusteru zadat certifikát SSL a soukromý klíč. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Pokud již nemáte certifikát SSL a soukromý klíč nebo používáte certifikát generovaný službou Azure Machine Learning, můžete soubory načíst před `kubectl` odpojením `azuremlfessl`clusteru připojením ke clusteru pomocí a načtením tajného klíče .

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes ukládá tajemství v základním-64 kódovaném formátu. Před poskytnutím `cert.pem` tajných kódů budete `key.pem` muset dekódovat součásti `attach_config.enable_ssl`tajných kódů . 

## <a name="labeling-projects-issues"></a>Označování problémů s projekty

Známé problémy s označováním projektů.

### <a name="only-datasets-created-on-blob-datastores-can-be-used"></a>Lze použít pouze datové sady vytvořené v úložišti dat objektů blob.

Toto je známé omezení aktuální verze.

### <a name="after-creation-the-project-shows-initializing-for-a-long-time"></a>Po vytvoření se v projektu zobrazí "Inicializace" po dlouhou dobu

Ručně aktualizujte stránku. Inicializace by měla probíhat přibližně 20 datových bodů za sekundu. Nedostatek automatické aktualizace je známý problém. 

### <a name="when-reviewing-images-newly-labeled-images-are-not-shown"></a>Při kontrole obrázků se nově označené obrázky nezobrazují

Chcete-li načíst všechny označené obrázky, zvolte **tlačítko První.** První **First** tlačítko vás přenese zpět na přední stranu seznamu, ale načte všechna označená data.

### <a name="pressing-esc-key-while-labeling-for-object-detection-creates-a-zero-size-label-on-the-top-left-corner-submitting-labels-in-this-state-fails"></a>Stisknutím klávesy Esc při popisování pro detekci objektů se vytvoří popisek nulové velikosti v levém horním rohu. Odeslání popisků v tomto stavu se nezdaří.

Odstraňte štítek kliknutím na křížovou značku vedle něj.

## <a name="moving-the-workspace"></a>Přesunutí pracovního prostoru

> [!WARNING]
> Přesunutí pracovního prostoru Azure Machine Learning do jiného předplatného nebo přesunutí vlastního předplatného do nového tenanta není podporováno. To může způsobit chyby.
