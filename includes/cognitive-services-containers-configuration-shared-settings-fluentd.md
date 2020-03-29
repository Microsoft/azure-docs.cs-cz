---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b08516b35a864eae6d15c4c5c928f0550c64c239
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67712448"
---
Fluentd je open source sběrač dat pro jednotné protokolování. Nastavení `Fluentd` spravovat připojení kontejneru k serveru [Fluentd.](https://www.fluentd.org) Kontejner obsahuje poskytovatele protokolování Fluentd, který umožňuje kontejneru zapisovat protokoly a volitelně metrická data na server Fluentd.

Následující tabulka popisuje nastavení konfigurace podporovaná v části. `Fluentd`

| Name (Název) | Datový typ | Popis |
|------|-----------|-------------|
| `Host` | Řetězec | Název hostitele PROTOKOLU IP nebo DNS serveru Fluentd. |
| `Port` | Integer | Port serveru Fluentd.<br/> Výchozí hodnota je 24224. |
| `HeartbeatMs` | Integer | Interval prezenčního signálu v milisekundách. Pokud před vypršením tohoto intervalu nebyl odeslán žádný provoz událostí, je na server Fluentd odeslán prezenční signál. Výchozí hodnota je 60000 milisekund (1 minuta). |
| `SendBufferSize` | Integer | Místo síťové vyrovnávací paměti v bajtech přidělené pro operace odesílání. Výchozí hodnota je 32768 bajtů (32 kilobajtů). |
| `TlsConnectionEstablishmentTimeoutMs` | Integer | Časový limit v milisekundách pro navázání připojení SSL/TLS se serverem Fluentd. Výchozí hodnota je 10000 milisekund (10 sekund).<br/> Pokud `UseTLS` je nastavena na false, tato hodnota je ignorována. |
| `UseTLS` | Logická hodnota | Označuje, zda má kontejner používat ssl/tls pro komunikaci se serverem Fluentd. Výchozí hodnota je False. |