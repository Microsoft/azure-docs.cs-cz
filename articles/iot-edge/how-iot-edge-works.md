---
title: Co je Azure IoT Edge | Dokumentace Microsoftu
description: Přehled služby Azure IoT Edge
services: iot-Edge
documentationcenter: ''
author: kgremban
manager: timlt
editor: chipalost
ms.assetid: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: kgremban
ms.custom: ''
ms.openlocfilehash: b14dd7ca2c35f9d660b571436f1d7f1b5185c5c1
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="what-is-azure-iot-edge---preview"></a>Co je Azure IoT Edge – Preview

Azure IoT Edge přesouvá analýzy a vlastní obchodní logiku z cloudu do zařízení, aby se vaše organizace mohla místo správy dat soustředit na obchodní přehledy. Nakonfigurujte vlastní software IoT, nasaďte ho do zařízení prostřednictvím standardních kontejnerů, to vše monitorujte z cloudu a umožněte tak skutečné škálování vašeho řešení.

>[!NOTE]
>Azure IoT Edge je k dispozici na úrovni Free a Standard služby IoT Hub. Úroveň Free je určená pouze k testování a posouzení. Další informace o úrovních Basic a Standard najdete v [návodu k výběru správné úrovně služby IoT Hub](../iot-hub/iot-hub-scaling.md).

Analýzy zvyšují přidanou hodnotu řešení IoT, ale ne všechny analýzy musí být v cloudu. Pokud chcete, aby zařízení reagovalo na nouzové situace co nejrychleji, můžete provádět detekci anomálií přímo v zařízení. Podobně pokud chcete snížit náklady na šířku pásma a vyhnout se přenosu terabajtů nezpracovaných dat, můžete provádět čištění a agregaci dat místně. Přehledy pak můžete odeslat do cloudu. 

Azure IoT Edge se skládá ze tří komponent:
* Moduly IoT Edge jsou kontejnery, ve kterých se spouští služby Azure, služby třetích stran nebo váš vlastní kód. Nasazují se do hraničních zařízení IoT a na těchto zařízeních se místně spouštějí. 
* Modul runtime IoT Edge běží na všech hraničních zařízeních IoT a spravuje moduly nasazené do jednotlivých zařízení. 
* Cloudové rozhraní umožňuje vzdáleně monitorovat a spravovat hraniční zařízení IoT.

## <a name="iot-edge-modules"></a>Moduly IoT Edge

Moduly IoT Edge jsou jednotky spuštění, aktuálně implementované jako kontejnery kompatibilní s Dockerem, které spouští vaši obchodní logiku na hraničních zařízeních. Můžete nakonfigurovat vzájemnou komunikaci několika modulů a vytvořit tak kanál zpracování dat. Můžete vyvíjet vlastní moduly nebo balit určité služby Azure do modulů, které poskytují přehledy v režimu offline a na hraničních zařízení. 

### <a name="artificial-intelligence-on-the-edge"></a>Umělá inteligence na hraničních zařízeních

Azure IoT Edge umožňuje nasazovat komplexní zpracování událostí, machine learning, rozpoznávání obrazu a další hodnotnou AI, aniž byste je museli sami psát. Prostřednictvím Azure IoT Edge můžete v místním prostředí spouštět služby Azure, jako jsou Azure Functions, Azure Stream Analytics a Azure Machine Learning, ale nejste omezeni pouze na služby Azure. Kdokoli může vytvářet moduly AI a zpřístupnit je pro použití komunitě. 

### <a name="bring-your-own-code"></a>Používání vlastního kódu

Azure IoT Edge podporuje také případy, kdy do svých zařízení chcete nasadit vlastní kód. Azure IoT Edge se drží stejného programovacího modelu jako ostatní služby Azure IoT. Stejný kód je možné spouštět v zařízení nebo v cloudu. Azure IoT Edge podporuje Linux i Windows, takže můžete psát kód pro platformu podle svého výběru. Podporuje jazyky Java, .NET Core 2.0, Node.js, C a Python, takže vaši vývojáři můžou psát kód v jazyce, který již znají, a používat existující obchodní logiku, aniž by ji museli psát od začátku.

## <a name="iot-edge-runtime"></a>Modul runtime IoT Edge

Modul runtime Azure IoT Edge umožňuje používat na hraničních zařízeních IoT vlastní a cloudovou logiku. Nachází se v hraničním zařízení IoT a provádí operace správy a komunikace. Modul runtime provádí několik funkcí:

* Instaluje a aktualizuje na zařízení úlohy.
* Udržuje na zařízení standardy zabezpečení Azure IoT Edge.
* Zajišťuje nepřetržitý provoz modulů IoT Edge.
* Hlásí do cloudu stav modulů pro účely vzdáleného monitorování.
* Usnadňuje komunikaci mezi podřízenými zařízeními typu list a příslušným hraničním zařízením IoT.
* Usnadňuje komunikaci mezi moduly v příslušném hraničním zařízení IoT.
* Usnadňuje komunikaci mezi příslušným hraničním zařízením IoT a cloudem.

![Modul runtime IoT Edge odesílá přehledy a sestavy do služby IoT Hub.][1]

Jak budete používat hraniční zařízení Azure IoT je zcela na vás. Modul runtime se často používá k nasazování AI do bran, které agregují a zpracovávají data z několika místních zařízení, to je však pouze jedna z možností. Zařízení typu list také můžou být hraničními zařízeními Azure IoT bez ohledu na to, jestli jsou připojená k bráně nebo přímo ke cloudu.

Modul runtime Azure IoT Edge běží ve velké sadě zařízení IoT a díky tomu jej můžete používat nejrůznějšími způsoby. Podporuje operační systémy Linux i Windows a také abstraktní podrobnosti o hardwaru. Pokud nezpracováváte velký objem dat, můžete použít menší zařízení než Raspberry Pi 3, a pokud spouštíte úlohy náročné na prostředky, můžete vertikálně navýšit kapacitu na průmyslový server.

## <a name="iot-edge-cloud-interface"></a>Cloudové rozhraní IoT Edge

Správa životního cyklu softwaru u podnikových zařízení je složitá. Správa životního cyklu softwaru u milionů různorodých zařízení IoT je ještě složitější. Úlohy se musí vytvářet a konfigurovat pro konkrétní typ zařízení, nasazovat ve velkém měřítku na miliony zařízení ve vašem řešení a monitorovat, aby se případně zachytila zařízení, která se nechovají podle očekávání. Tyto aktivity není možné provádět pro každé zařízení zvlášť a musí se provádět ve velkém měřítku.

Azure IoT Edge se bezproblémově integruje se sadou Azure IoT Suite a poskytuje tak jedinou rovinu řízení pro všechny požadavky vašeho řešení. Cloudové služby umožňují uživatelům:

* Vytvořit a nakonfigurovat úlohu, která se má spouštět na konkrétním typu zařízení.
* Odeslat úlohu do sady zařízení.
* Monitorovat úlohy spuštěné na zařízeních v poli.

![Telemetrie, přehledy a akce zařízení se koordinují s cloudem][2]

## <a name="next-steps"></a>Další kroky

Vyzkoušejte si tyto koncepty při [nasazení IoT Edge na simulovaném zařízení][lnk-quickstart].

<!-- Images -->
[1]: ./media/how-iot-edge-works/runtime.png
[2]: ./media/how-iot-edge-works/cloud-interface.png

<!-- Links -->
[lnk-quickstart]: quickstart.md
