---
title: Začínáme s konfigurací stavu azure automatizace
description: Vysvětlení a příklady nejběžnějších úloh v konfiguraci stavu automatizace Azure (DSC)
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 43268d5b48b44fffefa222f566c40151c85a5895
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392176"
---
# <a name="get-started-with-azure-automation-state-configuration"></a>Začínáme s konfigurací stavu azure automatizace

Tento článek vysvětluje, jak provést nejběžnější úkoly s Azure Automation state Configuration, jako je vytváření, import a kompilace konfigurací, připojení počítačů pro správu a zobrazení sestav. Přehled konfigurace stavu automatizace Azure najdete v tématu [Přehled konfigurace stavu automatizace Azure](automation-dsc-overview.md). Dokumentace k požadované konfiguraci stavu (DSC) naleznete v tématu [Windows PowerShell Přehled konfigurace požadovaného stavu](/powershell/scripting/dsc/overview/overview).

Tento článek obsahuje podrobný návod k použití konfigurace stavu automatizace Azure. Pokud chcete ukázkové prostředí, které je již nastaveno bez následujících kroků popsaných v tomto článku, můžete použít následující šablonu Správce prostředků: [šablonu spravovaného uzlu Azure Automation](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Tato šablona nastaví dokončené prostředí konfigurace stavu automatizace Azure, včetně virtuálního počítače Azure, který spravuje konfigurace stavu azure automatizace.

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit příklady v tomto článku, jsou požadovány následující:

- Účet Azure Automation. Pokyny k vytvoření účtu Azure Automation Spustit jako najdete v tématu [Účet Spustit jako pro Azure](automation-sec-configure-azure-runas-account.md).
- Virtuální počítač Azure Resource Manager (ne Classic) s [podporovaným operačním systémem](automation-dsc-overview.md#operating-system-requirements). Pokyny k vytvoření virtuálního počítače najdete v tématu [Vytvoření vašeho prvního virtuálního počítače s Windows na webu Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

## <a name="creating-a-dsc-configuration"></a>Vytvoření konfigurace DSC

Vytvoříte jednoduchou [konfiguraci DSC,](/powershell/scripting/dsc/configurations/configurations) která zajistí přítomnost nebo nepřítomnost funkce **systému Windows (IIS) webového serveru** v závislosti na způsobu přiřazení uzlů.

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

Tato konfigurace volá jeden prostředek v každém bloku uzlu, [prostředek WindowsFeature](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource). Tento prostředek zajišťuje přítomnost nebo nepřítomnost funkce **webového serveru.**

## <a name="importing-a-configuration-into-azure-automation"></a>Import konfigurace do Azure Automation

Dále importujete konfiguraci do účtu automatizace.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **Všechny prostředky** a potom na název účtu Automation.
1. Na stránce Účet automatizace vyberte **konfiguraci stavu (DSC)** v části **Správa konfigurace**.
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **Konfigurace** a potom klikněte na **Přidat**.
1. V podokně Importovat konfiguraci vyhledejte soubor v `TestConfig.ps1` počítači.

   ![Snímek obrazovky s rozhraním **Import Configuration**](./media/automation-dsc-getting-started/AddConfig.png)

1. Klikněte na tlačítko **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Zobrazení konfigurace v Azure Automation

Po importu konfigurace ji můžete zobrazit na webu Azure Portal.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **Všechny prostředky** a potom na název účtu Automation.
1. Na stránce Účet automatizace vyberte **konfiguraci stavu (DSC)** v části **Správa konfigurace**.
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **Konfigurace** a potom na **položku TestConfig**. Toto je název konfigurace, kterou jste importovali v předchozím postupu.
1. V podokně Konfigurace TestConfig klepněte na **tlačítko Zobrazit zdroj konfigurace**.

   ![Snímek obrazovky s konfiguračním nožem TestConfig](./media/automation-dsc-getting-started/ViewConfigSource.png)

   Otevře se podokno zdroje Konfigurace TestConfig, které zobrazuje kód prostředí PowerShell pro konfiguraci.

## <a name="compiling-a-configuration-in-azure-automation"></a>Kompilace konfigurace v Azure Automation

Před použitím požadovaného stavu na uzel, konfigurace DSC definující tento stav musí být zkompilována do jednoho nebo více konfigurací uzlu (dokument MOF) a umístěna na serveru pro vyžádanou službu Automation DSC. Podrobnější popis kompilace konfigurací v konfiguraci stavu azure automatizace najdete [v tématu kompilace konfigurací v konfiguraci stavu azure automatizace](automation-dsc-compile.md).
Další informace o kompilaci konfigurací naleznete v [tématu Konfigurace dsc](/powershell/scripting/dsc/configurations/configurations).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **Všechny prostředky** a potom na název účtu Automation.
1. Na stránce Účet automatizace klepněte na **položku Konfigurace stavu (DSC)** v části **Správa konfigurace**.
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **Konfigurace** a potom na **položku TestConfig**. Toto je název dříve importované konfigurace.
1. V podokně Konfigurace TestConfig klepněte na tlačítko **Kompilovat**a potom klepněte na tlačítko **Ano**. Tím se spustí kompilace úlohy.

   ![Snímek obrazovky s konfigurační stránkou TestConfig, která zvýrazňuje tlačítko kompilace](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Když zkompilujete konfiguraci v Azure Automation, automaticky nasadí všechny vytvořené soubory MOF konfigurace uzlu na server vyžádat.

## <a name="viewing-a-compilation-job"></a>Zobrazení kompilace

Po spuštění kompilace ji můžete zobrazit na **dlaždici Úlohy kompilace** na stránce **Konfigurace.** Dlaždice **Úlohy kompilace** zobrazuje aktuálně spuštěné, dokončené a neúspěšné úlohy. Při otevření podokna úloh kompilace se zobrazí informace o této úloze, včetně chyb nebo upozornění, vstupních parametrů použitých v konfiguraci a protokolů kompilace.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **Všechny prostředky** a potom na název účtu Automation.
1. Na stránce Účet automatizace klepněte na **položku Konfigurace stavu (DSC)** v části **Správa konfigurace**.
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **Konfigurace** a potom na **položku TestConfig**. Toto je název dříve importované konfigurace.
1. V části **Úlohy kompilace**vyberte úlohu kompilace, kterou chcete zobrazit. Otevře se podokno Úlohy kompilace s popiskem data spuštění úlohy kompilace.

   ![Snímek obrazovky se stránkou Úloha kompilace](./media/automation-dsc-getting-started/CompilationJob.png)

1. Kliknutím na libovolnou dlaždici v podokně Úloha kompilace zobrazíte další podrobnosti o úloze.

## <a name="viewing-node-configurations"></a>Zobrazení konfigurací uzlů

Úspěšné dokončení úlohy kompilace vytvoří jeden nebo více nových konfigurací uzlů. Konfigurace uzlu je dokument MOF, který je nasazen na server vyžádat a připraven k vyprovoknutí a použít jedním nebo více uzly. Konfigurace uzlů v účtu Automation můžete zobrazit na stránce Konfigurace stavu (DSC). Konfigurace uzlu má název s `ConfigurationName.NodeName`formulářem .

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **Všechny prostředky** a potom na název účtu Automation.
1. Na stránce Účet automatizace klepněte na **položku Konfigurace stavu (DSC)** v části **Správa konfigurace**.
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **Kompilované konfigurace.**

   ![Snímek obrazovky s kartou Kompilované konfigurace](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>Registrace virtuálního počítače Azure pro správu pomocí konfigurace stavu automatizace Azure

Konfigurace stavu Azure Automation můžete použít ke správě virtuálních počítačů Azure (klasické i Správce prostředků), místních virtuálních počítačů, počítačů s Linuxem, virtuálních počítačů AWS a místních fyzických počítačů. V tomto článku se dozvíte, jak napalubě pouze virtuální počítače Azure Resource Manager. Informace o připojení jiných typů počítačů najdete [v tématu Onboarding machines for management by Azure Automation State Configuration](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>Pokud chcete napalubě virtuálního počítače Azure Resource Manager pro správu pomocí konfigurace stavu automatizace Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **Všechny prostředky** a potom na název účtu Automation.
1. Na stránce Účet automatizace klepněte na **položku Konfigurace stavu (DSC)** v části **Správa konfigurace**.
1. Na stránce Konfigurace stavu (DSC) vyberte kartu **Uzly** a klikněte na **+ Přidat**.

   ![Snímek obrazovky se stránkou DSC Nodes zvýrazňující tlačítko Přidat virtuální počítač Azure](./media/automation-dsc-getting-started/OnboardVM.png)

1. V podokně Virtuální počítače vyberte virtuální počítač.
1. V podokně podrobností virtuálního počítače klepněte na tlačítko **+ Připojit**.

   > [!IMPORTANT]
   > Virtuální počítač musí být virtuální počítač Azure Resource Manager s [podporovaným operačním systémem](automation-dsc-overview.md#operating-system-requirements).

2. Na stránce Registrace vyberte název konfigurace uzlu, který se má použít pro virtuální počítač v poli **Název konfigurace uzlu.** Zadání názvu v tomto okamžiku je volitelné. Přiřazenou konfiguraci uzlu můžete změnit po připojení uzlu.

3. V **případě potřeby zaškrtněte políčko Restartovat uzel**a klepněte na tlačítko **OK**.

   ![Snímek obrazovky s registračním nožem](./media/automation-dsc-getting-started/RegisterVM.png)

   Zadaná konfigurace uzlu se použije na virtuální počítače v intervalech určených hodnotou poskytnutou pro **frekvenci konfiguračního režimu**. Virtuální počítače kontroluje aktualizace konfigurace uzlu v intervalech určených hodnotou **Aktualizovat frekvenci.** Další informace o tom, jak se tyto hodnoty používají, naleznete [v tématu Konfigurace správce místní konfigurace](/powershell/scripting/dsc/managing-nodes/metaConfig).

Azure spustí proces registrace virtuálního počítače. Po dokončení se virtuální virtuální počítače zobrazí na kartě **Uzly** na stránce Konfigurace stavu (DSC) v účtu automatizace.

## <a name="viewing-the-list-of-managed-nodes"></a>Zobrazení seznamu spravovaných uzlů

Seznam všech počítačů, které byly zaloizovány pro správu ve vašem účtu Automation, můžete zobrazit na kartě **Uzly** na stránce Konfigurace stavu (DSC).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **Všechny prostředky** a potom na název účtu Automation.
1. Na stránce Účet automatizace klepněte na **položku Konfigurace stavu (DSC)** v části **Správa konfigurace**.
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **Uzly.**

## <a name="viewing-reports-for-managed-nodes"></a>Zobrazení sestav pro spravované uzly

Pokaždé, když konfigurace stavu automatizace Azure provede kontrolu konzistence spravovaného uzlu, uzel odešle zprávu o stavu zpět na server vyžádat. Tyto sestavy můžete zobrazit na stránce pro tento uzel.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **Všechny prostředky** a potom na název účtu Automation.
1. Na stránce Účet automatizace klepněte na **položku Konfigurace stavu (DSC)** v části **Správa konfigurace**.
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **Uzly.** Zde můžete zobrazit přehled stavu konfigurace a podrobnosti pro každý uzel.

   ![Snímek obrazovky se stránkou Uzel](./media/automation-dsc-getting-started/NodesTab.png)

1. Na kartě **Uzly** kliknutím na záznam uzlu otevřete sestavy. Klikněte na sestavu, kterou chcete zobrazit další podrobnosti přehledů.

   ![Snímek obrazovky s uzemní](./media/automation-dsc-getting-started/NodeReport.png)

Na noži pro jednotlivé sestavy můžete zobrazit následující informace o stavu pro odpovídající kontrolu konzistence:

- Stav sestavy. Možné hodnoty:
    * Kompatibilní - uzel je kompatibilní s kontrolou.
   * Nezdařilo se - konfigurace se nezdařila kontrola.
   * Není kompatibilní - uzel `ApplyandMonitor` je v režimu a počítač není v požadovaném stavu.
- Čas zahájení kontroly konzistence.
- Celkový čas běhu pro kontrolu konzistence.
- Typ kontroly konzistence.
- Všechny chyby, včetně kódu chyby a chybové zprávy.
- Všechny prostředky DSC používané v konfiguraci a stav každého prostředku (zda je uzel v požadovaném stavu pro tento prostředek). Kliknutím na jednotlivé zdroje získáte podrobnější informace o tomto zdroji.
- Název, IP adresa a konfigurační režim uzlu.

Můžete také kliknout na **zobrazit nezpracovaná sestava** a zobrazit skutečná data, která uzel odešle na server.
Další informace o použití dat naleznete [v tématu Použití serveru sestav DSC](/powershell/scripting/dsc/pull-server/reportserver).

Může trvat nějakou dobu po vytvoření uzlu, než bude k dispozici první sestava. Po napalubě uzlu může být nutné počkat až 30 minut na první sestavu.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Opětovné přiřazení uzlu k jiné konfiguraci uzlu

Uzel můžete přiřadit k použití jiné konfigurace uzlu, než který jste původně přiřadili.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **Všechny prostředky** a potom na název účtu Automation.
1. Na stránce Účet automatizace klepněte na **položku Konfigurace stavu (DSC)** v části **Správa konfigurace**.
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **Uzly.**
1. Na kartě **Uzly** klikněte na název uzlu, který chcete znovu přiřadit.
1. Na stránce tohoto uzlu klepněte na **tlačítko Přiřadit konfiguraci uzlu**.

    ![Snímek obrazovky se stránkou Podrobnosti uzlu, která zvýrazňuje tlačítko Konfigurace uzlu Přiřadit](./media/automation-dsc-getting-started/AssignNode.png)

1. Na stránce Přiřadit konfiguraci uzlu vyberte konfiguraci uzlu, ke které chcete uzel přiřadit, a klepněte na tlačítko **OK**.

    ![Snímek obrazovky se stránkou Konfigurace přiřadit uzel](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Zrušení registrace uzlu

Pokud už nechcete, aby uzel spravoval Azure Automation DSC, můžete ho zrušit.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **Všechny prostředky** a potom na název účtu Automation.
1. Na stránce Účet automatizace klepněte na **položku Konfigurace stavu (DSC)** v části **Správa konfigurace**.
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **Uzly.**
1. Na kartě **Uzly** klikněte na název uzlu, který chcete zrušit registraci.
1. V podokně tohoto uzlu klepněte na tlačítko **Zrušit registraci**.

    ![Snímek obrazovky se stránkou Podrobnosti uzlu zvýrazňující tlačítko Zrušit registraci](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Související články

- [Přehled konfigurace stavu automatizace Azure](automation-dsc-overview.md)
- [Onboardingové počítače pro správu pomocí konfigurace stavu azure automatizace](automation-dsc-onboarding.md)
- [Windows PowerShell – přehled konfigurace požadovaného stavu](/powershell/scripting/dsc/overview/overview)
- [Ceny konfigurace stavu Azure Automation](https://azure.microsoft.com/pricing/details/automation/)
- [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)