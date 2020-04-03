---
title: Spuštění/zastavení virtuálních disponování během mimopracovního řešení
description: Toto řešení pro správu virtuálních počítačů spustí a zastaví vaše virtuální počítače Azure podle plánu a proaktivně monitoruje z protokolů Azure Monitoru.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 906c7728365cc902549bd46c57972e1c90af979c
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607480"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Řešení Start/Stop VMs during off-hours v Azure Automation

Spuštění a zastavení virtuálních počítačů během off-hours řešení spustit a zastaví vaše virtuální počítače Azure na uživatelem definované plány, poskytuje přehledy prostřednictvím protokolů Azure Monitor a odesílá volitelné e-maily pomocí [skupin akcí](../azure-monitor/platform/action-groups.md). Podporuje Azure Resource Manager a klasické virtuální počítače pro většinu scénářů. 

Chcete-li použít toto řešení s klasickými virtuálními aplikacemi, potřebujete účet Classic RunAs, který není vytvořen ve výchozím nastavení. Pokyny k vytvoření účtu Classic RunAs naleznete [v tématu Klasické účty run-as](automation-create-standalone-account.md#classic-run-as-accounts).

> [!NOTE]
> Řešení Start/Stop VM během mimopracovní doby bylo aktualizováno tak, aby podporovalo nejnovější verze modulů Azure, které jsou k dispozici.

Toto řešení poskytuje decentralizovou možnost automatizace s nízkými náklady pro uživatele, kteří chtějí optimalizovat náklady na virtuální počítač. S tímto řešením můžete:

- [Naplánujte spuštění a zastavení virtuálních montovek](automation-solution-vm-management-config.md#schedule).
- Naplánujte, aby se virtuální počítače spouštěla a zastavovala ve vzestupném pořadí [pomocí značek Azure](automation-solution-vm-management-config.md#tags) (není podporována pro klasické virtuální počítače).
- Virtuální počítače autostop na základě [nízkého využití procesoru](automation-solution-vm-management-config.md#cpuutil).

Níže jsou omezení s aktuální řešení:

- Toto řešení spravuje virtuální počítače v libovolné oblasti, ale lze použít jenom ve stejném předplatném jako váš účet Azure Automation.
- Toto řešení je dostupné v Azure a Azure Government pro všechny oblasti, které podporuje pracovní prostor Analýzy protokolů, účet Azure Automation a výstrahy. Oblasti Azure Government aktuálně nepodporují funkce e-mailu.

> [!NOTE]
> Pokud používáte řešení pro klasické virtuální počítače, pak všechny vaše virtuální počítače se budou zpracovávat postupně na cloudovou službu. Virtuální počítače se stále zpracovávají paralelně napříč různými cloudovými službami. Pokud máte více než 20 virtuálních počítačů na cloudovou službu, doporučujeme vytvořit více plánů s nadřazenou **ScheduledStartStop_Parent** runbooku a zadat 20 virtuálních počítačů na plán. Ve vlastnostech plánu zadejte jako seznam oddělený čárkami názvy virtuálních počítačů v parametru **VMList.** V opačném případě pokud úloha automatizace pro toto řešení běží více než tři hodiny, je dočasně uvolněna nebo zastavena na spravedlivý limit [sdílené složky.](automation-runbook-execution.md#fair-share)
>
> Předplatná Azure Cloud Solution Provider (Azure CSP) podporují jenom model Azure Resource Manager, jiné služby Azure Resource Manager nejsou v programu dostupné. Při spuštění řešení Start/Stop může dojít k chybám, protože má rutiny pro správu klasických prostředků. Další informace o csp najdete [v tématu Dostupné služby v předplatných CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Pokud používáte předplatné CSP, měli byste upravit [**proměnnou External_EnableClassicVMs**](#variables) na **False** po nasazení.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Požadavky

Sady Runbook pro toto řešení fungují s [účtem Azure Run As](automation-create-runas-account.md). Účet Spustit jako je upřednostňovaná metoda ověřování, protože používá ověřování certifikátu namísto hesla, jehož platnost může vypršet nebo často měnit.

Doporučujeme použít samostatný účet automatizace pro řešení spuštění a zastavení virtuálního provozu. Důvodem je, že verze modulu Azure jsou často upgradovány a jejich parametry se mohou změnit. Řešení Start/Stop VM není upgradován na stejné kadence, takže nemusí fungovat s novější verze rutiny, které používá. Doporučujeme také otestovat aktualizace modulů v testovacím účtu automatizace před jejich importem do produkčního účtu automatizace.

### <a name="permissions"></a>Oprávnění

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

## <a name="solution-components"></a>Součásti řešení

Toto řešení zahrnuje předem nakonfigurované sady runbooků, plány a integraci s protokoly Azure Monitoru, takže můžete přizpůsobit spuštění a vypnutí virtuálních počítačů vašim obchodním potřebám.

### <a name="runbooks"></a>Runbooky

V následující tabulce jsou uvedeny sady Runbook nasazené do vašeho účtu Automation tímto řešením. Neprovávejte změny kódu runbooku. Místo toho napište vlastní runbook pro nové funkce.

> [!IMPORTANT]
> Nespouštějte přímo žádný runbook s *podřízeným* připojeným k jeho názvu.

Všechny nadřazené sady Runbook obsahují parametr _WhatIf._ Když je nastavena na **True**, _WhatIf_ podporuje podrobné podrobnosti o přesné chování runbook se při spuštění bez _WhatIf_ parametr a ověřuje správné virtuální počítače jsou cílené. Sada Runbook provádí své definované akce pouze v případě, že je parametr _WhatIf_ nastaven na **hodnotu False**.

|Runbook | Parametry | Popis|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | Objekt VM <br> Akce výstrahy <br> WebHookURI | Volána z nadřazeného runbooku. Tento soubor Runbook vytváří výstrahy pro základě prostředků pro scénář AutoStop.|
|AutoStop_CreateAlert_Parent | Seznam VMList<br> WhatIf: Pravda nebo nepravda  | Vytvoří nebo aktualizuje pravidla výstrah Azure na virtuálních počítačích v cílovépředplatné nebo skupiny prostředků. <br> VMList: Seznam virtuálních vyvěšených podle čárek. Například _vm1, vm2, vm3_.<br> *WhatIf* ověří logiku runbooku bez spuštění.|
|AutoStop_Disable | Žádná | Zakáže výstrahy automatického zastavení a výchozí plán.|
|AutoStop_VM_Child | WebHookData | Volána z nadřazeného runbooku. Pravidla výstrah volání tohoto runbooku zastavit klasický virtuální ho.|
|AutoStop_VM_Child_ARM | WebHookData |Volána z nadřazeného runbooku. Pravidla výstrah volání tohoto runbooku zastavit virtuální ho.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Akce: Spuštění nebo zastavení<br> Seznam VMList  | Tento runbook slouží k provádění spuštění nebo zastavení akce ve skupině klasického virtuálního virtuálního připojení pomocí cloudových služeb.<br> VMList: Seznam virtuálních vyvěšených podle čárek. Například _vm1, vm2, vm3_. |
|ScheduledStartStop_Child | VMName <br> Akce: Spuštění nebo zastavení <br> ResourceGroupName | Volána z nadřazeného runbooku. Provede akci zahájení nebo zastavení pro naplánovanou zastávku.|
|ScheduledStartStop_Child_Classic | VMName<br> Akce: Spuštění nebo zastavení<br> ResourceGroupName | Volána z nadřazeného runbooku. Provede akci spuštění nebo zastavení pro naplánovanou zastávku pro klasické virtuální ho d). |
|ScheduledStartStop_Parent | Akce: Spuštění nebo zastavení <br>Seznam VMList <br> WhatIf: Pravda nebo nepravda | Toto nastavení ovlivní všechny virtuální ho disponia v předplatném. Upravte **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupNames** spouštět pouze v těchto cílových skupinách prostředků. Konkrétní virtuální hody můžete také vyloučit aktualizací **proměnné External_ExcludeVMNames.**<br> VMList: Seznam virtuálních vyvěšených podle čárek. Například _vm1, vm2, vm3_.<br> _WhatIf_ ověří logiku runbooku bez spuštění.|
|SequencedStartStop_Parent | Akce: Spuštění nebo zastavení <br> WhatIf: Pravda nebo nepravda<br>Seznam VMList| Vytvořte značky s názvem **sequencestart** a **sequencestop** na každém virtuálním počítači, pro který chcete sekvencovat aktivitu start/stop. Tyto názvy značek rozlišují malá a velká písmena. Hodnota značky by měla být kladné celé číslo (1, 2, 3), které odpovídá pořadí, ve kterém chcete spustit nebo zastavit. <br> VMList: Seznam virtuálních vyvěšených podle čárek. Například _vm1, vm2, vm3_. <br> _WhatIf_ ověří logiku runbooku bez spuštění. <br> **Poznámka:** Virtuální počítače musí být v rámci skupin prostředků definovaných jako External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames a External_ExcludeVMNames v proměnných Azure Automation. Musí mít příslušné značky, aby se akce projevily.|

### <a name="variables"></a>Proměnné

V následující tabulce jsou uvedeny proměnné vytvořené v účtu automatizace. Upravujte pouze proměnné s předponou **External**. Změna proměnných s předponou **Vnitřní** způsobuje nežádoucí účinky.

|Proměnná | Popis|
|---------|------------|
|External_AutoStop_Condition | Podmíněný operátor potřebný pro konfiguraci podmínky před aktivací výstrahy. Přijatelné hodnoty jsou **GreaterThan**, **GreaterThanOrEqual**, **LessThan**a **LessThanOrEqual**.|
|External_AutoStop_Description | Výstraha k zastavení virtuálního počítače, pokud procento procesoru překročí prahovou hodnotu.|
|External_AutoStop_Frequency | Četnost hodnocení pravidla. Tento parametr přijímá vstup ve formátu timespan. Možné hodnoty jsou od 5 minut do 6 hodin. |
|External_AutoStop_MetricName | Název metriky výkonu, pro kterou má být nakonfigurováno pravidlo výstrahy Azure.|
|External_AutoStop_Severity | Závažnost upozornění metriky, která může být v rozsahu od 0 do 4. |
|External_AutoStop_Threshold | Prahová hodnota pro pravidlo Výstrahy Azure zadaná v proměnné _External_AutoStop_MetricName_. Procentuální hodnoty mohou být v rozsahu od 1 do 100.|
|External_AutoStop_TimeAggregationOperator | Operátor agregace času, který je použit pro vybranou velikost okna k vyhodnocení podmínky. Přijatelné hodnoty jsou **Průměr**, **Minimální**, **Maximální**, **Součet**a **Poslední**.|
|External_AutoStop_TimeWindow | Velikost okna, během kterého Azure analyzuje vybrané metriky pro aktivaci výstrahy. Tento parametr přijímá vstup ve formátu timespan. Možné hodnoty jsou od 5 minut do 6 hodin.|
|External_EnableClassicVMs| Určuje, zda jsou klasické virtuální aplikace cílem řešení. Výchozí hodnota je True. To by měla být nastavena na False pro odběry CSP. Klasické virtuální společnosti vyžadují [klasický účet run-as](automation-create-standalone-account.md#classic-run-as-accounts).|
|External_ExcludeVMNames | Zadejte názvy virtuálních počítačů, které mají být vyloučeny, oddělující názvy pomocí čárky bez mezer. To je omezeno na 140 virtuálních stránek. Pokud přidáte více než 140 virtuálních stránek do tohoto seznamu odděleného čárkami, virtuální chody, které jsou nastaveny na vyloučení, mohou být neúmyslně spuštěny nebo zastaveny.|
|External_Start_ResourceGroupNames | Určuje jednu nebo více skupin prostředků, které oddělují hodnoty pomocí čárky, určené pro počáteční akce.|
|External_Stop_ResourceGroupNames | Určuje jednu nebo více skupin prostředků, které oddělují hodnoty pomocí čárky, určené pro akce stop.|
|External_WaitTimeForVMRetrySeconds |Čekací doba v sekundách pro akce, které mají být provedeny na virtuálních počítačích pro sekvenční start/stop runbook.<br> Výchozí hodnota je 2100 sekund a podporuje konfiguraci na maximální hodnotu 10800 nebo tři hodiny.|
|Internal_AutomationAccountName | Určuje název účtu automatizace.|
|Internal_AutoSnooze_ARM_WebhookURI | Určuje identifikátor URI webhooku volaná pro scénář Automatické ho zastavení pro virtuální počítače.|
|Internal_AutoSnooze_WebhookUri | Určuje identifikátor URI webhooku volaná pro scénář AutoStop pro klasické virtuální počítače.|
|Internal_AzureSubscriptionId | Určuje ID předplatného Azure.|
|Internal_ResourceGroupName | Určuje název skupiny prostředků účtu automatizace.|

>[!NOTE]
>Pro proměnnou **External_WaitTimeForVMRetryInSeconds**byla výchozí hodnota aktualizována z 600 na 2100. Tato proměnná umožňuje **sekvenční start/stop scénář** runbook čekat na podřízené operace pro zadaný počet sekund před pokračováním další akce.
>

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

## <a name="enable-the-solution"></a>Povolení řešení

Chcete-li začít používat řešení, proveďte kroky v [řešení Povolit spuštění a zastavení virtuálních her](automation-solution-vm-management-enable.md).

## <a name="viewing-the-solution"></a>Zobrazení řešení

K řešení můžete přistupovat poté, co jste ho povolili jedním z následujících způsobů:

* V účtu Automation vyberte v části **Související prostředky** **položku Spustit/zastavit virtuální počítač** . Na stránce **Start/Stop VM** vyberte **Spravovat řešení** z pravé strany stránky v části **Správa řešení spuštění a zastavení virtuálního počítače**.

* Přejděte do pracovního prostoru Log Analytics propojeného s účtem Automation a po výběru pracovního prostoru vyberte **řešení** v levém podokně. Na stránce **Řešení** vyberte ze seznamu řešení **Start-Stop-VM[pracovní prostor].**  

Při výběru řešení se zobrazí stránka řešení **Start-Stop-VM[pracovní prostor].** Zde si můžete prohlédnout důležité podrobnosti, jako je například dlaždice **StartStopVM.** Stejně jako v pracovním prostoru Log Analytics tato dlaždice zobrazuje počet a grafické znázornění úloh runbooku pro řešení, které bylo úspěšně zahájeno a dokončeno.

![Stránka řešení správa aktualizací automatizace](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Zde můžete provést další analýzu záznamů úloh kliknutím na dlaždici koblihy. Řídicí panel řešení zobrazuje historii úloh a předdefinované vyhledávací dotazy protokolu. Přepněte na pokročilý portál analýzy protokolů a můžete je prohledávat na základě vyhledávacích dotazů.

## <a name="update-the-solution"></a>Aktualizace tohoto řešení

Pokud jste nasadili předchozí verzi tohoto řešení, musíte ji nejprve odstranit z účtu před nasazením aktualizované verze. Podle pokynů [řešení odeberte](#remove-the-solution) a potom podle pokynů [nasadit řešení](automation-solution-vm-management-enable.md).

## <a name="remove-the-solution"></a>Odebrání tohoto řešení

Pokud se rozhodnete, že již nebudete muset používat řešení, můžete jej odstranit z účtu Automatizace. Odstraněním řešení odeberete pouze runbooky. Neodstraní plány nebo proměnné, které byly vytvořeny při přidání řešení. Tyto prostředky, které potřebujete odstranit ručně, pokud je nepoužíváte s jinými runbooky.

Chcete-li odstranit řešení, proveďte následující kroky:

1. V části Související **zdroje**vyberte v části Související zdroje **položku Propojený pracovní prostor**.

2. Vyberte **Přejít do pracovního prostoru**.

3. V části **Obecné**vyberte **možnost Řešení**. 

4. Na stránce **Řešení** vyberte řešení **Start-Stop-VM[Pracovní prostor]**. Na stránce **VMManagementSolution[Pracovní prostor]** vyberte v nabídce **odstranit**.<br><br> ![Odstranit řešení Mgmt virtuálního měn](media/automation-solution-vm-management/vm-management-solution-delete.png)

5. V okně **Odstranit řešení** potvrďte, že chcete odstranit řešení.

6. Při ověřování informací a odstranění řešení můžete sledovat jejich průběh v části **Oznámení** z nabídky. Po spuštění procesu odebrání řešení se vrátíte na stránku **Řešení.**

Účet automatizace a pracovní prostor Log Analytics nejsou odstraněny jako součást tohoto procesu. Pokud nechcete zachovat pracovní prostor Log Analytics, je třeba jej ručně odstranit. Toho lze dosáhnout z webu Azure Portal:

1. Na webu Azure Portal vyhledejte a vyberte **pracovní prostory Log Analytics**.

2. Na stránce **Pracovní prostory Analýzy protokolů** vyberte pracovní prostor.

3. Z nabídky na stránce nastavení pracovního prostoru vyberte **Odstranit.**

Pokud nechcete zachovat součásti účtu Azure Automation, můžete je odstranit ručně. Seznam sad Runbook, proměnných a plánů vytvořených řešením najdete v tématu [Součásti řešení](#solution-components).

## <a name="next-steps"></a>Další kroky

[Povolte](automation-solution-vm-management-enable.md) řešení Start/Stop během mimo pracovní doby pro virtuální počítače Azure.
