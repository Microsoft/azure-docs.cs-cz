---
title: Referenční informace k rozhraní API pro Azure IoT Hub MQTT 5 (Preview)
description: Další informace o referenčních informacích IoT Hub k rozhraní API pro MQTT 5
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: reference
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: 5f0af7d6bf16a05fad1ca9df5db1729abd088010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96603330"
---
# <a name="iot-hub-data-plane-mqtt-5-api-reference"></a>Referenční informace k rozhraní API pro IoT Hub roviny dat MQTT 5

Tento dokument definuje operace, které jsou k dispozici ve verzi 2,0 (rozhraní API-Version: `2020-10-01-preview` ) IoT Hub rozhraní API roviny dat.

## <a name="operations"></a>Operace

### <a name="get-twin"></a>Získat dvojitou

Získat dvojitou stav

#### <a name="request"></a>Žádost

**Název tématu:**`$iothub/twin/get`

**Vlastnosti**: žádné

**Datová část**: Empty

#### <a name="success-response"></a>Úspěšná odpověď

**Vlastnosti**: žádné

**Datová část**: vláken

#### <a name="alternative-responses"></a>Alternativní odpovědi

| Status | Název | Description |
| :----- | :--- | :---------- |
| 0100 |  Chybný požadavek | Zpráva operace je poškozená a nelze ji zpracovat. |
| 0101 |  Neautorizováno | Klient nemá autorizaci k provedení této operace. |
| 0102 |  Nepovolené | Operace není povolena. |
| 0501 |  Omezené | počet požadavků je příliš vysoký na SKU. |
| 0502 |  Překročení kvóty | překročila se denní kvóta za aktuální SKU. |
| 0601 |  Chyba serveru | Vnitřní chyba serveru |
| 0602 |  Časový limit | operaci vypršel časový limit před tím, než mohla být dokončena. |
| 0603 |  Zaneprázdněný Server | zaneprázdněný Server |

#### <a name="pseudo-code-sample"></a>Ukázka pseudo-code

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="patch-twin-reported"></a>Nahlášená dvojitá Oprava

Chybně nahlášený stav opravy

#### <a name="request"></a>Žádost

**Název tématu:**`$iothub/twin/patch/reported`

**Vlastnosti**:

| Název | Typ | Vyžadováno | Popis |
| :--- | :--- | :------- | :---------- |
| If – verze | u64 | ne |  |

**Datová část**: TwinState

#### <a name="success-response"></a>Úspěšná odpověď

**Vlastnosti**:

| Název | Typ | Vyžadováno | Popis |
| :--- | :--- | :------- | :---------- |
| verze | u64 | ano | Verze hlášeného stavu po použití opravy |

**Datová část**: Empty

#### <a name="alternative-responses"></a>Alternativní odpovědi

| Status | Název | Description |
| :----- | :--- | :---------- |
| 0104 |  Předběžná podmínka se nezdařila. | Předběžná podmínka nebyla splněna, výsledkem je zrušení žádosti. |
| 0100 |  Chybný požadavek | Zpráva operace je poškozená a nelze ji zpracovat. |
| 0101 |  Neautorizováno | Klient nemá autorizaci k provedení této operace. |
| 0102 |  Nepovolené | Operace není povolena. |
| 0501 |  Omezené | počet požadavků je příliš vysoký na SKU. |
| 0502 |  Překročení kvóty | překročila se denní kvóta za aktuální SKU. |
| 0601 |  Chyba serveru | Vnitřní chyba serveru |
| 0602 |  Časový limit | operaci vypršel časový limit před tím, než mohla být dokončena. |
| 0603 |  Zaneprázdněný Server | zaneprázdněný Server |

#### <a name="pseudo-code-sample"></a>Ukázka pseudo-code

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/reported
    [if-version: <u64>]
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-commands"></a>Přijmout příkazy

Příkazy Receive a Handle

#### <a name="message"></a>Zpráva

**Název tématu:**`$iothub/commands`

**Vlastnosti**:

| Název | Typ | Vyžadováno | Popis |
| :--- | :--- | :------- | :---------- |
| sekvence – ne | u64 | ano | Pořadové číslo zprávy |
| ve frontě – doba | time | ano | Časové razítko při vstupu zprávy do systému |
| doručení – počet | u32 | ano | Počet pokusů o doručení zprávy |
| čas vytvoření | time | ne | Časové razítko, kdy se zpráva vytvořila (od odesílatele) |
| ID zprávy | řetězec | ne | Identita zprávy (poskytovaná odesílatelem) |
| user-id | řetězec | ne | Identita uživatele (od odesílatele) |
| correlation-id | řetězec | ne | Korelační identita (poskytnutá odesílatelem) |
| Typ obsahu | řetězec | ne | Určuje typ obsahu datové části. |
| kódování obsahu | řetězec | ne | Určuje kódování obsahu datové části. |

**Datová část**: jakákoli sekvence bajtů

#### <a name="success-acknowledgment"></a>Potvrzení úspěšného dokončení

Indikuje, že byl přijat příkaz pro zpracování klientem.

**Vlastnosti**: žádné

**Datová část**: Empty

#### <a name="alternative-acknowledgments"></a>Alternativní potvrzování

| Kód příčiny | Status | Název | Description |
| :---------- | :----- | :--- | :---------- |
| 131 | 0603 | Opustit | Indikuje, že příkaz nebude zpracován v tuto chvíli a měl by být znovu dodán v budoucnu. |
| 131 | 0100 | Odmítnout | Indikuje, že klient odmítl příkaz a neměl by se opakovat. |

#### <a name="pseudo-code-sample"></a>Ukázka pseudo-code

```

-> SUBSCRIBE
    - Topic: $iothub/commands
      QoS: 1
<- PUBLISH
    QoS: 1
    Topic: $iothub/commands
    sequence-no: <u64>enqueued-time: <time>delivery-count: <u32>[creation-time: <time>][message-id: <string>][user-id: <string>][correlation-id: <string>][Content Type: <string>][content-encoding: <string>]
    Payload: ...

-> PUBACK

```

### <a name="receive-direct-methods"></a>Příjem přímých metod

Příjem a zpracování volání přímých metod

#### <a name="request"></a>Žádost

**Název tématu:**`$iothub/methods/{name}`

**Vlastnosti**: žádné

**Datová část**: jakákoli sekvence bajtů

#### <a name="success-response"></a>Úspěšná odpověď

**Vlastnosti**:

| Název | Typ | Vyžadováno | Popis |
| :--- | :--- | :------- | :---------- |
| kód odpovědi | u32 | ano |  |

**Datová část**: jakákoli sekvence bajtů

#### <a name="alternative-responses"></a>Alternativní odpovědi

| Status | Název | Description |
| :----- | :--- | :---------- |
| 06A0 |  Neaktivní | Indikuje, že klient není přes toto připojení dosažitelný. |

#### <a name="pseudo-code-sample"></a>Ukázka pseudo-code

```

-> SUBSCRIBE
    - Topic: methods/{name}
      QoS: 0
<- SUBACK
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/{name}
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-twin-desired-state-changes"></a>Přijmout nepotřebné změny stavu

Získávat aktualizace požadovaného stavu vlákna

#### <a name="message"></a>Zpráva

**Název tématu:**`$iothub/twin/patch/desired`

**Vlastnosti**:

| Název | Typ | Vyžadováno | Popis |
| :--- | :--- | :------- | :---------- |
| verze | u64 | ano | Verze požadovaného stavu odpovídající této aktualizaci |

**Datová část**: TwinState

#### <a name="pseudo-code-sample"></a>Ukázka pseudo-code

```

-> SUBSCRIBE
    - Topic: $iothub/twin/patch/desired
      QoS: 0
<- PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/desired
    version: <u64>
    Payload: ...

```

### <a name="send-telemetry"></a>Poslat telemetrii

Odeslání zprávy na kanál telemetrie – EventHubs ve výchozím nastavení nebo jiném koncovém bodu prostřednictvím konfigurace směrování.

#### <a name="message"></a>Zpráva

**Název tématu:**`$iothub/telemetry`

**Vlastnosti**:

| Název | Typ | Vyžadováno | Popis |
| :--- | :--- | :------- | :---------- |
| Typ obsahu | řetězec | ne | překládá se na `content-type` vlastnost System u publikované zprávy. |
| kódování obsahu | řetězec | ne | překládá se na `content-encoding` vlastnost System u publikované zprávy. |
| ID zprávy | řetězec | ne | překládá se na `message-id` vlastnost System u publikované zprávy. |
| user-id | řetězec | ne | překládá se na `user-id` vlastnost System u publikované zprávy. |
| correlation-id | řetězec | ne | překládá se na `correlation-id` vlastnost System u publikované zprávy. |
| čas vytvoření | time | ne | překládá se na `iothub-creation-time-utc` vlastnost u vystavené zprávy. |

**Datová část**: jakákoli sekvence bajtů

#### <a name="success-acknowledgment"></a>Potvrzení úspěšného dokončení

Zpráva se úspěšně publikovala do kanálu telemetrie.

**Vlastnosti**: žádné

**Datová část**: Empty

#### <a name="alternative-acknowledgments"></a>Alternativní potvrzování

| Kód příčiny | Status | Název | Description |
| :---------- | :----- | :--- | :---------- |
| 131 | 0100 | Chybný požadavek | Zpráva operace je poškozená a nelze ji zpracovat. |
| 135 | 0101 | Neautorizováno | Klient nemá autorizaci k provedení této operace. |
| 131 | 0102 | Nepovolené | Operace není povolena. |
| 131 | 0601 | Chyba serveru | Vnitřní chyba serveru |
| 151 | 0501 | Omezené | počet požadavků je příliš vysoký na SKU. |
| 151 | 0502 | Překročení kvóty | překročila se denní kvóta za aktuální SKU. |
| 131 | 0602 | Časový limit | operaci vypršel časový limit před tím, než mohla být dokončena. |
| 131 | 0603 | Zaneprázdněný Server | zaneprázdněný Server |

#### <a name="pseudo-code-sample"></a>Ukázka pseudo-code

```
-> PUBLISH
    QoS: 1
    Topic: $iothub/telemetry
    [Content Type: <string>]
    [content-encoding: <string>]
    [message-id: <string>]
    [user-id: <string>]
    [correlation-id: <string>]
    [creation-time: <time>]

<- PUBACK

```

## <a name="responses"></a>Odpovědi

### <a name="bad-request"></a>Chybný požadavek

Zpráva operace je poškozená a nelze ji zpracovat.

**Kód důvodu:**`131`

**Stav:**`0100`

**Vlastnosti**:

| Název | Typ | Vyžadováno | Popis |
| :--- | :--- | :------- | :---------- |
| reason | řetězec | ne | obsahuje informace o tom, co konkrétně není pro zprávu platné. |

**Datová část**: Empty

### <a name="conflict"></a>Konflikt

Operace je v konfliktu s jinou probíhající operací.

**Kód důvodu:**`131`

**Stav:**`0103`

**Vlastnosti**:

| Název | Typ | Vyžadováno | Popis |
| :--- | :--- | :------- | :---------- |
| ID trasování | řetězec | ne | ID trasování pro korelaci s další diagnostikou pro chybu |
| reason | řetězec | ne | obsahuje informace o tom, co konkrétně není pro zprávu platné. |

**Datová část**: Empty

### <a name="not-allowed"></a>Nepovolené

Operace není povolena.

**Kód důvodu:**`131`

**Stav:**`0102`

**Vlastnosti**:

| Název | Typ | Vyžadováno | Popis |
| :--- | :--- | :------- | :---------- |
| reason | řetězec | ne | obsahuje informace o tom, co konkrétně není pro zprávu platné. |

**Datová část**: Empty

### <a name="not-authorized"></a>Neautorizováno

Klient nemá autorizaci k provedení této operace.

**Kód důvodu:**`135`

**Stav:**`0101`

**Vlastnosti**:

| Název | Typ | Vyžadováno | Popis |
| :--- | :--- | :------- | :---------- |
| ID trasování | řetězec | ne | ID trasování pro korelaci s další diagnostikou pro chybu |

**Datová část**: Empty

### <a name="not-found"></a>Nenalezeno

požadovaný prostředek neexistuje.

**Kód důvodu:**`131`

**Stav:**`0504`

**Vlastnosti**:

| Název | Typ | Vyžadováno | Popis |
| :--- | :--- | :------- | :---------- |
| reason | řetězec | ne | obsahuje informace o tom, co konkrétně není pro zprávu platné. |

**Datová část**: Empty

### <a name="not-modified"></a>Neupraveno

Prostředek se na základě poskytnuté předběžné podmínky nezměnil.

**Kód důvodu:**`0`

**Stav:**`0001`

**Vlastnosti**: žádné

**Datová část**: Empty

### <a name="precondition-failed"></a>Předběžná podmínka se nezdařila.

Předběžná podmínka nebyla splněna, výsledkem je zrušení žádosti.

**Kód důvodu:**`131`

**Stav:**`0104`

**Vlastnosti**: žádné

**Datová část**: Empty

### <a name="quota-exceeded"></a>Překročení kvóty

překročila se denní kvóta za aktuální SKU.

**Kód důvodu:**`151`

**Stav:**`0502`

**Vlastnosti**: žádné

**Datová část**: Empty

### <a name="resource-exhausted"></a>Prostředek se vyčerpal.

prostředek nemá žádnou kapacitu k dokončení operace.

**Kód důvodu:**`131`

**Stav:**`0503`

**Vlastnosti**:

| Název | Typ | Vyžadováno | Popis |
| :--- | :--- | :------- | :---------- |
| reason | řetězec | ne | obsahuje informace o tom, co konkrétně není pro zprávu platné. |

**Datová část**: Empty

### <a name="server-busy"></a>Zaneprázdněný Server

zaneprázdněný Server

**Kód důvodu:**`131`

**Stav:**`0603`

**Vlastnosti**:

| Název | Typ | Vyžadováno | Popis |
| :--- | :--- | :------- | :---------- |
| ID trasování | řetězec | ne | ID trasování pro korelaci s další diagnostikou pro chybu |

**Datová část**: Empty

### <a name="server-error"></a>Chyba serveru

Vnitřní chyba serveru

**Kód důvodu:**`131`

**Stav:**`0601`

**Vlastnosti**:

| Název | Typ | Vyžadováno | Popis |
| :--- | :--- | :------- | :---------- |
| ID trasování | řetězec | ne | ID trasování pro korelaci s další diagnostikou pro chybu |

**Datová část**: Empty

### <a name="target-failed"></a>Cíl selhal

Cíl odpověděl, ale odpověď byla neplatná nebo má chybný formát.

**Kód důvodu:**`131`

**Stav:**`06A2`

**Vlastnosti**:

| Název | Typ | Vyžadováno | Popis |
| :--- | :--- | :------- | :---------- |
| reason | řetězec | ne | obsahuje informace o tom, co konkrétně není pro zprávu platné. |

**Datová část**: Empty

### <a name="target-timeout"></a>Cílový časový limit

vypršel časový limit při čekání na dokončení požadavku na cíl.

**Kód důvodu:**`131`

**Stav:**`06A1`

**Vlastnosti**:

| Název | Typ | Vyžadováno | Popis |
| :--- | :--- | :------- | :---------- |
| ID trasování | řetězec | ne | ID trasování pro korelaci s další diagnostikou pro chybu |
| reason | řetězec | ne | obsahuje informace o tom, co konkrétně není pro zprávu platné. |

**Datová část**: Empty

### <a name="target-unavailable"></a>Cíl není k dispozici

Cíl je nedosažitelný, aby bylo možné dokončit požadavek.

**Kód důvodu:**`131`

**Stav:**`06A0`

**Vlastnosti**: žádné

**Datová část**: Empty

### <a name="throttled"></a>Omezené

počet požadavků je příliš vysoký na SKU.

**Kód důvodu:**`151`

**Stav:**`0501`

**Vlastnosti**: žádné

**Datová část**: Empty

### <a name="timeout"></a>Časový limit

operaci vypršel časový limit před tím, než mohla být dokončena.

**Kód důvodu:**`131`

**Stav:**`0602`

**Vlastnosti**:

| Název | Typ | Vyžadováno | Popis |
| :--- | :--- | :------- | :---------- |
| ID trasování | řetězec | ne | ID trasování pro korelaci s další diagnostikou pro chybu |

**Datová část**: Empty

