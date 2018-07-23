---
title: zahrnout soubor
description: zahrnout soubor
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/25/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: d7497a0226f236cedc583de0a31013adcc2b830f
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188852"
---
# <a name="azure-and-the-internet-of-things"></a>Azure a internet věcí

Vítejte v tématu Microsoft Azure a internet věcí (IoT). Tento článek popisuje běžné charakteristiky řešení IoT v cloudu. Řešení IoT vyžadují bezpečnou obousměrnou komunikaci mezi zařízeními, jejichž počet se může vyšplhat k milionům, a back-end řešení. Řešení například může pomocí automatizované prediktivní analýzy odhalit další poznatky z datového proudu událostí ze zařízení do cloudu.

## <a name="iot-solution-architecture"></a>Architektura řešení IoT

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

Vrstva prezentačního a obchodního připojení umožňuje koncovým uživatelům pracovat s řešením IoT a se zařízeními. Umožňuje uživatelům zobrazit a analyzovat data shromážděná z jejich zařízení. Tato zobrazení můžou mít podobu řídicích panelů nebo sestav BI, které můžou zobrazit historická data i data téměř v reálném čase. Obsluha může například zkontrolovat stav konkrétní čerpací stanice a zobrazit všechny výstrahy vyvolané systémem. Tato vrstva také umožňuje integraci back-endu řešení IoT se stávajícími obchodními aplikacemi a jejich zapojení do podnikových obchodních procesů nebo pracovních postupů. Akcelerátor řešení Prediktivní údržba se může integrovat například s plánovacím systémem, který zarezervuje návštěvu technika na čerpací stanici v případě, kdy se zjistí, že některé čerpadlo potřebuje údržbu.

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-iot-hub]: ../articles/iot-hub/about-iot-hub.md
[lnk-iot-suite]: ../articles/iot-accelerators/about-iot-accelerators.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT solution accelerators]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
