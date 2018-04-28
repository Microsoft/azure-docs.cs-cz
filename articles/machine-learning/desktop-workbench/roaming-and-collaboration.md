---
title: Roaming a spolupráce v Azure Machine Learning Workbench | Microsoft Docs
description: Postup nastavení roamingu a spolupráce v nástroji Machine Learning Workbench.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: b587f5dcc9558ec52b85e4b53dae0e31ad475a4e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Roaming a spolupráce v Azure Machine Learning Workbench
Tento článek popisuje, jak můžete použít Azure Machine Learning Workbench nastavit projekty pro roaming mezi počítači a spolupráci s členy týmu. 

Když vytvoříte projekt Azure Machine Learning, který obsahuje odkaz vzdálené úložiště (úložiště) Git, metadata projektu a snímky jsou uložené v cloudu. Přístup k projektu z jiného počítače (roaming) můžete použít odkaz cloudu. Můžete také spolupráci s členy týmu pomocí bude mít přístup k projektu. 

## <a name="prerequisites"></a>Požadavky
1. Nainstalujte strojového učení Workbench aplikace. Ujistěte se, zda máte přístup k účtu Azure Machine Learning experimenty. Další informace najdete v tématu [Průvodce instalací](../service/quickstart-installation.md).

2. Přístup k [Visual Studio Team Services](https://www.visualstudio.com) (Team Services) a poté vytvořit úložišti propojení projektu pro. Další informace najdete v tématu [pomocí úložiště Git s projektem Machine Learning Workbench](using-git-ml-project.md).

## <a name="create-a-new-machine-learning-project"></a>Vytvoření nového projektu Machine Learning
Otevřete Machine Learning Workbench a pak vytvořte nový projekt (například projektu s názvem iris). V **adresu URL úložiště GIT Visualstudio.com** zadejte platnou adresu URL pro úložiště Team Services Git. 

> [!IMPORTANT]
> Pokud si zvolíte Šablona prázdného projektu, úložiště Git, kterou chcete použít už možná máte hlavní větve. Machine Learning jednoduše provede klonování hlavní větve místně. Přidá aml_config složky a další metadata soubory projektu do složky místní projektu. 
>
> Pokud zvolíte jakékoli další šablony projektu, vaše úložiště Git *nelze* už máte hlavní větve. Pokud ano, zobrazí se chyba. Alternativou je použít `az ml project create` příkaz pro vytvoření projektu, s `--force` přepínače. To odstraní soubory v původní hlavní větve a nahradí je nové soubory v šabloně, který zvolíte.

Po vytvoření projektu se odešlete několik běží na všech skriptů, které jsou v projektu. Tato akce provede stavu projektu do vzdáleného úložiště Git historie spouštění větve. 

> [!NOTE] 
> Pouze skript se spustí aktivační událost potvrzení do historie spouštění větve. Data připravená data spuštění a poznámkového bloku běží nemáte aktivovat snímky projektu ve větvi historie spouštění.

Pokud jste nastavili Git ověřování, můžou také fungovat v hlavní větve. Nebo můžete vytvořit nové větve. 

Příklad: 
```
# Check current repo status.
$ git status

# Stage all changes in the current repo.
$ git add -A

# Commit changes.
$ git commit -m "my commit fixes this weird bug!"

# Push to the remote repo.
$ git push origin master
```

## <a name="roaming"></a>Roaming
<a name="roaming"></a>

### <a name="open-machine-learning-workbench-on-a-second-computer"></a>Otevřete Machine Learning Workbench na druhý počítač
Po úložiště Team Services Git je propojená s projektu, můžete přístup k projektu iris z libovolného počítače, který má Machine Learning Workbench nainstalovaná. 

Chcete-li získat přístup k projektu iris na jiném počítači, musíte přihlásit k aplikaci pomocí stejných přihlašovacích údajů, které jste použili k vytvoření projektu. Také musíte být ve stejné účet Machine Learning experimentování a pracovního prostoru. Projekt iris je uvedena abecedně s jinými projekty v pracovním prostoru. 

### <a name="download-the-project-on-a-second-computer"></a>Stáhněte si projekt na druhý počítač
V otevřeném pracovním prostoru v druhém počítači, na ikonu přiléhající k projektu iris se liší od ikonu typické složky. Ikona stažení označuje, že obsah projektu jsou v cloudu, a zda projektu lze stáhnout do aktuálního počítače. 

![Vytvoření projektu](./media/roaming-and-collaboration/downloadable-project.png)

Vyberte projekt iris zahájíte stahování. Po dokončení stahování je připraven na druhý počítač přístup k projektu. 

V systému Windows, se nachází v C:\Users projektu\\< uživatelské jméno\>\Documents\AzureML.

V systému macOS, se nachází v /home/ projektu\<uživatelské jméno \> /dokumenty/AzureML.

V budoucí verzi plánujeme rozšíření funkcí, takže můžete vybrat cílovou složku. 

> [!NOTE]
> Pokud máte složku v adresáři Machine Learning, který má přesně stejný název jako projekt, stahování se nezdaří. Chcete-li tento problém obejít, dočasně přejmenujte existující složku.


### <a name="work-on-the-downloaded-project"></a>Práce na staženého projektu 
Nově staženého projektu odráží stav projektu při posledním spuštění v projektu. Snímek stavu projektu je automaticky provedeny do historie spouštění větve v úložišti Team Services Git pokaždé, když odešlete spustit. Snímek, který je přidružen nejnovější spustit slouží k vytváření instancí projekt na druhém počítači. 
 

## <a name="collaboration"></a>Spolupráce
Můžete spolupracovat s ostatními členy týmu v projektech, které jsou propojeny s úložišti Team Services Git. Můžete přiřadit oprávnění pro uživatele pro účet Machine Learning experimentování, pracovní prostor a projekt. V současné době můžete provést příkazy Azure Resource Manager pomocí rozhraní příkazového řádku Azure. Můžete také [portál Azure](https://portal.azure.com). Další informace najdete v tématu [na portálu Azure můžete přidat uživatele](#portal).    

### <a name="use-the-command-line-to-add-users"></a>Použijte příkazový řádek pro přidání uživatelů
Jako příklad Alice je vlastníkem iris projektu. Alice chce sdílet přístup k projektu s Bob. 

Vybere Alice **soubor** nabídce a potom vybere **příkazového řádku** položku nabídky. Otevře se okno příkazového řádku s iris projektu. Alice pak můžete rozhodnout, jakou úroveň přístupu, která chce předáte Bob. Jana uděluje oprávnění spuštěním následujících příkazů:  

```azurecli
# Find the Resource Manager ID of the Experimentation account.
az ml account experimentation show --query "id"

# Add Bob to the Experimentation account as a Contributor.
# Bob now has read/write access to all workspaces and projects under the account by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <Experimentation account Resource Manager ID>

# Find the Resource Manager ID of the workspace.
az ml workspace show --query "id"

# Add Bob to the workspace as an Owner.
# Bob now has read/write access to all projects under the workspace by inheritance. Bob can invite or remove other users.
az role assignment create --assignee bob@contoso.com --role Owner --scope <workspace Resource Manager ID>
```

Po přiřazení role buď přímo nebo prostřednictvím dědičnosti, uvidí Bob na projekt v seznamu Machine Learning Workbench projektu. Bob může být potřeba restartovat aplikaci zobrazíte projektu. Bob pak můžete stáhnout projektu, jak je popsáno v [Roaming](#roaming)a začít spolupracovat s Alice. 

Historie spouštění pro všechny uživatele, kteří spolupracovat na projektu se zaměřuje na stejné vzdáleného úložiště Git. Když Alice provede spuštění, Bob vidí spustit v části historie spouštění projektu v aplikaci Machine Learning Workbench. Bob můžete také obnovit projekt stavu všech běh, včetně spustí, kteří spustili Alice. 

Při sdílení vzdáleného úložiště Git pro projekt, může Alice a Bob také spolupracovat v hlavní pobočce. V případě potřeby můžou taky vytvořit osobní větve a používat žádosti o přijetí změn Git a sloučí spolupracovat. 

### <a name="use-the-azure-portal-to-add-users"></a>Na portálu Azure můžete přidat uživatele
<a name="portal"></a>

Účty počítače Learning experimentování, pracovní prostor a projekty jsou prostředky Azure Resource Manager. Chcete-li přiřadit role, můžete použít **řízení přístupu** na odkaz v [portál Azure](https://portal.azure.com). 

Najít prostředek, který chcete přidat uživatele do pomocí **všechny prostředky** zobrazení. Vyberte **přístup k ovládacímu prvku (IAM)** propojit a potom vyberte **přidat uživatele**. 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>Ukázkový pracovní postup spolupráce
Pro ilustraci pracovního postupu spolupráce, projděme příklad. Zaměstnanci contoso Alice a Bob chcete spolupracovat na datové vědě projektu pomocí Machine Learning Workbench. Svoji identitu patřit do stejné klienta Contoso Azure Active Directory (Azure AD). Tady jsou kroky Alice a Bob trvat:

1. Alice vytvoří prázdný úložiště Git v projektu Team Services. Projekt Team Services by měly mít předplatné Azure, který je vytvořen v klientovi Contoso Azure AD. 

2. Alice vytvoří účet Machine Learning experimentování, pracovní prostor a projekt Machine Learning Workbench na svém počítači. Když uživatel vytvoří projekt, zadá adresu URL úložiště Team Services Git.

3. Alice začne pracovat na projektu. Vytvoří některé skripty a provede několik spustí. Při každém spuštění se snímek složce celý projekt posune jako potvrzení změn na větev historie spouštění úložiště Git služby týmu, který vytváří Machine Learning Workbench automaticky.

4. Alice je radostí s probíhající práce. Chce potvrzení jeho změn v místní hlavní větve a vložit je do hlavní větve úložiště Team Services Git. S projektem otevřeným v nástroji Machine Learning Workbench se otevře okno příkazového řádku a potom zadání těchto příkazů:
    
    ```sh
    # Verify that the Git remote is pointing to the Team Services Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Stage all changes.
    $ git add -A

    # Commit changes with a comment.
    $ git commit -m "this is a good milestone"

    # Push the commit to the master branch of the remote Git repo in Team Services.
    $ git push
    ```

5. Alice přidá Roberta do pracovního prostoru jako Přispěvatel. Jana můžete to udělat na portálu Azure nebo pomocí `az role assignment` příkaz, jak je uvedeno výše. Alice také uděluje oprávnění k úložišti Team Services Git Bob čtení a zápis.

6. Bob se přihlásí k Machine Learning Workbench ve svém počítači. Si můžete zobrazit pracovní prostor, který Alice sdílet s ním. Si můžete zobrazit projektu iris uvedené v části tohoto pracovního prostoru. 

7. Bob vybere název projektu. Projekt se stáhne do svého počítače.
    * Kopie snímků nejnovější spuštění, které se zaznamená do historie spouštění jsou soubory staženého projektu. Nejsou poslední potvrzení na hlavní větve.
    * Složka místní projektu je nastavena do hlavní větve, s unstaged změny.

8. Bob můžete vyhledat spustí, které byly provedeny podle Alice. Si můžete obnovit snímky žádné dřívější spustí.

9. Bob chce získat nejnovější změny, které instaluje Alice a pak začít pracovat na jinou firemní pobočce. V nástroji Machine Learning Workbench Bob otevře okno příkazového řádku a spouští následující příkazy:

    ```sh
    # Verify that the Git remote is pointing to the Team Services Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Get the latest commit in the Team Services Git master branch and overwrite current files.
    $ git pull --force

    # Create a new local branch named "bob" so that Bob's work is done in the "bob" branch
    $ git checkout -b bob
    ```

10. Bob upraví projektu a odešle nové spuštění. Změny jsou probíhají bob větev. Spustí Boba také budou zobrazeny Alici.

11. Robert je připraven k odešlete své změny do vzdáleného úložiště Git. Aby se zabránilo konfliktu s hlavní větve, kde Alice funguje, vynutí Bob svou práci na nové vzdálené větve, který se nazývá také bob.

    ```sh
    # Verify that the current branch is "bob," and that it has unstaged changes.
    $ git status
    
    # Stage all changes.
    $ git add -A

    # Commit the changes with a comment.
    $ git commit -m "I found a cool new trick."

    # Create a new branch on the remote Team Services Git repo, and then push the changes.
    $ git push origin bob
    ```

12. Alice říct o nástrojů nové efektu ve svém kódu, Bob vytvoří žádost o přijetí změn na vzdálené úložiště Git z bob větve do hlavní větve. Alice pak sloučit žádost o přijetí změn do hlavní větve.

## <a name="next-steps"></a>Další postup
- Další informace o [pomocí úložiště Git s projektem Machine Learning Workbench](using-git-ml-project.md).
