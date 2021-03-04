---
title: Přehled Azure Automation Start/Stop VMs during off-hours
description: Tento článek popisuje funkci Start/Stop VMs during off-hours, která spouští nebo zastavuje virtuální počítače v plánu a proaktivně monitoruje z protokolů Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: e58f63b6ed7fb26a4e3b3069773810c5e5b7cdc3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732271"
---
# <a name="startstop-vms-during-off-hours-overview"></a>Přehled Start/Stop VMs during off-hours

Funkce Start/Stop VMs during off-hours spustí nebo zastaví povolený virtuální počítač Azure. Spouští nebo zastavuje počítače na uživatelem definovaných plánech, poskytuje přehledy prostřednictvím protokolů Azure Monitor a odesílá volitelné e-maily pomocí [skupin akcí](../azure-monitor/alerts/action-groups.md). Tuto funkci můžete pro většinu scénářů povolit na obou Azure Resource Manager i v klasických virtuálních počítačích. 

Tato funkce používá ke spuštění virtuálních počítačů rutinu [Start-AzVm](/powershell/module/az.compute/start-azvm) . Pro zastavování virtuálních počítačů používá [stop-AzVM](/powershell/module/az.compute/stop-azvm) .

> [!NOTE]
> I když byly Runbooky aktualizované, aby používaly nové rutiny AZ modul pro Azure, používají alias předpony AzureRM.

> [!NOTE]
> Start/Stop VMs during off-hours se aktualizovala tak, aby podporovala nejnovější verze modulů Azure, které jsou k dispozici. Aktualizovaná verze této funkce, která je k dispozici na webu Marketplace, nepodporuje moduly AzureRM, protože jsme migrovali z AzureRM na az Modules.

Tato funkce poskytuje decentralizovanou možnost automatizace s nízkými náklady pro uživatele, kteří chtějí optimalizovat náklady na virtuální počítače. Tuto funkci můžete použít k těmto akcím:

- [Naplánujte spouštění a zastavování virtuálních počítačů](automation-solution-vm-management-config.md#schedule).
- Naplánujte spouštění a zastavování virtuálních počítačů ve vzestupném pořadí [pomocí značek Azure](automation-solution-vm-management-config.md#tags). Tato aktivita není pro klasické virtuální počítače podporována.
- Zastavovat virtuální počítače na základě [nízkého využití procesoru](automation-solution-vm-management-config.md#cpuutil).

U aktuální funkce platí následující omezení:

- Spravuje virtuální počítače v jakékoli oblasti, ale dá se použít jenom ve stejném předplatném jako váš účet Azure Automation.
- Je k dispozici v Azure a Azure Government pro libovolnou oblast, která podporuje pracovní prostor Log Analytics, Azure Automation účet a výstrahy. Azure Government oblasti momentálně nepodporují funkce e-mailu.

## <a name="prerequisites"></a>Požadavky

- Sady Runbook pro spouštění/zastavování virtuálních počítačů v době mimo špičku fungují s [účtem spustit jako pro Azure](./automation-security-overview.md#run-as-accounts). Účet Spustit jako je upřednostňovanou metodou ověřování, protože místo hesla, jehož platnost může vypršet nebo často se mění, používá ověřování certifikátů.

- [Pracovní prostor Azure Monitor Log Analytics](../azure-monitor/logs/design-logs-deployment.md) , ve kterém jsou uloženy protokoly úloh sady Runbook a výsledky streamu úloh v pracovním prostoru pro dotazování a analýzu. Účet Automation se dá propojit s novým nebo existujícím Log Analytics pracovním prostorem a oba prostředky musí být ve stejné skupině prostředků.

Pro práci s virtuálními počítači povolenými pro funkci Start/Stop VMs during off-hours doporučujeme použít samostatný účet Automation. Verze modulů Azure se často upgradují a jejich parametry se můžou změnit. Tato funkce není upgradována na stejném tempo a nemusí fungovat s novějšími verzemi rutin, které používá. Před importem aktualizovaných modulů do účtů služby Automation doporučujeme, abyste je naimportovali do účtu služby test Automation, abyste ověřili, že nedochází k žádným problémům s kompatibilitou.

## <a name="permissions"></a>Oprávnění

Musíte mít určitá oprávnění k povolení virtuálních počítačů pro funkci Start/Stop VMs during off-hours. Tato oprávnění se liší v závislosti na tom, jestli funkce používá předem vytvořený účet Automation a Log Analytics pracovní prostor, nebo vytvoří nový účet a pracovní prostor.

Pokud jste přispěvatelem předplatného a globálním správcem v tenantovi Azure Active Directory (AD), nemusíte oprávnění konfigurovat. Pokud tato oprávnění nemáte nebo potřebujete nakonfigurovat vlastní roli, ujistěte se, že máte oprávnění popsaná níže.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Oprávnění pro již existující účet služby Automation a pracovní prostor Log Analytics

Pokud chcete povolit virtuální počítače pro funkci Start/Stop VMs during off-hours pomocí existujícího účtu Automation a pracovního prostoru Log Analytics, budete potřebovat následující oprávnění pro rozsah skupiny prostředků. Další informace o rolích najdete v tématu [vlastní role Azure](../role-based-access-control/custom-roles.md).

| Oprávnění | Obor|
| --- | --- |
| Microsoft. Automation/automationAccounts/Read | Skupina prostředků |
| Microsoft. Automation/automationAccounts/Variables/Write | Skupina prostředků |
| Microsoft. Automation/automationAccounts/Schedules/Write | Skupina prostředků |
| Microsoft. Automation/automationAccounts/Runbooky/Write | Skupina prostředků |
| Microsoft. Automation/automationAccounts/Connections/Write | Skupina prostředků |
| Microsoft. Automation/automationAccounts/Certificates/Write | Skupina prostředků |
| Microsoft. Automation/automationAccounts/modules/Write | Skupina prostředků |
| Microsoft. Automation/automationAccounts/Module/Read | Skupina prostředků |
| Microsoft. Automation/automationAccounts/jobSchedules/Write | Skupina prostředků |
| Microsoft. Automation/automationAccounts/Jobs/Write | Skupina prostředků |
| Microsoft. Automation/automationAccounts/Jobs/Read | Skupina prostředků |
| Microsoft. OperationsManagement/Solutions/Write | Skupina prostředků |
| Microsoft. OperationalInsights/pracovní prostory/* | Skupina prostředků |
| Microsoft. Insights/diagnosticSettings/Write | Skupina prostředků |
| Microsoft. Insights/ActionGroups/Write | Skupina prostředků |
| Microsoft. Insights/ActionGroups/Read | Skupina prostředků |
| Microsoft. Resources/Subscriptions/resourceGroups/Read | Skupina prostředků |
| Microsoft. Resources/nasazení/* | Skupina prostředků |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Oprávnění k novému účtu Automation a novému pracovnímu prostoru Log Analytics

Virtuální počítače pro funkci Start/Stop VMs during off-hours můžete povolit pomocí nového účtu Automation a pracovního prostoru Log Analytics. V takovém případě potřebujete oprávnění definovaná v předchozí části i oprávnění definovaná v této části. Vyžadujete také následující role:

- Co-Administrator v předplatném. Tato role se vyžaduje k vytvoření účtu Spustit jako pro Classic, pokud budete spravovat klasické virtuální počítače. [Účty Spustit jako pro Classic](automation-create-standalone-account.md#create-a-classic-run-as-account) se už ve výchozím nastavení nevytvářejí.
- Členství v roli vývojář aplikace [služby Azure AD](../active-directory/roles/permissions-reference.md) . Další informace o konfiguraci účtů spustit jako najdete v tématu [oprávnění ke konfiguraci účtů spustit jako](automation-security-overview.md#permissions).
- Přispěvatel v rámci předplatného nebo následujících oprávnění

| Oprávnění |Obor|
| --- | --- |
| Microsoft. Authorization/Operations/Read | Předplatné|
| Microsoft. Authorization/oprávnění/čtení |Předplatné|
| Microsoft. Authorization/roleAssignments/Read | Předplatné |
| Microsoft.Authorization/roleAssignments/write | Předplatné |
| Microsoft. Authorization/roleAssignments/DELETE | Předplatné || Microsoft. Automation/automationAccounts/Connections/Read | Skupina prostředků |
| Microsoft. Automation/automationAccounts/Certificates/Read | Skupina prostředků |
| Microsoft. Automation/automationAccounts/Write | Skupina prostředků |
| Microsoft. OperationalInsights/pracovní prostory/zápis | Skupina prostředků |

## <a name="components"></a>Komponenty

Funkce Start/Stop VMs during off-hours zahrnuje předkonfigurované Runbooky, plány a integraci s protokoly Azure Monitor. Tyto prvky můžete použít k přizpůsobení spouštění a vypínání virtuálních počítačů tak, aby vyhovovaly vašim obchodním potřebám.

### <a name="runbooks"></a>Runbooky

V následující tabulce jsou uvedeny Runbooky, které funkce nasadí do vašeho účtu Automation. Neprovádějte změny v kódu Runbooku. Místo toho napište vlastní Runbook pro nové funkce.

> [!IMPORTANT]
> Nespouštějte přímo žádnou sadu Runbook s **podřízenou položkou** , která je připojena k jejímu názvu.

Všechny nadřazené Runbooky obsahují `WhatIf` parametr. Při nastavení na hodnotu true podporuje parametr podrobné informace o přesném chování, které sada Runbook provede, když se spustí bez parametru a ověří, jestli jsou cílové správné virtuální počítače. Sada Runbook provede pouze své definované akce, pokud `WhatIf` je parametr nastaven na hodnotu false.

|Runbook | Parametry | Popis|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Volá se z nadřazeného Runbooku. Tato sada Runbook vytváří výstrahy na základě jednotlivých prostředků pro scénář automatického zastavení.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: true nebo false  | Vytvoří nebo aktualizuje pravidla upozornění Azure na virtuálních počítačích v cílovém předplatném nebo ve skupinách prostředků. <br> `VMList` je čárkami oddělený seznam virtuálních počítačů (bez prázdných znaků), například `vm1,vm2,vm3` .<br> `WhatIf` povolí ověřování logiky sady Runbook bez provedení.|
|AutoStop_Disable | Žádné | Zakáže automatické zastavení výstrah a výchozí plán.|
|AutoStop_VM_Child | WebHookData | Volá se z nadřazeného Runbooku. Pravidla výstrah volají tuto sadu Runbook, aby zastavila klasický virtuální počítač.|
|AutoStop_VM_Child_ARM | WebHookData |Volá se z nadřazeného Runbooku. Pravidla výstrah volají tuto sadu Runbook, aby zastavila virtuální počítač.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Akce: spustit nebo zastavit<br> VMList  | Provede akci spustit nebo zastavit ve skupině klasických virtuálních počítačů Cloud Services. |
|ScheduledStartStop_Child | VMName <br> Akce: spustit nebo zastavit <br> ResourceGroupName | Volá se z nadřazeného Runbooku. Provede akci spuštění nebo zastavení pro naplánované zastavení.|
|ScheduledStartStop_Child_Classic | VMName<br> Akce: spustit nebo zastavit<br> ResourceGroupName | Volá se z nadřazeného Runbooku. Provede akci spuštění nebo zastavení pro naplánované zastavení klasických virtuálních počítačů. |
|ScheduledStartStop_Parent | Akce: spustit nebo zastavit <br>VMList <br> WhatIf: true nebo false | Spustí nebo zastaví všechny virtuální počítače v rámci předplatného. Upravte proměnné a spouštějte je `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` jenom u těchto cílových skupin prostředků. Pomocí aktualizace proměnné můžete také vyloučit konkrétní virtuální počítače `External_ExcludeVMNames` .|
|SequencedStartStop_Parent | Akce: spustit nebo zastavit <br> WhatIf: true nebo false<br>VMList| Vytvoří značky s názvem **sequencestart** a **sequencestop** na každém virtuálním počítači, pro který chcete sekvenci aktivity spustit/zastavit. U těchto názvů značek se rozlišují malá a velká písmena. Hodnota značky by měla být seznam kladných celých čísel, například, `1,2,3` která odpovídají pořadí, ve kterém chcete spustit nebo zastavit. <br>**Poznámka**: virtuální počítače musí být v rámci skupin prostředků definovaných v `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` proměnných, a `External_ExcludeVMNames` . Aby se akce projevily, musí mít odpovídající značky.|

### <a name="variables"></a>Proměnné

V následující tabulce jsou uvedeny proměnné vytvořené v účtu Automation. Upravovat pouze proměnné s předponou `External` . Modifikace proměnných s předponou `Internal` způsobuje nežádoucí účinky.

> [!NOTE]
> Omezení pro název virtuálního počítače a skupinu prostředků jsou z velké části výsledkem proměnné velikosti. Viz [variabilní prostředky v Azure Automation](./shared-resources/variables.md).

|Proměnná | Popis|
|---------|------------|
|External_AutoStop_Condition | Podmíněný operátor vyžadovaný pro konfiguraci podmínky před aktivací výstrahy. Přijatelné hodnoty jsou `GreaterThan` , `GreaterThanOrEqual` , `LessThan` a `LessThanOrEqual` .|
|External_AutoStop_Description | Výstraha, která zastaví virtuální počítač, pokud procento procesoru překročí prahovou hodnotu.|
|External_AutoStop_Frequency | Frekvence vyhodnocení pro pravidlo. Tento parametr akceptuje vstup ve formátu TimeSpan. Možné hodnoty jsou 5 minut až 6 hodin. |
|External_AutoStop_MetricName | Název metriky výkonu, pro kterou má být nakonfigurováno pravidlo upozornění Azure.|
|External_AutoStop_Severity | Závažnost výstrahy metriky, která může být v rozsahu od 0 do 4. |
|External_AutoStop_Threshold | Prahová hodnota pro pravidlo upozornění Azure zadané v proměnné `External_AutoStop_MetricName` . Procentuální hodnoty v rozsahu od 1 do 100.|
|External_AutoStop_TimeAggregationOperator | Operátor časové agregace aplikovaný na velikost vybraného okna pro vyhodnocení podmínky. Přijatelné hodnoty jsou `Average` , `Minimum` ,, a `Maximum` `Total` `Last` .|
|External_AutoStop_TimeWindow | Velikost okna, během kterého Azure analyzuje vybrané metriky pro aktivaci výstrahy. Tento parametr akceptuje vstup ve formátu TimeSpan. Možné hodnoty jsou 5 minut až 6 hodin.|
|External_EnableClassicVMs| Hodnota, která určuje, jestli jsou na klasických virtuálních počítačích cílem funkce. Výchozí hodnota je true (pravda). Pro předplatná Azure Cloud Solution Provider (CSP) nastavte tuto proměnnou na false. Klasické virtuální počítače vyžadují [účet Spustit jako pro Classic](automation-create-standalone-account.md#create-a-classic-run-as-account).|
|External_ExcludeVMNames | Čárkami oddělený seznam názvů virtuálních počítačů, které se mají vyloučit, jsou omezené na 140 virtuálních počítačů. Pokud do seznamu přidáte více než 140 virtuálních počítačů, virtuální počítače určené k vyloučení můžou být neúmyslně spuštěny nebo zastaveny.|
|External_Start_ResourceGroupNames | Čárkami oddělený seznam jedné nebo více skupin prostředků, které jsou zaměřeny na počáteční akce.|
|External_Stop_ResourceGroupNames | Čárkami oddělený seznam jedné nebo více skupin prostředků, které jsou zaměřeny na operace zastavení.|
|External_WaitTimeForVMRetrySeconds |Doba čekání v sekundách, po kterou se akce na virtuálních počítačích pro **SequencedStartStop_Parent** Runbook mají provést. Tato proměnná umožňuje sadě Runbook počkat na podřízené operace po zadaný počet sekund, než bude pokračovat další akcí. Maximální doba čekání je 10800 nebo tři hodiny. Výchozí hodnota je 2100 sekund.|
|Internal_AutomationAccountName | Určuje název účtu Automation.|
|Internal_AutoSnooze_ARM_WebhookURI | Identifikátor URI Webhooku volaný pro scénář Autostop pro virtuální počítače|
|Internal_AutoSnooze_WebhookUri | Identifikátor URI Webhooku, který se volá pro scénář Autostop pro klasické virtuální počítače.|
|Internal_AzureSubscriptionId | ID předplatného Azure.|
|Internal_ResourceGroupName | Název skupiny prostředků účtu Automation|

>[!NOTE]
>Pro proměnnou byla `External_WaitTimeForVMRetryInSeconds` Výchozí hodnota aktualizována z 600 na 2100.

V rámci všech scénářů `External_Start_ResourceGroupNames` jsou proměnné,  `External_Stop_ResourceGroupNames` a `External_ExcludeVMNames` nezbytné pro cílení virtuálních počítačů, s výjimkou seznamů pro virtuální počítače oddělené čárkami pro **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent** a **ScheduledStartStop_Parent** sady Runbook. To znamená, že virtuální počítače musí patřit do cílových skupin prostředků pro spuštění a zastavení akcí. Tato logika funguje podobně jako Azure Policy. v takovém případě můžete cílit na předplatné nebo skupinu prostředků a mít akce děděné nově vytvořenými virtuálními počítači. Tento přístup zabraňuje nutnosti udržovat samostatný plán pro každý virtuální počítač a zahájí správu a zastaví se ve škálování.

### <a name="schedules"></a>Plány

V následující tabulce jsou uvedeny všechny výchozí plány vytvořené v účtu Automation. Můžete je upravit nebo vytvořit vlastní plány. Ve výchozím nastavení jsou všechny plány zakázané, kromě plánů **Scheduled_StartVM** a **Scheduled_StopVM** .

Nepovolujte všechny plány, protože se tak můžou vytvořit překrývající se akce plánování. Nejvhodnější je určit, které optimalizace chcete provést, a odpovídajícím způsobem je upravit. Další vysvětlení najdete v ukázkových scénářích v části Přehled.

|Název plánu | Frekvence | Popis|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Každých 8 hodin | Spouští sadu Runbook **AutoStop_CreateAlert_Parent** každých 8 hodin, která zase zastavuje hodnoty založené na virtuálním počítači v `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` proměnných, a `External_ExcludeVMNames` . Případně můžete pomocí parametru zadat čárkami oddělený seznam virtuálních počítačů `VMList` .|
|Scheduled_StopVM | Uživatelem definované, denní | Spustí **ScheduledStopStart_Parent** sadu Runbook s parametrem `Stop` každý den v zadaném čase. Automaticky zastaví všechny virtuální počítače, které splňují pravidla definovaná pomocí variabilních prostředků. Povolte související plán **naplánované – StartVM**.|
|Scheduled_StartVM | Uživatelem definované, denní | Spustí sadu **ScheduledStopStart_Parent** Runbook s hodnotou parametru `Start` každý den v zadaném čase. Automaticky spustí všechny virtuální počítače, které splňují pravidla definovaná pomocí variabilních prostředků. Povolte související plán **naplánované – StopVM**.|
|Sequenced-StopVM | 1:00 dop. (UTC), každý pátek | Spustí **Sequenced_StopStop_Parent** sadu Runbook s hodnotou parametru v `Stop` každém pátek v zadaném čase. Sekvenčně (vzestupně) zastaví všechny virtuální počítače s tagem **SequenceStop** definovanými příslušnými proměnnými. Další informace o hodnotách značek a proměnných prostředků naleznete v tématu [Runbooky](#runbooks). Povolí související plán, **Sequenced-StartVM**.|
|Sequenced-StartVM | 1:00 PM (UTC), každé pondělí | Spustí sadu **SequencedStopStart_Parent** Runbook s hodnotou parametru `Start` každé pondělí v zadanou dobu. Sekvenčně (sestupně) spustí všechny virtuální počítače se značkou **SequenceStart** definovanou příslušnými proměnnými. Další informace o hodnotách značek a proměnných prostředků naleznete v tématu [Runbooky](#runbooks). Povolí související plán, **Sequenced-StopVM**.

## <a name="use-the-feature-with-classic-vms"></a>Použití funkce u klasických virtuálních počítačů

Pokud používáte funkci Start/Stop VMs during off-hours pro klasické virtuální počítače, Automation zpracuje všechny virtuální počítače postupně na cloudovou službu. Virtuální počítače jsou pořád zpracovávány paralelně napříč různými Cloud Services. 

Pro použití funkce s klasickými virtuálními počítači potřebujete účet Spustit jako pro Classic, který se ve výchozím nastavení nevytvoří. Pokyny k vytvoření účtu Spustit jako pro Azure Classic najdete v tématu [Vytvoření účtu Spustit jako pro Azure Classic](automation-create-standalone-account.md#create-a-classic-run-as-account).

Pokud máte více než 20 virtuálních počítačů na cloudovou službu, tady je několik doporučení:

* Vytvořte více plánů s nadřazeným runbookm **ScheduledStartStop_Parent** a určete 20 virtuálních počítačů na jeden plán.
* Ve vlastnostech plánu použijte `VMList` parametr a zadejte názvy virtuálních počítačů jako seznam oddělený čárkami (žádné prázdné znaky).

V opačném případě, pokud se úloha automatizace pro tuto funkci spustí déle než tři hodiny, dočasně se uvolní nebo zastaví na základě [spravedlivého](automation-runbook-execution.md#fair-share) limitu sdílení.

Předplatné služby Azure CSP podporuje pouze model Azure Resource Manager. V programu nejsou k dispozici služby, které nejsou Azure Resource Manager. Když se spustí funkce Start/Stop VMs during off-hours, může docházet k chybám, protože mají rutiny pro správu klasických prostředků. Další informace o CSP najdete v tématu [dostupné služby v předplatných CSP](/azure/cloud-solution-provider/overview/azure-csp-available-services). Pokud používáte předplatné CSP, měli byste nastavit [External_EnableClassicVMs](#variables) proměnnou na hodnotu false po nasazení.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-the-feature"></a>Zobrazit funkci

Pro přístup k povolené funkci použijte jeden z následujících mechanismů:

* V účtu Automation vyberte **Spustit/zastavit virtuální počítač** v části **související prostředky**. Na stránce Spustit/zastavit virtuální počítač vyberte **Spravovat řešení** v části **Spravovat spustit/zastavit řešení virtuálních počítačů**.

* Přejděte do pracovního prostoru Log Analytics, který je propojený s vaším účtem Automation. Po výběru pracovního prostoru zvolte **řešení** v levém podokně. Na stránce řešení vyberte v seznamu **Start-Stop-VM [pracovní prostor]** .  

Výběrem této funkce se zobrazí stránka **Start-Stop-VM [pracovní prostor]** . Tady si můžete prohlédnout důležité podrobnosti, například informace na dlaždici **StartStopVM** . Stejně jako v pracovním prostoru Log Analytics tato dlaždice zobrazuje počet a grafické znázornění úloh sady Runbook pro funkci, která byla spuštěna a úspěšně skončila.

![Stránka Update Management Automation](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Kliknutím na dlaždici prstence můžete provádět další analýzu záznamů úloh. Řídicí panel zobrazuje historii úloh a předdefinované vyhledávací dotazy protokolů. Přepněte na rozšířený portál Log Analytics, abyste mohli vyhledávat na základě vyhledávacích dotazů.

## <a name="update-the-feature"></a>Aktualizace funkce

Pokud jste nasadili předchozí verzi Start/Stop VMs during off-hours, před nasazením aktualizované verze ji odstraňte z účtu. Použijte postup [odebrání této funkce](automation-solution-vm-management-remove.md#delete-the-feature) a pak postupujte podle pokynů k [jejímu povolení](automation-solution-vm-management-enable.md).

## <a name="next-steps"></a>Další kroky

Pokud chcete funkci na virtuálních počítačích ve vašem prostředí povolit, přečtěte si téma [povolení Start/Stop VMS during off-hours](automation-solution-vm-management-enable.md).