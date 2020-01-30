---
title: Průvodce přizpůsobitelnou výstrahou zabezpečení pro Azure Security Center pro IoT | Microsoft Docs
description: Přečtěte si o přizpůsobitelných výstrahách zabezpečení a doporučené nápravě pomocí Azure Security Center pro funkce a služby IoT.
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
ms.date: 1/27/2020
ms.author: mlottner
ms.openlocfilehash: f10604d23901c2d7ed23f8b4f32a296f063d8620
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776271"
---
# <a name="azure-security-center-for-iot-security-alerts"></a>Azure Security Center pro výstrahy zabezpečení IoT

Azure Security Center pro IoT průběžně analyzuje vaše řešení IoT pomocí pokročilých analýz a analýzy hrozeb, které vás upozorní na škodlivou aktivitu.

Doporučujeme, abyste vytvořili vlastní výstrahy na základě vašich znalostí očekávaného chování zařízení a zajistili, že výstrahy budou fungovat jako nejúčinnější indikátory potenciálního ohrožení v rámci jedinečného nasazení organizace a na šířku. 

Následující seznam Azure Security Center pro výstrahy IoT můžete definovat podle toho, jak očekáváte IoT Hub a/nebo chování zařízení. Další podrobnosti o tom, jak přizpůsobit jednotlivé výstrahy, najdete v tématu [Vytvoření vlastních výstrah](quickstart-create-custom-alerts.md).

## <a name="azure-security-center-for-iot-alerts-available-for-customization"></a>Azure Security Center pro výstrahy IoT dostupné pro přizpůsobení 



| Závažnost | Název výstrahy | Zdroj dat | Popis | Navrhovaná náprava|
|---|---|---|---|---|
| Nízký      | Vlastní upozornění – počet zpráv v cloudu na zařízení v protokolu AMQP je mimo povolený rozsah.          | Centrum IoT     | Počet zpráv z cloudu na zařízení (AMQP Protocol) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah.||
| Nízký      | Vlastní upozornění – počet odmítnutých zpráv v cloudu na zařízení v protokolu AMQP je mimo povolený rozsah. | Centrum IoT     | Počet zpráv z cloudu na zařízení (Protokol AMQP) zamítnutý zařízením v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah.||
| Nízký      | Vlastní upozornění – počet zpráv ze zařízení na Cloud v protokolu AMQP je mimo povolený rozsah.      | Centrum IoT     | Množství zpráv ze zařízení do cloudu (AMQP Protocol) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah.|   |
| Nízký      | Vlastní výstraha – počet volání přímé metody je mimo povolený rozsah. | Centrum IoT     | Množství přímé metody vyvolání v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah.||
| Nízký      | Vlastní výstraha – počet nahrávání souborů je mimo povolený rozsah. | Centrum IoT     | Velikost nahrávání souborů v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah.| |
| Nízký      | Vlastní upozornění – počet zpráv v cloudu na zařízení v protokolu HTTP je mimo povolený rozsah. | Centrum IoT     | Množství zpráv z cloudu na zařízení (protokol HTTP) v časovém okně není v nakonfigurovaném povoleném rozsahu.                                  |
| Nízký      | Vlastní výstraha – počet odmítnutých zpráv v cloudu na zařízení v protokolu HTTP není v povoleném rozsahu. | Centrum IoT     | Množství zpráv z cloudu na zařízení (protokol HTTP) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |
| Nízký      | Vlastní upozornění – počet zpráv ze zařízení na Cloud v protokolu HTTP je mimo povolený rozsah. | Centrum IoT| Velikost zařízení pro cloudové zprávy (protokol HTTP) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah.|    |
| Nízký      | Vlastní upozornění – počet zpráv v cloudu na zařízení v protokolu MQTT je mimo povolený rozsah. | Centrum IoT     | Množství zpráv z cloudu na zařízení (MQTT Protocol) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah.|   |
| Nízký      | Vlastní upozornění – počet odmítnutých zpráv v cloudu na zařízení v protokolu MQTT je mimo povolený rozsah. | Centrum IoT     | Množství zpráv z cloudu na zařízení (protokol MQTT) odmítnuté zařízením v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |
| Nízký      | Vlastní upozornění – počet zpráv ze zařízení na Cloud v protokolu MQTT je mimo povolený rozsah.          | Centrum IoT     | Množství zpráv ze zařízení do cloudu (MQTT Protocol) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah.|
| Nízký      | Vlastní upozornění – počet vyprázdnění fronty příkazů je mimo povolený rozsah.                               | Centrum IoT     | Velikost fronty příkazů v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah.||
| Nízký      | Vlastní výstraha – počet nevlákenných aktualizací modulu je mimo povolený rozsah.                                       | Centrum IoT     | Množství nespuštěných aktualizací v rámci určitého časového okna je mimo aktuálně nakonfigurovaný a povolený rozsah.|
| Nízký      | Vlastní výstraha – počet neautorizovaných operací je mimo povolený rozsah.  | Centrum IoT     | Množství neautorizovaných operací v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah.|
| Nízký      | Vlastní upozornění – počet aktivních připojení je mimo povolený rozsah.  | Agent       | Počet aktivních připojení v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah.|  Prozkoumejte protokoly zařízení. Zjistěte, kde připojení pochází, a zjistěte, jestli je neškodný nebo škodlivý. Pokud máte škodlivou, odstraňte možný malware a porozumět zdroji. Pokud je neškodný, přidejte zdroj do seznamu povolených připojení.  |
| Nízký      | Vlastní výstraha – odchozí připojení vytvořené na IP adresu, která není povolená                             | Agent       | Odchozí připojení bylo vytvořeno na IP adresu, která je mimo povolený seznam IP adres. |Prozkoumejte protokoly zařízení. Zjistěte, kde připojení pochází, a zjistěte, jestli je neškodný nebo škodlivý. Pokud máte škodlivou, odstraňte možný malware a porozumět zdroji. Pokud je neškodný, přidejte zdroj do seznamu povolených IP adres.                        |
| Nízký      | Vlastní upozornění – počet neúspěšných místních přihlášení je mimo povolený rozsah.                               | Agent       | Množství neúspěšných místních přihlášení v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |   |
| Nízký      | Vlastní výstraha – přihlášení uživatele, který není v seznamu povolených uživatelů | Agent       | Místní uživatel mimo seznam povolených uživatelů, přihlášený k zařízení.|  Pokud ukládáte nezpracovaná data, přejděte k účtu Log Analytics a pomocí dat Prozkoumejte zařízení, identifikujte zdroj a pak pro tato nastavení opravte seznam povolených/blokovaných dat. Pokud v současné době neukládáte nezpracovaná data, přečtěte si zařízení a opravte seznam povolených a blokovaných dat pro tato nastavení.|
| Nízký      | Vlastní výstraha – proces byl spuštěn, což není povoleno. | Agent       | V zařízení se spustil proces, který není povolený. |Pokud ukládáte nezpracovaná data, přejděte k účtu Log Analytics a pomocí dat Prozkoumejte zařízení, identifikujte zdroj a pak pro tato nastavení opravte seznam povolených/blokovaných dat. Pokud v současné době neukládáte nezpracovaná data, přečtěte si zařízení a opravte seznam povolených a blokovaných dat pro tato nastavení.  |
|


## <a name="next-steps"></a>Další kroky

- Informace o tom, jak [přizpůsobit upozornění](quickstart-create-custom-alerts.md)
- [Přehled](overview.md) služby Azure Security Center for IoT
- Informace o [přístupu k datům zabezpečení](how-to-security-data-access.md)
- Další informace o [prověřování zařízení](how-to-investigate-device.md)