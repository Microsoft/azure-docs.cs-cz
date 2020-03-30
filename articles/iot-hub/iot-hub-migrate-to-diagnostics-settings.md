---
title: Azure IoT Hub migrovat do nastavení diagnostiky | Dokumenty společnosti Microsoft
description: Jak aktualizovat Azure IoT Hub používat nastavení diagnostiky Azure namísto monitorování operací ke sledování stavu operací na vašem centru IoT v reálném čase.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: ab07da38c01b052a4220274fb059683a22950a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750689"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Migrace ioT hubu z monitorování operací do nastavení diagnostiky

Zákazníci, kteří používají [monitorování operací](iot-hub-operations-monitoring.md) ke sledování stavu operací v centru IoT Hub, můžou tento pracovní postup migrovat do [nastavení diagnostiky Azure](../azure-monitor/platform/platform-logs-overview.md), což je funkce Azure Monitoru. Nastavení diagnostiky poskytuje diagnostické informace na úrovni prostředků pro mnoho služeb Azure.

**Funkce monitorování operací ioT hubu je zastaralé**a byla odebrána z portálu. Tento článek obsahuje kroky k přesunutí úloh z monitorování operací do nastavení diagnostiky. Další informace o časové ose vyřazení najdete [v tématu Monitorování řešení Azure IoT pomocí Azure Monitor a Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>Aktualizace služby IoT Hub

Pokud chcete aktualizovat ioT hub na webu Azure Portal, nejdřív zapněte nastavení diagnostiky a pak vypněte monitorování operací.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Vypnout monitorování operací

> [!NOTE]
> března 2019 se funkce monitorování operací odebere z rozhraní portálu Azure pro Azure služby IoT Hub. Níže uvedené kroky již neplatí. Chcete-li migrovat, ujistěte se, že správné kategorie jsou zapnuté v nastavení diagnostiky Azure Monitor výše.

Po otestování nového nastavení diagnostiky v pracovním postupu můžete funkci monitorování operací vypnout. 

1. V nabídce IoT Hub vyberte **Sledování operací**.

2. V každé kategorii monitorování vyberte **možnost Žádný**.

3. Uložte změny monitorování operací.

## <a name="update-applications-that-use-operations-monitoring"></a>Aktualizace aplikací, které používají monitorování operací

Schémata pro monitorování operací a nastavení diagnostiky se mírně liší. Je důležité aktualizovat aplikace, které používají monitorování operací dnes mapovat na schéma používané nastavení diagnostiky. 

Nastavení diagnostiky také nabízí pět nových kategorií pro sledování. Po aktualizaci aplikací pro existující schéma přidejte také nové kategorie:

* Operace s dvojčaty cloud-to-device
* Operace dvojčete zařízení cloudu
* Dvojité dotazy
* Operace úloh
* Přímé metody

Konkrétní struktury schématu naleznete v [tématu Principy nastavení diagnostiky schématu](iot-hub-monitor-resource-health.md#understand-the-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Sledování událostí připojení a odpojení zařízení s nízkou latencí

Chcete-li sledovat události připojení a odpojení zařízení v produkčním prostředí, doporučujeme přihlásit se k odběru události [ **odpojené zařízení** ](iot-hub-event-grid.md#event-types) v programu Event Grid, abyste získali upozornění a monitorovali stav připojení zařízení. V tomto [kurzu](iot-hub-how-to-order-connection-state-events.md) se dozvíte, jak integrovat události připojeného zařízení a odpojeného zařízení z ioT hubu do vašeho řešení IoT.

## <a name="next-steps"></a>Další kroky

[Monitorování stavu služby Azure IoT Hub a rychlá diagnostika potíží](iot-hub-monitor-resource-health.md)
