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
ms.openlocfilehash: e6c8e83838240b989c8c782840d8c771a6f6b7db
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103487798"
---
# <a name="what-is-azure-iot-edge"></a>Co je Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge přesouvá analýzy a vlastní obchodní logiku z cloudu do zařízení, aby se vaše organizace mohla místo správy dat soustředit na obchodní přehledy. Škálujte své řešení IoT tím, že zabalíte svoji obchodní logiku do standardních kontejnerů, potom můžete tyto kontejnery nasadit do libovolného ze svých zařízení a monitorovat všechno z cloudu.

Analýzy zvyšují přidanou hodnotu řešení IoT, ale ne všechny analýzy musí být v cloudu. Pokud chcete co nejrychleji reagovat na naléhavé situace, můžete na hraničních zařízeních spouštět úlohy detekce anomálií. Pokud chcete snížit náklady na šířku pásma a vyhnout se přenosu terabajtů nezpracovaných dat, můžete data vyčistit a agregovat místně a pak je do cloudu odesílat jenom analýzy.

Azure IoT Edge se skládá ze tří komponent:

* **Moduly IoT Edge** jsou kontejnery, ve kterých běží služby Azure, služby třetích stran nebo váš vlastní kód. Moduly se nasazují do zařízení IoT Edge a spouštějí se místně na těchto zařízeních.
* **IoT Edge runtime** běží na každém zařízení IoT Edge a spravuje moduly nasazené na každé zařízení.
* **Cloudové rozhraní** umožňuje vzdáleně monitorovat a spravovat IoT Edge zařízení.

>[!NOTE]
>Azure IoT Edge je k dispozici na úrovni Free a Standard služby IoT Hub. Úroveň Free je určená pouze k testování a posouzení. Další informace o úrovních Basic a Standard najdete v [návodu k výběru správné úrovně služby IoT Hub](../iot-hub/iot-hub-scaling.md).

## <a name="iot-edge-modules"></a>Moduly IoT Edge

Moduly IoT Edge jsou jednotky spuštění, které se implementují jako kontejnery kompatibilní s Docker, které spouští vaši obchodní logiku na hraničních zařízeních. Můžete nakonfigurovat vzájemnou komunikaci několika modulů a vytvořit tak kanál zpracování dat. Můžete vyvíjet vlastní moduly nebo balit určité služby Azure do modulů, které poskytují přehledy v režimu offline a na hraničních zařízení.

### <a name="artificial-intelligence-at-the-edge"></a>Umělá logika na hraničních zařízeních

Azure IoT Edge umožňuje nasazení komplexního zpracování událostí, strojového učení, rozpoznávání obrázků a další vysoké hodnoty AI bez nutnosti psát interní. Služby Azure, jako jsou Azure Functions, Azure Stream Analytics a Azure Machine Learning, se dají spouštět místně prostřednictvím Azure IoT Edge. Nejste omezeni jenom na služby Azure, i když. Kdokoli může vytvořit moduly AI a zpřístupnit je komunitě pro použití prostřednictvím Azure Marketplace.

### <a name="bring-your-own-code"></a>Používání vlastního kódu

Azure IoT Edge podporuje také případy, kdy do svých zařízení chcete nasadit vlastní kód. Azure IoT Edge se drží stejného programovacího modelu jako ostatní služby Azure IoT. Stejný kód můžete spustit na zařízení nebo v cloudu. Azure IoT Edge podporuje Linux i Windows, takže můžete psát kód pro platformu podle svého výběru. Podporuje jazyk Java, .NET Core 2,0, Node.js, C a Python, takže vývojáři můžou kódovat kód v jazyce, který už znají, a používat stávající obchodní logiku.

## <a name="iot-edge-runtime"></a>Modul runtime IoT Edge

Modul runtime Azure IoT Edge umožňuje používat na hraničních zařízeních IoT vlastní a cloudovou logiku. Modul runtime je umístěný na zařízení IoT Edge a provádí operace správy a komunikace. Modul runtime provádí několik funkcí:

* Nainstaluje a aktualizuje úlohy na zařízení.
* Udržuje na zařízení standardy zabezpečení Azure IoT Edge.
* Zajišťuje nepřetržitý provoz modulů IoT Edge.
* Hlásí do cloudu stav modulů pro účely vzdáleného monitorování.
* Spravuje komunikaci mezi moduly pro příjem a IoT Edge zařízení, mezi moduly v IoT Edgem zařízení a mezi IoT Edgemi zařízeními a cloudem.

![Modul runtime IoT Edge odesílá přehledy a sestavy do služby IoT Hub.](./media/about-iot-edge/runtime.png)

Způsob použití Azure IoT Edge zařízení je až na vás. Modul runtime se často používá k nasazení AI do zařízení brány, která agregují a zpracovávají data z jiných místních zařízení, ale tento model nasazení je jenom jedna možnost.

Modul runtime Azure IoT Edge běží na velké sadě zařízení IoT, která umožňuje jejich použití v nejrůznějších ohledech. Podporuje operační systémy Linux i Windows a vyabstrakce podrobnosti o hardwaru. Pokud nezpracováváte mnoho dat, použijte zařízení, které je menší než Malina 3, nebo použijte průmyslový Server ke spouštění úloh náročných na prostředky.

## <a name="iot-edge-cloud-interface"></a>Cloudové rozhraní IoT Edge

Je obtížné spravovat životní cyklus softwaru pro miliony zařízení IoT, která jsou často odlišná pro modely a modely nebo geograficky rozptýlené. Musí se vytvořit a nakonfigurovat úlohy pro konkrétní typy zařízení a pak úlohy nasadit na všechna zařízení a monitorovat je, aby se zachytila zařízení, která se případně nechovají podle očekávání. Tyto aktivity není možné provádět pro každé zařízení zvlášť a musí probíhat ve velkém měřítku.

Azure IoT Edge se bezproblémově integruje s akcelerátory řešení Azure IoT a poskytuje tak jedinou rovinu řízení pro všechny požadavky vašeho řešení. Cloudové služby umožňují:

* Vytvořit a nakonfigurovat úlohu, která se má spouštět na konkrétním typu zařízení.
* Odeslat úlohu do sady zařízení.
* Monitorovat úlohy spuštěné na zařízeních v poli.

![Telemetrii a akce zařízení se koordinují s cloudem](./media/about-iot-edge/cloud-interface.png)

## <a name="next-steps"></a>Další kroky

Vyzkoušejte si tyto koncepty při [nasazení IoT Edge na simulovaném zařízení](quickstart.md).
