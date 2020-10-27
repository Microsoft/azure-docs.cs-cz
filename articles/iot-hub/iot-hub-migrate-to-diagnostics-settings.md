---
title: Migrace Azure IoT Hub do nastavení diagnostiky | Microsoft Docs
description: Postup aktualizace služby Azure IoT Hub pro použití nastavení diagnostiky Azure místo monitorování operací k monitorování stavu operací ve službě IoT Hub v reálném čase.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: 40c90142330b0530f1127beae1624ff27d7eb6ca
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92541481"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Migrace IoT Hub z monitorování provozu na nastavení diagnostiky

Zákazníci, kteří používají [monitorování operací](iot-hub-operations-monitoring.md) ke sledování stavu operací v IoT Hub, můžou tento pracovní postup migrovat do [nastavení diagnostiky Azure](../azure-monitor/platform/platform-logs-overview.md), což je funkce Azure monitor. Nastavení diagnostiky poskytují diagnostické informace na úrovni prostředku pro mnoho služeb Azure.

**Funkce monitorování operací IoT Hub je zastaralá** a byla odebrána z portálu. Tento článek popisuje kroky pro přesun úloh z monitorování provozu do nastavení diagnostiky. Další informace o časové ose zastaralosti najdete v tématu [monitorování řešení Azure IoT pomocí Azure monitor a Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>Aktualizovat IoT Hub

Chcete-li aktualizovat IoT Hub v Azure Portal, nejprve zapněte nastavení diagnostiky a pak vypněte monitorování provozu.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Vypnout monitorování operací

> [!NOTE]
> Od 11. března 2019 je funkce monitorování operací z Azure Portalho rozhraní IoT Hub odebrána. Níže uvedený postup už neplatí. Pokud chcete provést migraci, ujistěte se, že jsou ve Azure Monitor nastavení diagnostiky nahoře zapnuté správné kategorie.

Jakmile otestujete nové nastavení diagnostiky ve svém pracovním postupu, můžete funkci monitorování provozu vypnout. 

1. V nabídce IoT Hub vyberte **monitorování operací** .

2. V části každá kategorie monitorování vyberte **žádné** .

3. Uložte změny monitorování operací.

## <a name="update-applications-that-use-operations-monitoring"></a>Aktualizace aplikací, které používají monitorování operací

Schémata pro nastavení monitorování a diagnostiky operací se mírně liší. Je důležité aktualizovat aplikace, které dnes používají monitorování operací, k mapování schématu používaného nastavením diagnostiky. 

Nastavení diagnostiky taky nabízí pět nových kategorií pro sledování. Po aktualizaci aplikací pro existující schéma přidejte také nové kategorie:

* Zdvojené operace z cloudu na zařízení
* Zdvojené operace mezi zařízeními a cloudem
* Zdvojené dotazy
* Operace úloh
* Přímé metody

Konkrétní struktury schématu najdete v tématu [protokoly prostředků](monitor-iot-hub-reference.md#resource-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Monitorování událostí připojení a odpojení zařízení s nízkou latencí

Pokud chcete monitorovat události připojení a odpojení zařízení v produkčním prostředí, doporučujeme přihlášení k odběru [události **odpojení zařízení**](iot-hub-event-grid.md#event-types) v Event Grid, aby se zobrazily výstrahy a sledovaly stav připojení zařízení. V tomto [kurzu](iot-hub-how-to-order-connection-state-events.md) se dozvíte, jak integrovat události připojené k zařízení a odpojené zařízení z IoT Hub ve vašem řešení IoT.

## <a name="next-steps"></a>Další kroky

[IoT Hub monitorování](monitor-iot-hub.md)
