---
title: Postup při auditování operací roviny ovládacího prvku Azure Cosmos DB
description: Naučte se auditovat operace roviny ovládacího prvku, jako je například přidání oblasti, aktualizace propustnosti, převzetí služeb při selhání oblasti, přidání virtuální sítě atd. v Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/05/2020
ms.author: sngun
ms.openlocfilehash: 08cc3b08611947ac32973b2dfb01060140dc0798
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743892"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Postup při auditování operací roviny ovládacího prvku Azure Cosmos DB

Řídicí rovina v Azure Cosmos DB je služba RESTful, která umožňuje provádět různé sady operací na účtu Azure Cosmos. Zveřejňuje model veřejného prostředku (například databáze, účet) a různé operace pro koncové uživatele k provádění akcí v modelu prostředků. Operace roviny ovládacího prvku zahrnuje změny v rámci účtu nebo kontejneru Azure Cosmos. Například operace, jako je vytvoření účtu Azure Cosmos, přidání oblasti, aktualizace propustnosti, převzetí služeb při selhání, přidání virtuální sítě atd. jsou některé z operací roviny ovládacího prvku. Tento článek vysvětluje, jak auditovat operace roviny ovládacího prvku v Azure Cosmos DB. Pomocí Azure CLI nebo PowerShellu můžete spouštět operace řídicích roviny na účtech Azure Cosmos pomocí Azure CLI, PowerShellu nebo Azure Portal, zatímco kontejnery.

Následuje několik ukázkových scénářů, ve kterých je užitečné objednání rovin řízení auditování:

* Chcete získat upozornění, když se upraví pravidla brány firewall pro váš účet Azure Cosmos. Výstraha se vyžaduje k vyhledání neoprávněných změn pravidel, která řídí zabezpečení sítě vašeho účtu Azure Cosmos a rychlé fungování.

* Chcete získat výstrahu, pokud se do účtu Azure Cosmos přidá nebo odebere nová oblast. Přidávání nebo odebírání oblastí má vliv na požadavky fakturace a suverenity dat. Tato výstraha vám pomůže zjistit náhodné přidání nebo odebrání oblasti ve vašem účtu.

* Chcete získat další podrobnosti z diagnostických protokolů o tom, co se změnilo. Virtuální síť se například změnila.

## <a name="disable-key-based-metadata-write-access"></a>Zakázat přístup pro zápis metadat založených na klíčích

Před auditem operací řízení roviny v Azure Cosmos DB zakažte na svém účtu přístup k zápisu metadat na základě klíčů. Pokud je zakázaný přístup pro zápis metadat založených na klíčích, klienti připojující se k účtu Azure Cosmos prostřednictvím klíčů účtu nemají přístup k účtu. Přístup pro zápis můžete zakázat nastavením `disableKeyBasedMetadataWriteAccess` vlastnosti na hodnotu true. Po nastavení této vlastnosti se můžou změny libovolného prostředku vyskytnout od uživatele, který má správnou roli řízení přístupu na základě role (RBAC) a přihlašovací údaje. Další informace o tom, jak tuto vlastnost nastavit, najdete v článku [prevence změn ze sad SDK](role-based-access-control.md#prevent-sdk-changes) . 

`disableKeyBasedMetadataWriteAccess`Když je tato funkce zapnutá, klienti na bázi sady SDK spouštějí operace vytvořit nebo aktualizovat, protože se vrátí chyba *"post" na prostředku "ContainerNameorDatabaseName Azure Cosmos DB"* . Musíte zapnout přístup k takovým operacím pro váš účet nebo provádět operace vytvoření/aktualizace prostřednictvím Azure Resource Manager, Azure CLI nebo Azure PowerShell. Pokud chcete přejít zpátky, nastavte disableKeyBasedMetadataWriteAccess na **hodnotu false** pomocí rozhraní příkazového řádku Azure, jak je popsané v článku [prevence změn ze sady Cosmos SDK](role-based-access-control.md#prevent-sdk-changes) . Nezapomeňte změnit hodnotu `disableKeyBasedMetadataWriteAccess` na false namísto true.

Při vypnutí přístupu k zápisu metadat Vezměte v úvahu následující body:

* Vyhodnoťte a zajistěte, aby vaše aplikace nevytvářela volání metadat, která mění výše uvedené prostředky (například vytvoření kolekce, aktualizace propustnosti,...) pomocí sady SDK nebo klíčů účtu.

* V současné době Azure Portal používá klíče účtu pro operace s metadaty, takže tyto operace budou zablokovány. Alternativně můžete pomocí rozhraní příkazového řádku Azure CLI, sad SDK nebo Správce prostředků nasazení šablon provádět tyto operace.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>Povolení diagnostických protokolů pro operace roviny ovládacího prvku

Můžete povolit diagnostické protokoly pro operace řídicích roviny pomocí Azure Portal. Po povolení budou protokoly diagnostiky zaznamenávat operaci jako pár počátečních a úplných událostí s příslušnými podrobnostmi. Třeba *RegionFailoverStart* a *RegionFailoverComplete* dokončí událost převzetí služeb při selhání oblasti.

Chcete-li povolit protokolování na operace roviny ovládacího prvku, použijte následující postup:

1. Přihlaste se [Azure Portal](https://portal.azure.com) a přejděte k účtu Azure Cosmos.

1. Otevřete podokno **nastavení diagnostiky** , zadejte **název** protokolů, které chcete vytvořit.

1. Jako typ protokolu vyberte **ControlPlaneRequests** a vyberte možnost **Odeslat do Log Analytics** .

Protokoly můžete také ukládat do centra událostí v účtu úložiště nebo v datovém proudu. Tento článek ukazuje, jak odesílat protokoly do Log Analytics a pak je dotazovat. Po povolení bude trvat několik minut, než se diagnostické protokoly projeví. Všechny operace roviny ovládacího prvku provedené po tomto bodu je možné sledovat. Následující snímek obrazovky ukazuje, jak povolit protokoly roviny ovládacího prvku:

:::image type="content" source="./media/audit-control-plane-logs/enable-control-plane-requests-logs.png" alt-text="Povolit protokolování požadavků na řídicí rovinu":::

## <a name="view-the-control-plane-operations"></a>Zobrazit operace roviny ovládacího prvku

Po zapnutí protokolování použijte následující postup ke sledování operací pro určitý účet:

1. Přihlaste se [Azure Portal](https://portal.azure.com).

1. V levém navigačním panelu otevřete kartu **monitorování** a pak vyberte podokno **protokoly** . Otevře se uživatelské rozhraní, kde můžete snadno spouštět dotazy s konkrétním účtem v oboru. Spusťte následující dotaz pro zobrazení protokolů roviny ovládacího prvku:

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

Když se u účtu Azure Cosmos změní úroveň konzistence, zachytí následující snímky obrazovky.

:::image type="content" source="./media/audit-control-plane-logs/add-ip-filter-logs.png" alt-text="Povolit protokolování požadavků na řídicí rovinu":::

Následující snímky obrazovky zaznamenávají protokoly, když se vytvoří místo na disku nebo tabulka Cassandra účtu a když se aktualizuje propustnost. Protokoly roviny ovládacího prvku pro operace vytvoření a aktualizace v databázi a kontejner jsou protokolovány samostatně, jak je znázorněno na následujícím snímku obrazovky:

:::image type="content" source="./media/audit-control-plane-logs/throughput-update-logs.png" alt-text="Povolit protokolování požadavků na řídicí rovinu":::

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>Identifikace identity přidružené k určité operaci

Pokud chcete ladit další, můžete určit konkrétní operace v **protokolu aktivit** pomocí ID aktivity nebo časového razítka operace. Časové razítko se používá pro některé klienty Správce prostředků, kde ID aktivity není explicitně předáno. Protokol aktivit obsahuje podrobné informace o identitě, se kterou byla operace iniciována. Následující snímek obrazovky ukazuje, jak použít ID aktivity a najít k němu přidružené operace v protokolu aktivit:

:::image type="content" source="./media/audit-control-plane-logs/find-operations-with-activity-id.png" alt-text="Povolit protokolování požadavků na řídicí rovinu":::

## <a name="control-plane-operations-for-azure-cosmos-account"></a>Řízení operací roviny pro účet Azure Cosmos

Níže jsou uvedené operace roviny ovládacího prvku, které jsou k dispozici na úrovni účtu. Většina operací je sledována na úrovni účtu. Tyto operace jsou k dispozici jako metriky ve službě Azure monitor:

* Přidání oblasti
* Oblast odebrána
* Účet se odstranil.
* Převzetí služeb při selhání oblasti
* Účet vytvořen
* Virtuální síť se odstranila.
* Nastavení sítě účtu se aktualizovala.
* Nastavení replikace účtu se aktualizovala
* Klíče účtu se aktualizovaly.
* Nastavení zálohování účtu se aktualizovala.
* Nastavení diagnostiky účtu se aktualizovala

## <a name="control-plane-operations-for-database-or-containers"></a>Řízení operací roviny pro databázi nebo kontejnery

Níže jsou dostupné operace roviny ovládacího prvku na úrovni databáze a kontejneru. Tyto operace jsou k dispozici jako metriky ve službě Azure monitor:

* SQL Database vytvořena
* SQL Database Aktualizováno
* Aktualizace propustnosti SQL Database
* Odstraněné SQL Database
* Vytvořil se kontejner SQL.
* Kontejner SQL se aktualizoval.
* Propustnost kontejneru SQL se aktualizovala
* Kontejner SQL se odstranil.
* Cassandra místo pro vytváření klíčů
* Cassandra se aktualizované místo na disku
* Propustnost Cassandraho místa na disku se aktualizovala
* Odstraněné místo na Cassandra
* Tabulka Cassandra se vytvořila.
* Tabulka Cassandra se aktualizovala.
* Propustnost tabulky Cassandra se aktualizovala.
* Tabulka Cassandra se odstranila.
* Databáze Gremlin se vytvořila.
* Databáze Gremlin se aktualizovala.
* Propustnost databáze Gremlin se aktualizovala
* Databáze Gremlin se odstranila.
* Graf Gremlin se vytvořil.
* Graf Gremlin se aktualizoval.
* Byla aktualizována propustnost grafu Gremlin
* Graf Gremlin se odstranil.
* Databáze Mongo se vytvořila.
* Databáze Mongo se aktualizovala.
* Propustnost databáze Mongo se aktualizovala
* Databáze Mongo se odstranila.
* Kolekce Mongo se vytvořila.
* Kolekce Mongo se aktualizovala
* Byla aktualizována propustnost kolekce Mongo
* Kolekce Mongo se odstranila.
* Tabulka Azure se vytvořila.
* Tabulka Azure se aktualizovala
* Aktualizace propustnosti tabulky Azure
* Tabulka Azure se odstranila.

## <a name="diagnostic-log-operations"></a>Operace diagnostického protokolu

Níže jsou uvedené názvy operací v diagnostických protokolech pro různé operace:

* RegionAddStart, RegionAddComplete
* RegionRemoveStart, RegionRemoveComplete
* AccountDeleteStart, AccountDeleteComplete
* RegionFailoverStart, RegionFailoverComplete
* AccountCreateStart, AccountCreateComplete
* AccountUpdateStart, AccountUpdateComplete
* VirtualNetworkDeleteStart, VirtualNetworkDeleteComplete
* DiagnosticLogUpdateStart, DiagnosticLogUpdateComplete

Pro operace specifické pro rozhraní API je operace pojmenována s následujícím formátem:

* ApiKind + ApiKindResourceType + typem operace OperationType
* ApiKind + ApiKindResourceType + "propustnost" + typem operace OperationType

**Příklad** 

* CassandraKeyspacesCreate
* CassandraKeyspacesUpdate
* CassandraKeyspacesThroughputUpdate
* SqlContainersUpdate

Vlastnost *ResourceDetails* obsahuje celé tělo prostředku jako datovou část požadavku a obsahuje všechny vlastnosti požadované k aktualizaci.

## <a name="diagnostic-log-queries-for-control-plane-operations"></a>Dotazy diagnostického protokolu pro operace roviny ovládacího prvku

Následuje několik příkladů, jak získat diagnostické protokoly pro operace roviny řízení:

```kusto
AzureDiagnostics 
| where Category startswith "ControlPlane"
| where OperationName contains "Update"
| project httpstatusCode_s, statusCode_s, OperationName, resourceDetails_s, activityId_g
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where TimeGenerated >= todatetime('2020-05-14T17:37:09.563Z')
| project TimeGenerated, OperationName, apiKind_s, apiKindResourceType_s, operationType_s, resourceDetails_s
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where  OperationName startswith "SqlContainersUpdate"
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where  OperationName startswith "SqlContainersThroughputUpdate"
```

Dotaz pro získání activityId a volajícího, který inicioval operaci odstranění kontejneru:

```kusto
(AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName == "SqlContainersDelete"
| where TimeGenerated >= todatetime('9/3/2020, 5:30:29.300 PM')
| summarize by activityId_g )
| join (
AzureActivity
| parse HTTPRequest with * "clientRequestId\": \"" activityId_g "\"" * 
| summarize by Caller, HTTPRequest, activityId_g)
on activityId_g
| project Caller, activityId_g
```

Dotaz pro získání aktualizace indexu nebo TTL. Pak můžete porovnat výstup tohoto dotazu s předchozí aktualizací, abyste viděli změnu v indexu nebo TTL.

```Kusto
AzureDiagnostics
| where Category =="ControlPlaneRequests"
| where  OperationName == "SqlContainersUpdate"
| project resourceDetails_s
```

**výkonem**

```json
{id:skewed,indexingPolicy:{automatic:true,indexingMode:consistent,includedPaths:[{path:/*,indexes:[]}],excludedPaths:[{path:/_etag/?}],compositeIndexes:[],spatialIndexes:[]},partitionKey:{paths:[/pk],kind:Hash},defaultTtl:1000000,uniqueKeyPolicy:{uniqueKeys:[]},conflictResolutionPolicy:{mode:LastWriterWins,conflictResolutionPath:/_ts,conflictResolutionProcedure:}
```

## <a name="next-steps"></a>Další kroky

* [Prozkoumejte Azure Monitor pro Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Monitorování a ladění pomocí metrik v Azure Cosmos DB](use-metrics.md)
