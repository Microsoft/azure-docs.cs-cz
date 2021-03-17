---
title: Seznámení se službou Azure IoT Hub | Microsoft Docs
description: Seznamte se se službou Azure IoT Hub. Tato služba IoT je určená ke škálovatelnému příjmu dat, správě zařízení a zajištění zabezpečení.
author: nberdy
ms.author: nberdy
ms.date: 08/08/2019
ms.topic: overview
ms.custom:
- mvc
- amqp
- mqtt
- 'role: Direction'
- 'role: System Architecture'
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 86a373844b370cc9f9ce31dc65b2039a81279803
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454766"
---
# <a name="what-is-azure-iot-hub"></a>Co je Azure IoT Hub?

IoT Hub je spravovaná služba hostovaná v cloudu, která funguje jako centrum zpráv pro obousměrnou komunikaci mezi aplikací IoT a zařízeními, která spravuje. Pomocí služby Azure IoT Hub můžete vytvářet řešení IoT umožňující spolehlivou a zabezpečenou komunikací mezi miliony zařízeními IoT a back-endem řešení hostovaným v cloudu. Ke službě IoT Hub můžete připojit prakticky jakékoli zařízení.

IoT Hub podporuje komunikaci ve směru zařízení-cloud i cloud-zařízení. IoT Hub podporuje několik způsobů zasílání zpráv, například zasílání telemetrických dat typu zařízení-cloud, nahrávání souborů ze zařízení a metody požadavek-odpověď, kterými můžete ovládat svá zařízení z cloudu. Monitorování služby IoT Hub vám pomůže udržovat dobrý stav vašeho řešení díky sledování událostí, jako jsou vytvoření zařízení, selhání zařízení nebo připojení zařízení.

Možnosti služby IoT Hub vám pomůžou vytvářet škálovatelná řešení IoT se všemi funkcemi například pro správu průmyslových zařízení používaných ve výrobě, sledování cenností ve zdravotnictví nebo monitorování využití kancelářské budovy.

## <a name="scale-your-solution"></a>Škálování vlastního řešení

IoT Hub zajišťuje podporu vašich úloh IoT díky škálování na miliony současně připojených zařízení a miliony událostí za sekundu. Další informace o škálování IoT Hub najdete v tématu [IoT Hub škálování](iot-hub-scaling.md). Pokud chcete získat další informace o různých úrovních služby, které nabízí IoT Hub a co nejlépe vyhovuje vašim potřebám škálovatelnosti, podívejte se na [stránku s cenami](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="secure-your-communications"></a>Zabezpečení komunikace

IoT Hub poskytuje zabezpečený komunikační kanál, přes který vaše zařízení můžou odesílat data.

* Ověřování podle zařízení umožňuje zabezpečené připojení každého zařízení ke službě IoT Hub a zabezpečenou správu jednotlivých zařízení.

* Máte plnou kontrolu nad přístupem zařízení a můžete řídit připojení na úrovni jednotlivých zařízení.

* Služba [IoT Hub Device Provisioning](../iot-dps/index.yml) automaticky zřizuje zařízení pro správné centrum IoT při jejich prvním spuštění.

* Několik typů ověřování podporuje různé možnosti zařízení:

  * Ověřování založené na tokenech SAS umožňuje rychle začít s řešením IoT.

  * Ověřování jednotlivých certifikátů X.509 zajišťuje zabezpečené ověřování založené na standardech.

  * Ověřování certifikační autority X.509 umožňuje jednoduchou registraci založenou na standardech.

## <a name="route-device-data"></a>Směrování dat zařízení

Integrovaná funkce směrování zpráv poskytuje flexibilitu umožňující nastavení automatického odesílání zpráv založeného na pravidlech:

* [Směrování zpráv](iot-hub-devguide-messages-d2c.md) slouží k určení, kam váš rozbočovač odesílá telemetrii zařízení.

* Za směrování zpráv do více koncových bodů se neúčtují žádné další poplatky.

* Pravidla směrování bez kód nahrazují vlastní kód dispečera zpráv.

## <a name="integrate-with-other-services"></a>Integrace s ostatními službami

Službu IoT Hub můžete integrovat s ostatními službami Azure a vytvářet tak ucelená a komplexní řešení. Například můžete použít:

* Službu [Azure Event Grid](../event-grid/index.yml), která vaší firmě umožní rychle reagovat na kritické události, a to spolehlivým, škálovatelným a zabezpečeným způsobem.

* Službu [Azure Logic Apps](../logic-apps/index.yml) umožňující automatizaci firemních procesů.

* Službu [Azure Machine Learning](iot-hub-weather-forecast-machine-learning.md) umožňující přidání modelů AI a strojového učení do vašich řešení.

* Službu [Azure Stream Analytics](../stream-analytics/index.yml) umožňující spouštění analytických výpočtů v reálném čase s daty streamovanými z vašich zařízení.

## <a name="configure-and-control-your-devices"></a>Konfigurace a řízení zařízení

Pomocí sady integrovaných funkcí můžete spravovat zařízení připojená ke službě IoT Hub.

* Můžete ukládat, synchronizovat a dotazovat se na metadata a stav všech vašich zařízení.

* Stav zařízení můžete nastavit buď pro jednotlivá zařízení, nebo na základě společných charakteristik zařízení.

* Díky integraci směrování zpráv můžete automaticky reagovat na změnu hlášeného stavu zařízení.

## <a name="make-your-solution-highly-available"></a>Zajištění vysoké dostupnosti řešení

[Pro službu IoT Hub je k dispozici smlouva o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/iot-hub/) zajišťující 99,9% dostupnost. Úplná smlouva [Azure SLA](https://azure.microsoft.com/support/legal/sla/) vysvětluje garantovanou dostupnost Azure jako celku.

## <a name="connect-your-devices"></a>Připojení zařízení

Pomocí knihoven ze sady [SDK pro zařízení Azure IoT](./iot-hub-devguide-sdks.md) můžete vytvářet aplikace, které se spouští na vašich zařízeních a komunikují se službou IoT Hub. Mezi podporované platformy patří několik distribucí Linuxu, Windows a operační systémy pracující v reálném čase. Mezi podporované jazyky patří:

* C
* Vložené C
* C#
* Java
* Python
* Node.js

IoT Hub a sady SDK pro zařízení podporují připojení zařízení pomocí následujících protokolů:

* HTTPS
* AMQP
* AMQP přes WebSockets
* MQTT
* MQTT přes WebSockets

IoT Hub a sady SDK pro zařízení podporují konvence [technologie Plug and Play Azure IoT](../iot-pnp/overview-iot-plug-and-play.md) pro připojení zařízení. Zařízení IoT technologie Plug and Play používají model zařízení k inzerování svých schopností aplikacím s podporou IoT technologie Plug and Play. Model zařízení umožňuje tvůrcům řešení integrovat inteligentní zařízení s jejich řešeními bez jakékoli ruční konfigurace.

Pokud vaše řešení nemůže používat knihovny zařízení, můžou se zařízení připojovat k vašemu centru nativně pomocí protokolů MQTT v3.1.1, HTTPS 1.1 nebo AMQP 1.0.

Pokud vaše řešení nemůže používat některý z podporovaných protokolů, můžete službu IoT Hub rozšířit o podporu vlastních protokolů:

* Pomocí služby [Azure IoT Edge](../iot-edge/index.yml) můžete vytvořit hraniční bránu, která bude provádět převody protokolů na hraničních zařízeních.

* [Protokolovou bránu Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) můžete přizpůsobit tak, aby prováděla převody protokolů v cloudu.

## <a name="quotas-and-limits"></a>Kvóty a omezení

Pro každé předplatné Azure platí výchozí omezení kvót, která brání zneužití služeb, a tato omezení můžou ovlivnit dosah vašeho řešení IoT. Aktuální limit pro každé předplatné je 50 centra IoT na předplatné. O navýšení kvóty můžete požádat kontaktováním podpory. Další informace najdete v tématu [IoT Hub kvóty a omezování](iot-hub-devguide-quotas-throttling.md). Další podrobnosti o limitech kvót najdete v jednom z následujících článků:

* [Omezení služby předplatného Azure](../azure-resource-manager/management/azure-subscription-service-limits.md)

* [Omezování služby IoT Hub a vy](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)

## <a name="iot-hub-on-azure-stack-hub-preview"></a>IoT Hub v Azure Stack Hubu (Preview)

IoT Hub v centru Azure Stack (Preview) můžete vytvářet hybridní řešení IoT. IoT Hub je spravovaná služba, která slouží jako centrální Centrum zpráv pro obousměrnou komunikaci mezi aplikací IoT a zařízeními, která spravuje. Pomocí IoT Hub v centru Azure Stack můžete vytvářet řešení IoT s spolehlivou a zabezpečenou komunikací mezi zařízeními IoT a místními řešeními.

IoT Hub v centru Azure Stack je ve verzi Public Preview zdarma. Další informace najdete v tématu [přehled IoT Hub v centru Azure Stack](/azure-stack/operator/iot-hub-rp-overview).

## <a name="next-steps"></a>Další kroky

Pokud chcete vyzkoušet komplexní řešení IoT, projděte si rychlé starty pro službu IoT Hub:

* [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-node.md)

Další informace o tom, jak můžete vytvářet a nasazovat řešení IoT pomocí Azure IoT, najdete tady:

* [Základy: technologie a řešení Azure IoT](../iot-fundamentals/iot-services-and-technologies.md).