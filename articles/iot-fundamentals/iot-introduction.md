---
title: Úvod do Azure IoT (Internet věcí)
description: Přehled Azure IoT a souvisejících služeb a technologií
services: iot-hub
documentationcenter: ''
author: BryanLa
manager: timlt
editor: bryanla
ms.service: iot
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: bryanla; v-clay
layout: LandingPage
ms.openlocfilehash: 183925324c404b412b5d12c3a9bff93ccd842535
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="introduction-to-azure-and-the-internet-of-things"></a>Úvod do Azure a Internetu věcí

Azure IoT se skládá ze tří oblastí technologií a řešení – řešení, služeb platforem a hraničních zařízení. Vše je navrženo tak, aby usnadnilo kompletní vývoj vašich aplikací IoT. Tento článek začíná popisem běžných vlastností řešení IoT v cloudu, pokračuje přehledem toho, jak Azure IoT řeší výzvy v projektech IoT a proč byste měli přijetí Azure IoT zvážit.

## <a name="iot-solution-architecture"></a>Architektura řešení IoT

Řešení IoT vyžadují bezpečnou obousměrnou komunikaci mezi zařízeními, jejichž počet se může vyšplhat k milionům, a back-end řešení. Řešení například může pomocí automatizované prediktivní analýzy odhalit další poznatky z datového proudu událostí ze zařízení do cloudu. 

Následující diagram ukazuje klíčové prvky typické architektury řešení IoT. V tomto diagramu se nerozlišují podrobnosti konkrétní implementace, například použité služby Azure a operační systémy zařízení. V této architektuře zařízení IoT shromažďují data, která odesílají do cloudové brány. Cloudová brána zpřístupňuje data pro zpracování dalšími back-endovými službami. Tyto back-endové služby umožňují doručovat data pro:

* Další podnikové aplikace
* Lidskou obsluhu prostřednictvím řídicího panelu nebo jiného prezentačního zařízení

![Architektura řešení IoT][img-solution-architecture]

> [!NOTE]
> Podrobné informace o architektuře IoT najdete v článku [Referenční architektura IoT v systému Microsoft Azure][lnk-refarch].

### <a name="device-connectivity"></a>Připojení zařízení

V architektuře řešení IoT zařízení obvykle odesílají telemetrii do cloudu pro uložení a zpracování. Ve scénáři prediktivní údržby může back-end řešení například použít datový proud s daty ze snímačů k určení, kdy konkrétní čerpadlo vyžaduje údržbu. Zařízení může také přijímat a reagovat na zprávy typu cloud-zařízení tak, že si přečte zprávy z koncového bodu cloudu. Ve stejném příkladu může back-end řešení odesílat zprávy jiným čerpadlům čerpací stanice, aby těsně před plánovaným začátkem údržby přesměrovala toky. Tento postup umožní pracovníkovi údržby začít s prací hned, jak dorazí na místo.

Největším problémem řešení IoT často je bezpečné a spolehlivé připojení zařízení. Důvodem je to, že zařízení IoT mají se srovnání s různými klienty, například s prohlížeči a mobilními aplikacemi, jiné vlastnosti. Konkrétně zařízení IoT:

* Jsou často integrované systémy bez lidské obsluhy (na rozdíl od telefonu).
* Mohou být nasazená ve vzdálených umístěních, kam je fyzický přístup nákladný.
* Můžou být dostupná jenom prostřednictvím back-endu řešení. Neexistuje jiný způsob práce se zařízením.
* Můžou mít omezené prostředky pro napájení a zpracování.
* Můžou mít přerušované, pomalé nebo nákladné síťové připojení.
* Můžou potřebovat chráněné, vlastní nebo průmyslové protokoly aplikací.
* Můžou být vytvořená pomocí rozsáhlé sady oblíbených hardwarových a softwarových platforem.

Kromě předchozích omezení musí libovolné řešení IoT také být škálovatelné, zabezpečené a spolehlivé.

V závislosti na komunikačním protokolu a síťové dostupnosti může zařízení komunikovat s cloudem buď přímo, nebo prostřednictvím zprostředkující brány. Architektury IoT často využívají kombinaci těchto dvou komunikačních schémat.

### <a name="data-processing-and-analytics"></a>Zpracování a analýza dat

V moderních řešeních IoT může ke zpracování dat docházet v cloudu nebo na straně zařízení. Zpracování na straně zařízení se označuje jako *edge computing*. Volba umístění pro zpracování dat závisí na faktorech, jako jsou:

* Omezení sítě. Pokud šířka pásma mezi zařízeními a cloudem je omezená, je vhodné soustředit se víc na zpracování hraničních zařízení.
* Doba odezvy. Pokud se požadují akce v zařízení téměř v reálném čase, může být vhodnější zpracovat odpověď přímo v příslušném zařízení. Příkladem může být rameno robota, které je v nouzovém stavu nutné zastavit.
* Právní prostředí. Některá data nelze odeslat do cloudu.

Obecně platí, že zpracování dat v hraničních zařízeních i v cloudu představuje kombinaci následující možnosti:

* Příjem škálované telemetrie ze všech zařízení a určení, jak tato data zpracovávat a ukládat
* Analýza telemetrie s cílem poskytnout přehled, ať už v reálném čase, nebo dodatečně
* Odesílání příkazů z cloudu nebo zařízení brány do konkrétního zařízení

Kromě toho by měl back-end cloudu IoT poskytovat:

* Možnosti registrace zařízení, které vám umožní:
    * Zřizování zařízení
    * Kontroly, která zařízení mají oprávnění připojit se k vaší infrastruktuře
* Správu zařízení umožňující kontrolovat stav zařízení a sledovat jejich aktivity.

Například ve scénáři prediktivní údržby back-end cloudu ukládá historická telemetrická data. Toto řešení používá tato data k identifikaci potenciálně neobvyklého chování konkrétních čerpadel dřív, než způsobí skutečný problém. Pomocí analýzy dat může zjistit, že vhodnou prevencí je odeslat příkaz zpět do zařízení a provést nápravnou akci. Tento proces generuje automatizovanou smyčku zpětné vazby mezi zařízením a cloudem, která výrazně zvyšuje efektivitu řešení.

### <a name="presentation-and-business-connectivity"></a>Prezentační a obchodní připojení

Vrstva prezentačního a obchodního připojení umožňuje koncovým uživatelům pracovat s řešením IoT a se zařízeními. Umožňuje uživatelům zobrazit a analyzovat data shromážděná z jejich zařízení. Tato zobrazení můžou mít podobu řídicích panelů nebo sestav BI, které můžou zobrazit historická data i data téměř v reálném čase. Obsluha může například zkontrolovat stav konkrétní čerpací stanice a zobrazit všechny výstrahy vyvolané systémem. Tato vrstva také umožňuje integraci back-endu řešení IoT se stávajícími obchodními aplikacemi a jejich zapojení do podnikových obchodních procesů nebo pracovních postupů. Řešení prediktivní údržby se může integrovat například s plánovacím systémem, který zarezervuje návštěvu technika na čerpací stanici v případě, kdy se zjistí, že některé čerpadlo potřebuje údržbu.

[img-solution-architecture]: ./media/iot-introduction/iot-reference-architecture.png
[img-dashboard]: ./media/iot-introduction/iot-suite.png

[lnk-iot-hub]: ../articles/iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: ../articles/iot-suite/iot-suite-overview.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf


## <a name="why-azure-iot"></a>Proč Azure IoT?

Azure IoT zjednodušuje složité projekty IoT a řeší takové výzvy, jako je zabezpečení, nekompatibilita infrastruktury a škálování řešení IoT. A jak to dělá:

**Agilita** <br>
Urychlete svoji cestu k IoT
* Škálování: Začněte v malém, můžete se rozrůst na jakoukoli velikost, kdekoli a kdykoli – milióny zařízení, terabajty dat, ve většině regionů po celém světě.

* Otevřenost: Používejte to, co máte, nebo se modernizujte. Připojte se k libovolnému zařízení, softwaru nebo službě.

* Hybridnost: Provádějte sestavení podle vašich potřeb, nasazujte řešení IoT na hraniční zařízení, v cloudu nebo kdekoli mezi.

* Rychlost: Rychlejší nasazení, rychlé uvedení na trh a předběhnutí konkurence se špičkou v oblasti akcelerátorů řešení a rychlosti inovací v IoT.

**Komplexnost** <br>
Dopad na vaše podnikání

* Úplnost: Microsoft je jediným poskytovatelem řešení IoT se škálováním v oblasti zařízení na cloud, velkých objemů dat, pokročilé analýzy a spravovaných služeb pro všechny platformy.

* Partner, který vám zajistí úspěch: Využijte sílu největšího partnerského ekosystému na světě a oživte podnikání i technologii napříč odvětvím i po celém světě.

* Řízení daty: IoT je o datech a ta nejlepší řešení IoT spojují dohromady všechny nástroje, které potřebujete k ukládání, interpretaci, transformaci, analýze a prezentaci dat těm správným uživatelům, na správném místě, ve správný čas.

* Zaměření na zařízení: IoT od Microsoftu vám umožňuje připojit cokoli, od staršího zařízení po široký ekosystém certifikovaného hardwaru, a dává vám možnost sestavit svá vlastní zařízení napříč hraničními, mobilními a vloženými systémy.

**Zabezpečení** <br>
Vyřešte tu nejsložitější část IoT – zabezpečení

* Posílení: S IoT od Microsoftu můžete spojit svou vizi s technologií, osvědčenými postupy a možnostmi, které vám umožní vyřešit tu nejobtížnější část IoT – zabezpečení.

* Akce: Zabezpečte svá data IoT a spravujte rizika pomocí správy identit a přístupu, ochranou před ohrožením a ochranou informací a správou zabezpečení.

* Jistota: Zabezpečte citlivé informace napříč zařízeními, softwarem, aplikacemi, cloudovými službami a také v místních prostředích.

* Dodržování předpisů: Microsoft je přední společností v oblasti vytváření požadavků na zabezpečení, které odpovídají široké škále mezinárodních standardů a standardů specifických pro odvětví ohledně zařízení, dat a služeb IoT.

## <a name="next-steps"></a>Další kroky

Prozkoumejte následující oblasti technologií a řešení nebo se podívejte na obsah vlevo, kde najdete seznam služeb Azure IoT.

<ul class="panelContent cardsF">  
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Řešení</h3>
                        <a href="/azure/iot-suite">IoT Suite</a><br/>
                        <a href="/azure/iot-central">IoT Central</a>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Služby platformy</h3>
                        <a href="/azure/iot-hub">IoT Hub</a><br/>
                        <a href="/azure/iot-dps">Služba IoT Hub Device Provisioning</a><br/>
                        <a href="/azure/azure-maps">Maps</a><br/>
                        <a href="/azure/time-series-insights">Time Series Insights</a>
                    </div>
                </div>
            </div>
        </div>
    </li>  
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Edge</h3>
                        <a href="/azure/iot-edge">IoT Edge</a><br/>
                        <a href="/azure/iot-edge/how-iot-edge-works">Co je IoT Edge?</a>
                    </div>
                </div>
            </div>
        </div>
    </li>      
</ul>

[img-paas-saas-technologies-solutions]: media/index/paas-saas-technologies-solutions.png

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-iot-central-land]: https://docs.microsoft.com/microsoft-iot-central/
[lnk-iot-dps-land]: /azure/iot-dps/index.yml
[lnk-iot-edge-land]: /azure/iot-edge/index.yml
[lnk-iot-hub-land]: /azure/iot-hub/index.md
[lnk-iot-maps-land]: /azure/azure-maps/index.yml
[lnk-iot-suite-land]: /azure/iot-suite/index.md
[lnk-iot-tsi-land]: /azure/time-series-insights/index.yml


