---
title: Vlastní výstrahy zabezpečení založené na agentech
description: Přečtěte si o přizpůsobitelných výstrahách zabezpečení a doporučené nápravě pomocí programu Defender pro funkce a služby zařízení IoT.
ms.topic: conceptual
ms.date: 2/16/2021
ms.openlocfilehash: 2fb1385c12cbd9d0479d8528f54aad8816393ad1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784913"
---
# <a name="defender-for-iot-devices-custom-security-alerts"></a>Defender pro vlastní výstrahy zabezpečení pro zařízení IoT

Defender pro IoT průběžně analyzuje vaše řešení IoT pomocí pokročilých analýz a analýzy hrozeb, které vás upozorní na škodlivou aktivitu.

Doporučujeme, abyste vytvořili vlastní výstrahy na základě vašich znalostí očekávaného chování zařízení a zajistili, že výstrahy budou fungovat jako nejúčinnější indikátory potenciálního ohrožení v rámci jedinečného nasazení organizace a na šířku.

Následující seznamy programu Defender pro výstrahy IoT můžete definovat podle očekávaného chování zařízení IoT. Další informace o tom, jak přizpůsobit jednotlivé výstrahy, najdete v tématu [Vytvoření vlastních výstrah](quickstart-create-custom-alerts.md).

## <a name="agent-based-security-custom-alerts"></a>Vlastní výstrahy zabezpečení založené na agentech

| Závažnost | Název upozornění | Zdroj dat | Description | Navrhovaná náprava |
|--|--|--|--|--|
| Nízká | Vlastní výstraha – počet aktivních připojení je mimo povolený rozsah. | Klasický Defender – IoT-Micro-agent, Azure RTO | Počet aktivních připojení v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. | Prozkoumejte protokoly zařízení. Zjistěte, kde připojení pochází, a zjistěte, jestli je neškodný nebo škodlivý. Pokud máte škodlivou, odstraňte možný malware a porozumět zdroji. Pokud je neškodný, přidejte zdroj do seznamu povolených připojení. |
| Nízká | Vlastní výstraha – odchozí připojení vytvořené na IP adresu není povolené. | Klasický Defender – IoT-Micro-agent, Azure RTO | Odchozí připojení bylo vytvořeno na IP adresu, která je mimo povolený seznam IP adres. | Prozkoumejte protokoly zařízení. Zjistěte, kde připojení pochází, a zjistěte, jestli je neškodný nebo škodlivý. Pokud máte škodlivou, odstraňte možný malware a porozumět zdroji. Pokud je neškodný, přidejte zdroj do seznamu povolených IP adres. |
| Nízká | Vlastní výstraha – počet neúspěšných místních přihlášení je mimo povolený rozsah. | Klasický Defender – IoT-Micro-agent, Azure RTO | Počet neúspěšných místních přihlášení v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |  |
| Nízká | Vlastní výstraha – přihlášení uživatele, který není v seznamu povolených uživatelů | Klasický Defender – IoT-Micro-agent, Azure RTO | Místní uživatel mimo seznam povolených uživatelů, přihlášený k zařízení. | Pokud ukládáte nezpracovaná data, přejděte k účtu Log Analytics a pomocí dat Prozkoumejte zařízení, identifikujte zdroj a pak pro tato nastavení opravte seznam povolených/blokovaných dat. Pokud v současné době neukládáte nezpracovaná data, přečtěte si zařízení a opravte seznam povolených a blokovaných dat pro tato nastavení. |
| Nízká | Vlastní výstraha – proces byl spuštěn, což není povoleno. | Klasický Defender – IoT-Micro-agent, Azure RTO | V zařízení se spustil proces, který není povolený. | Pokud ukládáte nezpracovaná data, přejděte k účtu Log Analytics a pomocí dat Prozkoumejte zařízení, identifikujte zdroj a pak pro tato nastavení opravte seznam povolených/blokovaných dat. Pokud v současné době neukládáte nezpracovaná data, přečtěte si zařízení a opravte seznam povolených a blokovaných dat pro tato nastavení. |
|

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak [přizpůsobit upozornění](quickstart-create-custom-alerts.md)
- Defender pro službu IoT [– Přehled](overview.md)
- Informace o [přístupu k datům zabezpečení](how-to-security-data-access.md)
- Další informace o [prověřování zařízení](how-to-investigate-device.md)
