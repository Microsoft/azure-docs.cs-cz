---
title: Integrace ovládacích prvků zdrojového služby Azure Automation s Enterprise Githubu
description: Popisuje postup konfigurace integrace s GitHub Enterprise pro zdrojového kódu runbooků služeb automatizace podrobnosti.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/17/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 65894e40b192c6a5a226fa7a1dfb5cb0cfabb972
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-github-enterprise"></a>Azure Automation scénář – integrace ovládacích prvků zdrojového automatizace s Enterprise Githubu

Automatizace aktuálně podporuje integrace ovládacích prvků zdrojového, což vám umožní přidružit sady runbook ve vašem účtu Automation se úložištěm řízení zdrojů Githubu. Ale zákazníkům, kteří mají nasazenou [Githubu Enterprise](https://enterprise.github.com/home) pro podporu postupy jejich DevOps, také chtít použít ho ke správě životního cyklu sad runbook, která jsou vytvořena, aby automatizovat firemní procesy a služby management operations.

V tomto scénáři máte počítač se systémem Windows ve vašem datovém centru, který je nakonfigurovaný jako hybridní pracovní proces Runbooku s moduly Azure Resource Manageru a nainstalované nástroje Git. Tento počítač hybridní pracovní má klon místní úložiště Git. Při spuštění sady runbook na hybridní pracovní proces se synchronizuje adresář Git a obsah souboru sady runbook se importují do účtu Automation.

Tento článek popisuje postup nastavení této konfigurace ve vašem prostředí Azure Automation. Spustíte tak, že nakonfigurujete automatizace s zabezpečovací přihlašovací údaje, sady runbook, které jsou potřeba pro podporu tohoto scénáře a nasazení hybridní pracovní proces Runbooku ve vašem datovém centru pro spuštění sady runbook a přístupu k úložiště GitHub Enterprise k synchronizaci sady runbook s vaším účtem Automation.

## <a name="getting-the-scenario"></a>Získání scénáře

Tento scénář se skládá ze dvou Powershellové runbooky, které můžete importovat přímo z [Galerie Runbooků](automation-runbook-gallery.md) v portálu Azure nebo stažení [Galerie prostředí PowerShell](https://www.powershellgallery.com).

### <a name="runbooks"></a>Runbooky

Runbook | Popis|
--------|------------|
Export-RunAsCertificateToHybridWorker | Runbook Exportuje certifikát RunAs z účtu Automation do hybridního pracovního procesu tak, aby sady runbook v pracovním procesu můžete ověřit pomocí Azure, aby bylo možné naimportovat sady runbook do účtu Automation.|
Synchronizace LocalGitFolderToAutomationAccount | Runbook synchronizuje místní složce na počítači hybridní Git a následnému importování souborů služby runbook (*.ps1) do účtu Automation.|

### <a name="credentials"></a>Přihlašovací údaje

Přihlašovací údaj | Popis|
-----------|------------|
GitHRWCredential | Asset přihlašovacích údajů, můžete vytvořit tak, aby obsahovala uživatelské jméno a heslo pro uživatele s oprávněními k hybridní pracovní proces.|

## <a name="installing-and-configuring-this-scenario"></a>Instalace a konfigurace tohoto scénáře

### <a name="prerequisites"></a>Požadavky

1. Sada runbook LocalGitFolderToAutomationAccount synchronizace se ověří pomocí [účet spustit v Azure jako](automation-sec-configure-azure-runas-account.md).

2. Pracovní prostor analýzy protokolů Azure Automation řešení povolené a nakonfigurované je také nutný. Pokud nemáte jednu, která souvisí s automatizace účet použitý k instalaci a konfiguraci tohoto scénáře, se vytvoří a konfigurace. při spuštění **New-OnPremiseHybridWorker.ps1** skript z hybridní pracovní proces runbooku.

    > [!NOTE]
    > Aktuálně následující oblasti podporují pouze automatizace integrace s analýzy protokolů - **Austrálie – jihovýchod**, **východní USA 2**, **jihovýchodní Asie**, a  **Západní Evropa**.

3. Počítač, který může sloužit jako vyhrazené hybridní Runbook Worker hostujícím softwaru Githubu a udržovat souborů služby runbook (*runbook*.ps1) v zdrojový adresář v systému souborů k synchronizaci mezi Githubu a váš automatizace účet.

### <a name="import-and-publish-the-runbooks"></a>Import a publikovat sady runbook

K importu *Export RunAsCertificateToHybridWorker* a *synchronizace LocalGitFolderToAutomationAccount* sady runbook z Galerie sady Runbook z vašeho účtu Automation na portálu Azure, postupujte podle pokynů v [importovat Runbook z Galerie Runbooků](automation-runbook-gallery.md#to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal). Publikování sady runbook poté, co byly úspěšně importovány do vašeho účtu Automation.

### <a name="deploy-and-configure-hybrid-runbook-worker"></a>Nasaďte a nakonfigurujte hybridní pracovní proces Runbooku

Pokud nemáte Hybrid Runbook Worker už nasazená ve vašem datovém centru, zkontrolujte požadavky a postupujte podle kroků automatická instalace pomocí postupu v Azure Automation procesy Hybrid Runbook Worker - automatizaci instalace a konfigurace [Windows](automation-windows-hrw-install.md#automated-deployment) nebo [Linux](automation-linux-hrw-install.md#installing-linux-hybrid-runbook-worker). Úspěšně jste nainstalovali hybridní pracovní proces na počítači, proveďte následující kroky k dokončení její konfigurace pro podporu tohoto scénáře.

1. Přihlaste se k počítači, který hostuje roli hybridní pracovní proces Runbooku pomocí účtu, který má práva místního správce a vytvořte adresář k uložení souborů služby runbook Git. Klonování interní úložiště Git do adresáře.
1. Pokud již jste vytvořili účet RunAs, nebo chcete vytvořit novou jeden vyhrazený pro tento účel, z portálu Azure přejděte do účty Automation, vyberte svůj účet Automation a vytvořte [asset přihlašovacích údajů](automation-credentials.md) , obsahuje uživatelské jméno a heslo pro uživatele s oprávněními k hybridní pracovní proces.
1. Z vašeho účtu Automation [upravit runbook](automation-edit-textual-runbook.md)**Export RunAsCertificateToHybridWorker** a změňte hodnotu proměnné *$Password* silným heslem.  Po úpravě hodnota, klikněte na tlačítko **publikovat** má verzi konceptu sady runbook publikovat.
1. Spuštění runbooku **Export RunAsCertificateToHybridWorker**a v **spuštění Runbooku** okno, v části možnost **spustit nastavení** vyberte možnost **hybridní pracovní proces** a v rozevíracím seznamu vyberte skupinu hybridních pracovních procesů jste vytvořili dříve v tomto scénáři.

    To, aby se runbook na pracovní může ověřit s Azure pomocí připojení spustit jako, abyste mohli spravovat prostředky Azure (zejména v tomto scénáři – import sady runbook k účtu Automation) Exportuje certifikát do hybridní pracovní proces.

1. Z vašeho účtu Automation, vyberte skupinu hybridních pracovních procesů, které je vytvořený a [zadejte účet Spustit jako](automation-hrw-run-runbooks.md#runas-account) pro skupinu hybridních pracovních procesů a vyberte právě nebo již jste vytvořili asset přihlašovacích údajů. To zaručuje, že sada runbook synchronizaci můžete spustit příkazy Gitu. 
1. Spuštění runbooku **synchronizace LocalGitFolderToAutomationAccount**, zadejte požadované hodnoty vstupní parametr a v **spuštění Runbooku** okno, v části možnost **spustit nastavení** vyberte možnost **hybridní pracovní proces** a v rozevíracím seznamu vyberte skupinu hybridních pracovních procesů jste vytvořili dříve v tomto scénáři:

   * *ResourceGroup* -název vaší skupiny prostředků spojené s vaším účtem Automation.
   * *AutomationAccountName* – název účtu Automation
   * *GitPath* -místní složku nebo soubor na hybridní pracovní proces Runbooku kterém Git nastavit vyžádání nejnovější změny

    Tím se synchronizuje místní Git složky v počítači hybridní pracovní a poté importuje soubory .ps1 ze zdrojového adresáře do účtu Automation.

1. Zobrazit podrobnosti úlohy souhrnu pro sadu runbook tak, že vyberete z **Runbooky** okno v účtu Automation a potom vyberte **úlohy** dlaždici. Potvrďte byla úspěšně dokončena výběrem **všechny protokoly** dlaždice a kontrola datový proud podrobné protokolu.

## <a name="next-steps"></a>Další postup

* Další informace o typech runbooků, jejich výhodách a omezeních najdete v článku [Typy runbooků ve službě Azure Automation](automation-runbook-types.md).
* Další informace o funkci podpory powershellových skriptů najdete v článku [Nativní podpora powershellových skriptů ve službě Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
