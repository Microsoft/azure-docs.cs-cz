---
title: Povolit Azure Automation Start/Stop VMs during off-hours
description: V tomto článku se dozvíte, jak povolit funkci Start/Stop VMs during off-hours pro virtuální počítače Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 36f885416c5e9cb656d01a65b9c503f8897d2f9f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100593900"
---
# <a name="enable-startstop-vms-during-off-hours"></a>Povolení funkce Enable Start/Stop VMs during off-hours

Provedením kroků v tomto tématu zapněte funkci Start/Stop VMs during off-hours pro virtuální počítače s využitím nového nebo existujícího účtu Automation a propojeného pracovního prostoru Log Analytics. Po dokončení procesu instalace nakonfigurujte proměnné pro přizpůsobení funkce.

>[!NOTE]
>Pokud chcete tuto funkci použít u klasických virtuálních počítačů, potřebujete účet Spustit jako pro Classic, který se ve výchozím nastavení nevytvoří. Viz [Vytvoření účtu Spustit jako pro Azure Classic](automation-create-standalone-account.md#create-a-classic-run-as-account).
>

## <a name="create-resources-for-the-feature"></a>Vytvoření prostředků pro funkci

1. Přihlaste se k webu Azure [Portal](https://portal.azure.com).
2. Vyhledejte a vyberte **účty Automation**.
3. Na stránce účty Automation v seznamu vyberte svůj účet Automation.
4. Z účtu Automation vyberte **Spustit/zastavit virtuální počítač** v části **související prostředky**. Tady můžete kliknout na další **informace a povolit řešení**. Pokud již máte funkci nasazenou, můžete kliknout na možnost **Spravovat řešení** a vyhledat ji v seznamu.

   ![Povolit z účtu Automation](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Prostředek můžete také vytvořit z libovolného místa v Azure Portal kliknutím na **vytvořit prostředek**. Na stránce Marketplace zadejte klíčové slovo, například **Spustit** nebo **Spustit/zastavit**. Seznam se průběžně filtruje podle zadávaného textu. Případně můžete zadat jedno nebo více klíčových slov z celého jména funkce a potom stisknout klávesu **ENTER**. Ve výsledcích hledání vyberte **Start/Stop VMS during off-hours** .

5. Na stránce Start/Stop VMs during off-hours pro vybrané nasazení zkontrolujte souhrnné informace a klikněte na **vytvořit**.

   ![portál Azure](media/automation-solution-vm-management/azure-portal-01.png)

## <a name="configure-the-feature"></a>Konfigurace funkce

Po vytvoření prostředku se zobrazí stránka Přidat řešení. Před importem do předplatného automatizace budete vyzváni ke konfiguraci této funkce. Viz [konfigurace Start/Stop VMS during off-hours](automation-solution-vm-management-config.md).

   ![Stránka Přidat řešení správy virtuálních počítačů](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

## <a name="select-a-log-analytics-workspace"></a>Vybrat Log Analytics pracovní prostor

1. Na stránce Přidat řešení vyberte **pracovní prostor**. Vyberte Log Analytics pracovní prostor propojený s předplatným Azure, které používá účet Automation. 

2. Pokud nemáte pracovní prostor, vyberte **vytvořit nový pracovní prostor**. Na stránce Log Analytics pracovní prostor proveďte následující kroky:

   - Zadejte název nového pracovního prostoru Log Analytics, například **ContosoLAWorkspace**.
   - Vyberte **předplatné** , které chcete propojit, výběrem z rozevíracího seznamu, pokud výchozí volba není vhodná.
   - V případě **skupiny prostředků** můžete vytvořit novou skupinu prostředků nebo vybrat některou z existujících.
   - Vyberte **umístění**.
   - Vyberte **cenovou úroveň**. Vyberte možnost **za GB (samostatně)** . Protokoly Azure Monitor mají aktualizované [ceny](https://azure.microsoft.com/pricing/details/log-analytics/) a jedinou možností je úroveň na GB.

   > [!NOTE]
   > Při povolování funkcí jsou podporovány pouze určité oblasti pro propojení Log Analyticsho pracovního prostoru a účtu Automation. Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](how-to/region-mappings.md).

3. Po zadání požadovaných informací na stránce Log Analytics pracovního prostoru klikněte na **vytvořit**. Průběh můžete sledovat v části **oznámení** z nabídky, která vás po dokončení vrátí na stránku přidat řešení.

## <a name="add-automation-account"></a>Přidat účet Automation

Znovu přejděte na stránku přidat řešení a vyberte **účet Automation**. Můžete vybrat existující účet Automation, který ještě není propojený s pracovním prostorem Log Analytics. Pokud vytváříte nový pracovní prostor Log Analytics, můžete vytvořit nový účet Automation, který se k němu přidruží. Vyberte existující účet Automation, nebo klikněte na **vytvořit účet Automation** a na stránce Přidat účet Automation zadejte název účtu Automation do pole **název** .

Všechny ostatní možnosti se vyplní automaticky na základě vybraného pracovního prostoru Log Analytics. Tyto možnosti nemůžete změnit. Účet Spustit jako pro Azure je výchozí metoda ověřování pro Runbooky, které jsou součástí této funkce. 

Po kliknutí na **OK** se ověřují možnosti konfigurace a vytvoří se účet Automation. Průběh zpracování můžete sledovat prostřednictvím možnosti nabídky **Oznámení**.

## <a name="define-feature-parameters"></a>Definovat parametry funkce

1. Na stránce Přidat řešení vyberte **Konfigurace**. Zobrazí se stránka parametry.

    ![Stránka parametrů pro řešení](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

2. Zadejte hodnotu pro cílové pole **názvů zdrojových zdrojů** . Pole definuje názvy skupin, které obsahují virtuální počítače pro funkci, která se má spravovat. Můžete zadat více než jeden název a názvy oddělit čárkami (hodnoty nerozlišují velká a malá písmena). Je podporováno použití zástupného znaku, pokud jsou cílem virtuální počítače ve všech skupinách prostředků v rámci předplatného. Hodnoty jsou uloženy v `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` proměnných a.

    > [!IMPORTANT]
    > Výchozí hodnota pro **názvy cílových zdrojových zdrojů** je **&ast;** . Toto nastavení cílí na všechny virtuální počítače v rámci předplatného. Pokud nechcete, aby tato funkce zacílena na všechny virtuální počítače ve vašem předplatném, je nutné před výběrem plánu zadat seznam názvů skupin prostředků.
  
3. Zadejte hodnotu pro pole **seznam vyloučení (řetězce) virtuálního počítače** . Tato hodnota je název jednoho nebo více virtuálních počítačů z cílové skupiny prostředků. Můžete zadat více než jeden název a názvy oddělit čárkami (hodnoty nerozlišují velká a malá písmena). Je podporováno použití zástupného znaku. Tato hodnota je uložena v `External_ExcludeVMNames` proměnné.
  
4. Pomocí pole **plán** vyberte plán správy virtuálních počítačů pomocí této funkce. Vyberte počáteční datum a čas pro váš plán a vytvořte tak opakovaný denní plán, který začíná ve zvoleném čase. Výběr jiné oblasti není k dispozici. Pokud chcete nakonfigurovat plán na konkrétní časové pásmo po konfiguraci funkce, přečtěte si téma [Změna plánů spouštění a vypínání](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules).

5. Pokud chcete dostávat e-mailová oznámení ze [skupiny akcí](../azure-monitor/alerts/action-groups.md), přijměte výchozí hodnotu **Ano** v poli  **e-mailových oznámení** a zadejte platnou e-mailovou adresu. Pokud vyberete možnost **ne** , ale později se rozhodnete, že chcete dostávat e-mailová oznámení, můžete aktualizovat skupinu akcí vytvořenou pomocí platných e-mailových adres oddělených čárkami. 

6. Povolit následující pravidla upozornění:

   - `AutoStop_VM_Child`
   - `Scheduled_StartStop_Parent`
   - `Sequenced_StartStop_Parent`

## <a name="create-alerts"></a>Vytváření upozornění

Start/Stop VMs during off-hours neobsahuje předdefinovanou sadu výstrah. Přečtěte si téma [vytvoření výstrah protokolu pomocí Azure monitor](../azure-monitor/alerts/alerts-log.md) , kde se dozvíte, jak vytvořit neúspěšné výstrahy úlohy pro podporu vašich DevOpsch a provozních procesů a postupů.

## <a name="deploy-the-feature"></a>Nasazení funkce

1. Po nakonfigurování počátečního nastavení potřebného pro tuto funkci kliknutím na tlačítko **OK** zavřete stránku parametry.

2. Klikněte na **Vytvořit**. Po ověření všech nastavení se tato funkce nasadí do vašeho předplatného. Dokončení tohoto procesu může trvat několik sekund a průběh můžete sledovat v části **oznámení** z nabídky.

    > [!NOTE]
    > Pokud máte předplatné Azure Cloud Solution Provider (CSP), po dokončení nasazení klikněte v účtu Automation na **proměnné** v části **sdílené prostředky** a nastavte proměnnou [External_EnableClassicVMs](automation-solution-vm-management.md#variables) na **false**. Tím se řešení zastaví v hledání klasických prostředků virtuálních počítačů.

## <a name="next-steps"></a>Další kroky

* Pokud chcete tuto funkci nastavit, přečtěte si téma [Konfigurace virtuálních počítačů zastavit/spustit v době mimo špičku](automation-solution-vm-management-config.md).
* Informace o řešení chyb funkcí najdete v tématu [řešení potíží s Start/Stop VMS during off-hours](troubleshoot/start-stop-vm.md).