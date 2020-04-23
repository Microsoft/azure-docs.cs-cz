---
title: Povolit Azure Automation spustit nebo zastavit virtuální počítače v době nepracovních řešení
description: Tento článek popisuje, jak pro virtuální počítače Azure povolit řešení Azure Automation spustit nebo zastavit řešení virtuálního počítače.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: d47daa29c65f847fdeb33b9e24a892ac1f31b52a
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096958"
---
# <a name="enable-azure-automation-startstop-vms-solution"></a>Povolit Azure Automation řešení pro spouštění a zastavování virtuálních počítačů

Pomocí následujících kroků přidejte **Start/Stop VMS during off-hours** řešení do nového nebo existujícího účtu Automation a propojeného pracovního prostoru Log Analytics. Po dokončení procesu připojování nakonfigurujte proměnné pro přizpůsobení řešení.

>[!NOTE]
>Pokud chcete toto řešení použít u klasických virtuálních počítačů, potřebujete účet Spustit jako pro Classic, který se ve výchozím nastavení nevytvoří. Pokyny k vytvoření účtu Spustit jako pro Azure Classic najdete v tématu [Vytvoření účtu Spustit jako pro Azure Classic](automation-create-standalone-account.md#create-a-classic-run-as-account).
>

## <a name="enable-solution"></a>Povolení řešení

1. Přihlaste se k webu Azure [Portal](https://portal.azure.com).

2. Vyhledejte a vyberte **účty Automation**.

3. Na stránce účty Automation v seznamu vyberte svůj účet Automation.

4. Z účtu Automation vyberte **Spustit/zastavit virtuální počítač** v části **související prostředky**. Tady můžete kliknout na další **informace a povolit řešení**. Pokud už máte nasazené řešení pro spouštění a zastavování virtuálních počítačů, můžete ho vybrat kliknutím na **Spravovat řešení** a vyhledat ho v seznamu.

   ![Povolit z účtu Automation](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Můžete ho také vytvořit z libovolného místa v Azure Portal kliknutím na **vytvořit prostředek**. Na stránce Marketplace zadejte klíčové slovo, například **Spustit** nebo **Spustit/zastavit**. Seznam se průběžně filtruje podle zadávaného textu. Případně můžete zadat jedno nebo více klíčových slov z celého jména řešení a potom stisknout klávesu ENTER. Ve výsledcích hledání vyberte **Start/Stop VMS during off-hours** .

5. Na stránce **Start/Stop VMS during off-hours** pro vybrané řešení zkontrolujte souhrnné informace a klikněte na **vytvořit**.

   ![portál Azure](media/automation-solution-vm-management/azure-portal-01.png)

6. Zobrazí se stránka Přidat řešení. Před importem do předplatného automatizace budete vyzváni ke konfiguraci řešení.

   ![Stránka Přidat řešení správy virtuálních počítačů](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

7. Na stránce Přidat řešení vyberte **pracovní prostor**. Vyberte Log Analytics pracovní prostor propojený se stejným předplatným Azure, ve kterém je účet Automation. Pokud nemáte pracovní prostor, vyberte **vytvořit nový pracovní prostor**. Na stránce Log Analytics pracovní prostor proveďte následující kroky:

   - Zadejte název nového pracovního prostoru Log Analytics, například **ContosoLAWorkspace**.
   - Vyberte **předplatné** , které chcete propojit, výběrem z rozevíracího seznamu, pokud výchozí volba není vhodná.
   - V případě **skupiny prostředků**můžete vytvořit novou skupinu prostředků nebo vybrat některou z existujících.
   - Vyberte **umístění**.
   - Vyberte **cenovou úroveň**. Vyberte možnost **za GB (samostatně)** . Protokoly Azure Monitor mají aktualizované [ceny](https://azure.microsoft.com/pricing/details/log-analytics/) a jedinou možností je úroveň na GB.

   > [!NOTE]
   > Při povolování řešení jsou podporovány pouze určité oblasti pro propojení Log Analyticsho pracovního prostoru a účtu Automation.
   >
   > Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](how-to/region-mappings.md).

8. Po zadání požadovaných informací na stránce Log Analytics pracovního prostoru klikněte na **vytvořit**. Průběh můžete sledovat v části **oznámení** z nabídky, která vás po dokončení vrátí na stránku přidat řešení.

9. Na stránce Přidat řešení vyberte **účet Automation**. Pokud vytváříte nový pracovní prostor Log Analytics, můžete vytvořit nový účet Automation, který se k němu přidruží, nebo vybrat existující účet Automation, který ještě není propojený s pracovním prostorem Log Analytics. Vyberte existující účet Automation, nebo klikněte na **vytvořit účet Automation**a na stránce Přidat účet Automation zadejte následující informace:
 
   - Do pole **Název** zadejte název účtu služby Automation.

     Všechny ostatní možnosti se vyplní automaticky na základě vybraného pracovního prostoru Log Analytics. Tyto možnosti nelze upravovat. Účet Spustit v Azure jako představuje výchozí metodu ověřování pro runbooky obsažené v tomto řešení. Po kliknutí na **OK**se ověřují možnosti konfigurace a vytvoří se účet Automation. Průběh zpracování můžete sledovat prostřednictvím možnosti nabídky **Oznámení**.

10. Nakonec na stránce Přidat řešení vyberte **Konfigurace**. Zobrazí se stránka parametry.

    ![Stránka parametrů pro řešení](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Tady se zobrazí výzva k provedení těchto akcí:
  
   - Zadejte **cílové názvy zdrojových zdrojů**. Tyto hodnoty jsou názvy skupin prostředků, které obsahují virtuální počítače, které mají být spravovány tímto řešením. Můžete zadat více než jeden název a každý z nich oddělit čárkou (hodnoty nerozlišují velká a malá písmena). Je podporováno použití zástupného znaku, pokud jsou cílem virtuální počítače ve všech skupinách prostředků v rámci předplatného. Tato hodnota je uložena v proměnných **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupNames** .
  
   - Zadejte **seznam vyloučení virtuálních počítačů (řetězec)**. Tato hodnota je název jednoho nebo více virtuálních počítačů z cílové skupiny prostředků. Můžete zadat více než jeden název a každý z nich oddělit čárkou (hodnoty nerozlišují velká a malá písmena). Je podporováno použití zástupného znaku. Tato hodnota je uložena v proměnné **External_ExcludeVMNames** .
  
   - Vyberte **plán**. Vyberte datum a čas pro váš plán. Počínaje časem, který jste vybrali, se vytvoří znovu denní plán. Výběr jiné oblasti není k dispozici. Pokud chcete po konfiguraci řešení nakonfigurovat plán na konkrétní časové pásmo, přečtěte si téma [Změna plánu spuštění a vypnutí](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules).
  
   - Pokud chcete dostávat **e-mailová oznámení** ze skupiny akcí, přijměte výchozí hodnotu **Ano** a zadejte platnou e-mailovou adresu. Pokud vyberete možnost **ne** , ale později se rozhodnete, že chcete dostávat e-mailová oznámení, můžete aktualizovat [skupinu akcí](../azure-monitor/platform/action-groups.md) vytvořenou pomocí platných e-mailových adres oddělených čárkou. Musíte taky povolit následující pravidla upozornění:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > Výchozí hodnota pro **názvy cílových zdrojových zdrojů** je **&ast;**. Tím se cílí na všechny virtuální počítače v rámci předplatného. Pokud nechcete, aby řešení nevyhovovalo všem virtuálním počítačům ve vašem předplatném, je potřeba tuto hodnotu aktualizovat na seznam názvů skupin prostředků před povolením plánů.

11. Po nakonfigurování počátečního nastavení požadovaného pro řešení kliknutím na tlačítko **OK** zavřete stránku parametry a vyberte **vytvořit**. 

Po ověření všech nastavení se řešení nasadí do vašeho předplatného. Dokončení tohoto procesu může trvat několik sekund a průběh můžete sledovat v části **oznámení** z nabídky.

> [!NOTE]
> Pokud máte předplatné Azure Cloud Solution Provider (CSP), po dokončení nasazení klikněte v účtu Automation na **proměnné** v části **sdílené prostředky** a nastavte proměnnou [External_EnableClassicVMs](automation-solution-vm-management.md#variables) na **false**. Tím se řešení zastaví v hledání klasických prostředků virtuálních počítačů.

## <a name="next-steps"></a>Další kroky

Když teď máte řešení povolené, můžete ho [nakonfigurovat](automation-solution-vm-management-config.md) tak, aby podporovalo požadavky na správu virtuálních počítačů.