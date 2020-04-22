---
title: Principy cen azure iot hub | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – informace o tom, jak měření a ceny fungují s IoT Hubem, včetně odpracovaných příkladů.
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
ms.openlocfilehash: 4c7382f84522333b6aae0d79941aae8f2147a12f
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729146"
---
# <a name="azure-iot-hub-pricing-information"></a>Informace o cenách Azure IoT Hub

[Ceny azure iot hubposkytuje](https://azure.microsoft.com/pricing/details/iot-hub) obecné informace o různých skum a ceny pro Služby IoT Hub. Tento článek obsahuje další podrobnosti o tom, jak se různé funkce IoT Hubu měří jako zprávy pomocí ioT Hubu.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Poplatky za operaci

| Operace | Fakturační údaje | 
| --------- | ------------------- |
| Operace registru identit <br/> (vytvořit, načíst, seznam, aktualizovat, odstranit) | Není nabitá. |
| Zprávy typu zařízení-cloud | Úspěšně odeslané zprávy se účtují v blocích 4 KB při příchozím přenosu dat do ioT hubu. Například zpráva 6 KB je účtována 2 zprávy. |
| Zprávy z cloudu na zařízení | Úspěšně odeslané zprávy jsou účtovány v blocích 4 KB, například zpráva 6 KB je účtována 2 zprávy. |
| Nahrání souborů | Přenos souborů do Azure Storage se neměří službou IoT Hub. Zprávy o zahájení a dokončení přenosu souborů se účtují jako zprávy měřené v přírůstcích po 4 KB. Například přenos souboru 10 MB se účtuje jako dvě zprávy kromě nákladů na úložiště Azure. |
| Přímé metody | Úspěšné požadavky na metodu jsou účtovány v blocích 4 KB a odpovědi jsou účtovány v blocích 4 KB jako další zprávy. Požadavky na odpojená zařízení se účtují jako zprávy v blocích 4 KB. Například metoda s tělo 4 KB, která má za následek odpověď bez těla ze zařízení se účtuje jako dvě zprávy. Metoda s tělesem 6 KB, která má za následek odpověď 1 KB ze zařízení, se účtuje jako dvě zprávy pro požadavek a další zprávu pro odpověď. |
| Přečte dvojče zařízení a modulu | Twin čtení ze zařízení nebo modulu a z back-endu řešení se účtuje jako zprávy v blocích 512 bajtů. Například čtení dvojčete 6 KB se účtuje jako 12 zpráv. |
| Aktualizace dvojčete zařízení a modulu (značky a vlastnosti) | Twin aktualizace ze zařízení nebo modulu a z back-endu řešení se účtují jako zprávy v blocích 512 bajtů. Například čtení dvojčete 6 KB se účtuje jako 12 zpráv. |
| Dotazy na dvojče zařízení a modul | Dotazy jsou účtovány jako zprávy v závislosti na velikosti výsledku v blocích 512 bajtů. |
| Operace úloh <br/> (vytvoření, aktualizace, výpis, odstranění) | Není nabitá. |
| Úlohy na zařízení operace | Operace úloh (například aktualizace dvojčete a metody) se účtují jako obvykle. Například úloha, která má za následek volání metody 1000 s požadavky 1 KB a odpovědi prázdného těla, je účtována 1000 zpráv. |
| Zprávy keep-alive | Při použití protokolů AMQP nebo MQTT se neúčtují zprávy vyměňované za účelem navázání připojení a zprávy vyměňované při vyjednávání. |

> [!NOTE]
> Všechny velikosti jsou vypočítány s ohledem na velikost datové části v bajtů (rámování protokolu je ignorováno). Pro zprávy, které mají vlastnosti a tělo, velikost se vypočítá způsobem, který je nezávislá na protokolu. Další informace naleznete v [tématu Formát zpráv služby IoT Hub](iot-hub-devguide-messages-construct.md).

## <a name="example-1"></a>Příklad #1

Zařízení odesílá jednu zprávu zařízení ke cloudu o 1 KB za minutu do služby IoT Hub, která se pak čte službou Azure Stream Analytics. Back-end řešení vyvolá metodu (s datovou částí 512 bajtů) v zařízení každých 10 minut, aby se spustila konkrétní akce. Zařízení reaguje na metodu s výsledkem 200 bajtů.

Zařízení spotřebovává:

* Jedna zpráva * 60 minut * 24 hodin = 1440 zpráv za den pro zprávy zařízení cloud.
* Dva požadavky plus odpověď * 6krát za hodinu * 24 hodin = 288 zpráv pro metody.

Tento výpočet poskytuje celkem 1728 zpráv za den.

## <a name="example-2"></a>Příklad #2

Zařízení odešle jednu zprávu zařízení 100 KB zařízení cloud každou hodinu. Také aktualizuje dvojče svého zařízení s datovými zatíženími 1 KB každé čtyři hodiny. Back-end řešení, jednou za den, přečte dvojče zařízení 14 KB a aktualizuje jej s 512bajtovými datovými částmi pro změnu konfigurace.

Zařízení spotřebovává:

* 25 (100 KB / 4 KB) zprávy * 24 hodin pro zprávy zařízení cloud.
* Dvě zprávy (1 KB / 0,5 KB) * šestkrát denně pro aktualizace dvojčete zařízení.

Tento výpočet poskytuje celkem 612 zpráv za den.

Back-end řešení spotřebovává 28 zpráv (14 KB / 0,5 KB) ke čtení dvojčete zařízení, plus jednu zprávu aktualizovat, celkem 29 zpráv.

Celkově zařízení a back-end řešení spotřebovávají 641 zpráv denně.
