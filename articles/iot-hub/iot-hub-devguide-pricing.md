---
title: Vysvětlení ceny služby Azure IoT Hub | Microsoft Docs
description: Příručka pro vývojáře – informace o tom, jak měření a ceny fungují s IoT Hub včetně fungovalých příkladů.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 5b67f505a71d2af7950422a86ab45ef8aebb3bf4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056432"
---
# <a name="azure-iot-hub-pricing-information"></a>Informace o cenách Azure IoT Hub

[Ceny služby Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub) poskytují obecné informace o různých SKU a cenách pro IoT Hub. Tento článek obsahuje další podrobnosti o tom, jak se různé funkce IoT Hub měří jako zprávy pomocí IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Poplatky za operaci

| Operace | Fakturační údaje | 
| --------- | ------------------- |
| Operace registru identit <br/> (vytvořit, načíst, vypsat, aktualizovat, odstranit) | Neúčtují se. |
| Zprávy typu zařízení-cloud | Zprávy se úspěšně poslaly ve 4 KB blocích při příchozím přenosu dat do IoT Hub. Například zpráva o 6 – KB se účtuje 2 zprávy. |
| Zprávy z cloudu na zařízení | Úspěšné odeslání zpráv se účtuje v blocích po 4 KB, například zpráva o 6-KB se účtuje 2 zprávy. |
| Nahrání souborů | Přenos souborů do Azure Storage není IoT Hub. Zprávy o zahájení a dokončení přenosu souborů se účtují jako měřené v přírůstcích po 4 KB. Například přenos souboru o velikosti 10 MB se účtuje jako dvě zprávy kromě Azure Storagech nákladů. |
| Přímé metody | Úspěšné požadavky na metodu se účtují v blocích po 4 KB a odpovědi se účtují jako další zprávy v blocích po 4 KB. Požadavky na odpojená zařízení se účtují jako zprávy v blocích po 4 KB. Například metoda se základním textem 4 KB, který má za následek odpověď bez těla ze zařízení, se účtuje jako dvě zprávy. Metoda s textem 6 – KB, který má za následek odpověď 1 KB ze zařízení, bude účtována jako dvě zprávy pro požadavek a další zpráva pro odpověď. |
| Dvojitá čtení zařízení a modulu | Dvojitá čtení ze zařízení nebo modulu a z back-endu řešení se účtují jako zprávy v blocích po 4 KB. Například načtením vlákna 8-KB se účtuje jako 2 zprávy. |
| Nes dvojitými aktualizacemi zařízení a modulů (značky a vlastnosti) | S dvojitou aktualizací ze zařízení nebo modulu a z back-endu řešení se účtují jako zprávy v blocích po 4 KB. Například čtení typu vlákna 12 KB se účtuje jako 3 zprávy. |
| Nedotazované dotazy na zařízení a moduly | Dotazy se účtují jako zprávy v závislosti na velikosti výsledku v blocích po 4 KB. |
| Operace úloh <br/> (vytvoření, aktualizace, výpis, odstranění) | Neúčtují se. |
| Operace s úlohami na zařízení | Operace s úlohami (jako jsou například zdvojené aktualizace a metody) se účtují jako normální. Například úloha s výsledkem volání metody 1000 s požadavky 1 – KB a odpověďmi v prázdném těle se účtuje 1000 zpráv. |
| Zprávy Keep-Alive | Při použití protokolů AMQP nebo MQTT se neúčtují zprávy vyměňované k navázání připojení a zprávy vyměňované v vyjednávání. |

> [!NOTE]
> Vypočítávají se všechny velikosti s ohledem na velikost datové části v bajtech (rámce protokolu se ignorují). U zpráv, které mají vlastnosti a text, se velikost počítá v nezávislá protokolu. Další informace najdete v tématu [IoT Hub formátu zprávy](iot-hub-devguide-messages-construct.md).

## <a name="example-1"></a>Příklad #1

Zařízení pošle do IoT Hub zprávu typu zařízení-Cloud 1 1-KB za minutu, kterou pak přečte Azure Stream Analytics. Back-end řešení vyvolá metodu (s 512 bajtovou částí) na zařízení každých 10 minut, aby aktivoval určitou akci. Zařízení odpoví na metodu s výsledkem 200 bajtů.

Zařízení spotřebovává:

* Jedna zpráva × 60 minut × 24 hodin = 1440 zpráv za den pro zprávy ze zařízení do cloudu.
* Dvě žádosti a odpověď * 6 časů za hodinu × 24 hodin = 288 zpráv pro metody.

Tento výpočet poskytuje celkem 1728 zpráv denně.

## <a name="example-2"></a>Příklad #2

Každé hodiny zařízení pošle zprávu typu zařízení-Cloud 1 100 – KB. Také aktualizuje své zařízení s využitím datových částí o 1 KB každé čtyři hodiny. Back-end řešení, jednou za den, přečte zařízení o velikosti 14 MB a aktualizuje ho pomocí 512 bajtových částí pro změnu konfigurací.

Zařízení spotřebovává:

* 25 (100 KB/4 KB) zpráv × 24 hodin pro zprávy ze zařízení do cloudu.
* Dvě zprávy (1 KB/0,5 KB) * 6 minut denně pro aktualizace s dvojitým počtem zařízení.

Tento výpočet poskytuje celkem 612 zpráv denně.

Back-end řešení spotřebovává 28 zpráv (14 KB/0,5 KB) a přečte zařízení a jednu zprávu, která ho aktualizuje, a to za celkem 29 zpráv.

V celku bude zařízení a back-end řešení spotřebovat 641 zpráv denně.
