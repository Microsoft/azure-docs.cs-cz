---
title: Součásti & požadavky
description: Podrobnosti všeho potřebného pro zahájení práce s Azure Defenderem pro požadavky služby IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.custom: references_regions
ms.openlocfilehash: b0913dc48f807c26396a38e31d293877b4561b7d
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089175"
---
# <a name="azure-defender-for-iot-prerequisites"></a>Předpoklady pro Azure Defender pro IoT

Tento článek obsahuje vysvětlení různých komponent programu Defender pro službu IoT, co je potřeba začít, a vysvětluje základní koncepty, které vám pomůžou pochopit službu.

## <a name="minimum-requirements"></a>Minimální požadavky

- Monitorování pro zařízení IoT a OT bez agentů (na základě technologie CyberX)
    - Síťové přepínače podporující monitorování přenosů přes port SPAN
    - Hardwarová zařízení pro senzor NTA, další informace najdete v tématu [certifikovaný hardware](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) .
    - Role **přispěvatele** předplatného Azure (vyžaduje se jenom během připojování k definování potvrzených zařízení)
    - Role **přispěvatele** pro IoT Hub (volná nebo standardní úroveň) (pro správu připojenou ke cloudu)
- Zabezpečení spravovaných zařízení IoT spravovaných prostřednictvím Azure IoT Hub
    - Role **přispěvatele** IoT Hub (úroveň Standard)
    - IoT Hub: přepínač funkcí **v programu Azure Defender pro IoT** by měl být povolený.
    - Podpora modulu zabezpečení na úrovni zařízení  
        - Defender pro agenty IoT podporuje rostoucí seznam zařízení a platforem, viz [seznam podporovaných platforem](how-to-deploy-agent.md) .


## <a name="supported-service-regions"></a>Podporované oblasti služby

Další informace najdete v tématu [podporované oblasti IoT Hub](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) . 

Defender pro IoT směruje veškerý provoz ze všech evropských oblastí do Západní Evropa oblastních datových center a všech zbývajících oblastí do Střed USA oblastního datového centra.

## <a name="next-steps"></a>Další kroky

- Přečtěte si [Přehled](overview.md) zabezpečení Azure IoT.
- Informace o tom, jak [službu povolit](quickstart-onboard-iot-hub.md)
- Přečtěte si [Nejčastější dotazy k Defenderu pro IoT](resources-frequently-asked-questions.md)
- Prozkoumejte, jak [porozumět programu Defender pro výstrahy IoT](concept-security-alerts.md)
