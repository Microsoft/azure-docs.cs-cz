---
title: Roaming a spolupráce v Azure Machine Learning Workbench | Dokumentace Microsoftu
description: Další informace o nastavení roamingu a spolupráci v aplikaci Machine Learning Workbench.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 0abc5e34d2bfa1cf2a9fc0569831e21ed295891c
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296495"
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Roaming a spolupráce v Azure Machine Learning Workbench
Tento článek popisuje, jak můžete aplikaci Azure Machine Learning Workbench k nastavení projektů pro roaming mezi počítači a Spolupracujte s členy týmu. 

Když vytvoříte projekt Azure Machine Learning, která má vzdálený odkaz Git úložišti (úložišti), metadata projektu a snímky se ukládají v cloudu. Odkaz na cloud můžete použít pro přístup k projektu z jiného počítače (roaming). Spolupracujte s členy týmu můžete také tak, že jim udělíte přístup k projektu. 

## <a name="prerequisites"></a>Požadavky
1. Instalace Machine Learning Workbench aplikace. Ujistěte se, že máte přístup k účtu experimentování ve službě Azure Machine Learning. Další informace najdete v tématu [Průvodce instalací](../service/quickstart-installation.md).

2. Přístup [Azure DevOps](https://www.visualstudio.com) a pak vytvořte úložiště k propojení vašeho projektu. Další informace najdete v tématu [pomocí úložiště Git s projektem aplikace Machine Learning Workbench](using-git-ml-project.md).

## <a name="create-a-new-machine-learning-project"></a>Vytvoření nového projektu Machine Learning
Otevřete aplikaci Machine Learning Workbench a pak vytvořte nový projekt (například projekt s názvem iris). V **adresy URL úložiště GIT Visualstudio.com** zadejte platnou adresu URL pro úložiště Azure DevOps Git. 

> [!IMPORTANT]
> Pokud se rozhodnete šabloně prázdného projektu, pravděpodobně již úložiště Git, kterou chcete použít hlavní větve. Machine Learning jednoduše duplicity místně v hlavní větvi. Přidá aml_config složky a další metadata soubory projektu do složky místní projekt. 
>
> Pokud zvolíte jakékoli další šablony projektu, úložiště Git *nelze* již mít hlavní větev. Pokud ano, zobrazí se chyba. Alternativou je použít `az ml project create` příkaz pro vytvoření projektu, se `--force` přepnout. To odstraní soubory v původní hlavní větvi a nahradí je novými soubory v šabloně, kterou zvolíte.

Po vytvoření projektu, odešlete několik spustí všechny skripty, které jsou v projektu. Tato akce potvrzení stavu projektu do větve historii spuštění vzdálené úložiště Git. 

> [!NOTE] 
> Pouze skript se spouští aktivační událost potvrzení změn do větve historie spuštění. Spuštění a poznámkového bloku pro přípravu dat spuštění není aktivovat snímky projektu ve větvi historie spuštění.

Pokud jste nastavili ověřování Git, můžete také pracovat v hlavní větvi. Nebo můžete vytvořit novou větev. 

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

### <a name="open-machine-learning-workbench-on-a-second-computer"></a>Otevřete aplikaci Machine Learning Workbench na druhém počítači
Po úložiště Git v Azure DevOps je propojený s projektem, mají přístup k projektu iris z libovolného počítače, který má nainstalovanou aplikaci Machine Learning Workbench. 

Pro přístup k projektu iris na jiném počítači, musíte se do aplikace přihlásit pomocí stejných přihlašovacích údajů, které jste použili k vytvoření projektu. Také musíte být v pracovním prostoru a stejný účet experimentování ve službě Machine Learning. Projektu iris je označené podle abecedy jiné projekty v pracovním prostoru. 

### <a name="download-the-project-on-a-second-computer"></a>Stáhněte si projekt na druhém počítači
Při otevření v druhém počítači pracovní prostor se liší od ikony typické složky na ikonu vedle projektu iris. Ikona stahování označuje, že se obsah projektu v cloudu. proto, že projekt je připravený ke stažení na aktuálním počítači. 

![Vytvoření projektu](./media/roaming-and-collaboration/downloadable-project.png)

Vyberte projekt iris zahájíte stahování. Po dokončení stahování projektu přistupovat k druhý počítač připravený. 

Ve Windows, je umístěný v C:\Users projektu\\< uživatelské jméno\>\Documents\AzureML.

V systému macOS, projekt se nachází v /home/\<uživatelské jméno \> /dokumentů/AzureML.

V budoucí verzi plánujeme vylepšit funkce, takže můžete vybrat cílovou složku. 

> [!NOTE]
> Pokud máte složku v adresáři Machine Learning, která má přesně stejný název jako projekt, stahování se nezdaří. Chcete-li tento problém obejít, přejmenujte dočasně existující složku.


### <a name="work-on-the-downloaded-project"></a>Práce na staženého projektu 
Nově staženého projektu odráží stav projektu poslední spuštění projektu. Snímek stavu projektu je automaticky provedeny do historie spouštění větve v úložišti Git v Azure DevOps pokaždé, když odešlete spuštění. Snímek, který je přidružený k poslední spuštění se používá k vytvoření instance projektu na druhém počítači. 
 

## <a name="collaboration"></a>Spolupráce
Můžete spolupracovat s ostatními členy týmu na projektech, které jsou propojeny do úložiště Git v Azure DevOps. Můžete přiřadit oprávnění pro uživatele pro účet experimentování ve službě Machine Learning, pracovní prostor a projektu. V současné době můžete provádět příkazy Azure Resource Manageru pomocí rozhraní příkazového řádku Azure. Můžete také použít [webu Azure portal](https://portal.azure.com). Další informace najdete v tématu [pomocí webu Azure portal pro přidání uživatelů](#portal).    

### <a name="use-the-command-line-to-add-users"></a>Použijte příkazový řádek pro přidání uživatelů
Jako příklad je vlastník projektu iris. Alice chce sdílet přístup k projektu s Bob. 

Vybere Alice **souboru** nabídky a potom vybere **příkazového řádku** položky nabídky. Otevře se okno příkazového řádku s projektu iris. Alice může rozhodněte, jakou úroveň přístupu, která chce poskytnout Bob. Marcela uděluje oprávnění spuštěním následujících příkazů:  

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

Po přiřazení role buď přímo nebo prostřednictvím dědičnosti, uvidí Bob projektu v seznamu projektu Machine Learning Workbench. Bob může být nutné restartovat aplikaci, abyste viděli projektu. Bob pak si můžete stáhnout projektu jak je popsáno v [Roaming](#roaming)a začít spolupracovat s Alice. 

Historii spuštění pro všechny uživatele, kteří spolupracují na projektu se zaměřuje na stejné vzdálené úložiště Git. Když Alice provede spuštění, Bob můžete zobrazit spuštění v části historie spuštění projektu v aplikaci Machine Learning Workbench. Bob můžete také obnovit projekt do stavu jakékoli spuštění, včetně spuštění, které Alice spuštěny. 

Při sdílení vzdálené úložiště Git pro projekt, Alice a Bob můžou také spolupracovat s v hlavní větvi. V případě potřeby mohou také vytvářet osobní větve a žádosti o přijetí změn v Gitu a sloučení použít ke spolupráci. 

### <a name="use-the-azure-portal-to-add-users"></a>Přidání uživatelů pomocí webu Azure portal
<a name="portal"></a>

Účty experimentování ve službě Learning počítače, pracovní prostory a projekty jsou prostředky Azure Resource Manageru. Přiřadit role, můžete použít **řízení přístupu** propojit [webu Azure portal](https://portal.azure.com). 

Najít prostředek, který chcete přidat uživatele do pomocí **všechny prostředky** zobrazení. Vyberte **řízení přístupu (IAM)** propojit a pak vyberte **přidat uživatele**. 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>Ukázkový pracovní postup spolupráce
Pro ilustraci, spolupráce pracovního postupu, projděme si příklad. Zaměstnanci společnosti Contoso Alice a Bob chcete spolupracovat na projekt vědeckého zpracování dat pomocí Machine Learning Workbench. Identita patřit ke stejnému tenantovi Contoso Azure Active Directory (Azure AD). Tady jsou kroky Alice a Bob trvat:

1. Alice vytvoří prázdné úložiště Git v projektu Azure DevOps. Projekt Azure DevOps by měl být v předplatném Azure, který je vytvořen v rámci tenanta Azure AD Contoso. 

2. Alice vytvoří účet experimentování ve službě Machine Learning, pracovní prostor a projekt aplikace Machine Learning Workbench na svém počítači. Vytvoří projekt, zadá adresu URL úložiště Git v Azure DevOps.

3. Alice se začne pracovat na projektu. Vytvoří některé skripty a provede několik spuštění. Pro každé spuštění snímek celé složky projektu automaticky převede se jako potvrzení změn k historii spuštění větve úložiště Git v Azure DevOps, který vytváří aplikaci Machine Learning Workbench.

4. Je Alice spokojeni s probíhající práce. Chce potvrďte své změny v místní hlavní větve a jejich následnému doručování do hlavní větve úložiště Git v Azure DevOps. S otevřeným projektem, v aplikaci Machine Learning Workbench marcela otevře okno příkazového řádku a potom zadá tyto příkazy:
    
    ```sh
    # Verify that the Git remote is pointing to the Azure DevOps Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Stage all changes.
    $ git add -A

    # Commit changes with a comment.
    $ git commit -m "this is a good milestone"

    # Push the commit to the master branch of the remote Git repo in Azure DevOps.
    $ git push
    ```

5. Alice Bob přidá do pracovního prostoru jako přispěvatelé. Uživatel můžete to udělat na webu Azure Portal nebo pomocí `az role assignment` příkaz, jak je uvedeno výše. Alice navíc uděluje Bob r/w oprávnění k úložišti Git sady Azure DevOps.

6. Bob se přihlásí do aplikace Machine Learning Workbench na svém počítači. Si můžete zobrazit pracovní prostor, který Alice sdílí s ním. Si můžete zobrazit projektu iris uvedené v části tohoto pracovního prostoru. 

7. Bob vybere název projektu. Projekt se stáhne do jeho počítač.
    * Stažený projekt soubory jsou kopie snímků nejnovější spuštění, které jsou zaznamenány v historii spuštění. Nejsou poslední potvrzení změn ve větvi master.
    * Místní složce projektu je nastavena do hlavní větve s připravené změny.

8. Bob můžete procházet spuštění, které se spustily pomocí Alice. Mu můžete obnovit snímky všech předchozích spuštění.

9. Petr chce získat poslední změny, které nahrány Alice a pak začít pracovat v jiné větvi. V aplikaci Machine Learning Workbench Bob otevře okno příkazového řádku a provede následující příkazy:

    ```sh
    # Verify that the Git remote is pointing to the Azure DevOps Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Get the latest commit in the Azure DevOps Git master branch and overwrite current files.
    $ git pull --force

    # Create a new local branch named "bob" so that Bob's work is done in the "bob" branch
    $ git checkout -b bob
    ```

10. Bob bude projekt upraven a odešle nová spuštění. Změny probíhají v bob větve. Spuštění Boba také uživatelům zviditelní Alice.

11. Bob připravený své změny odeslat do vzdáleného úložiště Git. Aby nedošlo ke konfliktu s hlavní větví, kde se pracuje Alice, doručí Bob svou práci do nové vzdálené větve, který se také nazývá bob.

    ```sh
    # Verify that the current branch is "bob," and that it has unstaged changes.
    $ git status
    
    # Stage all changes.
    $ git add -A

    # Commit the changes with a comment.
    $ git commit -m "I found a cool new trick."

    # Create a new branch on the remote Azure DevOps Git repo, and then push the changes.
    $ git push origin bob
    ```

12. Alice říct o nástrojů novou možností v jeho kód, Bob vytvoří žádost o přijetí změn na vzdálené úložiště Git z bob větve do hlavní větve. Alice může pak sloučení žádosti o přijetí změn do hlavní větve.

## <a name="next-steps"></a>Další postup
- Další informace o [pomocí úložiště Git s projektem aplikace Machine Learning Workbench](using-git-ml-project.md).
