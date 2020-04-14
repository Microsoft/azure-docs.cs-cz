---
title: Povolení řešení spuštění a zastavení virtuálních disponecí mimo pracovní dobu
description: Tento článek popisuje, jak povolit řešení Azure Automation Start/Stop VM pro vaše virtuální počítače Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 7b619d3c9b4b334e637d6a1c456256cb33ad5134
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261376"
---
# <a name="enable-azure-startstop-vms-solution"></a>Povolení řešení Azure Start/Stop VM

Pomocí následujících kroků přidejte virtuální chod start/stop během mimopracovní doby do nového nebo existujícího účtu Automation a propojeného pracovního prostoru Log Analytics. Po dokončení procesu registrace nakonfigurujte proměnné tak, aby přizpůsobily řešení.

>[!NOTE]
>Chcete-li toto řešení použít s klasickými virtuálními aplikacemi, potřebujete účet Classic Run As, který není vytvořen ve výchozím nastavení. Pokyny k vytvoření účtu Classic Run As naleznete v [tématu Vytvoření účtu klasického spuštění jako](automation-create-standalone-account.md#create-a-classic-run-as-account).
>

## <a name="enable-solution"></a>Povolení řešení

1. Přihlaste se k [portálu](https://portal.azure.com)Azure .

2. Vyhledejte a vyberte **automatizační účty**.

3. Na stránce **Účty automatizace** vyberte účet automatizace ze seznamu.

4. V části Účet automatizace vyberte v části **Související prostředky** **položku Spustit/zastavit virtuální ms** . Tady můžete kliknout na **Další informace o řešení a povolit řešení**. Pokud už máte nasazené řešení virtuálního aplikace Start/Stop, můžete ho vybrat kliknutím na **Spravovat řešení** a jeho nahledáním v seznamu.

   ![Povolit z účtu automatizace](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Můžete ji také vytvořit z libovolného místa na webu Azure Portal, kliknutím na **vytvořit prostředek**. Na stránce Marketplace zadejte klíčové slovo, například **Start** nebo **Start/Stop**. Seznam se průběžně filtruje podle zadávaného textu. Případně můžete zadat jedno nebo více klíčových slov z celého názvu řešení a pak stisknout enter. Z výsledků hledání vyberte **Možnost Spustit/zastavit virtuální chod mimo pracovní dobu.**

5. Na stránce **Start/Stop VM během mimopracovní doby** vybraného řešení zkontrolujte souhrnné informace a klepněte na tlačítko **Vytvořit**.

   ![portál Azure](media/automation-solution-vm-management/azure-portal-01.png)

6. Zobrazí se stránka **Přidat řešení.** Před importem do předplatného automatizace budete vyzváni ke konfiguraci řešení.

   ![Stránka Přidání řešení správy virtuálních montovek](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

7. Na stránce **Přidat řešení** vyberte **Pracovní prostor**. Vyberte pracovní prostor Log Analytics, který je propojený se stejným předplatným Azure, ve které se nachází účet Automation. Pokud pracovní prostor nemáte, vyberte **Vytvořit nový pracovní prostor**. Na stránce **pracovního prostoru Log Analytics** proveďte následující kroky:

   - Zadejte název nového **pracovního prostoru Analýzy protokolů**, například "ContosoLAWorkspace".
   - Vyberte **předplatné,** na které chcete vytvořit odkaz, výběrem z rozevíracího seznamu, pokud výchozí vybráná možnost není vhodná.
   - V **aplikaci Skupina prostředků**můžete vytvořit novou skupinu prostředků nebo vybrat existující skupinu.
   - Vyberte **umístění**.
   - Vyberte **cenovou úroveň**. Zvolte možnost **Za GB (samostatný).** Protokoly Azure Monitor mají aktualizované [ceny](https://azure.microsoft.com/pricing/details/log-analytics/) a úroveň za GB je jedinou možností.

   > [!NOTE]
   > Při povolování řešení se podporuje propojení pracovního prostoru služby Log Analytics a účtu Automation pouze v určitých oblastech.
   >
   > Seznam podporovaných dvojic mapování naleznete v tématu [Mapování oblasti pro účet automatizace a pracovní prostor Log Analytics](how-to/region-mappings.md).

8. Po zadání požadovaných informací na stránce **pracovního prostoru Log Analytics** klepněte na tlačítko **Vytvořit**. Jeho průběh můžete sledovat v části **Oznámení** z nabídky, která vás po dokončení vrátí na stránku **Přidat řešení.**

9. Na stránce **Přidat řešení** vyberte **účet Automatizace**. Pokud vytváříte nový pracovní prostor Log Analytics, můžete vytvořit nový účet Automation, který se k němu přidrušit, nebo vybrat existující účet automatizace, který ještě není propojený s pracovním prostorem Log Analytics. Vyberte existující účet automatizace nebo klepněte na tlačítko **Vytvořit účet automatizace**a na stránce **Přidat účet automatizace** uveďte následující informace:
 
   - Do pole **Název** zadejte název účtu služby Automation.

     Všechny ostatní možnosti jsou automaticky vyplněny na základě vybraného pracovního prostoru Analýzy protokolů. Tyto možnosti nelze změnit. Účet Spustit v Azure jako představuje výchozí metodu ověřování pro runbooky obsažené v tomto řešení. Po klepnutí na tlačítko **OK**budou možnosti konfigurace ověřeny a vytvoří se účet automatizace. Průběh zpracování můžete sledovat prostřednictvím možnosti nabídky **Oznámení**.

10. Nakonec na stránce **Přidat řešení** vyberte **možnost Konfigurace**. Zobrazí se stránka **Parametry.**

    ![Stránka Parametrů pro řešení](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Zde budete vyzváni k:
  
   - Zadejte **názvy cílových skupin zdrojů**. Tyto hodnoty jsou názvy skupin prostředků, které obsahují virtuální chod, které mají být spravovány tímto řešením. Můžete zadat více než jeden název a oddělit každý pomocí čárky (hodnoty nejsou malá a velká písmena). Je podporováno použití zástupného znaku, pokud jsou cílem virtuální počítače ve všech skupinách prostředků v rámci předplatného. Tato hodnota je uložena v **proměnné External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupNames.**
  
   - Zadejte **seznam vyloučení virtuálních zařízení (řetězec).** Tato hodnota je název jednoho nebo více virtuálních počítačů z cílové skupiny prostředků. Můžete zadat více než jeden název a oddělit každý pomocí čárky (hodnoty nejsou malá a velká písmena). Použití zástupné znakové karty je podporováno. Tato hodnota je uložena v **proměnné External_ExcludeVMNames.**
  
   - Vyberte **plán**. Vyberte datum a čas plánu. Bude vytvořen opakující se denní plán počínaje vybraným časem. Výběr jiné oblasti není k dispozici. Chcete-li nakonfigurovat plán na konkrétní časové pásmo po konfiguraci řešení, přečtěte si informace [o úpravě plánu spuštění a vypnutí](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules).
  
   - Chcete-li dostávat **e-mailová oznámení** ze skupiny akcí, přijměte výchozí hodnotu **Ano** a zadejte platnou e-mailovou adresu. Pokud vyberete **možnost Ne,** ale později se rozhodnete, že chcete dostávat e-mailová oznámení, můžete aktualizovat [skupinu akcí,](../azure-monitor/platform/action-groups.md) která je vytvořena s platnými e-mailovými adresami oddělenými čárkou. Musíte také povolit následující pravidla výstrah:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > Výchozí hodnota pro **cílové názvy skupin zdrojů** je . **&ast;** To se zaměřuje na všechny virtuální aplikace v předplatném. Pokud nechcete, aby řešení cílit na všechny virtuální počítače ve vašem předplatném tato hodnota musí být aktualizována na seznam názvů skupin prostředků před povolením plány.

11. Po konfiguraci počátečního nastavení potřebného pro řešení zavřete stránku **Parametry** klepnutím na **tlačítko OK** a vyberte **příkaz Vytvořit**. 

Po ověření všech nastavení se řešení nasadí do vašeho předplatného. Tento proces může trvat několik sekund a můžete sledovat jeho průběh v části **Oznámení** z nabídky.

> [!NOTE]
> Pokud máte předplatné Azure Cloud Solution Provider (Azure CSP), po dokončení nasazení přejděte v účtu Automation na **proměnné** v části **Sdílené prostředky** a nastavte [**proměnnou External_EnableClassicVMs**](automation-solution-vm-management.md#variables) na **False**. Tím zastavíte řešení v hledání prostředků klasického virtuálního soudu.

## <a name="next-steps"></a>Další kroky

Teď, když máte povolené řešení, můžete [ho nakonfigurovat](automation-solution-vm-management-config.md) tak, aby podporovalo požadavky na správu virtuálních her.