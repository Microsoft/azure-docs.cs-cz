---
title: Integrace služby Azure Automation se správou zdrojového kódu služby Azure DevOps
description: Scénář vás provede nastavíte integraci s účtu Azure Automation a Správa služby Azure DevOps zdrojového kódu.
services: automation
author: eamonoreilly
ms.author: eamono
keywords: Azure powershell, Azure DevOps služby, ovládací prvek zdroje, automatizace
ms.service: automation
ms.component: process-automation
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: b06e315cc12856976dce87791b423d10f002c6df
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801433"
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-azure-devops"></a>Scénář Azure Automation – automatizace integraci správy zdrojových kódů s Azure DevOps

> [!NOTE]
> Je nové prostředí pro správu zdrojového kódu. Další informace o novém prostředí najdete v tématu [správy zdrojového kódu (Preview)](source-control-integration.md).

V tomto scénáři máte projekt Azure DevOps, který používáte ke správě Azure Automation. runbooky a konfigurace DSC pod správou zdrojových kódů.

Tento článek popisuje, jak integrovat Azure DevOps s vaším prostředím Azure Automation, takže se stane, průběžná integrace pro každé vrácení se změnami.

## <a name="getting-the-scenario"></a>Získání scénáře

Tento scénář se skládá ze dvou Powershellové runbooky, které můžete importovat přímo z [Galerie Runbooků](automation-runbook-gallery.md) webu Azure portal nebo stahování [Galerie prostředí PowerShell](https://www.powershellgallery.com).

### <a name="runbooks"></a>Runbooky

Runbook | Popis|
--------|------------|
Synchronizace VSTS | Importovat runbooky a konfigurace ze správy zdrojových kódů Azure DevOps při vrácení se změnami se provádí. Je-li spustit ručně, importuje a publikuje všechny runbooky a konfigurace do účtu Automation.| 
Sync-VSTSGit | Importovat runbooky a konfigurace z Azure DevOps v rámci správy zdrojového kódu Gitu při vracení se změnami se provádí. Je-li spustit ručně, importuje a publikuje všechny runbooky a konfigurace do účtu Automation.|

### <a name="variables"></a>Proměnné

Proměnná | Popis|
-----------|------------|
VSToken | Zabezpečte variabilní prostředek, který vytvoříte, která obsahuje osobní přístupový token Azure DevOps. Zjistěte, jak vytvořit osobní přístupový token Azure DevOps na [stránka ověřování Azure DevOps](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

## <a name="installing-and-configuring-this-scenario"></a>Instalace a konfigurace tohoto scénáře

Vytvoření [osobní přístupový token](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) v Azure DevOps, který použijete k synchronizaci runbooky a konfigurace do účtu automation.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPersonalToken.png) 

Vytvoření [zabezpečenou proměnnou](automation-variables.md) ve vašem účtu automation pro uložení osobní přístupový token, tak, aby sada runbook může ověření na Azure DevOps a synchronizovat runbooky a konfigurace do účtu Automation. Můžete pojmenovat tuto VSToken.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSTokenVariable.png)

Naimportujte sadu runbook, která se synchronizuje vaše runbooky a konfigurace do účtu automation. Můžete použít [ukázkové sady runbook Azure DevOps](https://www.powershellgallery.com/packages/Sync-VSTS) nebo [Azure DevOps s využitím Gitu ukázkové sady runbook](https://www.powershellgallery.com/packages/Sync-VSTSGit) z [Galerie prostředí PowerShell](https://www.powershellgallery.com) podle toho, pokud používáte zdroj Azure DevOps ovládací prvek nebo Azure DevOps s úložištěm Git a nasazení do vašeho účtu automation.

![Galerie prostředí PowerShell](media/automation-scenario-source-control-integration-with-VSTS/VSTSPowerShellGallery.png)

Teď můžete [publikovat](automation-creating-importing-runbook.md#publishing-a-runbook) tuto sadu runbook, abyste mohli vytvořit webhook.

![Pulish sady runbook](media/automation-scenario-source-control-integration-with-VSTS/VSTSPublishRunbook.png)

Vytvoření [webhooku](automation-webhooks.md) pro tuto sadu runbook synchronizace VSTS a zadejte parametry, jak je znázorněno níže. Ujistěte se, že zkopírujete adresu url webhooku, potřebovat pro volání služby v Azure DevOps. VSAccessTokenVariableName je název (VSToken) zabezpečené proměnné, kterou jste vytvořili dříve pro uložení osobní přístupový token.

Integrace s Azure DevOps (synchronizace VSTS.ps1) mají následující parametry:

### <a name="sync-vsts-parameters"></a>Parametry synchronizace VSTS

Parametr | Popis|
--------|------------|
WebhookData | Obsahuje informace o vrácení se změnami z volání služby Azure DevOps. Tento parametr měli nechat prázdné.| 
ResourceGroup | Toto je název skupiny prostředků, který obsahuje příslušný účet automation.|
AutomationAccountName | Název účtu služby automation, která se synchronizuje s Azure DevOps.|
VSFolder | Název složky v Azure DevOps, pokud existují runboocích a konfiguracích.|
VSAccount | Název organizace Azure DevOps.|
VSAccessTokenVariableName | Název zabezpečenou proměnnou (VSToken), která obsahuje osobní přístupový token Azure DevOps.|

![Webhook](media/automation-scenario-source-control-integration-with-VSTS/VSTSWebhook.png)

Pokud používáte Azure DevOps s úložištěm GIT (synchronizace VSTSGit.ps1) bude trvat následující parametry.

Parametr | Popis|
--------|------------|
WebhookData | To bude obsahovat informace o vrácení se změnami z volání služby Azure DevOps. Tento parametr měli nechat prázdné.|
ResourceGroup | Tento název skupiny prostředků, který obsahuje příslušný účet automation.|
AutomationAccountName | Název účtu služby automation, která se synchronizuje s Azure DevOps.|
VSAccount | Název organizace Azure DevOps.|
VSProject | Název projektu, kde existují runbooky a konfigurace Azure DevOps.|
GitRepo | Název úložiště Git.|
GitBranch | Název větve v úložišti Git v Azure DevOps.|
Složka | Název složky v Azure DevOps Git branch.|
VSAccessTokenVariableName | Název zabezpečenou proměnnou (VSToken), která obsahuje osobní přístupový token Azure DevOps.|

![GIT Webhooku](media/automation-scenario-source-control-integration-with-VSTS/VSTSGitWebhook.png)

Vytvoření volání služby v Azure DevOps pro vrácení se změnami do složky, která aktivuje tento webhook v kódu vrácení se změnami. Vyberte **Web Hooks** jako služba pro integraci s při vytváření nového předplatného. Další informace o volaných služeb na [dokumentaci pro volaných služeb Azure DevOps](https://www.visualstudio.com/docs/marketplace/integrate/service-hooks/get-started).
![Volání služby](media/automation-scenario-source-control-integration-with-VSTS/VSTSServiceHook.png)

Nyní byste měli být schopni provést všech vrácení se změnami z vašich runboocích a konfiguracích do Azure DevOps a mít tyto automaticky synchronizovat do účtu automation.

![Výstup runbooku synchronizace](media/automation-scenario-source-control-integration-with-VSTS/VSTSSyncRunbookOutput.png)

Pokud spustíte bez Azure DevOps se aktivuje Tato sada runbook ručně, parametr webhookdata můžete nechat prázdné a provádí úplnou synchronizaci z zadaná složka Azure DevOps.

Pokud chcete odinstalovat tento scénář, odeberte volání služby z Azure DevOps, odstraňte sadu runbook a VSToken proměnnou.
