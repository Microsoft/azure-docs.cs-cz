---
title: Použití úložiště Git s projektem aplikace Azure Machine Learning Workbench | Dokumentace Microsoftu
description: Tento článek vysvětluje, jak používat úložiště Git ve spojení s projektem aplikace Azure Machine Learning Workbench.
services: machine-learning
author: hning86
ms.author: haining
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 11/18/2017
ms.openlocfilehash: 58ab1d77218595344c899dff654ba5b7a5bfb0d8
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296632"
---
# <a name="use-a-git-repo-with-a-machine-learning-workbench-project"></a>Použití úložiště Git s projektem aplikace Machine Learning Workbench
Zjistěte, jak Azure Machine Learning Workbench používá Git k zajištění správy verzí a zajištění reprodukovatelnost v experimentu datové vědy. Zjistěte, jak přidružit cloudovému úložišti Git (úložiště) svůj projekt.

Machine Learning Workbench je určená pro integrace Gitu. Když vytvoříte nový projekt, složku projektu je automaticky "Git inicializována" jako místní úložiště Git. Druhý, skrytý místní úložiště Git se také vytvoří větev s názvem AzureMLHistory /\<projektu GUID\>. Větev uchovává informace o změny složku projektu pro každé spuštění. 

Automatická správa verzí, přidružení projekt Azure Machine Learning s úložištěm Git umožňuje, místně i vzdáleně. Úložiště Git je hostovaná v Azure DevOps. Protože projektu Machine Learning je spojen s úložištěm Git, každý, kdo má přístup do vzdáleného úložiště stáhnout nejnovější zdrojový kód do jiného počítače (roaming).  

> [!NOTE]
> Azure DevOps se vlastní ovládací prvek seznam přístupu (ACL), která je nezávislá služby experimentování ve službě Azure Machine Learning. Přístup uživatele se můžou lišit mezi úložištěm Git a pracovního prostoru Machine Learning nebo projektu. Budete potřebovat ke správě přístupu. 
> 
> Určuje, zda chcete poskytnout člen týmu přístup k projektu Machine Learning na úrovni kódu nebo prostě jenom Podělte o pracovním prostoru, budete muset udělit uživateli správná oprávnění pro přístup k úložišti Git v Azure DevOps. 

Správa verzí pomocí Gitu, můžete použít v hlavní větvi nebo vytvářet další větve v úložišti. Můžete také použít místní úložiště Git a vložit do vzdáleného úložiště Git, při zřízení.

Tento diagram znázorňuje vztah mezi úložiště Azure DevOps Git a projekt Machine Learning:

![Azure Machine Learning Git](media/using-git-ml-project/aml_git.png)

Abyste mohli začít používat vzdálené úložiště Git, proveďte kroky, které jsou popsány v následujících částech.

> [!NOTE]
> Azure Machine Learning v současné době podporuje úložiště Git pouze na Azure DevOps organizace.

## <a name="step-1-create-a-machine-learning-experimentation-account"></a>Krok 1. Vytvoření účtu experimentování ve službě Machine Learning
Vytvoření účtu experimentování ve službě Machine Learning a nainstalujte aplikaci Azure Machine Learning Workbench. Další informace najdete v tématu [instalace a vytvoření rychlý Start](../service/quickstart-installation.md).

## <a name="step-2-create-an-azure-devops-project-or-use-an-existing-project"></a>Krok 2. Vytvořte projekt Azure DevOps nebo použijte existující projekt
V [webu Azure portal](https://portal.azure.com/), vytvořte nový projekt:
1. Vyberte **+**.
2. Vyhledejte **týmový projekt**.
3. Zadejte požadované informace:
    - **Název**: název týmu.
    - **Správa verzí**: vyberte **Git**.
    - **Předplatné**: Vyberte předplatné, které má účet experimentování ve službě Machine Learning.
    - **Umístění**: v ideálním případě vyberte oblast, která se nachází blízko prostředky experimentování ve službě Machine Learning.
4. Vyberte **Vytvořit**. 

![Vytvoření projektu na webu Azure Portal](media/using-git-ml-project/create_vsts_team.png)

Ujistěte se, že se přihlásit pomocí stejného účtu služby Azure Active Directory (Azure AD), který používáte pro přístup k aplikaci Machine Learning Workbench. V opačném případě systém nemá přístup k aplikaci Machine Learning Workbench pomocí vašich přihlašovacích údajů Azure AD. Výjimkou je, pokud pomocí příkazového řádku k vytvoření projektu Machine Learning a zadat osobní přístupový token pro přístup k úložišti Git. Podíváme na to podrobněji dále v tomto článku.

Chcete-li přejít přímo na projekt, který jste vytvořili, použijte adresu URL https://\<název projektu\>. visualstudio.com.

## <a name="step-3-set-up-a-machine-learning-project-and-git-repo"></a>Krok 3. Nastavení úložiště Git a projekt služby Machine Learning

Nastavení projektu Machine learningu, máte dvě možnosti:
- Vytvoření projektu Machine Learning, která má vzdálené úložiště Git
- Existující projekt Machine Learning přidružit úložišti Git v Azure DevOps

### <a name="create-a-machine-learning-project-that-has-a-remote-git-repo"></a>Vytvoření projektu Machine Learning, která má vzdálené úložiště Git
Otevřete aplikaci Machine Learning Workbench a vytvořte nový projekt. V **úložiště Git** zadejte adresu URL úložiště Git v Azure DevOps z kroku 2. Obvykle vypadá takto: https://\<název organizace Azure DevOps\>.visualstudio.com/_git/\<název projektu\>

![Vytvoření projektu Machine Learning, která má úložiště Git](media/using-git-ml-project/create_project_with_git_rep.png)

Projekt můžete vytvořit také pomocí rozhraní příkazového řádku Azure (Azure CLI). Máte možnost zadat osobní přístupový token. Machine Learning můžete používat tento token pro přístup k úložišti Git namísto pomocí svých přihlašovacích údajů Azure AD:

```
# Create a new project that has a Git repo by using a personal access token.
$ az ml project create -a <Experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <Azure DevOps personal access token>
```

> [!IMPORTANT]
> Pokud se rozhodnete šabloně prázdného projektu, pravděpodobně již úložiště Git, kterou chcete použít hlavní větve. Machine Learning jednoduše duplicity místně v hlavní větvi. Přidá aml_config složky a další metadata soubory projektu do složky místní projekt. 
>
> Pokud zvolíte jakékoli další šablony projektu, úložiště Git *nelze* již mít hlavní větev. Pokud ano, zobrazí se chyba. Alternativou je použít `az ml project create` příkaz pro vytvoření projektu, se `--force` přepnout. To odstraní soubory v původní hlavní větvi a nahradí je novými soubory v šabloně, kterou zvolíte.

Vzdálené úložiště integrace Gitu povoleno se vytvoří nový projekt Machine Learning. Složku projektu je vždy inicializován pro Git jako místní úložiště Git. Vzdálené úložiště Git je nastaven na vzdálené úložiště Azure DevOps Git, tak můžete potvrzení změn vložíte změny do vzdáleného úložiště Git.

### <a name="associate-an-existing-machine-learning-project-with-an-azure-devops-git-repo"></a>Existující projekt Machine Learning přidružit úložišti Git v Azure DevOps
Můžete vytvořit projekt Machine Learning bez úložiště Azure DevOps Git a Spolehněte se na místní úložiště Git pro snímky historie spuštění. Úložiště Azure DevOps Git můžete později přidružit tento existující projekt služby Machine Learning pomocí následujícího příkazu:

```azurecli
# Ensure that you are in the project path so Azure CLI has the context of your current project.
$ az ml project update --repo https://<Azure DevOps organization name>.visualstudio.com/_git/<project name>
```

> [!NOTE] 
> Operaci aktualizace úložiště pouze na projektu Machine Learning, která nemá úložiště Git s ním spojená. Také poté, co přidružíte úložiště Git pomocí služby Machine Learning, nelze odebrat.

## <a name="step-4-capture-a-project-snapshot-in-the-git-repo"></a>Krok 4. Pořídit snímek projekt v úložišti Git
Spuštění několika skript se spustí v projektu a provést nějaké změny mezi spuštěními. Musíte v desktopové aplikaci nebo z příkazového řádku Azure s použitím `az ml experiment submit` příkazu. Další informace najdete v tématu [kurz klasifikace Iris](tutorial-classifying-iris-part-1.md). Pro každé spuštění, pokud se všechny změny provedené v všechny soubory ve složce projektu snímek celé složky projektu je potvrdíte a nabídnete do vzdáleného úložiště Git v rámci větev s názvem AzureMLHistory /\<projektu GUID\>. Chcete-li zobrazit větve a potvrzení změn, včetně AzureMLHistory /\<projektu GUID\> větve, přejděte na adresu URL úložiště Git v Azure DevOps. 

> [!NOTE] 
> Snímek je potvrzena pouze před spuštěním skriptu. V současné době přípravy dat snímku neaktivuje spuštění nebo spuštění buňky poznámkového bloku.

![Větev historie spuštění](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> Je vhodné, pokud už nefungují v historii větve pomocí příkazů Gitu. To může narušit historie spuštění. Místo toho použít hlavní větev nebo vytvářet další větve pro operace Git.

## <a name="step-5-restore-a-previous-project-snapshot"></a>Krok 5. Obnovení předchozí snímek projektu 
Chcete-li obnovit celé složky projektu do stavu předchozího historii spuštění snímku v aplikaci Machine Learning Workbench:
1. V aktivitě panelu (ikona přesýpacích hodin), vyberte **spuštění**.
2. V **seznam spuštění** zobrazit, vyberte spuštění, které chcete obnovit.
3. V **spustit podrobností** zobrazit, vyberte možnost **obnovení**.

![Větev historie spuštění](media/using-git-ml-project/restore_project.png)

Volitelně můžete použít následující příkazy v okně rozhraní příkazového řádku Azure v aplikaci Machine Learning Workbench:

```azurecli
# Discover the run I want to restore a snapshot from.
$ az ml history list -o table

# Restore the snapshot from a specific run.
$ az ml project restore --run-id <run ID>
```

Buďte opatrní při spuštění tohoto příkazu. Spuštění tohoto příkazu přepíše celé složky projektu snímek, který se provede, když se začaly tohoto konkrétního spuštění. Váš projekt probíhal v aktuální větvi. To znamená, že jste **ztratit všechny změny** v aktuální složce projektu.  

Můžete chtít použít Git potvrdit změny v aktuální větvi, před provedením této operace.

## <a name="step-6-use-the-master-branch"></a>Krok 6. Použití v hlavní větvi
Jedním ze způsobů ztratíte omylem váš aktuální stav projektu je projekt potvrzení do hlavní větve úložiště Git (nebo jakékoli větve, kterou jste sami vytvořili). Vám pomůže Git z příkazového řádku nebo z vašeho oblíbeného klientského nástroje Git pracovat s větví master. Příklad:

```sh
# Check status to make sure you are on the master branch (or branch of your choice).
$ git status

# Stage all changes.
$ git add -A

# Commit all changes locally on the master branch.
$ git commit -m 'these are my updates so far'

# Push changes to the remote Azure DevOps Git repo master branch.
$ git push origin master
```

Teď se můžete bezpečně obnovit projekt na předchozí snímek po dokončení kroku 5. Vždy se můžete vrátit zpět k potvrzení, které jste právě provedli v hlavní větvi.

## <a name="authentication"></a>Authentication
Pokud se spoléháte pouze na funkce historie spuštění ve službě Machine Learning k pořízení snímků projektu a jejich obnovení, není nutné starat o ověřování úložiště Git. Ověřování zařizuje služba vrstva služby experimentování ve službě Machine Learning.

Nicméně pokud používáte vlastní nástroje Git pro správu verzí, potřebujete zpracovávat ověřování proti vzdálené úložiště Git v Azure DevOps. Ve službě Machine Learning vzdálené úložiště Git se přidá do místního úložiště jako vzdálené úložiště Git s použitím protokolu HTTPS. To znamená, když do vzdálené příkazy Gitu (například nabízení nebo vyžádání), budete muset zadat svoje uživatelské jméno a heslo nebo osobní přístupový token. Chcete-li vytvořit osobní přístupový token v úložišti Git v Azure DevOps, postupujte podle pokynů v [pomocí osobního přístupového tokenu pro ověření](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

## <a name="next-steps"></a>Další postup
- Zjistěte, jak [používat vědecké zpracování týmových dat uspořádat vaše struktury projektů](how-to-use-tdsp-in-azure-ml.md).
