---
title: Pochopit, jak metrika výstrahy práce ve službě Azure Monitor.
description: Získejte přehled, co můžete dělat s upozornění na metriku a jak fungují ve službě Azure Monitor.
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.component: alerts
ms.openlocfilehash: 8eb23528391ffeccdad6ba92345d28b31e18f0a3
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322096"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Pochopit, jak metrika výstrahy práce ve službě Azure Monitor

Upozornění metrik ve službě Azure Monitor pracovat nad vícedimenzionálních metrik. Tyto metriky můžou být metriky platformy [vlastní metriky](../../azure-monitor/platform/metrics-custom-overview.md), [oblíbených protokolů ze služby Log Analytics převést na metriky](../../monitoring-and-diagnostics/monitoring-metric-alerts-logs.md), standardních metrik Application Insights. Upozornění metrik hodnocení v pravidelných intervalech a zkontrolujte, zda podmínky na jeden nebo více metrika časových řad jsou true a vás upozorní, pokud se splní hodnocení. Upozornění na metriky jsou stavová, to znamená, že pouze odeslat oznámení při změně stavu.

## <a name="how-do-metric-alerts-work"></a>Jak fungují upozornění na metriku

Pravidlo upozornění metriky můžete definovat tak, že zadáte název sledované, metriky a podmínku (operátor a prahové hodnoty) a skupinu akcí, až se spustí, když se pravidlo upozornění aktivuje cílový prostředek.
Řekněme, že jste vytvořili jednoduchý metriky pravidlo upozornění následujícím způsobem:

- Cílový prostředek (Azure prostředků, kterou chcete monitorovat): myVM
- Metrika: Procento CPU
- Časová agregace (Statistika, která se spouští nad nezpracované hodnoty metrik. Podporované času agregace jsou Min, Max, Avg, celkem): Průměr
- Období (vzhled back okno, přes které metriky jsou kontrolovány hodnoty):      Za posledních 5 minut.
- Frequency (četnost, se kterým zkontroluje upozornění metriky, pokud jsou splněny podmínky): 1 min.
- Operátor:     Větší než
- Prahová hodnota:      70

Od doby, kdy se vytvoří pravidlo upozornění monitorování spouští každou 1 minutu a prohledá hodnoty metrik za posledních 5 minut a zkontroluje, pokud průměr těchto hodnot, které je vyšší než 70. Pokud tedy je splněna podmínka, průměrné procento CPU za posledních 5 minut vyšší než 70, pravidlo upozornění aktivuje aktivovaná upozornění. Pokud jste nakonfigurovali e-mailu nebo akce hook webové do skupiny akcí přidružené pravidlo upozornění, zobrazí se aktivovaná upozornění u obou.

Tuto konkrétní instanci jeho spuštění pravidla upozornění můžete zobrazit také na webu Azure Portal v okně všechny výstrahy.

Řekněme využití na "myVM" pokračuje v následné kontroly je nad prahovou hodnotou, pravidlo upozornění nebude znovu vyvolat, dokud podmínka nebude vyřešen.

Po ukončení později, pokud využití na "myVM" se vrátí zpět na normální tedy pod zadanou prahovou hodnotu. Pravidlo výstrahy monitoruje podmínku pro ještě dvakrát, odeslat oznámení o vyřešené. Pravidlo upozornění odešle zprávu s přeložit/deaktivaci pro tři po sobě jdoucí tečky ke snížení šumu v případě netřepotá podmínek není splněna se vyskytl výstražný stav.

Jak vyřešit oznámení se posílá navýšení kapacity pomocí webhooků a e-mailu, stav výstrahy instance (označované jako stav monitorování) na webu Azure portal je také nastavena na vyřešený.

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Monitorování ve velkém měřítku pomocí upozornění na metriky ve službě Azure Monitor

### <a name="using-dimensions"></a>Pomocí dimenzí

Upozornění metrik ve službě Azure Monitor také podporovat monitorování několika kombinace hodnot dimenze s jedním pravidlem. Nyní se pokusíme pochopit, proč může používat více kombinace dimenzí s nám pomůže následující příklad.

Řekněme, že máte plán služby App Service pro váš web. Chcete monitorovat využití procesoru ve více instancích spuštění vaší webové stránky nebo aplikace. Můžete to udělat následujícím způsobem pravidlo upozornění metriky

- Cílový prostředek: myAppServicePlan
- Metrika: Procento CPU
- Dimenze
  - Instance = InstanceName1 InstanceName2
- Časová agregace: Průměr
- Období: Za posledních 5 minut.
- Frekvence: 1 min.
- Operátor: GreaterThan
- Prahová hodnota: 70

Stejně jako dříve, toto pravidlo monitoruje Pokud průměrné využití procesoru po dobu posledních 5 minut vyšší než 70 %. Pomocí stejného pravidla však můžete sledovat dvě instance provozovat svůj web. Každá instance se získat jednotlivě monitorovat a budete dostávat oznámení jednotlivě.

Řekněme, budete mít webovou aplikaci, která zaznamenává masivní poptávky a budete muset přidat další instance. Výše uvedené pravidlo pořád sleduje pouze dvě instance. Můžete však vytvořit pravidlo následujícím způsobem.

- Cílový prostředek: myAppServicePlan
- Metrika: Procento CPU
- Dimenze
  - Instance = *
- Časová agregace: Průměr
- Období: Za posledních 5 minut.
- Frekvence: 1 min.
- Operátor: GreaterThan
- Prahová hodnota: 70

Toto pravidlo bude automaticky sledovat všechny hodnoty pro instanci tj vaše instance můžete sledovat, jak objevují aniž byste museli upravovat vaše metriky pravidlo upozornění znovu.

### <a name="monitoring-multiple-resources-using-metric-alerts"></a>Monitorování více zdrojů pomocí upozornění na metriku

Jak jste viděli v předchozí části, je možné mít jediné pravidlo metriky výstrahy, které monitoruje každá kombinace jednotlivých dimenzí (např.) metriky časové řady). Ale dříve jste byli stále omezena na provádění po jednom jeden prostředek. Azure Monitor podporuje také monitorování více prostředků s jedním pravidlem upozornění metriky. Tato funkce je aktuálně ve verzi preview a jenom na podporovaných virtuálních počítačích. Navíc jeden upozornění na metriku můžete monitorovat prostředky v jedné oblasti Azure.

Můžete určit rozsah monitorování podle jednoho upozornění na metriku v jednom ze tří způsobů:

- jako seznam virtuálních počítačů v jedné oblasti Azure v rámci předplatného
- všechny virtuální počítače (v jedné oblasti Azure) v jedné nebo více skupin prostředků v rámci předplatného
- všechny virtuální počítače (v jedné oblasti Azure) v jednom předplatném

Vytváření pravidla upozornění na metriky, které monitorují několik prostředků není aktuálně podporováno prostřednictvím webu Azure portal. Můžete vytvořit tato pravidla pomocí [šablon Azure Resource Manageru](../../monitoring-and-diagnostics/monitoring-create-metric-alerts-with-templates.md#resource-manager-template-for-metric-alert-that-monitors-multiple-resources). Zobrazí se jednotlivá oznámení pro každý virtuální počítač. 

## <a name="typical-latency"></a>Typické latence

Pro upozornění metrik obvykle dostanete oznámení za méně než 5 minut Pokud nastavíte pravidlo upozornění frekvence bude 1 min. V případě velkého zatížení pro systémy oznámení případech může se zobrazit delší latence.

## <a name="supported-resource-types-for-metric-alerts"></a>Podporované typy prostředků pro upozornění na metriku

Úplný seznam podporovaných typů prostředků najdete v tomto [článku](../../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported).

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
|Microsoft.Storage/storageAccounts. | Ano|
|Microsoft.StreamAnalytics/streamingjobs| Ano|
|Microsoft.TimeSeriesInsights/environments | Ano|
|Společnosti Microsoft. Web/serverových farem | Ano |
|Společnosti Microsoft. Weby (s výjimkou funkce) | Ano|
|Společnosti Microsoft. Web/hostingEnvironments/multiRolePools | Ne|
|Společnosti Microsoft. Web/hostingEnvironments/workerPools| Ne
|Microsoft.SQL/Servers | Ne|

## <a name="next-steps"></a>Další postup

- [Zjistěte, jak vytvořit, zobrazit a spravovat upozornění metrik v Azure](alerts-metric.md)
- [Zjistěte, jak nasadit upozornění na metriku pomocí šablony Azure Resource Manageru](../../monitoring-and-diagnostics/monitoring-create-metric-alerts-with-templates.md)
- [Další informace o skupinách akcí](action-groups.md)
