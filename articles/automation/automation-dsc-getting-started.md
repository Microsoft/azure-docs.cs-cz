---
title: Začínáme s Azure Automation stavu konfigurace
description: Vysvětlení a příklady zvládnout běžné úkoly v Azure Automation stavu Configuration (DSC)
services: automation
ms.service: automation
ms.component: dsc
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9a18855d11c0b367b7d58ffb0f4c62e752c05b89
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "40004228"
---
# <a name="getting-started-with-azure-automation-state-configuration"></a>Začínáme s Azure Automation stavu konfigurace

Tento článek vysvětluje, jak provádět běžné úkoly s konfigurací stav automatizace Azure, jako je například vytvoření, import a kompilace konfigurací, připojování počítačů pro správu a prohlížení sestav. Přehled konfigurace stavu, které Azure Automation je, najdete v části [přehled Azure Automation stavu konfigurace](automation-dsc-overview.md). Desired State Configuration (DSC) dokumentaci najdete v tématu [Windows PowerShell Desired State Configuration přehled](/powershell/dsc/overview).

Tento článek poskytuje podrobný návod, jak pomocí Azure Automation stav konfigurace. Pokud chcete ukázkové prostředí, které je již nastavena bez kroků popsaných v tomto článku, můžete použít následující šablonu Resource Manageru: [šablony uzlu spravováno agenty Azure Automation](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Tato šablona nastavuje dokončené prostředí konfigurace stavu služby Azure Automation, včetně virtuálního počítače Azure, který je spravovaný nástrojem Konfigurace stavu služby Azure Automation.

## <a name="prerequisites"></a>Požadavky

Pokud chcete dokončit příklady v tomto článku, vyžadují splnění následujících předpokladů:

- Účet Azure Automation. Pokyny k vytvoření účtu Azure Automation Spustit jako najdete v tématu [Účet Spustit jako pro Azure](automation-sec-configure-azure-runas-account.md).
- Virtuální počítač Azure Resource Manageru (ne Classic) s Windows serverem 2008 R2 nebo novější. Pokyny k vytvoření virtuálního počítače najdete v tématu [Vytvoření vašeho prvního virtuálního počítače s Windows na webu Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

## <a name="creating-a-dsc-configuration"></a>Vytvoření konfigurace DSC

Vytvořit jednoduchou [konfigurace DSC](/powershell/dsc/configurations) zajistí se tak přítomnosti nebo nepřítomnosti **Webový Server** Windows funkci (IIS), v závislosti na tom, jak přiřadit uzlům.

1. Spustit [VSCode](https://code.visualstudio.com/docs) (nebo libovolného textového editoru).
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

Tato konfigurace vyžaduje jeden prostředek v každém uzlu bloku [prostředek WindowsFeature](/powershell/dsc/windowsfeatureresource), zajistí se tak přítomnosti nebo nepřítomnosti **Webový Server** funkce.

## <a name="importing-a-configuration-into-azure-automation"></a>Konfigurace importu do Azure Automation

V dalším kroku import konfigurace do účtu Automation.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na tlačítko **všechny prostředky** a potom na název vašeho účtu Automation.
1. Na **účtu Automation** stránce **konfigurace stavu (DSC)** pod **Configuration Management**.
1. Na **konfigurace stavu (DSC)** stránky, klikněte na tlačítko **konfigurace** kartu a potom klikněte na **+ přidat**.
1. Na **importovat konfiguraci** stránky, vyhledejte `TestConfig.ps1` souboru ve vašem počítači.

   ![Snímek obrazovky ** okno importu konfigurace **](./media/automation-dsc-getting-started/AddConfig.png)

1. Klikněte na **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Zobrazení konfigurace ve službě Azure Automation

Po importu konfigurace uvidíte na portálu Azure portal.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na tlačítko **všechny prostředky** a potom na název vašeho účtu Automation.
1. Na **účtu Automation** stránce **konfigurace stavu (DSC)** pod **Configuration Management**.
1. Na **konfigurace stavu (DSC)** stránky, klikněte na tlačítko **konfigurace** kartu a potom klikněte na **TestConfig** (Toto je název konfigurace, které jste naimportovali v předchozí postup).
1. Na **TestConfig konfigurace** klikněte na **zobrazit zdroj konfigurace**.

   ![Snímek obrazovky okna TestConfig konfigurace](./media/automation-dsc-getting-started/ViewConfigSource.png)

   A **zdroj konfigurace TestConfig** stránce otevře zobrazení kódu Powershellu pro konfiguraci.

## <a name="compiling-a-configuration-in-azure-automation"></a>Kompilace konfigurace ve službě Azure Automation

Než použijete požadovaný stav uzlu DSC konfigurace definování tento stav musí zkompilován minimálně jedna konfigurace uzlu (dokument MOF) a umístěn na serveru vyžádané replikace automatizace DSC. Podrobnější popis kompilace konfigurací v konfiguraci stavu služby Azure Automation, naleznete v tématu [kompilace konfigurací v Azure Automation stav konfigurace](automation-dsc-compile.md).
Další informace o kompilaci konfigurace najdete v tématu [konfigurací DSC](/powershell/dsc/configurations).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na tlačítko **všechny prostředky** a potom na název vašeho účtu Automation.
1. Na **účtu Automation** klikněte na **konfigurace stavu (DSC)** pod **Configuration Management**.
1. Na **konfigurace stavu (DSC)** stránky, klikněte na tlačítko **konfigurace** kartu a potom klikněte na **TestConfig** (název dříve importovaná konfigurační).
1. Na **TestConfig konfigurace** klikněte na **kompilaci**a potom klikněte na tlačítko **Ano**. Tím se spustí úloha kompilace.

   ![Snímek obrazovky stránky konfigurace TestConfig zvýraznění tlačítka kompilace](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Při kompilaci konfigurace ve službě Azure Automation, automaticky se nasadí žádnou konfiguraci vytvořený uzel soubory MOF do serveru vyžádané replikace.

## <a name="viewing-a-compilation-job"></a>Zobrazení úlohy kompilace

Po spuštění kompilace můžete zobrazit v **úlohy kompilace** v dlaždici **konfigurace** stránky. **Úlohy kompilace** dlaždice ukazuje aktuálně spuštěné, dokončení a neúspěšné úlohy. Když otevřete stránku úlohy kompilace, zobrazuje informace o této úloze, včetně žádné chyby nebo upozornění došlo k, vstupní parametry použity v konfiguraci a kompilace protokoly.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na tlačítko **všechny prostředky** a potom na název vašeho účtu Automation.
1. Na **účtu Automation** klikněte na **konfigurace stavu (DSC)** pod **Configuration Management**.
1. Na **konfigurace stavu (DSC)** stránky, klikněte na tlačítko **konfigurace** kartu a potom klikněte na **TestConfig** (název dříve importovaná konfigurační).
1. V části **úlohy kompilace**, vyberte úlohu kompilace, které chcete zobrazit. A **úloha kompilace** označené datum, která byla spuštěna úloha kompilace otevře se stránka.

   ![Snímek obrazovky stránky úlohu kompilace](./media/automation-dsc-getting-started/CompilationJob.png)

1. Kliknutím na kteroukoli dlaždici v **úloha kompilace** stránku, abyste zobrazili další podrobnosti o úloze.

## <a name="viewing-node-configurations"></a>Zobrazení konfigurace uzlu

Úspěšné dokončení úlohy kompilace vytvoří jednu nebo více nové konfigurace uzlu. Konfigurace uzlu je dokument MOF, který je nasazen na serveru vyžádané replikace a připraven k načíst a použít jeden nebo více uzlů. Můžete zobrazit konfigurace uzlů ve vašem účtu Automation v **konfigurace stavu (DSC)** stránky. Konfigurace uzlu má název s formuláři *ConfigurationName*.* NodeName*.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na tlačítko **všechny prostředky** a potom na název vašeho účtu Automation.
1. Na **účtu Automation** okna, klikněte na tlačítko **konfigurace stavu (DSC)** pod **Configuration Management**.
1. Na **konfigurace stavu (DSC)** stránky, klikněte na tlačítko **kompilaci konfigurace** kartu.

   ![Snímek obrazovky s kartou kompilaci konfigurace](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>Zprovoznění virtuálního počítače Azure pro správu pomocí Azure Automation State Configuration

Konfigurace stavu služby Azure Automation můžete použít ke správě virtuálních počítačů Azure (Classic i Resource Manager), místních virtuálních počítačů, počítačů Linux, virtuální počítače AWS a místních fyzických počítačů. V tomto článku se dozvíte, jak připojit pouze virtuální počítače Azure Resource Manageru. Informace o registraci najdete v další typy počítačů, [připojování počítačů pro správu Azure Automation stavu konfigurace](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>Připojit virtuální počítač Azure Resource Manageru pro správu konfigurace stavu služby Azure Automation

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na tlačítko **všechny prostředky** a potom na název vašeho účtu Automation.
1. Na **účtu Automation** okna, klikněte na tlačítko **konfigurace stavu (DSC)** pod **Configuration Management**.
1. Na **konfigurace stavu (DSC)** stránku, zatímco na **uzly** klikněte na tlačítko **+ přidat**.

   ![Snímek obrazovky stránky uzly DSC zvýraznění tlačítka Přidat virtuální počítač Azure](./media/automation-dsc-getting-started/OnboardVM.png)

1. Na **virtuálních počítačů** stránky, vyberte svůj virtuální počítač.
1. Na **virtuálního počítače** stránku podrobností, klikněte na tlačítko **+ připojit**.

   > [!IMPORTANT]
   > Tato hodnota musí být virtuální počítač Azure Resource Manageru s Windows serverem 2008 R2 nebo novější.

1. V **registrace** stránky, vyberte název konfigurace uzlu, které chcete použít k virtuálnímu počítači v **název konfigurace uzlu** pole. Poskytnutí názvu v tomto okamžiku je volitelný. Můžete změnit konfiguraci přiřazeném uzlu po připojení uzlu.
   Zkontrolujte **restartovat uzel v případě potřeby**, pak klikněte na tlačítko **OK**.

   ![Snímek obrazovky okna registrace](./media/automation-dsc-getting-started/RegisterVM.png)

   Konfigurace uzlu, který jste zadali, se použijí k virtuálnímu počítači v intervalech stanovených **frekvence režimu konfigurace**, a virtuální počítač znovu zkontroluje aktualizace konfigurace uzlu v intervalech stanovených **aktualizovat Frekvence**. Další informace o tom, jak se používají tyto hodnoty, najdete v části [konfigurace Local Configuration Manageru](https://msdn.microsoft.com/PowerShell/DSC/metaConfig).
1. V **přidat virtuální počítače Azure** okna, klikněte na tlačítko **vytvořit**.

Azure spustí proces zprovoznění virtuálního počítače. Po jeho dokončení se virtuální počítač zobrazuje v **uzly** karty **konfigurace stavu (DSC)** stránky v účtu Automation.

## <a name="viewing-the-list-of-managed-nodes"></a>Zobrazení seznamu spravovaných uzlů

Můžete zobrazit seznam všech počítačů, které mají byla zařazena do systému pro správu ve vašem účtu Automation v **uzly** karty **konfigurace stavu (DSC)** stránky.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na tlačítko **všechny prostředky** a potom na název vašeho účtu Automation.
1. Na **účtu Automation** okna, klikněte na tlačítko **konfigurace stavu (DSC)** pod **Configuration Management**.
1. Na **konfigurace stavu (DSC)** stránky, klikněte na tlačítko **uzly** kartu.

## <a name="viewing-reports-for-managed-nodes"></a>Zobrazení sestav pro spravované uzly

Pokaždé, když Azure Automation stav konfigurace provede kontrolu konzistence na spravovaných uzlů uzlu odešle zprávu o stavu zpět do serveru vyžádané replikace. Tyto sestavy můžete zobrazit na stránce pro tento uzel.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na tlačítko **všechny prostředky** a potom na název vašeho účtu Automation.
1. Na **účtu Automation** okna, klikněte na tlačítko **konfigurace stavu (DSC)** pod **Configuration Management**.
1. Na **konfigurace stavu (DSC)** stránky, klikněte na tlačítko **uzly** kartu. Tady uvidíte přehled konfigurace stav a podrobnosti pro každý uzel.

   ![Snímek obrazovky uzel stránky](./media/automation-dsc-getting-started/NodesTab.png)

1. Na while **uzly** klikněte na tlačítko Otevřít reporting záznam uzlu. Klikněte na sestavu, kterou chcete zobrazit další podrobnosti o vytváření sestav.

   ![Snímek obrazovky okna sestavy](./media/automation-dsc-getting-started/NodeReport.png)

V okně pro jednotlivé sestavy zobrazí se následující informace o stavu pro odpovídající Kontrola konzistence:

- Stav sestavy –, zda je uzel "Odpovídající", "Failed", konfigurace nebo uzel "Neodpovídá" (Pokud je uzel v **ApplyandMonitor** režimu a tento počítač není v požadovaném stavu).
- Čas zahájení pro kontrolu konzistence.
- Celková doba běhu pro kontrolu konzistence.
- Typ kontroly konzistence.
- Všechny chyby, včetně kód chyby a chybovou zprávou.
- Všechny prostředky DSC používané v konfiguraci a stavu každého prostředku (Určuje, zda uzel je v požadovaném stavu pro daný prostředek), můžete kliknout na každý prostředek zobrazíte podrobnější informace pro daný prostředek.
- Název, IP adresu a režim konfigurace uzlu.

Můžete také kliknout na **zobrazit nezpracovanou sestavu** zobrazíte skutečná data, která uzel odešle na server.
Další informace o použití těchto dat najdete v tématu [pomocí serveru sestav DSC](/powershell/dsc/reportserver).

Může trvat nějakou dobu, po uzlu zprovoznění před první sestava je k dispozici. Můžete potřebovat po čekat, až 30 minut, než první sestavu můžete připojit uzel.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Přiřazení uzlu do jiného uzlu Konfigurace

Můžete přiřadit uzel, který chcete použít konfiguraci jiný uzel než ten, který původně přiřazený.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na tlačítko **všechny prostředky** a potom na název vašeho účtu Automation.
1. Na **účtu Automation** okna, klikněte na tlačítko **konfigurace stavu (DSC)** pod **Configuration Management**.
1. Na **konfigurace stavu (DSC)** stránky, klikněte na tlačítko **uzly** kartu.
1. Na **uzly** kartu a potom klikněte na název uzlu, který chcete znovu přiřadit.
1. Na stránce pro tento uzel, klikněte na tlačítko **přiřadit konfiguraci uzlu**.

    ![Snímek obrazovky stránky s podrobnostmi uzel zvýraznění tlačítko přiřazení konfigurace uzlu](./media/automation-dsc-getting-started/AssignNode.png)

1. Na **přiřadit konfiguraci uzlu** vyberte konfigurace uzlu, ke kterému chcete přiřadit uzel a potom klikněte na tlačítko **OK**.

    ![Snímek obrazovky stránky přiřadit konfiguraci uzlu](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Zrušení registrace uzlu

Pokud už má uzel jej lze spravovat pomocí Azure Automation DSC, můžete ji zrušit registraci.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na tlačítko **všechny prostředky** a potom na název vašeho účtu Automation.
1. Na **účtu Automation** okna, klikněte na tlačítko **konfigurace stavu (DSC)** pod **Configuration Management**.
1. Na **konfigurace stavu (DSC)** stránky, klikněte na tlačítko **uzly** kartu.
1. Na **uzly** kartu a potom klikněte na název uzlu, které chcete zrušit registraci.
1. Na stránce pro tento uzel, klikněte na tlačítko **Unregister**.

    ![Snímek obrazovky stránky s podrobnostmi uzel zvýraznění tlačítka zrušení registrace](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Související články

- [Přehled Azure Automation stavu konfigurace](automation-dsc-overview.md)
- [Připojování počítačů pro správu podle konfigurace stavu služby Azure Automation](automation-dsc-onboarding.md)
- [Prostředí Windows PowerShell Desired State Configuration – přehled](/powershell/dsc/overview)
- [Rutiny Azure Automation stavu konfigurace](/powershell/module/azurerm.automation/#automation)
- [Ceny Azure Automation stavu konfigurace](https://azure.microsoft.com/pricing/details/automation/)