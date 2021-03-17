---
title: Doručení a opakování – Azure Event Grid IoT Edge | Microsoft Docs
description: Doručení a opakování Event Grid v IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: aa0b3a05fb26f6be951b697145d7b22e03b7792d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171614"
---
# <a name="delivery-and-retry"></a>Doručování a opakované pokusy

Event Grid poskytuje trvalé doručování. Pokusí se okamžitě doručovat každou zprávu alespoň jednou pro každé vyhovující předplatné. Pokud koncový bod předplatitele nepotvrdí příjem události nebo dojde k selhání, Event Grid pokusy o doručení na základě pevného **plánu opakování** a **zásady opakování**.  Ve výchozím nastavení modul Event Grid poskytuje odběrateli jednu událost současně. Datová část je však pole s jedinou událostí. Modul může současně poskytovat více než jednu událost tím, že povoluje výstupní dávkování funkce. Podrobnosti o této funkci najdete v tématu [výstupní dávkování](delivery-output-batching.md).  

> [!IMPORTANT]
>Pro data událostí neexistuje žádná podpora trvalosti. To znamená, že při opětovném nasazení nebo restartování modulu Event Grid dojde ke ztrátě všech událostí, které ještě nejsou doručeny.

## <a name="retry-schedule"></a>Plán opakování

Po doručení zprávy vyčká Event Grid až 60 sekund na odpověď. Pokud koncový bod předplatitele neobdrží odpověď, bude zpráva zařazená do fronty v jedné z back-mailových front pro následné pokusy.

K dispozici jsou dvě předem nakonfigurované back-Queues, které určují plán, podle kterého se bude opakovat pokus. Jsou to tyto:

| Plán | Description |
| ---------| ------------ |
| 1 min. | Zprávy, které končí, se pokoušejí každou minutu.
| 10 minut | Zprávy, které se tady ukončí, se pokoušejí každou desátou minutu.

### <a name="how-it-works"></a>Jak to funguje

1. Zpráva se dorazí do modulu Event Grid. Byl proveden pokus o okamžité doručení.
1. Pokud se doručení nepovede, zpráva se zařadí do fronty o 1 minutu a zkusí se znovu po minutě.
1. Pokud bude doručování dál neúspěšné, zpráva se zařadí do fronty o 10 minut a zkusí se znovu každých 10 minut.
1. Dokončí se doručení, dokud neproběhne úspěšně, nebo se nedosáhne omezení zásad opakování.

## <a name="retry-policy-limits"></a>Omezení zásad opakování

Existují dvě konfigurace, které určují zásady opakování. Jsou to tyto:

* Maximální počet pokusů
* Hodnota TTL (Time to Live)

Pokud je dosažena kterákoli z omezení zásady opakování, bude událost zahozena. Samotný plán opakování byl popsaný v oddílu plán opakování. Konfiguraci těchto omezení je možné provést pro všechny předplatitele nebo pro každé předplatné. V následující části jsou popsány další podrobnosti.

## <a name="configuring-defaults-for-all-subscribers"></a>Konfigurace výchozích hodnot pro všechny předplatitele

Existují dvě vlastnosti: `brokers__defaultMaxDeliveryAttempts` a `broker__defaultEventTimeToLiveInSeconds` , které je možné nakonfigurovat jako součást nasazení Event Grid, které řídí výchozí nastavení zásad opakování pro všechny předplatitele.

| Název vlastnosti | Description |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Maximální počet pokusů o doručení události Výchozí hodnota: 30.
| `broker__defaultEventTimeToLiveInSeconds` | Hodnota TTL události v sekundách, po které bude událost vyřazena, pokud nebyla doručena. Výchozí hodnota: **7200** sekund

## <a name="configuring-defaults-per-subscriber"></a>Konfigurace výchozích hodnot na odběratele

Omezení zásad opakování můžete zadat také pro každé předplatné.
V [dokumentaci k rozhraní API](api.md) najdete informace o tom, jak nakonfigurovat výchozí hodnoty pro předplatitele. Výchozí hodnoty na úrovni předplatného přepíšou konfigurace na úrovni modulu.

## <a name="examples"></a>Příklady

Následující příklad nastaví zásady opakování v modulu Event Grid s hodnotou maxNumberOfAttempts = 3 a hodnotou TTL události 30 minut.

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

Následující příklad nastaví odběr webového zavěšení s maxNumberOfAttempts = 3 a hodnotou TTL události 30 minut.

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
