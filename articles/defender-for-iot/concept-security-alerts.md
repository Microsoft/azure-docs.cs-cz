---
title: Předdefinovaný & vlastní seznam výstrah
description: Přečtěte si o výstrahách zabezpečení a doporučené nápravě pomocí programu Defender pro funkce a službu IoT Hub.
ms.topic: conceptual
ms.date: 2/16/2021
ms.openlocfilehash: 73b3a1ca3e52f571ab7b531235650b6bda870691
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784556"
---
# <a name="defender-for-iot-hub-security-alerts"></a>Defender pro IoT Hub výstrahy zabezpečení

Defender pro IoT průběžně analyzuje vaše řešení IoT pomocí pokročilých analýz a analýzy hrozeb, které vás upozorní na škodlivou aktivitu.
Kromě toho můžete vytvářet vlastní výstrahy na základě znalostí očekávaného chování zařízení.
Výstraha funguje jako ukazatel potenciálního ohrožení a měla by být prověřena a opravena.

V tomto článku najdete seznam integrovaných výstrah, které se můžou aktivovat na vašich IoT Hub.
Kromě integrovaných výstrah vám Defender pro IoT umožňuje definovat vlastní výstrahy na základě očekávaného IoT Hub nebo chování zařízení.
Další informace najdete v tématu [přizpůsobitelná upozornění](concept-customizable-security-alerts.md).

## <a name="built-in-alerts-for-iot-hub"></a>Integrované výstrahy pro IoT Hub

| Závažnost | Název | Description | Navrhovaná náprava |
|--|--|--|--|
| **Střední** závažnost |  |  |  |
| Nový certifikát se přidal do IoT Hub. | Střední | \' \' Do IoT Hub \' % {DescIoTHubName} se přidal certifikát s názvem% {DescCertificateName} \' . Pokud tato akce byla provedena neoprávněnou stranou, může to znamenat škodlivou aktivitu. | 1. Ujistěte se, že certifikát přidala oprávněná strana. <br> 2. Pokud ho nepřidala autorizovaný účastník, odeberte ho a upozorněte ho do organizačního týmu zabezpečení. |
| Certifikát se odstranil z IoT Hub. | Střední | Certifikát s názvem \' % {DescCertificateName} \' se odstranil z IoT Hub \' % {DescIoTHubName} \' . Pokud tato akce byla provedena neoprávněnou stranou, může to znamenat škodlivou aktivitu. | 1. Ujistěte se, že certifikát odebrala oprávněná strana. <br> 2. Pokud se certifikát neodebral autorizovaným účastníkem, přidejte certifikát zpátky a upozorněte ho do organizačního týmu zabezpečení. |
| Zjistil se neúspěšný pokus o přidání certifikátu do IoT Hub. | Střední | Došlo k neúspěšnému pokusu o přidání certifikátu \' % {DescCertificateName} \' do IoT Hub \' % {DescIoTHubName} \' . Pokud tato akce byla provedena neoprávněnou stranou, může to znamenat škodlivou aktivitu. | Ujistěte se, že oprávnění ke změně certifikátů jsou udělována pouze oprávněným stranám. |
| Zjistil se neúspěšný pokus o odstranění certifikátu z IoT Hub. | Střední | Došlo k neúspěšnému pokusu o odstranění certifikátu \' % {DescCertificateName} \' z IoT Hub \' % {DescIoTHubName} \' . Pokud tato akce byla provedena neoprávněnou stranou, může to znamenat škodlivou aktivitu. | Ujistěte se, že oprávnění ke změně certifikátů jsou udělována pouze oprávněným stranám. |
| Neshoda kryptografických otisků certifikátů zařízení x. 509 | Střední | kryptografický otisk certifikátu zařízení x. 509 se neshodoval s konfigurací. | Zkontrolujte výstrahy na zařízeních. Není vyžadována žádná další akce. |
| platnost certifikátu x. 509 vypršela. | Střední | Platnost certifikátu zařízení X. 509 vypršela. | Může se jednat o legitimní zařízení s certifikátem, jehož platnost vypršela, nebo se pokusí o zosobnění legitimního zařízení. Pokud legitimní zařízení aktuálně komunikuje správně, je pravděpodobný pokus o zosobnění. |
| **Nízká** závažnost |  |  |  |
| Pokus o přidání nebo úpravu nastavení diagnostiky zjištěného IoT Hub | Nízká | Byl zjištěn pokus o přidání nebo úpravu nastavení diagnostiky IoT Hub. Nastavení diagnostiky umožňují znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení zabezpečení vaší sítě. Pokud tuto akci neudělala autorizovaný účastník, může to znamenat škodlivou aktivitu. | 1. Ujistěte se, že certifikát odebrala oprávněná strana.<br> 2. Pokud se certifikát neodebral autorizovaným účastníkem, přidejte certifikát zpátky a upozorněte ho do svého týmu zabezpečení informací. |
| Došlo k pokusu o odstranění nastavení diagnostiky ze zjištěného IoT Hub. | Nízká | Byl \' proveden pokus o přidání nebo úpravu nastavení diagnostiky \' % {DescDiagnosticSettingName} \' IoT Hub \' % {DescIoTHubName} \' ,% {DescAttemptStatusMessage}. Nastavení diagnostiky umožňuje znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení zabezpečení vaší sítě. Pokud tuto akci neudělala autorizovaný účastník, může to znamenat škodlivou aktivitu. | Ujistěte se, že oprávnění ke změně nastavení diagnostiky jsou udělována pouze oprávněným stranám. |
| Token SAS s vypršenou platností | Nízká | Vypršení platnosti tokenu SAS používaného zařízením | Může to být legitimní zařízení s tokenem, jehož platnost vypršela, nebo se pokouší zosobnit legitimní zařízení. Pokud legitimní zařízení aktuálně komunikuje správně, je to nejspíš pokus o zosobnění. |
| Neplatný podpis tokenu SAS | Nízká | Token SAS používaný zařízením má neplatný podpis. Signatura nesouhlasí s primárním nebo sekundárním klíčem. | Zkontrolujte výstrahy na zařízeních. Není vyžadována žádná další akce. |

## <a name="next-steps"></a>Další kroky

- Defender pro službu IoT [– Přehled](overview.md)
- Informace o [přístupu k datům zabezpečení](how-to-security-data-access.md)
- Další informace o [prověřování zařízení](how-to-investigate-device.md)
