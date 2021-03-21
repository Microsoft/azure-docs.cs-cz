---
title: Defender for IoT Defender – IoT-Micro-Agent pro IoT Edge
description: Seznamte se s architekturou a funkcemi Azure Defenderu pro IoT Defender – IoT-Micro-Agent pro IoT Edge.
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
ms.openlocfilehash: 4b7377f74e24660945555c112fed4d77d2c5ddcc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103495942"
---
# <a name="azure-defender-for-iot-edge-defender-iot-micro-agent"></a>Azure Defender pro IoT Edge Defender – IoT-Micro-Agent

[Azure IoT Edge](../iot-edge/index.yml) poskytuje výkonné funkce pro správu a provádění obchodních pracovních postupů na hraničních zařízeních.
Klíčovou součástí, kterou IoT Edge hraje v prostředí IoT, je pro škodlivé objekty actor obzvláště atraktivní.

Defender for IoT Defender – IoT-Micro-agent poskytuje komplexní řešení zabezpečení pro vaše IoT Edge zařízení.
Defender pro modul IoT shromažďuje, agreguje a analyzuje nezpracované bezpečnostní údaje z operačního systému a systému kontejnerů do užitečných doporučení zabezpečení a výstrah.

Podobně jako program Defender pro agenty zabezpečení IoT pro zařízení IoT je možné, že je modul Defender for IoT Edge vysoce přizpůsobitelný prostřednictvím jeho nevlákenných modulů.
Další informace najdete v tématu [Konfigurace agenta](how-to-agent-configuration.md) .

Defender for IoT Defender – IoT-Micro-Agent pro IoT Edge nabízí tyto funkce:

- Shromažďuje nezpracované události zabezpečení ze základního operačního systému (Linux) a systémů IoT Edge kontejnerů.

  Další informace o dostupných kolekcích dat zabezpečení najdete v tématu [Defender pro konfiguraci agenta IoT](how-to-agent-configuration.md) .

- Analýza manifestů nasazení IoT Edge.

- Agreguje nezpracované události zabezpečení do zpráv odesílaných pomocí [centra IoT Edge](../iot-edge/iot-edge-runtime.md#iot-edge-hub).

- Odeberte konfiguraci prostřednictvím používání programu Defender-IoT-Micro-agent s dvojitou platností.

  Další informace najdete v tématu [Konfigurace programu Defender pro agenta IoT](how-to-agent-configuration.md) .

Defender pro IoT Defender – IoT-Micro-Agent pro IoT Edge běží v privilegovaném režimu v části IoT Edge.
Aby modul mohl monitorovat operační systém a další IoT Edge moduly, je potřeba privilegovaný režim.

## <a name="module-supported-platforms"></a>Platformy podporované modulem

Defender for IoT Defender – IoT-Micro-Agent pro IoT Edge je v tuto chvíli dostupný jenom pro Linux.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se seznámili s architekturou a funkcemi programu Defender pro IoT Defender – IoT-Micro-Agent pro IoT Edge.

Pokud chcete pokračovat v seznámení s Defenderem pro nasazení IoT, postupujte podle následujících článků:

- Nasazení [programu Defender-IoT-Micro-Agent pro IoT Edge](how-to-deploy-edge.md)
- Informace o tom, jak [nakonfigurovat Defender – IoT-Micro-agent](how-to-agent-configuration.md)
- Kontrola programu Defender for IoT [Defender pro IoT Horizon](resources-manage-proprietary-protocols.md)
- Naučte se, jak [ve IoT Hub povolit Defender pro službu IoT](quickstart-onboard-iot-hub.md) .
- Další informace o službě najdete v tématu [Nejčastější dotazy k programu Defender pro IoT](resources-frequently-asked-questions.md) .