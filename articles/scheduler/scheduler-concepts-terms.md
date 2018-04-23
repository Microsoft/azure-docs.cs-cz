---
title: Koncepty, pojmy a entity Scheduleru | Dokumentace Microsoftu
description: Koncepty, terminologie a hierarchie entit služby Azure Scheduler včetně úloh a kolekcí úloh.  Zobrazí ucelený příklad naplánované úlohy.
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 3ef16fab-d18a-48ba-8e56-3f3e0a1bcb92
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 91302d57c43a6c9d14aeeee95df3d61fa6f73172
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="scheduler-concepts-terminology--entity-hierarchy"></a>Koncepty, terminologie a hierarchie entit Scheduleru
## <a name="scheduler-entity-hierarchy"></a>Hierarchie entit Scheduleru
V následující tabulce jsou uvedené hlavní prostředky, které rozhraní API Scheduleru vystavuje nebo používá.

| Prostředek | Popis |
| --- | --- |
| **Kolekce úloh** |Kolekce úloh obsahuje skupinu úloh a zachovává nastavení, kvóty a omezení, které jsou sdílené mezi úlohami v kolekci. Kolekci úloh vytvoří vlastník předplatného a seskupuje úlohy podle použití nebo hranic aplikací. Je omezená na jednu oblast. Taky umožňuje vynucovat kvóty a dál tak omezovat používání všech úloh v dané kolekci. Tyto kvóty zahrnují hodnoty MaxJobs a MaxRecurrence. |
| **Úloha** |Úloha definuje jednu opakující se akci s jednoduchými nebo komplexními strategiemi provedení. Akce můžou zahrnovat požadavky HTTP, fronty úložiště, fronty sběrnice nebo témata sběrnice. |
| **Historie úlohy** |Historie úlohy obsahuje podrobnosti o provedení úlohy. Obsahuje podrobnosti o úspěchu/neúspěchu a jakékoli odezvě. |

## <a name="scheduler-entity-management"></a>Správa entit Scheduleru
Na vysoké úrovni plánovač a rozhraní API pro správu služeb vystavují na prostředky tyto operace:

| Schopnost | Popis a adresa identifikátoru URI |
| --- | --- |
| **Správa kolekce úloh** |Podpora pro GET, PUT a DELETE pro vytváření a úpravu kolekcí úloh a v nich obsažených úloh. Kolekce úloh je kontejner pro úlohy a mapy ke kvótám a sdíleným nastavením. Příklady kvót popsané později jsou maximální počet úloh a nejmenší interval opakování. <p>PUT a DELETE: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p><p>GET: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p> |
| **Správa úloh** |Podpora pro GET, PUT, POST, PATCH a DELETE pro vytváření a úpravu úloh. Všechny úlohy musí patřit do stejné kolekce, která už existuje, takže k vytváření nedochází. <p>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`</p> |
| **Správa historie úloh** |Podpora pro GET pro načtení historie provádění úloh, jako je uplynulá doba úlohy a výsledky provedení úlohy, za posledních 60 dní. Přidá podporu parametru řetězce dotazu pro filtrování podle stavu a statusu. <P>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`</p> |

## <a name="job-types"></a>Typy úloh
Existují různé typy úloh: úlohy HTTP (a úlohy HTTPS, které podporují SSL), úlohy fronty úložiště, úlohy fronty sběrnice a úlohy témata sběrnice. Úlohy HTTP jsou ideální, pokud máte koncový bod existující úlohy nebo služby. Úlohy fronty úložiště můžete použít k odeslání zprávy do front úložiště, takže jsou ideální pro úlohy, které používají fronty úložiště. Úlohy sběrnice služby jsou zase ideální pro úlohy, které používají témata a fronty sběrnice služby.

## <a name="the-job-entity-in-detail"></a>Podrobnosti o entitě „úloha“
Na základní úrovni má naplánovaná úloha několik částí:

* Akce, která se má provést, když časovač úlohy sepne  
* (Volitelné) Čas, kdy se má úloha spustit  
* (Volitelné) Kdy a jak často se má úloha opakovat  
* (Volitelné) Akce, která se má provést, pokud primární akce selže  

Naplánovaná úloha interně taky obsahuje data poskytnutá systémem, jako je třeba čas příštího naplánovaného provedení.

Následující kód je komplexní příklad naplánované úlohy. Podrobnější informace jsou uvedené v dalších částech.

    {
        "startTime": "2012-08-04T00:00Z",               // optional
        "action":
        {
            "type": "http",
            "retryPolicy": { "retryType":"none" },
            "request":
            {
                "uri": "http://contoso.com/foo",        // required
                "method": "PUT",                        // required
                "body": "Posting from a timer",         // optional
                "headers":                              // optional

                {
                    "Content-Type": "application/json"
                },
            },
           "errorAction":
           {
               "type": "http",
               "request":
               {
                   "uri": "http://contoso.com/notifyError",
                   "method": "POST",
               },
           },
        },
        "recurrence":                                   // optional
        {
            "frequency": "week",                        // can be "year" "month" "day" "week" "minute"
            "interval": 1,                              // optional, how often to fire (default to 1)
            "schedule":                                 // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]
            },
            "count": 10,                                 // optional (default to recur infinitely)
            "endTime": "2012-11-04",                     // optional (default to recur infinitely)
        },
        "state": "disabled",                           // enabled or disabled
        "status":                                       // controlled by Scheduler service
        {
            "lastExecutionTime": "2007-03-01T13:00:00Z",
            "nextExecutionTime": "2007-03-01T14:00:00Z ",
            "executionCount": 3,
                                                "failureCount": 0,
                                                "faultedCount": 0
        },
    }

Jak je vidět na předešlé ukázce naplánované úlohy, má definice úlohy několik částí:

* Čas spuštění („startTime“)  
* Akce („action“), která zahrnuje i akci při chybě („errorAction“)
* Opakování („recurrence“)  
* Stav („state“)  
* Status („status“)  
* Zásady opakovaných pokusů („retryPolicy“)  

Podívejme se na každou podrobněji:

## <a name="starttime"></a>startTime
„startTime“ je čas spuštění a umožňuje volajícímu zadat posun časového pásma ve [formátu ISO-8601](http://en.wikipedia.org/wiki/ISO_8601).

## <a name="action-and-erroraction"></a>action a errorAction
„action“ je akce vyvolaná při každém výskytu a popisuje typ vyvolání služby. Akce je to, co se provede podle stanoveného plánu. Scheduler podporuje akce HTTP, fronty úložiště, fronty sběrnice a témata sběrnice.

V příkladu nahoře je akce HTTP. Dole je příklad akce fronty úložiště:

    {
            "type": "storageQueue",
            "queueMessage":
            {
                "storageAccount": "myStorageAccount",  // required
                "queueName": "myqueue",                // required
                "sasToken": "TOKEN",                   // required
                "message":                             // required
                    "My message body",
            },
    }

Dole je příklad akce témata sběrnice.

  "action": { "type": "serviceBusTopic", "serviceBusTopicMessage": { "topicPath": "t1",  
      "namespace": "mySBNamespace", "transportType": "netMessaging", // Can be either netMessaging or AMQP "authentication": { "sasKeyName": "QPolicy", "type": "sharedAccessKey" }, "message": "Some message", "brokeredMessageProperties": {}, "customMessageProperties": { "appname": "FromScheduler" } }, }

Dole je příklad akce fronty sběrnice:

  "action": { "serviceBusQueueMessage": { "queueName": "q1",  
      "namespace": "mySBNamespace", "transportType": "netMessaging", // Can be either netMessaging or AMQP "authentication": {  
        "sasKeyName": "QPolicy", "type": "sharedAccessKey" }, "message": "Some message",  
      "brokeredMessageProperties": {}, "customMessageProperties": { "appname": "FromScheduler" } }, "type": "serviceBusQueue" }

„errorAction“ je obslužná rutina chyb – akce vyvolaná při selhání primární akce. Tuto proměnnou můžete použít k zavolání koncového hodu pro zpracování chyby nebo odeslání oznámení pro uživatele. To se může použít pro spojení se sekundárním koncovým bodem v případě, že primární koncový bod není dostupný (např. při nehodě nebo přírodní katastrofě v lokalitě koncového bodu) nebo se může použít pro upozornění koncového bodu pro zpracování chyby. Stejně jako primární akce může i obslužná rutina mít jednoduchou nebo složenou logiku podle jiných akcí. Pokud se chcete dozvědět, jak vytvořit token SAS, podívejte se na téma [Vytvoření a používání sdíleného přístupového podpisu](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## <a name="recurrence"></a>recurrence
Opakování má několik částí:

* Frequency (frekvence): Minuta, hodina, den, týden, měsíc nebo rok  
* Interval: Interval při dané frekvenci opakování  
* Prescribed schedule (předepsaný plán): Minuty, hodiny, dny v týdnu, měsíce a dny v měsíci pro opakování  
* Count (počet): Počet výskytů  
* End time (čas ukončení): Po zadaném čase ukončení se neprovedou žádné úlohy  

Úloha se opakuje, jestliže má ve své definici JSON opakující se objekt. Pokud jsou zadané count i endTime, má přednost to pravidlo dokončení, které nastane jako první.

## <a name="state"></a>state
Stav (state) úlohy může mít jednu ze čtyř hodnot: enabled (zapnuto, disabled (vypnuto), completed (dokončeno) nebo faulted (chyba). Pomocí PUT nebo PATCH můžete aktualizovat stav úloh a tím jim přidělit stav zapnuto nebo vypnuto. Pokud má úloha stav dokončeno nebo chyba, je to konečný stav, který se už nedá změnit (na úlohu ale stále můžete použít DELETE). Příklad vlastnosti state:

        "state": "disabled", // enabled, disabled, completed, or faulted
Dokončené úlohy a úlohy, které selhaly, se odstraní po 60 dnech.

## <a name="status"></a>status
Po zahájení úlohy Scheduleru se vrátí informace o aktuálním statusu (stavu) úlohy. Uživatel nemůže tento objekt nastavit – nastaví ho systém. Je ale obsažený v objektu úlohy (a ne v samostatném propojeném prostředku), aby se mohl snadno získat stav (status) úlohy.

Stav (status) úlohy obsahuje čas předchozího spuštění (pokud existuje), čas dalšího naplánovaného spuštění (pro probíhající úlohy) a počet provedení úlohy.

## <a name="retrypolicy"></a>retryPolicy
Pro případ, že úloha Scheduleru selže, se můžou zadat zásady opakovaných pokusů a pomocí nich se může nastavit, jestli se má pokus o provedení úlohy opakovat a když ano, tak jak. To se nastavuje pomocí objektu **retryType** – pokud nejsou žádné zásady opakovaných pokusů, jako v příkladu nahoře, je nastavený na **none**. Pokud zásady opakovaných pokusů existují, nastavte ho na **fixed**.

Pokud chcete nastavit zásady opakovaných pokusů, můžete nastavit další dvě nastavení: interval opakovaných pokusů (**retryInterval**) a počet opakovaných pokusů (**retryCount**).

Interval opakovaných pokusů zadaný v objektu **retryInterval** je interval mezi opakovanými pokusy. Výchozí hodnota je 30 sekund, minimální nastavitelná hodnota je 15 sekund a maximální hodnota je 18 měsíců. Definuje se ve formátu ISO 8601. Podobně je to s hodnotou počtu opakovaných pokusů nastavených v objektu **retryCount** – je to počet, kolikrát se má pokus opakovat. Výchozí hodnota je 4 a maximální hodnota je 20. Oba objekty **retryInterval** a **retryCount** jsou volitelné. Pokud je objekt **retryType** nastavený na **fixed** a nejsou explicitně zadané žádné konkrétní hodnoty, použijí se výchozí hodnoty.

## <a name="see-also"></a>Viz také
 [Co je Scheduler?](scheduler-intro.md)

 [Úvod do používání Scheduleru na portálu Azure Portal](scheduler-get-started-portal.md)

 [Plány a fakturace v Azure Scheduleru](scheduler-plans-billing.md)

 [Sestavení komplexních plánů a pokročilé opakování v Azure Scheduleru](scheduler-advanced-complexity.md)

 [REST API Azure Scheduleru – referenční informace](https://msdn.microsoft.com/library/mt629143)

 [Rutiny PowerShellu pro Azure Scheduler – referenční informace](scheduler-powershell-reference.md)

 [Vysoká dostupnost a spolehlivost Azure Scheduleru](scheduler-high-availability-reliability.md)

 [Omezení, výchozí hodnoty a chybové kódy Azure Scheduleru](scheduler-limits-defaults-errors.md)

 [Odchozí ověření Azure Scheduleru](scheduler-outbound-authentication.md)

