---
title: Monitorujte metriky výkonu a úložiště ve službě Azure Cosmos DB
description: Další informace o monitorování účtu služby Azure Cosmos DB pro metriky výkonu, jako jsou požadavky a chyby serveru a metriky využití, jako je využití úložiště.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: fe527e6e9933d25d7f1a16561ce4822d7b9a33ab
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135878"
---
# <a name="monitor-performance-and-storage-metrics-in-azure-cosmos-db"></a>Monitorujte metriky výkonu a úložiště ve službě Azure Cosmos DB

Můžete monitorovat své účty služby Azure Cosmos DB [webu Azure portal](https://portal.azure.com/). Pro každý účet služby Azure Cosmos DB je možné monitorovat výkon, úložiště, dostupnosti, latence a konzistence kompletní sadu metriky.

Metriky lze zobrazit na stránce účtu, nové metriky stránky, nebo ve službě Azure Monitor.

## <a name="view-performance-metrics-on-the-metrics-page"></a>Zobrazit metriku výkonu na stránce metriky
1. V [webu Azure portal](https://portal.azure.com/), klikněte na tlačítko **všechny služby**, přejděte k položce **databází**, klikněte na tlačítko **služby Azure Cosmos DB**a pak klikněte na název Azure Účet služby cosmos DB pro kterou chcete zobrazit metriky výkonu.
2. Když načte nová stránka, v nabídce prostředků v části **monitorování**, klikněte na tlačítko **metriky**.
3. Po otevření stránky metrik vyberte kolekci, aby kontrolovali z **kolekcí** rozevíracího seznamu.

   Na webu Azure portal zobrazuje sadu shromažďování metrik, které jsou k dispozici. Všimněte si, že propustnost, úložiště, dostupnosti, latence a konzistence metriky jsou k dispozici v samostatných kartách. Chcete-li získat další podrobnosti týkající se metrik k dispozici, klikněte na dvojitou šipku v horní části pravého podokna jednotlivých metrik.

   ![Snímek obrazovky s přehledu monitorování, zobrazující sady metriky](./media/monitor-accounts/metrics-suite.png)

## <a name="view-performance-metrics-by-using-azure-monitoring"></a>Zobrazit metriku výkonu s využitím monitorování Azure
1. V [webu Azure portal](https://portal.azure.com/), klikněte na tlačítko **monitorování** na levém panelu.
2. V nabídce prostředku klikněte na tlačítko **metriky**.
3. V **monitorování – metrika** okno v **skupiny prostředků** rozevírací nabídky, vyberte skupinu prostředků přidružené k účtu Azure Cosmos DB, který chcete monitorovat. 
4. V **prostředků** rozevírací nabídce vyberte možnost účtu databáze k monitorování.
5. V seznamu **dostupné metriky**, vyberte metriky pro zobrazení. Pomocí tlačítka CTRL proveďte vícenásobný výběr. 

## <a name="view-performance-metrics-on-the-account-page"></a>Zobrazit metriku výkonu na stránce účtu
1. V [webu Azure portal](https://portal.azure.com/), klikněte na tlačítko **všechny služby**, přejděte k položce **databází**, klikněte na tlačítko **služby Azure Cosmos DB**a pak klikněte na název Azure Účet služby cosmos DB pro kterou chcete zobrazit metriky výkonu.
2. **Monitorování** lens zobrazí následující dlaždice ve výchozím nastavení:
   
   * Celkový počet požadavků na aktuální den.
   * Využité úložiště.
   
   ![Snímek obrazovky okna přehledu monitorování, který popisuje požadavky a využití úložiště](./media/monitor-accounts/documentdb-total-requests-and-usage.png)
3. Kliknutím na dvojité šipky v pravé horní části **požadavky** dlaždice se otevře na konkrétní **metrika** stránky.
4. **Metrika** stránky obsahuje podrobnosti o celkový počet požadavků. 

## <a name="set-up-alerts-in-the-portal"></a>Nastavte si upozornění na portálu
1. V [webu Azure portal](https://portal.azure.com/), klikněte na tlačítko **všechny služby**, klikněte na tlačítko **služby Azure Cosmos DB**a pak klikněte na název účtu Azure Cosmos DB, pro kterou chcete nastavit výkonu upozornění na metriku.
2. V nabídce prostředku klikněte na tlačítko **pravidla upozornění** otevřete stránku pravidla upozornění.  
   ![Snímek obrazovky upozornění pravidla vybrané části](./media/monitor-accounts/madocdb10.5.png)
3. V **pravidla upozornění** klikněte na **přidat upozornění**.  
   ![Snímek obrazovky se zvýrazněným tlačítkem Přidat upozornění v seznamu na stránce upozornění pravidla](./media/monitor-accounts/madocdb11.png)
4. V **přidání pravidla výstrahy** stránky, zadejte:
   
   * Název pravidla výstrahy nastavujete.
   * Popis nové pravidlo upozornění.
   * Metriky pro pravidla upozornění.
   * Podmínku prahové hodnoty a období, které určují, kdy se aktivuje upozornění. Chyba serveru počet například větší než 5 za posledních 15 minut.
   * Určuje, zda správce služeb a spolusprávci jsou e-mailem při spuštění výstrahy.
   * Další e-mailové adresy pro oznámení o výstrahách.  
     ![Snímek obrazovky okna Přidat stránku pravidlo upozornění](./media/monitor-accounts/madocdb12.png)

## <a name="monitor-azure-cosmos-db-programmatically"></a>Programové monitorování služby Azure Cosmos DB
Účtu metriky na úrovni k dispozici na portálu, jako je například využití a celkový počet požadavků na účet úložiště, nejsou k dispozici prostřednictvím rozhraní SQL API. Však můžete načíst data o využití na úrovni kolekce pomocí rozhraní SQL API. K načtení dat na úrovni kolekce, postupujte takto:

* Použití rozhraní REST API [provádět GET na kolekci](https://msdn.microsoft.com/library/mt489073.aspx). V záhlaví x-ms-resource-quota a x-ms-resource využití v odpovědi se vrátí informace kvótu a využití pro kolekci.
* Použití sady .NET SDK, použijte [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) metoda, která vrátí [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) , která obsahuje počet použití vlastnosti jako  **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**a provádění dalších akcí.

Chcete-li přistupovat k dalším metrikám, použijte [SDK služby Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Insights). Dostupné definice metrik může být načten voláním:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Dotazy k načtení jednotlivých metrik použijte následující formát:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

Další informace najdete v tématu [načítání metriky prostředků prostřednictvím REST API služby Azure Monitor](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/). Všimněte si, že byl přejmenován "Azure Insights" "Azure Monitor".  Tato položka blogu odkazuje na starší název.

## <a name="next-steps"></a>Další postup
Další informace o plánování kapacity služby Azure Cosmos DB najdete v tématu [Kalkulačka Plánovač kapacity služby Azure Cosmos DB](https://www.documentdb.com/capacityplanner).

