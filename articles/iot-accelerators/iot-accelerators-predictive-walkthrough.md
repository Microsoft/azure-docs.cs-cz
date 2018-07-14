---
title: Přehled akcelerátoru řešení prediktivní údržby – Azure | Microsoft Docs
description: Přehled akcelerátorů řešení prediktivní údržby Azure IoT.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: dobett
ms.openlocfilehash: e7c6c8d017e4371919088ec414d3108939ca4a19
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036250"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>Přehled akcelerátoru řešení prediktivní údržby

Akcelerátor řešení prediktivní údržby je uceleným řešením pro podnikový scénář, které se pokouší předvídat bod, ve kterém pravděpodobně nastane chyba. Tento akcelerátor řešení můžete aktivně využívat pro různé činnosti, jako je třeba optimalizace údržby. Řešení kombinuje klíčové služby akcelerátorů řešení Azure IoT, jako je služba IoT Hub, Stream Analytics a pracovní prostor [Azure Machine Learning][lnk-machine-learning]. Tento pracovní prostor obsahuje model založený na veřejné ukázkové sadě dat, který předpovídá zbývající dobu životnosti (RUL) leteckého motoru. Řešení nabízí úplnou implementaci daného obchodního scénáře IoT jako výchozího bodu pro plánování a implementaci řešení, které vyhovuje vašim konkrétním obchodním požadavkům.

## <a name="logical-architecture"></a>Logická architektura

Následující diagram popisuje logické součásti tohoto akcelerátoru řešení:

![][img-architecture]

Modré položky jsou služby Azure zřízené v oblasti, kam jste nasadili akcelerátor řešení. Seznam oblastí, do kterých můžete nasadit akcelerátor řešení, najdete na [stránce zřizování][lnk-azureiotsuite].

Zelená položka je simulované zařízení, které představuje letecký motor. Další informace o těchto simulovaných zařízeních najdete v části [Simulovaná zařízení](#simulated-devices).

Šedé položky představují součásti, které implementují funkce *správy zařízení*. Aktuální verze akcelerátoru řešení prediktivní údržby tyto prostředky neposkytuje. Další informace o správě zařízení, najdete v tématu [akcelerátor řešení vzdálené monitorování][lnk-remote-monitoring].

## <a name="azure-resources"></a>Prostředky Azure

Na webu Azure Portal přejděte do skupiny prostředků s názvem řešení, které jste si vybrali k zobrazení zřízených prostředků.

![Prostředky akcelerátoru][img-resource-group]

Při zřizování akcelerátoru řešení obdržíte e-mail s odkazem na pracovní prostor Machine Learning. Můžete také přejít do pracovního prostoru Machine Learning z [akcelerátory řešení IoT Microsoft Azure] [ lnk-azureiotsuite] stránky zřízeného řešení. Na této stránce je k dispozici dlaždice v případě, že je řešení ve stavu **Připraveno**.

![Model strojového učení][img-machine-learning]

## <a name="simulated-devices"></a>Simulovaná zařízení

V akcelerátoru řešení představuje simulované zařízení letecký motor. Řešení obsahuje dva motory, které jsou součástí jednoho letadla. Každý motor vysílá čtyři typy telemetrických dat: ze snímačů Sensor 9, Sensor 11, Sensor 14 a Sensor 15, které poskytují data potřebná k tomu, aby mohl model Machine Learning vypočítat zbývající dobu životnosti pro tento motor. Každé simulované zařízení posílá do služby IoT Hub následující telemetrické zprávy:

*Počet cyklů*. Cyklus představuje dokončený let trvající 2 až 10 hodin. Během letu se telemetrická data zaznamenávají každou půlhodinu.

*Telemetrická data*. V řešení jsou čtyři snímače, které představují vlastnosti motoru. Snímače jsou obecně označeny jako Sensor 9, Sensor 11, Sensor 14 a Sensor 15. Tyto čtyři snímače představují dostatek telemetrických dat pro získání užitečných výsledků z modelu zbývající doby životnosti. Model použitý v akcelerátoru řešení je vytvořený z veřejné sady dat, obsahující data snímačů skutečného motoru. Další informace o způsobu vytvoření modelu z původní sady dat najdete v tématu [Šablona prediktivní údržby v galerii Cortana Intelligence][lnk-cortana-analytics].

Simulovaná zařízení mohou v řešení zpracovávat následující příkazy odeslané ze služby IoT Hub:

| Příkaz | Popis |
| --- | --- |
| StartTelemetry |Řídí stav simulace.<br/>Spustí odesílání telemetrických dat ze zařízení |
| StopTelemetry |Řídí stav simulace.<br/>Zastaví odesílání telemetrických dat ze zařízení |

Služba IoT Hub zajišťuje potvrzení příkazu zařízení.

## <a name="azure-stream-analytics-job"></a>Úlohy služby Azure Stream Analytics

**Úloha: Telemetrie** funguje v příchozím datovém proudu telemetrických dat ze zařízení pomocí dvou příkazů:

* První vybere veškerá telemetrická data ze zařízení a odešle je do úložiště objektů blob. Odtud se data vizualizují ve webové aplikaci.
* Druhý vypočítá průměrné hodnoty snímačů v rámci dvouminutového posuvného okna a odešle je prostřednictvím centra událostí do **procesoru událostí**.

## <a name="event-processor"></a>Procesor událostí
**Hostitel procesoru událostí** běží ve webové úloze Azure. **Procesor událostí** přebírá průměrné hodnoty snímačů za dokončený cyklus. Potom tyto hodnoty předá do rozhraní API, které zpřístupní trénovaný model pro výpočet zbývající doby životnosti motoru. Rozhraní API je zveřejněné pracovním prostorem Machine Learning zřízeným jako součást řešení.

## <a name="machine-learning"></a>Machine Learning
Součást Machine Learning používá model odvozený z dat shromážděných z reálných leteckých motorů. Do pracovního prostoru Machine Learning se můžete dostat z dlaždice vašeho řešení na stránce [azureiotsuite.com][lnk-azureiotsuite]. Tato dlaždice je k dispozici v případě, že je řešení ve stavu **Připraveno**.

Model Azure Machine Learning je dostupná jako šablonu, která ukazuje možnosti práce z telemetrických dat zařízení shromážděnými prostřednictvím služeb akcelerátory řešení IoT. Společnost Microsoft vytvořila [regresní model] [ lnk_regression_model] leteckého motoru na základě veřejně dostupných dat<sup>\[1\]</sup>a podrobné pokyny o tom, jak použít model.

Akcelerátor řešení prediktivní údržby Azure IoT používá regresní model vytvořený z této šablony. Model je nasazený do vašeho předplatného Azure a vystavený prostřednictvím automaticky generovaného rozhraní API. Řešení obsahuje podmnožinu testovacích dat, která představují 4 motory (z celkem 100) a 4 datové proudy ze snímačů (z celkem 21). Tato data jsou dostatečná pro poskytování přesných výsledků z trénovaného modelu.

*\[1\] A. Saxena and K. Goebel (2008). „Turbofan Engine Degradation Simulation Data Set“, datové úložiště NASA Ames Prognostics Data Repository (https://c3.nasa.gov/dashlink/resources/139/)), NASA Ames Research Center, Moffett Field, CA*

## <a name="next-steps"></a>Další postup
Když jste se seznámili s klíčovými součástmi akcelerátoru řešení prediktivní údržby, můžete si jej přizpůsobit.

Můžete si také prostudovat některé další funkce a možnosti akcelerátorů řešení IoT:

* [Nejčastější dotazy k akcelerátorům řešení IoT][lnk-faq]
* [Zabezpečení IoT od počátku][lnk-security-groundup]

[img-architecture]: media/iot-accelerators-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-accelerators-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-accelerators-predictive-walkthrough/machine-learning.png

[lnk-remote-monitoring]: quickstart-predictive-maintenance-deploy.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsolutions.com/
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3