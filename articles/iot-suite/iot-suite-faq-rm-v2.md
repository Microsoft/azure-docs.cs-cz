---
title: Accelerator řešení vzdáleného monitorování – nejčastější dotazy | Microsoft Docs
description: Nejčastější dotazy pro vzdálené monitorování řešení akcelerátoru
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: d1cc260710d025428a1ca77c41c104dc172447e6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Nejčastější dotazy pro vzdálené monitorování řešení akcelerátoru

Viz také Obecné [– nejčastější dotazy](iot-suite-faq.md).

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

* [Prozkoumejte možnosti vzdáleného monitorování řešení akcelerátoru](iot-suite-remote-monitoring-explore.md)
* [– Přehled akcelerátoru řešení prediktivní údržby](iot-suite-predictive-overview.md)
* [Připojené přehled akcelerátoru objekt pro vytváření řešení](iot-suite-connected-factory-overview.md)
* [Zabezpečení IoT od základů](securing-iot-ground-up.md)