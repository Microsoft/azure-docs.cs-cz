---
title: Vytváření výstrah v OMS Log Analytics | Microsoft Docs
description: Výstrahy v Log Analytics identifikovat důležité informace ve svém úložišti OMS a můžete proaktivně upozorňují na problémy nebo vyvolání akce se pokusit o opravte je.  Tento článek popisuje, jak vytvořit pravidlo výstrahy a podrobnosti o různé akce, které jejich zajištění může trvat.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2018
ms.author: bwren
ms.openlocfilehash: b692822660ab12f89b274cea75727fb808d673f8
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="working-with-alert-rules-in-log-analytics"></a>Práce s pravidla výstrah v analýzy protokolů

> [!NOTE]
> Tento článek popisuje classic výstrahy analýzy protokolů, které jsou spravované na portálu OMS.  Výstrahy v analýzy protokolů jsou [rozšiřovanou do Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md).  Až bude vše Hotovo, pak bude vytvářet a upravovat pravidla výstrah na portálu Azure a Poznámka pomocí postupů v tomto článku.


Upozornění se vytvářejí na základě pravidel upozornění, která automaticky a v pravidelných intervalech spouští prohledávání protokolů.  Uživatel vytvořit záznam výstrahy, pokud výsledky odpovídají konkrétním kritériím.  Pravidlo pak může automaticky spustit jednu nebo více akcí a proaktivně vás upozornit nebo vyvolat jiný proces.   

Tento článek popisuje procesy vytvářet a upravovat pravidla výstrah pomocí portálu OMS.  Podrobnosti o různých nastavení a implementovat požadované logiku najdete v tématu [vysvětlení výstrah v analýzy protokolů](log-analytics-alerts.md).


## <a name="create-an-alert-rule"></a>Vytvoření pravidla upozornění

Pokud chcete vytvořit pravidlo výstrahy pomocí portálu OMS, začněte vytvořením hledání protokolů pro záznamy, které by měla vyvolat výstrahu.  **Výstraha** tlačítko pak bude k dispozici, můžete vytvořit a nakonfigurovat pravidlo výstrahy.

>[!NOTE]
> V pracovním prostoru analýzy protokolů můžete aktuálně vytvořit maximálně 250 pravidla výstrah. 

1. Na stránce Přehled OMS, klikněte na tlačítko **hledání protokolů**.
2. Vytvořit nový dotaz vyhledávání protokolu nebo zvolte hledání, uložený protokol. 
3. Kliknutím na **Upozornění** v horní části stránky otevřete obrazovku **Přidat pravidlo upozornění**.
4. Konfigurace výstrah pravidla pomocí informací v [podrobnosti pravidla výstrah](#details-of-alert-rules) níže.
6. Kliknutím na **Uložit** dokončete pravidlo upozornění.  Zahájí se okamžitě spuštěna.


## <a name="edit-an-alert-rule"></a>Upravit pravidlo výstrahy
Můžete získat seznam všech pravidel výstrah v **výstrahy** nabídky v analýzy protokolů **nastavení**.  

![Správa výstrah](./media/log-analytics-alerts/configure.png)

1. Ve OMS konzoly vyberte **nastavení** dlaždici.
2. Vyberte **výstrahy**.

Z tohoto hlediska můžete provádět různé akce.

* Zakázat pravidlo výběrem **vypnout** vedle sebe.
* Upravte pravidlo výstrahy kliknutím na ikonu tužky vedle sebe.
* Odebrat pravidlo výstrahy kliknutím **X** Ikona vedle sebe. 

## <a name="details-of-alert-rules"></a>Podrobnosti o pravidla výstrah
Při vytvoření nebo úprava pravidla výstrahy na portálu OMS, pracujete s **přidat pravidlo výstrah** nebo **upravit pravidlo výstrahy** stránky.  Následující tabulky popisují pole na této obrazovce.

![Pravidlo výstrahy](media/log-analytics-alerts/add-alert-rule.png)

### <a name="alert-information"></a>Informace o výstrahách
Toto jsou základní nastavení pro pravidlo výstrahy a oznámení, která vytvoří.

| Vlastnost | Popis |
|:--- |:---|
| Název | Jedinečný název pro identifikaci pravidlo výstrahy. Tento název je součástí všech výstrah vytvořena pravidlem.  |
| Popis | Volitelný popis pravidla výstrahy. |
| Závažnost |Závažnost žádné výstrahy vytvořených tímto pravidlem. |

### <a name="search-query-and-time-window"></a>Vyhledávací dotaz a časového okna
Vyhledávací dotaz a čas okně, které vrátí záznamy, které se vyhodnocují k určení, pokud mají být vytvořeny žádné výstrahy.

| Vlastnost | Popis |
|:--- |:---|
| Vyhledávací dotaz | Toto je dotaz, který se má spustit.  Vrácené tímto dotazem záznamy se použije k určení, zda je vytvořena výstraha.<br><br>Vyberte **použít aktuální vyhledávací dotaz** aktuální dotaz nebo vyberte existující uložené hledání ze seznamu.  Syntaxe dotazu je uvedený v textovém poli, kde ji můžete upravit v případě potřeby. |
| Časový interval |Určuje časový rozsah pro dotaz.  Dotaz vrátí pouze záznamy, které byly vytvořeny v tomto rozsahu aktuální čas.  To může být libovolná hodnota 5 minut až 24 hodin.  To by měla být větší než nebo rovna hodnotě četnost výstrah.  <br><br> Například pokud časový interval je nastavena na 60 minut a spuštění dotazu: 15: 00, bude vrácen pouze záznamy vytvořené 12:15:00 až 1:15 hodin. |

Když poskytujete časový interval pro pravidlo výstrahy, zobrazí se počet existující záznamy, které by odpovídaly kritériím hledání pro dané časové okno.  Můžete určit frekvenci, který vám poskytne počet výsledků, které byste očekávali.

### <a name="schedule"></a>Plán
Definuje, jak často je spouštět vyhledávací dotaz.

| Vlastnost | Popis |
|:--- |:---|
| Frekvence výstrah | Určuje, jak často se má spustit dotaz. Může být libovolná hodnota 5 minut až 24 hodin. Musí být rovna nebo menší než časový interval.  Pokud hodnota je větší než časový interval, riskujete záznamů je vynechán.<br><br>Představte si třeba časové okno 30 minut a četnost 60 minut.  Pokud je v 1:00 spustit dotaz, vrátí záznamy 12:30 až 1:00 PM.  Při příštím spuštění dotazu by je 2:00, když měla by vrátit záznamy 1:30 až 2:00.  Všechny záznamy vytvořené 1:00 až 1:30 by nikdy vyhodnotí. |


### <a name="generate-alert-based-on"></a>Generovat výstrahu na základě
Definuje kritéria, která se vyhodnotí proti výsledky vyhledávání dotazu k určení, jestli by vytvořit výstrahu.  Tyto informace se liší v závislosti na typu pravidlo výstrahy, které vyberete.  Můžete získat podrobnosti o pro typy jiné pravidlo výstrahy z [vysvětlení výstrah v analýzy protokolů](log-analytics-alerts.md).

| Vlastnost | Popis |
|:--- |:---|
| Potlačit upozornění | Když zapnete potlačení pro pravidlo výstrahy, jsou zakázány akce pro pravidlo definované dobu, po vytvoření nového upozornění. Pravidlo je stále spuštěná a vytvoří výstrahy záznamů, pokud je splněna kritéria. Toto je tak, aby umožnily že čas k odstranění problému bez spuštění duplicitní akce. |

#### <a name="number-of-results-alert-rules"></a>Počet výsledků pravidla výstrah

| Vlastnost | Popis |
|:--- |:---|
| Počet výsledků |Výstraha se vytvoří, pokud je počet záznamů vrácených dotazem buď **větší než** nebo **menší než** hodnotu zadáte.  |

#### <a name="metric-measurement-alert-rules"></a>Pravidla výstrah metriky měření

| Vlastnost | Popis |
|:--- |:---|
| Agregovaná hodnota | Prahovou hodnotu, která každý agregovaná hodnota ve výsledcích musí být větší než bude považována za porušení. |
| Výstraha aktivační události na základě | Počet porušení u výstrahu, který se má vytvořit.  Můžete zadat **celkový počet narušení** pro libovolnou kombinaci narušení napříč výsledky nastavit nebo **po sobě jdoucích narušení** tak, aby vyžadovala, musí dojít k porušení v po sobě jdoucích vzorků. |

### <a name="actions"></a>Akce
Pravidla výstrah vždy vytvoří [výstrahy záznam](#alert-records) při splnění prahovou hodnotu.  Můžete také definovat jeden nebo více odpovědí ke spuštění jako e-mailu nebo spuštění sady runbook.



#### <a name="email-actions"></a>Akce e-mailu
E-mailu akce odeslání e-mailu s detaily výstrahy jednoho nebo více příjemců.

| Vlastnost | Popis |
|:--- |:---|
| E-mailové oznámení |Zadejte **Ano** Pokud chcete e-mailů, když je výstraha. |
| Předmět |Subjektu v e-mailu.  Tělo e-mailu se nedá změnit. |
| Příjemci |Adresy všech příjemců e-mailu.  Pokud zadáte více než jednu adresu, oddělte adresy středníkem (;). |

#### <a name="webhook-actions"></a>Akce Webhooku
Akce Webhooku umožňují vyvolání externího procesu prostřednictvím jedné žádosti HTTP POST.

| Vlastnost | Popis |
|:--- |:---|
| Webhook |Zadejte **Ano** Pokud chcete volat webhook, jehož při spuštění výstrahy. |
| Adresa URL Webhooku |Adresa URL webhooku. |
| Zahrnout vlastní datovou část JSON |Tuto možnost vyberte, pokud chcete nahradit datové části výchozí vlastní datovou část. |
| Zadejte vlastní datovou část JSON |Vlastní datovou část webhooku.  Najdete v předchozí části Podrobnosti. |

#### <a name="runbook-actions"></a>Akce sady Runbook
Runbook akce spuštění sady runbook ve službě Azure Automation. 

>[!NOTE]
> Musíte mít nainstalován v pracovním prostoru pro tuto akci, aby byl povolen řešení služby Automation. 


| Vlastnost | Popis |
|:--- |:---|
| Runbook | Zadejte **Ano** Pokud chcete spustit runbook služby automatizace Azure, když je výstraha.  |
| Účet Automation | Určuje účet Automation, jsou z vybrané sady runbook.  Toto je účet akce, který je propojený do pracovního prostoru. |
| Vyberte sadu runbook | Vyberte sadu runbook, který chcete spustit v případě, že se vytvoří výstraha. |
| Spusťte na | Vyberte **Azure** ke spuštění runbooku v cloudu.  Vyberte **hybridní pracovní proces** ke spuštění sady runbook na agenta s [hybridní pracovní proces Runbooku](../automation/automation-hybrid-runbook-worker.md ) nainstalována.  |




## <a name="next-steps"></a>Další postup
* Nainstalujte [řešení pro správu výstrah](log-analytics-solution-alert-management.md) k analýze výstrahy vytvořené v analýzy protokolů společně s shromáždit ze System Center Operations Manager (SCOM) výstrahy.
* Další informace o [protokolu hledání](log-analytics-log-searches.md) , mohou generovat výstrahy.
* Dokončete průvodce pro [konfigurace webook](log-analytics-alerts-webhooks.md) s pravidlo výstrahy.  
* Další informace o zápisu [sady runbook ve službě Azure Automation](https://azure.microsoft.com/documentation/services/automation) k nápravě problémů identifikovaný výstrahy.

