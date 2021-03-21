---
title: Vytvoření Runbooku PowerShellu v Azure Automation
description: Tento článek vás seznámí s vytvořením, otestováním a publikováním jednoduchého Runbooku PowerShellu.
keywords: azure powershell, kurz k powershellovému scriptu, automatizace powershellu
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: 896b4db433164471f41aa09791ede5d677028bfb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896609"
---
# <a name="tutorial-create-a-powershell-runbook"></a>Kurz: vytvoření Runbooku PowerShellu

V tomto kurzu se seznámíte s vytvořením [powershellového runbooku](../automation-runbook-types.md#powershell-runbooks) ve službě Azure Automation. Runbooky PowerShellu jsou založené na Windows PowerShellu. Můžete přímo upravit kód sady Runbook pomocí textového editoru v Azure Portal.

> [!div class="checklist"]
> * Vytvoření jednoduchého Runbooku PowerShellu
> * Testování a publikování Runbooku
> * Spuštění a sledování stavu úlohy Runbooku
> * Aktualizace Runbooku pro spuštění virtuálního počítače Azure s parametry Runbooku

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](../automation-quickstart-create-account.md), abyste si mohli runbook podržet a mohli ověřovat prostředky Azure. Tento účet musí mít oprávnění ke spuštění a zastavení virtuálního počítače.
* Virtuální počítač Azure. Vzhledem k tomu, že tento počítač zastavíte a spustíte, neměli by se jednat o produkční virtuální počítač.
* V případě potřeby [importujte moduly Azure](../shared-resources/modules.md) nebo [moduly pro aktualizaci](../automation-update-azure-modules.md) na základě používaných rutin.

## <a name="differences-from-powershell-workflow-runbooks"></a>Rozdíly mezi Runbooky pracovních postupů PowerShellu

Runbooky PowerShellu mají stejný životní cyklus, možnosti a správu jako Runbooky pracovních postupů PowerShellu. Existují však určité rozdíly a omezení.

| Charakteristika  | PowerShellové Runbooky | Runbooky pracovních postupů PowerShellu |
| ------ | ----- | ----- |
| Rychlost | Spusťte rychle, protože nepoužívají krok kompilace. | Pozvolna běžet. |
| Kontrolní body | Nepodporují kontrolní body. PowerShellový Runbook může obnovit pouze operaci od začátku. | Používejte kontrolní body, které umožní sešitu obnovení operace z libovolného bodu. |
| Spuštění příkazu | Podporuje pouze sériová spuštění. | Podpora sériového i paralelního spuštění.|
| Prostředí | Jediné prostředí runspace spouští vše ve skriptu. | Samostatné prostředí runspace lze použít pro aktivitu, příkaz nebo blok skriptu. |

Kromě těchto rozdílů mají Runbooky PowerShellu několik [syntaktických rozdílů](/previous-versions/technet-magazine/dn151046(v=msdn.10)) od runbooků pracovního postupu PowerShellu.

## <a name="step-1---create-runbook"></a>Krok 1 – vytvoření runbooku

Začněte vytvořením jednoduchého Runbooku, který vypíše text `Hello World` .

1. Na webu Azure Portal otevřete účet Automation.

2. V části **Automatizace procesu** vyberte **Runbooky** a otevřete seznam runbooků.

3. Kliknutím na **vytvořit Runbook** vytvořte nový Runbook.

4. Dejte runbooku název **MyFirstRunbook-PowerShell**.

5. V tomto případě vytvoříte [powershellový Runbook](../automation-runbook-types.md#powershell-runbooks). Jako **typ Runbooku** vyberte **PowerShell** .

6. Kliknutím na **Vytvořit** vytvoříte runbook a otevřete textový editor.

## <a name="step-2---add-code-to-the-runbook"></a>Krok 2 – přidání kódu do runbooku

Kód můžete buď zadat přímo do runbooku, nebo můžete vybrat rutiny, runbooky a assety z ovládacího prvku Knihovna a přidat je do runbooku spolu se všemi souvisejícími parametry. Pro tento kurz budete psát kód přímo do Runbooku.

1. Vaše sada Runbook je aktuálně prázdná. Zadejte `Write-Output "Hello World"` text do textového pole skriptu.

   ![Hello World](../media/automation-tutorial-runbook-textual-powershell/automation-helloworld.png)

2. Kliknutím na **Uložit** runbook uložte.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"></a> Krok 3 – otestování Runbooku

Před publikováním Runbooku, aby byl dostupný v produkčním prostředí, byste ho měli otestovat, abyste se ujistili, že funguje správně. Testování Runbooku spustí svou verzi konceptu a umožňuje interaktivní zobrazení výstupu.

1. Kliknutím na **Testovací podokno** otevřete testovací podokno.

2. Kliknutím na **Spustit** spustíte test. Měla by to být jediná povolená možnost.

3. Všimněte si, že se vytvoří [úloha Runbooku](../automation-runbook-execution.md) a její stav se zobrazí v podokně.

   Stav úlohy se spustí jako zařazený do fronty, což značí, že úloha čeká na zpřístupnění pracovního procesu Runbooku v cloudu. Stav se změní na zahájeno, když pracovní proces tuto úlohu vystaví. Nakonec se stav spustí, když se Runbook skutečně začne spouštět.

4. Po dokončení úlohy Runbooku zobrazí podokno test svůj výstup. V takovém případě se zobrazí `Hello World` .

   ![Výstup testovacího podokna](../media/automation-tutorial-runbook-textual-powershell/automation-testpane-output.png)

5. Zavřete testovací podokno a vraťte se na plátno.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4 – publikování a spuštění runbooku

Sada Runbook, kterou jste vytvořili, je stále v režimu konceptu. Abyste ho mohli spustit v produkčním prostředí, je potřeba ho publikovat. Když runbook publikujete, přepíšete vydanou verzi verzí v režimu konceptu. V tomto případě zatím ještě publikovanou verzi nemáte, protože jste runbook teprve vytvořili.

1. Kliknutím na **Publikovat** runbook publikujte a po zobrazení výzvy klikněte na **Ano**.

2. Posunutím doleva zobrazíte sadu Runbook na stránce sady Runbook a Všimněte si, že hodnota **stav vytváření** je nastavena na **Publikováno**.

3. Posuňte se zpět doprava, abyste si zobrazili stránku **MyFirstRunbook-PowerShell**.
   
   Možnosti v horní části umožňují spuštění sady Runbook nyní, naplánování budoucího času spuštění nebo vytvoření [Webhooku](../automation-webhooks.md) , aby bylo možné sadu Runbook spustit prostřednictvím volání protokolu HTTP.

4. Po zobrazení výzvy ke spuštění Runbooku vyberte **Start** a potom na **Ano** . 

5. Otevře se podokno úlohy pro vytvořenou úlohu Runbooku. I když toto podokno můžete zavřít, nechte ho otevřený hned, abyste mohli sledovat průběh úlohy. Stav úlohy se zobrazí v **souhrnu úlohy** a možné stavy jsou popsané pro testování Runbooku.

   ![Souhrn úlohy](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

6. Po zobrazení stavu Runbooku kliknutím na **výstup** otevřete stránku výstup, kde vidíte `Hello World` zobrazené.

   ![Výstup úlohy](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

7. Zavřete výstupní stránku.

8. Klikněte na **Všechny protokoly** a otevřete podokno Datové proudy, které patří k úloze runbooku. Měla by se zobrazit jenom `Hello World` ve výstupním datovém proudu.

    Všimněte si, že podokno streamy může zobrazit další datové proudy pro úlohu Runbooku, jako jsou například podrobné a chybové streamy, pokud je do nich zapisuje sada Runbook.

   ![Všechny protokoly](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

9. Zavřete podokno datové proudy a podokno úlohy, které se vrátí na stránku MyFirstRunbook-PowerShell.

10. V části **Podrobnosti** klikněte na **úlohy** a otevřete stránku úlohy pro tuto sadu Runbook. Tato stránka obsahuje seznam všech úloh, které sada Runbook vytvořila. Měla by se zobrazit jenom jedna úloha, protože jste úlohu spustili jenom jednou.

   ![Seznam úloh](../media/automation-tutorial-runbook-textual-powershell/runbook-control-job-tile.png)

11. Kliknutím na název úlohy otevřete stejné podokno úloh, které jste zobrazili při spuštění Runbooku. V tomto podokně si můžete zobrazit podrobnosti o všech úlohách vytvořených pro sadu Runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Krok 5 – přidání ověřování ke správě prostředků Azure

Runbook jste otestovali a publikovali, ale zatím nedělá nic užitečného. Chcete po něm, aby spravoval prostředky Azure. Aby to bylo možné, musí být sada Runbook ověřena pomocí účtu Spustit jako, který byl automaticky vytvořen při vytváření účtu Automation.

Jak je znázorněno v následujícím příkladu, připojení spustit jako je vytvořeno pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Pokud spravujete prostředky v rámci více předplatných, musíte použít `AzContext` parametr s příkazem [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext).

> [!NOTE]
> Pro PowerShellové Runbooky `Add-AzAccount` a `Add-AzureRMAccount` jsou aliasy pro `Connect-AzAccount` . Tyto rutiny můžete použít nebo můžete [své moduly](../automation-update-azure-modules.md) v účtu Automation aktualizovat na nejnovější verze. Vaše moduly možná budete muset aktualizovat i v případě, že jste právě vytvořili nový účet Automation.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```

1. Kliknutím na tlačítko **Upravit** na stránce MyFirstRunbook-PowerShell otevřete textový editor.

2. Řádek už nepotřebujete `Write-Output` . Stačí přejít a odstranit.

3. Zadejte nebo zkopírujte a vložte následující kód, který zpracovává ověřování pomocí účtu Automation spustit jako.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

4. Klikněte na **testovací podokno** , abyste mohli Runbook otestovat.

5. Kliknutím na **Spustit** spustíte test. Po dokončení by se měl zobrazit výstup podobný následujícímu, který zobrazuje základní informace z vašeho účtu. Tento výstup potvrdí, že účet Spustit jako je platný.

   ![Ověření](../media/automation-tutorial-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Krok 6 – přidání kódu pro spuštění virtuálního počítače

Teď, když se váš Runbook ověřuje pro vaše předplatné Azure, můžete spravovat prostředky. Pojďme přidat příkaz ke spuštění virtuálního počítače. V rámci vašeho předplatného Azure si můžete vybrat libovolný virtuální počítač a v sadě Runbook teď jenom pevně zakódovat název.

1. Do skriptu Runbooku přidejte rutinu [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) , která spustí virtuální počítač. Jak vidíte níže, rutina spustí virtuální počítač s názvem `VMName` a skupinou prostředků s názvem `ResourceGroupName` .

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

2. Uložte Runbook a pak klikněte na **testovací podokno** , abyste ho mohli otestovat.

3. Kliknutím na **Spustit spusťte** test. Až se dokončí, ujistěte se, že je virtuální počítač spuštěný.

## <a name="step-7---add-an-input-parameter"></a>Krok 7 – přidání vstupního parametru

Sada Runbook aktuálně spouští virtuální počítač, který je pevně zakódovaný v sadě Runbook. Sada Runbook je užitečnější, pokud při spuštění sady Runbook zadáte virtuální počítač. Pojďme do Runbooku přidat vstupní parametry, které tuto funkci poskytují.

1. V textovém editoru změňte `Start-AzVM` rutinu na použití proměnných pro parametry `VMName` a `ResourceGroupName` . 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

2. Uložte runbook a otevřete testovací podokno. Teď můžete zadat hodnoty dvou vstupních proměnných, které v testu používáte.

3. Zavřete testovací podokno.

4. Kliknutím na **Publikovat** publikujte novou verzi runbooku.

5. Zastavte virtuální počítač, který jste spustili dříve.

6. Kliknutím na **Spustit** spustíte runbook. 

7. Zadejte hodnoty pro **VMName** a **RESOURCEGROUPNAME** pro virtuální počítač, který chcete spustit, a pak klikněte na **OK**.

    ![Předání parametru](../media/automation-tutorial-runbook-textual-powershell/automation-pass-params.png)

8. Po dokončení Runbooku se ujistěte, že je virtuální počítač spuštěný.

## <a name="next-steps"></a>Další kroky

* Další informace o PowerShellu, včetně referenčních modulů jazyka a výukových modulů, najdete v [dokumentaci k PowerShellu](/powershell/scripting/overview).
* Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](/powershell/module/az.automation).
* Chcete-li začít s grafickými Runbooky, přečtěte si téma [Vytvoření grafického Runbooku](automation-tutorial-runbook-graphical.md).
* Informace o tom, jak začít s Runbooky pracovních postupů PowerShellu, najdete v tématu [Vytvoření Runbooku pracovního postupu PowerShellu](automation-tutorial-runbook-textual.md).
* Další informace o typech runbooků a jejich výhodách a omezeních najdete v tématu [Azure Automation typy runbooků](../automation-runbook-types.md).
* Další informace o funkci podpory skriptů PowerShellu najdete v tématu [Podpora nativních skriptů PowerShellu v Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
