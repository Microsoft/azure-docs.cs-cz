---
title: Vzdáleného sledování nejčastější dotazy k Azure IoT Suite | Microsoft Docs
description: Nejčastější dotazy pro předkonfigurované řešení vzdáleného sledování IoT Suite
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
ms.openlocfilehash: b0d6dbb567f66537c599d7ac3e4337988b3374c0
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="frequently-asked-questions-for-iot-suite-remote-monitoring-preconfigured-solution"></a>Nejčastější dotazy pro předkonfigurované řešení vzdáleného sledování IoT Suite

Viz také Obecné [– nejčastější dotazy](iot-suite-faq.md).

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Kolik stojí zřídit nové řešení vzdáleného monitorování?

Nové předkonfigurované řešení nabízí dvě možnosti nasazení:

* A *základní* možnost určená pro vývojáře, kteří hledají nižší náklady na vývoj nebo zákazníci chtějí vytvořit ukázku nebo testování konceptu.
* A *standardní* možnost určený pro podniky chtějí nasadit infrastrukturu produkční prostředí.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Jak můžete zajistit, že I snížení Moje nákladů při I vyvíjet mém řešení?

Kromě dvě odlišné nasazení, nové řešení vzdáleného monitorování má nastavení, které povolí nebo zakáže všechna Simulovaná zařízení na vyžádání. Zakázání simulaci snižuje požity v řešení a proto celkové náklady na data.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Jaký je rozdíl mezi možnostmi úrovně basic a standard nasazení? Jak se rozhodnout mezi možnostmi dvě nasazení?

Jednotlivé možnosti nasazení odpoví na různé potřeby. Základní nasazení slouží k zahájení práce a vývoj, testování koncepce a rozsáhlá pilotní nasazení. Nabízí efektivní architektura s minimální potřebné prostředky a nižší náklady. Standardní nasazení slouží k vytvoření a přizpůsobení řešení pro produkční prostředí a poskytuje nasazení s nezbytné elementy pro Pamatujte si, že. Spolehlivost a škálování aplikace mikroslužeb jsou vytvořené jako kontejnery Docker a nasazují pomocí orchestrator (Kubernetes ve výchozím nastavení). Orchestrator je zodpovědná za nasazení, škálování a správu aplikace. Měli byste vybrat možnost podle aktuální potřeby. Můžete použít jednu, dalších nebo kombinace obojího, v závislosti na vaší fázi projektu.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Jak nakonfigurovat dynamické mapy na řídicím panelu?

Další informace najdete v tématu [upgradu mapy klíč zobrazit zařízení na mapě dynamické](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Kolik bezplatných rozhraní API Map Bing můžu zřídit v jednom předplatném?

Dvě. Můžete vytvořit pouze dvě vnitřní transakce úroveň 1 mapy Bing pro podnikových plánů v předplatné Azure. Ve výchozím nastavení s plánem vnitřní transakce úrovně 1 se zřídí řešení vzdáleného monitorování. V důsledku toho můžete v daném předplatném zřídit nanejvýš dvě předkonfigurovaná řešení vzdáleného monitorování bez možnosti úprav.

### <a name="next-steps"></a>Další postup

Můžete si taky prostudovat některé další funkce a možnosti předkonfigurovaných řešení sady IoT Suite:

* [Prozkoumejte možnosti předkonfigurovaného řešení vzdáleného monitorování](iot-suite-remote-monitoring-explore.md)
* [Přehled řešení předkonfigurované prediktivní údržby](iot-suite-predictive-overview.md)
* [Přehled připojené objekt pro vytváření předkonfigurovaného řešení](iot-suite-connected-factory-overview.md)
* [Zabezpečení IoT od základů](securing-iot-ground-up.md)