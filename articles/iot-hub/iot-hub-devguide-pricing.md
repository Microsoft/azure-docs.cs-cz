---
title: Pochopení ceny služby Azure IoT Hub | Microsoft Docs
description: Příručka vývojáře - informace o jak měření a ceny funguje službou IoT Hub, včetně šlo příklady.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 1ac90923-1edf-4134-bbd4-77fee9b68d24
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 9b0d2df078c59c7d261fd3231450ddfb2fdcd88e
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="azure-iot-hub-pricing-information"></a>Informace o cenách Azure IoT Hub

[Azure IoT Hub ceny] [ lnk-pricing] poskytuje obecné informace o různých SKU a ceny pro centra IoT. Tento článek obsahuje další podrobnosti o tom, jak jsou různé funkce IoT Hub měřené jako zprávy službou IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Poplatky za operace

| Operace | Informace o fakturaci | 
| --------- | ------------------- |
| Operace registru identit <br/> (vytvořit, získat, seznam, aktualizace, odstranění) | Není účtován. |
| Zprávy typu zařízení-cloud | Úspěšně odeslané zprávy jsou účtovány v bloky 4 KB na příjem příchozích dat do služby IoT Hub. Například zprávu 6 KB je účtován 2 zprávy. |
| Zprávy typu cloud zařízení | Budou se účtovat úspěšně odeslaných zpráv v 4 KB bloků, například zprávu 6 KB je účtován 2 zprávy. |
| Nahrávání souborů | Přenos souborů do úložiště Azure není – měření podle objemu službou IoT Hub. Zprávy pro zahájení a ukončení přenosu souborů budou účtovat podle messaged měřené v přírůstcích po 4 KB. Například 10 MB soubor je účtován dvě zprávy kromě náklady na úložiště Azure. |
| Přímé metody | Metoda úspěšné požadavky budou účtovat v bloky 4 KB, odpovědi s neprázdný subjekty účtované v 4 KB bloky jako další zprávy. Požadavky na odpojený zařízení budou účtovat jako zprávy v bloky 4 KB. Metoda s 6 KB textu, jejímž výsledkem odpověď se žádný text ze zařízení, například účtován jako dvě zprávy. Metoda s 6 KB textu, jejímž výsledkem 1 KB odpověď od zařízení, je účtován jako dvě zprávy pro požadavek plus další zprávu pro odpověď. |
| Přečte zařízení a modul twin | Twin přečte z modulu nebo zařízení a řešení zpět end účtujeme jako zprávy v bloky o velikosti 512 bajtů. Například čtení twin 6 KB je účtován jako 12 zprávy. |
| Aktualizace zařízení a modul twin (značky a vlastnosti) | Aktualizace Twin z modulu na zařízení a back-end řešení budou účtovat jako zprávy v bloky o velikosti 512 bajtů. Například čtení twin 6 KB je účtován jako 12 zprávy. |
| Zařízení a modul dotazů twin | Dotazy budou účtovat jako zprávy v závislosti na velikosti výsledek v bloky o velikosti 512 bajtů. |
| Operace úloh <br/> (vytvoření, aktualizace, výpis, odstranění) | Není účtován. |
| Operace úlohy na zařízení | Budou se účtovat normální úlohy operací (například twin aktualizace a metody). Například úlohu výsledkem volání metody 1000 s požadavky 1 KB a prázdný text odpovědi je účtován 1000 zprávy. |

> [!NOTE]
> Všech velikostí se vypočítávají zvažování velikost datové části v bajtech (protokol rámcovacích bude ignorován). Pro zprávy, které se mají vlastnosti a text, velikost se vypočítává způsobem, bez ohledu na protokol. Další informace najdete v tématu [IoT Hub – Příručka vývojáře pro zasílání zpráv][lnk-message-size].

## <a name="example-1"></a>Příklad #1

Zařízení odesílá do služby IoT Hub, který je pak přečte Azure Stream Analytics jednu zprávu typu zařízení cloud 1 KB za minutu. Back-end řešení volá metodu (s odebranou datovou částí 512 bajtů) na zařízení každých 10 minut k aktivaci určité akce. Zařízení reaguje na metodu s tím výsledkem 200 bajtů.

Zařízení využívá:

* Jednu zprávu * 60 minut * 24 hodin = 1440 zprávy na každý den pro zprávy typu zařízení cloud.
* Dva požadavku a odpovědi * 6krát za hodinu * 24 hodin = 288 zprávy pro metody.

Tento výpočet poskytuje celkový počet zpráv. 1728 za den.

## <a name="example-2"></a>Příklad #2

Zařízení odesílá jednu zprávu 100 KB zařízení cloud každou hodinu. Aktualizuje také jeho dvojče zařízení s datových částí 1 KB každé čtyři hodiny. Řešení zpět ukončení, jednou za den, čte dvojče zařízení 14 KB a aktualizuje se 512 bajtů datové části, chcete-li změnit konfiguraci.

Zařízení využívá:

* 25 zprávy (100 KB nebo 4 KB) * 24 hodin pro zprávy typu zařízení cloud.
* Dvě zprávy (1 KB/0,5 KB) * šestkrát na každý den pro aktualizace twin zařízení.

Tento výpočet poskytuje celkem 612 zpráv za den.

Back-end řešení využívá 28 zprávy (14 KB/0,5 KB) ke čtení dvojče zařízení a navíc jednu zprávu k aktualizaci, celkem 29 zprávy.

Celkem zařízení a back-end řešení využívat 641 zpráv za den.


[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-message-size]: iot-hub-devguide-messages-construct.md
