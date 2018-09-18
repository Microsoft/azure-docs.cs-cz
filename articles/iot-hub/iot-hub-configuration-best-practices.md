---
title: Osvědčenými postupy konfigurace zařízení pro službu Azure IoT Hub | Dokumentace Microsoftu
description: Další informace o osvědčené postupy pro konfiguraci zařízení IoT ve velkém měřítku
author: chrisgre
ms.author: chrisgre
ms.date: 06/24/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 5eb0ba659961d809d0ae471034b03263f87e3894
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985494"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Osvědčené postupy pro konfiguraci zařízení v rámci řešení IoT

Automatické správy ve službě Azure IoT Hub automatizuje mnoho opakovaných a složité úlohy správy flotily nebo velké zařízení přes celého jejich životního cyklu. Tento článek definuje řadu osvědčené postupy pro různé role zapojené při vývoji a provozování řešení IoT.

* **Výrobce hardwaru IoT/integrátor:** IoT výrobci hardwaru, integrátorům sestavování hardwarem od různých výrobců a dodavatelů poskytuje hardware pro nasazení IoT vyrobenými nebo integrované podle jiných dodavatelů. Součástí vývoje a integrace firmwaru, embedded operačních systémů a integrovaný software.

* **Řešení IoT pro vývojáře:** vývoje řešení IoT se obvykle provádí vývojář řešení. Tento vývojář může být složená z interního týmu nebo systémový integrátor, která se specializuje na tuto aktivitu. Pro vývojáře IoT řešení můžete vyvíjet různé součásti řešení IoT od začátku, integrovat různé standardní nebo open source komponenty nebo upravit [akcelerátorů řešení IoT](/azure/iot-accelerators/).

* **Operátor řešení IoT:** po IoT řešení nasazuje, vyžaduje dlouhodobé provoz, sledování, upgradu a údržby. Tyto úlohy provést interně týmem, které obsahuje informace o technologii specialisty, hardwarových operací a údržby týmy a domény specialistů, kteří monitorovat správné chování celkovou infrastrukturu IoT.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Princip automatické správy pro konfiguraci zařízení IoT ve velkém měřítku

Správa automatického zařízení zahrnuje mnoho výhod [dvojčata zařízení](iot-hub-devguide-device-twins.md) a [dvojčaty modulů](iot-hub-devguide-module-twins.md) synchronizovat požadované a oznámená stavů mezi cloudem a zařízení. [Automatické konfigurace] [lnk automatické – zařízení – konfigurace] automaticky aktualizovat velkých sad dvojčata a summerize průběh a dodržování předpisů. Následující základní kroky popisují, jak automatické správy zařízení vyvíjí a použít:

* **Výrobce hardwaru IoT/integrátor** implementuje funkce správy zařízení v rámci vložené aplikace s využitím [dvojčata zařízení](iot-hub-devguide-device-twins.md). Tyto funkce by mohla obsahovat aktualizace firmwaru, instalace softwaru a aktualizace a Správa nastavení.

* **Řešení IoT pro vývojáře** implementuje vrstva správy operací správy zařízení pomocí [dvojčata zařízení](iot-hub-devguide-device-twins.md) a [automatické konfigurace](iot-hub-auto-device-config.md). Řešení by měl obsahovat definice operátoru rozhraní k provádění úloh správy zařízení.

* **Operátor řešení IoT** používá řešení IoT umožní provádět úlohy správy zařízení, zejména k seskupení zařízení společně, zahájení změny konfigurace, jako je aktualizace firmwaru, sledovat průběh a řešení problémů ke kterým dochází.

## <a name="iot-hardware-manufacturerintegrator"></a>Výrobce hardwaru IoT/integrátor

Tady jsou osvědčené postupy pro výrobce hardwaru a integrátory pracující s vývojem pro integrovaný software:

* **Implementace [dvojčata zařízení](iot-hub-devguide-device-twins.md):** dvojčata zařízení povolit synchronizaci požadovaných konfigurací z cloudu a za ohlašování aktuální vlastnosti konfigurace a zařízení. Nejlepší způsob, jak implementovat dvojčata zařízení v rámci vložené aplikace je prostřednictvím [sad SDK Azure IoT](https://github.com/Azure/azure-iot-sdks). Dvojčata zařízení jsou nejvhodnější pro konfiguraci, protože jsou:

    * Podpora obousměrnou komunikaci.
    * Povolit pro oba stavy zařízení připojené a odpojené.
    * Postupujte podle principu konečné konzistence.
    * Jsou plně dotazovatelné v cloudu.

* **Struktury dvojče zařízení pro správu zařízení:** dvojče zařízení by měla být strukturovaná, takže vlastnosti správy zařízení jsou logicky seskupeny dohromady do oddílů. To vám umožní být izolované, aniž by to ovlivnilo ostatní oddíly tohoto dvojčeti změny konfigurace. Například vytvořte oddíl v rámci požadované vlastnosti pro firmwaru, jiné části softwaru a třetí oddíl pro nastavení sítě. 

* **Sestavy atributy zařízení, které jsou užitečné ke správě zařízení:** atributů, například zařízení fyzických značka a model, firmwaru, operačním systému, sériové číslo a jsou užitečné pro vytváření sestav a jako parametry pro cílení na další identifikátory. změny konfigurace.

* **Definuje hlavní pro vytváření sestav stavu a průběhu zpracování:** nejvyšší úrovně stavy by měl vytvořit výčet tak, aby popřípadě operátoru. Aktualizace firmwaru by například informuje o stavu jako aktuální, stahování, použití, probíhající a chyba. Definovali ještě další pole pro další informace o jednotlivých stavech.

## <a name="iot-solution-developer"></a>Řešení IoT pro vývojáře

Tady jsou osvědčené postupy pro vývojáře řešení IoT, kteří zodpovídají za tvorbu systémů v Azure:

* **Implementace [dvojčata zařízení](iot-hub-devguide-device-twins.md):** dvojčata zařízení povolit synchronizaci požadovaných konfigurací z cloudu a za ohlašování aktuální vlastnosti konfigurace a zařízení. Nejlepší způsob, jak implementovat dvojčata zařízení v rámci cloudové řešení pro aplikace je prostřednictvím [sad SDK Azure IoT](https://github.com/Azure/azure-iot-sdks). Dvojčata zařízení jsou nejvhodnější pro konfiguraci, protože jsou:

    * Podpora obousměrnou komunikaci.
    * Povolit pro oba stavy zařízení připojené a odpojené. 
    * Postupujte podle principu konečné konzistence.
    * Jsou plně dotazovatelné v cloudu.

* **Umožňují uspořádat zařízení pomocí značky dvojčat zařízení:** řešení by mělo umožnit operátor pro definování okruhy kvality nebo jiné sady založené na různé strategie nasazení, jako je například testovací zařízení. Je možné implementovat zařízení organizace v rámci vašeho řešení pomocí značky dvojčat zařízení a [dotazy](iot-hub-devguide-query-language.md). Zařízení organizace je potřeba povolit pro zavádění konfigurace bezpečně a přesně.

* **Implementace [automatické konfigurace](iot-hub-auto-device-config.md):** automatické konfigurace nasazení a konfigurace monitorování se změní na velkých sadách zařízení IoT prostřednictvím dvojčata zařízení. Konfigurace automatického zařízení cílit na dvojčata zařízení prostřednictvím sady **cílové podmínce,** což je dotaz na zařízení značky dvojčat nebo ohlášené vlastnosti. **Směrovat obsah** je sada požadované vlastnosti nastavené v rámci dvojčat cílové zařízení. Cíl obsahu by mělo odpovídat definované výrobce hardwaru IoT/integrátor strukturu dvojčete zařízení. 

   **Metriky** jsou ohlášené vlastnosti dotazy na dvojče zařízení a také by mělo odpovídat definované výrobce hardwaru IoT/integrátor strukturu dvojčete zařízení. Konfigurace automatického zařízení také přináší výhody služby IoT Hub provádí operace dvojčete zařízení sazbou se nikdy nepřekročí [limitů omezování](iot-hub-devguide-quotas-throttling.md) pro aktualizace a čtení dvojčat zařízení.

* **Použít [služby Device Provisioning](../iot-dps/how-to-manage-enrollments.md):** řešení by vývojáři služby Device Provisioning a přiřaďte značky dvojčat zařízení na nová zařízení, tak, aby se automaticky nakonfiguruje  **Konfigurace automatického zařízení** , který cílí na dvojčata s konkrétní značkou. 

## <a name="iot-solution-operator"></a>Operátor řešení IoT

Tady jsou osvědčené postupy pro operátory řešení IoT, kteří pomocí řešení IoT založených na platformě Azure:

* **Uspořádání zařízení pro správu:** by měl definovat nebo povolit pro vytvoření instancí kvality nebo jiné sady zařízení podle různé strategie nasazení, jako je například testovací řešení IoT. Nastaví zařízení se použije k zavedení změn konfigurace a provádět jiné operace správy zařízení ve velkém měřítku.

* **Provádět změny konfigurace pomocí postupného uvádění:** postupného uvádění je celkový proces kterým operátor nasadí změny do otevření sadu zařízení IoT. Cílem je, aby změny postupně, aby se snížilo riziko zpřístupnění celou škálovací rozbíjející změny.  Operátor by měl použít rozhraní toto řešení k vytvoření [automatické konfigurace](iot-hub-auto-device-config.md) a cílení podmínku zaměřit počáteční skupiny zařízení (například testovací skupiny). Operátor by měl pak ověření změny konfigurace v počáteční sadu zařízení. 

   Po dokončení ověření operátor, který se bude aktualizovat konfiguraci automatické zahrnout s větším počtem zařízení. Operátor by měl také nastavit prioritu pro konfiguraci vyšší než ostatní konfigurace, které aktuálně cílená na těchto zařízeních. Zavedení je možné monitorovat pomocí metrik hlášených automatické konfigurace. 

* **Proveďte vrácení zpět v případě chyby nebo chybné konfigurace:** automatické konfigurace, který způsobí, že chyby nebo chybné konfigurace může být vrácena zpět tak, že změníte **cílení na podmínku** tak, aby zařízení bez už splňují cílovou podmínku. Ujistěte se, že tato zařízení stále jinou konfiguraci automatického zařízení s nižší prioritou cílí. Ověřte, že vrácení změn zobrazením metriky: Konfigurace vrácena zpět již musí zobrazit stav pro untargeted zařízení a druhý konfigurace metrik by teď měl obsahovat počet zařízení, která stále cílí.

## <a name="next-steps"></a>Další postup

* Další informace o implementaci dvojčata zařízení v [principy a použití dvojčat zařízení ve službě IoT Hub](iot-hub-devguide-device-twins.md).

* Provede kroky k vytvoření, aktualizace nebo odstranění automatické konfiguraci v [konfigurovat a monitorovat zařízení IoT ve velkém měřítku](iot-hub-auto-device-config.md).

* Implementace vzoru aktualizace firmwaru pomocí dvojčata zařízení a automatické konfigurace v [kurz: implementujte proces aktualizace firmwaru zařízení](tutorial-firmware-update.md). 
