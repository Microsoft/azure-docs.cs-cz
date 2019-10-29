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
ms.openlocfilehash: a9bac9a84e1ba034b011691ae82a1cb67eb71af0
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992428"
---
# <a name="event-grid-configuration"></a>Konfigurace Event Grid

Event Grid poskytuje mnoho konfigurací, které je možné upravit pro každé prostředí. V následující části najdete odkaz na všechny dostupné možnosti a jejich výchozí hodnoty.

## <a name="tls-configuration"></a>Konfigurace TLS

Obecné informace o ověřování klientů najdete v tématu [zabezpečení a ověřování](security-authentication.md). Příklady jeho použití najdete v [tomto článku](configure-api-protocol.md).

| Název vlastnosti | Popis |
| ---------------- | ------------ |
|`inbound:serverAuth:tlsPolicy`| Zásada TLS modulu Event Grid. Výchozí hodnota je pouze HTTPS.
|`inbound:serverAuth:serverCert:source`| Zdroj certifikátu serveru, který používá modul Event Grid pro konfiguraci protokolu TLS. Výchozí hodnota je IoT Edge.

## <a name="incoming-client-authentication"></a>Příchozí ověřování klientů

Obecné informace o ověřování klientů najdete v tématu [zabezpečení a ověřování](security-authentication.md). Příklady najdete v [tomto článku](configure-client-auth.md).

| Název vlastnosti | Popis |
| ---------------- | ------------ |
|`inbound:clientAuth:clientCert:enabled`| Pro zapnutí nebo vypnutí ověřování klientů na základě certifikátu. Výchozí hodnota je true.
|`inbound:clientAuth:clientCert:source`| Zdroj pro ověřování klientských certifikátů. Výchozí hodnota je IoT Edge.
|`inbound:clientAuth:clientCert:allowUnknownCA`| Zásady povolující klientský certifikát podepsaný svým držitelem. Výchozí hodnota je true.
|`inbound:clientAuth:sasKeys:enabled`| Chcete-li zapnout nebo vypnout ověřování klienta na základě klíčů SAS. Výchozí hodnota je off.
|`inbound:clientAuth:sasKeys:key1`| Jedna z hodnot pro ověření příchozích požadavků.
|`inbound:clientAuth:sasKeys:key2`| Volitelná druhá hodnota pro ověření příchozích požadavků.

## <a name="outgoing-client-authentication"></a>Odchozí ověřování klientů
Obecné informace o ověřování klientů najdete v tématu [zabezpečení a ověřování](security-authentication.md). Příklady najdete v [tomto článku](configure-identity-auth.md).

| Název vlastnosti | Popis |
| ---------------- | ------------ |
|`outbound:clientAuth:clientCert:enabled`| Zapnutí nebo vypnutí připojení certifikátu identity k odchozím žádostem. Výchozí hodnota je true.
|`outbound:clientAuth:clientCert:source`| Zdroj pro načtení odchozího certifikátu modulu Event Grid Výchozí hodnota je IoT Edge.

## <a name="webhook-event-handlers"></a>Obslužné rutiny událostí Webhooku

Obecné informace o ověřování klientů najdete v tématu [zabezpečení a ověřování](security-authentication.md). Příklady najdete v [tomto článku](configure-webhook-subscriber-auth.md).

| Název vlastnosti | Popis |
| ---------------- | ------------ |
|`outbound:webhook:httpsOnly`| Zásady, které určují, jestli se mají povolit jenom odběratelé HTTPS Výchozí hodnota je true (pouze HTTPS).
|`outbound:webhook:skipServerCertValidation`| Příznak, který určuje, jestli se má ověřit certifikát předplatitele. Výchozí hodnota je true.
|`outbound:webhook:allowUnknownCA`| Zásady, které určují, jestli může odběratel předložit certifikát podepsaný svým držitelem. Výchozí hodnota je true. 

## <a name="delivery-and-retry"></a>Doručování a opakované pokusy

Obecné informace o této funkci najdete v tématu [doručování a opakování](delivery-retry.md).

| Název vlastnosti | Popis |
| ---------------- | ------------ |
| `broker:defaultMaxDeliveryAttempts` | Maximální počet pokusů o doručení události Výchozí hodnota je 30.
| `broker:defaultEventTimeToLiveInSeconds` | Hodnota TTL (Time to Live) v sekundách, po které se událost vynechá, pokud se nedodá. Výchozí hodnota je **7200** sekund.

## <a name="output-batching"></a>Výstup dávkování

Další informace o této funkci najdete v tématu [doručování a výstup dávkování](delivery-output-batching.md).

| Název vlastnosti | Popis |
| ---------------- | ------------ |
| `api:deliveryPolicyLimits:maxBatchSizeInBytes` | Maximální povolená hodnota ovladače `ApproxBatchSizeInBytes`. Výchozí hodnota je `1_058_576`.
| `api:deliveryPolicyLimits:maxEventsPerBatch` | Maximální povolená hodnota ovladače `MaxEventsPerBatch`. Výchozí hodnota je `50`.
| `broker:defaultMaxBatchSizeInBytes` | Maximální velikost žádosti o doručení, je-li zadána pouze `MaxEventsPerBatch`. Výchozí hodnota je `1_058_576`.
| `broker:defaultMaxEventsPerBatch` | Maximální počet událostí, které mají být přidány do dávky, pokud je zadána pouze `MaxBatchSizeInBytes`. Výchozí hodnota je `10`.
