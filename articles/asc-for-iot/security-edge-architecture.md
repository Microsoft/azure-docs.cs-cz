---
title: Principy Azure Security Center pro modul zabezpečení IoT pro IoT Edge Preview | Microsoft Docs
description: Seznamte se s architekturou a funkcemi Azure Security Center pro modul zabezpečení IoT pro IoT Edge.
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
ms.openlocfilehash: 6114fc768ad04ef812f6093d006ec9ad91b17af3
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596853"
---
# <a name="azure-iot-edge-security-module"></a>Modul zabezpečení Azure IoT Edge

> [!IMPORTANT]
> Azure Security Center pro službu IoT pro IoT Edge je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) poskytuje výkonné funkce pro správu a provádění obchodních pracovních postupů na hraničních zařízeních.
Klíčovou součástí, kterou IoT Edge hraje v prostředí IoT, je pro škodlivé objekty actor obzvláště atraktivní.

Azure Security Center for IoT Security Module poskytuje komplexní řešení zabezpečení pro vaše IoT Edgeá zařízení.
Azure Security Center pro modul IoT shromažďuje, agreguje a analyzuje hrubá data zabezpečení z operačního systému a systému kontejnerů do užitečných doporučení zabezpečení a výstrah.

Podobně jako u Azure Security Center pro agenty zabezpečení IoT pro zařízení IoT je modul Azure Security Center pro IoT Edge vysoce přizpůsobitelný díky jeho nevlastnímu modulu.
Další informace najdete v tématu [Konfigurace agenta](how-to-agent-configuration.md) .

Azure Security Center pro modul zabezpečení IoT pro IoT Edge nabízí tyto funkce:

- Shromažďuje nezpracované události zabezpečení ze základního operačního systému (Linux) a systémů IoT Edge kontejnerů.
  
  Další informace o dostupných kolekcích dat zabezpečení najdete v tématu [Azure Security Center pro konfiguraci agenta IoT](how-to-agent-configuration.md) .

- Analýza manifestů nasazení IoT Edge.

- Agreguje nezpracované události zabezpečení do zpráv odesílaných pomocí [centra IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Odeberte konfiguraci pomocí modulu zabezpečení s dvojitou platností.

  Další informace najdete v tématu [konfigurace Azure Security Center pro agenta IoT](how-to-agent-configuration.md) .

Azure Security Center pro modul zabezpečení IoT pro IoT Edge běží v privilegovaném režimu pod IoT Edge.
Aby modul mohl monitorovat operační systém a další IoT Edge moduly, je potřeba privilegovaný režim.

## <a name="module-supported-platforms"></a>Platformy podporované modulem

Azure Security Center pro modul zabezpečení IoT pro IoT Edge je aktuálně dostupná jenom pro Linux. 

## <a name="next-steps"></a>Další kroky

V tomto článku jste se seznámili s architekturou a funkcemi Azure Security Center pro modul zabezpečení IoT pro IoT Edge.

Pokud chcete pokračovat v seznámení s Azure Security Center pro nasazení IoT, postupujte podle následujících článků:

- Nasazení [modulu zabezpečení pro IoT Edge](how-to-deploy-edge.md)
- Informace o tom, jak [nakonfigurovat modul zabezpečení](how-to-agent-configuration.md)
- Přečtěte si téma Azure Security Center pro [požadavky služby](service-prerequisites.md) IoT
- Naučte se, jak [povolit Azure Security Center pro službu IoT v IoT Hub](quickstart-onboard-iot-hub.md)
- Další informace o službě najdete v tématu [Nejčastější dotazy k Azure Security Center pro IoT](resources-frequently-asked-questions.md)