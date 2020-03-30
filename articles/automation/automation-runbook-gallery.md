---
title: Galerie runbooků a modulů pro Azure Automation
description: Sady Runbook a moduly od Microsoftu a komunity jsou k dispozici pro instalaci a použití v prostředí Azure Automation.  Tento článek popisuje, jak můžete získat přístup k těmto prostředkům a přispět sady Runbook do galerie.
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: 52a0ab0a31600c1548283c7d899b17e497811b5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75421489"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Galerie runbooků a modulů pro Azure Automation

Místo vytváření vlastních runbooků a modulů v Azure Automation máte přístup ke scénářům, které už microsoft a komunita vytvořily.

Sady Runbook a [moduly](#modules-in-powershell-gallery) prostředí PowerShell můžete získat z Galerie prostředí PowerShell a [runbooků Pythonu](#python-runbooks) z Galerie Centra skriptů. Můžete také přispět do komunity sdílením scénářů, které vyvíjíte, najdete v tématu Přidání runbooku do galerie

## <a name="runbooks-in-powershell-gallery"></a>Runbooky v Galerii PowerShellu

[Galerie Prostředí PowerShell](https://www.powershellgallery.com/packages) poskytuje celou řadu runbooků od Microsoftu a komunity, které můžete importovat do Azure Automation. Pokud ho chcete použít, stáhněte si runbook z galerie nebo můžete runbooky importovat přímo z galerie nebo z účtu Automation na webu Azure Portal.

Importovat můžete jenom přímo z Galerie PowerShellu pomocí portálu Azure. Tuto funkci nelze provést pomocí prostředí PowerShell.

> [!NOTE]
> Měli byste ověřit obsah všech runbooků, které získáte z Galerie prostředí PowerShell a při instalaci a spuštění v produkčním prostředí buďte velmi opatrní.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Import runbooku Prostředí PowerShell z Galerie Runbooku pomocí portálu Azure

1. Na webu Azure Portal otevřete účet Automation.
2. V části **Automatizace procesů**klikněte na **Galerii runbooků.**
3. Vyberte **zdroj: PowerShell Gallery**.
4. Vyhledejte požadovanou položku galerie a vyberte ji, chcete-li zobrazit její podrobnosti. Na levé straně můžete zadat další parametry vyhledávání pro vydavatele a typ.

   ![Galerie procházení](media/automation-runbook-gallery/browse-gallery.png)

5. Kliknutím na **Zobrazit zdrojový projekt** zobrazíte položku v Centru [skriptů TechNet](https://gallery.technet.microsoft.com/).
6. Chcete-li položku importovat, kliknutím na ni zobrazte její podrobnosti a potom klepněte na tlačítko **Importovat.**

   ![Tlačítko Importovat](media/automation-runbook-gallery/gallery-item-detail.png)

7. Volitelně změňte název sady Runbook a klepnutím na **tlačítko OK** runbook importujte.
8. Runbook se zobrazí na kartě **Runbook** pro účet Automation.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>Přidání runbooku prostředí PowerShell do galerie

Společnost Microsoft doporučuje přidat runbooky do Galerie prostředí PowerShell, o kterých si myslíte, že by byly užitečné pro ostatní zákazníky. Galerie Prostředí PowerShell přijímá moduly PowerShellu a skripty PowerShellu. Runbook můžete přidat [tak, že ho nahrajete do Galerie prostředí PowerShell](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

> [!NOTE]
> Grafické sady Runbook nejsou v Galerii prostředí PowerShell podporovány.

## <a name="modules-in-powershell-gallery"></a>Moduly v Galerii Prostředí PowerShell

Moduly Prostředí PowerShell obsahují rutiny, které můžete použít ve svých sadách Runbook, a stávající moduly, které můžete nainstalovat v Azure Automation, jsou k dispozici v [Galerii prostředí PowerShell](https://www.powershellgallery.com). Tuto galerii můžete spustit z webu Azure Portal a nainstalovat je přímo do Azure Automation. Můžete si je také stáhnout a nainstalovat ručně.

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Import modulu z Galerie modulů automatizace pomocí portálu Azure

1. Na webu Azure Portal otevřete účet Automation.
2. Včásti **Sdílené prostředky** vyberte **Moduly,** chcete-li otevřít seznam modulů.
3. V horní části stránky klikněte na **Procházet galerii.**

   ![Galerie modulů](media/automation-runbook-gallery/modules-blade.png)

4. Na stránce **Galerie procházení** můžete vyhledávat podle následujících polí:

   * Název modulu
   * Značky
   * Autor
   * Název prostředku rutiny/DSC

5. Vyhledejte modul, který vás zajímá, a vyberte ho, abyste zobrazili jeho podrobnosti.

   Při přechodu k podrobnostem o konkrétním modulu můžete zobrazit další informace. Tyto informace zahrnují odkaz zpět na Galerii prostředí PowerShell, všechny požadované závislosti a všechny rutiny nebo prostředky DSC, které modul obsahuje.

   ![Podrobnosti o modulu PowerShellu](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Chcete-li modul nainstalovat přímo do Azure Automation, klikněte na tlačítko **Importovat.**
7. Když kliknete na tlačítko Importovat, v podokně **Import** se zobrazí název modulu, který se chystáte importovat. Pokud jsou nainstalovány všechny závislosti, aktivuje se tlačítko **OK.** Pokud vám chybí závislosti, musíte tyto závislosti importovat, než budete moci importovat tento modul.
8. Na stránce **Import** klikněte na **OK,** abyste importují modul. Zatímco Azure Automation importuje modul do vašeho účtu, extrahuje metadata o modulu a rutinách. Tato akce může trvat několik minut, protože každá aktivita musí být extrahován.
9. Obdržíte počáteční oznámení, že modul je nasazen a další oznámení po jeho dokončení.
10. Po importu modulu můžete zobrazit dostupné aktivity. Jeho prostředky můžete použít v sadách Runbook a konfigurace požadovaného stavu.

> [!NOTE]
> Moduly, které podporují jenom jádro PowerShellu, nejsou v Azure Automation podporované a nenejde se importovat na webu Azure Portal ani nasadit přímo z Galerie Prostředí PowerShell.

## <a name="python-runbooks"></a>Runbooky pythonu

Sady Runbook pythonu jsou k dispozici v [galerii Script Center](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Runbooky Pythonu můžete do galerie Centra skriptů přispívat kliknutím na **Nahrát příspěvek**. Když to uděláte, ujistěte se, že při nahrávání příspěvku přidáte značku **Python.**

> [!NOTE]
> Pro nahrání obsahu do [Centra skriptů](https://gallery.technet.microsoft.com/scriptcenter) je vyžadováno minimálně 100 bodů.

## <a name="requesting-a-runbook-or-module"></a>Vyžádání runbooku nebo modulu

Požadavky můžete odesílat [uživateli Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Pokud potřebujete pomoc s psaním runbooku nebo máte dotaz ohledně Prostředí PowerShell, pošlete dotaz na naše [fórum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="common-solutions-available-in-the-runbook-gallery"></a>Běžná řešení dostupná v galerii runbooků

Níže uvedený seznam obsahuje několik sad Runbook, které poskytují řešení běžných scénářů. Úplný seznam runbooků vytvořených týmem Azure Automation najdete v [tématu Profil AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

* [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) - Importuje nejnovější verzi v Galerii prostředí PowerShell všech modulů v účtu automatizace.
* [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) – tento skript konfiguruje Diagnostika Azure a Log Analytics pro příjem protokolů Azure Automation obsahující stav úlohy a toky úloh.
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) – Tato runbook zkopíruje vzdálený soubor z virtuálního počítače Windows Azure.
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) – Tato runbook zkopíruje místní soubor do virtuálního počítače Azure.

## <a name="next-steps"></a>Další kroky

* Pokud chcete začít s runbooky, přečtěte si témat [správa runbooku v Azure Automation.](manage-runbooks.md)
* Informace o rozdílech mezi pracovním postupem PowerShellu a PowerShellu v runbookech najdete v [tématu Learning PowerShell workflow](automation-powershell-workflow.md)
* Další informace o prostředí PowerShell, včetně jazykových odkazů a výukových modulů, najdete v dokumentu [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
