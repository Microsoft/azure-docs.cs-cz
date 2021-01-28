---
title: Řešení potíží s orchestrací kanálu a triggery v Azure Data Factory
description: K řešení potíží s aktivací kanálu v Azure Data Factory použijte různé metody.
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 1a5f665627da1b08ec57b04863a58f227c673af4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944908"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Řešení potíží s orchestrací kanálu a triggery v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Spuštění kanálu v Azure Data Factory definuje instanci spuštění kanálu. Řekněme například, že máte kanál, který běží v 8:00 AM, 9:00 a 10:00. V tomto případě existují tři samostatná spuštění kanálu. Každé spuštění kanálu má jedinečné ID spuštění. ID spuštění je globálně jedinečný identifikátor (GUID), který definuje konkrétní spuštění kanálu.

Instance spuštění kanálu se obvykle vytvářejí předáváním argumentů do parametrů, které definujete v kanálech. Kanál můžete spustit buď ručně, nebo pomocí triggeru. Podrobnosti najdete [v tématu spuštění kanálu a triggery v Azure Data Factory](concepts-pipeline-execution-triggers.md) .

## <a name="common-issues-causes-and-solutions"></a>Běžné problémy, příčiny a řešení

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>Azure Functions kanál aplikace vyvolá chybu s připojením privátního koncového bodu.
 
Máte Data Factory a aplikace funkce Azure běží na privátním koncovém bodu. Pokoušíte se spustit kanál, který komunikuje s aplikací Function App. Vyzkoušeli jste tři různé metody, ale jedna vrátí chybu "špatný požadavek" a ostatní dvě metody vrátí "103 chyba zakázáno".

**Příčina**: Data Factory v současné době nepodporuje pro aplikace Function App konektor privátního koncového bodu. Azure Functions odmítne volání, protože je nakonfigurována tak, aby povolovala pouze připojení z privátního odkazu.

**Řešení**: Vytvořte koncový bod **PrivateLinkService** a poskytněte službě DNS vaší aplikace Function App.

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>Spuštění kanálu se zrušilo, ale monitor stále zobrazuje stav průběhu.

Když zrušíte spuštění kanálu, monitorování kanálu často zobrazuje stav průběhu. K tomu dojde kvůli problému s mezipamětí prohlížeče. Také možná nemáte správné filtry monitorování.

**Řešení**: aktualizujte prohlížeč a použijte správné filtry monitorování.
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>Při kopírování kanálu se zobrazí chyba "DelimitedTextMoreColumnsThanDefined".
 
Pokud složka, kterou kopírujete, obsahuje soubory s různými schématy, jako je například proměnný počet sloupců, jiné oddělovače, nastavení znaků uvozovek nebo některý problém s daty, může Data Factory kanál vyvolat tuto chybu:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**Řešení**: při vytváření aktivity kopírování vyberte možnost **binární kopírování** . Tímto způsobem se v případě hromadného kopírování nebo migrace dat z jednoho data Lake na jiný Data Factory neotevřou soubory pro čtení schématu. Místo toho Data Factory zpracuje každý soubor jako binární a zkopíruje ho do druhého umístění.

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime"></a>Spuštění kanálu se při dosažení limitu kapacity prostředí Integration runtime nezdařilo.

Chybová zpráva:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**Příčina**: dosáhli jste limitu kapacity prostředí Integration runtime. Je možné, že budete provozovat velké množství toku dat pomocí stejného prostředí Integration runtime ve stejnou dobu. Podrobnosti najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#version-2) .

**Řešení**:
 
- Spusťte své kanály v různých časech triggerů.
- Vytvořte nový prostředí Integration runtime a rozdělte kanály do několika prostředí Integration runtime.

### <a name="you-have-activity-level-errors-and-failures-in-pipelines"></a>Máte chyby na úrovni aktivity a chyby v kanálech.

Azure Data Factory orchestrace umožňuje podmíněnou logiku a umožňuje uživatelům provádět různé cesty na základě výsledku předchozí aktivity. Umožňuje čtyři podmíněné cesty: **po úspěšném** dokončení (výchozí průchod) **po neúspěšném** **dokončení** a **Při přeskočení**. 

Azure Data Factory vyhodnocuje výsledek všech aktivit na úrovni listu. Výsledky kanálu jsou úspěšné pouze v případě, že všechny pokusy pozůstávají úspěšné. Pokud se koncová aktivita přeskočí, vyhodnotí se místo toho její Nadřazená aktivita. 

**Řešení**

1. Implementujte kontroly na úrovni aktivity podle následujících pokynů, [jak zpracovat selhání a chyby kanálu](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
1. Pomocí Azure Logic Apps můžete sledovat kanály v pravidelných intervalech po [dotazech podle továrny](/rest/api/datafactory/pipelineruns/querybyfactory).

## <a name="monitor-pipeline-failures-in-regular-intervals"></a>Monitorování selhání kanálu v pravidelných intervalech

Možná budete muset monitorovat neúspěšné Data Factory kanály v intervalech, 5 minut. Pomocí koncového bodu se můžete dotazovat a filtrovat spouštění kanálů z datové továrny. 

Nastavte aplikaci logiky Azure pro dotazování všech neúspěšných kanálů každých 5 minut, jak je popsáno v tématu [dotazování podle továrny](/rest/api/datafactory/pipelineruns/querybyfactory). Potom můžete hlásit incidenty do našeho systému lístků.

Další informace najdete v [části odesílání oznámení z Data Factory, část 2](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/).

## <a name="next-steps"></a>Další kroky

Pro další nápovědu k řešení potíží zkuste tyto prostředky:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stránka s otázkou Microsoft Q&](/answers/topics/azure-data-factory.html)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)