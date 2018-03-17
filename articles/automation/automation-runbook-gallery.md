---
title: "Galerie Runbooků a modulů pro Azure Automation."
description: "Runbooky a moduly od společnosti Microsoft a komunitou jsou k dispozici pro instalaci a použití ve vašem prostředí Azure Automation.  Tento článek popisuje, jak přistupovat k těmto prostředkům a přispívání do Galerie runbooků."
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 6a9298c0b7331bfa8af76eb904d256f6302816bf
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Galerie Runbooků a modulů pro Azure Automation.
Místo vytvoření vlastní sady runbook a modulů ve službě Azure Automation, můžete zpřístupnit různé scénáře, které jste již vytvořili společností Microsoft a komunitou.  Můžete použít tyto scénáře bez jakýchkoli úprav nebo můžete používat jako výchozí bod a je upravit pro vaše konkrétní požadavky.

Můžete získat runbooků [Galerie Runbooků](#runbooks-in-runbook-gallery) a moduly z [Galerie prostředí PowerShell](#modules-in-powerShell-gallery).  Také můžete přispívat komunitě sdílením scénáře, které vyvíjíte.

## <a name="runbooks-in-runbook-gallery"></a>Sady Runbook v Galerie Runbooků
[Galerie Runbooků](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=RootCategory&f\[0\].Value=WindowsAzure&f\[1\].Type=SubCategory&f\[1\].Value=WindowsAzure_automation&f\[1\].Text=Automation) poskytuje celou řadu runbooky od Microsoftu a komunity, který můžete importovat do Azure Automation. Buď můžete stáhnout sady runbook z galerie, který je hostován v [centra skriptů TechNet](https://gallery.technet.microsoft.com/scriptcenter/site/upload), nebo můžete přímo naimportovat sady runbook z Galerie na portálu Azure.

Lze importovat pouze přímo z Galerie sady Runbook pomocí portálu Azure. Nelze provést tuto funkci pomocí prostředí Windows PowerShell.

> [!NOTE]
> Je třeba ověřit obsah jakékoli sady runbook můžete získat z Galerie Runbooků a buďte velmi opatrní při instalaci a spustil je v produkčním prostředí. |
> 
> 

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Chcete-li importovat sady runbook z Galerie sady Runbook pomocí portálu Azure
1. Na webu Azure Portal otevřete účet Automation.
2. V části **automatizace procesu**, klikněte na **Galerie sady Runbook**
3. Vyhledejte položku Galerie a vybrat zobrazíte její podrobnosti. Na levé straně můžete zadat parametry další vyhledávání pro vydavatele a typu.
   
    ![Procházet galerii](media/automation-runbook-gallery/browse-gallery.png)
5. Klikněte na **zobrazení zdroje projektu** zobrazíte položky v [centra skriptů TechNet](http://gallery.technet.microsoft.com/).
6. Chcete-li importovat položku, klikněte na Zobrazit podrobnosti, a klikněte **importovat** tlačítko.
   
    ![Tlačítko Import](media/automation-runbook-gallery/gallery-item-detail.png)
7. Volitelně můžete změnit název sady runbook a pak klikněte na tlačítko **OK** k importu sady runbook.
8. Runbook se zobrazí na **Runbooky** kartě pro účet služby Automation.

### <a name="adding-a-runbook-to-the-runbook-gallery"></a>Přidání sady runbook do Galerie runbooků
Společnost Microsoft doporučuje, můžete přidat do Galerie sady Runbook, které by být užitečné ostatním zákazníkům sady runbook.  Můžete přidat sadu runbook pomocí [nahrávání do centra skriptů](http://gallery.technet.microsoft.com/site/upload) vezme v úvahu následující podrobnosti.

* Je nutné zadat *systému Windows Azure* pro **kategorie** a *automatizace* pro **podkategorie** pro sadu runbook, který se má zobrazit v Průvodce.  
* Nahrávání musí být do jednoho souboru .ps1 nebo .graphrunbook.  Pokud runbook vyžaduje, aby všechny moduly, podřízené runbooky a prostředky, by měl těch, které v popisu odesílání a v sekci komentáře sady runbook seznamu.  Pokud máte scénáři, které vyžadují více sad runbook, každý nahrát samostatně a seznam názvů souvisejících runbooků v každé z jejich popisy. Ujistěte se, že používáte stejné značky, aby zobrazovala ve stejné kategorii. Uživatel bude muset přečíst popis vědět, že jiné sady runbook jsou povinné scénář fungovat.
* Přidání značka "GraphicalPS", při publikování **grafický runbook** (ne grafický Workflow). 
* Vložit fragment kódu prostředí PowerShell nebo pracovního postupu Powershellu do popis pomocí **část kódu vložit** ikonu.
* Souhrn pro nahrávání se zobrazí ve výsledcích Galerie Runbooků, proto byste měli poskytnout podrobné informace, které pomáhá identifikovat funkce sady runbook uživatele.
* Jednu až tři z těchto značek byste měli přiřadit k odeslání.  Sada runbook je uveden v průvodci v části kategorie, které odpovídají jeho značky.  Průvodce jsou ignorovány všechny značky není v tomto seznamu. Pokud nezadáte žádné odpovídající značky, sada runbook je uvedený v jiné kategorii.
  
  * Backup
  * Správa kapacit
  * Řízení změn
  * Dodržování předpisů
  * Dev / testovací prostředí
  * Zotavení po havárii
  * Monitorování
  * Oprava
  * Zřizování
  * Náprava
  * Správa životního cyklu virtuálních počítačů
* Automatizace aktualizuje Galerie tak vaše příspěvky se nezobrazí okamžitě jednou za hodinu.

## <a name="modules-in-powershell-gallery"></a>Moduly v galerii prostředí PowerShell
Moduly prostředí PowerShell obsahují rutiny, které můžete použít ve vašich sadách runbook, a jsou k dispozici v existující moduly, které můžete nainstalovat ve službě Azure Automation [Galerie prostředí PowerShell](http://www.powershellgallery.com).  Můžete spustit tento Galerie z portálu Azure a nainstalovat je přímo do Azure Automation, nebo můžete je stáhnout a nainstalovat ručně.  

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Postup importování modulu z Galerie automatizace modulu pomocí portálu Azure
1. Na webu Azure Portal otevřete účet Automation.
2. Vyberte **moduly** pod **sdílené prostředky** otevření seznamu modulů.
4. Klikněte na tlačítko **procházet galerii** z horní části stránky.
   
    ![V galerii modulů](media/automation-runbook-gallery/modules-blade.png) <br>
5. Na **procházet galerii** stránky, můžete vyhledávat podle následující pole:
   
   * Název modulu
   * Značky
   * Autor
   * Název prostředku rutiny nebo DSC
6. Vyhledejte modul, který vás zajímá a vyberte ho zobrazíte její podrobnosti.  
   Když můžete přejít k podrobnostem konkrétního modulu, můžete zobrazit další informace o modulu, včetně odkaz zpět do Galerie prostředí PowerShell potřebné závislosti a všechny rutiny a/nebo prostředky DSC, které tento modul obsahuje.
   
    ![Podrobnosti o modulu prostředí PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png) <br>
7. Nainstalovat modul přímo do Azure Automation, klikněte na **Import** tlačítko.
8. Po kliknutí na tlačítko Importovat na **importovat** podokně se zobrazí název modulu, který chcete importovat. Pokud jsou nainstalovány všechny závislosti, **OK** se aktivuje tlačítko. Pokud jsou chybějících závislostí, je třeba importovat ty před importováním tohoto modulu.
9. Na **importovat** klikněte na **OK** k importu modulu. Zatímco Azure Automation importuje modul ke svému účtu, extrahuje metadata o modulu a rutiny. Může to trvat několik minut vzhledem k tomu, že každá aktivita musí být rozbalena.
10. Po dokončení zobrazí počáteční oznámení se nasazuje modul a jiné oznámení.
11. Po importu modulu uvidíte dostupných aktivit a její prostředky v runboocích a konfigurace požadovaného stavu můžete použít.

## <a name="requesting-a-runbook-or-module"></a>Požaduje sady runbook nebo modulu
Může odesílat požadavky na [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Pokud potřebujete pomoc, zápis sady runbook nebo máte dotazy týkající se prostředí PowerShell, odeslat dotaz na našem [fórum](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Další kroky
* Kroky s runbooky, najdete v tématu [vytvoření nebo import runbooku ve službě Azure Automation](automation-creating-importing-runbook.md)
* Chcete-li pochopit rozdíly mezi prostředí PowerShell a pracovní postup prostředí PowerShell pomocí runbooků, přečtěte si téma [pracovního postupu Powershellu učení](automation-powershell-workflow.md)

