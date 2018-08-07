---
title: Začínáme s Azure Automation DSC
description: Vysvětlení a příklady zvládnout běžné úkoly v Azure Automation Desired State Configuration (DSC)
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 08/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9f2312064e9fb7676d5609ee077d5ed7e02e8f30
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524460"
---
# <a name="getting-started-with-azure-automation-dsc"></a>Začínáme s Azure Automation DSC

Tento článek vysvětluje, jak provádět běžné úkoly s Azure Automation Desired State Configuration (DSC), jako je například vytvoření, import a kompilace konfigurací, připojování počítačů pro správu a prohlížení sestav. Přehled o jaké Azure Automation DSC je, najdete v části [Přehled služby Azure Automation DSC](automation-dsc-overview.md). DSC dokumentaci najdete v tématu [Windows PowerShell Desired State Configuration přehled](/powershell/dsc/overview).

Tento článek poskytuje podrobný návod, jak pomocí Azure Automation DSC. Pokud chcete ukázkové prostředí, které je již nastavena bez kroků popsaných v tomto článku, můžete použít následující šablonu Resource Manageru: Tato šablona nastavuje dokončené Azure Automation DSC prostředí, včetně virtuálního počítače Azure, který je spravuje Azure Automation DSC.

## <a name="prerequisites"></a>Požadavky

Pokud chcete dokončit příklady v tomto článku, vyžadují splnění následujících předpokladů:

* Účet Azure Automation. Pokyny k vytvoření účtu Azure Automation Spustit jako najdete v tématu [Účet Spustit jako pro Azure](automation-sec-configure-azure-runas-account.md).
* Virtuální počítač Azure Resource Manageru (ne Classic) s Windows serverem 2008 R2 nebo novější. Pokyny k vytvoření virtuálního počítače najdete v tématu [Vytvoření vašeho prvního virtuálního počítače s Windows na webu Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

## <a name="creating-a-dsc-configuration"></a>Vytvoření konfigurace DSC

Vytvořit jednoduchou [konfigurace DSC](/powershell/dsc/configurations) zajistí se tak přítomnosti nebo nepřítomnosti **Webový Server** Windows funkci (IIS), v závislosti na tom, jak přiřadit uzlům.

1. Spusťte Windows PowerShell ISE (nebo libovolného textového editoru).
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

Tato konfigurace vyžaduje jeden prostředek v každém uzlu bloku [prostředek WindowsFeature](https://msdn.microsoft.com/powershell/dsc/windowsfeatureresource), zajistí se tak přítomnosti nebo nepřítomnosti **Webový Server** funkce.

## <a name="importing-a-configuration-into-azure-automation"></a>Konfigurace importu do Azure Automation

V dalším kroku import konfigurace do účtu Automation.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na tlačítko **všechny prostředky** a potom na název vašeho účtu Automation.
1. Na **účtu Automation** stránce **konfigurací DSC** pod **Configuration Management**.
1. Na **konfigurací DSC** klikněte na **+ Přidat konfiguraci**.
1. Na **importovat konfiguraci** stránky, vyhledejte `TestConfig.ps1` souboru ve vašem počítači.

    ![Snímek obrazovky ** importu konfigurace ** stránky](./media/automation-dsc-getting-started/AddConfig.png)
1. Klikněte na **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Zobrazení konfigurace ve službě Azure Automation

Po importu konfigurace uvidíte na portálu Azure portal.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na tlačítko **všechny prostředky** a potom na název vašeho účtu Automation.
1. Na **účtu Automation** stránce **konfigurací DSC** pod **Configuration Management**.
1. Na **konfigurací DSC** klikněte na **TestConfig** (Toto je název konfigurace, které jste naimportovali v předchozím postupu).
1. Na **TestConfig konfigurace** klikněte na **zobrazit zdroj konfigurace**.

    ![Snímek obrazovky stránky konfigurace TestConfig](./media/automation-dsc-getting-started/ViewConfigSource.png)

    A **zdroj konfigurace TestConfig** stránce otevře zobrazení kódu Powershellu pro konfiguraci.

## <a name="compiling-a-configuration-in-azure-automation"></a>Kompilace konfigurace ve službě Azure Automation

Než použijete požadovaný stav uzlu DSC konfigurace definování tento stav musí zkompilován minimálně jedna konfigurace uzlu (dokument MOF) a umístěn na serveru vyžádané replikace automatizace DSC. Podrobnější popis kompilace konfigurací v Azure Automation DSC, naleznete v tématu [kompilace konfigurací v Azure Automation DSC](automation-dsc-compile.md). Další informace o kompilaci konfigurace najdete v tématu [konfigurací DSC](/powershell/dsc/configurations).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na tlačítko **všechny prostředky** a potom na název vašeho účtu Automation.
1. Na **účtu Automation** klikněte na **konfigurací DSC** pod **Configuration Management**.
1. Na **konfigurací DSC** klikněte na **TestConfig** (název dříve importovaná konfigurační).
1. Na **TestConfig konfigurace** klikněte na **kompilaci**a potom klikněte na tlačítko **Ano**. Tím se spustí úloha kompilace.

    ![Snímek obrazovky stránky konfigurace TestConfig zvýraznění tlačítka kompilace](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Při kompilaci konfigurace ve službě Azure Automation, automaticky se nasadí žádnou konfiguraci vytvořený uzel soubory MOF do serveru vyžádané replikace.

## <a name="viewing-a-compilation-job"></a>Zobrazení úlohy kompilace

Po spuštění kompilace můžete zobrazit v **úlohy kompilace** v dlaždici **konfigurace** stránky. **Úlohy kompilace** dlaždice ukazuje aktuálně spuštěné, dokončení a neúspěšné úlohy. Když otevřete stránku úlohy kompilace, zobrazuje informace o této úloze, včetně žádné chyby nebo upozornění došlo k, vstupní parametry použity v konfiguraci a kompilace protokoly.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na tlačítko **všechny prostředky** a potom na název vašeho účtu Automation.
1. Na **účtu Automation** klikněte na **konfigurací DSC** pod **Configuration Management**.
1. Na **konfigurací DSC** klikněte na **TestConfig** (název dříve importovaná konfigurační).
1. V části **úlohy kompilace**, vyberte úlohu kompilace, které chcete zobrazit. A **úloha kompilace** otevření označené datum, která byla spuštěna úloha kompilace stránky.

    ![Snímek obrazovky stránky úlohu kompilace](./media/automation-dsc-getting-started/CompilationJob.png)
1. Kliknutím na kteroukoli dlaždici v **úloha kompilace** stránku, abyste zobrazili další podrobnosti o úloze.

## <a name="viewing-node-configurations"></a>Zobrazení konfigurace uzlu

Úspěšné dokončení úlohy kompilace vytvoří jednu nebo více nové konfigurace uzlu. Konfigurace uzlu je dokument MOF, který je nasazen na serveru vyžádané replikace a připraven k načíst a použít jeden nebo více uzlů. Můžete zobrazit konfigurace uzlů ve vašem účtu Automation v **konfigurace uzlu DSC** stránky. Konfigurace uzlu má název s formuláři *ConfigurationName*. *NodeName*.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V nabídce centra klikněte na tlačítko **všechny prostředky** a potom na název vašeho účtu Automation.
1. Na **účtu Automation** klikněte na **konfigurace stavu (DSC)** a pak vyberte **konfigurace**.

    ![Snímek obrazovky stránky konfigurace uzlu DSC](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-dsc"></a>Zprovoznění virtuálního počítače Azure pro správu s Azure Automation DSC

Azure Automation DSC můžete spravovat virtuální počítače Azure (Classic i Resource Manager), místních virtuálních počítačů, počítačů Linux, virtuální počítače AWS a místních fyzických počítačů. V tomto článku se dozvíte, jak připojit pouze virtuální počítače Azure Resource Manageru. Informace o registraci najdete v další typy počítačů, [připojování počítačů pro správu pomocí Azure Automation DSC](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-dsc"></a>Připojit virtuální počítač Azure Resource Manageru pro správu Azure Automation DSC

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na tlačítko **všechny prostředky** a potom na název vašeho účtu Automation.
1. Na **účtu Automation** klikněte na **konfigurace stavu (DSC)** pod **Configuration Management** a vyberte **uzly**.
1. V **uzly** klikněte na **+ přidat**.

    ![Snímek obrazovky stránky uzly DSC zvýraznění tlačítka Přidat virtuální počítač Azure](./media/automation-dsc-getting-started/OnboardVM.png)

1. Na stránce virtuální počítače vyberte svůj virtuální počítač. **Přidání virtuálních počítačů Azure** klikněte na **výběr virtuálních počítačů na připojení**.
1. Klikněte na **Připojit**.

   > [!IMPORTANT]
   > Tato hodnota musí být virtuální počítač Azure Resource Manageru s Windows serverem 2008 R2 nebo novější.

1. V **registrace** stránky, zadejte název konfigurace uzlu, které chcete použít k virtuálnímu počítači v **název konfigurace uzlu** pole. Toto musí přesně odpovídat názvu konfigurace uzlu v účtu Automation. Poskytnutí názvu v tomto okamžiku je volitelný. Můžete změnit konfiguraci přiřazeném uzlu po připojení uzlu.
   Zkontrolujte **restartovat uzel v případě potřeby**a potom klikněte na tlačítko **OK**.

    ![Snímek obrazovky stránky registrace](./media/automation-dsc-getting-started/RegisterVM.png)

    Konfigurace uzlu, který jste zadali, se použijí k virtuálnímu počítači v intervalech stanovených **frekvence režimu konfigurace**, a virtuální počítač znovu zkontroluje aktualizace konfigurace uzlu v intervalech stanovených **aktualizovat Frekvence**. Další informace o tom, jak se používají tyto hodnoty, najdete v části [konfigurace Local Configuration Manageru](https://msdn.microsoft.com/PowerShell/DSC/metaConfig).
1. V **přidat virtuální počítače Azure** klikněte na **vytvořit**.

Azure spustí proces zprovoznění virtuálního počítače. Po jeho dokončení se virtuální počítač zobrazuje v **uzly** kartě **konfigurace stavu (DSC)** stránky v účtu Automation.

## <a name="viewing-the-list-of-dsc-nodes"></a>Zobrazení seznamu uzlů DSC

Můžete zobrazit seznam všech počítačů, které mají byla zařazena do systému pro správu ve vašem účtu Automation v **uzly** kartě **konfigurace stavu (DSC)** stránky.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně klikněte na tlačítko **všechny prostředky** a potom na název vašeho účtu Automation.
3. Na **účtu Automation** klikněte na **konfigurace stavu (DSC)**, klikněte **uzly** kartu.

## <a name="viewing-reports-for-dsc-nodes"></a>Zobrazení sestav pro uzly DSC

Pokaždé, když Azure Automation DSC provede kontrolu konzistence na spravovaných uzlů uzlu odešle zprávu o stavu zpět do serveru vyžádané replikace. Tyto sestavy můžete zobrazit na stránce pro tento uzel.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně klikněte na tlačítko **všechny prostředky** a potom na název vašeho účtu Automation.
3. Na **účtu Automation** klikněte na **konfigurace stavu (DSC)** a potom klikněte na tlačítko **uzly** kartu.
4. V **uzly** vyberte uzel, který chcete zobrazit.
5. Na **uzel** klikněte na sestavu, kterou chcete zobrazit v části **sestavy**.

    ![Snímek obrazovky stránky sestavy](./media/automation-dsc-getting-started/NodeReport.png)

Na stránce pro jednotlivé sestavy se zobrazí následující informace o stavu pro odpovídající Kontrola konzistence:

* Stav sestavy –, zda je uzel "Odpovídající", "Failed", konfigurace nebo uzel "Neodpovídá" (Pokud je uzel v **applyandmonitor** režimu a tento počítač není v požadovaném stavu).
* Čas zahájení pro kontrolu konzistence.
* Celková doba běhu pro kontrolu konzistence.
* Typ kontroly konzistence.
* Všechny chyby, včetně kód chyby a chybovou zprávou.
* Všechny prostředky DSC používané v konfiguraci a stavu každého prostředku (Určuje, zda uzel je v požadovaném stavu pro daný prostředek), můžete kliknout na každý prostředek zobrazíte podrobnější informace pro daný prostředek.
* Název, IP adresu a režim konfigurace uzlu.

Můžete také kliknout na **zobrazit nezpracovanou sestavu** zobrazíte skutečná data, která uzel odešle na server. Další informace o použití těchto dat najdete v tématu [pomocí serveru sestav DSC](https://msdn.microsoft.com/powershell/dsc/reportserver).

Může trvat nějakou dobu, po uzlu zprovoznění před první sestava je k dispozici. Můžete potřebovat po čekat, až 30 minut, než první sestavu můžete připojit uzel.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Přiřazení uzlu do jiného uzlu Konfigurace

Můžete přiřadit uzel, který chcete použít konfiguraci jiný uzel než ten, který původně přiřazený.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně klikněte na tlačítko **všechny prostředky** a potom na název vašeho účtu Automation.
3. Na **účtu Automation** klikněte na **konfigurace stavu (DSC)** a potom klikněte na tlačítko **uzly** kartu.
4. Na **uzly** kartu a potom klikněte na název uzlu, který chcete znovu přiřadit.
5. Na stránce pro tento uzel, klikněte na tlačítko **přiřadit konfiguraci uzlu**.

    ![Snímek obrazovky stránky uzel zvýraznění tlačítko přiřadit uzlu](./media/automation-dsc-getting-started/AssignNode.png)
6. Na **přiřadit konfiguraci uzlu** vyberte konfigurace uzlu, ke kterému chcete přiřadit uzel a potom klikněte na tlačítko **OK**.

    ![Snímek obrazovky stránky přiřadit konfiguraci uzlu](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Zrušení registrace uzlu

Pokud už má uzel jej lze spravovat pomocí Azure Automation DSC, můžete ji zrušit registraci.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně klikněte na tlačítko **všechny prostředky** a potom na název vašeho účtu Automation.
3. Na **účtu Automation** klikněte na **konfigurace stavu (DSC)** a potom klikněte na tlačítko **uzly** tab.git 
4. Na **uzly** kartu a potom klikněte na název uzlu, které chcete zrušit registraci.
5. Na stránce pro tento uzel, klikněte na tlačítko **Unregister**.

    ![Snímek obrazovky stránky uzel zvýraznění tlačítka zrušení registrace](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Související články

* [Přehled služby Azure Automation DSC](automation-dsc-overview.md)
* [Připojování počítačů pro správu pomocí Azure Automation DSC](automation-dsc-onboarding.md)
* [Prostředí Windows PowerShell Desired State Configuration – přehled](https://msdn.microsoft.com/powershell/dsc/overview)
* [Rutiny Azure Automation DSC](/powershell/module/azurerm.automation/#automation)
* [Ceny za Azure Automation DSC](https://azure.microsoft.com/pricing/details/automation/)
