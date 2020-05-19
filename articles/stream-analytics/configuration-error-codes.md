---
title: Kódy chyb konfigurace – Azure Stream Analytics
description: Řešení potíží s Azure Stream Analyticsmi kódy chyb konfigurace.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 9951a6ddfbd1c562184e2b7e02a9da98f7d94b12
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598063"
---
# <a name="azure-stream-analytics-configuration-error-codes"></a>Kódy chyb konfigurace Azure Stream Analytics

Protokoly aktivit a protokoly prostředků můžete použít k ladění neočekávaného chování z vaší Azure Stream Analytics úlohy. V tomto článku je uveden popis každého kódu chyby konfigurace. Chyby konfigurace souvisejí s konfigurací vaší úlohy nebo se vstupními a výstupními konfiguracemi.

## <a name="kafkainvalidpermission"></a>KafkaInvalidPermission

* **Příčina**: úloha Stream Analytics nemá správná oprávnění k provedení určitých akcí.

## <a name="kafkainvalidconfiguration"></a>KafkaInvalidConfiguration

* **Příčina**: některé konfigurace na adaptéru Kafka jsou neplatné.

## <a name="kafkainvalidtimestamptype"></a>KafkaInvalidTimestampType

* **Příčina**: typ časového razítka používaný Kafka je neplatný.

## <a name="eventhubunauthorizedaccess"></a>EventHubUnauthorizedAccess

* **Příčina**: centrum událostí vyvolalo chybu *neautorizovaného přístupu* .

## <a name="eventhubreceiverepochconflict"></a>EventHubReceiverEpochConflict

* **Příčina**: existuje více než jeden přijímač centra událostí s různými epocha hodnotami.
* **Doporučení**: Ujistěte se, že *Service Bus Explorer* nebo aplikace *EventProcessorHost* nejsou připojené, když je vaše úloha Stream Analytics spuštěná.

## <a name="eventhubreceiverquotaexceeded"></a>EventHubReceiverQuotaExceeded

* **Příčina**: Stream Analytics se nemůže připojit k oddílu, protože bylo dosaženo maximálního počtu povolených přijímačů na oddíl ve skupině uživatelů.
* **Doporučení**: Zajistěte, aby jiné úlohy Stream Analytics nebo Service Bus Explorer nepoužívaly stejnou skupinu příjemců.

## <a name="eventhuboutputthrottled"></a>EventHubOutputThrottled

* **Příčina**: při zápisu dat do centra událostí došlo k chybě z důvodu omezení.
* **Doporučení**: Pokud k tomu dojde konzistentně, upgradujte propustnost.

## <a name="eventhuboutputinvalidconnectionconfig"></a>EventHubOutputInvalidConnectionConfig

* **Příčina**: zadaná konfigurace připojení není správná.
* **Doporučení**: Opravte konfiguraci a restartujte úlohu.

## <a name="eventhuboutputinvalidhostname"></a>EventHubOutputInvalidHostname

* **Příčina**: hostitel centra událostí je nedosažitelný.
* **Doporučení**: Ujistěte se, že zadaný název hostitele je správný.

## <a name="eventhuboutputunexpectedpartitioncount"></a>EventHubOutputUnexpectedPartitionCount

* **Příčina**: odesílatel centra eventhub zjistil neočekávaný počet oddílů centra eventhub.
* **Doporučení**: Pokud se změnil počet oddílů centra EventHub, restartujte úlohu Stream Analytics.

## <a name="cosmosdbpartitionkeynotfound"></a>CosmosDBPartitionKeyNotFound

* **Příčina**: Stream Analytics nemohl najít klíč oddílu konkrétní kolekce Cosmos DB v databázi.
* **Doporučení**: Ujistěte se, že je pro kolekci v Cosmos DB zadaný platný klíč oddílu.

## <a name="cosmosdbinvalidpartitionkeycolumn"></a>CosmosDBInvalidPartitionKeyColumn

* **Příčina**: vyvolána, když klíč oddílu není ani uzel typu list, ani na nejvyšší úrovni.

## <a name="cosmosdbinvalididcolumn"></a>CosmosDBInvalidIdColumn

* **Příčina**: výstup dotazu nemůže obsahovat \[ ID sloupce], pokud je zvolen jiný sloupec jako vlastnost primárního klíče.

## <a name="cosmosdbdatabasenotfound"></a>CosmosDBDatabaseNotFound

* **Příčina**: Stream Analytics nemůže najít databázi CosmosDB.

## <a name="cosmosdbcollectionnotfound"></a>CosmosDBCollectionNotFound

* **Příčina**: Stream Analytics nemůže najít konkrétní kolekci Cosmos DB v databázi.

## <a name="cosmosdboutputwritethrottling"></a>CosmosDBOutputWriteThrottling

* **Příčina**: při zápisu dat došlo k chybě z důvodu omezení Cosmos DB.
* **Doporučení**: Upgradujte úroveň výkonu kolekce a Optimalizujte výkon vaší databáze.

## <a name="sqldatabaseconnectionstringerror"></a>SQLDatabaseConnectionStringError

* **Příčina**: v úloze Stream Analytics došlo k chybě ověřování.
* **Doporučení**: Ujistěte se, že je připojovací řetězec SQL Database správný.

## <a name="sqldatabasemanagedidentityauthenticationerror"></a>SQLDatabaseManagedIdentityAuthenticationError

* **Příčina**: v úloze Stream Analytics došlo k chybě ověřování. 
* **Doporučení**: Ujistěte se, že je název účtu správně nakonfigurovaný a že má spravovaná identita úlohy přístup k SQL Database.

## <a name="sqldatabaseoutputnotableerror"></a>SQLDatabaseOutputNoTableError

* **Příčina**: Stream Analytics nemůže najít informace o schématu pro konkrétní tabulku.

## <a name="sqldwoutputinvalidserviceedition"></a>SQLDWOutputInvalidServiceEdition

* **Příčina**: SQL Database se nepodporuje.
* **Doporučení**: použijte synapse fond SQL.

## <a name="next-steps"></a>Další kroky

* [Řešení potíží se vstupními připojeními](stream-analytics-troubleshoot-input.md)
* [Řešení potíží s výstupy Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Řešení potíží s Azure Stream Analytics dotazy](stream-analytics-troubleshoot-query.md)
* [Řešení potíží s Azure Stream Analytics pomocí protokolů prostředků](stream-analytics-job-diagnostic-logs.md)
* [Chyby Azure Stream Analytics dat](data-errors.md)
