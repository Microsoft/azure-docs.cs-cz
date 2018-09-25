---
title: Integrace správy zdrojového kódu ve službě Azure Automation
description: Tento článek popisuje integrace správy zdrojového kódu pomocí GitHub ve službě Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c2d13a409d095bca64da781e5c5ca58553f9710c
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047321"
---
# <a name="source-control-integration-in-azure-automation"></a>Integrace správy zdrojového kódu ve službě Azure Automation

Správy zdrojového kódu umožňuje udržovat vaše sady runbook v automatizace účtu se až do data pomocí skriptů v úložišti Githubu nebo Azure Dev Ops zdrojového ovládacího prvku. Správy zdrojového kódu můžete snadno spolupracovat se svým týmem, sledovat změny a vrátit zpět na starší verze vašich sadách runbook. Například správy zdrojového kódu umožňuje synchronizovat různými větvemi ve správě zdrojového kódu pro vývojové, testovací nebo produkční prostředí účty služby Automation, což usnadňuje podporu kód, který byl testován ve vašem vývojovém prostředí automatizace v produkčním prostředí účet.

Azure Automation podporuje 3 typy správy zdrojového kódu:

* GitHubu
* Visual Studio Team Services (Git)
* Visual Studio Team Services (TFVC)

## <a name="pre-requisites"></a>Požadavky

* Úložiště správy zdrojového kódu (GitHub nebo Visual Studio Team Services)
* A [Run-As účet a připojení](manage-runas-account.md)

> [!NOTE]
> Úlohy synchronizace řízení zdrojů spustit v části s uživateli účtu Automation a se účtuje stejná sazba jako za jiných úloh služby Automation.

## <a name="configure-source-control"></a>Konfigurace správy zdrojového kódu

V rámci účtu Automation vyberte **správy zdrojového kódu (preview)** a klikněte na tlačítko **+ přidat**

![Vyberte správy zdrojového kódu](./media/source-control-integration/select-source-control.png)

Zvolte **– typ ovládacího prvku zdroje** , klikněte na tlačítko **ověřit**.

Zkontrolujte na stránku žádosti o oprávnění aplikace a klikněte na **přijmout**.

Na **souhrnný ovládací prvek zdroje** stránce, potřebné informace a klikněte na tlačítko **Uložit**. Následující tabulka obsahuje popis dostupných polí.

|Vlastnost  |Popis  |
|---------|---------|
|Název správy zdrojového kódu     | Popisný název pro správu zdrojového kódu        |
|Typ správy zdrojového kódu     | Typ zdrojového ovládacího prvku zdroje. Dostupné možnosti jsou:</br> GitHub</br>Visual Studio Team Services (Git)</br>Visual Studio Team Services (TFVC)        |
|Úložiště     | Název úložiště nebo projektu. To se načítají z úložiště správy zdrojového kódu. Příklad: $/ ContosoFinanceTFVCExample         |
|Větev     | Větev o přijetí změn zdrojových souborů ze. Cílení na větev není k dispozici pro typ ovládacího prvku zdroje TFVC.          |
|Cesta ke složce     | Tato složka obsahuje sady runbook k synchronizaci. Příklad: /Runbooks         |
|Automatická synchronizace     | Zapne nebo vypne Automatická synchronizace při potvrzení změn do úložiště správy zdrojového kódu         |
|Publikování Runbooku     | Pokud hodnotu **na**po sady runbook se synchronizují ze správy zdrojových kódů, bude automaticky publikován.         |
|Popis     | Textové pole poskytnout další podrobnosti        |

![Přehled ovládacího prvku zdroje](./media/source-control-integration/source-control-summary.png)

## <a name="syncing"></a>Synchronizace

Pokud automatickou synchronizaci byl nastaven při konfiguraci integrace správy zdrojového kódu, začnou automaticky počáteční synchronizace. Pokud nebyla nastavena automatická synchronizace, vyberte zdroj z tabulky na **(Preview) správy zdrojového kódu** stránky. Klikněte na tlačítko **zahájit synchronizaci** zahájíte proces synchronizace.  

Můžete zobrazit stav aktuální úloha synchronizace nebo předchozích balíčcích kliknutím **synchronizovat úlohy** kartu. Na **správy zdrojových kódů** rozevíracího seznamu, vyberte správy zdrojového kódu.

![Stav synchronizace](./media/source-control-integration/sync-status.png)

Kliknutím na úlohu vám umožní zobrazit výstup úlohy. Následuje příklad výstupu z úlohy synchronizace zdrojového ovládacího prvku.

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

## <a name="disconnecting-source-control"></a>Odpojení správy zdrojového kódu

Chcete-li odpojit od úložiště správy zdrojového kódu, otevřete **(Preview) správy zdrojového kódu** pod **nastavení účtu** ve vašem účtu Automation.

Vyberte modul správy zdrojových kódů, které chcete odebrat. Na **souhrnný ovládací prvek zdroje** klikněte na **odstranit**.

## <a name="next-steps"></a>Další postup

Další informace o typech runbooků, jejich výhodách a omezeních najdete v článku [Typy runbooků ve službě Azure Automation](automation-runbook-types.md).
