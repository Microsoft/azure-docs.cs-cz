---
title: Defender pro modul zabezpečení IoT pro IoT Edge
description: Seznamte se s architekturou a možnostmi modulu zabezpečení Azure Defender pro IoT pro IoT Edge.
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
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 1933e60892430b907e070ea04f39d8acc86ddac2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90935328"
---
# <a name="azure-defender-for-iot-edge-security-module"></a>Modul zabezpečení Azure Defenderu pro IoT Edge

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) poskytuje výkonné funkce pro správu a provádění obchodních pracovních postupů na hraničních zařízeních.
Klíčovou součástí, kterou IoT Edge hraje v prostředí IoT, je pro škodlivé objekty actor obzvláště atraktivní.

Modul zabezpečení služby Defender pro IoT poskytuje komplexní řešení zabezpečení pro vaše IoT Edge zařízení.
Defender pro modul IoT shromažďuje, agreguje a analyzuje nezpracované bezpečnostní údaje z operačního systému a systému kontejnerů do užitečných doporučení zabezpečení a výstrah.

Podobně jako program Defender pro agenty zabezpečení IoT pro zařízení IoT je možné, že je modul Defender for IoT Edge vysoce přizpůsobitelný prostřednictvím jeho nevlákenných modulů.
Další informace najdete v tématu [Konfigurace agenta](how-to-agent-configuration.md) .

Defender for IoT Security Module for IoT Edge nabízí tyto funkce:

- Shromažďuje nezpracované události zabezpečení ze základního operačního systému (Linux) a systémů IoT Edge kontejnerů.

  Další informace o dostupných kolekcích dat zabezpečení najdete v tématu [Defender pro konfiguraci agenta IoT](how-to-agent-configuration.md) .

- Analýza manifestů nasazení IoT Edge.

- Agreguje nezpracované události zabezpečení do zpráv odesílaných pomocí [centra IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Odeberte konfiguraci pomocí modulu zabezpečení s dvojitou platností.

  Další informace najdete v tématu [Konfigurace programu Defender pro agenta IoT](how-to-agent-configuration.md) .

Modul zabezpečení Defenderu pro IoT IoT Edge běží v privilegovaném režimu v části IoT Edge.
Aby modul mohl monitorovat operační systém a další IoT Edge moduly, je potřeba privilegovaný režim.

## <a name="module-supported-platforms"></a>Platformy podporované modulem

Defender for IoT Security Module for IoT Edge je v tuto chvíli dostupný jenom pro Linux.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se seznámili s architekturou a funkcemi Defenderu pro modul zabezpečení IoT pro IoT Edge.

Pokud chcete pokračovat v seznámení s Defenderem pro nasazení IoT, postupujte podle následujících článků:

- Nasazení [modulu zabezpečení pro IoT Edge](how-to-deploy-edge.md)
- Informace o tom, jak [nakonfigurovat modul zabezpečení](how-to-agent-configuration.md)
- Přečtěte si téma Defender for IoT [Service – požadavky](service-prerequisites.md)
- Naučte se, jak [ve IoT Hub povolit Defender pro službu IoT](quickstart-onboard-iot-hub.md) .
- Další informace o službě najdete v tématu [Nejčastější dotazy k programu Defender pro IoT](resources-frequently-asked-questions.md) .
