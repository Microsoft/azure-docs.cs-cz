---
title: Možnosti nasazení
description: Seznamte se s principy základního pracovního postupu Defenderu pro funkce a službu IoT.
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
ms.openlocfilehash: 6aa525fd7f2d82194baa2e2a0c910cb71509c2d5
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340012"
---
# <a name="getting-started-with-azure-defender-for-iot"></a>Začínáme s Azure Defenderem pro IoT

Tento článek popisuje procesy nasazení a zprovoznění, které jsou nezbytné k získání programu Azure Defender pro službu IoT. Vyžadují se taky další kroky. Doporučujeme, abyste tyto kroky pochopili a seznámili se s informacemi v doprovodných dokumentech.

Po dokončení všech kroků bude Azure Defender pro IoT Monitors monitorovat vaši síť. V závislosti na tom, jak vaše řešení nastavili, se detekce dají také odeslat do místní konzoly pro správu nebo do IoT Hub.

Provedením následujících kroků Získejte Azure Defender pro Azure v provozu a spusťte službu IoT.

## <a name="1-set-up-azure"></a>1. nastavení Azure

- Nastavte účet Azure. Další informace najdete v tématu [Vytvoření účtu Azure](/learn/modules/create-an-azure-account/).

- Brána firewall nebo proxy server: Pokud máte bránu firewall nebo podobná síťová zařízení, která je nakonfigurovaná tak, aby umožňovala specifická připojení, ověřte, že je na bráně firewall nebo proxy server otevřený buď znak *. azure-devices.net:443. Pokud se zástupné znaky nepodporují nebo chcete mít větší kontrolu, měl by se v rámci nástroje FW nebo proxy serveru otevřít konkrétní plně kvalifikovaný název domény IoT Hub. Další informace najdete v tématu [referenční koncové body IoT Hub](../iot-hub/iot-hub-devguide-endpoints.md).

## <a name="2-deploy-hardware-software-and-onboard-to-sensor"></a>2. nasazení hardwaru, softwaru a zprovoznění na senzor

- Zakupte hardware snímače a nainstalujte software. Postupujte podle kroků uvedených tady a další informace najdete v tomto článku a v [příručce pro hardware Defender for IoT](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) a v [příručce pro instalaci](https://aka.ms/AzureDefenderforIoTInstallSensorISO).

  - Po instalaci senzoru bezpečně zaznamenejte přihlašovací údaje pro přihlášení snímače. K nahrání aktivačního souboru na senzor budete potřebovat přihlašovací údaje.

  - Pokud pracujete se senzory, které jsou místně spravované, bezpečně zaznamenejte IP adresu snímače nebo název snímače definovaný v instalaci. Můžete ho chtít použít při vytváření názvu snímače během registrace senzoru v programu Defender pro IoT Portal. Později je můžete použít k zajištění jednoduššího sledování a konzistentního pojmenování mezi názvem registrace v Azure Defenderu pro IoT Portal a IP adresou nasazeného senzoru zobrazeného v konzole senzorů.

- Zaregistrujte senzor pomocí portálu Defender pro IoT a Stáhněte si soubor aktivace senzoru.

- Nahrajte aktivační soubor na senzor.

## <a name="3-perform-network-setup-for-sensor-monitoring-and-management"></a>3. proveďte nastavení sítě pro monitorování a správu senzorů

- Připojte senzor k síti. Popsané v [Průvodci nastavením sítě](https://aka.ms/AzureDefenderForIoTNetworkSetup).

## <a name="4-start-discovering-your-network"></a>4. zahájení zjišťování sítě

- Nástroj pro vylepšení nastavení systému v konzole senzorů.

- Připojte senzory k místní konzole pro správu.

Další informace najdete v tématu [uživatelská příručka k Azure Defenderu pro IoT snímače](https://aka.ms/AzureDefenderforIoTUserGuide) a [v příručce pro uživatele pro místní konzolu pro správu IoT](https://aka.ms/DefenderForIoTManagementConsole).

## <a name="5-populate-azure-sentinel-with-alert-information"></a>5. naplnit službu Azure Sentinel informacemi o výstrahách

- Pokud chcete odesílat informace o výstrahách do Azure Sentinel, nakonfigurujte konfiguraci Azure Sentinel: [Připojte svá data z Defenderu pro IoT do Azure Sentinel](how-to-configure-with-sentinel.md).
 

## <a name="next-steps"></a>Další kroky

- Povolit [Defender pro IoT](quickstart-onboard-iot-hub.md)
- Konfigurace [řešení](quickstart-configure-your-solution.md)
- [Vytváření modulů zabezpečení](quickstart-create-security-twin.md)
- Konfigurace [vlastních výstrah](quickstart-create-custom-alerts.md)
- [Nasazení agenta zabezpečení](how-to-deploy-agent.md)