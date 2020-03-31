---
title: Integrace správy zdrojového kódu ve službě Azure Automation
description: Tento článek popisuje integraci správy zdrojového kódu s GitHubv Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 166902978d1641458f18aeee6269c8d819e85233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132932"
---
# <a name="source-control-integration-in-azure-automation"></a>Integrace správy zdrojového kódu ve službě Azure Automation

 Integrace správy zdrojového kódu v Azure Automation podporuje synchronizaci jednoho směru z úložiště správy zdrojového kódu. Ovládací prvek zdroj umožňuje udržovat vaše runbooky ve vašem účtu Automation aktuální pomocí skriptů ve vašem úložišti zdrojového kódu GitHub nebo Azure Repos. Tato funkce usnadňuje propagaci kódu, který byl testován ve vývojovém prostředí, na váš účet automatizace výroby.
 
 Integrace správy zdrojového kódu umožňuje snadno spolupracovat s týmem, sledovat změny a vrátit se k dřívějším verzím runbooků. Například řízení zdrojového kódu umožňuje synchronizovat různé větve ve směřování zdrojového kódu s účty automatizace vývoje, testování a výroby. 

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="source-control-types"></a>Typy správy zdrojového kódu

Azure Automation podporuje tři typy správy zdrojového kódu:

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>Požadavky

* Úložiště správy zdrojového kódu (GitHub nebo Azure Repos)
* Účet [Spustit jako](manage-runas-account.md)
* [Nejnovější moduly Azure](automation-update-azure-modules.md) ve vašem účtu `Az.Accounts` Automation, včetně modulu (ekvivalent modulu `AzureRM.Profile`Az)

> [!NOTE]
> Úlohy synchronizace správy zdrojového kódu jsou spouštěny pod účtem automatizace uživatele a účtují se stejným tempem jako ostatní úlohy automatizace.

## <a name="configuring-source-control"></a>Konfigurace správy zdrojového kódu

V této části je uvedeno, jak nakonfigurovat správou zdrojového kódu pro váš účet automatizace. Můžete použít buď portál Azure nebo PowerShell.

### <a name="configure-source-control-in-azure-portal"></a>Konfigurace správy zdrojového kódu na webu Azure Portal

Tento postup slouží ke konfiguraci správy zdrojového kódu pomocí portálu Azure.

1. V účtu Automation vyberte **Položku Řízení zdrojového kódu** a klepněte na tlačítko **Přidat**.

    ![Vybrat ovládací prvek zdroj](./media/source-control-integration/select-source-control.png)

2. Zvolte **Typ správy zdrojového kódu**a klepněte na tlačítko **Ověřit**. 

3. Otevře se okno prohlížeče a zobrazí výzvu k přihlášení. Podle pokynů dokončete ověřování.

4. Na stránce Souhrn správy zdrojového kódu použijte pole k vyplnění níže definovaných vlastností správy zdrojového kódu. Po dokončení klikněte na **Uložit.** 

    |Vlastnost  |Popis  |
    |---------|---------|
    |Název ovládacího prvku zdroje     | Popisný název pro ovládací prvek zdroj. Tento název musí obsahovat pouze písmena a čísla.        |
    |Typ ovládacího prvku zdroje     | Typ mechanismu správy zdrojového kódu. Dostupné možnosti jsou:</br> * GitHub</br>* Azure Repos (Git)</br> * Azure Repos (TFVC)        |
    |Repository     | Název úložiště nebo projektu. Prvních 200 repozitářů jsou načteny. Chcete-li vyhledat úložiště, zadejte název do pole a klepněte na tlačítko **Hledat na GitHubu**.|
    |Větvení.     | Větev, ze které chcete vytáhnout zdrojové soubory. Cílení na větve není k dispozici pro typ správy zdrojového kódu TFVC.          |
    |Cesta ke složce     | Složka, která obsahuje sady Runbook pro synchronizaci, například **/Runbook**. Synchronizovány jsou pouze sady Runbook v zadané složce. Rekurze není podporována.        |
    |Automatická synchronizace<sup>1</sup>     | Nastavení, které zapne nebo vypne automatickou synchronizaci při potvrzení v úložišti správy zdrojového kódu.        |
    |Publikovat runbook     | Nastavení Zapnuto, pokud jsou sady Runbook automaticky publikovány po synchronizaci ze správy zdrojového kódu, a jinak vypnuto.           |
    |Popis     | Text určující další podrobnosti o ovládacím prvku zdrojového kódu.        |

    <sup>1</sup> Chcete-li povolit automatické synchronizace při konfiguraci integrace správy zdrojového kódu s Azure Repos, musíte být správce projektu.

   ![Souhrn správy zdrojového kódu](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Přihlášení pro úložiště správy zdrojového kódu se může lišit od vašeho přihlášení pro portál Azure. Ujistěte se, že jste přihlášeni pomocí správného účtu pro úložiště správy zdrojového kódu při konfiguraci správy zdrojového kódu. Pokud máte pochybnosti, otevřete v prohlížeči novou kartu, odhlaste se z **dev.azure.com**, **visualstudio.com**nebo **github.com**a zkuste se znovu připojit ke složce zdrojového kódu.

### <a name="configure-source-control-in-powershell"></a>Konfigurace správy zdrojového kódu v PowerShellu

PowerShell můžete taky použít ke konfiguraci správy zdrojového kódu v Azure Automation. Chcete-li pro tuto operaci použít rutiny prostředí PowerShell, potřebujete osobní přístupový token (PAT). Pomocí rutiny [New-AzAutomationSourceControl](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
) vytvořte připojení správy zdrojového kódu. Tato rutina vyžaduje bezpečný řetězec pro PAT. Informace o vytvoření zabezpečeného řetězce naleznete v tématu [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

Následující podsekce ilustrují vytvoření powershellového prostředí připojení správy zdrojového kódu pro GitHub, Azure Repos (Git) a Azure Repos (TFVC). 

#### <a name="create-source-control-connection-for-github"></a>Vytvoření připojení správy zdrojového kódu pro GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Vytvoření připojení správy zdrojového kódu pro azure úložiště (Git)

> [!NOTE]
> Azure Repos (Git) používá adresu URL, která přistupuje **dev.azure.com** místo **visualstudio.com**, který se používá v dřívějších formátech. Starší formát `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` adresy URL je zastaralá, ale stále podporována. Upřednostňuje se nový formát.


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Vytvořit připojení správy zdrojového kódu pro azure repos (TFVC)

> [!NOTE]
> Azure Repos (TFVC) používá adresu URL, která přistupuje **dev.azure.com** místo **visualstudio.com**, který se používá v dřívějších formátech. Starší formát `https://<accountname>.visualstudio.com/<projectname>/_versionControl` adresy URL je zastaralá, ale stále podporována. Upřednostňuje se nový formát.

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Oprávnění k osobnímu přístupovému tokenu (PAT)

Správa zdrojového kódu vyžaduje určitá minimální oprávnění pro PATs. Následující pododdíly obsahují minimální oprávnění požadovaná pro GitHub a Azure Repos.

##### <a name="minimum-pat-permissions-for-github"></a>Minimální oprávnění PAT pro GitHub

V následující tabulce jsou definována minimální oprávnění PAT požadovaná pro GitHub. Další informace o vytvoření PAT v GitHubu najdete v [tématu Vytvoření osobního přístupového tokenu pro příkazový řádek](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Rozsah  |Popis  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Stav potvrzení přístupu         |
|`repo_deployment`      | Přístup k stavu nasazení         |
|`public_repo`     | Přístup k veřejným úložištím         |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Háky úložiště zápisu         |
|`read:repo_hook`|Připojit háky úložiště pro čtení|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Minimální oprávnění PAT pro azure repos

Následující seznam definuje minimální oprávnění PAT požadovaná pro Azure Repos. Další informace o vytvoření PAT v Azure Repos, najdete [v tématu Ověření přístupu pomocí tokenů osobního přístupu](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

| Rozsah  |  Typ přístupu  |
|---------| ----------|
| `Code`      | Čtení  |
| `Project and team` | Čtení |
| `Identity` | Čtení     |
| `User profile` | Čtení     |
| `Work items` | Čtení    |
| `Service connections` | Čtení, dotazování, správa<sup>1</sup>    |

<sup>1</sup> `Service connections` Oprávnění je vyžadováno pouze v případě, že jste povolili automatickou synchronizaci.

## <a name="synchronizing"></a>Synchronizace

Chcete-li se synchronizovat se správou zdrojového kódu, postupujte takto. 

1. Vyberte zdroj z tabulky na stránce Ovládací prvek zdroj. 

2. Kliknutím na **Spustit synchronizaci** spusťte proces synchronizace. 

3. Kliknutím na kartu **Synchronizovat úlohy** zobrazíte stav aktuální nebo předchozí úlohy synchronizace. 

4. V rozevírací nabídce **Source Control** vyberte mechanismus správy zdrojového kódu.

    ![Stav synchronizace](./media/source-control-integration/sync-status.png)

5. Kliknutím na úlohu můžete zobrazit výstup úlohy. Následující příklad je výstup z úlohy synchronizace správy zdrojového kódu.

    ```output
    ===================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzureRmEnvironment.

    Getting AzureRunAsConnection.

    Logging in to Azure...

    Source control information for syncing:

    [Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

    Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

    Connecting to VSTS...

    Source Control Sync Summary:

    2 files synced:
     - ExampleRunbook1.ps1
     - ExampleRunbook2.ps1

    ==================================================================

    ```

6. Další protokolování je k dispozici výběrem **všech protokolů** na stránce Souhrn úlohy synchronizace synchronizace správy zdrojového kódu. Tyto další položky protokolu vám mohou pomoci při řešení problémů, které mohou vzniknout při použití správy zdrojového kódu.

## <a name="disconnecting-source-control"></a>Odpojení správy zdrojového kódu

Odpojení od úložiště správy zdrojového kódu:

1. Open **Source control** v části Nastavení **účtu** ve vašem účtu Automation.

2. Vyberte mechanismus správy zdrojového kódu, který chcete odebrat. 

3. Na stránce Souhrn správy zdrojového kódu klepněte na tlačítko **Odstranit**.

## <a name="handling-encoding-issues"></a>Zpracování problémů s kódováním

Pokud více lidí upravuje runbooky ve vašem úložišti správy zdrojového kódu pomocí různých editorů, může dojít k problémům s kódováním. Další informace o této situaci naleznete [v tématu Běžné příčiny problémů s kódováním](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues).

## <a name="updating-the-pat"></a>Aktualizace pat

V současné době nelze použít portál Azure k aktualizaci PAT ve správě zdrojového kódu. Když platnost patu vypršela nebo byla odvolána, můžete aktualizovat správu zdrojového kódu pomocí nového přístupového tokenu jedním z těchto způsobů:

* Použijte [rozhraní REST API](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Použijte rutinu [Update-AzAutomationSourceControl.](/powershell/module/az.automation/update-azautomationsourcecontrol)

## <a name="next-steps"></a>Další kroky

Další informace o typech runbooků a jejich výhodách a omezeních najdete v [tématu Typy runbooků Azure Automation](automation-runbook-types.md).
