---
title: Úvod do Azure Internet věcí (IoT)
description: Úvod vysvětlující základy Azure IoT a služeb IoT, včetně příkladů, které pomáhají ilustrovat použití IoT.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 73eb0b3164a386bb270e42ceba56d5dc7045af1c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729003"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Co je Azure Internet věcí (IoT)?

Internet věcí (IoT) Azure je kolekce cloudových služeb spravovaných Microsoftem, která připojuje, monitoruje a řídí miliardy prostředků IoT. Jednodušeji řečeno, řešení IoT se skládá z jednoho nebo více zařízení IoT, která komunikují s jednou nebo více back-endovými službami hostovanými v cloudu. 

## <a name="iot-devices"></a>Zařízení IoT

Zařízení IoT se obvykle skládá z desky s plošnými spoji s připojenými senzory, které používají WiFi pro připojení k internetu. Příklad:

* Snímač tlaku na dálkovém olejovém čerpadle.
* Čidla teploty a vlhkosti v klimatizační jednotce.
* Akcelerometr ve výtahu.
* Senzory v místnosti.

K dispozici je široká škála zařízení od různých výrobců, která vytvářejí vaše řešení. Seznam zařízení certifikovaných pro práci s Azure IoT Hub najdete v [katalogu zařízení Azure Certified for IoT](https://catalog.azureiotsolutions.com/alldevices). Pro vytváření prototypů můžete použít zařízení, jako je [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) nebo [Raspberry Pi](https://www.raspberrypi.org/). Devkit má vestavěné senzory pro teplotu, tlak, vlhkost a gyroskop, akcelerometr a magnetometr. Raspberry Pi umožňuje připojit mnoho různých typů senzorů. 

Společnost Microsoft poskytuje sady SDK s otevřeným zdrojovým [kódem,](../iot-hub/iot-hub-devguide-sdks.md) které můžete použít k vytváření aplikací spuštěných na vašich zařízeních. Tyto [sady SDK zjednodušují a urychlují](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) vývoj vašich řešení IoT.

## <a name="communication"></a>Communication

Zařízení IoT obvykle odesílají telemetrii ze senzorů do back-endových služeb v cloudu. Jiné typy komunikace jsou však možné, například služba back-end odesílání příkazů do vašich zařízení. Následují některé příklady komunikace mezi zařízeními a cloudem a zařízením:

* Mobilní chladicí vůz posílá teplotu každých 5 minut do ioT hubu. 

* Služba back-end odešle příkaz do zařízení změnit frekvenci, na které odesílá telemetrie pomoci diagnostikovat problém. 

* Zařízení odesílá výstrahy na základě hodnot čtené ze snímačů. Například zařízení monitorující dávkový reaktor v chemickém závodě odešle výstrahu, když teplota překročí určitou hodnotu.

* Vaše zařízení odesílají informace k zobrazení na řídicím panelu pro zobrazení lidskými operátory. Například řídicí místnost v rafinérii může zobrazovat teploty, tlak a objemy průtoku v každé trubce, což umožňuje obsluhám sledovat zařízení. 

Sady [SDK zařízení IoT](../iot-hub/iot-hub-devguide-sdks.md) a Služby IoT Hub podporují běžné [komunikační protokoly,](../iot-hub/iot-hub-devguide-protocols.md) jako je HTTP, MQTT a AMQP.

Zařízení IoT mají ve srovnání s jinými klienty, jako jsou prohlížeče a mobilní aplikace, různé charakteristiky. Sady SDK zařízení vám pomohou bezpečně a spolehlivě řešit problémy spojené s bezpečným a spolehlivým připojením zařízení k back-endové službě.  Konkrétně zařízení IoT:

* Jsou často integrované systémy bez lidské obsluhy (na rozdíl od telefonu).
* Mohou být nasazená ve vzdálených umístěních, kam je fyzický přístup nákladný.
* Můžou být dostupná jenom prostřednictvím back-endu řešení.
* Můžou mít omezené prostředky pro napájení a zpracování.
* Můžou mít přerušované, pomalé nebo nákladné síťové připojení.
* Můžou potřebovat chráněné, vlastní nebo průmyslové protokoly aplikací.

## <a name="back-end-services"></a>Back-end služby 

V řešení IoT poskytuje back-endová služba funkce, jako jsou:

* Příjem telemetrie ve velkém měřítku z vašich zařízení a určení, jak zpracovat a ukládat tato data.
* Analýza telemetrie poskytnout přehledy, buď v reálném čase nebo po faktu.
* Odesílání příkazů z cloudu do konkrétního zařízení. 
* Zřizování zařízení a řízení, která zařízení se mohou připojit k vaší infrastruktuře.
* Řízení stavu vašich zařízení a sledování jejich aktivit.
* Správa firmwaru nainstalovaného na vašich zařízeních.

Například v řešení vzdáleného monitorování pro čerpací stanici oleje, cloud back-end používá telemetrii z čerpadel k identifikaci neobvyklé chování. Když back-endová služba identifikuje anomálii, může automaticky odeslat příkaz zpět do zařízení, aby přijala nápravná opatření. Tento proces generuje automatizovanou smyčku zpětné vazby mezi zařízením a cloudem, která výrazně zvyšuje efektivitu řešení.

## <a name="azure-iot-examples"></a>Příklady Azure IoT

Skutečné příklady toho, jak organizace používají Azure IoT, najdete [v tématu Technické případové studie Microsoftu pro IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

## <a name="next-steps"></a>Další kroky

Některé skutečné obchodní případy a použitou architekturu najdete v [technických případových studiích Microsoft Azure IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured).

U některých ukázkových projektů, které můžete vyzkoušet pomocí ioT DevKit, najdete v tématu [Katalog projektů IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/). 

Podrobnější vysvětlení různých služeb a jejich použití najdete v [tématu služby a technologie Azure IoT](iot-services-and-technologies.md).

Podrobné informace o architektuře IoT najdete v článku [Referenční architektura IoT v Microsoft Azure](https://aka.ms/iotrefarchitecture).
