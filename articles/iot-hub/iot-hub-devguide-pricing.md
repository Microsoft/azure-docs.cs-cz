---
title: Lepší pochopení cen Azure IoT Hub | Dokumentace Microsoftu
description: Příručka pro vývojáře – informace o jak měření a cen funguje službou IoT Hub, včetně šlo příklady.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: 9b6db1b7171652ea5ace4db370b72dc22b6bdc90
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626226"
---
# <a name="azure-iot-hub-pricing-information"></a>Informace o cenách služby Azure IoT Hub

[Ceny centra IoT Azure](https://azure.microsoft.com/pricing/details/iot-hub) obsahuje obecné informace o různých SKU a cenách pro službu IoT Hub. Tento článek obsahuje další podrobnosti o tom, jak se měří různým funkcím služby IoT Hub jako zprávy ve službě IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Poplatky za operace

| Operace | Informace o fakturaci | 
| --------- | ------------------- |
| Operace registru identit <br/> (vytvoření, načtení, výpis, aktualizace nebo odstranění) | Není placený. |
| Zprávy typu zařízení-cloud | Úspěšně odeslané zprávy se účtují po 4 KB na příchozí přenos dat do služby IoT Hub. Například zpráva 6 KB se účtuje 2 zprávy. |
| Zprávy typu cloud zařízení | Úspěšně odeslané zprávy se účtují po 4 KB, třeba zprávu 6 KB se účtuje 2 zprávy. |
| Nahrání souborů | Přenos souborů do služby Azure Storage se měří ve službě IoT Hub. Soubor přenos zahájení a ukončení zprávy se účtují podle messaged Účtovaná podle objemu dat v přírůstcích po 4 KB. Třeba přenesení 10 MB soubor se účtuje jako dvě zprávy navíc náklady na úložiště Azure. |
| Přímé metody | Úspěšná metoda žádosti se účtují po 4 KB, a odpovědi se účtují po 4 KB jako další zprávy. Požadavky na odpojená zařízení se účtují jako zprávy v 4 KB blocích. Například metody s tělem 4 KB, jehož výsledkem odpověď se žádný text ze zařízení se účtuje jako dvě zprávy. Metody s tělem 6 KB, jehož výsledkem 1 KB odpověď od zařízení, se účtuje jako dvě zprávy požadavku a další zprávu pro odpověď. |
| Čtení dvojčat zařízení a modul | Čtení dvojčat zařízení nebo modulu a z řešení zpět end se účtují jako zpráv do bloků velikosti 512 bajtů. Například čtení 6-KB dvojčete se účtuje jako 12 zprávy. |
| Dvojče zařízení a modul aktualizací (značek a vlastnosti) | Aktualizace dvojčat zařízení nebo modulu a z back-end řešení se účtují jako zpráv do bloků velikosti 512 bajtů. Například čtení 6-KB dvojčete se účtuje jako 12 zprávy. |
| Dotazů na dvojčata zařízení a modul | Dotazy se účtují jako zprávy v závislosti na velikosti výsledek v blocích o velikosti 512 bajtů. |
| Operace úloh <br/> (vytvoření, aktualizace, výpis, odstranění) | Není placený. |
| Operace úloh podle zařízení | Operace úloh (jako například aktualizace dvojčat a metody) se účtuje jako obvykle. Úlohy, což vede k volání metody 1000 žádostí 1 KB a prázdné tělo odpovědi je třeba za 1 000 zpráv. |
| Zprávy keep-alive | Při použití přes protokol AMQP nebo protokol MQTT, neúčtují se vyměňují k navázání připojení a při vyjednávání, které si vyměňují zprávy. |

> [!NOTE]
> Všechny velikosti se vypočítávají vzhledem k tomu, velikost datové části v bajtech (protokol rámce se ignoruje). Pro zprávy, které mají vlastnosti a text, velikost je vypočítán způsobem bez ohledu na protokol. Další informace najdete v tématu [formát zprávy služby IoT Hub](iot-hub-devguide-messages-construct.md).

## <a name="example-1"></a>Příklad #1

Zařízení odesílá do služby IoT Hub, který je pak přečte Azure Stream Analytics jednu zprávu typu zařízení cloud 1 KB za minutu. Back-end řešení volá metodu (s datovou částí 512 bajtů) na zařízení každých 10 minut pro aktivaci určité akce. Zařízení jsou reaguje na metodu s výsledkem 200 bajtů.

Zařízení využívá:

* Jedna zpráva * 60 minut * 24 hodin = 1 440 zpráv za den pro zprávy typu zařízení cloud.
* Dvě žádosti a odpovědi * 6krát za hodinu * 24 hodin = 288 zprávy pro metody.

Tento výpočet dává. 1728 zpráv za den.

## <a name="example-2"></a>Příklad #2

Zařízení odesílá zprávy typu zařízení cloud jeden 100 KB každou hodinu. Také jeho dvojče zařízení se aktualizuje datových částí 1 KB každé čtyři hodiny. Řešení zpět ukončit, jednou za den, přečte dvojčeti zařízení 14 KB a aktualizace s datovými částmi 512 bajtů, chcete-li změnit konfigurace.

Zařízení využívá:

* 25 zprávy (100 KB a 4 KB) * 24 hodin pro zprávy typu zařízení cloud.
* Dvě zprávy (1 KB/0,5 KB) * šestkrát za den pro aktualizace dvojčete zařízení.

Tento výpočet poskytuje 612 zpráv za den.

Back-end řešení využívá pro 29 zpráv 28 zprávy (14 KB/0,5 KB) ke čtení dvojčat zařízení plus jedna zpráva, ji aktualizovat.

Celkem zařízení a back-end řešení využívat 641 zpráv denně.
