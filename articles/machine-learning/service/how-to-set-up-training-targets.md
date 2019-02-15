---
title: Vytvoření a použití cílových výpočetních prostředí pro trénování modelu
titleSuffix: Azure Machine Learning service
description: Konfigurace prostředí školení (cílových výpočetních prostředí) pro strojové učení cvičení modelu. Můžete snadno přepínat mezi prostředími školení. Spuštění trénovací místně. Pokud je potřeba škálovat na více systémů, přepněte do cílové výpočetní prostředí založené na cloudu.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: article
ms.date: 01/07/2019
ms.custom: seodec18
ms.openlocfilehash: a0d20d6cdb719f34a50052ff2eb693071c7ece96
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268154"
---
# <a name="set-up-compute-targets-for-model-training"></a>Nastavení cílových výpočetních prostředí pro trénování modelu

Pomocí služby Azure Machine Learning můžete trénování modelu na širokou škálu prostředků nebo prostředí, které se souhrnně označují jako [ __cílových výpočetních prostředí__](concept-azure-machine-learning-architecture.md#compute-target). Cílové výpočetní prostředí může být místním počítači nebo prostředku cloudu, jako jsou Azure Machine Learning Compute, Azure HDInsight nebo vzdáleného virtuálního počítače.  Můžete také vytvořit cílových výpočetních prostředí pro model nasazení, jak je popsáno v ["kde a jak nasadit modely"](how-to-deploy-and-where.md).

Můžete vytvořit a spravovat cílové výpočetní prostředí pomocí Azure Machine Learning SDK, webu Azure portal nebo rozhraní příkazového řádku Azure. Pokud máte cílových výpočetních prostředí, které byly vytvořené pomocí jiné služby (například cluster HDInsight), můžete jejich připojením do pracovního prostoru služby Azure Machine Learning.
 
V tomto článku se dozvíte, jak používat různé cílových výpočetních prostředí pro cvičení modelu.  Postup pro všechny cílových výpočetních prostředí postupujte podle stejného pracovního postupu:
1. __Vytvoření__ cílové výpočetní prostředí, pokud ho ještě nemáte.
2. __Připojit__ cílové výpočetní prostředí do pracovního prostoru.
3. __Konfigurace__ cílové výpočetní prostředky tak, aby obsahoval Python prostředí a balíček potřebné závislosti pro váš skript.


>[!NOTE]
> S využitím Azure Machine Learning SDK verze 1.0.6 testovaný kód v tomto článku.

## <a name="compute-targets-for-training"></a>Cílových výpočetních prostředí pro školení

Služba Azure Machine Learning nabízí různé podporu napříč různými výpočetními cíli. Životní cyklus vývoje typické modelu začíná dev/experimentování na malé množství dat ve službě. V této fázi doporučujeme používat místní prostředí. Například místního počítače nebo virtuálního počítače založené na cloudu. Vertikálně navýšit kapacitu trénování na větších datových sad, nebo proveďte distribuované trénování, doporučujeme vytvořit jeden nebo více node cluster tohoto pravidla automatického škálování provedou pokaždé, když odešlete spuštění pomocí Azure Machine Learning Compute. Můžete také připojit své vlastní výpočetní prostředek, ačkoli podpory pro různé scénáře se může lišit jako podrobnosti jsou dole:


|Cílové školení výpočetní prostředí| Akcelerace GPU | Automatizované<br/> hyperparametrů | Automatizované</br> Strojové učení | Kanály Azure Machine Learning |
|----|:----:|:----:|:----:|:----:|
|[Místní počítač](#local)| Možná | &nbsp; | ✓ | &nbsp; |
|[Azure Machine Learning Compute](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[Vzdáleném virtuálním počítači](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](how-to-create-your-first-pipeline.md#databricks)| &nbsp; | &nbsp; | ✓ | ✓ |
|[Azure Data Lake Analytics](how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | &nbsp; | ✓ |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

**Všechny výpočetní cíle lze opětovně použít pro více úlohy trénování**. Například po připojení vzdáleném virtuálním počítači do svého pracovního prostoru, jej můžete znovu použít pro více úloh.

> [!NOTE]
> Azure Machine Learning Compute můžou vytvořit jako prostředek trvalé nebo dynamicky vytvoří, když požádáte o spuštění. Na základě spuštění vytvoření odebere cílové výpočetní prostředí po spuštění školení je dokončeno, proto nelze znovu použít cílových výpočetních prostředí vytvořené tímto způsobem.

## <a name="whats-a-run-configuration"></a>Co je konfigurace spuštění?

Při tréninku, je společný pro spuštění v místním počítači a později spustit trénovací skript v jiného cílového výpočetního prostředí. Pomocí služby Azure Machine Learning můžete spustit skript v různých cílových výpočetních prostředí bez nutnosti změny vašeho skriptu. 

Všechno, co potřebujete udělat se definují prostředí pro každé cílové výpočetní prostředí s **konfigurace spuštění**.  Pokud chcete spouštět výukového experimentu jiného cílového výpočetního prostředí, zadejte konfigurace spuštění pro tento výpočet. 

Další informace o [odesílání experimentů](#submit) na konci tohoto článku.

### <a name="manage-environment-and-dependencies"></a>Správa prostředí a závislosti

Při vytváření konfigurace spuštění, budete muset rozhodnout, jak spravovat prostředí a v závislosti na cílové výpočetní prostředí. 

#### <a name="system-managed-environment"></a>Systém spravované prostředí

Použijte prostředí spravované systému, když potřebujete [Conda](https://conda.io/docs/) ke správě prostředí Pythonu a závislosti skript za vás. Prostředí spravované systému se předpokládá, že ve výchozím nastavení a nejběžnější podle výběru. Je užitečné v cílových výpočetních prostředí vzdálené, zejména v případě, že nemůžete nakonfigurovat, které cílí. 

Vše je třeba provést, je určit závislosti každého balíčku pomocí [CondaDependency třídy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) Conda pak vytvoří soubor s názvem **conda_dependencies.yml** v **aml_config** adresář ve vašem pracovním prostoru s seznamu závislosti balíčků a nastaví prostředí Pythonu při odesílání vašeho výukového experimentu. 

Počáteční nastavení nové prostředí může trvat několik minut v závislosti na velikosti požadované závislosti. Tak dlouho, dokud seznam balíčků zůstane beze změny, doba instalace dojde jenom jednou.
  
Následující kód ukazuje příklad pro prostředí systému spravované vyžadující scikit-informace:
    
[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_system_managed)]

#### <a name="user-managed-environment"></a>Uživatel spravován prostředí

Pro prostředí uživatel spravován jste zodpovědní za nastavením vašeho prostředí a instalace všech balíčků musí cvičný skript v cílové výpočetní prostředí. Pokud prostředí školení je už nakonfigurovaný (například na místním počítači), můžete přeskočit krok instalace pomocí nastavení `user_managed_dependencies` na hodnotu True. Conda nebude kontrolovat vaše prostředí nebo nic instalovat za vás.

Následující kód ukazuje příklad konfigurace tréninkových spuštění pro uživatele, spravovat prostředí:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_user_managed)]
  
## <a name="set-up-compute-targets-with-python"></a>Nastavení cílových výpočetních prostředí s využitím Pythonu

Použijte následující části, abyste konfiguraci těchto cílových výpočetních prostředí:

* [Místní počítač](#local)
* [Azure Machine Learning Compute](#amlcompute)
* [Virtuálním počítačům](#vm)
* [Azure HDInsight](#hdinsight)


### <a id="local"></a>Místní počítač

1. **Vytvořte a připojte**: Není nutné vytvořit nebo připojit cílové výpočetní prostředí pro použití místního počítače jako prostředí pro školení.  

1. **Konfigurace**:  Při použití místního počítače jako cílové výpočetní prostředí, školení kód spuštěný vaše [vývojové prostředí](how-to-configure-environment.md).  Pokud je toto prostředí už balíčky Pythonu, které potřebujete, použijte prostředí spravované uživatele.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Teď, když jste připojené tak výpočetní prostředky a nakonfigurovat spuštění, dalším krokem je [odeslat spuštění školení](#submit).

### <a id="amlcompute"></a>Azure Machine Learning Compute

Azure Machine Learning Compute je spravované výpočetní infrastruktura, která umožňuje uživatelům snadno vytvořit jeden nebo více uzly výpočetní. Výpočetní prostředky se vytvoří v rámci oblasti vašeho pracovního prostoru jako prostředek, který můžete sdílet s ostatními uživateli ve vašem pracovním prostoru. Výpočetní prostředky automaticky škáluje při odeslání úlohy a můžete umístit do služby Azure Virtual Network. Výpočetní provede v prostředí kontejnerizovaných a balíčky závislostí v modelu [kontejneru Dockeru](https://www.docker.com/why-docker).

Azure Machine Learning Compute můžete použít k procesu trénování distribuovat napříč clusterem CPU nebo GPU výpočetních uzlů v cloudu. Další informace o velikostech virtuálních počítačů, které zahrnují GPU najdete v tématu [velikosti virtuálního počítače GPU optimalizované](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Azure Machine Learning Compute má výchozí omezení, jako je počet jader, které mohou být přiděleny. Další informace najdete v tématu [spravovat a žádosti o kvóty pro prostředky Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).


Výpočetní prostředí Azure Machine Learning můžete vytvořit na vyžádání při spuštění naplánovat, nebo jako trvalý prostředek.

#### <a name="run-based-creation"></a>Tvorby založené na spuštění

Můžete vytvořit Azure Machine Learning Compute jako cílové výpočetní prostředí za běhu. Výpočetní prostředky se automaticky vytvoří pro spuštění. Škálování clusteru až počet **max_nodes** , který zadáte v běhu konfiguraci. Výpočetní je automaticky odstraněn, až instance doběhne.

> [!IMPORTANT]
> Zřízení spustit výpočetní prostředky Azure Machine Learning je aktuálně ve verzi Preview. Nepoužívejte tvorby založené na spuštění, pokud používáte automatické hyperparametrů nebo automatizované strojového učení. Pokud chcete použít hyperparametrů nebo automatizované strojového učení, vytvořte [trvalé výpočetní](#persistent) cílové místo toho.

1.  **Vytvořit, připojit a nakonfigurovat**: Vytvoření na základě spuštění provede všechny potřebné kroky k vytvoření, připojit a nakonfigurovat cílové výpočetní prostředí pomocí konfigurace spuštění.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Teď, když jste připojené tak výpočetní prostředky a nakonfigurovat spuštění, dalším krokem je [odeslat spuštění školení](#submit).

#### <a id="persistent"></a>Trvalé výpočetní prostředky

Trvalé Azure Machine Learning Compute můžete použít opakovaně napříč úlohy. Výpočetní prostředky můžete sdílet s ostatními uživateli v pracovním prostoru a zůstane mezi úlohami.

1. **Vytvořte a připojte**: Chcete-li vytvořit trvalý prostředek Azure Machine Learning Compute v Pythonu, zadejte **vm_size** a **max_nodes** vlastnosti. Azure Machine Learning potom použije inteligentních výchozích hodnot pro ostatní vlastnosti. Provádí výpočty na 0 uzlů, kdy se nepoužívá.   Vyhrazených virtuálních počítačů vytvářejí ke spouštění úloh podle potřeby.
    
    * **vm_size**: Virtuální počítač řady uzly vytvořené pomocí Azure Machine Learning Compute.
    * **max_nodes**: Maximální počet uzlů na automatické škálování až při spuštění úlohy v Azure Machine Learning Compute.
    
 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

  Můžete také nakonfigurovat několik upřesňující vlastnosti při vytváření Azure Machine Learning Compute. Vlastnosti umožňují vytvářet trvalé clusteru s pevnou velikostí, nebo v rámci existující virtuální síť Azure v rámci vašeho předplatného.  Zobrazit [AmlCompute třídy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) podrobnosti.
    
 Nebo můžete vytvořit a připojit trvalý prostředek Azure Machine Learning Compute [na webu Azure Portal](#portal-create).

1. **Konfigurace**: Vytvoření konfigurace spuštění pro cílové trvalé výpočetní prostředí.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Teď, když jste připojené tak výpočetní prostředky a nakonfigurovat spuštění, dalším krokem je [odeslat spuštění školení](#submit).


### <a id="vm"></a>Virtuálním počítačům

Azure Machine Learning podporuje také přináší vlastní výpočetních prostředků a připojíte ho do pracovního prostoru. Jeden takový typ prostředku je libovolné vzdáleném virtuálním počítači, pokud je přístupná ze služby Azure Machine Learning. Prostředek může být virtuální počítač Azure, vzdáleném serveru ve vaší organizaci, nebo místní. Konkrétně zadaný IP adresu a přihlašovací údaje (uživatelské jméno a heslo nebo klíč SSH), můžete použít jakýkoli dostupný virtuální počítač pro vzdálené spuštění.

Můžete použít prostředí integrovaného systému conda, již existující prostředí Python nebo kontejneru Docker. Ke spuštění v kontejneru Dockeru, musíte mít běžící na virtuálním počítači modul Docker. Tato funkce je zvlášť užitečné, pokud chcete dev/experimentování flexibilnější a cloudové prostředí než místního počítače.

Použití Azure Data virtuálního počítače VĚDY jako virtuální počítač Azure podle výběru v tomto scénáři. Tento virtuální počítač je předem nakonfigurované datové vědy a AI vývojovým prostředím v Azure. Virtuální počítač nabízí kurátorované řadu nástrojů a architektur pro úplné životního cyklu strojového učení vývoje. Další informace o tom, jak používat datové VĚDY s Azure Machine Learning najdete v tématu [nakonfigurovat prostředí pro vývoj](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

1. **Vytvoření**: Vytvoření DSVM než je použijete k natrénování modelu. Chcete-li vytvořit tento prostředek, naleznete v tématu [zřídit virtuální počítač pro datové vědy pro Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Azure Machine Learning podporuje pouze virtuální počítače se systémem Ubuntu. Při vytváření virtuálního počítače nebo vyberte existující virtuální počítač, je nutné vybrat virtuální počítač, který používá Ubuntu.

1. **Připojit**: Připojit existující virtuální počítač jako cílové výpočetní prostředí, musíte zadat plně kvalifikovaný název domény (FQDN), uživatelské jméno a heslo pro virtuální počítač. V tomto příkladu nahraďte \<plně kvalifikovaný název domény > s veřejný plně kvalifikovaný název domény virtuálního počítače nebo veřejnou IP adresu. Nahraďte \<uživatelské jméno > a \<heslo > s SSH uživatelské jméno a heslo pro virtuální počítač.

 ```python
 from azureml.core.compute import RemoteCompute, ComputeTarget

 # Create the compute config 
 compute_target_name = "attach-dsvm"
 attach_config = RemoteCompute.attach_configuration(address = "<fqdn>",
                                                    ssh_port=22,
                                                    username='<username>',
                                                    password="<password>")

 # If you authenticate with SSH keys instead, use this code:
 #                                                  ssh_port=22,
 #                                                  username='<username>',
 #                                                  password=None,
 #                                                  private_key_file="<path-to-file>",
 #                                                  private_key_passphrase="<passphrase>")

 # Attach the compute
 compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

 compute.wait_for_completion(show_output=True)
 ```

 Nebo datové VĚDY můžete připojit k vašemu pracovnímu prostoru [pomocí webu Azure portal](#portal-reuse).

1. **Konfigurace**: Vytvoření konfigurace spuštění pro DSVM cílového výpočetního prostředí. Docker a conda slouží k vytvoření a konfigurace prostředí pro školení na datové VĚDY.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Teď, když jste připojené tak výpočetní prostředky a nakonfigurovat spuštění, dalším krokem je [odeslat spuštění školení](#submit).

### <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight je oblíbená platforma pro analýzy velkých objemů dat. Tato platforma poskytuje Apache Spark, který slouží k natrénování modelu.

1. **Vytvoření**:  Vytvoření clusteru HDInsight, předtím, než ho použijete k natrénování modelu. Vytvoření Spark na clusteru HDInsight najdete v tématu [vytvoření clusteru Spark v HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    Při vytváření clusteru, musíte zadat uživatelské jméno SSH a heslo. Poznamenejte si tyto hodnoty, podle potřeby je používat jako cílové výpočetní prostředí HDInsight.
    
    Po vytvoření clusteru se k němu připojit pomocí názvu hostitele \<Název_clusteru >-ssh.azurehdinsight.net, kde \<Název_clusteru > je název, který jste zadali pro cluster. 

1. **Připojit**: Připojit cluster HDInsight jako cílové výpočetní prostředí, musíte zadat název hostitele, uživatelské jméno a heslo pro HDInsight cluster. Následující příklad používá sadu SDK připojit cluster do pracovního prostoru. V tomto příkladu nahraďte \<Název_clusteru > s názvem vašeho clusteru. Nahraďte \<uživatelské jméno > a \<heslo > s SSH uživatelské jméno a heslo pro cluster.

  ```python
 from azureml.core.compute import ComputeTarget, HDInsightCompute
 from azureml.exceptions import ComputeTargetException

 try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase
    attach_config = HDInsightCompute.attach_configuration(address='<clustername>-ssh.azureinsight.net', 
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

 except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

 hdi_compute.wait_for_completion(show_output=True)
  ```

  Nebo clusteru HDInsight můžete připojit k vašemu pracovnímu prostoru [pomocí webu Azure portal](#portal-reuse).

1. **Konfigurace**: Vytvoření konfigurace spuštění pro cílové výpočetní prostředí Hdinsight. 

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Teď, když jste připojené tak výpočetní prostředky a nakonfigurovat spuštění, dalším krokem je [odeslat spuštění školení](#submit).


## <a name="set-up-compute-in-the-azure-portal"></a>Nastavte výpočetní prostředky na webu Azure Portal

Můžete přistupovat cílových výpočetních prostředí, které jsou spojeny s pracovního prostoru na webu Azure Portal.  Na portálu můžete použít:

* [Zobrazení cílových výpočetních prostředí](#portal-view) připojené k vašemu pracovnímu prostoru
* [Vytvořte cílové výpočetní prostředí](#portal-create) ve vašem pracovním prostoru
* [Připojit cílové výpočetní prostředí](#portal-reuse) , který byl vytvořen mimo pracovní prostor

Po cíl se vytvoří a připojí k vašemu pracovnímu prostoru, budete ho používat v konfiguraci spuštění s `ComputeTarget` objektu: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>Zobrazení cílových výpočetních prostředí


Pokud chcete zobrazit cílových výpočetních prostředí pro váš pracovní prostor, postupujte následovně:

1. Přejděte [webu Azure portal](https://portal.azure.com) a otevřete pracovní prostor. 
1. V části __aplikací__vyberte __Compute__.

    ![Karta výpočetní zobrazení](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a id="portal-create"></a>Vytvořte cílové výpočetní prostředí

Postupujte podle předchozích kroků a zobrazí se seznam cílových výpočetních prostředí. Potom vytvořte cílové výpočetní prostředí pomocí těchto kroků: 

1. Vyberte znaménko plus (+) Chcete-li přidat cílové výpočetní prostředí.

    ![Přidat cílové výpočetní prostředí](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Zadejte název cílového výpočetního prostředí. 

1. Vyberte **Machine Learning Compute** jako typ výpočetních prostředků a použít pro __školení__. 

    >[!NOTE]
    >Azure Machine Learning Compute je pouze spravované výpočetních prostředků, které vytvoříte na webu Azure Portal.  Další výpočetní prostředky je možné připojit po jejich vytvoření.

1. Vyplňte formulář. Zadejte hodnoty pro požadované vlastnosti, zejména **řadu virtuálních počítačů**a **maximální počet uzlů** můžete aktivovat tak výpočetní prostředky.  

    ![Vyplňte formulář](./media/how-to-set-up-training-targets/add-compute-form.png) 

1. Vyberte __Vytvořit__.


1. Zobrazení stavu operace vytvoření cílové výpočetní prostředí výběrem ze seznamu:

    ![Vyberte cílové výpočetní prostředí, chcete-li zobrazit stav operace create](./media/how-to-set-up-training-targets/View_list.png)

1. Zobrazí podrobnosti pro cílové výpočetní prostředí: 

    ![Zobrazit podrobnosti o cílovém počítači](./media/how-to-set-up-training-targets/compute-target-details.png) 



### <a id="portal-reuse"></a>Připojit cílových výpočetních prostředí

Pokud chcete použít cílových výpočetních prostředí vytvořená mimo pracovní prostor služby Azure Machine Learning, je nutné připojit je. Připojení cílového výpočetního prostředí je k dispozici pro váš pracovní prostor.

Postupujte podle kroků popsaných dříve zobrazí seznam cílových výpočetních prostředí. Pak použijte následující kroky k připojení cílového výpočetního prostředí: 

1. Vyberte znaménko plus (+) Chcete-li přidat cílové výpočetní prostředí. 
1. Zadejte název cílového výpočetního prostředí. 
1. Vyberte typ compute připojit pro __školení__:

    > [!IMPORTANT]
    > Ne všechny výpočetní typy je možné připojit na webu Azure Portal. Typy výpočetní prostředky, které může být aktuálně připojena k trénování zahrnují:
    >
    > * Vzdáleném virtuálním počítači
    > * Azure Databricks (pro použití v kanálech strojové učení)
    > * Azure Data Lake Analytics (pro použití v kanálech strojové učení)
    > * Azure HDInsight

1. Vyplňte formulář a zadejte hodnoty pro požadované vlastnosti.

    > [!NOTE]
    > Společnost Microsoft doporučuje používat klíče SSH, které jsou bezpečnější než hesla. Hesla se stát terčem útoku hrubou silou. Klíče SSH využívají kryptografické podpisy. Informace o tom, jak vytvořit klíče SSH pro použití s Azure Virtual Machines najdete v následujících dokumentech:
    >
    > * [Vytvoření a použití klíčů SSH v Linuxu nebo macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Vytvoření a používání klíčů SSH na Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Vyberte __připojit__. 
1. Zobrazení stavu operace připojení tak, že vyberete cílového výpočetního prostředí v seznamu.

## <a name="set-up-compute-with-the-cli"></a>Nastavte výpočetní pomocí rozhraní příkazového řádku

Můžete přistupovat cílových výpočetních prostředí, které jsou spojené s použitím vašeho pracovního prostoru [rozšíření rozhraní příkazového řádku](reference-azure-machine-learning-cli.md) pro službu Azure Machine Learning.  Můžete použít rozhraní příkazového řádku pro:

* Vytvořit cílové spravované výpočetní prostředí
* Aktualizace cílové spravované výpočetní prostředí
* Připojení cílové výpočetní prostředí nespravované

Další informace najdete v tématu [správy prostředků](reference-azure-machine-learning-cli.md#resource-management).


## <a id="submit"></a>Odeslat školení spuštění

Po vytvoření konfigurace spuštění můžete použít ke spuštění experimentu.  Model kódu k odeslání školení spuštění je stejný pro všechny typy cílových výpočetních prostředí:

1. Vytvořit nový experiment ke spuštění
1. Odešlete spuštění.
1. Vyčkat, než běžet do dokončení.

### <a name="create-an-experiment"></a>Vytvoření experimentu

Nejprve vytvořte experiment ve vašem pracovním prostoru.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Odeslání experimentu

Odeslání experimentu se `ScriptRunConfig` objektu.  Tento objekt obsahuje:

* **source_directory**: Zdrojový adresář, který obsahuje cvičný skript
* **skript**: Identifikujte cvičný skript
* **run_config**: Konfigurace spuštění, který zase definuje, ve kterém bude probíhat na školení.

Při odesílání spustit trénovací snímek adresáře, který obsahuje trénovací skripty se vytvoří a odešle cílového výpočetního prostředí. Další informace najdete v tématu [snímky](concept-azure-machine-learning-architecture.md#snapshot).

Chcete-li například použít [místní cíl](#local) konfigurace:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Přepnout tom stejném experimentu ke spuštění v jiného cílového výpočetního prostředí pomocí jiné konfigurace spuštění, například [amlcompute cílové](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

Nebo můžete:

* Odeslání experimentu s `Estimator` jak je znázorněno v [ML trénování modelů s odhady](how-to-train-ml-models.md). 
* Odeslání experimentu [pomocí rozšíření rozhraní příkazového řádku](reference-azure-machine-learning-cli.md#experiments).

## <a name="notebook-examples"></a>Příklady poznámkového bloku

Tyto poznámkové bloky příklady trénování s využitím různých cílových výpočetních prostředí najdete:
* [postupy-k-použití azureml a školení](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [kurzy/img – klasifikace – část 1 – training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další postup

* [Kurz: Trénování modelu](tutorial-train-models-with-aml.md) používá cílové spravované výpočetní prostředí k natrénování modelu.
* Jakmile budete mít trénovaného modelu, přečtěte si [jak a kde nasadit modely](how-to-deploy-and-where.md).
* Zobrazení [RunConfiguration třídy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) odkaz na sadu SDK.
* [Použití služby Azure Machine Learning s Azure Virtual Network](how-to-enable-virtual-network.md)
