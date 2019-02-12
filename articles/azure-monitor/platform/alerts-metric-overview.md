---
title: Pochopit, jak metrika výstrahy práce ve službě Azure Monitor.
description: Získejte přehled, co můžete dělat s upozornění na metriku a jak fungují ve službě Azure Monitor.
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.subservice: alerts
ms.openlocfilehash: 3c7feda32bf162499888720ce56edac55197abe4
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005511"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Pochopit, jak metrika výstrahy práce ve službě Azure Monitor

Upozornění metrik ve službě Azure Monitor pracovat nad vícedimenzionálních metrik. Tyto metriky můžou být metriky platformy [vlastní metriky](../../azure-monitor/platform/metrics-custom-overview.md), [oblíbených protokolů ze služby Azure Monitor převést na metriky](../../azure-monitor/platform/alerts-metric-logs.md), standardních metrik Application Insights. Upozornění metrik hodnocení v pravidelných intervalech a zkontrolujte, zda podmínky na jeden nebo více metrika časových řad jsou true a vás upozorní, pokud se splní hodnocení. Upozornění na metriky jsou stavová, to znamená, že pouze odeslat oznámení při změně stavu.

## <a name="how-do-metric-alerts-work"></a>Jak fungují upozornění na metriku

Pravidlo upozornění metriky můžete definovat tak, že zadáte cílový prostředek, které se mají monitorovat, název metriky, typ podmínky (statická nebo dynamická) a podmínky (operátor a prahová hodnota citlivosti) a skupinu akcí, až se spustí, když se pravidlo upozornění aktivuje. Typy podmínek ovlivňují způsob, jakým se určují prahové hodnoty. [Další informace o možnostech typu a citlivosti podmínku dynamickými prahovými hodnotami](alerts-dynamic-thresholds.md).

### <a name="alert-rule-with-static-condition-type"></a>Pravidlo upozornění s typem statická podmínka

Řekněme, že jste vytvořili pravidlo metriky upozornění jednoduché statické prahové hodnoty následujícím způsobem:

- Cílový prostředek (Azure prostředků, kterou chcete monitorovat): myVM
- Metrika: Procento CPU
- Typ podmínky: Statická
- Časová agregace (Statistika, která se spouští nad nezpracované hodnoty metrik. Podporované času agregace jsou Min, Max, Avg, celkem): Průměr
- Období (vzhled back okno, přes které metriky jsou kontrolovány hodnoty): Za posledních 5 minut.
- Frequency (četnost, se kterým zkontroluje upozornění metriky, pokud jsou splněny podmínky): 1 min.
- Operátor: Větší než
- Prahová hodnota: 70

Od doby, kdy se vytvoří pravidlo upozornění monitorování spouští každou 1 minutu a prohledá hodnoty metrik za posledních 5 minut a zkontroluje, pokud průměr těchto hodnot, které je vyšší než 70. Pokud tedy je splněna podmínka, průměrné procento CPU za posledních 5 minut vyšší než 70, pravidlo upozornění aktivuje aktivovaná upozornění. Pokud jste nakonfigurovali e-mailu nebo akce hook webové do skupiny akcí přidružené pravidlo upozornění, zobrazí se aktivovaná upozornění u obou.

### <a name="alert-rule-with-dynamic-condition-type"></a>Pravidlo upozornění s typem dynamická podmínka

Řekněme, že jste vytvořili jednoduchý metriky upozornění pravidla dynamickými prahovými hodnotami následujícím způsobem:

- Cílový prostředek (Azure prostředků, kterou chcete monitorovat): myVM
- Metrika: Procento CPU
- Typ podmínky: Dynamická
- Časová agregace (Statistika, která se spouští nad nezpracované hodnoty metrik. Podporované času agregace jsou Min, Max, Avg, celkem): Průměr
- Období (vzhled back okno, přes které metriky jsou kontrolovány hodnoty): Za posledních 5 minut.
- Frequency (četnost, se kterým zkontroluje upozornění metriky, pokud jsou splněny podmínky): 1 min.
- Operátor: Větší než
- Citlivost: Střednědobé používání
- Vzhled Back období: 4
- Počet porušení zásad: 4

Jakmile se vytvoří pravidlo upozornění, dynamickými prahovými hodnotami algoritmu strojového učení se získat historická data, která je k dispozici, vypočítá prahovou hodnotu, která nejlíp odpovídá vzoru chování řady metrik a průběžně se další závislosti na nová data, aby Prahová hodnota přesnější.

Od doby, kdy se vytvoří pravidlo upozornění monitorování spouští každou 1 minutu a prohledá hodnoty metrik za posledních 20 minut, které jsou seskupeny do období 5 minut a zkontroluje, pokud průměr hodnot období v každé 4 období překročí prahovou hodnotu očekávané. Pokud tedy je splněna podmínka, průměrné procento CPU za posledních 20 minut (čtyři období 5 minut) se od chování je očekávané čtyřikrát, pravidlo upozornění aktivuje aktivovaná upozornění. Pokud jste nakonfigurovali e-mailu nebo akce hook webové do skupiny akcí přidružené pravidlo upozornění, zobrazí se aktivovaná upozornění u obou.

### <a name="view-and-resolution-of-fired-alerts"></a>Zobrazení a řešení aktivovaná upozornění

Výše uvedené příklady pravidel upozornění ohlásí je možné zobrazit i na webu Azure Portal v **všechny výstrahy** okno.

Dejme tomu, že využití na "myVM" pokračuje v následné kontroly je nad prahovou hodnotou, pravidlo upozornění nebude znovu vyvolat, dokud nebudou vyřešeny podmínky.

Zopakovat později, pokud využití na "myVM" se vrátí zpět na normální tedy neklesne pod prahovou hodnotou. Pravidlo výstrahy monitoruje podmínku pro ještě dvakrát, odeslat oznámení o vyřešené. Pravidlo upozornění odešle zprávu s přeložit/deaktivaci pro tři po sobě jdoucí tečky ke snížení šumu v případě netřepotá podmínek není splněna se vyskytl výstražný stav.

Jak vyřešit oznámení se posílá navýšení kapacity pomocí webhooků a e-mailu, stav výstrahy instance (označované jako stav monitorování) na webu Azure portal je také nastavena na vyřešený.

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Monitorování ve velkém měřítku pomocí upozornění na metriky ve službě Azure Monitor

### <a name="using-dimensions"></a>Pomocí dimenzí

Upozornění metrik ve službě Azure Monitor také podporovat monitorování několika kombinace hodnot dimenze s jedním pravidlem. Nyní se pokusíme pochopit, proč může používat více kombinace dimenzí s nám pomůže následující příklad.

Řekněme, že máte plán služby App Service pro váš web. Chcete monitorovat využití procesoru ve více instancích spuštění vaší webové stránky nebo aplikace. Můžete to udělat pomocí metrik pravidlo upozornění následujícím způsobem:

- Cílový prostředek: myAppServicePlan
- Metrika: Procento CPU
- Typ podmínky: Statická
- Dimenze
  - Instance = InstanceName1, InstanceName2
- Časová agregace: Průměr
- Období: Za posledních 5 minut.
- Frekvence: 1 min.
- Operátor: GreaterThan
- Prahová hodnota: 70

Stejně jako dříve, toto pravidlo monitoruje Pokud průměrné využití procesoru po dobu posledních 5 minut vyšší než 70 %. Pomocí stejného pravidla však můžete sledovat dvě instance provozovat svůj web. Každá instance se získat jednotlivě monitorovat a budete dostávat oznámení jednotlivě.

Řekněme, že máte webovou aplikaci, která zaznamenává masivní poptávky a budete muset přidat další instance. Výše uvedené pravidlo pořád sleduje pouze dvě instance. Můžete však vytvořit pravidlo následujícím způsobem:

- Cílový prostředek: myAppServicePlan
- Metrika: Procento CPU
- Typ podmínky: Statická
- Dimenze
  - Instance = *
- Časová agregace: Průměr
- Období: Za posledních 5 minut.
- Frekvence: 1 min.
- Operátor: GreaterThan
- Prahová hodnota: 70

Toto pravidlo bude automaticky sledovat všechny hodnoty pro instanci tj vaše instance můžete sledovat, jak objevují aniž byste museli upravovat vaše metriky pravidlo upozornění znovu.

Při monitorování více dimenzí, dynamickými prahovými hodnotami, které můžete vytvořit pravidlo upozornění přizpůsobené prahové hodnoty pro stovky metriky řad najednou. Dynamické prahové hodnoty za následek méně pravidel upozornění pro správu a spoustu času ukládá na správu a vytváření pravidel upozornění.

Řekněme, že můžete mít třeba webovou aplikaci s více instancemi a nevíte, co je nejvhodnější prahovou hodnotu. Výše uvedených pravidel bude vždy používat prahovou hodnotu 70 %. Můžete však vytvořit pravidlo následujícím způsobem:

- Cílový prostředek: myAppServicePlan
- Metrika: Procento CPU
- Typ podmínky: Dynamická
- Dimenze
  - Instance = *
- Časová agregace: Průměr
- Období: Za posledních 5 minut.
- Frekvence: 1 min.
- Operátor: GreaterThan
- Citlivost: Střednědobé používání
- Vzhled Back období: 1
- Počet porušení zásad: 1

Toto pravidlo monitoruje Pokud průměrné využití procesoru po dobu posledních 5 minut vyšší než očekávané chování u každé instance. Stejné pravidlo, že instance můžete sledovat, jak objevují aniž byste museli upravovat vaše metriky pravidlo upozornění znovu. Každá instance bude získat prahovou hodnotu, která odpovídá vzoru chování řady metrik a se neustále mění v závislosti na nová data se zpřesnit prahovou hodnotu. Stejně jako dříve, každá instance bude monitorovat jednotlivě a budete dostávat oznámení jednotlivě.

Zvýšení období zpět vzhled a počet porušení zásad můžete také povolit filtrování výstrah pouze upozornění na vaší definice významné odchylky. [Další informace o dynamické prahové hodnoty pokročilé možnosti](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean).

### <a name="monitoring-multiple-resources-using-metric-alerts"></a>Monitorování více zdrojů pomocí upozornění na metriku

Jak jste viděli v předchozí části, je možné mít jediné pravidlo metriky výstrahy, které monitoruje každá kombinace jednotlivých dimenzí (např.) metriky časové řady). Ale dříve jste byli stále omezena na provádění po jednom jeden prostředek. Azure Monitor podporuje také monitorování více prostředků s jedním pravidlem upozornění metriky. Tato funkce je aktuálně ve verzi preview a jenom na podporovaných virtuálních počítačích. Navíc jeden upozornění na metriku můžete monitorovat prostředky v jedné oblasti Azure.

Můžete určit rozsah monitorování podle jednoho upozornění na metriku v jednom ze tří způsobů:

- jako seznam virtuálních počítačů v jedné oblasti Azure v rámci předplatného
- všechny virtuální počítače (v jedné oblasti Azure) v jedné nebo více skupin prostředků v rámci předplatného
- všechny virtuální počítače (v jedné oblasti Azure) v jednom předplatném

Vytváření pravidla upozornění na metriky, které monitorují několik prostředků není aktuálně podporováno prostřednictvím webu Azure portal. Můžete vytvořit tato pravidla pomocí [šablon Azure Resource Manageru](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-metric-alert-that-monitors-multiple-resources). Zobrazí se jednotlivá oznámení pro každý virtuální počítač.

## <a name="typical-latency"></a>Typické latence

Pro upozornění metrik obvykle dostanete oznámení za méně než 5 minut Pokud nastavíte pravidlo upozornění frekvence bude 1 min. V případě velkého zatížení pro systémy oznámení případech může se zobrazit delší latence.

## <a name="supported-resource-types-for-metric-alerts"></a>Podporované typy prostředků pro upozornění na metriku

Úplný seznam podporovaných typů prostředků najdete v tomto [článku](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

Pokud používáte klasického upozornění metrik ještě dnes a chcete zjistit, pokud podporují upozornění na metriku všechny typy prostředků, který používáte, následující tabulka ukazuje na prostředek typy podporovaných klasického upozornění metrik a pokud se podporuje upozornění metrik ještě dnes nebo ne.

|Typ prostředku podporuje klasického upozornění metrik | Podporuje upozornění na metriku |
|-------------------------------------------------|----------------------------|
| Microsoft.ApiManagement/service | Ano |
| Microsoft.Batch/batchAccounts| Ano|
|Microsoft.Cache/redis| Ano
|Microsoft.ClassicCompute/virtualMachines | Ne |
|Microsoft.ClassicCompute/domainNames/slots/roles | Ne|
|Microsoft.CognitiveServices/accounts | Ne |
|Microsoft.Compute/virtualMachines | Ano|
|Microsoft.Compute/virtualMachineScaleSets| Ano|
|Microsoft.ClassicStorage/storageAccounts| Ne |
|Microsoft.DataFactory/datafactories | Ano|
|Microsoft.DBforMySQL/servers| Ano|
|Microsoft.DBforPostgreSQL/servers| Ano|
|Microsoft.Devices/IotHubs | Ne|
|Microsoft.DocumentDB/databaseAccounts| Ne|
|Microsoft.EventHub/namespaces | Ano|
|Microsoft.Logic/workflows | Ano|
|Microsoft.Network/loadBalancers |Ano|
|Microsoft.Network/publicIPAddresses| Ano|
|Microsoft.Network/applicationGateways| Ano|
|Microsoft.Network/expressRouteCircuits| Ano|
|Microsoft.Network/trafficManagerProfiles | Ano|
|Microsoft.Search/searchServices | Ne|
|Microsoft.ServiceBus/namespaces| Ne|
|Microsoft.Storage/storageAccounts | Ano|
|Microsoft.StreamAnalytics/streamingjobs| Ano|
|Microsoft.TimeSeriesInsights/environments | Ano|
|Microsoft Web/serverfarms | Ano |
|Microsoft Weby (s výjimkou funkce) | Ano|
|Microsoft Web/hostingEnvironments/multiRolePools | Ne|
|Microsoft Web/hostingEnvironments/workerPools| Ne
|Microsoft.SQL/Servers | Ne|

## <a name="next-steps"></a>Další postup

- [Zjistěte, jak vytvořit, zobrazit a spravovat upozornění metrik v Azure](alerts-metric.md)
- [Zjistěte, jak nasadit upozornění na metriku pomocí šablony Azure Resource Manageru](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [Další informace o skupinách akcí](action-groups.md)
- [Další informace o typ podmínky dynamickými prahovými hodnotami](alerts-dynamic-thresholds.md)
