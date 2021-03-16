---
title: Defender – IoT-Micro-Agent pro Azure RTO integrovaná & přizpůsobitelné výstrahy a doporučení
description: Přečtěte si o výstrahách zabezpečení a doporučené nápravě pomocí Azure IoT Defenderu IoT-Micro-agent-RTO.
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
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: d6dfeb01e174d71c926e9fcf7f5f1ee078524cf9
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103494270"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-security-alerts-and-recommendations-preview"></a>Defender – IoT-Micro-Agent pro Azure RTO – výstrahy a doporučení zabezpečení (Preview)

Defender – IoT-Micro-Agent pro Azure RTO průběžně analyzuje vaše řešení IoT pomocí pokročilých analýz a analýzy hrozeb, které vás upozorní na potenciální škodlivou aktivitu a podezřelé změny systému. Můžete také vytvořit vlastní výstrahy na základě znalostí očekávaného chování zařízení a směrných plánů.

Defender-IoT-Micro-Agent pro Azure RTO Alert funguje jako ukazatel potenciálního ohrožení a měl by se prozkoumat a opravit. V programu Defender-IoT-Micro-Agent pro Azure RTO se dá opravit slabá stav zabezpečení, která se má opravit a aktualizovat. 

V tomto článku najdete seznam integrovaných výstrah a doporučení, která se spouštějí na základě výchozích rozsahů, a přizpůsobit se vlastními hodnotami na základě očekávaného nebo základního chování. 

Další informace o tom, jak přizpůsobení výstrah funguje ve službě Defender pro službu IoT, najdete v tématu [přizpůsobitelná upozornění](concept-customizable-security-alerts.md). Konkrétní výstrahy a doporučení, která jsou k dispozici pro přizpůsobení při používání programu Defender-IoT-Micro-Agent pro Azure RTO, jsou podrobně popsány v následujících tabulkách. 

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-security-alerts"></a>Defender – IoT-Micro-Agent pro Azure RTO – podporované výstrahy zabezpečení

### <a name="device-related-security-alerts"></a>Výstrahy zabezpečení související se zařízením

|Aktivita výstrah zabezpečení související s zařízením  |Název upozornění  |
|---------|---------|
|IP adresa| Byla zjištěna komunikace s podezřelými IP adresami.|
|Kryptografický otisk certifikátu zařízení X. 509|Neshoda kryptografických otisků certifikátů zařízení X. 509|
|Certifikát X. 509| Platnost certifikátu X. 509 vypršela.|
|Token SAS| Token SAS s vypršenou platností|
|Token SAS| Neplatný podpis tokenu SAS|

### <a name="iot-hub-related-security-alerts"></a>Výstrahy zabezpečení související s IoT Hubm

|Aktivita výstrahy zabezpečení IoT Hub  |Název upozornění  |
|---------|---------|
|Přidat certifikát    |  Byl zjištěn neúspěšný pokus o přidání certifikátu do IoT Hub       |
|Přidání nebo úprava nastavení diagnostiky    | Byl zjištěn pokus o přidání nebo úpravu nastavení diagnostiky IoT Hub      |
|Odstranit certifikát    |  Byl zjištěn neúspěšný pokus o odstranění certifikátu z IoT Hub.       |
|Odstraní nastavení diagnostiky.    |  Byl zjištěn pokus o odstranění nastavení diagnostiky z IoT Hub.      |
|Odstraněný certifikát    | Bylo zjištěno odstranění certifikátu z IoT Hub.        |
|Nový certifikát     |  Bylo zjištěno přidání nového certifikátu do IoT Hub.       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-customizable-alerts"></a>Defender – IoT-Micro-Agent pro Azure RTO podporuje přizpůsobitelná upozornění

### <a name="device-related-customizable-alerts"></a>Přizpůsobitelné výstrahy související se zařízením

|Aktivita související se zařízením |Název upozornění  |
|---------|---------|
|Aktivní připojení|Počet aktivních připojení není v povoleném rozsahu.|
|Zprávy z cloudu do zařízení v protokolu **MQTT**|Počet zpráv v cloudu na zařízení v protokolu **MQTT** není v povoleném rozsahu.|
|Odchozí připojení| Odchozí připojení k IP adrese, která není povolená|

### <a name="hub-related-customizable-alerts"></a>Přizpůsobitelné výstrahy týkající se centra 

|Aktivita související s centrem  |Název upozornění  |
|---------|---------|
|Vyprázdnění fronty příkazů     |  Počet vyprázdnění fronty příkazů mimo povolený rozsah       |
|Zprávy z cloudu do zařízení v protokolu **MQTT**    |  Počet zpráv z cloudu na zařízení v protokolu **MQTT** mimo povolený rozsah       |
|Zprávy ze zařízení do cloudu v protokolu **MQTT**    | Počet zpráv ze zařízení do cloudu v protokolu **MQTT** mimo povolený rozsah        |
|Přímá volání metody     |  Počet volání přímé metody mimo povolený rozsah       |
|Odmítnuté zprávy z cloudu do zařízení v **MQTT** protokolu     |   Počet odmítnutých zpráv cloudu na zařízení v protokolu **MQTT** mimo povolený rozsah      |
|Aktualizace dvojitých modulů     |  Počet aktualizací pro dvojitou moduly mimo povolený rozsah       |
|Neautorizované operace    |  Počet neautorizovaných operací mimo povolený rozsah       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-recommendations"></a>Defender – IoT-Micro-Agent pro Azure RTO – podporovaná doporučení

### <a name="device-related-recommendations"></a>Doporučení týkající se zařízení

|Aktivita související se zařízením  |Název doporučení |
|---------|---------|
|Přihlašovací údaje pro ověřování    |  Totožná pověření ověřování používaná více zařízeními       |

### <a name="hub-related-recommendations"></a>Doporučení týkající se centra

|Aktivita související s IoT Hub  |Název doporučení |
|---------|---------|
|Zásady filtru IP   |  Výchozí zásada filtru IP musí být nastavená na **Odepřít** .  |
|Pravidlo filtru IP| Pravidlo filtru IP obsahuje velký rozsah IP adres.|
|Protokoly diagnostiky|Návrh pro povolení diagnostických protokolů v IoT Hub|

### <a name="all-defender-for-iot-alerts-and-recommendations"></a>All Defender pro výstrahy a doporučení IoT

Úplný seznam všech výstrah a doporučení týkajících se služby IoT najdete v tématu [výstrahy zabezpečení](concept-security-alerts.md)IoT, [doporučení](concept-recommendations.md)pro zabezpečení IoT.

## <a name="next-steps"></a>Další kroky

- [Rychlý Start: Defender-IoT-Micro Agent pro Azure RTO](quickstart-azure-rtos-security-module.md)
- [Konfigurace a přizpůsobení programu Defender – IoT-Micro-Agent pro Azure RTO](how-to-azure-rtos-security-module.md)
- Informace najdete v tématu [Defender-IoT-Micro-Agent pro Azure RTO API](azure-rtos-security-module-api.md) .