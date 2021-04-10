---
title: Řešení potíží s orchestrací kanálu a triggery v Azure Data Factory
description: K řešení potíží s aktivací kanálu v Azure Data Factory použijte různé metody.
author: ssabat
ms.service: data-factory
ms.date: 03/13/2021
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 72f2a5eec25b9acc2aedd7b006fe3380141781c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563408"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Řešení potíží s orchestrací kanálu a triggery v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Spuštění kanálu v Azure Data Factory definuje instanci spuštění kanálu. Řekněme například, že máte kanál, který běží v 8:00 AM, 9:00 a 10:00. V tomto případě existují tři samostatná spuštění kanálu. Každé spuštění kanálu má jedinečné ID spuštění. ID spuštění je globálně jedinečný identifikátor (GUID), který definuje konkrétní spuštění kanálu.

Instance spuštění kanálu se obvykle vytvářejí předáváním argumentů do parametrů, které definujete v kanálech. Kanál můžete spustit buď ručně, nebo pomocí triggeru. Podrobnosti najdete [v tématu spuštění kanálu a triggery v Azure Data Factory](concepts-pipeline-execution-triggers.md) .

## <a name="common-issues-causes-and-solutions"></a>Běžné problémy, příčiny a řešení

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>Azure Functions kanál aplikace vyvolá chybu s připojením privátního koncového bodu.
 
Máte Data Factory a aplikace funkce Azure běží na privátním koncovém bodu. Pokoušíte se spustit kanál, který komunikuje s aplikací Function App. Vyzkoušeli jste tři různé metody, ale jedna vrátí chybu "špatný požadavek" a ostatní dvě metody vrátí "103 chyba zakázáno".

**Příčina**

Data Factory v současné době nepodporuje pro aplikace Function App konektor privátního koncového bodu. Azure Functions odmítne volání, protože je nakonfigurována tak, aby povolovala pouze připojení z privátního odkazu.

**Řešení**

Vytvořte koncový bod **PrivateLinkService** a poskytněte službě DNS vaší aplikace Function App.

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>Spuštění kanálu se zrušilo, ale monitor stále zobrazuje stav průběhu.

**Příčina**

Když zrušíte spuštění kanálu, monitorování kanálu často zobrazuje stav průběhu. K tomu dojde kvůli problému s mezipamětí prohlížeče. Také možná nemáte správné filtry monitorování.

**Řešení**

Aktualizujte prohlížeč a použijte správné filtry monitorování.
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>Při kopírování kanálu se zobrazí chyba "DelimitedTextMoreColumnsThanDefined".
 
 **Příčina**
 
Pokud složka, kterou kopírujete, obsahuje soubory s různými schématy, jako je například proměnný počet sloupců, jiné oddělovače, nastavení znaků uvozovek nebo některý problém s daty, může Data Factory kanál vyvolat tuto chybu:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**Řešení**

Při vytváření aktivity kopírování vyberte možnost **binární kopírování** . Tímto způsobem se v případě hromadného kopírování nebo migrace dat z jednoho data Lake na jiný Data Factory neotevřou soubory pro čtení schématu. Místo toho Data Factory zpracuje každý soubor jako binární a zkopíruje ho do druhého umístění.

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime-for-data-flow"></a>Spuštění kanálu se při dosažení limitu kapacity prostředí Integration runtime pro tok dat nezdařilo.

**Problém**

Chybová zpráva:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**Příčina**

Dosáhli jste limitu kapacity prostředí Integration runtime. Je možné, že budete provozovat velké množství toku dat pomocí stejného prostředí Integration runtime ve stejnou dobu. Podrobnosti najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#version-2) .

**Řešení**
 
- Spusťte své kanály v různých časech triggerů.
- Vytvořte nový prostředí Integration runtime a rozdělte kanály do několika prostředí Integration runtime.

### <a name="how-to-perform-activity-level-errors-and-failures-in-pipelines"></a>Postup provádění chyb na úrovni aktivity a selhání v kanálech

**Příčina**

Azure Data Factory orchestrace umožňuje podmíněnou logiku a umožňuje uživatelům provádět různé cesty na základě výsledku předchozí aktivity. Umožňuje čtyři podmíněné cesty: **po úspěšném** dokončení (výchozí průchod) **po neúspěšném** **dokončení** a **Při přeskočení**. 

Azure Data Factory vyhodnocuje výsledek všech aktivit na úrovni listu. Výsledky kanálu jsou úspěšné pouze v případě, že všechny pokusy pozůstávají úspěšné. Pokud se koncová aktivita přeskočí, vyhodnotí se místo toho její Nadřazená aktivita. 

**Řešení**

* Implementujte kontroly na úrovni aktivity podle následujících pokynů, [jak zpracovat selhání a chyby kanálu](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
* Pomocí Azure Logic Apps můžete sledovat kanály v pravidelných intervalech po [dotazech podle továrny](/rest/api/datafactory/pipelineruns/querybyfactory).
* [Vizuálně monitorovat kanál](./monitor-visually.md)

### <a name="how-to-monitor-pipeline-failures-in-regular-intervals"></a>Jak monitorovat selhání kanálu v pravidelných intervalech

**Příčina**

Možná budete muset monitorovat neúspěšné Data Factory kanály v intervalech, 5 minut. Pomocí koncového bodu se můžete dotazovat a filtrovat spouštění kanálů z datové továrny. 

**Řešení**
* Můžete nastavit aplikaci logiky Azure pro dotazování všech neúspěšných kanálů každých 5 minut, jak je popsáno v tématu [dotazování podle továrny](/rest/api/datafactory/pipelineruns/querybyfactory). Potom můžete hlásit incidenty vašemu systému lístků.
* [Vizuálně monitorovat kanál](./monitor-visually.md)

### <a name="degree-of-parallelism--increase-does-not-result-in-higher-throughput"></a>Zvýšení úrovně paralelismu nevede k vyšší propustnosti.

**Příčina** 

Stupeň paralelismu v *foreach* má ve skutečnosti maximální stupeň paralelismu. Nemůžeme zaručit konkrétní počet spuštění současně, ale tento parametr zaručuje, že nikdy Nepřekračujeme nastavenou hodnotu. Měli byste vidět toto omezení, aby bylo možné ho využít při řízení souběžného přístupu ke zdrojům a jímka.

Známá fakta o příkazu *foreach*
 * Foreach má vlastnost s názvem počet dávek (n), kde výchozí hodnota je 20 a maximální hodnota je 50.
 * Počet dávek, n, se používá ke konstrukci n front. Později se podíváme na podrobné informace o tom, jak jsou tyto fronty vytvořené.
 * Každá fronta běží sekvenčně, ale můžete mít paralelně spuštěné několik front.
 * Fronty jsou předem vytvořeny. To znamená, že během běhu nedojde k žádnému novému vyrovnávání front.
 * V každém okamžiku máte na jednu frontu více zpracovávaných položek. To znamená, že se v daném okamžiku zpracovává maximálně n položek.
 * Celková doba zpracování příkazu foreach je stejná jako doba zpracování nejdelší fronty. To znamená, že aktivita foreach závisí na způsobu konstrukce front.
 
**Řešení**

 * Nepoužívejte aktivitu *SetVariable* uvnitř *každého* spouštěného paralelního prostředí.
 * Vezměte v úvahu způsob, jakým jsou fronty vytvářeny. Zákazník může vylepšit výkon foreach nastavením více *foreachs* , kde každý foreach bude mít položky s podobným časem zpracování. Tím zajistíte, že dlouhotrvající běhy se zpracovávají paralelně, a ne postupně.

 ### <a name="pipeline-status-is-queued-or-stuck-for-a-long-time"></a>Stav kanálu je ve frontě nebo zablokovaný po dlouhou dobu.
 
 **Příčina**
 
 K tomu může dojít z různých důvodů, jako jsou limity souběžnosti, výpadky služby, selhání sítě atd.
 
 **Řešení**
 
* Limit souběžnosti: Pokud má váš kanál zásady souběžnosti, ověřte, že neprobíhá žádná stará spuštění kanálu. Maximální souběžnost kanálu povolená v Azure Data Factory je 10 kanálů. 
* Omezení monitorování: na plátně pro vytváření ADF vyberte svůj kanál a určete, jestli má přiřazenou vlastnost souběžnosti. Pokud ano, přejděte do zobrazení Monitorování a ujistěte se, že právě neprobíhá žádné spuštění z posledních 45 dnů. Pokud je něco v průběhu, můžete ho zrušit a nové spuštění kanálu by se mělo spustit.
* Přechodné problémy: je možné, že váš běh byl ovlivněn přechodným problémem sítě, selháním přihlašovacích údajů, výpadky služeb atd.  Pokud k tomu dojde, Azure Data Factory má interní proces obnovení, který monitoruje všechna spuštění a spouští je, když si vyvšimla, že došlo k chybě. K tomuto procesu dochází každou hodinu, takže pokud je spuštění zablokované déle než jednu hodinu, vytvořte případ podpory.
 
### <a name="longer-start-up-times-for-activities-in-adf-copy-and-data-flow"></a>Pro aktivity v kopírování ADF a toku dat se prodlouží doba spuštění.

**Příčina**

K tomu může dojít, pokud jste neimplementovali čas do živé funkce pro tok dat nebo optimalizované SHIR.

**Řešení**

* Pokud spuštění jednotlivých aktivit kopírování trvá až 2 minuty a k problému dochází hlavně u připojení k virtuální síti (a ne u prostředí Azure IR), může se jednat o problém s výkonem kopírování. Pokud chcete zkontrolovat postup řešení potíží, podívejte se na téma [vylepšení pro kopírování výkonu.](./copy-activity-performance-troubleshooting.md)
* K snížení doby spuštění clusteru pro aktivity toku dat můžete použít funkci Time to Live. Zkontrolujte prosím [Integration runtime toku dat.](./control-flow-execute-data-flow-activity.md#data-flow-integration-runtime)

 ### <a name="hitting-capacity-issues-in-shirself-hosted-integration-runtime"></a>Problémy s kapacitou v SHIR (Integration Runtime pro osobní hostování)
 
 **Příčina**
 
K tomu může dojít, pokud jste neSHIRi škálování podle vašich úloh.

**Řešení**

* Pokud narazíte na problém s kapacitou z SHIR, upgradujte virtuální počítač, aby se uzel zvýšil pro vyrovnání aktivit. Pokud se vám zobrazí chybová zpráva o obecném selhání nebo chybě v místním prostředí IR, místním prostředí s podporou INFRAČERVENého připojení nebo potížích s INFRAČERVENým připojením, které může generovat dlouhou frontu, přečtěte si [řešení potíží s místním hostováním prostředí Integration runtime.](./self-hosted-integration-runtime-troubleshoot-guide.md)

### <a name="error-messages-due-to-long-queues-for-adf-copy-and-data-flow"></a>Chybové zprávy z důvodu dlouhých front pro kopírování a tok dat ADF

**Příčina**

Z různých důvodů se můžou zobrazit chybové zprávy týkající se dlouhých front. 

**Řešení**
* Pokud obdržíte chybovou zprávu z libovolného zdroje nebo cíle přes konektory, které můžou generovat dlouhou frontu, přečtěte si [Průvodce řešením potíží s konektorem.](./connector-troubleshoot-guide.md)
* Pokud se zobrazí chybová zpráva týkající se toku mapování dat, který může generovat dlouhou frontu, přečtěte si [Průvodce řešením potíží s datovými toky.](./data-flow-troubleshoot-guide.md)
* Pokud se vám zobrazí chybová zpráva o dalších aktivitách, jako jsou například datacihly, vlastní aktivity nebo HDI, které mohou generovat dlouhou frontu, přečtěte si [Průvodce řešením potíží s aktivitami.](./data-factory-troubleshoot-guide.md)
* Pokud se vám zobrazí chybová zpráva týkající se spouštění balíčků SSIS, která může generovat dlouhou frontu, přečtěte si [příručku k odstraňování potíží se službou Azure-SSIS](./ssis-integration-runtime-ssis-activity-faq.md) a Průvodce odstraňováním [potíží se správou Integration runtime.](./ssis-integration-runtime-management-troubleshoot.md)


## <a name="next-steps"></a>Další kroky

Pro další nápovědu k řešení potíží zkuste tyto prostředky:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stránka s otázkou Microsoft Q&](/answers/topics/azure-data-factory.html)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)