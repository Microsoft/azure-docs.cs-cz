---
title: Přehled agenta zabezpečení
description: Začněte s porozuměním, konfigurací, nasazením a používáním Azure Security Center pro agenty služby zabezpečení IoT na zařízeních IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 0d4d21db5185e4564666e526b3edb5ca6d3451e0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81310651"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>Začínáme s Azure Security Center pro agenty zabezpečení zařízení IoT

Azure Security Center pro agenty zabezpečení IoT nabízí rozšířené možnosti zabezpečení, jako je monitorování vzdálených připojení, aktivních aplikací, přihlašovacích událostí a osvědčených postupů konfigurace operačního systému. Využijte řízení ochrany před hrozbami v oblasti zařízení a zabezpečení stav pomocí jediné služby.

K dispozici je referenční architektura pro agenty zabezpečení systému Linux a Windows v jazyce C# i C.

Azure Security Center pro agenty zabezpečení IoT zpracovávají nezpracované shromažďování událostí z operačního systému zařízení, agregaci událostí za účelem snížení nákladů a konfiguraci pomocí modulu zařízení s dvojitou funkční konfigurací. Zprávy o zabezpečení se odesílají prostřednictvím IoT Hub do Azure Security Center pro služby IoT Analytics.

Pomocí následujícího pracovního postupu nasaďte a otestujte Azure Security Center pro agenty zabezpečení IoT:

1. [Povolení Azure Security Center pro službu IoT pro IoT Hub](quickstart-onboard-iot-hub.md)
1. Pokud IoT Hub nemá žádná registrovaná zařízení, [Zaregistrujte nové zařízení](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Vytvořte modul zabezpečení azureiotsecurity](quickstart-create-security-twin.md) pro vaše zařízení.
1. Pokud chcete nainstalovat agenta na simulovaném zařízení Azure místo instalace na skutečném zařízení, [vystavte si nový virtuální počítač Azure (VM)](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) v dostupné zóně.
1. [Nasaďte Azure Security Center pro agenta zabezpečení IoT](how-to-deploy-linux-cs.md) do svého zařízení IoT nebo do nového virtuálního počítače.
1. Postupujte podle pokynů [trigger_events](https://aka.ms/iot-security-github-trigger-events) a spusťte simulaci neškodných útoků.
1. Ověřte Azure Security Center výstrah IoT v reakci na simulovaný útok v předchozím kroku. Po spuštění skriptu spusťte ověření 5 minut.
1. Prozkoumejte [výstrahy](concept-security-alerts.md), [doporučení](concept-recommendations.md)a [hloubkové podrobně pomocí Log Analytics](how-to-security-data-access.md) s využitím IoT Hub.

## <a name="next-steps"></a>Další kroky

- Konfigurace [řešení](quickstart-configure-your-solution.md)
- [Vytváření modulů zabezpečení](quickstart-create-security-twin.md)
- Konfigurace [vlastních výstrah](quickstart-create-custom-alerts.md)
- [Nasazení agenta zabezpečení](how-to-deploy-agent.md)
