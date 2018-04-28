---
title: Pomocí projektu Azure Machine Learning Workbench úložiště Git | Microsoft Docs
description: Tento článek vysvětluje, jak používat úložiště Git ve spojení s projektu Azure Machine Learning Workbench.
services: machine-learning
author: hning86
ms.author: haining
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/18/2017
ms.openlocfilehash: 6d244e990552ac4899361360efb44be140b06fe8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="use-a-git-repo-with-a-machine-learning-workbench-project"></a>Použít úložiště Git s projektem Machine Learning Workbench
Zjistěte, jak Azure Machine Learning Workbench využívá Git k poskytování správy verzí a zajistěte reprodukovatelnosti v experimentu vědecké účely data. Zjistěte, jak přidružit projektu cloudové úložiště Git (úložiště).

Machine Learning Workbench je navržený tak, že pro integrace Gitu. Když vytvoříte nový projekt, složce projektu je automaticky "inicializován pro Git" jako místní úložiště Git. Druhé, skrytý místní úložiště Git se také vytvoří s větev s názvem AzureMLHistory /\<projektu GUID\>. Větev uchovává informace o projektu změny složky pro každé spuštění. 

Přidružení projektu Azure Machine Learning úložiště Git umožňuje automatické verzí místně i vzdáleně. Úložiště Git je hostován ve Visual Studio Team Services (Team Services). Vzhledem k projektu Machine Learning je přidružen úložiště Git, kdokoliv, kdo má přístup do vzdáleného úložiště můžete stáhnout nejnovější zdrojový kód do jiného počítače (roaming).  

> [!NOTE]
> Team Services má vlastní ovládací prvek seznam přístupu (ACL), který je nezávislý na službu Azure Machine Learning experimenty. Uživatelský přístup můžou lišit mezi úložiště Git a pracovní prostor Machine Learning nebo projektu. Možná budete muset spravovat přístup. 
> 
> Jestli chcete udělit seskupení kód úrovně přístup k projektu Machine Learning nebo prostě jenom Podělte o pracovním prostoru, potřebujete udělit správná oprávnění pro přístup k úložišti Team Services Git. 

Ke správě verzí s Gitem, můžete buď použít hlavní větve, nebo vytvořte ostatní větve zůstanou v úložišti. Můžete také použít místní úložiště Git a odešlete do vzdáleného úložiště Git, pokud je zřízený.

Tento diagram znázorňuje vztah mezi úložišti Team Services Git a projekt Machine Learning:

![Azure Machine Learning Git](media/using-git-ml-project/aml_git.png)

Chcete-li začít používat vzdálené úložiště Git, proveďte kroky, které jsou popsané v následujících částech.

> [!NOTE]
> V současné době Azure Machine Learning podporuje úložiště Git pouze na účty Team Services. Podpora pro obecné úložiště Git, jako je Githubu, je plánované v budoucnosti.

## <a name="step-1-create-a-machine-learning-experimentation-account"></a>Krok 1. Vytvořit účet Machine Learning experimentování
Vytvořte účet Machine Learning experimentování a nainstalujte aplikaci Azure Machine Learning Workbench. Další informace najdete v tématu [instalovat a vytvořte rychlý Start](../service/quickstart-installation.md).

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>Krok 2. Vytvoření týmového projektu, nebo použijte existující týmového projektu
V [portál Azure](https://portal.azure.com/), vytvoření nového týmového projektu:
1. Vyberte **+**.
2. Vyhledejte **Team projektu**.
3. Zadejte požadované informace:
    - **Název**: název týmu.
    - **Správa verzí**: vyberte **Git**.
    - **Předplatné**: Vyberte předplatné, které má účet počítače Learning experimenty.
    - **Umístění**: v ideálním případě vyberte oblast, která je blízko prostředkům na Machine Learning experimenty.
4. Vyberte **Vytvořit**. 

![Vytvoření týmového projektu na portálu Azure](media/using-git-ml-project/create_vsts_team.png)

Ujistěte se, přihlásit se pomocí stejný účet služby Azure Active Directory (Azure AD), který používáte pro přístup k Machine Learning Workbench. Jinak systém nemá přístup k Machine Learning Workbench pomocí svých přihlašovacích údajů Azure AD. Výjimkou je, pokud pomocí příkazového řádku k vytvoření projektu Machine Learning a zadat osobní přístupový token pro přístup k úložišti Git. To probereme podrobněji později v článku.

Chcete-li přejít přímo na týmový projekt, který jste vytvořili, použijte adresu URL https://\<název týmového projektu\>. visualstudio.com.

## <a name="step-3-set-up-a-machine-learning-project-and-git-repo"></a>Krok 3. Nastavení projektu Machine Learning a úložiště Git

Pokud chcete nastavit projektu Machine Learning, máte dvě možnosti:
- Vytvoření projektu Machine Learning, který má vzdáleného úložiště Git
- Přidružit existujícího projektu Machine Learning úložiště Team Services Git

### <a name="create-a-machine-learning-project-that-has-a-remote-git-repo"></a>Vytvoření projektu Machine Learning, který má vzdáleného úložiště Git
Otevřete Machine Learning Workbench a vytvořte nový projekt. V **úložiště Git** pole, zadejte adresu URL úložiště Team Services Git z kroku 2. Obvykle vypadá takto: https://\<název účtu Team Services\>.visualstudio.com/_git/\<název projektu\>

![Vytvoření projektu Machine Learning, který má úložiště Git](media/using-git-ml-project/create_project_with_git_rep.png)

Můžete také vytvořit projekt pomocí nástroje příkazového řádku Azure (Azure CLI). Máte možnost zadat osobní přístupový token. Machine Learning můžete použít tento token pro přístup k úložišti Git místo pomocí svých přihlašovacích údajů Azure AD:

```
# Create a new project that has a Git repo by using a personal access token.
$ az ml project create -a <Experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <Team Services personal access token>
```

> [!IMPORTANT]
> Pokud si zvolíte Šablona prázdného projektu, úložiště Git, kterou chcete použít už možná máte hlavní větve. Machine Learning jednoduše provede klonování hlavní větve místně. Přidá aml_config složky a další metadata soubory projektu do složky místní projektu. 
>
> Pokud zvolíte jakékoli další šablony projektu, vaše úložiště Git *nelze* už máte hlavní větve. Pokud ano, zobrazí se chyba. Alternativou je použít `az ml project create` příkaz pro vytvoření projektu, s `--force` přepínače. To odstraní soubory v původní hlavní větve a nahradí je nové soubory v šabloně, který zvolíte.

Vzdálené úložiště integrace Gitu povoleno se vytvoří nový projekt Machine Learning. Složce projektu je vždy inicializován pro Git jako místní úložiště Git. Vzdálené úložiště Git je nastavené na vzdálené úložiště Team Services Git, takže potvrzení můžete posouvat nastavení do vzdáleného úložiště Git.

### <a name="associate-an-existing-machine-learning-project-with-a-team-services-git-repo"></a>Přidružit existujícího projektu Machine Learning úložiště Team Services Git
Můžete vytvořit projekt Machine Learning bez úložiště Team Services Git a spoléhají na místní úložiště Git pro snímky historie spouštění. Úložiště Team Services Git můžete později přidružit tento existující projekt Machine Learning pomocí následujícího příkazu:

```azurecli
# Ensure that you are in the project path so Azure CLI has the context of your current project.
$ az ml project update --repo https://<Team Services account name>.visualstudio.com/_git/<project name>
```

> [!NOTE] 
> Můžete provést operaci aktualizace úložišti jenom na Machine Learning projektu, který nemá úložiště Git, s ním spojená. Kromě toho po přidružíte úložiště Git Machine Learning, nelze odebrat.

## <a name="step-4-capture-a-project-snapshot-in-the-git-repo"></a>Krok 4. Zachycení snímku projektu v úložišti Git
Provést několik skript se spustí v projektu a provedeme některé změny mezi je spuštěn. To provedete v desktopová aplikace nebo z příkazového řádku Azure pomocí `az ml experiment submit` příkaz. Další informace najdete v tématu [klasifikace Iris kurzu](tutorial-classifying-iris-part-1.md). Při každém spuštění, pokud se všechny změny provedené v všechny soubory ve složce projektu snímek složce celý projekt je potvrzeny a instaluje do vzdáleného úložiště Git v části s názvem AzureMLHistory větev nebo\<projektu GUID\>. Chcete-li zobrazit větve a potvrdí, včetně AzureMLHistory /\<projektu GUID\> větve, přejděte na adresu URL úložiště Team Services Git. 

> [!NOTE] 
> Snímek se potvrdí pouze před spuštěním skriptu. V současné době data Příprava aplikace spuštění nebo spuštění Poznámkový blok buněk neaktivuje snímku.

![Historie spouštění větve](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> Je nejvhodnější Pokud nemáte provozujete v pobočce historie pomocí příkazů Git. Ho může narušovat historie spouštění. Místo toho použít hlavní větve nebo vytvořit ostatní větve zůstanou pro operace Git.

## <a name="step-5-restore-a-previous-project-snapshot"></a>Krok 5. Obnovit předchozí snímek projektu 
Chcete-li obnovit složce celý projekt stav předchozí historie spouštění snímku v nástroji Machine Learning Workbench:
1. V rámci aktivity panelu (ikona přesýpacích hodin) vyberte **spustí**.
2. V **seznamu spustit** zobrazit, vyberte spuštění, které chcete obnovit.
3. V **spustit podrobností** zobrazit, vyberte možnost **obnovení**.

![Historie spouštění větve](media/using-git-ml-project/restore_project.png)

V okně příkazového řádku Azure CLI v nástroji Machine Learning Workbench můžete volitelně použít následující příkazy:

```azurecli
# Discover the run I want to restore a snapshot from.
$ az ml history list -o table

# Restore the snapshot from a specific run.
$ az ml project restore --run-id <run ID>
```

Buďte opatrní při spuštění tohoto příkazu. Spuštění tohoto příkazu přepíše složce celý projekt snímku, která se provede, když tento konkrétní spustit byla spuštěna. Váš projekt probíhal v aktuální větvi. To znamená, že jste **, ztratíte všechny změny** v aktuální složce projektu.  

Můžete chtít pomocí Git uložte provedené změny do aktuální větve, před provedením této operace.

## <a name="step-6-use-the-master-branch"></a>Krok 6. Použít hlavní větve
Jedním ze způsobů, abyste se vyhnuli ztrátě omylem vaše aktuální stav projektu je potvrzení projektu do hlavní větve úložiště Git (nebo všechny firemní pobočky, který jste sami vytvořili). Můžete Git z příkazového řádku nebo z vaše oblíbené nástroje klienta Git pracovat na hlavní větve. Příklad:

```sh
# Check status to make sure you are on the master branch (or branch of your choice).
$ git status

# Stage all changes.
$ git add -A

# Commit all changes locally on the master branch.
$ git commit -m 'these are my updates so far'

# Push changes to the remote Team Services Git repo master branch.
$ git push origin master
```

Nyní můžete bezpečně obnovit projekt starší snímek provedením kroku 5. Vždy se můžete vrátit zpět k potvrzení, který jste právě vytvořili na hlavní větve.

## <a name="authentication"></a>Authentication
Pokud byste tedy spoléhat jenom na funkce historie spouštění v Machine Learning k pořízení snímků projektu a jejich obnovení, nemusíte si dělat starosti o ověřování úložiště Git. Ověřování se provádí vrstvě služby Machine Learning experimenty.

Ale pokud používáte vlastní Git nástroje pro správu verzí, budete muset zpracovávat ověřování proti vzdáleného úložiště Git v Team Services. Ve strojovém učení se vzdálené úložiště Git je přidán do místního úložiště jako vzdálené úložiště Git pomocí protokolu HTTPS. To znamená, pokud vydáte příkazy Gitu (například push nebo pull) ke vzdálenému, budete muset zadat svoje uživatelské jméno a heslo nebo osobní přístupový token. Pokud chcete vytvořit osobní přístupový token v úložišti Team Services Git, postupujte podle pokynů v [k ověření použít osobní přístupový token](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate).

## <a name="next-steps"></a>Další postup
- Zjistěte, jak [pomocí procesu Team dat vědecké účely uspořádat strukturu projektu](how-to-use-tdsp-in-azure-ml.md).
