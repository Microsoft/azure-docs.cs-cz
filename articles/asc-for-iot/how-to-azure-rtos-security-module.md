---
title: Konfigurace a přizpůsobení modulu zabezpečení pro Azure RTO
description: Přečtěte si, jak nakonfigurovat a přizpůsobit modul zabezpečení pro Azure RTO.
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
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: eed0422f574e54ff6d7df486b4929850a26418fa
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514824"
---
# <a name="configure-and-customize-security-module-for-azure-rtos-preview"></a>Konfigurace a přizpůsobení modulu zabezpečení pro Azure RTO (Preview)

Pomocí následujícího souboru nakonfigurujte chování zařízení.

## <a name="azure_iot_security_moduleincasc_porth"></a>azure_iot_security_module/Inc/asc_port. h

 Výchozí chování každé konfigurace je k dispozici v následujících tabulkách: 

### <a name="general"></a>Obecné

| Název | Typ | Výchozí | Podrobnosti |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | Řetězec | --- | Jedinečný identifikátor zařízení  |
| ASC_SECURITY_MODULE_PENDING_TIME  | Číslo | 300 | Doba čekání modulu zabezpečení v sekundách Pokud čas překročí změnu stavu na pozastavit. |

#### <a name="collection"></a>Kolekce

| Název | Typ | Výchozí | Podrobnosti |
| - | - | - | - |
| ASC_HIGH_PRIORITY_INTERVAL | Číslo | 10 | Intervaly skupiny s vysokou prioritou v sekundách. |
| ASC_MEDIUM_PRIORITY_INTERVAL | Číslo | 30 | Interval skupiny s prioritou středních kolekcí v sekundách |
| ASC_LOW_PRIORITY_INTERVAL | Číslo | 145 440  | Intervaly skupiny s nízkou prioritou v sekundách. |

#### <a name="collector-network-activity"></a>Aktivita sítě kolektoru

K přizpůsobení konfigurace vaší síťové aktivity kolektoru použijte následující:

| Název | Typ | Výchozí | Podrobnosti |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Logická hodnota | false (nepravda) | Filtrovat `TCP` aktivitu sítě |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Logická hodnota | false (nepravda) | Filtrovat `UDP` události síťové aktivity |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Logická hodnota | false (nepravda) | Filtrovat `ICMP` události síťové aktivity |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Logická hodnota | true | Zaznamenávat pouze příchozí pakety jednosměrového vysílání, pokud je nastaveno na hodnotu false zachytit také všesměrové vysílání a vícesměrové vysílání |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Číslo | 64 | Maximální počet událostí sítě IPv4 pro uložení v paměti |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Číslo | 64  | Maximální počet událostí sítě IPv6 pro uložení v paměti |


## <a name="compile-flags"></a>Příznaky kompilace
Příznaky kompilace umožňují přepsat předdefinované konfigurace.

### <a name="collectors"></a>Kolektory
| Název | Typ | Výchozí | Podrobnosti |
| - | - | - | - |
| collector_heartbeat_enabled | Logická hodnota | ON | Povolit kolektor prezenčních signálů |
| collector_network_activity_enabled | Logická hodnota | ON | Povolit kolektor síťových aktivit |
| collector_system_information_enabled | Logická hodnota | ON | Povolit kolektor systémových informací |

## <a name="supported-security-alerts-and-recommendations"></a>Podporované výstrahy a doporučení zabezpečení

Modul zabezpečení pro Azure RTO podporuje konkrétní výstrahy a doporučení zabezpečení. Nezapomeňte [si projít a přizpůsobit relevantní výstrahy a doporučené hodnoty](concept-rtos-security-alerts-recommendations.md) pro vaši službu.

## <a name="log-analytics-optional"></a>Log Analytics (volitelné)

I když volitelné a nevyžadujíelné, povolení a konfigurace Log Analytics může být užitečné, když chcete dál prozkoumat události a aktivity zařízení. Přečtěte si o tom, jak nastavit a použít [Log Analytics s Azure Security Center pro službu IoT](how-to-security-data-access.md#log-analytics) , abyste se dozvěděli víc. 

## <a name="next-steps"></a>Další kroky

- Kontrola a přizpůsobení modulu zabezpečení pro [výstrahy a doporučení zabezpečení](concept-rtos-security-alerts-recommendations.md) Azure RTO
- V případě potřeby vyhledejte v [modulu zabezpečení rozhraní API služby Azure RTO](azure-rtos-security-module-api.md) .

