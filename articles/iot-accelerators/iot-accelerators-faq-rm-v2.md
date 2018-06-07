---
title: Accelerator řešení vzdáleného monitorování – nejčastější dotazy | Microsoft Docs
description: Nejčastější dotazy pro vzdálené monitorování řešení akcelerátoru
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 094bb4b781bb554d340580377ec343f33579299e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627650"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Nejčastější dotazy pro vzdálené monitorování řešení akcelerátoru

Viz také Obecné [– nejčastější dotazy](iot-accelerators-faq.md).

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Kolik stojí zřídit nové řešení vzdáleného monitorování?

Nový akcelerátor řešení nabízí dvě možnosti nasazení:

* A *základní* možnost určená pro vývojáře, kteří hledají nižší náklady na vývoj nebo zákazníci chtějí vytvořit ukázku nebo testování konceptu.
* A *standardní* možnost určený pro podniky chtějí nasadit infrastrukturu produkční prostředí.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Jak můžete zajistit, že I snížení Moje nákladů při I vyvíjet mém řešení?

Kromě dvě odlišné nasazení, nové řešení vzdáleného monitorování má nastavení, které povolí nebo zakáže všechna Simulovaná zařízení na vyžádání. Zakázání simulaci snižuje požity v řešení a proto celkové náklady na data.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Jaký je rozdíl mezi možnostmi úrovně basic a standard nasazení? Jak se rozhodnout mezi možnostmi dvě nasazení?

Jednotlivé možnosti nasazení odpoví na různé potřeby. Základní nasazení slouží k zahájení práce a vývoj, testování koncepce a rozsáhlá pilotní nasazení. Nabízí efektivní architektura s minimální potřebné prostředky a nižší náklady. Standardní nasazení slouží k vytvoření a přizpůsobení řešení pro produkční prostředí a poskytuje nasazení s nezbytné elementy pro Pamatujte si, že. Spolehlivost a škálování aplikace mikroslužeb jsou vytvořené jako kontejnery Docker a nasazují pomocí orchestrator (Kubernetes ve výchozím nastavení). Orchestrator je zodpovědná za nasazení, škálování a správu aplikace. Měli byste vybrat možnost podle aktuální potřeby. Můžete použít jednu, dalších nebo kombinace obojího, v závislosti na vaší fázi projektu.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Jak nakonfigurovat dynamické mapy na řídicím panelu?

Další informace najdete v tématu [upgradu mapy klíč zobrazit zařízení na mapě dynamické](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="next-steps"></a>Další postup

Můžete si také prostudovat některé další funkce a možnosti akcelerátorů řešení IoT:

* [Prozkoumejte možnosti akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-explore.md)
* [Přehled akcelerátorů řešení prediktivní údržby](iot-accelerators-predictive-overview.md)
* [Připojené přehled akcelerátoru objekt pro vytváření řešení](iot-accelerators-connected-factory-overview.md)
* [Zabezpečení IoT od základů](securing-iot-ground-up.md)
