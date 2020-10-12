---
title: Vytvoření grafického Runbooku v Azure Automation
description: Tento článek vás seznámí s vytvořením, otestováním a publikováním jednoduché grafické sady Runbook v Azure Automation.
keywords: runbook, šablona sady runbook, automatizace sady runbook, runbook azure
services: automation
ms.subservice: process-automation
ms.date: 09/15/2020
ms.topic: tutorial
ms.openlocfilehash: 81dc23c208ca9fb292c849bdf35d8b91311ed9ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987646"
---
# <a name="tutorial-create-a-graphical-runbook"></a>Kurz: Vytvoření grafického Runbooku

V tomto kurzu se seznámíte s vytvořením [grafického runbooku](../automation-runbook-types.md#graphical-runbooks) ve službě Azure Automation. Můžete vytvářet a upravovat grafické a grafické Runbooky pracovních postupů PowerShellu pomocí grafického editoru v Azure Portal. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření jednoduché grafické sady Runbook
> * Testování a publikování Runbooku
> * Spuštění a sledování stavu úlohy Runbooku
> * Aktualizace Runbooku na spuštění virtuálního počítače Azure s parametry Runbooku a podmíněnými odkazy

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free).
* [Účet Automation](../index.yml), abyste si mohli runbook podržet a mohli ověřovat prostředky Azure. Tento účet musí mít oprávnění ke spuštění a zastavení virtuálního počítače.
* Virtuální počítač Azure. Vzhledem k tomu, že tento počítač zastavíte a spustíte, neměli by se jednat o produkční virtuální počítač.
* V případě potřeby [importujte moduly Azure](../shared-resources/modules.md) nebo [moduly pro aktualizaci](../automation-update-azure-modules.md) na základě používaných rutin.

## <a name="step-1---create-runbook"></a>Krok 1 – vytvoření runbooku

Začněte vytvořením jednoduchého Runbooku, který vypíše text `Hello World` .

1. Na webu Azure Portal otevřete účet Automation.

    Stránka účtu Automation nabízí rychlý přehled prostředků v tomto účtu. Už byste tam měli mít nějaké prostředky. Většina těchto assetů je moduly, které jsou automaticky zahrnuté do nového účtu Automation. Měli byste mít také Asset přihlašovacích údajů přidružený k vašemu předplatnému.

2. V části **Automatizace procesu** vyberte **Runbooky** a otevřete seznam runbooků.

3. Kliknutím na **vytvořit Runbook**vytvořte nový Runbook.

4. Dejte runbooku název **MyFirstRunbook-Graphical**.

5. V takovém případě se chystáte vytvořit [grafický Runbook](../automation-graphical-authoring-intro.md). Vyberte **grafický** **typ Runbooku**.

    ![Nový runbook](../media/automation-tutorial-runbook-graphical/create-new-runbook.png)

6. Kliknutím na **Vytvořit** vytvoříte runbook a otevřete grafický editor.

## <a name="step-2---add-activities"></a>Krok 2 – Přidání aktivit

Ovládací prvek Knihovna na levé straně editoru vám umožní vybrat aktivity, které chcete do runbooku přidat. Chystáte se přidat `Write-Output` rutinu do výstupního textu ze sady Runbook.

1. V ovládacím prvku Knihovna klikněte na vyhledávací pole a zadejte `write-output` . Výsledky hledání jsou zobrazené na následujícím obrázku.

    ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

2. Přejděte do dolní části seznamu. Klikněte pravým tlačítkem na **Write-Output** a vyberte **Přidat na plátno**. Případně můžete kliknout na tři tečky (...) vedle názvu rutiny a pak vybrat **Přidat na plátno**.

3. Klikněte na aktivitu **Write-Output** na plátně. Tato akce otevře stránku řízení konfigurace, která umožňuje konfiguraci aktivity.

4. Pole **popisku** je standardně nastavené na název rutiny, ale můžete ho změnit na smysluplnější. Změňte na `Write Hello World to output` .

5. Klikněte na tlačítko **Parametry** a zadejte hodnoty parametru rutiny.

   Některé rutiny mají více sad parametrů a Vy musíte vybrat, která z nich se má použít. V tomto případě `Write-Output` má pouze jednu sadu parametrů.

6. Vyberte `InputObject` parametr. Toto je parametr, který použijete k určení textu, který se odešle do výstupního datového proudu.

7. Rozevírací nabídka **zdroje dat** poskytuje zdroje, které můžete použít k naplnění hodnoty parametru. V této nabídce vyberte **powershellový výraz**.

   Můžete použít výstup z takových zdrojů jako jinou aktivitu, Asset Automation nebo PowerShellový výraz. V tomto případě je výstup pouze `Hello World` . Můžete použít powershellový výraz a zadat řetězec.

8. Do pole **výraz** zadejte `Hello World` a dvakrát klikněte na tlačítko **OK** , čímž se vrátíte na plátno.

9. Kliknutím na **Uložit** runbook uložte.

## <a name="step-3---test-the-runbook"></a>Krok 3 – otestování runbooku

Před publikováním Runbooku, aby byl dostupný v produkčním prostředí, byste ho měli otestovat, abyste se ujistili, že funguje správně. Testování Runbooku spustí svou verzi konceptu a umožňuje interaktivní zobrazení výstupu.

1. Vyberte **podokno test** a otevřete tak podokno test.

2. Kliknutím na **Spustit** spustíte test. Měla by to být jediná povolená možnost.

3. Všimněte si, že se vytvoří [úloha Runbooku](../automation-runbook-execution.md) a její stav se zobrazí v podokně.

   Stav úlohy se spustí s `Queued` označením, že úloha čeká na zpřístupnění pracovního procesu Runbooku v cloudu. Stav se změní, `Starting` když pracovní proces tuto úlohu vystaví. Nakonec se stav projeví, `Running` když se Runbook skutečně začne spouštět.

4. Po dokončení úlohy Runbooku zobrazí podokno test svůj výstup. V takovém případě se zobrazí `Hello World` .

    ![Výstup runbooku Hello World](../media/automation-tutorial-runbook-graphical/runbook-test-results.png)

5. Zavřete testovací podokno a vraťte se na plátno.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4 – publikování a spuštění runbooku

Sada Runbook, kterou jste vytvořili, je stále v režimu konceptu. Abyste ho mohli spustit v produkčním prostředí, je potřeba ho publikovat. Když runbook publikujete, přepíšete vydanou verzi verzí v režimu konceptu. V tomto případě zatím ještě publikovanou verzi nemáte, protože jste runbook teprve vytvořili.

1. Vyberte **publikovat** a publikujte Runbook a po zobrazení výzvy klikněte na **Ano** .

2. Posunutím doleva zobrazíte sadu Runbook na stránce sady Runbook a Všimněte si, že hodnota **stav vytváření** je nastavena na **Publikováno**.

3. Posuňte se zpět doprava, abyste si zobrazili stránku **MyFirstRunbook-Graphics**.

   Možnosti v horní části umožňují spuštění sady Runbook nyní, naplánování budoucího času spuštění nebo vytvoření [Webhooku](../automation-webhooks.md) , aby bylo možné sadu Runbook spustit prostřednictvím volání protokolu HTTP.

4. Po zobrazení výzvy ke spuštění Runbooku vyberte **Start** a potom na **Ano** .

5. Otevře se podokno úlohy pro vytvořenou úlohu Runbooku. Ověřte, že se v poli **stav úlohy** zobrazuje **dokončeno**.

6. Kliknutím na **výstup** otevřete stránku výstup, kde vidíte `Hello World` zobrazené.

7. Zavřete výstupní stránku.

8. Klikněte na **Všechny protokoly** a otevřete podokno Datové proudy, které patří k úloze runbooku. Měla by se zobrazit jenom `Hello World` ve výstupním datovém proudu.

    Všimněte si, že podokno streamy může zobrazit další datové proudy pro úlohu Runbooku, jako jsou například podrobné a chybové streamy, pokud je do nich zapisuje sada Runbook.

9. Zavřete podokno datové proudy a podokno úlohy, které se vrátí na stránku MyFirstRunbook-Graphical.

10. Chcete-li zobrazit všechny úlohy pro sadu Runbook, vyberte v části **prostředky**položku **úlohy** . Na stránce úlohy jsou uvedeny všechny úlohy, které sada Runbook vytvořila. V seznamu by se měla zobrazit jenom jedna úloha, protože jste úlohu spustili jenom jednou.

11. Kliknutím na název úlohy otevřete stejné podokno úloh, které jste zobrazili při spuštění Runbooku. V tomto podokně si můžete zobrazit podrobnosti o všech úlohách vytvořených pro sadu Runbook.

## <a name="step-5---create-variable-assets"></a>Krok 5 – vytvoření proměnných assetů

Runbook jste otestovali a publikovali, ale zatím neděláme žádnou užitečnou správu prostředků Azure. Před konfigurací Runbooku na ověřování musíte vytvořit proměnnou, která bude uchovávat ID předplatného, nastavit aktivitu k ověření a pak na proměnnou odkazovat. Zahrnutí odkazu na kontext předplatného umožňuje snadnou práci s více předplatnými.

1. Zkopírujte ID předplatného z možnosti **předplatná** v navigačním podokně.

2. Na stránce účty Automation v části **sdílené prostředky**vyberte **proměnné** .

3. Vyberte **přidat proměnnou**.

4. Na stránce Nová proměnná proveďte v zadaných polích následující nastavení.

    * **Název** – zadejte `AzureSubscriptionId` .
    * **Hodnota** – zadejte ID předplatného.
    * **Typ** – Nechejte vybraný řetězec.
    * **Šifrování** – použijte výchozí hodnotu.

5. Kliknutím na **Vytvořit** vytvořte proměnnou.

## <a name="step-6---add-authentication"></a>Krok 6 – přidání ověřování

Teď, když máte proměnnou, která bude uchovávat ID předplatného, můžete sadu Runbook nakonfigurovat tak, aby se ověřovala pomocí přihlašovacích údajů spustit jako pro vaše předplatné. Provedete to přidáním připojení spustit jako pro Azure jako prostředku. Musíte také přidat rutinu [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) a rutinu [set-AzContext](/powershell/module/az.accounts/Set-AzContext) na plátno.

>[!NOTE]
>Pro PowerShellové Runbooky `Add-AzAccount` a `Add-AzureRMAccount` jsou aliasy pro `Connect-AzAccount` . Všimněte si, že tyto aliasy nejsou k dispozici pro vaše grafické Runbooky. Grafická sada Runbook může používat pouze `Connect-AzAccount` sebe sama.

1. Přejděte na Runbook a na stránce MyFirstRunbook-Graphical vyberte **Upravit** .

2. Tuto položku už nepotřebujete `Write Hello World to output` . Stačí kliknout na tři tečky a vybrat **Odstranit**.

3. V ovládacím prvku knihovna rozbalte **prostředky**a pak **připojení**. Přidejte `AzureRunAsConnection` na plátno tak, že vyberete **Přidat na plátno**.

4. Přejmenujte `AzureRunAsConnection` na `Get Run As Connection` .

5. V ovládacím prvku knihovna zadejte `Connect-AzAccount` do vyhledávacího pole.

6. Přidat `Connect-AzAccount` na plátno.

7. Najeďte myší na další, `Get Run As Connection` dokud se v dolní části obrazce nezobrazí kruh. Kliknutím na kroužek a přetažením šipky `Connect-AzAccount` vytvoříte odkaz. Sada Runbook se spustí `Get Run As Connection` a potom spustí `Connect-AzAccount` .

    ![Vytvoření propojení mezi aktivitami](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

8. Na plátně vyberte `Connect-AzAccount` . V podokně ovládacího prvku konfigurace zadejte do pole **popisek** **přihlášení do Azure** .

9. Klikněte na **parametry**a zobrazí se stránka Konfigurace parametru aktivity.

10. `Connect-AzAccount`Rutina má více sad parametrů a před zadáním hodnot parametrů je nutné ji vybrat. Klikněte na **sada parametrů** a pak vyberte **ServicePrincipalCertificateWithSubscriptionId**.

11. Parametry pro tuto sadu parametrů se zobrazí na stránce Konfigurace parametru aktivity. Klikněte na **APPLICATIONID**.

    ![Přidat parametry účtu Azure](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

12. Na stránce hodnota parametru proveďte následující nastavení a potom klikněte na tlačítko **OK**.

   * **Zdroj dat** – vyberte **výstup aktivity**.
   * Seznam zdrojů dat – vyberte **získat připojení Automation**.
   * **Cesta k poli** -- `ApplicationId` Type Určujete název vlastnosti pro cestu k poli, protože výstupem aktivity je objekt s více vlastnostmi.

13. Klikněte na **CERTIFICATETHUMBPRINT**a na stránce hodnota parametru proveďte následující nastavení a pak klikněte na **OK**.

    * **Zdroj dat** – vyberte **výstup aktivity**.
    * Seznam zdrojů dat – vyberte **získat připojení Automation**.
    * **Cesta k poli** -- `CertificateThumbprint` Type

14. Klikněte na **SERVICEPRINCIPAL**a na stránce hodnota parametru vyberte **ConstantValue** pro pole **zdroj dat** . Klikněte na možnost **pravda**a pak klikněte na tlačítko **OK**.

15. Klikněte na **TENANTID**a na stránce hodnota parametru proveďte následující nastavení. Až budete hotovi, klikněte dvakrát na **OK** .

    * **Zdroj dat** – vyberte **výstup aktivity**.
    * Seznam zdrojů dat – vyberte **získat připojení Automation**.
    * **Cesta k poli** -- `TenantId` Type

16. V ovládacím prvku knihovna zadejte `Set-AzContext` do vyhledávacího pole.

17. Přidat `Set-AzContext` na plátno.

18. Vyberte `Set-AzContext` na plátně. V podokně ovládacího prvku konfigurace zadejte `Specify Subscription Id` do pole **popisek** .

19. Klikněte na **parametry** a zobrazí se stránka Konfigurace parametru aktivity.

20. `Set-AzContext`Rutina má více sad parametrů a před zadáním hodnot parametrů je nutné ji vybrat. Klikněte na **sada parametrů** a potom vyberte **SubscriptionId**.

21. Parametry pro tuto sadu parametrů se zobrazí na stránce Konfigurace parametru aktivity. Klikněte na **SubscriptionId**.

22. Na stránce hodnota parametru vyberte položku **prostředek** pro pole **zdroj dat** a v seznamu zdroj vyberte možnost **AzureSubscriptionId** . Až budete hotovi, klikněte dvakrát na **OK** .

23. Najeďte myší na další, `Login to Azure` dokud se v dolní části obrazce nezobrazí kruh. Klikněte na kroužek a přetáhněte šipku na `Specify Subscription Id` . Sada Runbook by měla v tomto okamžiku vypadat jako v následujícím.

    :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-auth-config.png" alt-text="Snímek obrazovky sady Runbook po přetažení šipky na ' zadejte ID předplatného '.":::

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Krok 7 – přidání aktivity pro spuštění virtuálního počítače

Nyní musíte přidat `Start-AzVM` aktivitu pro spuštění virtuálního počítače. V předplatném Azure si můžete vybrat libovolný virtuální počítač a teď jste zakódujeme jeho název do rutiny [Start-AzVM](/powershell/module/az.compute/start-azvm) .

1. V ovládacím prvku knihovna zadejte `Start-Az` do vyhledávacího pole.

2. Přidejte `Start-AzVM` na plátno a potom na něj klikněte a přetáhněte ho pod ním `Specify Subscription Id` .

3. Najeďte myší na další, `Specify Subscription Id` dokud se v dolní části obrazce nezobrazí kruh. Klikněte na kroužek a přetáhněte šipku na `Start-AzVM` .

4. Vyberte `Start-AzVM`. Klikněte na **parametry** a pak na **sada parametrů** , aby se zobrazily sady pro aktivitu.

5. Jako sadu parametrů vyberte **ResourceGroupNameParameterSetName** . Pole **ResourceGroupName** a **Name** obsahují vykřičník vedle sebe, aby označovali, že se jedná o požadované parametry. Všimněte si, že obě pole očekávají hodnoty řetězce.

6. Vyberte **Název**. V poli **zdroj dat** vyberte **powershellový výraz** . Pro virtuální počítač, který používáte ke spuštění této sady Runbook, zadejte název počítače, který je ohraničený dvojitými uvozovkami. Klikněte na **OK**.

7. Vyberte **ResourceGroupName**. Pro pole **zdroje dat** použijte **výraz PowerShell** Value a zadejte název skupiny prostředků, která je ohraničená dvojitými uvozovkami. Klikněte na **OK**.

8. Klikněte na **testovací podokno** , abyste mohli Runbook otestovat.

9. Kliknutím na **Spustit spusťte** test. Až se dokončí, ujistěte se, že je virtuální počítač spuštěný. Sada Runbook by měla v tomto okamžiku vypadat jako v následujícím.

    ![Výstup Start-AzVM Runbooku](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Krok 8 – přidání dalších vstupních parametrů

Runbook aktuálně spouští virtuální počítač ve skupině prostředků, kterou jste zadali pro `Start-AzVM` rutinu. Sada Runbook bude užitečnější, pokud při spuštění Runbooku zadáte jak název, tak i skupinu prostředků. Pojďme do Runbooku přidat vstupní parametry, které tuto funkci poskytují.

1. Kliknutím na tlačítko **Upravit** na stránce MyFirstRunbook-Graphical otevřete grafický editor.

2. Vyberte **vstup a výstup** a pak **přidejte vstup** . otevře se podokno vstupní parametr Runbooku.

3. V zadaných polích proveďte následující nastavení a pak klikněte na **OK**.
   * **Název** – zadejte `VMName` .
   * **Typ** – zachovejte nastavení řetězce.
   * **Povinné** – změňte hodnotu na **Ano**.

4. Vytvořte druhý povinný vstupní parametr s názvem `ResourceGroupName` a potom kliknutím na tlačítko **OK** zavřete podokno vstup a výstup.

    ![Vstupní parametry runbooku](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

5. Vyberte `Start-AzVM` aktivitu a pak klikněte na **parametry**.

6. Změňte pole **zdroj dat** pro **název** na **vstup ze sady Runbook**. Pak vyberte **VMName**.

7. Změňte pole **zdroj dat** pro **ResourceGroupName** na **vstup z Runbooku** a potom vyberte **ResourceGroupName**.

    ![Parametry Start-AzVM](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

8. Uložte runbook a otevřete testovací podokno. Teď můžete zadat hodnoty dvou vstupních proměnných, které v testu používáte.

9. Zavřete testovací podokno.

10. Kliknutím na **Publikovat** publikujte novou verzi runbooku.

11. Zastavte virtuální počítač, který jste spustili dřív.

12. Kliknutím na **Spustit** spustíte runbook. Zadejte hodnoty pro `VMName` a `ResourceGroupName` pro virtuální počítač, který se chystáte spustit.

13. Po dokončení Runbooku se ujistěte, že je virtuální počítač spuštěný.

## <a name="step-9---create-a-conditional-link"></a>Krok 9 – vytvoření podmíněného propojení

Nyní můžete sadu Runbook upravit tak, aby se pokusila spustit virtuální počítač pouze v případě, že ještě není spuštěn. Udělejte to přidáním rutiny [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) , která načte stav na úrovni instance virtuálního počítače. Pak můžete přidat modul kódu pracovního postupu PowerShellu `Get Status` s názvem fragment kódu PowerShellu k určení, jestli je stav virtuálního počítače spuštěný nebo zastavený. Podmíněné propojení z modulu se `Get Status` spustí pouze `Start-AzVM` v případě, že je aktuální stav spuštění zastaveno. Na konci tohoto postupu sada Runbook používá `Write-Output` rutinu k výstupu zprávy, která vás informuje o tom, jestli byl virtuální počítač úspěšně spuštěn.

1. V grafickém editoru otevřete **MyFirstRunbook-Graphics** .

2. Odstraňte propojení mezi `Specify Subscription Id` a `Start-AzVM` kliknutím na něj a stisknutím klávesy **Delete**.

3. V ovládacím prvku knihovna zadejte `Get-Az` do vyhledávacího pole.

4. Přidat `Get-AzVM` na plátno.

5. Vyberte `Get-AzVM` a potom klikněte na **sada parametrů** , abyste zobrazili sady pro rutinu.

6. Vyberte sadu parametrů **GetVirtualMachineInResourceGroupNameParamSet**. Pole **ResourceGroupName** a **Name** obsahují vykřičník vedle sebe, což značí, že určují požadované parametry. Všimněte si, že obě pole očekávají hodnoty řetězce.

7. V části **zdroj dat** pro **název**vyberte **vstup ze sady Runbook**a pak **VMName**. Klikněte na **OK**.

8. V části **zdroj dat** pro **ResourceGroupName**vyberte **vstup ze sady Runbook**a pak **ResourceGroupName**. Klikněte na **OK**.

9. V části **zdroj dat** pro **stav**vyberte **konstantní hodnota**a potom na **true (pravda**). Klikněte na **OK**.

10. Vytvořte odkaz z `Specify Subscription Id` na `Get-AzVM` .

11. V ovládacím prvku knihovna rozbalte položku **řízení Runbooku** a přidejte **kód** na plátno.  

12. Vytvořte odkaz z `Get-AzVM` na `Code` .  

13. Klikněte na `Code` a v podokně Konfigurace Změňte popisek na **získat stav**.

14. Vyberte `Code` a zobrazí se stránka Editor kódu.  

15. Vložte následující fragment kódu do stránky editoru.

    ```powershell
    $Statuses = $ActivityOutput['Get-AzVM'].Statuses
    $StatusOut = ""
    foreach ($Status in $Statuses) {
      if($Status.Code -eq "Powerstate/running")
        {$StatusOut = "running"}
      elseif ($Status.Code -eq "Powerstate/deallocated")
        {$StatusOut = "stopped"}
    }
    $StatusOut
    ```

16. Vytvořte odkaz z `Get Status` na `Start-AzVM` .

    ![Runbook s modulem kódu](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

17. Vyberte odkaz a v podokně Konfigurace změňte **Podmínka použít** na **Ano**. Všimněte si, že odkaz se zobrazí jako přerušovaná čára, což značí, že se cílová aktivita spustí pouze v případě, že se podmínka přeloží na hodnotu true.  

18. Jako **výraz podmínky**zadejte `$ActivityOutput['Get Status'] -eq "Stopped"` . `Start-AzVM` Teď se spustí jenom v případě, že je virtuální počítač zastavený.

19. V ovládacím prvku Knihovna rozbalte položku **Rutiny** a potom **Microsoft.PowerShell.Utility**.

20. `Write-Output`Dvakrát přidejte na plátno.

21. V případě prvního `Write-Output` ovládacího prvku klikněte na **parametry** a změňte hodnotu **popisek** na **oznamuje spuštění virtuálního počítače**.

22. Pro **položky InputObject**změňte **zdroj dat** na **powershellový výraz**a zadejte výraz `$VMName successfully started.` .

23. V druhém `Write-Output` ovládacím prvku klikněte na **parametry** a změňte hodnotu **popisek** na **oznamovat spuštění virtuálního počítače se nezdařilo**.

24. Pro **položky InputObject**změňte **zdroj dat** na **powershellový výraz**a zadejte výraz `$VMName could not start` .

25. Vytvořte odkazy z `Start-AzVM` na `Notify VM Started` a `Notify VM Start Failed` .

26. Vyberte odkaz `Notify VM Started` a změňte **podmínku použít** na hodnotu true.

27. Pro **výraz podmínky**zadejte `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` . Tento `Write-Output` ovládací prvek se teď spustí jenom v případě, že se virtuální počítač úspěšně spustí.

28. Vyberte odkaz `Notify VM Start Failed` a změňte **podmínku použít** na hodnotu true.

29. Do pole **výraz podmínky** zadejte `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` . Tento `Write-Output` ovládací prvek se teď spustí jenom v případě, že se virtuální počítač nespustil úspěšně. Váš Runbook by měl vypadat jako na následujícím obrázku.

    ![Runbook s aktivitou Write-Output](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

30. Uložte runbook a otevřete testovací podokno.

31. Spusťte sadu Runbook se zastaveným virtuálním počítačem a počítač by měl být spuštěn.

## <a name="next-steps"></a>Další kroky

* Další informace o vytváření grafického obsahu najdete [v tématu Vytvoření grafického Runbooku v Azure Automation](../automation-graphical-authoring-intro.md).
* Pokud chcete začít pracovat s Runbooky PowerShellu, přečtěte si téma [Vytvoření Runbooku PowerShellu](automation-tutorial-runbook-textual-powershell.md).
* Informace o tom, jak začít s Runbooky pracovních postupů PowerShellu, najdete v tématu [Vytvoření Runbooku pracovního postupu PowerShellu](automation-tutorial-runbook-textual.md).
* Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](/powershell/module/az.automation).
