---
title: Přehled agenta zabezpečení
description: Začněte s porozuměním, konfigurací, nasazením a používáním Azure Defenderu pro agenty zabezpečení služby IoT na vašich zařízeních IoT.
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
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 888fdbb2352c4ac972eac8b24c29d22ce973e04d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936367"
---
# <a name="get-started-with-azure-defender-for-iot-device-security-agents"></a>Začínáme se službou Azure Defender pro agenty zabezpečení zařízení IoT

Defender pro agenty zabezpečení IoT nabízí rozšířené možnosti zabezpečení, jako je monitorování vzdálených připojení, aktivních aplikací, přihlašovacích událostí a osvědčených postupů konfigurace operačního systému. Využijte řízení ochrany před hrozbami v oblasti zařízení a zabezpečení stav pomocí jediné služby.

K dispozici je referenční architektura pro agenty zabezpečení systému Linux a Windows v jazyce C# i C.

Agenti zabezpečení služby IoT pro IoT zpracovávají nezpracované shromažďování událostí z operačního systému zařízení, agregace událostí pro snížení nákladů a konfiguraci pomocí modulu zařízení s dvojitou funkční konfigurací. Zprávy o zabezpečení se odesílají prostřednictvím IoT Hub do programu Defender pro služby IoT Analytics.

K nasazení a otestování programu Defender pro agenty zabezpečení IoT použijte následující pracovní postup:

1. [Povolit Defender pro službu IoT na IoT Hub](quickstart-onboard-iot-hub.md)
1. Pokud IoT Hub nemá žádná registrovaná zařízení, [Zaregistrujte nové zařízení](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Vytvořte modul zabezpečení azureiotsecurity](quickstart-create-security-twin.md) pro vaše zařízení.
1. Pokud chcete nainstalovat agenta na simulovaném zařízení Azure místo instalace na skutečném zařízení, [vystavte si nový virtuální počítač Azure (VM)](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) v dostupné zóně.
1. Nasaďte do svého zařízení IoT nebo nového virtuálního počítače [agenta zabezpečení pro IoT for IoT](how-to-deploy-linux-cs.md) .
1. Postupujte podle pokynů [trigger_events](https://aka.ms/iot-security-github-trigger-events) a spusťte simulaci neškodných útoků.
1. Ověřte Defender pro výstrahy IoT v reakci na simulovaný útok v předchozím kroku. Po spuštění skriptu spusťte ověření 5 minut.
1. Prozkoumejte [výstrahy](concept-security-alerts.md), [doporučení](concept-recommendations.md)a [hloubkové podrobně pomocí Log Analytics](how-to-security-data-access.md) s využitím IoT Hub.

## <a name="next-steps"></a>Další kroky

- Konfigurace [řešení](quickstart-configure-your-solution.md)
- [Vytváření modulů zabezpečení](quickstart-create-security-twin.md)
- Konfigurace [vlastních výstrah](quickstart-create-custom-alerts.md)
- [Nasazení agenta zabezpečení](how-to-deploy-agent.md)
