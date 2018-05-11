---
title: Můj první runbook pracovního postupu Powershellu ve službě Azure Automation
description: Kurz vás provede vytvořením, otestováním a publikováním jednoduchého textového runbooku, který využívá pracovní postup PowerShellu.
keywords: pracovní postup v powershellu, příklady pracovního postupu v powershellu, pracovní postup powershell
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: cd57c6be9d962899bb3468fb86323af3fe01bc5b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="my-first-powershell-workflow-runbook"></a>Můj první runbook pracovního postupu PowerShellu

> [!div class="op_single_selector"]
> * [Grafický](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Pracovní postup PowerShellu](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

V tomto kurzu se seznámíte s vytvořením [runbooku pracovního postupu PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks) ve službě Azure Automation. Spuštění s jednoduchým runbookem, který testování a publikování při vysvětlením, jak sledovat stav úlohy sady runbook. Potom runbook upravíte, aby skutečně spravoval prostředky Azure, v tomto případě virtuální počítač Azure. Nakonec můžete nastavit sadu runbook robustnější přidáním parametrů.

## <a name="prerequisites"></a>Požadované součásti
Pro absolvování tohoto kurzu potřebujete:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](automation-offering-get-started.md), abyste si mohli runbook podržet a mohli ověřovat prostředky Azure.  Tento účet musí mít oprávnění ke spuštění a zastavení virtuálního počítače.
* Virtuální počítač Azure. Zastavení a spuštění tohoto počítače, proto to nesmí být produkční počítač.

## <a name="step-1---create-new-runbook"></a>Krok 1 – vytvoření nového runbooku
Začněte vytvořením jednoduchého runbooku, který zobrazí text *Hello, World*.

1. Na webu Azure Portal otevřete účet Automation.

   Stránka účtu Automation nabízí rychlý přehled prostředků v tomto účtu. Už byste tam měli mít nějaké prostředky. Většina z nich jsou moduly, které jsou automaticky obsažené v novém účtu Automation. Také potřebujete asset přihlašovacích údajů, který je uvedený v [požadavcích](#prerequisites).

1. Klikněte na tlačítko **Runbooky** pod **automatizace procesu** otevřete seznam runbooků.
2. Kliknutím na vytvořit novou sadu runbook **+ přidat runbook** tlačítko a potom **vytvořit nový runbook**.
3. Dejte runbooku název *MyFirstRunbook-Workflow*.
4. V takovém případě se chystáte vytvořit [runbook pracovního postupu Powershellu](automation-runbook-types.md#powershell-workflow-runbooks) tak vyberte **pracovního postupu Powershellu** pro **typ Runbooku**.
5. Kliknutím na **Vytvořit** vytvoříte runbook a otevřete textový editor.

## <a name="step-2---add-code-to-the-runbook"></a>Krok 2 – přidání kódu do runbooku
Kód můžete buď zadat přímo do runbooku, nebo můžete vybrat rutiny, runbooky a assety z ovládacího prvku Knihovna a přidat je do runbooku spolu se všemi souvisejícími parametry. Pro účely tohoto postupu můžete zadat přímo do runbooku.

1. Vaše sada runbook je teď prázdný a obsahuje jenom požadované *pracovního postupu* – klíčové slovo, název runbooku a složené závorky, který encases celého pracovního postupu.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```
2. Napište text *Write-Output "Hello World".* do prostoru mezi složené závorky.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```
3. Kliknutím na **Uložit** runbook uložte.

## <a name="step-3---test-the-runbook"></a>Krok 3 – otestování runbooku
Před publikováním runbooku, které ho zpřístupní v produkčním prostředí, byste měli runbook otestovat a ujistit se, že funguje správně. Když runbook testujete, spustíte jeho  verzi **Koncept** a interaktivně zobrazíte jeho výsledek.

1. Kliknutím na **Testovací podokno** otevřete testovací podokno.
2. Kliknutím na **Spustit** spustíte test. Měla by to být jediná povolená možnost.
3. Vytvoří se [úloha runbooku](automation-runbook-execution.md) a její stav se zobrazí.

   Počáteční stav úlohy bude *Zařazeno ve frontě*. To označuje, že čekáte na zpřístupnění pracovního procesu runbooku v cloudu. Přesune na *počáteční* když pracovní proces úlohu a potom *systémem* při runbook skutečně spustí.  

1. Po dokončení úlohy runbooku se zobrazí jeho výstup. V případě, byste měli vidět *Hello, World*.<br><br> ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)
2. Zavřete testovací podokno a vraťte se na plátno.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4 – publikování a spuštění runbooku
Vytvořený runbook je stále v režimu konceptu. Je potřeba publikovat před jeho spuštěním v produkčním prostředí. Když runbook publikujete, přepíšete vydanou verzi verzí v režimu konceptu. Ve vašem případě publikovanou verzi ještě nemáte vzhledem k tomu, že jste právě vytvořili sadu runbook.

1. Kliknutím na **Publikovat** runbook publikujte a po zobrazení výzvy klikněte na **Ano**.
2. Pokud jste posunete doleva, abyste runbook viděli v **Runbooky** podokně nyní zobrazuje **stav vytváření** z **publikováno**.
3. Posuňte se zpět doprava, abyste viděli podokno **MyFirstRunbook-Workflow**.  
   Možnosti v horní části nám umožňují spuštění runbooku, naplánování jeho spuštění někdy v budoucnu nebo vytvoření [webhooku](automation-webhooks.md), který umožní spuštění prostřednictvím volání protokolu HTTP.
4. Chcete spustit runbook, klikněte na tlačítko **spustit** a potom **Ano** po zobrazení výzvy.<br><br> ![Spuštění runbooku](media/automation-first-runbook-textual/automation-runbook-controls-start.png)
5. Podokno úlohy se spustí úloha sady runbook, kterou jste vytvořili. V tomto podokně můžete zavřít, ale v takovém případě můžete ho můžete nechat otevřený, mohli sledovat průběh úlohy.
6. Stav úlohy se zobrazí v **Souhrn úlohy** a odpovídá stavům, že jste viděli při testování runbooku.<br><br> ![Souhrn úlohy](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)
7. Když se jako stav runbooku zobrazí *Dokončeno*, klikněte na **Výstup**. Otevře se podokno výstup a zobrazí se vaše *Hello, World*.<br><br> ![Souhrn úlohy](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)  
8. Zavřete podokno Výstup.
9. Klikněte na **Všechny protokoly** a otevřete podokno Datové proudy, které patří k úloze runbooku. Byste měli vidět jenom *Hello, World* ve výstupu datového proudu, ale můžou se zobrazit jiné datové proudy z úlohy runbooku, například podrobný nebo Chyba Pokud runbook zapisuje na ně.<br><br> ![Souhrn úlohy](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)
10. Zavřete podokno Datové proudy a podokno Úloha a vraťte se do podokna MyFirstRunbook.
11. Kliknutím na **Úlohy** otevřete podokno Úlohy, které patří k tomuto runbooku. Vypíšou se všechny úlohy, které tento runbook vytvořil. Měli byste vidět jenom jednu úlohu, protože jste spustili jenom jednou.<br><br> ![Úlohy](media/automation-first-runbook-textual/runbook-control-job-tile.png)
12. Kliknutím na tuto úlohu chcete-li otevřít podokno úloha, která jste si zobrazili při spuštění sady runbook. Pomocí této možnosti se můžete vrátit v čase a zobrazit si podrobnosti libovolné úlohy, která byla pro konkrétní runbook vytvořena.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Krok 5 – přidání ověřování ke správě prostředků Azure
Jste otestovali a publikovali runbookem, ale zatím nedělá nic užitečného. Chcete ho spravovat prostředky Azure. Nebudete moci v případě, že pokud nemáte ověření pomocí přihlašovacích údajů, které jsou uvedené v to udělat [požadavky](#prerequisites). Můžete to udělat pomocí **Connect-AzureRmAccount** rutiny.

1. Kliknutím na **Upravit** v podokně MyFirstRunbook-Workflow otevřete textový editor.
2. Není nutné **Write-Output** řádek už, proto pokračujte a odstraňte ji.
3. Umístěte kurzor na prázdný řádek mezi složenými závorkami.
4. Zadejte nebo zkopírujte a vložte následující kód, který bude zpracovávat ověřování pomocí vašeho účtu Automation Spustit jako:

   ```powershell-interactive
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
5. Klikněte na tlačítko **testovací podokno** tak, aby mohli runbook otestovat.
6. Kliknutím na **Spustit** spustíte test. Po dokončení byste měli obdržet výstup podobný následujícímu se základními informacemi z vašeho účtu. Tím se potvrdí platnost přihlašovacích údajů.<br><br> ![Ověření](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Krok 6 – přidání kódu pro spuštění virtuálního počítače
Teď, když runbook umí ověřit vašeho předplatného Azure, můžete spravovat prostředky. Můžete přidat příkaz ke spuštění virtuálního počítače. Můžete vybrat jakýkoli virtuální počítač ve vašem předplatném Azure a teď jste hardcoding, název sady runbook.

1. Po *Connect-AzureRmAccount*, typ *Start-AzureRmVM-Name 'VMName' - ResourceGroupName 'NameofResourceGroup'* název a název skupiny prostředků virtuálního počítače spustit.  

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   }
   ```
2. Uložte runbook a pak klikněte na tlačítko **testovací podokno** tak, aby ji můžete otestovat.
3. Kliknutím na **Spustit** spustíte test. Po jeho dokončení zkontrolujte, jestli se virtuální počítač spustil.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Krok 7 – přidání vstupního parametru do runbooku
vaše sada runbook aktuálně spouští virtuální počítač tento pevně zakódované můžete v sadě runbook, ale by být užitečnější, pokud se virtuální počítač mohl při spuštění runbooku. Vstupní parametry přidáte do sady runbook pro poskytnutí těchto funkcí.

1. Přidejte do runbooku parametry pro *VMName* a *ResourceGroupName* a použijte tyto proměnné s rutinou **Start-AzureRmVM**, jak vidíte na níže uvedeném příkladu.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )  
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```
2. Uložte runbook a otevřete testovací podokno. Teď můžete zadat hodnoty dvou vstupních proměnných, které jsou v testu.
3. Zavřete testovací podokno.
4. Kliknutím na **Publikovat** publikujte novou verzi runbooku.
5. Zastavte virtuální počítač, který jste v předchozím kroku spustili.
6. Kliknutím na **Spustit** spustíte runbook. Zadejte **VMName** a **ResourceGroupName** pro virtuální počítač, který chcete spustit.<br><br> ![Spuštění runbooku](media/automation-first-runbook-textual/automation-pass-params.png)<br>  
7. Po dokončení runbooku zkontrolujte, jestli se virtuální počítač spustil.  

## <a name="next-steps"></a>Další postup
* První kroky s grafickými runbooky najdete v článku [Můj první grafický runbook](automation-first-runbook-graphical.md).
* První kroky s powershellovými runbooky najdete v článku [Můj první powershellový runbook](automation-first-runbook-textual-powershell.md).
* Další informace o typech runbooků, jejich výhodách a omezeních najdete v článku [Typy runbooků ve službě Azure Automation](automation-runbook-types.md).
* Další informace o funkci podpory powershellových skriptů najdete v článku [Nativní podpora powershellových skriptů ve službě Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
