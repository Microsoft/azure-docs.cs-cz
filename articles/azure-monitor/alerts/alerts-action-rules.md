---
title: Pravidla akcí pro výstrahy Azure Monitor
description: Vysvětlení toho, jaká pravidla akcí v Azure Monitor jsou a jak je nakonfigurovat a spravovat.
ms.topic: conceptual
ms.date: 03/15/2021
ms.openlocfilehash: f70d798270ad82193f7ae5935d34f8f418d35e05
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471673"
---
# <a name="action-rules-preview"></a>Pravidla akcí (Preview)

Pravidla akcí vám pomůžou definovat nebo potlačit akce v jakémkoli oboru Azure Resource Manager (předplatné Azure, skupina prostředků nebo cílový prostředek). Mají různé filtry, které vám pomohou zúžit určitou podmnožinu instancí výstrah, na kterých chcete pracovat.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rBZ2]

## <a name="why-and-when-should-you-use-action-rules"></a>Proč a kdy byste měli použít pravidla akcí?

### <a name="suppression-of-alerts"></a>Potlačení výstrah

Existuje mnoho scénářů, kde je vhodné potlačit oznámení, která generují výstrahy. Tyto scénáře jsou v rozsahu od potlačení při plánovaném časovém období údržby, aby se potlačily v nepracovních hodinách. Například tým zodpovědný za  **ContosoVM** chce potlačit oznámení o výstrahách pro nadcházející víkend, protože **ContosoVM** provádí plánovanou údržbu.

I když tým může zakázat každé pravidlo upozornění, které je nakonfigurováno na **ContosoVM** ručně (a znovu ho povolit po údržbě), není to jednoduchý proces. Pravidla akcí vám pomůžou definovat potlačení výstrah ve velkém měřítku s možností pružně konfigurovat dobu potlačení. V předchozím příkladu může tým definovat jedno pravidlo pro akci na **ContosoVM** , které potlačí všechna oznámení o upozorněních na víkend.

### <a name="actions-at-scale"></a>Škálovatelné akce

I když pravidla výstrah umožňují definovat skupinu akcí, která se aktivuje při vygenerování výstrahy, zákazníci často mají společnou skupinu akcí v rámci jejich oboru operací. Například tým zodpovědný za skupinu prostředků **ContosoRG** bude pravděpodobně definovat stejnou skupinu akcí pro všechna pravidla upozornění definovaná v rámci **ContosoRG**.

Pravidla akcí vám pomůžou tento proces zjednodušit. Když definujete akce se škálováním, může se aktivovat skupina akcí pro všechny výstrahy, které se generují v konfigurovaném oboru. V předchozím příkladu může tým definovat jedno pravidlo akce na **ContosoRG** , které aktivuje stejnou skupinu akcí pro všechny výstrahy, které jsou v ní vygenerované.

> [!NOTE]
> Pravidla akcí se v současnosti nevztahují na Azure Service Health výstrahy.

## <a name="configuring-an-action-rule"></a>Konfigurace pravidla akce

### <a name="portal"></a>[Azure Portal](#tab/portal)

Přístup k této funkci získáte tak, že na úvodní stránce **výstrahy** v Azure monitor vyberete **Spravovat akce** . Pak vyberte **pravidla akce (Preview)**. K pravidlům můžete přistupovat tak, že na řídicím panelu cílové stránky zobrazíte výstrahy a zvolíte **pravidla akcí (Preview)** .

![Pravidla akce z Azure Monitor cílové stránky](media/alerts-action-rules/action-rules-landing-page.png)

Vyberte **+ nové pravidlo akce**.

![Snímek obrazovky se zvýrazněným tlačítkem pro pravidlo nové akce, které se zvýrazní, se zobrazí stránka Správa akcí.](media/alerts-action-rules/action-rules-new-rule.png)

Případně můžete vytvořit pravidlo akce při konfiguraci pravidla upozornění.

![Snímek obrazovky zobrazující stránku vytvořit pravidlo s zvýrazněným tlačítkem vytvořit pravidlo akce](media/alerts-action-rules/action-rules-alert-rule.png)

Nyní byste měli vidět stránku Flow pro vytváření pravidel akcí. Nakonfigurujte následující prvky:

![Tok vytváření nového pravidla akce](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Obor

Nejdřív vyberte obor (předplatné Azure, skupinu prostředků nebo cílový prostředek). V rámci jednoho předplatného můžete také vybrat několik kombinací oborů.

![Rozsah pravidla akce](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Kritéria filtru

Volitelně můžete definovat filtry, takže pravidlo bude platit pro konkrétní podmnožinu výstrah nebo konkrétní události pro každou výstrahu (například pouze "Trigger" nebo pouze "vyřešené").

Jsou k dispozici tyto filtry:

* **Závažnost**  
Toto pravidlo bude platit jenom pro výstrahy s vybranými závažnostmi.  
Například **závažnost = Sev1** znamená, že pravidlo bude platit jenom pro výstrahy se závažností Sev1.
* **Monitorovat službu**  
Toto pravidlo bude platit pouze pro výstrahy přicházející z vybraných monitorovacích služeb.  
Například **monitor služba = "Azure Backup"** znamená, že pravidlo bude platit jenom pro výstrahy zálohování (pocházející z Azure Backup).
* **Typ prostředku**  
Toto pravidlo bude platit jenom pro výstrahy pro vybrané typy prostředků.  
Například **typ prostředku = "Virtual Machines"** znamená, že pravidlo bude platit pouze pro výstrahy na virtuálních počítačích.
* **ID pravidla výstrahy**  
Toto pravidlo bude platit jenom pro výstrahy přicházející z konkrétního pravidla výstrahy. Hodnota by měla být Správce prostředků ID pravidla výstrahy.  
Například **pravidlo upozornění ID = "/Subscriptions/SubId1/resourceGroups/RG1/Providers/Microsoft.Insights/metricalerts/API-Latency"** znamená, že toto pravidlo bude platit pouze pro výstrahy přicházející z pravidla upozornění metriky rozhraní API-latence.  
_Poznámka: můžete získat správné ID pravidla upozornění uvedením pravidel upozornění z rozhraní příkazového řádku nebo otevřením konkrétního pravidla výstrahy na portálu, kliknutím na vlastnosti a zkopírováním hodnoty ID prostředku._
* **Podmínka monitorování**  
Toto pravidlo bude platit pouze pro události výstrah se zadanou podmínkou monitorování – buď **aktivováno** , nebo **Vyřešeno**.
* **Popis**  
Toto pravidlo bude platit pouze pro výstrahy, které v poli Popis výstrahy obsahují konkrétní řetězec. Toto pole obsahuje popis pravidla výstrahy.  
Například **Popis obsahuje ' prod '** znamená, že pravidlo bude odpovídat pouze výstrahám, které obsahují řetězec "prod" v jeho popisu.
* **Kontext výstrahy (datová část)**  
Toto pravidlo bude platit pouze pro výstrahy, které obsahují jednu nebo více konkrétních hodnot v polích kontextu výstrahy.  
Například **kontext výstrahy (datová část) obsahuje řetězec ' Computer-01 '** znamená, že pravidlo bude platit pouze pro výstrahy, jejichž datová část obsahuje řetězec "Computer-01".

Pokud v pravidle nastavíte více filtrů, uplatní se všechny. Pokud například nastavíte **typ prostředku = Virtual Machines** a **závažnost = Sev0**, pravidlo se použije jenom pro upozornění Sev0 na virtuálních počítačích.

![Filtry pravidla akcí](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Nastavení potlačení nebo skupiny akcí

Dále nakonfigurujte pravidlo akce pro potlačení výstrahy nebo podporu skupiny akcí. Nelze zvolit obojí. Konfigurace funguje na všech instancích výstrahy, které odpovídají dříve definovanému oboru a filtrům.

#### <a name="suppression"></a>Potlačení

Pokud vyberete **potlačení**, nakonfigurujte dobu trvání pro potlačení akcí a oznámení. Zvolte jednu z následujících možností:
* **Z Now (vždy)**: potlačí všechna oznámení na neomezenou dobu.
* **V naplánovaném čase**: potlačí oznámení v rámci ohraničené doby trvání.
* **Opakování**: potlačí oznámení na opakující se denní, týdenní nebo měsíční plán.

![Potlačení pravidla akce](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Skupina akcí

Pokud vyberete možnost **Skupina akcí** v přepínači, buď přidejte existující skupinu akcí, nebo vytvořte novou.

> [!NOTE]
> K pravidlu akce můžete přidružit jenom jednu skupinu akcí.

![Přidat nebo vytvořit nové pravidlo akce výběrem skupiny akcí](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Podrobnosti pravidla akce

Nakonec pro pravidlo akce nakonfigurujte následující podrobnosti:
* Name
* Skupina prostředků, ve které je uložená
* Description

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí příkazu [AZ monitor Action-Rule Create](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) můžete vytvořit pravidla akcí pomocí Azure CLI.  `az monitor action-rule`Odkaz je pouze jedním z mnoha odkazů na rozhraní příkazového [řádku Azure CLI pro Azure monitor](/cli/azure/azure-cli-reference-for-monitor).

### <a name="prepare-your-environment"></a>Příprava prostředí

1. [Instalace Azure CLI](/cli/azure/install-azure-cli)

   Pokud budete chtít, můžete k dokončení kroků v tomto článku použít také Azure Cloud Shell.  Azure Cloud Shell je interaktivní prostředí prostředí, které používáte v prohlížeči.  Spusťte Cloud Shell pomocí jedné z následujících metod:

   - Otevřete Cloud Shell tak, že na [https://shell.azure.com](https://shell.azure.com)

   - Vyberte tlačítko **Cloud Shell** na řádku nabídek v pravém horním rohu [Azure Portal](https://portal.azure.com)

1. Přihlaste se.

   Pokud používáte místní instalaci rozhraní příkazového řádku, přihlaste se pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login) .  Dokončete proces ověřování podle kroků zobrazených v terminálu.

    ```azurecli
    az login
    ```

1. Nainstalovat `alertsmanagement` rozšíření

   `az monitor action-rule`Příkaz je experimentálním rozšířením základního rozhraní příkazového řádku Azure. Další informace o odkazech na rozšíření v [používání rozšíření pomocí Azure CLI](/cli/azure/azure-cli-extensions-overview?).

   ```azurecli
   az extension add --name alertsmanagement
   ```

   Očekává se následující upozornění.

   ```output
   The installed extension `alertsmanagement` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="create-action-rules-with-the-azure-cli"></a>Vytváření pravidel akcí pomocí Azure CLI

Informace o požadovaných a volitelných parametrech najdete v referenčním obsahu Azure CLI pro [AZ monitor Action-Rule Create](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) .

Vytvořte pravidlo akce pro potlačení oznámení ve skupině prostředků.

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --scope-type ResourceGroup \
                              --scope /subscriptions/0b1f6471-1bf0-4dda-aec3-cb9272f09590/resourceGroups/MyResourceGroupName \
                              --suppression-recurrence-type Always \
                              --alert-context Contains Computer-01 \
                               --monitor-service Equals "Log Analytics"
```

Vytvořte pravidlo akce pro potlačení oznámení pro všechny výstrahy Sev4 na všech virtuálních počítačích v rámci předplatného, a to každý víkend.

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --severity Equals Sev4 \
                              --target-resource-type Equals Microsoft.Compute/VirtualMachines \
                              --suppression-recurrence-type Weekly \
                              --suppression-recurrence 0 6 \
                              --suppression-start-date 12/09/2018 \
                              --suppression-end-date 12/18/2018 \
                              --suppression-start-time 06:00:00 \
                              --suppression-end-time 14:00:00

```

* * *

## <a name="example-scenarios"></a>Příklady scénářů

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scénář 1: potlačení výstrah na základě závažnosti

Společnost Contoso chce potlačit oznámení pro všechny Sev4 výstrahy na všech virtuálních počítačích v rámci předplatného **ContosoSub** každé víkendy.

**Řešení:** Vytvořit pravidlo akce s:
* Scope = **ContosoSub**
* Filtry
    * Závažnost = **Sev4**
    * Typ prostředku = **Virtual Machines**
* Potlačení s opakováním nastaveným na týdně a zkontrolováno v **sobotu** a **neděli**

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scénář 2: potlačení výstrah na základě kontextu výstrahy (datové části)

Společnost Contoso chce potlačit oznámení pro všechny výstrahy protokolu vygenerované pro **počítač-01** v **ContosoSub** , a to po celou dobu údržby.

**Řešení:** Vytvořit pravidlo akce s:
* Scope = **ContosoSub**
* Filtry
    * Služba monitorování = **Log Analytics**
    * Kontext výstrahy (datová část) obsahuje **počítač-01** .
* Nastavit pro potlačení **od Now (vždy)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scénář 3: Skupina akcí definovaná ve skupině prostředků

Společnost Contoso definovala [výstrahu metriky na úrovni předplatného](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor). Ale chce definovat akce, které se spustí konkrétně pro výstrahy vygenerované ze skupiny prostředků **ContosoRG**.

**Řešení:** Vytvořit pravidlo akce s:
* Scope = **ContosoRG**
* Žádné filtry
* Skupina akcí nastavená na **ContosoActionGroup**

> [!NOTE]
> *Skupiny akcí definované v rámci pravidel akcí a pravidel výstrah pracují nezávisle bez odstranění duplicit.* Pokud je v dříve popsaném scénáři pro pravidlo výstrahy definována skupina akcí, aktivuje se ve spojení se skupinou akcí definovanou v pravidle akce.

## <a name="managing-your-action-rules"></a>Správa pravidel akcí

### <a name="portal"></a>[Azure Portal](#tab/portal)

Pravidla akcí můžete zobrazit a spravovat ze zobrazení seznamu:

![Zobrazení seznamu pravidel akcí](media/alerts-action-rules/action-rules-list-view.png)

Z tohoto místa můžete pravidla akcí povolit, zakázat nebo odstranit ve velkém měřítku zaškrtnutím políčka vedle nich. Když vyberete pravidlo akce, otevře se stránka konfigurace. Stránka vám pomůže aktualizovat definici pravidla akcí a povolit nebo zakázat.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pravidla akcí můžete zobrazit a spravovat pomocí příkazu [AZ monitor Action-Rule](/cli/azure/ext/alertsmanagement/monitor) z Azure CLI.

Než budete spravovat pravidla akcí pomocí Azure CLI, připravte prostředí podle pokynů uvedených v tématu [Konfigurace pravidla akce](#configuring-an-action-rule).

```azurecli
# List all action rules for a subscription
az monitor action-rule list

# Get details of an action rule
az monitor action-rule show --resource-group MyResourceGroupName --name MyActionRuleName

# Update an action rule.
az monitor action-rule update --resource-group MyResourceGroupName --name MyActionRuleName --status Disabled

# Delete an action rule.
az monitor action-rule delete --resource-group MyResourceGroupName --name MyActionRuleName
```

* * *

## <a name="best-practices"></a>Osvědčené postupy

Výstrahy protokolu vytvořené pomocí možnosti [počet výsledků](./alerts-unified-log.md) generují jednu instanci výstrahy pomocí celého výsledku hledání (který může být rozložen mezi několik počítačů). Pokud v tomto scénáři pravidlo akce používá filtr **kontext výstrahy (datové části)** , funguje v instanci výstrahy, pokud existuje shoda. Pokud ve scénáři 2 popsaném v části výsledky hledání pro vygenerovanou výstrahu protokolu existuje jak **počítač-01** , tak i **počítač-02**, potlačí se celé oznámení. Pro **počítač-2** není vygenerováno žádné oznámení.

![Diagram zobrazuje pravidla akcí a výstrahy protokolu s zvýrazněnou jedinou instancí výstrahy.](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Chcete-li nejlépe používat výstrahy protokolu s pravidly akcí, vytvořte výstrahy protokolu s možností [měření metriky](./alerts-unified-log.md) . Jednotlivé instance výstrah jsou vygenerovány touto možností na základě pole definované skupiny. Ve scénáři 2 pak jsou pro **počítač-01** a **počítač-02** vygenerovány samostatné instance výstrah. V důsledku pravidla akce popsaného ve scénáři je potlačeno pouze oznámení pro **počítač-01** . Oznámení pro **počítač-02** se nadále aktivuje jako normální.

![Pravidla akcí a výstrahy protokolu (počet výsledků)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Časté otázky

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>I když konfigurujem pravidlo akce, chci zobrazit všechna možná překrývající se pravidla akcí, aby se zamezilo duplicitním oznámením. Je to možné?

Po definování oboru při konfiguraci pravidla akce uvidíte seznam pravidel akcí, která se překrývají na stejném oboru (pokud existují). Toto překrytí může být jedna z následujících možností:

* Přesná shoda: například pravidlo akce, které definujete, a pravidlo překrývající se akce jsou ve stejném předplatném.
* Podmnožina: pravidlo akce, které definujete, je na předplatném a pravidlo překrývající se akce je ve skupině prostředků v rámci předplatného.
* Nadmnožina: pravidlo akce, které definujete, je ve skupině prostředků a pravidlo překrývající se akce je v předplatném, které obsahuje skupinu prostředků.
* Průsečík: pravidlo akce, které definujete, je na **VM1** a **VM2** a překrývající se pravidlo akce je na **VM2** a **VM3**.

![Snímek obrazovky se zobrazí nová stránka pravidla akce s pravidly akcí, která se zobrazují v pravidlech akcí definovaných ve stejném okně oboru.](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>I když konfigurujem pravidlo výstrahy, je možné zjistit, jestli jsou definovaná pravidla pro pravidla, která se můžou chovat na základě pravidla upozornění, které definujete?

Po definování cílového prostředku pro pravidlo výstrahy se můžete podívat na seznam pravidel akcí, která se chovají ve stejném oboru (pokud existuje) výběrem možnosti **Zobrazit nakonfigurované akce** v části **Akce** . Tento seznam je vyplněný na základě následujících scénářů pro rozsah:

* Přesná shoda: například pravidlo upozornění, které definujete, a pravidlo akce se nachází ve stejném předplatném.
* Podmnožina: pravidlo upozornění, které definujete, se nachází na předplatném a pravidlo akce je v rámci předplatného ve skupině prostředků.
* Nadmnožina: pravidlo výstrahy, které definujete, je ve skupině prostředků a pravidlo akce je v předplatném, které obsahuje skupinu prostředků.
* Průsečík: pravidlo upozornění, které definujete, je na **VM1** a **VM2** a pravidlo akce je na **VM2** a **VM3**.

![Překrývající se pravidla akcí](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Můžu zobrazit výstrahy, které byly potlačeny pravidlem akce?

Na [stránce seznam výstrah](./alerts-managing-alert-instances.md)můžete zvolit další sloupec s názvem **potlačení stavu**. Pokud bylo oznámení pro instanci výstrahy potlačeno, bude tento stav zobrazen v seznamu.

![Potlačené instance výstrah](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Pokud existuje pravidlo akce se skupinou akcí a druhým potlačením aktivní ve stejném oboru, co se stane?

Potlačení vždy má přednost na stejném oboru.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Co se stane, když mám prostředek monitorovaný ve dvou samostatných pravidlech akcí? Získám jedno nebo dvě oznámení? Například **VM2** v následujícím scénáři:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with action group AG1`

Pro každou výstrahu v VM1 a VM3 se aktivuje skupina akcí AG1. Pro každé upozornění v **VM2** se skupina akcí AG1 spustí dvakrát, protože pravidla akcí neodstraňují duplicitní akce.

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Co se stane, když mám prostředek monitorovaný ve dvou samostatných pravidlech akce a jednom volání akce, zatímco další pro potlačení? Například **VM2** v následujícím scénáři:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with suppression`

Pro každé upozornění v VM1 se aktivuje skupina akcí AG1. Akce a oznámení pro každou výstrahu v VM2 a VM3 se potlačí.

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Co se stane, když mám pravidlo výstrahy a pravidlo akce definované pro stejný prostředek, který volá různé skupiny akcí? Například **VM1** v následujícím scénáři:

   `alert rule rule1 on VM1 with action group AG2`

   `action rule AR1 defined for VM1 with action group AG1`

Pro každé upozornění v VM1 se aktivuje skupina akcí AG1. Pokaždé, když se aktivuje pravidlo upozornění "rule1", bude také aktivovat AG2. Skupiny akcí definované v rámci pravidel akcí a pravidel výstrah pracují nezávisle bez odstranění duplicit.

## <a name="next-steps"></a>Další kroky

- [Další informace o výstrahách v Azure](./alerts-overview.md)
