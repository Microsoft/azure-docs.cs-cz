---
title: Začínáme s konfigurací stavu Azure Automation
description: Tento článek obsahuje informace o tom, jak provádět nejběžnější úlohy v konfiguraci stavu Azure Automation.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0e98078fe2799d1c524190c8ced36588f35498b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186431"
---
# <a name="get-started-with-azure-automation-state-configuration"></a>Začínáme s konfigurací stavu Azure Automation

Tento článek poskytuje podrobný návod, jak provádět nejběžnější úlohy s konfigurací stavu Azure Automation, jako je vytváření, importování a kompilování konfigurací, povolení správy a zobrazování sestav v počítačích. Přehled konfigurace stavu najdete v tématu [Přehled konfigurace stavu](automation-dsc-overview.md). Dokumentaci k konfiguraci požadovaného stavu (DSC) najdete v tématu [Přehled konfigurace požadovaného stavu prostředí Windows PowerShell](/powershell/scripting/dsc/overview/overview).

Pokud chcete ukázkové prostředí, které už je nastavené, bez kroků popsaných v tomto článku, můžete použít [šablonu spravovaného uzlu Azure Automation](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Tato šablona nastavuje kompletní prostředí Konfigurace stavu (DSC), včetně virtuálního počítače Azure spravovaného pomocí konfigurace stavu (DSC).

## <a name="prerequisites"></a>Požadavky

K dokončení příkladů v tomto článku jsou potřeba následující:

- Účet Azure Automation. Pokyny k vytvoření účtu Azure Automation Spustit jako najdete v tématu [Účet Spustit jako pro Azure](./manage-runas-account.md).
- Azure Resource Manager virtuální počítač (ne Classic), na kterém běží [podporovaný operační systém](automation-dsc-overview.md#operating-system-requirements). Pokyny k vytvoření virtuálního počítače najdete v tématu [Vytvoření vašeho prvního virtuálního počítače s Windows na webu Azure Portal](../virtual-machines/windows/quick-create-portal.md).

## <a name="create-a-dsc-configuration"></a>Vytvoření konfigurace DSC

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
1. Uložte soubor jako **TestConfig.ps1**.

Tato konfigurace volá jeden prostředek v bloku každého uzlu, [prostředek WindowsFeature](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource). Tento prostředek zajišťuje buď přítomnost, nebo absenci funkce **webového serveru** .

## <a name="import-a-configuration-into-azure-automation"></a>Import konfigurace do Azure Automation

Potom naimportujete konfiguraci do účtu Automation.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. Na stránce účet Automation vyberte v části **Správa konfigurace**možnost **Konfigurace stavu (DSC)** .
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **Konfigurace** a pak klikněte na **Přidat**.
1. V podokně importovat konfiguraci vyhledejte `TestConfig.ps1` soubor v počítači.

   ![Snímek obrazovky s oknem * * import konfigurace * *](./media/automation-dsc-getting-started/AddConfig.png)

1. Klikněte na **OK**.

## <a name="view-a-configuration-in-azure-automation"></a>Zobrazit konfiguraci v Azure Automation

Po importu konfigurace ji můžete zobrazit v Azure Portal.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. Na stránce účet Automation vyberte v části **Správa konfigurace**možnost **Konfigurace stavu (DSC)** .
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **Konfigurace** a pak klikněte na **TestConfig**. Jedná se o název konfigurace, kterou jste importovali v předchozím postupu.
1. V podokně Konfigurace TestConfig klikněte na **Zobrazit zdroj konfigurace**.

   ![Snímek obrazovky okna konfigurace TestConfig](./media/automation-dsc-getting-started/ViewConfigSource.png)

   Otevře se podokno zdroje konfigurace TestConfig, ve kterém se zobrazí kód PowerShellu pro konfiguraci.

## <a name="compile-a-configuration-in-azure-automation"></a>Zkompilovat konfiguraci v Azure Automation

Než budete moci použít požadovaný stav na uzel, konfigurace DSC definující tento stav musí být zkompilována do jedné nebo více konfigurací uzlů (dokument MOF) a umístěna na Automatizace DSC vyžádaného serveru. Podrobnější popis kompilace konfigurací v konfiguraci stavu (DSC) najdete v tématu [Konfigurace kompilace v konfiguraci stavu Azure Automation](automation-dsc-compile.md).
Další informace o kompilaci konfigurací najdete v tématu [Konfigurace DSC](/powershell/scripting/dsc/configurations/configurations).

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. Na stránce účet Automation klikněte v části **Správa konfigurace**na **Konfigurace stavu (DSC)** .
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **Konfigurace** a pak klikněte na **TestConfig**. Toto je název dříve importované konfigurace.
1. V podokně Konfigurace TestConfig klikněte na **kompilovat**a potom klikněte na **Ano**. Tím se spustí úloha kompilace.

   ![Snímek obrazovky s tlačítkem kompilace stránky konfigurace TestConfig](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Při kompilaci konfigurace v Azure Automation automaticky nasadí všechny vytvořené soubory MOF konfigurace uzlu na server vyžádané replikace.

## <a name="view-a-compilation-job"></a>Zobrazit úlohu kompilace

Po spuštění kompilace je můžete zobrazit na dlaždici **úlohy kompilace** na stránce **Konfigurace** . Dlaždice **úlohy kompilace** zobrazuje aktuálně spuštěné, dokončené a neúspěšné úlohy. Po otevření podokna úlohy kompilace se zobrazí informace o této úloze, včetně všech zjištěných chyb nebo upozornění, vstupních parametrů použitých v konfiguraci a protokolů kompilace.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. Na stránce účet Automation klikněte v části **Správa konfigurace**na **Konfigurace stavu (DSC)** .
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **Konfigurace** a pak klikněte na **TestConfig**. Toto je název dříve importované konfigurace.
1. V části **úlohy kompilace**vyberte úlohu kompilace, kterou chcete zobrazit. Otevře se podokno úlohy kompilace označené datem při spuštění úlohy kompilace.

   ![Snímek obrazovky se stránkou úlohy kompilace](./media/automation-dsc-getting-started/CompilationJob.png)

1. Kliknutím na libovolnou dlaždici v podokně úloha kompilace zobrazíte další podrobnosti o úloze.

## <a name="view-node-configurations"></a>Zobrazit konfigurace uzlů

Úspěšné dokončení úlohy kompilace vytvoří jednu nebo více nových konfigurací uzlu. Konfigurace uzlu je dokument MOF, který je nasazený na serveru Pull a připravený k vyžádání a použití v jednom nebo několika uzlech. Konfigurace uzlů můžete zobrazit ve svém účtu Automation na stránce Konfigurace stavu (DSC). Konfigurace uzlu má název s formulářem `ConfigurationName.NodeName` .

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. Na stránce účet Automation klikněte v části **Správa konfigurace**na **Konfigurace stavu (DSC)** .
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **zkompilované konfigurace** .

   ![Snímek obrazovky s kartou kompilovaných konfigurací](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="enable-an-azure-resource-manager-vm-for-management-with-state-configuration"></a>Povolení Azure Resource Manager virtuálního počítače pro správu s konfigurací stavu

Konfiguraci stavu můžete použít ke správě virtuálních počítačů Azure (klasických i Správce prostředků), místních virtuálních počítačů, počítačů se systémem Linux, virtuálních počítačů s AWS a místních fyzických počítačů. V tomto článku se dozvíte, jak povolit jenom Azure Resource Manager virtuálních počítačů. Informace o tom, jak povolit jiné typy počítačů, najdete v tématu [Povolení počítačů pro správu pomocí konfigurace stavu Azure Automation](automation-dsc-onboarding.md).

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. Na stránce účet Automation klikněte v části **Správa konfigurace**na **Konfigurace stavu (DSC)** .
1. Na stránce Konfigurace stavu (DSC) vyberte kartu **uzly** a pak klikněte na **+ Přidat**.

   ![Snímek stránky uzlů DSC zvýrazněním tlačítka Přidat virtuální počítač Azure](./media/automation-dsc-getting-started/OnboardVM.png)

1. V podokně Virtual Machines vyberte svůj virtuální počítač.
1. V podokně podrobností virtuálního počítače klikněte na **+ připojit**.

   > [!IMPORTANT]
   > Virtuální počítač musí být Azure Resource Manager virtuálním počítačem, na kterém běží [podporovaný operační systém](automation-dsc-overview.md#operating-system-requirements).

2. Na stránce registrace vyberte název konfigurace uzlu, který se má použít pro virtuální počítač v poli **název konfigurace uzlu** . Zadání názvu v tomto okamžiku je volitelné. Po povolení uzlu můžete změnit konfiguraci přiřazeného uzlu.

3. V **případě potřeby zaškrtněte uzel restartovat**a pak klikněte na tlačítko **OK**.

   ![Snímek obrazovky okna registrace](./media/automation-dsc-getting-started/RegisterVM.png)

   Konfigurace uzlu, kterou jste zadali, se aplikuje na virtuální počítač v intervalech určených hodnotou zadanou pro **četnost režimu konfigurace**. Virtuální počítač vyhledá aktualizace konfigurace uzlu v intervalech určených hodnotou **četnosti aktualizace** . Další informace o tom, jak se tyto hodnoty používají, najdete v tématu [Konfigurace místní Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

Azure spustí proces povolení virtuálního počítače. Po dokončení se virtuální počítač zobrazí na kartě **uzly** stránky Konfigurace stavu (DSC) v účtu Automation.

## <a name="view-the-list-of-managed-nodes"></a>Zobrazit seznam spravovaných uzlů

Seznam všech počítačů, které byly povoleny pro správu v účtu Automation, můžete zobrazit na kartě **uzly** na stránce Konfigurace stavu (DSC).

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. Na stránce účet Automation klikněte v části **Správa konfigurace**na **Konfigurace stavu (DSC)** .
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **uzly** .

## <a name="view-reports-for-managed-nodes"></a>Zobrazit sestavy pro spravované uzly

Každá konfigurace časových stavů provádí kontrolu konzistence na spravovaném uzlu, uzel pošle zprávu o stavu zpět na server vyžádané replikace. Tyto sestavy můžete zobrazit na stránce pro daný uzel.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. Na stránce účet Automation klikněte v části **Správa konfigurace**na **Konfigurace stavu (DSC)** .
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **uzly** . Tady můžete zobrazit přehled stavu konfigurace a podrobnosti o jednotlivých uzlech.

   ![Snímek stránky s uzlem](./media/automation-dsc-getting-started/NodesTab.png)

1. Na kartě **uzly** kliknutím na záznam uzlu otevřete vytváření sestav. Klikněte na sestavu, ve které chcete zobrazit podrobnosti o dalších sestavách.

   ![Snímek obrazovky okna sestavy](./media/automation-dsc-getting-started/NodeReport.png)

V okně pro jednotlivou sestavu můžete zobrazit následující informace o stavu pro odpovídající kontrolu konzistence:

- Stav sestavy Možné hodnoty:
    * Kompatibilní – uzel je kompatibilní s kontrolou.
   * Selhání – ověření konfigurace se nezdařilo.
   * Nekompatibilní – uzel je v `ApplyandMonitor` režimu a počítač není v požadovaném stavu.
- Čas spuštění kontroly konzistence.
- Celková doba běhu pro kontrolu konzistence.
- Typ kontroly konzistence.
- Jakékoli chyby, včetně kódu chyby a chybové zprávy.
- Všechny prostředky DSC používané v konfiguraci a stav každého prostředku (zda je uzel v požadovaném stavu pro daný prostředek). Pro získání podrobnějších informací o prostředku můžete kliknout na jednotlivé prostředky.
- Název, IP adresa a režim konfigurace uzlu.

Můžete také kliknout na **Zobrazit hrubou sestavu** a zobrazit tak skutečná data, která uzel odesílá na server.
Další informace o použití těchto dat najdete v tématu [použití serveru sestav DSC](/powershell/scripting/dsc/pull-server/reportserver).

Může nějakou dobu trvat, než je uzel povolený před tím, než bude k dispozici první sestava. Po povolení uzlu možná budete muset počkat až 30 minut od první sestavy.

## <a name="reassign-a-node-to-a-different-node-configuration"></a>Změna přiřazení uzlu k jiné konfiguraci uzlu

Uzel můžete přiřadit k použití jiné konfigurace uzlu, než je ta, kterou jste původně přiřadili.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. Na stránce účet Automation klikněte v části **Správa konfigurace**na **Konfigurace stavu (DSC)** .
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **uzly** .
1. Na kartě **uzly** klikněte na název uzlu, který chcete znovu přiřadit.
1. Na stránce pro tento uzel klikněte na **přiřadit konfiguraci uzlu**.

    ![Snímek obrazovky se stránkou podrobností uzlu zvýrazněním tlačítka pro přiřazení konfigurace uzlu](./media/automation-dsc-getting-started/AssignNode.png)

1. Na stránce přiřadit konfiguraci uzlu vyberte konfiguraci uzlu, ke kterému chcete uzel přiřadit, a pak klikněte na **OK**.

    ![Snímek obrazovky se stránkou konfigurace přiřazení uzlu](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregister-a-node"></a>Zrušení registrace uzlu

Pokud už nechcete, aby se uzel spravoval konfigurací stavu, můžete ho zrušit.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. Na stránce účet Automation klikněte v části **Správa konfigurace**na **Konfigurace stavu (DSC)** .
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **uzly** .
1. Na kartě **uzly** klikněte na název uzlu, jehož registraci chcete zrušit.
1. V podokně pro tento uzel klikněte na **zrušit registraci**.

    ![Snímek obrazovky se stránkou podrobností uzlu zvýrazněním tlačítka zrušit registraci](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [Přehled konfigurace stavu Azure Automation](automation-dsc-overview.md).
- Pokud chcete funkci pro virtuální počítače ve vašem prostředí povolit, přečtěte si téma [povolení konfigurace stavu Azure Automation](automation-dsc-onboarding.md).
- Informace o prostředí PowerShell DSC najdete v tématu [Přehled konfigurace požadovaného stavu prostředí Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- Informace o cenách najdete v tématu [Azure Automation ceny konfigurace stavu](https://azure.microsoft.com/pricing/details/automation/).
- Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation).
