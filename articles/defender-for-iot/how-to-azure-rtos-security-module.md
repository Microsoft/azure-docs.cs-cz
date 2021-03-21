---
title: Konfigurace a přizpůsobení programu Defender – IoT-Micro-Agent pro Azure RTO
description: Přečtěte si, jak nakonfigurovat a přizpůsobit program Defender – IoT-Micro-Agent pro Azure RTO.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2021
ms.author: shhazam
ms.openlocfilehash: 874a783763882a28f2fe7078e3a264d09107808a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103495092"
---
# <a name="configure-and-customize-defender-iot-micro-agent-for-azure-rtos-preview"></a>Konfigurace a přizpůsobení programu Defender-IoT-Micro-Agent pro Azure RTO (Preview)

Tento článek popisuje, jak nakonfigurovat program Defender-IoT-Micro-Agent pro zařízení Azure RTO, aby splňoval požadavky na síť, šířku pásma a paměť.

Z adresáře musíte vybrat cílový distribuční soubor s `*.dist` příponou `netxduo/addons/azure_iot/azure_iot_security_module/configs` .  

Při použití prostředí kompilace CMake musíte pro zvolenou hodnotu nastavit parametr příkazového řádku na `IOT_SECURITY_MODULE_DIST_TARGET` . Například, `-DIOT_SECURITY_MODULE_DIST_TARGET=RTOS_BASE`.

V IAR nebo jiném prostředí kompilace bez CMake musíte přidat `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/` cestu k jakýmkoli známým zahrnutým cestám. Například, `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/RTOS_BASE`.

Pomocí následujícího souboru nakonfigurujte chování zařízení.

**netxduo/doplňky/azure_iot/azure_iot_security_module/Inc/CONFIGS/ \<target distribution> /asc_config. h**

V prostředí kompilace CMake musíte změnit výchozí konfiguraci úpravou `netxduo/addons/azure_iot/azure_iot_security_module/configs/<target distribution>.dist` souboru. Použijte následující formát CMake `set(ASC_XXX ON)` nebo následující soubor `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/asc_config.h` pro všechna ostatní prostředí. Například, `#define ASC_XXX`.

Výchozí chování každé konfigurace je k dispozici v následujících tabulkách: 

## <a name="general"></a>Obecné

| Název | Typ | Výchozí | Podrobnosti |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | Řetězec | Defender – IoT-Micro Agent | Jedinečný identifikátor zařízení  |
| SECURITY_MODULE_VERSION_ (HLAVNÍ) (DÍLČÍ) (OPRAVA)  | Číslo | 3.2.1 | Verze |
| ASC_SECURITY_MODULE_SEND_MESSAGE_RETRY_TIME  | Číslo  | 3 | Doba, po kterou program Defender-IoT-Micro-agent odešle zprávu o zabezpečení po selhání. (v sekundách) |
| ASC_SECURITY_MODULE_PENDING_TIME  | Číslo | 300 | Doba čekání v programu Defender-IoT-Micro Agent (v sekundách). Stav se změní na pozastavit, pokud dojde k překročení časového limitu.. |

## <a name="collection"></a>Kolekce

| Název | Typ | Výchozí | Podrobnosti |
| - | - | - | - |
| ASC_FIRST_COLLECTION_INTERVAL | Číslo  | 30  | Posunutí intervalu spouštění kolekce. Během spouštění se hodnota přidá do kolekce systému, aby se předešlo posílání zpráv z více zařízení současně.  |
| ASC_HIGH_PRIORITY_INTERVAL | Číslo | 10 | Interval skupiny s vysokou prioritou kolektoru (v sekundách). |
| ASC_MEDIUM_PRIORITY_INTERVAL | Číslo | 30 | Interval skupiny středních priorit kolektoru (v sekundách). |
| ASC_LOW_PRIORITY_INTERVAL | Číslo | 145 440  | Interval skupiny s nízkou prioritou kolektoru (v sekundách). |

#### <a name="collector-network-activity"></a>Aktivita sítě kolektoru

K přizpůsobení konfigurace vaší síťové aktivity kolektoru použijte následující:

| Název | Typ | Výchozí | Podrobnosti |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Logická hodnota | false (nepravda) | Vyfiltruje `TCP` aktivitu sítě. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Logická hodnota | false (nepravda) | Vyfiltruje `UDP` události síťové aktivity. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Logická hodnota | false (nepravda) | Vyfiltruje `ICMP` události síťové aktivity. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Logická hodnota | true | Zachytí pouze příchozí pakety jednosměrového vysílání. Když se nastaví na false, zachytí taky všesměrové vysílání i vícesměrové vysílání. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_SEND_EMPTY_EVENTS  | Logická hodnota  | false (nepravda)  | Odesílá prázdné události sběrače. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Číslo | 64 | Maximální počet událostí sítě IPv4, které mají být uloženy v paměti. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Číslo | 64  | Maximální počet událostí sítě IPv6 pro uložení v paměti. |

### <a name="collectors"></a>Kolektory
| Název | Typ | Výchozí | Podrobnosti |
| - | - | - | - |
| ASC_COLLECTOR_HEARTBEAT_ENABLED | Logická hodnota | ON | Povolí kolektor prezenčních signálů. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ENABLED  | Logická hodnota | ON | Povolí shromažďování síťových aktivit. |
| ASC_COLLECTOR_SYSTEM_INFORMATION_ENABLED | Logická hodnota | ON | Povolí kolektor systémových informací.  |

Další příznaky konfigurace jsou rozšířené a mají nepodporované funkce. Pokud chcete tuto změnu změnit, obraťte se na podporu, nebo další informace.
 
## <a name="supported-security-alerts-and-recommendations"></a>Podporované výstrahy a doporučení zabezpečení

Defender-IoT-Micro-Agent pro Azure RTO podporuje konkrétní výstrahy a doporučení zabezpečení. Nezapomeňte [si projít a přizpůsobit relevantní výstrahy a doporučené hodnoty](concept-rtos-security-alerts-recommendations.md) pro vaši službu.

## <a name="log-analytics-optional"></a>Log Analytics (volitelné)

Můžete povolit a nakonfigurovat Log Analytics pro zkoumání událostí a aktivit zařízení. Přečtěte si o tom, jak nastavit, a použijte [Log Analytics se službou Defender for IoT](how-to-security-data-access.md#log-analytics) , abyste se dozvěděli víc. 

## <a name="next-steps"></a>Další kroky


- Kontrola a přizpůsobení programu Defender – IoT-Micro-Agent pro Azure RTO [výstrahy a doporučení zabezpečení](concept-rtos-security-alerts-recommendations.md)
- V případě potřeby najdete informace v tématu [Defender-IoT-Micro-Agent pro Azure RTO API](azure-rtos-security-module-api.md) .
