---
title: Principy ASC pro IoT zabezpečení modul IoT Edge | Dokumentace Microsoftu
description: Vysvětlení architektury a možností ASC pro modul zabezpečení IoT pro IoT Edge.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 68117370e21c7b667bde5447cef510a4e7c77df0
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580760"
---
# <a name="azure-iot-edge-security-module"></a>Modul zabezpečení Azure IoT Edge

> [!IMPORTANT]
> ASC pro IoT je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se používat pro produkční worklo§1ads. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Azure IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/) poskytuje výkonné funkce pro správu a provádět obchodní pracovní postupy na hraničních zařízeních.
Klíčovým faktorem, který přehrává IoT Edge v prostředí IoT umožňují především pro útočníky.

ASC pro modul zabezpečení IoT poskytuje komplexní řešení zabezpečení pro IoT Edge zařízení.
ASC pro modul IoT shromažďuje, agreguje a analyzuje data zabezpečení nezpracovaná z operačního systému a systém kontejnerů do užitečná doporučení zabezpečení a upozornění.

Podobně jako ASC pro agenty zabezpečení IoT pro zařízení IoT, ASC pro modul IoT Edge je vysoce přizpůsobitelné prostřednictvím jeho dvojčete modulu.
Zobrazit [nakonfigurujte agenta](how-to-agent-configuration.md) Další informace.

ASC pro IoT zabezpečení modul IoT Edge nabízí následující funkce:

- Shromažďuje události nezpracovaná zabezpečení od základního operačního systému (Linux) a systémy IoT Edge kontejneru.
  
  V tématu [ASC pro konfiguraci agenta IoT](how-to-agent-configuration.md) získat další informace o kolekce dat zabezpečení k dispozici.

- Analýza manifesty nasazení IoT Edge.

- Agreguje nezpracovaná bezpečnostních událostí do zprávy odeslané přes [centrum IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Odeberte konfiguraci pomocí tohoto dvojčete modulu zabezpečení.

  Zobrazit [konfigurace ASC pro agenta IoT](how-to-agent-configuration.md) Další informace.

ASC pro IoT zabezpečení modul IoT Edge běží ve výhradním režimu v IoT Edge.
Privilegovaném režimu je nutný pro povolení modulu monitorovat operační systém a ostatní moduly IoT Edge.

## <a name="agent-supported-platforms"></a>Agent podporovaný platformy

ASC pro IoT zabezpečení modul IoT Edge je momentálně dostupný jenom pro Linux.

## <a name="next-steps"></a>Další postup

V tomto článku jste se seznámili architekturu a funkce ASC pro IoT zabezpečení modul IoT Edge.

Pokud chcete pokračovat v seznamování s ASC pro nasazení IoT, použijte v následujících článcích:

- Nasazení [zabezpečení modul IoT Edge](how-to-deploy-edge.md)
- Zjistěte, jak [nakonfigurovat modul zabezpečení](how-to-agent-configuration.md)
- Přečtěte si ASC pro IoT [požadované součásti služby](service-prerequisites.md)
- Zjistěte, jak [povolit ASC pro služby Azure IoT ve službě IoT Hub](quickstart-onboard-iot-hub.md)
- Další informace o službě z [ASC pro IoT – nejčastější dotazy](resources-frequently-asked-questions.md)