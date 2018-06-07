---
title: Sledování požadavků Azure Cosmos DB a úložiště | Microsoft Docs
description: Naučte se monitorovat účtu Azure Cosmos DB metrik výkonu, například požadavky a chyby serveru a metriky využití, například spotřebu úložiště.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: sngun
ms.openlocfilehash: 1e5dba722f240bd6535ba64494912a153e9224c0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34612635"
---
# <a name="monitor-azure-cosmos-db"></a>Monitorování Azure Cosmos DB
Můžete monitorovat své účty Azure Cosmos DB [portál Azure](https://portal.azure.com/). U každého účtu Azure Cosmos DB je k dispozici ke sledování propustnosti, úložiště, dostupnosti, latence a konzistence úplnou sadu metriky.

Metriky lze zobrazit na stránku účtu, nová stránka metriky, nebo v Azure monitorování.

## <a name="view-performance-metrics-on-the-metrics-page"></a>Zobrazení výkonu metriky na stránce metriky
1. V [portál Azure](https://portal.azure.com/), klikněte na tlačítko **všechny služby**, přejděte k položce **databáze**, klikněte na tlačítko **Azure Cosmos DB**a pak klikněte na název Azure Cosmos DB účet, pro který chcete zobrazit metrik výkonu.
2. Pokud nová stránka načte, v nabídce prostředků v části **monitorování**, klikněte na tlačítko **metriky**.
3. Po otevření stránky metriky vyberte kolekci, chcete-li zkontrolovat z **kolekcí** rozevíracího seznamu.

   Portál Azure zobrazí sadu kolekce metriky, které jsou k dispozici. Všimněte si, že metriky propustnosti, úložiště, dostupnosti, latence a konzistence jsou k dispozici na samostatných záložkách. Chcete-li získat další podrobnosti o metrikách k dispozici, klepněte na dvojitou šipku v horní části vpravo na jednotlivých panelech metriky.

   ![Snímek obrazovky zobrazující sadu metriky přehledu monitorování](./media/monitor-accounts/metrics-suite.png)

## <a name="view-performance-metrics-by-using-azure-monitoring"></a>Zobrazení metriky výkonu pomocí sledování Azure
1. V [portál Azure](https://portal.azure.com/), klikněte na tlačítko **monitorování** na levém panelu.
2. V nabídce prostředků, klikněte na tlačítko **metriky**.
3. V **monitorování – metriky** okno v **skupiny prostředků** rozevírací nabídce vyberte skupinu prostředků, které jsou přidružené k účtu Azure Cosmos DB, které chcete monitorovat. 
4. V **prostředků** rozevírací nabídce vyberte možnost databáze účet k monitorování.
5. V seznamu **dostupné metriky**, vybrat metriky pro zobrazení. Pomocí tlačítka CTRL k vícenásobným výběrem. 

## <a name="view-performance-metrics-on-the-account-page"></a>Zobrazení výkonu metriky na stránku účtu
1. V [portál Azure](https://portal.azure.com/), klikněte na tlačítko **všechny služby**, přejděte k položce **databáze**, klikněte na tlačítko **Azure Cosmos DB**a pak klikněte na název Azure Cosmos DB účet, pro který chcete zobrazit metrik výkonu.
2. **Monitorování** přehledu ve výchozím nastavení zobrazí tyto dlaždice:
   
   * Celkový počet požadavků aktuálního dne.
   * Použít úložiště.
   
   ![Snímek obrazovky přehledu monitorování, který popisuje požadavky a využití úložiště](./media/monitor-accounts/documentdb-total-requests-and-usage.png)
3. Kliknutím na dvojitou šipku v pravém horním rohu **požadavky** dlaždice otevře podrobné **metrika** stránky.
4. **Metrika** stránce se zobrazují podrobnosti o celkový počet požadavků. 

## <a name="set-up-alerts-in-the-portal"></a>Nastavit výstrahy na portálu
1. V [portál Azure](https://portal.azure.com/), klikněte na tlačítko **všechny služby**, klikněte na tlačítko **Azure Cosmos DB**a potom klikněte na název účtu Azure Cosmos DB, pro kterou chcete nastavit výkonu metriky výstrahy.
2. V nabídce prostředků, klikněte na tlačítko **pravidla výstrah** chcete otevřít stránku pravidla výstrah.  
   ![Snímek obrazovky výstrahy pravidla vybrané části](./media/monitor-accounts/madocdb10.5.png)
3. V **výstrah pravidla** klikněte na tlačítko **přidat upozornění**.  
   ![Snímek obrazovky stránky pravidla výstrah, pomocí tlačítka Přidat výstrahy zvýrazněná](./media/monitor-accounts/madocdb11.png)
4. V **přidání pravidla výstrahy** stránky, zadejte:
   
   * Název pravidla výstrahy, které nastavíte.
   * Popis nové pravidlo výstrahy.
   * Metrika pro pravidlo výstrahy.
   * Podmínka, prahovou hodnotu a období určující, když se aktivuje výstrahu. Například server chyba počet větší než 5 za posledních 15 minut.
   * Jestli správce služeb a coadministrators jsou e-mailem při aktivuje výstrahu.
   * Další e-mailové adresy pro oznámení o výstrahách.  
     ![Snímek obrazovky s možností přidat stránku pravidlo výstrahy](./media/monitor-accounts/madocdb12.png)

## <a name="monitor-azure-cosmos-db-programmatically"></a>Monitorování databáze Azure Cosmos prostřednictvím kódu programu
Účet úrovně metriky dostupné na portálu, jako je například využití a celkový počet požadavků na účet úložiště, nejsou k dispozici prostřednictvím rozhraní API SQL. Data o využití na úrovni kolekce však můžete načíst pomocí rozhraní API SQL. Načtení dat na úrovni kolekce, postupujte takto:

* Chcete-li použít rozhraní API REST [provádět GET na kolekci](https://msdn.microsoft.com/library/mt489073.aspx). V záhlaví x-ms-resource kvóty a x-ms-resource využití v odpovědi se vrátí informace kvóta a využití pro kolekci.
* Chcete-li používat sadu .NET SDK, použijte [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) metoda, která vrátí hodnotu [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) obsahující počet použití vlastnosti, jako **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**a další.

Chcete-li získat přístup k další metriky, použijte [Azure monitorování SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights). K dispozici definice metrik můžete načíst pomocí volání:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Dotazy k načtení jednotlivé metriky použijte následující formát:

    https://management.azure.com/subscriptions/{SubecriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

Další informace najdete v tématu [načítání prostředků metriky přes REST API Azure monitorování](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/). Všimněte si, že se přejmenoval "Statistika Azure" "Azure sledování".  Tuto položku blogu odkazuje na starší název.

## <a name="next-steps"></a>Další postup
Další informace o plánování kapacity Azure Cosmos DB, najdete v článku [kalkulačky Plánovač kapacity Azure Cosmos DB](https://www.documentdb.com/capacityplanner).

