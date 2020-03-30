---
title: Pravidla akcí pro výstrahy Azure Monitoru
description: Pochopení toho, co jsou pravidla akcí ve Službě Azure Monitor a jak je nakonfigurovat a spravovat.
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: alerts
ms.openlocfilehash: 42f8d9cd30caa48376cda049f6404aa897a6866c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668464"
---
# <a name="action-rules-preview"></a>Pravidla akce (preview)

Pravidla akcí vám pomohou definovat nebo potlačit akce v libovolném oboru Azure Resource Manager (předplatné Azure, skupina prostředků nebo cílový prostředek). Mají různé filtry, které vám pomohou zúžit konkrétní podmnožinu instancí výstrah, na které chcete zajít.

## <a name="why-and-when-should-you-use-action-rules"></a>Proč a kdy byste měli používat pravidla akce?

### <a name="suppression-of-alerts"></a>Potlačení výstrah

Existuje mnoho scénářů, kde je užitečné potlačit oznámení, která výstrahy generovat. Tyto scénáře se pohybují od potlačení během plánovaného okna údržby k potlačení během mimo pracovní dobu. Například tým odpovědný za **ContosoVM** chce potlačit oznámení výstrah pro nadcházející víkend, protože **ContosoVM** prochází plánovanou údržbou. 

Přestože tým může zakázat každé pravidlo výstrahy, které je nakonfigurováno na **ContosoVM** ručně (a povolit jej znovu po údržbě), není to jednoduchý proces. Pravidla akcí pomáhají definovat potlačení výstrah ve velkém měřítku se schopností pružně konfigurovat dobu potlačení. V předchozím příkladu může tým definovat jedno pravidlo akce pro **ContosoVM,** které potlačí všechna oznámení výstrah pro víkend.


### <a name="actions-at-scale"></a>Akce ve velkém měřítku

Přestože pravidla výstrah pomáhají definovat skupinu akcí, která se aktivuje při generování výstrahy, zákazníci mají často společnou skupinu akcí v rámci svého rozsahu operací. Například tým odpovědný za skupinu prostředků **ContosoRG** pravděpodobně definuje stejnou skupinu akcí pro všechna pravidla výstrah definovaná v rámci **ContosoRG**. 

Pravidla akce vám pomohou tento proces zjednodušit. Definováním akcí ve velkém měřítku může být skupina akcí spuštěna pro jakoukoli výstrahu, která je generována v nakonfigurovaném oboru. V předchozím příkladu může tým definovat jedno pravidlo akce na **ContosoRG,** které aktivuje stejnou skupinu akcí pro všechny výstrahy generované v něm.

> [!NOTE]
> Pravidla akce se aktuálně nevztahují na výstrahy stavu služby Azure.

## <a name="configuring-an-action-rule"></a>Konfigurace pravidla akce

K této funkci se dostanete výběrem **možnosti Spravovat akce** ze vstupní stránky **výstrah** v Azure Monitoru. Potom vyberte **pravidla akce (náhled)**. K pravidlům se dostanete výběrem **pravidel akce (náhledu)** z řídicího panelu vstupní stránky pro výstrahy.

![Pravidla akcí ze vstupní stránky Azure Monitoru](media/alerts-action-rules/action-rules-landing-page.png)

Vyberte **+ nové pravidlo akce**. 

![Přidat nové pravidlo akce](media/alerts-action-rules/action-rules-new-rule.png)

Případně můžete vytvořit pravidlo akce při konfiguraci pravidla výstrahy.

![Přidat nové pravidlo akce](media/alerts-action-rules/action-rules-alert-rule.png)

Nyní byste měli vidět stránku toku pro vytváření pravidel akce. Nakonfigurujte následující prvky: 

![Tok vytvoření nového pravidla akce](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Rozsah

Nejprve zvolte obor (předplatné Azure, skupina prostředků nebo cílový prostředek). Můžete také více vybrat kombinaci oborů v rámci jednoho předplatného.

![Obor pravidla akce](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Kritéria filtru

Můžete také definovat filtry zúžit je na konkrétní podmnožinu výstrah. 

Jsou k dispozici tyto filtry: 

* **Závažnost**: Možnost vybrat jednu nebo více výstrah. **Závažnost = Sev1** znamená, že pravidlo akce je použitelná pro všechny výstrahy nastavené na Sev1.
* **Služba monitorování**: Filtr založený na původní monitorovací službě. Tento filtr je také vícenásobný výběr. **Například Monitor Service = "Application Insights"** znamená, že pravidlo akce je použitelné pro všechny výstrahy založené na application insights.
* **Typ prostředku**: Filtr založený na určitém typu prostředku. Tento filtr je také vícenásobný výběr. Například **Typ prostředku = "Virtuální počítače"** znamená, že pravidlo akce je použitelné pro všechny virtuální počítače.
* **ID pravidla výstrahy**: Možnost filtrování konkrétních pravidel výstrah pomocí ID Správce prostředků pravidla výstrahy.
* **Podmínka monitorování**: Filtr pro instance výstrah s podmínkou **Fired** nebo **Resolved** jako podmínkou monitorování.
* **Popis**: Regulární výraz (regulární výraz), který definuje shodu řetězce s popisem definovaným jako součást pravidla výstrahy. **Například Popis obsahuje 'prod'** bude odpovídat všechny výstrahy, které obsahují řetězec "prod" v jejich popisech.
* **Kontext výstrahy (datová část):** Shoda regulárních výrazů, která definuje shodu řetězce s poli kontextu výstrahy datové části výstrahy. **Například kontext výstrahy (datová část) obsahuje počítač 01,** bude odpovídat všem výstraham, jejichž datové části obsahují řetězec "Počítač-01".

Tyto filtry se používají ve spojení s sebou. Například pokud nastavíte **typ prostředku' = virtuální počítače** a **závažnost' = Sev0**, pak jste filtrovali pro všechny výstrahy **Sev0** pouze na virtuálních počítačích. 

![Filtry pravidel akce](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Konfigurace skupiny potlačení nebo akce

Dále nakonfigurujte pravidlo akce pro potlačení výstrah nebo podporu skupiny akcí. Nemůžeš si vybrat obojí. Konfigurace funguje na všech instancích výstrah, které odpovídají dříve definovanému oboru a filtrům.

#### <a name="suppression"></a>Potlačení

Pokud vyberete **potlačení**, nakonfigurujte dobu trvání potlačení akcí a oznámení. Zvolte jednu z následujících možností:
* **Od této chvíle (vždy)**: Potlačí všechna oznámení po neomezenou dobu.
* **V naplánovaném čase**: Potlačí oznámení v rámci ohraničené doby trvání.
* **Při opakování**: Potlačí oznámení v opakovaném denním, týdenním nebo měsíčním plánu.

![Potlačení pravidla akce](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Skupina akcí

Pokud v přepínači vyberete **skupinu akcí,** přidejte existující skupinu akcí nebo vytvořte novou. 

> [!NOTE]
> K pravidlu akce můžete přidružit pouze jednu skupinu akcí.

![Přidání nebo vytvoření nového pravidla akce výběrem skupiny Akcí](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Podrobnosti pravidla akce

Nakonec nakonfigurujte následující podrobnosti pravidla akce:
* Name (Název)
* Skupina prostředků, ve které je uložena
* Popis 

## <a name="example-scenarios"></a>Ukázkové scénáře

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scénář 1: Potlačení výstrah na základě závažnosti

Contoso chce potlačit oznámení pro všechny výstrahy Sev4 na všech virtuálních počítačích v rámci předplatného **ContosoSub** každý víkend.

**Řešení:** Vytvořte pravidlo akce pomocí:
* Rozsah = **ContosoSub**
* Filtry
    * Závažnost = **Sev4**
    * Typ prostředku = **virtuální počítače**
* Potlačení s opakováním nastaveným na týdenní a **zaškrtnuto v sobotu** a **neděli**

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scénář 2: Potlačení výstrah na základě kontextu výstrahy (datová část)

Společnost Contoso chce potlačit oznámení pro všechny výstrahy protokolu generované pro **počítač-01** v **ContosoSub** neomezeně dlouho, protože prochází údržbou.

**Řešení:** Vytvořte pravidlo akce pomocí:
* Rozsah = **ContosoSub**
* Filtry
    * Monitorovat službu = **Analýza protokolů**
    * Kontext výstrahy (datová část) obsahuje **počítač 01**
* Potlačení nastaveno na **Od této chvíle (vždy)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scénář 3: Skupina akcí definovaná ve skupině prostředků

Společnost Contoso definovala [upozornění na metriku na úrovni předplatného](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor). Ale chce definovat akce, které se aktivují speciálně pro výstrahy generované ze skupiny prostředků **ContosoRG**.

**Řešení:** Vytvořte pravidlo akce pomocí:
* Rozsah = **ContosoRG**
* Žádné filtry
* Skupina akcí nastavená na **ContosoActionGroup**

> [!NOTE]
> *Skupiny akcí definované v rámci pravidel akcí a pravidel výstrah fungují nezávisle, bez odstranění duplicit.* Ve scénáři popsaném výše, pokud je pro pravidlo výstrahy definována skupina akcí, aktivuje se ve spojení se skupinou akcí definovanou v pravidle akce. 

## <a name="managing-your-action-rules"></a>Správa pravidel akce

Pravidla akcí můžete zobrazit a spravovat ze zobrazení seznamu:

![Zobrazení seznamu pravidel akcí](media/alerts-action-rules/action-rules-list-view.png)

Zde můžete povolit, zakázat nebo odstranit pravidla akcí ve velkém měřítku zaškrtnutím políčka vedle nich. Když vyberete pravidlo akce, otevře se jeho konfigurační stránka. Stránka vám pomůže aktualizovat definici pravidla akce a povolit nebo zakázat.

## <a name="best-practices"></a>Osvědčené postupy

Protokolvýstrahy, které vytvoříte s [počtem výsledků](alerts-unified-log.md) možnost generovat jednu instanci výstrahy pomocí celého výsledku hledání (který může span přes více počítačů). V tomto scénáři pokud pravidlo akce používá filtr **Kontextu výstrahy (datová část),** pracuje s instancí výstrahy, dokud je shoda. Ve scénáři 2, popsaném dříve, pokud výsledky hledání výstrahy generovaného protokolu obsahují **počítač 01** i **počítač-02**, je potlačeno celé oznámení. Pro **počítač-02** není generováno žádné oznámení.

![Pravidla akce a výstrahy protokolu (počet výsledků)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Chcete-li nejlépe použít výstrahy protokolu s pravidly akce, vytvořte výstrahy protokolu s možností [měření metriky.](alerts-unified-log.md) Tato možnost generuje samostatné instance výstrah na základě definovaného skupinového pole. Potom ve scénáři 2 jsou generovány samostatné instance výstrah pro **počítače 01** a **počítač-02**. Z důvodu pravidla akce popsaného ve scénáři je potlačeno pouze oznámení pro **Počítač 01.** Oznámení pro **Computer-02** pokračuje v palbě jako obvykle.

![Pravidla akce a výstrahy protokolu (počet výsledků)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Nejčastější dotazy

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Při konfiguraci pravidla akce chci zobrazit všechna možná překrývající se pravidla akce, aby se zabránilo duplicitním oznámením. Je to možné?

Po definování oboru při konfiguraci pravidla akce se zobrazí seznam pravidel akce, které se překrývají ve stejném oboru (pokud existuje). Toto překrytí může být jednou z následujících možností:

* Přesná shoda: Například pravidlo akce, které definujete, a překrývající se pravidlo akce jsou ve stejném předplatném.
* Podmnožina: Například pravidlo akce, které definujete, je na předplatné a překrývající se pravidlo akce je na skupině prostředků v rámci předplatného.
* Nadmnožina: Například pravidlo akce, které definujete, je ve skupině prostředků a překrývající se pravidlo akce je na předplatné, které obsahuje skupinu prostředků.
* Průsečík: Například pravidlo akce, které definujete, je na **VM1** a **VM2**a překrývající se pravidlo akce je na **VM2** a **VM3**.

![Překrývající se pravidla akcí](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Během konfigurace pravidla výstrahy je možné zjistit, zda již existují definovaná pravidla akce, která by mohla fungovat podle pravidla výstrahy, které definuji?

Po definování cílového prostředku pro pravidlo výstrahy se zobrazí seznam pravidel akcí, která fungují ve stejném oboru (pokud existuje) výběrem **možnosti Zobrazit nakonfigurované akce** v části **Akce.** Tento seznam je naplněn na základě následujících scénářů pro obor:

* Přesná shoda: Například pravidlo výstrahy, které definujete, a pravidlo akce jsou ve stejném předplatném.
* Podmnožina: Například pravidlo výstrahy, které definujete, je na předplatné a pravidlo akce je na skupinu prostředků v rámci předplatného.
* Nadmnožina: Například pravidlo výstrahy, které definujete, je ve skupině prostředků a pravidlo akce je na předplatné, které obsahuje skupinu prostředků.
* Průsečík: Například pravidlo výstrahy, které definujete, je na **VM1** a **VM2**a pravidlo akce je na **VM2** a **VM3**.
    
![Překrývající se pravidla akcí](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Mohu zobrazit výstrahy, které byly potlačeny pravidlem akce?

Na [stránce se seznamem upozornění](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances)můžete zvolit další sloupec s názvem **Stav potlačení**. Pokud oznámení o instanci výstrahy byla potlačena, by se zobrazí tento stav v seznamu.

![Potlačené instance výstrah](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Pokud existuje pravidlo akce se skupinou akcí a jiné s potlačením aktivním ve stejném oboru, co se stane?

Potlačení má vždy přednost ve stejném oboru.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Co se stane, když mám zdroj, který je monitorován ve dvou samostatných pravidlech akce? Dostanu jedno nebo dvě oznámení? Například **VM2** v následujícím scénáři:

      action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1

Pro každou výstrahu na VM1 a VM3 by se jednou spustila skupina akcí AG1. Pro každou výstrahu na **VM2**by se skupina akcí AG1 spustila dvakrát, protože pravidla akcí neoddvojují akce. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Co se stane, když mám zdroj monitorovaný ve dvou samostatných pravidlech akce a jedno volá po akci, zatímco jiné k potlačení? Například **VM2** v následujícím scénáři:

      action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression

Pro každou výstrahu na VM1 by se jednou spustila skupina akcí AG1. Akce a oznámení pro každou výstrahu na VM2 a VM3 budou potlačeny. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Co se stane, když mám pravidlo výstrahy a pravidlo akce definované pro stejný prostředek volající různé skupiny akcí? Například **VM1** v následujícím scénáři:

      alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1 
 
Pro každou výstrahu na VM1 by se jednou spustila skupina akcí AG1. Při každém spuštění pravidla výstrahy "pravidlo1", bude také aktivovat AG2 navíc. Skupiny akcí definované v rámci pravidel akcí a pravidel výstrah fungují nezávisle, bez odstranění duplicit. 

## <a name="next-steps"></a>Další kroky

- [Další informace o výstrahách v Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
