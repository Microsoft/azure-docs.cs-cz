---
title: Přizpůsobitelné výstrahy zabezpečení pro Azure Security Center pro IoT| Dokumenty společnosti Microsoft
description: Přečtěte si o přizpůsobitelných výstrahách zabezpečení a doporučených nápravách pomocí azure security center pro funkce a služby IoT.
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
ms.date: 03/04/2020
ms.author: mlottner
ms.openlocfilehash: 32d79267559480de8ec91e5e66196bfd57e01cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299569"
---
# <a name="azure-security-center-for-iot-security-alerts"></a>Výstrahy zabezpečení ve službě Azure Security Center pro IoT

Azure Security Center for IoT průběžně analyzuje vaše řešení IoT pomocí pokročilých analýz a analýzy hrozeb, které vás upozorní na škodlivou aktivitu.

Doporučujeme vám vytvořit vlastní výstrahy na základě vašich znalostí očekávaného chování zařízení, abyste zajistili, že výstrahy budou fungovat jako nejúčinnější ukazatele potenciálního ohrožení ve vašem jedinečném nasazení a prostředí organizace. 

Následující seznam výstrah Azure Security Center pro IoT jsou definovatelné na základě očekávaného ioT hubu nebo chování zařízení. Další podrobnosti o přizpůsobení jednotlivých výstrah naleznete v [tématu vytvoření vlastních výstrah](quickstart-create-custom-alerts.md).

## <a name="iot-hub-alerts-available-for-customization"></a>Výstrahy služby IoT Hub, které jsou k dispozici pro vlastní nastavení 



| Severity | Název výstrahy | Zdroj dat | Popis | Navrhovaná náprava|
|---|---|---|---|---|
| Nízká      | Vlastní výstraha – počet zpráv z cloudu na zařízení v protokolu AMQP je mimo povolený rozsah          | IoT Hub     | Počet zpráv cloud na zařízení (protokol AMQP) v určitém časovém okně je mimo aktuálně nakonfigurovaný a povolený rozsah.||
| Nízká      | Vlastní výstraha - počet odmítnutých zpráv cloudu zařízení v protokolu AMQP je mimo povolený rozsah | IoT Hub     | Počet zpráv cloud na zařízení (protokol AMQP) odmítnutých zařízením, v určitém časovém okně je mimo aktuálně nakonfigurovaný a povolený rozsah.||
| Nízká      | Vlastní výstraha – počet zpráv od zařízení ke cloudu v protokolu AMQP je mimo povolený rozsah      | IoT Hub     | Množství zpráv zařízení do cloudu (protokol AMQP) v určitém časovém okně je mimo aktuálně nakonfigurovaný a povolený rozsah.|   |
| Nízká      | Vlastní výstraha - počet přímých metod vyvolá je mimo povolený rozsah | IoT Hub     | Množství přímé metody vyvolá v určitém časovém okně je mimo aktuálně nakonfigurovaný a povolený rozsah.||
| Nízká      | Vlastní výstraha - počet nahraných souborů je mimo povolený rozsah | IoT Hub     | Množství odeslání souborů v určitém časovém okně je mimo aktuálně nakonfigurovaný a povolený rozsah.| |
| Nízká      | Vlastní výstraha – počet zpráv z cloudu na zařízení v protokolu HTTP je mimo povolený rozsah | IoT Hub     | Množství zpráv z cloudu na zařízení (protokol HTTP) v časovém okně není v nakonfigurovaném povoleném rozsahu                                  |
| Nízká      | Vlastní výstraha - počet odmítnutých zpráv cloudu na zařízení v protokolu HTTP není v povoleném rozsahu | IoT Hub     | Množství zpráv cloud na zařízení (protokol HTTP) v určitém časovém okně je mimo aktuálně nakonfigurovaný a povolený rozsah. |
| Nízká      | Vlastní výstraha – počet zpráv zařízení ke cloudu v protokolu HTTP je mimo povolený rozsah | IoT Hub| Množství zpráv zařízení do cloudu (protokol HTTP) v určitém časovém okně je mimo aktuálně nakonfigurovaný a povolený rozsah.|    |
| Nízká      | Vlastní výstraha – počet zpráv z cloudu na zařízení v protokolu MQTT je mimo povolený rozsah | IoT Hub     | Množství zpráv cloud na zařízení (protokol MQTT) v určitém časovém okně je mimo aktuálně nakonfigurovaný a povolený rozsah.|   |
| Nízká      | Vlastní výstraha – počet odmítnutých zpráv z cloudu na zařízení v protokolu MQTT je mimo povolený rozsah | IoT Hub     | Množství zpráv cloud na zařízení (protokol MQTT) odmítnuté zařízením v určitém časovém okně je mimo aktuálně nakonfigurovaný a povolený rozsah. |
| Nízká      | Vlastní výstraha – počet zpráv od zařízení ke cloudu v protokolu MQTT je mimo povolený rozsah          | IoT Hub     | Množství zpráv zařízení do cloudu (protokol MQTT) v určitém časovém okně je mimo aktuálně nakonfigurovaný a povolený rozsah.|
| Nízká      | Vlastní výstraha - počet vymazání fronty příkazů je mimo povolený rozsah                               | IoT Hub     | Množství vymazání fronty příkazů v určitém časovém okně je mimo aktuálně nakonfigurovaný a povolený rozsah.||
| Nízká      | Vlastní výstraha - počet aktualizací dvojčete modulu je mimo povolený rozsah                                       | IoT Hub     | Množství aktualizací dvojčete modulu v určitém časovém okně je mimo aktuálně nakonfigurovaný a povolený rozsah.|
| Nízká      | Vlastní výstraha - počet neoprávněných operací je mimo povolený rozsah  | IoT Hub     | Množství neoprávněných operací v určitém časovém okně je mimo aktuálně nakonfigurovaný a povolený rozsah.|
|

## <a name="agent-alerts-available-for-customization"></a>Výstrahy agentů, které jsou k dispozici pro vlastní nastavení 

| Severity | Název výstrahy | Zdroj dat | Popis | Navrhovaná náprava|
|---|---|---|---|---|
| Nízká      | Vlastní výstraha - počet aktivních připojení je mimo povolený rozsah  | Agent       | Počet aktivních připojení v určitém časovém okně je mimo aktuálně nakonfigurovaný a povolený rozsah.|  Prozkoumejte protokoly zařízení. Zjistěte, odkud připojení pochází, a zjistěte, zda je neškodné nebo škodlivé. Pokud je škodlivý, odstraňte možný malware a pochopte zdroj. Pokud je neškodný, přidejte zdroj do seznamu povolených připojení.  |
| Nízká      | Vlastní výstraha – odchozí připojení vytvořené do IP adresy, která není povolena                             | Agent       | Odchozí připojení bylo vytvořeno s ip adresou, která je mimo povolený seznam IP adres. |Prozkoumejte protokoly zařízení. Zjistěte, odkud připojení pochází, a zjistěte, zda je neškodné nebo škodlivé. Pokud je škodlivý, odstraňte možný malware a pochopte zdroj. Pokud je neškodný, přidejte zdroj do seznamu povolených ip adres.                        |
| Nízká      | Vlastní výstraha - počet neúspěšných místních přihlášení je mimo povolený rozsah                               | Agent       | Množství neúspěšných místních přihlášení v určitém časovém okně je mimo aktuálně nakonfigurovaný a povolený rozsah. |   |
| Nízká      | Vlastní výstraha - přihlášení uživatele, který není v seznamu povolených uživatelů | Agent       | Místní uživatel mimo povolený seznam uživatelů, přihlášen k zařízení.|  Pokud ukládáte nezpracovaná data, přejděte na účet analýzy protokolů a použijte data k prozkoumání zařízení, identifikaci zdroje a následné opravě seznamu povolených a blokovaných položek pro tato nastavení. Pokud aktuálně neukládáte nezpracovaná data, přejděte do zařízení a opravte seznam povolených a blokovaných položek pro tato nastavení.|
| Nízká      | Vlastní výstraha - byl proveden proces, který není povolen | Agent       | Proces, který není povolen, byl v zařízení proveden. |Pokud ukládáte nezpracovaná data, přejděte na účet analýzy protokolů a použijte data k prozkoumání zařízení, identifikaci zdroje a následné opravě seznamu povolených a blokovaných položek pro tato nastavení. Pokud aktuálně neukládáte nezpracovaná data, přejděte do zařízení a opravte seznam povolených a blokovaných položek pro tato nastavení.  |
|

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [přizpůsobit výstrahu](quickstart-create-custom-alerts.md)
- [Přehled](overview.md) služby Azure Security Center for IoT
- Přečtěte si, jak získat přístup k [bezpečnostním datům](how-to-security-data-access.md)
- Další informace o [zkoumání zařízení](how-to-investigate-device.md)