---
title: Známé problémy & řešení potíží
titleSuffix: Azure Machine Learning
description: Seznam známých problémů, řešení a řešení potíží pro Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2522b31788df294c37db4326985edd6c85774561
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191839"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Známé problémy a řešení potíží Azure Machine Learning

Tento článek vám pomůže najít a opravit chyby nebo chyby, ke kterým došlo při použití Azure Machine Learning.

## <a name="sdk-installation-issues"></a>Problémy při instalaci sady SDK

**Chybová zpráva: Nejde odinstalovat ' PyYAML '.**

Azure Machine Learning SDK pro Python: PyYAML je projekt distutils nainstalované. Proto nemůžeme přesně určit, které soubory do ní patří, pokud dojde k částečné odinstalaci. Pokud chcete pokračovat v instalaci sady SDK při tato chyba se ignoruje, použijte:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**Chybová zpráva: `ERROR: No matching distribution found for azureml-dataprep-native`**

Distribuce Anaconda Python 3.7.4 obsahuje chybu, která přerušuje instalaci aplikace AzureML-SDK. Tento problém je popsaný v tomto [problému GitHubu](https://github.com/ContinuumIO/anaconda-issues/issues/11195) . to se dá vyřešit vytvořením nového prostředí conda pomocí tohoto příkazu:
```bash
conda create -n <env-name> python=3.7.3
```
Díky tomu vytvoří prostředí conda s využitím Pythonu 3.7.3, ve kterém není problém instalace přítomen v 3.7.4.

## <a name="training-and-experimentation-issues"></a>Problémy s školením a experimentováním

### <a name="metric-document-is-too-large"></a>Dokument metriky je moc velký.
Azure Machine Learning má interní omezení velikosti objektů metriky, které je možné v rámci školicích běhů současně přihlásit. Pokud narazíte na chybu "dokument metriky je moc velký" při protokolování metriky s hodnotou seznamu, zkuste seznam rozdělit na menší bloky dat, například:

```python
run.log_list("my metric name", my_metric[:N])
run.log_list("my metric name", my_metric[N:])
```

Azure ML interně zřetězí bloky se stejným názvem metriky do souvislého seznamu.

### <a name="moduleerrors-no-module-named"></a>ModuleErrors (žádný modul s názvem)
Pokud při odesílání experimentů v Azure ML pracujete v ModuleErrors, znamená to, že skript školení očekává instalaci balíčku, ale nepřidá se. Až zadáte název balíčku, Azure ML nainstaluje balíček do prostředí, které se používá pro váš školicí běh. 

Pokud používáte [odhady](concept-azure-machine-learning-architecture.md#estimators) k odesílání experimentů, můžete zadat název balíčku pomocí `pip_packages` nebo `conda_packages` parametr v Estimator na základě toho, ze kterého zdroje chcete balíček nainstalovat. Můžete také zadat soubor YML se všemi vašimi závislostmi pomocí `conda_dependencies_file`nebo vypsat všechny požadavky PIP v souboru txt pomocí parametru `pip_requirements_file`. Pokud máte vlastní objekt prostředí Azure ML a chcete přepsat výchozí image, kterou používá Estimator, můžete toto prostředí zadat pomocí parametru `environment` konstruktoru Estimator.

Azure ML také poskytuje odhady specificky pro rozhraní pro Tensorflow, PyTorch, chainer a skriptu sklearn. Pomocí těchto odhady se zajistí, že se závislosti Core Frameworku nainstalují vaším jménem do prostředí používaného pro školení. Máte možnost zadat další závislosti, jak je popsáno výše. 
 
Azure ML zachovává image Docker a jejich obsah se může zobrazit v [kontejnerech AzureML](https://github.com/Azure/AzureML-Containers).
Závislosti specifické pro rozhraní jsou uvedeny v dokumentaci k příslušnému rozhraní – [chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [skriptu sklearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

> [!Note]
> Pokud si myslíte, že konkrétní balíček je dostatečně společný, aby ho bylo možné přidat do spravovaných imagí a prostředí Azure ML, vyřešte v [kontejnerech AzureML](https://github.com/Azure/AzureML-Containers)problém GitHubu. 
 
### <a name="nameerror-name-not-defined-attributeerror-object-has-no-attribute"></a>NameError (název není definován), AttributeError (objekt nemá žádný atribut)
Tato výjimka by se měla nacházet z vašich školicích skriptů. Můžete si prohlédnout soubory protokolu z Azure Portal a získat další informace o konkrétním názvu, který není definován nebo chyba atributu. V sadě SDK můžete použít `run.get_details()` k zobrazení chybové zprávy. Zobrazí se také seznam všech souborů protokolu generovaných pro váš běh. Ujistěte se prosím, že se podíváte na školicí skript a opravte chybu před opětovným odesláním běhu. 

### <a name="horovod-has-been-shut-down"></a>Horovod se ukončil.
Ve většině případů, pokud narazíte na "AbortedError: Horovod byl vypnut" Tato výjimka znamená, že došlo k základní výjimce v jednom z procesů, které způsobily vypnutí Horovod. Každé pořadí v úloze MPI získá vlastní vyhrazený soubor protokolu v Azure ML. Tyto protokoly jsou pojmenovány `70_driver_logs`. V případě distribuovaného školení jsou názvy protokolů `_rank` s příponou, aby bylo snazší odlišit protokoly. Pokud chcete najít přesnou chybu, která způsobila vypnutí Horovod, Projděte všechny soubory protokolů a hledejte `Traceback` na konci driver_log souborů. Jeden z těchto souborů vám poskytne vlastní podkladovou výjimku. 

### <a name="sr-iov-availability-on-ncv3-machines-in-amlcompute-for-distributed-training"></a>Dostupnost rozhraní SR-IOV na počítačích s NCv3 v AmlCompute pro distribuované školení
Azure COMPUTE vyvolal [upgrade SR-IOV](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku/) počítačů s NCv3, kteří můžou využívat Azure ml Managed COMPUTE (AmlCompute). Aktualizace umožní podporu celého MPI zásobníku a používání sítě InfiniBand RDMA pro lepší výkon distribuovaného školení ve více uzlech, zejména pro obsáhlý Learning.

Podívejte se na [Plán aktualizací](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku/) , kde zjistíte, kdy bude podpora pro vaši oblast zavedená.

### <a name="run-or-experiment-deletion"></a>Spuštění nebo experimentování při odstraňování
Experimenty je možné archivovat pomocí metody [experiment. Archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) nebo na kartě experiment v nástroji Azure Machine Learning Studio Client prostřednictvím tlačítka "archivní experiment". Tato akce skryje experiment ze seznamu dotazy a zobrazení, ale neodstraní ho.

Trvalé odstranění individuálních experimentů nebo spuštění není aktuálně podporováno. Další informace o odstraňování prostředků pracovního prostoru najdete v tématu [Export nebo odstranění dat pracovního prostoru služby Machine Learning](how-to-export-delete-data.md).

## <a name="azure-machine-learning-compute-issues"></a>Azure Machine Learning výpočetní problémy
Známé problémy s používáním Azure Machine Learning COMPUTE (AmlCompute).

### <a name="trouble-creating-amlcompute"></a>Problémy při vytváření AmlCompute

Je pravděpodobné, že někteří uživatelé, kteří vytvořili svůj Azure Machine Learning pracovní prostor z Azure Portal před vydáním GA, nemusí být schopni vytvořit AmlCompute v tomto pracovním prostoru. Můžete buď vyvolat žádost o podporu na službu, nebo vytvořit nový pracovní prostor prostřednictvím portálu nebo sadu SDK pro okamžité odblokování.

### <a name="outage-sr-iov-upgrade-to-ncv3-machines-in-amlcompute"></a>Výpadek: upgrade SR-IOV na počítače NCv3 v AmlCompute

Azure COMPUTE bude aktualizovat skladové položky NCv3 počínaje začátkem listopadu 2019, aby podporovaly všechny MPI implementace a verze, a operace RDMA pro virtuální počítače s podporou InfiniBand. To bude vyžadovat krátké výpadky. [Další informace o upgradu SR-IOV najdete v tématu](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku).

Jako zákazník z nabídky spravované COMPUTE (AmlCompute) Azure Machine Learning nemusíte v tuto chvíli provádět žádné změny. Na základě [plánu aktualizací](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku) byste museli naplánovat krátké přerušení školení. Služba bude mít za úkol, aby na uzlech clusteru aktualizovala image virtuálních počítačů a automaticky nastavila horizontální navýšení kapacity clusteru. Po dokončení upgradu může být možné použít všechny ostatní MPI distribuce (například OpenMP s Pytorch), kromě toho, že se zvyšuje větší šířka pásma InfiniBand, nižší latence a lepší výkon distribuovaných aplikací.

## <a name="azure-machine-learning-designer-issues"></a>Problémy s návrhářem Azure Machine Learning

Známé problémy s návrhářem.

### <a name="long-compute-preparation-time"></a>Čas přípravy na dlouhou výpočetní výkon

Vytváření nových výpočetních prostředků nebo EVOKE, které opouští výpočetní výkon, může trvat několik minut nebo i delší dobu. Tým pracuje na optimalizaci.


### <a name="cannot-run-an-experiment-only-contains-a-dataset"></a>Nelze spustit experiment pouze obsahující datovou sadu. 

Možná budete chtít spustit experiment pouze s datovou sadou, která bude vizualizovat datovou sadu. Není však povoleno spouštět experiment pouze v dnešní době. Aktivně opravujeme tento problém.
 
Před opravou můžete datovou sadu připojit k jakémukoli modulu transformace dat (výběr sloupců v datové sadě, upravit metadata, rozdělit data atd.) a spustit experiment. Pak můžete vizualizovat datovou sadu. 

Následující obrázek ukazuje, jak: ![visulize-data](./media/resource-known-issues/aml-visualize-data.png)

## <a name="image-building-failure"></a>Chyba vytváření bitové kopie

Obrázek po nasazení webové služby vytvářet selhání. Alternativním řešením je přidat "pynacl == 1.2.1" jako pip závislosti systému Conda v souboru konfigurace image.

## <a name="deployment-failure"></a>Selhání nasazení

Pokud zjistíte `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`, změňte SKU pro virtuální počítače používané ve vašem nasazení na jednu, která má více paměti.

## <a name="fpgas"></a>FPGA

Nebude moct nasazovat modely na FPGA, dokud si vyžádáte a byla schválena pro FPGA kvótu. Chcete-li požádat o přístup, vyplňte formulář žádosti o kvótu: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Automatizované strojové učení

Služba tensor Flow automatizovaného strojového učení v současné době nepodporuje tensor Flow verze 1,13. Instalace této verze způsobí, že se závislosti balíčku přestanou pracovat. Pracujeme na řešení tohoto problému v budoucí verzi. 

### <a name="experiment-charts"></a>Grafy experimentů

Binární klasifikační grafy (přesnost-odvolání, ROC, křivka získání atd.) zobrazené v automatizovaných iteracích experimentu se v uživatelském rozhraní nevykreslují správně, od 4/12. V grafu jsou v současné době zobrazeny inverzní výsledky, kde je lepší provádět modely s nižšími výsledky. Řešení je v šetření.

## <a name="datasets-and-data-preparation"></a>Datové sady a Příprava dat

Jedná se o známé problémy pro Azure Machine Learning datové sady.

### <a name="typeerror-filenotfound-no-such-file-or-directory"></a>TypeError: FileNotFound: žádný takový soubor nebo adresář

K této chybě dochází v případě, že zadáte cestu k souboru, kde se nachází. Je nutné zajistit, aby se způsob, jakým na soubor odkazujete, shodoval s tím, kam jste připojili datovou sadu na výpočetním cíli. Pro zajištění deterministického stavu doporučujeme při připojování datové sady k cíli výpočtů použít abstraktní cestu. Například v následujícím kódu připojíme datovou sadu do kořenového adresáře systému souborů cíle výpočtů `/tmp`. 

```python
# Note the leading / in '/tmp/dataset'
script_params = {
    '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
} 
```

Pokud nezadáte úvodní lomítko, "/", budete muset zadat předponu pracovního adresáře, např. `/mnt/batch/.../tmp/dataset` na výpočetním cíli, abyste označili, kam chcete datovou sadu připojit. 

### <a name="fail-to-read-parquet-file-from-http-or-adls-gen-2"></a>Nepodařilo se přečíst soubor Parquet z HTTP nebo ADLS Gen 2.

Došlo k známému problému v sadě 1.1.25 sady SDK pro AzureML, který způsobuje selhání při vytváření datové sady, a to čtením souborů Parquet z protokolu HTTP nebo ADLS Gen 2. `Cannot seek once reading started.`se nezdaří. Pokud chcete tento problém vyřešit, upgradujte prosím `azureml-dataprep` na verzi vyšší než 1.1.26, nebo downgradujte na verzi nižší než 1.1.24.

```python
pip install --upgrade azureml-dataprep
```

### <a name="typeerror-mount-got-an-unexpected-keyword-argument-invocation_id"></a>TypeError: příkaz Mount () získal neočekávaný argument klíčové slovo invocation_id.

K této chybě dochází, pokud máte nekompatibilní verzi mezi `azureml-core` a `azureml-dataprep`. Pokud se zobrazí tato chyba, upgradujte balíček `azureml-dataprep` na novější verzi (větší než nebo se rovná 1.1.29).

```python
pip install --upgrade azureml-dataprep
```

## <a name="databricks"></a>Databricks

Problémy s Databricks a Azure Machine Learning.

### <a name="failure-when-installing-packages"></a>Chyba při instalaci balíčků

Instalace sady Azure Machine Learning SDK se v Azure Databricks při instalaci dalších balíčků nezdařila. Některé balíčky, například `psutil`, můžou způsobit konflikty. Aby nedocházelo k chybám při instalaci, nainstalujte balíčky zmrazením verze knihovny. Tento problém se vztahuje k datacihlům a nikoli k sadě Azure Machine Learning SDK. Tento problém se může vyskytnout i u jiných knihoven. Příklad:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Případně můžete použít skripty init, pokud máte potíže s instalací v knihovně Python. Tento přístup není oficiálně podporován. Další informace najdete v tématu [skripty init v oboru clusteru](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Zrušení automatizovaného spuštění strojového učení

Když v Azure Databricks používáte automatizované funkce machine learningu, zrušíte spuštění a spustíte nový experiment, restartujete cluster Azure Databricks.

### <a name="10-iterations-for-automated-machine-learning"></a>> 10 iterací pro automatizované strojové učení

V případě automatizovaného nastavení strojového učení, pokud máte více než 10 iterací, nastavte `show_output` na `False` při odeslání běhu.

### <a name="widget-for-the-azure-machine-learning-sdk-and-automated-machine-learning"></a>Widget pro sadu Azure Machine Learning SDK a automatizované strojové učení

Pomůcka Azure Machine Learning SDK není v poznámkovém bloku datacihly podporovaná, protože poznámkové bloky nemůžou analyzovat widgety HTML. Widget můžete zobrazit na portálu pomocí tohoto kódu Pythonu v buňce s Azure Databricksm poznámkového bloku:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-cannot-import-name-timedelta-from-pandas_libstslibs"></a>Chyba importu: název ' Timedelta ' nelze importovat z ' PANDAS. _libs. tslibs '

Pokud se vám tato chyba zobrazí při použití automatizovaného strojového učení, spusťte na poznámkovém bloku tyto dva řádky:
```
%sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
%sh /databricks/python/bin/pip install pandas==0.23.4
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Chyba importu: žádný modul s názvem PANDAS. Core. Indexes

Pokud se tato chyba zobrazí při použití automatizovaného strojového učení:

1. Spuštěním tohoto příkazu nainstalujete do clusteru Azure Databricks dva balíčky: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Odpojte a znovu připojte cluster ke svému poznámkovému bloku. 

Pokud tyto kroky problém nevyřeší, zkuste restartovat cluster.

### <a name="failtosendfeather"></a>FailToSendFeather

Pokud se při čtení dat v clusteru Azure Databricks zobrazí chyba `FailToSendFeather`, přečtěte si následující řešení:

* Upgradovat balíček `azureml-sdk[automl]` na nejnovější verzi.
* Přidejte `azureml-dataprep` verze 1.1.8 nebo novější.
* Přidejte `pyarrow` verze 0,11 nebo vyšší.

## <a name="azure-portal"></a>Azure Portal

Pokud přejdete přímo na váš pracovní prostor z sdílet odkaz ze sady SDK nebo na portálu zobrazit, nebudete moct zobrazit stránka s přehledem normální s informace o předplatném v rozšíření. Nebudete také moci přepnout do jiného pracovního prostoru. Pokud potřebujete zobrazit jiný pracovní prostor, alternativní řešení je přejít přímo na [Azure Machine Learning Studio](https://ml.azure.com) a vyhledat název pracovního prostoru.

## <a name="diagnostic-logs"></a>Diagnostické protokoly

V některých případech může být užitečné, pokud může poskytnout diagnostické informace, pokud s žádostí o pomoc. Pokud chcete zobrazit některé protokoly, přejděte na web [Azure Machine Learning Studio](https://ml.azure.com) , přejděte do svého pracovního prostoru a vyberte **pracovní prostor > experiment > Spustit protokoly >** .  

> [!NOTE]
> Azure Machine Learning v průběhu školení protokolovat informace z nejrůznějších zdrojů, jako je například AutoML nebo kontejner Docker, který spouští školicí úlohu. Mnohé z těchto protokolů nejsou dokumentovány. Pokud narazíte na problémy a kontaktujte podporu Microsoftu, můžou při řešení potíží používat tyto protokoly.

## <a name="activity-logs"></a>Protokoly aktivit

Některé akce v pracovním prostoru Azure Machine Learning neprotokolují informace do __protokolu aktivit__. Například spuštění školení nebo registrace modelu.

Některé z těchto akcí se zobrazí v oblasti __aktivity__ pracovního prostoru, ale nenaznačují, kdo aktivitu inicioval.

## <a name="resource-quotas"></a>Kvóty prostředků

Přečtěte si o [kvótách prostředků](how-to-manage-quotas.md) , se kterými se můžete setkat při práci s Azure Machine Learning.

## <a name="authentication-errors"></a>Chyby ověřování

Pokud provádíte operaci správy na výpočetním cíli ze vzdálené úlohy, zobrazí se jedna z následujících chyb:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Například se zobrazí chyba, pokud se pokusíte vytvořit nebo připojit výpočetní cíl z kanálu ML, který je odeslán pro vzdálené spuštění.

## <a name="overloaded-azurefile-storage"></a>Přetížené úložiště AzureFile

Pokud se zobrazí chybová `Unable to upload project files to working directory in AzureFile because the storage is overloaded`, použijte následující alternativní řešení.

Pokud používáte sdílenou složku pro jiné úlohy, jako je třeba přenos dat, doporučuje se použít objekty blob, aby bylo možné používat pro odeslání spuštění sdílení souborů. Úlohy můžete rozdělit také mezi dva různé pracovní prostory.

## <a name="webservices-in-azure-kubernetes-service-failures"></a>Služby WebServices ve službě Azure Kubernetes – chyby 

Mnoho selhání webové služby ve službě Azure Kubernetes se dá ladit tak, že se připojí ke clusteru pomocí `kubectl`. `kubeconfig.json` pro cluster služby Azure Kubernetes můžete získat spuštěním

```bash
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Aktualizace komponent Azure Machine Learning v clusteru AKS

Aktualizace komponent Azure Machine Learning nainstalovaných v clusteru služby Azure Kubernetes se musí použít ručně. 

Tyto aktualizace můžete použít tak, že cluster odpojíte z pracovního prostoru Azure Machine Learning a pak cluster znovu připojíte k pracovnímu prostoru. Pokud je v clusteru povolený protokol SSL, budete muset při opětovném připojení clusteru dodat certifikát SSL a privátní klíč. 

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

Pokud už certifikát SSL a soukromý klíč nepoužíváte nebo certifikát vygenerovaný Azure Machine Learning, můžete načíst soubory před odpojením clusteru, a to tak, že se připojíte ke clusteru pomocí `kubectl` a načtete tajný `azuremlfessl`.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes ukládá tajné klíče ve formátu kódování Base-64. Před tím, než jim poskytnete `attach_config.enable_ssl`, budete muset 64 základní `cert.pem` dekódovat a `key.pem` komponenty tajných kódů. 

## <a name="labeling-projects-issues"></a>Problémy s označováním projektů

Známé problémy s označováním projektů.

### <a name="only-datasets-created-on-blob-datastores-can-be-used"></a>Můžou se použít jenom datové sady vytvořené pro úložiště dat objektu BLOB.

Toto je známé omezení aktuální verze. 

### <a name="after-creation-the-project-shows-initializing-for-a-long-time"></a>Po vytvoření se v projektu zobrazí "inicializace" po dlouhou dobu.

Aktualizujte stránku ručně. Inicializace by měla pokračovat zhruba o 20 databody za sekundu. Nedostatečná možnost AutoRefresh je známý problém. 

### <a name="when-reviewing-images-newly-labeled-images-are-not-shown"></a>Při kontrole imagí se nezobrazují nově označené obrázky.

Chcete-li načíst všechny označené obrázky, klikněte na tlačítko **první** . **První** tlačítko se vrátí zpět na začátek seznamu, ale načte všechna označená data.

### <a name="pressing-esc-key-while-labeling-for-object-detection-creates-a-zero-size-label-on-the-top-left-corner-submitting-labels-in-this-state-fails"></a>Když stisknete klávesu ESC, zatímco při rozpoznávání objektu se vytvoří popisek s nulovou velikostí v levém horním rohu. Odesílání popisků v tomto stavu se nezdařilo.

Odstraňte popisek kliknutím na křížek vedle něj.

## <a name="moving-the-workspace"></a>Přesun pracovního prostoru

> [!WARNING]
> Přesunutím pracovního prostoru Azure Machine Learning do jiného předplatného nebo přesunutím vlastnícího předplatného na nového tenanta se nepodporuje. V takovém případě může dojít k chybám.
