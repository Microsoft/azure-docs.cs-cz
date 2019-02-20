---
title: Galerie modulů a Runbooků Azure Automation
description: Jsou si můžete nainstalovat a používat ve vašem prostředí Azure Automation. Runbooky a moduly, od Microsoftu a komunity.  Tento článek popisuje, jak přistupovat k těmto prostředkům a k přispívání do Galerie runbooků.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 68eb3f3e5c568bb518251aca2a4e76932ce2eee5
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416203"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Galerie modulů a Runbooků Azure Automation

Místo vytváření vlastních modulů a runbooků ve službě Azure Automation, můžete přistupovat scénáře, které již byly vytvořeny od Microsoftu a komunity.

Můžete získat runbooky [Galerie Runbooků](#runbooks-in-runbook-gallery) a modulů [Galerie prostředí PowerShell](#modules-in-powerShell-gallery).  Můžete přispívat také v komunitě sdílením scénáře, které vyvíjíte, naleznete v tématu [přidání sady runbook do Galerie](automation-runbook-gallery.md#adding-a-runbook-to-the-runbook-gallery)

## <a name="runbooks-in-runbook-gallery"></a>Sady Runbook ve galerii Runbooků

[Galerie Runbooků](https://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=RootCategory&f\[0\].Value=WindowsAzure&f\[1\].Type=SubCategory&f\[1\].Value=WindowsAzure_automation&f\[1\].Text=Automation) od Microsoftu a komunity, který můžete importovat do služby Azure Automation poskytuje celou řadu sad runbook. Pokud chcete použít jeden, stáhnout z galerie, která je hostována v sadě runbook [centra skriptů TechNet](https://gallery.technet.microsoft.com/scriptcenter/site/upload), nebo může přímo importovat sady runbook z Galerie na webu Azure Portal.

Můžete importovat pouze přímo z Galerie Runbooků pomocí webu Azure portal. Nelze provést tuto funkci pomocí Windows Powershellu.

> [!NOTE]
> Měli byste ověřit obsah všechny runbooky pořídit v galerii Runbooků a buďte extrémně opatrní při instalaci a spuštění v produkčním prostředí.

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Import runbooku z Galerie Runbooků pomocí webu Azure portal

1. Na webu Azure Portal otevřete účet Automation.
2. V části **automatizace procesů**, klikněte na **Galerie Runbooků**
3. Vyhledejte položku Galerie a vybrat zobrazíte její podrobnosti. Na levé straně můžete zadat další vyhledávací parametry pro vydavatele a typu.

   ![Procházet galerii](media/automation-runbook-gallery/browse-gallery.png)

4. Klikněte na **zobrazit zdrojový projekt** zobrazíte položky ve [centra skriptů TechNet](https://gallery.technet.microsoft.com/).
5. Pokud chcete importovat položky, klikněte na něj a zobrazit jeho podrobnosti a potom klikněte na tlačítko **importovat** tlačítko.

   ![Tlačítko Import](media/automation-runbook-gallery/gallery-item-detail.png)

6. Volitelně můžete změnit název sady runbook a potom klikněte na **OK** a importujte runbook.
7. Runbook se zobrazí na **sady Runbook** kartu pro účet Automation.

### <a name="adding-a-runbook-to-the-runbook-gallery"></a>Přidání sady runbook do Galerie runbooků

Microsoft doporučuje, abyste k přidání runbooků do Galerie Runbooků, které si myslíte, že bude užitečné pro ostatní zákazníci.  Můžete přidat sady runbook pomocí [pak ho nahrát do centra skriptů](https://gallery.technet.microsoft.com/site/upload) zohledněním následující podrobnosti.

* Je nutné zadat *Windows Azure* pro **kategorie** a *automatizace* pro **podkategorie** pro sadu runbook, který se má zobrazit Průvodce.  
* Nahrávání musí být jediný `.ps1` nebo `.graphrunbook` souboru.  Pokud runbook vyžaduje všechny moduly, podřízené runbooky a prostředky, by seznam těchto položek v popisu odesílání a v části komentáře sady runbook.  Pokud máte scénář, které vyžadují více sad runbook, nahrajte každý samostatně a seznam názvů souvisejících sad runbook v každé z jejich popisy. Ujistěte se, že je možné použít stejné značky tak, aby se zobrazí ve stejné kategorii. Uživatel bude muset přečíst popis vědět, že se vyžadují další sady runbook scénář fungovat.
* Přidat značku "GraphicalPS" Pokud publikujete **grafický runbook** (ne grafické pracovního postupu).
* Vložit fragment kódu Powershellu nebo pracovního postupu Powershellu pomocí popis **vložit část s kódem** ikonu.
* Proto byste měli poskytnout podrobné informace, které pomáhá uživateli určit funkce sady runbook, zobrazí se souhrn pro nahrávání ve výsledcích v galerii Runbooků.
* Nahrávání byste měli přiřadit jedné do tří z těchto značek.  Sada runbook je uveden v průvodci v části kategorie, které odpovídají jeho značky.  Žádné značky není v tomto seznamu jsou ignorovány průvodcem. Pokud nezadáte žádné odpovídající značky, sada runbook je uveden v jiné kategorii.
  
  * Backup
  * Správa kapacit
  * Řízení změn
  * Dodržování předpisů
  * Dev / testovacích prostředí
  * Zotavení po havárii
  * Monitorování
  * Oprava
  * Zřizování
  * Náprava
  * Správa životního cyklu virtuálního počítače

* Automatizace aktualizuje v galerii, neuvidíte své příspěvky ihned jednou za hodinu.

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
9.  Po dokončení zobrazí počáteční oznámení, že se nasazuje modulu a další oznámení.
10. Po importu modulu uvidíte dostupných aktivit. Můžete použít jeho prostředky v runboocích a Desired State Configuration.

> [!NOTE]
> Moduly, které podporují jenom prostředí PowerShell core nejsou podporované ve službě Azure Automation a se nepovedlo importovat na webu Azure Portal, nebo se nasadí přímo z Galerie prostředí PowerShell.

## <a name="python-runbooks"></a>Sady Runbook Pythonu

Jsou k dispozici v sadách Runbook Python [Galerie centra skriptů](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Runbook Python do Galerie centra skriptů můžete přispívat. Když, zajistíte tím, že přidáte značku **Python** při odesílání vašeho příspěvku.

## <a name="requesting-a-runbook-or-module"></a>Vyžádání do sady runbook nebo modulu

Můžete odesílat žádosti o [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Pokud potřebujete pomoc s psaním sady runbook nebo máte dotaz týkající se prostředí PowerShell, odeslat dotaz do našich [fórum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Další postup

* Začínáme se sadami runbook, najdete v článku [spravovat sady runbook ve službě Azure Automation](manage-runbooks.md)
* Rozdíly mezi Powershellu a pracovní postup Powershellu se sadami runbook najdete v tématu [Learning Powershellový pracovní postup](automation-powershell-workflow.md)