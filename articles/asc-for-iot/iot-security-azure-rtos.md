---
title: Podpora Azure RTO
description: Přečtěte si o podpoře Azure RTO v Azure Security Center pro službu IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2020
ms.author: mlottner
ms.openlocfilehash: af4579d25b94fc12f67cfc91d0f6a5af4b35c810
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096697"
---
# <a name="azure-security-center-for-iot-security-solution-for-azure-rtos"></a>Řešení zabezpečení IoT pro Azure RTO Azure Security Center 

Modul zabezpečení Azure Security Center pro IoT poskytuje komplexní řešení zabezpečení pro zařízení Azure RTO. Azure RTO se dodává s integrovaným modulem zabezpečení, který se zabývá běžnými hrozbami na zařízeních s operačním systémem v reálném čase. 

![Azure Security Center pro IoT Azure RTO](./media/architecture/azure-rtos-security-monitoring.png)


Azure Security Center pro modul zabezpečení IoT se službou Azure RTO support nabízí tyto funkce: 
- Detekce škodlivé síťové aktivity
- Vlastní upozornění založené na monitorování standardních hodnot chování zařízení
- Zlepšení hygieny zabezpečení zařízení

### <a name="detection-of-malicious-network-activities"></a>Detekce síťových aktivit se zlými úmysly

Příchozí a odchozí síťové aktivity jednotlivých zařízení se monitorují (podporované protokoly: TCP, UDP, ICMP na IPv4 a IPv6). Azure Security Center pro IoT kontroluje každou z těchto síťových aktivit v informačním kanálu Microsoft Threat Intelligence. Informační kanál se aktualizuje v reálném čase s miliony jedinečných indikátorů hrozeb shromážděných po celém světě. 

### <a name="device-behavior-baselining-based-on-custom-alerts"></a>Monitorování standardních hodnot chování zařízení na základě vlastních výstrah

Monitorování standardních hodnot umožňuje clusterování zařízení do skupin zabezpečení a definování očekávaného chování jednotlivých skupin. Vzhledem k tomu, že zařízení IoT jsou obvykle navržená tak, aby fungovala v dobře definovaných a omezených scénářích, je snadné vytvořit směrný plán, který definuje očekávané chování pomocí sady parametrů. Jakákoli odchylka od standardních hodnot vyvolá výstrahu. 

### <a name="improve-your-device-security-hygiene"></a>Vylepšení hygieny zabezpečení zařízení

Využitím Doporučené infrastruktury Azure Security Center pro IoT získáte znalostní báze a přehled o problémech v prostředí, které mají dopad na stav zabezpečení vašich zařízení a jejich poškození. Špatné zabezpečení zařízení IoT stav může dovolit úspěšné útoky, pokud zůstane beze změny, protože zabezpečení se vždy měří podle nejslabšího odkazu v rámci jakékoli organizace. 

## <a name="get-started-protecting-azure-rtos-devices"></a>Začínáme s ochranou zařízení Azure RTO

- Azure Security Center pro modul zabezpečení IoT pro Azure RTO je k dispozici jako bezplatné stažení pro vaše zařízení. Cloudová služba Azure Security Center for IoT je k dispozici se zkušební verzí na předplatné Azure za 30 dní. Pokud chcete začít, Stáhněte si [modul zabezpečení Azure Security Center for IoT pro Azure RTO](https://github.com/azure-rtos/iot-security-module-preview) . 


## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o Azure Security Center podpoře služby IoT Azure RTO. Informace o tom, jak začít a jak povolit řešení zabezpečení v IoT Hub, najdete v následujících článcích:

- [Požadavky služeb](service-prerequisites.md)
- [Začínáme](getting-started.md)
- [Konfigurace vašeho řešení](quickstart-configure-your-solution.md)
- [Povolit zabezpečení v IoT Hub](quickstart-onboard-iot-hub.md)
- [Nejčastější dotazy k Azure Security Center pro IoT](resources-frequently-asked-questions.md)
- [Výstrahy zabezpečení ve službě Azure Security Center pro IoT](concept-security-alerts.md)
