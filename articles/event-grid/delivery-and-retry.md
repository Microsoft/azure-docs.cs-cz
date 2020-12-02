---
title: Azure Event Grid doručování a opakování
description: Popisuje, jak Azure Event Grid doručuje události a jak zpracovává nedoručené zprávy.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 51473cf457a1c713e6694edd23c344be8c4d439e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463245"
---
# <a name="event-grid-message-delivery-and-retry"></a>Doručování zpráv Event Grid a opakování

Tento článek popisuje, jak Azure Event Grid zpracovává události v případě, že doručení není potvrzené.

Event Grid poskytuje trvalé doručování. Každou zprávu pro každé předplatné zajišťuje **aspoň jednou** . Události se odesílají do registrovaného koncového bodu každého předplatného hned. Pokud koncový bod nepotvrdí příjem události, Event Grid pokusy o doručení události.

> [!NOTE]
> Event Grid nezaručuje obdobu doručování událostí, takže předplatitelé je mohou obdržet mimo pořadí. 

## <a name="batched-event-delivery"></a>Doručování událostí v dávce

Event Grid ve výchozím nastavení odesílá každou událost jednotlivě předplatitelům. Předplatitel obdrží pole s jednou událostí. Můžete nakonfigurovat Event Grid pro dávkové zpracování událostí pro doručování za účelem zvýšení výkonu protokolu HTTP ve scénářích s vysokou propustností.

Dávkové doručování má dvě nastavení:

* Maximální počet **událostí na dávku** – maximální počet událostí, které Event Grid bude poskytovat za dávku. Toto číslo nebude nikdy překročeno, ale pokud v době publikování nejsou k dispozici žádné další události, mohou být doručeny méně událostí. Event Grid nezpozdí události, aby se vytvořila dávka, pokud je k dispozici méně událostí. Musí být v rozmezí od 1 do 5 000.
* **Upřednostňovaná velikost dávky v kilobajtech** – cílový strop pro velikost dávky v kilobajtech Podobně jako u maximálního počtu událostí může být velikost dávky menší, pokud v době publikování nejsou k dispozici další události. Je možné, že je dávka větší než upřednostňovaná velikost dávky, *Pokud* je jedna událost větší než upřednostňovaná velikost. Pokud je například upřednostňovaná velikost 4 KB a událost o velikosti 10 KB je vložená do Event Grid, bude se událost o velikosti 10 KB stále doručovat do vlastní dávky místo toho, aby se vynechala.

Dávkové doručování je nakonfigurované na základě předplatného pro jednotlivé události prostřednictvím portálu, rozhraní příkazového řádku, PowerShellu nebo sad SDK.

### <a name="azure-portal"></a>Azure Portal: 
![Nastavení dávkového doručování](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure CLI
Při vytváření odběru událostí použijte následující parametry: 

- **Maximum-události – za-Batch** – maximální počet událostí v dávce. Hodnota musí být číslo mezi 1 a 5000.
- **preferované – velikost** dávky-v kilobajtech – upřednostňovaná velikost dávky v kilobajtech Hodnota musí být číslo mezi 1 a 1024.

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

Další informace o použití rozhraní příkazového řádku Azure s Event Grid najdete v tématu [Směrování událostí úložiště do webového koncového bodu pomocí Azure CLI](../storage/blobs/storage-blob-event-quickstart.md).

## <a name="retry-schedule-and-duration"></a>Plán opakování a doba trvání

Pokud EventGrid obdrží při pokusu o doručení události chybu, EventGrid rozhodne, zda má opakovat doručení nebo nedoručené písmeno, nebo událost vyřaďte na základě typu chyby. 

Pokud chyba vrácená koncovým bodem nastavila chybu související se konfigurací, která se nedá opravit pomocí opakování (například při odstranění koncového bodu), EventGrid buď neodstraní odkládání události, nebo událost vyřaďte, pokud není nenakonfigurované žádné nedoručené písmeno.

Níže jsou uvedené typy koncových bodů, pro které opakování neproběhne:

| Typ koncového bodu | Kódy chyb |
| --------------| -----------|
| Prostředky Azure | 400 Chybný požadavek, příliš velký entita žádosti 413, 403 zakázáno | 
| Webhook | 400 Chybný požadavek, je příliš velká entita žádosti 413, 403 zakázáno, 404 Nenalezeno, 401 Neautorizováno |
 
> [!NOTE]
> Pokud pro koncový bod není nakonfigurované Dead-Letter, události se při výskytu chyb ztratí, takže pokud nechcete, aby se tyto druhy událostí vynechaly, zvažte konfiguraci nedoručených zpráv.

Pokud chyba vrácená koncovým bodem není mezi seznamem výše, EventGrid provede opakování pomocí zásad popsaných níže:

Po doručení zprávy vyčká Event Grid 30 sekund na odpověď. Pokud koncový bod nereagoval po 30 sekundách, zpráva se zařadí do fronty pro opakování. Event Grid používá pro doručování událostí exponenciální zásady opakování omezení rychlosti. Event Grid se pokusy o doručení podle následujícího plánu doručovat na nejlepší úsilí:

- 10 sekund
- 30 sekund
- 1 min.
- 5 minut
- 10 minut
- 30 minut
- 1 hodina
- Po hodinách po dobu až 24 hodin

Pokud koncový bod během 3 minut odpoví, Event Grid se pokusí odebrat událost z fronty opakovaných pokusů, ale stále se můžou přijmout duplicity.

Event Grid přidá ke všem opakovaným krokům malou náhodnost a může oportunisticky přeskočit některé opakované pokusy, pokud je koncový bod konzistentně nezdravý, v dlouhou dobu nebo se může převažovat za přetížený.

V případě deterministického chování nastavte čas události na živý a maximální počet pokusů o doručení v [zásadách opakování předplatného](manage-event-delivery.md).

Ve výchozím nastavení Event Grid vyprší platnost všech událostí, které nejsou dodány do 24 hodin. [Zásady opakování můžete přizpůsobit](manage-event-delivery.md) při vytváření odběru události. Zadáváte maximální počet pokusů o doručení (výchozí hodnota je 30) a čas do provozu události (výchozí hodnota je 1440 minut).

## <a name="delayed-delivery"></a>Zpožděné doručení

V případě selhání doručení koncovým bodem se Event Grid začne zpozdit doručení a opakovat události do tohoto koncového bodu. Pokud například selže prvních 10 událostí publikovaných do koncového bodu, Event Grid bude předpokládat, že u koncového bodu dochází k problémům, budou všechny následné pokusy *a nové* doručení v některých případech trvat až několik hodin.

Funkčním účelem opožděného doručení je chránit nestavové koncové body i Event Grid systém. Bez zálohování a zpoždění doručování do špatných koncových bodů se můžou zásady opakování Event Grid a možnosti svazku snadno přesazovat systémem.

## <a name="dead-letter-events"></a>Nedoručené události
Když Event Grid nemůže doručovat událost v určitém časovém období nebo po pokusu o doručení události v určitém počtu opakování, může odeslat nedoručenou událost do účtu úložiště. Tento proces se označuje jako **nedoručené**. Event Grid nedoručená událost, pokud je splněna **jedna z následujících** podmínek. 

- Událost není dodána v období od **času do Live** . 
- **Počet pokusů** o doručení události překročil limit.

Je-li splněna některá z podmínek, událost je vyřazena nebo byla nedoručena.  Ve výchozím nastavení Event Grid nezapne nedoručené písmeno. Pokud ho chcete povolit, musíte zadat účet úložiště pro ukládání nedoručených událostí při vytváření odběru události. Vyžádáte si události z tohoto účtu úložiště, abyste mohli vyřešit dodávky.

Event Grid pošle událost do umístění nedoručených zpráv, když se pokusí všechny jeho pokusy opakovat. Pokud Event Grid obdrží kód odpovědi 400 (špatný požadavek) nebo 413 (příliš velký požadavek na entitu požadavku), okamžitě ho pošle do koncového bodu se nedoručenými písmeny. Tyto kódy odpovědí ukazují, že doručení události nebude nikdy úspěšné.

Doba do živého vypršení platnosti se kontroluje jenom při příštím naplánovaném pokusu o doručení. Proto platí, že i když doba do provozu vyprší před dalším plánovaným pokusem o doručení, je tato událost kontrolována pouze v okamžiku příštího doručení a následně nedoručená. 

Poslední pokus o doručení události a při jejím doručování do umístění nedoručených zpráv je prodleva pět minut. Toto zpoždění má za cíl snížit počet operací BLOB Storage. Pokud umístění nedoručených zpráv není k dispozici po dobu čtyř hodin, událost se zahozena.

Před nastavením umístění nedoručených zpráv musíte mít účet úložiště s kontejnerem. Koncový bod pro tento kontejner zadáte při vytváření odběru události. Koncový bod je ve formátu: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Po odeslání události do umístění nedoručených zpráv můžete chtít být upozorněni. Pokud chcete použít Event Grid k reakci na nedoručené události, [Vytvořte odběr událostí](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) pro úložiště objektů BLOB s nedoručenými písmeny. Pokaždé, když úložiště BLOB nedoručených zpráv obdrží nedoručenou událost, Event Grid upozorní vaši obslužnou rutinu. Obslužná rutina reaguje na akce, které chcete provést pro sjednocení nedoručených událostí. Příklad nastavení umístění nedoručeného písmen a zásad opakování najdete v tématu [zásady nedoručených zpráv a opakování](manage-event-delivery.md).

## <a name="delivery-event-formats"></a>Formáty událostí doručení
V této části najdete příklady událostí a nedoručených událostí v různých formátech schématu doručení (Event Grid schéma, schéma CloudEvents 1,0 a vlastní schéma). Další informace o těchto formátech najdete v článku [Event Grid schématu](event-schema.md) a [cloudové události 1,0](cloud-event-schema.md) . 

### <a name="event-grid-schema"></a>Schéma služby Event Grid

#### <a name="event"></a>Událost 
```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/000000000-0000-0000-0000-00000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    }
}
```

#### <a name="dead-letter-event"></a>Nedoručená událost

```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    },

    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T17:18:14.0265758Z",
    "lastDeliveryAttemptTime": "2020-08-13T17:18:14.0465788Z" 
}
```

### <a name="cloudevents-10-schema"></a>Schéma CloudEvents 1,0

#### <a name="event"></a>Událost

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    }
}
```

#### <a name="dead-letter-event"></a>Nedoručená událost

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    },

    "deadletterreason": "MaxDeliveryAttemptsExceeded",
    "deliveryattempts": 1,
    "lastdeliveryoutcome": "NotFound",
    "publishtime": "2020-08-13T21:21:36.4018726Z",
}
```

### <a name="custom-schema"></a>Vlastní schéma

#### <a name="event"></a>Událost

```json
{
    "prop1": "my property",
    "prop2": 5,
    "myEventType": "fooEventType"
}

```

#### <a name="dead-letter-event"></a>Nedoručená událost
```json
{
    "id": "8bc07e6f-0885-4729-90e4-7c3f052bd754",
    "eventTime": "2020-08-13T18:11:29.4121391Z",
    "eventType": "myEventType",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/00000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.EventGrid/topics/myCustomSchemaTopic",
    "subject": "subjectDefault",
  
    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T18:11:29.4121391Z",
    "lastDeliveryAttemptTime": "2020-08-13T18:11:29.4277644Z",
  
    "data": {
        "prop1": "my property",
        "prop2": 5,
        "myEventType": "fooEventType"
    }
}
```


## <a name="message-delivery-status"></a>Stav doručení zprávy

Event Grid používá k potvrzení přijetí událostí kódy odpovědí HTTP. 

### <a name="success-codes"></a>Kódy úspěchu

Event Grid považuje za úspěšné doručení **pouze** následující kódy odpovědí HTTP. Všechny ostatní stavové kódy se považují za neúspěšné doručení a v případě potřeby se budou opakovat nebo deadlettered. Po přijetí úspěšného stavového kódu Event Grid považuje doručení za dokončenou.

- 200 OK
- 201 vytvořeno
- 202 přijato
- 203 neautoritativní informace
- 204 bez obsahu

### <a name="failure-codes"></a>Kódy chyb

Všechny ostatní kódy, které nejsou ve výše uvedené sadě (200-204), se považují za selhání a v případě potřeby se zopakují. Některé mají konkrétní zásady opakování, které jsou pro ně vázané níže. všechny ostatní se řídí standardním exponenciálním modelem. Je důležité mít na paměti, že kvůli vysoce paralelismuější povaze architektury Event Grid není chování při opakování deterministické. 

| Stavový kód | Chování opakování |
| ------------|----------------|
| 400 – Chybný požadavek | Nepokusit se znovu |
| 401 – Neautorizováno | Zkuste to znovu za 5 minut nebo více pro koncové body prostředků Azure. |
| 403 – Zakázáno | Nepokusit se znovu |
| 404 Nenalezeno | Zkuste to znovu za 5 minut nebo více pro koncové body prostředků Azure. |
| 408 – Časový limit žádosti | Opakovat po 2 nebo více minutách |
| Entita požadavku 413 je moc velká. | Nepokusit se znovu |
| 503 – Nedostupná služba | Opakovat po 30 sekundách nebo více |
| Všichni ostatní | Opakovat po 10 sekundách nebo více |


## <a name="next-steps"></a>Další kroky

* Pokud chcete zobrazit stav doručení událostí, přečtěte si téma [monitorování Event Grid doručování zpráv](monitor-event-delivery.md).
* Pokud chcete přizpůsobit možnosti doručení událostí, přečtěte si téma [zásady nedoručených zpráv a opakování](manage-event-delivery.md).
