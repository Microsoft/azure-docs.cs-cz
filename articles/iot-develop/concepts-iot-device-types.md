---
title: Přehled typů zařízení Azure IoT
description: Seznamte se s různými typy zařízení, které podporuje Azure IoT, a dostupných nástrojů.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: aa99594fe3de98635e37d15beebf015f15dc4f64
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654158"
---
# <a name="overview-of-azure-iot-device-types"></a>Přehled typů zařízení Azure IoT
Zařízení IoT existují napříč širokou volbou hardwarových platforem. Existuje malé 8bitové Mikrokontroleryy až do nejnovějších procesorů x86, jak se nachází na stolním počítači. Řada proměnných je faktorem do rozhodnutí, pro který hardware si zvolíte pro zařízení IoT, a v tomto článku jsou uvedené některé hlavní rozdíly.

## <a name="key-hardware-differentiators"></a>Klíčové rozdíly hardwaru
Mezi důležité faktory při výběru hardwaru patří náklady, spotřeba energie, sítě a dostupné vstupy a výstupy.

* **Náklady:** Menší levnější zařízení se obvykle používá v případě, že se hromadně vyrábí konečný produkt. Pokud je však obchod vypnutý, může být vývoj zařízení levnější vzhledem k vysokému omezení zařízení. Náklady na vývoj mohou být rozloženy do všech vyráběných zařízení, takže náklady na vývoj jednotek budou nízké.

* **Napájení:** Kolik energie zařízení spotřebovává, je důležité, pokud bude zařízení používat baterie a není připojené k Power gridu. Mikrokontrolery jsou často navržené pro nižší scénáře napájení a můžou být lepší volbou pro rozšíření výdrže baterie.

* **Přístup k síti:** Existuje mnoho způsobů, jak připojit zařízení ke cloudové službě. Síť Ethernet, Wi-Fi a mobilní síť a některé z dostupných možností. Typ připojení, který zvolíte, bude záviset na tom, kde je zařízení nasazené a jak se používá. Mobilní síť může být například zajímavou možností pro vysoké pokrytí, ale u zařízení s vysokým objemem přenosů může být nákladný. Hardwired Ethernet poskytuje levnější náklady na data, ale s nevýhodouem, který je méně přenosný.

* **Vstup a výstupy:** Vstupy a výstupy, které jsou k dispozici na zařízení, mají přímý vliv na možnosti provozu zařízení. Mikrokontrolerů má obvykle mnoho vstupně-výstupních funkcí sestavených přímo do čipu a nabízí nejrůznější senzory pro přímé připojení.

## <a name="microcontrollers-vs-microprocessors"></a>Mikrořadiče vs mikroprocesory
Zařízení IoT je možné rozdělit do dvou rozsáhlých kategorií, mikrokontrolérů (mikrokontrolery) a mikroprocesorů (MPUs).

**Mikrokontrolery** jsou levnější a jednodušší pro provozování než MPUs. MCU bude obsahovat mnoho funkcí, jako je například paměť, rozhraní a vstupně-výstupní operace v rámci samotného čipu. Technologie MPU vykreslí tuto funkci z komponent v podpoře čipy. MCU bude často používat operační systém v reálném čase (RTO) nebo spouštět holé systémy (bez operačního systému) a poskytovat odpovědi v reálném čase a vysoce deterministické reakce na externí události.

**MPUs** bude obecně spouštět operační systém pro obecné účely, jako je například Windows, Linux nebo MacOSX, které poskytují nedeterministické odezvy v reálném čase. V případě, že úkol bude dokončen, obvykle není zaručena žádná záruka. 

:::image type="content" border="false" source="media/concepts-iot-device-types/mcu-vs-mpu.png" alt-text="MCU vs – MPU":::

Níže je tabulka znázorňující některé z definicí rozdílů mezi MCU a systémem na bázi MPU:

||Mikrokontrolerů (MCU)|Mikroprocesor (MPU)|
|-|-|-|
|**Procesor**| Less | Víc |
|**SRAM**| Less | Víc |
|**Blikající**| Less | Víc |
|**OS**| Ne nebo RTO | Pro obecné účely |
|**Problémy s vývojem**| Složitější |  Snadný |
|**Spotřeba energie**| Nižší | Vyšší |
|**Náklady**| Nižší | Vyšší |
|**Deterministický**| Yes | Ne – s výjimkami|
|**Velikost zařízení**| Zmenšen | Větší |
