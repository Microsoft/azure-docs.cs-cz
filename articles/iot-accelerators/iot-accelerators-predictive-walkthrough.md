---
title: Přehled akcelerátoru řešení prediktivní údržby – Azure | Microsoft Docs
description: Přehled akcelerátoru řešení prediktivní údržby Azure IoT, který předpovídá bod, ve kterém se může při obchodním scénáři vyskytnout selhání.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 0661503dce7ac2707065f60c3952da866ce9dcf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "73827417"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>Přehled akcelerátoru řešení prediktivní údržby

Akcelerátor řešení prediktivní údržby je uceleným řešením pro podnikový scénář, které se pokouší předvídat bod, ve kterém pravděpodobně nastane chyba. Tento akcelerátor řešení můžete aktivně využívat pro různé činnosti, jako je třeba optimalizace údržby. Řešení kombinuje klíčové služby pro řešení Azure IoT, jako je například IoT Hub a pracovní prostor [Azure Machine Learning][lnk-machine-learning] . Tento pracovní prostor obsahuje model založený na veřejné ukázkové sadě dat, který předpovídá zbývající dobu životnosti (RUL) leteckého motoru. Řešení nabízí úplnou implementaci daného obchodního scénáře IoT jako výchozího bodu pro plánování a implementaci řešení, které vyhovuje vašim konkrétním obchodním požadavkům.

Kód akcelerátoru řešení prediktivní údržby [je k dispozici na GitHubu](https://github.com/Azure/azure-iot-predictive-maintenance).

## <a name="logical-architecture"></a>Logická architektura

Následující diagram popisuje logické součásti tohoto akcelerátoru řešení:

![Logická architektura][img-architecture]

Modré položky jsou služby Azure zřízené v oblasti, kam jste nasadili akcelerátor řešení. Seznam oblastí, do kterých můžete nasadit akcelerátor řešení, najdete na [stránce zřizování][lnk-azureiotsolutions].

Zelená položka je simulovaný modul pro letadlo. Další informace o těchto simulovaných zařízeních najdete v části [Simulovaná zařízení](#simulated-devices).

Šedé položky jsou komponenty, které implementují funkce *správy zařízení* . Aktuální verze akcelerátoru řešení prediktivní údržby tyto prostředky neposkytuje. Další informace o správě zařízení najdete v [akcelerátoru řešení vzdáleného monitorování][lnk-remote-monitoring].

## <a name="azure-resources"></a>Prostředky Azure

Na webu Azure Portal přejděte do skupiny prostředků s názvem řešení, které jste si vybrali k zobrazení zřízených prostředků.

![Prostředky akcelerátoru][img-resource-group]

Při zřizování akcelerátoru řešení obdržíte e-mail s odkazem na pracovní prostor Machine Learning. Můžete také přejít na Machine Learning pracovní prostor ze stránky [Microsoft Azure akcelerátory řešení IoT][lnk-azureiotsolutions] . Na této stránce je k dispozici dlaždice v případě, že je řešení ve stavu **Připraveno**.

![Model strojového učení][img-machine-learning]

## <a name="simulated-devices"></a>Simulovaná zařízení

V akcelerátoru řešení je simulované zařízení modulem letadla. Řešení obsahuje dva motory, které jsou součástí jednoho letadla. Každý motor vysílá čtyři typy telemetrických dat: ze snímačů Sensor 9, Sensor 11, Sensor 14 a Sensor 15, které poskytují data potřebná k tomu, aby mohl model Machine Learning vypočítat zbývající dobu životnosti pro tento motor. Každé simulované zařízení posílá do služby IoT Hub následující telemetrické zprávy:

*Počet cyklů*. Cyklus je dokončený let s dobou trvání mezi dvěma a deseti hodinami. Během letu se telemetrická data zaznamenávají každou půlhodinu.

*Telemetrie*. Existují čtyři senzory, které zaznamenávají atributy modulu. Snímače jsou obecně označeny jako Sensor 9, Sensor 11, Sensor 14 a Sensor 15. Tyto čtyři snímače odesílají telemetrii dostačující pro získání užitečných výsledků z modelu RUL. Model použitý v akcelerátoru řešení je vytvořený z veřejné sady dat, obsahující data snímačů skutečného motoru. Další informace o způsobu vytvoření modelu z původní sady dat najdete v tématu [Šablona prediktivní údržby v galerii Cortana Intelligence][lnk-cortana-analytics].

Simulovaná zařízení mohou v řešení zpracovávat následující příkazy odeslané ze služby IoT Hub:

| Příkaz | Popis |
| --- | --- |
| StartTelemetry |Řídí stav simulace.<br/>Spustí odesílání telemetrických dat ze zařízení |
| StopTelemetry |Řídí stav simulace.<br/>Zastaví odesílání telemetrických dat ze zařízení |

Služba IoT Hub zajišťuje potvrzení příkazu zařízení.

## <a name="azure-stream-analytics-job"></a>Úlohy služby Azure Stream Analytics

**Úloha: telemetrie** pracuje na datovém proudu telemetrie příchozích zařízení pomocí dvou příkazů:

* První vybere veškerá telemetrická data ze zařízení a odešle je do úložiště objektů blob. Tady je vizuál ve webové aplikaci.
* Druhý vypočítá průměrné hodnoty snímačů v rámci dvouminutového posuvného okna a odešle je prostřednictvím centra událostí do **procesoru událostí**.

## <a name="event-processor"></a>Procesor událostí
**Hostitel procesoru událostí** běží ve webové úloze Azure. **Procesor událostí** přebírá průměrné hodnoty snímačů za dokončený cyklus. Pak tyto hodnoty předá do trained model, který vypočítá RUL pro modul. Rozhraní API poskytuje přístup k modelu v pracovním prostoru Machine Learning, který je součástí řešení.

## <a name="machine-learning"></a>Machine Learning
Součást Machine Learning používá model odvozený z dat shromážděných z reálných leteckých motorů. Do pracovního prostoru Machine Learning můžete přejít na dlaždici vašeho řešení na stránce [azureiotsolutions.com][lnk-azureiotsolutions] . Tato dlaždice je k dispozici v případě, že je řešení ve stavu **Připraveno**.

Model Machine Learning je k dispozici jako šablona, která ukazuje, jak pracovat se telemetrie shromážděnými prostřednictvím služeb akcelerátoru řešení IoT. Společnost Microsoft vytvořila [regresní model][lnk_regression_model] leteckého motoru na základě veřejně dostupných dat<sup> \[ 1 \] </sup>a podrobné pokyny k použití modelu.

Akcelerátor řešení prediktivní údržby Azure IoT používá regresní model vytvořený z této šablony. Model se nasadí do vašeho předplatného Azure a zpřístupní se prostřednictvím automaticky generovaného rozhraní API. Řešení zahrnuje podmnožinu testovacích dat pro 4 (z celkem 100) modulů a 4 (z celkem 21) datových proudů senzorů. Tato data jsou dostatečná pro poskytování přesných výsledků z trénovaného modelu.

*\[1 a \] . Saxena a K. Goebel (2008). "Sada dat simulace degradace modulu Turbofan", úložiště dat NASA Ames Prognostics ( https://c3.nasa.gov/dashlink/resources/139/) , NASA Ames Research Center, Moffett Field pole, CA*

## <a name="next-steps"></a>Další kroky
Když jste se seznámili s klíčovými součástmi akcelerátoru řešení prediktivní údržby, můžete si jej přizpůsobit.

Můžete si také projít některé z dalších funkcí akcelerátorů řešení IoT:

* [Nejčastější dotazy k akcelerátorům řešení IoT][lnk-faq]
* [Zabezpečení IoT od počátku][lnk-security-groundup]

[img-architecture]: media/iot-accelerators-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-accelerators-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-accelerators-predictive-walkthrough/machine-learning.png

[lnk-remote-monitoring]: quickstart-predictive-maintenance-deploy.md
[lnk-cortana-analytics]: https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsolutions]: https://www.azureiotsolutions.com/
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_regression_model]: https://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
