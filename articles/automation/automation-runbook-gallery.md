---
title: Galerie runbooků a modulů pro Azure Automation
description: Sady Runbook a moduly od Microsoftu a komunity jsou k dispozici pro instalaci a použití v prostředí Azure Automation.  Tento článek popisuje, jak můžete získat přístup k těmto prostředkům a přispět sady Runbook do galerie.
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: 90b475e275598363314c8f131911fe12650cd3df
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535549"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Galerie runbooků a modulů pro Azure Automation

Místo vytváření vlastních runbooků a modulů v Azure Automation máte přístup ke scénářům, které už microsoft a komunita vytvořily. Sady Runbook a [moduly](#modules-in-powershell-gallery) prostředí PowerShell můžete získat z Galerie prostředí PowerShell a [runbooků Pythonu](#use-python-runbooks) z Galerie Centra skriptů. Můžete také přispět do komunity sdílením [scénářů, které vyvíjíte](#add-a-powershell-runbook-to-the-gallery). 

## <a name="runbooks-in-powershell-gallery"></a>Runbooky v Galerii PowerShellu

[Galerie Prostředí PowerShell](https://www.powershellgallery.com/packages) poskytuje celou řadu runbooků od Microsoftu a komunity, které můžete importovat do Azure Automation. Pokud ho chcete použít, stáhněte si runbook z galerie nebo můžete runbooky importovat přímo z galerie nebo z účtu Automation na webu Azure Portal.

> [!NOTE]
> Grafické sady Runbook nejsou v Galerii prostředí PowerShell podporovány.

Importovat můžete jenom přímo z Galerie PowerShellu pomocí portálu Azure. Tuto funkci nelze provést pomocí prostředí PowerShell.

> [!NOTE]
> Měli byste ověřit obsah všech runbooků, které získáte z Galerie prostředí PowerShell a při instalaci a spuštění v produkčním prostředí buďte velmi opatrní.

## <a name="modules-in-powershell-gallery"></a>Moduly v Galerii Prostředí PowerShell

Moduly Prostředí PowerShell obsahují rutiny, které můžete použít ve svých sadách Runbook, a stávající moduly, které můžete nainstalovat v Azure Automation, jsou k dispozici v [Galerii prostředí PowerShell](https://www.powershellgallery.com). Tuto galerii můžete spustit z webu Azure Portal a nainstalovat je přímo do Azure Automation. Můžete si je také stáhnout a nainstalovat ručně.

## <a name="common-solutions-available-in-powershell-gallery"></a>Běžná řešení dostupná v Galerii PowerShellu

Níže uvedený seznam obsahuje několik sad Runbook, které poskytují řešení běžných scénářů. Úplný seznam runbooků vytvořených týmem Azure Automation najdete v [tématu Profil AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) - Importuje nejnovější verzi všech modulů v účtu automatizace z Galerie prostředí PowerShell.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) – konfiguruje diagnostiku Azure a analýzu protokolů tak, aby přijímali protokoly Azure Automation obsahující stav úloh y a toky úloh.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) – Zkopíruje vzdálený soubor z virtuálního počítače Windows Azure.
   * [Kopírovat položkuFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) – zkopíruje místní soubor do virtuálního počítače Azure.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Import runbooku PowerShellu z galerie runbooků pomocí portálu Azure

1. Na webu Azure Portal otevřete účet Automation.
2. V části **Automatizace procesů**vyberte **galerii Runbooks** .
3. Vyberte **zdroj: PowerShell Gallery**.
4. Vyhledejte požadovanou položku galerie a vyberte ji, chcete-li zobrazit její podrobnosti. Na levé straně můžete zadat další parametry vyhledávání pro vydavatele a typ.

   ![Galerie procházení](media/automation-runbook-gallery/browse-gallery.png)

5. Kliknutím na **Zobrazit zdrojový projekt** zobrazíte položku v Centru [skriptů TechNet](https://gallery.technet.microsoft.com/).
6. Chcete-li položku importovat, kliknutím na ni zobrazte její podrobnosti a klepněte na tlačítko **Importovat**.

   ![Tlačítko Importovat](media/automation-runbook-gallery/gallery-item-detail.png)

7. Volitelně změňte název sady Runbook a klepnutím na **tlačítko OK** runbook importujte.
8. Runbook se zobrazí na kartě **Runbook** pro účet Automation.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>Přidání runbooku Prostředí PowerShell do galerie

Společnost Microsoft doporučuje přidat runbooky do Galerie prostředí PowerShell, o kterých si myslíte, že by byly užitečné pro ostatní zákazníky. Galerie Prostředí PowerShell přijímá moduly PowerShellu a skripty PowerShellu. Runbook můžete přidat [tak, že ho nahrajete do Galerie prostředí PowerShell](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Import modulu z galerie modulů pomocí portálu Azure

1. Na webu Azure Portal otevřete účet Automation.
2. Včásti **Sdílené prostředky** vyberte **Moduly,** chcete-li otevřít seznam modulů.
3. V horní části stránky klikněte na **Procházet galerii.**

   ![Galerie modulů](media/automation-runbook-gallery/modules-blade.png)

4. Na stránce Galerie procházení můžete vyhledávat podle následujících polí:

   * Název modulu
   * Značky
   * Autor
   * Název prostředku rutiny/DSC

5. Vyhledejte modul, který vás zajímá, a vyberte ho, abyste zobrazili jeho podrobnosti.

   Při přechodu k podrobnostem o konkrétním modulu můžete zobrazit další informace. Tyto informace zahrnují odkaz zpět na Galerii prostředí PowerShell, všechny požadované závislosti a všechny rutiny nebo prostředky DSC, které modul obsahuje.

   ![Podrobnosti o modulu PowerShellu](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Pokud chcete modul nainstalovat přímo do Azure Automation, klikněte na **Importovat**.
7. V podokně Import uvidíte název modulu, který chcete importovat. Pokud jsou nainstalovány všechny závislosti, aktivuje se tlačítko **OK.** Pokud vám chybí závislosti, musíte tyto závislosti importovat, než budete moci importovat tento modul.
8. V podokně Import klepněte na **tlačítko OK,** chcete-li modul importovat. Zatímco Azure Automation importuje modul do vašeho účtu, extrahuje metadata o modulu a rutinách. Tato akce může trvat několik minut, protože každá aktivita musí být extrahovány.
9. Obdržíte počáteční oznámení, že modul je nasazen a další oznámení po jeho dokončení.
10. Po importu modulu můžete zobrazit dostupné aktivity. Prostředky modulu můžete použít v sadách Runbook a DSC prostředky.

> [!NOTE]
> Moduly, které podporují jenom jádro PowerShellu, nejsou v Azure Automation podporované a nenejde se importovat na webu Azure Portal ani nasadit přímo z Galerie Prostředí PowerShell.

## <a name="use-python-runbooks"></a>Použití runbooků Pythonu

Sady Runbook pythonu jsou k dispozici v [galerii Script Center](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Runbooky Pythonu můžete do galerie Centra skriptů přispívat kliknutím na **Nahrát příspěvek**. Pokud tak učiníte, ujistěte `Python` se, že značku přidáte při nahrávání příspěvku.

> [!NOTE]
> Chcete-li nahrát obsah do [Centra skriptů](https://gallery.technet.microsoft.com/scriptcenter), potřebujete minimálně 100 bodů.

## <a name="request-a-runbook-or-module"></a>Žádost o runbook nebo modul

Požadavky můžete odesílat [uživateli Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Pokud potřebujete pomoc s psaním runbooku nebo máte dotaz ohledně Prostředí PowerShell, pošlete dotaz na naše [fórum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Další kroky

* Pokud chcete začít s runbooky, [přečtěte si tématu Správa runbooku v Azure Automation](manage-runbooks.md).
* Informace o rozdílech mezi pracovním postupem PowerShellu a PowerShellu v sadách Runbook najdete v [tématu Learning PowerShell workflow](automation-powershell-workflow.md).
* Další informace o prostředí PowerShell, včetně jazykových odkazů a výukových modulů, najdete v dokumentu [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
