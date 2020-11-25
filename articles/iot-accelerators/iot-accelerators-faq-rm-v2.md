---
title: Nejčastější dotazy k akcelerátoru řešení vzdáleného monitorování – Azure | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy k akcelerátorům řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 64391d7f5a7b1a295be7e404d27e5cbe8c691eb2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995943"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Nejčastější dotazy k akcelerátoru řešení vzdáleného monitorování

Viz také obecné [Nejčastější dotazy](iot-accelerators-faq.md).

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Kolik stojí za zřízení nového řešení vzdáleného monitorování?

Nové akcelerátory řešení nabízí dvě možnosti nasazení:

* *Základní* možnost určená pro vývojáře, kteří hledají nižší náklady na vývoj nebo zákazníky, kteří chtějí vytvořit ukázku nebo zkoušku konceptu.
* *Standardní* možnost navržená pro podniky, které chtějí nasadit infrastrukturu připravenou pro produkční prostředí.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Jak se dá zajistit, aby se při vývoji mého řešení udržovaly mé náklady?

Kromě poskytování dvou různých nasazení má nové řešení vzdáleného monitorování nastavení pro povolení nebo zakázání všech simulovaných zařízení na vyžádání. Zakázáním simulace se zmenší data ingestovaná v řešení a tak celkové náklady.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Jaký je rozdíl mezi možnostmi Basic a standard pro nasazení? Návody se mezi těmito dvěma možnostmi nasazení rozhodnout?

Každá možnost nasazení reaguje na různé potřeby. Základní nasazení je navržené tak, aby bylo možné začít s vývojem a malými pilotními modulem pro ověření koncepce. Nabízí zjednodušenou architekturu s minimálními potřebnými prostředky a nižšími náklady. Standardní nasazení je navrženo tak, aby vytvořilo a upravilo řešení připravené pro produkční prostředí a poskytuje nasazení s nezbytnými prvky, které je potřeba realizovat. Pro spolehlivost a škálování jsou mikroslužby aplikací sestavené jako kontejnery Docker a nasazené pomocí nástroje Orchestrator (Kubernetes standardně). Produkt Orchestrator zodpovídá za nasazení, škálování a správu aplikace. V závislosti na vašich aktuálních potřebách byste si měli vybrat možnost. Můžete použít jeden, druhý nebo kombinaci obou obou v závislosti na fázi projektu.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Návody nakonfigurovat na řídicím panelu dynamickou mapu?

Další informace najdete v tématu [upgrade mapového klíče pro zobrazení zařízení na dynamické mapě](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Kde najdu informace o předchozí verzi řešení vzdáleného monitorování?

Předchozí verze akcelerátoru řešení vzdáleného monitorování byla známá jako předkonfigurované řešení vzdáleného monitorování IoT Suite. Archivovanou dokumentaci najdete na adrese [https://docs.microsoft.com/previous-versions/azure/iot-suite/](/previous-versions/azure/iot-suite/) .

### <a name="next-steps"></a>Další kroky

Můžete si také prostudovat některé další funkce a možnosti akcelerátorů řešení IoT:

* [Prozkoumejte možnosti akcelerátoru řešení vzdáleného monitorování](quickstart-remote-monitoring-deploy.md)
* [Přehled akcelerátoru řešení prediktivní údržby](./iot-accelerators-predictive-walkthrough.md)
* [Nasadit akcelerátor řešení propojené továrny](quickstart-connected-factory-deploy.md)
* [Zabezpečení IoT od počátku](../iot-fundamentals/iot-security-ground-up.md)