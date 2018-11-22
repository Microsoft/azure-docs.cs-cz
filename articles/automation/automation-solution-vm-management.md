---
title: Spuštění/zastavení virtuálních počítačů v době mimo špičku řešení
description: Toto řešení správy virtuálního počítače spustí a zastaví virtuální počítače Azure Resource Managerem podle časového plánu a aktivně monitoruje od Log Analytics.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 10/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fa1fa65315f38d0ce2900b738b70ca3718b0c00e
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52285097"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Spuštění/zastavení virtuálních počítačů v době mimo špičku řešení ve službě Azure Automation

Spuštění/zastavení virtuálních počítačů mimo špičku řešení spustí a zastaví virtuální počítače Azure na uživatelské plány, poskytuje přehledy prostřednictvím Azure Log Analytics a posílání e-mailů volitelné [skupiny akcí](../monitoring-and-diagnostics/monitoring-action-groups.md). Azure Resource Manager a klasické virtuální počítače podporuje pro většinu scénářů.

Toto řešení poskytuje možnost automatizace decentralizované s nízkými náklady pro uživatele, kteří chtějí optimalizovat náklady na jejich virtuální počítač. S tímto řešením můžete:

- Plánování virtuálních počítačů pro spuštění a zastavení.
- Plánování virtuálních počítačů pro spuštění a zastavení ve vzestupném pořadí s použitím značky Azure (není podporováno pro klasické virtuální počítače).
- AUTOSTOP virtuálních počítačů založených na nízké využití procesoru.

Toto jsou omezení do aktuálního řešení:

- Toto řešení spravuje virtuální počítače v libovolné oblasti, ale jde použít jenom ve stejném předplatném jako účet Azure Automation.
- Toto řešení je k dispozici v Azure a AzureGov do libovolné oblasti, která podporuje výstrahy, účet Azure Automation a pracovního prostoru Log Analytics. Oblasti AzureGov aktuálně nepodporují e-mailové funkce.

> [!NOTE]
> Pokud používáte řešení pro klasické virtuální počítače, pak všechny virtuální počítače se budou zpracovávat postupně na jednu cloudovou službu. Zpracování paralelních úloh je stále podporována napříč různými cloudovými službami.
>
> Předplatná Azure Cloud Solution Provider (CSP pro Azure) podporují pouze model Azure Resource Manageru, services – Azure Resource Manageru nejsou k dispozici v programu. Při spuštění/zastavení řešení běží se může zobrazit chyby jako obsahuje rutiny pro správu klasických prostředků. Další informace o CSP najdete v tématu [dostupných služeb v předplatných CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments).

## <a name="prerequisites"></a>Požadavky

Sady runbook pro toto řešení pracovat [účet Spustit jako pro Azure](automation-create-runas-account.md). Účet Spustit jako je upřednostňovanou metodou ověřování, protože používá ověřování certifikátem místo hesla, která může být vypršení platnosti nebo se často měnit.

## <a name="deploy-the-solution"></a>Nasazení řešení

Proveďte následující kroky pro přidání spouštění/zastavování virtuálních počítačů špičku do vašeho účtu Automation a pak nakonfigurujte proměnné k přizpůsobení řešení.

1. Účet Automation vyberte **spuštění/zastavení virtuálních počítačů** pod **související prostředky**. Z tohoto místa můžete kliknout na **Další informace o a povolte řešení**. Pokud už máte nasazené řešení spuštění/zastavení virtuálních počítačů, které můžete vybrat kliknutím **spravovat řešení** a hledání v seznamu.

   ![Povolit v účtu automation](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Můžete ji vytvořit také z libovolného místa na webu Azure Portal, klikněte na **vytvořit prostředek**. Na stránce Marketplace zadejte klíčové slovo **Start** nebo **operací Spustit/Zastavit**. Seznam se průběžně filtruje podle zadávaného textu. Alternativně můžete zadejte jeden nebo více klíčových slov z celý název řešení a stiskněte klávesu Enter. Vyberte **spuštění/zastavení virtuálních počítačů mimo špičku** ve výsledcích hledání.
2. V **spuštění/zastavení virtuálních počítačů mimo špičku** stránky pro vybrané řešení zkontrolujte souhrnné informace a klikněte na **vytvořit**.

   ![portál Azure](media/automation-solution-vm-management/azure-portal-01.png)

3. **Přidat řešení** se zobrazí stránka. Zobrazí se výzva ke konfigurování řešení, než je můžete naimportovat do svého předplatného služby Automation.

   ![Stránka Přidat řešení správy virtuálních počítačů](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. Na **přidat řešení** stránce **pracovní prostor**. Vyberte pracovní prostor Log Analytics, který je propojený s předplatným Azure, který obsahuje příslušný účet Automation. Pokud nemáte pracovní prostor, vyberte **vytvořit nový pracovní prostor**. Na **pracovní prostor Log Analytics** stránce, proveďte následující kroky:
   - Zadejte název pro nový **pracovní prostor Log Analytics**.
   - Vyberte **předplatné** k propojení, výběrem z rozevíracího seznamu, pokud výchozí vybrané není vhodné.
   - Pro **skupiny prostředků**, můžete vytvořit novou skupinu prostředků nebo vyberte existující.
   - Vyberte **Umístění**. V současné době jsou k dispozici pouze umístění **Austrálie – jihovýchod**, **Kanada – střed**, **střed Indie**, **USA – východ**, **Japonsko – východ**, **jihovýchodní Asie**, **Velká Británie – jih**, a **západní Evropa**.
   - Vyberte možnost u položky **Cenová úroveň**. Zvolte **Per GB (Standalone)** možnost. Log Analytics se aktualizovala [ceny](https://azure.microsoft.com/pricing/details/log-analytics/) a úroveň Per GB je jedinou možností.

5. Po zadání požadovaných informací v **pracovní prostor Log Analytics** klikněte na **vytvořit**. Můžete sledovat jeho průběh **oznámení** z nabídky, která se vrátíte na **přidat řešení** stránce až budete hotovi.
6. Na **přidat řešení** stránce **účtu Automation**. Pokud vytváříte nový pracovní prostor Log Analytics, můžete vytvořit nový účet Automation, který se má přidružit ho nebo vyberte existující účet Automation, který není již propojený s pracovním prostorem Log Analytics. Vyberte existující účet Automation, nebo klikněte na tlačítko **vytvořit účet Automation**a na **přidat účet Automation** stránky, zadejte následující informace:
   - Do pole **Název** zadejte název účtu služby Automation.

    Všechny ostatní možnosti se vyplní automaticky podle vybrané pracovní prostor Log Analytics. Tyto možnosti nelze upravovat. Účet Spustit v Azure jako představuje výchozí metodu ověřování pro runbooky obsažené v tomto řešení. Po kliknutí na **OK**, se ověří možnosti konfigurace a vytvoření účtu Automation. Průběh zpracování můžete sledovat prostřednictvím možnosti nabídky **Oznámení**.

7. Nakonec v **přidat řešení** stránce **konfigurace**. **Parametry** se zobrazí stránka.

   ![Stránka parametry pro řešení](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Sem budete vyzváni k:
   - Zadejte **cílové názvy ResourceGroup**. Tyto hodnoty jsou názvy skupin prostředků obsahujících virtuální počítače, které být spravovány tímto řešením. Můžete zadat více než jeden název a jednotlivé oddělte čárkou (hodnoty nejsou malá a velká písmena). Je podporováno použití zástupného znaku, pokud jsou cílem virtuální počítače ve všech skupinách prostředků v rámci předplatného. Tato hodnota bude uložena v **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupNames** proměnné.
   - Zadejte **seznamu vyloučení virtuálních počítačů (řetězec)**. Tato hodnota je název jednoho nebo více virtuálních počítačů z cílová skupina prostředků. Můžete zadat více než jeden název a jednotlivé oddělte čárkou (hodnoty nejsou malá a velká písmena). Je podporováno použití zástupného znaku. Tato hodnota bude uložena v **External_ExcludeVMNames** proměnné.
   - Vyberte **plán**. Tato hodnota je o opakované datum a čas spouštění a zastavování virtuálních počítačů v cílové skupiny prostředků. Ve výchozím nastavení je nakonfigurovaný plán po dobu 30 minut od této chvíle. Vybrat jinou oblast, není k dispozici. Konfigurace plánu na konkrétní časové pásmo po dokončení konfigurace řešení, najdete v článku [Úprava plánů spouštění a vypínání](#modify-the-startup-and-shutdown-schedules).
   - Pro příjem **e-mailová oznámení** ze skupiny akcí, přijměte výchozí hodnotu **Ano** a zadejte platnou e-mailovou adresu. Pokud vyberete **ne** , ale později rozhodnete, že chcete dostávat e-mailová oznámení, můžete aktualizovat [skupiny akcí](../monitoring-and-diagnostics/monitoring-action-groups.md) , který je vytvořen s platnou e-mailové adresy oddělené čárkou. Je také potřeba povolit následující pravidla upozornění:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > Výchozí hodnota pro **názvy cílové skupiny prostředků** je **&ast;**. To cílí na všechny virtuální počítače v rámci předplatného. Pokud nechcete, aby řešení cílit na všechny virtuální počítače v rámci vašeho předplatného, tato hodnota se musí aktualizovat tak, aby seznam názvy skupin prostředků před povolením plány.

8. Po dokončení konfigurace počátečních nastavení vyžadovaných pro příslušné řešení, klikněte na tlačítko **OK** zavřete **parametry** stránku a vybrat **vytvořit**. Po všechna nastavení se ověří, toto řešení nasadí do vašeho předplatného. Tento proces může trvat několik sekund na dokončení, a můžete sledovat jeho průběh **oznámení** z nabídky.

## <a name="scenarios"></a>Scénáře

Řešení obsahuje tři různé scénáře. Tyto scénáře jsou:

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>Scénář 1: Spouštění/zastavování virtuálních počítačů podle plánu

Tento scénář je výchozí konfigurace, při prvním nasazení řešení. Například můžete nakonfigurovat ji zastavit všechny virtuální počítače napříč předplatným opustit pracovní večer, a spusťte ráno, až budete zpět v kanceláři. Když konfigurujete plány **plánovaný-StartVM** a **naplánované StopVM** během nasazení, spuštění a zastavení cílových virtuálních počítačů. Konfigurace tohohle řešení pro právě zastavením virtuálních počítačů se podporuje, najdete v článku [Úprava plánů spouštění a vypínání plánů](#modify-the-startup-and-shutdown-schedules) se naučíte nakonfigurovat vlastní plán.

> [!NOTE]
> Časové pásmo je aktuálního časového pásma, pokud nakonfigurujete plán parametru času. Ale je ukládat ve formátu UTC ve službě Azure Automation. Není potřeba dělat žádné převodu časového pásma, protože tento problém řeší během nasazení.

Můžete určit, které virtuální počítače jsou v oboru konfigurací následující proměnné: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, a **External_ ExcludeVMNames**.

Můžete povolit cílení na reagovat na předplatné a skupinu prostředků nebo cílení na konkrétní seznam virtuálních počítačů, ale ne obojí.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Cíl akce spouštění a zastavování proti předplatném a skupině prostředků

1. Konfigurace **External_Stop_ResourceGroupNames** a **External_ExcludeVMNames** proměnné k určení cílových virtuálních počítačů.
1. Povolit a aktualizovat **plánovaný-StartVM** a **naplánované StopVM** plány.
1. Spustit **ScheduledStartStop_Parent** runbooku pomocí akce parametrem nastaveným na **start** a WHATIF parametrem nastaveným na **True** náhled změn.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Akce spouštění a zastavování cílit, seznam virtuálních počítačů

1. Spustit **ScheduledStartStop_Parent** runbooku pomocí akce parametrem nastaveným na **start**, přidejte čárkou oddělený seznam virtuálních počítačů v *seznamů VMList* parametr a pak nastavte Parametr WHATIF **True**. Náhled změn.
1. Konfigurace **External_ExcludeVMNames** parametr s čárkou oddělený seznam virtuálních počítačů (VM1, VM2 VM3).
1. Tento scénář nerespektuje **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupnames** proměnné. V tomto scénáři je potřeba vytvořit vlastní plán Automation. Podrobnosti najdete v tématu [plánování runbooku ve službě Azure Automation](../automation/automation-schedules.md).

> [!NOTE]
> Hodnota pro **názvy cílové skupiny prostředků** se ukládá jako hodnota pro obě **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupNames**. Pro další podrobností můžete upravit každé z těchto proměnných pro cílení různých skupin prostředků. Pro akci spuštění, použijte **External_Start_ResourceGroupNames**a pro akci zastavit, použijte **External_Stop_ResourceGroupNames**. Virtuální počítače jsou automaticky přidány do spuštění a zastavení plány.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>Scénář 2: Spuštění/zastavení virtuálních počítačů v pořadí pomocí značek

V prostředí, které obsahuje dvě nebo více součástí na podporu úlohách distribuovaných mezi několika virtuálních počítačích podpora pořadí, ve kterém jsou spuštěny a zastavení součásti v pořadí je důležité. Tento scénář lze provádět pomocí následujících kroků:

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Cíl akce spouštění a zastavování proti předplatném a skupině prostředků

1. Přidat **sequencestart** a **sequencestop** značky s hodnotou kladné celé číslo k virtuálním počítačům, které cílí **External_Start_ResourceGroupNames** a  **External_Stop_ResourceGroupNames** proměnné. Spouštění a zastavování akce proběhnou ve vzestupném pořadí. Zjistěte, jak označit virtuální počítač, najdete v článku [označit virtuální počítače s Windows v Azure](../virtual-machines/windows/tag.md) a [označit virtuální počítač s Linuxem v Azure](../virtual-machines/linux/tag.md).
1. Změnit plány **Sequenced StartVM** a **Sequenced StopVM** na datum a čas, který podle svých požadavků a plán povolit.
1. Spustit **SequencedStartStop_Parent** runbooku pomocí akce parametrem nastaveným na **start** a WHATIF parametrem nastaveným na **True** náhled změn.
1. Zobrazit náhled akce a proveďte potřebné změny před implementací proti produkčních virtuálních počítačů. Když budete připraveni, je nutné ručně spustit sadu runbook s parametrem nastaveným na **False**, nebo nechat plán Automation **Sequenced StartVM** a **Sequenced StopVM** spuštění automaticky podle předepsaného plánu.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Akce spouštění a zastavování cílit, seznam virtuálních počítačů

1. Přidat **sequencestart** a **sequencestop** značky s hodnotou kladné celé číslo k virtuálním počítačům, které chcete přidat do **seznamů VMList** proměnné. 
1. Spustit **SequencedStartStop_Parent** runbooku pomocí akce parametrem nastaveným na **start**, přidejte čárkou oddělený seznam virtuálních počítačů v *seznamů VMList* parametr a pak nastavte Parametr WHATIF **True**. Náhled změn.
1. Konfigurace **External_ExcludeVMNames** parametr s čárkou oddělený seznam virtuálních počítačů (VM1, VM2 VM3).
1. Tento scénář nerespektuje **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupnames** proměnné. V tomto scénáři je potřeba vytvořit vlastní plán Automation. Podrobnosti najdete v tématu [plánování runbooku ve službě Azure Automation](../automation/automation-schedules.md).
1. Zobrazit náhled akce a proveďte potřebné změny před implementací proti produkčních virtuálních počítačů. Když budete připraveni, je nutné ručně provést monitorování a diagnostics/monitorování – akce – groupsrunbook s parametrem nastaveným na **False**, nebo nechat plán Automation **Sequenced StartVM** a **Sequenced StopVM** spouštět automaticky podle předepsaného plánu.

### <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a>Scénář 3: Spuštění/zastavení automaticky na základě využití procesoru

Toto řešení může pomoct spravovat náklady na provozování virtuálních počítačů ve vašem předplatném, virtuální počítače Azure, které nejsou používány během období mimo špičku, například po hodinách, a automaticky tyto služby vypnout Pokud je využití procesoru významu méně než x %.

Ve výchozím nastavení toto řešení je nakonfigurovaná tak, aby vyhodnotit metriky procesoru procento jestli průměrné využití je 5 % nebo méně. V tomto scénáři řídí následující proměnné a je možné upravit, pokud výchozí hodnoty nesplňují vaše požadavky:

- External_AutoStop_MetricName
- External_AutoStop_Threshold
- External_AutoStop_TimeAggregationOperator
- External_AutoStop_TimeWindow

Můžete povolit cílení na reagovat na předplatné a skupinu prostředků nebo cílení na konkrétní seznam virtuálních počítačů, ale ne obojí.

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Cíl akce zastavení proti předplatném a skupině prostředků

1. Konfigurace **External_Stop_ResourceGroupNames** a **External_ExcludeVMNames** proměnné k určení cílových virtuálních počítačů.
1. Povolit a aktualizovat **Schedule_AutoStop_CreateAlert_Parent** plánu.
1. Spustit **AutoStop_CreateAlert_Parent** runbooku pomocí akce parametrem nastaveným na **start** a WHATIF parametrem nastaveným na **True** náhled změn.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Akce spouštění a zastavování cílit, seznam virtuálních počítačů

1. Spustit **AutoStop_CreateAlert_Parent** runbooku pomocí akce parametrem nastaveným na **start**, přidejte čárkou oddělený seznam virtuálních počítačů v *seznamů VMList* parametr a pak nastavte Parametr WHATIF **True**. Náhled změn.
1. Konfigurace **External_ExcludeVMNames** parametr s čárkou oddělený seznam virtuálních počítačů (VM1, VM2 VM3).
1. Tento scénář nerespektuje **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupnames** proměnné. V tomto scénáři je potřeba vytvořit vlastní plán Automation. Podrobnosti najdete v tématu [plánování runbooku ve službě Azure Automation](../automation/automation-schedules.md).

Teď, když máte plán pro zastavení virtuálních počítačů založených na využití procesoru, je potřeba povolit jeden z následujících plánů spustit.

- Spouštěcí akci cílové předplatné a skupinu prostředků. Podívejte se na postup v [scénář 1](#scenario-1-startstop-vms-on-a-schedule) pro účely testování a povolení **plánovaný-StartVM** plány.
- Cíl zahájení předplatné, skupinu prostředků a značky. Podívejte se na postup v [scénář 2](#scenario-2-startstop-vms-in-sequence-by-using-tags) pro účely testování a povolení **Sequenced StartVM** plány.

## <a name="solution-components"></a>Součásti řešení

Toto řešení obsahuje předem nakonfigurované sady runbook, plány a integraci s Log Analytics, takže můžete přizpůsobit plánů spouštění a vypínání virtuálních počítačů tak, aby vyhovovala vašim obchodním potřebám.

### <a name="runbooks"></a>Runbooky

V následující tabulce jsou uvedeny runbooky nasazení do vašeho účtu Automation pomocí tohoto řešení. Neprovádějte změny kódu sady runbook. Místo toho napište vlastní sadu runbook pro nové funkce.

> [!IMPORTANT]
> Nelze spustit přímo libovolné sady runbook s "podřízený" připojenou k názvu.

Zahrnout všechny nadřazené sady runbook _WhatIf_ parametru. Pokud je nastavena na **True**, _WhatIf_ podporuje s podrobnostmi o stejné chování sada runbook provede při spuštění bez _WhatIf_ parametr a ověří správnou virtuální počítače se Služba je určená. Sada runbook provádí pouze jeho definované akce při _WhatIf_ parametr je nastaven na **False**.

|Runbook | Parametry | Popis|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Volá se, nezávisle na nadřízeném runbooku. Tato sada runbook vytvoří výstrahy na základě jednotlivých zdrojích pro scénář AutoStop.|
|AutoStop_CreateAlert_Parent | Seznamů VMList<br> WhatIf: True nebo False  | Vytvoří nebo aktualizuje Azure pravidla upozornění na virtuálních počítačích v cílových skupinách předplatné nebo prostředek. <br> Seznamů VMList: Čárkou oddělený seznam virtuálních počítačů. Například _vm1, vm2 vm3_.<br> *WhatIf* ověří logiky sad runbook bez spuštění.|
|AutoStop_Disable | žádný | Zakáže upozornění AutoStop a výchozí plán.|
|AutoStop_StopVM_Child | WebHookData | Volá se, nezávisle na nadřízeném runbooku. Pravidla upozornění voláním tohoto runbooku se zastavit virtuální počítač.|
|Bootstrap_Main | žádný | Jednou použít ke konfiguraci bootstrap konfigurací, jako jsou třeba webhookURI, které nejsou obvykle přístupné z Azure Resource Manageru. Tato sada runbook je automaticky odebere po úspěšném nasazení.|
|ScheduledStartStop_Child | VMName <br> Akce: Spuštění nebo zastavení <br> ResourceGroupName | Volá se, nezávisle na nadřízeném runbooku. Provede akci spuštění nebo zastavení pro plánované stop.|
|ScheduledStartStop_Parent | Akce: Spuštění nebo zastavení <br>Seznamů VMList <br> WhatIf: True nebo False | Toto nastavení ovlivňuje všechny virtuální počítače v rámci předplatného. Upravit **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupNames** provádět pouze na tyto cílové skupiny prostředků. Můžete také vyloučit konkrétní virtuální počítače prostřednictvím aktualizace **External_ExcludeVMNames** proměnné.<br> Seznamů VMList: Čárkou oddělený seznam virtuálních počítačů. Například _vm1, vm2 vm3_.<br> _WhatIf_ ověří logiky sad runbook bez spuštění.|
|SequencedStartStop_Parent | Akce: Spuštění nebo zastavení <br> WhatIf: True nebo False<br>Seznamů VMList| Vytvoření značky s názvem **sequencestart** a **sequencestop** na každý virtuální počítač, pro které chcete do sekvenční aktivity spuštění/zastavení. Tyto názvy tagů rozlišují malá a velká písmena. Hodnota značky musí být kladné celé číslo (1, 2, 3), který odpovídá pořadí, ve kterém chcete spustit nebo zastavit. <br> Seznamů VMList: Čárkou oddělený seznam virtuálních počítačů. Například _vm1, vm2 vm3_. <br> _WhatIf_ ověří logiky sad runbook bez spuštění. <br> **Poznámka:**: virtuální počítače musí být v rámci skupiny prostředků, které jsou definované jako External_Start_ResourceGroupNames External_Stop_ResourceGroupNames a External_ExcludeVMNames ve službě Azure Automation proměnné. Musí mít odpovídající značky pro akce se projeví.|

### <a name="variables"></a>Proměnné

V následující tabulce jsou uvedeny proměnných vytvořené v účtu Automation. Změnit pouze proměnné s předponou **externí**. Úprava proměnné s předponou **interní** způsobí, že nežádoucí účinky.

|Proměnná | Popis|
|---------|------------|
|External_AutoStop_Condition | Podmíněný operátor vyžadované pro konfiguraci podmínky před aktivací výstrahu. Přípustné hodnoty jsou **GreaterThan**, **GreaterThanOrEqual**, **LessThan**, a **LessThanOrEqual**.|
|External_AutoStop_Description | Výstrahu, kterou chcete zastavit virtuální počítač, pokud procento využití procesoru překračuje prahovou hodnotu.|
|External_AutoStop_MetricName | Název metriky výkonu, pro který má být nakonfigurované pravidlo upozornění Azure.|
|External_AutoStop_Threshold | Prahová hodnota pro pravidlo výstrahy Azure zadané v proměnné _External_AutoStop_MetricName_. Procentuální hodnoty musí být v rozsahu 1 až 100.|
|External_AutoStop_TimeAggregationOperator | Operátor agregace času, který se použije pro vybrané okno velikost vyhodnocení podmínky. Přípustné hodnoty jsou **průměrné**, **minimální**, **maximální**, **celkový**, a **poslední**.|
|External_AutoStop_TimeWindow | Velikost okna, během kterého Azure analyzuje vybrané metriky pro aktivaci výstrahy. Tento parametr přijímá vstup ve formátu timespan. Možné hodnoty jsou od 5 minut až 6 hodin.|
|External_ExcludeVMNames | Zadejte názvy virtuálních počítačů, které se mají vyloučit, oddělení názvy oddělte čárkou bez mezer.|
|External_Start_ResourceGroupNames | Určuje jeden nebo více skupin prostředků oddělení hodnoty oddělte čárkou, je určená pro zahájení akce.|
|External_Stop_ResourceGroupNames | Určuje jeden nebo více skupin prostředků oddělení hodnoty oddělte čárkou, je určená pro zastavení akce.|
|Internal_AutomationAccountName | Určuje název účtu služby Automation.|
|Internal_AutoSnooze_WebhookUri | Určuje že identifikátor URI Webhooku pro scénář AutoStop volána.|
|Internal_AzureSubscriptionId | Určuje ID předplatného Azure.|
|Internal_ResourceGroupName | Určuje název skupiny prostředků účtu Automation.|

Ve všech scénářích **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, a **External_ExcludeVMNames** proměnné jsou nezbytné pro cílení na virtuální počítače, s výjimkou čárkou oddělený seznam virtuálních počítačů pro zajištění **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**, a  **ScheduledStartStop_Parent** sady runbook. To znamená že vaše virtuální počítače musí nacházet v cílové skupiny prostředků pro spuštění a zastavit akcí. Logika funguje podobně jako ke službě Azure policy, můžete cílit předplatné nebo skupinu prostředků a akce, zdědí nově vytvořených virtuálních počítačů. Tento přístup díky tomu není nutné udržovat samostatný plán pro každý virtuální počítač a spravovat spustí a zastaví ve škálování.

### <a name="schedules"></a>Plány

Následující tabulka obsahuje seznam všech výchozích plánů vytvořené v účtu Automation. Můžete je upravit nebo vytvořit vlastní schémata. Ve výchozím nastavení, všechny plány jsou zakázané s výjimkou **Scheduled_StartVM** a **Scheduled_StopVM**.

Všechny plány, neměli byste povolit, protože by to mohlo způsobit překrývající se plán akcí. Je vhodné určit optimalizace, které chcete provést a změňte je odpovídajícím způsobem. Zobrazit ukázkové scénáře v oddílu přehled další vysvětlení.

|Název plánu | Frekvence | Popis|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Každých 8 hodin | Ke spuštění sady runbook AutoStop_CreateAlert_Parent každých 8 hodin, což zase zastaví hodnoty založená na virtuálních počítačích v External_Start_ResourceGroupNames External_Stop_ResourceGroupNames a External_ExcludeVMNames ve službě Azure Automation proměnné. Alternativně můžete zadat čárkou oddělený seznam virtuálních počítačů s použitím seznamů VMList parametru.|
|Scheduled_StopVM | Uživatelem definované, každý den | Ke spuštění sady runbook Scheduled_Parent s parametrem _Zastavit_ každý den v určený čas. Automaticky zastaví všechny virtuální počítače, které splňují pravidel definovaných funkcemi asset proměnné. Povolit související plán **plánovaný-StartVM**.|
|Scheduled_StartVM | Uživatelem definované, každý den | Ke spuštění sady runbook Scheduled_Parent s parametrem _Start_ každý den v určený čas. Všechny virtuální počítače, které splňují pravidel definovaných funkcemi příslušných proměnných se automaticky spustí. Povolit související plán **naplánované StopVM**.|
|Sekvencování StopVM | 1:00 AM (UTC), každý pátek | Ke spuštění sady runbook Sequenced_Parent s parametrem _Zastavit_ každý pátek v zadanou dobu. Postupně (vzestupně) se zastaví všechny virtuální počítače se značkou **SequenceStop** určené příslušných proměnných. Další informace o hodnoty značek a asset proměnné naleznete v části sady Runbook. Povolit související plán **Sequenced StartVM**.|
|Sekvencování StartVM | 1:00 hodin (UTC), každé pondělí | Ke spuštění sady runbook Sequenced_Parent s parametrem _Start_ každé pondělí v zadanou dobu. Postupně (sestupně) začíná značku ze všech virtuálních počítačů **SequenceStart** určené příslušných proměnných. Další informace o hodnoty značek a asset proměnné naleznete v části sady Runbook. Povolit související plán **Sequenced StopVM**.|

## <a name="log-analytics-records"></a>Záznamy služby Log Analytics

Služba Automation vytváří dva typy záznamů v pracovním prostoru Log Analytics: protokoly úloh a úloh datových proudů.

### <a name="job-logs"></a>Protokoly úloh

|Vlastnost | Popis|
|----------|----------|
|Volající |  Kdo operaci zahájil. Možnou hodnotou je e-mailová adresa nebo systém pro naplánované úlohy.|
|Kategorie | Klasifikace typu dat. Službě Automation odpovídá hodnota JobLogs.|
|CorrelationId | Identifikátor GUID, který se o ID korelace úlohy runbooku.|
|JobId | Identifikátor GUID, který představuje ID úlohy runbooku.|
|operationName | Určuje typ operace prováděné v Azure. Hodnota pro automatizaci, je úloha.|
|resourceId | Určuje typ prostředku v Azure. V případě služby Automation je hodnotou účet služby Automation přidružený k příslušnému runbooku.|
|ResourceGroup | Určuje název skupiny prostředků příslušné úlohy runbooku.|
|ResourceProvider | Určuje službu Azure poskytující prostředky, které můžete nasadit a spravovat. Službě Automation odpovídá hodnota Azure Automation.|
|ResourceType | Určuje typ prostředku v Azure. V případě služby Automation je hodnotou účet služby Automation přidružený k příslušnému runbooku.|
|resultType | Stav úlohy runbooku. Možné hodnoty:<br>- Spuštěno<br>- Zastaveno<br>- Pozastaveno<br>- Neúspěch<br>- Úspěch|
|resultDescription | Popisuje výsledný stav úlohy runbooku. Možné hodnoty:<br>- Úloha se spustila<br>- Zpracování úlohy se nezdařilo<br>- Úloha je dokončená|
|RunbookName | Určuje název runbooku.|
|SourceSystem | Určuje zdrojový systém pro odeslaná data. Pro službu Automation je hodnota OpsManager|
|StreamType | Určuje typ události. Možné hodnoty:<br>- Podrobné<br>- Výstup<br>- Chyba<br>- Varování|
|SubscriptionId | Určuje ID předplatného úlohy.
|Time | Datum a čas provedení úlohy runbooku.|

### <a name="job-streams"></a>Datové proudy úlohy

|Vlastnost | Popis|
|----------|----------|
|Volající |  Kdo operaci zahájil. Možnou hodnotou je e-mailová adresa nebo systém pro naplánované úlohy.|
|Kategorie | Klasifikace typu dat. Službě Automation odpovídá hodnota JobStreams.|
|JobId | Identifikátor GUID, který představuje ID úlohy runbooku.|
|operationName | Určuje typ operace prováděné v Azure. Hodnota pro automatizaci, je úloha.|
|ResourceGroup | Určuje název skupiny prostředků příslušné úlohy runbooku.|
|resourceId | Určuje ID prostředku v Azure. V případě služby Automation je hodnotou účet služby Automation přidružený k příslušnému runbooku.|
|ResourceProvider | Určuje službu Azure poskytující prostředky, které můžete nasadit a spravovat. Službě Automation odpovídá hodnota Azure Automation.|
|ResourceType | Určuje typ prostředku v Azure. V případě služby Automation je hodnotou účet služby Automation přidružený k příslušnému runbooku.|
|resultType | Výsledek úlohy runbooku v době, kdy byla událost vygenerována. Možná hodnota je:<br>- Probíhá zpracování|
|resultDescription | Zahrnuje výstupní datový proud z runbooku.|
|RunbookName | Název runbooku.|
|SourceSystem | Určuje zdrojový systém pro odeslaná data. Pro službu Automation je hodnota OpsManager.|
|StreamType | Typ datového proudu úlohy. Možné hodnoty:<br>-Průběh<br>- Výstup<br>- Varování<br>- Chyba<br>- Ladění<br>- Podrobné|
|Čas | Datum a čas provedení úlohy runbooku.|

Při provádění jakékoli hledání v protokolu, které vrací záznamy kategorie **JobLogs** nebo **JobStreams**, můžete vybrat **JobLogs** nebo **JobStreams**zobrazení, které obsahuje sadu dlaždic se souhrnem aktualizací vrácených hledáním.

## <a name="sample-log-searches"></a>Ukázky hledání v protokolech

V následující tabulce jsou uvedeny ukázky hledání v protokolech pro záznamy úloh shromážděné tímto řešením.

|Dotaz | Popis|
|----------|----------|
|Najít úlohy runbooku ScheduledStartStop_Parent, která mají bylo úspěšně dokončeno | "" hledání kategorie == "JobLogs" | kde (RunbookName_s == "ScheduledStartStop_Parent") | kde (hodnotu ResultType == "Dokončeno")  | shrnutí |AggregatedValue = count() by hodnotu ResultType bin (TimeGenerated, 1 hodina) | Seřadit podle TimeGenerated desc ".|
|Najít úlohy runbooku SequencedStartStop_Parent, která mají bylo úspěšně dokončeno | "" hledání kategorie == "JobLogs" | kde (RunbookName_s == "SequencedStartStop_Parent") | kde (hodnotu ResultType == "Dokončeno") | shrnutí |AggregatedValue = count() by hodnotu ResultType bin (TimeGenerated, 1 hodina) | Seřadit podle TimeGenerated desc ".|

## <a name="viewing-the-solution"></a>Zobrazení řešení

Pro přístup k řešení, přejděte na svém účtu Automation vyberte **pracovní prostor** pod **související prostředky**. Na stránce Log Analytics vyberte **řešení** pod **Obecné**. Na **řešení** vyberte řešení, **Start-Stop-VM [pracovní prostor]** ze seznamu.

Výběr řešení zobrazí **Start-Stop-VM [pracovní prostor]** stránka řešení. Tady můžete zkontrolovat důležité podrobnosti, jako **StartStopVM** dlaždici. Stejně jako v pracovním prostoru Log Analytics této dlaždici se zobrazuje počet a grafická reprezentace úloh runbooků pro příslušné řešení, které byly spuštěny a úspěšně dokončili.

![Stránka řešení Update Management Automation](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Tady můžete provádět další analýzu záznamů úlohy po kliknutí na dlaždici prstenec. Na řídicím panelu řešení zobrazuje historii úloh a předem definované vyhledávací dotazy protokolů. Přepnout na portálu Log Analytics rozšířené hledání podle vyhledávací dotazy.

## <a name="configure-email-notifications"></a>Konfigurace e-mailových oznámení

Chcete-li změnit e-mailová oznámení po nasazení řešení, upravte skupinu akcí, které jste vytvořili během nasazení.  

> [!NOTE]
> Předplatná v cloudu Azure Government nepodporují e-mailové funkce tohoto řešení.

Na webu Azure Portal, přejděte na monitorování -> skupiny akcí. Vyberte skupinu akcí s názvem **StartStop_VM_Notication**.

![Stránka řešení Update Management Automation](media/automation-solution-vm-management/azure-monitor.png)

Na **StartStop_VM_Notification** klikněte na **upravit podrobnosti** pod **podrobnosti**. Tím se otevře **e-mailu/SMS nebo nabízená/hlasové** stránky. Aktualizace e-mailovou adresu a klikněte na tlačítko **OK** uložte provedené změny.

![Stránka řešení Update Management Automation](media/automation-solution-vm-management/change-email.png)

Případně můžete přidat další akce související se skupiny akcí, další informace o skupinách akcí naleznete v tématu [skupiny akcí](../monitoring-and-diagnostics/monitoring-action-groups.md)

Následuje ukázkový e-mail, který se odešle, když virtuální počítače vypne řešení.

![Stránka řešení Update Management Automation](media/automation-solution-vm-management/email.png)

## <a name="modify-the-startup-and-shutdown-schedules"></a>Úprava plánů spouštění a vypínání

Správa plánů spouštění a vypínání v tomto řešení probíhá podle stejných kroků, jak je uvedeno v [plánování runbooku ve službě Azure Automation](automation-schedules.md).

Konfigurace tohohle řešení pro právě zastavením virtuálních počítačů v určité době se podporuje. Budete muset:

1. Ujistěte se, jste přidali skupiny prostředků pro virtuální počítače vypnout ve **External_Start_ResourceGroupNames** proměnné.
2. Vytvořte vlastní plán dobu, kdy chcete vypnout virtuální počítače.
3. Přejděte **ScheduledStartStop_Parent** sady runbook a klikněte na tlačítko **plán**. To umožňuje vybrat plán, který jste vytvořili v předchozím kroku.
4. Vyberte **nastavení parametrů a běhu** a nastavte parametr akce "Stop".
5. Klikněte na tlačítko **OK** a uložte změny.

## <a name="update-the-solution"></a>Aktualizace řešení

Pokud jste nasadili předchozí verzi tohoto řešení, je nutné nejdřív odstranit ji z vašeho účtu před nasazením aktualizovanou verzi. Uvedený postup [odebrat řešení](#remove-the-solution) a pak postupujte podle kroků výše na [nasazení řešení](#deploy-the-solution).

## <a name="remove-the-solution"></a>Odebrat řešení

Pokud se rozhodnete, že už nemusíte používat řešení, můžete jej odstranit z účtu služby Automation. Odstraňuje se řešení pouze odebere sady runbook. Neodstraní se plány nebo proměnné, které byly vytvořeny při přidání řešení. Tyto prostředky, které budete muset odstranit ručně, pokud nejsou jejich pomocí jiné sady runbook.

Pokud chcete odstranit toto řešení, postupujte následovně:

1. Ve svém účtu Automation vyberte **pracovní prostor** z levá stránka.
1. Na **řešení** vyberte řešení, **Start-Stop-VM [pracovní prostor]**. Na **VMManagementSolution [pracovní prostor]** stránky, v nabídce vyberte **odstranit**.<br><br> ![Odstranit řešení pro správu virtuálních počítačů](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. V **odstranit řešení** okně potvrďte, že chcete odstranit řešení.
1. Během ověřování informací a řešení se odstraní, můžete sledovat jeho průběh **oznámení** z nabídky. Budete přesměrováni **řešení** stránce po spuštění procesu se odebrat řešení.

Účet Automation a pracovního prostoru Log Analytics se neodstraní jako součást tohoto procesu. Pokud nechcete zachovat pracovní prostor Log Analytics, musíte ručně odstranit. Můžete to provést z portálu Azure portal:

1. V Azure domovskou obrazovku portálu vyberte **Log Analytics**.
1. Na **Log Analytics** stránky, vyberte pracovní prostor.
1. Vyberte **odstranit** v nabídce na stránce nastavení pracovního prostoru.

Pokud nechcete zachovat součásti účet Azure Automation, můžete každý ručně odstranit. Seznam sad runbook, proměnné a plány runbooků vytvořené řešení, najdete v článku [komponenty řešení](#solution-components).

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak vytvářet různé vyhledávací dotazy a zkontrolujte protokoly úloh služby Automation s Log Analytics najdete v tématu [prohledávání protokolů v Log Analytics](../log-analytics/log-analytics-log-searches.md).
- Další informace o spouštění runbooků, postupy při monitorování úloh runbooků a další technické podrobnosti najdete v článku [Sledování úlohy runbooku](automation-runbook-execution.md).
- Další informace o Log Analytics a zdrojích pro shromažďování dat, naleznete v tématu [shromažďování dat úložiště Azure v Log Analytics – přehled](../log-analytics/log-analytics-azure-storage.md).