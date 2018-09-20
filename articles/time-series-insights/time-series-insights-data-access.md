---
title: Konfigurace zabezpečení pro přístup k a spravovat Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje postup konfigurace zabezpečení a oprávnění jako přístup pro správu zásad a přístupu k datům zásady pro zabezpečení služby Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: 7cb5dc5b170103f98d56abc920f36dd85f855961
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364807"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Udělení přístupu k datům prostředí Time Series Insights pomocí webu Azure Portal

V prostředích Time Series Insights jsou dva nezávislé typy zásad přístupu:

* Zásady přístupu ke správě
* Zásady přístupu k datům

Oba typy zásad udělují objektům zabezpečení Azure Active Directory (uživatelům a aplikacím) různá oprávnění ke konkrétnímu prostředí. Objekty zabezpečení (uživatelé a aplikace) musí patřit do active directory (označuje se jako Azure tenant) přidružené k předplatnému, které obsahuje prostředí.

Zásady přístupu ke správě udělují oprávnění související s konfigurací prostředí, jako je například
*   vytvoření nebo odstranění prostředí, zdrojů událostí nebo referenčních datových sad a
*   správa zásad přístupu k datům.

Zásady přístupu k datům udělují oprávnění k vydávání dotazů na data, zpracování referenčních dat v rámci prostředí a sdílení uložených dotazů a perspektiv přidruženým k danému prostředí.

Tyto dva typy zásad umožňují jasné oddělení přístupu ke správě prostředí od přístupu k datům v prostředí. Například je možné nastavit prostředí tak, aby vlastník nebo Tvůrce prostředí se odebere přístup k datům. Kromě toho uživatele a služby, které můžou číst data z prostředí může udělit přístup ke konfiguraci prostředí.

[!INCLUDE [iot-tsi-data-access](../../includes/iot-tsi-data-access.md)]

## <a name="next-steps"></a>Další postup

* Přečtěte si [přidání zdroje událostí centra událostí do prostředí Azure Time Series Insights](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Odesílání událostí](time-series-insights-send-events.md) ke zdroji události.
* Zobrazte své prostředí v [Průzkumníka služby Time Series Insights](https://insights.timeseries.azure.com).
