---
title: Můj první PowerShellový Runbook v Azure Automation
description: Kurz vás provede vytvořením, otestováním a publikováním jednoduchého powershellového runbooku.
keywords: azure powershell, kurz k powershellovému scriptu, automatizace powershellu
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 11/27/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9718185b41795da6d95486972441ee20bc250316
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68850690"
---
# <a name="my-first-powershell-runbook"></a>Můj první powershellový runbook

> [!div class="op_single_selector"]
> * [Grafický](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Pracovní postup PowerShellu](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

V tomto kurzu se seznámíte s vytvořením [powershellového runbooku](automation-runbook-types.md#powershell-runbooks) ve službě Azure Automation. Začnete s jednoduchým runbookm, který otestujete a publikujete, a zjistíte, jak sledovat stav úlohy Runbooku. Potom runbook upravíte, aby skutečně spravoval prostředky Azure, v tomto případě virtuální počítač Azure. Nakonec uděláte robustnější sadu Runbook přidáním parametrů Runbooku.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](automation-quickstart-create-account.md), abyste si mohli runbook podržet a mohli ověřovat prostředky Azure. Tento účet musí mít oprávnění ke spuštění a zastavení virtuálního počítače.
* Virtuální počítač Azure. Tento počítač zastavíte a spustíte, takže by neměl být produkční virtuální počítač.
* Možná budete muset [aktualizovat moduly Azure](automation-update-azure-modules.md) na základě používaných rutin.

## <a name="create-new-runbook"></a>Vytvořit nový Runbook

Začnete vytvořením jednoduchého Runbooku, který vypíše text *Hello World*.

1. Na webu Azure Portal otevřete účet Automation.
2. Kliknutím na **Runbooky** v části **Automatizace procesu** otevřete seznam runbooků.
3. Kliknutím na tlačítko **+ Přidat Runbook** a potom na **vytvořit nový**Runbook vytvořte nový Runbook.
4. Dejte runbooku název *MyFirstRunbook-PowerShell*.
5. V tomto případě vytvoříte [powershellový Runbook](automation-runbook-types.md#powershell-runbooks) , takže vyberte **PowerShell** pro **typ Runbooku**.
6. Kliknutím na **Vytvořit** vytvoříte runbook a otevřete textový editor.

## <a name="add-code-to-the-runbook"></a>Přidat kód do Runbooku

Kód můžete buď zadat přímo do runbooku, nebo můžete vybrat rutiny, runbooky a assety z ovládacího prvku Knihovna a přidat je do runbooku spolu se všemi souvisejícími parametry. Pro tento návod zadáte přímo v Runbooku.

1. Vaše sada Runbook je aktuálně prázdná, zadejte *Write-Output "Hello World".* v těle skriptu.

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. Kliknutím na **Uložit** runbook uložte.

## <a name="step-3---test-the-runbook"></a> Otestování Runbooku

Před publikováním runbooku, které ho zpřístupní v produkčním prostředí, byste měli runbook otestovat a ujistit se, že funguje správně. Když runbook testujete, spustíte jeho  verzi **Koncept** a interaktivně zobrazíte jeho výsledek.

1. Kliknutím na **Testovací podokno** otevřete testovací podokno.
2. Kliknutím na **Spustit** spustíte test. Měla by to být jediná povolená možnost.
3. Vytvoří se [úloha runbooku](automation-runbook-execution.md) a její stav se zobrazí.

   Počáteční stav úlohy bude ve *frontě* . to označuje, že čekáte na zpřístupnění pracovního procesu Runbooku v cloudu. Přesune se ke *spuštění* , když pracovní proces tuto úlohu vyvolá a pak se spustí, když se Runbook skutečně spustí.

4. Po dokončení úlohy runbooku se zobrazí jeho výstup. V takovém případě byste měli vidět *Hello World*.

   ![Výstup testovacího podokna](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. Zavřete testovací podokno a vraťte se na plátno.

## <a name="publish-and-start-the-runbook"></a>Publikování a spuštění Runbooku

Vytvořený runbook je stále v režimu konceptu. Aby ho bylo možné spustit v produkčním prostředí, je nutné ho publikovat.  Když runbook publikujete, přepíšete vydanou verzi verzí v režimu konceptu. V případě, že jste sadu Runbook právě vytvořili, ještě nemáte publikovanou verzi.

1. Kliknutím na **Publikovat** runbook publikujte a po zobrazení výzvy klikněte na **Ano**.
1. Pokud se posunete doleva, abyste sadu Runbook zobrazili v podokně sady **Runbook** nyní, zobrazí se **stav vytváření obsahu** **Publikováno**.
1. Posuňte se zpět doprava, abyste viděli podokno **MyFirstRunbook-PowerShell**.
   Možnosti v horní části nám umožňují spuštění runbooku, zobrazení runbooku, naplánování jeho spuštění někdy v budoucnu nebo vytvoření [webhooku](automation-webhooks.md), který umožní spuštění prostřednictvím volání protokolu HTTP.
1. Chcete spustit sadu Runbook, klikněte na tlačítko **Start** a po otevření stránky spustit Runbook klikněte na tlačítko **OK** .
1. Pro vytvořenou úlohu Runbooku se otevře stránka úlohy. Toto podokno můžete zavřít, ale v tomto případě ho necháte otevřené, abyste mohli sledovat průběh úlohy.
1. Stav úlohy se zobrazí v **souhrnu úlohy** a odpovídá stavům, které jste viděli při testování Runbooku.

   ![Souhrn úlohy](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Jakmile se stav Runbooku zobrazí *dokončeno*, v části **Přehled** klikněte na možnost **výstup**. Otevře se podokno výstup a uvidíte své *Hello World*.

   ![Výstup úlohy](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Zavřete výstupní stránku.
1. Klikněte na **Všechny protokoly** a otevřete podokno Datové proudy, které patří k úloze runbooku. Měla by se zobrazit pouze *Hello World* ve výstupním datovém proudu, ale tento výstup může zobrazit další datové proudy pro úlohu Runbooku, jako je například verbose a chyba, pokud je sada Runbook zapisuje do nich.

   ![Všechny protokoly](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Zavřením stránky streamy a stránky úlohy se vraťte na stránku MyFirstRunbook-PowerShell.
1. V části **Podrobnosti**klikněte na **úlohy** a otevřete podokno úlohy pro tuto sadu Runbook. Tato stránka obsahuje seznam všech úloh, které tento Runbook vytvořil. Ve výpisu by se měla zobrazit pouze jedna úloha, protože jste ji spustili jenom jednou.

   ![Seznam úloh](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. Na tuto úlohu můžete kliknout a otevřít podokno Úloha, které jste zobrazili při spuštění runbooku. Tato akce vám umožní vrátit se zpět v čase a zobrazit podrobnosti o každé úloze vytvořené pro konkrétní Runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Přidání ověřování pro správu prostředků Azure

Runbook jste otestovali a publikovali, ale zatím nedělá nic užitečného. Chcete po něm, aby spravoval prostředky Azure. To ale není to možné, pokud je neověřujete pomocí připojení spustit jako, které se automaticky vytvoří při vytváření účtu Automation. Použijete připojení spustit jako s rutinou **Connect-AzureRmAccount** . Pokud spravujete prostředky v rámci více předplatných, musíte použít parametr **-AzureRmContext** spolu s příkazem [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext).

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $AzureContext
   ```

1. Kliknutím na **Upravit** na stránce MyFirstRunbook-PowerShell otevřete textový editor.
1. Řádek **Write-Output** již nepotřebujete, a proto jej odstraňte.
1. Zadejte nebo zkopírujte a vložte následující kód, který bude zpracovávat ověřování pomocí vašeho účtu Automation Spustit jako:

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** a **Login-AzureRmAccount** jsou nyní aliasy pro **Connect-AzureRmAccount**. Pokud rutina **Connect-AzureRMAccount** neexistuje, můžete použít rutinu **Add-AzureRMAccount** nebo **Login-AzureRMAccount**, případně můžete aktualizovat moduly v účtu Automation na nejnovější verze.

1. Klikněte na **testovací podokno** , abyste mohli Runbook otestovat.
1. Kliknutím na **Spustit** spustíte test. Po dokončení byste měli obdržet výstup podobný následujícímu se základními informacemi z vašeho účtu. Tento výstup potvrdí, že účet Spustit jako je platný.

   ![Ověřit](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="add-code-to-start-a-virtual-machine"></a>Přidat kód pro spuštění virtuálního počítače

Teď, když se váš Runbook ověřuje pro vaše předplatné Azure, můžete spravovat prostředky. Přidáte příkaz ke spuštění virtuálního počítače. V rámci vašeho předplatného Azure si můžete vybrat libovolný virtuální počítač a teď nekódujte pevně tento název v Runbooku.

1. Po *připojení-AzureRmAccount*zadejte *Start-AzureRmVM-name ' VMName '-ResourceGroupName ' NameofResourceGroup '* , který poskytuje název a název skupiny prostředků virtuálního počítače, který se má spustit.

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Uložte Runbook a pak klikněte na **testovací podokno** , abyste ho mohli otestovat.
1. Kliknutím na **Spustit** spustíte test. Po jeho dokončení zkontrolujte, jestli se virtuální počítač spustil.

## <a name="add-an-input-parameter"></a>Přidat vstupní parametr

Sada Runbook aktuálně spouští virtuální počítač, který jste pevně zakódované v sadě Runbook, ale byla užitečnější, pokud při spuštění sady Runbook zadáte virtuální počítač. Do Runbooku přidáte vstupní parametry, které tuto funkci poskytují.

1. Do sady Runbook přidejte parametry pro *VMName* a *ResourceGroupName* a použijte tyto proměnné s rutinou **Start-AzureRmVM** , jak je uvedeno v následujícím příkladu.

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Uložte runbook a otevřete testovací podokno. Teď můžete zadat hodnoty dvou vstupních proměnných, které budete v testu používat.
1. Zavřete testovací podokno.
1. Kliknutím na **Publikovat** publikujte novou verzi runbooku.
1. Zastavte virtuální počítač, který jste v předchozím kroku spustili.
1. Spusťte sadu Runbook kliknutím na tlačítko **OK** . Zadejte **VMName** a **ResourceGroupName** pro virtuální počítač, který chcete spustit.<br><br> ![Předání parametru](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. Po dokončení runbooku zkontrolujte, jestli se virtuální počítač spustil.

## <a name="differences-from-powershell-workflow"></a>Odlišnosti od runbooků pracovních postupů PowerShellu

Powershellové runbooky mají stejný životní cyklus, možnosti a správu jako runbooky pracovních postupů PowerShellu, ale existují tady určité rozdíly a omezení:

1. Powershellové runbooky běží rychleji než runbooky pracovních postupů PowerShellu, protože neobsahují kompilační krok.
2. Runbooky pracovních postupů PowerShellu podporují kontrolní body, které používají kontrolní body, Runbooky PowerShellu pro pracovní postupy mohou pokračovat z libovolného bodu v sadě Runbook. Runbooky PowerShellu můžou pokračovat jenom od začátku.
3. Runbooky pracovních postupů PowerShellu podporují paralelní a sériové spouštění. Runbooky PowerShellu můžou příkazy spouštět jenom v sériovém tvaru.
4. V Runbooku pracovního postupu PowerShellu může mít aktivita, příkaz nebo blok skriptu vlastní prostředí runspace. V Runbooku PowerShellu se všechno ve skriptu spouští v jediném prostředí runspace. Existují také určité [syntaktické rozdíly](https://technet.microsoft.com/magazine/dn151046.aspx) mezi nativním powershellovým runbookem a runbookem pracovního postupu PowerShellu.

## <a name="next-steps"></a>Další kroky

* Další informace o PowerShellu, včetně referenčních modulů jazyka a výukových modulů, najdete v [dokumentaci](/powershell/scripting/overview)k PowerShellu.
* První kroky s grafickými runbooky najdete v článku [Můj první grafický runbook](automation-first-runbook-graphical.md).
* První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md).
* Další informace o typech runbooků, jejich výhodách a omezeních najdete v článku [Typy runbooků ve službě Azure Automation](automation-runbook-types.md).
* Další informace o funkci podpory powershellových skriptů najdete v článku [Nativní podpora powershellových skriptů ve službě Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
