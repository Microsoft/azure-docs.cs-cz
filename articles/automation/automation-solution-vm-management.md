---
title: Spuštění/zastavení virtuálních disponování během mimopracovního řešení
description: Toto řešení správy virtuálních počítačů spustí a zastaví vaše virtuální počítače Azure Resource Manager podle plánu a proaktivně monitoruje z protokolů Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2020
ms.topic: conceptual
ms.openlocfilehash: cbf181b9a6d3860854c7b61cca0e6c50810cced9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278542"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Řešení Start/Stop VMs during off-hours v Azure Automation

Spuštění a zastavení virtuálních počítačů během off-hours řešení spustit a zastaví vaše virtuální počítače Azure na uživatelem definované plány, poskytuje přehledy prostřednictvím protokolů Azure Monitor a odesílá volitelné e-maily pomocí [skupin akcí](../azure-monitor/platform/action-groups.md). Podporuje Azure Resource Manager a klasické virtuální počítače pro většinu scénářů. Chcete-li použít toto řešení s klasickými virtuálními aplikacemi, potřebujete účet Classic RunAs, který není vytvořen ve výchozím nastavení. Pokyny k vytvoření účtu Classic RunAs naleznete [v tématu Klasické účty run-as](automation-create-standalone-account.md#classic-run-as-accounts).

> [!NOTE]
> Řešení Start/Stop VM během mimopracovní doby bylo testováno pomocí modulů Azure, které se importují do vašeho účtu Automation při nasazování řešení. Řešení aktuálně nefunguje s novějšími verzemi modulu Azure. To má vliv pouze na účet automatizace, který používáte ke spuštění spuštění virtuálních účtů Start/Stop během mimopracovní ho řešení. Novější verze modulu Azure můžete dál používat v jiných účtech automatizace, jak je popsáno v části [Jak aktualizovat moduly Azure PowerShell u Azure Automation.](automation-update-azure-modules.md)

Toto řešení poskytuje decentralizovou možnost automatizace s nízkými náklady pro uživatele, kteří chtějí optimalizovat náklady na virtuální počítač. S tímto řešením můžete:

- Naplánujte spuštění a zastavení virtuálních montovek.
- Naplánujte, aby se virtuální počítače spouštěla a zastavovala ve vzestupném pořadí pomocí značek Azure (není podporována pro klasické virtuální počítače).
- Virtuální počítače autostop na základě nízké využití procesoru.

Níže jsou omezení s aktuální řešení:

- Toto řešení spravuje virtuální počítače v libovolné oblasti, ale lze použít jenom ve stejném předplatném jako váš účet Azure Automation.
- Toto řešení je dostupné v Azure a AzureGov pro všechny oblasti, které podporuje pracovní prostor Analýzy protokolů, účet Azure Automation a výstrahy. AzureGov oblasti aktuálně nepodporují funkce e-mailu.

> [!NOTE]
> Pokud používáte řešení pro klasické virtuální počítače, pak všechny vaše virtuální počítače se budou zpracovávat postupně na cloudovou službu. Virtuální počítače se stále zpracovávají paralelně napříč různými cloudovými službami. Pokud máte více než 20 virtuálních počítačů na cloudovou službu, doporučujeme vytvořit více plánů s nadřazenou **ScheduledStartStop_Parent** runbooku a zadat 20 virtuálních počítačů na plán. Ve vlastnostech plánu zadejte jako seznam oddělený čárkami názvy virtuálních počítačů v parametru **VMList.** V opačném případě pokud úloha automatizace pro toto řešení běží více než tři hodiny, je dočasně uvolněna nebo zastavena na spravedlivý limit [sdílené složky.](automation-runbook-execution.md#fair-share)
>
> Předplatná Azure Cloud Solution Provider (Azure CSP) podporují jenom model Azure Resource Manager, jiné služby Azure Resource Manager nejsou v programu dostupné. Při spuštění řešení Start/Stop může dojít k chybám, protože má rutiny pro správu klasických prostředků. Další informace o csp najdete [v tématu Dostupné služby v předplatných CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Pokud používáte předplatné CSP, měli byste upravit [**proměnnou External_EnableClassicVMs**](#variables) na **False** po nasazení.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Požadavky

Sady Runbook pro toto řešení fungují s [účtem Azure Run As](automation-create-runas-account.md). Účet Spustit jako je upřednostňovaná metoda ověřování, protože používá ověřování certifikátu namísto hesla, jehož platnost může vypršet nebo často měnit.

Doporučujeme použít samostatný účet automatizace pro řešení spuštění a zastavení virtuálního provozu. Důvodem je, že verze modulu Azure jsou často upgradovány a jejich parametry se mohou změnit. Řešení Start/Stop VM není upgradován na stejné kadence, takže nemusí fungovat s novější verze rutiny, které používá. Doporučujeme také otestovat aktualizace modulů v testovacím účtu automatizace před jejich importem do produkčního účtu automatizace.

### <a name="permissions-needed-to-deploy"></a>Oprávnění potřebná k nasazení

Existují určitá oprávnění, která uživatel musí mít k nasazení spuštění a zastavení virtuálních aplikací během mimo pracovní dobu řešení. Tato oprávnění se liší, pokud používáte předem vytvořený pracovní prostor Automation Account a Log Analytics nebo vytváříte nové během nasazení. Pokud jste přispěvatelem předplatného a globálním správcem v tenantovi služby Azure Active Directory, není nutné konfigurovat následující oprávnění. Pokud tato práva nemáte nebo potřebujete nakonfigurovat vlastní roli, přečtěte si níže požadovaná oprávnění.

#### <a name="pre-existing-automation-account-and-log-analytics-workspace"></a>Již existující pracovní prostor Automatizační účet a log Analytics

Chcete-li nasadit řešení Spuštění a zastavení virtuálních počítačových aplikací mimo pracovní dobu do existujícího pracovního prostoru Automation Account a Log Analytics, vyžaduje uživatel, který řešení nasazuje, následující oprávnění ve **skupině prostředků**. Další informace o rolích najdete [v tématu Vlastní role pro prostředky Azure](../role-based-access-control/custom-roles.md).

| Oprávnění | Rozsah|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | Skupina prostředků |
| Microsoft.Automation/automationAccounts/variables/write | Skupina prostředků |
| Microsoft.Automation/automationAccounts/schedules/write | Skupina prostředků |
| Microsoft.Automation/automationAccounts/runbook/write | Skupina prostředků |
| Microsoft.Automation/automationAccounts/connections/write | Skupina prostředků |
| Microsoft.Automation/automationAccounts/certificates/write | Skupina prostředků |
| Microsoft.Automation/automationAccounts/modules/write | Skupina prostředků |
| Microsoft.Automation/automationAccounts/modules/read | Skupina prostředků |
| Microsoft.automation/automationAccounts/jobSchedules/write | Skupina prostředků |
| Microsoft.Automation/automationAccounts/jobs/write | Skupina prostředků |
| Microsoft.Automation/automationAccounts/jobs/read | Skupina prostředků |
| Microsoft.OperationsManagement/solutions/write | Skupina prostředků |
| Microsoft.OperationalInsights/pracovní prostory/* | Skupina prostředků |
| Microsoft.Insights/diagnosticSettings/write | Skupina prostředků |
| Microsoft.Insights/ActionGroups/Zápis | Skupina prostředků |
| Microsoft.Insights/ActionGroups/čt | Skupina prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Skupina prostředků |
| Microsoft.Resources/deployments/* | Skupina prostředků |

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>Nový účet automatizace a nový pracovní prostor Log Analytics

Chcete-li nasadit řešení spuštění a zastavení virtuálních aplikací během mimo pracovní dobu do nového pracovního prostoru automatizačního účtu a analýzy protokolů, potřebuje uživatel, který nasadí řešení, oprávnění definovaná v předchozí části a následující oprávnění:

- Spolusprávce předplatného – je potřeba k vytvoření klasického spuštění jako účtu, pokud se chystáte spravovat klasické virtuální počítače. [Klasické runas účty](automation-create-standalone-account.md#classic-run-as-accounts) již nejsou vytvořeny ve výchozím nastavení.
- Člen role **vývojáře aplikací služby** [Azure Active Directory.](../active-directory/users-groups-roles/directory-assign-admin-roles.md) Další informace o konfiguraci spouštět jako účty naleznete v [tématu Oprávnění ke konfiguraci účtů Spustit jako](manage-runas-account.md#permissions).
- Přispěvatel k předplatnému nebo následujícím oprávněním.

| Oprávnění |Rozsah|
| --- | --- |
| Microsoft.Authorization/Operations/read | Předplatné|
| Microsoft.Authorization/authorization/read |Předplatné|
| Microsoft.Authorization/roleAssignments/read | Předplatné |
| Microsoft.Authorization/roleAssignments/write | Předplatné |
| Microsoft.Authorization/roleAssignments/delete | Předplatné |
| Microsoft.Automation/automationAccounts/connections/read | Skupina prostředků |
| Microsoft.Automation/automationAccounts/certificates/read | Skupina prostředků |
| Microsoft.Automation/automationAccounts/write | Skupina prostředků |
| Microsoft.OperationalInsights/pracovní prostory/zápis | Skupina prostředků |

## <a name="deploy-the-solution"></a>Nasazení řešení

Proveďte následující kroky k přidání spuštění a zastavení virtuálních počítačů během mimo pracovní dobu řešení do účtu automatizace a potom nakonfigurujte proměnné pro přizpůsobení řešení.

1. V části Související prostředky vyberte v části **Související prostředky**možnost **Spustit/zastavit virtuální virtuální ms** . Tady můžete kliknout na **Další informace o řešení a povolit řešení**. Pokud už máte nasazené řešení virtuálního aplikace Start/Stop, můžete ho vybrat kliknutím na **Spravovat řešení** a jeho nahledáním v seznamu.

   ![Povolit z účtu automatizace](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Můžete ji také vytvořit z libovolného místa na webu Azure Portal, kliknutím na **vytvořit prostředek**. Na stránce Marketplace zadejte klíčové slovo, například **Start** nebo **Start/Stop**. Seznam se průběžně filtruje podle zadávaného textu. Případně můžete zadat jedno nebo více klíčových slov z celého názvu řešení a pak stisknout enter. Z výsledků hledání vyberte **Možnost Spustit/zastavit virtuální chod mimo pracovní dobu.**

2. Na stránce **Start/Stop VM během mimopracovní doby** vybraného řešení zkontrolujte souhrnné informace a klepněte na tlačítko **Vytvořit**.

   ![portál Azure](media/automation-solution-vm-management/azure-portal-01.png)

3. Zobrazí se stránka **Přidat řešení.** Před importem do předplatného automatizace budete vyzváni ke konfiguraci řešení.

   ![Stránka Přidání řešení správy virtuálních montovek](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. Na stránce **Přidat řešení** vyberte **Pracovní prostor**. Vyberte pracovní prostor Log Analytics, který je propojený se stejným předplatným Azure, ve které se nachází účet Automation. Pokud pracovní prostor nemáte, vyberte **Vytvořit nový pracovní prostor**. Na stránce **pracovního prostoru Log Analytics** proveďte následující kroky:
   - Zadejte název nového **pracovního prostoru Analýzy protokolů**, například "ContosoLAWorkspace".
   - Vyberte **předplatné,** na které chcete vytvořit odkaz, výběrem z rozevíracího seznamu, pokud výchozí vybráná možnost není vhodná.
   - V **aplikaci Skupina prostředků**můžete vytvořit novou skupinu prostředků nebo vybrat existující skupinu.
   - Vyberte **umístění**.
   - Vyberte **cenovou úroveň**. Zvolte možnost **Za GB (samostatný).** Protokoly Azure Monitor mají aktualizované [ceny](https://azure.microsoft.com/pricing/details/log-analytics/) a úroveň za GB je jedinou možností.

   > [!NOTE]
   > Při povolování řešení se podporuje propojení pracovního prostoru služby Log Analytics a účtu Automation pouze v určitých oblastech.
   >
   > Seznam podporovaných dvojic mapování naleznete v tématu [Mapování oblasti pro účet automatizace a pracovní prostor Log Analytics](how-to/region-mappings.md).

5. Po zadání požadovaných informací na stránce **pracovního prostoru Log Analytics** klepněte na tlačítko **Vytvořit**. Jeho průběh můžete sledovat v části **Oznámení** z nabídky, která vás po dokončení vrátí na stránku **Přidat řešení.**
6. Na stránce **Přidat řešení** vyberte **účet Automatizace**. Pokud vytváříte nový pracovní prostor Log Analytics, můžete vytvořit nový účet Automation, který se k němu přidrušit, nebo vybrat existující účet automatizace, který ještě není propojený s pracovním prostorem Log Analytics. Vyberte existující účet automatizace nebo klepněte na tlačítko **Vytvořit účet automatizace**a na stránce **Přidat účet automatizace** uveďte následující informace:
   - Do pole **Název** zadejte název účtu služby Automation.

     Všechny ostatní možnosti jsou automaticky vyplněny na základě vybraného pracovního prostoru Analýzy protokolů. Tyto možnosti nelze změnit. Účet Spustit v Azure jako představuje výchozí metodu ověřování pro runbooky obsažené v tomto řešení. Po klepnutí na tlačítko **OK**budou možnosti konfigurace ověřeny a vytvoří se účet automatizace. Průběh zpracování můžete sledovat prostřednictvím možnosti nabídky **Oznámení**.

7. Nakonec na stránce **Přidat řešení** vyberte **možnost Konfigurace**. Zobrazí se stránka **Parametry.**

   ![Stránka Parametrů pro řešení](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Zde budete vyzváni k:
   - Zadejte **názvy cílových skupin zdrojů**. Tyto hodnoty jsou názvy skupin prostředků, které obsahují virtuální chod, které mají být spravovány tímto řešením. Můžete zadat více než jeden název a oddělit každý pomocí čárky (hodnoty nejsou malá a velká písmena). Je podporováno použití zástupného znaku, pokud jsou cílem virtuální počítače ve všech skupinách prostředků v rámci předplatného. Tato hodnota je uložena v **proměnné External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupNames.**
   - Zadejte **seznam vyloučení virtuálních zařízení (řetězec).** Tato hodnota je název jednoho nebo více virtuálních počítačů z cílové skupiny prostředků. Můžete zadat více než jeden název a oddělit každý pomocí čárky (hodnoty nejsou malá a velká písmena). Použití zástupné znakové karty je podporováno. Tato hodnota je uložena v **proměnné External_ExcludeVMNames.**
   - Vyberte **plán**. Vyberte datum a čas plánu. Bude vytvořen opakující se denní plán počínaje vybraným časem. Výběr jiné oblasti není k dispozici. Chcete-li nakonfigurovat plán na konkrétní časové pásmo po konfiguraci řešení, přečtěte si informace [o úpravě plánu spuštění a vypnutí](#modify-the-startup-and-shutdown-schedules).
   - Chcete-li dostávat **e-mailová oznámení** ze skupiny akcí, přijměte výchozí hodnotu **Ano** a zadejte platnou e-mailovou adresu. Pokud vyberete **možnost Ne,** ale později se rozhodnete, že chcete dostávat e-mailová oznámení, můžete aktualizovat [skupinu akcí,](../azure-monitor/platform/action-groups.md) která je vytvořena s platnými e-mailovými adresami oddělenými čárkou. Musíte také povolit následující pravidla výstrah:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > Výchozí hodnota pro **cílové názvy skupin zdrojů** je . **&ast;** To se zaměřuje na všechny virtuální aplikace v předplatném. Pokud nechcete, aby řešení cílit na všechny virtuální počítače ve vašem předplatném tato hodnota musí být aktualizována na seznam názvů skupin prostředků před povolením plány.

8. Po konfiguraci počátečního nastavení potřebného pro řešení zavřete stránku **Parametry** klepnutím na **tlačítko OK** a vyberte **příkaz Vytvořit**. Po ověření všech nastavení se řešení nasadí do vašeho předplatného. Tento proces může trvat několik sekund a můžete sledovat jeho průběh v části **Oznámení** z nabídky.

> [!NOTE]
> Pokud máte předplatné Azure Cloud Solution Provider (Azure CSP), po dokončení nasazení přejděte v účtu Automation na **proměnné** v části **Sdílené prostředky** a nastavte [**proměnnou External_EnableClassicVMs**](#variables) na **False**. Tím zastavíte řešení v hledání prostředků klasického virtuálního soudu.

## <a name="scenarios"></a>Scénáře

Řešení obsahuje tři odlišné scénáře. Jedná se o tyto scénáře:

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>Scénář 1: Spuštění a zastavení virtuálních počítačů podle plánu

Tento scénář je výchozí konfigurace při prvním nasazení řešení. Můžete ji například nakonfigurovat tak, aby zastavila všechny virtuální počítače v rámci předplatného, když večer opustíte práci, a začít je ráno, když jste zpátky v kanceláři. Když nakonfigurujete plány **Naplánované StartVM** a **Scheduled-StopVM** během nasazení, spustí a zastaví cílené virtuální počítače. Konfigurace tohoto řešení pouze zastavit virtuální počítače je podporována, najdete [v tématu Úprava plánů spuštění a vypnutí se dozvíte,](#modify-the-startup-and-shutdown-schedules) jak nakonfigurovat vlastní plán.

> [!NOTE]
> Časové pásmo je aktuální časové pásmo při konfiguraci parametru plánu času. Je však uloženve formátu UTC v Azure Automation. Není třeba provést převod časového pásma, protože je zpracována během nasazení.

Virtuální chody jsou v oboru, které najdete tak, že nakonfigurujete následující proměnné: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**a **External_ExcludeVMNames**.

Můžete povolit cílení akce proti předplatnému a skupině prostředků nebo cílení na konkrétní seznam virtuálních aplikací, ale ne obojí.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Cílení na akce zahájení a zastavení proti předplatnému a skupině prostředků

1. Nakonfigurujte **proměnné External_Stop_ResourceGroupNames** a **External_ExcludeVMNames** k určení cílových virtuálních počítače.
2. Povolte a aktualizujte plány **Naplánovaného startvm** a **plánovaného stopvm.**
3. Spusťte **ScheduledStartStop_Parent** runbook s parametrem ACTION nastaveným na **spuštění** a parametrem WHATIF nastaveným na **Hodnotu True,** aby se zobrazil náhled změn.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Cílení na počáteční a zastavení akce podle seznamu virtuálních mís

1. **Spusťte ScheduledStartStop_Parent** runbook s parametrem ACTION nastaveným na **začátek**, přidejte seznam virtuálních knih odstředěných čárkami do parametru *VMList* a nastavte parametr WHATIF na **hodnotu True**. Prohlédněte si náhled změn.
1. Nakonfigurujte **parametr External_ExcludeVMNames** se seznamem virtuálních počítače odděleným čárkami (VM1, VM2, VM3).
1. Tento scénář nerespektuje **proměnné External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupnames.** V tomto scénáři je třeba vytvořit vlastní plán automatizace. Podrobnosti najdete [v tématu Plánování runbooku v Azure Automation](../automation/automation-schedules.md).

> [!NOTE]
> Hodnota pro **cílové názvy skupin zdrojů** je uložena jako hodnota pro **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupNames**. Pro další rozlišovací schopnost můžete upravit každou z těchto proměnných tak, aby cílila na různé skupiny prostředků. Pro akci zahájení použijte **External_Start_ResourceGroupNames**a pro akci stop použijte **External_Stop_ResourceGroupNames**. Virtuální aplikace se automaticky přidají do plánů zahájení a zastavení.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>Scénář 2: Spuštění a zastavení virtuálního počítače v pořadí pomocí značek

V prostředí, které obsahuje dvě nebo více součástí na více virtuálních počítačích podporujících distribuované úlohy, je důležité podporovat pořadí, ve kterém jsou komponenty spuštěny a zastaveny v pořadí. Tento scénář můžete provést provedením následujících kroků:

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Cílení na akce zahájení a zastavení proti předplatnému a skupině prostředků

1. Přidejte **sequencestart** a **sequencestop** tag s kladnou hodnotou celé číslo do virtuálních disek, které jsou cílené v **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupNames** proměnných. Akce start a stop se provádějí ve vzestupném pořadí. Informace o tom, jak označit virtuální počítač, najdete [v tématu Označení virtuálního počítače s Windows v Azure](../virtual-machines/windows/tag.md) a označení [virtuálního počítače s Linuxem v Azure](../virtual-machines/linux/tag.md).
1. Upravte plány **Sequenced-StartVM** a **Sequenced-StopVM** na datum a čas, které splňují vaše požadavky a umožňují plán.
1. Spusťte **SequencedStartStop_Parent** runbook s parametrem ACTION nastaveným na **spuštění** a parametrem WHATIF nastaveným na **hodnotu True** pro zobrazení náhledu změn.
1. Náhled akce a provést všechny nezbytné změny před implementací proti produkční virtuální chod. Až budete připraveni, ručně spusťte runbook s parametrem nastaveným na **False**nebo nechte automatizaci naplánovat **sekvenční StartVM** a **Sequenced-StopVM** spustit automaticky podle předepsaného plánu.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Cílení na počáteční a zastavení akce podle seznamu virtuálních mís

1. Přidejte **sequencestart** a **sequencestop** tag s kladnou hodnotou celé číslo do virtuálních disek, které chcete přidat do parametru **VMList.**
1. **Spusťte SequencedStartStop_Parent** runbook s parametrem ACTION nastaveným na **začátek**, přidejte seznam virtuálních knih odstředěných čárkami do parametru *VMList* a nastavte parametr WHATIF na **hodnotu True**. Prohlédněte si náhled změn.
1. Nakonfigurujte **parametr External_ExcludeVMNames** se seznamem virtuálních počítače odděleným čárkami (VM1, VM2, VM3).
1. Tento scénář nerespektuje **proměnné External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupnames.** V tomto scénáři je třeba vytvořit vlastní plán automatizace. Podrobnosti najdete [v tématu Plánování runbooku v Azure Automation](../automation/automation-schedules.md).
1. Náhled akce a provést všechny nezbytné změny před implementací proti produkční virtuální chod. Až budete připraveni, ručně spusťte monitorování a diagnostiku/monitorování-akce-groupsrunbook s parametrem **nastaveným**na False , nebo nechte automatizace naplánovat **sequenced-StartVM** a **Sequenced-StopVM** spustit automaticky podle předepsaného plánu.

## <a name="solution-components"></a>Součásti řešení

Toto řešení zahrnuje předem nakonfigurované sady runbooků, plány a integraci s protokoly Azure Monitoru, takže můžete přizpůsobit spuštění a vypnutí virtuálních počítačů vašim obchodním potřebám.

### <a name="runbooks"></a>Runbooky

V následující tabulce jsou uvedeny sady Runbook nasazené do vašeho účtu Automation tímto řešením. Neprovávejte změny kódu runbooku. Místo toho napište vlastní runbook pro nové funkce.

> [!IMPORTANT]
> Nespouštějte přímo žádný runbook s "podřízeným" připojeným k jeho názvu.

Všechny nadřazené sady Runbook obsahují parametr _WhatIf._ Když je nastavena na **True**, _WhatIf_ podporuje podrobné podrobnosti o přesné chování runbook se při spuštění bez _WhatIf_ parametr a ověřuje správné virtuální počítače jsou cílené. Sada Runbook provádí své definované akce pouze v případě, že je parametr _WhatIf_ nastaven na **hodnotu False**.

|Runbook | Parametry | Popis|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | Objekt VM <br> Akce výstrahy <br> WebHookURI | Volána z nadřazeného runbooku. Tento soubor Runbook vytváří výstrahy pro základě prostředků pro scénář AutoStop.|
|AutoStop_CreateAlert_Parent | Seznam VMList<br> WhatIf: Pravda nebo nepravda  | Vytvoří nebo aktualizuje pravidla výstrah Azure na virtuálních počítačích v cílovépředplatné nebo skupiny prostředků. <br> VMList: Seznam virtuálních vyvěšených podle čárek. Například _vm1, vm2, vm3_.<br> *WhatIf* ověří logiku runbooku bez spuštění.|
|AutoStop_Disable | Žádná | Zakáže výstrahy automatického zastavení a výchozí plán.|
|AutoStop_StopVM_Child | WebHookData | Volána z nadřazeného runbooku. Pravidla výstrah volání tohoto runbooku zastavit virtuální ho.|
|Bootstrap_Main | Žádná | Používá se jednou nastavit bootstrap konfigurace, jako je webhookURI, které jsou obvykle nejsou přístupné ze Správce prostředků Azure. Tato kniha runbook je automaticky odebrána po úspěšném nasazení.|
|ScheduledStartStop_Child | VMName <br> Akce: Spuštění nebo zastavení <br> ResourceGroupName | Volána z nadřazeného runbooku. Provede akci zahájení nebo zastavení pro naplánovanou zastávku.|
|ScheduledStartStop_Parent | Akce: Spuštění nebo zastavení <br>Seznam VMList <br> WhatIf: Pravda nebo nepravda | Toto nastavení ovlivní všechny virtuální ho disponia v předplatném. Upravte **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupNames** spouštět pouze v těchto cílových skupinách prostředků. Konkrétní virtuální hody můžete také vyloučit aktualizací **proměnné External_ExcludeVMNames.**<br> VMList: Seznam virtuálních vyvěšených podle čárek. Například _vm1, vm2, vm3_.<br> _WhatIf_ ověří logiku runbooku bez spuštění.|
|SequencedStartStop_Parent | Akce: Spuštění nebo zastavení <br> WhatIf: Pravda nebo nepravda<br>Seznam VMList| Vytvořte značky s názvem **sequencestart** a **sequencestop** na každém virtuálním počítači, pro který chcete sekvencovat aktivitu start/stop. Tyto názvy značek rozlišují malá a velká písmena. Hodnota značky by měla být kladné celé číslo (1, 2, 3), které odpovídá pořadí, ve kterém chcete spustit nebo zastavit. <br> VMList: Seznam virtuálních vyvěšených podle čárek. Například _vm1, vm2, vm3_. <br> _WhatIf_ ověří logiku runbooku bez spuštění. <br> **Poznámka:** Virtuální počítače musí být v rámci skupin prostředků definovaných jako External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames a External_ExcludeVMNames v proměnných Azure Automation. Musí mít příslušné značky, aby se akce projevily.|

### <a name="variables"></a>Proměnné

V následující tabulce jsou uvedeny proměnné vytvořené v účtu automatizace. Upravujte pouze proměnné s předponou **External**. Změna proměnných s předponou **Vnitřní** způsobuje nežádoucí účinky.

|Proměnná | Popis|
|---------|------------|
|External_AutoStop_Condition | Podmíněný operátor potřebný pro konfiguraci podmínky před aktivací výstrahy. Přijatelné hodnoty jsou **GreaterThan**, **GreaterThanOrEqual**, **LessThan**a **LessThanOrEqual**.|
|External_AutoStop_Description | Výstraha k zastavení virtuálního počítače, pokud procento procesoru překročí prahovou hodnotu.|
|External_AutoStop_MetricName | Název metriky výkonu, pro kterou má být nakonfigurováno pravidlo výstrahy Azure.|
|External_AutoStop_Threshold | Prahová hodnota pro pravidlo Výstrahy Azure zadaná v proměnné _External_AutoStop_MetricName_. Procentuální hodnoty mohou být v rozsahu od 1 do 100.|
|External_AutoStop_TimeAggregationOperator | Operátor agregace času, který je použit pro vybranou velikost okna k vyhodnocení podmínky. Přijatelné hodnoty jsou **Průměr**, **Minimální**, **Maximální**, **Součet**a **Poslední**.|
|External_AutoStop_TimeWindow | Velikost okna, během kterého Azure analyzuje vybrané metriky pro aktivaci výstrahy. Tento parametr přijímá vstup ve formátu timespan. Možné hodnoty jsou od 5 minut do 6 hodin.|
|External_EnableClassicVMs| Určuje, zda jsou klasické virtuální aplikace cílem řešení. Výchozí hodnota je True. To by měla být nastavena na False pro odběry CSP. Klasické virtuální společnosti vyžadují [klasický účet run-as](automation-create-standalone-account.md#classic-run-as-accounts).|
|External_ExcludeVMNames | Zadejte názvy virtuálních počítačů, které mají být vyloučeny, oddělující názvy pomocí čárky bez mezer. To je omezeno na 140 virtuálních stránek. Pokud přidáte více než 140 virtuálních stránek do tohoto seznamu odděleného čárkami, virtuální chody, které jsou nastaveny na vyloučení, mohou být neúmyslně spuštěny nebo zastaveny.|
|External_Start_ResourceGroupNames | Určuje jednu nebo více skupin prostředků, které oddělují hodnoty pomocí čárky, určené pro počáteční akce.|
|External_Stop_ResourceGroupNames | Určuje jednu nebo více skupin prostředků, které oddělují hodnoty pomocí čárky, určené pro akce stop.|
|Internal_AutomationAccountName | Určuje název účtu automatizace.|
|Internal_AutoSnooze_WebhookUri | Určuje identifikátor URI webhooku volaná pro scénář AutoStop.|
|Internal_AzureSubscriptionId | Určuje ID předplatného Azure.|
|Internal_ResourceGroupName | Určuje název skupiny prostředků účtu automatizace.|

Ve všech scénářích jsou pro cílení na virtuální zařízení nezbytné proměnné **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**a **External_ExcludeVMNames,** s výjimkou poskytnutí seznamu virtuálních knih oddělených čárkami pro **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**a **ScheduledStartStop_Parent** runbooky. To znamená, že vaše virtuální počítače musí být umístěny v cílových skupinách prostředků pro spuštění a zastavení akce dojít. Logika funguje podobně jako zásady Azure v tom, že můžete cílit na předplatné nebo skupinu prostředků a mít akce zděděné nově vytvořenými virtuálními počítači. Tento přístup se vyhýbá nutnosti udržovat samostatný plán pro každý virtuální virtuální ms a spravovat spuštění a zastavení ve velkém měřítku.

### <a name="schedules"></a>Plány

V následující tabulce jsou uvedeny všechny výchozí plány vytvořené v účtu automatizace.Můžete je upravit nebo vytvořit vlastní plány.Ve výchozím nastavení jsou zakázány všechny plány s výjimkou **Scheduled_StartVM** a **Scheduled_StopVM**.

Neměli byste povolit všechny plány, protože to může způsobit překrývající se akce plánu. Je nejlepší určit, které optimalizace chcete provést a odpovídajícím způsobem upravit. Další vysvětlení najdete v ukázkových scénářích v části s přehledem.

|Název plánu | Frequency | Popis|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Každých 8 hodin | Spouští AutoStop_CreateAlert_Parent runbook každých 8 hodin, což zase zastaví hodnoty založené na virtuálních počítačích v External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames a External_ExcludeVMNames v proměnných Azure Automation. Případně můžete zadat seznam virtuálních vod oddělených čárkami pomocí parametru VMList.|
|Scheduled_StopVM | Uživatelem definované, denně | Spustí Scheduled_Parent runbook s parametrem _Stop_ každý den v určený čas.Automaticky zastaví všechny virtuální uživatele, které splňují pravidla definovaná proměnnými majetku.Povolte související plán **Scheduled-StartVM**.|
|Scheduled_StartVM | Uživatelem definované, denně | Spustí Scheduled_Parent runbook s parametrem _Start_ každý den v určený čas. Automaticky spustí všechny virtuální uživatele, které splňují pravidla definovaná příslušnými proměnnými.Povolte související plán **Scheduled-StopVM**.|
|Sekvenční-StopVM | 1:00 (UTC), každý pátek | Spustí Sequenced_Parent runbook s parametrem _Stop_ každý pátek v určený čas.Postupně (vzestupně) zastaví všechny virtuální uživatele se značkou **SequenceStop** definované příslušné proměnné. Další informace o hodnotách značek a proměnných datových zdrojů najdete v části Runbook.Povolte související plán **Sequenced-StartVM**.|
|Sekvenční-StartVM | 13:00 (UTC), každé pondělí | Spustí Sequenced_Parent runbook s parametrem _Start_ každé pondělí v určený čas. Postupně (sestupně) spustí všechny virtuální uživatele s tag **sequencestart** definované příslušné proměnné. Další informace o hodnotách značek a proměnných datových zdrojů najdete v části Runbook. Povolte související plán **Sequenced-StopVM**.|

## <a name="azure-monitor-logs-records"></a>Azure Monitor zaznamenává záznamy

Automatizace vytvoří dva typy záznamů v pracovním prostoru Log Analytics: protokoly úloh a toky úloh.

### <a name="job-logs"></a>Protokoly úloh

|Vlastnost | Popis|
|----------|----------|
|Volající |  Kdo operaci zahájil. Možnou hodnotou je e-mailová adresa nebo systém pro naplánované úlohy.|
|Kategorie | Klasifikace typu dat. Službě Automation odpovídá hodnota JobLogs.|
|CorrelationId | IDENTIFIKÁTOR GUID, který je ID korelace úlohy runbook.|
|JobId | IDENTIFIKÁTOR GUID, který je ID úlohy runbooku.|
|operationName | Určuje typ operace prováděné v Azure. Pro automatizaci je hodnota Job.|
|resourceId | Určuje typ prostředku v Azure. V případě služby Automation je hodnotou účet služby Automation přidružený k příslušnému runbooku.|
|ResourceGroup | Určuje název skupiny prostředků příslušné úlohy runbooku.|
|ResourceProvider | Určuje službu Azure poskytující prostředky, které můžete nasadit a spravovat. Službě Automation odpovídá hodnota Azure Automation.|
|ResourceType | Určuje typ prostředku v Azure. V případě služby Automation je hodnotou účet služby Automation přidružený k příslušnému runbooku.|
|resultType | Stav úlohy runbooku. Možné hodnoty:<br>- Spuštěno<br>- Zastaveno<br>- Pozastaveno<br>- Neúspěch<br>- Úspěch|
|resultDescription | Popisuje výsledný stav úlohy runbooku. Možné hodnoty:<br>- Úloha se spustila<br>- Zpracování úlohy se nezdařilo<br>- Úloha je dokončená|
|RunbookName | Určuje název runbooku.|
|SourceSystem | Určuje zdrojový systém pro odeslaná data. Pro automatizaci je hodnota OpsManager|
|StreamType | Určuje typ události. Možné hodnoty:<br>- Podrobné<br>- Výstup<br>- Chyba<br>- Varování|
|SubscriptionId | Určuje ID předplatného úlohy.
|Time | Datum a čas provedení úlohy runbooku.|

### <a name="job-streams"></a>Datové proudy úlohy

|Vlastnost | Popis|
|----------|----------|
|Volající |  Kdo operaci zahájil. Možnou hodnotou je e-mailová adresa nebo systém pro naplánované úlohy.|
|Kategorie | Klasifikace typu dat. Službě Automation odpovídá hodnota JobStreams.|
|JobId | IDENTIFIKÁTOR GUID, který je ID úlohy runbooku.|
|operationName | Určuje typ operace prováděné v Azure. Pro automatizaci je hodnota Job.|
|ResourceGroup | Určuje název skupiny prostředků příslušné úlohy runbooku.|
|resourceId | Určuje ID prostředku v Azure. V případě služby Automation je hodnotou účet služby Automation přidružený k příslušnému runbooku.|
|ResourceProvider | Určuje službu Azure poskytující prostředky, které můžete nasadit a spravovat. Službě Automation odpovídá hodnota Azure Automation.|
|ResourceType | Určuje typ prostředku v Azure. V případě služby Automation je hodnotou účet služby Automation přidružený k příslušnému runbooku.|
|resultType | Výsledek úlohy runbooku v době, kdy byla událost vygenerována. Možná hodnota je:<br>- Probíhá zpracování|
|resultDescription | Zahrnuje výstupní datový proud z runbooku.|
|RunbookName | Název runbooku.|
|SourceSystem | Určuje zdrojový systém pro odeslaná data. Pro automatizaci je hodnota OpsManager.|
|StreamType | Typ datového proudu úlohy. Možné hodnoty:<br>- Pokrok<br>- Výstup<br>- Varování<br>- Chyba<br>- Ladění<br>- Podrobné|
|Time | Datum a čas provedení úlohy runbooku.|

Při každém hledání protokolu, který vrací záznamy kategorií **JobLogs** nebo **JobStreams**, můžete vybrat **JobLogs** nebo **JobStreams** zobrazení, které zobrazí sadu dlaždic shrnující aktualizace vrácené hledáním.

## <a name="sample-log-searches"></a>Ukázky hledání v protokolech

V následující tabulce jsou uvedeny ukázky hledání v protokolech pro záznamy úloh shromážděné tímto řešením.

|Dotaz | Popis|
|----------|----------|
|Najít úlohy pro ScheduledStartStop_Parent runbooku, které byly úspěšně dokončeny | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Najít úlohy pro SequencedStartStop_Parent runbooku, které byly úspěšně dokončeny | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="viewing-the-solution"></a>Zobrazení řešení

Chcete-li získat přístup k řešení, přejděte na účet automatizace, vyberte **pracovní prostor v** části SOUVISEJÍCÍ **ZDROJE**. Na stránce analýzy protokolů vyberte **řešení** v části **OBECNÉ**. Na stránce **Řešení** vyberte řešení **Start-Stop-VM[pracovní prostor]** ze seznamu.

Při výběru řešení se zobrazí stránka řešení **Start-Stop-VM[pracovní prostor].** Zde si můžete prohlédnout důležité podrobnosti, jako je například dlaždice **StartStopVM.** Stejně jako v pracovním prostoru Log Analytics tato dlaždice zobrazuje počet a grafické znázornění úloh runbooku pro řešení, které bylo úspěšně zahájeno a dokončeno.

![Stránka řešení správa aktualizací automatizace](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Zde můžete provést další analýzu záznamů úloh kliknutím na dlaždici koblihy. Řídicí panel řešení zobrazuje historii úloh a předdefinované vyhledávací dotazy protokolu. Přepněte na pokročilý portál analýzy protokolů a můžete je prohledávat na základě vyhledávacích dotazů.

## <a name="configure-email-notifications"></a>Konfigurace e-mailových oznámení

Chcete-li změnit e-mailová oznámení po nasazení řešení, upravte skupinu akcí, která byla vytvořena během nasazení.  

> [!NOTE]
> Předplatná v Azure Government Cloud nepodporují e-mailové funkce tohoto řešení.

Na webu Azure Portal přejděte do skupiny sledování -> akcí. Vyberte skupinu akcí s názvem **StartStop_VM_Notication**.

![Stránka řešení správa aktualizací automatizace](media/automation-solution-vm-management/azure-monitor.png)

Na **stránce StartStop_VM_Notification** klikněte v části **Podrobnosti**na Upravit **podrobnosti** . Otevře se stránka **E-mail/SMS/Push/Voice.** Aktualizujte e-mailovou adresu a kliknutím na **OK** změny uložte.

![Stránka řešení správa aktualizací automatizace](media/automation-solution-vm-management/change-email.png)

Případně můžete do skupiny akcí přidat další akce, abyste se dozvěděli více o skupinách akcí, viz [skupiny akcí](../azure-monitor/platform/action-groups.md)

Následuje příklad e-mailu, který je odeslán při vypnutí řešení virtuálních počítačů.

![Stránka řešení správa aktualizací automatizace](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>Přidání nebo vyloučení virtuálních měn

Řešení poskytuje možnost přidávat virtuální počítače, které mají být cílem řešení nebo konkrétně vyloučit počítače z řešení.

### <a name="add-a-vm"></a>Přidání virtuálního počítače

Existuje několik možností, které můžete použít k ujistěte se, že virtuální ho virtuálního zařízení je součástí řešení Start/Stop při spuštění.

* Každá nadřazená [runbooky](#runbooks) řešení má parametr **VMList.** Můžete předat seznam názvů virtuálních počítačů oddělených čárkami tomuto parametru při plánování příslušnénadřazené runbook pro vaši situaci a tyto virtuální počítače budou zahrnuty při spuštění řešení.

* Pokud chcete vybrat víc virtuálních počítačů, nastavte **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupNames** s názvy skupin prostředků, které obsahují virtuální počítač, který chtějí spustit nebo zastavit. Můžete také nastavit tuto `*`hodnotu , aby bylo řešení spuštěno proti všem skupinám prostředků v předplatném.

### <a name="exclude-a-vm"></a>Vyloučení virtuálního počítače

Chcete-li vyloučit virtuální ho virtuálního zařízení z řešení, můžete jej přidat do **proměnné External_ExcludeVMNames.** Tato proměnná je seznam specifických virtuálních zařízení oddělený čárkami, který má být vyloučen z řešení Start/Stop. Tento seznam je omezen na 140 virtuálních stránek. Pokud přidáte více než 140 virtuálních stránek do tohoto seznamu odděleného čárkami, virtuální chody, které jsou nastaveny na vyloučení, mohou být neúmyslně spuštěny nebo zastaveny.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Úprava plánů spuštění a vypnutí

Správa plánů spuštění a vypnutí v tomto řešení se řídí stejnými kroky, jako je popsáno v [plánování sady Runbook v Azure Automation](automation-schedules.md). Musí existovat samostatný plán pro spuštění a zastavení virtuálních můe.

Konfigurace řešení pouze zastavit virtuální chod v určitém čase je podporována. V tomto scénáři stačí vytvořit **plán Stop** a žádné odpovídající **Start** naplánováno. Budete muset:

1. Ujistěte se, že jste přidali skupiny prostředků pro virtuální chod, které mají být ukončeny v **proměnné External_Stop_ResourceGroupNames.**
2. Vytvořte si vlastní plán pro čas, který chcete vypnout virtuální počítače.
3. Přejděte do **ScheduledStartStop_Parent** runbooku a klepněte na **tlačítko Naplánovat**. To umožňuje vybrat plán, který jste vytvořili v předchozím kroku.
4. Vyberte **Parametry a spusťte nastavení** a nastavte parametr ACTION na "Stop".
5. Klikněte na tlačítko **OK** a uložte změny.

## <a name="update-the-solution"></a>Aktualizace tohoto řešení

Pokud jste nasadili předchozí verzi tohoto řešení, musíte ji nejprve odstranit z účtu před nasazením aktualizované verze. Podle pokynů [řešení odeberte](#remove-the-solution) a potom podle výše uvedených kroků [nasadit řešení](#deploy-the-solution).

## <a name="remove-the-solution"></a>Odebrání tohoto řešení

Pokud se rozhodnete, že již nebudete muset používat řešení, můžete jej odstranit z účtu Automatizace. Odstraněním řešení odeberete pouze runbooky. Neodstraní plány nebo proměnné, které byly vytvořeny při přidání řešení. Tyto prostředky, které potřebujete odstranit ručně, pokud je nepoužíváte s jinými runbooky.

Chcete-li odstranit řešení, proveďte následující kroky:

1. V části Související **zdroje**vyberte v části Související zdroje **položku Propojený pracovní prostor**.
1. Vyberte **Přejít do pracovního prostoru**.
1. V části **Obecné**vyberte **možnost Řešení**. 
1. Na stránce **Řešení** vyberte řešení **Start-Stop-VM[Pracovní prostor]**. Na stránce **VMManagementSolution[Pracovní prostor]** vyberte v nabídce **odstranit**.<br><br> ![Odstranit řešení Mgmt virtuálního měn](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. V okně **Odstranit řešení** potvrďte, že chcete odstranit řešení.
1. Při ověřování informací a odstranění řešení můžete sledovat jejich průběh v části **Oznámení** z nabídky. Po spuštění procesu odebrání řešení se vrátíte na stránku **Řešení.**

Účet automatizace a pracovní prostor Log Analytics nejsou odstraněny jako součást tohoto procesu. Pokud nechcete zachovat pracovní prostor Log Analytics, je třeba jej ručně odstranit. Toho lze dosáhnout z webu Azure Portal:

1. Na webu Azure Portal vyhledejte a vyberte **pracovní prostory Log Analytics**.
1. Na stránce **Pracovní prostory Analýzy protokolů** vyberte pracovní prostor.
1. Z nabídky na stránce nastavení pracovního prostoru vyberte **Odstranit.**

Pokud nechcete zachovat součásti účtu Azure Automation, můžete je odstranit ručně. Seznam sad Runbook, proměnných a plánů vytvořených řešením najdete v tématu [Součásti řešení](#solution-components).

## <a name="next-steps"></a>Další kroky

- Další informace o tom, jak vytvářet různé vyhledávací dotazy a prohlížet protokoly úloh automatizace pomocí protokolů Azure Monitor, najdete [v tématu Protokoly protokolů protokolu protokolu.](../log-analytics/log-analytics-log-searches.md)
- Další informace o spouštění runbooků, postupy při monitorování úloh runbooků a další technické podrobnosti najdete v článku [Sledování úlohy runbooku](automation-runbook-execution.md).
- Další informace o protokolech Azure Monitoru a zdrojích shromažďování dat najdete [v tématu Shromažďování dat úložiště Azure v přehledu protokolů Azure Monitoru](../azure-monitor/platform/collect-azure-metrics-logs.md).
