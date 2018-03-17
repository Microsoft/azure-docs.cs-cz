---
title: "Spuštění a zastavení virtuálních počítačů během počítačem nepracujete řešení (preview)"
description: "Toto řešení správy virtuální počítač spustí a zastaví virtuální počítače Azure Resource Manager podle plánu a proaktivně monitoruje z analýzy protokolů."
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: ec15859a92527c4e084075b40d3439d7a19fea1a
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="startstop-vms-during-off-hours-solution-preview-in-azure-automation"></a>Spuštění a zastavení virtuálních počítačů během počítačem nepracujete řešení (preview) ve službě Azure Automation

Spuštění a zastavení virtuálních počítačů špičku řešení spustí a zastaví virtuální počítače Azure na uživatelem definované plány, poskytuje přehledy prostřednictvím analýzy protokolů Azure a odešle volitelné e-mailů pomocí [sendgrid vám umožňuje](https://azuremarketplace.microsoft.com/marketplace/apps/SendGrid.SendGrid?tab=Overview). Podporuje Azure Resource Manager i klasické virtuální počítače pro většinu scénářů. 

Toto řešení poskytuje možnost decentralizované automatizace pro uživatele, kteří chtějí snížit náklady na jejich pomocí prostředky bez serveru, nízkými náklady. Toto řešení můžete:

* Naplánovat virtuální počítače na spuštění a zastavení.
* Naplánovat virtuální počítače na spuštění a zastavení ve vzestupném pořadí pomocí značek Azure (není podporováno pro klasické virtuální počítače).
* Automaticky zastaví virtuální počítače na základě nízké využití procesoru.

## <a name="prerequisites"></a>Požadavky

- Runbooky pracují s [účtem Spustit jako Azure](automation-offering-get-started.md#authentication-methods).  Účet Spustit jako je preferovanou metodou ověřování, protože používá ověřování certifikátem místo hesla, která může vypršení platnosti nebo často mění.  

- Toto řešení spravuje jenom virtuální počítače, které jsou ve stejném předplatném jako účet Azure Automation.  

- Toto řešení je nasadit jenom na následující oblasti: Austrálie – jihovýchod, Střední Kanada, střed, východní USA, Japonsko – východ, Asie a Tichomoří – jihovýchod, Spojené království – jih a západní Evropa.  
    
    > [!NOTE]
    > Sady runbook Správa virtuálních počítačů plánu, můžete vybrat virtuální počítače v libovolné oblasti.  

- K odeslání e-mailová oznámení po dokončení spouštění a zastavování sad runbook virtuálních počítačů při připojování z Azure Marketplace, vyberte **Ano** sendgrid vám umožňuje nasadit. 

    > [!IMPORTANT]
    > Sendgrid vám umožňuje je službu třetí strany. Obraťte se na podporu [sendgrid vám umožňuje](https://sendgrid.com/contact/).  
    >
   
    Omezení sendgridu jsou:

    * Maximálně jeden účet sendgrid vám umožňuje na uživatele za předplatné.
    * Nesmí být delší než dva účty sendgrid vám umožňuje podle předplatného.

Pokud jste nasadili předchozí verze tohoto řešení, můžete jej nejprve odstranit z vašeho účtu před nasazením této verze.  

## <a name="solution-components"></a>Součásti řešení

Toto řešení zahrnuje předkonfigurovaná sady runbook, plány a integrace s analýzy protokolů, můžete přizpůsobit spuštění a vypnutí virtuálních počítačů tak, aby vyhovovala vašim obchodním potřebám. 

### <a name="runbooks"></a>Runbooky

Následující tabulka uvádí sady runbook nasazené do vašeho účtu Automation.  Kód runbook by neměla měnit. Místo toho napište vlastní sadu runbook pro nové funkce.

> [!IMPORTANT]
> Nelze spustit přímo všechny sady runbook s "podřízený" připojí k jeho názvu.
>

Zahrnout všechny nadřízené runbooky *WhatIf* parametr. Pokud nastavíte hodnotu **True**, *WhatIf* podporuje s podrobnostmi o přesné chování sada runbook provede při spuštění bez *WhatIf* parametr a ověří správnou probíhá virtuální počítače cílem.  Sady runbook pouze provede jeho definované akce při *WhatIf* parametr je nastaven na **False**. 

|**Runbook** | **Parametry** | **Popis**|
| --- | --- | ---| 
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Volat pouze z nadřízeného runbooku. Na základě za prostředků pro scénář AutoStop vytváří výstrahy.| 
|AutoStop_CreateAlert_Parent | WhatIf: True nebo False <br> VMList | Vytvoří nebo aktualizuje Azure pravidla výstrah na virtuálních počítačích v cílových skupinách, předplatné nebo prostředek. <br> VMList: Čárkami oddělený seznam virtuálních počítačů.  Například *vm1, virtuálního počítače 2, vm3*.| 
|AutoStop_Disable | Žádné | Zakáže AutoStop výstrahy a výchozí plán.| 
|AutoStop_StopVM_Child | WebHookData | Volat pouze z nadřízeného runbooku. Pravidla výstrah volat tuto sadu runbook k zastavení virtuálního počítače.|  
|Bootstrap_Main | Žádné | Použít jednou nastavit bootstrap konfigurace, třeba webhookURI, které nejsou obvykle přístupné ze Správce prostředků Azure. Tato sada runbook se po úspěšném nasazení automaticky odebere.|  
|ScheduledStartStop_Child | VMName <br> Akce: Zastavení nebo spuštění <br> ResourceGroupName | Volat pouze z nadřízeného runbooku. Provede zastavení nebo spuštění pro naplánované zastavení.|  
|ScheduledStartStop_Parent | Akce: Zastavení nebo spuštění <br> WhatIf: True nebo False | Tato akce ovlivní všechny virtuální počítače v rámci předplatného. Upravit **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupNames** provést pouze na tyto cílové skupiny prostředků. Můžete také vyloučit konkrétní virtuálních počítačů tím, že aktualizuje **External_ExcludeVMNames** proměnné. *WhatIf* se chová stejně jako jiné runbooky.|  
|SequencedStartStop_Parent | Akce: Zastavení nebo spuštění <br> WhatIf: True nebo False | Vytvoření značky s názvem **SequenceStart** a **SequenceStop** na každý virtuální počítač, pro které chcete do pořadí spuštění a zastavení aktivity. Hodnota značky musí být celé kladné číslo (1, 2, 3), který odpovídá pořadí, ve kterém chcete spustit nebo zastavit. *WhatIf* se chová stejně jako jiné runbooky. <br> **Poznámka:**: virtuální počítače musí být v rámci skupiny prostředků, které jsou definované jako External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames a External_ExcludeVMNames v proměnné automatizace Azure. Musí mít odpovídající značky pro akce se projeví.|

### <a name="variables"></a>Proměnné

Následující tabulka uvádí proměnných vytvořené v účtu Automation.  Pouze upravte proměnné předponu **externí**. Úprava proměnné předponu **interní** způsobí, že nežádoucí účinky.  

|**Variable** | **Popis**|
---------|------------|
|External_AutoStop_Condition | Podmíněný operátor vyžaduje pro konfiguraci stavu před spuštěním výstrahu. Přípustné hodnoty jsou **GreaterThan**, **GreaterThanOrEqual**, **LessThan**, a **LessThanOrEqual**.|  
|External_AutoStop_Description | Výstrahu, kterou chcete zastavit virtuální počítač, pokud procento využití procesoru překročí prahovou hodnotu.|  
|External_AutoStop_MetricName | Název metriky výkonu, pro kterou má být nakonfigurován pravidlo výstrahy Azure.| 
|External_AutoStop_Threshold | Prahová hodnota pro pravidlo výstrahy Azure určeném v proměnné *External_AutoStop_MetricName*. Procentuální hodnoty musí být v rozsahu 1 až 100.|  
|External_AutoStop_TimeAggregationOperator | Operátor agregace čas, který se použije na vybrané okno pro velikost vyhodnotit podmínku. Přípustné hodnoty jsou **průměrná**, **minimální**, **maximální**, **celkový**, a **poslední**.|  
|External_AutoStop_TimeWindow | Velikost okna, během které analyzuje Azure vybrané metriky pro aktivaci výstrahy. Tento parametr přijme vstup ve formátu timespan. Možné hodnoty jsou od 5 minut do 6 hodin.|  
|External_EmailFromAddress | Určuje odesílatele e-mailu.|  
|External_EmailSubject | Určuje text pro řádek předmětu e-mailu.|  
|External_EmailToAddress | Určuje příjemcům e-mailu. Jednotlivé názvy oddělte čárkou.|  
|External_ExcludeVMNames | Zadejte názvy virtuálních počítačů, které se mají vyloučit, oddělení názvy oddělte čárkou bez mezer.|  
|External_IsSendEmail | Určuje možnost odeslat e-mailové oznámení po dokončení.  Zadejte **Ano** nebo **ne** není odeslat e-mailu.  Tato možnost by měla být **ne** Pokud jste nepovolili e-mailová oznámení během počátečního nasazení.|  
|External_Start_ResourceGroupNames | Určuje jeden nebo více skupin prostředků oddělení hodnoty oddělte čárkou, určený pro spuštění akce.|  
|External_Stop_ResourceGroupNames | Určuje jeden nebo více skupin prostředků oddělení hodnoty oddělte čárkou, určený pro zastavení akce.|  
|Internal_AutomationAccountName | Určuje název účtu služby Automation.|  
|Internal_AutoSnooze_WebhookUri | Určuje že identifikátor URI Webhooku pro scénář AutoStop volat.|  
|Internal_AzureSubscriptionId | Určuje ID předplatného Azure.|  
|Internal_ResourceGroupName | Určuje název skupiny prostředků účtu Automation.|  
|Internal_SendGridAccountName | Určuje název účtu sendgrid vám umožňuje.|  
|Internal_SendGridPassword | Určuje heslo účtu sendgrid vám umožňuje.|  

<br>

Ve všech scénářích **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, a **External_ExcludeVMNames** nutných proměnné pro cílení na virtuálních počítačích, s výjimkou poskytuje seznam virtuálních počítačů pro textový soubor s oddělovači **AutoStop_CreateAlert_Parent** sady runbook. To znamená že virtuální počítače musí nacházet v cílové skupiny prostředků pro spuštění a zastavit akcí. Logiku funguje trochu jako Azure zásad v, že se cílovou skupinu předplatné nebo prostředek a máte akce zdědí nově vytvořený virtuální počítače. Tento přístup tomu není nutné udržovat samostatné plán pro každý virtuální počítač a spravovat spustí a zastaví v škálování.

### <a name="schedules"></a>Plány

Následující tabulka uvádí každý z výchozích plánů vytvoří ve vašem účtu Automation.  Můžete je upravit nebo vytvořit vlastní vlastní plány.  Ve výchozím nastavení, každý z nich jsou zakázány s výjimkou **Scheduled_StartVM** a **Scheduled_StopVM**.

Všechny plány, by neměla být povolena, protože to může vytvořit překrývající se plán akcí. Je nejvhodnější určit, které optimalizace, kterou chcete provést a odpovídajícím způsobem upravit.  Najdete ukázkové scénáře v oddílu přehled další vysvětlení.   

|**Název plánu** | **Frekvence** | **Popis**|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Každých 8 hodin | Ke spuštění sady runbook AutoStop_CreateAlert_Parent každých 8 hodin, která zase zastaví hodnot na základě virtuálních počítačů v External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames a External_ExcludeVMNames v proměnné automatizace Azure.  Alternativně můžete čárkami oddělený seznam virtuálních počítačů pomocí parametru VMList.|  
|Scheduled_StopVM | Uživatelem definované denně | Ke spuštění sady runbook Scheduled_Parent s parametrem *Zastavit* každý den v určeném čase.  Automaticky zastaví všechny virtuální počítače, které splňují pravidla definované proměnné asset. Měli byste povolit související plán **naplánovaná-StartVM**.|  
|Scheduled_StartVM | Uživatelem definované denně | Ke spuštění sady runbook Scheduled_Parent s parametrem *spustit* každý den v určeném čase.  Všechny virtuální počítače, které splňují pravidla definované odpovídající proměnné se automaticky spustí.  Měli byste povolit související plán **naplánovaná StopVM**.|
|Sekvencování StopVM | 1:00 AM (UTC), každý pátek | Ke spuštění sady runbook Sequenced_Parent s parametrem *Zastavit* každý pátek v určeném čase.  Postupně (vzestupně) zastaví všechny virtuální počítače s značky **SequenceStop** definované odpovídající proměnné.  Informace naleznete v sekci sady Runbook další podrobnosti o značky hodnoty a proměnné asset.  Měli byste povolit související plán **Sequenced-StartVM**.|
|Sequenced-StartVM | 1:00 PM (UTC), každé pondělí | Ke spuštění sady runbook Sequenced_Parent s parametrem *spustit* každé pondělí v určeném čase. Postupně (sestupně) začíná značku ze všech virtuálních počítačů **SequenceStart** definované odpovídající proměnné.  Informace naleznete v sekci sady Runbook další podrobnosti o značky hodnoty a proměnné asset.  Měli byste povolit související plán **Sequenced StopVM**.|

<br>

## <a name="configuration"></a>Konfigurace

Proveďte následující kroky k přidání spuštění a zastavení virtuálních počítačů během počítačem nepracujete řešení na účtu Automation a pak nakonfigurujte proměnné, které chcete přizpůsobit řešení.

1. Na portálu Azure Portal klikněte na **Vytvořit prostředek**.<br> ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)<br>  
2. V podokně Marketplace zadejte klíčové slovo **spustit** nebo **spuštění a zastavení**. Seznam se průběžně filtruje podle zadávaného textu. Alternativně můžete zadejte jeden nebo více klíčových slov z úplný název tohoto řešení a stiskněte klávesu Enter.  Vyberte **spuštění a zastavení virtuálních počítačů, která [Preview]** ve výsledcích hledání.  
3. V **spuštění a zastavení virtuálních počítačů, která [Preview]** vybraného řešení. v podokně zkontrolujte souhrnné informace a potom klikněte na **vytvořit**.  
4. **Přidat řešení** podokně se zobrazí. Zobrazí se výzva ke konfiguraci řešení, než můžete ho importovat do vašeho předplatného automatizace.<br><br> ![Okno Přidat řešení správy virtuálních počítačů](media/automation-solution-vm-management/azure-portal-add-solution-01.png)<br><br>
5.  Na **přidat řešení** podokně, vyberte **pracovního prostoru**. Vyberte pracovní prostor OMS, propojené stejného předplatného Azure, zda má účet Automation v. Pokud nemáte pracovního prostoru, vyberte **vytvořit nový pracovní prostor**. Na **pracovním prostorem OMS** podokně, proveďte následující kroky: 
   - Zadejte název pro nový **pracovní prostor OMS**.
   - Vyberte **předplatné** propojení s výběrem z rozevíracího seznamu, pokud výchozí vybraný není vhodná.
   - Pro **skupiny prostředků**, můžete vytvořit novou skupinu prostředků nebo vyberte nějaký existující.  
   - Vyberte **Umístění**.  V současné době jsou k dispozici pouze umístění **Austrálie – jihovýchod**, **Kanada centrální**, **střed**, **východní USA**, **Japonsko – východ**, **jihovýchodní Asie**, **Spojené království – jih**, a **západní Evropa**.
   - Vyberte možnost u položky **Cenová úroveň**.  Řešení nabízí dvě úrovně: uvolněte a placené OMS.  Úroveň free limit je množství shromažďovaných dat denně, dobu uchování a minut runtime úlohy sady runbook.  V placené úrovni OMS není objem dat shromážděných za den omezen.  

        > [!NOTE]
        > I když za GB (samostatně) placené vrstvy se zobrazí jako možnost, není použitelný.  Pokud ji vyberete a pokračujte vytvoření tohoto řešení v rámci vašeho předplatného, dojde k chybě.  Problém bude odstraněn po oficiálním vydání tohoto řešení.<br>Toto řešení pouze používá automatizace úloh minut a přijímání protokolu.  Nepřidává žádné další další uzly OMS pro vaše prostředí.  

6. Po zadání požadovaných informací na **pracovním prostorem OMS** podokně klikněte na tlačítko **vytvořit**.  V části jeho průběh můžete sledovat **oznámení** v nabídce, která vám vrací **přidat řešení** podokně po dokončení.  
7. Na **přidat řešení** podokně, vyberte **účet Automation**.  Pokud vytváříte nový pracovní prostor OMS, budete muset taky vytvořit nový účet Automation se k ní přidružena.  Vyberte **vytvořit účet Automation**a na **účet Automation přidat** podokně zadejte následující: 
  - Do pole **Název** zadejte název účtu služby Automation.

    Všechny ostatní možnosti se vyplní automaticky na základě na pracovní prostor OMS vybrané. Tyto možnosti nelze změnit.  Účet Spustit v Azure jako představuje výchozí metodu ověřování pro runbooky obsažené v tomto řešení.  Po kliknutí na tlačítko **OK**, se ověřují možností konfigurace a vytvoření účtu Automation.  Průběh zpracování můžete sledovat prostřednictvím možnosti nabídky **Oznámení**. 

    Můžete také vybrat existující účet služby Automation Spustit jako.  Všimněte si, že účet, který jste vybrali nelze již propojit s jiným pracovním prostorem OMS. Pokud už je propojená, se zobrazí zprávu a muset vybrat jiný účet Automation spustit jako nebo vytvořte novou.<br><br> ![Účet služby Automation, který je již propojen s pracovním prostorem OMS](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. Nakonec na **přidat řešení** podokně, vyberte **konfigurace**. **Parametry** podokně se zobrazí.<br><br> ![Podokno parametrů pro řešení](media/automation-solution-vm-management/azure-portal-add-solution-02.png)<br><br>  Zde se zobrazí výzva k:  
   - Zadejte **cílové názvy ResourceGroup**. Jedná se o názvy skupin prostředků, které obsahují virtuální počítače lze spravovat pomocí tohoto řešení.  Můžete zadat více než jeden název a oddělit každou oddělte čárkou (hodnoty nejsou malá a velká písmena).  Je podporováno použití zástupného znaku, pokud jsou cílem virtuální počítače ve všech skupinách prostředků v rámci předplatného.
   - Zadejte **seznamu vyloučení virtuálních počítačů (string)**. Toto je název jednoho nebo více virtuálních počítačů z cílové skupiny prostředků.  Můžete zadat více než jeden název a oddělit každou oddělte čárkou (hodnoty nejsou malá a velká písmena).  Pomocí zástupného znaku je podporována.
   - Vyberte **plán**. Toto je opakovaný datum a čas spuštění a zastavení virtuálních počítačů v cílové skupiny prostředků.  Ve výchozím nastavení je plán nakonfigurovaná na časové pásmo UTC. Vybrat jinou oblast, není k dispozici.  Konfigurace plánu pro konkrétní časové pásmo po dokončení konfigurace řešení, najdete v části [úprava plán spuštění a vypnutí](#modifying-the-startup-and-shutdown-schedule).
   - Pro příjem **e-mailová oznámení** z Sendgridu, přijměte výchozí hodnotu **Ano** a zadejte platnou e-mailovou adresu. Pokud vyberete **ne** ale rozhodnout později, že chcete dostávat e-mailová oznámení, můžete aktualizovat **External_EmailToAddress** proměnná s platnou e-mailové adresy oddělené čárkou a potom Upravit proměnnou **External_IsSendEmail** s hodnotou **Ano**.  

9. Po nakonfigurování počátečního nastavení potřebné pro řešení, vyberte **vytvořit**.  Po ověření jsou všechna nastavení, je řešení nasazeno do vašeho předplatného.  Tento proces může trvat několik sekund a v jeho průběh můžete sledovat **oznámení** z nabídky. 

## <a name="collection-frequency"></a>Četnost shromažďování dat

Automatizace úloh protokolu a úlohy datový proud dat je do úložiště analýzy protokolů konzumována každých 5 minut.  

## <a name="using-the-solution"></a>Použití řešení

Když přidáte do řešení pro správu virtuálních počítačů, **StartStopVMView** dlaždice se přidá na řídicí panel v pracovním prostoru analýzy protokolů z portálu Azure.  Tuto dlaždici se zobrazuje počet a grafické reprezentace úlohy sady runbook pro řešení, které mají spustit a bylo úspěšně dokončeno.<br><br> ![Dlaždice Zobrazení StartStopVM správy virtuálních počítačů](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

V účtu Automation, můžete používat a spravovat řešení výběrem **prostoru** možnost. Na stránce analýzy protokolů vyberte **řešení** v levém podokně.  Na **řešení** vyberte řešení **Start-Stop-VM [prostoru]** ze seznamu.<br><br> ![Seznam řešení v analýzy protokolů](media/automation-solution-vm-management/azure-portal-select-solution-01.png)  

Výběr řešení zobrazí **Start-Stop-VM [prostoru]** podokně řešení. Tady najdete důležité informace, jako **StartStopVM** dlaždici. Tuto dlaždici jako pracovní prostor analýzy protokolů zobrazí počet a grafické reprezentace úlohy sady runbook pro řešení, které byly zahájeny a mít úspěšně dokončila.<br><br> ![Stránka řešení automatizace správy aktualizací](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)  

Tady můžete provést další analýzu záznamů úlohy kliknutím na dlaždici prstenec. Řídicí panel řešení zobrazuje historie úlohy a předem definované protokolu vyhledávací dotazy. Přepnout na portálu pokročilé analýzy protokolů pro vyhledávání podle vyhledávací dotazy.  

Zahrnout všechny nadřízené runbooky *WhatIf* parametr. Pokud nastavíte hodnotu **True**, tento podporuje s podrobnostmi o přesné chování sady runbook provede, když spustit, aniž by *WhatIf* parametr a ověří cílí správné virtuálních počítačů. Sady Runbook provádět jenom akce, jejich definované při *WhatIf* parametr je nastaven na **False**.  


### <a name="scenario-1-daily-startstop-vms-across-a-subscription-or-target-resource-groups"></a>Scénář 1: Denně spuštění a zastavení virtuálních počítačů mezi předplatné nebo cílové skupiny prostředků 

Toto je výchozí konfigurace, při prvním nasazení řešení.  Například můžete nakonfigurovat, aby zastavit všechny virtuální počítače napříč předplatné, když opustit pracovní večer a spusťte je ráno, když jste zpátky v office. Když konfigurujete plány **naplánovaná-StartVM** a **naplánovaná StopVM** během nasazení, spuštění a zastavení cílových virtuálních počítačů.
>[!NOTE]
>Časové pásmo je aktuální časové pásmo, když konfigurujete parametru času plán. Je však uložený ve formátu UTC ve službě Azure Automation.  Nemusíte dělat žádné převodu časového pásma, jako to je zpracování během nasazení.

Můžete určit, které virtuální počítače, které se nacházejí v oboru nakonfigurováním následující proměnné: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, a **External_ ExcludeVMNames**.  

>[!NOTE]
> Hodnota **cílové názvy ResourceGroup** je uloženo jako hodnota pro obě **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupNames**. Pro další členitosti můžete upravit, každá z těchto proměnných pro různé skupiny prostředků.  Pro akci spuštění, použijte **External_Start_ResourceGroupNames**a pro akce zastavení, použijte **External_Stop_ResourceGroupNames**. Virtuální počítače se automaticky přidají do spuštění a zastavení plány.

Chcete-li otestovat a ověřit konfiguraci, spusťte ručně **ScheduledStartStop_Parent** runbook a nastavte parametr akce na **spustit** nebo **Zastavit** a WHATIF Parametr **True**.<br><br> ![Konfigurovat parametry runbooku](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)


 Zobrazte náhled naplánované akce a proveďte potřebné změny před implementací proti produkční virtuálních počítačů.  Můžete ručně spustit sadu runbook s parametrem nastavena na **False**, nebo můžete nechat plán Automation **plán-StartVM** a **plán StopVM** spustit automaticky po předepsaný plán.

### <a name="scenario-2-sequence-the-startstop-vms-across-a-subscription-by-using-tags"></a>Scénář 2: Pořadí spuštění a zastavení virtuálních počítačů mezi předplatné pomocí značek
V prostředí, které obsahuje dvě nebo více součástí na podpora distribuovaných mezi několika virtuálními počítači je důležité pořadí, ve které jsou součástí spuštění a zastavení podpora v pořadí.  To můžete udělat tak, že provedete následující kroky:

1. Přidat **SequenceStart** a **SequenceStop** značku s hodnotou kladné celé číslo na virtuální počítače, které jsou cíleny v **External_Start_ResourceGroupNames** a  **External_Stop_ResourceGroupNames** proměnné.  Spuštění a ukončení akce se provedou ve vzestupném pořadí.  Další postupy k označení virtuálního počítače najdete v tématu [značku virtuálního počítače s Windows v Azure](../virtual-machines/windows/tag.md) a [značku virtuální počítač s Linuxem v Azure](../virtual-machines/linux/tag.md).
2. Změnit plány **Sequenced-StartVM** a **Sequenced StopVM** datum a čas, která splňují požadavky a povolte plán.  

Chcete-li otestovat a ověřit konfiguraci, spusťte ručně **SequencedStartStop_Parent** sady runbook. Nastavte parametr akce na **spustit** nebo **Zastavit** a parametr WHATIF **True**.<br><br> ![Konfigurovat parametry runbooku](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> Zobrazte náhled akce a proveďte potřebné změny před implementací proti produkční virtuálních počítačů.  Při připravené, je nutné ručně spuštění sady runbook s parametrem nastavena na **False**, nebo nechte plán Automation **Sequenced-StartVM** a **Sequenced StopVM** spustit automaticky po předepsaný plán.  

### <a name="scenario-3-automate-startstop-vms-across-a-subscription-based-on-cpu-utilization"></a>Scénář 3: Automatizovat spuštění a zastavení virtuálních počítačů mezi odběru na základě využití procesoru
Toto řešení může pomoct spravovat náklady na provozování virtuálních počítačů ve vašem předplatném hodnocením virtuálních počítačích Azure, který se používá během období mimo špičku, například po hodinách, a automaticky jejich vypínání pokud využití procesoru je menší než hodnota x %.  

Ve výchozím nastavení řešení je předem nakonfigurovaný k vyhodnocení metriku procento procesoru jestli průměrné využití je 5 procent nebo méně.  Tím je řízeno následující proměnné a lze upravit, pokud výchozí hodnoty nevyhovují vašim požadavkům:

* External_AutoStop_MetricName
* External_AutoStop_Threshold
* External_AutoStop_TimeAggregationOperator
* External_AutoStop_TimeWindow

Můžete povolit buď nebo cílením na akci vůči předplatné a skupina prostředků, na konkrétní seznam virtuálních počítačů, ale ne obojí.  

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Cíl akce zastavení pro skupinu předplatného a prostředků

1. Konfigurace **External_Stop_ResourceGroupNames** a **External_ExcludeVMNames** proměnné, které chcete zadat cílový virtuální počítače.  
2. Povolení a aktualizovat **Schedule_AutoStop_CreateAlert_Parent** plán.
3. Spustit **AutoStop_CreateAlert_Parent** runbook s parametrem akce nastavena na **spustit** a parametr WHATIF nastavena na **True** náhled změny.

#### <a name="target-the-stop-action-by-vm-list"></a>Cíl akce zastavení podle seznamu virtuálních počítačů

1. Spustit **AutoStop_CreateAlert_Parent** runbook s parametrem akce nastavena na **spustit**, přidejte seznam virtuálních počítačů v oddělených čárkami *VMList* parametr a potom nastavte Parametr WHATIF **True**. Zobrazení náhledu změn.  
2. Konfigurace **External_ExcludeVMNames** parametr s hodnotami oddělenými čárkou seznam virtuálních počítačů (VM1, virtuálního počítače 2, VM3).
3. Tento scénář nectí **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupnames** proměnné.  V tomto scénáři musíte vytvořit svůj vlastní plán Automation. Podrobnosti najdete v tématu [plánování runbooku ve službě Azure Automation](../automation/automation-schedules.md).

Teď, když máte plán pro zastavení virtuálních počítačů na základě využití procesoru, budete muset povolit jedno z následujících plánů spustit.

* Cíl zahájení předplatného a skupiny prostředků.  Podívejte se na postup v [scénáři 1](#scenario-1:-daily-stop/start-vms-across-a-subscription-or-target-resource-groups) pro účely testování a povolení **naplánovaná-StartVM** plány.
* Cíl zahájení předplatné, skupinu prostředků a značky.  Podívejte se na postup v [scénáři 2](#scenario-2:-sequence-the-stop/start-vms-across-a-subscription-using-tags) pro účely testování a povolení **Sequenced-StartVM** plány.


### <a name="configuring-email-notifications"></a>Konfigurace e-mailových oznámení

Konfigurace e-mailová oznámení po nasazení řešení, upravte následující tři proměnné:

* External_EmailFromAddress: Zadejte e-mailovou adresu odesílatele.
* External_EmailToAddress: Zadejte čárkami oddělený seznam e-mailové adresy (user@hotmail.com, user@outlook.com) pro příjem oznámení e-mailů.
* External_IsSendEmail: Nastavte na **Ano** příjmu e-mailů. Chcete-li zakázat e-mailová oznámení, nastavte hodnotu na **ne**.   


### <a name="modifying-the-startup-and-shutdown-schedules"></a>Úprava plány spuštění a vypnutí

Správa spuštění a vypnutí plány v tomto řešení následuje stejný postup, jak je uvedeno v [plánování runbooku ve službě Azure Automation](automation-schedules.md).     

## <a name="log-analytics-records"></a>Záznamy služby Log Analytics

Automatizace vytvoří dva typy záznamů v úložišti OMS: protokoly úlohy a úlohy datových proudů.

### <a name="job-logs"></a>Protokoly úloh

Vlastnost | Popis|
----------|----------|
Volající |  Kdo operaci zahájil.  Možnou hodnotou je e-mailová adresa nebo systém pro naplánované úlohy.|
Kategorie | Klasifikace typu dat.  Službě Automation odpovídá hodnota JobLogs.|
CorrelationId | Identifikátor GUID, který představuje ID korelace úlohy runbooku.|
JobId | Identifikátor GUID, který představuje ID úlohy runbooku.|
operationName | Určuje typ operace prováděné v Azure.  Hodnota pro automatizaci je úloha.|
resourceId | Určuje typ prostředku v Azure.  V případě služby Automation je hodnotou účet služby Automation přidružený k příslušnému runbooku.|
ResourceGroup | Určuje název skupiny prostředků příslušné úlohy runbooku.|
ResourceProvider | Určuje službu Azure poskytující prostředky, které můžete nasadit a spravovat.  Službě Automation odpovídá hodnota Azure Automation.|
ResourceType | Určuje typ prostředku v Azure.  V případě služby Automation je hodnotou účet služby Automation přidružený k příslušnému runbooku.|
resultType | Stav úlohy runbooku.  Možné hodnoty:<br>- Spuštěno<br>- Zastaveno<br>- Pozastaveno<br>- Neúspěch<br>- Úspěch|
resultDescription | Popisuje výsledný stav úlohy runbooku.  Možné hodnoty:<br>- Úloha se spustila<br>- Zpracování úlohy se nezdařilo<br>- Úloha je dokončená|
RunbookName | Určuje název runbooku.|
SourceSystem | Určuje zdrojový systém pro odeslaná data.  Pro automatizaci hodnota je OpsManager|
StreamType | Určuje typ události. Možné hodnoty:<br>- Podrobné<br>- Výstup<br>- Chyba<br>- Varování|
SubscriptionId | Určuje ID předplatného úlohy.
Time | Datum a čas provedení úlohy runbooku.|


### <a name="job-streams"></a>Datové proudy úlohy

Vlastnost | Popis|
----------|----------|
Volající |  Kdo operaci zahájil.  Možnou hodnotou je e-mailová adresa nebo systém pro naplánované úlohy.|
Kategorie | Klasifikace typu dat.  Službě Automation odpovídá hodnota JobStreams.|
JobId | Identifikátor GUID, který představuje ID úlohy runbooku.|
operationName | Určuje typ operace prováděné v Azure.  Hodnota pro automatizaci je úloha.|
ResourceGroup | Určuje název skupiny prostředků příslušné úlohy runbooku.|
resourceId | Určuje ID prostředku v Azure.  V případě služby Automation je hodnotou účet služby Automation přidružený k příslušnému runbooku.|
ResourceProvider | Určuje službu Azure poskytující prostředky, které můžete nasadit a spravovat.  Službě Automation odpovídá hodnota Azure Automation.|
ResourceType | Určuje typ prostředku v Azure.  V případě služby Automation je hodnotou účet služby Automation přidružený k příslušnému runbooku.|
resultType | Výsledek úlohy runbooku v době, kdy byla událost vygenerována. Možná hodnota je:<br>- Probíhá zpracování|
resultDescription | Zahrnuje výstupní datový proud z runbooku.|
RunbookName | Název runbooku.|
SourceSystem | Určuje zdrojový systém pro odeslaná data.  Hodnota pro automatizaci je OpsManager.|
StreamType | Typ datového proudu úlohy. Možné hodnoty:<br>-Průběh<br>- Výstup<br>- Varování<br>- Chyba<br>- Ladění<br>- Podrobné|
Čas | Datum a čas provedení úlohy runbooku.|

Při vyhledávání všech protokolu, který vrátí záznamy kategorie **JobLogs** nebo **JobStreams**, můžete vybrat **JobLogs** nebo **JobStreams**zobrazení, které obsahuje sadu dlaždic shrnutí aktualizací nalezené.

## <a name="sample-log-searches"></a>Ukázky hledání v protokolech

V následující tabulce jsou uvedeny ukázky hledání v protokolech pro záznamy úloh shromážděné tímto řešením. 

Dotaz | Popis|
----------|----------|
Najít úlohy pro runbook ScheduledStartStop_Parent, které dokončily úspěšně | hledání kategorie == "JobLogs" &#124; kde (RunbookName_s == "ScheduledStartStop_Parent") &#124; kde (ResultType == "Dokončeno") &#124; shrnout AggregatedValue = count() podle ResultType bin (TimeGenerated, 1 hod) &#124; řazení podle TimeGenerated desc|
Najít úlohy pro runbook SequencedStartStop_Parent, které dokončily úspěšně | hledání kategorie == "JobLogs" &#124; kde (RunbookName_s == "SequencedStartStop_Parent") &#124; kde (ResultType == "Dokončeno") &#124; shrnout AggregatedValue = count() podle ResultType bin (TimeGenerated, 1 hod) &#124; řazení podle TimeGenerated desc

## <a name="removing-the-solution"></a>Odebrání řešení

Pokud se rozhodnete, že již nepotřebujete používat řešení, odstraňte jej z účtu služby Automation.  Odstraňování řešení pouze odebere sady runbook. Neodstraní se plány nebo proměnné, které byly vytvořeny při přidání řešení.  Tyto prostředky, které budete muset ručně odstranit, pokud se nepoužívá s jiné runbooky.  

Pokud chcete odstranit řešení, proveďte následující kroky:

1.  Z vašeho účtu Automation vyberte **prostoru** v levém podokně.  
2.  Na **řešení** vyberte řešení **Start-Stop-VM [prostoru]**.  Na **VMManagementSolution [prostoru]** stránky, v nabídce vyberte **odstranit**.<br><br> ![Odstranit řešení pro správu virtuálních počítačů](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  V **odstranit řešení** okně potvrďte, že chcete odstranit řešení.
4.  Při ověření informace a řešení, se odstraní, můžete sledovat průběh v části **oznámení** z nabídky.  Vrátíte se **řešení** stránky po spuštění procesu odebrat řešení.  

Účet Automation a pracovní prostor analýzy protokolů nejsou odstraněna jako součást tohoto procesu.  Pokud nechcete zachovat pracovní prostor analýzy protokolů, budete muset ručně odstranit.  Můžete to provést z portálu Azure:

1.    Z Azure portálu domovskou obrazovku, vyberte **analýzy protokolů**.
2. Na **analýzy protokolů** podokně vyberte pracovní prostor.
3. Vyberte **odstranit** z nabídky v podokně pracovní prostor nastavení.  
      
## <a name="next-steps"></a>Další postup

- Další informace o tom, jak vytvořit různé vyhledávací dotazy a kontrolujte protokoly úlohy automatizace s analýzy protokolů najdete v tématu [přihlásit analýzy protokolů hledání](../log-analytics/log-analytics-log-searches.md).
- Další informace o spouštění runbooků, postupy při monitorování úloh runbooků a další technické podrobnosti najdete v článku [Sledování úlohy runbooku](automation-runbook-execution.md).
- Další informace o analýzy protokolů a kolekci zdrojů dat naleznete v tématu [shromažďování Azure úložiště dat v přehledu analýzy protokolů](../log-analytics/log-analytics-azure-storage.md).






   

