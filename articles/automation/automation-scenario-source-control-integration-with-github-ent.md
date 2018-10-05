---
title: Integrace správy služby Azure Automation zdrojového kódu pomocí GitHub Enterprise
description: Popisuje podrobnosti o tom, jak konfigurovat integraci pomocí GitHub Enterprise pro správu zdrojového kódu, sad runbook služby Automation.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 09/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d987e7d1e47e2e88c2f615e1d1bb5446c575ef47
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2018
ms.locfileid: "48785194"
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-github-enterprise"></a>Scénář Azure Automation – integrace správy zdrojového kódu automatizace pomocí GitHub Enterprise

Automatizace v současnosti podporuje integraci správy zdrojového kódu, který vám umožní přidružit sady runbook ve vašem účtu Automation a úložištěm správy zdrojového kódu na Githubu. Ale zákazníci, kteří mají nasazenou [Githubu Enterprise](https://enterprise.github.com/home) pro podporu jejich postupů DevOps, také chtít použít ho ke správě životního cyklu sad runbook, které jsou vyvíjeny k automatizaci obchodních procesů a službu pro operace správy.

V tomto scénáři máte počítač Windows ve vašem datovém centru, který je nakonfigurovaný jako Hybrid Runbook Worker s nainstalovanými moduly Azure Resource Manageru a nainstalované nástroje Git. Počítač hybridního pracovního procesu má klon místní úložiště Git. Při spuštění sady runbook v procesu hybrid worker, se synchronizuje adresář Git a obsah souboru sady runbook se importují do účtu Automation.

Tento článek popisuje, jak nastavit tuto konfiguraci v prostředí Azure Automation. Začnete tím, že nakonfigurování zabezpečovací přihlašovací údaje, sady runbook, které jsou potřeba pro podporu tohoto scénáře a nasazení funkce Hybrid Runbook Worker ve vašem datovém centru ke spuštění sady runbook a přístup k úložišti Githubu Enterprise pro synchronizaci runbooků služby Automation s účtem Automation.

## <a name="getting-the-scenario"></a>Získání scénáře

Tento scénář se skládá ze dvou Powershellové runbooky, které můžete importovat přímo z [Galerie Runbooků](automation-runbook-gallery.md) webu Azure portal nebo stahování [Galerie prostředí PowerShell](https://www.powershellgallery.com).

### <a name="runbooks"></a>Runbooky

Runbook | Popis|
--------|------------|
Export-RunAsCertificateToHybridWorker | Runbook Exportuje certifikát RunAs z účtu služby Automation hybrid worker tak, aby sad runbook na pracovní proces může ověřování pomocí Azure, aby bylo možné naimportovat sady runbook do účtu Automation.|
Synchronizace LocalGitFolderToAutomationAccount | Runbook synchronizuje místní složky Gitu v hybridní počítače a pak můžete importovat soubory sady runbook (*.ps1) do účtu Automation.|

### <a name="credentials"></a>Přihlašovací údaje

Přihlašovací údaj | Popis|
-----------|------------|
GitHRWCredential | Asset přihlašovacích údajů, můžete vytvořit tak, aby obsahovala uživatelské jméno a heslo pro uživatele s oprávněními k procesu hybrid worker.|

## <a name="installing-and-configuring-this-scenario"></a>Instalace a konfigurace tohoto scénáře

### <a name="prerequisites"></a>Požadavky

1. Synchronizace LocalGitFolderToAutomationAccount runbook se ověřuje pomocí [účet Spustit jako pro Azure](automation-sec-configure-azure-runas-account.md).

2. Pracovní prostor Log Analytics pomocí Azure Automation řešení povolené a nakonfigurované, je také nutný. Pokud nemáte takový, který je přidružený k účtu Automation, použít k instalaci a konfiguraci tohoto scénáře, se vytvoří a nakonfigurují automaticky po spuštění **New-OnPremiseHybridWorker.ps1** skriptu ze sady runbook hybridní pracovního procesu.

    > [!NOTE]
    > Aktuálně následující oblasti podporují pouze integrace služby Automation s Log Analytics – **Austrálie – jihovýchod**, **USA – východ 2**, **jihovýchodní Asie**, a  **Západní Evropa**.

3. Počítač, který může sloužit jako vyhrazené Hybrid Runbook Worker, který je hostitelem také Githubu softwaru a udržovat soubory sady runbook (*runbook*.ps1) do zdrojového adresáře v systému souborů pro synchronizaci mezi Githubem a automatizace účet.

### <a name="import-and-publish-the-runbooks"></a>Import a publikování sady runbook

K importu *Export RunAsCertificateToHybridWorker* a *synchronizace LocalGitFolderToAutomationAccount* sad runbook z Galerie Runbooků ve svém účtu Automation na webu Azure Portal, postupujte postupy v [importovat Runbook z Galerie Runbooků](automation-runbook-gallery.md#to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal). Publikování sady runbook po jejich byly úspěšně naimportovány do účtu Automation.

### <a name="deploy-and-configure-hybrid-runbook-worker"></a>Nasazení a konfigurace procesu Hybrid Runbook Worker

Pokud už nasazené ve vašem datovém centru pomocí procesu Hybrid Runbook Worker, zkontrolujte požadavky a postupujte podle kroků automatická instalace pomocí postupu v Azure Hybrid Runbook Worker služby Automation – automatizace instalace a konfigurace pro [Windows](automation-windows-hrw-install.md#automated-deployment) nebo [Linux](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker). Po procesu hybrid worker úspěšně instalaci na počítač, proveďte následující kroky k dokončení její konfigurace pro podporu tohoto scénáře.

1. Přihlaste se k počítači, který hostuje roli pracovního procesu Hybrid Runbook Worker pomocí účtu, který má práva místního správce a vytvořte adresář pro uložení souborů Git sady runbook. Naklonujte interní úložiště Git do adresáře.
1. Pokud ještě nemáte účet Spustit jako vytvořit nebo chcete vytvořit nový jeden vyhrazený pro tento účel, na webu Azure Portal přejděte na účty Automation, vyberte svůj účet Automation a vytvořte [asset přihlašovacích údajů](automation-credentials.md) , který obsahuje uživatelské jméno a heslo pro uživatele s oprávněními k procesu hybrid worker.
1. Ve svém účtu Automation [sadu runbook upravovat](automation-edit-textual-runbook.md)**Export RunAsCertificateToHybridWorker** a změňte hodnotu pro proměnnou *$Password* silným heslem.    Po úpravě hodnoty klikněte na tlačítko **publikovat** má verzi konceptu sady runbook publikovat.
1. Spuštění sady runbook **Export RunAsCertificateToHybridWorker**a **spustit Runbook** okně v části Možnosti **parametry běhu** vyberte možnost  **Hybridní pracovní proces** a v rozevíracím seznamu vyberte skupina hybridních pracovních procesů jste vytvořili dříve v tomto scénáři.

    To Exportuje certifikát do procesu hybrid worker, aby sad runbook na pracovního procesu můžou ověřovat pomocí Azure s využitím připojení spustit jako, abyste mohli spravovat prostředky Azure (zejména v tomto scénáři – import sad runbook do účtu Automation).

1. Ve svém účtu Automation vyberte skupinu hybridních pracovních procesů vytvořené dříve a [zadejte účet Spustit jako](automation-hrw-run-runbooks.md#runas-account) pro skupinu hybridních pracovních procesů a zvolili právě nebo už jste vytvořili asset přihlašovacích údajů. To zaručuje, že runbook synchronizace se může spustit příkazy Gitu. 
1. Spuštění sady runbook **LocalGitFolderToAutomationAccount synchronizace**, zadejte požadované hodnoty vstupního parametru a **spustit Runbook** okně v části Možnosti **nastavení spuštění**  vyberte možnost **Hybrid Worker** a v rozevíracím seznamu vyberte skupina hybridních pracovních procesů jste vytvořili dříve v tomto scénáři:

   * *Skupina prostředků* – název vaší skupiny prostředků přidružené k účtu služby Automation
   * *AutomationAccountName* – název účtu služby Automation
   * *GitPath* – místní složku nebo soubor, na kdy Git je nastaven na vyžádání nejnovější změny procesu Hybrid Runbook Worker

    Tím synchronizuje místní složky Gitu v počítači hybridních pracovních procesů a poté importuje soubory .ps1 ze zdrojového adresáře do účtu Automation.

1. Zobrazení souhrnných detailů úlohy pro runbook tak, že ho vyberete **sady Runbook** okna účtu Automation a pak vyberte **úlohy** dlaždici. Potvrďte správnost tak, že vyberete **všechny protokoly** dlaždice a kontrola podrobnému protokolu datového proudu.

## <a name="next-steps"></a>Další postup

* Další informace o typech runbooků, jejich výhodách a omezeních najdete v článku [Typy runbooků ve službě Azure Automation](automation-runbook-types.md).
* Další informace o funkci podpory powershellových skriptů najdete v článku [Nativní podpora powershellových skriptů ve službě Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
