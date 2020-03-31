---
title: Jak nainstalovat IoT Edge na Kubernetes | Dokumenty společnosti Microsoft
description: Další informace o instalaci IoT Edge na Kubernetes pomocí prostředí clusteru pro místní vývoj
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b2068c3944f9e7616b0666c7bafcafc68ee0cd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471281"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Jak nainstalovat IoT Edge na Kubernetes (Náhled)

IoT Edge se může integrovat s Kubernetes a používat ho jako odolnou a vysoce dostupnou vrstvu infrastruktury. Zde je místo, kde tato podpora zapadá do řešení IoT Edge na vysoké úrovni:

![k8s úvod](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>Dobrým mentálním modelem pro tuto integraci je myslet na Kubernetes jako na další operační prostředí, na kterých mohou aplikace IoT Edge běžet kromě Linuxu a Windows.

## <a name="architecture"></a>Architektura 
Na Kubernetes, IoT Edge poskytuje *vlastní definici prostředků* (CRD) pro nasazení úloh y edge. IoT Edge Agent přebírá roli *řadiče CRD,* který odsouhlasí clouds spravované požadovaný stav se stavem místního clusteru.

Životnost modulu spravuje plánovač Kubernetes, který udržuje dostupnost modulu a vybírá jejich umístění. IoT Edge spravuje hraniční aplikační platformu spuštěnou nahoře a průběžně odřazuje požadovaný stav zadaný v ioT hubu se stavem v hraničním clusteru. Aplikační model je stále známý model založený na modulech a trasách IoT Edge. Řadič IoT Edge Agent provádí *automatický* překlad aplikačního modelu IoT Edge do nativních konstrukcí Kubernetes, jako jsou pody, nasazení, služby atd.

Zde je diagram architektury na vysoké úrovni:

![kubernetes oblouk](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Každá součást nasazení hraniční sítě je vymezena na obor názvů Kubernetes specifické pro zařízení, což umožňuje sdílet stejné prostředky clusteru mezi více hraničních zařízení a jejich nasazení.

>[!NOTE]
>IoT Edge na Kubernetes je ve [veřejné verzi Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="tutorials-and-references"></a>Kurzy a reference 

Další informace, včetně podrobných výukových lekcí a odkazů, najdete [na miniwebu náhledu dokumentů IoT Edge na Kubernetes.](https://aka.ms/edgek8sdoc)
