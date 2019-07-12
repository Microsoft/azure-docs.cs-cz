---
title: Akce pravidla pro výstrahy monitorování Azure
description: Ke zjištění, jaké jsou akce pravidla ve službě Azure Monitor a jak konfigurovat a spravovat je.
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: df069ee398ea2937f03765b10576061b5e541390
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656725"
---
# <a name="action-rules-preview"></a>Akce pravidla (preview)

Akce pravidla umožňují definovat nebo potlačit akce v jakékoli Azure Resource Manageru oboru (předplatné Azure, skupinu prostředků nebo cílový prostředek). Mají různé filtry, které nápovědy můžete zúžit určitou podmnožinu instancí upozornění, které chcete pracovat.

## <a name="why-and-when-should-you-use-action-rules"></a>Proč a kdy byste měli použít akce pravidla?

### <a name="suppression-of-alerts"></a>Potlačení výstrah

Existuje mnoho scénářů, kdy je vhodné potlačit oznámení, které generují výstrahy. Tyto scénáře sahají od potlačení během naplánovaného časového období údržby potlačení během mimo pracovní dobu. Například tým odpovědný za **ContosoVM** chce potlačit oznámení pro nadcházející víkendu, protože **ContosoVM** Probíhá plánovaná údržba. 

I když tým můžete zakázat upozornění pravidla, která je nakonfigurovaná na **ContosoVM** ručně (a povolit znovu po údržby), není jednoduchý proces. Akce pravidla umožňují definovat potlačení výstrahy ve velkém měřítku umožňuje flexibilně konfigurace období potlačení. V předchozím příkladu, může tým definovat jedno pravidlo akce na **ContosoVM** , potlačí všechna oznámení o výstrahách pro víkendu.


### <a name="actions-at-scale"></a>Akce ve velkém měřítku

Přestože pravidla upozornění umožňují definovat skupiny akcí, která se aktivuje, když výstraha se vygeneruje, zákazníci často mají běžné skupiny akcí v oboru jejich operací. Například tým odpovědný za skupiny prostředků **ContosoRG** budou pravděpodobně definovat stejnou skupinu akcí pro všechna pravidla výstrah, které jsou definované v rámci **ContosoRG**. 

Akce pravidla vám tento proces zjednodušit. Definuje akce ve velkém měřítku, se dá spouštět skupiny akcí pro všechny výstrahy vygenerované na konfigurovaném oboru. V předchozím příkladu, může tým definovat jedno pravidlo akce na **ContosoRG** , který spustí stejnou skupinu akcí pro všechny výstrahy generované v něm.

> [!NOTE]
> Akce pravidla aktuálně nevztahují na upozornění služby Azure Service Health.

## <a name="configuring-an-action-rule"></a>Konfigurace pravidlo akce

Funkce se zpřístupní po výběru **spravovat akce** z **výstrahy** cílová stránka ve službě Azure Monitor. Vyberte **akce pravidla (preview)** . Pravidla můžete přejít výběrem **akce pravidla (preview)** z řídicího panelu z úvodní stránky pro výstrahy.

![Akce pravidla z cílové stránky Azure Monitor](media/alerts-action-rules/action-rules-landing-page.png)

Vyberte **+ nové pravidlo akce**. 

![Přidat nové pravidlo akce](media/alerts-action-rules/action-rules-new-rule.png)

Alternativně můžete vytvořit pravidlo akce při konfiguraci pravidla upozornění.

![Přidat nové pravidlo akce](media/alerts-action-rules/action-rules-alert-rule.png)

Měli byste vidět stránka flow pro vytváření pravidel akce. Konfigurujete následující prvky: 

![Nové akce pravidla vytváření toku](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Scope

Nejprve zvolte obor (předplatné Azure, skupinu prostředků nebo cílový prostředek). Je možné také vícenásobného výběru kombinaci oborů v rámci jednoho předplatného.

![Rozsah pravidla akce](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Kritéria filtru

Kromě toho můžete definovat filtry můžete zúžit na určitou podmnožinu výstrahy. 

Jsou k dispozici tyto filtry: 

* **Závažnost**: Možnost vybrat si jeden nebo více závažnosti výstrah. **Závažnost = Sev1** znamená, že je akce pravidla použít pro všechny výstrahy nastaven na hodnotu Sev1.
* **Monitorovat službu**: Filtr založený na původní monitorování služby. Tento filtr je také vícenásobného výběru. Například **monitorovat službu = "Application Insights"** znamená, že akce pravidla platí pro všechny výstrahy na základě Application Insights.
* **Typ prostředku**:  Filtr podle konkrétního typu prostředku. Tento filtr je také vícenásobného výběru. Například **typ prostředku = "Virtual Machines"** znamená, že akce pravidla platí pro všechny virtuální počítače.
* **ID pravidla upozornění**: Možnost filtrovat konkrétní pravidla výstrah pomocí Resource Manageru ID pravidla výstrahy.
* **Monitorování stavu**:  Filtr pro výstrahy instancí se buď **Fired** nebo **Vyřešeno** jako stav monitorování.
* **Popis**: Shoda regulární výraz (regulární výraz), který definuje shodu řetězce popisu definované jako součást pravidla upozornění. Například **popis obsahuje "produkční"** bude odpovídat všechny výstrahy, které obsahují řetězec "produkční" v jejich popisy.
* **Upozornění kontextu (payload)** : Shoda regulární výraz, který definuje shodu řetězce s poli kontext výstrahy upozornění datové části. Například **upozornění kontextu (payload) obsahuje "Počítač-01"** bude odpovídat všechny výstrahy, jehož datové části obsahují řetězec "Počítač-01."

Tyto filtry se použijí ve spojení mezi sebou. Například pokud nastavíte **typ prostředku "= virtuálních počítačů** a **závažnost" = Sev0**, pak vyfiltrování pro všechny **Sev0** výstrahy týkající se jenom virtuální počítače. 

![Filtry akce pravidla](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Konfigurace skupiny pro potlačení nebo akce

Potom nakonfigurujte akci pravidla pro potlačení výstrahy nebo podpora skupiny akcí. Nelze zvolit oba. Konfigurace má vliv na všechny výstrahy instance, které odpovídají dříve definovaného oboru a filtry.

#### <a name="suppression"></a>Potlačení

Pokud vyberete **potlačení**, nakonfigurovat doba trvání pro potlačení akcí a oznámení. Zvolte jednu z následujících možností:
* **Od tohoto okamžiku (vždy)** : Potlačí všechna oznámení po neomezenou dobu.
* **V naplánovaném čase**: Potlačí zobrazování oznámení v rámci omezená doba trvání.
* **S opakování**: Potlačí zobrazování oznámení podle opakovaného plánu denní, týdenní nebo měsíční.

![Akce pravidla potlačení](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Skupina akcí

Pokud vyberete **skupiny akcí** přepínací tlačítko, buď přidejte existující skupiny akcí nebo vytvořte novou. 

> [!NOTE]
> Pravidlo akce lze přidružit pouze jednu skupinu akcí.

![Přidejte nebo vytvořte nové pravidlo akce tak, že vyberete skupinu akcí](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Podrobnosti akce pravidla

Poslední nakonfigurujte následující podrobnosti pro pravidlo akce:
* Name
* Skupina prostředků, ve kterém je uložen
* Popis 

## <a name="example-scenarios"></a>Ukázkové scénáře

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scénář 1: Potlačení výstrah podle závažnosti

Contoso chce potlačit oznámení pro všechna upozornění Sev4 na všechny virtuální počítače v rámci předplatného **ContosoSub** každý víkend.

**Řešení:** Vytvoření pravidla akci pomocí:
* Scope = **ContosoSub**
* Filtry
    * Závažnost = **Sev4**
    * Typ prostředku = **virtuálních počítačů**
* Potlačení s nastavena na jednou týdně, opakování a **sobota** a **neděle** zaškrtnuto

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scénář 2: Potlačení výstrah na základě kontextu výstrahy (datová část)

Contoso chce potlačit oznámení pro všechna přihlášení výstrah vygenerovaných pro **počítače-01** v **ContosoSub** po neomezenou dobu protože právě probíhá údržba.

**Řešení:** Vytvoření pravidla akci pomocí:
* Scope = **ContosoSub**
* Filtry
    * Monitorovat službu = **Log Analytics**
    * Upozornění kontext (payload) obsahuje **počítače-01**
* Potlačení nastavena na **od tohoto okamžiku (vždy)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scénář 3: Skupina akcí definované na skupinu prostředků

Contoso má definované [upozornění metriky na úrovni předplatného](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor). Ale chce definují akce, které aktivují speciálně pro výstrahy generované ze skupiny prostředků **ContosoRG**.

**Řešení:** Vytvoření pravidla akci pomocí:
* Rozsah = **ContosoRG**
* Žádné filtry.
* Skupina akcí nastavena na **ContosoActionGroup**

> [!NOTE]
> *Skupiny akcí definovaných v rámci akce pravidla a pravidla upozornění fungovat nezávisle, bez odstranění duplicit.* V podle scénáře popsaného starší, pokud skupiny akcí je definována pro pravidla upozornění se aktivuje ve spojení s definovaným v pravidle akce skupiny akcí. 

## <a name="managing-your-action-rules"></a>Správa pravidel akce

Můžete zobrazit a spravovat vaše akce pravidla ze zobrazení seznamu:

![Zobrazení seznamu akce pravidla](media/alerts-action-rules/action-rules-list-view.png)

Z tohoto místa můžete povolit, zakázat nebo odstranit akce pravidla ve velkém měřítku zaškrtnutím políčka vedle sebe. Když vyberete pravidlo akce, otevře se stránka jeho konfigurace. Na stránce umožňuje aktualizovat definici pravidla akce a povolení nebo zakázání ho.

## <a name="best-practices"></a>Osvědčené postupy

Upozornění, které vytvoříte pomocí protokolů [počet výsledků](alerts-unified-log.md) možnost generovat jednu instanci výstrahy pomocí výsledek celého vyhledávání (které můžou pokrývat více počítačů). V tomto scénáři, kdy používá pravidlo akce **kontext výstrahy (payload)** filtr funguje na instanci výstrahy za předpokladu, pokud se zjistí shoda. Ve scénáři 2, jsou popsané dříve, pokud výsledky hledání pro výstrahy generované protokolu obsahují **počítače-01** a **počítače-02**, je potlačeno celé oznámení. Se neobjeví žádná oznámení generovaná pro **počítače-02** vůbec.

![Akce pravidel a upozornění protokolů (počet výsledků)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Upozornění protokolů nejlépe pomocí akce pravidla, vytvořit s využitím upozornění log [měření metriky](alerts-unified-log.md) možnost. Samostatné instance výstrahy jsou generovány tuto možnost, v závislosti na jeho pole definované skupiny. Ve scénáři 2, pak samostatných instancí služby Výstrahy jsou generovány pro **počítače-01** a **počítače-02**. Z důvodu akce pravidla popsaných ve scénáři pouze oznámení o **počítače-01** je potlačeno. Oznámení pro **počítače-02** nadále vyvolat jako obvykle.

![Akce pravidel a upozornění protokolů (počet výsledků)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Nejčastější dotazy

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Když mám teď konfigurace pravidlo akce, chci zobrazit všechny případné překrývající se akce pravidla tak, aby se můžu vyhnout duplicitní oznámení. Je možné udělat?

Po definování oboru nakonfigurovat pravidlo akce se zobrazí seznam pravidel akcí, které se překrývají ve stejném oboru (pokud existuje). Tento překrytí může být jedna z následujících možností:

* Přesná shoda: Například akce pravidlo, které definujete a překrývající se akce pravidla jsou ve stejném předplatném.
* Podmnožina: Například je pravidlo akce, které definujete v rámci předplatného a překrývající se akce pravidlo je na skupinu prostředků v rámci předplatného.
* Nadmnožina: Například je pravidlo akce, které definujete pro skupinu prostředků a překrývající se pravidlo akce není v předplatném, která obsahuje skupiny prostředků.
* Průnik: Například je pravidlo akce, které definujete v **VM1** a **VM2**, a překrývající se pravidlo akce **VM2** a **VM3**.

![Překrývající se akce pravidla](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Když mám jsem konfigurace pravidla výstrahy, je možné vědět, pokud jsou již definována akce pravidla, která může reagovat na pravidlo upozornění, které můžu jsem definování?

Po definování cílový prostředek pro pravidlo výstrahy můžete zobrazit seznam pravidel akce, které fungují ve stejném oboru (pokud existuje) tak, že vyberete **zobrazit nakonfigurovaná akce** pod **akce** oddílu. Tento seznam se vyplní na základě následujících scénářů pro obor:

* Přesná shoda: Pravidlo výstrahy, které definujete a pravidlo akce, třeba jsou ve stejném předplatném.
* Podmnožina: Například se pravidlo upozornění, které definujete v rámci předplatného, a akce pravidlo je na skupinu prostředků v rámci předplatného.
* Nadmnožina: Například se pravidlo upozornění, které definujete pro skupinu prostředků a akce pravidla je v předplatném, která obsahuje skupiny prostředků.
* Průnik: Pravidlo výstrahy, které definujete je třeba na **VM1** a **VM2**, a akce pravidlo je na **VM2** a **VM3**.
    
![Překrývající se akce pravidla](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Můžete zobrazit výstrahy, které mají potlačila pravidlo akce?

V [stránka se seznamem výstrah](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances), můžete použít další sloupec s názvem **stav potlačení**. Pokud došlo k potlačení oznámení pro výstrahy instance, jeho stav zobrazí v seznamu.

![Potlačená upozornění instancí](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Pokud je pravidlo akce s skupiny akcí a druhý s potlačení aktivní ve stejném oboru, co se stane?

Potlačení vždy přednost ve stejném oboru.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Co se stane, když mám prostředků, která je sledována v dvě samostatné akce pravidla? Získat jednu nebo dvě oznámení? Například **VM2** v následujícím scénáři:

      "action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1"

Pro každé upozornění na VM1 a VM3 by skupina akcí AG1 aktivováno jednou. Pro každé upozornění na **VM2**, skupina akcí AG1 by dvakrát, neaktivuje, protože akce pravidla neodstraňujte duplicity akce. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Co se stane, když mám prostředků sledovat v dvě samostatné akce pravidla a jednu pro akce při další položku pro potlačení volá? Například **VM2** v následujícím scénáři:

      "action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression"

Pro každé upozornění na VM1 by skupina akcí AG1 aktivováno jednou. Akce a oznámení pro každé upozornění na VM2 a VM3 závažné, budou potlačeny. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Co se stane, když mám pravidlo upozornění a pravidlo akce definovány pro stejný prostředek volání skupiny různých akcí? Například **VM1** v následujícím scénáři:

      "alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1" 
 
Pro každé upozornění na VM1 by skupina akcí AG1 aktivováno jednou. Pokaždé, když se aktivuje pravidlo upozornění "pravidla 1", také aktivuje AG2 dále. Skupiny akcí definovaných v rámci akce pravidla a pravidla upozornění fungovat nezávisle, bez odstranění duplicit. 

## <a name="next-steps"></a>Další postup

- [Další informace o výstrahách v Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
