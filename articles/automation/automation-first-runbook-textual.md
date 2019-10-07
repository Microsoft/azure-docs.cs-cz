---
title: Můj první Runbook pracovního postupu PowerShellu v Azure Automation
description: Kurz vás provede vytvořením, otestováním a publikováním jednoduchého textového Runbooku pomocí pracovního postupu PowerShellu.
keywords: powershellový pracovní postup, příklady pracovního postupu PowerShellu, Workflow PowerShellu
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 09/24/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ab6d213e83c2d7eba95c6c9a6dca5edc1f0f2215
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996518"
---
# <a name="my-first-powershell-workflow-runbook"></a>Můj první Runbook pracovního postupu PowerShellu

> [!div class="op_single_selector"]
> * [Graphic](automation-first-runbook-graphical.md)
> * [Prostředí](automation-first-runbook-textual-powershell.md)
> * [Pracovní postup PowerShellu](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Tento kurz vás provede vytvořením [Runbooku pracovního postupu PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks) v Azure Automation. Začnete s jednoduchým runbookm, který otestujete a publikujete, a zároveň vysvětlujete, jak sledovat stav úlohy Runbooku. Pak můžete Runbook upravit tak, aby ve skutečnosti spravoval prostředky Azure, v tomto případě spustíte virtuální počítač Azure. Nakonec můžete sadu Runbook zvýšit robustní přidáním parametrů Runbooku.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující:

* Předplatné Azure. Pokud ho ještě nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet služby Automation](automation-offering-get-started.md) , který bude obsahovat Runbook a provede ověření v prostředcích Azure.  Tento účet musí mít oprávnění ke spouštění a zastavování virtuálního počítače.
* Virtuální počítač Azure. Tento počítač zastavíte a spustíte, takže by neměl být produkční virtuální počítač.

## <a name="step-1---create-new-runbook"></a>Krok 1 – Vytvoření nové sady Runbook

Začnete vytvořením jednoduchého Runbooku, který vypíše text *Hello World*.

1. V Azure Portal otevřete svůj účet Automation.

   Stránka účet Automation poskytuje rychlé zobrazení prostředků v tomto účtu. Už byste měli mít nějaké assety. Většina těchto prostředků je moduly, které jsou automaticky zahrnuté do nového účtu Automation. Měli byste také mít Asset přihlašovacích údajů, který je uvedený v [požadavcích](#prerequisites).

1. Kliknutím na **Runbooky** v části **Automatizace procesu** otevřete seznam runbooků.
1. Kliknutím na tlačítko **+ Přidat Runbook** a potom na **vytvořit nový**Runbook vytvořte nový Runbook.
1. Dejte Runbooku název *MyFirstRunbook-Workflow*.
1. V tomto případě vytvoříte [Runbook pracovního postupu PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks) tak, že pro **typ Runbooku**vyberete **powershellový pracovní postup** .
1. Kliknutím na **vytvořit** vytvořte Runbook a otevřete textový editor.

## <a name="step-2---add-code-to-the-runbook"></a>Krok 2 – Přidání kódu do Runbooku

Můžete buď zadat kód přímo do Runbooku, nebo můžete vybrat rutiny, Runbooky a prostředky z řízení knihovny a přidat je do Runbooku s případnými souvisejícími parametry. Pro tento návod zadáte přímo do Runbooku.

1. Sada Runbook je aktuálně prázdná pouze pomocí klíčového slova vyžadované *pracovní postup* , název Runbooku a složené závorky, které encases celý pracovní postup.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Typ *Write-Output Hello World.* mezi složenými závorkami.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Uložte Runbook kliknutím na **Uložit**.

## <a name="step-3---test-the-runbook"></a>Krok 3 – otestování Runbooku

Před publikováním Runbooku, aby byl dostupný v produkčním prostředí, ho budete chtít otestovat a ujistit se, že funguje správně. Při testování Runbooku se spustí jeho verze **konceptu** a interaktivně se zobrazí jeho výstup.

1. Kliknutím na **testovací podokno** otevřete podokno test.
1. Kliknutím na **Spustit** spusťte test. Tato možnost by měla být jediná povolená možnost.
1. Vytvoří se [úloha Runbooku](automation-runbook-execution.md) a zobrazí se její stav.

   Stav úlohy se spustí jako *ve frontě* . to značí, že čekáte na zpřístupnění pracovního procesu Runbooku v cloudu. Přesune se ke *spuštění* , když pracovní proces tuto úlohu vyvolá a *pak se spustí,* když se Runbook skutečně spustí.

1. Po dokončení úlohy Runbooku se zobrazí její výstup. V takovém případě byste měli vidět *Hello World*.

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Zavřete testovací podokno a vraťte se na plátno.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4 – publikování a spuštění Runbooku

Vytvořený Runbook je stále v režimu konceptu. Abyste ho mohli spustit v produkčním prostředí, musíte ho publikovat. Když publikujete sadu Runbook, přepíšete existující publikovanou verzi verzí konceptu. V případě, že jste sadu Runbook právě vytvořili, ještě nemáte publikovanou verzi.

1. Klikněte na **publikovat** a publikujte Runbook a po zobrazení výzvy klikněte na **Ano** .
1. Pokud se posunete doleva, abyste sadu Runbook zobrazili v podokně sady **Runbook** nyní, zobrazí se **stav vytváření obsahu** **Publikováno**.
1. Posuňte se zpět doprava, abyste viděli podokno **MyFirstRunbook-Workflow**.
   Možnosti v horní části nám umožňují spuštění Runbooku, naplánování jeho spuštění někdy v budoucnu nebo vytvoření [Webhooku](automation-webhooks.md) , aby ho bylo možné spustit prostřednictvím volání protokolu HTTP.
1. Chcete Runbook spustit pouze tak, že kliknete na tlačítko **Start** a po zobrazení výzvy **Ano** .

   ![Spustit Runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Otevře se podokno úlohy pro vytvořenou úlohu Runbooku. Toto podokno můžete zavřít, ale v tomto případě ho necháte otevřené, abyste mohli sledovat průběh úlohy.
1. Stav úlohy se zobrazí v **souhrnu úlohy** a odpovídá stavům, které jste viděli při testování Runbooku.

   ![Souhrn úlohy](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Jakmile se stav Runbooku zobrazí *dokončeno*, klikněte na **výstup**. Otevře se podokno výstup a uvidíte své *Hello World*.

   ![Souhrn úlohy](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Zavřete podokno výstup.
1. Kliknutím na **všechny protokoly** otevřete podokno streamy pro úlohu Runbooku. měla by se zobrazit pouze *Hello World* ve výstupním datovém proudu, ale toto zobrazení může zobrazit další datové proudy pro úlohu Runbooku, jako je například verbose a chyba, pokud je sada Runbook zapisuje do nich.

   ![Souhrn úlohy](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Zavřením stránky streamy a stránky úlohy se vraťte na stránku MyFirstRunbook.
1. Kliknutím na **úlohy** otevřete stránku úlohy pro tuto sadu Runbook. Tato stránka obsahuje seznam všech úloh, které tento Runbook vytvořil. měla by se zobrazit jenom jedna úloha, protože jste úlohu spustili jenom jednou.

   ![Úlohy](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Kliknutím na tuto úlohu můžete otevřít stejnou stránku úlohy, kterou jste zobrazili při spuštění Runbooku. Tato akce vám umožní vrátit se zpět v čase a zobrazit podrobnosti o každé úloze vytvořené pro konkrétní Runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Krok 5 – přidání ověřování pro správu prostředků Azure

Runbook jste otestovali a publikovali, ale zatím nedělá cokoli užitečné. Chcete mít k dispozici správu prostředků Azure. To ale nemůže dělat, pokud jste neověřili pomocí přihlašovacích údajů, které jsou uvedené v [požadavcích](#prerequisites). Uděláte to pomocí rutiny **Connect-AzAccount** .

1. Kliknutím na **Upravit** v podokně MyFirstRunbook-Workflow otevřete textový editor.
2. řádek **Write-Output** již nepotřebujete, a proto jej odstraňte.
3. Umístěte kurzor na prázdný řádek mezi složené závorky.
4. Zadejte nebo zkopírujte a vložte následující kód, který bude zpracovávat ověřování pomocí vašeho účtu Automation spustit jako:

   ```powershell-interactive
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

   > [!IMPORTANT]
   > **Add-AzAccount** a **Login-AzAccount** jsou nyní aliasy pro **Connect-AzAccount**. Pokud rutina **Connect-AzAccount** neexistuje, můžete použít rutinu **Add-AzAccount** nebo **Login-AzAccount**, případně můžete [aktualizovat moduly](automation-update-azure-modules.md) v účtu Automation na nejnovější verze.

> [!NOTE]
> [Vaše moduly](automation-update-azure-modules.md) možná budete muset aktualizovat, i když jste právě vytvořili nový účet Automation.

1. Klikněte na **testovací podokno** , abyste mohli Runbook otestovat.
1. Kliknutím na **Spustit** spusťte test. Po dokončení by se měl zobrazit výstup podobný následujícímu, který zobrazuje základní informace z vašeho účtu. Tato akce potvrdí, že přihlašovací údaje jsou platné.

   ![Prohlížečů](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Krok 6 – přidání kódu pro spuštění virtuálního počítače

Teď, když se váš Runbook ověřuje pro vaše předplatné Azure, můžete spravovat prostředky. přidáte příkaz ke spuštění virtuálního počítače. V rámci vašeho předplatného Azure si můžete vybrat libovolný virtuální počítač. teď už jste zakódujeme tento název v Runbooku. Pokud spravujete prostředky v rámci více předplatných, musíte použít parametr **-AzContext** spolu s příkazem [Get-AzContext](/powershell/module/az.accounts/get-azcontext).

1. Po *připojení-AzAccount*zadejte *Start-AzVM-name ' VMName '-ResourceGroupName ' NameofResourceGroup '* , který poskytuje název a název skupiny prostředků virtuálního počítače, který se má spustit.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

1. Uložte Runbook a pak klikněte na **testovací podokno** , abyste ho mohli otestovat.
1. Kliknutím na **Spustit** spusťte test. Po dokončení ověřte, že je virtuální počítač spuštěný.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Krok 7 – přidání vstupního parametru do Runbooku

sada Runbook aktuálně spouští virtuální počítač, který jste pevně zakódované v sadě Runbook, ale byla užitečnější, pokud byste mohli při spuštění sady Runbook zadat virtuální počítač. Do Runbooku přidáte vstupní parametry, které tuto funkci poskytují.

1. Přidejte do Runbooku parametry pro *VMName* a *ResourceGroupName* a použijte tyto proměnné s rutinou **Start-AzVM** jako v následujícím příkladu.

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

2. Uložte Runbook a otevřete testovací podokno. Nyní můžete zadat hodnoty dvou vstupních proměnných, které jsou v testu.
3. Zavřete podokno test.
4. Kliknutím na **publikovat** publikujte novou verzi Runbooku.
5. Zastavte virtuální počítač, který jste spustili v předchozím kroku.
6. Kliknutím na **Spustit** spustíte Runbook. Zadejte **VMName** a **ResourceGroupName** pro virtuální počítač, který se chystáte spustit.

   ![Spustit Runbook](media/automation-first-runbook-textual/automation-pass-params.png)

7. Po dokončení sady Runbook ověřte, zda byl virtuální počítač spuštěn.

## <a name="next-steps"></a>Další kroky

* Další informace o PowerShellu, včetně referenčních modulů jazyka a výukových modulů, najdete v [dokumentaci k PowerShellu](https://docs.microsoft.com/en-us/powershell/scripting/overview).
* Informace o tom, jak začít s grafickými Runbooky, najdete v tématu [můj první grafický Runbook](automation-first-runbook-graphical.md) .
* Začínáme se sadami Runbook PowerShellu najdete v tématu [můj první powershellový Runbook](automation-first-runbook-textual-powershell.md) .
* Další informace o typech runbooků, jejich výhodách a omezeních najdete v tématu [Azure Automation typy runbooků](automation-runbook-types.md) .
* Další informace o funkci podpory skriptů PowerShellu najdete [v tématu Podpora nativních skriptů PowerShellu v Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
