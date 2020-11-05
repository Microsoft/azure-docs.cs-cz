---
title: Referenční informace – protokol prostředků Azure API Management
description: Odkaz na schéma pro protokol prostředků Azure API Management GatewayLogs
services: api-management
author: dlepow
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: 064ac21e01239f7be3ddca9e48089ce880c6af58
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93380355"
---
# <a name="reference-api-management-resource-log-schema"></a>Referenční dokumentace: API Management schéma protokolu prostředků

Tento článek poskytuje referenční informace ke schématu pro protokol prostředků Azure API Management GatewayLogs. Položky protokolu zahrnují také pole ve [společném schématu nejvyšší úrovně](../azure-monitor/platform/resource-logs-schema.md#top-level-common-schema).

Pokud chcete povolit shromažďování protokolu prostředků v API Management, přečtěte si téma [monitorování publikovaných rozhraní API](api-management-howto-use-azure-monitor.md#resource-logs).

## <a name="gatewaylogs-schema"></a>GatewayLogs schéma

Následující vlastnosti jsou protokolovány pro každou žádost rozhraní API.

| Vlastnost  | Typ | Popis |
| ------------- | ------------- | ------------- |
| method | řetězec | Metoda HTTP příchozího požadavku. |
| url | řetězec | Adresa URL příchozího požadavku. |
| responseCode | integer | Stavový kód odpovědi HTTP odeslané do klienta. |
| responseSize | integer | Počet bajtů odeslaných do klienta během zpracování požadavku. | 
| cache | řetězec | Stav zapojení do mezipaměti API Management při zpracování žádosti (úspěšný, neúspěšný, žádný) | 
| apiId | řetězec | Identifikátor entity rozhraní API pro aktuální požadavek. | 
| operationId | řetězec | Identifikátor entity operace pro aktuální požadavek. | 
| clientProtocol | řetězec | Verze protokolu HTTP příchozího požadavku. |
| clientTime | integer | Počet milisekund strávený na celkovém vstupu/výstupu klienta (připojování, odesílání a přijímání bajtů) | 
| apiRevision | řetězec | Revize rozhraní API pro aktuální požadavek | 
| clientTlsVersion| řetězec | Verze TLS používaná požadavkem na odeslání klienta |
| lastError | object | Pro neúspěšnou žádost, podrobnosti o Poslední chybě zpracování žádosti | 
| backendMethod | řetězec | Metoda HTTP požadavku odeslaného do back-endu. |
| backendUrl | řetězec | Adresa URL požadavku odeslaného do back-endu. |
| backendResponseCode | integer | Kód odpovědi HTTP přijaté z back-endu. |
| backedProtocol | řetězec | Verze protokolu HTTP požadavku odeslaného do back-endu. |
| backendTime | integer | Počet milisekund strávený při celkovém vstupně-výstupních operacích (připojení, odesílání a příjem bajtů) | 


## <a name="next-steps"></a>Další kroky

* Informace o rozhraních API pro monitorování v API Management najdete v tématu [monitorování publikovaných rozhraní API](api-management-howto-use-azure-monitor.md) .
* Další informace o [Common a schématu specifického pro službu pro protokoly prostředků Azure](../azure-monitor/platform/resource-logs-schema.md)

