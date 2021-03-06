---
title: Jak nainstalovat IoT Edge v Kubernetes | Microsoft Docs
description: Naučte se, jak nainstalovat IoT Edge v Kubernetes pomocí prostředí místního vývojového clusteru.
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: iotedge-2018-06
ms.openlocfilehash: a5407c46be6726974a920b5dddd87639dc426d0d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587251"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Postup instalace IoT Edge v Kubernetes (Preview)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

IoT Edge můžete integrovat s Kubernetes a používat ji jako odolnou, vysoce dostupnou infrastrukturu infrastruktury. Tady je místo, kde se tato podpora vejde do řešení IoT Edge vysoké úrovně:

![k8s Úvod](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>Dobrým duševním modelem této integrace je představit Kubernetes jako jiné operační prostředí, IoT Edge Aplikace můžou běžet kromě systémů Linux a Windows.

## <a name="architecture"></a>Architektura 
V Kubernetes IoT Edge poskytuje pro nasazení hraničních úloh *vlastní definici prostředků* (CRD). Agent IoT Edge předpokládá, že role  *řadiče CRD* sjednotí požadovaný stav cloudu spravovaného cloudu s místním stavem clusteru.

Doba života modulu je spravovaná plánovačem Kubernetes, který udržuje dostupnost modulu a volí jejich umístění. IoT Edge spravuje platformu hraniční aplikace spuštěnou nahoře a nepřetržitě slučuje požadovaný stav zadaný v IoT Hub se stavem na hraničním clusteru. Model aplikace je stále známý model založený na IoT Edgech modulech a trasách. Řadič IoT Edge agenta provádí model aplikace *automatických* IoT Edge překladu do nativních konstrukcí Kubernetes, jako jsou lusky, nasazení, služby atd.

Tady je diagram architektury vysoké úrovně:

![Kubernetes arch](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Každá součást nasazení Edge je vymezená na obor názvů Kubernetes specifický pro zařízení, takže je možné sdílet stejné prostředky clusteru mezi více hraničními zařízeními a jejich nasazeními.

>[!NOTE]
>IoT Edge v Kubernetes je ve [verzi Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="tutorials-and-references"></a>Kurzy a odkazy 

Další informace, včetně podrobných kurzů a odkazů, najdete v článku [IoT Edge na webu Kubernetes ve verzi Preview](https://aka.ms/edgek8sdoc) .
