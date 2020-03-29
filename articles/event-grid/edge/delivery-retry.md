---
title: Doručování a opakování – Azure Event Grid IoT Edge | Dokumenty společnosti Microsoft
description: Doručení a opakování v event gridu na IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7df283b12a0d04d2b785c13a2f12b03115581e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841708"
---
# <a name="delivery-and-retry"></a>Doručování a opakované pokusy

Event Grid poskytuje trvalé doručení. Pokusí se doručit každou zprávu alespoň jednou pro každý odpovídající předplatné okamžitě. Pokud koncový bod odběratele nepotvrdí přijetí události nebo pokud dojde k selhání, event grid opakuje doručení na základě pevného **plánu opakování** a **zásady opakování**.  Ve výchozím nastavení modul Event Grid doručuje účastníkovi jednu událost najednou. Datová část je však pole s jedinou událostí. Modul může dodávat více než jednu událost najednou povolením funkce dávkování výstupu. Podrobnosti o této funkci naleznete v [tématu výstupní dávkování](delivery-output-batching.md).  

> [!IMPORTANT]
>Neexistuje žádná podpora trvalosti pro data událostí. To znamená, že opětovné nasazení nebo restartování modulu Event Grid způsobí ztrátu všech událostí, které ještě nebyly doručeny.

## <a name="retry-schedule"></a>Plán opakování

Event Grid čeká až 60 sekund na odpověď po doručení zprávy. Pokud koncový bod odběratele není Potvrzení odpověď, pak zpráva bude zařazena do fronty pro naše back off fronty pro následné opakování.

Existují dvě předem nakonfigurované fronty vypnutí, které určují plán, na kterém bude pokus o opakování. Jsou to tyto:

| Plán | Popis |
| ---------| ------------ |
| 1 minuta | Zprávy, které skončí tady, se pokoušejí každou minutu.
| 10 minut | Zprávy, které skončí tady, se pokoušejí každou 10.

### <a name="how-it-works"></a>Jak to funguje

1. Zpráva dorazí do modulu Mřížka událostí. Je učiněn pokus o jeho okamžité doručení.
1. Pokud se doručení nezdaří, zpráva je zařazena do fronty 1 minuty a po minutě bude opakována.
1. Pokud doručení stále nezdaří, pak zpráva je zařazendo fronty 10 minut a opakovat každých 10 minut.
1. Dodávek se pokoušíte, dokud není dosaženo omezení zásad úspěšného opakování nebo opakování.

## <a name="retry-policy-limits"></a>Omezení zásad opakování

Existují dvě konfigurace, které určují zásady opakování. Jsou to tyto:

* Maximální počet pokusů
* Aktuální čas události (TTL)

Událost bude zrušena, pokud je dosaženo některého z omezení zásady opakování. Samotný plán opakování byl popsán v části Plán opakování. Konfigurace těchto limitů lze provést buď pro všechny předplatitele nebo na základě předplatného. Následující část popisuje každý z nich je další podrobnosti.

## <a name="configuring-defaults-for-all-subscribers"></a>Konfigurace výchozích hodnot pro všechny předplatitele

Existují dvě vlastnosti: `brokers__defaultMaxDeliveryAttempts` a `broker__defaultEventTimeToLiveInSeconds` které lze nakonfigurovat jako součást nasazení Event Grid, který řídí výchozí zásady opakování pro všechny předplatitele.

| Název vlastnosti | Popis |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Maximální počet pokusů o doručení události. Výchozí hodnota: 30.
| `broker__defaultEventTimeToLiveInSeconds` | TTL události v sekundách, po které bude událost zrušena, pokud není doručena. Výchozí hodnota: **7200** sekund

## <a name="configuring-defaults-per-subscriber"></a>Konfigurace výchozích hodnot pro odběratele

Můžete také zadat omezení zásad opakování na základě předplatného.
Informace o konfiguraci výchozích hodnot pro předplatitele najdete v naší dokumentaci k [rozhraní API.](api.md) Výchozí nastavení úrovně předplatného přepíše konfigurace na úrovni modulu.

## <a name="examples"></a>Příklady

Následující příklad nastaví zásady opakování v modulu Event Grid s maxNumberOfAttempts = 3 a TTL události 30 minut

```json
{
  "Env": [
    "broker__defaultMaxDeliveryAttempts=3",
    "broker__defaultEventTimeToLiveInSeconds=1800"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

Následující příklad nastaví předplatné webového háku s maxNumberOfAttempts = 3 a TTL události 30 minut

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": "eventgridschema"
   }
  },
  "retryPolicy": {
   "eventExpiryInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
}
```
