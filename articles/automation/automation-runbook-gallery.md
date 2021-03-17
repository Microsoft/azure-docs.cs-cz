---
title: Použití Azure Automation runbooků a modulů v Galerie prostředí PowerShell
description: V tomto článku se dozvíte, jak používat Runbooky a moduly od Microsoftu a komunitě v Galerie prostředí PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: c38a6236fe3ad9164d11d94e5563a7dddf5b4b32
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452777"
---
# <a name="use-runbooks-and-modules-in-powershell-gallery"></a>Použití runbooků a modulů v Galerii prostředí PowerShell

Místo vytváření vlastních runbooků a modulů v Azure Automation máte přístup ke scénářům, které už jsou sestavené Microsoftem a komunitou. Runbooky a [moduly](#modules-in-powershell-gallery) PowerShellu můžete získat z Galerie prostředí PowerShell a [runbooků v Pythonu](#use-python-runbooks) z organizace pro Azure Automation GitHub. Můžete také přispět do komunity tím, že nasdílíte [scénáře, které vyvíjíte](#add-a-powershell-runbook-to-the-gallery).

> [!NOTE]
> Centrum skriptů TechNet vyřazování z provozu. Všechny Runbooky z centra skriptů v galerii runbooků se přesunuly do naší [organizace služby Automation](https://github.com/azureautomation) na webu GitHub, kde najdete další informace [.](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)

## <a name="runbooks-in-powershell-gallery"></a>Runbooky v Galerie prostředí PowerShell

[Galerie prostředí PowerShell](https://www.powershellgallery.com/packages) poskytuje řadu runbooků od Microsoftu a komunity, které můžete importovat do Azure Automation. Pokud ho chcete použít, Stáhněte si Runbook z Galerie nebo můžete přímo importovat Runbooky z Galerie nebo z účtu Automation v Azure Portal.

> [!NOTE]
> Grafické Runbooky nejsou v Galerie prostředí PowerShell podporovány.

Přímo z Galerie prostředí PowerShell můžete importovat jenom pomocí Azure Portal. Tuto funkci nelze provést pomocí prostředí PowerShell.

> [!NOTE]
> Měli byste ověřit obsah všech runbooků, které získáte z Galerie prostředí PowerShell, a při jejich instalaci a spuštění v produkčním prostředí Buďte velmi opatrní.

## <a name="modules-in-powershell-gallery"></a>Moduly v Galerie prostředí PowerShell

Moduly PowerShellu obsahují rutiny, které můžete použít ve svých sadách Runbook. Existující moduly, které můžete nainstalovat v Azure Automation, jsou k dispozici v [Galerie prostředí PowerShell](https://www.powershellgallery.com). Tuto galerii můžete spustit z Azure Portal a nainstalovat moduly přímo do Azure Automation, nebo je můžete stáhnout a nainstalovat ručně.

## <a name="common-scenarios-available-in-powershell-gallery"></a>Běžné scénáře dostupné v Galerie prostředí PowerShell

Níže uvedený seznam obsahuje několik sad Runbook, které podporují běžné scénáře. Úplný seznam runbooků vytvořených Azure Automation týmem naleznete v tématu [profil AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) -importuje nejnovější verzi všech modulů v účtu Automation z Galerie prostředí PowerShell.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) – konfiguruje Azure Diagnostics a Log Analytics, aby přijímaly protokoly Azure Automation, které obsahují stav úloh a proudy úloh.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) -kopíruje vzdálený soubor z virtuálního počítače Windows Azure.
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) – zkopíruje místní soubor na virtuální počítač Azure.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Import Runbooku PowerShellu z Galerie runbooků pomocí Azure Portal

1. Na webu Azure Portal otevřete účet Automation.
1. V části **Automatizace procesu** vyberte **Galerie runbooků** .
1. Vyberte **Zdroj: Galerie prostředí PowerShell**. Tím se zobrazí seznam dostupných runbooků, které můžete procházet.
1. K zúžení seznamu můžete použít vyhledávací pole nad seznamem, nebo můžete použít filtry k zúžení zobrazení podle vydavatele, typu a řazení. Vyhledejte položku galerie, kterou chcete, a vyberte ji, abyste zobrazili její podrobnosti.

   :::image type="content" source="media/automation-runbook-gallery/browse-gallery-sm.png" alt-text="Procházení Galerie runbooků" lightbox="media/automation-runbook-gallery/browse-gallery-lg.png":::

1. Pokud chcete položku importovat, klikněte v okně podrobností na **importovat** .

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-detail-sm.png" alt-text="Zobrazí podrobnosti položky Galerie runbooků." lightbox="media/automation-runbook-gallery/gallery-item-detail-lg.png":::

1. Volitelně můžete změnit název Runbooku a pak kliknutím na **OK** sadu Runbook naimportovat.
1. Sada Runbook se zobrazí na kartě **Runbooky** pro účet Automation.

## <a name="import-a--powershell-runbook-from-github-with-the-azure-portal"></a>Import Runbooku PowerShellu z GitHubu pomocí Azure Portal

1. Na webu Azure Portal otevřete účet Automation.
1. V části **Automatizace procesu** vyberte **Galerie runbooků** .
1. Vyberte **Zdroj: GitHub**.
1. Filtry nad seznamem můžete použít k zúžení zobrazení podle vydavatele, typu a řazení. Vyhledejte položku galerie, kterou chcete, a vyberte ji, abyste zobrazili její podrobnosti.

   :::image type="content" source="media/automation-runbook-gallery/browse-gallery-github-sm.png" alt-text="Procházení Galerie GitHub." lightbox="media/automation-runbook-gallery/browse-gallery-github-lg.png":::

1. Pokud chcete položku importovat, klikněte v okně podrobností na **importovat** .

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-github-sm.png" alt-text="Podrobné zobrazení Runbooku z Galerie GitHubu." lightbox="media/automation-runbook-gallery/gallery-item-details-blade-github-lg.png":::

1. Volitelně můžete změnit název Runbooku a pak kliknutím na **OK** sadu Runbook naimportovat.
1. Sada Runbook se zobrazí na kartě **Runbooky** pro účet Automation.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>Přidání Runbooku PowerShellu do galerie

Microsoft doporučuje přidat Runbooky do Galerie prostředí PowerShell, které by se vám mohly hodit pro ostatní zákazníky. Galerie prostředí PowerShell přijímá moduly prostředí PowerShell a skripty prostředí PowerShell. Runbook můžete přidat tak, že [ho nahrajete do galerie prostředí PowerShell](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Import modulu z Galerie modulů pomocí Azure Portal

1. Na webu Azure Portal otevřete účet Automation.
1. V části **sdílené prostředky** vyberte **moduly** a otevřete tak seznam modulů.
1. V horní části stránky klikněte na **Procházet galerii** .

      :::image type="content" source="media/automation-runbook-gallery/modules-blade-sm.png" alt-text="Zobrazení galerie modulů" lightbox="media/automation-runbook-gallery/modules-blade-lg.png":::

1. Na stránce Procházet galerii můžete pomocí vyhledávacího pole Vyhledat shody v libovolném z následujících polí:

   * Název modulu
   * Značky
   * Autor
   * Název prostředku rutiny/DSC

1. Vyhledejte modul, který vás zajímá, a vyberte ho, aby se zobrazily jeho podrobnosti.

   Když přejdete k určitému modulu, můžete zobrazit další informace. Tyto informace zahrnují odkaz zpátky na Galerie prostředí PowerShell, všechny požadované závislosti a všechny rutiny nebo prostředky DSC, které modul obsahuje.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-sm.png" alt-text="Podrobné zobrazení modulu z galerie." lightbox="media/automation-runbook-gallery/gallery-item-details-blade-lg.png":::

1. Pokud chcete modul nainstalovat přímo do Azure Automation, klikněte na **importovat**.
1. V podokně import můžete zobrazit název modulu, který se má importovat. Pokud jsou všechny závislosti nainstalovány, je aktivováno tlačítko **OK** . Pokud postrádáte závislosti, musíte tyto závislosti před importem tohoto modulu naimportovat.
1. V podokně Import klikněte na tlačítko **OK** . tím modul naimportujete. Když Azure Automation importuje modul do svého účtu, extrahuje metadata týkající se modulu a rutin. Tato akce může trvat několik minut, protože je potřeba extrahovat každou aktivitu.
1. Obdržíte počáteční oznámení o tom, že se modul nasazuje, a další oznámení, když se dokončí.
1. Po importu modulu můžete zobrazit dostupné aktivity. Prostředky modulů můžete použít ve svých sadách Runbook a prostředcích DSC.

> [!NOTE]
> Moduly, které podporují pouze PowerShell Core, nejsou v Azure Automation podporovány a nelze je importovat v Azure Portal nebo nasazeny přímo z Galerie prostředí PowerShell.

## <a name="use-python-runbooks"></a>Použití runbooků v Pythonu

Runbooky Python jsou k dispozici v [Azure Automation organizaci GitHubu](https://github.com/azureautomation). Když přispějete k našemu úložišti GitHub, přidejte značku **(téma GitHubu): python3** , když nahrajete příspěvek.

## <a name="request-a-runbook-or-module"></a>Vyžádat Runbook nebo modul

Můžete odesílat požadavky na [uživatelský hlas](https://feedback.azure.com/forums/246290-azure-automation/).  Pokud potřebujete nápovědu k zápisu Runbooku nebo máte dotaz týkající se PowerShellu, pošlete dotaz na [stránku s dotazem na Microsoft Q&](/answers/topics/azure-automation.html).

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak začít s PowerShellovým runbookm, najdete v tématu [kurz: vytvoření Runbooku PowerShellu](learn/automation-tutorial-runbook-textual-powershell.md).
* Informace o práci se sadami Runbook najdete [v tématu Správa runbooků v Azure Automation](manage-runbooks.md).
* Podrobnosti o PowerShellu najdete v tématu [dokumentace k PowerShellu](/powershell/scripting/overview).
* Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](/powershell/module/az.automation).
