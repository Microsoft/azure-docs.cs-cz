---
title: Můj první Powershellový runbook ve službě Azure Automation
description: Kurz vás provede vytvořením, otestováním a publikováním jednoduchého powershellového runbooku.
keywords: azure powershell, kurz k powershellovému scriptu, automatizace powershellu
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 11/27/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f083a5a9fe8027467eb95711a15725859f59e4fa
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438044"
---
# <a name="my-first-powershell-runbook"></a>Můj první powershellový runbook

> [!div class="op_single_selector"]
> * [Grafický](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Pracovní postup PowerShellu](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

V tomto kurzu se seznámíte s vytvořením [powershellového runbooku](automation-runbook-types.md#powershell-runbooks) ve službě Azure Automation. Začnete s jednoduchým runbookem pro testování a publikování, zatímco se dozvíte, jak sledovat stav úlohy runbooku. Potom runbook upravíte, aby skutečně spravoval prostředky Azure, v tomto případě virtuální počítač Azure. Nakonec provedete runbook robustnější přidáním parametrů runbooku.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](automation-quickstart-create-account.md), abyste si mohli runbook podržet a mohli ověřovat prostředky Azure. Tento účet musí mít oprávnění ke spuštění a zastavení virtuálního počítače.
* Virtuální počítač Azure. Zastavení a spuštění tohoto počítače, proto to nesmí být produkční virtuální počítač.

## <a name="create-new-runbook"></a>Vytvořit nový runbook.

Začněte vytvořením jednoduchého runbooku, který zobrazí text *Hello World*.

1. Na webu Azure Portal otevřete účet Automation.
2. Klikněte na tlačítko **sady Runbook** pod **automatizace procesů** otevřete seznam runbooků.
3. Vytvořit novou sadu runbook kliknutím **+ přidat runbook** tlačítko a pak **vytvořit nový runbook**.
4. Dejte runbooku název *MyFirstRunbook-PowerShell*.
5. V takovém případě se chystáte vytvořit [Powershellového runbooku](automation-runbook-types.md#powershell-runbooks) proto vyberte **Powershell** pro **typ Runbooku**.
6. Kliknutím na **Vytvořit** vytvoříte runbook a otevřete textový editor.

## <a name="add-code-to-the-runbook"></a>Přidání kódu do runbooku

Kód můžete buď zadat přímo do runbooku, nebo můžete vybrat rutiny, runbooky a assety z ovládacího prvku Knihovna a přidat je do runbooku spolu se všemi souvisejícími parametry. V tomto návodu zadáte přímo v sadě runbook.

1. Vaše sada runbook je aktuálně prázdný, typ *Write-Output "Hello World."* v těle skriptu.

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  

2. Kliknutím na **Uložit** runbook uložte.

##<a name="a-namestep-3---test-the-runbook-test-the-runbook"></a><a name="step-3---test-the-runbook"> Otestování runbooku

Před publikováním runbooku, které ho zpřístupní v produkčním prostředí, byste měli runbook otestovat a ujistit se, že funguje správně. Když runbook testujete, spustíte jeho  verzi **Koncept** a interaktivně zobrazíte jeho výsledek.

1. Kliknutím na **Testovací podokno** otevřete testovací podokno.
2. Kliknutím na **Spustit** spustíte test. Měla by to být jediná povolená možnost.
3. Vytvoří se [úloha runbooku](automation-runbook-execution.md) a její stav se zobrazí.

   Počáteční stav úlohy *ve frontě* označující, že čekáte pracovního procesu runbooku v cloudu k dispozici. Přejde do *počáteční* když pracovní proces úlohu a potom *systémem* když sada runbook skutečně spustí, změní.  

4. Po dokončení úlohy runbooku se zobrazí jeho výstup. V případě, byste měli vidět *Hello World*.

   ![Výstup testovacího podokna](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  

5. Zavřete testovací podokno a vraťte se na plátno.

## <a name="publish-and-start-the-runbook"></a>Publikování a spuštění sady runbook

Vytvořený runbook je stále v režimu konceptu. Musí být zveřejněna před spuštěním v produkčním prostředí.  Když runbook publikujete, přepíšete vydanou verzi verzí v režimu konceptu. V případě publikovanou verzi ještě nemáte protože jste runbook teprve vytvořili.

1. Kliknutím na **Publikovat** runbook publikujte a po zobrazení výzvy klikněte na **Ano**.
1. Pokud se posunete doleva, abyste runbook viděli v **sady Runbook** podokně nyní zobrazuje **stav vytváření** z **publikováno**.
1. Posuňte se zpět doprava, abyste viděli podokno **MyFirstRunbook-PowerShell**.  
   Možnosti v horní části nám umožňují spuštění runbooku, zobrazení runbooku, naplánování jeho spuštění někdy v budoucnu nebo vytvoření [webhooku](automation-webhooks.md), který umožní spuštění prostřednictvím volání protokolu HTTP.
1. Chcete spouštět sadu runbook, proto klikněte na **Start** a potom klikněte na tlačítko **Ok** po otevření stránky spuštění Runbooku.
1. Na stránce úlohy je otevřen pro úlohy runbooku, který jste vytvořili. v tomto podokně můžete zavřít, ale v takovém případě vám ho můžete nechat otevřený mohli sledovat průběh úlohy.
1. Stav úlohy se zobrazuje v **Souhrn úlohy** a odpovídá stavům, že jste viděli při testování runbooku.

   ![Souhrn úlohy](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Jednou jako stav runbooku zobrazí *dokončeno*v části **přehled** klikněte na tlačítko **výstup**. Otevře se podokno výstup a zobrazí se váš *Hello World*.

   ![Výstup úlohy](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Zavřete stránku výstup.
1. Klikněte na **Všechny protokoly** a otevřete podokno Datové proudy, které patří k úloze runbooku. Byste měli vidět jenom *Hello World* ve výstupu datového proudu, ale tento výstup můžete zobrazit jiné datové proudy z úlohy runbooku, například podrobný nebo Chyba Pokud do nich runbook zapisuje.

   ![Všechny protokoly](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Zavřete stránku datových proudů a na stránku úlohy se vrátíte na stránku MyFirstRunbook-PowerShell.
1. V části **podrobnosti**, klikněte na tlačítko **úlohy** a otevřete tak podokno úloh této sady runbook. Tato stránka obsahuje seznam všech úloh, které tento runbook vytvořil. Ve výpisu by se měla zobrazit pouze jedna úloha, protože jste ji spustili jenom jednou.

   ![Seznam úloh](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  

1. Na tuto úlohu můžete kliknout a otevřít podokno Úloha, které jste zobrazili při spuštění runbooku. Tuto akci můžete vrátit v čase a zobrazit si podrobnosti libovolné úlohy, která byla vytvořena konkrétní sady runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Přidání ověřování ke správě prostředků Azure

Runbook jste otestovali a publikovali, ale zatím nedělá nic užitečného. Chcete po něm, aby spravoval prostředky Azure. Není možné provést, ale pokud nemáte ověření pomocí připojení spustit jako, které se automaticky vytvoří při vytvoření účtu automation. Použití připojení spustit jako s **Connect-AzureRmAccount** rutiny. Pokud spravujete prostředků napříč několika předplatnými, budete muset použít **- AzureRmContext** parametr spolu s [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext).

   ```powershell
   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $AzureContext
   ```

1. Kliknutím otevřete textový editor **upravit** na stránce MyFirstRunbook-PowerShell.
1. není nutné **Write-Output** řádek už, neváhejte a sami ho odstranit.
1. Zadejte nebo zkopírujte a vložte následující kód, který bude zpracovávat ověřování pomocí vašeho účtu Automation Spustit jako:

   ```powershell
   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** a **Login-AzureRmAccount** jsou nyní aliasy pro **Connect-AzureRMAccount**. Pokud **Connect-AzureRMAccount** rutina neexistuje, můžete použít **Add-AzureRmAccount** nebo **Login-AzureRmAccount**, nebo můžete aktualizovat moduly ve vaší službě Automation Účet pro nejnovější verze.

1. Klikněte na tlačítko **testovací podokno** tak, aby mohli runbook otestovat.
1. Kliknutím na **Spustit** spustíte test. Po dokončení byste měli obdržet výstup podobný následujícímu se základními informacemi z vašeho účtu. Tento výstup potvrzuje, že je platný účet Spustit jako.

   ![Ověření](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="add-code-to-start-a-virtual-machine"></a>Přidejte kód pro spuštění virtuálního počítače

Teď, když vaše sada runbook umí ověřit vaše předplatné Azure, můžete spravovat prostředky. můžete přidat příkaz ke spuštění virtuálního počítače. Je můžete vybrat jakýkoli virtuální počítač ve vašem předplatném Azure a pro nyní jeho název pevně zakódujete v sadě runbook.

1. Po *Connect-AzureRmAccount*, typ *Start-AzureRmVM-Name 'VMName' - ResourceGroupName 'NameofResourceGroup'* název a název skupiny prostředků virtuálního počítače spustit.  

   ```powershell
   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Uložte runbook a potom klikněte na tlačítko **testovací podokno** tak, aby ji mohli otestovat.
1. Kliknutím na **Spustit** spustíte test. Po jeho dokončení zkontrolujte, jestli se virtuální počítač spustil.

## <a name="add-an-input-parameter"></a>Přidejte vstupní parametr

Vaše sada runbook aktuálně spouští virtuální počítač tohoto pevně zakódované můžete v sadě runbook, ale by mohl být užitečnější, pokud je virtuální počítač zadat při spuštění runbooku. Přidání vstupních parametrů do runbooku pro poskytnutí těchto funkcí.

1. Přidat parametry pro *VMName* a *ResourceGroupName* do sady runbook a použijte tyto proměnné s **Start-AzureRmVM** rutiny jako v následujícím příkladu.

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Uložte runbook a otevřete testovací podokno. Teď můžete zadat hodnoty dvou vstupních proměnných, které budete v testu používat.
1. Zavřete testovací podokno.
1. Kliknutím na **Publikovat** publikujte novou verzi runbooku.
1. Zastavte virtuální počítač, který jste v předchozím kroku spustili.
1. Klikněte na tlačítko **OK** pro spuštění sady runbook. Zadejte **VMName** a **ResourceGroupName** pro virtuální počítač, který chcete spustit.<br><br> ![Předání parametru](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
1. Po dokončení runbooku zkontrolujte, jestli se virtuální počítač spustil.

## <a name="differences-from-powershell-workflow"></a>Odlišnosti od runbooků pracovních postupů PowerShellu

Powershellové runbooky mají stejný životní cyklus, možnosti a správu jako runbooky pracovních postupů PowerShellu, ale existují tady určité rozdíly a omezení:

1. Powershellové runbooky běží rychleji než runbooky pracovních postupů PowerShellu, protože neobsahují kompilační krok.
2. Runbooky pracovních postupů Powershellu podporují kontrolní body. pomocí kontrolních bodů, můžete runbooky pracovních postupů Powershellu pokračovat z libovolného bodu v sadě runbook. Powershellové runbooky můžou pokračovat jenom od začátku.
3. Runbooky pracovních postupů Powershellu podporují paralelní a sériové provádění. Powershellové runbooky může být vykonávána pouze příkazy sériově.
4. V pracovních postupů Powershellu, aktivita, příkaz nebo skript bloku může mít svoje vlastní prostředí runspace. V Powershellovém runbooku běží všechno ve skriptu v jednom prostředí runspace. Existují také určité [syntaktické rozdíly](https://technet.microsoft.com/magazine/dn151046.aspx) mezi nativním powershellovým runbookem a runbookem pracovního postupu PowerShellu.

## <a name="next-steps"></a>Další postup

* První kroky s grafickými runbooky najdete v článku [Můj první grafický runbook](automation-first-runbook-graphical.md).
* První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md).
* Další informace o typech runbooků, jejich výhodách a omezeních najdete v článku [Typy runbooků ve službě Azure Automation](automation-runbook-types.md).
* Další informace o funkci podpory powershellových skriptů najdete v článku [Nativní podpora powershellových skriptů ve službě Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
