---
title: Integrace správy zdrojového kódu ve službě Azure Automation
description: Tento článek popisuje integrace správy zdrojového kódu pomocí GitHub ve službě Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/26/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 95355a6d1a0aff9829d75789df86f37768d25e22
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53342234"
---
# <a name="source-control-integration-in-azure-automation"></a>Integrace správy zdrojového kódu ve službě Azure Automation

Správy zdrojového kódu umožňuje udržovat vaše sady runbook ve vaší službě Automation jsou účet aktuální pomocí skriptů v úložišti Githubu nebo Azure DevOps zdrojového ovládacího prvku. Správy zdrojového kódu můžete snadno spolupracovat se svým týmem, sledovat změny a vrátit zpět na starší verze vašich sadách runbook. Například správy zdrojového kódu můžete synchronizovat různými větvemi ve správě zdrojového kódu pro účty služby Automation vývojové, testovací nebo produkční prostředí. To usnadňuje podporu kód, který byl testován ve vašem vývojovém prostředí účtu Automation v produkčním prostředí.

Azure Automation podporuje 3 typy správy zdrojového kódu:

* GitHubu
* Azure DevOps (Git)
* Azure DevOps (TFVC)

## <a name="pre-requisites"></a>Požadavky

* Úložiště správy zdrojového kódu (GitHub nebo Azure DevOps)
* Správné [oprávnění](#personal-access-token-permissions) do úložiště správy zdrojového kódu
* A [Run-As účet a připojení](manage-runas-account.md)

> [!NOTE]
> Úlohy synchronizace řízení zdrojů spustit v části s uživateli účtu Automation a se účtuje stejná sazba jako za jiných úloh služby Automation.

## <a name="configure-source-control"></a>Konfigurace správy zdrojového kódu

V rámci účtu Automation vyberte **správy zdrojového kódu (preview)** a klikněte na tlačítko **+ přidat**

![Vyberte správy zdrojového kódu](./media/source-control-integration/select-source-control.png)

Zvolte **– typ ovládacího prvku zdroje**, klikněte na tlačítko **ověřit**.

Zkontrolujte na stránku žádosti o oprávnění aplikace a klikněte na **přijmout**.

Na **souhrnný ovládací prvek zdroje** stránce, potřebné informace a klikněte na tlačítko **Uložit**. Následující tabulka obsahuje popis dostupných polí.

|Vlastnost  |Popis  |
|---------|---------|
|Název správy zdrojového kódu     | Popisný název pro správu zdrojového kódu        |
|Typ správy zdrojového kódu     | Typ zdrojového ovládacího prvku zdroje. Dostupné možnosti jsou:</br> GitHubu</br>Azure DevOps (Git)</br> Azure DevOps (TFVC)        |
|Úložiště     | Název úložiště nebo projektu. Tato hodnota se načítají z úložiště správy zdrojového kódu. Příklad: $/ ContosoFinanceTFVCExample         |
|Větev     | Větev o přijetí změn zdrojových souborů ze. Cílení na větev není k dispozici pro typ ovládacího prvku zdroje TFVC.          |
|Cesta ke složce     | Tato složka obsahuje sady runbook k synchronizaci. Příklad: /Runbooks         |
|Automatická synchronizace     | Zapne nebo vypne Automatická synchronizace při potvrzení změn do úložiště správy zdrojového kódu         |
|Publikování Runbooku     | Pokud hodnotu **na**po sady runbook se synchronizují ze správy zdrojových kódů, bude automaticky publikován.         |
|Popis     | Textové pole poskytnout další podrobnosti        |

![Přehled ovládacího prvku zdroje](./media/source-control-integration/source-control-summary.png)

## <a name="syncing"></a>Synchronizace

Při konfiguraci integrace správy zdrojového kódu, konfigurace autosync, počáteční synchronizace automaticky spustí. Pokud nebyla nastavena automatická synchronizace, vyberte zdroj z tabulky na **(Preview) správy zdrojového kódu** stránky. Klikněte na tlačítko **zahájit synchronizaci** zahájíte proces synchronizace.

Můžete zobrazit stav aktuální úloha synchronizace nebo předchozích balíčcích kliknutím **synchronizovat úlohy** kartu. Na **správy zdrojových kódů** rozevíracího seznamu, vyberte správy zdrojového kódu.

![Stav synchronizace](./media/source-control-integration/sync-status.png)

Kliknutím na úlohu vám umožní zobrazit výstup úlohy. V následujícím příkladu je výstup z úlohy synchronizace zdrojového ovládacího prvku.

```output
========================================================================================================

Azure Automation Source Control Public Preview.
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

## <a name="personal-access-token-permissions"></a>Osobní přístupový token oprávnění

Správy zdrojových kódů vyžaduje některé minimální sadu oprávnění pro osobní přístupové tokeny. Následující tabulky obsahují minimální sadu oprávnění potřebných pro GitHub a Azure DevOps.

### <a name="github"></a>GitHubu

|Rozsah  |Popis  |
|---------|---------|
|**úložiště**     |         |
|úložiště: stav     | Stav přístupu pro potvrzení změn         |
|repo_deployment      | Stav přístupu pro nasazení         |
|public_repo     | Přístup veřejných úložišť         |
|**Admin: repo_hook**     |         |
|zápis: repo_hook     | Zápis háky úložiště         |
|čtení: repo_hook|Háky úložiště pro čtení|

### <a name="azure-devops"></a>Azure DevOps

|Rozsah  |
|---------|
|Kódování (čtení)     |
|Projekt a tým (čtení)|
|Identita (čtení)      |
|Profil uživatele (čtení)     |
|Pracovní položky (čtení)    |
|Připojení služby (čtení, dotazování a správu)<sup>1</sup>    |

<sup>1</sup>oprávnění k připojení k Service je jenom nutné, pokud jste povolili autosync.

## <a name="disconnecting-source-control"></a>Odpojení správy zdrojového kódu

Chcete-li odpojit od úložiště správy zdrojového kódu, otevřete **(Preview) správy zdrojového kódu** pod **nastavení účtu** ve vašem účtu Automation.

Vyberte modul správy zdrojových kódů, které chcete odebrat. Na **souhrnný ovládací prvek zdroje** klikněte na **odstranit**.

## <a name="next-steps"></a>Další postup

Další informace o typech runbooků, jejich výhodách a omezeních najdete v článku [Typy runbooků ve službě Azure Automation](automation-runbook-types.md).
