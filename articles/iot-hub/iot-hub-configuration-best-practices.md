---
title: Doporučené postupy konfigurace zařízení pro Azure IoT Hub | Dokumenty společnosti Microsoft
description: Seznamte se s doporučenými postupy pro používání automatické správy zařízení k minimalizaci opakujících se a složitých úloh spojených se správou zařízení IoT ve velkém měřítku.
author: chrisgre
ms.author: chrisgre
ms.date: 06/28/2019
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: a3b70af71c2ce19835ac2ef8fc8ceed79ca5fe1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889531"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Doporučené postupy pro konfiguraci zařízení v rámci řešení IoT

Automatická správa zařízení v Azure IoT Hub automatizuje mnoho opakovaných a složitých úloh správy velkých vozových parků zařízení po celou dobu jejich životního cyklu. Tento článek definuje mnoho osvědčených postupů pro různé role, které se podílejí na vývoji a provozu řešení IoT.

* **Výrobce/integrátor hardwaru IoT:** Výrobci hardwaru IoT, integrátoři sestavující hardware od různých výrobců nebo dodavatelé poskytující hardware pro nasazení IoT vyrobené nebo integrované jinými dodavateli. Podílí se na vývoji a integraci firmwaru, vestavěných operačních systémů a vestavěného softwaru.

* **Vývojář řešení IoT:** Vývoj řešení IoT se obvykle provádí vývojář řešení. Tento vývojář může být součástí interního týmu nebo systémového integrátora specializujícího se na tuto činnost. Vývojář řešení IoT může vyvíjet různé součásti řešení IoT od začátku, integrovat různé standardní nebo open source komponenty nebo přizpůsobit [akcelerátor řešení IoT](/azure/iot-accelerators/).

* **Operátor řešení IoT:** Po nasazení řešení IoT vyžaduje dlouhodobé operace, monitorování, upgrady a údržbu. Tyto úkoly může provádět interní tým, který se skládá z odborníků na informační technologie, týmů pro operace s hardwarem a údržby a specialistů na domény, kteří monitorují správné chování celkové infrastruktury IoT.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Principy automatické správy zařízení pro konfiguraci zařízení IoT ve velkém měřítku

Automatická správa zařízení zahrnuje mnoho výhod [dvojčat zařízení](iot-hub-devguide-device-twins.md) a [dvojčat modulů](iot-hub-devguide-module-twins.md) pro synchronizaci požadovaných a hlášených stavů mezi cloudem a zařízeními. [Automatické konfigurace zařízení](iot-hub-auto-device-config.md) automaticky aktualizují velké sady dvojčat a shrnují průběh a dodržování předpisů. Následující kroky vysoké úrovně popisují vývoj a použití automatické správy zařízení:

* **Výrobce/integrátor hardwaru IoT** implementuje funkce správy zařízení v rámci integrované aplikace pomocí [dvojčat zařízení](iot-hub-devguide-device-twins.md). Tyto funkce mohou zahrnovat aktualizace firmwaru, instalaci a aktualizaci softwaru a správu nastavení.

* **Vývojář řešení IoT** implementuje vrstvu správy operací správy zařízení pomocí [dvojčat zařízení](iot-hub-devguide-device-twins.md) a [automatických konfigurací zařízení](iot-hub-auto-device-config.md). Řešení by mělo zahrnovat definování rozhraní operátora k provádění úloh správy zařízení.

* **Operátor řešení IoT** používá řešení IoT k provádění úloh správy zařízení, zejména k seskupení zařízení, k zahájení změn konfigurace, jako jsou aktualizace firmwaru, sledování průběhu a řešení problémů, které vznikají.

## <a name="iot-hardware-manufacturerintegrator"></a>Výrobce/integrátor hardwaru IoT

Následující jsou doporučené postupy pro výrobce hardwaru a integrátory zabývající se vývojem vestavěného softwaru:

* **Implementovat [dvojčata zařízení](iot-hub-devguide-device-twins.md):** Dvojčata zařízení umožňují synchronizaci požadované konfigurace z cloudu a pro vytváření zpráv o aktuální konfiguraci a vlastnostech zařízení. Nejlepší způsob, jak implementovat dvojčata zařízení v rámci integrovaných aplikací je prostřednictvím [sad Azure IoT SDK](https://github.com/Azure/azure-iot-sdks). Dvojčata zařízení jsou nejvhodnější pro konfiguraci, protože:

    * Podpora obousměrné komunikace.
    * Povolte stavy připojeného i odpojeného zařízení.
    * Dodržujte zásadu případné konzistence.
    * Jsou plně queriable v cloudu.

* **Strukturujte dvojče zařízení pro správu zařízení:** Dvojče zařízení by měla být strukturována tak, aby vlastnosti správy zařízení byly logicky seskupeny do oddílů. To umožní, aby změny konfigurace byly izolovány bez ovlivnění jiných částí dvojčete. Můžete například vytvořit oddíl v rámci požadovaných vlastností firmwaru, další oddíl pro software a třetí oddíl pro nastavení sítě. 

* **Nahlásit atributy zařízení, které jsou užitečné pro správu zařízení:** Atributy, jako je fyzická značky zařízení a model, firmware, operační systém, sériové číslo a další identifikátory, jsou užitečné pro vytváření sestav a jako parametry pro změny konfigurace cílení.

* **Definujte hlavní stavy pro vykazování stavu a průběhu:** Stavy nejvyšší úrovně by měly být uvedeny tak, aby mohly být hlášeny operátorovi. Aktualizace firmwaru by například hlásila stav jako Aktuální, Stahování, Použití, Probíhá a Chyba. Definujte další pole pro další informace o každém stavu.

## <a name="iot-solution-developer"></a>Vývojář řešení IoT

Následující jsou doporučené postupy pro vývojáře řešení IoT, kteří budují systémy založené v Azure:

* **Implementovat [dvojčata zařízení](iot-hub-devguide-device-twins.md):** Dvojčata zařízení umožňují synchronizaci požadované konfigurace z cloudu a pro vytváření zpráv o aktuální konfiguraci a vlastnostech zařízení. Nejlepší způsob, jak implementovat dvojčata zařízení v rámci aplikací cloudových řešení, je prostřednictvím sad [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks). Dvojčata zařízení jsou nejvhodnější pro konfiguraci, protože:

    * Podpora obousměrné komunikace.
    * Povolte stavy připojeného i odpojeného zařízení.
    * Dodržujte zásadu případné konzistence.
    * Jsou plně queriable v cloudu.

* **Uspořádání zařízení pomocí značek dvojčete zařízení:** Řešení by mělo obsluze umožnit definovat kroužky kvality nebo jiné sady zařízení na základě různých strategií nasazení, jako jsou kanárky. Organizace zařízení může být implementována v rámci vašeho řešení pomocí značek dvojčete zařízení a [dotazů](iot-hub-devguide-query-language.md). Organizace zařízení je nezbytná k tomu, aby bylo možné bezpečně a přesně provést konfiguraci.

* **Implementujte [automatické konfigurace zařízení](iot-hub-auto-device-config.md):** Automatické konfigurace zařízení nasazují a monitorují změny konfigurace velkých sad zařízení IoT prostřednictvím dvojčat zařízení.

   Automatické konfigurace zařízení cílí na sady dvojčat zařízení prostřednictvím **cílové podmínky,** což je dotaz na značky dvojčete zařízení nebo ohlášené vlastnosti. **Cílový obsah** je sada požadovaných vlastností, které budou nastaveny v rámci cílových dvojčat zařízení. Cílový obsah by měl být v souladu se strukturou dvojčete zařízení definovanou výrobcem nebo integrátorem hardwaru IoT. **Metriky** jsou dotazy na vlastnosti hlášené dvojčete zařízení a měly by také zarovnat se strukturou dvojčete zařízení definovanou výrobcem nebo integrátorem hardwaru IoT.

   Automatické konfigurace zařízení běží poprvé krátce po vytvoření konfigurace a poté v pětiminutových intervalech. Mají také prospěch z ioT Hub provádění operací dvojčete zařízení rychlostí, která nikdy nepřekročí [omezení omezení](iot-hub-devguide-quotas-throttling.md) pro dvojče zařízení čtení a aktualizace.

* **Použití [služby Zřizování zařízení](../iot-dps/how-to-manage-enrollments.md):** Vývojáři řešení by měli používat službu Device Provisioning Service k přiřazení značek dvojčete zařízení novým zařízením, takže budou automaticky **konfigurovány automatickými konfiguracemi zařízení,** které jsou zaměřeny na dvojčata s touto značkou. 

## <a name="iot-solution-operator"></a>Operátor řešení IoT

Následující jsou doporučené postupy pro operátory řešení IoT, kteří používají řešení IoT postavené na Azure:

* **Uspořádání zařízení pro správu:** Řešení IoT by mělo definovat nebo umožnit vytváření kroužků kvality nebo jiných sad zařízení založených na různých strategiích nasazení, jako jsou kanárky. Sady zařízení budou použity k zavedení změn konfigurace a k provádění dalších operací správy zařízení ve velkém měřítku.

* **Provádění změn konfigurace pomocí postupného zavádění:**  Postupné zavádění je celkový proces, kdy operátor nasazuje změny do rozšiřující se sady zařízení IoT. Cílem je provádět změny postupně, aby se snížilo riziko provádění rozsáhlých změn.Operátor by měl použít rozhraní řešení k vytvoření [automatické konfigurace zařízení](iot-hub-auto-device-config.md) a podmínka cílení by měla cílit na počáteční sadu zařízení (například kanárskou skupinu). Operátor by pak měl ověřit změnu konfigurace v počáteční sadě zařízení.

   Po dokončení ověření operátor aktualizuje automatickou konfiguraci zařízení tak, aby zahrnovala větší sadu zařízení. Operátor by měl také nastavit prioritu konfigurace, která má být vyšší než jiné konfigurace, které jsou aktuálně zaměřeny na tato zařízení. Zavedení lze sledovat pomocí metriky hlášené automatickou konfigurací zařízení.

* **Proveďte vrácení zpět v případě chyb nebo chybné konfigurace:**  Automatickou konfiguraci zařízení, která způsobuje chyby nebo chybné konfigurace, lze vrátit zpět změnou **podmínky cílení** tak, aby zařízení již nesplňovala podmínku cílení. Ujistěte se, že pro tato zařízení je stále zacílena jiná automatická konfigurace zařízení s nižší prioritou. Ověřte, zda bylo vrácení zpět úspěšné zobrazením metrik: Konfigurace vráceného zpět by již neměla zobrazovat stav necílových zařízení a metriky druhé konfigurace by nyní měly obsahovat počty zařízení, která jsou stále cílená.

## <a name="next-steps"></a>Další kroky

* Další informace o implementaci dvojčat zařízení v [principu a použití dvojčat zařízení v centru IoT Hub](iot-hub-devguide-device-twins.md).

* Projděte si postup k vytvoření, aktualizaci nebo odstranění automatické konfigurace zařízení v [části Konfigurace a monitorování zařízení IoT ve velkém měřítku](iot-hub-auto-device-config.md).

* Implementujte vzor aktualizace firmwaru pomocí dvojčat zařízení a automatických konfigurací zařízení v [kurzu: Implementace procesu aktualizace firmwaru zařízení](tutorial-firmware-update.md).
