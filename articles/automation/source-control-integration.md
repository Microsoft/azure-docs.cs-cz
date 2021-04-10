---
title: Použití integrace správy zdrojového kódu v Azure Automation
description: Tento článek obsahuje informace o tom, jak synchronizovat Azure Automation správy zdrojového kódu s jinými úložišti.
services: automation
ms.subservice: process-automation
ms.date: 03/10/2021
ms.topic: conceptual
ms.openlocfilehash: 281da27ce95649e85dae5d0795bb743f21fdb578
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102631740"
---
# <a name="use-source-control-integration"></a>Použití integrace správy zdrojového kódu

 Integrace správy zdrojového kódu v Azure Automation podporuje synchronizaci v jednom směru z úložiště správy zdrojového kódu. Správa zdrojového kódu vám umožňuje udržovat vaše Runbooky v účtu Automation v aktuálním stavu pomocí skriptů ve vašem GitHubu nebo Azure Repos úložiště správy zdrojového kódu. Tato funkce usnadňuje zvýšení úrovně kódu, který byl testován ve vašem vývojovém prostředí, do vašeho účtu služby Automation.

 Integrace správy zdrojového kódu umožňuje snadnou spolupráci se svým týmem, sledovat změny a vracet se zpět k předchozím verzím runbooků. Například Správa zdrojového kódu umožňuje synchronizovat různé větve ve správě zdrojového kódu pomocí účtů pro vývoj, testování a produkční automatizaci.

## <a name="source-control-types"></a>Typy správy zdrojového kódu

Azure Automation podporuje tři typy správy zdrojového kódu:

* GitHubu
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>Požadavky

* Úložiště správy zdrojového kódu (GitHub nebo Azure Repos)
* [Účet Spustit jako](automation-security-overview.md#run-as-accounts)
* [ `AzureRM.Profile` Modul](/powershell/module/azurerm.profile/) musí být importován do svého účtu Automation. Všimněte si, že ekvivalentní modul AZ Module ( `Az.Accounts` ) nebude spolupracovat se správou zdrojových kódů Automation.

> [!NOTE]
> Úlohy synchronizace správy zdrojového kódu se spouštějí v účtu Automation uživatele a účtují se stejnou sazbou jako ostatní úlohy automatizace.

## <a name="configure-source-control"></a>Konfigurovat správu zdrojového kódu

V této části se dozvíte, jak nakonfigurovat správu zdrojového kódu pro váš účet Automation. Můžete použít buď Azure Portal, nebo PowerShell.

### <a name="configure-source-control-in-azure-portal"></a>Konfigurovat správu zdrojového kódu v Azure Portal

Pomocí tohoto postupu můžete nakonfigurovat správu zdrojového kódu pomocí Azure Portal.

1. V účtu Automation vyberte **Správa zdrojového kódu** a klikněte na **Přidat**.

    ![Vybrat správu zdrojového kódu](./media/source-control-integration/select-source-control.png)

2. Zvolte **typ správy zdrojového kódu** a pak klikněte na **ověřit**.

3. Otevře se okno prohlížeče s výzvou, abyste se přihlásili. Dokončete ověření podle zobrazených výzev.

4. Na stránce Souhrn správy zdrojových kódů použijte pole k vyplnění vlastností správy zdrojového kódu, které jsou definovány níže. Po dokončení klikněte na **Uložit** .

    |Vlastnost  |Popis  |
    |---------|---------|
    |Název správy zdrojového kódu     | Popisný název pro správu zdrojového kódu. Tento název musí obsahovat jenom písmena a číslice.        |
    |Typ správy zdrojového kódu     | Typ mechanismu správy zdrojového kódu. Dostupné možnosti jsou:</br> * GitHub</br>* Azure Repos (Git)</br> * Azure Repos (TFVC)        |
    |Repository     | Název úložiště nebo projektu. Načtou se první 200 úložišť. Pokud chcete vyhledat úložiště, zadejte název do pole a klikněte na **Hledat na GitHubu**.|
    |Větvení.     | Větev, ze které se mají načíst zdrojové soubory Cílení na větev není k dispozici pro typ správy zdrojového kódu TFVC.          |
    |Cesta ke složce     | Složka, která obsahuje Runbooky, které se mají synchronizovat, například **/runbooks**. Synchronizovány jsou pouze Runbooky v zadané složce. Rekurze není podporována.        |
    |Automatická synchronizace<sup>1</sup>     | Nastavení, které zapne nebo vypne automatickou synchronizaci při provedení potvrzení v úložišti správy zdrojů.        |
    |Publikování Runbooku     | Nastavení zapnuto, pokud jsou Runbooky automaticky publikovány po synchronizaci ze správy zdrojového kódu a mimo jiné.           |
    |Description     | Text určující další podrobnosti o správě zdrojového kódu.        |

    <sup>1</sup> Chcete-li povolit automatickou synchronizaci při konfiguraci integrace správy zdrojového kódu pomocí Azure Repos, musíte být správcem projektu.

   ![Souhrn správy zdrojového kódu](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Přihlašovací údaje pro úložiště správy zdrojového kódu se můžou lišit od přihlášení k Azure Portal. Při konfiguraci správy zdrojového kódu se ujistěte, že jste přihlášeni pomocí správného účtu pro úložiště správy zdrojového kódu. Pokud dojde k nějaké pochybnosti, otevřete v prohlížeči novou kartu, odhlaste se z **dev.Azure.com**, **VisualStudio.com** nebo **GitHub.com** a zkuste se znovu připojit ke správě zdrojového kódu.

### <a name="configure-source-control-in-powershell"></a>Konfigurace správy zdrojového kódu v PowerShellu

PowerShell můžete také použít ke konfiguraci správy zdrojového kódu v Azure Automation. Pokud chcete pro tuto operaci používat rutiny PowerShellu, budete potřebovat osobní přístupový token (PAT). Pomocí rutiny [New-AzAutomationSourceControl](/powershell/module/az.automation/new-azautomationsourcecontrol) vytvořte připojení správy zdrojového kódu. Tato rutina vyžaduje zabezpečený řetězec pro PAT. Informace o tom, jak vytvořit zabezpečený řetězec, najdete v tématu [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring).

Následující témata ukazují prostředí PowerShell pro vytvoření připojení správy zdrojového kódu pro GitHub, Azure Repos (Git) a Azure Repos (TFVC).

#### <a name="create-source-control-connection-for-github"></a>Vytvořit připojení správy zdrojového kódu pro GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Vytvořit připojení správy zdrojového kódu pro Azure Repos (Git)

> [!NOTE]
> Azure Repos (Git) používá adresu URL, která přistupuje k **dev.Azure.com** místo **VisualStudio.com**, a používá se v dřívějších formátech. Starší formát adresy URL `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` je zastaralý, ale je stále podporován. Doporučuje se nový formát.


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Vytvořit připojení správy zdrojového kódu pro Azure Repos (TFVC)

> [!NOTE]
> Azure Repos (TFVC) používá adresu URL, která přistupuje k **dev.Azure.com** místo **VisualStudio.com**, které se používá v dřívějších formátech. Starší formát adresy URL `https://<accountname>.visualstudio.com/<projectname>/_versionControl` je zastaralý, ale je stále podporován. Doporučuje se nový formát.

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Oprávnění k tokenu PAT (Personal Access token)

Správa zdrojového kódu vyžaduje minimální oprávnění pro PATs. Následující pododdíly obsahují minimální oprávnění požadovaná pro GitHub a Azure Repos.

##### <a name="minimum-pat-permissions-for-github"></a>Minimální oprávnění PAT pro GitHub

Následující tabulka definuje minimální oprávnění PAT potřebná pro GitHub. Další informace o vytvoření PAT v GitHubu najdete v tématu [Vytvoření osobního přístupového tokenu pro příkazový řádek](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Obor  |Popis  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Stav potvrzení přístupu         |
|`repo_deployment`      | Stav nasazení přístupu         |
|`public_repo`     | Přístup k veřejným úložištím         |
|`repo:invite` | Pozvánky na úložiště přístupu |
|`security_events` | Čtení a zápis událostí zabezpečení |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Zapsat háky úložiště         |
|`read:repo_hook`|Číst háky úložiště|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Minimální oprávnění PAT pro Azure Repos

Následující seznam definuje minimální oprávnění PAT potřebná pro Azure Repos. Další informace o vytvoření PAT v Azure Repos najdete v tématu [ověření přístupu pomocí tokenů osobního přístupu](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

| Obor  |  Typ přístupu  |
|---------| ----------|
| `Code`      | Čtení  |
| `Project and team` | Čtení |
| `Identity` | Čtení     |
| `User profile` | Čtení     |
| `Work items` | Čtení    |
| `Service connections` | Číst, dotazovat, spravovat<sup>1</sup>    |

<sup>1</sup> `Service connections` oprávnění je požadováno pouze v případě, že jste povolili automatickou synchronizaci.

## <a name="synchronize-with-source-control"></a>Synchronizovat se správou zdrojových kódů

Pomocí těchto kroků proveďte synchronizaci se správou zdrojových kódů.

1. Vyberte zdroj z tabulky na stránce Správa zdrojového kódu.

2. Kliknutím na **Spustit synchronizaci** spusťte proces synchronizace.

3. Kliknutím na kartu **úlohy synchronizace** zobrazte stav aktuální úlohy synchronizace nebo předchozí.

4. V rozevírací nabídce **Správa zdrojového kódu** vyberte mechanismus správy zdrojového kódu.

    ![Stav synchronizace](./media/source-control-integration/sync-status.png)

5. Kliknutím na úlohu můžete zobrazit výstup úlohy. Následující příklad je výstupem z úlohy synchronizace správy zdrojového kódu.

    ```output
    ===================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzEnvironment.

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

6. Další protokolování je k dispozici výběrem možnosti **všechny protokoly** na stránce Souhrn úlohy synchronizace správy zdrojového kódu. Tyto další položky protokolu vám mohou pomoci při řešení problémů, které mohou nastat při použití správy zdrojového kódu.

## <a name="disconnect-source-control"></a>Odpojit správu zdrojového kódu

Odpojení od úložiště správy zdrojového kódu:

1. V části **Nastavení účtu** v účtu Automation otevřete **Správa zdrojového kódu** .

2. Vyberte mechanismus správy zdrojového kódu, který se má odebrat.

3. Na stránce Souhrn správy zdrojového kódu klikněte na **Odstranit**.

## <a name="handle-encoding-issues"></a>Zpracování potíží s kódováním

Pokud více lidí upravuje Runbooky v úložišti správy zdrojového kódu pomocí různých editorů, může dojít k problémům s kódováním. Další informace o této situaci najdete v tématu [běžné příčiny potíží s kódováním](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues).

## <a name="update-the-pat"></a>Aktualizace PAT

V současné době nemůžete použít Azure Portal k aktualizaci PAT ve správě zdrojového kódu. Pokud vaše PAT vypršela nebo odvolala, můžete v jednom z těchto způsobů aktualizovat správu zdrojového kódu pomocí nového přístupového tokenu:

* Použijte [REST API](/rest/api/automation/sourcecontrol/update).
* Použijte rutinu [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) .

## <a name="next-steps"></a>Další kroky

* Pro integraci správy zdrojového kódu v Azure Automation naleznete v tématu [Azure Automation: integrace správy zdrojového kódu v Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/).  
* Informace o integraci správy zdrojového kódu sady Runbook se sadou Visual Studio Codespaces naleznete v tématu [Azure Automation: integrování správy zdrojového kódu sady Runbook pomocí sady Visual Studio Codespaces](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/).
