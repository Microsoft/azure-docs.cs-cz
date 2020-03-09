---
title: Můj první PowerShellový Runbook v Azure Automation
description: Kurz vás provede vytvořením, otestováním a publikováním jednoduchého powershellového runbooku.
keywords: azure powershell, kurz k powershellovému scriptu, automatizace powershellu
services: automation
ms.subservice: process-automation
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: b9808ddc3b61b0055642c5a0f2a82b0dc7553b33
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384860"
---
# <a name="my-first-powershell-runbook"></a>Můj první powershellový runbook

> [!div class="op_single_selector"]
> * [Grafický](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Pracovní postup PowerShellu](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

V tomto kurzu se seznámíte s vytvořením [powershellového runbooku](automation-runbook-types.md#powershell-runbooks) ve službě Azure Automation. Začněte s jednoduchým runbookm, který můžete testovat a publikovat, a Naučte se sledovat stav úlohy Runbooku. Pak upravte Runbook tak, aby skutečně spravoval prostředky Azure. v tomto případě se spustí virtuální počítač Azure. Dokončete kurz, aby se Runbook lépe robustní přidáním parametrů Runbooku.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Předpoklady

Pro absolvování tohoto kurzu potřebujete:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](automation-quickstart-create-account.md), abyste si mohli runbook podržet a mohli ověřovat prostředky Azure. Tento účet musí mít oprávnění ke spuštění a zastavení virtuálního počítače.
* Virtuální počítač Azure. Vzhledem k tomu, že tento počítač zastavíte a spustíte, neměli by se jednat o produkční virtuální počítač.
* V případě potřeby [importujte moduly Azure](shared-resources/modules.md) nebo [moduly pro aktualizaci](automation-update-azure-modules.md) na základě používaných rutin.

## <a name="differences-from-powershell-workflow-runbooks"></a>Rozdíly mezi Runbooky pracovních postupů PowerShellu

Runbooky PowerShellu mají stejný životní cyklus, možnosti a správu jako Runbooky pracovních postupů PowerShellu. Existují však určité rozdíly a omezení.

* PowerShellové Runbooky se rychle spouštějí v porovnání se sadami Runbook pracovního postupu, protože nepoužívají krok kompilace.
* Sešity PowerShellu nepodporují kontrolní body, které se používají v sadách PowerShell Workflow Runbook. Pomocí kontrolních bodů mohou Runbooky PowerShellu pracovních postupů obnovit provoz z libovolného bodu. Runbook PowerShellu může obnovit pouze operaci od začátku.
* Runbooky PowerShellu můžou příkazy spouštět jenom v sériovém tvaru. Runbooky pracovních postupů PowerShellu podporují sériové i paralelní spouštění.
* V případě Runbooku PowerShellu se všechno ve skriptu spouští v jediném prostředí runspace. V Runbooku pracovního postupu PowerShellu může mít aktivita, příkaz nebo blok skriptu vlastní prostředí runspace. 
* Runbooky PowerShellu obsahují některé [syntaktické rozdíly](https://technet.microsoft.com/magazine/dn151046.aspx) z runbooků Powershellového pracovního postupu.

## <a name="step-1---create-runbook"></a>Krok 1 – vytvoření runbooku

Začnete vytvořením jednoduchého runbooku, který zobrazí text **Hello World**.

1. Na webu Azure Portal otevřete účet Automation.
2. V části **Automatizace procesu** vyberte **Runbooky** a otevřete seznam runbooků.
3. Kliknutím na **vytvořit Runbook**vytvořte nový Runbook.
4. Dejte runbooku název **MyFirstRunbook-PowerShell**.
5. V tomto případě vytvoříte [powershellový Runbook](automation-runbook-types.md#powershell-runbooks). Jako **typ Runbooku**vyberte **PowerShell** .
6. Kliknutím na **Vytvořit** vytvoříte runbook a otevřete textový editor.

## <a name="step-2---add-code-to-the-runbook"></a>Krok 2 – přidání kódu do runbooku

Kód můžete buď zadat přímo do runbooku, nebo můžete vybrat rutiny, runbooky a assety z ovládacího prvku Knihovna a přidat je do runbooku spolu se všemi souvisejícími parametry. Pro tento kurz budete psát kód přímo do Runbooku.

1. Vaše sada Runbook je aktuálně prázdná. Do textu skriptu zadejte `Write-Output "Hello World"`.

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. Kliknutím na **Uložit** runbook uložte.

## <a name="step-3---test-the-runbook"></a> Krok 3 – otestování Runbooku

Před publikováním Runbooku, aby byl dostupný v produkčním prostředí, byste ho měli otestovat, abyste se ujistili, že funguje správně. Testování Runbooku spustí svou verzi konceptu a umožňuje interaktivní zobrazení výstupu.

1. Kliknutím na **Testovací podokno** otevřete testovací podokno.
2. Kliknutím na **Spustit** spustíte test. Měla by to být jediná povolená možnost.
3. Všimněte si, že se vytvoří [úloha Runbooku](automation-runbook-execution.md) a její stav se zobrazí v podokně.

   Stav úlohy se spustí jako **zařazený do fronty**, což značí, že úloha čeká na zpřístupnění pracovního procesu Runbooku v cloudu. Stav se změní na **zahájeno** , když pracovní proces tuto úlohu vystaví. Nakonec se stav spustí, **když se** Runbook skutečně začne spouštět.

4. Po dokončení úlohy Runbooku zobrazí podokno test svůj výstup. V tomto případě se zobrazí **Hello World**.

   ![Výstup testovacího podokna](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. Zavřete testovací podokno a vraťte se na plátno.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4 – publikování a spuštění runbooku

Sada Runbook, kterou jste vytvořili, je stále v režimu konceptu. Abyste ho mohli spustit v produkčním prostředí, je potřeba ho publikovat. Když runbook publikujete, přepíšete vydanou verzi verzí v režimu konceptu. V tomto případě zatím ještě publikovanou verzi nemáte, protože jste runbook teprve vytvořili.

1. Kliknutím na **Publikovat** runbook publikujte a po zobrazení výzvy klikněte na **Ano**.
1. Posunutím doleva zobrazíte sadu Runbook na stránce sady Runbook a Všimněte si, že hodnota **stav vytváření** je nastavena na **Publikováno**.
1. Posuňte se zpět doprava, abyste viděli podokno **MyFirstRunbook-PowerShell**.
   
   Možnosti v horní části umožňují spuštění sady Runbook nyní, naplánování budoucího času spuštění nebo vytvoření [Webhooku](automation-webhooks.md) , aby bylo možné sadu Runbook spustit prostřednictvím volání protokolu HTTP.
1. Po zobrazení výzvy ke spuštění Runbooku vyberte **Start** a potom na **Ano** . 
1. Otevře se podokno úlohy pro vytvořenou úlohu Runbooku. I když toto podokno můžete zavřít, nechte ho otevřený hned, abyste mohli sledovat průběh úlohy. Stav úlohy se zobrazí v **souhrnu úlohy**a možné stavy jsou popsané pro testování Runbooku.

   ![Souhrn úlohy](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Jakmile **se zobrazí stav**Runbooku, kliknutím na **výstup** otevřete stránku výstup, kde vidíte **Hello World** zobrazené.

   ![Výstup úlohy](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Zavřete výstupní stránku.
1. Klikněte na **Všechny protokoly** a otevřete podokno Datové proudy, které patří k úloze runbooku. Ve výstupním datovém proudu byste měli vidět jenom **Hello World** .

    Všimněte si, že podokno streamy může zobrazit další datové proudy pro úlohu Runbooku, jako jsou například podrobné a chybové streamy, pokud je do nich zapisuje sada Runbook.

   ![Všechny protokoly](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Zavřete podokno streamy a podokno úloh a vraťte se na stránku **MyFirstRunbook-PowerShell** .
1. V části **Podrobnosti**klikněte na **úlohy** a otevřete stránku úlohy pro tuto sadu Runbook. Tato stránka obsahuje seznam všech úloh, které sada Runbook vytvořila. Měla by se zobrazit jenom jedna úloha, protože jste úlohu spustili jenom jednou.

   ![Seznam úloh](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. Kliknutím na název úlohy otevřete stejné podokno úloh, které jste zobrazili při spuštění Runbooku. V tomto podokně si můžete zobrazit podrobnosti o všech úlohách vytvořených pro sadu Runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Krok 5 – přidání ověřování ke správě prostředků Azure

Runbook jste otestovali a publikovali, ale zatím nedělá nic užitečného. Chcete po něm, aby spravoval prostředky Azure. Aby to bylo možné, musí být sada Runbook ověřena pomocí účtu Spustit jako, který byl automaticky vytvořen při vytváření účtu Automation.

Jak je znázorněno v následujícím příkladu, připojení spustit jako je vytvořeno pomocí rutiny [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) . Pokud spravujete prostředky v rámci více předplatných, musíte použít parametr *AzContext* spolu s příkazem [Get-AzContext](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0).

> [!NOTE]
> Pro Runbooky PowerShellu jsou rutiny **Add-AzAccount** a **Add-AzureRMAccount** aliasy pro **Connect-AzAccount**. Tyto rutiny můžete použít nebo můžete [své moduly](automation-update-azure-modules.md) v účtu Automation aktualizovat na nejnovější verze. Vaše moduly možná budete muset aktualizovat i v případě, že jste právě vytvořili nový účet Automation.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```
1. Kliknutím na **Upravit** na stránce **MyFirstRunbook-PowerShell** otevřete textový editor.
1. Řádek **Write-Output** už nebudete potřebovat. Stačí přejít a odstranit.
1. Zadejte nebo zkopírujte a vložte následující kód, který zpracovává ověřování pomocí účtu Automation spustit jako.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

1. Klikněte na **testovací podokno** , abyste mohli Runbook otestovat.
1. Kliknutím na **Spustit** spustíte test. Po dokončení by se měl zobrazit výstup podobný následujícímu, který zobrazuje základní informace z vašeho účtu. Tento výstup potvrdí, že účet Spustit jako je platný.

   ![Ověření](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Krok 6 – přidání kódu pro spuštění virtuálního počítače

Teď, když se váš Runbook ověřuje pro vaše předplatné Azure, můžete spravovat prostředky. Pojďme přidat příkaz ke spuštění virtuálního počítače. Můžete si vybrat libovolný virtuální počítač v rámci vašeho předplatného Azure a teď ho jenom nekódujte pevně v Runbooku.

1. Do skriptu Runbooku přidejte rutinu [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) , která spustí virtuální počítač. Jak vidíte níže, rutina spustí virtuální počítač s názvem **VMName** a skupinou prostředků s názvem **ResourceGroupName**.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Uložte Runbook a pak klikněte na **testovací podokno** , abyste ho mohli otestovat.
1. Kliknutím na **Spustit spusťte** test. Až se dokončí, ujistěte se, že je virtuální počítač spuštěný.

## <a name="step-7---add-an-input-parameter"></a>Krok 7 – přidání vstupního parametru

Runbook aktuálně spouští virtuální počítač, který jste pevně zakódované v Runbooku. Sada Runbook bude užitečnější, pokud při spuštění sady Runbook zadáte virtuální počítač. Pojďme do Runbooku přidat vstupní parametry, které tuto funkci poskytují.

1. V textovém editoru upravte rutinu **Start-AzVM** pro použití proměnných pro parametry *VMName* a *ResourceGroupName*. 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Uložte runbook a otevřete testovací podokno. Teď můžete zadat hodnoty dvou vstupních proměnných, které v testu používáte.
1. Zavřete testovací podokno.
1. Kliknutím na **Publikovat** publikujte novou verzi runbooku.
1. Zastavte virtuální počítač, který jste spustili dříve.
1. Kliknutím na **Spustit** spustíte runbook. 
1. Zadejte hodnoty pro **VMName** a **RESOURCEGROUPNAME** pro virtuální počítač, který chcete spustit, a pak klikněte na **OK**.<br><br> ![Předání parametru](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. Po dokončení Runbooku se ujistěte, že je virtuální počítač spuštěný.

## <a name="next-steps"></a>Další kroky

* Další informace o PowerShellu, včetně referenčních modulů jazyka a výukových modulů, najdete v [dokumentaci k PowerShellu](/powershell/scripting/overview).
* Informace o tom, jak začít s grafickými Runbooky, najdete v tématu [můj první grafický Runbook](automation-first-runbook-graphical.md).
* První kroky s runbooky pracovních postupů prostředí PowerShell najdete v článku [Můj první runbook pracovního postupu prostředí PowerShell](automation-first-runbook-textual.md).
* Další informace o typech runbooků a jejich výhodách a omezeních najdete v tématu [Azure Automation typy runbooků](automation-runbook-types.md).
* Další informace o funkci podpory skriptů PowerShellu najdete v tématu [Podpora nativních skriptů PowerShellu v Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
