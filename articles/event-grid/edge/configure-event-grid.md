---
title: Konfigurace-Azure Event Grid IoT Edge | Microsoft Docs
description: Konfigurace v Event Grid IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 841b5092775353bbe3340dbbd55610026f998a15
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846471"
---
# <a name="event-grid-configuration"></a>Konfigurace Event Grid

Event Grid poskytuje mnoho konfigurací, které je možné upravit pro každé prostředí. V následující části najdete odkaz na všechny dostupné možnosti a jejich výchozí hodnoty.

## <a name="tls-configuration"></a>Konfigurace TLS

Obecné informace o ověřování klientů najdete v tématu [zabezpečení a ověřování](security-authentication.md). Příklady jeho použití najdete v [tomto článku](configure-api-protocol.md).

| Název vlastnosti | Popis |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| Zásada TLS modulu Event Grid. Výchozí hodnota je pouze HTTPS.
|`inbound__serverAuth__serverCert__source`| Zdroj certifikátu serveru, který používá modul Event Grid pro konfiguraci protokolu TLS. Výchozí hodnota je IoT Edge.

## <a name="incoming-client-authentication"></a>Příchozí ověřování klientů

Obecné informace o ověřování klientů najdete v tématu [zabezpečení a ověřování](security-authentication.md). Příklady najdete v [tomto článku](configure-client-auth.md).

| Název vlastnosti | Popis |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| Pro zapnutí nebo vypnutí ověřování klientů na základě certifikátu. Výchozí hodnota je true.
|`inbound__clientAuth__clientCert__source`| Zdroj pro ověřování klientských certifikátů. Výchozí hodnota je IoT Edge.
|`inbound__clientAuth__clientCert__allowUnknownCA`| Zásady povolující klientský certifikát podepsaný svým držitelem. Výchozí hodnota je true.
|`inbound__clientAuth__sasKeys__enabled`| Chcete-li zapnout nebo vypnout ověřování klienta na základě klíčů SAS. Výchozí hodnota je off.
|`inbound__clientAuth__sasKeys__key1`| Jedna z hodnot pro ověření příchozích požadavků.
|`inbound__clientAuth__sasKeys__key2`| Volitelná druhá hodnota pro ověření příchozích požadavků.

## <a name="outgoing-client-authentication"></a>Odchozí ověřování klientů
Obecné informace o ověřování klientů najdete v tématu [zabezpečení a ověřování](security-authentication.md). Příklady najdete v [tomto článku](configure-identity-auth.md).

| Název vlastnosti | Popis |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| Zapnutí nebo vypnutí připojení certifikátu identity k odchozím žádostem. Výchozí hodnota je true.
|`outbound__clientAuth__clientCert__source`| Zdroj pro načtení odchozího certifikátu modulu Event Grid Výchozí hodnota je IoT Edge.

## <a name="webhook-event-handlers"></a>Obslužné rutiny událostí Webhooku

Obecné informace o ověřování klientů najdete v tématu [zabezpečení a ověřování](security-authentication.md). Příklady najdete v [tomto článku](configure-webhook-subscriber-auth.md).

| Název vlastnosti | Popis |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| Zásady, které určují, jestli se mají povolit jenom odběratelé HTTPS Výchozí hodnota je true (pouze HTTPS).
|`outbound__webhook__skipServerCertValidation`| Příznak, který určuje, jestli se má ověřit certifikát předplatitele. Výchozí hodnota je true.
|`outbound__webhook__allowUnknownCA`| Zásady, které určují, jestli může odběratel předložit certifikát podepsaný svým držitelem. Výchozí hodnota je true. 

## <a name="delivery-and-retry"></a>Doručování a opakované pokusy

Obecné informace o této funkci najdete v tématu [doručování a opakování](delivery-retry.md).

| Název vlastnosti | Popis |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Maximální počet pokusů o doručení události Výchozí hodnota je 30.
| `broker__defaultEventTimeToLiveInSeconds` | Hodnota TTL (Time to Live) v sekundách, po které se událost vynechá, pokud se nedodá. Výchozí hodnota je **7200** sekund.

## <a name="output-batching"></a>Dávkování výstupu

Další informace o této funkci najdete v tématu [doručování a výstup dávkování](delivery-output-batching.md).

| Název vlastnosti | Popis |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | Maximální povolená hodnota ovladače `ApproxBatchSizeInBytes`. Výchozí hodnota je `1_058_576`.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Maximální povolená hodnota ovladače `MaxEventsPerBatch`. Výchozí hodnota je `50`.
| `broker__defaultMaxBatchSizeInBytes` | Maximální velikost žádosti o doručení, je-li zadána pouze `MaxEventsPerBatch`. Výchozí hodnota je `1_058_576`.
| `broker__defaultMaxEventsPerBatch` | Maximální počet událostí, které mají být přidány do dávky, pokud je zadána pouze `MaxBatchSizeInBytes`. Výchozí hodnota je `10`.

## <a name="metrics"></a>Metriky

Další informace o použití metrik s Event Grid v IoT Edge najdete v tématu [monitorování témat a odběrů](monitor-topics-subscriptions.md) .

| Název vlastnosti | Popis |
| ---------------- | ------------ |
| `metrics__reporterType` | Typ zpravodaje pro metriky enpoint. Výchozí hodnota je `none` a zakáže metriky. Nastavení na `prometheus` povolí metriky ve formátu Exposition pro Prometheus.