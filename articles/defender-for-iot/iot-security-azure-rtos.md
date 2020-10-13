---
title: Modul zabezpečení pro Azure RTO – přehled
description: Další informace o modulu zabezpečení pro podporu a implementaci Azure RTO jako součást služby Defender pro IoT Service
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
ms.openlocfilehash: 22bd12bbdcccef2fd0e9010f926cd18e95d42967
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761851"
---
# <a name="overview-defender-for-iot-security-module-for-azure-rtos-preview"></a>Přehled: Defender pro modul zabezpečení IoT pro Azure RTO (Preview)

Modul zabezpečení Defender for IoT pro RTO poskytuje komplexní řešení zabezpečení pro zařízení Azure RTO. Azure RTO se teď dodává s integrovaným modulem zabezpečení Azure IoT a poskytuje pokrytí pro běžné hrozby a potenciální škodlivé aktivity na zařízeních s operačním systémem v reálném čase. 

![Defender pro IoT Azure RTO](./media/architecture/azure-rtos-security-monitoring.png)


Modul zabezpečení pro Azure RTO nabízí tyto funkce: 
- Detekce škodlivé síťové aktivity
- Vlastní upozornění založené na monitorování standardních hodnot chování zařízení
- Zlepšení hygieny zabezpečení zařízení

## <a name="detection-of-malicious-network-activities"></a>Detekce síťových aktivit se zlými úmysly

Příchozí a odchozí síťové aktivity jednotlivých zařízení se monitorují (podporované protokoly: TCP, UDP, ICMP na IPv4 a IPv6). Defender for IoT kontroluje každou z těchto síťových aktivit v kanálu Microsoft Threat Intelligence. Informační kanál se aktualizuje v reálném čase s miliony jedinečných indikátorů hrozeb shromážděných po celém světě. 

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>Monitorování standardních hodnot chování zařízení na základě vlastních výstrah

Monitorování standardních hodnot umožňuje clusterování zařízení do skupin zabezpečení a definování očekávaného chování jednotlivých skupin. Vzhledem k tomu, že zařízení IoT jsou obvykle navržená tak, aby fungovala v dobře definovaných a omezených scénářích, je snadné vytvořit směrný plán, který definuje očekávané chování pomocí sady parametrů. Jakákoli odchylka od standardních hodnot vyvolá výstrahu. 

## <a name="improve-your-device-security-hygiene"></a>Vylepšení hygieny zabezpečení zařízení

Využitím Doporučené infrastruktury Defenderu pro IoT získáte znalosti a přehled o problémech v prostředí, které mají dopad na stav zabezpečení vašich zařízení a jejich poškození. Slabé zabezpečení zařízení IoT stav může dovolit úspěšné útoky, pokud zůstane beze změny, protože zabezpečení se vždy měří podle nejslabšího odkazu v rámci jakékoli organizace. 

## <a name="get-started-protecting-azure-rtos-devices"></a>Začínáme s ochranou zařízení Azure RTO

Modul zabezpečení pro Azure RTO je k dispozici jako bezplatné stahování vašich zařízení. Cloudová služba Defender for IoT je dostupná s 30denní zkušební verzí na předplatné Azure. Pokud chcete začít, Stáhněte si [modul zabezpečení pro Azure RTO](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/iot-security-azure-rtos.md) . 


## <a name="next-steps"></a>Další kroky

V tomto článku jste se seznámili s modulem zabezpečení pro službu Azure RTO. Další informace o modulu zabezpečení a o tom, jak začít, najdete v následujících článcích:

- [Koncepce modulu zabezpečení Azure RTO IoT](concept-rtos-security-module.md)
- [Rychlý Start: modul zabezpečení Azure RTO IoT](quickstart-azure-rtos-security-module.md)
