---
title: Agenti zabezpečení
description: Začněte s porozuměním, konfigurací, nasazením a používáním Azure Defenderu pro agenty zabezpečení služby IoT na vašich zařízeních IoT.
ms.topic: conceptual
ms.date: 1/24/2021
ms.openlocfilehash: a1d74de9824b9e825f87754da21070d4e7f1ee33
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783485"
---
# <a name="get-started-with-azure-defender-for-iot-device-micro-agents"></a>Začínáme s Azure Defenderem pro zařízení IoT Micro agenty

Defender pro agenty zabezpečení IoT nabízí rozšířené možnosti zabezpečení, jako je monitorování osvědčených postupů konfigurace operačního systému. Využijte řízení ochrany před hrozbami v oblasti zařízení a zabezpečení stav pomocí jediné služby.

Obrana pro agenty zabezpečení IoT zpracovávají nezpracované shromažďování událostí z operačního systému zařízení, agregaci událostí za účelem snížení nákladů a konfiguraci pomocí modulu zařízení s dvojitou funkční konfigurací. Zprávy o zabezpečení se odesílají prostřednictvím IoT Hub do programu Defender pro služby IoT Analytics.

K nasazení a otestování programu Defender pro agenty zabezpečení IoT použijte následující pracovní postup:

1. [Povolte ve svém IoT Hub Defender pro službu IoT](quickstart-onboard-iot-hub.md).

1. Pokud IoT Hub nemá žádná registrovaná zařízení, [Zaregistrujte nové zařízení](../iot-accelerators/iot-accelerators-device-simulation-overview.md).

1. Vytvořte pro svá zařízení [dvojitě DefenderIotMicroAgent modul](quickstart-create-micro-agent-module-twin.md) .

1. Pokud chcete nainstalovat agenta na simulovaném zařízení Azure místo instalace na skutečném zařízení, [vystavte si nový virtuální počítač Azure (VM)](../virtual-machines/linux/quick-create-portal.md) v dostupné zóně.

1. Nasaďte do zařízení IoT nebo nového virtuálního počítače program [Defender pro IoT Security Agent](how-to-deploy-linux-cs.md) .

1. Podle pokynů [trigger_events](https://aka.ms/iot-security-github-trigger-events) spusťte událost směrného plánu operačního systému.

1. V reakci na neúspěšné kontroly základního plánu operačního systému v předchozím kroku ověřte, jestli jsou doporučení pro IoT v programu Defender. Po spuštění skriptu spusťte ověření 30 minut.

## <a name="next-steps"></a>Další kroky

- Konfigurace [řešení](quickstart-configure-your-solution.md)
- [Vytvoření Defenderu – IoT-mikro-agenti](quickstart-create-security-twin.md)
- Konfigurace [vlastních výstrah](quickstart-create-custom-alerts.md)
- [Nasazení agenta zabezpečení](how-to-deploy-agent.md)