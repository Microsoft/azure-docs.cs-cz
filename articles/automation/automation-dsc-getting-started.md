---
title: Začínáme s Azure Automation DSC.
description: Vysvětlení a příklady nejběžnější úlohy v Azure Automation požadovaného stavu konfigurace (DSC)
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cd26c4b070ed7b92a73e43c1568805a9ac679d12
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="getting-started-with-azure-automation-dsc"></a>Začínáme s Azure Automation DSC.

Tento článek vysvětluje, jak provádět běžné úkoly s Azure Automation požadovaného stavu konfigurace (DSC), jako je například vytváření, importování a kompilování konfigurace, registrace počítače, které chcete spravovat a zobrazování sestav. Přehled co Azure Automation DSC je, najdete v části [přehled Azure Automation DSC](automation-dsc-overview.md). DSC dokumentaci najdete v tématu [Přehled konfigurace prostředí Windows PowerShell požadovaného stavu](/powershell/dsc/overview).

Tento článek obsahuje podrobný návod, jak pomocí Azure Automation DSC. Pokud chcete ukázkové prostředí, které jsou již nastaveny bez kroků popsaných v tomto článku, můžete použít následující šablonu Resource Manager: Tato šablona nastavuje dokončené prostředí Azure Automation DSC, včetně virtuálního počítače Azure, který je spravuje Azure Automation DSC.

## <a name="prerequisites"></a>Požadavky

Abyste mohli dokončit příklady v tomto článku, je potřeba splnit následující nástroje:

* Účet Azure Automation. Pokyny k vytvoření účtu Azure Automation Spustit jako najdete v tématu [Účet Spustit jako pro Azure](automation-sec-configure-azure-runas-account.md).
* Virtuální počítač Azure Resource Manager (ne Classic) systémem Windows Server 2008 R2 nebo novější. Pokyny k vytvoření virtuálního počítače najdete v tématu [Vytvoření vašeho prvního virtuálního počítače s Windows na webu Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

## <a name="creating-a-dsc-configuration"></a>Vytvoření konfigurace DSC

Vytvořit jednoduchou [konfigurace DSC](/powershell/dsc/configurations) zajistí se tak přítomnosti nebo absenci **Webový Server** Windows funkce (IIS), v závislosti na tom, jak přiřadit uzly.

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

Tato konfigurace volá jeden prostředek v každém uzlu bloku [WindowsFeature prostředků](https://msdn.microsoft.com/powershell/dsc/windowsfeatureresource), zajistí se tak přítomnosti nebo absenci **Webový Server** funkce.

## <a name="importing-a-configuration-into-azure-automation"></a>Konfigurace importu do Azure Automation

V dalším kroku importu konfigurace do účtu Automation.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na tlačítko **všechny prostředky** a potom název účtu Automation.
1. Na **účet Automation** vyberte **konfigurace DSC** pod **Configuration Management**.
1. Na **konfigurace DSC** klikněte na tlačítko **+ Přidat konfiguraci**.
1. Na **importovat konfiguraci** stránky, vyhledejte `TestConfig.ps1` souboru ve svém počítači.

    ![Snímek obrazovky ** okno importu konfigurace **](./media/automation-dsc-getting-started/AddConfig.png)
1. Klikněte na **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Zobrazení konfigurace ve službě Azure Automation

Po dokončení importu konfigurace, můžete ji zobrazit na portálu Azure.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na tlačítko **všechny prostředky** a potom název účtu Automation.
1. Na **účet Automation** vyberte **konfigurace DSC** pod **Configuration Management**.
1. Na **konfigurace DSC** klikněte na tlačítko **TestConfig** (Toto je název konfigurace, které jste importovali v předchozím postupu).
1. Na **TestConfig konfigurace** klikněte na tlačítko **zdroj konfigurace zobrazení**.

    ![Snímek obrazovky okna TestConfig konfigurace](./media/automation-dsc-getting-started/ViewConfigSource.png)

    A **zdroj konfigurace TestConfig** otevře okno se zobrazí kód prostředí PowerShell pro konfiguraci.

## <a name="compiling-a-configuration-in-azure-automation"></a>Kompilování konfigurace ve službě Azure Automation

Než použijete požadovaný stav do uzlu Konfigurace DSC, definování tohoto stavu musí být zkompilovány do minimálně jedna konfigurace uzlu (dokument MOF) a na Server pro vyžádání obsahu Automation DSC. Podrobnější popis kompilování konfigurace v Azure Automation DSC, najdete v části [kompilování konfigurace v Azure Automation DSC](automation-dsc-compile.md). Další informace o kompilování konfigurace najdete v tématu [konfigurace DSC](/powershell/dsc/configurations).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na tlačítko **všechny prostředky** a potom název účtu Automation.
1. Na **účet Automation** klikněte na tlačítko **konfigurace DSC** pod **Configuration Management**.
1. Na **konfigurace DSC** klikněte na tlačítko **TestConfig** (název dřív naimportovaný konfigurace).
1. Na **TestConfig konfigurace** klikněte na tlačítko **zkompilovat**a potom klikněte na **Ano**. Spustí úlohu kompilace.

    ![Snímek obrazovky stránky konfigurace TestConfig zvýraznění tlačítko kompilace](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Při kompilaci konfigurace ve službě Azure Automation, automaticky se nasadí žádnou konfiguraci vytvořený uzel soubory MOF na server vyžádané replikace.

## <a name="viewing-a-compilation-job"></a>Zobrazení úlohu kompilace

Po spuštění kompilace, můžete ji v zobrazit **úlohy kompilace** dlaždice v nástroji **konfigurace** okno. **Úlohy kompilace** dlaždice ukazuje aktuálně spuštěna, byla dokončena a neúspěšné úlohy. Když otevřete okno úlohy kompilace, zobrazuje informace o této úloze, včetně žádné chyby nebo upozornění došlo, vstupní parametry použít v konfiguraci a kompilace protokoly.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na tlačítko **všechny prostředky** a potom název účtu Automation.
1. Na **účet Automation** klikněte na tlačítko **konfigurace DSC** pod **Configuration Management**.
1. Na **konfigurace DSC** klikněte na tlačítko **TestConfig** (název dřív naimportovaný konfigurace).
1. V části **úlohy kompilace**, vyberte úlohu kompilace, které chcete zobrazit. A **úloha kompilace** otevře, s názvem bez přípony s datem, která byla spuštěna úloha kompilace se stránka.

    ![Snímek obrazovky stránky úloha kompilace](./media/automation-dsc-getting-started/CompilationJob.png)
1. Kliknutím na libovolnou dlaždici v **úloha kompilace** stránky zobrazíte další podrobnosti o úloze.

## <a name="viewing-node-configurations"></a>Zobrazení konfigurace uzlu

Úspěšné dokončení úlohy kompilace vytvoří jeden nebo více nové konfigurace uzlu. Konfigurace uzlu je dokument MOF, který je nasazen na server vyžádané replikace a připravené k vyžádat a použít jeden nebo více uzly. Konfigurace uzlu si můžete prohlédnout v účtu Automation v **konfigurace uzlu DSC** okno. Konfigurace uzlu, má název s formulářem *ConfigurationName*. *NodeName*.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V nabídce centra klikněte na tlačítko **všechny prostředky** a potom název účtu Automation.
1. Na **účet Automation** okně klikněte na tlačítko **konfigurace uzlu DSC**.

    ![Snímek obrazovky okna konfigurace uzlu DSC](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-dsc"></a>Registrace virtuální počítač Azure pro správu s Azure Automation DSC.

Azure Automation DSC můžete použít ke správě virtuálních počítačích Azure (Classic i Resource Manager), místní virtuální počítače, Linux počítače, virtuální počítače AWS a místní fyzických počítačů. V tomto článku se dozvíte, jak připojit pouze virtuální počítače Azure Resource Manager. Informace o registraci najdete v části Další typy počítačů, [registrace počítačů pro správu Azure Automation DSC](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-dsc"></a>Se budou registrovat virtuální počítač Azure Resource Manager pro správu Azure Automation DSC.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na tlačítko **všechny prostředky** a potom název účtu Automation.
1. Na **účet Automation** klikněte na tlačítko **uzly DSC** pod **Configuration Management**.
1. V **uzly DSC** klikněte na tlačítko **přidat virtuální počítač Azure**.

    ![Snímek obrazovky stránky uzly DSC zvýraznění na tlačítko Přidat virtuální počítač Azure](./media/automation-dsc-getting-started/OnboardVM.png)
1. Na stránce virtuální počítače vyberte virtuální počítač. **Přidat virtuální počítače Azure** klikněte na tlačítko **vybrat virtuální počítače chcete zařadit**.
1. Klikněte na **Připojit**.

   > [!IMPORTANT]
   > Tato hodnota musí být virtuální počítač Azure Resource Manager systémem Windows Server 2008 R2 nebo novější.

1. V **registrace** stránky, zadejte název konfigurace uzlu, kterou chcete použít pro virtuální počítač v **název konfigurace uzlu** pole. Toto musí přesně odpovídat názvu konfigurace uzlu v účtu Automation. Poskytnutí názvu v tomto okamžiku je volitelné. Můžete změnit konfiguraci přiřazené uzlu po registraci uzlu.
   Zkontrolujte **restartovat uzel v případě potřeby**a potom klikněte na **OK**.

    ![Snímek obrazovky okna registrace](./media/automation-dsc-getting-started/RegisterVM.png)

    Konfigurace uzlu, který jste zadali, se použijí pro virtuální počítač v intervalech určeného **frekvence režimu konfigurace**, a virtuální počítač zkontroluje aktualizace konfigurace uzlu v intervalech určeného **aktualizovat Frekvence**. Další informace o tom, jak se používají tyto hodnoty, najdete v části [konfigurace správce místní konfigurace](https://msdn.microsoft.com/PowerShell/DSC/metaConfig).
1. V **přidat virtuální počítače Azure** okně klikněte na tlačítko **vytvořit**.

Azure spustí proces registrace virtuálních počítačů. Po dokončení virtuálního počítače se zobrazí v **uzly DSC** okno v účtu Automation.

## <a name="viewing-the-list-of-dsc-nodes"></a>Zobrazení seznamu uzlů DSC

Můžete zobrazit seznam všech počítačů, které byly zařazený, nemá pro správu ve vašem účtu Automation v **uzly DSC** okno.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně klikněte na tlačítko **všechny prostředky** a potom název účtu Automation.
3. Na **účet Automation** klikněte na tlačítko **uzly DSC**.

## <a name="viewing-reports-for-dsc-nodes"></a>Prohlížení sestav pro uzly DSC

Pokaždé, když Azure Automation DSC provede kontrolu konzistence na spravovaný uzel, uzel odešle zprávu o stavu zpět na server vyžádané replikace. Tyto sestavy můžete zobrazit na stránce pro tento uzel.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně klikněte na tlačítko **všechny prostředky** a potom název účtu Automation.
3. Na **účet Automation** klikněte na tlačítko **uzly DSC**.
4. V **uzly DSC** seznamu, vyberte uzel, který chcete zobrazit.
5. Na **uzlu** stránky, klikněte na sestavu, kterou chcete zobrazit v části **sestavy**.

    ![Snímek obrazovky okna sestavy](./media/automation-dsc-getting-started/NodeReport.png)

V okně pro jednotlivé sestavy zobrazí se následující informace o stavu odpovídající kontrolu konzistence:

* Sestava stavu – jestli uzlu je "Kompatibilní", "Failed" konfigurace nebo uzel "Neodpovídá" (Pokud je uzel v **applyandmonitor** režimu a počítač není v požadovaném stavu).
* Čas zahájení pro kontrolu konzistence.
* Celkové doby běhu pro kontrolu konzistence.
* Typ kontroly konzistence.
* Všechny chyby, včetně kód chyby a chybová zpráva.
* Veškeré prostředky DSC v konfiguraci a stavu každého prostředku (jestli uzlu je v požadovaném stavu pro tento prostředek), můžete kliknutím na každého prostředku, chcete-li získat podrobnější informace pro tento prostředek.
* Název, IP adresu a režim konfigurace uzlu.

Můžete také kliknout na **zobrazit nezpracované sestavy** zobrazíte skutečná data, která uzlu odešle na server. Další informace o používání dat najdete v tématu [pomocí serveru sestav DSC](https://msdn.microsoft.com/powershell/dsc/reportserver).

Může trvat delší dobu, po uzel, který je zařazený nemá před první sestava je k dispozici. Možná budete muset po čekat, až 30 minut pro první zprávu můžete zaváděním uzlu.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Opětovné přiřazování uzlu do jiného uzlu Konfigurace

Můžete přiřadit uzel, který použijete jiný uzel konfigurace než ten, který původně přiřazený.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně klikněte na tlačítko **všechny prostředky** a potom název účtu Automation.
3. Na **účet Automation** klikněte na tlačítko **uzly DSC**.
4. Na **uzly DSC** klikněte na název uzlu, který chcete přiřadit.
5. Na stránce pro tento uzel, klikněte na **přiřazení uzlu**.

    ![Snímek obrazovky okna uzlu zvýraznění tlačítko přiřadit uzlu](./media/automation-dsc-getting-started/AssignNode.png)
6. Na **přiřadit konfigurace uzlu** vyberte konfigurace uzlu, ke kterému chcete přiřadit uzlu a pak klikněte na tlačítko **OK**.

    ![Snímek obrazovky okna přiřadit konfigurace uzlu](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Zrušení registrace uzlu

Pokud již nechcete uzlu lze spravovat pomocí Azure Automation DSC, můžete zrušení její registrace.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně klikněte na tlačítko **všechny prostředky** a potom název účtu Automation.
3. Na **účet Automation** klikněte na tlačítko **uzly DSC**.
4. Na **uzly DSC** klikněte na název uzlu, kterou chcete zrušit registraci.
5. Na stránce pro tento uzel, klikněte na **Unregister**.

    ![Snímek obrazovky okna uzlu zvýraznění tlačítko Unregister](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Související články

* [Přehled služby Azure Automation DSC](automation-dsc-overview.md)
* [Registrace počítačů pro správu Azure Automation DSC.](automation-dsc-onboarding.md)
* [Přehled stavu konfigurace požadovaného prostředí Windows PowerShell](https://msdn.microsoft.com/powershell/dsc/overview)
* [Rutiny Azure Automation DSC](/powershell/module/azurerm.automation/#automation)
* [Ceny služby Azure Automation DSC](https://azure.microsoft.com/pricing/details/automation/)
