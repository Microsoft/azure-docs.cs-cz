---
title: Co je Azure IoT Edge | Dokumentace Microsoftu
description: Přehled služby Azure IoT Edge
author: kgremban
manager: philmea
ms.reviewer: chipalost
ms.service: iot-edge
services: iot-edge
ms.topic: overview
ms.date: 04/17/2019
ms.author: kgremban
ms.custom: mvc
ms.openlocfilehash: 70a8e17ecdd318a800c51e04b4453c182d1fbdc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61248854"
---
# <a name="what-is-azure-iot-edge"></a>Co je Azure IoT Edge

Azure IoT Edge přesouvá analýzy a vlastní obchodní logiku z cloudu do zařízení, aby se vaše organizace mohla místo správy dat soustředit na obchodní přehledy. Horizontální navýšení kapacity váš Internet věcí o balení obchodní logiky do standardních kontejnerů, můžete nasadit těchto kontejnerů na libovolném zařízení a monitorovat všechno z cloudu. 

Analýzy zvyšují přidanou hodnotu řešení IoT, ale ne všechny analýzy musí být v cloudu. Pokud chcete reagovat na nouzové situace co nejrychleji, můžete spouštět úlohy detekce anomálií na hraničních zařízeních. Pokud chcete snížit náklady na šířku pásma a vyhnout se přenosu terabajtů nezpracovaných dat, můžete vyčistit a agregaci dat místně pak pouze takové informace odesílat do cloudu pro účely analýzy. 

Azure IoT Edge se skládá ze tří komponent:
* **Moduly IoT Edge** jsou kontejnery, na kterých běží služby Azure, třeba služby třetích stran nebo váš vlastní kód. Moduly jsou nasazené na zařízení IoT Edge a na těchto zařízeních se místně spouštějí. 
* **Modul runtime IoT Edge** běží na všech hraničních zařízeních IoT a spravuje moduly nasazené do jednotlivých zařízení. 
* A **rozhraní založené na cloudu** umožňuje vzdáleně monitorovat a spravovat zařízení IoT Edge.

>[!NOTE]
>Azure IoT Edge je k dispozici na úrovni Free a Standard služby IoT Hub. Úroveň Free je určená pouze k testování a posouzení. Další informace o úrovních Basic a Standard najdete v [návodu k výběru správné úrovně služby IoT Hub](../iot-hub/iot-hub-scaling.md).

## <a name="iot-edge-modules"></a>Moduly IoT Edge

Moduly IoT Edge jsou jednotky spuštění, které jsou implementované jako kontejnery kompatibilní s Dockerem, které spouští vaši obchodní logiku na hraničních zařízeních. Můžete nakonfigurovat vzájemnou komunikaci několika modulů a vytvořit tak kanál zpracování dat. Můžete vyvíjet vlastní moduly nebo balit určité služby Azure do modulů, které poskytují přehledy v režimu offline a na hraničních zařízení. 

### <a name="artificial-intelligence-at-the-edge"></a>Umělá inteligence na hraničních zařízeních

Azure IoT Edge umožňuje nasazovat komplexní zpracování událostí, machine learning, rozpoznávání obrazu a další hodnotnou AI aniž byste museli napsat interně. Služby Azure, jako je Azure Functions, Azure Stream Analytics a Azure Machine Learning je všechny možné spouštět v místním prostřednictvím Azure IoT Edge. Nejste omezeni pouze na služby Azure, ale. Každý, je schopna vytvářet moduly AI a zpřístupnit je pro použití v Tržišti Azure Marketplace komunitě. 

### <a name="bring-your-own-code"></a>Používání vlastního kódu

Azure IoT Edge podporuje také případy, kdy do svých zařízení chcete nasadit vlastní kód. Azure IoT Edge se drží stejného programovacího modelu jako ostatní služby Azure IoT. Stejný kód je možné spouštět v zařízení nebo v cloudu. Azure IoT Edge podporuje Linux i Windows, takže můžete psát kód pro platformu podle svého výběru. Takže vaši vývojáři můžou psát kód v jazyce, které už znají a používat existující obchodní logiku podporuje Javu, .NET Core 2.0, Node.js, C a Python.

## <a name="iot-edge-runtime"></a>Modul runtime IoT Edge

Modul runtime Azure IoT Edge umožňuje používat na hraničních zařízeních IoT vlastní a cloudovou logiku. Nachází se v hraničním zařízení IoT a provádí operace správy a komunikace. Modul runtime provádí několik funkcí:

* Instalace a aktualizace úlohy na zařízení.
* Udržujte standardy zabezpečení Azure IoT Edge na zařízení.
* Ujistěte se, že nepřetržitý provoz modulů IoT Edge.
* Sestava Stav modulů do cloudu pro vzdálené monitorování.
* Spravujte komunikaci mezi podřízenými zařízeními typu list a zařízení IoT Edge, mezi moduly v zařízení IoT Edge a mezi zařízení IoT Edge a cloudem.

![Modul runtime IoT Edge odesílá přehledy a sestavy do služby IoT Hub.](./media/about-iot-edge/runtime.png)

Použití zařízení Azure IoT Edge je na vás. Modul runtime se často používá k nasazování AI do zařízení brány, které agregovat a zpracovávat data z jiných místních zařízení, ale tento model nasazení je pouze jedna z možností. 

Modul runtime Azure IoT Edge běží ve velké sadě zařízení IoT a díky tomu jej můžete používat nejrůznějšími způsoby. Podporuje operační systémy Linux a Windows a abstraktní podrobnosti o hardwaru. Můžete použijte menší zařízení než Raspberry Pi 3 Pokud nezpracováváte množství dat, nebo použijete průmyslové server ke spouštění náročných úloh.

## <a name="iot-edge-cloud-interface"></a>Cloudové rozhraní IoT Edge

Je obtížné spravovat životní cyklus softwaru pro miliony zařízení IoT, které jsou často různých značek a modely nebo geograficky rozmístěny. Úlohy jsou vytvořené a nakonfigurované pro konkrétní typ zařízení, nasadit do všech svých zařízení a monitorovat zachytit všechna identifikovala zařízení. Tyto aktivity není možné provádět pro každé zařízení zvlášť a musí se provádět ve velkém měřítku.

Azure IoT Edge se bezproblémově integruje s akcelerátory řešení Azure IoT a poskytuje tak jedinou rovinu řízení pro všechny požadavky vašeho řešení. Cloudové služby vám umožní:

* Vytvořit a nakonfigurovat úlohu, která se má spouštět na konkrétním typu zařízení.
* Odeslat úlohu do sady zařízení.
* Monitorovat úlohy spuštěné na zařízeních v poli.

![Akce a telemetrii zařízení se koordinují s cloudem](./media/about-iot-edge/cloud-interface.png)

## <a name="next-steps"></a>Další postup

Vyzkoušejte si tyto koncepty při [nasazení IoT Edge na simulovaném zařízení](quickstart.md).