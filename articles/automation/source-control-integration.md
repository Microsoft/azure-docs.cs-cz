---
title: Integrace správy zdrojového kódu ve službě Azure Automation
description: Tento článek popisuje integrace správy zdrojového kódu pomocí GitHub ve službě Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/21/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3b2df5b24a12f3d2ea5d8a03721c08f8d2a742ad
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58539985"
---
# <a name="source-control-integration-in-azure-automation"></a>Integrace správy zdrojového kódu ve službě Azure Automation

Správy zdrojového kódu umožňuje udržovat vaše sady runbook ve vaší službě Automation jsou účet aktuální pomocí skriptů v úložišti Githubu nebo úložiště Azure zdrojového ovládacího prvku. Správy zdrojového kódu můžete snadno spolupracovat se svým týmem, sledovat změny a vrátit zpět na starší verze vašich sadách runbook. Například správy zdrojového kódu můžete synchronizovat různými větvemi ve správě zdrojového kódu pro účty služby Automation vývojové, testovací nebo produkční prostředí. To usnadňuje podporu kód, který byl testován ve vašem vývojovém prostředí účtu Automation v produkčním prostředí. Integrace správy zdrojového kódu pomocí automatizace podporuje jeden směr synchronizaci z svým úložištěm řízení zdrojů.

Azure Automation podporuje tři typy správy zdrojového kódu:

* GitHubu
* Úložiště Azure (Git)
* Úložiště Azure (TFVC)

## <a name="pre-requisites"></a>Požadavky

* Úložiště správy zdrojového kódu (GitHub nebo úložiště Azure)
* A [účet Spustit jako](manage-runas-account.md)
* Zkontrolujte, že máte [nejnovější moduly Azure](automation-update-azure-modules.md) ve vašem účtu Automation

> [!NOTE]
> Úlohy synchronizace řízení zdrojů spustit v části s uživateli účtu Automation a se účtuje stejná sazba jako za jiných úloh služby Automation.

## <a name="configure-source-control---azure-portal"></a>Konfigurace správy zdrojového kódu – Azure portal

V rámci účtu Automation vyberte **správy zdrojových kódů** a klikněte na tlačítko **+ přidat**

![Vyberte správy zdrojového kódu](./media/source-control-integration/select-source-control.png)

Zvolte **– typ ovládacího prvku zdroje**, klikněte na tlačítko **ověřit**. Okno prohlížeče se otevře a zobrazí výzvu k přihlášení, postupujte podle pokynů dokončete ověření.

Na **souhrnný ovládací prvek zdroje** stránce, potřebné informace a klikněte na tlačítko **Uložit**. Následující tabulka obsahuje popis dostupných polí.

|Vlastnost  |Popis  |
|---------|---------|
|Název správy zdrojového kódu     | Popisný název pro správu zdrojového kódu        |
|Typ správy zdrojového kódu     | Typ zdrojového ovládacího prvku zdroje. Dostupné možnosti jsou:</br> GitHubu</br>Úložiště Azure (Git)</br> Úložiště Azure (TFVC)        |
|Úložiště     | Název úložiště nebo projektu. Prvních 200 úložišť jsou vráceny. K vyhledání úložiště, zadejte název do pole a klikněte na tlačítko **hledání na Githubu**.|
|Branch     | Větev o přijetí změn zdrojových souborů ze. Cílení na větev není k dispozici pro typ ovládacího prvku zdroje TFVC.          |
|Cesta ke složce     | Tato složka obsahuje sady runbook k synchronizaci. Příklad: /Runbooks </br>*Jenom runbooky ve složce určené synchronizují. Rekurze se nepodporuje.*        |
|Automatická synchronizace     | Zapne nebo vypne Automatická synchronizace při potvrzení změn do úložiště správy zdrojového kódu         |
|Publikování Runbooku     | Pokud hodnotu **na**po sady runbook se synchronizují ze správy zdrojových kódů, bude automaticky publikován.         |
|Popis     | Textové pole poskytnout další podrobnosti        |

![Přehled ovládacího prvku zdroje](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Ujistěte se, že jste přihlášeni správný účet při konfiguraci správy zdrojového kódu. Pokud je nejisté, otevřete novou kartu v prohlížeči a odhlaste se z visualstudio.com nebo webu github.com a opakujte připojení správy zdrojového kódu.

## <a name="configure-source-control---powershell"></a>Konfigurace správy zdrojového kódu – PowerShell

Prostředí PowerShell můžete také použít ke konfiguraci správy zdrojového kódu ve službě Azure Automation. Konfigurace správy zdrojového kódu pomocí rutin prostředí PowerShell, je potřeba osobní přístupový token PAT. Můžete použít [New-AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl) vytvořit připojení správy zdrojů. Rutina vyžaduje zabezpečený řetězec osobní přístupový Token, se naučíte vytvořit zabezpečený řetězec, naleznete v tématu [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

### <a name="azure-repos-git"></a>Úložiště Azure (Git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Úložiště Azure (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHubu

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>Osobní přístupový token oprávnění

Správy zdrojových kódů vyžaduje některé minimální sadu oprávnění pro osobní přístupové tokeny. Následující tabulky obsahují minimální sadu oprávnění potřebných pro GitHub a úložiště Azure.

#### <a name="github"></a>GitHubu

Další informace o vytvoření osobního přístupového tokenu githubu najdete [vytvoření osobního přístupového tokenu pro příkazový řádek](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Rozsah  |Popis  |
|---------|---------|
|**úložiště**     |         |
|repo:status     | Stav přístupu pro potvrzení změn         |
|repo_deployment      | Stav přístupu pro nasazení         |
|public_repo     | Přístup veřejných úložišť         |
|**admin:repo_hook**     |         |
|write:repo_hook     | Zápis háky úložiště         |
|read:repo_hook|Háky úložiště pro čtení|

#### <a name="azure-repos"></a>Azure Repos

Další informace o vytvoření osobního přístupového tokenu v úložišti Azure, navštivte [ověření přístupu s osobní přístupové tokeny](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

|Rozsah  |
|---------|
|Kódování (čtení)     |
|Projekt a tým (čtení)|
|Identita (čtení)      |
|Profil uživatele (čtení)     |
|Pracovní položky (čtení)    |
|Připojení služby (čtení, dotazování a správu)<sup>1</sup>    |

<sup>1</sup> oprávnění připojení služby je jenom nutné, pokud jste povolili autosync.

## <a name="syncing"></a>Synchronizace

Vyberte zdroj z tabulky na **správy zdrojového kódu** stránky. Klikněte na tlačítko **zahájit synchronizaci** zahájíte proces synchronizace.

Můžete zobrazit stav aktuální úloha synchronizace nebo předchozích balíčcích kliknutím **synchronizovat úlohy** kartu. Na **správy zdrojových kódů** rozevíracího seznamu, vyberte správy zdrojového kódu.

![Stav synchronizace](./media/source-control-integration/sync-status.png)

Kliknutím na úlohu vám umožní zobrazit výstup úlohy. V následujícím příkladu je výstup z úlohy synchronizace zdrojového ovládacího prvku.

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

Dodatečné protokolování je k dispozici výběrem **všechny protokoly** na **Souhrn úlohy synchronizace zdrojového ovládacího prvku** stránky. Tyto položky protokolu další může pomoci při odstraňování problémů, které mohou nastat při použití správy zdrojového kódu.

## <a name="disconnecting-source-control"></a>Odpojení správy zdrojového kódu

Chcete-li odpojit od úložiště správy zdrojového kódu, otevřete **správy zdrojového kódu** pod **nastavení účtu** ve vašem účtu Automation.

Vyberte modul správy zdrojových kódů, které chcete odebrat. Na **souhrnný ovládací prvek zdroje** klikněte na **odstranit**.

## <a name="encoding"></a>Kódování

Pokud najde víc lidí upravujete s různými editory sady runbook v svým úložištěm řízení zdrojů, může se stát narazíte na problémy s kódováním. Tato situace může vést k nesprávné znaky ve své sadě runbook. Další informace o tom najdete v tématu [běžných příčin některých kódování problémy](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="next-steps"></a>Další postup

Další informace o typech runbooků, jejich výhodách a omezeních najdete v článku [Typy runbooků ve službě Azure Automation](automation-runbook-types.md).
