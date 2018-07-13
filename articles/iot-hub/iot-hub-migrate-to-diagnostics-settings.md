---
title: Azure IoT Hub migrace do nastavení diagnostiky | Dokumentace Microsoftu
description: Postup aktualizace Azure IoT Hub a nastavení Azure diagnostics nahrazujícím operace monitorování ke sledování stavu operací ve službě IoT hub v reálném čase.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.author: kgremban
ms.openlocfilehash: 85bdb4b4802283c591e4d7a9e8b14ae74fa44e8d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38666718"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Migrace služby IoT Hub z operací do nastavení diagnostiky

Zákazníci, kteří používají [monitorování operací] [ lnk-opsmon] sledovat stav operací ve službě IoT Hub můžete migrovat do pracovního postupu [nastavení Azure diagnostics] [ lnk-diagnostics-settings], která je součástí Azure Monitor. Nastavení diagnostiky zadat diagnostické informace na úrovni prostředků pro řadou služeb Azure.

Operace monitorování funkcí služby IoT Hub je zastaralá a v budoucnu se odebere. Tento článek vysvětluje, jak přesunovat zatížení z operací do nastavení diagnostiky. Další informace o vyřazení časové osy, naleznete v tématu [monitorování řešení Azure IoT pomocí Azure monitoru a Azure Resource Health][lnk-blog-announcement].

## <a name="update-iot-hub"></a>Aktualizace služby IoT Hub

Aktualizace služby IoT Hub na webu Azure Portal, nejprve zapnout nastavení diagnostiky a potom vypnout monitorování operací.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Vypnout monitorování operací

Jakmile jste otestovali nové nastavení diagnostiky na váš pracovní postup, můžete vypnout funkci monitorování operací. 

1. V nabídce centra IoT, vyberte **monitorování operací**.
1. V každé kategorii monitorování vyberte **žádný**.
1. Uložte provoz, sledování změn.

## <a name="update-applications-that-use-operations-monitoring"></a>Aktualizace aplikace, které používají monitorování operací

Schémata pro monitorování operací a nastavení diagnostiky se mírně liší. Je důležité, že můžete aktualizovat aplikace, které operace sledování dnes můžete využít k mapovány na schéma používané nastavení diagnostiky. 

Navíc diagnostické nastavení nabízí sledování pět nových kategorií. Po aktualizaci aplikace pro existující schéma, přidáte nové kategorie:

- Operace dvojčete typu cloud zařízení
- Operace dvojčete zařízení cloud
- Dotazy dvojčete
- Operace úloh
- Přímé metody

Struktury konkrétní schématu naleznete v tématu [pochopit, že schéma pro nastavení diagnostiky][lnk-diagnostics-schema].

## <a name="next-steps"></a>Další postup

- [Monitorování stavu služby Azure IoT Hub a rychlá Diagnostika potíží][lnk-monitor]

[lnk-opsmon]: iot-hub-operations-monitoring.md
[lnk-diagnostics-settings]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[lnk-diagnostics-schema]: iot-hub-monitor-resource-health.md#understand-the-logs
[lnk-blog-announcement]: https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/
[lnk-monitor]: iot-hub-monitor-resource-health.md
