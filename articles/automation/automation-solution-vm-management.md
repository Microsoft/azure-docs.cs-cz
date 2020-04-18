---
title: Spuštění a zastavení virtuálních disponování během mimopracovního řešení
description: Toto řešení pro správu virtuálních počítačů spustí a zastaví vaše virtuální počítače Azure podle plánu a proaktivně monitoruje z protokolů Azure Monitoru.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 968e609772e08814a9943734d30c16bf6f5972e8
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604710"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Spuštění a zastavení virtuálních počítačů během mimopracovní ho řešení v Azure Automation

**Spuštění a zastavení virtuálních počítačů během mimo pracovní dobu řešení** spustí nebo zastaví vaše virtuální počítače Azure. Spustí nebo zastaví počítače na uživatelem definovaných plánech, poskytuje přehledy prostřednictvím protokolů Azure Monitor a odesílá volitelné e-maily pomocí [skupin akcí](../azure-monitor/platform/action-groups.md). Řešení podporuje Azure Resource Manager a klasické virtuální počítače pro většinu scénářů. 

Toto řešení poskytuje decentralizovou možnost automatizace s nízkými náklady pro uživatele, kteří chtějí optimalizovat náklady na virtuální počítač. S tímto řešením můžete:

- [Naplánujte spuštění a zastavení virtuálních montovek](automation-solution-vm-management-config.md#schedule).
- Naplánujte, aby se virtuální počítače spouštěla a zastavovala ve vzestupném pořadí [pomocí značek Azure](automation-solution-vm-management-config.md#tags) (není podporována pro klasické virtuální počítače).
- Virtuální počítače autostop na základě [nízkého využití procesoru](automation-solution-vm-management-config.md#cpuutil).

> [!NOTE]
> **Řešení Start/stop VM během mimopracovní doby** bylo aktualizováno tak, aby podporovalo nejnovější verze modulů Azure, které jsou k dispozici.

Níže jsou omezení s aktuální řešení:

- Spravuje virtuální počítače v libovolné oblasti, ale lze použít jenom ve stejném předplatném jako váš účet Azure Automation.
- Je k dispozici v Azure a Azure Government do jakékoli oblasti, která podporuje pracovní prostor Log Analytics, účet Azure Automation a výstrahy. Oblasti Azure Government aktuálně nepodporují funkce e-mailu.

## <a name="solution-prerequisites"></a>Předpoklady řešení

Sady Runbook pro toto řešení fungují s [účtem Azure Run As](automation-create-runas-account.md). Účet Spustit jako je upřednostňovaná metoda ověřování, protože používá ověřování certifikátu namísto hesla, jehož platnost může vypršet nebo často měnit.

Doporučujeme použít samostatný účet automatizace pro **virtuální chody Start/stop během mimopracovního** provozu řešení. Verze modulu Azure jsou často upgradovány a jejich parametry se mohou změnit. Řešení není upgradováno na stejné kadence a nemusí fungovat s novějšími verzemi rutin, které používá. Doporučujeme otestovat aktualizace modulů v účtu automatizace testu před jejich importem do produkčního účtu automatizace.

## <a name="solution-permissions"></a>Oprávnění řešení

Musíte mít určitá oprávnění k nasazení **virtuálních aplikací Start/stop během mimopracovního** řešení. Oprávnění se liší, pokud řešení používá předem vytvořený účet Automation a pracovní prostor Log Analytics z oprávnění potřebných, pokud řešení vytvoří nový účet a pracovní prostor během nasazení. 

Pokud jste přispěvatelem předplatného a globálním správcem v tenantovi Služby Azure Active Directory, nemusíte konfigurovat oprávnění. Pokud tato práva nemáte nebo potřebujete nakonfigurovat vlastní roli, ujistěte se, že máte níže popsaná oprávnění.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Oprávnění pro již existující účet Automatizace a pracovní prostor Log Analytics

Chcete-li nasadit **virtuální počítač start/stop během mimopracovní ch odpracovaných hodin** do existujícího účtu Automation a pracovního prostoru Log Analytics, vyžaduje uživatel, který nasadí řešení, následující oprávnění v oboru Skupiny prostředků. Další informace o rolích najdete [v tématu Vlastní role pro prostředky Azure](../role-based-access-control/custom-roles.md).

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

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Oprávnění pro nový účet automatizace a nový pracovní prostor Log Analytics

Virtuální chod start/stop můžete nasadit **během mimopracovního** řešení do nového účtu Automation a pracovního prostoru Log Analytics. V takovém případě uživatel, který nasadí řešení, potřebuje oprávnění definovaná v předchozí části a také oprávnění definovaná v této části. 

Uživatel, který nasadí řešení, potřebuje následující role:

- Coadministrator na předplatné. Tato role je nutné vytvořit účet Klasické spuštění jako, pokud se chystáte spravovat klasické virtuální chod. [Klasické spustit jako účty](automation-create-standalone-account.md#create-a-classic-run-as-account) již nejsou vytvořeny ve výchozím nastavení.
- Členství v roli vývojáře aplikací [Služby Azure Active Directory.](../active-directory/users-groups-roles/directory-assign-admin-roles.md) Další informace o konfiguraci spouštět jako účty naleznete v [tématu Oprávnění ke konfiguraci účtů Spustit jako](manage-runas-account.md#permissions).
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

**Řešení Start/stop VM během mimopracovní doby** zahrnuje předem nakonfigurované sady runbooků, plány a integraci s protokoly Azure Monitoru. Pomocí těchto prvků můžete přizpůsobit spuštění a vypnutí virtuálních počítačů tak, aby vyhovovaly vašim obchodním potřebám.

### <a name="runbooks"></a>Runbooky

V následující tabulce jsou uvedeny sady Runbook, které řešení nasazuje do vašeho účtu Automation. Neprovávejte změny kódu runbooku. Místo toho napište vlastní runbook pro nové funkce.

> [!IMPORTANT]
> Nespouštějte přímo žádný runbook s **podřízeným** připojeným k jeho názvu.

Všechny nadřazené sady Runbook obsahují `WhatIf` parametr. Při nastavení true, parametr podporuje podrobné podrobnosti o přesné chování runbook se při spuštění bez parametru a ověří, že jsou cíleny správné virtuální počítače. Sada Runbook provádí své definované `WhatIf` akce pouze v případě, že je parametr nastaven na hodnotu False.

|Runbook | Parametry | Popis|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | Objekt VM <br> Akce výstrahy <br> WebHookURI | Volána z nadřazeného runbooku. Tento soubor Runbook vytváří výstrahy pro základě prostředků pro scénář automatického zastavení.|
|AutoStop_CreateAlert_Parent | Seznam VMList<br> WhatIf: Pravda nebo nepravda  | Vytvoří nebo aktualizuje pravidla výstrah Azure na virtuálních počítačích v cílovépředplatné nebo skupiny prostředků. <br> `VMList`je seznam virtuálních vitéodů oddělený čárkami. Například, `vm1, vm2, vm3`.<br> `WhatIf`umožňuje ověření logiky sady Runbook bez spuštění.|
|AutoStop_Disable | Žádná | Zakáže automatické zastavení výstrah a výchozí plán.|
|AutoStop_VM_Child | WebHookData | Volána z nadřazeného runbooku. Pravidla výstrah volání tohoto runbooku zastavit klasický virtuální hod.|
|AutoStop_VM_Child_ARM | WebHookData |Volána z nadřazeného runbooku. Pravidla výstrah volání tohoto runbooku zastavit virtuální ho.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Akce: Spuštění nebo zastavení<br> Seznam VMList  | Provádí spuštění nebo zastavení akce v klasické skupině virtuálních virtuálních připojení pomocí cloudových služeb. |
|ScheduledStartStop_Child | VMName <br> Akce: Spuštění nebo zastavení <br> ResourceGroupName | Volána z nadřazeného runbooku. Provede akci zahájení nebo zastavení pro naplánovanou zastávku.|
|ScheduledStartStop_Child_Classic | VMName<br> Akce: Spuštění nebo zastavení<br> ResourceGroupName | Volána z nadřazeného runbooku. Provede akci spuštění nebo zastavení pro naplánovanou zastávku pro klasické virtuální hody. |
|ScheduledStartStop_Parent | Akce: Spuštění nebo zastavení <br>Seznam VMList <br> WhatIf: Pravda nebo nepravda | Spustí nebo zastaví všechny virtuální aplikace v předplatném. Upravte proměnné `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` a spouštět pouze na těchto cílových skupin ách prostředků. Můžete také vyloučit konkrétní virtuální chod aktualizací `External_ExcludeVMNames` proměnné.|
|SequencedStartStop_Parent | Akce: Spuštění nebo zastavení <br> WhatIf: Pravda nebo nepravda<br>Seznam VMList| Vytvoří značky s názvem **sequencestart** a **sequencestop** na každém virtuálním počítači, pro který chcete sekvencovat aktivitu start/stop. Tyto názvy značek rozlišují malá a velká písmena. Hodnota značky by měla být kladné celé číslo (1, 2, 3), které odpovídá pořadí, ve kterém chcete spustit nebo zastavit. <br>**Poznámka:** Virtuální ho dispoziční `External_Stop_ResourceGroupNames`správce `External_ExcludeVMNames` musí být v rámci skupin prostředků definovaných v `External_Start_ResourceGroupNames`, a proměnných. Musí mít příslušné značky, aby se akce projevily.|

### <a name="variables"></a>Proměnné

V následující tabulce jsou uvedeny proměnné vytvořené v účtu automatizace. Upravujte pouze proměnné `External`s předponou . Změna proměnných s `Internal` předponou způsobuje nežádoucí účinky.

> [!NOTE]
> Omezení názvu virtuálního počítače a skupiny prostředků jsou z velké části výsledkem velikosti proměnné.

|Proměnná | Popis|
|---------|------------|
|External_AutoStop_Condition | Podmíněný operátor potřebný pro konfiguraci podmínky před aktivací výstrahy. Přijatelné hodnoty `GreaterThan`jsou `GreaterThanOrEqual` `LessThan`, `LessThanOrEqual`, a .|
|External_AutoStop_Description | Výstraha k zastavení virtuálního počítače, pokud procento procesoru překročí prahovou hodnotu.|
|External_AutoStop_Frequency | Četnost hodnocení pravidla. Tento parametr přijímá vstup ve formátu timespan. Možné hodnoty jsou od 5 minut do 6 hodin. |
|External_AutoStop_MetricName | Název metriky výkonu, pro kterou má být nakonfigurováno pravidlo výstrahy Azure.|
|External_AutoStop_Severity | Závažnost upozornění metriky, která může být v rozsahu od 0 do 4. |
|External_AutoStop_Threshold | Prahová hodnota pro pravidlo Výstrahy `External_AutoStop_MetricName`Azure zadaná v proměnné . Procentuální hodnoty jsou v rozsahu od 1 do 100.|
|External_AutoStop_TimeAggregationOperator | Operátor agregace času použitý na vybranou velikost okna k vyhodnocení podmínky. Přijatelné hodnoty `Average`jsou `Minimum` `Maximum`, `Total`, `Last`, a .|
|External_AutoStop_TimeWindow | Velikost okna, během kterého Azure analyzuje vybrané metriky pro aktivaci výstrahy. Tento parametr přijímá vstup ve formátu timespan. Možné hodnoty jsou od 5 minut do 6 hodin.|
|External_EnableClassicVMs| Hodnota určující, pokud jsou klasické virtuální chody cílené řešení. Výchozí hodnota je True. Nastavte tuto proměnnou na false pro předplatná Zprostředkovatele cloudových řešení Azure (CSP). Klasické virtuální ho dispozice vyžadují [klasický účet spustit jako](automation-create-standalone-account.md#create-a-classic-run-as-account).|
|External_ExcludeVMNames | Seznam názvů virtuálních počítačů oddělených čárkami, který má být vyloučen, omezený na 140 virtuálních počítačů. Pokud do seznamu přidáte více než 140 virtuálních ms, virtuální ch odvod, který chytat není možné je vyloučit, může být neúmyslně spuštěné nebo zastavené.|
|External_Start_ResourceGroupNames | Seznam jedné nebo více skupin prostředků oddělených čárkami, které jsou určeny pro počáteční akce.|
|External_Stop_ResourceGroupNames | Seznam jedné nebo více skupin prostředků oddělených čárkami, které jsou určeny pro akce stop.|
|External_WaitTimeForVMRetrySeconds |Čekací doba v sekundách pro akce, které mají být provedeny na virtuálních počítačích pro **SequencedStartStop_Parent** runbook. Tato proměnná umožňuje runbook čekat na podřízené operace pro zadaný počet sekund před pokračováním další akce. Maximální čekací doba je 10800 nebo tři hodiny. Výchozí hodnota je 2100 sekund.|
|Internal_AutomationAccountName | Určuje název účtu automatizace.|
|Internal_AutoSnooze_ARM_WebhookURI | Webhooku URI volal pro scénář AutoStop pro virtuální počítače.|
|Internal_AutoSnooze_WebhookUri | Webhooku URI volal pro scénář AutoStop pro klasické virtuální počítače.|
|Internal_AzureSubscriptionId | ID předplatného Azure.|
|Internal_ResourceGroupName | Název skupiny prostředků účtu automatizace.|

>[!NOTE]
>Pro proměnnou `External_WaitTimeForVMRetryInSeconds`byla výchozí hodnota aktualizována z 600 na 2100. 

Ve všech scénářích `External_Start_ResourceGroupNames`proměnné `External_Stop_ResourceGroupNames`, `External_ExcludeVMNames` a jsou nezbytné pro cílení na virtuální chod, s výjimkou seznamů virtuálních zařízení oddělených čárkami pro **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**a **ScheduledStartStop_Parent** runbooky. To znamená, že vaše virtuální počítače musí patřit do cílových skupin prostředků pro spuštění a zastavení akce dojít. Logika funguje podobně jako zásady Azure v tom, že můžete cílit na předplatné nebo skupinu prostředků a mít akce zděděné nově vytvořenými virtuálními počítači. Tento přístup se vyhýbá nutnosti udržovat samostatný plán pro každý virtuální virtuální ms a spravovat spuštění a zastavení ve velkém měřítku.

### <a name="schedules"></a>Plány

V následující tabulce jsou uvedeny všechny výchozí plány vytvořené v účtu automatizace.Můžete je upravit nebo vytvořit vlastní plány.Ve výchozím nastavení jsou zakázány všechny plány s výjimkou **Scheduled_StartVM** a **Scheduled_StopVM** plánů.

Nepovolujte všechny plány, protože by to mohlo způsobit překrývající se akce plánu. Nejlepší je určit, které optimalizace chcete provést, a odpovídajícím způsobem je upravit. Další vysvětlení najdete v ukázkových scénářích v části s přehledem.

|Název plánu | Frequency | Popis|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Každých 8 hodin | Spustí **AutoStop_CreateAlert_Parent** runbookkaždý 8 hodin, což zase zastaví hodnoty `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames`založené `External_ExcludeVMNames` na virtuálních mích v , a proměnné. Případně můžete zadat seznam virtuálních vod oddělených čárkami `VMList` pomocí parametru.|
|Scheduled_StopVM | Uživatelem definované, denně | Spustí **ScheduledStopStart_Parent** runbook s `Stop` parametrem každý den v zadaném čase.Automaticky zastaví všechny virtuální uživatele, které splňují pravidla definovaná proměnnými prostředky.Povolte související plán **Naplánovaný StartVM**.|
|Scheduled_StartVM | Uživatelem definované, denně | Spustí **ScheduledStopStart_Parent** runbook s hodnotou parametru `Start` každý den v určený čas. Automaticky spustí všechny virtuální uživatele, které splňují pravidla definovaná proměnnými aktivy.Povolte související plán **Scheduled-StopVM**.|
|Sekvenční-StopVM | 1:00 (UTC), každý pátek | Spustí Sequenced_Parent runbook s hodnotou parametru `Stop` každý pátek v určený čas.Postupně (vzestupně) zastaví všechny virtuální uživatele se značkou **SequenceStop** definované příslušné proměnné. Další informace o hodnotách značek a proměnných datových zdrojů najdete v části Runbook.Povolte související plán **Sequenced-StartVM**.|
|Sekvenční-StartVM | 13:00 (UTC), každé pondělí | Spustí **SequencedStopStart_Parent** runbook s hodnotou parametru `Start` každé pondělí v určený čas. Postupně (sestupně) spustí všechny virtuální uživatele s tag **sequencestart** definované příslušné proměnné. Další informace o hodnotách značek a proměnných datových zdrojů najdete v tématu [Runbook](#runbooks). Povolte související plán **Sequenced-StopVM**.

## <a name="use-of-the-solution-with-classic-vms"></a>Použití řešení s klasickými virtuálními aplikacemi

Pokud používáte **řešení Start/stop v průběhu mimopracovní doby** pro klasické virtuální počítače, pak automatizace zpracovává všechny vaše virtuální počítače postupně na cloudovou službu. Virtuální aplikace se stále zpracovávají paralelně napříč různými cloudovými službami. 

Pro použití řešení s klasickými virtuálními aplikacemi potřebujete účet Classic Run As, který se ve výchozím nastavení nevytvoří. Pokyny k vytvoření účtu Classic Run As naleznete v [tématu Vytvoření účtu klasického spuštění jako](automation-create-standalone-account.md#create-a-classic-run-as-account).

Pokud máte více než 20 virtuálních připojení na cloudovou službu, tady jsou některá doporučení:

* Vytvořte více plánů s nadřazenou **ScheduledStartStop_Parent** runbooku a určením 20 virtuálních počítačů podle plánu. 
* Ve vlastnostech plánu `VMList` použijte parametr k určení názvů virtuálních počítačů jako seznamu odděleného čárkami. 

V opačném případě pokud úloha automatizace pro toto řešení běží déle než tři hodiny, je dočasně uvolněna nebo zastavena podle limitu [spravedlivého podílu.](automation-runbook-execution.md#fair-share)

Předplatná Azure CSP podporují jenom model Azure Resource Manageru. Služby Azure Resource Manager nejsou v programu dostupné. Při **spuštění virtuálních her Start/stop během mimopracovní doby řešení** může dojít k chybám, protože má rutiny pro správu klasických prostředků. Další informace o csp najdete [v tématu Dostupné služby v předplatných CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Pokud používáte předplatné CSP, měli byste nastavit [proměnnou External_EnableClassicVMs](#variables) na False po nasazení.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-solution"></a>Povolení řešení

Chcete-li začít používat řešení, postupujte podle kroků v [povolit řešení virtuálních her Start/stop](automation-solution-vm-management-enable.md).

## <a name="view-the-solution"></a>Zobrazení řešení

Pomocí jednoho z následujících mechanismů získáte přístup k řešení po jeho povolení:

* V účtu Automation vyberte v části **Související prostředky** **položku Spustit/zastavit virtuální počítač** . Na stránce Start/Stop VM vyberte **Spravovat řešení** z pravé strany stránky v části Správa **řešení virtuálních počítačů Start/Stop**.

* Přejděte do pracovního prostoru Log Analytics propojeného s vaším účtem Automation. Po výběru pracovního prostoru zvolte **Řešení** z levého podokna. Na stránce Řešení vyberte ze seznamu řešení **Start-Stop-VM[pracovní prostor].**  

Při výběru řešení se zobrazí stránka řešení **Start-Stop-VM[pracovní prostor].** Zde si můžete prohlédnout důležité podrobnosti, jako jsou informace na dlaždici **StartStopVM.** Stejně jako v pracovním prostoru Log Analytics tato dlaždice zobrazuje počet a grafické znázornění úloh runbooku pro řešení, které bylo úspěšně zahájeno a dokončeno.

![Stránka řešení správa aktualizací automatizace](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Další analýzu záznamů úloh můžete provést kliknutím na dlaždici koblihy. Řídicí panel řešení zobrazuje historii úloh a předdefinované vyhledávací dotazy protokolu. Přepněte na pokročilý portál analýzy protokolů a můžete je prohledávat na základě vyhledávacích dotazů.

## <a name="update-the-solution"></a>Aktualizace tohoto řešení

Pokud jste nasadili předchozí verzi tohoto řešení, odstraňte ji z účtu před nasazením aktualizované verze. Podle pokynů [řešení odeberte](#remove-the-solution) a potom podle pokynů [nasadit řešení](automation-solution-vm-management-enable.md).

## <a name="remove-the-solution"></a>Odebrání tohoto řešení

Pokud již nepotřebujete řešení používat, můžete jej odstranit z účtu automatizace. Odstraněním řešení odeberete pouze runbooky. Neodstraní plány nebo proměnné, které byly vytvořeny při přidání řešení. Pokud je nepoužíváte s jinými runbooky, odeberte je ručně.

Chcete-li odstranit řešení:

1. V části **Související prostředky**vyberte v části Související prostředky možnost Propojený pracovní **prostor** .

2. Vyberte **Přejít do pracovního prostoru**.

3. Klepněte na **položku Řešení** v části **Obecné**. 

4. Na stránce Řešení vyberte řešení **Start-Stop-VM[Pracovní prostor]**. 

5. Na stránce **VMManagementSolution[Pracovní prostor]** vyberte **odstranit** z nabídky.<br><br> ![Odstranit řešení Mgmt virtuálního měn](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. V okně **Odstranit řešení** potvrďte, že chcete odstranit řešení.

7. Při ověřování informací a odstranění řešení můžete sledovat průběh v části **Oznámení**vybraná z nabídky. Po spuštění procesu odebrání řešení se vrátíte na stránku Řešení.

Účet automatizace a pracovní prostor Log Analytics nejsou odstraněny jako součást tohoto procesu. Pokud nechcete zachovat pracovní prostor Log Analytics, musíte ho ručně odstranit z webu Azure Portal:

1. Vyhledejte a vyberte **pracovní prostory Analýzy protokolů**.

2. Na stránce pracovního prostoru Log Analytics vyberte pracovní prostor.

3. Z nabídky na stránce nastavení pracovního prostoru vyberte **Odstranit.**

4. Pokud nechcete zachovat [součásti řešení](#solution-components)účtu Azure Automation , můžete je ručně odstranit.

## <a name="next-steps"></a>Další kroky

[Povolte](automation-solution-vm-management-enable.md) **virtuální počítače Start/stop během mimopracovní ho** řešení pro vaše virtuální počítače Azure.
