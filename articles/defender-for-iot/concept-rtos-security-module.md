---
title: Koncepční vysvětlení základů programu Defender-IoT-Micro-Agent pro Azure RTO
description: Seznamte se se základními informacemi o konceptech a pracovních postupech v programu Defender-IoT-Micro-Agent pro Azure RTO.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 04b86d401bcb9fc919c36b28cf4f80ea3bfd7030
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750477"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-preview"></a>Defender – IoT-Micro-Agent pro Azure RTO (Preview)

Tento článek vám umožní lépe porozumět programu Defender-IoT-Micro-Agent pro Azure RTO, včetně funkcí a výhod, a odkazy na relevantní konfigurační a referenční prostředky. 

## <a name="azure-rtos-iot-defender-iot-micro-agent"></a>Azure RTO IoT Defender – IoT-Micro-Agent

Defender-IoT-Micro-Agent pro Azure RTO poskytuje komplexní řešení zabezpečení pro zařízení Azure RTO v rámci nabídky NetX Duo. V rámci nabídky NetX Duo Azure RTO dodává s předdefinovaným Azure IoT Defenderu IoT-Micro-agent a po aktivaci nabízí pokrytí běžných hrozeb na zařízeních s operačním systémem v reálném čase.

Defender-IoT-Micro-Agent pro Azure RTO běží na pozadí a poskytuje bezproblémové uživatelské prostředí při posílání zpráv o zabezpečení pomocí jedinečných připojení jednotlivých zákazníků k jejich IoT Hub. Defender-IoT-Micro-Agent pro Azure RTO je ve výchozím nastavení povolený.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTO NetX Duo je Pokročilá sada protokolů TCP/IP v průmyslové úrovni navržená speciálně pro bohaté aplikace v reálném čase i v aplikacích IoT. Azure RTO NetX Duo je duální síťový zásobník IPv4 a IPv6 poskytující bohatou sadu protokolů, včetně zabezpečení a cloudu. Přečtěte si další informace o řešeních [Azure RTO NetX Duo](/azure/rtos/netx-duo/) .

Modul nabízí následující funkce:

- **Zjištění škodlivých síťových aktivit**
- **Směrné plány chování zařízení na základě vlastních výstrah**
- **Zlepšení hygieny zabezpečení zařízení**

## <a name="defender-iot-micro-agent-for-azure-rtos-architecture"></a>Defender – IoT-Micro-Agent pro Azure RTO Architecture

Defender-IoT-Micro-Agent pro Azure RTO je inicializován platformou middleware Azure IoT a používá IoT Hub klienty k posílání telemetrie zabezpečení do centra.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Azure IoT Defender – IoT-mikro-agent Stavový diagram a tok informací":::

Defender-IoT-Micro-Agent pro Azure RTO sleduje následující aktivitu a informace o zařízení pomocí tří sběračů:
- Síťová aktivita zařízení **TCP**, **UDP** a **ICM**
- Systémové informace jako **Threadx** a **NetX verze Duo**
- Události prezenčního signálu

Každá kolekce je propojená se skupinou priorit a každá skupina priorit má svůj vlastní interval s možnými hodnotami **Nízká**, **střední** a **Vysoká**. Intervaly ovlivňují časový interval, ve kterém se data shromažďují a odesílají.

Každý časový interval se dá nakonfigurovat a konektory IoT můžou být povolené a zakázané, aby bylo možné ještě víc [přizpůsobit vaše řešení](how-to-azure-rtos-security-module.md). 

## <a name="supported-security-alerts-and-recommendations"></a>Podporované výstrahy a doporučení zabezpečení

Defender-IoT-Micro-Agent pro Azure RTO podporuje konkrétní výstrahy a doporučení zabezpečení. Po dokončení počáteční konfigurace nezapomeňte [zkontrolovat a přizpůsobit relevantní výstrahy a hodnoty doporučení](concept-rtos-security-alerts-recommendations.md) pro vaši službu.

## <a name="ready-to-begin"></a>Jste připraveni začít?

Defender – IoT-Micro-Agent pro Azure RTO je k dispozici jako bezplatné stažení pro vaše zařízení IoT. Cloudová služba Defender for IoT je dostupná s 30denní zkušební verzí na předplatné Azure. [Stáhněte si Defender-IoT-Micro-agent hned](https://github.com/azure-rtos/azure-iot-preview/releases) a pojďme začít. 

## <a name="next-steps"></a>Další kroky

- Začínáme s programem Defender – IoT-Micro-Agent pro Azure RTO [požadavky a nastavení](quickstart-azure-rtos-security-module.md).
- Další informace o programu Defender – IoT-Micro-Agent pro Azure RTO [výstrahy zabezpečení a doporučení pro podporu](concept-rtos-security-alerts-recommendations.md). 
- Použijte [referenční rozhraní API](azure-rtos-security-module-api.md)pro Defender-IoT-Micro-Agent pro Azure RTO.