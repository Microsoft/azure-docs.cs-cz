---
title: Úvod do Azure Internet věcí (IoT)
description: Seznámení s vysvětlením základních služeb Azure IoT a IoT, včetně příkladů, které vám pomůžou ilustrovat používání IoT.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: d2afd88500801f1e2b1e4da5a1e267bddfa69fc7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96453024"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Co je Azure Internet věcí (IoT)?

Internet věcí (IoT) Azure je kolekce cloudových služeb spravovaných Microsoftem, která připojuje, monitoruje a řídí miliardy prostředků IoT. Jednodušší pojem řešení IoT je tvořeno jedním nebo více zařízeními IoT, která komunikují s jednou nebo více službami back-end hostovanými v cloudu. 

## <a name="iot-devices"></a>Zařízení IoT

Zařízení IoT obvykle tvoří desku okruhu s připojenými senzory, které používají Wi-Fi pro připojení k Internetu. Například:

* Tlakový senzor na vzdáleném olejovém pumpě.
* Senzory teploty a vlhkosti v klimatizační jednotce.
* Akcelerometr v výtahu.
* Senzory přítomnosti v místnosti.

Existuje široká škála zařízení, která jsou k dispozici od různých výrobců pro sestavení řešení. Seznam zařízení s certifikací pro práci s Azure IoT Hub najdete v [katalogu zařízení Azure Certified for IoT](https://catalog.azureiotsolutions.com/alldevices). Pro vytváření prototypů můžete používat zařízení, jako je [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) nebo [Malina Pi](https://www.raspberrypi.org/). DevKit má integrované senzory pro teplotu, tlak, vlhkost a vybavený gyroskopem, akcelerometr a magnetometer. Malina Pi umožňuje připojit mnoho různých typů senzorů. 

Microsoft poskytuje sady [SDK](../iot-hub/iot-hub-devguide-sdks.md) open source, které můžete použít k sestavení aplikací, které běží na vašich zařízeních. Tyto sady [SDK zjednodušují a urychlují](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) vývoj vašich řešení IoT.

## <a name="communication"></a>Komunikace

Zařízení IoT obvykle odesílají telemetrii ze senzorů do back-endové služby v cloudu. Je ale možné použít i jiné typy komunikace, jako je například back-end služba odesílající příkazy do vašich zařízení. Následuje několik příkladů komunikace typu zařízení-Cloud a Cloud-zařízení:

* Mobilní chladicí vozík posílá do IoT Hub teplotu každých 5 minut. 

* Back-endové služba pošle příkaz do zařízení, aby změnil četnost, s jakou posílá telemetrii, aby pomohla diagnostikovat problém. 

* Zařízení odesílá výstrahy na základě hodnot čtených z jeho senzorů. Například zařízení, které se v chemickém podniku sleduje v rámci služby Batch, pošle výstrahu, když teplota překročí určitou hodnotu.

* Vaše zařízení odesílají informace, aby se zobrazily na řídicím panelu, aby je zobrazovaly pomocí operátorů lidské. Například řídicí místnost v rafinérii může ukázat teplotu, tlak a objem toků v každém kanálu a umožnit tak operátorům monitorovat zařízení. 

Sady [SDK pro zařízení IoT](../iot-hub/iot-hub-devguide-sdks.md) a IoT Hub podporují běžné [komunikační protokoly](../iot-hub/iot-hub-devguide-protocols.md) , jako jsou http, MQTT a AMQP.

Zařízení IoT mají ve srovnání s jinými klienty, jako jsou prohlížeče a mobilní aplikace, různé charakteristiky. Sady SDK pro zařízení vám pomůžou vyřešit výzvy k bezpečnému a spolehlivému připojení zařízení k vaší back-endové službě.  Konkrétně zařízení IoT:

* Jsou často integrované systémy bez lidské obsluhy (na rozdíl od telefonu).
* Mohou být nasazená ve vzdálených umístěních, kam je fyzický přístup nákladný.
* Můžou být dostupná jenom prostřednictvím back-endu řešení.
* Můžou mít omezené prostředky pro napájení a zpracování.
* Můžou mít přerušované, pomalé nebo nákladné síťové připojení.
* Můžou potřebovat chráněné, vlastní nebo průmyslové protokoly aplikací.

## <a name="back-end-services"></a>Back-endové služby 

V řešení IoT služba back-end poskytuje funkce, jako například:

* Příjem telemetrie se škálováním ze zařízení a určení způsobu zpracování a ukládání těchto dat.
* Analýza telemetrie za účelem poskytování přehledů v reálném čase nebo po faktu.
* Posílání příkazů z cloudu na konkrétní zařízení. 
* Zřizování zařízení a řízení zařízení, která se můžou připojovat k vaší infrastruktuře
* Řízení stavu zařízení a monitorování jejich aktivit.
* Správa firmwaru nainstalovaného na vašich zařízeních.

Například v řešení vzdáleného monitorování pro čerpací stanice v cloudu používá cloudový back-end telemetrie z čerpadel k identifikaci chování neobvyklé. Když back-end služba identifikuje anomálii, může automaticky odeslat příkaz zpět do zařízení, aby probral nápravné akce. Tento proces generuje automatizovanou smyčku zpětné vazby mezi zařízením a cloudem, která výrazně zvyšuje efektivitu řešení.

## <a name="azure-iot-examples"></a>Příklady Azure IoT

Příklady použití Azure IoT v reálném čase najdete v tématu [technické studie Microsoftu pro IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

Podrobné informace o architektuře IoT najdete v článku [Referenční architektura IoT v Microsoft Azure](/azure/architecture/reference-architectures/iot).

## <a name="next-steps"></a>Další kroky

V případě některých skutečných obchodních případů a používané architektury si přečtěte [Microsoft Azure technické případové studie IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured).

U některých ukázkových projektů, které si můžete vyzkoušet s IoT DevKit, přečtěte si [katalog projektu IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/). 

Komplexnější vysvětlení různých služeb a způsobu jejich použití najdete v tématu [služby a technologie Azure IoT](iot-services-and-technologies.md).