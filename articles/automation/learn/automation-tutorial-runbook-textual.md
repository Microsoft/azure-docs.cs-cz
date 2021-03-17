---
title: Vytvoření Runbooku pracovního postupu PowerShellu v Azure Automation
description: Tento článek vás seznámí s vytvořením, otestováním a publikováním jednoduchého Runbooku pracovního postupu PowerShellu.
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: e822e17f154b72854c5150e071301d7bbd53072e
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98890811"
---
# <a name="tutorial-create-a-powershell-workflow-runbook"></a>Kurz: vytvoření Runbooku pracovního postupu PowerShellu

V tomto kurzu se seznámíte s vytvořením [runbooku pracovního postupu PowerShellu](../automation-runbook-types.md#powershell-workflow-runbooks) ve službě Azure Automation. Runbooky pracovních postupů PowerShellu jsou textové Runbooky založené na pracovním postupu prostředí Windows PowerShell. Kód sady Runbook můžete vytvořit a upravit pomocí textového editoru v Azure Portal. 

> [!div class="checklist"]
> * Vytvoření jednoduchého Runbooku pracovního postupu PowerShellu
> * Testování a publikování Runbooku
> * Spuštění a sledování stavu úlohy Runbooku
> * Aktualizace Runbooku pro spuštění virtuálního počítače Azure s parametry Runbooku

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](../index.yml), abyste si mohli runbook podržet a mohli ověřovat prostředky Azure. Tento účet musí mít oprávnění ke spuštění a zastavení virtuálního počítače.
* Virtuální počítač Azure. Vzhledem k tomu, že tento počítač zastavíte a spustíte, neměli by se jednat o produkční virtuální počítač.

## <a name="step-1---create-new-runbook"></a>Krok 1 – vytvoření nového runbooku

Začněte vytvořením jednoduchého Runbooku, který vypíše text `Hello World` .

1. Na webu Azure Portal otevřete účet Automation.

   Stránka účtu Automation nabízí rychlý přehled prostředků v tomto účtu. Už byste tam měli mít nějaké prostředky. Většina těchto assetů je moduly, které jsou automaticky zahrnuté do nového účtu Automation. Měli byste mít také Asset přihlašovacích údajů přidružený k vašemu předplatnému.
 
2. V části **Automatizace procesu** vyberte **Runbooky** a otevřete seznam runbooků.

3. Kliknutím na **vytvořit Runbook** vytvořte nový Runbook.

4. Dejte runbooku název **MyFirstRunbook-Workflow**.

5. V tomto případě vytvoříte [Runbook pracovního postupu PowerShellu](../automation-runbook-types.md#powershell-workflow-runbooks). Jako **typ Runbooku** vyberte **pracovní postup PowerShellu** .

6. Kliknutím na **Vytvořit** vytvoříte runbook a otevřete textový editor.

## <a name="step-2---add-code-to-the-runbook"></a>Krok 2 – přidání kódu do runbooku

Můžete buď zadat kód přímo do Runbooku, nebo můžete vybrat rutiny, Runbooky a prostředky z ovládacího prvku knihovny a přidat je do Runbooku pomocí všech souvisejících parametrů. Pro tento kurz zadáte kód přímo do Runbooku.

1. Sada Runbook je aktuálně prázdná pouze s požadovaným `Workflow` klíčovým slovem, názvem sady Runbook a závorkami, které uzavřou celý pracovní postup.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

2. Zadejte `Write-Output "Hello World"` mezi složené závorky.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

3. Kliknutím na **Uložit** runbook uložte.

## <a name="step-3---test-the-runbook"></a>Krok 3 – otestování runbooku

Před publikováním Runbooku, aby byl dostupný v produkčním prostředí, byste ho měli otestovat, abyste se ujistili, že funguje správně. Testování Runbooku spustí svou verzi konceptu a umožňuje interaktivní zobrazení výstupu.

1. Vyberte **podokno test** a otevřete tak podokno test.

2. Kliknutím na tlačítko **Spustit** spusťte test s testováním jediné povolené možnosti.

3. Všimněte si, že se vytvoří [úloha Runbooku](../automation-runbook-execution.md) a její stav se zobrazí v podokně.

   Stav úlohy se spustí jako zařazený do fronty, což značí, že úloha čeká na zpřístupnění pracovního procesu Runbooku v cloudu. Stav se změní na zahájeno, když pracovní proces tuto úlohu vystaví. Nakonec se stav spustí, když se Runbook skutečně začne spouštět.

4. Po dokončení úlohy Runbooku zobrazí podokno test svůj výstup. V takovém případě se zobrazí `Hello World` .

   ![Hello World](../media/automation-tutorial-runbook-textual/test-output-hello-world.png)

5. Zavřete testovací podokno a vraťte se na plátno.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4 – publikování a spuštění runbooku

Sada Runbook, kterou jste vytvořili, je stále v režimu konceptu. Abyste ho mohli spustit v produkčním prostředí, musíte ho publikovat. Když runbook publikujete, přepíšete vydanou verzi verzí v režimu konceptu. V tomto případě zatím ještě publikovanou verzi nemáte, protože jste runbook teprve vytvořili.

1. Kliknutím na **Publikovat** runbook publikujte a po zobrazení výzvy klikněte na **Ano**.

2. Posunutím doleva zobrazíte sadu Runbook na stránce sady **Runbook** a Všimněte si, že pole **stav vytváření** je nastaveno na **Publikováno**.

3. Posuňte se zpět doprava, abyste zobrazili stránku pro **MyFirstRunbook-Workflow**.

   Možnosti v horní části umožňují spuštění sady Runbook nyní, naplánování budoucího času spuštění nebo vytvoření [Webhooku](../automation-webhooks.md) , aby bylo možné sadu Runbook spustit prostřednictvím volání protokolu HTTP.

4. Po zobrazení výzvy ke spuštění Runbooku vyberte **Start** a potom na **Ano** .

   ![Spuštění runbooku](../media/automation-tutorial-runbook-textual/automation-runbook-controls-start.png)

5. Otevře se podokno úlohy pro vytvořenou úlohu Runbooku. V takovém případě ponechte podokno otevřené, abyste mohli sledovat průběh úlohy.

6. Všimněte si, že stav úlohy se zobrazuje v **souhrnu úlohy**. Tento stav odpovídá stavům, které jste viděli při testování Runbooku.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-jobsummary.png" alt-text="Snímek obrazovky s podoknem úloh sady Runbook zobrazující zvýrazněný oddíl souhrnu úlohy":::

7. Jakmile se stav Runbooku zobrazí dokončeno, klikněte na **výstup**. Otevře se stránka výstup, kde můžete zobrazit vaši `Hello World` zprávu.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-outputtile.png" alt-text="Snímek obrazovky s zvýrazněným tlačítkem výstup na panelu úloh sady Runbook":::

8. Zavřete výstupní stránku.

9. Klikněte na **Všechny protokoly** a otevřete podokno Datové proudy, které patří k úloze runbooku. Měla by se zobrazit jenom `Hello World` ve výstupním datovém proudu. Všimněte si, že podokno streamy může zobrazit další datové proudy pro úlohu Runbooku, jako jsou například podrobné a chybové streamy, pokud je do nich zapisuje sada Runbook.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-alllogstile.png" alt-text="Snímek obrazovky s panelem úloh sady Runbook zobrazující zvýrazněné tlačítko všechny protokoly":::

10. Zavřete podokno streamy a podokno úloh a vraťte se na stránku MyFirstRunbook.

11. Kliknutím na **úlohy** v části **prostředky** otevřete stránku úlohy pro tuto sadu Runbook. Tato stránka obsahuje seznam všech úloh, které sada Runbook vytvořila. V seznamu byste měli vidět jenom jednu úlohu, protože jste úlohu spustili jenom jednou.

   ![Úlohy](../media/automation-tutorial-runbook-textual/runbook-control-job-tile.png)

12. Kliknutím na název úlohy otevřete stejné podokno úloh, které jste zobrazili při spuštění Runbooku. V tomto podokně si můžete zobrazit podrobnosti o všech úlohách vytvořených pro sadu Runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Krok 5 – přidání ověřování ke správě prostředků Azure

Runbook jste otestovali a publikovali, ale zatím nedělá nic užitečného. Chcete po něm, aby spravoval prostředky Azure. Nemůže to dělat, pokud se neověřuje pomocí přihlašovacích údajů k předplatnému. Ověřování používá rutinu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

>[!NOTE]
>Pro PowerShellové Runbooky `Add-AzAccount` a `Add-AzureRMAccount` jsou aliasy pro `Connect-AzAccount` . Tyto rutiny můžete použít nebo můžete [své moduly](../automation-update-azure-modules.md) v účtu Automation aktualizovat na nejnovější verze. Vaše moduly možná budete muset aktualizovat i v případě, že jste právě vytvořili nový účet Automation.

1. Přejděte na stránku MyFirstRunbook-Workflow a otevřete textový Editor kliknutím na **Upravit**.

2. Odstraňte `Write-Output` řádek.

3. Umístěte kurzor na prázdný řádek mezi složenými závorkami.

4. Zadejte nebo zkopírujte a vložte následující kód, který zpracovává ověřování pomocí účtu Automation spustit jako.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

5. Klikněte na **testovací podokno** , abyste mohli Runbook otestovat.

6. Kliknutím na **Spustit** spustíte test. Po dokončení by se měl zobrazit výstup podobný následujícímu, který zobrazuje základní informace z vašeho účtu. Tato akce potvrdí, že přihlašovací údaje jsou platné.

   ![Ověření](../media/automation-tutorial-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Krok 6 – přidání kódu pro spuštění virtuálního počítače

Teď, když se Runbook ověřuje pro předplatné Azure, můžete spravovat prostředky. Pojďme přidat příkaz ke spuštění virtuálního počítače. V rámci vašeho předplatného Azure si můžete vybrat libovolný virtuální počítač. teď už jste zakódujeme tento název v Runbooku. Pokud spravujete prostředky v rámci více předplatných, musíte použít `AzContext` parametr s rutinou [Get-AzContext](/powershell/module/az.accounts/get-azcontext) .

1. Zadejte název a název skupiny prostředků pro spuštění virtuálního počítače zadáním volání rutiny [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) , jak je znázorněno níže. 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

2. Uložte Runbook a pak klikněte na **testovací podokno** , abyste ho mohli otestovat.

3. Kliknutím na **Spustit** spustíte test. Po dokončení ověřte, že je virtuální počítač spuštěný.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Krok 7 – přidání vstupního parametru do runbooku

Runbook aktuálně spouští virtuální počítač, který jste pevně zakódované v Runbooku. Bude užitečnější, pokud při spuštění Runbooku budete moct zadat virtuální počítač. Pojďme do Runbooku přidat vstupní parametry, které tuto funkci poskytují.

1. Přidejte proměnné pro `VMName` parametry a `ResourceGroupName` do sady Runbook a použijte proměnné s `Start-AzVM` rutinou, jak je znázorněno níže.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Uložte runbook a otevřete testovací podokno. Nyní můžete zadat hodnoty dvou vstupních proměnných, které jsou v testu.

3. Zavřete testovací podokno.

4. Kliknutím na **Publikovat** publikujte novou verzi runbooku.

5. Zastavte virtuální počítač, který jste spustili.

6. Kliknutím na **Spustit** spustíte runbook. 

7. Zadejte hodnoty pro **VMName** a **RESOURCEGROUPNAME** pro virtuální počítač, který se chystáte spustit.

   ![Spuštění runbooku](../media/automation-tutorial-runbook-textual/automation-pass-params.png)

8. Po dokončení sady Runbook ověřte, zda byl virtuální počítač spuštěn.

## <a name="next-steps"></a>Další kroky

* Další informace o PowerShellu, včetně referenčních modulů jazyka a výukových modulů, najdete v [dokumentaci k PowerShellu](/powershell/scripting/overview).
* Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](/powershell/module/az.automation).
* Chcete-li začít s grafickými Runbooky, přečtěte si téma [Vytvoření grafického Runbooku](automation-tutorial-runbook-graphical.md).
* Pokud chcete začít pracovat s Runbooky PowerShellu, přečtěte si téma [Vytvoření Runbooku PowerShellu](automation-tutorial-runbook-textual-powershell.md).
* Další informace o typech runbooků a jejich výhodách a omezeních najdete v tématu [Azure Automation typy runbooků](../automation-runbook-types.md).
* Další informace o funkcích podpory skriptů PowerShellu najdete [v tématu Podpora nativních skriptů PowerShellu v Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
