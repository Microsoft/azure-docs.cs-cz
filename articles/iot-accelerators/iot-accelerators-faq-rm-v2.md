---
title: Nejčastější dotazy k akcelerátoru řešení vzdáleného monitorování – Azure | Dokumenty společnosti Microsoft
description: Tento článek odpovídá na nejčastější dotazy pro akcelerátory řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: c410ac7f41adb623a7198320a1edced097778569
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73826239"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Nejčastější dotazy týkající se akcelerátoru řešení vzdáleného monitorování

Viz také obecné [nejčastější dotazy](iot-accelerators-faq.md).

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Kolik stojí zřízení nového řešení vzdáleného monitorování?

Nový akcelerátor řešení nabízí dvě možnosti nasazení:

* *Základní* možnost určená pro vývojáře, kteří hledají nižší náklady na vývoj, nebo zákazníky, kteří chtějí vytvořit demo nebo důkaz koncepce.
* *Standardní* možnost určená pro podniky, které chtějí nasadit infrastrukturu připravenou pro produkční prostředí.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Jak zajistím, že při vývoji řešení udržím své náklady na nízké samých?

Kromě poskytování dvou diferencovaných nasazení má nové řešení vzdáleného monitorování nastavení pro povolení nebo zakázání všech simulovaných zařízení na vyžádání. Zakázání simulace snižuje data požitá v řešení a tím i celkové náklady.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Jaký je rozdíl mezi základními a standardními možnostmi nasazení? Jak se rozhodnu mezi těmito dvěma možnostmi nasazení?

Každá možnost nasazení reaguje na různé potřeby. Základní nasazení je navrženo tak, aby začalo a vyvíjelo PoC a malé piloty. Poskytuje zjednodušenou architekturu s minimálními potřebnými zdroji a nižšími náklady. Standardní nasazení je navrženo tak, aby vytvářelo a přizpůsobovat řešení připravené pro produkční prostředí, a poskytuje nasazení s nezbytnými prvky k jeho realizaci. Pro spolehlivost a škálování jsou mikroslužby aplikací sestaveny jako kontejnery Dockeru a nasazeny pomocí orchestrator (Kubernetes ve výchozím nastavení). Orchestrator je zodpovědný za nasazení, škálování a správu aplikace. Měli byste zvolit možnost na základě vašich aktuálních potřeb. Můžete použít jeden, druhý nebo kombinaci obou v závislosti na fázi projektu.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Jak nakonfiguruji dynamickou mapu na řídicím panelu?

Další informace naleznete v [tématu Upgrade mapového klíče a zobrazení zařízení na dynamické mapě](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Kde najdu informace o předchozí verzi řešení vzdáleného monitorování?

Předchozí verze akcelerátoru řešení vzdáleného monitorování byla známá jako předkonfigurované řešení vzdáleného monitorování sady IoT Suite. Archivoci naleznete na [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/)adrese .

### <a name="next-steps"></a>Další kroky

Můžete si také prostudovat některé další funkce a možnosti akcelerátorů řešení IoT:

* [Seznamte se s možnostmi akcelerátoru řešení vzdáleného monitorování](quickstart-remote-monitoring-deploy.md)
* [Přehled akcelerátorů řešení prediktivní údržby](iot-accelerators-predictive-overview.md)
* [Nasazení akcelerátoru řešení připojené továrny](quickstart-connected-factory-deploy.md)
* [Zabezpečení IoT od počátku](/azure/iot-fundamentals/iot-security-ground-up)
