---
title: Galerie runbooků a modulů pro Azure Automation
description: Runbooky a moduly od Microsoftu a komunity jsou k dispozici pro instalaci a použití v prostředí Azure Automation.  Tento článek popisuje, jak můžete získat přístup k těmto prostředkům a přispívat do galerie.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5ef739a7053fffdd45c6d5e144ae736429597a4f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68850501"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Galerie runbooků a modulů pro Azure Automation

Místo vytváření vlastních runbooků a modulů v Azure Automation máte přístup ke scénářům, které už jsou sestavené Microsoftem a komunitou.

Runbooky a [moduly](#modules-in-powershell-gallery) PowerShellu můžete z Galerie služby Script Center získat z Galerie prostředí PowerShell a [runbooků](#python-runbooks) v Pythonu. Můžete také přispět ke komunitě pomocí scénářů, které vyvíjíte, v tématu Přidání Runbooku do galerie.

## <a name="runbooks-in-powershell-gallery"></a>Runbooky v Galerie prostředí PowerShell

[Galerie prostředí PowerShell](https://www.powershellgallery.com/packages) poskytuje řadu runbooků od Microsoftu a komunity, které můžete importovat do Azure Automation. Pokud ho chcete použít, Stáhněte si Runbook z Galerie nebo můžete přímo importovat Runbooky z Galerie nebo z účtu Automation v Azure Portal.

Přímo z Galerie prostředí PowerShell můžete importovat jenom pomocí Azure Portal. Tuto funkci nelze provést pomocí prostředí PowerShell.

> [!NOTE]
> Měli byste ověřit obsah všech runbooků, které získáte z Galerie prostředí PowerShell, a při jejich instalaci a spuštění v produkčním prostředí Buďte velmi opatrní.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Import Runbooku PowerShellu z Galerie runbooků pomocí Azure Portal

1. Na webu Azure Portal otevřete účet Automation.
2. V části **Automatizace procesu**klikněte na **Galerie Runbooky** .
3. Vybrat **zdroj: Galerie prostředí PowerShell**.
4. Vyhledejte položku galerie, kterou chcete, a vyberte ji, abyste zobrazili její podrobnosti. Vlevo můžete zadat další parametry vyhledávání pro vydavatele a typ.

   ![Procházet galerii](media/automation-runbook-gallery/browse-gallery.png)

5. Kliknutím na **Zobrazit zdrojový projekt** zobrazíte položku v [centru skriptů TechNet](https://gallery.technet.microsoft.com/).
6. Pokud chcete položku importovat, kliknutím na ni zobrazíte její podrobnosti a pak klikněte na tlačítko **Import** .

   ![Tlačítko Import](media/automation-runbook-gallery/gallery-item-detail.png)

7. Volitelně můžete změnit název Runbooku a pak kliknutím na **OK** sadu Runbook naimportovat.
8. Sada Runbook se zobrazí na kartě **Runbooky** pro účet Automation.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>Přidání Runbooku PowerShellu do galerie

Microsoft doporučuje přidat Runbooky do Galerie prostředí PowerShell, které by se vám mohly hodit pro ostatní zákazníky. Galerie prostředí PowerShell přijímá moduly prostředí PowerShell a skripty prostředí PowerShell. Runbook můžete přidat tak, že [ho nahrajete do galerie prostředí PowerShell](/powershell/gallery/how-to/publishing-packages/publishing-a-package).

> [!NOTE]
> Grafické Runbooky nejsou v Galerie prostředí PowerShell podporovány.

## <a name="modules-in-powershell-gallery"></a>Moduly v Galerie prostředí PowerShell

Moduly prostředí PowerShell obsahují rutiny, které lze použít v sadách Runbook, a existující moduly, které lze instalovat v nástroji Azure Automation jsou k dispozici v [Galerie prostředí PowerShell](https://www.powershellgallery.com). Tuto galerii můžete spustit z Azure Portal a nainstalovat z nich přímo do Azure Automation. Můžete je také stáhnout a nainstalovat ručně.

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Import modulu z Galerie modulů automatizace pomocí Azure Portal

1. Na webu Azure Portal otevřete účet Automation.
2. V části **sdílené prostředky** vyberte **moduly** a otevřete tak seznam modulů.
3. V horní části stránky klikněte na **Procházet galerii** .

   ![Galerie modulů](media/automation-runbook-gallery/modules-blade.png)

4. Na stránce **Procházet galerii** můžete vyhledávat podle následujících polí:

   * Název modulu
   * Tags
   * Autor
   * Název prostředku rutiny/DSC

5. Vyhledejte modul, který vás zajímá, a vyberte ho, aby se zobrazily jeho podrobnosti.

   Když přejdete k určitému modulu, můžete zobrazit další informace. Tyto informace zahrnují odkaz zpátky na Galerie prostředí PowerShell, všechny požadované závislosti a všechny rutiny nebo prostředky DSC, které modul obsahuje.

   ![Podrobnosti o modulu PowerShellu](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Pokud chcete modul nainstalovat přímo do Azure Automation, klikněte na tlačítko **Import** .
7. Po kliknutí na tlačítko importovat se v podokně **Import** zobrazí název modulu, který se chystáte importovat. Pokud jsou všechny závislosti nainstalovány, je aktivováno tlačítko **OK** . Pokud postrádáte závislosti, musíte tyto závislosti před importem tohoto modulu naimportovat.
8. Na stránce **Import** klikněte na tlačítko **OK** a importujte modul. Když Azure Automation importuje modul do svého účtu, extrahuje metadata týkající se modulu a rutin. Tato akce může trvat několik minut, protože je potřeba extrahovat každou aktivitu.
9. Obdržíte počáteční oznámení o tom, že se modul nasazuje, a další oznámení, když se dokončí.
10. Po importu modulu můžete zobrazit dostupné aktivity. Můžete použít jeho prostředky ve svých sadách Runbook a konfiguraci požadovaného stavu.

> [!NOTE]
> Moduly, které podporují pouze PowerShell Core, nejsou v Azure Automation podporovány a nelze je importovat v Azure Portal nebo nasazeny přímo z Galerie prostředí PowerShell.

## <a name="python-runbooks"></a>Runbooky Python

Runbooky Python jsou k dispozici v [galerii centra skriptů](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Kliknutím na **Odeslat příspěvek**můžete přispívat sady Runbook Runbooky do galerie Center skriptů. Když to uděláte, nezapomeňte při nahrávání vašeho příspěvku přidat značku **Python** .

> [!NOTE]
> Aby bylo možné odeslat obsah do [centra skriptů](https://gallery.technet.microsoft.com/scriptcenter) , je vyžadováno minimálně 100 bodů.

## <a name="requesting-a-runbook-or-module"></a>Vyžádání Runbooku nebo modulu

Můžete odesílat požadavky na [uživatelský hlas](https://feedback.azure.com/forums/246290-azure-automation/).  Pokud potřebujete nápovědu k zápisu Runbooku nebo máte dotaz týkající se PowerShellu, odešlete na náš [Fórum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)otázku.

## <a name="next-steps"></a>Další postup

* Informace o tom, jak začít se sadami Runbook, najdete [v tématu Správa runbooku v Azure Automation](manage-runbooks.md)
* Informace o rozdílech mezi PowerShellem a pracovním postupem PowerShellu se sadami Runbook najdete v tématu [výuka pracovního postupu prostředí PowerShell](automation-powershell-workflow.md) .
* Další informace o PowerShellu, včetně referenčních modulů jazyka a výukových modulů, najdete v [dokumentaci](https://docs.microsoft.com/en-us/powershell/scripting/overview)k PowerShellu.
