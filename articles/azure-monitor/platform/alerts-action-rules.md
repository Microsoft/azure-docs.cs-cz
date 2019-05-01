---
title: Akce pravidla pro výstrahy monitorování Azure
description: Ke zjištění, jaké akce pravidla jsou a jak konfigurovat a spravovat je.
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: c260273c647dd5dd6050f1fd543ebd5a5aa47b89
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922770"
---
# <a name="action-rules-preview"></a>Akce pravidla (preview)

Tento článek popisuje, jaké akce pravidla jsou a jak konfigurovat a spravovat je.

## <a name="what-are-action-rules"></a>Co jsou pravidla akce?

Akce pravidla umožňují definovat akce (nebo potlačení akce) v jakékoli Resource Manageru oboru (předplatné, skupinu prostředků, nebo prostředek). Mají celou řadu filtry, které umožňují omezit na konkrétní podmnožinu instancí upozornění, které chcete zpracovat. 

Pomocí akce pravidla je možné:

* Potlačit akce a oznámení, pokud jste naplánovali údržbu nebo pro víkendové/svátky, namísto nutnosti úplného vypnutí každé pravidlo výstrah jednotlivě.
* Definujte akce a oznámení ve velkém měřítku: Namísto toho definujte skupinu akcí jednotlivě pro každé pravidlo výstrahy, je možnost definovat skupiny akcí pro aktivaci výstrah generovaných v oboru. Můžu třeba zvolit, aby aktivační událost akce skupiny "ContosoActionGroup" pro všechny výstrahy vygenerované v rámci předplatného.

## <a name="configuring-an-action-rule"></a>Konfigurace pravidlo akce

Funkce se zpřístupní po výběru **spravovat akce** z výstrah úvodní stránka ve službě Azure Monitor. Potom vyberte **akce pravidla (Preview)**. Se dostanete tak, že vyberete **akce pravidla (preview)** z řídicího panelu z úvodní stránky pro výstrahy.

![Akce pravidla z cílové stránky Azure Monitor](media/alerts-action-rules/action-rules-landing-page.png)

Vyberte **+ nové pravidlo akce**. 

![Přidat nové pravidlo akce](media/alerts-action-rules/action-rules-new-rule.png)

Alternativně můžete také vytvořit pravidlo akce při konfiguraci pravidla upozornění.

![Přidat nové pravidlo akce](media/alerts-action-rules/action-rules-alert-rule.png)

Teď byste měli vidět tok vytváření pravidla akci Otevřít. Konfigurujete následující prvky: 

![Nové akce pravidla vytváření toku](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Rozsah

Nejprve zvolte obor, to znamená, cílový prostředek, skupinu prostředků nebo předplatného. Máte také možnost vícenásobný výběr kombinaci výše uvedených možností (v rámci jednoho předplatného). 

![Rozsah pravidla akce](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Kritéria filtru

Kromě toho můžete definovat filtry můžete dále zúžit na určitou podmnožinu výstrahy týkající se definovaného oboru. 

Jsou k dispozici tyto filtry: 

* **Závažnost**: Vyberte jeden nebo více závažnosti výstrah. Závažnost = Sev1 znamená, že akce pravidla platí pro všechny výstrahy se závažností jako Sev1.
* **Monitorovat službu**: Filtr založený na původní monitorování služby. Toto je vícenásobný výběr. Například monitorovat službu = "Application Insights" znamená, že akce pravidla platí pro všechny "Application Insights" na základě výstrah.
* **Typ prostředku**: Filtrovat podle konkrétního typu prostředku. Toto je vícenásobný výběr. Například typ prostředku = "Virtual Machines" znamená, že akce pravidla platí pro všechny virtuální počítače.
* **ID pravidla upozornění**: Umožňuje filtrování pro konkrétní pravidla výstrah pomocí Resource Manageru ID pravidla výstrahy.
* **Monitorování stavu**: Filtr pro výstrahy instancí se "Fired" nebo "Vyřešeno" jako stav monitorování.
* **Popis**: Regulární výraz porovnávání v rámci popis definované jako součást pravidla upozornění.
* **Kontext výstrahy (payload)**: V rámci odpovídající regulární výraz [kontext výstrahy](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) pole instanci výstrahy.

Tyto filtry se použijí ve spojení mezi sebou. Například pokud nastavit 'Resource type' = "Virtual Machines" a "Závažnost" = "Sev0", pak můžu mít filtruje pro všechny výstrahy "Sev0" pouze svoje virtuální počítače. 

![Filtry akce pravidla](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Konfigurace skupiny pro potlačení nebo akce

Další konfiguraci akce pravidla pro potlačení výstrahy nebo podpora skupiny akcí. Nelze zvolit oba. Konfigurace má vliv na všechny výstrahy instance odpovídající dříve definovaného oboru a filtry.

#### <a name="suppression"></a>Potlačení

Pokud vyberete **potlačení**, nakonfigurovat doba trvání pro potlačení akcí a oznámení. Vyberte jednu z následujících akcí:
* **Od tohoto okamžiku (vždy)**: Potlačí všechna oznámení po neomezenou dobu.
* **V naplánovaném čase**: Potlačit oznámení v rámci omezená doba trvání.
* **S opakování**: Potlačit podle plánu opakování, který může být denní, týdenní nebo měsíční.

![Akce pravidla potlačení](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Skupina akcí

Pokud vyberete **skupiny akcí** přepínací tlačítko, buď přidejte existující skupiny akcí nebo vytvořte novou. 

> [!NOTE]
> Pravidlo akce lze přidružit pouze jednu skupinu akcí.

![Skupina akcí akce pravidla](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Podrobnosti akce pravidla

A konečně nakonfigurujte následující podrobnosti pro pravidlo akce
* Název
* Skupina prostředků, ve kterém bude uložen
* Popis 

## <a name="example-scenarios"></a>Ukázkové scénáře

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scénář 1: Potlačení výstrah podle závažnosti

Contoso chce potlačit oznámení pro všechna upozornění Sev4 na všechny virtuální počítače v rámci svého předplatného "ContosoSub" každý víkend.

**Řešení:** Vytvoření akce pravidla s
* Rozsah = "ContosoSub.
* Filtry
    * Závažnost = "Sev4.
    * Typ prostředku = "virtuální počítače.
* Potlačení s opakování nastavena na jednou týdně a "Sobota" a "Neděle"

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scénář 2: Potlačení výstrah na základě kontextu výstrahy (datová část)

Contoso chce potlačit oznámení pro všechna přihlášení výstrah vygenerovaných pro "Počítač-01" v "ContosoSub" po neomezenou dobu protože právě probíhá údržba.

**Řešení:** Vytvoření akce pravidla s
* Rozsah = "ContosoSub.
* Filtry
    * Monitorovat službu = "Log Analytics.
    * Kontext výstrahy (payload) obsahuje "Počítač-01"
* Potlačení nastavena na "od tohoto okamžiku (vždy).

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scénář 3: Skupina akcí definované na skupinu prostředků

Contoso má definované [upozornění metriky na úrovni předplatného](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor), ale chce se definují akce, které aktivují výstrahy samostatně pro jejich skupinu prostředků "ContosoRG".

**Řešení:** Vytvoření akce pravidla s
* Rozsah = "ContosoRG.
* Žádné filtry.
* Nastavte na "ContosoActionGroup" skupina akcí

## <a name="managing-your-action-rules"></a>Správa pravidel akce

Můžete zobrazit a spravovat vaše akce pravidla ze zobrazení seznamu, jak je znázorněno níže.

![Zobrazení seznamu akce pravidla](media/alerts-action-rules/action-rules-list-view.png)

Z tohoto místa můžete zaškrtnutím políčka vedle sebe akce povolit, zakázat nebo odstranění pravidla ve velkém měřítku. Kliknutím na pravidlo všechny akce otevřete jeho stránku konfigurace, díky tomu můžete aktualizovat definici a povolí nebo zakáže ho.

## <a name="best-practices"></a>Osvědčené postupy

Upozornění vytvořené pomocí protokolů ["počet výsledků"](https://docs.microsoft.com/azure-monitor/platform/alerts-unified-log) možnost Generovat **jednu instanci výstrahy** pomocí výsledek celého vyhledávání (které by mohly být mezi několik počítačů třeba). V tomto scénáři Pokud pravidlo akce využívá filtr "Kontext výstrahy (payload)", bude fungovat na instanci výstrahy za předpokladu, pokud se zjistí shoda. Ve scénáři 2 jak je popsáno výše, pokud výsledky hledání pro výstrahy protokolu vygenerované obsahovat "Počítač-01" a "Počítač-02" celé oznámení je potlačeno (to znamená, že se neobjeví žádná oznámení vůbec vygenerovaný pro "Počítač-02").

![Akce pravidel a upozornění protokolů (počet výsledků)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Na upozornění protokolu nejlepší využití s pravidly akci, doporučujeme vytvořit s využitím upozornění log ["metriky měření"](https://docs.microsoft.com/azure-monitor/platform/alerts-unified-log) možnost. Použití této možnosti samostatných instancí služby Výstrahy jsou generované na základě definované pole skupiny. Ve scénáři 2, pak samostatných instancí služby Výstrahy jsou generovány pro "Počítač-01" a "Počítač-02". S pravidlem akcí popsaných ve scénáři by pouze oznámení "Počítač-01" potlačit, zatímco oznámení "Počítač-02" by stále chcete-li vyvolat jako obvykle.

![Akce pravidel a upozornění protokolů (počet výsledků)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Nejčastější dotazy

* Otázka: Při konfiguraci pravidla činnosti, chci zobrazit všechny případné překrývající se akce pravidla tak, aby se můžu vyhnout duplicitní oznámení. Je možné provést?

    A. Jakmile definujete obor při konfiguraci pravidla činnosti, zobrazí se seznam pravidel akce, které se překrývají ve stejném oboru (pokud existuje). Tento překrytí může být jedna z následujících možností:
    * Přesná shoda: Například akce pravidlo, které definujete a překrývající se akce pravidla jsou ve stejném předplatném.
    * Dílčí: Například je pravidlo akce, které definujete v rámci předplatného a překrývající se akce pravidlo je na skupinu prostředků v rámci předplatného.
    * Nadmnožinou: Například je pravidlo akce, které definujete pro skupinu prostředků a překrývající se pravidlo akce není v předplatném, která obsahuje skupiny prostředků.
    * Průnik: Například akce pravidlo, které definujete je na "VM1" a "VM2" a překrývající se akce pravidlo je na "VM2" a "VM3".

    ![Překrývající se akce pravidla](media/alerts-action-rules/action-rules-overlapping.png)

* Otázka: Při konfiguraci pravidla upozornění, je možné vědět, pokud jsou již definována akce pravidla, která může reagovat na pravidlo upozornění, které můžu jsem definování?

    A. Jakmile definujete cílový prostředek pro pravidlo výstrahy, zobrazí se seznam pravidel akce, které fungují ve stejném oboru (pokud existuje) kliknutím na "Zobrazit nakonfigurovaná akce" v části "Akce". Tento seznam se vyplní na základě následujících scénářů pro obor:
    * Přesná shoda: Pravidlo výstrahy, kterou definujete a pravidlo akce, třeba jsou ve stejném předplatném.
    * Dílčí: Například se pravidlo upozornění, které definujete v rámci předplatného, a akce pravidlo je na skupinu prostředků v rámci předplatného.
    * Nadmnožinou: Například, kterou definujete pravidlo výstrahy je na skupinu prostředků a akce pravidla je v předplatném, která obsahuje skupiny prostředků.
    * Průnik: Například, které definujete pravidlo výstrahy je na "VM1" a "VM2" a akce pravidlo je na "VM2" a "VM3".
    
    ![Překrývající se akce pravidla](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

* Otázka: Můžete zobrazit výstrahy, které mají potlačila pravidlo akce?

    A. V [stránka se seznamem výstrah](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances), existuje další sloupec, který je možné zvolit volané potlačení stav. Pokud došlo k potlačení oznámení pro výstrahy instance, jeho stav zobrazí v seznamu.

    ![Potlačená upozornění instancí](media/alerts-action-rules/action-rules-suppressed-alerts.png)

* Otázka: Pokud je pravidlo akce s skupiny akcí a druhý s potlačení aktivní ve stejném oboru, co se stane?

    A. **Potlačení vždy přednost ve stejném oboru**.

* Otázka: Co se stane, když mám prostředků sledovat v dvě samostatné akce pravidla? Získat jednu nebo dvě oznámení? Například "VM2" v tomto scénáři:

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1' 
      action rule 'AR2' defined for 'VM2' and 'VM3' with action group 'AG1' 

    A. Pro každé upozornění na "VM1" a 'VM3' by jednou aktivuje skupina akcí "AG1". Pro každé upozornění na 'VM2' by dvakrát aktivuje skupina akcí "AG1" (**akce pravidla není zrušit duplicitní akce**). 

* Otázka: Co se stane, když mám prostředků sledovat v dvě samostatné akce pravidla a jednu pro akce při další položku pro potlačení volá? Například "VM2" v tomto scénáři:

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1' 
      action rule 'AR2' defined for 'VM2' and 'VM3' with suppression

    A. Pro každé upozornění na 'VM1' by jednou aktivuje skupina akcí "AG1". Akce a oznámení pro každé upozornění na "VM2" a "VM3" závažné, budou potlačeny. 

* Otázka: Co se stane, když mám pravidlo upozornění a pravidlo akce definovány pro stejný prostředek volání skupiny různých akcí? Příklad: VM1' v tomto scénáři:

     pravidlo upozornění pravidla "1" na "VM1" pravidlu akce "AG2" skupiny akcí, že 'AR1' definované pro "VM1" skupinou akce "AG1",  
 
    A. Pro každé upozornění na 'VM1' by jednou aktivuje skupina akcí "AG1". Pokaždé, když se aktivuje pravidlo upozornění pravidla "1", také aktivuje "AG2" dále. (**akce skupiny definované v rámci akce pravidla a pravidla upozornění pracovat nezávisle na sobě s žádné duplicit**) 

## <a name="next-steps"></a>Další postup

- [Další informace o výstrahách v Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
