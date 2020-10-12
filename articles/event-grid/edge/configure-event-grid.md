---
title: Konfigurace-Azure Event Grid IoT Edge | Microsoft Docs
description: Konfigurace v Event Grid IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 632227579fd021a0d2ce1d0b1bb0b8a8288c5f47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171665"
---
# <a name="event-grid-configuration"></a>Konfigurace Event Grid

Event Grid poskytuje mnoho konfigurací, které je možné upravit pro každé prostředí. V následující části najdete odkaz na všechny dostupné možnosti a jejich výchozí hodnoty.

## <a name="tls-configuration"></a>Konfigurace TLS

Obecné informace o ověřování klientů najdete v tématu [zabezpečení a ověřování](security-authentication.md). Příklady jeho použití najdete v [tomto článku](configure-api-protocol.md).

| Název vlastnosti | Description |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| Zásada TLS modulu Event Grid. Výchozí hodnota je pouze HTTPS.
|`inbound__serverAuth__serverCert__source`| Zdroj certifikátu serveru, který používá modul Event Grid pro konfiguraci protokolu TLS. Výchozí hodnota je IoT Edge.

## <a name="incoming-client-authentication"></a>Příchozí ověřování klientů

Obecné informace o ověřování klientů najdete v tématu [zabezpečení a ověřování](security-authentication.md). Příklady najdete v [tomto článku](configure-client-auth.md).

| Název vlastnosti | Description |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| Pro zapnutí nebo vypnutí ověřování klientů na základě certifikátu. Výchozí hodnota je true (pravda).
|`inbound__clientAuth__clientCert__source`| Zdroj pro ověřování klientských certifikátů. Výchozí hodnota je IoT Edge.
|`inbound__clientAuth__clientCert__allowUnknownCA`| Zásady povolující klientský certifikát podepsaný svým držitelem. Výchozí hodnota je true (pravda).
|`inbound__clientAuth__sasKeys__enabled`| Chcete-li zapnout nebo vypnout ověřování klienta na základě klíčů SAS. Výchozí hodnota je off.
|`inbound__clientAuth__sasKeys__key1`| Jedna z hodnot pro ověření příchozích požadavků.
|`inbound__clientAuth__sasKeys__key2`| Volitelná druhá hodnota pro ověření příchozích požadavků.

## <a name="outgoing-client-authentication"></a>Odchozí ověřování klientů
Obecné informace o ověřování klientů najdete v tématu [zabezpečení a ověřování](security-authentication.md). Příklady najdete v [tomto článku](configure-identity-auth.md).

| Název vlastnosti | Description |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| Zapnutí nebo vypnutí připojení certifikátu identity k odchozím žádostem. Výchozí hodnota je true (pravda).
|`outbound__clientAuth__clientCert__source`| Zdroj pro načtení odchozího certifikátu modulu Event Grid Výchozí hodnota je IoT Edge.

## <a name="webhook-event-handlers"></a>Obslužné rutiny událostí Webhooku

Obecné informace o ověřování klientů najdete v tématu [zabezpečení a ověřování](security-authentication.md). Příklady najdete v [tomto článku](configure-webhook-subscriber-auth.md).

| Název vlastnosti | Description |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| Zásady, které určují, jestli se mají povolit jenom odběratelé HTTPS Výchozí hodnota je true (pouze HTTPS).
|`outbound__webhook__skipServerCertValidation`| Příznak, který určuje, jestli se má ověřit certifikát předplatitele. Výchozí hodnota je true (pravda).
|`outbound__webhook__allowUnknownCA`| Zásady, které určují, jestli může odběratel předložit certifikát podepsaný svým držitelem. Výchozí hodnota je true (pravda). 

## <a name="delivery-and-retry"></a>Doručování a opakované pokusy

Obecné informace o této funkci najdete v tématu [doručování a opakování](delivery-retry.md).

| Název vlastnosti | Description |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Maximální počet pokusů o doručení události Výchozí hodnota je 30.
| `broker__defaultEventTimeToLiveInSeconds` | Hodnota TTL (Time to Live) v sekundách, po které se událost vynechá, pokud se nedodá. Výchozí hodnota je  **7200** sekund.

## <a name="output-batching"></a>Dávkování výstupu

Další informace o této funkci najdete v tématu [doručování a výstup dávkování](delivery-output-batching.md).

| Název vlastnosti | Description |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | Maximální povolená hodnota `ApproxBatchSizeInBytes` ovladače Výchozí hodnota je `1_058_576`.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Maximální povolená hodnota `MaxEventsPerBatch` ovladače Výchozí hodnota je `50`.
| `broker__defaultMaxBatchSizeInBytes` | Maximální velikost žádosti o doručení `MaxEventsPerBatch` , pokud je určena pouze. Výchozí hodnota je `1_058_576`.
| `broker__defaultMaxEventsPerBatch` | Maximální počet událostí, které mají být přidány do dávky, pokud `MaxBatchSizeInBytes` je určena pouze hodnota. Výchozí hodnota je `10`.

## <a name="metrics"></a>Metriky

Další informace o použití metrik s Event Grid v IoT Edge najdete v tématu [monitorování témat a odběrů](monitor-topics-subscriptions.md) .

| Název vlastnosti | Description |
| ---------------- | ------------ |
| `metrics__reporterType` | Typ zpravodaje pro koncový bod metrik Výchozí hodnota je `none` a zakáže metriky. Nastavením `prometheus` této možnost povolíte metriky ve formátu Prometheus Exposition.