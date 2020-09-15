---
title: Použití Azure Automation runbooků a modulů v Galerie prostředí PowerShell
description: V tomto článku se dozvíte, jak používat Runbooky a moduly od Microsoftu a komunitě v Galerie prostředí PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: f90d88e49675681a7f73df838ca489a7193955b3
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90087856"
---
# <a name="use-runbooks-and-modules-in-powershell-gallery"></a>Použití runbooků a modulů v Galerii prostředí PowerShell

Místo vytváření vlastních runbooků a modulů v Azure Automation máte přístup ke scénářům, které už jsou sestavené Microsoftem a komunitou. Runbooky a [moduly](#modules-in-powershell-gallery) PowerShellu můžete z Galerie služby Script Center získat z Galerie prostředí PowerShell a [runbooků v Pythonu](#use-python-runbooks) . Můžete také přispět do komunity tím, že nasdílíte [scénáře, které vyvíjíte](#add-a-powershell-runbook-to-the-gallery). 

## <a name="runbooks-in-powershell-gallery"></a>Runbooky v Galerie prostředí PowerShell

[Galerie prostředí PowerShell](https://www.powershellgallery.com/packages) poskytuje řadu runbooků od Microsoftu a komunity, které můžete importovat do Azure Automation. Pokud ho chcete použít, Stáhněte si Runbook z Galerie nebo můžete přímo importovat Runbooky z Galerie nebo z účtu Automation v Azure Portal.

> [!NOTE]
> Grafické Runbooky nejsou v Galerie prostředí PowerShell podporovány.

Přímo z Galerie prostředí PowerShell můžete importovat jenom pomocí Azure Portal. Tuto funkci nelze provést pomocí prostředí PowerShell.

> [!NOTE]
> Měli byste ověřit obsah všech runbooků, které získáte z Galerie prostředí PowerShell, a při jejich instalaci a spuštění v produkčním prostředí Buďte velmi opatrní.

## <a name="modules-in-powershell-gallery"></a>Moduly v Galerie prostředí PowerShell

Moduly prostředí PowerShell obsahují rutiny, které lze použít v sadách Runbook, a existující moduly, které lze instalovat v nástroji Azure Automation jsou k dispozici v [Galerie prostředí PowerShell](https://www.powershellgallery.com). Tuto galerii můžete spustit z Azure Portal a nainstalovat z nich přímo do Azure Automation. Můžete je také stáhnout a nainstalovat ručně.

## <a name="common-scenarios-available-in-powershell-gallery"></a>Běžné scénáře dostupné v Galerie prostředí PowerShell

Níže uvedený seznam obsahuje několik sad Runbook, které podporují běžné scénáře. Úplný seznam runbooků vytvořených Azure Automation týmem naleznete v tématu [profil AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) -importuje nejnovější verzi všech modulů v účtu Automation z Galerie prostředí PowerShell.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) – konfiguruje Azure Diagnostics a Log Analytics, aby přijímaly protokoly Azure Automation, které obsahují stav úloh a proudy úloh.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) -kopíruje vzdálený soubor z virtuálního počítače Windows Azure.
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) – zkopíruje místní soubor na virtuální počítač Azure.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Import Runbooku PowerShellu z Galerie runbooků pomocí Azure Portal

1. Na webu Azure Portal otevřete účet Automation.
2. V části **Automatizace procesu**vyberte **Galerie runbooků** .
3. Vyberte **Zdroj: Galerie prostředí PowerShell**.
4. Vyhledejte položku galerie, kterou chcete, a vyberte ji, abyste zobrazili její podrobnosti. Vlevo můžete zadat další parametry vyhledávání pro vydavatele a typ.

   ![Galerie procházení](media/automation-runbook-gallery/browse-gallery.png)

5. Kliknutím na **Zobrazit zdrojový projekt** zobrazíte položku v [centru skriptů TechNet](https://gallery.technet.microsoft.com/).
6. Pokud chcete položku importovat, kliknutím na ni zobrazíte její podrobnosti a pak klikněte na **importovat**.

   ![Tlačítko Import](media/automation-runbook-gallery/gallery-item-detail.png)

7. Volitelně můžete změnit název Runbooku a pak kliknutím na **OK** sadu Runbook naimportovat.
8. Sada Runbook se zobrazí na kartě **Runbooky** pro účet Automation.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>Přidání Runbooku PowerShellu do galerie

Microsoft doporučuje přidat Runbooky do Galerie prostředí PowerShell, které by se vám mohly hodit pro ostatní zákazníky. Galerie prostředí PowerShell přijímá moduly prostředí PowerShell a skripty prostředí PowerShell. Runbook můžete přidat tak, že [ho nahrajete do galerie prostředí PowerShell](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Import modulu z Galerie modulů pomocí Azure Portal

1. Na webu Azure Portal otevřete účet Automation.
2. V části **sdílené prostředky** vyberte **moduly** a otevřete tak seznam modulů.
3. V horní části stránky klikněte na **Procházet galerii** .

   ![Galerie modulů](media/automation-runbook-gallery/modules-blade.png)

4. Na stránce Procházet galerii můžete vyhledávat podle následujících polí:

   * Název modulu
   * Značky
   * Autor
   * Název prostředku rutiny/DSC

5. Vyhledejte modul, který vás zajímá, a vyberte ho, aby se zobrazily jeho podrobnosti.

   Když přejdete k určitému modulu, můžete zobrazit další informace. Tyto informace zahrnují odkaz zpátky na Galerie prostředí PowerShell, všechny požadované závislosti a všechny rutiny nebo prostředky DSC, které modul obsahuje.

   ![Podrobnosti o modulu PowerShellu](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Pokud chcete modul nainstalovat přímo do Azure Automation, klikněte na **importovat**.
7. V podokně import můžete zobrazit název modulu, který se má importovat. Pokud jsou všechny závislosti nainstalovány, je aktivováno tlačítko **OK** . Pokud postrádáte závislosti, musíte tyto závislosti před importem tohoto modulu naimportovat.
8. V podokně Import klikněte na tlačítko **OK** . tím modul naimportujete. Když Azure Automation importuje modul do svého účtu, extrahuje metadata týkající se modulu a rutin. Tato akce může trvat několik minut, protože je potřeba extrahovat každou aktivitu.
9. Obdržíte počáteční oznámení o tom, že se modul nasazuje, a další oznámení, když se dokončí.
10. Po importu modulu můžete zobrazit dostupné aktivity. Prostředky modulů můžete použít ve svých sadách Runbook a prostředcích DSC.

> [!NOTE]
> Moduly, které podporují pouze PowerShell Core, nejsou v Azure Automation podporovány a nelze je importovat v Azure Portal nebo nasazeny přímo z Galerie prostředí PowerShell.

## <a name="use-python-runbooks"></a>Použití runbooků v Pythonu

Runbooky Python jsou k dispozici v [galerii centra skriptů](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Kliknutím na **Odeslat příspěvek**můžete přispívat sady Runbook Runbooky do galerie Center skriptů. Když to uděláte, nezapomeňte při `Python` nahrávání vašeho příspěvku přidat značku.

> [!NOTE]
> Pokud chcete nahrát obsah do [centra skriptů](https://gallery.technet.microsoft.com/scriptcenter), potřebujete minimálně 100 bodů.

## <a name="request-a-runbook-or-module"></a>Vyžádat Runbook nebo modul

Můžete odesílat požadavky na [uživatelský hlas](https://feedback.azure.com/forums/246290-azure-automation/).  Pokud potřebujete nápovědu k zápisu Runbooku nebo máte dotaz týkající se PowerShellu, pošlete dotaz na [stránku s dotazem na Microsoft Q&](/answers/topics/azure-automation.html).

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak začít s PowerShellovým runbookm, najdete v tématu [kurz: vytvoření Runbooku PowerShellu](learn/automation-tutorial-runbook-textual-powershell.md).
* Informace o práci se sadami Runbook najdete [v tématu Správa runbooků v Azure Automation](manage-runbooks.md).
* Podrobnosti o PowerShellu najdete v tématu [dokumentace k PowerShellu](/powershell/scripting/overview).
* * Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation).
