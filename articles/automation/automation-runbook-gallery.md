---
title: Galerie modulů a Runbooků Azure Automation
description: Jsou si můžete nainstalovat a používat ve vašem prostředí Azure Automation. Runbooky a moduly, od Microsoftu a komunity.  Tento článek popisuje, jak přistupovat k těmto prostředkům a k přispívání do Galerie runbooků.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 58f666d7ebf8ac02d393a42f55e00f08d82b8cae
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337083"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Galerie modulů a Runbooků Azure Automation

Místo vytváření vlastních modulů a runbooků ve službě Azure Automation, můžete přistupovat scénáře, které již byly vytvořeny od Microsoftu a komunity.

Můžete získat [Powershellové runbooky](#runbooks-in-runbook-gallery) a [moduly](#modules-in-powerShell-gallery) z Galerie prostředí PowerShell a [runbooky Python](#python-runbooks) z Galerie centra skriptů. Můžete přispívat také v komunitě sdílením scénáře, které vyvíjíte, naleznete v tématu [přidání sady runbook do Galerie](#adding-a-runbook-to-the-runbook-gallery)

## <a name="runbooks-in-powershell-gallery"></a>Sady Runbook ve galerii prostředí PowerShell

[Galerie prostředí PowerShell](https://www.powershellgallery.com/packages) od Microsoftu a komunity, který můžete importovat do služby Azure Automation poskytuje celou řadu sad runbook. Pokud chcete použít jeden, stáhněte si sadu runbook z Galerie nebo může přímo importovat sady runbook z galerie, nebo ve svém účtu Automation na webu Azure Portal.

Můžete importovat pouze přímo z Galerie prostředí PowerShell pomocí webu Azure portal. Nelze provést tuto funkci pomocí Powershellu.

> [!NOTE]
> Měli byste ověřit obsah všechny runbooky pořídit v galerii prostředí PowerShell a buďte extrémně opatrní při instalaci a spuštění v produkčním prostředí.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Import runbooku Powershellu z Galerie Runbooků pomocí webu Azure portal

1. Na webu Azure Portal otevřete účet Automation.
2. V části **automatizace procesů**, klikněte na **Galerie Runbooků**
3. Vyberte **zdroje: Galerie prostředí PowerShell**.
4. Vyhledejte položku Galerie a vybrat zobrazíte její podrobnosti. Na levé straně můžete zadat další vyhledávací parametry pro vydavatele a typu.

   ![Procházet galerii](media/automation-runbook-gallery/browse-gallery.png)

5. Klikněte na **zobrazit zdrojový projekt** zobrazíte položky ve [centra skriptů TechNet](https://gallery.technet.microsoft.com/).
6. Pokud chcete importovat položky, klikněte na něj a zobrazit jeho podrobnosti a potom klikněte na tlačítko **importovat** tlačítko.

   ![Tlačítko Import](media/automation-runbook-gallery/gallery-item-detail.png)

7. Volitelně můžete změnit název sady runbook a potom klikněte na **OK** a importujte runbook.
8. Runbook se zobrazí na **sady Runbook** kartu pro účet Automation.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>Přidání Powershellový runbook v galerii

Microsoft doporučuje, abyste k přidání runbooků do Galerie prostředí PowerShell, který si myslíte, že bude užitečné pro ostatní zákazníci. Galerie prostředí PowerShell přijímá moduly Powershellu a skripty prostředí PowerShell. Můžete přidat sady runbook pomocí [nahrávání do Galerie prostředí PowerShell](/powershell/gallery/how-to/publishing-packages/publishing-a-package).

> [!NOTE]
> Grafické runbooky nejsou podporované v galerii prostředí PowerShell.

## <a name="modules-in-powershell-gallery"></a>Moduly Galerie prostředí PowerShell

Moduly prostředí PowerShell obsahují rutiny, které můžete použít v runboocích a jsou k dispozici v existující moduly, které můžete nainstalovat ve službě Azure Automation [Galerie prostředí PowerShell](https://www.powershellgallery.com). Můžete spustit tuto galerii na portálu Azure portal a nainstalovat přímo do Azure Automation. Můžete také si je stáhnout a je nainstalovat ručně.  

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Postup importování modulu z Galerie modul Automation pomocí webu Azure portal

1. Na webu Azure Portal otevřete účet Automation.
2. Vyberte **moduly** pod **sdílené prostředky** otevřete seznam modulů.
3. Klikněte na tlačítko **procházet galerii** z horní části stránky.

   ![Modul Galerie](media/automation-runbook-gallery/modules-blade.png)

4. Na **procházet galerii** stránky, můžete vyhledávat podle následující pole:

   * Název modulu
   * Značky
   * Autor
   * Název prostředku rutiny/DSC

5. Vyhledejte modul, který vás zajímá a vyberte ho zobrazíte její podrobnosti.  

   Když můžete přejít k podrobnostem konkrétní modul, zobrazí se další informace. Tyto informace zahrnují odkaz zpět do Galerie prostředí PowerShell, všechny požadované závislosti a všechny rutiny a prostředky DSC, které modul obsahuje.

   ![Podrobnosti o modulu prostředí PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Chcete-li nainstalovat modul přímo do Azure Automation, klikněte na tlačítko **Import** tlačítko.
7. Po kliknutí na tlačítko Import na **importovat** podokně se zobrazí název modulu, který se chystáte se import. Pokud jsou nainstalovány všechny závislosti, **OK** se aktivuje tlačítko. Pokud jste chybějící závislosti, musíte k importu těchto závislostí, předtím, než importujete tento modul.
8. Na **importovat** klikněte na **OK** importujte modul. Zatímco Azure Automation importuje modul ke svému účtu, extrahuje metadata modulu a rutin. Tato akce může trvat několik minut, protože každá aktivita je třeba extrahovat.
9. Po dokončení zobrazí počáteční oznámení, že se nasazuje modulu a další oznámení.
10. Po importu modulu uvidíte dostupných aktivit. Můžete použít jeho prostředky v runboocích a Desired State Configuration.

> [!NOTE]
> Moduly, které podporují jenom prostředí PowerShell core nejsou podporované ve službě Azure Automation a se nepovedlo importovat na webu Azure Portal, nebo se nasadí přímo z Galerie prostředí PowerShell.

## <a name="python-runbooks"></a>Sady Runbook Pythonu

Jsou k dispozici v sadách Runbook Python [Galerie centra skriptů](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Kliknutím můžete přispívat runbooky Python do Galerie centra skriptů **Nahrát příspěvek**. Když, zajistíte tím, že přidáte značku **Python** při odesílání vašeho příspěvku.

> [!NOTE]
> Aby bylo možné nahrát obsah do [centra skriptů](https://gallery.technet.microsoft.com/scriptcenter) se vyžaduje minimálně 100 bodů. 

## <a name="requesting-a-runbook-or-module"></a>Vyžádání do sady runbook nebo modulu

Můžete odesílat žádosti o [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Pokud potřebujete pomoc s psaním sady runbook nebo máte dotaz týkající se prostředí PowerShell, odeslat dotaz do našich [fórum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Další postup

* Začínáme se sadami runbook, najdete v článku [spravovat sady runbook ve službě Azure Automation](manage-runbooks.md)
* Rozdíly mezi Powershellu a pracovní postup Powershellu se sadami runbook najdete v tématu [Learning Powershellový pracovní postup](automation-powershell-workflow.md)