---
title: Moje první runbook prostředí PowerShell v Azure Automation
description: Kurz vás provede vytvořením, otestováním a publikováním jednoduchého powershellového runbooku.
keywords: azure powershell, kurz k powershellovému scriptu, automatizace powershellu
services: automation
ms.subservice: process-automation
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: e0c48137f5eecc96b6e7b1cbce5f0c683b2a976a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367308"
---
# <a name="my-first-powershell-runbook"></a>Můj první powershellový runbook

> [!div class="op_single_selector"]
> * [Grafický](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Pracovní postup PowerShellu](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

V tomto kurzu se seznámíte s vytvořením [powershellového runbooku](automation-runbook-types.md#powershell-runbooks) ve službě Azure Automation. Začněte s jednoduchým runbookem, který můžete testovat a publikovat, a zároveň se naučíte sledovat stav úlohy runbooku. Pak upravte runbook tak, aby skutečně spravoval prostředky Azure, v tomto případě spuštění virtuálního počítače Azure. Přidáním parametrů runbooku dokončete kurz, aby byl runbook robustnější.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](automation-quickstart-create-account.md), abyste si mohli runbook podržet a mohli ověřovat prostředky Azure. Tento účet musí mít oprávnění ke spuštění a zastavení virtuálního počítače.
* Virtuální počítač Azure. Vzhledem k tomu, že zastavíte a spustíte tento počítač, neměl by to být produkční virtuální počítač.
* V případě potřeby [importujte moduly Azure](shared-resources/modules.md) nebo [aktualizační moduly](automation-update-azure-modules.md) na základě rutin, které používáte.

## <a name="differences-from-powershell-workflow-runbooks"></a>Rozdíly od runbooků pracovního postupu prostředí PowerShell

Sady Runbook prostředí PowerShell mají stejný životní cyklus, možnosti a správu jako runbooky pracovního postupu prostředí PowerShell. Existují však určité rozdíly a omezení.

| Charakteristika  | Sady Runbook powershellu | Runbooky pracovního postupu prostředí PowerShell |
| ------ | ----- | ----- |
| Rychlost | Spouštějte rychle, protože nepoužívají krok kompilace. | Utíkej pomaleji. |
| Kontrolní body | Nepodporujte kontrolní stanoviště. Runbook prostředí PowerShell může pokračovat v provozu pouze od začátku. | Použijte kontrolní body, které umožňují sešitu obnovit provoz z libovolného bodu. |
| Spuštění příkazu | Podporujte pouze sériové spuštění. | Podpora sériového i paralelního spuštění.|
| Runspace | Jeden runspace spustí vše ve skriptu. | Samostatný runspace lze použít pro aktivitu, příkaz nebo blok skriptu. |

Kromě těchto rozdílů mají sady Runbook prostředí PowerShell některé [syntaktické rozdíly](https://technet.microsoft.com/magazine/dn151046.aspx) od runbooků pracovního postupu prostředí PowerShell.

## <a name="step-1---create-runbook"></a>Krok 1 – vytvoření runbooku

Začněte vytvořením jednoduchého souboru `Hello World`runbook, který vypíše text .

1. Na webu Azure Portal otevřete účet Automation.
2. Vyberte **Runbooky** v části **Automatizace procesů,** chcete-li otevřít seznam runbooků.
3. Vytvořte novou runbook výběrem **možnosti Vytvořit runbook**.
4. Dejte runbooku název **MyFirstRunbook-PowerShell**.
5. V takovém případě vytvoříte [runbook prostředí PowerShell](automation-runbook-types.md#powershell-runbooks). Vyberte **prostředí PowerShell** pro **typ runbooku**.
6. Kliknutím na **Vytvořit** vytvoříte runbook a otevřete textový editor.

## <a name="step-2---add-code-to-the-runbook"></a>Krok 2 – přidání kódu do runbooku

Kód můžete buď zadat přímo do runbooku, nebo můžete vybrat rutiny, runbooky a assety z ovládacího prvku Knihovna a přidat je do runbooku spolu se všemi souvisejícími parametry. V tomto kurzu zadáte kód přímo do runbooku.

1. Runbook je momentálně prázdný. Zadejte `Write-Output "Hello World"` text skriptu.

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. Kliknutím na **Uložit** runbook uložte.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"> </a> Krok 3 – Testování runbooku

Před publikováním sady Runbook, abyste ji zpřístupní v produkčním prostředí, byste ji měli otestovat, abyste se ujistili, že funguje správně. Testování sady Runbook spustí verzi konceptu a umožňuje interaktivní zobrazení jeho výstupu.

1. Kliknutím na **Testovací podokno** otevřete testovací podokno.
2. Kliknutím na **Spustit** spustíte test. Měla by to být jediná povolená možnost.
3. Všimněte si, že je vytvořena [úloha sady Runbook](automation-runbook-execution.md) a její stav se zobrazí v podokně.

   Stav úlohy `Queued`začíná jako , což znamená, že úloha čeká na pracovníka runbooku v cloudu, aby byl k dispozici. Stav se `Starting` změní na způsob, kdy pracovník požaduje úlohu. Nakonec se stav `Running` stane, když se runbook skutečně spustí.

4. Po dokončení úlohy sady Runbook se v podokně Test zobrazí její výstup. V tomto případě `Hello World`vidíte .

   ![Výstup testovacího podokna](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. Zavřete testovací podokno a vraťte se na plátno.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4 – publikování a spuštění runbooku

Vytvořený soubor Runbook je stále v režimu konceptu. Abyste ho mohli spustit v produkčním prostředí, je potřeba ho publikovat. Když runbook publikujete, přepíšete vydanou verzi verzí v režimu konceptu. V tomto případě zatím ještě publikovanou verzi nemáte, protože jste runbook teprve vytvořili.

1. Kliknutím na **Publikovat** runbook publikujte a po zobrazení výzvy klikněte na **Ano**.
1. Posunutím doleva zobrazíte sadu Runbook na stránce Sady Runbook a všimněte si, že hodnota **Stav vytváření** je nastavená na **Publikováno**.
1. Posuňte se zpět doprava, abyste viděli podokno **MyFirstRunbook-PowerShell**.
   
   Možnosti v horní části umožňují spustit runbook nyní, naplánovat budoucí čas zahájení nebo vytvořit [webhooku](automation-webhooks.md) tak, aby sada runbook mohla být spuštěna prostřednictvím volání HTTP.
1. Po zobrazení **výzvy** ke spuštění runbooku vyberte Spustit a potom **Ano.** 
1. Pro vytvořenou úlohu runbooku se otevře podokno úlohy. I když můžete zavřít toto podokno, nechte ho otevřené hned teď, abyste mohli sledovat průběh úlohy. Stav úlohy je zobrazen ve **shrnutí úlohy**a možné stavy jsou popsány pro testování runbooku.

   ![Souhrn úlohy](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Jakmile se zobrazí `Completed`stav runbooku , kliknutím na **Výstup** otevřete stránku Výstup, kde se zobrazí. `Hello World`

   ![Výstup úlohy](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Zavřete stránku Výstup.
1. Klikněte na **Všechny protokoly** a otevřete podokno Datové proudy, které patří k úloze runbooku. Měli byste `Hello World` vidět pouze ve výstupním datovém proudu.

    Všimněte si, že podokno Datové proudy můžete zobrazit další datové proudy pro úlohu runbook, jako je například Verbose a Error datové proudy, pokud runbook zapíše do nich.

   ![Všechny protokoly](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Zavřete podokno Datové proudy a podokno úloh a vraťte se na stránku **MyFirstRunbook-PowerShell.**
1. V části **Podrobnosti**klikněte na **Úlohy** a otevřete stránku Úlohy pro tento runbook. Na této stránce jsou uvedeny všechny úlohy vytvořené v aplikaci Runbook. Měli byste vidět pouze jednu úlohu uvedenou, protože jste ji spustit pouze jednou.

   ![Seznam úloh](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. Kliknutím na název úlohy otevřete stejné podokno úloh, které jste si prohlíželi při spuštění runbooku. Toto podokno slouží k zobrazení podrobností o libovolné úloze vytvořené pro runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Krok 5 – přidání ověřování ke správě prostředků Azure

Runbook jste otestovali a publikovali, ale zatím nedělá nic užitečného. Chcete po něm, aby spravoval prostředky Azure. Chcete-li to provést, musí být runbook schopen ověřit pomocí účtu Spustit jako, který byl automaticky vytvořen při vytvoření účtu Automatizace.

Jak je znázorněno v příkladu níže, spustit jako připojení je provedeno s [rutinou Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) Pokud spravujete prostředky napříč více předplatnými, musíte použít `AzContext` parametr s [Get-AzContext](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0).

> [!NOTE]
> Pro sady Runbook `Add-AzAccount` `Add-AzureRMAccount` prostředí PowerShell `Connect-AzAccount`a jsou aliasy pro . Můžete použít tyto rutiny nebo můžete [aktualizovat moduly](automation-update-azure-modules.md) v účtu Automation na nejnovější verze. Možná budete muset aktualizovat moduly, i když jste právě vytvořili nový účet Automatizace.

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
1. Otevřete textový editor kliknutím na **Upravit** na stránce **MyFirstRunbook-PowerShell.**
1. Už tu `Write-Output` linku nepotřebuješ. Jen do toho a smaž to.
1. Zadejte nebo zkopírujte a vložte následující kód, který zpracovává ověřování pomocí účtu Spustit jako automatizace.

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

1. Klikněte na **podokno Test,** abyste mohli otestovat runbook.
1. Kliknutím na **Spustit** spustíte test. Po dokončení byste měli vidět výstup podobný následujícímu, který zobrazuje základní informace z vašeho účtu. Tento výstup potvrzuje, že spustit jako účet je platný.

   ![Ověření](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Krok 6 – přidání kódu pro spuštění virtuálního počítače

Teď, když se vaše runbook autentuje pro vaše předplatné Azure, můžete spravovat prostředky. Přidáme příkaz ke spuštění virtuálního počítače. Můžete si vybrat libovolný virtuální počítač ve vašem předplatném Azure a jen pevně kód, že název v runbooku pro tuto chvíli.

1. Do skriptu runbooku přidejte rutinu [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) pro spuštění virtuálního počítače. Jak je znázorněno níže, rutina spustí `VMName` virtuální počítač s `ResourceGroupName`názvem a se skupinou prostředků s názvem .

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

1. Uložte runbook a klikněte na **Podokno test,** abyste ho mohli otestovat.
1. Chcete-li zahájit test, klepněte na tlačítko **Start.** Po dokončení se ujistěte, že se virtuální počítač spustil.

## <a name="step-7---add-an-input-parameter"></a>Krok 7 – Přidání vstupního parametru

Runbook aktuálně spustí virtuální počítač, který jste v aplikaci Runbook zakódovali. Runbook bude užitečnější, pokud zadáte virtuální počítač při spuštění runbooku. Přidáme vstupní parametry do runbooku, abychom tuto funkci poskytli.

1. V textovém editoru `Start-AzVM` upravte rutinu tak, aby `VMName` používala proměnné pro parametry a `ResourceGroupName`. 

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
1. Zadejte hodnoty pro **VMNAME** a **RESOURCEGROUPNAME** pro virtuální počítač, který budete spouštět, a klikněte na **OK**.<br><br> ![Předání parametru](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. Po dokončení runbooku se ujistěte, že byl spuštěn virtuální počítač.

## <a name="next-steps"></a>Další kroky

* Další informace o Prostředí PowerShell, včetně jazykových referencí a výukových modulů, najdete v [tématu Dokumenty prostředí PowerShell](/powershell/scripting/overview).
* Informace o tom, jak začít s grafickými runbooky, najdete [v tématu Moje první grafická kniha runbook](automation-first-runbook-graphical.md).
* Pokud chcete začít s runbooky pracovního postupu prostředí PowerShell, [přečtěte si první runbook pracovního postupu prostředí PowerShell](automation-first-runbook-textual.md).
* Další informace o typech runbooků a jejich výhodách a omezeních najdete v [tématu Typy runbooků Azure Automation](automation-runbook-types.md).
* Další informace o funkci podpory skriptů Prostředí PowerShell najdete [v tématu Podpora nativního skriptu PowerShellu v Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
