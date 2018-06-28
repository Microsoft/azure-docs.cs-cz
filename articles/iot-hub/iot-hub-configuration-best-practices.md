---
title: Zařízení osvědčenými postupy konfigurace pro Azure IoT Hub | Microsoft Docs
description: Další informace o doporučených postupů pro konfiguraci zařízení IoT ve velkém měřítku
author: chrisgre
manager: briz
ms.author: chrisgre
ms.date: 06/24/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 7314c5ec05bec61e5bbbc406b6a39a7c5a8f011f
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035750"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Osvědčené postupy pro konfiguraci zařízení v rámci řešení IoT

Správa automatického zařízení v Azure IoT Hub automatizuje mnoho opakovaných a složitých úloh spojených se správou loďstev velké zařízení přes celého jejich životních cyklů. Tento článek definuje řadu osvědčené postupy pro jednotlivým rolím ve vývoji a provozování řešení IoT.

* **Výrobce hardwaru IoT/integrátor:** výrobců IoT hardwaru, ty se hardware od různých výrobců a dodavatelů poskytuje hardwaru pro nasazení služby IoT integrátorem pocházejí nebo integrovaný jinými dodavateli. Součástí vývoj a integrace firmwaru, vložených operačních systémů a embedded softwaru.
* **Vývojář řešení IoT:** vývoji řešení IoT se obvykle provádí vývojář řešení. Tento vývojář může být součástí interní tým nebo systémový integrátor specializované této aktivity. Vývojář řešení IoT můžete vyvíjet různé součásti řešení IoT od začátku, integrovat různé součásti standardní nebo open source nebo si přizpůsobit [akcelerátoru řešení IoT][lnk-solution].
* **Operátor řešení IoT:** nasazení po IoT řešení, vyžaduje dlouhodobé provoz, sledování, upgradu a údržby. Tyto úlohy lze provést interní tým, který se skládá z specialisty a informace o technologii, hardwarových operací a údržby týmů a odborníky domény, kteří monitorování správné chování celé infrastruktury IoT.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Pochopení automatické zařízení správy pro konfiguraci zařízení IoT ve velkém měřítku

Správa automatického zařízení zahrnuje mnoho výhod [dvojčata zařízení] [ lnk-device-twins] a [modulu dvojčata] [ lnk-module-twins] synchronizovat požadovaných a stavy ohlášené mezi cloudu a zařízení.  [Konfigurace automatického zařízení] [ lnk-auto-device-config] automaticky aktualizovat velkých sad dvojčata a summerize průběh a dodržování předpisů. Následující postup vysoké úrovně popisují, jak Automatická správa zařízení vyvíjí a použít:

* **IoT hardwaru výrobce nebo integrátor** implementuje funkce správy zařízení v rámci embedded aplikace pomocí [dvojčata zařízení][lnk-device-twins]. Tyto funkce můžou zahrnovat aktualizace firmwaru, instalace softwaru a aktualizace a správy nastavení.
* **Vývojáře řešení IoT** implementuje vrstva správy operací správy zařízení pomocí [dvojčata zařízení] [ lnk-device-twins] a [automatické zařízení konfigurace][lnk-auto-device-config]. Řešení by měly obsahovat definice operátor rozhraní k provádění úloh správy zařízení.
* **IoT řešení operátor** používá řešení IoT umožní provádět úlohy správy zařízení, zejména k seskupení zařízení společně, zahájení změny konfigurace, jako je aktualizace firmwaru, monitorovat průběh a řešení potíží s potíže, které nastat.

## <a name="iot-hardware-manufacturerintegrator"></a>Výrobce hardwaru IoT/integrátor

Následují osvědčené postupy pro výrobcům hardwaru a integrátorem zabývají vývoj embedded softwaru:

* **Implementace [dvojčata zařízení][lnk-device-twins]:** dvojčata zařízení povolit synchronizaci požadované konfigurace z cloudu a pro generování sestav vlastnosti aktuální konfigurace a zařízení.  Nejlepší způsob, jak implementovat dvojčata zařízení v rámci aplikací embedded je prostřednictvím [SDK služby Azure IoT][lnk-azure-sdk].  Dvojčata zařízení jsou nejvhodnější pro konfiguraci, protože jejich:. Podpora obousměrnou komunikaci b. Povolit pro obě c stavy zařízení připojené a odpojené. postupujte podle Princip konzistence typu případné d. jsou plně dotazovatelné v cloudu
* **Struktury dvojče zařízení pro správu zařízení:** dvojče zařízení by měly být navrženy tak, aby vlastnosti správy zařízení jsou logicky seskupeny dohromady na oddíly. Díky tomu vám umožní vyžadující izolaci bez dopadu na ostatní části twin změny konfigurace. Můžete například vytvořit oddíl v rámci požadované vlastnosti pro firmwaru, jinou část softwaru a třetí část nastavení sítě. 
* **Sestavy zařízení atributy, které jsou užitečné pro správu zařízení:** atributů, například fyzického zařízení značka a model, firmwaru, operačním systému, sériové číslo, a jsou užitečné pro vytváření sestav a jako parametry pro cílení na další identifikátory. změny konfigurace.
* **Definujte hlavní stavy pro vytváření sestav stavu a průběhu:** nejvyšší úrovně stavy by měl uvedené, takže mohou být oznámeny operátor. Aktualizace firmwaru by například sestavy stavu jako aktuální, stahování, Applying, probíhá a chyby.  Definujte další pole pro další informace pro každý stav.  

## <a name="iot-solution-developer"></a>Vývojář řešení IoT

Následují osvědčené postupy pro vývojáře řešení IoT, kteří jsou vytváření systémy založené na Azure:

* **Implementace [dvojčata zařízení][lnk-device-twins]:** dvojčata zařízení povolit synchronizaci požadované konfigurace z cloudu a pro generování sestav vlastnosti aktuální konfigurace a zařízení.  Nejlepší způsob, jak implementovat dvojčata zařízení v rámci cloudové řešení aplikace je prostřednictvím [SDK služby Azure IoT][lnk-azure-sdk].  Dvojčata zařízení jsou nejvhodnější pro konfiguraci, protože jejich:. Podpora obousměrnou komunikaci b. Povolit pro obě c stavy zařízení připojené a odpojené. postupujte podle Princip konzistence typu případné d. jsou plně dotazovatelné v cloudu
* **Umožňují uspořádat zařízení pomocí značek twin zařízení:** řešení by měl povolit operátor definovat kvality okruhy nebo jiné sady založené na různých strategie nasazení, jako je například Kanárských zařízení. Zařízení organizace se dají implementovat v rámci vašeho řešení pomocí značek twin zařízení a [dotazy][lnk-queries].  Zařízení organizace je potřeba povolit pro konfiguraci shrnutí výpisů bezpečně a přesně.
* **Implementace [konfigurací automatické zařízení][lnk-auto-device-config]:** konfigurací zařízení automatické nasazení a konfiguraci monitorování změní na velkých sad zařízení IoT pomocí dvojčata zařízení.  Konfigurace automatického zařízení cílové sady dvojčata zařízení pomocí **cíle podmínku,** která je dotaz na zařízení twin značky nebo hlášené vlastnosti. **Směrovat obsah** je sada požadované vlastnosti, které bude nastavena v rámci dvojčata cílové zařízení. Obsah cílového by měl zarovnané s strukturu twin zařízení definované výrobce nebo integrátor IoT hardwaru.  **Metriky** jsou dotazy na dvojče zařízení hlášené vlastnosti a musí taky zarovnané s strukturu twin zařízení IoT hardwaru výrobce nebo integrátor definované. Konfigurace automatického zařízení také využívat výhod IoT Hub provádění operací twin zařízení s rychlostí, který se nikdy nepřekročí [omezení] [ lnk-throttling] pro čtení twin zařízení a aktualizací.
* **Použít [služba zřizování zařízení][lnk-dps]:** řešení vývojáři měli používat službu zřizování zařízení přiřazení značek twin zařízení na nová zařízení, tak, že budou automaticky konfigurovat tak, že **konfigurací automatické zařízení** , se budou zaměřovat na dvojčata s touto značkou. 

## <a name="iot-solution-operator"></a>Operátor řešení IoT

Následují osvědčené postupy pro operátory řešení IoT, kteří pomocí řešení IoT založený na Azure:

* **Uspořádání zařízení pro správu:** IoT řešení musí definovat nebo povolit pro vytvoření instancí kvality nebo jiné sady založené na různých strategie nasazení, jako je například Kanárských zařízení. Nastaví zařízení se použije zavedení změn konfigurace a provádět další operace správy zařízení v odpovídajícím měřítku.
* **Provést změny konfigurace pomocí postupné uděláte:** postupné uděláte je celého procesu, při němž operátor nasadí změny na sadu zařízení IoT otevření. Cílem je provést změny postupně snižte riziko provádění široké škálování nejnovější změny.  Operátor měli používat při vytváření rozhraní na řešení [konfigurace automatického zařízení][lnk-auto-device-config], a cílení podmínku by měl cíle počáteční sadu zařízení (například skupinu lesknice).  Operátor by pak ověření změny konfigurace v sadě počáteční zařízení.  Po dokončení ověřování bude operátor aktualizovat konfiguraci automatického zařízení zahrnout s větším počtem zařízení. Operátor by také nastavit prioritu pro danou konfiguraci a být vyšší než ostatní konfigurace, které aktuálně cílená na těchto zařízeních.  Uděláte je možné monitorovat pomocí metriky hlášené konfigurace automatického zařízení. 
* **Provedení vrácení zpět v případě chyby nebo nesprávné konfigurace:** konfigurace aplikace automatické zařízení, která způsobuje chyby nebo nesprávné konfigurace můžete vrátit zpět změnou **cílení podmínku** tak, aby zařízení žádné nebudou splňovat cílení podmínku.  Ujistěte se, že jiné konfigurace automatického zařízení s nižší prioritou je stále určený pro tato zařízení.  Ověřte, že vrácení zpět úspěšně zobrazením metriky: Konfigurace vrátit zpět, musí už zobrazit stav pro untargeted zařízení a druhý konfigurace metriky by měl nyní obsahují počty pro zařízení, která jsou stále cílem.


## <a name="next-steps"></a>Další postup

* Další informace o implementaci dvojčata zařízení v [Rady pro pochopení a použití dvojčata zařízení IoT hub][lnk-device-twins]
* Provede kroky k vytvoření, aktualizace nebo odstranění konfigurace aplikace automatické zařízení v [konfigurovat a monitorovat zařízení IoT ve velkém měřítku][lnk-auto-device-config].
* Implementace vzoru aktualizace firmwaru pomocí dvojčata zařízení a jeho automatické konfigurace v [kurz: implementujte proces aktualizace firmwaru zařízení][lnk-firmware-update].

<!-- Links -->
[lnk-device-twins]: iot-hub-devguide-device-twins.md
[lnk-module-twins]: iot-hub-devguide-module-twins.md
[lnk-auto-device-config]: iot-hub-auto-device-config.md
[lnk-firmware-update]: tutorial-firmware-update.md
[lnk-throttling]: iot-hub-devguide-quotas-throttling.md
[lnk-queries]: iot-hub-devguide-query-language.md
[lnk-dps]: ../iot-dps/how-to-manage-enrollments.md
[lnk-azure-sdk]: https://github.com/Azure/azure-iot-sdks
[lnk-solution]: https://azure.microsoft.com/features/iot-accelerators/