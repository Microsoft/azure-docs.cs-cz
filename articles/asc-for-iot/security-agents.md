---
title: Začínáme používat Azure Security Center pro agenty zabezpečení IoT| Dokumenty společnosti Microsoft
description: Už začínáte s porozuměním, konfigurací, nasazením a používáním agentů služby zabezpečení Azure Security Center for IoT na vašich zařízeních IoT.
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
ms.openlocfilehash: 1ed890d9d3602de24e6f85f6f0ae7f59849f3df2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74664179"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>Začínáme s agenty Azure Security Center pro zabezpečení ioT zařízení

Agenti zabezpečení Azure Security Center pro IoT nabízejí rozšířené možnosti zabezpečení, jako je monitorování vzdálených připojení, aktivních aplikací, událostí přihlášení a doporučených postupů konfigurace operačního systému. Převezměte kontrolu nad ochranou před hrozeb v poli zařízení a stavem zabezpečení pomocí jediné služby. 

Referenční architektura pro linuxové a windows bezpečnostní agenty, jak v C# a C jsou k dispozici.

Azure Security Center pro agenty zabezpečení IoT zpracovávat shromažďování nezpracovaných událostí z operačního systému zařízení, agregace událostí ke snížení nákladů a konfigurace prostřednictvím dvojčete modulu zařízení. Zprávy zabezpečení se posílají prostřednictvím služby IoT Hub do služby Azure Security Center for IoT analytics.

Pomocí následujícího pracovního postupu nasazujte a otestujte agenty zabezpečení Azure Security Center pro IoT: 

1. [Povolení služby Azure Security Center for IoT do služby IoT Hub](quickstart-onboard-iot-hub.md)
1. Pokud vaše IoT Hub nemá žádná registrovaná zařízení, [zaregistrujte nové zařízení](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. Vytvořte pro svá zařízení [bezpečnostní modul azureiotsecurity.](quickstart-create-security-twin.md)
1. Pokud chcete agenta nainstalovat na simulované zařízení Azure namísto instalace na skutečné zařízení, [snižte nový virtuální počítač Azure (VM)](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) v dostupné zóně. 
1. Nasaďte na zařízení IoT nebo nový virtuální počítač [agenta Azure Security Center pro IoT.](how-to-deploy-linux-cs.md)
1. Postupujte podle pokynů pro [trigger_events](https://aka.ms/iot-security-github-trigger-events) spustit neškodnou simulaci útoku.
1. Ověřte výstrahy Azure Security Center for IoT v reakci na simulovaný útok v předchozím kroku. Začněte s ověřením pět minut po spuštění skriptu.
1. Prozkoumejte [výstrahy](concept-security-alerts.md), [doporučení](concept-recommendations.md)a [podrobné informace pomocí log analytics](how-to-security-data-access.md) pomocí služby IoT Hub. 


## <a name="next-steps"></a>Další kroky

- Konfigurace [řešení](quickstart-configure-your-solution.md)
- [Vytvoření modulů zabezpečení](quickstart-create-security-twin.md)
- Konfigurace [vlastních výstrah](quickstart-create-custom-alerts.md)
- [Nasazení agenta zabezpečení](how-to-deploy-agent.md)
