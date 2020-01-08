---
title: Integrace správy zdrojového kódu v Azure Automation
description: Tento článek popisuje integraci správy zdrojového kódu pomocí GitHubu v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 837ebd71886e9435a44080b06c079623c3936c69
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75417073"
---
# <a name="source-control-integration-in-azure-automation"></a>Integrace správy zdrojového kódu ve službě Azure Automation

Správa zdrojového kódu vám umožňuje udržovat vaše Runbooky v účtu Automation v aktuálním stavu pomocí vašich skriptů ve vašem GitHubu nebo v úložišti správy zdrojového kódu Azure Repos. Správa zdrojového kódu umožňuje snadnou spolupráci se svým týmem, sledovat změny a vracet se zpět k předchozím verzím runbooků. Například Správa zdrojového kódu umožňuje synchronizovat různé větve ve správě zdrojového kódu do účtů pro vývoj, testování nebo provoz v rámci služby Automation. Díky tomu je možné snadno zvýšit úroveň kódu, který byl testován ve vašem vývojovém prostředí, na váš účet služby Automation. Integrace správy zdrojového kódu s automatizací podporuje synchronizaci jednoho směru z úložiště správy zdrojového kódu.

Azure Automation podporuje tři typy správy zdrojového kódu:

* GitHubu
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="pre-requisites"></a>Požadavky

* Úložiště správy zdrojového kódu (GitHub nebo Azure Repos)
* [Účet Spustit jako](manage-runas-account.md)
* Ujistěte se, že máte ve svém účtu Automation [nejnovější moduly Azure](automation-update-azure-modules.md) , včetně modulu **AzureRM. Profile** . 

> [!NOTE]
> Úlohy synchronizace správy zdrojového kódu se spouštějí pod účtem automatizace uživatelů a účtují se stejnou sazbou jako ostatní úlohy automatizace.

## <a name="configure-source-control---azure-portal"></a>Konfigurovat správu zdrojového kódu – Azure Portal

V rámci účtu Automation vyberte **Správa zdrojového kódu** a klikněte na **+ Přidat** .

![Vybrat správu zdrojového kódu](./media/source-control-integration/select-source-control.png)

Vyberte **typ správy zdrojového kódu**, klikněte na tlačítko **ověřit**. Otevře se okno prohlížeče s výzvou, abyste se přihlásili. Dokončete ověření podle pokynů.

Na stránce **Souhrn správy zdrojových kódů** vyplňte informace a klikněte na **Uložit**. V následující tabulce je uveden popis dostupných polí.

|Vlastnost  |Popis  |
|---------|---------|
|Název správy zdrojového kódu     | Popisný název pro správu zdrojového kódu. *Tento název musí obsahovat jenom písmena a číslice.*        |
|Typ správy zdrojového kódu     | Typ zdroje správy zdrojového kódu. Dostupné možnosti jsou:</br> GitHubu</br>Azure Repos (Git)</br> Azure Repos (TFVC)        |
|Úložiště     | Název úložiště nebo projektu. Vrátí se první 200 úložišť. Pokud chcete vyhledat úložiště, zadejte název do pole a klikněte na **Hledat na GitHubu**.|
|Branch     | Větev, ze které se mají načíst zdrojové soubory. Cílení na větev není k dispozici pro typ správy zdrojového kódu TFVC.          |
|Cesta ke složce     | Složka obsahující Runbooky, které se mají synchronizovat Příklad:/runbooks </br>*Budou synchronizovány pouze Runbooky v zadané složce. Rekurze není podporována.*        |
|Automatická synchronizace<sup>1</sup>     | Zapne nebo vypne automatickou synchronizaci při provedení potvrzení v úložišti správy zdrojových kódů.         |
|Publikování Runbooku     | Pokud je nastavené na **zapnuto**, po synchronizaci sad Runbook se správou zdrojových kódů budou automaticky publikovány.         |
|Popis     | Textové pole, které poskytuje další podrobnosti        |

<sup>1</sup> Chcete-li povolit automatickou synchronizaci při konfiguraci integrace správy zdrojového kódu pomocí Azure Repos, musíte být správcem projektu.

![Souhrn správy zdrojového kódu](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Vaše přihlašovací údaje pro úložiště správy zdrojového kódu se můžou lišit od přihlášení k Azure Portal. Ujistěte se, že jste se přihlásili se správným účtem pro úložiště správy zdrojového kódu při konfiguraci správy zdrojového kódu. V případě pochybnosti otevřete v prohlížeči novou kartu a odhlaste se z visualstudio.com nebo github.com a pokuste se znovu připojit správu zdrojového kódu.

## <a name="configure-source-control---powershell"></a>Konfigurace správy zdrojového kódu – PowerShell

PowerShell můžete také použít ke konfiguraci správy zdrojového kódu v Azure Automation. Pokud chcete nakonfigurovat správu zdrojového kódu pomocí rutin PowerShellu, je potřeba použít token PAT (Personal Access token). Pomocí [New-AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl) vytvoříte připojení správy zdrojového kódu. Rutina vyžaduje zabezpečený řetězec osobního přístupového tokenu, abyste se dozvěděli, jak vytvořit zabezpečený řetězec, viz [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

### <a name="azure-repos-git"></a>Azure Repos (Git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Azure Repos (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHubu

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>Oprávnění tokenu pro osobní přístup

Správa zdrojového kódu vyžaduje minimální oprávnění pro tokeny osobních přístupů. Následující tabulky obsahují minimální oprávnění požadovaná pro GitHub a Azure Repos.

#### <a name="github"></a>GitHubu

Další informace o vytvoření osobního přístupového tokenu v GitHubu najdete v [části Vytvoření osobního přístupového tokenu pro příkazový řádek](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Rozsah  |Popis  |
|---------|---------|
|**úložiště**     |         |
|repo:status     | Stav potvrzení přístupu         |
|repo_deployment      | Stav nasazení přístupu         |
|public_repo     | Přístup k veřejným úložištím         |
|**admin:repo_hook**     |         |
|write:repo_hook     | Zapsat háky úložiště         |
|read:repo_hook|Číst háky úložiště|

#### <a name="azure-repos"></a>Azure Repos

Další informace o vytvoření osobního přístupového tokenu v Azure Repos najdete na stránce [ověření přístupu pomocí tokenů osobních přístupů](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

|Rozsah  |
|---------|
|Kód (čtení)     |
|Projekt a tým (čtení)|
|Identita (čtení)      |
|Profil uživatele (čtení)     |
|Pracovní položky (čtení)    |
|Připojení služby (čtení, dotazování a správa)<sup>1</sup>    |

<sup>1</sup> oprávnění připojení služby se vyžaduje jenom v případě, že jste povolili automatickou synchronizaci.

## <a name="syncing"></a>Synchronizaci

Vyberte zdroj z tabulky na stránce **Správa zdrojového kódu** . Kliknutím na **Spustit synchronizaci** spusťte proces synchronizace.

Stav aktuální úlohy synchronizace nebo předchozích úloh můžete zobrazit kliknutím na kartu **úlohy synchronizace** . V rozevíracím seznamu **Správa zdrojového kódu** vyberte ovládací prvek zdroje.

![Stav synchronizace](./media/source-control-integration/sync-status.png)

Kliknutím na úlohu můžete zobrazit výstup úlohy. Následující příklad je výstupem z úlohy synchronizace správy zdrojového kódu.

```output
========================================================================================================

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



========================================================================================================
```

Další protokolování je k dispozici výběrem možnosti **všechny protokoly** na stránce **Souhrn úlohy synchronizace správy zdrojového kódu** . Tyto další položky protokolu vám mohou pomoci při řešení problémů, které mohou nastat při použití správy zdrojového kódu.

## <a name="disconnecting-source-control"></a>Odpojuje se Správa zdrojového kódu.

Pokud se chcete odpojit od úložiště správy zdrojového kódu, otevřete **správu zdrojového kódu** v části **Nastavení účtu** v účtu Automation.

Vyberte správu zdrojového kódu, kterou chcete odebrat. Na stránce **Souhrn správy zdrojového kódu** klikněte na **Odstranit**.

## <a name="encoding"></a>Encoding

Pokud více lidí upravuje Runbooky v úložišti správy zdrojového kódu s různými editory, je pravděpodobné, že je možné spouštět problémy s kódováním. Tato situace může vést k nesprávným znakům v Runbooku. Další informace najdete v tématu [běžné příčiny potíží s kódováním](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues) .

## <a name="updating-the-access-token"></a>Aktualizace přístupového tokenu

V současné době neexistuje způsob, jak aktualizovat přístupový token ve správě zdrojového kódu z portálu. Po vypršení nebo odvolání vašeho osobního přístupového tokenu můžete aktualizovat správu zdrojového kódu pomocí nového přístupového tokenu následujícími způsoby:

* Přes [rozhraní REST API](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Pomocí rutiny [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) .

## <a name="next-steps"></a>Další kroky

Další informace o typech runbooků, jejich výhodách a omezeních najdete v článku [Typy runbooků ve službě Azure Automation](automation-runbook-types.md).
