---
title: Kurz k funkcím služby Azure Machine Learning ve verzi Preview – Rozhraní příkazového řádku | Microsoft Docs
description: Tento kurz vás od provede všemi kroky nezbytnými k dokončení klasifikace Iris kompletně v rozhraní příkazového řádku.
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 10/15/2017
ms.openlocfilehash: 2d7965c0028e8f2e82db2e3ddd4eed6e20f4f443
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-classifying-iris-using-the-command-line-interface"></a>Kurz: Klasifikace Iris pomocí rozhraní příkazového řádku
Služby Azure Machine Learning (verze Preview) představují integrované, komplexní řešení datové vědy a pokročilé analýzy pro profesionální datové vědce, které slouží k přípravě dat, vývoji experimentů a nasazování modelů na úrovni cloudu.

V tomto kurzu zjistíte, jak pomocí nástrojů rozhraní příkazového řádku ve funkcích služby Azure Machine Learning ve verzi Preview provádět následující úlohy: 
> [!div class="checklist"]
> * Nastavení účtu Experimentování a vytvoření pracovního prostoru
> * Vytvoření projektu
> * Odeslání experimentu do několika cílových výpočetních prostředí
> * Zvýšení úrovně a registrace trénovaného modelu
> * Nasazení webové služby sloužící ke stanovení skóre nových dat

## <a name="prerequisites"></a>Požadavky
Pro absolvování tohoto kurzu potřebujete:
- Přístup k předplatnému Azure a oprávnění k vytváření prostředků v tomto předplatném. 
  
  Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

- Nainstalovanou aplikaci Azure Machine Learning Workbench, jak je popsáno v tématu [Rychlý start: Instalace a spuštění služeb Azure Machine Learning](quickstart-installation.md). 

  >[!IMPORTANT]
  >Nevytvářejte účty služeb Azure Machine Learning, protože to provedete v tomto článku pomocí rozhraní příkazového řádku.
 
## <a name="getting-started"></a>Začínáme
Rozhraní příkazového řádku (CLI) služby Azure Machine Learning umožňuje provádět veškeré úlohy nezbytné pro komplexní pracovní postup datových věd. Přístup k nástrojům rozhraní příkazového řádku můžete získat následujícími způsoby:

### <a name="option-1-launch-azure-ml-cli-from-azure-ml-workbench-log-in-dialog-box"></a>Možnost 1. Spuštění Azure ML CLI z přihlašovacího dialogového okna aplikace Azure ML Workbench
Při prvním spuštění aplikace Azure ML Workbench se po přihlášení v případě, že ještě nemáte přístup k účtu Experimentování, zobrazí následující obrazovka:

![nenašel se žádný účet](media/tutorial-iris-azure-cli/no_account_found.png)

Kliknutím na odkaz **Okno příkazového řádku** v dialogovém okně otevřete okno příkazového řádku.

### <a name="option-2-launch-azure-ml-cli-from-azure-ml-workbench-app"></a>Možnost 2. Spuštění Azure ML CLI z aplikace Azure ML Workbench
Pokud již máte přístup k účtu Experimentování, můžete se úspěšně přihlásit. Pak můžete otevřít okno příkazového řádku kliknutím na nabídku **Soubor** --> **Otevřít příkazový řádek**.

### <a name="option-3-enable-azure-ml-cli-in-an-arbitrary-command-line-window"></a>Možnost 3. Zapnutí Azure ML CLI v libovolném okně příkazového řádku
Azure ML CLI můžete zapnout také v jakémkoli okně příkazového řádku. Provedete to tak, že otevřete příkazové okno a zadáte následující příkazy:

```sh
# Windows Command Prompt
set PATH=%LOCALAPPDATA%\amlworkbench\Python;%LOCALAPPDATA%\amlworkbench\Python\Scripts;%PATH%

# Windows PowerShell
$env:Path = $env:LOCALAPPDATA+"\amlworkbench\Python;"+$env:LOCALAPPDATA+"\amlworkbench\Python\Scripts;"+$env:Path

# macOS Bash Shell
PATH=$HOME/Library/Caches/AmlWorkbench/Python/bin:$PATH
```
Pokud chcete změnu nastavit jako trvalou, ve Windows můžete použít `SETX`. V systému macOS můžete použít `setenv`.

>[!TIP]
>Nastavením předchozích proměnných prostředí můžete Azure CLI zapnout v oblíbeném okně terminálu.

## <a name="step-1-log-in-to-azure"></a>Krok 1. Přihlášení k Azure
Prvním krokem je otevření rozhraní příkazového řádku z aplikace AMLWorkbench (Soubor > Otevřít příkazový řádek). Tím se zajistí používání správného prostředí Python a dostupnost příkazů ML CLI. 

Teď ve svém rozhraní příkazového řádku můžete nastavit správný kontext pro přístup k prostředkům Azure a jejich správu.
 
```azure-cli
# log in
$ az login

# list all subscriptions
$ az account list -o table

# set the current subscription
$ az account set -s <subscription id or name>
```

## <a name="step-2-create-a-new-azure-machine-learning-experimentation-account-and-workspace"></a>Krok 2. Vytvoření nového účtu a pracovního prostoru Experimentování ve službě Azure Machine Learning

V tomto kroku vytvoříte nový účet Experimentování a nový pracovní prostor. Další podrobnosti o účtech a pracovních prostorech Experimentování najdete v tématu [Koncepty služby Azure Machine Learning](overview-general-concepts.md).

> [!NOTE]
> Účty Experimentování vyžadují účet úložiště, který slouží k ukládání výstupů spuštění experimentů. Název účtu úložiště musí být v Azure globálně jedinečný, protože je k němu přidružená adresa URL. Pokud nezadáte existující účet úložiště, vytvoří se nový účet úložiště a jako název se použije název vašeho účtu Experimentování. Nezapomeňte použít jedinečný název, jinak se zobrazí chyba, například _Účet úložiště s názvem \<název_účtu_úložiště> se už používá._ Případně můžete pomocí argumentu `--storage` zadat existující účet úložiště.

```azure-cli
# create a resource group 
$ az group create --name <resource group name> --location <supported Azure region>

# create a new experimentation account with a new storage account
$ az ml account experimentation create --name <experimentation account name> --resource-group <resource group name>

# create a new experimentation account with an existing storage account
$ az ml account experimentation create --name <experimentation account name>  --resource-group <resource group name> --storage <storage account Azure Resource ID>

# create a workspace in the experimentation account
az ml workspace create --name <workspace name> --account <experimentation account name> --resource-group <resource group name>
```

## <a name="step-2a-optional-share-a-workspace-with-co-worker"></a>Krok 2.a (volitelné) Sdílení pracovního prostoru s kolegou
Tady můžete prozkoumat, jak sdílet přístup k pracovnímu prostoru s kolegou. Postup pro sdílení přístupu k účtu Experimentování nebo projektu bude stejný. Bude potřeba jenom změnit způsob získání příslušného ID prostředku Azure.

```azure-cli
# find the workspace Azure Resource ID
$az ml workspace show --name <workspace name> --account <experimentation account name> --resource-group <resource group name>

# add Bob to this workspace as a new owner
$az role assignment create --assignee bob@contoso.com --role owner --scope <workspace Azure Resource ID>
```

> [!TIP]
> `bob@contoso.com` ve výše uvedeném příkazu musí být platná identita Azure v adresáři, do kterého patří aktuální předplatné.

## <a name="step-3-create-a-new-project"></a>Krok 3. Vytvoření nového projektu
Naším dalším krokem je vytvoření nového projektu. Existuje několik způsobů, jak začít s novým projektem.

### <a name="create-a-new-blank-project"></a>Vytvoření nového prázdného projektu

```azure-cli
# create a new project
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path>
```

### <a name="create-a-new-project-with-a-default-project-template"></a>Vytvoření nového projektu pomocí výchozí šablony projektu
Nový projekt můžete vytvořit pomocí výchozí šablony.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template
```

### <a name="create-a-new-project-associated-with-a-cloud-git-repository"></a>Vytvoření nového projektu přidruženého ke cloudovému úložišti Git
Můžete vytvořit nový projekt přidružený k úložišti VSTS (Visual Studio Team Services) Git. Při každém odeslání experimentu se do vzdáleného úložiště Git uloží snímek celé složky projektu. Další podrobnosti najdete v tématu [Použití úložiště Git s projektem aplikace Azure Machine Learning Workbench](using-git-ml-project.md).

> [!NOTE]
> Azure Machine Learning podporuje pouze prázdná úložiště Git vytvořená ve VSTS.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --repo <VSTS repo URL>
```
> [!TIP]
> Pokud se zobrazuje chyba „Adresa URL úložiště je možná neplatná nebo uživatel možná nemá přístup“, můžete ve VSTS vytvořit token zabezpečení (v části _Zabezpečení_ v nabídce _Přidat tokeny PAT_) a při vytváření projektu použít argument `--vststoken`. 

### <a name="sample_create"></a>Vytvoření nového projektu z ukázky
V tomto příkladu vytvoříte nový projekt s použitím ukázkového projektu jako šablony.

```azure-cli
# List the project samples, find the Classifying Iris sample
$ az ml project sample list

# Create a new project from the sample
az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template-url https://github.com/MicrosoftDocs/MachineLearningSamples-Iris
```
Po vytvoření projektu pomocí příkazu `cd` přejděte do adresáře projektu.

## <a name="step-4-run-the-training-experiment"></a>Krok 4. Spuštění výukového experimentu 
V následujících krocích se předpokládá, že máte projekt s ukázkou Iris (viz [Vytvoření nového projektu z online ukázky](#sample_create)).

### <a name="prepare-your-environment"></a>Příprava prostředí 
Pro ukázku Iris musíte nainstalovat balíček matplotlib.

```azure-cli
$ pip install matplotlib
```

###  <a name="submit-the-experiment"></a>Odeslání experimentu

```azure-cli
# Execute the file
$ az ml experiment submit --run-configuration local iris_sklearn.py
```

### <a name="iterate-on-your-experiment-with-descending-regularization-rates"></a>Iterace experimentu s klesajícími mírami regularizace
S trochou představivosti je jednoduché sestavit skript Pythonu, který odesílá experimenty s různými mírami regularizace. (Možná bude potřeba soubor upravit tak, aby odkazoval na správnou cestu k projektu.)

```azure-cli
$ python run.py
```

## <a name="step-5-view-run-history"></a>Krok 5. Zobrazení historie spuštění
Následující příkaz vypíše všechna předchozí provedená spuštění. 

```azure-cli
$ az ml history list -o table
```
Po spuštění předchozího příkazu se zobrazí seznam všech spuštění patřících k tomuto projektu. Jak vidíte, zobrazí se také metriky přesnosti a míry regularizace. Díky tomu je snadné v seznamu určit nejlepší spuštění.

## <a name="step-5a-view-attachment-created-by-a-given-run"></a>Krok 5.a Zobrazení přílohy vytvořené daným spuštěním 
Pokud chcete zobrazit přílohu přidruženou k danému spuštění, můžete použít příkaz info historie spuštění. Vyhledejte ID konkrétního spuštění z předchozího seznamu.

```azure-cli
$ az ml history info --run <run id> --artifact driver_log
```

Pokud chcete stáhnout artefakty spuštění, můžete použít následující příkaz:

```azure-cli
# Stream a given attachment 
$ az ml history info --run <run id> --artifact <artifact location>
```

## <a name="step-6-promote-artifacts-of-a-run"></a>Krok 6. Zvýšení úrovně artefaktů spuštění 
Jedno ze spuštění má lepší hodnotu AUC a proto by se mělo použít při vytváření hodnoticí webové služby pro nasazení do produkčního prostředí. Pokud to chcete provést, nejprve musíte zvýšit úroveň artefaktů na prostředek.

```azure-cli
$ az ml history promote --run <run id> --artifact-path outputs/model.pkl --name model.pkl
```

Tím se v adresáři vašeho projektu vytvoří složka `assets` se souborem `model.pkl.link`. Tento soubor slouží jako odkaz na prostředek se zvýšenou úrovní.

## <a name="step-7-download-the-files-to-be-operationalized"></a>Krok 7. Stažení souborů, které se mají zprovoznit
Stáhněte model se zvýšenou úrovní, abyste pomocí něj mohli vytvořit webovou službu pro předpovědi. 

```azure-cli
$ az ml asset download --link-file assets\pickle.link -d asset_download
```

## <a name="step-8-set-up-your-model-management-environment"></a>Krok 8. Nastavení prostředí pro správu modelů 
Vytvořte prostředí pro nasazení webových služeb. Webovou službu můžete spouštět na místním počítači pomocí Dockeru. Případně ji můžete nasadit do clusteru ACS pro účely operací ve velkém měřítku. 

```azure-cli
# Create new local operationalization environment
$ az ml env setup -l <supported Azure region> -n <env name>
# Once setup is complete, set your environment for current context
$ az ml env set -g <resource group name> -n <env name>
```

## <a name="step-9-create-a-model-management-account"></a>Krok 9. Vytvoření účtu služby Správa modelů 
Účet služby Správa modelů je nezbytný pro nasazení a sledování modelů v produkčním prostředí. 

```azure-cli
$ az ml account modelmanagement create -n <model management account name> -g <resource group name> -l <supported Azure region>
```

## <a name="step-10-create-a-web-service"></a>Krok 10. Vytvoření webové služby
Vytvořte webovou službu, která vrací předpověď na základě modelu, který jste nasadili. 

```azure-cli
$ az ml service create realtime -m asset_download/model.pkl -f score_iris.py -r python –n <web service name>
```

## <a name="step-11-run-the-web-service"></a>Krok 11. Spuštění webové služby
S použitím ID webové služby z výstupu z předchozího kroku zavolejte webovou službu a otestujte ji. 

```azure-cli
# Get web service usage infomration
$ az ml service usage realtime -i <web service id>

# Call the web service with the run command:
$ az ml service run realtime -i <web service id> -d <input data>
```

## <a name="step-12-deleting-all-the-resources"></a>Krok 12. Odstranění všech prostředků 
Ukončeme tento kurz odstraněním všech prostředků, které jsme vytvořili, pokud s nimi nechcete pracovat dál. 

Provedete to tak, že odstraníte skupinu prostředků, ve které se prostředky nacházejí. 

```azure-cli
az group delete --name <resource group name>
```

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak pomocí služby Azure Machine Learning provádět následující úlohy: 
> [!div class="checklist"]
> * Nastavení účtu Experimentování a vytvoření pracovního prostoru
> * Vytváření projektů
> * Odesílání experimentů do několika cílových výpočetních prostředí
> * Zvýšení úrovně a registrace trénovaného modelu
> * Vytvoření účtu služby Správa modelů pro účely správy modelů
> * Vytvoření prostředí pro nasazování webových služeb
> * Nasazení webové služby a určování skóre s využitím nových dat
