---
title: Začínáme s konfigurací stavu Azure Automation
description: Vysvětlení a Příklady nejběžnějších úloh v konfiguraci stavu Azure Automation (DSC)
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9fce9a769dd6d88c9926913d22716666284938c1
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850954"
---
# <a name="getting-started-with-azure-automation-state-configuration"></a>Začínáme s konfigurací stavu Azure Automation

Tento článek vysvětluje, jak provádět nejběžnější úlohy s konfigurací stavu Azure Automation, jako je vytváření, importování a kompilování konfigurací, připojování počítačů ke správě a zobrazování sestav. Přehled informací o konfiguraci stavu Azure Automation najdete v tématu [Přehled konfigurace stavu Azure Automation](automation-dsc-overview.md). Dokumentaci k konfiguraci požadovaného stavu (DSC) najdete v tématu [Přehled konfigurace požadovaného stavu prostředí Windows PowerShell](/powershell/scripting/dsc/overview/overview).

Tento článek poskytuje podrobný návod k používání konfigurace stavu Azure Automation. Pokud chcete ukázkové prostředí, které je již nastaveno, bez kroků popsaných v tomto článku, můžete použít následující šablonu Správce prostředků: [Azure Automation šablona spravovaného uzlu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Tato šablona nastavuje kompletní prostředí Konfigurace stavu Azure Automation, včetně virtuálního počítače Azure spravovaného pomocí konfigurace stavu Azure Automation.

## <a name="prerequisites"></a>Předpoklady

K dokončení příkladů v tomto článku jsou potřeba následující:

- Účet Azure Automation. Pokyny k vytvoření účtu Azure Automation Spustit jako najdete v tématu [Účet Spustit jako pro Azure](automation-sec-configure-azure-runas-account.md).
- Azure Resource Manager virtuální počítač (ne Classic), na kterém běží [podporovaný operační systém](automation-dsc-overview.md#operating-system-requirements). Pokyny k vytvoření virtuálního počítače najdete v tématu [Vytvoření vašeho prvního virtuálního počítače s Windows na webu Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

## <a name="creating-a-dsc-configuration"></a>Vytvoření konfigurace DSC

Vytvoříte jednoduchou [konfiguraci DSC](/powershell/scripting/dsc/configurations/configurations) , která zajistí buď přítomnost, nebo nepřítomnost funkce **webového serveru** (IIS) v závislosti na tom, jak přiřadíte uzly.

1. Spusťte [VSCode](https://code.visualstudio.com/docs) (nebo libovolný textový editor).
1. Zadejte následující text:

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
            }
        }
    }
    ```
1. Uložte soubor jako `TestConfig.ps1`.

Tato konfigurace volá jeden prostředek v bloku každého uzlu, [prostředek WindowsFeature](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource), který zajišťuje buď přítomnost, nebo nepřítomnost funkce **webového serveru** .

## <a name="importing-a-configuration-into-azure-automation"></a>Import konfigurace do Azure Automation

Potom naimportujete konfiguraci do účtu Automation.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. Na stránce **účet Automation** vyberte v části **Správa konfigurace**možnost **Konfigurace stavu (DSC)** .
1. Na stránce **Konfigurace stavu (DSC)** klikněte na kartu **Konfigurace** a pak klikněte na **+ Přidat**.
1. Na stránce **Importovat konfiguraci** přejděte k souboru `TestConfig.ps1` ve vašem počítači.

   ![Snímek obrazovky s oknem * * import konfigurace * *](./media/automation-dsc-getting-started/AddConfig.png)

1. Klikněte na **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Zobrazení konfigurace v Azure Automation

Po importu konfigurace ji můžete zobrazit v Azure Portal.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. Na stránce **účet Automation** vyberte v části **Správa konfigurace**možnost **Konfigurace stavu (DSC)** .
1. Na stránce **Konfigurace stavu (DSC)** klikněte na kartu **Konfigurace** a pak klikněte na **TestConfig** (Jedná se o název konfigurace, kterou jste importovali v předchozím postupu).
1. Na stránce **Konfigurace TestConfig** klikněte na **Zobrazit zdroj konfigurace**.

   ![Snímek obrazovky okna konfigurace TestConfig](./media/automation-dsc-getting-started/ViewConfigSource.png)

   Otevře se stránka **zdroje konfigurace TestConfig** , ve které se zobrazí kód PowerShellu pro konfiguraci.

## <a name="compiling-a-configuration-in-azure-automation"></a>Kompilace konfigurace v Azure Automation

Než budete moci použít požadovaný stav na uzel, konfigurace DSC definující tento stav musí být zkompilována do jedné nebo více konfigurací uzlů (dokument MOF) a umístěna na Automatizace DSC vyžádaného serveru. Podrobnější popis kompilace konfigurací v konfiguraci stavu Azure Automation najdete v tématu [kompilace konfigurací v konfiguraci stavu Azure Automation](automation-dsc-compile.md).
Další informace o kompilaci konfigurací najdete v tématu [Konfigurace DSC](/powershell/scripting/dsc/configurations/configurations).

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. Na stránce **účet Automation** klikněte v části **Správa konfigurace**na **Konfigurace stavu (DSC)** .
1. Na stránce **Konfigurace stavu (DSC)** klikněte na kartu **Konfigurace** a pak klikněte na **TestConfig** (název dříve importované konfigurace).
1. Na stránce **Konfigurace TestConfig** klikněte na **kompilovat**a potom klikněte na **Ano**. Tím se spustí úloha kompilace.

   ![Snímek obrazovky s tlačítkem kompilace stránky konfigurace TestConfig](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Při kompilaci konfigurace v Azure Automation automaticky nasadí všechny vytvořené konfigurace uzlů soubory MOF na server vyžádané replikace.

## <a name="viewing-a-compilation-job"></a>Zobrazení úlohy kompilace

Po spuštění kompilace je můžete zobrazit na dlaždici **úlohy kompilace** na stránce **Konfigurace** . Dlaždice **úlohy kompilace** zobrazuje aktuálně spuštěné, dokončené a neúspěšné úlohy. Po otevření stránky úlohy kompilace se zobrazí informace o této úloze, včetně všech zjištěných chyb nebo upozornění, vstupních parametrů použitých v konfiguraci a protokolů kompilace.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. Na stránce **účet Automation** klikněte v části **Správa konfigurace**na **Konfigurace stavu (DSC)** .
1. Na stránce **Konfigurace stavu (DSC)** klikněte na kartu **Konfigurace** a pak klikněte na **TestConfig** (název dříve importované konfigurace).
1. V části **úlohy kompilace**vyberte úlohu kompilace, kterou chcete zobrazit. Otevře se stránka **úloha kompilace** s označením datum zahájení úlohy kompilace.

   ![Snímek obrazovky se stránkou úlohy kompilace](./media/automation-dsc-getting-started/CompilationJob.png)

1. Kliknutím na libovolnou dlaždici na stránce **úlohy kompilace** zobrazíte další podrobnosti o úloze.

## <a name="viewing-node-configurations"></a>Zobrazení konfigurací uzlu

Úspěšné dokončení úlohy kompilace vytvoří jednu nebo více nových konfigurací uzlu. Konfigurace uzlu je dokument MOF, který je nasazený na serveru Pull a připravený k vyžádání a použití v jednom nebo několika uzlech. Konfigurace uzlů můžete zobrazit ve svém účtu Automation na stránce **Konfigurace stavu (DSC)** . Konfigurace uzlu má název, který má formu *ConfigurationName*. *Node*.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. V okně **účet Automation** klikněte v části **Správa konfigurace**na **Konfigurace stavu (DSC)** .
1. Na stránce **Konfigurace stavu (DSC)** klikněte na kartu **zkompilované konfigurace** .

   ![Snímek obrazovky s kartou kompilovaných konfigurací](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>Připojování virtuálního počítače Azure pro správu s konfigurací stavu Azure Automation

Konfiguraci stavu Azure Automation můžete použít ke správě virtuálních počítačů Azure (klasických i Správce prostředků), místních virtuálních počítačů, počítačů se systémem Linux, virtuálních počítačů s AWS a místních fyzických počítačů. V tomto článku se naučíte, jak začlenit jenom Azure Resource Manager virtuálních počítačů. Informace o připojování jiných typů počítačů najdete v tématu věnovaném [připojování počítačů ke správě podle konfigurace stavu Azure Automation](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>Připojení virtuálního počítače s Azure Resource Manager pro správu pomocí konfigurace stavu Azure Automation

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. V okně **účet Automation** klikněte v části **Správa konfigurace**na **Konfigurace stavu (DSC)** .
1. Na stránce **Konfigurace stavu (DSC)** na kartě **uzly** klikněte na **+ Přidat**.

   ![Snímek stránky uzlů DSC zvýrazněním tlačítka Přidat virtuální počítač Azure](./media/automation-dsc-getting-started/OnboardVM.png)

1. Na stránce **Virtual Machines** vyberte svůj virtuální počítač.
1. Na stránce s podrobnostmi **virtuálního počítače** klikněte na **+ připojit**.

   > [!IMPORTANT]
   > Musí se jednat o Azure Resource Manager virtuální počítač s [podporovaným operačním systémem](automation-dsc-overview.md#operating-system-requirements).

2. Na stránce **registrace** vyberte název konfigurace uzlu, kterou chcete použít pro virtuální počítač v poli **název konfigurace uzlu** . Zadání názvu v tomto okamžiku je volitelné. Po připojení uzlu můžete změnit konfiguraci přiřazeného uzlu.
   V **případě potřeby zaškrtněte uzel restartovat**a pak klikněte na tlačítko **OK**.

   ![Snímek obrazovky okna registrace](./media/automation-dsc-getting-started/RegisterVM.png)

   Konfigurace uzlu, kterou jste zadali, se na virtuální počítač aplikuje v intervalech určených **četností režimu konfigurace**a virtuální počítač zkontroluje aktualizace konfigurace uzlu v intervalech určených **frekvencí aktualizace**. Další informace o tom, jak se tyto hodnoty používají, najdete v tématu [Konfigurace místní Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

Azure spustí proces připojování virtuálního počítače. Po dokončení se virtuální počítač zobrazí na kartě **uzly** stránky **Konfigurace stavu (DSC)** v účtu Automation.

## <a name="viewing-the-list-of-managed-nodes"></a>Zobrazení seznamu spravovaných uzlů

Na kartě **uzly** stránky **Konfigurace stavu (DSC)** si můžete zobrazit seznam všech počítačů, které jsou zaregistrované pro správu v účtu Automation.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. V okně **účet Automation** klikněte v části **Správa konfigurace**na **Konfigurace stavu (DSC)** .
1. Na stránce **Konfigurace stavu (DSC)** klikněte na kartu **uzly** .

## <a name="viewing-reports-for-managed-nodes"></a>Zobrazení sestav pro spravované uzly

Při každé konfiguraci stavu Azure Automation provádí kontrolu konzistence na spravovaném uzlu, uzel pošle zprávu o stavu zpět na server vyžádané replikace. Tyto sestavy můžete zobrazit na stránce pro daný uzel.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. V okně **účet Automation** klikněte v části **Správa konfigurace**na **Konfigurace stavu (DSC)** .
1. Na stránce **Konfigurace stavu (DSC)** klikněte na kartu **uzly** . Tady můžete zobrazit přehled stavu konfigurace a podrobnosti o jednotlivých uzlech.

   ![Snímek stránky s uzlem](./media/automation-dsc-getting-started/NodesTab.png)

1. Na kartě **uzly** kliknutím na záznam uzlu otevřete vytváření sestav. Klikněte na sestavu, ve které chcete zobrazit podrobnosti o dalších sestavách.

   ![Snímek obrazovky okna sestavy](./media/automation-dsc-getting-started/NodeReport.png)

V okně pro jednotlivou sestavu můžete zobrazit následující informace o stavu pro odpovídající kontrolu konzistence:

- Stav sestavy – zda je uzel "kompatibilní", "konfigurace" selhala ", nebo je uzel" nekompatibilní "(Pokud je uzel v režimu **ApplyandMonitor** a počítač není v požadovaném stavu).
- Čas spuštění kontroly konzistence.
- Celková doba běhu pro kontrolu konzistence.
- Typ kontroly konzistence.
- Jakékoli chyby, včetně kódu chyby a chybové zprávy.
- Všechny prostředky DSC používané v konfiguraci a stav každého prostředku (zda je uzel v požadovaném stavu pro daný prostředek) – můžete kliknout na jednotlivé prostředky a získat podrobnější informace o tomto prostředku.
- Název, IP adresa a režim konfigurace uzlu.

Můžete také kliknout na **Zobrazit hrubou sestavu** a zobrazit tak skutečná data, která uzel odesílá na server.
Další informace o použití těchto dat najdete v tématu [použití serveru sestav DSC](/powershell/scripting/dsc/pull-server/reportserver).

Může trvat nějakou dobu, než se uzel připojí, než bude k dispozici první sestava. Po zprovoznění uzlu možná budete muset počkat až 30 minut od první sestavy.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Změna přiřazení uzlu k jiné konfiguraci uzlu

Uzel můžete přiřadit k použití jiné konfigurace uzlu, než je ta, kterou jste původně přiřadili.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. V okně **účet Automation** klikněte v části **Správa konfigurace**na **Konfigurace stavu (DSC)** .
1. Na stránce **Konfigurace stavu (DSC)** klikněte na kartu **uzly** .
1. Na kartě **uzly** klikněte na název uzlu, který chcete znovu přiřadit.
1. Na stránce pro tento uzel klikněte na **přiřadit konfiguraci uzlu**.

    ![Snímek obrazovky se stránkou podrobností uzlu zvýrazněním tlačítka pro přiřazení konfigurace uzlu](./media/automation-dsc-getting-started/AssignNode.png)

1. Na stránce **přiřadit konfiguraci uzlu** vyberte konfiguraci uzlu, ke kterému chcete uzel přiřadit, a pak klikněte na **OK**.

    ![Snímek obrazovky se stránkou konfigurace přiřazení uzlu](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Ruší se registrace uzlu.

Pokud už nechcete, aby se uzel spravoval Azure Automation DSC, můžete ho zrušit.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. V okně **účet Automation** klikněte v části **Správa konfigurace**na **Konfigurace stavu (DSC)** .
1. Na stránce **Konfigurace stavu (DSC)** klikněte na kartu **uzly** .
1. Na kartě **uzly** klikněte na název uzlu, jehož registraci chcete zrušit.
1. Na stránce pro tento uzel klikněte na **zrušit registraci**.

    ![Snímek obrazovky se stránkou podrobností uzlu zvýrazněním tlačítka zrušit registraci](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Související články

- [Přehled konfigurace stavu Azure Automation](automation-dsc-overview.md)
- [Připojování počítačů pro správu podle konfigurace stavu Azure Automation](automation-dsc-onboarding.md)
- [Přehled konfigurace požadovaného stavu prostředí Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Rutiny konfigurace stavu Azure Automation](/powershell/module/azurerm.automation/#automation)
- [Ceny konfigurace Azure Automation stavu](https://azure.microsoft.com/pricing/details/automation/)
