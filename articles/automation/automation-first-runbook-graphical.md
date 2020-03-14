---
title: Můj první grafický Runbook v Azure Automation
description: Kurz vás provede vytvořením, otestováním a publikováním jednoduchého grafického runbooku.
keywords: runbook, šablona sady runbook, automatizace sady runbook, runbook azure
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: 8b836ebc0adc6f0616d28b16bfb743dfc4553d1a
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367411"
---
# <a name="my-first-graphical-runbook"></a>Můj první grafický runbook

> [!div class="op_single_selector"]
> * [Grafický](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Pracovní postup PowerShellu](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

V tomto kurzu se seznámíte s vytvořením [grafického runbooku](automation-runbook-types.md#graphical-runbooks) ve službě Azure Automation. Začněte s jednoduchým runbookm, který můžete testovat a publikovat, a Naučte se sledovat stav úlohy Runbooku. Pak upravte Runbook tak, aby skutečně spravoval prostředky Azure. v tomto případě se spustí virtuální počítač Azure. Dokončete kurz, aby se Runbook lépe robustní přidáním parametrů Runbooku a podmíněných propojení.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](automation-offering-get-started.md), abyste si mohli runbook podržet a mohli ověřovat prostředky Azure. Tento účet musí mít oprávnění ke spuštění a zastavení virtuálního počítače.
* Virtuální počítač Azure. Vzhledem k tomu, že tento počítač zastavíte a spustíte, neměli by se jednat o produkční virtuální počítač.

## <a name="step-1---create-runbook"></a>Krok 1 – vytvoření runbooku

Začněte vytvořením jednoduchého Runbooku, který vypíše text `Hello World`.

1. Na webu Azure Portal otevřete účet Automation. 

    Stránka účtu Automation nabízí rychlý přehled prostředků v tomto účtu. Už byste tam měli mít nějaké prostředky. Většina těchto assetů je moduly, které jsou automaticky zahrnuté do nového účtu Automation. Měli byste mít také Asset přihlašovacích údajů přidružený k vašemu předplatnému.
2. V části **Automatizace procesu** vyberte **Runbooky** a otevřete seznam runbooků.
3. Kliknutím na **vytvořit Runbook**vytvořte nový Runbook.
4. Dejte runbooku název **MyFirstRunbook-Graphical**.
5. V takovém případě se chystáte vytvořit [grafický Runbook](automation-graphical-authoring-intro.md). Vyberte **grafický** **typ Runbooku**.<br> ![Nový runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Kliknutím na **Vytvořit** vytvoříte runbook a otevřete grafický editor.

## <a name="step-2---add-activities"></a>Krok 2 – Přidání aktivit

Ovládací prvek Knihovna na levé straně editoru vám umožní vybrat aktivity, které chcete do runbooku přidat. Chystáte se přidat rutinu `Write-Output` do výstupního textu ze sady Runbook.

1. V ovládacím prvku Knihovna klikněte do vyhledávacího pole a zadejte `write-output`. Výsledky hledání jsou zobrazené na následujícím obrázku. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Přejděte do dolní části seznamu. Klikněte pravým tlačítkem na **Write-Output** a vyberte **Přidat na plátno**. Případně můžete kliknout na tři tečky (...) vedle názvu rutiny a pak vybrat **Přidat na plátno**.
1. Klikněte na aktivitu **Write-Output** na plátně. Tato akce otevře stránku řízení konfigurace, která umožňuje konfiguraci aktivity.
1. Pole **popisku** je standardně nastavené na název rutiny, ale můžete ho změnit na smysluplnější. Změňte ji na `Write Hello World to output`.
1. Klikněte na tlačítko **Parametry** a zadejte hodnoty parametru rutiny.

   Některé rutiny mají více sad parametrů a Vy musíte vybrat, která z nich se má použít. V takovém případě má `Write-Output` jenom jednu sadu parametrů.

1. Vyberte parametr `InputObject`. Toto je parametr, který použijete k určení textu, který se odešle do výstupního datového proudu.
1. Rozevírací nabídka **zdroje dat** poskytuje zdroje, které můžete použít k naplnění hodnoty parametru. V této nabídce vyberte **powershellový výraz**. 

   Můžete použít výstup z takových zdrojů jako jinou aktivitu, Asset Automation nebo PowerShellový výraz. V tomto případě je výstup pouze `Hello World`. Můžete použít powershellový výraz a zadat řetězec.<br>

1. Do pole **výraz** zadejte `Hello World` a dvakrát klikněte na tlačítko **OK** , čímž se vrátíte na plátno.
1. Kliknutím na **Uložit** runbook uložte.

## <a name="step-3---test-the-runbook"></a>Krok 3 – otestování runbooku

Před publikováním Runbooku, aby byl dostupný v produkčním prostředí, byste ho měli otestovat, abyste se ujistili, že funguje správně. Testování Runbooku spustí svou verzi konceptu a umožňuje interaktivní zobrazení výstupu.

1. Vyberte **podokno test** a otevřete tak podokno test.
1. Kliknutím na **Spustit** spustíte test. Měla by to být jediná povolená možnost.
1. Všimněte si, že se vytvoří [úloha Runbooku](automation-runbook-execution.md) a její stav se zobrazí v podokně.

   Stav úlohy se spustí jako `Queued`, což znamená, že úloha čeká na zpřístupnění pracovního procesu Runbooku v cloudu. Stav se změní na `Starting`, když pracovní proces tuto úlohu vystaví. Nakonec se stav `Running`, když se Runbook skutečně začne spouštět.

1. Po dokončení úlohy Runbooku zobrazí podokno test svůj výstup. V takovém případě se zobrazí `Hello World`.<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Zavřete testovací podokno a vraťte se na plátno.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4 – publikování a spuštění runbooku

Sada Runbook, kterou jste vytvořili, je stále v režimu konceptu. Abyste ho mohli spustit v produkčním prostředí, je potřeba ho publikovat. Když runbook publikujete, přepíšete vydanou verzi verzí v režimu konceptu. V tomto případě zatím ještě publikovanou verzi nemáte, protože jste runbook teprve vytvořili.

1. Vyberte **publikovat** a publikujte Runbook a po zobrazení výzvy klikněte na **Ano** .
1. Posunutím doleva zobrazíte sadu Runbook na stránce sady Runbook a Všimněte si, že hodnota **stav vytváření** je nastavena na **Publikováno**.
1. Posuňte se zpět doprava, abyste si zobrazili stránku **MyFirstRunbook-Graphics**.

   Možnosti v horní části umožňují spuštění sady Runbook nyní, naplánování budoucího času spuštění nebo vytvoření [Webhooku](automation-webhooks.md) , aby bylo možné sadu Runbook spustit prostřednictvím volání protokolu HTTP.

1. Po zobrazení výzvy ke spuštění Runbooku vyberte **Start** a potom na **Ano** .
1. Otevře se podokno úlohy pro vytvořenou úlohu Runbooku. Ověřte, že se v poli **stav úlohy** zobrazuje **dokončeno**.
1. Kliknutím na **výstup** otevřete stránku výstup, kde vidíte `Hello World` zobrazené.
1. Zavřete výstupní stránku.
1. Klikněte na **Všechny protokoly** a otevřete podokno Datové proudy, které patří k úloze runbooku. Ve výstupním datovém proudu byste měli vidět jenom `Hello World`. 

    Všimněte si, že podokno streamy může zobrazit další datové proudy pro úlohu Runbooku, jako jsou například podrobné a chybové streamy, pokud je do nich zapisuje sada Runbook.
1. Zavřete podokno streamy a podokno úloh a vraťte se na stránku **MyFirstRunbook-Graphics** .
1. Chcete-li zobrazit všechny úlohy pro sadu Runbook, vyberte v části **prostředky**položku **úlohy** . Na stránce úlohy jsou uvedeny všechny úlohy, které sada Runbook vytvořila. V seznamu by se měla zobrazit jenom jedna úloha, protože jste úlohu spustili jenom jednou.
1. Kliknutím na název úlohy otevřete stejné podokno úloh, které jste zobrazili při spuštění Runbooku. V tomto podokně si můžete zobrazit podrobnosti o všech úlohách vytvořených pro sadu Runbook.

## <a name="step-5---create-variable-assets"></a>Krok 5 – vytvoření proměnných assetů

Runbook jste otestovali a publikovali, ale zatím neděláme žádnou užitečnou správu prostředků Azure. Před konfigurací Runbooku na ověřování musíte vytvořit proměnnou, která bude uchovávat ID předplatného, nastavit aktivitu k ověření a pak na proměnnou odkazovat. Zahrnutí odkazu na kontext předplatného umožňuje snadnou práci s více předplatnými.

1. Zkopírujte ID předplatného z možnosti **předplatná** v navigačním podokně.
1. Na stránce účty Automation v části **sdílené prostředky**vyberte **proměnné** .
1. Vyberte **přidat proměnnou**.
1. Na stránce Nová proměnná proveďte v zadaných polích následující nastavení.

    * **Název** – zadejte `AzureSubscriptionId`.
    * **Hodnota** – zadejte ID předplatného. 
    * **Typ** – Nechejte vybraný řetězec.
    * **Šifrování** – použijte výchozí hodnotu.
1. Kliknutím na **Vytvořit** vytvořte proměnnou.

## <a name="step-6---add-authentication"></a>Krok 6 – přidání ověřování

Teď, když máte proměnnou, která bude uchovávat ID předplatného, můžete sadu Runbook nakonfigurovat tak, aby se ověřovala pomocí přihlašovacích údajů spustit jako pro vaše předplatné. Provedete to přidáním připojení spustit jako pro Azure jako prostředku. Musíte také přidat rutinu [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) a rutinu [set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) na plátno.

>[!NOTE]
>Pro Runbooky PowerShell jsou `Add-AzAccount` a `Add-AzureRMAccount` aliasy pro `Connect-AzAccount`. Všimněte si, že tyto aliasy nejsou k dispozici pro vaše grafické Runbooky. Grafický Runbook může použít `Connect-AzAccount`sám sebe.

1. Přejděte na sadu Runbook a vyberte možnost **Upravit** na stránce **MyFirstRunbook-Graphics** .
1. Žádné další položky `Write Hello World to output` nepotřebujete. Stačí kliknout na tři tečky a vybrat **Odstranit**.
1. V ovládacím prvku knihovna rozbalte **prostředky**a pak **připojení**. Přidejte `AzureRunAsConnection` k plátnu tak, že vyberete **Přidat na plátno**.
1. V ovládacím prvku knihovna zadejte do vyhledávacího pole `Connect-AzAccount`.
1. Přidejte `Connect-AzAccount` k plátnu.
1. Najeďte ukazatelem myši nad položku **Get Run As Connection**, dokud se v dolní části obrazce nezobrazí kruh. Kliknutím na kroužek a přetažením šipky `Connect-AzAccount` vytvoříte odkaz. Sada Runbook se spustí s `Get Run As Connection` a potom spustí `Connect-AzAccount`.<br> ![Vytvoření propojení mezi aktivitami](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. Na plátně vyberte `Connect-AzAccount`. V podokně ovládacího prvku konfigurace zadejte do pole **popisek** **přihlášení do Azure** .
1. Klikněte na **parametry**a zobrazí se stránka Konfigurace parametru aktivity.
1. Rutina `Connect-AzAccount` obsahuje několik sad parametrů a před zadáním hodnot parametrů je nutné ji vybrat. Klikněte na **sada parametrů** a pak vyberte **ServicePrincipalCertificate**.
1. Parametry pro tuto sadu parametrů se zobrazí na stránce Konfigurace parametru aktivity. Klikněte na **APPLICATIONID**.<br> ![přidat parametry účtu Azure](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. Na stránce hodnota parametru proveďte následující nastavení a potom klikněte na tlačítko **OK**.

   * **Zdroj dat** – vyberte **výstup aktivity**.
   * Seznam zdrojů dat – vyberte **získat připojení spustit jako**.
   * **Cesta k poli** --zadejte `ApplicationId`. Určujete název vlastnosti pro cestu k poli, protože výstupem aktivity je objekt s více vlastnostmi.
1. Klikněte na **CERTIFICATETHUMBPRINT**a na stránce hodnota parametru proveďte následující nastavení a pak klikněte na **OK**.

    * **Zdroj dat** – vyberte **výstup aktivity**.
    * Seznam zdrojů dat – vyberte **získat připojení spustit jako**.
    * **Cesta k poli** --zadejte `CertificateThumbprint`.
1. Klikněte na **SERVICEPRINCIPAL**a na stránce hodnota parametru vyberte **ConstantValue** pro pole **zdroj dat** ; klikněte na možnost **pravda**. a pak klikněte na **OK**.
1. Klikněte na **TENANTID**a na stránce hodnota parametru proveďte následující nastavení. Až budete hotovi, klikněte dvakrát na **OK** .

    * **Zdroj dat** – vyberte **výstup aktivity**. 
    * Seznam zdrojů dat – vyberte **získat připojení spustit jako**.
    * **Cesta k poli** --zadejte `TenantId`. 
1. V ovládacím prvku knihovna zadejte do vyhledávacího pole `Set-AzContext`.
1. Přidejte `Set-AzContext` k plátnu.
1. Na plátně vyberte `Set-AzContext`. V podokně ovládacího prvku konfigurace zadejte do pole **popisek** `Specify Subscription Id`.
1. Klikněte na **parametry** a zobrazí se stránka Konfigurace parametru aktivity.
1. Rutina `Set-AzContext` obsahuje několik sad parametrů a před zadáním hodnot parametrů je nutné ji vybrat. Klikněte na **sada parametrů** a potom vyberte **SubscriptionId**.
1. Parametry pro tuto sadu parametrů se zobrazí na stránce Konfigurace parametru aktivity. Klikněte na **SubscriptionId**.
1. Na stránce hodnota parametru vyberte položku **prostředek** pro pole **zdroj dat** a v seznamu zdroj vyberte možnost **AzureSubscriptionId** . Až budete hotovi, klikněte dvakrát na **OK** .
1. Najeďte myší na `Login to Azure`, dokud se v dolní části obrazce nezobrazí kruh. Klikněte na kruh a přetáhněte šipku na `Specify Subscription Id`. 

Váš runbook by měl v tuto chvíli vypadat následovně: <br>![Konfigurace ověření runbooku](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Krok 7 – přidání aktivity pro spuštění virtuálního počítače

Nyní je nutné přidat aktivitu `Start-AzVM` pro spuštění virtuálního počítače. V předplatném Azure si můžete vybrat libovolný virtuální počítač a teď jste zakódujeme jeho název do rutiny [Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0) .

1. V ovládacím prvku knihovna zadejte do vyhledávacího pole `Start-Az`.
2. Přidejte `Start-AzVM` k plátnu a potom na něj klikněte a přetáhněte ho pod `Specify Subscription Id`.
1. Najeďte myší na `Specify Subscription Id`, dokud se v dolní části obrazce nezobrazí kruh. Klikněte na kruh a přetáhněte šipku na `Start-AzVM`.
1. Vyberte `Start-AzVM`. Klikněte na **parametry** a pak na **sada parametrů** , aby se zobrazily sady pro aktivitu.
1. Jako sadu parametrů vyberte **ResourceGroupNameParameterSetName** . Pole **ResourceGroupName** a **Name** obsahují vykřičník vedle sebe, aby označovali, že se jedná o požadované parametry. Všimněte si, že obě pole očekávají hodnoty řetězce.
1. Vyberte **Název**. V poli **zdroj dat** vyberte **powershellový výraz** . Pro virtuální počítač, který používáte ke spuštění této sady Runbook, zadejte název počítače, který je ohraničený dvojitými uvozovkami. Klikněte na tlačítko **OK**.
1. Vyberte **ResourceGroupName**. Pro pole **zdroje dat** použijte **výraz PowerShell** Value a zadejte název skupiny prostředků, která je ohraničená dvojitými uvozovkami. Klikněte na tlačítko **OK**.
1. Klikněte na **testovací podokno** , abyste mohli Runbook otestovat.
1. Kliknutím na **Spustit spusťte** test. Až se dokončí, ujistěte se, že je virtuální počítač spuštěný. 

Váš runbook by měl v tuto chvíli vypadat následovně: <br>![Konfigurace ověření runbooku](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Krok 8 – přidání dalších vstupních parametrů

Runbook aktuálně spouští virtuální počítač ve skupině prostředků, kterou jste zadali pro rutinu `Start-AzVM`. Sada Runbook bude užitečnější, pokud při spuštění Runbooku zadáte jak název, tak i skupinu prostředků. Pojďme do Runbooku přidat vstupní parametry, které tuto funkci poskytují.

1. Kliknutím na **Upravit** v podokně **MyFirstRunbook-Graphics** otevřete grafický editor.
1. Vyberte **vstup a výstup** a pak **přidejte vstup** . otevře se podokno vstupní parametr Runbooku.
1. V zadaných polích proveďte následující nastavení a pak klikněte na **OK**.
   * **Název** – zadejte `VMName`.
   * **Typ** – zachovejte nastavení řetězce.
   * **Povinné** – změňte hodnotu na **Ano**.
1. Vytvořte druhý povinný vstupní parametr s názvem `ResourceGroupName` a potom kliknutím na tlačítko **OK** zavřete podokno vstup a výstup.<br> ![Vstupní parametry runbooku](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Vyberte aktivitu `Start-AzVM` a pak klikněte na **parametry**.
1. Změňte pole **zdroj dat** pro **název** na **vstup ze sady Runbook**. Pak vyberte **VMName**.
1. Změňte pole **zdroj dat** pro **ResourceGroupName** na **vstup z Runbooku** a potom vyberte **ResourceGroupName**.<br> ![parametry Start-AzVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Uložte runbook a otevřete testovací podokno. Teď můžete zadat hodnoty dvou vstupních proměnných, které v testu používáte.
1. Zavřete testovací podokno.
1. Kliknutím na **Publikovat** publikujte novou verzi runbooku.
1. Zastavte virtuální počítač, který jste spustili dřív.
1. Kliknutím na **Spustit** spustíte runbook. Zadejte hodnoty pro `VMName` a `ResourceGroupName` pro virtuální počítač, který se chystáte spustit.
1. Po dokončení Runbooku se ujistěte, že je virtuální počítač spuštěný.

## <a name="step-9---create-a-conditional-link"></a>Krok 9 – vytvoření podmíněného propojení

Nyní můžete sadu Runbook upravit tak, aby se pokusila spustit virtuální počítač pouze v případě, že ještě není spuštěn. Udělejte to přidáním rutiny [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) , která načte stav na úrovni instance virtuálního počítače. Pak můžete přidat modul kódu pracovního postupu PowerShellu s názvem `Get Status` s fragmentem kódu PowerShellu, abyste zjistili, jestli je stav virtuálního počítače spuštěný nebo zastavený. Podmíněné propojení z modulu `Get Status` se `Start-AzVM` spustí pouze v případě, že je aktuální stav spuštění zastaveno. Na konci tohoto postupu sada Runbook používá rutinu `Write-Output` pro výstup zprávy, která vás informuje o tom, jestli byl virtuální počítač úspěšně spuštěn.

1. V grafickém editoru otevřete **MyFirstRunbook-Graphics** .
1. Odstraňte propojení mezi `Specify Subscription Id` a `Start-AzVM` kliknutím na ni a následným stisknutím klávesy **Delete**.
1. V ovládacím prvku knihovna zadejte do vyhledávacího pole `Get-Az`.
1. Přidejte `Get-AzVM` k plátnu.
1. Vyberte `Get-AzVM` a potom kliknutím na **sada parametrů** Zobrazte sady pro rutinu. 
1. Vyberte sadu parametrů **GetVirtualMachineInResourceGroupNameParamSet**. Pole **ResourceGroupName** a **Name** obsahují vykřičník vedle sebe, což značí, že určují požadované parametry. Všimněte si, že obě pole očekávají hodnoty řetězce.
1. V části **zdroj dat** pro **název**vyberte **vstup ze sady Runbook**a pak **VMName**. Klikněte na tlačítko **OK**.
1. V části **zdroj dat** pro **ResourceGroupName**vyberte **vstup ze sady Runbook**a pak **ResourceGroupName**. Klikněte na tlačítko **OK**.
1. V části **zdroj dat** pro **stav**vyberte **konstantní hodnota**a potom na **true (pravda**). Klikněte na tlačítko **OK**.
1. Vytvoří odkaz z `Specify Subscription Id` na `Get-AzVM`.
1. V ovládacím prvku knihovna rozbalte položku **řízení Runbooku** a přidejte **kód** na plátno.  
1. Vytvoří odkaz z `Get-AzVM` na `Code`.  
1. Klikněte na `Code` a v podokně Konfigurace Změňte popisek na **získat stav**.
1. Vyberte `Code` a zobrazí se stránka Editor kódu.  
1. Vložte následující fragment kódu do stránky editoru.

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

1. Vytvoří odkaz z `Get Status` na `Start-AzVM`.<br> ![Runbook s modulem kódu](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Vyberte odkaz a v podokně Konfigurace změňte **Podmínka použít** na **Ano**. Všimněte si, že odkaz se zobrazí jako přerušovaná čára, což značí, že se cílová aktivita spustí pouze v případě, že se podmínka přeloží na hodnotu true.  
1. V případě **výrazu podmínky**zadejte `$ActivityOutput['Get Status'] -eq "Stopped"`. `Start-AzVM` se teď spustí jenom v případě, že je virtuální počítač zastavený.
1. V ovládacím prvku Knihovna rozbalte položku **Rutiny** a potom **Microsoft.PowerShell.Utility**.
1. Přidejte `Write-Output` na plátno dvakrát.
1. U prvního ovládacího prvku `Write-Output` klikněte na **parametry** a změňte hodnotu **popisek** na **oznamuje spuštění virtuálního počítače**.
1. Pro **položky InputObject**, změňte **zdroj dat** na **powershellový výraz**a zadejte výraz `$VMName successfully started.`.
1. V druhém ovládacím prvku `Write-Output` klikněte na **parametry** a změňte hodnotu **popisek** na **Odeslat zprávu oznamující spuštění virtuálního počítače se nezdařilo**.
1. Pro **položky InputObject**, změňte **zdroj dat** na **powershellový výraz**a zadejte výraz `$VMName could not start.`.
1. Vytvořte odkazy z `Start-AzVM` na `Notify VM Started` a `Notify VM Start Failed`.
1. Vyberte odkaz pro `Notify VM Started` a změňte **podmínku použít** na hodnotu true.
1. Pro **výraz podmínky**zadejte `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`. Tento ovládací prvek `Write-Output` se teď spustí jenom v případě, že se virtuální počítač úspěšně spustí.
1. Vyberte odkaz pro `Notify VM Start Failed` a změňte **podmínku použít** na hodnotu true.
1. Do pole **výraz podmínky** zadejte `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`. Tato `Write-Output`á kontrola se teď spustí jenom v případě, že se virtuální počítač nespustil úspěšně. Sada Runbook by měla vypadat jako na následujícím obrázku: <br> ![Runbook s aktivitou Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Uložte runbook a otevřete testovací podokno.
1. Spusťte sadu Runbook se zastaveným virtuálním počítačem a počítač by měl být spuštěn.

## <a name="next-steps"></a>Další kroky

* Další informace o vytváření grafického obsahu najdete v tématu věnovaném [vytváření grafik v Azure Automation](automation-graphical-authoring-intro.md).
* První kroky s powershellovými runbooky najdete v článku [Můj první powershellový runbook](automation-first-runbook-textual-powershell.md).
* Informace o tom, jak začít s Runbooky pracovních postupů PowerShellu najdete v tématu [můj první Runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md).