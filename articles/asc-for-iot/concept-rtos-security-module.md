---
title: Koncepční vysvětlení základních modulů zabezpečení Azure IoT – Azure RTO
description: Seznamte se se základy konceptů modulu zabezpečení Azure RTO IoT a pracovního postupu.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: f4793aa27dfbb037416d6165ddc659bb864a6647
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514828"
---
# <a name="security-module-for-azure-rtos-preview"></a>Modul zabezpečení pro Azure RTO (Preview)

Tento článek vám umožní lépe pochopit modul zabezpečení pro Azure RTO, včetně funkcí a výhod, a odkazy na relevantní konfigurační a referenční prostředky. 

## <a name="azure-rtos-iot-security-module"></a>Modul zabezpečení Azure RTO IoT

Modul zabezpečení pro Azure RTO poskytuje komplexní řešení zabezpečení pro zařízení Azure RTO v rámci nabídky NetX Duo. V rámci nabídky NetX Duo Azure RTO dodává s integrovaným modulem zabezpečení Azure IoT a po aktivaci poskytuje pokrytí pro běžné hrozby na zařízeních s operačním systémem v reálném čase. 

Modul zabezpečení pro Azure RTO běží na pozadí a poskytuje bezproblémové uživatelské prostředí při posílání zpráv o zabezpečení pomocí jedinečných připojení jednotlivých zákazníků k jejich IoT Hub. Modul zabezpečení pro Azure RTO je ve výchozím nastavení povolený.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTO NetX Duo je Pokročilá sada protokolů TCP/IP v průmyslové úrovni navržená speciálně pro bohaté aplikace v reálném čase i v aplikacích IoT. Azure RTO NetX Duo je duální síťový zásobník IPv4 a IPv6 poskytující bohatou sadu protokolů, včetně zabezpečení a cloudu. Přečtěte si další informace o řešeních [Azure RTO NetX Duo](https://aka.ms/netxduo) .

Modul nabízí následující funkce:

- **Zjištění škodlivých síťových aktivit**
- **Směrné plány chování zařízení na základě vlastních výstrah**
- **Zlepšení hygieny zabezpečení zařízení**

## <a name="security-module-for-azure-rtos-architecture"></a>Modul zabezpečení pro architekturu Azure RTO

Modul zabezpečení pro Azure RTO je inicializován platformou middleware Azure IoT a používá IoT Hub klienty k posílání telemetrie zabezpečení do centra.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Diagram stavu modulu zabezpečení Azure IoT a tok informací":::

Modul zabezpečení pro Azure RTO sleduje následující aktivitu a informace o zařízení pomocí tří sběračů:
- Síťová aktivita zařízení **TCP**, **UDP**a **ICM**
- Systémové informace jako **Threadx** a **NetX verze Duo**
- Události prezenčního signálu

Každá kolekce je propojená se skupinou priorit a každá skupina priorit má svůj vlastní interval s možnými hodnotami **Nízká**, **střední**a **Vysoká**. Intervaly ovlivňují časový interval, ve kterém se data shromažďují a odesílají.

Každý časový interval se dá nakonfigurovat a konektory IoT můžou být povolené a zakázané, aby bylo možné ještě víc [přizpůsobit vaše řešení](how-to-azure-rtos-security-module.md). 

## <a name="supported-security-alerts-and-recommendations"></a>Podporované výstrahy a doporučení zabezpečení

Modul zabezpečení pro Azure RTO podporuje konkrétní výstrahy a doporučení zabezpečení. Po dokončení počáteční konfigurace nezapomeňte [zkontrolovat a přizpůsobit relevantní výstrahy a hodnoty doporučení](concept-rtos-security-alerts-recommendations.md) pro vaši službu.

## <a name="ready-to-begin"></a>Jste připraveni začít?

Modul zabezpečení pro Azure RTO je k dispozici jako bezplatné stažení pro vaše zařízení IoT. Cloudová služba Azure Security Center for IoT je k dispozici s 30denní zkušební verzí na předplatné Azure. [Stáhněte si modul zabezpečení hned](https://github.com/azure-rtos/azure-iot-preview/releases) a pojďme začít. 

## <a name="next-steps"></a>Další kroky

- Začínáme s modulem zabezpečení pro [požadavky a nastavení](quickstart-azure-rtos-security-module.md)Azure RTO
- Další informace o modulu zabezpečení pro [výstrahy zabezpečení Azure RTO a doporučení pro podporu](concept-rtos-security-alerts-recommendations.md). 
- Použijte modul zabezpečení pro [referenční rozhraní API](azure-rtos-security-module-api.md)služby Azure RTO.

