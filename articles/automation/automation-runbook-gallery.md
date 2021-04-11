---
title: Použití Azure Automation runbooků a modulů v Galerie prostředí PowerShell
description: V tomto článku se dozvíte, jak používat Runbooky a moduly z úložišť Microsoft GitHub a Galerie prostředí PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 04/07/2021
ms.topic: conceptual
ms.openlocfilehash: 2df019888d293cd8a25a34e6f0f4e7dd215c6a41
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030624"
---
# <a name="use-existing-runbooks-and-modules"></a>Použít existující Runbooky a moduly

Místo vytváření vlastních runbooků a modulů v Azure Automation máte přístup ke scénářům, které už jsou sestavené Microsoftem a komunitou. Runbooky PowerShellu a Pythonu pro Azure můžete získat z Galerie runbooků v Azure Portal, [moduly](#modules-in-the-powershell-gallery) a [Runbooky](#runbooks-in-the-powershell-gallery) (které můžou nebo nemusí být specifické pro Azure) z Galerie prostředí PowerShell. Můžete také přispět do komunity tím, že nasdílíte [scénáře, které vyvíjíte](#contribute-to-the-community).

> [!NOTE]
> Centrum skriptů TechNet vyřazování z provozu. Všechny Runbooky z centra skriptů v galerii runbooků se přesunuly do naší [organizace služby Automation](https://github.com/azureautomation) na webu GitHub. Další informace najdete v tématu [Azure Automation Runbooky se přesunou do GitHubu](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337).

## <a name="import-runbooks-from-github-with-the-azure-portal"></a>Import runbooků z GitHubu pomocí Azure Portal

1. Na webu Azure Portal otevřete účet Automation.
2. V části **Automatizace procesu** vyberte **Galerie runbooků** .
3. Vyberte **Zdroj: GitHub**.
4. Filtry nad seznamem můžete použít k zúžení zobrazení podle vydavatele, typu a řazení. Vyhledejte položku galerie, kterou chcete, a vyberte ji, abyste zobrazili její podrobnosti.

   :::image type="content" source="./media/automation-runbook-gallery/browse-gallery-github.png" alt-text="Procházení Galerie runbooků" lightbox="./media/automation-runbook-gallery/browse-gallery-github-expanded.png":::

5. Pokud chcete položku importovat, klikněte na stránce Podrobnosti na **importovat** .

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import.png" alt-text="Import položky Galerie":::

6. Volitelně můžete změnit název Runbooku v okně Import a potom kliknutím na **OK** sadu Runbook naimportovat.

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import-blade.png" alt-text="Okno pro import položky Galerie":::

7. Sada Runbook se zobrazí na kartě **Runbooky** pro účet Automation.
 
## <a name="runbooks-in-the-powershell-gallery"></a>Runbooky v Galerie prostředí PowerShell

> [!IMPORTANT]
> Měli byste ověřit obsah všech runbooků, které získáte z Galerie prostředí PowerShell. Při instalaci a spouštění v produkčním prostředí používejte mimořádně opatrní.

[Galerie prostředí PowerShell](https://www.powershellgallery.com/packages) poskytuje různé Runbooky od Microsoftu a komunitu, které můžete importovat do Azure Automation. Pokud ho chcete použít, Stáhněte si Runbook z Galerie nebo můžete přímo importovat Runbooky z Galerie nebo z účtu Automation v Azure Portal.

> [!NOTE]
> Grafické Runbooky nejsou v Galerie prostředí PowerShell podporovány.

Přímo z Galerie prostředí PowerShell můžete importovat jenom pomocí Azure Portal. Tuto funkci nelze provést pomocí prostředí PowerShell. Postup je stejný, jak je uvedeno v [části Import runbooků z GitHubu s Azure Portal](#import-runbooks-from-github-with-the-azure-portal), s tím rozdílem, že **zdroj** bude **Galerie prostředí PowerShell**.

:::image type="content" source="./media/automation-runbook-gallery/source-runbook-gallery-small.png" alt-text="Zobrazuje se výběr zdroje v galerii runbooků." lightbox="./media/automation-runbook-gallery/source-runbook-gallery-large.png":::

## <a name="modules-in-the-powershell-gallery"></a>Moduly v Galerie prostředí PowerShell

Moduly PowerShellu obsahují rutiny, které můžete použít ve svých sadách Runbook. Existující moduly, které můžete nainstalovat v Azure Automation, jsou k dispozici v [Galerie prostředí PowerShell](https://www.powershellgallery.com). Tuto galerii můžete spustit z Azure Portal a nainstalovat moduly přímo do Azure Automation, nebo je můžete stáhnout a nainstalovat ručně.

Můžete také najít moduly pro import do Azure Portal. Jsou uvedené pro váš účet Automation v **galerii modulů** v části **sdílené prostředky**.

## <a name="common-scenarios-available-in-the-powershell-gallery"></a>Běžné scénáře dostupné v Galerie prostředí PowerShell

Níže uvedený seznam obsahuje několik sad Runbook, které podporují běžné scénáře. Úplný seznam runbooků vytvořených Azure Automation týmem naleznete v tématu [profil AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) -importuje nejnovější verzi všech modulů v účtu Automation z Galerie prostředí PowerShell.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) – konfiguruje Azure Diagnostics a Log Analytics, aby přijímaly protokoly Azure Automation, které obsahují stav úloh a proudy úloh.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) -kopíruje vzdálený soubor z virtuálního počítače Windows Azure.
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) – zkopíruje místní soubor na virtuální počítač Azure.

## <a name="contribute-to-the-community"></a>Přispívání do komunity

Důrazně doporučujeme, abyste přispívat a mohli lépe rozšířit Azure Automation komunitě. Sdílejte Skvělé sady Runbook, které jste sestavili ve komunitě. Budou vám zacházet vaše příspěvky.

### <a name="add-a-runbook-to-the-github-runbook-gallery"></a>Přidání Runbooku do galerie Runbook GitHubu

Pomocí tohoto pracovního postupu GitHubu můžete do galerie runbooků přidat nové Runbooky PowerShellu nebo Python.

1. Vytvořte si veřejné úložiště na GitHubu a přidejte sadu Runbook a všechny další potřebné soubory (například readme.md, Description atd.).
1. Přidejte téma `azureautomationrunbookgallery` , abyste měli jistotu, že bude úložiště zjistit naše služba, takže se dá zobrazit v galerii runbooků Automation.
1. Pokud Runbook, který jste vytvořili, je pracovní postup PowerShellu, přidejte téma `PowerShellWorkflow` . Pokud se jedná o Runbook sady Python 3, přidejte `Python3` . Pro Runbooky Azure nejsou potřeba žádná další konkrétní témata, ale doporučujeme přidat další témata, která se dají použít ke kategorizaci a hledání v galerii runbooků.

   >[!NOTE]
   >Podívejte se na existující Runbooky v galerii, kde najdete formátování, záhlaví a existující značky, které byste mohli použít (například `Azure Automation` nebo `Linux Azure Virtual Machines` ).

Chcete-li navrhnout změny existující sady Runbook, zasouboru do ní žádost o získání dat. 

Pokud se rozhodnete klonovat a upravit existující Runbook, osvědčeným postupem je zadat jiný název. Pokud starý název znovu použijete, zobrazí se dvakrát v seznamu v galerii runbooků.

>[!NOTE]
>Pro aktualizované i nové Runbooky prosím povolte aspoň 12 hodin pro synchronizaci mezi GitHubem a galerií runbooků Automation.

### <a name="add-a-powershell-runbook-to-the-powershell-gallery"></a>Přidání Runbooku PowerShellu do galerie prostředí PowerShell

Microsoft doporučuje přidat Runbooky do Galerie prostředí PowerShell, které by se vám mohly hodit pro ostatní zákazníky. Galerie prostředí PowerShell přijímá moduly prostředí PowerShell a skripty prostředí PowerShell. Runbook můžete přidat tak, že [ho nahrajete do galerie prostředí PowerShell](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-modules-gallery-in-the-azure-portal"></a>Import modulu z Galerie modulů v Azure Portal

1. Na webu Azure Portal otevřete účet Automation.
1. V části **sdílené prostředky** vyberte **Galerie moduly** a otevřete seznam modulů.

      :::image type="content" source="media/automation-runbook-gallery/modules-blade-sm.png" alt-text="Zobrazení galerie modulů" lightbox="media/automation-runbook-gallery/modules-blade-lg.png":::

1. Na stránce Procházet galerii můžete vyhledávat podle následujících polí:

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

## <a name="request-a-runbook-or-module"></a>Vyžádat Runbook nebo modul

Můžete odesílat požadavky na [uživatelský hlas](https://feedback.azure.com/forums/246290-azure-automation/).  Pokud potřebujete nápovědu k zápisu Runbooku nebo máte dotaz týkající se PowerShellu, pošlete dotaz na [stránku s dotazem na Microsoft Q&](/answers/topics/azure-automation.html).

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak začít se sadami Runbook PowerShellu, najdete v tématu [kurz: vytvoření Runbooku PowerShellu](learn/automation-tutorial-runbook-textual-powershell.md).
* Informace o práci se sadami Runbook najdete [v tématu Správa runbooků v Azure Automation](manage-runbooks.md).
* Další informace o skriptování PowerShellu najdete v tématu [dokumentace k PowerShellu](/powershell/scripting/overview).
* Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](/powershell/module/az.automation).
