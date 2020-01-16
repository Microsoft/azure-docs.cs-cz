---
title: Řešení Start/Stop VMs during off-hours
description: Toto řešení správy virtuálních počítačů spustí a zastaví vaše Azure Resource Manager virtuálních počítačů podle plánu a proaktivně monitoruje z protokolů Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2019
ms.topic: conceptual
ms.openlocfilehash: 37fee7f96a27942a1295cb8c2315fedffc5bdefe
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030171"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Řešení Start/Stop VMs during off-hours v Azure Automation

Řešení Start/Stop VMs during off-hours spustí a zastaví vaše virtuální počítače Azure na uživatelem definovaných plánech, poskytuje přehledy prostřednictvím protokolů Azure Monitor a odesílá volitelné e-maily pomocí [skupin akcí](../azure-monitor/platform/action-groups.md). Ve většině scénářů podporuje virtuální počítače s Azure Resource Manager i Classic. Pokud chcete toto řešení použít u klasických virtuálních počítačů, potřebujete klasický účet RunAs, který se ve výchozím nastavení nevytvoří. Pokyny k vytvoření účtu Spustit jako pro Classic najdete v tématu [účty Spustit jako pro Classic](automation-create-standalone-account.md#classic-run-as-accounts).

> [!NOTE]
> Řešení Start/Stop VMs during off-hours bylo testováno pomocí modulů Azure, které jsou importovány do účtu Automation při nasazení řešení. Řešení aktuálně nefunguje s novějšími verzemi modulu Azure. To má vliv jenom na účet Automation, který používáte ke spuštění řešení Start/Stop VMs during off-hours. V dalších účtech Automation můžete dál používat novější verze modulu Azure, jak je popsáno v tématu [Postup aktualizace Azure PowerShellch modulů v Azure Automation](automation-update-azure-modules.md)

Toto řešení poskytuje decentralizovanou možnost automatizace s nízkými náklady pro uživatele, kteří chtějí optimalizovat náklady na virtuální počítače. V tomto řešení můžete:

- Naplánujte spouštění a zastavování virtuálních počítačů.
- Naplánování spouštění a zastavování virtuálních počítačů ve vzestupném pořadí pomocí značek Azure (není podporováno pro klasické virtuální počítače).
- Zastavovat virtuální počítače na základě nízkého využití procesoru.

Toto jsou omezení s aktuálním řešením:

- Toto řešení spravuje virtuální počítače v jakékoli oblasti, ale dá se použít jenom ve stejném předplatném jako váš účet Azure Automation.
- Toto řešení je dostupné v Azure a AzureGov do jakékoli oblasti, která podporuje Log Analytics pracovní prostor, účet Azure Automation a výstrahy. Oblasti AzureGov aktuálně nepodporují funkce e-mailu.

> [!NOTE]
> Pokud používáte řešení pro klasické virtuální počítače, všechny vaše virtuální počítače se zpracují postupně na každou cloudovou službu. Virtuální počítače jsou pořád zpracovávány paralelně napříč různými Cloud Services. Pokud máte více než 20 virtuálních počítačů na cloudovou službu, doporučujeme vytvořit více plánů s nadřazenou sadou Runbook **ScheduledStartStop_Parent** a zadat 20 virtuálních počítačů na plán. Ve vlastnostech plánu zadejte jako čárkami oddělený seznam názvy virtuálních počítačů v parametru **VMList** . V opačném případě, pokud je úloha automatizace pro toto řešení spuštěná déle než tři hodiny, dočasně se uvolní nebo zastaví na základě [spravedlivého](automation-runbook-execution.md#fair-share) limitu sdílení.
>
> Předplatná Azure Cloud Solution Provider (CSP) podporují jenom model Azure Resource Manager, ale služby, které nejsou Azure Resource Manager, nejsou v programu k dispozici. Když je spuštěno řešení spustit/zastavit, může docházet k chybám, protože jsou rutiny pro správu klasických prostředků. Další informace o CSP najdete v tématu [dostupné služby v předplatných CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Pokud používáte předplatné CSP, měli byste po nasazení upravit [**External_EnableClassicVMs**](#variables) proměnnou na **false** .

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Požadavky

Runbooky pro toto řešení fungují s [účtem spustit jako pro Azure](automation-create-runas-account.md). Účet Spustit jako je upřednostňovanou metodou ověřování, protože používá ověřování certifikátů namísto hesla, které může vypršet nebo často měnit.

Pro řešení spuštění/zastavení virtuálního počítače doporučujeme použít samostatný účet Automation. Je to proto, že verze modulů Azure jsou často upgradované a jejich parametry se můžou změnit. Řešení pro spuštění/zastavení virtuálního počítače není upgradované na stejném tempo, takže nemusí fungovat s novějšími verzemi rutin, které používá. Doporučujeme vám také otestovat aktualizace modulu v účtu služby test Automation ještě předtím, než je naimportujete do provozních účtů služby Automation.

### <a name="permissions-needed-to-deploy"></a>Oprávnění potřebná k nasazení

K dispozici jsou určitá oprávnění, která musí uživatel mít, aby mohli nasadit virtuální počítače spustit/zastavit během nepracovních řešení. Tato oprávnění se liší při použití předem vytvořeného účtu Automation a Log Analyticsho pracovního prostoru nebo při vytváření nových prostředí během nasazování. Pokud jste přispěvatelem předplatného a globálním správcem ve vašem tenantovi Azure Active Directory, nemusíte konfigurovat následující oprávnění. Pokud tato práva nemáte nebo potřebujete nakonfigurovat vlastní roli, přečtěte si níže uvedené oprávnění.

#### <a name="pre-existing-automation-account-and-log-analytics-workspace"></a>Již existující účet služby Automation a pracovní prostor Log Analytics

Pokud chcete nasadit virtuální počítače spustit/zastavit v době mimo špičku do existujícího účtu Automation a pracovního prostoru Log Analytics, uživatel nasazení řešení vyžaduje pro **skupinu prostředků**následující oprávnění. Další informace o rolích najdete v tématu [vlastní role pro prostředky Azure](../role-based-access-control/custom-roles.md).

| Oprávnění | Rozsah|
| --- | --- |
| Microsoft. Automation/automationAccounts/Read | Skupina prostředků |
| Microsoft. Automation/automationAccounts/Variables/Write | Skupina prostředků |
| Microsoft. Automation/automationAccounts/Schedules/Write | Skupina prostředků |
| Microsoft.Automation/automationAccounts/runbooks/write | Skupina prostředků |
| Microsoft. Automation/automationAccounts/Connections/Write | Skupina prostředků |
| Microsoft. Automation/automationAccounts/Certificates/Write | Skupina prostředků |
| Microsoft. Automation/automationAccounts/modules/Write | Skupina prostředků |
| Microsoft. Automation/automationAccounts/Module/Read | Skupina prostředků |
| Microsoft.automation/automationAccounts/jobSchedules/write | Skupina prostředků |
| Microsoft. Automation/automationAccounts/Jobs/Write | Skupina prostředků |
| Microsoft. Automation/automationAccounts/Jobs/Read | Skupina prostředků |
| Microsoft. OperationsManagement/Solutions/Write | Skupina prostředků |
| Microsoft.OperationalInsights/workspaces/* | Skupina prostředků |
| Microsoft.Insights/diagnosticSettings/write | Skupina prostředků |
| Microsoft.Insights/ActionGroups/Write | Skupina prostředků |
| Microsoft.Insights/ActionGroups/read | Skupina prostředků |
| Microsoft. Resources/Subscriptions/resourceGroups/Read | Skupina prostředků |
| Microsoft. Resources/nasazení/* | Skupina prostředků |

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>Nový účet Automation a nový Log Analytics pracovní prostor

Pokud chcete nasadit virtuální počítače spustit/zastavit v době mimo špičku na nový účet Automation a Log Analytics pracovní prostor, uživatel nasazení řešení potřebuje oprávnění definovaná v předchozí části a taky následující oprávnění:

- Spolusprávce předplatného – účet Spustit jako pro Classic je potřeba vytvořit jenom v případě, že budete spravovat klasické virtuální počítače. [Klasické účty runas](automation-create-standalone-account.md#classic-run-as-accounts) již nejsou ve výchozím nastavení vytvářeny.
- Člen role **vývojář aplikace** [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) . Další informace o konfiguraci účtů spustit jako najdete v tématu [oprávnění ke konfiguraci účtů spustit jako](manage-runas-account.md#permissions).
- Přispěvatel v rámci předplatného nebo následujících oprávnění

| Oprávnění |Rozsah|
| --- | --- |
| Microsoft. Authorization/Operations/Read | Předplatné|
| Microsoft. Authorization/oprávnění/čtení |Předplatné|
| Microsoft. Authorization/roleAssignments/Read | Předplatné |
| Microsoft.Authorization/roleAssignments/write | Předplatné |
| Microsoft.Authorization/roleAssignments/delete | Předplatné |
| Microsoft. Automation/automationAccounts/Connections/Read | Skupina prostředků |
| Microsoft. Automation/automationAccounts/Certificates/Read | Skupina prostředků |
| Microsoft. Automation/automationAccounts/Write | Skupina prostředků |
| Microsoft.OperationalInsights/workspaces/write | Skupina prostředků |

## <a name="deploy-the-solution"></a>Nasazení řešení

Provedením následujících kroků přidejte řešení Start/Stop VMs during off-hours do svého účtu Automation a pak nakonfigurujte proměnné pro přizpůsobení řešení.

1. Z účtu Automation vyberte **Spustit/zastavit virtuální počítač** v části **související prostředky**. Tady můžete kliknout na další **informace a povolit řešení**. Pokud už máte nasazené řešení pro spouštění a zastavování virtuálních počítačů, můžete ho vybrat kliknutím na **Spravovat řešení** a vyhledat ho v seznamu.

   ![Povolit z účtu Automation](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Můžete ho také vytvořit z libovolného místa v Azure Portal kliknutím na **vytvořit prostředek**. Na stránce Marketplace zadejte klíčové slovo, například **Spustit** nebo **Spustit/zastavit**. Seznam se průběžně filtruje podle zadávaného textu. Případně můžete zadat jedno nebo více klíčových slov z celého jména řešení a potom stisknout klávesu ENTER. Ve výsledcích hledání vyberte **Start/Stop VMS during off-hours** .

2. Na stránce **Start/Stop VMS during off-hours** pro vybrané řešení zkontrolujte souhrnné informace a klikněte na **vytvořit**.

   ![Portál Azure](media/automation-solution-vm-management/azure-portal-01.png)

3. Zobrazí se stránka **Přidat řešení** . Před importem do předplatného automatizace se zobrazí výzva ke konfiguraci řešení.

   ![Stránka Přidat řešení správy virtuálních počítačů](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. Na stránce **Přidat řešení** vyberte **pracovní prostor**. Vyberte Log Analytics pracovní prostor propojený se stejným předplatným Azure, ve kterém je účet Automation. Pokud nemáte pracovní prostor, vyberte **vytvořit nový pracovní prostor**. Na stránce **Log Analytics pracovní prostor** proveďte následující kroky:
   - Zadejte název nového **pracovního prostoru Log Analytics**, například "ContosoLAWorkspace".
   - Vyberte **předplatné** , které chcete propojit, výběrem z rozevíracího seznamu, pokud výchozí hodnota není vhodná.
   - V případě **skupiny prostředků**můžete vytvořit novou skupinu prostředků nebo vybrat některou z existujících.
   - Vyberte **Umístění**.
   - Vyberte možnost u položky **Cenová úroveň**. Vyberte možnost **za GB (samostatně)** . Protokoly Azure Monitor mají aktualizované [ceny](https://azure.microsoft.com/pricing/details/log-analytics/) a jedinou možností je úroveň na GB.

   > [!NOTE]
   > Při povolování řešení se podporuje propojení pracovního prostoru služby Log Analytics a účtu Automation pouze v určitých oblastech.
   >
   > Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](how-to/region-mappings.md).

5. Po zadání požadovaných informací na stránce **Log Analytics pracovního prostoru** klikněte na **vytvořit**. Průběh můžete sledovat v části **oznámení** z nabídky, která vás po dokončení vrátí na stránku **Přidat řešení** .
6. Na stránce **Přidat řešení** vyberte **účet Automation**. Pokud vytváříte nový pracovní prostor Log Analytics, můžete vytvořit nový účet Automation, který se k němu přidruží, nebo vybrat existující účet Automation, který ještě není propojený s pracovním prostorem Log Analytics. Vyberte existující účet Automation, nebo klikněte na **vytvořit účet Automation**a na stránce **Přidat účet Automation** zadejte následující informace:
   - Do pole **Název** zadejte název účtu služby Automation.

     Všechny ostatní možnosti se vyplní automaticky na základě vybraného pracovního prostoru Log Analytics. Tyto možnosti nelze upravovat. Účet Spustit v Azure jako představuje výchozí metodu ověřování pro runbooky obsažené v tomto řešení. Po kliknutí na **OK**se ověřují možnosti konfigurace a vytvoří se účet Automation. Průběh zpracování můžete sledovat prostřednictvím možnosti nabídky **Oznámení**.

7. Nakonec na stránce **Přidat řešení** vyberte **Konfigurace**. Zobrazí se stránka **parametry** .

   ![Stránka parametrů pro řešení](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Tady se zobrazí výzva k provedení těchto akcí:
   - Zadejte **cílové názvy zdrojových zdrojů**. Tyto hodnoty jsou názvy skupin prostředků, které obsahují virtuální počítače, které mají být spravovány tímto řešením. Můžete zadat více než jeden název a každý z nich oddělit čárkou (hodnoty nerozlišují velká a malá písmena). Je podporováno použití zástupného znaku, pokud jsou cílem virtuální počítače ve všech skupinách prostředků v rámci předplatného. Tato hodnota je uložena v proměnných **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupNames** .
   - Zadejte **seznam vyloučení virtuálních počítačů (řetězec)** . Tato hodnota je název jednoho nebo více virtuálních počítačů z cílové skupiny prostředků. Můžete zadat více než jeden název a každý z nich oddělit čárkou (hodnoty nerozlišují velká a malá písmena). Je podporováno použití zástupného znaku. Tato hodnota je uložena v proměnné **External_ExcludeVMNames** .
   - Vyberte **plán**. Vyberte datum a čas pro váš plán. Počínaje časem, který jste vybrali, se vytvoří znovu denní plán. Výběr jiné oblasti není k dispozici. Pokud chcete po konfiguraci řešení nakonfigurovat plán na konkrétní časové pásmo, přečtěte si téma [Změna plánu spuštění a vypnutí](#modify-the-startup-and-shutdown-schedules).
   - Pokud chcete dostávat **e-mailová oznámení** ze skupiny akcí, přijměte výchozí hodnotu **Ano** a zadejte platnou e-mailovou adresu. Pokud vyberete možnost **ne** , ale později se rozhodnete, že chcete dostávat e-mailová oznámení, můžete aktualizovat [skupinu akcí](../azure-monitor/platform/action-groups.md) vytvořenou pomocí platných e-mailových adres oddělených čárkou. Musíte taky povolit následující pravidla upozornění:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > Výchozí hodnota pro **názvy cílových zdrojových zdrojů** je **&ast;** . Tím se cílí na všechny virtuální počítače v rámci předplatného. Pokud nechcete, aby řešení nevyhovovalo všem virtuálním počítačům ve vašem předplatném, je potřeba tuto hodnotu aktualizovat na seznam názvů skupin prostředků před povolením plánů.

8. Po nakonfigurování počátečního nastavení požadovaného pro řešení kliknutím na tlačítko **OK** zavřete stránku **parametry** a vyberte **vytvořit**. Po ověření všech nastavení se řešení nasadí do vašeho předplatného. Dokončení tohoto procesu může trvat několik sekund a průběh můžete sledovat v části **oznámení** z nabídky.

> [!NOTE]
> Pokud máte předplatné Azure Cloud Solution Provider (CSP), po dokončení nasazení klikněte v účtu Automation na **proměnné** v části **sdílené prostředky** a nastavte proměnnou [**External_EnableClassicVMs**](#variables) na **false**. Tím se řešení zastaví v hledání klasických prostředků virtuálních počítačů.

## <a name="scenarios"></a>Scénáře

Řešení obsahuje tři různé scénáře. Mezi tyto scénáře patří:

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>Scénář 1: spuštění/zastavení virtuálních počítačů podle plánu

Tento scénář je výchozí konfigurací při prvním nasazení řešení. Můžete ji například nakonfigurovat tak, aby zastavila všechny virtuální počítače v rámci předplatného, když ponecháte práci v večeru, a po návratu do kanceláře ji začít používat v ráno. Když nakonfigurujete plány **naplánované – StartVM** a **Schedule-StopVM** během nasazení, začnou a zastavují cílové virtuální počítače. Konfigurace tohoto řešení pro jenom zastavení virtuálních počítačů je podporovaná. informace o tom, jak nakonfigurovat vlastní plán, najdete v tématu [Změna plánů spouštění a vypínání](#modify-the-startup-and-shutdown-schedules) .

> [!NOTE]
> Časové pásmo je vaším aktuálním časovým pásmem, když konfigurujete parametr časového plánu. Je ale uložený ve formátu UTC v Azure Automation. Nemusíte provádět žádné převody časových pásem, protože to je zpracováváno během nasazení.

Konfigurací následujících proměnných můžete řídit, které virtuální počítače jsou v oboru: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**a **External_ExcludeVMNames**.

Můžete povolit buď cílení akce na předplatné a skupinu prostředků, nebo cílení na konkrétní seznam virtuálních počítačů, ale ne obojí.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Cílem akcí spuštění a zastavení u předplatného a skupiny prostředků

1. Nakonfigurujte proměnné **External_Stop_ResourceGroupNames** a **External_ExcludeVMNames** k určení cílových virtuálních počítačů.
2. Povolte a aktualizujte **naplánované –** plány plánování StartVM a **StopVM** .
3. Spusťte sadu **ScheduledStartStop_Parent** Runbook s parametrem Action nastaveným na **Start** a parametr WHATIF nastaven na **hodnotu true** , chcete-li zobrazit náhled změn.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Zacílit akci spustit a zastavit podle seznamu VM

1. Spusťte sadu **ScheduledStartStop_Parent** Runbook s parametrem Action nastaveným na **Start**, do parametru *VMList* přidejte čárkami oddělený seznam virtuálních počítačů a pak nastavte parametr WHATIF na **hodnotu true**. Zobrazte náhled změn.
1. Nakonfigurujte parametr **External_ExcludeVMNames** pomocí čárkami oddělený seznam virtuálních počítačů (VM1, VM2, VM3).
1. Tento scénář nedodržuje proměnné **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupnames** . V tomto scénáři potřebujete vytvořit vlastní plán automatizace. Podrobnosti najdete v tématu [plánování Runbooku v Azure Automation](../automation/automation-schedules.md).

> [!NOTE]
> Hodnota pro **název cílové zdrojové** služby je uložená jako hodnota pro **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupNames**. Pro další členitost můžete každou z těchto proměnných upravit a cílit na jiné skupiny prostředků. Pro akci spustit použijte **External_Start_ResourceGroupNames**a pro akci zastavení použijte **External_Stop_ResourceGroupNames**. Virtuální počítače se automaticky přidají do plánů zahájení a zastavení.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>Scénář 2: spuštění/zastavení virtuálních počítačů v sekvenci pomocí značek

V prostředí, které obsahuje dvě nebo více součástí na více virtuálních počítačích, které podporují distribuovanou úlohu, je důležité zajistit, aby se v uvedeném pořadí podporovalo pořadí, ve kterém jsou komponenty spouštěny a zastaveny. Tento scénář můžete dosáhnout provedením následujících kroků:

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Cílem akcí spuštění a zastavení u předplatného a skupiny prostředků

1. Přidejte **sequencestart** a značku **sequencestop** s kladnou celočíselnou hodnotou do virtuálních počítačů, které jsou cíleny na **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupNames** proměnných. Akce spuštění a zastavení se provádí ve vzestupném pořadí. Informace o tom, jak označit virtuální počítač, najdete v tématu [označení virtuálního počítače s Windows v Azure](../virtual-machines/windows/tag.md) a [označení virtuálního počítače se systémem Linux v Azure](../virtual-machines/linux/tag.md).
1. Upravte plány **Sequenced-StartVM** a **Sequenced-StopVM** na datum a čas, který splňuje vaše požadavky, a povolte plán.
1. Spusťte sadu **SequencedStartStop_Parent** Runbook s parametrem Action nastaveným na **Start** a parametr WHATIF nastaven na **hodnotu true** , chcete-li zobrazit náhled změn.
1. Zobrazte náhled akce a proveďte potřebné změny před implementací na produkčních virtuálních počítačích. Až budete připraveni, ručně spusťte sadu Runbook s parametrem nastaveným na **hodnotu false**, nebo nechte naplánované rutiny Automation **-StartVM** a **Sequenced-StopVM** spustit automaticky podle předepsaného plánu.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Zacílit akci spustit a zastavit podle seznamu VM

1. Přidejte **sequencestart** a značku **sequencestop** s kladnou celočíselnou hodnotou do virtuálních počítačů, které chcete přidat do parametru **VMList** .
1. Spusťte sadu **SequencedStartStop_Parent** Runbook s parametrem Action nastaveným na **Start**, do parametru *VMList* přidejte čárkami oddělený seznam virtuálních počítačů a pak nastavte parametr WHATIF na **hodnotu true**. Zobrazte náhled změn.
1. Nakonfigurujte parametr **External_ExcludeVMNames** pomocí čárkami oddělený seznam virtuálních počítačů (VM1, VM2, VM3).
1. Tento scénář nedodržuje proměnné **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupnames** . V tomto scénáři potřebujete vytvořit vlastní plán automatizace. Podrobnosti najdete v tématu [plánování Runbooku v Azure Automation](../automation/automation-schedules.md).
1. Zobrazte náhled akce a proveďte potřebné změny před implementací na produkčních virtuálních počítačích. Až budete připraveni, ručně spusťte monitor-and-Diagnostics/monitoring-Action-groupsrunbook s parametrem nastaveným na **hodnotu false**, nebo ponechte plán automatizace **Sequenced-StartVM** a **Sequenced-StopVM** spouštěný automaticky podle předepsaného plánu.

### <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a>Scénář 3: spuštění/zastavení automaticky na základě využití procesoru

Toto řešení může pomáhat spravovat náklady na běžící virtuální počítače ve vašem předplatném Pomocí vyhodnocení virtuálních počítačů Azure, které se nepoužívají během období mimo špičku, například po hodinách, a automaticky je vypíná, pokud je využití procesoru menší než x%.

Ve výchozím nastavení je řešení předem nakonfigurované k vyhodnocení procentuální metriky procesoru, aby bylo možné zjistit, zda je průměrné využití 5 procent nebo méně. Tento scénář je řízen následujícími proměnnými a může být upraven, pokud výchozí hodnoty nesplňují vaše požadavky:

- External_AutoStop_MetricName
- External_AutoStop_Threshold
- External_AutoStop_TimeAggregationOperator
- External_AutoStop_TimeWindow

Můžete povolit buď cílení akce na předplatné a skupinu prostředků, nebo cílení na konkrétní seznam virtuálních počítačů, ale ne obojí.

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Cíl akce zastavení u předplatného a skupiny prostředků

1. Nakonfigurujte proměnné **External_Stop_ResourceGroupNames** a **External_ExcludeVMNames** k určení cílových virtuálních počítačů.
1. Povolí a aktualizuje plán **Schedule_AutoStop_CreateAlert_Parent** .
1. Spusťte sadu **AutoStop_CreateAlert_Parent** Runbook s parametrem Action nastaveným na **Start** a parametr WHATIF nastaven na **hodnotu true** , chcete-li zobrazit náhled změn.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Zacílit akci spustit a zastavit podle seznamu VM

1. Spusťte sadu **AutoStop_CreateAlert_Parent** Runbook s parametrem Action nastaveným na **Start**, do parametru *VMList* přidejte čárkami oddělený seznam virtuálních počítačů a pak nastavte parametr WHATIF na **hodnotu true**. Zobrazte náhled změn.
1. Nakonfigurujte parametr **External_ExcludeVMNames** pomocí čárkami oddělený seznam virtuálních počítačů (VM1, VM2, VM3).
1. Tento scénář nedodržuje proměnné **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupnames** . V tomto scénáři potřebujete vytvořit vlastní plán automatizace. Podrobnosti najdete v tématu [plánování Runbooku v Azure Automation](../automation/automation-schedules.md).

Teď, když máte plán pro zastavování virtuálních počítačů na základě využití procesoru, musíte povolit jedno z následujících plánů, abyste je mohli spustit.

- Cílová akce zahájení podle předplatného a skupiny prostředků. Postup testování a povolení plánů **plánovaného StartVM** najdete ve [scénáři 1](#scenario-1-startstop-vms-on-a-schedule) .
- Cílová akce zahájení podle předplatného, skupiny prostředků a značky. Podívejte se na kroky ve [scénáři 2](#scenario-2-startstop-vms-in-sequence-by-using-tags) pro testování a povolení plánů **sekvenčního StartVM** .

## <a name="solution-components"></a>Součásti řešení

Toto řešení zahrnuje předem nakonfigurované Runbooky, plány a integraci s protokoly Azure Monitor, takže můžete přizpůsobit spouštění a vypínání virtuálních počítačů tak, aby vyhovovaly vašim obchodním potřebám.

### <a name="runbooks"></a>Runbooky

V následující tabulce jsou uvedeny Runbooky nasazené pro váš účet Automation pomocí tohoto řešení. Neprovádějte změny v kódu Runbooku. Místo toho napište vlastní Runbook pro nové funkce.

> [!IMPORTANT]
> Nespouštějte přímo žádnou sadu Runbook s názvem "podřízená", která je připojena k názvu.

Všechny nadřazené Runbooky obsahují parametr _whatIf_ . Při nastavení na **hodnotu true**podporuje _whatIf_ podrobné informace o přesném chování, které sada Runbook provede, když se spustí bez parametru _whatIf_ a ověří, jestli jsou cílené správné virtuální počítače. Pokud je parametr _whatIf_ nastaven na **hodnotu false**, sada Runbook provede pouze své definované akce.

|Runbook | Parametry | Popis|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Volá se z nadřazeného Runbooku. Tato sada Runbook vytváří výstrahy na základě jednotlivých prostředků pro scénář automatického zastavení.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: true nebo false  | Vytvoří nebo aktualizuje pravidla upozornění Azure na virtuálních počítačích v cílovém předplatném nebo ve skupinách prostředků. <br> VMList: čárkami oddělený seznam virtuálních počítačů. Například _VM1, VM2, VM3_.<br> *WhatIf* ověří logiku sady Runbook bez provedení.|
|AutoStop_Disable | Žádná | Zakáže výstrahy na autostop a výchozí plán.|
|AutoStop_StopVM_Child | WebHookData | Volá se z nadřazeného Runbooku. Pravidla výstrah volají tuto sadu Runbook, aby se virtuální počítač zastavil.|
|Bootstrap_Main | Žádná | Jednorázově se používá k nastavení konfigurací spouštění, jako je například webhookURI, které obvykle nejsou přístupné z Azure Resource Manager. Tato sada Runbook se po úspěšném nasazení automaticky odebere.|
|ScheduledStartStop_Child | VMName <br> Akce: spustit nebo zastavit <br> ResourceGroupName | Volá se z nadřazeného Runbooku. Provede akci spuštění nebo zastavení pro naplánované zastavení.|
|ScheduledStartStop_Parent | Akce: spustit nebo zastavit <br>VMList <br> WhatIf: true nebo false | Toto nastavení má vliv na všechny virtuální počítače v rámci předplatného. Upravte **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupNames** , aby se spouštěly jenom u těchto cílových skupin prostředků. Pomocí aktualizace proměnné **External_ExcludeVMNames** můžete taky vyloučit konkrétní virtuální počítače.<br> VMList: čárkami oddělený seznam virtuálních počítačů. Například _VM1, VM2, VM3_.<br> _WhatIf_ ověří logiku sady Runbook bez provedení.|
|SequencedStartStop_Parent | Akce: spustit nebo zastavit <br> WhatIf: true nebo false<br>VMList| Na každém virtuálním počítači, pro který chcete sekvenci aktivity spustit/zastavit, vytvořte značky s názvem **sequencestart** a **sequencestop** . U těchto názvů značek se rozlišují malá a velká písmena. Hodnota značky by měla být kladné celé číslo (1, 2, 3), které odpovídá pořadí, ve kterém chcete spustit nebo zastavit. <br> VMList: čárkami oddělený seznam virtuálních počítačů. Například _VM1, VM2, VM3_. <br> _WhatIf_ ověří logiku sady Runbook bez provedení. <br> **Poznámka**: virtuální počítače musí být v rámci skupin prostředků definovaných jako External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames a External_ExcludeVMNames v Azure Automation proměnných. Aby se akce projevily, musí mít odpovídající značky.|

### <a name="variables"></a>Proměnné

V následující tabulce jsou uvedeny proměnné vytvořené v účtu Automation. Upravovat pouze proměnné s **externím**. Úprava proměnných s předponou **interní** způsobuje nežádoucí účinky.

|Proměnná | Popis|
|---------|------------|
|External_AutoStop_Condition | Podmíněný operátor vyžadovaný pro konfiguraci podmínky před aktivací výstrahy. Přijatelné hodnoty jsou **GreaterThan**, **GreaterThanOrEqual**, **LessThan**a **LessThanOrEqual**.|
|External_AutoStop_Description | Výstraha, která zastaví virtuální počítač, pokud procento procesoru překročí prahovou hodnotu.|
|External_AutoStop_MetricName | Název metriky výkonu, pro kterou má být nakonfigurováno pravidlo upozornění Azure.|
|External_AutoStop_Threshold | Prahová hodnota pro pravidlo Azure výstrahy zadané v proměnné _External_AutoStop_MetricName_. Procentuální hodnoty mohou být v rozsahu od 1 do 100.|
|External_AutoStop_TimeAggregationOperator | Operátor časové agregace, který se použije na velikost vybraného okna pro vyhodnocení podmínky. Přijatelné hodnoty jsou **průměr**, **minimum**, **Maximum**, **celkem**a **Poslední**.|
|External_AutoStop_TimeWindow | Velikost okna, během kterého Azure analyzuje vybrané metriky pro aktivaci výstrahy. Tento parametr akceptuje vstup ve formátu TimeSpan. Možné hodnoty jsou 5 minut až 6 hodin.|
|External_EnableClassicVMs| Určuje, jestli je na klasických virtuálních počítačích cíleno řešení. Výchozí hodnota je True (Pravda). Toto nastavení by mělo být pro odběry CSP nastaveno na hodnotu NEPRAVDA. Klasické virtuální počítače vyžadují [účet Spustit jako pro Classic](automation-create-standalone-account.md#classic-run-as-accounts).|
|External_ExcludeVMNames | Zadejte názvy virtuálních počítačů, které se mají vyloučit, a oddělte názvy čárkami bez mezer. To je omezeno na 140 virtuálních počítačů. Pokud do tohoto seznamu odděleného čárkami přidáte více než 140 virtuálních počítačů, virtuální počítače, které mají být vyloučeny, mohou být neúmyslně spuštěny nebo zastaveny.|
|External_Start_ResourceGroupNames | Určuje jednu nebo více skupin prostředků, které oddělují hodnoty pomocí čárky, cílené na počáteční akce.|
|External_Stop_ResourceGroupNames | Určuje jednu nebo více skupin prostředků, které oddělují hodnoty pomocí čárky, cílené pro akce zastavení.|
|Internal_AutomationAccountName | Určuje název účtu Automation.|
|Internal_AutoSnooze_WebhookUri | Určuje identifikátor URI Webhooku volaný pro scénář autostop.|
|Internal_AzureSubscriptionId | Určuje ID předplatného Azure.|
|Internal_ResourceGroupName | Určuje název skupiny prostředků účtu Automation.|

V rámci všech scénářů jsou pro cílení na virtuální počítače nutné proměnné **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**a **External_ExcludeVMNames** , a to s výjimkou poskytnutí seznamu virtuálních počítačů oddělených čárkou pro **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**a **ScheduledStartStop_Parent** sady Runbook. To znamená, že virtuální počítače se musí nacházet v cílových skupinách prostředků pro spuštění a zastavení akcí. Tato logika funguje podobně jako Azure Policy. v takovém případě můžete cílit na předplatné nebo skupinu prostředků a mít akce děděné nově vytvořenými virtuálními počítači. Tento přístup zabraňuje nutnosti udržovat samostatný plán pro každý virtuální počítač a zahájí správu a zastaví se ve škálování.

### <a name="schedules"></a>Plány

V následující tabulce jsou uvedeny všechny výchozí plány vytvořené v účtu Automation. Můžete je upravit nebo vytvořit vlastní plány. Ve výchozím nastavení jsou všechny plány zakázané, kromě **Scheduled_StartVM** a **Scheduled_StopVM**.

Neměli byste povolit všechny plány, protože to může vytvořit překrývající se akce plánování. Nejvhodnější je určit, které optimalizace chcete provést, a odpovídajícím způsobem upravit. Další vysvětlení najdete v ukázkových scénářích v části Přehled.

|Název plánu | Frequency | Popis|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Každých 8 hodin | Spustí sadu AutoStop_CreateAlert_Parent Runbook každých 8 hodin, což zase zastaví hodnoty založené na virtuálním počítači v External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames a External_ExcludeVMNames v Azure Automation proměnných. Alternativně můžete zadat čárkami oddělený seznam virtuálních počítačů pomocí parametru VMList.|
|Scheduled_StopVM | Uživatelem definované, denně | Spustí Scheduled_Parent sadu Runbook s parametrem _stop_ každý den v zadaném čase. Automaticky zastaví všechny virtuální počítače, které splňují pravidla definovaná v proměnných prostředků. Povolte související plán, **naplánované – StartVM**.|
|Scheduled_StartVM | Uživatelem definované, denně | Spustí sadu Scheduled_Parent Runbook s parametrem _Start_ každý den v zadaném čase. Automaticky spustí všechny virtuální počítače, které splňují pravidla definovaná příslušnými proměnnými. Povolte související plán, **naplánované – StopVM**.|
|Sequenced – StopVM | 1:00 dop. (UTC), každý pátek | Spustí sadu Sequenced_Parent Runbook s parametrem _zastavit_ každý pátek v daném čase. Sekvenčně (vzestupně) zastaví všechny virtuální počítače s tagem **SequenceStop** definovanými příslušnými proměnnými. Další informace o hodnotách značek a proměnných prostředků naleznete v části Runbooky. Povolí související plán, **Sequenced-StartVM**.|
|Sequenced – StartVM | 1:00 PM (UTC), každé pondělí | Spustí sadu Sequenced_Parent Runbook s parametrem _Start_ každé pondělí v zadanou dobu. Sekvenčně (sestupně) spustí všechny virtuální počítače se značkou **SequenceStart** definovanou příslušnými proměnnými. Další informace o hodnotách značek a proměnných prostředků naleznete v části Runbooky. Povolí související plán, **Sequenced-StopVM**.|

## <a name="azure-monitor-logs-records"></a>Záznamy protokolu Azure Monitor

Automatizace vytvoří dva typy záznamů v pracovním prostoru Log Analytics: protokoly úloh a datové proudy úloh.

### <a name="job-logs"></a>Protokoly úloh

|Vlastnost | Popis|
|----------|----------|
|Volající |  Kdo operaci zahájil. Možnou hodnotou je e-mailová adresa nebo systém pro naplánované úlohy.|
|Kategorie | Klasifikace typu dat. Službě Automation odpovídá hodnota JobLogs.|
|CorrelationId | Identifikátor GUID, který představuje ID korelace úlohy Runbooku.|
|JobId | Identifikátor GUID, který je ID úlohy Runbooku.|
|operationName | Určuje typ operace prováděné v Azure. Pro automatizaci je tato hodnota úloha.|
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
|JobId | Identifikátor GUID, který je ID úlohy Runbooku.|
|operationName | Určuje typ operace prováděné v Azure. Pro automatizaci je tato hodnota úloha.|
|ResourceGroup | Určuje název skupiny prostředků příslušné úlohy runbooku.|
|resourceId | Určuje ID prostředku v Azure. V případě služby Automation je hodnotou účet služby Automation přidružený k příslušnému runbooku.|
|ResourceProvider | Určuje službu Azure poskytující prostředky, které můžete nasadit a spravovat. Službě Automation odpovídá hodnota Azure Automation.|
|ResourceType | Určuje typ prostředku v Azure. V případě služby Automation je hodnotou účet služby Automation přidružený k příslušnému runbooku.|
|resultType | Výsledek úlohy runbooku v době, kdy byla událost vygenerována. Možná hodnota je:<br>- Probíhá zpracování|
|resultDescription | Zahrnuje výstupní datový proud z runbooku.|
|RunbookName | Název runbooku.|
|SourceSystem | Určuje zdrojový systém pro odeslaná data. Pro automatizaci je hodnota OpsManager.|
|StreamType | Typ datového proudu úlohy. Možné hodnoty:<br>– Průběh<br>- Výstup<br>- Varování<br>- Chyba<br>- Ladění<br>- Podrobné|
|Time | Datum a čas provedení úlohy runbooku.|

Když provedete jakékoli prohledávání protokolů, které vrátí záznamy kategorie **JobLogs** nebo **JobStreams**, můžete vybrat zobrazení **JobLogs** nebo **JobStreams** , které zobrazí sadu dlaždic shrnujících aktualizace vrácené hledáním.

## <a name="sample-log-searches"></a>Ukázky hledání v protokolech

V následující tabulce jsou uvedeny ukázky hledání v protokolech pro záznamy úloh shromážděné tímto řešením.

|Dotaz | Popis|
|----------|----------|
|Najde úlohy pro Runbook ScheduledStartStop_Parent, které byly úspěšně dokončeny. | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Najde úlohy pro Runbook SequencedStartStop_Parent, které byly úspěšně dokončeny. | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="viewing-the-solution"></a>Zobrazení řešení

Pokud chcete získat přístup k řešení, přejděte na svůj účet Automation a v části **související prostředky**vyberte **pracovní prostor** . Na stránce Log Analytics vyberte **řešení** **obecně**. Na stránce **řešení** vyberte řešení **Start-Stop-VM [pracovní prostor]** ze seznamu.

Výběrem řešení se zobrazí stránka řešení **Start-Stop-VM [pracovní prostor]** . Tady si můžete prohlédnout důležité podrobnosti, jako je například dlaždice **StartStopVM** . Stejně jako ve vašem pracovním prostoru Log Analytics tato dlaždice zobrazuje počet a grafické znázornění úloh sady Runbook pro řešení, které bylo spuštěno a úspěšně skončilo.

![Stránka řešení Update Management pro automatizaci](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Odsud můžete provádět další analýzu záznamů úloh kliknutím na dlaždici prstenec. Řídicí panel řešení zobrazuje historii úloh a předem definované dotazy na prohledávání protokolů. Přepněte na rozšířený portál Log Analytics, abyste mohli vyhledávat na základě vyhledávacích dotazů.

## <a name="configure-email-notifications"></a>Konfigurace e-mailových oznámení

Chcete-li změnit e-mailová oznámení po nasazení řešení, upravte skupinu akcí, která byla vytvořena během nasazení.  

> [!NOTE]
> Předplatná v cloudu Azure Government nepodporují funkce e-mailu tohoto řešení.

V Azure Portal přejděte do skupiny akcí monitorovat >. Vyberte skupinu akcí s názvem **StartStop_VM_Notication**.

![Stránka řešení Update Management pro automatizaci](media/automation-solution-vm-management/azure-monitor.png)

Na stránce **StartStop_VM_Notification** klikněte na **Upravit podrobnosti** v části **Podrobnosti**. Tím se otevře stránka **e-mail/SMS/Push/Voice** . Aktualizujte e-mailovou adresu a kliknutím na **OK** uložte změny.

![Stránka řešení Update Management pro automatizaci](media/automation-solution-vm-management/change-email.png)

Případně můžete do skupiny akcí přidat další akce. Další informace o skupinách akcí naleznete v tématu [skupiny akcí](../azure-monitor/platform/action-groups.md) .

Následuje příklad e-mailu, který se pošle, když řešení vypíná virtuální počítače.

![Stránka řešení Update Management pro automatizaci](media/automation-solution-vm-management/email.png)

## <a name="add-exclude-vms"></a>Přidat nebo vyloučit virtuální počítače

Řešení poskytuje možnost Přidat virtuální počítače, na které má řešení cílit, nebo konkrétně vyloučit počítače z řešení.

### <a name="add-a-vm"></a>Přidání virtuálního počítače

K dispozici je několik možností, pomocí kterých se můžete ujistit, že virtuální počítač je součástí řešení spustit/zastavit při jeho spuštění.

* Každé nadřazené [Runbooky](#runbooks) řešení má parametr **VMList** . K tomuto parametru můžete předat čárkami oddělený seznam názvů virtuálních počítačů při plánování příslušné nadřazené sady Runbook pro vaši situaci a tyto virtuální počítače budou zahrnuty při spuštění řešení.

* Pokud chcete vybrat víc virtuálních počítačů, nastavte **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupNames** názvy skupin prostředků, které obsahují virtuální počítače, které chcete spustit nebo zastavit. Tuto hodnotu můžete také nastavit na `*`, aby bylo řešení spuštěné u všech skupin prostředků v rámci předplatného.

### <a name="exclude-a-vm"></a>Vyloučení virtuálního počítače

Pokud chcete virtuální počítač z řešení vyloučit, můžete ho přidat do proměnné **External_ExcludeVMNames** . Tato proměnná je čárkami oddělený seznam konkrétních virtuálních počítačů, které se mají vyloučit z řešení spustit/zastavit. Tento seznam je omezený na 140 virtuálních počítačů. Pokud do tohoto seznamu odděleného čárkami přidáte více než 140 virtuálních počítačů, virtuální počítače, které mají být vyloučeny, mohou být neúmyslně spuštěny nebo zastaveny.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Změna plánů spouštění a vypínání

Správa plánů spouštění a vypínání v tomto řešení se řídí stejnými kroky, jak je popsáno v části [plánování sady Runbook v Azure Automation](automation-schedules.md). Aby bylo možné začít a zastavovat virtuální počítače, musí být samostatný plán.

Konfigurace řešení pouze k zastavení virtuálních počítačů v určitou dobu je podporována. V tomto scénáři stačí vytvořit plán **zastavení** a nemusíte mít žádné odpovídající plánované **zahájení** . Budete muset:

1. Ujistěte se, že jste přidali skupiny prostředků pro virtuální počítače, které se mají vypnout v proměnné **External_Stop_ResourceGroupNames** .
2. Vytvořte si vlastní plán pro čas, kdy chcete virtuální počítače vypnout.
3. Přejděte do sady Runbook **ScheduledStartStop_Parent** a klikněte na **plán**. To vám umožní vybrat plán, který jste vytvořili v předchozím kroku.
4. Vyberte **parametry a nastavení spuštění** a nastavte parametr Action na stop (zastavit).
5. Klikněte na tlačítko **OK** a uložte změny.

## <a name="update-the-solution"></a>Aktualizace řešení

Pokud jste nasadili předchozí verzi tohoto řešení, musíte ji před nasazením aktualizované verze nejdřív odstranit z účtu. Postupujte podle kroků pro [Odebrání řešení](#remove-the-solution) a pak postupujte podle výše uvedených kroků a [nasaďte řešení](#deploy-the-solution).

## <a name="remove-the-solution"></a>Odebrat řešení

Pokud se rozhodnete, že už toto řešení nebudete používat, můžete ho odstranit z účtu Automation. Odstraněním řešení se odstraní jenom Runbooky. Neodstraní plány nebo proměnné, které byly vytvořeny při přidání řešení. Ty prostředky, které potřebujete odstranit ručně, pokud je nepoužíváte s jinými sadami Runbook.

Řešení odstraníte tak, že provedete následující kroky:

1. V účtu Automation v části **související prostředky**vyberte **propojený pracovní prostor**.
1. Vyberte **Přejít k pracovnímu prostoru**.
1. V části **Obecné**vyberte **řešení**. 
1. Na stránce **řešení** vyberte řešení **Start-Stop-VM [pracovní prostor]** . Na stránce **VMManagementSolution [pracovní prostor]** v nabídce vyberte **Odstranit**.<br><br> ![odstranit řešení pro správu virtuálních počítačů](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. V okně **Odstranit řešení** potvrďte, že chcete odstranit řešení.
1. I když jsou informace ověřené a řešení se odstraní, můžete sledovat jeho průběh v části **oznámení** z nabídky. Až se spustí proces odebrání řešení, vrátíte se na stránku **řešení** .

Účet služby Automation a Log Analytics pracovní prostor se v rámci tohoto procesu neodstraní. Pokud nechcete zachovat Log Analytics pracovní prostor, budete ho muset odstranit ručně. To lze provést z Azure Portal:

1. V Azure Portal vyhledejte a vyberte **Log Analytics pracovní prostory**.
1. Na stránce **Log Analytics pracovní prostory** vyberte pracovní prostor.
1. V nabídce na stránce nastavení pracovního prostoru vyberte **Odstranit** .

Pokud nechcete zachovat komponenty účtu Azure Automation, můžete je ručně odstranit. Seznam runbooků, proměnných a plánů vytvořených řešením najdete v tématu [součásti řešení](#solution-components).

## <a name="next-steps"></a>Další kroky

- Další informace o tom, jak vytvářet různé vyhledávací dotazy a kontrolovat protokoly úloh služby Automation pomocí protokolů Azure Monitor, najdete [v tématu prohledávání protokolů v protokolech Azure monitor](../log-analytics/log-analytics-log-searches.md).
- Další informace o spouštění runbooků, postupy při monitorování úloh runbooků a další technické podrobnosti najdete v článku [Sledování úlohy runbooku](automation-runbook-execution.md).
- Další informace o Azure Monitor protokolů a zdrojích shromažďování dat najdete [v tématu shromažďování dat služby Azure Storage v protokolech Azure monitor přehled](../azure-monitor/platform/collect-azure-metrics-logs.md).
