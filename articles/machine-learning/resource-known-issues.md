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
ms.date: 03/31/2020
ms.openlocfilehash: 2db7a25f3f463e9210544354395c9d33a75f633c
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619373"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Známé problémy a řešení potíží s Azure Machine Learning

Tento článek vám pomůže najít a opravit chyby nebo chyby, se kterými se můžete setkat při používání Azure Machine Learning.

## <a name="diagnostic-logs"></a>Diagnostické protokoly

Někdy může být užitečné, pokud můžete poskytnout diagnostické informace při žádosti o pomoc. Chcete-li zobrazit některé protokoly: 
1. Navštivte [Azure Machine Learning studio](https://ml.azure.com). 
1. Na levé straně vyberte **Experimentovat** 
1. Vyberte experiment.
1. Vyberte spustit.
1. Nahoře vyberte **Výstupy + protokoly**.

> [!NOTE]
> Azure Machine Learning zaznamenává informace z různých zdrojů během školení, jako je například AutoML nebo kontejner Dockeru, který spouští trénovací úlohu. Mnohé z těchto protokolů nejsou dokumentovány. Pokud narazíte na problémy a obraťte se na podporu společnosti Microsoft, mohou být schopni používat tyto protokoly při řešení potíží.


## <a name="resource-quotas"></a>Kvóty prostředků

Přečtěte si o [kvótách prostředků,](how-to-manage-quotas.md) se kterými se můžete setkat při práci s Azure Machine Learning.

## <a name="installation-and-import"></a>Instalace a import

* **Chybová zpráva: Nelze odinstalovat "PyYAML"**

    Azure Machine Learning SDK pro Python: PyYAML je nainstalovaný `distutils` projekt. Proto nemůžeme přesně určit, které soubory patří k němu, pokud je částečná odinstalace. Chcete-li pokračovat v instalaci sady SDK při ignorování této chyby, použijte:
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Databricks selhání při instalaci balíčků**

    Instalace sady Azure Machine Learning SDK se nezdaří na Azure Databricks při instalaci dalšíbalíčky. Některé balíčky, `psutil`například , mohou způsobit konflikty. Chcete-li se vyhnout chybám při instalaci, nainstalujte balíčky zmrazením verze knihovny. Tento problém souvisí s Databricks a ne s Azure Machine Learning SDK. K tomuto problému může dojít i v jiných knihovnách. Příklad:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    Případně můžete použít init skripty, pokud budete mít stále čelí problémům s instalací s knihovnami Pythonu. Tento přístup není oficiálně podporován. Další informace naleznete [v tématu Init skripty s rozsahem clusteru](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

* **Chyba importu datových cihel: Nelze importovat název Timedelta z "pandas._libs.tslibs"**: Pokud se tato chyba zobrazí při použití automatického strojového učení, spusťte v poznámkovém bloku dva následující řádky:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Chyba importu databricks: Žádný modul s názvem "pandas.core.indexes"**: Pokud se tato chyba zobrazí při použití automatického strojového učení:

    1. Spusťte tento příkaz a nainstalujte dva balíčky do clusteru Azure Databricks:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Odpojte a znovu připojte cluster k poznámkovému bloku.
    
    Pokud tyto kroky problém nevyřeší, zkuste cluster restartovat.

* **Databricks FailToSendFeather**: Pokud `FailToSendFeather` se při čtení dat v clusteru Azure Databricks zobrazí chyba, podívejte se na následující řešení:
    
    * Upgrade `azureml-sdk[automl]` balíčku na nejnovější verzi.
    * Přidejte `azureml-dataprep` verzi 1.1.8 nebo vyšší.
    * Přidejte `pyarrow` verzi 0.11 nebo vyšší.

## <a name="create-and-manage-workspaces"></a>Vytváření a správa pracovních prostorů

> [!WARNING]
> Přesunutí pracovního prostoru Azure Machine Learning do jiného předplatného nebo přesunutí vlastního předplatného do nového tenanta není podporováno. To může způsobit chyby.

* **Portál Azure**: Pokud přejdete přímo k zobrazení pracovního prostoru z odkazu sdílení z sady SDK nebo portálu, nebudete moct zobrazit normální stránku **Přehled** s informacemi o předplatném v rozšíření. Také nebudete moci přepnout do jiného pracovního prostoru. Pokud potřebujete zobrazit jiný pracovní prostor, přejděte přímo do [centra Azure Machine Learning](https://ml.azure.com) a vyhledejte název pracovního prostoru.

## <a name="set-up-your-environment"></a>Nastavení prostředí

* **Potíže s vytvářením AmlCompute**: Existuje vzácná šance, že někteří uživatelé, kteří vytvořili svůj pracovní prostor Azure Machine Learning z portálu Azure před vydáním GA, nemusí být schopni vytvořit AmlCompute v tomto pracovním prostoru. Můžete buď vyvolat žádost o podporu proti službě nebo vytvořit nový pracovní prostor prostřednictvím portálu nebo sady SDK odblokovat sami okamžitě.

## <a name="work-with-data"></a>Práce s daty

### <a name="overloaded-azurefile-storage"></a>Přetížené úložiště AzureFile

Pokud se zobrazí `Unable to upload project files to working directory in AzureFile because the storage is overloaded`chyba , použijte následující řešení.

Pokud používáte sdílenou složku pro jiné úlohy, jako je například přenos dat, doporučujeme použít objekty BLOB, aby byla sdílená složka souboru volně použita pro odesílání spuštění. Můžete také rozdělit zatížení mezi dva různé pracovní prostory.

### <a name="passing-data-as-input"></a>Předávání dat jako vstupu

*  **TypeError: FileNotFound: Žádný takový soubor nebo adresář**: K této chybě dochází, pokud cesta k souboru, kterou zadáte, není tam, kde je soubor umístěn. Musíte se ujistit, že způsob, jakým odkazujete na soubor, je konzistentní s tím, kde jste namontovali datovou sadu na váš výpočetní cíl. Chcete-li zajistit deterministický stav, doporučujeme použít abstraktní cestu při připojovat datovou sadu k výpočetnímu cíli. Například v následujícím kódu připojíme datovou sadu pod kořenem souborového `/tmp`systému výpočetního cíle . 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Pokud nezahrnete úvodní lomítko,/, budete muset předponu pracovního `/mnt/batch/.../tmp/dataset` adresáře, například na výpočetním cíli, abyste označili, kam chcete datovou sadu připojit.

### <a name="data-labeling-projects"></a>Projekty označování dat

|Problém  |Řešení  |
|---------|---------|
|Lze použít pouze datové sady vytvořené v úložišti dat objektů blob.     |  toto je známé omezení aktuální verze.       |
|Po vytvoření se v projektu zobrazí "Inicializace" po dlouhou dobu     | Ručně aktualizujte stránku. Inicializace by měla probíhat přibližně 20 datových bodů za sekundu. Nedostatek automatické aktualizace je známý problém.         |
|Při kontrole obrázků se nově označené obrázky nezobrazují     |   Chcete-li načíst všechny označené obrázky, zvolte **tlačítko První.** První **First** tlačítko vás přenese zpět na přední stranu seznamu, ale načte všechna označená data.      |
|Stisknutím klávesy Esc při popisování pro detekci objektů se vytvoří popisek nulové velikosti v levém horním rohu. Odeslání popisků v tomto stavu se nezdaří.     |   Odstraňte štítek kliknutím na křížovou značku vedle něj.  |

## <a name="azure-machine-learning-designer"></a>Návrhář Azure Machine Learning

Známé problémy:

* **Dlouhá doba přípravy výpočetních prostředků**: Může být několik minut nebo i déle, když se poprvé připojíte nebo vytvoříte výpočetní cíl. 

## <a name="train-models"></a>Trénování modelů

* **ModuleErrors (Žádný modul s názvem)**: Pokud běžíte do ModuleErrors při odesílání experimentů v Azure ML, znamená to, že školicí skript očekává balíček k instalaci, ale není přidán. Jakmile zadáte název balíčku, Azure ML nainstaluje balíček v prostředí používaném pro vaše školení. 

    Pokud používáte Odhady k odesílání [experimentů,](concept-azure-machine-learning-architecture.md#estimators) můžete `pip_packages` zadat `conda_packages` název balíčku prostřednictvím nebo parametr v odhadu na základě zdroje, ze kterého zdroje chcete balíček nainstalovat. Můžete také zadat soubor yml se všemi vašimi závislostmi pomocí `conda_dependencies_file`nebo `pip_requirements_file` vypsat všechny vaše pip požadavky v souboru txt pomocí parametru. Pokud máte vlastní objekt prostředí Azure ML, který chcete přepsat výchozí bitovou kopii používanou `environment` odhadce, můžete určit, že prostředí prostřednictvím parametru konstruktoru odhadu.

    Azure ML také poskytuje odhady specifické pro architekturu pro Tensorflow, PyTorch, Chainer a SKLearn. Pomocí těchto odhadů se ujistěte, že základní framework závislosti jsou nainstalovány vaším jménem v prostředí používaném pro školení. Máte možnost zadat další závislosti, jak je popsáno výše. 
 
    Azure ML udržovány docker image a jejich obsah lze zobrazit v [kontejnerech AzureML](https://github.com/Azure/AzureML-Containers).
    Závislosti specifické pro architekturu jsou uvedeny v příslušné rámcové dokumentaci - [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

    > [!Note]
    > Pokud si myslíte, že konkrétní balíček je natolik běžný, že se dá přidat do azure ml udržovaných bitových kopií a prostředí, vyvolávejte problém GitHubu v [kontejnerech AzureML](https://github.com/Azure/AzureML-Containers). 
 
* **NameError (Název není definován), AttributeError (Objekt nemá žádný atribut)**: Tato výjimka by měla pocházet z vašich trénovacích skriptů. Můžete se podívat na soubory protokolu z webu Azure Portal získat další informace o konkrétní název není definován nebo chyba atribut. Ze sady SDK můžete `run.get_details()` použít k zobrazení chybové zprávy. To bude také seznam všech souborů protokolu generovaných pro váš běh. Prosím, ujistěte se, že se podívat na váš školicí skript a opravit chybu před resubmitting vašeho běhu. 

* **Horovod byl vypnut**: Ve většině případů, pokud narazíte na "AbortedError: Horovod byl vypnut" tato výjimka znamená, že v jednom z procesů, které způsobily vypnutí Horovoda, byla základní výjimka. Každé pořadí v úloze MPI získá vlastní vyhrazený soubor protokolu v Azure ML. Tyto protokoly `70_driver_logs`jsou pojmenovány . V případě distribuované školení, názvy `_rank` protokolu jsou suffix s usnadnit rozlišení protokoly. Chcete-li najít přesnou chybu, která způsobila Horovod vypnout, projít všechny soubory protokolu a hledat `Traceback` na konci driver_log souborů. Jeden z těchto souborů vám poskytne skutečnou základní výjimku. 

* **Spustit nebo experimentovat odstranění**: Experimenty lze archivovat pomocí [metody Experiment.archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) nebo z karty Experiment zobrazení v klientovi studio Azure Machine Learning pomocí tlačítka "Experiment archivovat". Tato akce skryje experiment z dotazů a zobrazení seznamu, ale neodstraní jej.

    Trvalé odstranění jednotlivých experimentů nebo spuštění není v současné době podporováno. Další informace o odstranění datových zdrojů pracovního prostoru naleznete v [tématu Export nebo odstranění dat pracovního prostoru služby Machine Learning](how-to-export-delete-data.md).

* **Metric Document je příliš velký**: Azure Machine Learning má vnitřní omezení velikosti objektů metriky, které lze protokolovat najednou z trénovacího běhu. Pokud při protokolování metriky s hodnotou seznamu narazíte na chybu "Metrika je příliš velký", zkuste seznam rozdělit na menší bloky, například:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Interně Azure ML zřetězí bloky se stejným názvem metriky do souvislého seznamu.

## <a name="automated-machine-learning"></a>Automatizované strojové učení

* **TenzorOvý tok**: Automatizované strojové učení v současné době nepodporuje tenzorový tok verze 1.13. Instalace této verze způsobí, že závislosti balíčků přestanou fungovat. Pracujeme na vyřešení tohoto problému v budoucí verzi.

* **Experimentální grafy:** Binární klasifikační grafy (přesnost-odvolání, ROC, gain křivka atd.) zobrazené v automatizovaných iterací experimentu ML nejsou vykreslování správně v uživatelském rozhraní od 4 / 12. Obrázky grafu jsou aktuálně zobrazeny inverzní výsledky, kde jsou zobrazeny výkonnější modely s nižšími výsledky. Usnesení se vyšetřuje.

* **Databricks zrušit automatizovaný strojový učení spustit**: Pokud používáte funkce automatického strojového učení na Azure Databricks, zrušit spuštění a spustit nový experiment spustit, restartujte cluster Azure Databricks.

* **Databricks >10 iterací pro automatizované strojové učení**: V automatickém nastavení `show_output` `False` strojového učení, pokud máte více než 10 iterací, nastavte při odeslání spuštění.

* **Widget Databricks pro Azure Machine Learning SDK a automatizované strojové učení**: Widget Azure Machine Learning SDK není v poznámkovém bloku Databricks podporovaný, protože poznámkové bloky nemohou analyzovat widgety HTML. Widget můžete zobrazit na portálu pomocí tohoto kódu Pythonu v buňce poznámkového bloku Azure Databricks:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

## <a name="deploy--serve-models"></a>Nasazení a obsluha modelů

Pro následující chyby provázte tyto akce:

|Chyba  | Řešení  |
|---------|---------|
|Selhání vytváření obrázků při nasazování webové služby     |  Přidat "pynacl==1.2.1" jako závislost pipu do souboru Conda pro konfiguraci obrazu       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Změňte skladovou položku pro virtuální počítače používané ve vašem nasazení na tu, která má více paměti. |
|Selhání FPGA     |  Modely nebude možné nasadit na FPGA, dokud nepožádáte o kvótu FPGA a nebudete schváleni. Chcete-li požádat o přístup, vyplňte formulář žádosti o kvótu:https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Aktualizace součástí Azure Machine Learning v clusteru AKS

Aktualizace součástí Azure Machine Learning nainstalovaných v clusteru služby Azure Kubernetes musí být použity ručně. 

Tyto aktualizace můžete použít odpojením clusteru z pracovního prostoru Azure Machine Learning a opětovným připojením clusteru k pracovnímu prostoru. Pokud je v clusteru povolena technologie TLS, budete muset při opětovném připojení clusteru zadat certifikát TLS/SSL a soukromý klíč. 

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

Pokud již nemáte certifikát TLS/SSL a soukromý klíč nebo používáte certifikát generovaný službou Azure Machine Learning, můžete soubory načíst před `kubectl` odpojením `azuremlfessl`clusteru připojením ke clusteru pomocí a načtením tajného klíče .

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes ukládá tajemství v základním-64 kódovaném formátu. Před poskytnutím `cert.pem` tajných kódů budete `key.pem` muset dekódovat součásti `attach_config.enable_ssl`tajných kódů . 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Webservices v Azure Kubernetes service selhání

Mnoho selhání webové služby ve službě Azure Kubernetes lze ladit `kubectl`připojením ke clusteru pomocí . Pro cluster `kubeconfig.json` služeb Azure Kubernetes můžete získat spuštěním

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>Chyby ověřování

Pokud provedete operaci správy výpočetního cíle ze vzdálené úlohy, obdržíte jednu z následujících chyb:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Například se zobrazí chyba, pokud se pokusíte vytvořit nebo připojit cíl výpočetní ch od ML kanálu, který je odeslán ke vzdálenému spuštění.
