---
title: Modul zabezpečení pro Azure RTO – přehled
description: Přečtěte si další informace o modulu zabezpečení pro podporu a implementaci Azure RTO jako součást služby Azure Defender pro IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: ec314a729914fee26d30165c9df1644bde3845aa
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91995496"
---
# <a name="overview-defender-for-iot-security-module-for-azure-rtos-preview"></a>Přehled: Defender pro modul zabezpečení IoT pro Azure RTO (Preview)

Modul zabezpečení Azure Defenderu pro IoT poskytuje komplexní řešení zabezpečení pro zařízení, která používají Azure RTO. Poskytuje pokrytí pro běžné hrozby a potenciální škodlivé aktivity na zařízeních s operačním systémem v reálném čase (RTO). Azure RTO se teď dodává s předdefinovaným modulem zabezpečení Azure IoT.

![Vizualizace Defenderu pro IoT Azure RTO](./media/architecture/azure-rtos-security-monitoring.png)


Modul zabezpečení pro Azure RTO nabízí tyto funkce:

- Detekce škodlivé síťové aktivity
- Vlastní monitorování standardních hodnot chování zařízení založené na výstrahách
- Vylepšená hygiena zabezpečení zařízení

## <a name="detect-malicious-network-activities"></a>Zjištění škodlivých síťových aktivit

Příchozí a odchozí síťové aktivity jednotlivých zařízení jsou monitorovány. Podporované protokoly jsou TCP, UDP a ICMP na IPv4 a IPv6. Defender for IoT kontroluje každou z těchto síťových aktivit v kanálu Microsoft Threat Intelligence. Informační kanál se aktualizuje v reálném čase s miliony jedinečných indikátorů hrozeb shromážděných po celém světě.

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>Monitorování standardních hodnot chování zařízení na základě vlastních výstrah

Monitorování standardních hodnot umožňuje clusterování zařízení do skupin zabezpečení a definování očekávaného chování jednotlivých skupin. Vzhledem k tomu, že zařízení IoT jsou obvykle navržená tak, aby fungovala v dobře definovaných a omezených scénářích, je snadné vytvořit směrný plán, který definuje očekávané chování pomocí sady parametrů. Jakákoli odchylka od standardních hodnot aktivuje výstrahu.

## <a name="improve-your-device-security-hygiene"></a>Vylepšení hygieny zabezpečení zařízení

Pomocí Doporučené infrastruktury Defenderu pro IoT poskytujete znalosti a přehled o problémech v prostředí, které mají dopad na stav zabezpečení vašich zařízení a jejich poškození. Slabá stav zabezpečení pro zařízení IoT může způsobit, že potenciální útoky budou úspěšné, pokud zůstane beze změny. Zabezpečení se vždy měří podle nejslabšího propojení v rámci každé organizace.

## <a name="get-started-protecting-azure-rtos-devices"></a>Začínáme s ochranou zařízení Azure RTO

Modul zabezpečení pro Azure RTO je k dispozici jako bezplatné stahování vašich zařízení. Cloudová služba Defender for IoT je dostupná s 30denní zkušební verzí na předplatné Azure. Začněte tím, že si stáhnete [modul zabezpečení pro Azure RTO](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/iot-security-azure-rtos.md). 

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o modulu zabezpečení pro Azure RTO. Další informace o modulu zabezpečení a o tom, jak začít, najdete v následujících článcích:

- [Koncepce modulu zabezpečení Azure RTO IoT](concept-rtos-security-module.md)
- [Rychlý Start: modul zabezpečení Azure RTO IoT](quickstart-azure-rtos-security-module.md)
