---
title: Bezpečnostní modul pro IoT Edge
description: Seznamte se s architekturou a možnostmi modulu zabezpečení Azure Security Center pro IoT pro IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 0eb68f517c4c5b04dd4f92dd1a804dcf5ad24362
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310630"
---
# <a name="azure-iot-edge-security-module"></a>Modul zabezpečení Azure IoT Edge

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) poskytuje výkonné funkce pro správu a provádění obchodních pracovních postupů na hraničních zařízeních.
Klíčovou část, kterou ioT Edge hraje v prostředí IoT, je obzvláště atraktivní pro škodlivé herce.

Modul zabezpečení Azure Security Center for IoT poskytuje komplexní řešení zabezpečení pro vaše zařízení IoT Edge.
Azure Security Center pro ioT modul shromažďuje, agreguje a analyzuje nezpracovaná data zabezpečení z vašeho operačního systému a kontejnerového systému na užitečné doporučení zabezpečení a výstrahy.

Podobně jako Azure Security Center pro agenty zabezpečení IoT pro zařízení IoT, Azure Security Center pro IoT Edge modul je vysoce přizpůsobitelné prostřednictvím svého dvojčete modulu.
Další informace [najdete v tématu Konfigurace agenta.](how-to-agent-configuration.md)

Modul zabezpečení Azure Security Center for IoT pro IoT Edge nabízí následující funkce:

- Shromažďuje nezpracované události zabezpečení ze základního operačního systému (Linux) a systémů IoT Edge Container.

  Další informace o dostupných kolekcích dat zabezpečení najdete v [tématu Azure Security Center for IoT agent configuration.](how-to-agent-configuration.md)

- Analýza manifestů nasazení IoT Edge.

- Agreguje nezpracované události zabezpečení do zpráv odeslaných prostřednictvím [centra IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Odeberte konfiguraci pomocí dvojčete modulu zabezpečení.

  Další informace [najdete v tématu Konfigurace azure security center pro agenta IoT.](how-to-agent-configuration.md)

Azure Security Center pro zabezpečení IoT modul pro IoT Edge běží v privilegovaném režimu v rámci IoT Edge.
Privilegovaný režim je vyžadován, aby modul mohl monitorovat operační systém a další moduly IoT Edge.

## <a name="module-supported-platforms"></a>Platformy podporované moduly

Azure Security Center pro zabezpečení IoT modul pro IoT Edge je momentálně k dispozici jenom pro Linux.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o architektuře a možnostech modulu zabezpečení Azure Security Center pro IoT pro IoT Edge.

Pokud chcete pokračovat v začínáme s Azure Security Center pro nasazení IoT, použijte následující články:

- Nasazení [modulu zabezpečení pro IoT Edge](how-to-deploy-edge.md)
- Přečtěte si, jak [nakonfigurovat modul zabezpečení](how-to-agent-configuration.md)
- Kontrola požadavků Centra zabezpečení Azure pro [Službu](service-prerequisites.md) IoT
- Přečtěte si, jak [povolit službu Azure Security Center for IoT ve vašem Centru IoT Hub](quickstart-onboard-iot-hub.md)
- Další informace o službě v [centru zabezpečení Azure pro ioT nejčastější dotazy](resources-frequently-asked-questions.md)
