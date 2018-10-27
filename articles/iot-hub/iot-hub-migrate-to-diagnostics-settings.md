---
title: Azure IoT Hub migrace do nastavení diagnostiky | Dokumentace Microsoftu
description: Postup aktualizace Azure IoT Hub a nastavení Azure diagnostics nahrazujícím operace monitorování ke sledování stavu operací ve službě IoT hub v reálném čase.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.author: kgremban
ms.openlocfilehash: 3cb0f91f3143e6a4828548f3a15678b3814cba17
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50154857"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Migrace služby IoT Hub z operací do nastavení diagnostiky

Zákazníci, kteří používají [monitorování operací](iot-hub-operations-monitoring.md) sledovat stav operací ve službě IoT Hub můžete migrovat do pracovního postupu [nastavení Azure diagnostics](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md), funkce služby Azure Monitor. Nastavení diagnostiky zadat diagnostické informace na úrovni prostředků pro řadou služeb Azure.

Operace monitorování funkcí služby IoT Hub je zastaralá a v budoucnu se odebere. Tento článek vysvětluje, jak přesunovat zatížení z operací do nastavení diagnostiky. Další informace o vyřazení časové osy, naleznete v tématu [monitorování řešení Azure IoT pomocí Azure monitoru a Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>Aktualizace služby IoT Hub

Aktualizace služby IoT Hub na webu Azure Portal, nejprve zapnout nastavení diagnostiky a potom vypnout monitorování operací.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Vypnout monitorování operací

Jakmile jste otestovali nové nastavení diagnostiky na váš pracovní postup, můžete vypnout funkci monitorování operací. 

1. V nabídce centra IoT, vyberte **monitorování operací**.

2. V každé kategorii monitorování vyberte **žádný**.

3. Uložte provoz, sledování změn.

## <a name="update-applications-that-use-operations-monitoring"></a>Aktualizace aplikace, které používají monitorování operací

Schémata pro monitorování operací a nastavení diagnostiky se mírně liší. Je důležité, že můžete aktualizovat aplikace, které operace sledování dnes můžete využít k mapovány na schéma používané nastavení diagnostiky. 

Navíc diagnostické nastavení nabízí sledování pět nových kategorií. Po aktualizaci aplikace pro existující schéma, přidáte nové kategorie:

* Operace dvojčete typu cloud zařízení
* Operace dvojčete zařízení cloud
* Dotazy dvojčete
* Operace úloh
* Přímé metody

Struktury konkrétní schématu naleznete v tématu [pochopit, že schéma pro nastavení diagnostiky](iot-hub-monitor-resource-health.md#understand-the-logs).

## <a name="next-steps"></a>Další postup

* [Monitorování stavu služby Azure IoT Hub a rychlá diagnostika potíží](iot-hub-monitor-resource-health.md)