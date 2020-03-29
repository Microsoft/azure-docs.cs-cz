---
title: Konfigurace – Azure Event Grid IoT Edge | Dokumenty společnosti Microsoft
description: Konfigurace v mřížce událostí na ioT edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 841b5092775353bbe3340dbbd55610026f998a15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76846471"
---
# <a name="event-grid-configuration"></a>Konfigurace mřížky událostí

Event Grid poskytuje mnoho konfigurací, které lze upravit v prostředí. Následující část obsahuje odkaz na všechny dostupné možnosti a jejich výchozí hodnoty.

## <a name="tls-configuration"></a>Konfigurace TLS

Informace o ověřování klientů obecně naleznete v tématu [Zabezpečení a ověřování](security-authentication.md). Příklady jeho použití naleznete v [tomto článku](configure-api-protocol.md).

| Název vlastnosti | Popis |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| Zásady TLS modulu Event Grid. Výchozí hodnota je pouze https.
|`inbound__serverAuth__serverCert__source`| Zdroj certifikátu serveru používaný modulem Event Grid pro konfiguraci TLS. Výchozí hodnota je IoT Edge.

## <a name="incoming-client-authentication"></a>Příchozí ověřování klienta

Informace o ověřování klientů obecně naleznete v tématu [Zabezpečení a ověřování](security-authentication.md). Příklady lze nalézt v [tomto článku](configure-client-auth.md).

| Název vlastnosti | Popis |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| Zapnutí/vypnutí ověřování klienta založeného na certifikátu. Výchozí hodnota je true.
|`inbound__clientAuth__clientCert__source`| Zdroj pro ověřování klientských certifikátů. Výchozí hodnota je IoT Edge.
|`inbound__clientAuth__clientCert__allowUnknownCA`| Zásady umožňující klientský certifikát podepsaný svým držitelem. Výchozí hodnota je true.
|`inbound__clientAuth__sasKeys__enabled`| Zapnutí/vypnutí ověřování klienta s klíčem SAS. Výchozí hodnota je vypnutá.
|`inbound__clientAuth__sasKeys__key1`| Jedna z hodnot pro ověření příchozích požadavků.
|`inbound__clientAuth__sasKeys__key2`| Volitelná druhá hodnota pro ověření příchozích požadavků.

## <a name="outgoing-client-authentication"></a>Ověření odchozího klienta
Informace o ověřování klientů obecně naleznete v tématu [Zabezpečení a ověřování](security-authentication.md). Příklady lze nalézt v [tomto článku](configure-identity-auth.md).

| Název vlastnosti | Popis |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| Zapnutí/vypnutí připojení certifikátu identity pro odchozí požadavky. Výchozí hodnota je true.
|`outbound__clientAuth__clientCert__source`| Zdroj pro načítání odchozího certifikátu modulu Event Grid. Výchozí hodnota je IoT Edge.

## <a name="webhook-event-handlers"></a>Obslužné rutiny událostí Webhooku

Informace o ověřování klientů obecně naleznete v tématu [Zabezpečení a ověřování](security-authentication.md). Příklady lze nalézt v [tomto článku](configure-webhook-subscriber-auth.md).

| Název vlastnosti | Popis |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| Zásady řízení, zda budou povoleny pouze předplatitelé PROTOKOLU HTTPS. Výchozí hodnota je true (pouze HTTPS).
|`outbound__webhook__skipServerCertValidation`| Příznak řídit, zda chcete ověřit certifikát odběratele. Výchozí hodnota je true.
|`outbound__webhook__allowUnknownCA`| Zásady řízení, zda certifikát podepsaný svým držitelem může být předložen předplatitelem. Výchozí hodnota je true. 

## <a name="delivery-and-retry"></a>Doručování a opakované pokusy

Informace o této funkci obecně naleznete v [tématu Doručení a opakování](delivery-retry.md).

| Název vlastnosti | Popis |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Maximální počet pokusů o doručení události. Výchozí hodnota je 30.
| `broker__defaultEventTimeToLiveInSeconds` | Time-to-live (TTL) v sekundách, po kterém bude událost zrušena, pokud není doručena. Výchozí hodnota je **7200** sekund

## <a name="output-batching"></a>Dávkování výstupu

Informace o této funkci obecně naleznete v [tématu Dodávka a výstup dávkování](delivery-output-batching.md).

| Název vlastnosti | Popis |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | Maximální povolená `ApproxBatchSizeInBytes` hodnota knoflíku. Výchozí hodnota `1_058_576`je .
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Maximální povolená `MaxEventsPerBatch` hodnota knoflíku. Výchozí hodnota `50`je .
| `broker__defaultMaxBatchSizeInBytes` | Maximální velikost požadavku `MaxEventsPerBatch` na doručení, pokud je zadán pouze. Výchozí hodnota `1_058_576`je .
| `broker__defaultMaxEventsPerBatch` | Maximální počet událostí, které mají `MaxBatchSizeInBytes` být zahrnuty do dávky, pokud je zadán pouze. Výchozí hodnota `10`je .

## <a name="metrics"></a>Metriky

Informace o používání metrik pomocí služby Event Grid na IoT Edge najdete v [tématu sledování témat a předplatných](monitor-topics-subscriptions.md)

| Název vlastnosti | Popis |
| ---------------- | ------------ |
| `metrics__reporterType` | Typ reportéra pro metriky enpoint. Výchozí `none` hodnota je a zakáže metriky. Nastavení `prometheus` pro povolování metrik ve formátu expozice Prometheus.