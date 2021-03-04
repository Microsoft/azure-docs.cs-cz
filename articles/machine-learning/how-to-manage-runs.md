---
title: Spuštění, monitorování a zrušení školicích běhů v Pythonu
titleSuffix: Azure Machine Learning
description: Naučte se spouštět, nastavovat a spravovat experimenty ve službě Machine Learning pomocí Azure Machine Learning Python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 12/04/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 3eaab31d3948e41a216eaa402c2a11e470a6545d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691497"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Spuštění, monitorování a zrušení školicích běhů v Pythonu

[Sada SDK Azure Machine Learning pro Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py), [Machine Learning CLI](reference-azure-machine-learning-cli.md)a [Azure Machine Learning Studio](https://ml.azure.com) nabízí různé metody pro monitorování, organizování a správu vašich běhů pro školení a experimentování.

Tento článek ukazuje příklady následujících úloh:

* Monitorujte výkon spouštění.
* Zrušení nebo selhání spuštění.
* Vytvoření podřízených spuštění.
* Označení a hledání spuštění.

> [!TIP]
> Pokud hledáte informace o monitorování služby Azure Machine Learning a přidružených služeb Azure, přečtěte si téma [jak monitorovat Azure Machine Learning](monitor-azure-machine-learning.md).
> Pokud hledáte informace o modelech monitorování nasazených jako webové služby nebo IoT Edgech modulech, přečtěte si téma [shromáždění dat modelu](how-to-enable-data-collection.md) a [monitorování pomocí Application Insights](how-to-enable-app-insights.md).

## <a name="prerequisites"></a>Požadavky

Budete potřebovat následující položky:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

* [Pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).

* Sada SDK Azure Machine Learning pro Python (verze 1.0.21 nebo novější). Chcete-li nainstalovat nebo aktualizovat nejnovější verzi sady SDK, přečtěte si téma [instalace nebo aktualizace sady SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).

    Chcete-li zjistit verzi sady Azure Machine Learning SDK, použijte následující kód:

    ```python
    print(azureml.core.VERSION)
    ```

* Rozšíření [Azure CLI](/cli/azure/?preserve-view=true&view=azure-cli-latest) a rozhraní příkazového [řádku pro Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="monitor-run-performance"></a>Monitorování výkonu spuštění

* Spuštění běhu a jeho procesu protokolování

    # <a name="python"></a>[Python](#tab/python)
    
    1. Nastavte experiment tak, že naimportujete třídy [pracovní prostor](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py), [experiment](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py), [Run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py)a [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) z balíčku [AzureML. Core](/python/api/azureml-core/azureml.core?preserve-view=true&view=azure-ml-py) .
    
        ```python
        import azureml.core
        from azureml.core import Workspace, Experiment, Run
        from azureml.core import ScriptRunConfig
        
        ws = Workspace.from_config()
        exp = Experiment(workspace=ws, name="explore-runs")
        ```
    
    1. Spusťte běh a jeho proces protokolování s [`start_logging()`](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truestart-logging--args----kwargs-) metodou.
    
        ```python
        notebook_run = exp.start_logging()
        notebook_run.log(name="message", value="Hello from run!")
        ```
        
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    Pro spuštění experimentu použijte následující postup:
    
    1. V prostředí nebo příkazovém řádku můžete pomocí Azure CLI ověřit předplatné Azure:
    
        ```azurecli-interactive
        az login
        ```
        
        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 
    
    1. Připojte konfiguraci pracovního prostoru ke složce, která obsahuje váš školicí skript. Nahraďte `myworkspace` pracovním prostorem Azure Machine Learning. Nahraďte `myresourcegroup` skupinou prostředků Azure, která obsahuje váš pracovní prostor:
    
        ```azurecli-interactive
        az ml folder attach -w myworkspace -g myresourcegroup
        ```
    
        Tento příkaz vytvoří `.azureml` podadresář, který obsahuje příklady souborů prostředí RunConfig a conda. Obsahuje taky `config.json` soubor, který se používá ke komunikaci s vaším pracovním prostorem Azure Machine Learning.
    
        Další informace najdete v tématu [AZ ml složka připojit](/cli/azure/ext/azure-cli-ml/ml/folder?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).
    
    2. Chcete-li spustit spuštění, použijte následující příkaz. Při použití tohoto příkazu zadejte název souboru RunConfig (text před \* . RunConfig, pokud hledáte v systému souborů) s parametrem-c.
    
        ```azurecli-interactive
        az ml run submit-script -c sklearn -e testexperiment train.py
        ```
    
        > [!TIP]
        > `az ml folder attach`Příkaz vytvořil `.azureml` podadresář, který obsahuje dva příklady souborů RunConfig.
        >
        > Pokud máte skript Pythonu, který vytvoří objekt konfigurace spuštění programově, můžete použít [RunConfig. Save ()](/python/api/azureml-core/azureml.core.runconfiguration?preserve-view=true&view=azure-ml-py#&preserve-view=truesave-path-none--name-none--separate-environment-yaml-false-) a uložit ho jako soubor RunConfig.
        >
        > Další příklady souborů RunConfig naleznete v tématu [https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/) .
    
        Další informace najdete v tématu [AZ ml Run odeslání-Script](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

    ---

* Monitoruje stav spuštění.

    # <a name="python"></a>[Python](#tab/python)
    
    * Získejte stav spuštění s [`get_status()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-status--) metodou.
    
        ```python
        print(notebook_run.get_status())
        ```
    
    * Chcete-li získat ID spuštění, dobu provádění a další podrobnosti o spuštění, použijte [`get_details()`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-details--) metodu.
    
        ```python
        print(notebook_run.get_details())
        ```
    
    * Po úspěšném dokončení spuštění použijte [`complete()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecomplete--set-status-true-) metodu k označení jako dokončenou.
    
        ```python
        notebook_run.complete()
        print(notebook_run.get_status())
        ```
    
    * Použijete `with...as` -li vzor návrhu Pythonu, bude spuštění při nedostatku rozsahu automaticky označen jako dokončené. Nemusíte ručně označit rutinu Run jako dokončenou.
        
        ```python
        with exp.start_logging() as notebook_run:
            notebook_run.log(name="message", value="Hello from run!")
            print(notebook_run.get_status())
        
        print(notebook_run.get_status())
        ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    * Chcete-li zobrazit seznam spuštění experimentu, použijte následující příkaz. Nahraďte `experiment` názvem experimentu:
    
        ```azurecli-interactive
        az ml run list --experiment-name experiment
        ```
    
        Tento příkaz vrátí dokument JSON se seznamem informací o běhu pro tento experiment.
    
        Další informace najdete v tématu [AZ ml experiment list](/cli/azure/ext/azure-cli-ml/ml/experiment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).
    
    * Chcete-li zobrazit informace o konkrétním spuštění, použijte následující příkaz. Nahraďte `runid` ID běhu:
    
        ```azurecli-interactive
        az ml run show -r runid
        ```
    
        Tento příkaz vrátí dokument JSON se seznamem informací o běhu.
    
        Další informace najdete v tématu [AZ ml Run show](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).
    
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Zobrazení vašich běhů v studiu: 
    
    1. Přejděte na kartu **experimenty** .
    
    1. Výběrem **všech experimentů** zobrazíte všechna spuštění v experimentu nebo výběrem **všech běhů** zobrazíte všechna spuštění odeslaná v pracovním prostoru.
    
        Na stránce **všechny běhy** můžete filtrovat seznam spuštění podle značek, experimentů, cíle výpočtů a dalších pro lepší uspořádání a rozsah práce.  
    
    1. Proveďte úpravy stránky tak, že vyberete možnost spustit pro porovnání, přidávání grafů nebo použití filtrů. Tyto změny si můžete uložit jako **vlastní zobrazení** , abyste se mohli snadno vrátit k práci. Uživatelé s oprávněními v pracovním prostoru mohou upravit nebo zobrazit vlastní zobrazení. Můžete také sdílet vlastní zobrazení se členy týmu pro lepší spolupráci, a to tak, že vyberete **zobrazení sdílení**.   
    
        :::image type="content" source="media/how-to-manage-runs/custom-views.gif" alt-text="Snímek obrazovky: Vytvoření vlastního zobrazení":::
    
    1. Chcete-li zobrazit protokoly spuštění, vyberte konkrétní spuštění a na kartě **výstupy + protokoly** můžete najít diagnostické a chybové protokoly pro svůj běh.
    
    ---

## <a name="run-description"></a>Popis běhu 

Popis spuštění lze přidat ke spuštění a poskytnout tak více kontextu a informace ke spuštění. Můžete také vyhledat tyto popisy ze seznamu spuštění a přidat popis spuštění jako sloupec v seznamu spuštění. 

Přejděte na stránku s **podrobnostmi o spuštění** pro svůj běh a výběrem ikony upravit nebo tužka přidejte, upravte nebo odstraňte popisy pro vaši práci. Chcete-li zachovat změny v seznamu spuštění, uložte změny do stávajícího vlastního zobrazení nebo do nového vlastního zobrazení. Pro popisy spuštění se podporuje formát Markdownu, který umožňuje vkládání a hloubkové propojování obrázků, jak vidíte níže.

:::image type="content" source="media/how-to-manage-runs/rundescription.gif" alt-text="Snímek obrazovky: vytvoření popisu spuštění"::: 
    

## <a name="tag-and-find-runs"></a>Označení a hledání spuštění

V Azure Machine Learning můžete použít vlastnosti a značky, které vám pomůžou organizovat a dotazovat vaše běhy na důležité informace.

* Přidat vlastnosti a značky

    # <a name="python"></a>[Python](#tab/python)
    
    Chcete-li přidat hledaná metadata k vašim běhům, použijte [`add_properties()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueadd-properties-properties-) metodu. Například následující kód přidá `"author"` vlastnost do běhu:
    
    ```Python
    local_run.add_properties({"author":"azureml-user"})
    print(local_run.get_properties())
    ```
    
    Vlastnosti jsou neměnné, takže vytvoří trvalý záznam pro účely auditování. Následující příklad kódu má za následek chybu, protože jsme už přidali `"azureml-user"` jako `"author"` hodnotu vlastnosti v předchozím kódu:
    
    ```Python
    try:
        local_run.add_properties({"author":"different-user"})
    except Exception as e:
        print(e)
    ```
    
    Na rozdíl od vlastností jsou značky proměnlivé. K přidání prohledávatelných a smysluplných informací pro uživatele experimentu použijte [`tag()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truetag-key--value-none-) metodu.
    
    ```Python
    local_run.tag("quality", "great run")
    print(local_run.get_tags())
    
    local_run.tag("quality", "fantastic run")
    print(local_run.get_tags())
    ```
    
    Můžete také přidat jednoduché řetězcové značky. Když se tyto značky objeví ve slovníku značek jako klíče, mají hodnotu `None` .
    
    ```Python
    local_run.tag("worth another look")
    print(local_run.get_tags())
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    > [!NOTE]
    > Pomocí rozhraní příkazového řádku můžete přidat nebo aktualizovat pouze značky.
    
    Chcete-li přidat nebo aktualizovat značku, použijte následující příkaz:
    
    ```azurecli-interactive
    az ml run update -r runid --add-tag quality='fantastic run'
    ```
    
    Další informace najdete v tématu [AZ ml Run Update](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Z studia můžete přidat, upravit nebo odstranit značky run. Přejděte na stránku s **podrobnostmi o spuštění** pro svůj běh a výběrem ikony upravit nebo tužka přidejte, upravte nebo odstraňte značky pro vaše běhy. Tyto značky můžete na stránce se seznamem spuštění vyhledávat i filtrovat.
    
    :::image type="content" source="media/how-to-manage-runs/run-tags.gif" alt-text="Snímek obrazovky: Přidání, úprava nebo odstranění značek run":::
    
    ---

* Vlastnosti a značky dotazu

    Dotazování můžete spustit v experimentu a vrátit seznam spuštění, který odpovídá specifickým vlastnostem a značkám.

    # <a name="python"></a>[Python](#tab/python)
    
    ```Python
    list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
    list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    Rozhraní příkazového řádku Azure podporuje dotazy [JMESPath](http://jmespath.org) , které lze použít k filtrování spuštění na základě vlastností a značek. Pokud chcete použít dotaz JMESPath s rozhraním příkazového řádku Azure CLI, zadejte ho s `--query` parametrem. Následující příklady znázorňují některé dotazy pomocí vlastností a značek:
    
    ```azurecli-interactive
    # list runs where the author property = 'azureml-user'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user']
    # list runs where the tag contains a key that starts with 'worth another look'
    az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
    # list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
    ```
    
    Další informace o dotazování na výsledky rozhraní příkazového řádku Azure najdete v tématu [dotazování výstupu příkazu Azure CLI](/cli/azure/query-azure-cli?preserve-view=true&view=azure-cli-latest).
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    1. Přejděte do seznamu  **všechny běhy** .
    
    1. Pomocí panelu hledání můžete filtrovat metadata spuštění, jako jsou značky, popisy, názvy experimentů a jméno odesílatele. Filtr značek lze také použít k filtrování značek. 
    
    ---


## <a name="cancel-or-fail-runs"></a>Zrušení nebo selhání spuštění

Pokud si všimnete omylem nebo pokud dokončení běhu trvá příliš dlouho, můžete spustit operaci.

# <a name="python"></a>[Python](#tab/python)

Chcete-li zrušit běh pomocí sady SDK, použijte [`cancel()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecancel--) metodu:

```python
src = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_run = exp.submit(src)
print(local_run.get_status())

local_run.cancel()
print(local_run.get_status())
```

Pokud se vaše spuštění dokončí, ale obsahuje chybu (například byl použit nesprávný školicí skript), můžete použít [`fail()`](/python/api/azureml-core/azureml.core.run%28class%29#fail-error-details-none--error-code-none---set-status-true-) metodu k jejímu označení jako neúspěšné.

```python
local_run = exp.submit(src)
local_run.fail()
print(local_run.get_status())
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li zrušit běh pomocí rozhraní příkazového řádku, použijte následující příkaz. Nahraďte `runid` ID běhu.

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Další informace najdete v tématu [AZ ml Run Cancel](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Chcete-li zrušit spuštění v nástroji Studio, použijte následující postup:

1. V části **experimenty** nebo **kanály** se dostanete na běžící kanál. 

1. Vyberte číslo běhu kanálu, které chcete zrušit.

1. Na panelu nástrojů vyberte **Zrušit** .

---

## <a name="create-child-runs"></a>Vytvořit podřízená spuštění

Vytvoření podřízených běhů pro seskupení souvisejících běhů, například pro různé iterace ladění parametrů.

> [!NOTE]
> Podřízená spuštění lze vytvořit pouze pomocí sady SDK.

Tento příklad kódu používá `hello_with_children.py` skript k vytvoření dávky pěti podřízených spuštění v rámci odeslaného běhu pomocí [`child_run()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truechild-run-name-none--run-id-none--outputs-none-) metody:

```python
!more hello_with_children.py
src = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_run = exp.submit(src)
local_run.wait_for_completion(show_output=True)
print(local_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> Při přesunu z oboru jsou podřízená spuštění automaticky označena jako dokončená.

Chcete-li vytvořit mnoho podřízených spuštění efektivně, použijte [`create_children()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-children-count-none--tag-key-none--tag-values-none-) metodu. Vzhledem k tomu, že při každém vytváření dojde k síťovému volání, vytvoření dávky spuštění je efektivnější než jejich vytvoření po jednom.

### <a name="submit-child-runs"></a>Odeslat podřízená spuštění

Podřízené běhy lze také odeslat z nadřazeného spuštění. To umožňuje vytvářet hierarchie nadřazených a podřízených spuštění. Nemůžete vytvořit podřízené spuštění bez nadřazeného prvku: i když nadřazený běh neprovede žádné akce, ale spustí podřízené spuštění, je stále nutné vytvořit hierarchii. Stav všech spuštění je nezávisle: nadřazený objekt může být v `"Completed"` úspěšném stavu, i když byl jeden nebo více podřízených spuštění zrušeno nebo selhalo.  

Můžete chtít, aby vaše podřízená spuštění používala jinou konfiguraci spuštění než v nadřazeném spuštění. Můžete například použít méně náročnou konfiguraci založenou na PROCESORech pro nadřazený objekt při použití konfigurací založených na GPU pro vaše děti. Dalším běžným přáním je předat každý podřízený objekt různým argumentům a datům. Pro přizpůsobení podřízeného spuštění vytvořte `ScriptRunConfig` objekt pro podřízený běh. Níže uvedený kód provede následující:

- Načtěte výpočetní prostředek s názvem `"gpu-cluster"` z pracovního prostoru. `ws`
- Iteruje přes různé hodnoty argumentů, které se mají předat podřízeným `ScriptRunConfig` objektům.
- Vytvoří a odešle nové podřízené spuštění pomocí vlastního výpočetního prostředku a argumentu.
- Blokuje až do dokončení všech podřízených spuštění.

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig

compute_target = ws.compute_targets["gpu-cluster"]

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], compute_target=compute_target)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

Chcete-li vytvořit mnoho podřízených spuštění se stejnou konfigurací, argumenty a vstupy efektivně, použijte [`create_children()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-children-count-none--tag-key-none--tag-values-none-) metodu. Vzhledem k tomu, že při každém vytváření dojde k síťovému volání, vytvoření dávky spuštění je efektivnější než jejich vytvoření po jednom.

V rámci podřízeného spuštění můžete zobrazit ID nadřazeného spuštění:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Dotaz na podřízená spuštění

Chcete-li zadat dotaz na podřízená spuštění konkrétního nadřazeného objektu, použijte [`get_children()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) metodu. ``recursive = True``Argument slouží k dotazování vnořené stromové struktury podřízených a podřízené.

```python
print(parent_run.get_children())
```

### <a name="log-to-parent-or-root-run"></a>Přihlášení k nadřazenému nebo kořenovému spuštění

Pole můžete použít `Run.parent` pro přístup ke spuštění, které spustilo aktuální podřízené spuštění. Běžný případ použití je v případě, že chcete konsolidovat výsledky protokolu na jednom místě. Všimněte si, že podřízené běhy se spouštějí asynchronně a není nijak zaručeno řazení a synchronizace nad rámec možnosti nadřazeného objektu, aby čekal na dokončení jeho podřízených běhů.

```python
# in child (or even grandchild) run

def root_run(self : Run) -> Run :
    if self.parent is None : 
        return self
    return root_run(self.parent)

current_child_run = Run.get_context()
root_run(current_child_run).log("MyMetric", f"Data from child run {current_child_run.id}")

```

## <a name="example-notebooks"></a>Příklady poznámkových bloků

Následující poznámkové bloky ukazují koncepty v tomto článku:

* Další informace o rozhraních API pro protokolování najdete v [poznámkovém bloku rozhraní API pro protokolování](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb).

* Další informace o správě běžících v sadě Azure Machine Learning SDK najdete v [poznámkovém bloku spravovat běhy](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb).

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak zaznamenat metriky pro vaše experimenty, najdete v tématu [metriky protokolu během školicích běhů](how-to-track-experiments.md).
* Informace o tom, jak monitorovat prostředky a protokoly z Azure Machine Learning, najdete v tématu [monitorování Azure Machine Learning](monitor-azure-machine-learning.md).
