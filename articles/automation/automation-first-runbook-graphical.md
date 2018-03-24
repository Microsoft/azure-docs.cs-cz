---
title: Můj první grafický runbook ve službě Azure Automation
description: Kurz vás provede vytvořením, otestováním a publikováním jednoduchého grafického runbooku.
keywords: runbook, šablona sady runbook, automatizace sady runbook, runbook azure
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 4f0db9a7381468216c6b9a6e46b4e8f0fe7db59f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="my-first-graphical-runbook"></a>Můj první grafický runbook

> [!div class="op_single_selector"]
> * [Grafický](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Pracovní postup PowerShellu](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

V tomto kurzu se seznámíte s vytvořením [grafického runbooku](automation-runbook-types.md#graphical-runbooks) ve službě Azure Automation. Začnete s jednoduchým runbookem pro testování a publikování, zatímco se budete učit, jak sledovat stav úlohy runbooku. Potom runbook upravíte, aby skutečně spravoval prostředky Azure, v tomto případě virtuální počítač Azure. Na závěr kurzu runbook rozšíříte přidáním parametrů runbooku a podmíněných propojení.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](automation-offering-get-started.md), abyste si mohli runbook podržet a mohli ověřovat prostředky Azure. Tento účet musí mít oprávnění ke spuštění a zastavení virtuálního počítače.
* Virtuální počítač Azure. Tento počítač zastavíte a spustíte, proto to nesmí být produkční virtuální počítač.

## <a name="create-runbook"></a>Vytvoření runbooku

Začnete vytvořením jednoduchého runbooku, který zobrazí text *Hello World*.

1. Na webu Azure Portal otevřete účet Automation.

   Stránka účtu Automation nabízí rychlý přehled prostředků v tomto účtu. Už byste tam měli mít nějaké assety. Většina z těchto assetů jsou moduly, které jsou automaticky obsažené v novém účtu Automation. Také potřebujete asset přihlašovacích údajů, který je uvedený v [požadavcích](#prerequisites).

2. V části **SPRÁVA PROCESŮ** vyberte **Runbooky** a otevřete seznam runbooků.
3. Vytvořit novou sadu runbook tak, že vyberete **+ přidat runbook**, pak klikněte na tlačítko **vytvořit nový runbook**.
4. Dejte runbooku název *MyFirstRunbook-Graphical*.
5. V tomto případě vytvoříte [grafický runbook](automation-graphical-authoring-intro.md), proto jako **Typ runbooku** vyberte **Grafický**.<br> ![Nový runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Kliknutím na **Vytvořit** vytvoříte runbook a otevřete grafický editor.

## <a name="add-activities"></a>Přidání aktivit

Ovládací prvek Knihovna na levé straně editoru vám umožní vybrat aktivity, které chcete do runbooku přidat. Přidáte rutinu **Write-Output**, která zajistí textový výstup z runbooku.

1. V ovládacím prvku Knihovna klikněte do textového pole hledání a zadejte text **Write-Output**. Výsledky hledání jsou znázorněné na následujícím obrázku: <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Přejděte do dolní části seznamu. Můžete buď kliknout pravým tlačítkem na **Write-Output** a vybrat **Přidat na plátno**, nebo kliknout na tři tečky vedle rutiny a vybrat **Přidat na plátno**.
1. Klikněte na aktivitu **Write-Output** na plátně. Tato akce otevře stránce ovládacího prvku konfigurace, které umožňuje konfigurovat aktivity.
1. **Popisek** obsahuje ve výchozím nastavení název rutiny, ale můžete ho změnit na něco popisnějšího. Změňte ho na *Textový výstup Hello World*.
1. Klikněte na tlačítko **Parametry** a zadejte hodnoty parametru rutiny.

   Některé rutiny obsahují více sad parametrů, proto musíte vybrat, kterou chcete používat. V tomto případě **Write-Output** obsahuje jenom jednu sadu parametrů, takže není nutné žádnou vybírat.

1. Vyberte parametr **InputObject**. V tomto parametru budete zadávat text, který se odešle do výstupního datového proudu.
1. V rozevíracím seznamu **Zdroj dat** vyberte **Powershellový výraz**. Rozevírací seznam **Zdroj dat** obsahuje různé zdroje, které můžete použít k naplnění hodnoty parametru.

   Můžete použít výstup z těchto zdrojů, například další aktivitu, asset Automation nebo powershellový výraz. V tomto případě je výstupem jenom *Hello World*. Můžete použít powershellový výraz a zadat řetězec.<br>

1. Do pole **Výraz** zadejte *"Hello World"* a potom dvakrát klikněte na tlačítko **OK**, a vraťte se tak na plátno.
1. Kliknutím na **Uložit** runbook uložte.

## <a name="test-the-runbook"></a>Otestování runbooku

Před publikováním runbooku, které ho zpřístupní v produkčním prostředí, byste měli runbook otestovat a ujistit se, že funguje správně. Když runbook testujete, spustíte jeho  verzi **Koncept** a interaktivně zobrazíte jeho výsledek.

1. Vyberte **testovací podokno** chcete otevřít stránku testu.
1. Kliknutím na **Spustit** spustíte test. Měla by to být jediná povolená možnost.
1. Vytvoří se [úloha runbooku](automation-runbook-execution.md) a její stav se zobrazí v podokně.

   Počáteční stav úlohy bude *Ve frontě*. To označuje, že čekáte na zpřístupnění pracovního procesu runbooku v cloudu. Když pracovní proces úlohu převezme, změní se stav na *Spouštění*, a když je runbook skutečně spuštěn, změní se stav na *Spuštěno*.

1. Po dokončení úlohy runbooku se zobrazí jeho výstup. V tomto případě se zobrazí *Hello World*.<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Zavřete stránku Test se vraťte plátno.

## <a name="publish-and-start-the-runbook"></a>Publikování a spuštění sady runbook

Vytvořený runbook je stále v režimu konceptu. Abyste ho mohli spustit v produkčním prostředí, je potřeba ho publikovat. Když runbook publikujete, přepíšete vydanou verzi verzí v režimu konceptu. V tomto případě zatím ještě publikovanou verzi nemáte, protože jste runbook teprve vytvořili.

1. Vyberte **publikovat** Publikovat sadu runbook a potom **Ano** po zobrazení výzvy.
1. Pokud jste posunete doleva, abyste runbook viděli v **sady Runbook** stránce se zobrazí **stav vytváření** z **publikováno**.
1. Posuňte se zpět doprava, abyste viděli stránce **MyFirstRunbook**.

   Možnosti v horní části nám umožňují spuštění runbooku, naplánování jeho spuštění někdy v budoucnu nebo vytvoření [webhooku](automation-webhooks.md), který umožní spuštění prostřednictvím volání protokolu HTTP.

1. Vyberte **spustit** a potom **Ano** po zobrazení výzvy pro spuštění sady runbook.
1. Na stránce úlohy je otevřené úlohy runbooku, který byl vytvořen. Ověřte, že se jako **Stav úlohy** zobrazuje **Dokončeno**.
1. Když se jako stav runbooku zobrazí *Dokončeno*, klikněte na **Výstup**. **Výstup** otevření stránky a zobrazí se *Hello, World* v podokně.
1. Zavřete stránku výstup.
1. Klikněte na tlačítko **všechny protokoly** chcete otevřít stránku datové proudy k úloze runbooku. Ve výstupním datovém proudu byste měli vidět jenom text *Hello World*, ale můžou se zobrazit i jiné datové proudy z úlohy runbooku, například Podrobný nebo Chyba, pokud do nich runbook zapisuje.
1. Zavřete stránku všechny protokoly a stránku úloha a vraťte na stránku MyFirstRunbook.
1. Chcete-li zobrazit všechny úlohy pro runbook zavřete **úlohy** a vyberte **úlohy** pod **prostředky**. Vypíšou se všechny úlohy, které tento runbook vytvořil. Ve výpisu by se měla zobrazit pouze jedna úloha, protože jste ji spustili jenom jednou.
1. Na tuto úlohu můžete kliknout a otevřít podokno Úloha, které jste zobrazili při spuštění runbooku. Pomocí této možnosti se můžete vrátit v čase a zobrazit si podrobnosti libovolné úlohy, která byla pro konkrétní runbook vytvořena.

## <a name="create-variable-assets"></a>Vytvoření proměnných assetů

Runbook jste otestovali a publikovali, ale zatím nedělá nic užitečného. Chcete po něm, aby spravoval prostředky Azure. Než runbook nakonfigurujete pro ověřování, vytvoříte proměnnou, která bude uchovávat ID předplatného a bude na něj odkazovat poté, co v kroku 6 níže nastavíte aktivitu k ověření. Zahrnutí odkazu na kontext předplatného usnadňuje práci s několika předplatnými. Než budete pokračovat, zkopírujte si ID předplatného z možnosti Předplatná, která se nachází v navigačním podokně.

1. Na stránce účty Automation vyberte **proměnné** pod **SDÍLENÉ prostředky**.
1. Vyberte **přidat proměnnou**.
1. Na stránce nové proměnné v **název** zadejte **AzureSubscriptionId** a v **hodnotu** pole zadejte ID předplatného. Ponechte *řetězec* možnosti **Typ** a výchozí hodnotu možnosti **Šifrování**.
1. Kliknutím na **Vytvořit** vytvořte proměnnou.

## <a name="add-authentication"></a>Přidání ověřování

Teď, když máte proměnnou, do které se uloží ID předplatného, můžete runbook nakonfigurovat k ověřování pomocí přihlašovacích údajů Spustit jako, které jsou uvedené v [požadavcích](#prerequisites). Provedete to přidáním **assetu** připojení Spustit v Azure jako a přidáním rutiny **Add-AzureRMAccount** na plátno.

1. Přejděte zpět do sady runbook a vyberte **upravit** na stránce MyFirstRunbook.
1. Není nutné **zápisu textový výstup Hello World** už nepotřebujeme, takže klikněte na výpustky (...) a vyberte **odstranit**.
1. V ovládacím prvku Knihovna rozbalte **ASSETY**, **Připojení** a výběrem možnosti **Přidat na plátno** přidejte **AzureRunAsConnection** na plátno.
1. V ovládacím prvku Knihovna zadejte do textového pole hledání text **Add-AzureRmAccount**.
1. Přidejte **Add-AzureRmAccount** na plátno.
1. Najeďte ukazatelem myši nad položku **Get Run As Connection**, dokud se v dolní části obrazce nezobrazí kruh. Klikněte na kruh a přetáhněte šipku na **Add-AzureRmAccount**. Šipka, kterou jste vytvořili, je *odkazem*. Runbook se spustí pomocí aktivity **Get Run As Connection**. Potom spusťte rutinu **Add-AzureRmAccount**.<br> ![Vytvoření propojení mezi aktivitami](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. Na plátně vyberte **Add-AzureRmAccount** a v podokně ovládacího prvku Konfigurace zadejte do textového pole **Popisek** text **Přihlášení k Azure**.
1. Klikněte na tlačítko **parametry** a zobrazí se stránka konfigurace parametru aktivity.
1. **Add-AzureRmAccount** obsahuje několik sad parametrů, takže před zadáním hodnot parametrů musíte nejprve jednu sadu vybrat. Klikněte na **Sada parametrů** a potom vyberte sadu **ServicePrincipalCertificate**.
1. Po výběru sady parametrů se zobrazí na stránce konfigurace parametru aktivity. Klikněte na **APPLICATIONID**.<br> ![Přidání parametrů účtu Azure RM](media/automation-first-runbook-graphical/add-azurermaccount-params.png)
1. Na stránce hodnota parametru vyberte **výstup aktivity** pro **zdroj dat** a vyberte **získat připojení spustit jako** ze seznamu v **cesta pole** zadejte **ApplicationId**a potom klikněte na **OK**. Název vlastnosti pro cestu pole zadáváte kvůli tomu, že výstupem aktivity je objekt s více vlastnostmi.
1. Klikněte na tlačítko **CERTIFICATETHUMBPRINT**a na stránce hodnota parametru vyberte **výstup aktivity** pro **zdroj dat**. V seznamu vyberte **Get Run As Connection**, do textového pole **Cesta pole** zadejte **CertificateThumbrprint**. Potom klikněte na **OK**.
1. Klikněte na tlačítko **SERVICEPRINCIPAL**a na stránce hodnota parametru vyberte **ConstantValue** pro **zdroj dat**, klikněte na možnost **True**, a pak klikněte na **OK**.
1. Klikněte na tlačítko **TENANTID**a na stránce hodnota parametru vyberte **výstup aktivity** pro **zdroj dat**. V seznamu vyberte **Get Run As Connection**, do textového pole **Cesta pole** zadejte **TenantId** a potom dvakrát klikněte na tlačítko **OK**.
1. V ovládacím prvku Knihovna zadejte do textového pole hledání text **Set-AzureRmContext**.
1. Přidejte **Set-AzureRmContext** na plátno.
1. Na plátně vyberte **Set-AzureRmContext** a v podokně ovládacího prvku Konfigurace zadejte do textového pole **Popisek** text **Zadat ID předplatného**.
1. Klikněte na tlačítko **parametry** a zobrazí se stránka konfigurace parametru aktivity.
1. **Set-AzureRmContext** obsahuje několik sad parametrů, takže před zadáním hodnot parametrů musíte nejprve jednu sadu vybrat. Klikněte na **Sada parametrů** a potom vyberte sadu **SubscriptionId**.
1. Po výběru sady parametrů se zobrazí na stránce konfigurace parametru aktivity. Klikněte na **ID předplatného**.
1. Na stránce hodnota parametru vyberte **variabilní prostředek** pro **zdroj dat** a vyberte **AzureSubscriptionId** ze seznamu a pak klikněte na tlačítko **OK** dvakrát.
1. Pozastavte ukazatel myši nad možností **Přihlášení k Azure**, dokud se v dolní části obrazce nezobrazí kruh. Klikněte na kruh a přetáhněte šipku na **Zadat ID předplatného**.

Váš runbook by měl v tuto chvíli vypadat následovně: <br>![Konfigurace ověření runbooku](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="add-activity-to-start-a-vm"></a>Přidání aktivity pro spuštění virtuálního počítače

Tady přidáte aktivitu **Start-AzureRmVM**, která spustí virtuální počítač. V rámci vašeho předplatného Azure můžete vybrat jakýkoli virtuální počítač, ale prozatím jeho název pevně zakódujete do rutiny.

1. V ovládacím prvku Knihovna zadejte do textového pole hledání text **Start-AzureRm**.
2. Přidejte aktivitu **Start-AzureRmVM** na plátno a potom na ni klikněte a přetáhněte ji pod **Zadat ID předplatného**.
1. Pozastavte ukazatel myši nad možností **Zadat ID předplatného**, dokud se v dolní části obrazce nezobrazí kruh. Klikněte na kruh a přetáhněte šipku na **Start-AzureRmVM**.
1. Vyberte **Start-AzureRmVM**. Pokud chcete zobrazit sady, které patří k **Start-AzureRmVM**, klikněte na **Parametry** a potom na **Sada parametrů**. Vyberte sadu parametrů **ResourceGroupNameParameterSetName**. Vedle **ResourceGroupName** a **Název** se zobrazuje vykřičník. To znamená, že tyto parametry jsou povinné. Všimněte si také, že oba očekávají řetězcové hodnoty.
1. Vyberte **Název**. V části **Zdroj dat** vyberte **Powershellový výraz** a zadejte název virtuálního počítače (v uvozovkách), který pomocí tohoto runbooku spustíte. Klikněte na **OK**.
1. Vyberte **ResourceGroupName**. V části **Zdroj dat** použijte **Powershellový výraz** a zadejte název skupiny prostředků (v uvozovkách). Klikněte na **OK**.
1. Klikněte na testovací podokno, abyste mohli runbook otestovat.
1. Kliknutím na **Spustit** spustíte test. Po jeho dokončení zkontrolujte, jestli se virtuální počítač spustil.

Váš runbook by měl v tuto chvíli vypadat následovně: <br>![Konfigurace ověření runbooku](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="add-additional-input-parameters"></a>Přidání dalších vstupních parametrů

Náš runbook nyní spouští virtuální počítač ve skupině prostředků, kterou jste určili v rutině **Start-AzureRmVM**. Runbook by mohl být užitečnější, kdybychom mohli obojí zadat při spuštění runbooku. Teď do runbooku přidáte vstupní parametry, které tuto funkci zajistí.

1. Kliknutím na **Upravit** v podokně **MyFirstRunbook** otevřete grafický editor.
1. Vyberte **vstup a výstup** a potom **přidat vstup** otevřete podokno vstupní parametr Runbooku.
1. V části **Název** zadejte *VMName*. U možnosti **Typ** nechte *řetězec*, ale možnost **Povinné** změňte na *Ano*. Klikněte na **OK**.
1. Vytvořte druhý povinný vstupní parametr s názvem *ResourceGroupName* a potom kliknutím na tlačítko **OK** zavřete podokno **Vstup a výstup**.<br> ![Vstupní parametry runbooku](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Vyberte aktivitu **Start-AzureRmVM** a potom klikněte na **Parametry**.
1. Změňte **Zdroj dat** možnosti **Název** na **Vstup z runbooku** a potom vyberte **VMName**.
1. Změňte **Zdroj dat** možnosti **ResourceGroupName** na **Vstup z runbooku** a potom vyberte **ResourceGroupName**.<br> ![Parametry Start-AzureVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Uložte runbook a otevřete testovací podokno. Teď můžete zadat hodnoty dvou vstupních proměnných, které v testu používáte.
1. Zavřete testovací podokno.
1. Kliknutím na **Publikovat** publikujte novou verzi runbooku.
1. Zastavte virtuální počítač, který jste v předchozím kroku spustili.
1. Kliknutím na **Spustit** spustíte runbook. Zadejte **VMName** a **ResourceGroupName** pro virtuální počítač, který chcete spustit.
1. Po dokončení runbooku zkontrolujte, jestli se virtuální počítač spustil.

## <a name="create-a-conditional-link"></a>Vytvoření podmíněného propojení

Teď runbook upravíte, aby se pokusil virtuální počítač spustit jenom v případě, že ještě není spuštěný. To provedete tak, že do runbooku přidáte rutinu **Get-AzureRmVM**, která získá stav úrovně instance virtuálního počítače. Potom přidáte modul s kódem pracovního postupu PowerShellu s názvem **Get Status** s fragmentem kódu PowerShellu, který bude zjišťovat, jestli je virtuální počítač ve spuštěném nebo zastaveném stavu. Podmíněné propojení z modulu **Get Status** spustí rutinu **Start-AzureRmVM** jenom v případě, že je počítač v zastaveném stavu. Nakonec pomocí rutiny PowerShellu Write-Output odešlete výstup v podobě zprávy, která vás bude informovat o tom, jestli se virtuální počítač úspěšně spustil nebo ne.

1. Otevřete **MyFirstRunbook** v grafickém editoru.
1. Kliknutím na propojení a stisknutím klávesy *Delete* odebere propojení mezi **Zadat ID předplatného** a **Start-AzureRmVM**.
1. V ovládacím prvku Knihovna zadejte do textového pole hledání text **Get-AzureRm**.
1. Přidejte **Get-AzureRmVM** na plátno.
1. Pokud chcete zobrazit sady, které patří ke **Get-AzureRmVM**, vyberte **Get-AzureRmVM** a potom **Sada parametrů**. Vyberte sadu parametrů **GetVirtualMachineInResourceGroupNameParamSet**. Vedle **ResourceGroupName** a **Název** se zobrazuje vykřičník. To znamená, že tyto parametry jsou povinné. Všimněte si také, že oba očekávají řetězcové hodnoty.
1. V části **Zdroj dat** u možnosti **Název** vyberte **Vstup z runbooku** a potom vyberte **VMName**. Klikněte na **OK**.
1. V části **Zdroj dat** u možnosti **ResourceGroupName** vyberte **Vstup z runbooku** a potom vyberte **ResourceGroupName**. Klikněte na **OK**.
1. V části **Zdroj dat** u možnosti **Stav** vyberte **Konstantní hodnota** a potom klikněte na **Pravda**. Klikněte na **OK**.
1. Propojte **Zadat ID předplatného** s **Get-AzureRmVM**.
1. V ovládacím prvku Knihovna rozbalte **Ovládací prvek runbooku** a přidejte **Kód** na plátno.  
1. Propojte **Get-AzureRmVM** s možností **Kód**.  
1. Klikněte na **Kód** a v podokně Konfigurace změňte popisek na **Získání stavu**.
1. Vyberte **kód** parametr a **Editor kódu** se zobrazí stránka.  
1. Do editoru kódu vložte následující fragment kódu:

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. Propojte modul **Získání stavu** se **Start-AzureRmVM**.<br> ![Runbook s modulem kódu](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Vyberte propojení a v podokně Konfigurace změňte možnost **Použít podmínku** na **Ano**. Všimněte si, že propojení se změní na přerušovanou čáru, která označuje, že cílová aktivita se spustí jenom v případě, že se podmínka přeloží na hodnotu pravda.  
1. V případě **výrazu podmínky** zadejte text *$ActivityOutput['Get Status'] -eq "Stopped"*. Rutina **Start-AzureRmVM** se spustí jenom v případě, že je virtuální počítač zastavený.
1. V ovládacím prvku Knihovna rozbalte položku **Rutiny** a potom **Microsoft.PowerShell.Utility**.
1. Přidejte **Write-Output** dvakrát na plátno.
1. V prvním ovládacím prvku **Write-Output** klikněte na **Parametry** a změňte hodnotu **Popisek** na *Oznámit spuštění virtuálního počítače*.
1. U položky **InputObject** změňte**Zdroj dat** na **Powershellový výraz** a zadejte výraz *"$VMName successfully started."*.
1. V druhém ovládacím prvku **Write-Output** klikněte na **Parametry** a změňte hodnotu **Popisek** na *Oznámit neúspěšné spuštění virtuálního počítače*.
1. U položky **InputObject** změňte **Zdroj dat** na **Powershellový výraz** a zadejte výraz *"$VMName could not start."*.
1. Propojte **Start-AzureRmVM** s **Oznámit spuštění virtuálního počítače** a **Oznámit neúspěšné spuštění virtuálního počítače**.
1. Vyberte propojení s **Oznámit spuštění virtuálního počítače** a změňte možnost **Použít podmínku** na **Pravda**.
1. V případě **výrazu podmínky** zadejte *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*. Tato rutina Write-Output se teď spustí jenom v případě, že je virtuální počítač úspěšně spuštěn.
1. Vyberte propojení na **Oznámit neúspěšné spuštění virtuálního počítače** a změňte možnost **Použít podmínku** na **Pravda**.
1. V případě **výrazu podmínky** zadejte *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*. Tato rutina Write-Output se teď spustí jenom v případě, že virtuální počítač není úspěšně spuštěn. Runbook by měl vypadat podobně jako na následujícím obrázku: <br> ![Runbook s aktivitou Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Uložte runbook a otevřete testovací podokno.
1. Spusťte runbook se zastaveným virtuálním počítačem  a virtuální počítač by se měl spustit.

## <a name="next-steps"></a>Další postup

* Další informace o vytváření grafického obsahu najdete v článku [Vytváření grafického obsahu v Azure Automation](automation-graphical-authoring-intro.md).
* První kroky s powershellovými runbooky najdete v článku [Můj první powershellový runbook](automation-first-runbook-textual-powershell.md).
* První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md).

