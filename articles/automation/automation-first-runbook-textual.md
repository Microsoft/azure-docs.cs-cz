---
title: Moje první runbook pracovního postupu prostředí PowerShell v Azure Automation
description: Kurz vás provede vytvořením, otestováním a publikováním jednoduchého textového runbooku, který využívá pracovní postup PowerShellu.
keywords: pracovní postup v powershellu, příklady pracovního postupu v powershellu, pracovní postup powershell
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: 8b103437ab30c05ddab88b7a8a723cd2b4b1d5f6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405892"
---
# <a name="my-first-powershell-workflow-runbook"></a>Můj první runbook pracovního postupu PowerShellu

> [!div class="op_single_selector"]
> * [Grafický](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Pracovní postup PowerShellu](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

V tomto kurzu se seznámíte s vytvořením [runbooku pracovního postupu PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks) ve službě Azure Automation. Začněte s jednoduchým runbookem, který testujete a publikujete, zatímco se naučíte sledovat stav úlohy runbooku. Pak upravte runbook tak, aby skutečně spravoval prostředky Azure, ilustrovaný spuštěním virtuálního počítače Azure. Nakonec pomocí parametrů runbooku zrobustovaněji.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](automation-offering-get-started.md), abyste si mohli runbook podržet a mohli ověřovat prostředky Azure. Tento účet musí mít oprávnění ke spuštění a zastavení virtuálního počítače.
* Virtuální počítač Azure. Vzhledem k tomu, že zastavíte a spustíte tento počítač, neměl by to být produkční virtuální počítač.

## <a name="step-1---create-new-runbook"></a>Krok 1 – vytvoření nového runbooku

Začněte vytvořením jednoduchého souboru `Hello World`runbook, který vypíše text .

1. Na webu Azure Portal otevřete účet Automation.

   Stránka účtu Automation nabízí rychlý přehled prostředků v tomto účtu. Už byste tam měli mít nějaké prostředky. Většina těchto prostředků jsou moduly automaticky zahrnuty do nového účtu automatizace. Měli byste mít také prostředek přihlašovacíúdaje přidružený k vašemu předplatnému.
 
1. Vyberte **Runbooky** v části **Automatizace procesů,** chcete-li otevřít seznam runbooků.
1. Vytvořte novou runbook výběrem **možnosti Vytvořit runbook**.
1. Dejte runbooku název **MyFirstRunbook-Workflow**.
1. V takovém případě vytvoříte [runbook pracovního postupu prostředí PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Vyberte **pracovní postup prostředí PowerShell** pro **typ runbooku**.
1. Kliknutím na **Vytvořit** vytvoříte runbook a otevřete textový editor.

## <a name="step-2---add-code-to-the-runbook"></a>Krok 2 – přidání kódu do runbooku

Můžete buď zadat kód přímo do sady Runbook, nebo můžete vybrat rutiny, sady Runbook a datové zdroje z ovládacího prvku Knihovna a přidat je do sady Runbook se všemi souvisejícími parametry. Pro účely tohoto kurzu zadáte kód přímo do runbooku.

1. Runbook je aktuálně prázdný pouze `Workflow` s požadovaným klíčovým slovem, názvem runbooku a závorkami, které zaobějí celý pracovní postup.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Zadejte `Write-Output "Hello World"` mezi závorky.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Kliknutím na **Uložit** runbook uložte.

## <a name="step-3---test-the-runbook"></a>Krok 3 – otestování runbooku

Před publikováním sady Runbook, abyste ji zpřístupní v produkčním prostředí, byste ji měli otestovat, abyste se ujistili, že funguje správně. Testování sady Runbook spustí verzi konceptu a umožňuje interaktivní zobrazení jeho výstupu.

1. Výběrem **podokna Test** otevřete podokno Testování.
1. Klepnutím na **tlačítko Start** spusťte test s testováním jediné povolené možnosti.
1. Všimněte si, že je vytvořena [úloha sady Runbook](automation-runbook-execution.md) a její stav se zobrazí v podokně.

   Stav úlohy začíná jako ve frontě, což znamená, že úloha čeká na pracovníka runbooku v cloudu, aby byl k dispozici. Stav se změní na Spuštění, když pracovník nárokuje úlohu. Nakonec stav se stane Spuštěno, když se spuštění runbooku skutečně spustí.

1. Po dokončení úlohy sady Runbook se v podokně Test zobrazí její výstup. V tomto případě `Hello World`vidíte .

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Zavřete testovací podokno a vraťte se na plátno.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4 – publikování a spuštění runbooku

Vytvořený runbook je stále v režimu konceptu. Před spuštěním v produkčním prostředí je nutné jej publikovat. Když runbook publikujete, přepíšete vydanou verzi verzí v režimu konceptu. V tomto případě zatím ještě publikovanou verzi nemáte, protože jste runbook teprve vytvořili.

1. Kliknutím na **Publikovat** runbook publikujte a po zobrazení výzvy klikněte na **Ano**.
1. Posunutím doleva zobrazíte sadu Runbook na stránce **Runbook** a všimněte si, že pole **Stav vytváření** je nastaveno na **Publikováno**.
1. Přejděte zpět doprava a zobrazte stránku **pro MyFirstRunbook-Workflow**.

   Možnosti v horní části umožňují spustit runbook nyní, naplánovat budoucí čas zahájení nebo vytvořit [webhooku](automation-webhooks.md) tak, aby sada runbook mohla být spuštěna prostřednictvím volání HTTP.

1. Po zobrazení **výzvy** ke spuštění runbooku vyberte Spustit a potom **Ano.**

   ![Spuštění runbooku](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Pro vytvořenou úlohu runbooku se otevře podokno úlohy. V takovém případě ponechte podokno otevřené, abyste mohli sledovat průběh úlohy.

1. Všimněte si, že stav úlohy je zobrazen v **souhrnu úlohy**. Tento stav odpovídá stavy, které jste viděli při testování runbook.

   ![Souhrn úlohy](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Jakmile se stav runbooku zobrazí Dokončeno, klikněte na **Výstup**. Otevře se stránka Výstup, kde `Hello World` se zobrazí vaše zpráva.

   ![Souhrn úlohy](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Zavřete stránku Výstup.

1. Klikněte na **Všechny protokoly** a otevřete podokno Datové proudy, které patří k úloze runbooku. Měli byste `Hello World` vidět pouze ve výstupním datovém proudu. Všimněte si, že podokno Datové proudy můžete zobrazit další datové proudy pro úlohu runbook, jako je například podrobné a chybové datové proudy, pokud runbook zapíše do nich.

   ![Souhrn úlohy](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Zavřete podokno Datové proudy a podokno úloh a vraťte se na stránku MyFirstRunbook.
1. Kliknutím na **Úlohy** v části **Zdroje** otevřete stránku Úlohy pro tento runbook. Na této stránce jsou uvedeny všechny úlohy vytvořené v aplikaci Runbook. Měla by se zobrazit pouze jedna úloha uvedená, protože jste ji spouštěli pouze jednou.

   ![Úlohy](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Kliknutím na název úlohy otevřete stejné podokno úloh, které jste si prohlíželi při spuštění runbooku. Toto podokno slouží k zobrazení podrobností o libovolné úloze vytvořené pro runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Krok 5 – přidání ověřování ke správě prostředků Azure

Runbook jste otestovali a publikovali, ale zatím nedělá nic užitečného. Chcete po něm, aby spravoval prostředky Azure. To nemůže udělat, pokud se ověří pomocí pověření pro předplatné. Ověřování používá rutinu [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0)

>[!NOTE]
>Pro sady Runbook `Add-AzAccount` `Add-AzureRMAccount` prostředí PowerShell `Connect-AzAccount`a jsou aliasy pro . Můžete použít tyto rutiny nebo můžete [aktualizovat moduly](automation-update-azure-modules.md) v účtu Automation na nejnovější verze. Možná budete muset aktualizovat moduly, i když jste právě vytvořili nový účet Automatizace.

1. Přejděte na stránku MyFirstRunbook-Workflow a otevřete textový editor klepnutím na tlačítko **Upravit**.
2. Odstraňte `Write-Output` řádek.
3. Umístěte kurzor na prázdný řádek mezi složenými závorkami.
4. Zadejte nebo zkopírujte a vložte následující kód, který zpracovává ověřování pomocí účtu Spustit jako automatizace.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

1. Klikněte na **podokno Test,** abyste mohli otestovat runbook.
1. Kliknutím na **Spustit** spustíte test. Po dokončení byste měli vidět výstup podobný následujícímu, který zobrazuje základní informace z vašeho účtu. Tato akce potvrzuje, že pověření je platné.

   ![Ověření](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Krok 6 – přidání kódu pro spuštění virtuálního počítače

Teď, když se vaše runbook autentuje pro předplatné Azure, můžete spravovat prostředky. Přidáme příkaz ke spuštění virtuálního počítače. Můžete si vybrat libovolný virtuální počítač ve vašem předplatném Azure a pro tuto chvíli jste hardcoding, že název v runbooku. Pokud spravujete prostředky napříč více předplatnými, `AzContext` musíte použít parametr s rutinou [Get-AzContext.](/powershell/module/az.accounts/get-azcontext)

1. Zadejte název a název skupiny prostředků virtuálního virtuálního soudu, který má být zahájen, zadáním volání rutiny [Start-AzVM,](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0
) jak je znázorněno níže. 

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

1. Uložte runbook a klikněte na **Podokno test,** abyste ho mohli otestovat.
1. Kliknutím na **Spustit** spustíte test. Po dokončení zkontrolujte, že byl spuštěn virtuální ho.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Krok 7 – přidání vstupního parametru do runbooku

Váš runbook aktuálně spustí virtuální počítač, který jste pevně zakódovali v runbooku. Bude užitečnější, pokud můžete zadat virtuální ho dispoziti při spuštění runbooku. Přidáme vstupní parametry do runbooku, abychom tuto funkci poskytli.

1. Přidejte proměnné `VMName` pro `ResourceGroupName` parametry a do runbooku a `Start-AzVM` použijte proměnné s rutinou, jak je znázorněno níže.

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

2. Uložte runbook a otevřete testovací podokno. Nyní můžete zadat hodnoty pro dvě vstupní proměnné, které jsou v testu.
3. Zavřete testovací podokno.
4. Kliknutím na **Publikovat** publikujte novou verzi runbooku.
5. Zastavte virtuální hod, který jste spustili.
6. Kliknutím na **Spustit** spustíte runbook. 
7. Zadejte hodnoty pro **VMNAME** a **RESOURCEGROUPNAME** pro virtuální hosti, který se chystáte spustit.

   ![Spuštění runbooku](media/automation-first-runbook-textual/automation-pass-params.png)

8. Po dokončení runbooku ověřte, že byl virtuální virtuální hotel spuštěn.

## <a name="next-steps"></a>Další kroky

* Další informace o prostředí PowerShell, včetně jazykových odkazů a výukových modulů, najdete v dokumentu [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
* Odkaz na rutinu prostředí PowerShell naleznete v tématu [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
* Informace o tom, jak začít s grafickými runbooky, najdete [v tématu Moje první grafická kniha runbook](automation-first-runbook-graphical.md).
* První kroky s powershellovými runbooky najdete v článku [Můj první powershellový runbook](automation-first-runbook-textual-powershell.md).
* Další informace o typech runbooků a jejich výhodách a omezeních najdete v [tématu Typy runbooků Azure Automation](automation-runbook-types.md).
* Další informace o funkcích podpory skriptů Prostředí PowerShell najdete [v tématu Podpora nativního skriptu PowerShellu v Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).