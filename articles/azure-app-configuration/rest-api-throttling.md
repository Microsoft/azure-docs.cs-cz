---
title: REST API konfigurace aplikací Azure – omezování
description: Referenční stránky pro porozumění omezování při použití konfigurace aplikace Azure REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: b4472fdcacfe5b62c0cded089224c6d41e32f47d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932434"
---
# <a name="throttling"></a>Throttling

Úložiště konfigurací mají omezení požadavků, které mohou obsluhovat. Všechny žádosti, které překročí přidělenou kvótu pro úložiště konfigurace, obdrží odpověď HTTP 429 (příliš mnoho požadavků).

Omezování je rozdělené do různých zásad kvót:

- **Celkový počet požadavků** – celkový počet požadavků
- **Celková šířka pásma** – odchozí data v bajtech
- **Storage** – celková velikost úložiště uživatelských dat v bajtech

## <a name="handling-throttled-responses"></a>Zpracování omezených odpovědí

Po dosažení limitu přenosové rychlosti pro danou kvótu bude server reagovat na další požadavky tohoto typu se stavovým kódem _429_ . Odpověď _429_ bude obsahovat hlavičku _Retry-After-MS_ , která klientovi poskytne navrhovanou čekací dobu (v milisekundách), aby se kvóta žádosti mohla doplnit.

```http
HTTP/1.1 429 (Too Many Requests)
retry-after-ms: 10
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/too-many-requests",
  "title": "Resource utilization has surpassed the assigned quota",
  "policy": "Total Requests",
  "status": 429
}
```

V předchozím příkladu klient překročil povolenou kvótu a doporučuje se zpomalit a počkat 10 milisekund, než se pokusí o jakékoli další požadavky. Klienti by měli zvážit také progresivní omezení rychlosti.

## <a name="other-retry"></a>Další opakování

Služba může identifikovat jiné situace než omezování, které vyžaduje opakování klienta (např.: 503 Služba není k dispozici). Ve všech takových případech bude k `retry-after-ms` dispozici hlavička odpovědi. Aby se zvýšila odolnost, měl by klient postupovat podle navrženého intervalu a opakovat akci.

```http
HTTP/1.1 503 Service Unavailable
retry-after-ms: 787
```
