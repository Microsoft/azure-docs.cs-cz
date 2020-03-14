---
title: Integrace správy zdrojového kódu ve službě Azure Automation
description: Tento článek popisuje integraci správy zdrojového kódu pomocí GitHubu v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: b0eed8fe9d548ee54698d187e192960bb3b44e44
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368804"
---
# <a name="source-control-integration-in-azure-automation"></a>Integrace správy zdrojového kódu ve službě Azure Automation

 Integrace správy zdrojového kódu v Azure Automation podporuje synchronizaci v jednom směru z úložiště správy zdrojového kódu. Správa zdrojového kódu vám umožňuje udržovat vaše Runbooky v účtu Automation v aktuálním stavu pomocí skriptů ve vašem GitHubu nebo Azure Repos úložiště správy zdrojového kódu. Tato funkce usnadňuje zvýšení úrovně kódu, který byl testován ve vašem vývojovém prostředí, do vašeho účtu služby Automation.
 
 Pomocí integrace správy zdrojového kódu můžete snadno spolupracovat se svým týmem, sledovat změny a vracet se zpět k předchozím verzím runbooků. Například Správa zdrojového kódu umožňuje synchronizovat různé větve ve správě zdrojového kódu pomocí účtů pro vývoj, testování a produkční automatizaci. 

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](automation-update-azure-modules.md).

## <a name="source-control-types"></a>Typy správy zdrojového kódu

Azure Automation podporuje tři typy správy zdrojového kódu:

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>Požadavky

* Úložiště správy zdrojového kódu (GitHub nebo Azure Repos)
* [Účet Spustit jako](manage-runas-account.md)
* [Nejnovější moduly Azure](automation-update-azure-modules.md) v účtu Automation, včetně modulu `Az.Accounts` (AZ Module ekvivalent of `AzureRM.Profile`)

> [!NOTE]
> Úlohy synchronizace správy zdrojového kódu se spouštějí v účtu Automation uživatele a účtují se stejnou sazbou jako ostatní úlohy automatizace.

## <a name="configuring-source-control"></a>Konfigurace správy zdrojového kódu

V této části se dozvíte, jak nakonfigurovat správu zdrojového kódu pro váš účet Automation. Můžete použít buď Azure Portal, nebo PowerShell.

### <a name="configure-source-control----azure-portal"></a>Konfigurovat správu zdrojového kódu--Azure Portal

Pomocí tohoto postupu můžete nakonfigurovat správu zdrojového kódu pomocí Azure Portal.

1. V rámci účtu Automation vyberte **Správa zdrojového kódu** a klikněte na **+ Přidat**.

    ![Vybrat správu zdrojového kódu](./media/source-control-integration/select-source-control.png)

2. Zvolte **typ správy zdrojového kódu**a pak klikněte na **ověřit**. 

3. Otevře se okno prohlížeče s výzvou, abyste se přihlásili. Dokončete ověření podle zobrazených výzev.

4. Na stránce Souhrn správy zdrojových kódů použijte pole k vyplnění vlastností správy zdrojového kódu, které jsou definovány níže. Po dokončení klikněte na **Uložit** . 

    |Vlastnost  |Popis  |
    |---------|---------|
    |Název správy zdrojového kódu     | Popisný název pro správu zdrojového kódu. Tento název musí obsahovat jenom písmena a číslice.        |
    |Typ správy zdrojového kódu     | Typ mechanismu správy zdrojového kódu. Dostupné možnosti jsou:</br> * GitHub</br>* Azure Repos (Git)</br> * Azure Repos (TFVC)        |
    |Úložiště     | Název úložiště nebo projektu. Načtou se první 200 úložišť. Pokud chcete vyhledat úložiště, zadejte název do pole a klikněte na **Hledat na GitHubu**.|
    |Součástí     | Větev, ze které se mají načíst zdrojové soubory Cílení na větev není k dispozici pro typ správy zdrojového kódu TFVC.          |
    |Cesta ke složce     | Složka, která obsahuje Runbooky, které se mají synchronizovat, například **/runbooks**. Synchronizovány jsou pouze Runbooky v zadané složce. Rekurze není podporována.        |
    |Automatická synchronizace<sup>1</sup>     | Nastavení, které zapne nebo vypne automatickou synchronizaci při provedení potvrzení v úložišti správy zdrojů.        |
    |Publikování Runbooku     | Nastavení zapnuto, pokud jsou Runbooky automaticky publikovány po synchronizaci ze správy zdrojového kódu a mimo jiné.           |
    |Popis     | Text určující další podrobnosti o správě zdrojového kódu.        |

    <sup>1</sup> Chcete-li povolit automatickou synchronizaci při konfiguraci integrace správy zdrojového kódu pomocí Azure Repos, musíte být správcem projektu.

   ![Souhrn správy zdrojového kódu](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Přihlašovací údaje pro úložiště správy zdrojového kódu se můžou lišit od přihlášení k Azure Portal. Při konfiguraci správy zdrojového kódu se ujistěte, že jste přihlášeni pomocí správného účtu pro úložiště správy zdrojového kódu. Pokud dojde k pochybnostem, otevřete v prohlížeči novou kartu, odhlaste se z **VisualStudio.com** nebo **GitHub.com**a zkuste se znovu připojit ke správě zdrojového kódu.

### <a name="configure-source-control----powershell"></a>Konfigurace správy zdrojového kódu – PowerShell

PowerShell můžete také použít ke konfiguraci správy zdrojového kódu v Azure Automation. Pokud chcete pro tuto operaci používat rutiny PowerShellu, budete potřebovat osobní přístupový token (PAT). Pomocí rutiny [New-AzAutomationSourceControl](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
) vytvořte připojení správy zdrojového kódu. Tato rutina vyžaduje zabezpečený řetězec pro PAT. Informace o tom, jak vytvořit zabezpečený řetězec, najdete v tématu [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

Následující témata ukazují prostředí PowerShell pro vytvoření připojení správy zdrojového kódu pro GitHub, Azure Repos (Git) a Azure Repos (TFVC).

#### <a name="create-source-control-connection-for-github"></a>Vytvořit připojení správy zdrojového kódu pro GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Vytvořit připojení správy zdrojového kódu pro Azure Repos (Git)

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Vytvořit připojení správy zdrojového kódu pro Azure Repos (TFVC)

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Oprávnění k tokenu PAT (Personal Access token)

Správa zdrojového kódu vyžaduje minimální oprávnění pro PATs. Následující pododdíly obsahují minimální oprávnění požadovaná pro GitHub a Azure Repos.

##### <a name="minimum-pat-permissions-for-github"></a>Minimální oprávnění PAT pro GitHub

Následující tabulka definuje minimální oprávnění PAT potřebná pro GitHub. Další informace o vytvoření PAT v GitHubu najdete v tématu [Vytvoření osobního přístupového tokenu pro příkazový řádek](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Scope  |Popis  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Stav potvrzení přístupu         |
|`repo_deployment`      | Stav nasazení přístupu         |
|`public_repo`     | Přístup k veřejným úložištím         |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Zapsat háky úložiště         |
|`read:repo_hook`|Číst háky úložiště|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Minimální oprávnění PAT pro Azure Repos

Následující seznam definuje minimální oprávnění PAT potřebná pro Azure Repos. Další informace o vytvoření PAT v Azure Repos najdete v tématu [ověření přístupu pomocí tokenů osobního přístupu](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

| Scope  |  Typ přístupu  |
|---------| ----------|
| Kód      | Pro čtení  |
| Projekt a tým | Pro čtení |
| Identita | Pro čtení     |
| Profil uživatele | Pro čtení     |
| Pracovní položky | Pro čtení    |
| Připojení služby | Číst, dotazovat, spravovat<sup>1</sup>    |

<sup>1</sup> oprávnění připojení služby se vyžaduje jenom v případě, že jste povolili automatickou synchronizaci.

## <a name="synchronizing"></a>Synchroniz

Pomocí těchto kroků proveďte synchronizaci se správou zdrojových kódů. 

1. Vyberte zdroj z tabulky na stránce Správa zdrojového kódu. 

2. Kliknutím na **Spustit synchronizaci** spusťte proces synchronizace. 

3. Kliknutím na kartu **úlohy synchronizace** zobrazte stav aktuální úlohy synchronizace nebo předchozí. 

4. V rozevírací nabídce **Správa zdrojového kódu** vyberte mechanismus správy zdrojového kódu.

    ![Stav synchronizace](./media/source-control-integration/sync-status.png)

5. Kliknutím na úlohu můžete zobrazit výstup úlohy. Následující příklad je výstupem z úlohy synchronizace správy zdrojového kódu.

    ```output
    ============================================================================

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

     =========================================================================

    ```

6. Další protokolování je k dispozici výběrem možnosti **všechny protokoly** na stránce Souhrn úlohy synchronizace správy zdrojového kódu. Tyto další položky protokolu vám mohou pomoci při řešení problémů, které mohou nastat při použití správy zdrojového kódu.

## <a name="disconnecting-source-control"></a>Odpojuje se Správa zdrojového kódu.

Odpojení od úložiště správy zdrojového kódu:

1. V části **Nastavení účtu** v účtu Automation otevřete **Správa zdrojového kódu** .

2. Vyberte mechanismus správy zdrojového kódu, který se má odebrat. 

3. Na stránce Souhrn správy zdrojového kódu klikněte na **Odstranit**.

## <a name="handling-encoding-issues"></a>Zpracování potíží s kódováním

Pokud více lidí upravuje Runbooky v úložišti správy zdrojového kódu pomocí různých editorů, může dojít k problémům s kódováním. Další informace o této situaci najdete v tématu [běžné příčiny potíží s kódováním](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues).

## <a name="updating-the-pat"></a>Aktualizace PAT

V současné době neexistuje způsob, jak použít Azure Portal k aktualizaci PAT ve správě zdrojového kódu. Po vypršení nebo odvolání platnosti vaší PAT můžete aktualizovat správu zdrojového kódu pomocí nového přístupového tokenu jedním z následujících způsobů:

* Použijte [REST API](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Použijte rutinu [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) .

## <a name="next-steps"></a>Další kroky

Další informace o typech runbooků a jejich výhodách a omezeních najdete v tématu [Azure Automation typy runbooků](automation-runbook-types.md).
