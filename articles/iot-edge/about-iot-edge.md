---
title: Co je Azure IoT Edge | Dokumentace Microsoftu
description: Přehled služby Azure IoT Edge
author: kgremban
manager: philmea
ms.reviewer: chipalost
ms.service: iot-edge
services: iot-edge
ms.topic: overview
ms.date: 10/28/2019
ms.author: kgremban
ms.custom: mvc
ms.openlocfilehash: 1ba133acda414d9779e2fb10150bbdd57285e9a5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76511208"
---
# <a name="what-is-azure-iot-edge"></a>Co je Azure IoT Edge

Azure IoT Edge přesouvá analýzy a vlastní obchodní logiku z cloudu do zařízení, aby se vaše organizace mohla místo správy dat soustředit na obchodní přehledy. Škálujte své řešení IoT tak, že zabalíte obchodní logiku do standardních kontejnerů, pak můžete tyto kontejnery nasadit do libovolného zařízení a sledovat je z cloudu.

Analýzy zvyšují přidanou hodnotu řešení IoT, ale ne všechny analýzy musí být v cloudu. Pokud chcete reagovat na mimořádné události co nejrychleji, můžete spustit úlohy detekce anomálií na hraničních zařízeních. Pokud chcete snížit náklady na šířku pásma a vyhnout se přenosu terabajtů nezpracovaných dat, můžete data vyčistit a agregovat místně a pak pouze odeslat přehledy do cloudu pro analýzu.

Azure IoT Edge se skládá ze tří komponent:

* **Moduly IoT Edge jsou kontejnery,** které spouštějí služby Azure, služby třetích stran nebo vlastní kód. Moduly se nasazují do zařízení IoT Edge a spouštějí se místně na těchto zařízeních.
* **Modul runtime IoT Edge** běží na každém zařízení IoT Edge a spravuje moduly nasazené do každého zařízení.
* **Cloudové rozhraní** umožňuje vzdáleně monitorovat a spravovat zařízení IoT Edge.

>[!NOTE]
>Azure IoT Edge je k dispozici na úrovni Free a Standard služby IoT Hub. Úroveň Free je určená pouze k testování a posouzení. Další informace o úrovních Basic a Standard najdete v [návodu k výběru správné úrovně služby IoT Hub](../iot-hub/iot-hub-scaling.md).

## <a name="iot-edge-modules"></a>Moduly IoT Edge

Moduly IoT Edge jsou jednotky provádění, implementované jako kontejnery kompatibilní s Dockerem, které spouštějí vaši obchodní logiku na hraničních zařízeních. Můžete nakonfigurovat vzájemnou komunikaci několika modulů a vytvořit tak kanál zpracování dat. Můžete vyvíjet vlastní moduly nebo balit určité služby Azure do modulů, které poskytují přehledy v režimu offline a na hraničních zařízení.

### <a name="artificial-intelligence-at-the-edge"></a>Umělá inteligence na okraji

Azure IoT Edge umožňuje nasadit komplexní zpracování událostí, strojové učení, rozpoznávání image a další umělou ai s vysokou hodnotou bez psaní interní. Služby Azure, jako jsou Azure Functions, Azure Stream Analytics a Azure Machine Learning, se můžou spouštět místně přes Azure IoT Edge. Nejste však omezeni na služby Azure. Kdokoli je schopný vytvářet moduly AI a zpřístupnit je komunitě pro použití na Azure Marketplace.

### <a name="bring-your-own-code"></a>Používání vlastního kódu

Azure IoT Edge podporuje také případy, kdy do svých zařízení chcete nasadit vlastní kód. Azure IoT Edge se drží stejného programovacího modelu jako ostatní služby Azure IoT. Stejný kód můžete spustit na zařízení nebo v cloudu. Azure IoT Edge podporuje Linux i Windows, takže můžete psát kód pro platformu podle svého výběru. Podporuje Java, .NET Core 2.0, Node.js, C a Python, takže vaši vývojáři mohou kódovat v jazyce, který už znají, a používat existující obchodní logiku.

## <a name="iot-edge-runtime"></a>Modul runtime IoT Edge

Modul runtime Azure IoT Edge umožňuje používat na hraničních zařízeních IoT vlastní a cloudovou logiku. Runtime je na zařízení IoT Edge a provádí operace správy a komunikace. Modul runtime provádí několik funkcí:

* Nainstaluje a aktualizuje úlohy v zařízení.
* Udržuje na zařízení standardy zabezpečení Azure IoT Edge.
* Zajišťuje nepřetržitý provoz modulů IoT Edge.
* Hlásí do cloudu stav modulů pro účely vzdáleného monitorování.
* Spravuje komunikaci mezi zařízeními s navazujícími listy a zařízením IoT Edge, mezi moduly na zařízení IoT Edge a mezi zařízením IoT Edge a cloudem.

![Modul runtime IoT Edge odesílá přehledy a sestavy do služby IoT Hub.](./media/about-iot-edge/runtime.png)

Jak používáte zařízení Azure IoT Edge, je na vás. Doba runtime se často používá k nasazení ai do zařízení brány, které agregují a zpracovávají data z jiných místních zařízení, ale tento model nasazení je jenom jednou z možností.

Runtime Azure IoT Edge běží na velké sadě zařízení IoT, která umožňuje jeho použití v mnoha různými způsoby. Podporuje operační systémy Linux i Windows a abstrahuje hardwarové detaily. Pokud nezpracováváte mnoho dat, použijte zařízení menší než Raspberry Pi 3 nebo používáte průmyslový server ke spuštění úloh náročných na prostředky.

## <a name="iot-edge-cloud-interface"></a>Cloudové rozhraní IoT Edge

Je obtížné spravovat životní cyklus softwaru pro miliony zařízení IoT, které jsou často různé způsoby a modely nebo geograficky rozptýlené. Musí se vytvořit a nakonfigurovat úlohy pro konkrétní typy zařízení a pak úlohy nasadit na všechna zařízení a monitorovat je, aby se zachytila zařízení, která se případně nechovají podle očekávání. Tyto aktivity není možné provádět pro každé zařízení zvlášť a musí se provádět ve velkém měřítku.

Azure IoT Edge se bezproblémově integruje s akcelerátory řešení Azure IoT a poskytuje tak jedinou rovinu řízení pro všechny požadavky vašeho řešení. Cloudové služby vám umožňují:

* Vytvořit a nakonfigurovat úlohu, která se má spouštět na konkrétním typu zařízení.
* Odeslat úlohu do sady zařízení.
* Monitorovat úlohy spuštěné na zařízeních v poli.

![Telemetrie zařízení a akce jsou koordinovány s cloudem](./media/about-iot-edge/cloud-interface.png)

## <a name="next-steps"></a>Další kroky

Vyzkoušejte si tyto koncepty při [nasazení IoT Edge na simulovaném zařízení](quickstart.md).
