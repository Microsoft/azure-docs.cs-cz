---
title: Spuštění/zastavení virtuálních počítačů během nepracovních řešení
description: Toto řešení pro správu virtuálních počítačů spustí a zastaví vaše virtuální počítače Azure na základě plánu a proaktivně monitoruje z protokolů Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 04/28/2020
ms.topic: conceptual
ms.openlocfilehash: 760c56ad6179a7bf94f19e004e2fbbece3908198
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683508"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Spuštění/zastavení virtuálních počítačů v řešení mimo špičku v Azure Automation

**Spuštění nebo zastavení virtuálních počítačů v době mimo** špičku spouští nebo zastavuje vaše virtuální počítače Azure. Spouští nebo zastavuje počítače na uživatelem definovaných plánech, poskytuje přehledy prostřednictvím protokolů Azure Monitor a odesílá volitelné e-maily pomocí [skupin akcí](../azure-monitor/platform/action-groups.md). Řešení podporuje pro většinu scénářů Azure Resource Manager i klasické virtuální počítače. 

Toto řešení spustí virtuální počítače pomocí rutiny [Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0) . Pro zastavování virtuálních počítačů používá [stop-AzureRmVM](https://docs.microsoft.com/powershell/module/AzureRM.Compute/Stop-AzureRmVM?view=azurermps-6.13.0) .

> [!NOTE]
> **Virtuální počítače spustit/zastavit v době mimo špičku** byly aktualizovány tak, aby podporovaly nejnovější verze modulů Azure, které jsou k dispozici. Aktualizovaná verze tohoto řešení, která je dostupná na webu Marketplace, nepodporuje AzureRM moduly, protože jsme migrovali z AzureRM na az Modules.

Řešení poskytuje decentralizovanou možnost automatizace s nízkými náklady pro uživatele, kteří chtějí optimalizovat náklady na virtuální počítače. V tomto řešení můžete:

- [Naplánujte spouštění a zastavování virtuálních počítačů](automation-solution-vm-management-config.md#schedule).
- Naplánování spouštění a zastavování virtuálních počítačů ve vzestupném pořadí [pomocí značek Azure](automation-solution-vm-management-config.md#tags) (není podporováno pro klasické virtuální počítače).
- Zastavovat virtuální počítače na základě [nízkého využití procesoru](automation-solution-vm-management-config.md#cpuutil).

Toto jsou omezení s aktuálním řešením:

- Spravuje virtuální počítače v jakékoli oblasti, ale dá se použít jenom ve stejném předplatném jako váš účet Azure Automation.
- Je k dispozici v Azure a Azure Government do jakékoli oblasti, která podporuje pracovní prostor Log Analytics, Azure Automation účet a výstrahy. Azure Government oblasti momentálně nepodporují funkce e-mailu.

## <a name="solution-prerequisites"></a>Požadavky na řešení

Runbooky pro toto řešení fungují s [účtem spustit jako pro Azure](automation-create-runas-account.md). Účet Spustit jako je upřednostňovanou metodou ověřování, protože místo hesla, jehož platnost může vypršet nebo často se mění, používá ověřování certifikátů.

Pro **spuštění nebo zastavení virtuálních počítačů v době mimo** špičku doporučujeme použít samostatný účet Automation. Verze modulů Azure se často upgradují a jejich parametry se můžou změnit. Řešení není upgradované na stejném tempo a nemusí fungovat s novějšími verzemi rutin, které používá. Doporučuje se před importem na účet služby Automation v produkčním účtu služby test Automation otestovat aktualizace modulu.

## <a name="solution-permissions"></a>Oprávnění řešení

K nasazení **virtuálních počítačů spustit/zastavit během nepracovních** řešení je potřeba mít určitá oprávnění. Tato oprávnění se liší, pokud řešení používá předem vytvořený účet Automation a Log Analytics pracovní prostor z oprávnění potřebných, pokud řešení vytvoří během nasazování nový účet a pracovní prostor. 

Pokud jste přispěvatelem předplatného a globálním správcem ve vašem tenantovi Azure Active Directory, nemusíte oprávnění konfigurovat. Pokud tato oprávnění nemáte nebo potřebujete nakonfigurovat vlastní roli, ujistěte se, že máte oprávnění popsaná níže.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Oprávnění pro již existující účet služby Automation a pracovní prostor Log Analytics

Pokud chcete nasadit **virtuální počítače spustit/zastavit v době mimo** špičku na existující účet Automation a Log Analytics pracovní prostor, uživatel nasazení řešení vyžaduje následující oprávnění pro rozsah skupiny prostředků. Další informace o rolích najdete v tématu [vlastní role pro prostředky Azure](../role-based-access-control/custom-roles.md).

| Oprávnění | Rozsah|
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

Můžete nasadit **virtuální počítače spustit/zastavit v době mimo špičku** do nového účtu Automation a Log Analytics pracovního prostoru. V takovém případě uživatel, který řešení nasazuje, potřebuje oprávnění definovaná v předchozí části i oprávnění definovaná v této části. 

Uživatel, který řešení nasazuje, potřebuje následující role:

- Spolusprávce předplatného. Tato role se vyžaduje k vytvoření účtu Spustit jako pro Classic, pokud budete spravovat klasické virtuální počítače. [Účty Spustit jako pro Classic](automation-create-standalone-account.md#create-a-classic-run-as-account) se už ve výchozím nastavení nevytvářejí.
- Členství v roli vývojář aplikace [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) . Další informace o konfiguraci účtů spustit jako najdete v tématu [oprávnění ke konfiguraci účtů spustit jako](manage-runas-account.md#permissions).
- Přispěvatel v rámci předplatného nebo následujících oprávnění

| Oprávnění |Rozsah|
| --- | --- |
| Microsoft. Authorization/Operations/Read | Předplatné|
| Microsoft. Authorization/oprávnění/čtení |Předplatné|
| Microsoft. Authorization/roleAssignments/Read | Předplatné |
| Microsoft.Authorization/roleAssignments/write | Předplatné |
| Microsoft. Authorization/roleAssignments/DELETE | Předplatné |
| Microsoft. Automation/automationAccounts/Connections/Read | Skupina prostředků |
| Microsoft. Automation/automationAccounts/Certificates/Read | Skupina prostředků |
| Microsoft. Automation/automationAccounts/Write | Skupina prostředků |
| Microsoft. OperationalInsights/pracovní prostory/zápis | Skupina prostředků |

## <a name="solution-components"></a>Součásti řešení

**Spuštění/zastavení virtuálních počítačů v době mimo špičku** zahrnuje předkonfigurované Runbooky, plány a integraci s protokoly Azure monitor. Tyto prvky můžete použít k přizpůsobení spouštění a vypínání virtuálních počítačů tak, aby vyhovovaly vašim obchodním potřebám.

### <a name="runbooks"></a>Runbooky

V následující tabulce jsou uvedeny Runbooky, které se řešení nasadí do vašeho účtu Automation. Neprovádějte změny v kódu Runbooku. Místo toho napište vlastní Runbook pro nové funkce.

> [!IMPORTANT]
> Nespouštějte přímo žádnou sadu Runbook s **podřízenou položkou** , která je připojena k jejímu názvu.

Všechny nadřazené Runbooky obsahují `WhatIf` parametr. Při nastavení na hodnotu true podporuje parametr podrobné informace o přesném chování, které sada Runbook provede, když se spustí bez parametru a ověří, jestli jsou cílové správné virtuální počítače. Sada Runbook provede pouze své definované akce, pokud `WhatIf` je parametr nastaven na hodnotu false.

|Runbook | Parametry | Popis|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Volá se z nadřazeného Runbooku. Tato sada Runbook vytváří výstrahy na základě jednotlivých prostředků pro scénář automatického zastavení.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: true nebo false  | Vytvoří nebo aktualizuje pravidla upozornění Azure na virtuálních počítačích v cílovém předplatném nebo ve skupinách prostředků. <br> `VMList`je čárkami oddělený seznam virtuálních počítačů. Například, `vm1, vm2, vm3`.<br> `WhatIf`povolí ověřování logiky sady Runbook bez provedení.|
|AutoStop_Disable | Žádné | Zakáže automatické zastavení výstrah a výchozí plán.|
|AutoStop_VM_Child | WebHookData | Volá se z nadřazeného Runbooku. Pravidla výstrah volají tuto sadu Runbook, aby zastavila klasický virtuální počítač.|
|AutoStop_VM_Child_ARM | WebHookData |Volá se z nadřazeného Runbooku. Pravidla výstrah volají tuto sadu Runbook, aby zastavila virtuální počítač.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Akce: spustit nebo zastavit<br> VMList  | Provede akci spustit nebo zastavit ve skupině klasických virtuálních počítačů Cloud Services. |
|ScheduledStartStop_Child | VMName <br> Akce: spustit nebo zastavit <br> ResourceGroupName | Volá se z nadřazeného Runbooku. Provede akci spuštění nebo zastavení pro naplánované zastavení.|
|ScheduledStartStop_Child_Classic | VMName<br> Akce: spustit nebo zastavit<br> ResourceGroupName | Volá se z nadřazeného Runbooku. Provede akci spuštění nebo zastavení pro naplánované zastavení klasických virtuálních počítačů. |
|ScheduledStartStop_Parent | Akce: spustit nebo zastavit <br>VMList <br> WhatIf: true nebo false | Spustí nebo zastaví všechny virtuální počítače v rámci předplatného. Upravte proměnné a spouštějte je `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` jenom u těchto cílových skupin prostředků. Pomocí aktualizace proměnné můžete také vyloučit konkrétní virtuální počítače `External_ExcludeVMNames` .|
|SequencedStartStop_Parent | Akce: spustit nebo zastavit <br> WhatIf: true nebo false<br>VMList| Vytvoří značky s názvem **sequencestart** a **sequencestop** na každém virtuálním počítači, pro který chcete sekvenci aktivity spustit/zastavit. U těchto názvů značek se rozlišují malá a velká písmena. Hodnota značky by měla být kladné celé číslo (1, 2, 3), které odpovídá pořadí, ve kterém chcete spustit nebo zastavit. <br>**Poznámka**: virtuální počítače musí být v rámci skupin prostředků definovaných v `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` proměnných, a `External_ExcludeVMNames` . Aby se akce projevily, musí mít odpovídající značky.|

### <a name="variables"></a>Proměnné

V následující tabulce jsou uvedeny proměnné vytvořené v účtu Automation. Upravovat pouze proměnné s předponou `External` . Modifikace proměnných s předponou `Internal` způsobuje nežádoucí účinky.

> [!NOTE]
> Omezení pro název virtuálního počítače a skupinu prostředků jsou z velké části výsledkem proměnné velikosti. Viz [variabilní prostředky v Azure Automation](https://docs.microsoft.com/azure/automation/shared-resources/variables).

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
|External_EnableClassicVMs| Hodnota, která určuje, jestli je na klasických virtuálních počítačích cíleno řešení. Výchozí hodnota je true (pravda). Pro předplatná Azure Cloud Solution Provider (CSP) nastavte tuto proměnnou na false. Klasické virtuální počítače vyžadují [účet Spustit jako pro Classic](automation-create-standalone-account.md#create-a-classic-run-as-account).|
|External_ExcludeVMNames | Čárkami oddělený seznam názvů virtuálních počítačů, které se mají vyloučit, jsou omezené na 140 virtuálních počítačů. Pokud do seznamu přidáte více než 140 virtuálních počítačů, virtuální počítače, které mají být vyloučeny, mohou být neúmyslně spuštěny nebo zastaveny.|
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

V rámci všech scénářů `External_Start_ResourceGroupNames` jsou proměnné, `External_Stop_ResourceGroupNames` a `External_ExcludeVMNames` nezbytné pro cílení virtuálních počítačů, s výjimkou seznamů pro virtuální počítače oddělené čárkami pro **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**a **ScheduledStartStop_Parent** sady Runbook. To znamená, že virtuální počítače musí patřit do cílových skupin prostředků pro spuštění a zastavení akcí. Tato logika funguje podobně jako Azure Policy. v takovém případě můžete cílit na předplatné nebo skupinu prostředků a mít akce děděné nově vytvořenými virtuálními počítači. Tento přístup zabraňuje nutnosti udržovat samostatný plán pro každý virtuální počítač a zahájí správu a zastaví se ve škálování.

### <a name="schedules"></a>Plány

V následující tabulce jsou uvedeny všechny výchozí plány vytvořené v účtu Automation.Můžete je upravit nebo vytvořit vlastní plány.Ve výchozím nastavení jsou všechny plány zakázané, kromě plánů **Scheduled_StartVM** a **Scheduled_StopVM** .

Nepovolujte všechny plány, protože se tak můžou vytvořit překrývající se akce plánování. Nejvhodnější je určit, které optimalizace chcete provést, a odpovídajícím způsobem je upravit. Další vysvětlení najdete v ukázkových scénářích v části Přehled.

|Název plánu | Frekvence | Popis|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Každých 8 hodin | Spouští sadu Runbook **AutoStop_CreateAlert_Parent** každých 8 hodin, která zase zastavuje hodnoty založené na virtuálním počítači v `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` proměnných, a `External_ExcludeVMNames` . Případně můžete pomocí parametru zadat čárkami oddělený seznam virtuálních počítačů `VMList` .|
|Scheduled_StopVM | Uživatelem definované, denní | Spustí **ScheduledStopStart_Parent** sadu Runbook s parametrem `Stop` každý den v zadaném čase.Automaticky zastaví všechny virtuální počítače, které splňují pravidla definovaná pomocí variabilních prostředků.Povolte související plán **naplánované – StartVM**.|
|Scheduled_StartVM | Uživatelem definované, denní | Spustí sadu **ScheduledStopStart_Parent** Runbook s hodnotou parametru `Start` každý den v zadaném čase. Automaticky spustí všechny virtuální počítače, které splňují pravidla definovaná pomocí variabilních prostředků.Povolte související plán **naplánované – StopVM**.|
|Sequenced – StopVM | 1:00 dop. (UTC), každý pátek | Spustí **Sequenced_StopStop_Parent** sadu Runbook s hodnotou parametru v `Stop` každém pátek v zadaném čase.Sekvenčně (vzestupně) zastaví všechny virtuální počítače s tagem **SequenceStop** definovanými příslušnými proměnnými. Další informace o hodnotách značek a proměnných prostředků naleznete v tématu [Runbooky](#runbooks).Povolí související plán, **Sequenced-StartVM**.|
|Sequenced – StartVM | 1:00 PM (UTC), každé pondělí | Spustí sadu **SequencedStopStart_Parent** Runbook s hodnotou parametru `Start` každé pondělí v zadanou dobu. Sekvenčně (sestupně) spustí všechny virtuální počítače se značkou **SequenceStart** definovanou příslušnými proměnnými. Další informace o hodnotách značek a proměnných prostředků naleznete v tématu [Runbooky](#runbooks). Povolí související plán, **Sequenced-StopVM**.

## <a name="use-of-the-solution-with-classic-vms"></a>Použití řešení s klasickými virtuálními počítači

Pokud používáte **virtuální počítače spustit/zastavit** v době mimo špičku u klasických virtuálních počítačů, pak automatizace zpracuje všechny virtuální počítače postupně na cloudovou službu. Virtuální počítače jsou pořád zpracovávány paralelně napříč různými Cloud Services. 

Pro použití řešení s klasickými virtuálními počítači potřebujete účet Spustit jako pro Classic, který se ve výchozím nastavení nevytvoří. Pokyny k vytvoření účtu Spustit jako pro Azure Classic najdete v tématu [Vytvoření účtu Spustit jako pro Azure Classic](automation-create-standalone-account.md#create-a-classic-run-as-account).

Pokud máte více než 20 virtuálních počítačů na cloudovou službu, tady je několik doporučení:

* Vytvořte více plánů s nadřazeným runbookm **ScheduledStartStop_Parent** a určete 20 virtuálních počítačů na jeden plán. 
* Ve vlastnostech plánu použijte `VMList` parametr a zadejte názvy virtuálních počítačů jako seznam oddělený čárkami. 

Jinak, pokud se úloha automatizace pro toto řešení spouští déle než tři hodiny, dočasně se uvolní nebo zastaví na základě [spravedlivého](automation-runbook-execution.md#fair-share) limitu sdílení.

Předplatné služby Azure CSP podporuje pouze model Azure Resource Manager. V programu nejsou k dispozici služby, které nejsou Azure Resource Manager. Když se spouští **nebo zastavují virtuální počítače v době mimo** špičku, může docházet k chybám, protože mají rutiny pro správu klasických prostředků. Další informace o CSP najdete v tématu [dostupné služby v předplatných CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Pokud používáte předplatné CSP, měli byste nastavit [External_EnableClassicVMs](#variables) proměnnou na hodnotu false po nasazení.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-solution"></a>Povolení řešení

Pokud chcete začít používat řešení, postupujte podle kroků v části [Povolení řešení spuštění/zastavení virtuálních počítačů](automation-solution-vm-management-enable.md).

## <a name="view-the-solution"></a>Zobrazení řešení

Pro přístup k řešení po jeho povolení použijte jeden z následujících mechanismů:

* V účtu Automation vyberte **Spustit/zastavit virtuální počítač** v části **související prostředky**. Na stránce Spustit/zastavit virtuální počítač vyberte **Spravovat řešení** na pravé straně stránky v části **Spravovat spuštění/zastavení řešení virtuálních počítačů**.

* Přejděte do pracovního prostoru Log Analytics, který je propojený s vaším účtem Automation. Po výběru pracovního prostoru zvolte **řešení** v levém podokně. Na stránce řešení v seznamu vyberte řešení **Start-Stop-VM [pracovní prostor]** .  

Výběrem řešení se zobrazí stránka řešení **Start-Stop-VM [pracovní prostor]** . Tady si můžete prohlédnout důležité podrobnosti, například informace na dlaždici **StartStopVM** . Stejně jako ve vašem pracovním prostoru Log Analytics tato dlaždice zobrazuje počet a grafické znázornění úloh sady Runbook pro řešení, které bylo spuštěno a úspěšně skončilo.

![Stránka řešení Update Management pro automatizaci](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Kliknutím na dlaždici prstence můžete provádět další analýzu záznamů úloh. Řídicí panel řešení zobrazuje historii úloh a předdefinované vyhledávací dotazy protokolů. Přepněte na rozšířený portál Log Analytics, abyste mohli vyhledávat na základě vyhledávacích dotazů.

## <a name="update-the-feature"></a>Aktualizace funkce

Pokud jste nasadili předchozí verzi tohoto řešení, před nasazením aktualizované verze ho odstraňte z účtu. Postupujte podle kroků pro [Odebrání řešení](#remove-the-feature) a pak postupujte podle kroků k [nasazení řešení](automation-solution-vm-management-enable.md).

## <a name="remove-the-feature"></a>Odebrání funkce

Pokud už řešení použít nechcete, můžete ho odstranit z účtu Automation. Odstraněním řešení se odstraní jenom Runbooky. Neodstraní plány nebo proměnné, které byly vytvořeny při přidání řešení. Tyto prostředky odeberte ručně, pokud je nepoužíváte s jinými sadami Runbook.

Odstranění řešení:

1. V účtu Automation vyberte v části **související prostředky**možnost **propojený pracovní prostor** .

2. Vyberte **Přejít k pracovnímu prostoru**.

3. V části **Obecné**klikněte na **řešení** . 

4. Na stránce řešení vyberte řešení **Start-Stop-VM [pracovní prostor]**. 

5. Na stránce **VMManagementSolution [pracovní prostor]** v nabídce vyberte **Odstranit** .<br><br> ![Odstranit řešení pro správu virtuálních počítačů](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. V okně **Odstranit řešení** potvrďte, že chcete odstranit řešení.

7. I když jsou informace ověřené a řešení se odstraní, můžete sledovat průběh v části **oznámení**, kterou zvolíte v nabídce. Až se spustí proces odebrání řešení, vrátíte se na stránku řešení.

Účet služby Automation a Log Analytics pracovní prostor se v rámci tohoto procesu neodstraní. Pokud nechcete zachovat Log Analytics pracovní prostor, musíte ho ručně odstranit z Azure Portal:

1. Vyhledejte a vyberte **Log Analytics pracovní prostory**.

2. Na stránce Log Analytics pracovní prostor vyberte pracovní prostor.

3. V nabídce na stránce nastavení pracovního prostoru vyberte **Odstranit** .

4. Pokud nechcete zachovat [komponenty řešení](#solution-components)Azure Automation účtu, můžete je ručně odstranit.

## <a name="next-steps"></a>Další kroky

[Povolte](automation-solution-vm-management-enable.md) **spouštění a zastavování virtuálních počítačů v době mimo špičku** pro vaše virtuální počítače Azure.
