---
title: Koncepty, termíny a entity
description: Seznamte se s koncepty, terminologií a hierarchií entit, včetně úloh a kolekcí úloh, ve službě Azure Scheduler.
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: conceptual
ms.date: 08/18/2016
ms.openlocfilehash: 899c64e818896cde18e955d6abd82594734c4b57
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368158"
---
# <a name="concepts-terminology-and-entities-in-azure-scheduler"></a>Koncepty, terminologie a entity ve službě Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje [vyřazení](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)Azure Scheduleru. Pokud chcete pokračovat v práci s úlohami, které jste nastavili v plánovači, [migrujte prosím na Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) co nejdříve. 
>
> Plánovač již není v Azure Portal k dispozici, ale [rutiny prostředí PowerShell](scheduler-powershell-reference.md) [REST API](/rest/api/scheduler) a Azure Scheduler jsou v tuto chvíli dostupné, abyste mohli spravovat úlohy a kolekce úloh.

## <a name="entity-hierarchy"></a>Hierarchie entit

Rozhraní REST API služby Scheduler zpřístupňuje a používá tyto hlavní entity neboli prostředky:

| Entita | Popis |
|--------|-------------|
| **Úloha** | Definuje jednu opakující se akci s jednoduchými nebo komplexními strategiemi provedení. Akce můžou zahrnovat požadavky HTTP, fronty úložiště, fronty služby Service Bus nebo tématu služby Service Bus. | 
| **Kolekce úloh** | Obsahuje skupinu úloh a zachovává nastavení, kvóty a omezení, které jsou sdílené mezi úlohami v kolekci. Jako vlastník předplatného Azure můžete vytvářet kolekce úloh a seskupovat úlohy na základě jejich využití nebo hranic aplikací. Kolekce úloh má tyto vlastnosti: <p>– Je omezená na jednu oblast. <br>– Umožňuje vynucovat kvóty, takže můžete omezit využití všech úloh v kolekci. <br>– Mezi kvóty patří například MaxJobs a MaxRecurrence. | 
| **Historie úlohy** | Popisuje podrobnosti provádění úlohy, například stav a všechny podrobnosti odpovědi. |
||| 

## <a name="entity-management"></a>Správa entit

Na nejvyšší úrovni rozhraní REST API služby Scheduler zpřístupňuje tyto operace pro správu entit.

### <a name="job-management"></a>Správa úloh

Podporuje operace vytváření a úpravy úloh. Všechny úlohy musí patřit do existující kolekce, aby se implicitně nevytvářely další kolekce. Další informace najdete v tématu [Rozhraní REST API služby Scheduler – Úlohy](/rest/api/scheduler/jobs). Zde je adresa identifikátoru URI pro tyto operace:

```
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}
```

### <a name="job-collection-management"></a>Správa kolekce úloh

Podporuje operace vytváření a úpravy úloh a kolekce úloh, které se mapují na kvóty a sdílená nastavení. Kvóty určují například maximální počet úloh nebo nejmenší interval opakování. Další informace najdete v tématu [Rozhraní REST API služby Scheduler – Kolekce úloh](/rest/api/scheduler/jobcollections). Zde je adresa identifikátoru URI pro tyto operace:

```
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}
```

### <a name="job-history-management"></a>Správa historie úloh

Podporuje operaci GET pro načtení historie provádění úloh, jako je uplynulá doba úlohy a výsledky provedení úlohy, za posledních 60 dní. Zahrnuje podporu parametru řetězce dotazu pro filtrování podle stavu a statusu. Další informace najdete v tématu [Rozhraní REST API služby Scheduler – Úlohy – Výpis historie úloh](/rest/api/scheduler/jobs/listjobhistory). Zde je adresa identifikátoru URI pro tuto operaci:

```
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history
```

## <a name="job-types"></a>Typy úloh

Azure Scheduler podporuje několik typů úloh: 

* Úlohy HTTP, včetně úloh HTTPS, které podporují protokol TLS, pokud máte koncový bod pro existující službu nebo úlohu
* Úlohy fronty úložiště pro úlohy využívající fronty úložiště, jako je odesílání zpráv do front úložiště
* Úlohy fronty služby Service Bus pro úlohy využívající fronty služby Service Bus
* Úlohy tématu služby Service Bus pro úlohy využívající témata služby Service Bus

## <a name="job-definition"></a>Definice úlohy

Úloha služby Scheduler se skládá z těchto tří základních částí:

* Akce, která se spustí, když se aktivuje časovač úlohy
* Volitelné: Čas, kdy se má úloha spustit
* Volitelné: Kdy a jak často se má úloha opakovat
* Volitelné: Akce při chybě, která se spustí, pokud primární akce selže

Úloha obsahuje také data poskytnutá systémem, jako je například čas dalšího plánovaného spuštění úlohy. Definice kódu úlohy je objekt ve formátu JSON (JavaScript Object Notation), který obsahuje tyto elementy:

| Prvek | Povinné | Popis | 
|---------|----------|-------------| 
| [**Spuštění**](#start-time) | Ne | Čas spuštění úlohy s posunem časového pásma ve [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) | 
| [**kroky**](#action) | Ano | Podrobnosti pro primární akci, které můžou zahrnovat objekt **errorAction** | 
| [**errorAction**](#error-action) | Ne | Podrobnosti pro sekundární akci, která se spustí, pokud primární akce selže |
| [**vzorec**](#recurrence) | Ne | Podrobnosti pro opakující se úlohu, například frekvence a interval opakování | 
| [**retryPolicy**](#retry-policy) | Ne | Podrobnosti o tom, jak často se má akce opakovat | 
| [**státech**](#state) | Ano | Podrobnosti o aktuálním stavu úlohy |
| [**stav**](#status) | Ano | Podrobnosti o aktuálním statusu úlohy, který řídí služba |
||||

Tady je příklad, který ukazuje komplexní definici úlohy pro akci HTTP (podrobnější popis elementů najdete v dalších částech): 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z",
   "action": {
      "type": "Http",
      "request": {
         "uri": "http://contoso.com/some-method", 
         "method": "PUT",          
         "body": "Posting from a timer",
         "headers": {
            "Content-Type": "application/json"
         },
         "retryPolicy": { 
             "retryType": "None" 
         },
      },
      "errorAction": {
         "type": "Http",
         "request": {
            "uri": "http://contoso.com/notifyError",
            "method": "POST"
         }
      }
   },
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]
      },
      "count": 10,
      "endTime": "2012-11-04"
   },
   "state": "Disabled",
   "status": {
      "lastExecutionTime": "2007-03-01T13:00:00Z",
      "nextExecutionTime": "2007-03-01T14:00:00Z ",
      "executionCount": 3,
      "failureCount": 0,
      "faultedCount": 0
   }
}
```

<a name="start-time"></a>

## <a name="starttime"></a>startTime

V objektu **startTime** můžete zadat čas spuštění a posun časového pásma ve [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).

<a name="action"></a>

## <a name="action"></a>akce

Úloha služby Scheduler spouští primární **akci** podle zadaného plánu. Scheduler podporuje akce HTTP, fronty úložiště, fronty služby Service Bus a tématu služby Service Bus. Pokud primární **akce** selže, může Scheduler spustit sekundární akci [**errorAction**](#erroraction), která chybu zpracuje. Objekt **action** popisuje tyto elementy:

* Typ služby akce
* Podrobnosti akce
* Alternativní akci **errorAction**

Předchozí příklad popisuje akci HTTP. Tady je příklad akce fronty úložiště:

```json
"action": {
   "type": "storageQueue",
   "queueMessage": {
      "storageAccount": "myStorageAccount",  
      "queueName": "myqueue",                
      "sasToken": "TOKEN",                   
      "message": "My message body"
    }
}
```

Tady je příklad akce fronty služby Service Bus:

```json
"action": {
   "type": "serviceBusQueue",
   "serviceBusQueueMessage": {
      "queueName": "q1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {  
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",  
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

Tady je příklad akce tématu služby Service Bus:

```json
"action": {
   "type": "serviceBusTopic",
   "serviceBusTopicMessage": {
      "topicPath": "t1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

Další informace o tokenech sdíleného přístupového podpisu (SAS) najdete v tématu [Autorizace s využitím sdílených přístupových podpisů](../storage/common/storage-sas-overview.md).

<a name="error-action"></a>

## <a name="erroraction"></a>errorAction

Pokud primární **akce** úlohy selže, může Scheduler spustit akci **errorAction**, která chybu zpracuje. V primární **akci** můžete zadat objekt **errorAction** a tím umožnit službě Scheduler zavolat koncový bod pro zpracování chyb nebo odeslat oznámení uživateli. 

Pokud například dojde k havárii na primárním koncovém bodu, můžete pomocí akce **errorAction** volat sekundární koncový bod nebo odeslat oznámení do koncového bodu pro zpracování chyb. 

Stejně jako primární **akce** může i akce při chybě využívat jednoduchou nebo složenou logiku na základě jiných akcí. 

<a name="recurrence"></a>

## <a name="recurrence"></a>recurrence

Pokud definice JSON úlohy zahrnuje objekt **recurrence**, úloha se bude opakovat. Příklad:

```json
"recurrence": {
   "frequency": "Week",
   "interval": 1,
   "schedule": {
      "hours": [10, 22],
      "minutes": [0, 30],
      "weekDays": ["Monday", "Wednesday", "Friday"]
   },
   "count": 10,
   "endTime": "2012-11-04"
},
```

| Vlastnost | Požaduje se | Hodnota | Popis | 
|----------|----------|-------|-------------| 
| **frequency** | Ano, pokud se používá **opakování** | Minute (minuta), Hour (hodina), Day (den), Week (týden), Month (měsíc), Year (rok) | Časová jednotka intervalu mezi opakováními | 
| **interval** | Ne | 1 až 1000 (včetně) | Kladné číslo, které určuje počet časových jednotek mezi jednotlivými opakováními na základě vlastnosti **frequency** | 
| **CXL** | Ne | Různé | Podrobnosti pro složitější a pokročilejší plány. Viz **hours**, **minutes**, **weekDays**, **months** a **monthDays**. | 
| **hodin** | Ne | 1 až 60 | Pole s hodinami, kdy se má úloha spustit | 
| **minuty** | Ne | 0 až 59 | Pole s minutami, kdy se má úloha spustit | 
| **months** | Ne | 1 až 12 | Pole s měsíci, kdy se má úloha spustit | 
| **monthDays** | Ne | Různé | Pole se dny v měsíci, kdy se má úloha spustit | 
| **weekDays** | Ne | Monday (pondělí), Tuesday (úterý), Wednesday (středa), Thursday (čtvrtek), Friday (pátek), Saturday (sobota), Sunday (neděle) | Pole se dny v týdnu, kdy se má úloha spustit | 
| **výpočtu** | Ne | <*nTato*> | Počet opakování. Ve výchozím nastavení se opakování bude provádět donekonečna. Není možné použít vlastnost **count** a zároveň **endTime** – přednost bude mít pravidlo, které se dokončí jako první. | 
| **endTime** | Ne | <*nTato*> | Datum a čas, kdy se má opakování zastavit. Ve výchozím nastavení se opakování bude provádět donekonečna. Není možné použít vlastnost **count** a zároveň **endTime** – přednost bude mít pravidlo, které se dokončí jako první. | 
||||

Další informace o těchto elementech najdete v tématu [Vytváření složitých plánů a pokročilých opakování](../scheduler/scheduler-advanced-complexity.md).

<a name="retry-policy"></a>

## <a name="retrypolicy"></a>retryPolicy

Pro případ, že by došlo k selhání úlohy služby Scheduler, můžete nastavit zásadu opakování, která určuje, jestli a jak má služba Scheduler akci zopakovat. Ve výchozím nastavení zopakuje služba Scheduler úlohu ještě čtyřikrát v 30sekundových intervalech. Agresivitu této zásady můžete zvýšit nebo snížit například tak, aby zásada opakovala akci dvakrát denně:

```json
"retryPolicy": { 
   "retryType": "Fixed",
   "retryInterval": "PT1D",
   "retryCount": 2
},
```

| Vlastnost | Požaduje se | Hodnota | Popis | 
|----------|----------|-------|-------------| 
| **retryType** | Ano | **Pevné**, **Žádné** | Určuje, jestli jste zadali určili zásadu opakování (**pevné**), nebo ne (**žádné**). | 
| **retryInterval** | Ne | PT30S | Určuje interval a frekvenci opakovaných pokusů ve [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Minimální hodnota je 15 sekund a maximální hodnota je 18 měsíců. | 
| **retryCount** | Ne | 4 | Určuje počet opakovaných pokusů. Maximální hodnota je 20. | 
||||

Další informace najdete v tématu [Vysoká dostupnost a spolehlivost](../scheduler/scheduler-high-availability-reliability.md).

<a name="status"></a>

## <a name="state"></a>state

Stav úlohy je **Povoleno**, **Zakázáno**, **Dokončeno** nebo **Došlo k chybě**. Příklad: 

`"state": "Disabled"`

Pokud chcete změnit stav úloh na **Povoleno** nebo **Zakázáno**, můžete pro tyto úlohy použít operaci PUT nebo PATCH.
Pokud je však úloha ve stavu **Dokončeno** nebo **Došlo k chybě**, její stav aktualizovat nemůžete, ale můžete pro ni provést operaci DELETE. Dokončené úlohy a úlohy, které selhaly, Scheduler po 60 dnech odstraní. 

<a name="status"></a>

## <a name="status"></a>status

Po spuštění úlohy vrací Scheduler informace o stavu úlohy prostřednictvím objektu **status**, který řídí pouze služba Scheduler. Objekt **status** však můžete najít v objektu **job**. Stav úlohy zahrnuje následující informace:

* Čas předchozího spuštění, pokud k nějakému došlo
* Čas dalšího naplánovaného spuštění u probíhajících úloh
* Počet spuštění úlohy
* Počet selhání, pokud k nějakým došlo
* Počet chyb, pokud k nějakým došlo

Příklad:

```json
"status": {
   "lastExecutionTime": "2007-03-01T13:00:00Z",
   "nextExecutionTime": "2007-03-01T14:00:00Z ",
   "executionCount": 3,
   "failureCount": 0,
   "faultedCount": 0
}
```

## <a name="next-steps"></a>Další kroky

* [Vytváření složitých plánů a pokročilých opakování](scheduler-advanced-complexity.md)
* [REST API Azure Scheduleru – referenční informace](/rest/api/scheduler)
* [Rutiny PowerShellu pro Azure Scheduler – referenční informace](scheduler-powershell-reference.md)
* [Omezení, kvóty, výchozí hodnoty a kódy chyb](scheduler-limits-defaults-errors.md)