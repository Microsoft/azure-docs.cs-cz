---
title: Migrace monitorování provozu Azure IoT Hub do IoT Hubch protokolů prostředků v Azure Monitor | Microsoft Docs
description: Postup aktualizace služby Azure IoT Hub pro použití Azure Monitor místo monitorování operací k monitorování stavu operací ve službě IoT Hub v reálném čase.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: 48b646881b12047b28490999a96326f6076af2c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100591845"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-azure-monitor-resource-logs"></a>Migrace IoT Hub z monitorování operací na protokoly prostředků Azure Monitor

Zákazníci, kteří používají [monitorování operací](iot-hub-operations-monitoring.md) ke sledování stavu operací v IoT Hub, můžou tento pracovní postup migrovat do [Azure Monitorch protokolů prostředků](../azure-monitor/essentials/platform-logs-overview.md), což je funkce Azure monitor. Protokoly prostředků poskytují diagnostické informace na úrovni prostředku pro mnoho služeb Azure.

**Funkce monitorování operací IoT Hub je zastaralá** a byla odebrána z portálu. Tento článek popisuje kroky, jak přesunout vaše úlohy z monitorování provozu do Azure Monitorch protokolů prostředků. Další informace o časové ose zastaralosti najdete v tématu [monitorování řešení Azure IoT pomocí Azure monitor a Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>Aktualizovat IoT Hub

Chcete-li aktualizovat IoT Hub v Azure Portal, nejprve vytvořte nastavení diagnostiky a pak vypněte monitorování provozu.  

### <a name="create-a--diagnostic-setting"></a>Vytvoření nastavení diagnostiky

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do služby IoT Hub.

1. V levém podokně v části **monitorování** vyberte **nastavení diagnostiky**. Pak vyberte **Přidat nastavení diagnostiky**.

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/open-diagnostic-settings.png" alt-text="Snímek obrazovky, který zvýrazní nastavení diagnostiky v části monitorování.":::

1. V podokně **nastavení diagnostiky** zadejte název pro nastavení diagnostiky.

1. V části **Podrobnosti kategorie** vyberte kategorie pro operace, které chcete monitorovat. Další informace o kategoriích operací, které jsou k dispozici v IoT Hub, najdete v tématu [protokoly prostředků](monitor-iot-hub-reference.md#resource-logs).

1. V části **Podrobnosti o cíli** vyberte, kam chcete protokoly odesílat. Můžete vybrat libovolnou kombinaci těchto umístění:

   * Archivovat v účtu úložiště
   * Streamovat do centra událostí
   * Odeslání do Azure Monitor protokolů přes pracovní prostor Log Analytics

   Následující snímek obrazovky ukazuje nastavení diagnostiky, které směruje operace v kategorii připojení a telemetrie zařízení do Log Analyticsho pracovního prostoru:

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/add-diagnostic-setting.png" alt-text="Snímek obrazovky znázorňující dokončené nastavení diagnostiky":::

1. Vyberte **Uložit** a nastavení se uloží.

Nové nastavení se projeví přibližně po dobu 10 minut. Pak se protokoly zobrazí v nakonfigurovaném cíli. Další informace o konfiguraci diagnostiky najdete v tématu [shromažďování a využívání dat protokolů z prostředků Azure](../azure-monitor/essentials/platform-logs-overview.md).

Podrobnější informace o tom, jak vytvořit nastavení diagnostiky, včetně PowerShellu a rozhraní příkazového řádku Azure, najdete v tématu [nastavení diagnostiky](../azure-monitor/essentials/diagnostic-settings.md) v dokumentaci k Azure monitor.

### <a name="turn-off-operations-monitoring"></a>Vypnout monitorování operací

> [!NOTE]
> Od 11. března 2019 je funkce monitorování operací z Azure Portalho rozhraní IoT Hub odebrána. Níže uvedený postup už neplatí. Chcete-li provést migraci, ujistěte se, že jsou správné kategorie směrovány do cíle s výše uvedeným nastavením diagnostiky Azure Monitor.

Jakmile otestujete nové nastavení diagnostiky ve svém pracovním postupu, můžete funkci monitorování provozu vypnout. 

1. V nabídce IoT Hub vyberte **monitorování operací**.

2. V části každá kategorie monitorování vyberte **žádné**.

3. Uložte změny monitorování operací.

## <a name="update-applications-that-use-operations-monitoring"></a>Aktualizace aplikací, které používají monitorování operací

Schémata pro monitorování provozu a protokoly prostředků se mírně liší. Je důležité aktualizovat aplikace, které dnes používají monitorování operací, k mapování schématu používaného protokoly prostředků.

IoT Hub protokoly prostředků taky nabízí pět nových kategorií pro sledování. Po aktualizaci aplikací pro existující schéma přidejte také nové kategorie:

* Zdvojené operace z cloudu na zařízení
* Zdvojené operace mezi zařízeními a cloudem
* Zdvojené dotazy
* Operace úloh
* Přímé metody

Konkrétní struktury schématu najdete v tématu [protokoly prostředků](monitor-iot-hub-reference.md#resource-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Monitorování událostí připojení a odpojení zařízení s nízkou latencí

Pokud chcete monitorovat události připojení a odpojení zařízení v produkčním prostředí, doporučujeme přihlášení k odběru [události **odpojení zařízení**](iot-hub-event-grid.md#event-types) v Event Grid, aby se zobrazily výstrahy a sledovaly stav připojení zařízení. V tomto [kurzu](iot-hub-how-to-order-connection-state-events.md) se dozvíte, jak integrovat události připojené k zařízení a odpojené zařízení z IoT Hub ve vašem řešení IoT.

## <a name="next-steps"></a>Další kroky

[Monitorování IoT Hubu](monitor-iot-hub.md)