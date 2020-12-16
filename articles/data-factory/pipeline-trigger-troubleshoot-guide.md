---
title: Řešení potíží s orchestrací kanálu a triggery v Azure Data Factory
description: K řešení potíží s aktivací kanálu v Azure Data Factory použijte různé metody.
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 0e67a316b012eda61607c84edfd8e10d6aa3318d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589147"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Řešení potíží s orchestrací kanálu a triggery v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Spuštění kanálu v Azure Data Factory definuje instanci spuštění kanálu. Máte například kanál, který se spouští v 8:00 dop. 9:00 AM a 10:00. V tomto případě existují tři samostatná spuštění kanálu nebo spuštění kanálu. Každé spuštění kanálu má jedinečné ID spuštění. ID spuštění je identifikátor GUID (globálně jedinečný identifikátor), který definuje konkrétní spuštění kanálu.

Instance spuštění kanálu se obvykle vytvářejí předáváním argumentů do parametrů, které definujete v kanálech. Kanál můžete spustit ručně nebo prostřednictvím aktivační události. Podrobnosti najdete [v tématu spuštění kanálu a triggery v Azure Data Factory](concepts-pipeline-execution-triggers.md) .

## <a name="common-issues-causes-and-solutions"></a>Běžné problémy, příčiny a řešení

### <a name="pipeline-with-azure-function-throws-error-with-private-end-point-connectivity"></a>Kanál s funkcí Azure Functions vyvolá chybu s privátním připojením koncového bodu.
 
#### <a name="issue"></a>Problém
V případě nějakého kontextu máte Data Factory a Azure Function App běžet na privátním koncovém bodu. Snažíte se získat kanál, který komunikuje s Function App Azure pro práci. Vyzkoušeli jste tři různé metody, ale jedna z nich vrátí chybu `Bad Request` , další dvě metody vrátí `103 Error Forbidden` .

#### <a name="cause"></a>Příčina 
Data Factory v současné době nepodporuje pro Azure Function App konektor privátního koncového bodu. A mělo by to být důvod, proč Azure Function App odmítl volání, protože by byl nakonfigurovaný tak, aby povoloval jenom připojení z privátního odkazu.

#### <a name="resolution"></a>Řešení
Můžete vytvořit soukromý koncový bod typu **PrivateLinkService** a zadat DNS vaší aplikace Function App a připojení by mělo fungovat.

### <a name="pipeline-run-is-killed-but-the-monitor-still-shows-progress-status"></a>Běh kanálu byl ukončen, ale monitor stále zobrazuje stav průběhu.

#### <a name="issue"></a>Problém
Při ukončení běhu kanálu se často v monitorování kanálu stále zobrazuje stav průběhu. K tomu dochází kvůli problému s mezipamětí v prohlížeči a nemáte správné filtry pro monitorování.

#### <a name="resolution"></a>Řešení
Aktualizujte prohlížeč a použijte pro monitorování filtry vpravo.
 
### <a name="copy-pipeline-failure--found-more-columns-than-expected-column-count-delimitedtextmorecolumnsthandefined"></a>Kopírování selhání kanálu – našlo se víc sloupců, než se očekával počet sloupců (DelimitedTextMoreColumnsThanDefined).

#### <a name="issue"></a>Problém  
Pokud soubory v rámci konkrétní složky, kterou kopírujete, obsahují soubory s různými schématy, jako je například proměnlivý počet sloupců, různé oddělovače, nastavení znak uvozovek nebo nějaký problém s daty, kanál Data Factory bude spuštěn v této chybě:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

#### <a name="resolution"></a>Řešení
Při vytváření aktivity Kopírování dat vyberte možnost binární kopírování. Tímto způsobem můžete pro hromadné kopírování nebo migraci dat z jednoho Data Lake do druhé s možností **binary** Data Factory otevřít soubory pro čtení schématu, ale stačí považovat každý soubor za binární a zkopírovat je do jiného umístění.

### <a name="pipeline-run-fails-when-capacity-limit-of-integration-runtime-is-reached"></a>Spuštění kanálu se nepovede, když se dosáhne limitu kapacity prostředí Integration runtime.

#### <a name="issue"></a>Problém
Chybová zpráva:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

Tato chyba označuje omezení za prostředí Integration runtime, které je aktuálně 50. Podrobnosti najdete v tématu [omezení](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#version-2) .

Pokud spustíte velké množství toku dat pomocí stejného prostředí Integration runtime současně, může to způsobit tento druh chyby.

#### <a name="resolution"></a>Řešení 
- Oddělte tyto kanály pro různé doby triggeru, které se mají spustit.
- Vytvořte nový prostředí Integration runtime a rozdělte tyto kanály v rámci více prostředí Integration runtime.

### <a name="how-to-monitor-pipeline-failures-on-regular-interval"></a>Jak monitorovat selhání kanálu v pravidelných intervalech

#### <a name="issue"></a>Problém
Často je potřeba monitorovat kanály Data Factory v intervalech, což znamená 5 minut. Pomocí koncového bodu se můžete dotazovat a filtrovat spouštění kanálů z datové továrny. 

#### <a name="recommendation"></a>Doporučení
1. Nastavte aplikaci logiky Azure pro dotazování všech neúspěšných kanálů každých 5 minut.
2. Potom můžete hlásit incidenty do našeho systému lístků podle [QueryByFactory](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory).

#### <a name="reference"></a>Referenční informace
- [Externí – odesílání oznámení z Data Factory](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/)

### <a name="how-to-handle-activity-level-errors-and-failures-in-pipelines"></a>Postup zpracování chyb na úrovni aktivity a selhání v kanálech

#### <a name="issue"></a>Problém
Azure Data Factory orchestrace umožňuje podmíněnou logiku a umožňuje uživateli v závislosti na výsledcích předchozí aktivity provádět různé cesty. Umožňuje čtyři podmíněné cesty: "po úspěchu (výchozí průchod)", "při selhání", "po dokončení" a "Při přeskočení". Používání různých cest je povoleno.

Azure Data Factory definuje úspěšné a neúspěšné spuštění kanálu následujícím způsobem:

- Vyhodnotit výsledek pro všechny aktivity na úrovni listu. Pokud se koncová aktivita přeskočí, vyhodnotí se místo toho její Nadřazená aktivita.
- Výsledek kanálu je úspěšný pouze v případě, že všechny ponechují úspěšné.

#### <a name="recommendation"></a>Doporučení
- Implementujte kontroly úrovně aktivity za [účelem zpracování selhání a chyb kanálu](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
- Pomocí aplikace Azure Logic Apps můžete kanály monitorovat v pravidelných intervalech po [dotazování pomocí DataFactory]( https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory).

## <a name="next-steps"></a>Další kroky

Pro další nápovědu k řešení potíží zkuste tyto prostředky:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stránka s otázkou Microsoft Q&](/answers/topics/azure-data-factory.html)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)