---
title: Moje první grafická runbook v Azure Automation
description: Kurz vás provede vytvořením, otestováním a publikováním jednoduchého grafického runbooku.
keywords: runbook, šablona sady runbook, automatizace sady runbook, runbook azure
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: 6bd360b2075c337e3ed3d69d84368d16571a9335
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536050"
---
# <a name="my-first-graphical-runbook"></a>Můj první grafický runbook

> [!div class="op_single_selector"]
> * [Grafický](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Pracovní postup PowerShellu](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

V tomto kurzu se seznámíte s vytvořením [grafického runbooku](automation-runbook-types.md#graphical-runbooks) ve službě Azure Automation. Začněte s jednoduchým runbookem, který můžete testovat a publikovat, a zároveň se naučíte sledovat stav úlohy runbooku. Pak upravte runbook tak, aby skutečně spravoval prostředky Azure, v tomto případě spuštění virtuálního počítače Azure. Dokončete kurz, aby byl runbook robustnější přidáním parametrů runbooku a podmíněných odkazů.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](automation-offering-get-started.md), abyste si mohli runbook podržet a mohli ověřovat prostředky Azure. Tento účet musí mít oprávnění ke spuštění a zastavení virtuálního počítače.
* Virtuální počítač Azure. Vzhledem k tomu, že zastavíte a spustíte tento počítač, neměl by to být produkční virtuální počítač.

## <a name="step-1---create-runbook"></a>Krok 1 – vytvoření runbooku

Začněte vytvořením jednoduchého souboru `Hello World`runbook, který vypíše text .

1. Na webu Azure Portal otevřete účet Automation. 

    Stránka účtu Automation nabízí rychlý přehled prostředků v tomto účtu. Už byste tam měli mít nějaké prostředky. Většina těchto prostředků jsou moduly automaticky zahrnuty do nového účtu automatizace. Měli byste mít také prostředek přihlašovacíúdaje přidružený k vašemu předplatnému.
2. Vyberte **Runbooky** v části **Automatizace procesů,** chcete-li otevřít seznam runbooků.
3. Vytvořte novou runbook výběrem **možnosti Vytvořit runbook**.
4. Dejte runbooku název **MyFirstRunbook-Graphical**.
5. V takovém případě vytvoříte [grafickou runbook](automation-graphical-authoring-intro.md). Vyberte **možnost Grafické** pro **typ runbooku**.<br> ![Nový runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Kliknutím na **Vytvořit** vytvoříte runbook a otevřete grafický editor.

## <a name="step-2---add-activities"></a>Krok 2 – Přidání aktivit

Ovládací prvek Knihovna na levé straně editoru vám umožní vybrat aktivity, které chcete do runbooku přidat. Přidáte rutinu do `Write-Output` výstupního textu z runbooku.

1. V ovládacím prvku Knihovna klikněte `write-output`do vyhledávacího pole a zadejte . Výsledky hledání jsou zobrazeny na následujícím obrázku. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Přejděte do dolní části seznamu. Klepněte pravým tlačítkem myši na **příkaz Write-Output** a vyberte **přidat na plátno**. Případně můžete kliknout na tři tečky (...) vedle názvu rutiny a pak vybrat **Přidat na plátno**.
1. Klikněte na aktivitu **Write-Output** na plátně. Tato akce otevře stránku ovládacího prvku Konfigurace, která umožňuje konfigurovat aktivitu.
1. Pole **Popisek** je výchozí pro název rutiny, ale můžete ho změnit na něco popisnějšího. Změňte `Write Hello World to output`ji na .
1. Klikněte na tlačítko **Parametry** a zadejte hodnoty parametru rutiny.

   Některé rutiny mají více sad parametrů a je třeba vybrat, kterou z nich chcete použít. V tomto `Write-Output` případě má pouze jednu sadu parametrů.

1. Vyberte `InputObject` parametr. Toto je parametr, který slouží k určení textu, který má být odeslán do výstupního datového proudu.
1. Rozevírací nabídka **Zdroj dat** poskytuje zdroje, které můžete použít k naplnění hodnoty parametru. V této nabídce vyberte **výraz prostředí PowerShell**. 

   Můžete použít výstup z takových zdrojů, jako je jiná aktivita, prostředek automatizace nebo výraz prostředí PowerShell. V tomto případě je `Hello World`výstup pouze . Můžete použít powershellový výraz a zadat řetězec.<br>

1. Do pole **Výraz** `Hello World` zadejte a dvakrát klepněte na **TLAČÍTKO OK,** abyste se vrátili na plátno.
1. Kliknutím na **Uložit** runbook uložte.

## <a name="step-3---test-the-runbook"></a>Krok 3 – otestování runbooku

Před publikováním sady Runbook, abyste ji zpřístupní v produkčním prostředí, byste ji měli otestovat, abyste se ujistili, že funguje správně. Testování sady Runbook spustí verzi konceptu a umožňuje interaktivní zobrazení jeho výstupu.

1. Výběrem **podokna Test** otevřete podokno Testování.
1. Kliknutím na **Spustit** spustíte test. Měla by to být jediná povolená možnost.
1. Všimněte si, že je vytvořena [úloha sady Runbook](automation-runbook-execution.md) a její stav se zobrazí v podokně.

   Stav úlohy `Queued`začíná jako , což znamená, že úloha čeká na pracovníka runbooku v cloudu, aby byl k dispozici. Stav se `Starting` změní na způsob, kdy pracovník požaduje úlohu. Nakonec se stav `Running` stane, když se runbook skutečně spustí.

1. Po dokončení úlohy sady Runbook se v podokně Test zobrazí její výstup. V tomto případě `Hello World`vidíte .

    ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Zavřete testovací podokno a vraťte se na plátno.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4 – publikování a spuštění runbooku

Vytvořený soubor Runbook je stále v režimu konceptu. Abyste ho mohli spustit v produkčním prostředí, je potřeba ho publikovat. Když runbook publikujete, přepíšete vydanou verzi verzí v režimu konceptu. V tomto případě zatím ještě publikovanou verzi nemáte, protože jste runbook teprve vytvořili.

1. Vyberte **Publikovat,** chcete-li publikovat runbook, a pak **Ano,** když se zobrazí výzva.
1. Posunutím doleva zobrazíte sadu Runbook na stránce Sady Runbook a všimněte si, že hodnota **Stav vytváření** je nastavená na **Publikováno**.
1. Přejděte zpět doprava a zobrazte stránku **pro MyFirstRunbook-Graphical**.

   Možnosti v horní části umožňují spustit runbook nyní, naplánovat budoucí čas zahájení nebo vytvořit [webhooku](automation-webhooks.md) tak, aby sada runbook mohla být spuštěna prostřednictvím volání HTTP.

1. Po zobrazení **výzvy** ke spuštění runbooku vyberte Spustit a potom **Ano.**
1. Pro vytvořenou úlohu runbooku se otevře podokno úlohy. Ověřte, zda se v poli **Stav úlohy** zobrazuje **funkce Dokončeno**.
1. Kliknutím na **Výstup** otevřete stránku `Hello World` Výstup, kde se zobrazí zobrazená stránka.
1. Zavřete stránku Výstup.
1. Klikněte na **Všechny protokoly** a otevřete podokno Datové proudy, které patří k úloze runbooku. Měli byste `Hello World` vidět pouze ve výstupním datovém proudu. 

    Všimněte si, že podokno Datové proudy můžete zobrazit další datové proudy pro úlohu runbook, jako je například Verbose a Error datové proudy, pokud runbook zapíše do nich.
1. Zavřete podokno Datové proudy a podokno úloh a vraťte se na stránku **MyFirstRunbook-Graphical.**
1. Chcete-li zobrazit všechny úlohy pro runbook, vyberte **úlohy v** části **Zdroje**. Na stránce Úlohy jsou uvedeny všechny úlohy vytvořené v aplikaci Runbook. Měli byste vidět pouze jednu úlohu uvedenou, protože jste ji spustit pouze jednou.
1. Kliknutím na název úlohy otevřete stejné podokno úloh, které jste si prohlíželi při spuštění runbooku. Toto podokno slouží k zobrazení podrobností o libovolné úloze vytvořené pro runbook.

## <a name="step-5---create-variable-assets"></a>Krok 5 – vytvoření proměnných assetů

Otestovali jste a publikovali runbook, ale zatím nefunguje nic užitečného pro správu prostředků Azure. Před konfigurací sady Runbook k ověření je nutné vytvořit proměnnou pro uložení ID předplatného, nastavit aktivitu k ověření a poté na proměnnou odkazovat. Včetně odkazu na kontext předplatného umožňuje snadno pracovat s více odběry.

1. Zkopírujte ID předplatného z možnosti **Předplatná** v navigačním podokně.
1. Na stránce Účty automatizace vyberte **proměnné v** části **Sdílené prostředky**.
1. Vyberte **Přidat proměnnou**.
1. Na stránce Nová proměnná proveďte následující nastavení v uvedených polích.

    * **Name** -- `AzureSubscriptionId`zadejte .
    * **Hodnota** – zadejte ID předplatného. 
    * **Typ** -- zachovat řetězec vybraný.
    * **Šifrování** – použijte výchozí hodnotu.
1. Kliknutím na **Vytvořit** vytvořte proměnnou.

## <a name="step-6---add-authentication"></a>Krok 6 – Přidání ověřování

Teď, když máte proměnnou pro uložení ID předplatného, můžete nakonfigurovat runbook k ověření pomocí pověření Spustit jako pro vaše předplatné. To udělejte přidáním připojení Azure Run As jako prostředku. Musíte také přidat [rutinu Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) a rutinu [Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) na plátno.

>[!NOTE]
>Pro sady Runbook `Add-AzAccount` `Add-AzureRMAccount` prostředí PowerShell `Connect-AzAccount`a jsou aliasy pro . Všimněte si, že tyto aliasy nejsou k dispozici pro grafické sady Runbook. Grafická kniha runbook `Connect-AzAccount`může používat pouze samostatně.

1. Přejděte do runbooku a na stránce **MyFirstRunbook-Graphical** vyberte **Upravit.**
1. Už ten `Write Hello World to output` záznam nepotřebuješ. Stačí kliknout na tři tečky a vybrat **odstranit**.
1. V ovládacím prvku Knihovna rozbalte **položky DATOVÝCH ZDROJŮ**a potom **položku Připojení**. Přidat `AzureRunAsConnection` na plátno výběrem **přidat na plátno**.
1. Přejmenovat `AzureRunAsConnection` `Get Run As Connection`na .
1. Do ovládacího prvku `Connect-AzAccount` Knihovna zadejte vyhledávací pole.
1. Přidejte `Connect-AzAccount` na plátno.
1. Najeďte `Get Run As Connection` přes, dokud se ve spodní části obrazce nezobrazí kruh. Klepněte na kruh a `Connect-AzAccount` přetažením šipky vytvořte odkaz. Runbook začíná `Get Run As Connection` a poté `Connect-AzAccount`běží .<br> ![Vytvoření propojení mezi aktivitami](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. Na plátně `Connect-AzAccount`vyberte . V ovládacím podokně Konfigurace zadejte do pole **Popisek** **příkaz Přihlásit se do Azure.**
1. Klikněte na **Parametry**a zobrazí se stránka Konfigurace parametru aktivity.
1. Rutina `Connect-AzAccount` má více sad parametrů a před zadáním hodnot parametrů je třeba ji vybrat. Klepněte na **položku Parametr Set** a potom vyberte **položku ServicePrincipalCertificateWithSubscriptionId**.
1. Parametry pro tuto sadu parametrů jsou zobrazeny na stránce Konfigurace parametru aktivity. Klikněte na **APPLICATIONID**.<br> ![Přidání parametrů účtu Azure](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. Na stránce Hodnota parametru proveďte následující nastavení a klepněte na tlačítko **OK**.

   * **Zdroj dat** – vyberte **výstup aktivity**.
   * Seznam zdrojů dat – vyberte **možnost Získat připojení automatizace**.
   * **Cesta pole** `ApplicationId`-- typ . Zadáváte název vlastnosti pro cestu pole, protože aktivita vyvede objekt s více vlastnostmi.

1. Klepněte na **příkaz CERTIFICATETHUMBPRINT**a na stránce Hodnota parametru proveďte následující nastavení a klepněte na tlačítko **OK**.

    * **Zdroj dat** – vyberte **výstup aktivity**.
    * Seznam zdrojů dat – vyberte **možnost Získat připojení automatizace**.
    * **Cesta pole** `CertificateThumbprint`-- typ .
1. Klepněte na **položku SERVICEPRINCIPAL**a na stránce Hodnota parametru vyberte položku **ConstantValue** pro pole **Zdroje dat.** klikněte na možnost **True**; a potom klepněte na tlačítko **OK**.
1. Klepněte na **příkaz TENANTID**a proveďte následující nastavení na stránce Hodnota parametru. Po dokončení dvakrát klikněte na **OK.**

    * **Zdroj dat** – vyberte **výstup aktivity**. 
    * Seznam zdrojů dat – vyberte **možnost Získat připojení automatizace**.
    * **Cesta pole** `TenantId`-- typ . 
1. Do ovládacího prvku `Set-AzContext` Knihovna zadejte vyhledávací pole.
1. Přidejte `Set-AzContext` na plátno.
1. Vyberte `Set-AzContext` na plátně. V ovládacím podokně `Specify Subscription Id` Konfigurace zadejte do pole **Popisek.**
1. Klikněte na **Parametry** a zobrazí se stránka Konfigurace parametru aktivity.
1. Rutina `Set-AzContext` má více sad parametrů a před zadáním hodnot parametrů je třeba ji vybrat. Klepněte na **položku Parametr Set** a vyberte **položku SubscriptionId**.
1. Parametry pro tuto sadu parametrů jsou zobrazeny na stránce Konfigurace parametru aktivity. Klepněte na **položku SubscriptionID**.
1. Na stránce Hodnota parametru vyberte **proměnnou prostředek** pro pole **zdroj dat** a vyberte **AzureSubscriptionId** ze zdrojového seznamu. Po dokončení dvakrát klikněte na **OK.**
1. Najeďte `Login to Azure` přes, dokud se ve spodní části obrazce nezobrazí kruh. Klepněte na kruh a `Specify Subscription Id`přetáhněte šipku na . V tomto okamžiku by měl váš runbook vypadat takto.

    ![Konfigurace ověření runbooku](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Krok 7 – přidání aktivity pro spuštění virtuálního počítače

Teď musíte přidat `Start-AzVM` aktivitu ke spuštění virtuálního počítače. Můžete si vybrat libovolný virtuální počítač ve vašem předplatném Azure a prozatím pevně kódujete jeho název do rutiny [Start-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0)

1. Do ovládacího prvku `Start-Az` Knihovna zadejte vyhledávací pole.
2. Přidejte `Start-AzVM` na plátno a klepněte na něj a přetáhněte ho pod něj `Specify Subscription Id`.
1. Najeďte `Specify Subscription Id` přes, dokud se ve spodní části obrazce nezobrazí kruh. Klepněte na kruh a `Start-AzVM`přetáhněte šipku na .
1. Vyberte `Start-AzVM`. Kliknutím na **Parametry** a potom na **Nastavit parametry** zobrazíte sady aktivity.
1. Vyberte **Položku ResourceGroupNameNameNamepro** sadu parametrů. Pole **ResourceGroupName** a **Name** mají vedle sebe vykřičníky označující, že se jedná o požadované parametry. Všimněte si, že obě pole očekávají hodnoty řetězce.
1. Vyberte **Název**. Zvolte **výraz Prostředí PowerShell** pro pole **Zdroj dat.** Pro virtuální počítač, který používáte ke spuštění tohoto runbooku, zadejte název počítače obklopený dvojitými uvozovkami. Klikněte na tlačítko **OK**.
1. Vyberte **ResourceGroupName**. Použijte hodnotu **výrazu Prostředí PowerShell** pro pole **Zdroje dat** a zadejte název skupiny prostředků obklopené dvojitými uvozovkami. Klikněte na tlačítko **OK**.
1. Klikněte na **podokno Test,** abyste mohli otestovat runbook.
1. Chcete-li zahájit test, klepněte na tlačítko **Start.** Po dokončení se ujistěte, že virtuální hod začal. V tomto okamžiku by měl váš runbook vypadat takto.

    ![Konfigurace ověření runbooku](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Krok 8 – Přidání dalších vstupních parametrů

Runbook aktuálně spustí virtuální počítač ve skupině prostředků, `Start-AzVM` kterou jste zadali pro rutinu. Runbook bude užitečnější, pokud při spuštění runbooku zadáte název i skupinu prostředků. Přidáme vstupní parametry do runbooku, abychom tuto funkci poskytli.

1. Otevřete grafický editor kliknutím na **upravit** v podokně **MyFirstRunbook-Graphical.**
1. Vyberte **Vstup a výstup** a pak **Přidejte vstup** otevřete podokno Vstupní parametr knihy Runbook.
1. V zadaných polích proveďte následující nastavení a klepněte na tlačítko **OK**.
   * **Name** -- `VMName`zadejte .
   * **Type** -- zachovat nastavení řetězce.
   * **Povinné** -- změňte hodnotu na **Ano**.
1. Vytvořte druhý povinný `ResourceGroupName` vstupní parametr s názvem a klepnutím na tlačítko **OK** zavřete podokno Vstup a Výstup.<br> ![Vstupní parametry runbooku](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Vyberte `Start-AzVM` aktivitu a klepněte na tlačítko **Parametry**.
1. Změňte pole **Zdroj dat** pro **name** to **Runbook input**. Pak vyberte **Položku VMName**.
1. Změňte pole **Zdroj dat** pro **ResourceGroupName** na **vstup runbooku** a pak vyberte **ResourceGroupName**.<br> ![Parametry Start-AzVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Uložte runbook a otevřete testovací podokno. Teď můžete zadat hodnoty dvou vstupních proměnných, které v testu používáte.
1. Zavřete testovací podokno.
1. Kliknutím na **Publikovat** publikujte novou verzi runbooku.
1. Zastavte virtuální hod, který jste spustili dříve.
1. Kliknutím na **Spustit** spustíte runbook. Zadejte hodnoty `VMName` pro `ResourceGroupName` a pro virtuální ho, který se chystáte spustit.
1. Po dokončení runbooku se ujistěte, že byl spuštěn virtuální ho.

## <a name="step-9---create-a-conditional-link"></a>Krok 9 – vytvoření podmíněného propojení

Nyní můžete upravit runbook tak, aby se pokusí spustit pouze virtuální ho, pokud ještě není spuštěn. To provést přidáním [rutiny Get-AzVM,](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) která načte stav na úrovni instance virtuálního soudu. Pak můžete přidat modul kódu pracovního postupu prostředí PowerShell s názvem `Get Status` s fragmentem kódu Prostředí PowerShell, abyste zjistili, jestli je stav virtuálního aplikace spuštěný nebo zastavený. Podmíněné propojení `Get Status` z modulu se spustí `Start-AzVM` pouze v případě, že je zastaven aktuální spuštěný stav. Na konci tohoto postupu vaše runbook `Write-Output` používá rutinu k výstupu zprávy, která vás informuje, pokud byl virtuální počítač úspěšně spuštěn.

1. Otevřete **MyFirstRunbook-Graphical** v grafickém editoru.
1. Odstraňte propojení `Specify Subscription Id` `Start-AzVM` mezi a kliknutím na něj a stiskněte **klávesu Delete**.
1. Do ovládacího prvku `Get-Az` Knihovna zadejte vyhledávací pole.
1. Přidejte `Get-AzVM` na plátno.
1. Výběrem `Get-AzVM` a kliknutím na **Nastavit parametr** zobrazíte sady rutiny. 
1. Vyberte sadu parametrů **GetVirtualMachineInResourceGroupNameParamSet**. Pole **ResourceGroupName** a **Name** mají vedle sebe vykřičníky, které označují, že určují požadované parametry. Všimněte si, že obě pole očekávají hodnoty řetězce.
1. V **části Zdroj dat** pro **Název**vyberte **vstup runbooku**a potom **položku VMName**. Klikněte na tlačítko **OK**.
1. V **části Zdroj dat** pro **ResourceGroupName**vyberte **položku Vstup aplikace Runbook**a potom **položku ResourceGroupName**. Klikněte na tlačítko **OK**.
1. V části **Zdroj dat** pro **Stav**vyberte **Hodnotu Konstanta**a potom **True**. Klikněte na tlačítko **OK**.
1. Vytvořte odkaz `Specify Subscription Id` `Get-AzVM`z do .
1. V ovládacím prvku Knihovna rozbalte **ovládací prvek runbooku** a přidejte **kód** na plátno.  
1. Vytvořte odkaz `Get-AzVM` `Code`z do .  
1. Klepněte na `Code` tlačítko a v podokně Konfigurace změňte popisek na **Získat stav**.
1. Vyberte `Code` a zobrazí se stránka Editor kódu.  
1. Na stránku editoru vložte následující fragment kódu.

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. Vytvořte odkaz `Get Status` `Start-AzVM`z do .

    ![Runbook s modulem kódu](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Vyberte odkaz a v konfiguračním podokně změňte **podmínku Použít** na **ano**. Všimněte si, že odkaz se stane přerušovanou čárou, což znamená, že cílová aktivita se spustí pouze v případě, že se podmínka vyřeší na hodnotu true.  
1. Do **výrazu** `$ActivityOutput['Get Status'] -eq "Stopped"`Podmínka zadejte . `Start-AzVM`teď běží jenom v případě, že je zastaven virtuální ms.
1. V ovládacím prvku Knihovna rozbalte položku **Rutiny** a potom **Microsoft.PowerShell.Utility**.
1. Přidejte `Write-Output` na plátno dvakrát.
1. U `Write-Output` prvního ovládacího prvku klikněte na **Parametry** a změňte hodnotu **Popisek** na **Upozornit spuštění virtuálního soudu**.
1. V **případě objektu InputObject**změňte **zdroj dat** na výraz Prostředí **PowerShell**a zadejte výraz `$VMName successfully started.`.
1. U druhého `Write-Output` ovládacího prvku klikněte na **Parametry** a změňte hodnotu **Label** na **Upozornit spuštění virtuálního počítače se nezdařilo**.
1. V **případě objektu InputObject**změňte **zdroj dat** na výraz Prostředí **PowerShell**a zadejte výraz `$VMName could not start.`.
1. Vytvořte `Start-AzVM` odkazy `Notify VM Started` `Notify VM Start Failed`z do a .
1. Vyberte odkaz `Notify VM Started` na a změňte **použít podmínku** na hodnotu true.
1. Do **výrazu**Podmínka `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`zadejte . Tento `Write-Output` ovládací prvek se nyní spustí pouze v případě, že virtuální ho spouští úspěšně.
1. Vyberte odkaz `Notify VM Start Failed` na a změňte **použít podmínku** na hodnotu true.
1. Do pole **výrazu** `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`Podmínka zadejte . Tento `Write-Output` ovládací prvek se nyní spustí pouze v případě, že virtuální virtuální byl úspěšně spuštěn. Runbook by měl vypadat jako na následujícím obrázku.

    ![Runbook s aktivitou Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Uložte runbook a otevřete testovací podokno.
1. Spusťte runbook se zastaveným virtuálním počítačem a počítač by se měl spustit.

## <a name="next-steps"></a>Další kroky

* Další informace o vytváření grafických funkcí najdete [v tématu Vytváření grafikva v Azure Automation](automation-graphical-authoring-intro.md).
* První kroky s powershellovými runbooky najdete v článku [Můj první powershellový runbook](automation-first-runbook-textual-powershell.md).
* Pokud chcete začít s runbooky pracovního postupu prostředí PowerShell, [přečtěte si první runbook pracovního postupu Prostředí PowerShell](automation-first-runbook-textual.md).