---
title: Osvědčené postupy konfigurace zařízení pro Azure IoT Hub | Microsoft Docs
description: Seznamte se s osvědčenými postupy pro použití automatické správy zařízení k minimalizaci opakujících se a složitých úloh zapojených do správy škálování zařízení IoT.
author: robinsh
ms.author: robinsh
ms.date: 06/28/2019
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 67f0d9eb1fdac603ee82d568644e8ad8550d1c80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82024774"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Osvědčené postupy pro konfiguraci zařízení v rámci řešení IoT

Automatická správa zařízení v Azure IoT Hub automatizuje mnoho opakujících se a složitých úloh správy rozsáhlých loďstva zařízení v celém životním cyklu. Tento článek definuje mnoho osvědčených postupů pro různé role, které se podílejí na vývoji a provozu řešení IoT.

* **Výrobce nebo integrátor hardwaru IoT:** Výrobci hardwaru IoT, integrátory napravují hardware od různých výrobců nebo dodavatelé, kteří poskytují hardware pro nasazení IoT vyráběné nebo integrované jinými dodavateli. Zapojené do vývoje a integrace firmwaru, vloženého operačního systému a integrovaného softwaru.

* **Vývojář řešení IoT:** Vývoj řešení IoT obvykle provádí vývojář řešení. Tento vývojář může být součástí interního týmu nebo specializace systémového integrátoru v této aktivitě. Vývojář řešení IoT může vyvíjet různé komponenty řešení IoT od začátku, integrovat různé standardní nebo open source komponenty nebo přizpůsobit [akcelerátor řešení IoT](/azure/iot-accelerators/).

* **Operátor řešení IoT:** Po nasazení řešení IoT se vyžaduje dlouhodobá operace, monitorování, upgrady a údržba. Tyto úlohy může udělat interní tým, který se skládá z specialistů na informační technologie, hardwarových operací a týmů údržby a specialistů na domény, kteří monitorují správné chování celkové infrastruktury IoT.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Vysvětlení automatické správy zařízení pro konfiguraci zařízení IoT ve velkém měřítku

Automatická správa zařízení zahrnuje mnoho výhod [vláken zařízení](iot-hub-devguide-device-twins.md) a [vláken v modulech](iot-hub-devguide-module-twins.md) k synchronizaci požadovaných a nahlášených stavů mezi cloudem a zařízeními. [Automatické konfigurace zařízení](iot-hub-auto-device-config.md) automaticky aktualizují velké sady vláken a shrnují průběh a dodržování předpisů. Následující kroky vysoké úrovně popisují, jak se vyvíjí a používá Automatická správa zařízení:

* **Výrobce a integrátor hardwaru IoT** implementují funkce správy zařízení v rámci vložené aplikace pomocí [vláken zařízení](iot-hub-devguide-device-twins.md). Mezi tyto funkce patří aktualizace firmwaru, instalace a aktualizace softwaru a Správa nastavení.

* **Vývojář řešení IoT** implementuje vrstvu správy operací správy zařízení pomocí [nevláken zařízení](iot-hub-devguide-device-twins.md) a [automatických konfigurací zařízení](iot-hub-auto-device-config.md). Řešení by mělo zahrnovat definování rozhraní operátora pro provádění úloh správy zařízení.

* **Operátor řešení IoT** používá řešení IoT k provádění úloh správy zařízení, zejména k seskupení zařízení, zahájí změny konfigurace, jako jsou aktualizace firmwaru, průběh monitorování a řešení potíží, ke kterým dojde.

## <a name="iot-hardware-manufacturerintegrator"></a>Výrobce nebo integrátor hardwaru IoT

Níže jsou uvedené osvědčené postupy pro výrobce hardwaru a integrátory, kteří se zabývat integrovaným vývojem softwaru:

* **Implementovat [vlákna zařízení](iot-hub-devguide-device-twins.md):** Vlákna zařízení umožňují synchronizaci požadované konfigurace z cloudu a vytváření sestav aktuální konfigurace a vlastností zařízení. Nejlepším způsobem, jak implementovat vlákna zařízení v rámci integrovaných aplikací, je prostřednictvím [sad SDK služby Azure IoT](https://github.com/Azure/azure-iot-sdks). Vlákna zařízení jsou nejvhodnější pro konfiguraci, protože:

    * Podporuje obousměrnou komunikaci.
    * Povolte pro stav připojených i odpojených zařízení.
    * Řiďte se principem konečné konzistence.
    * V cloudu jsou plně dotazovatelné.

* **Strukturujte zařízení, které je pro správu zařízení** nepřesné: Vlákna zařízení by měla být strukturovaná tak, aby vlastnosti správy zařízení byly logicky seskupeny do oddílů. Tím umožníte, aby se změny konfigurace izolované bez dopadu na jiné části vlákna. Můžete například vytvořit oddíl v rámci požadovaných vlastností pro firmware, jiný oddíl pro software a třetí oddíl pro nastavení sítě. 

* **Sestavování atributů zařízení, které jsou užitečné pro správu zařízení:** Pro vytváření sestav a jako parametry pro cílení změn konfigurace jsou užitečné atributy, jako je například vytvoření fyzického zařízení a model, firmware, operační systém, sériové číslo a jiné identifikátory.

* **Definujte hlavní stavy pro vytváření sestav o stavu a průběhu:** Stavy nejvyšší úrovně by měly být vyčísleny tak, aby mohly být hlášeny operátorovi. Například aktualizace firmwaru by mohla ohlásit stav jako aktuální, stáhnout, použít, probíhá a chyba. Definujte další pole pro další informace o jednotlivých stavech.

## <a name="iot-solution-developer"></a>Vývojář řešení IoT

Níže jsou uvedené osvědčené postupy pro vývojáře řešení IoT, kteří sestavují systémy založené na Azure:

* **Implementovat [vlákna zařízení](iot-hub-devguide-device-twins.md):** Vlákna zařízení umožňují synchronizaci požadované konfigurace z cloudu a vytváření sestav aktuální konfigurace a vlastností zařízení. Nejlepším způsobem, jak implementovat vlákna zařízení v rámci aplikací pro cloudová řešení, je projít sady [SDK Azure IoT](https://github.com/Azure/azure-iot-sdks). Vlákna zařízení jsou nejvhodnější pro konfiguraci, protože:

    * Podporuje obousměrnou komunikaci.
    * Povolte pro stav připojených i odpojených zařízení.
    * Řiďte se principem konečné konzistence.
    * V cloudu jsou plně dotazovatelné.

* **Uspořádání zařízení pomocí značek nevlákenných zařízení:** Řešení by mělo umožňovat operátorovi definovat kanály kvality nebo jiné sady zařízení na základě různých strategií nasazení, jako je například Kanárské. V rámci vašeho řešení je možné implementovat organizaci zařízení pomocí značek a [dotazů](iot-hub-devguide-query-language.md), které jsou v zařízení. Organizace zařízení je nutná k tomu, aby bylo umožněno bezpečné a přesné vypínání konfigurace.

* **Implementace [automatických konfigurací zařízení](iot-hub-auto-device-config.md):** Automatické konfigurace zařízení nasazují a monitorují změny konfigurace velkých sad zařízení IoT prostřednictvím vláken zařízení.

   Automatické konfigurace zařízení: cílové sady vláken zařízení přes **cílovou podmínku,** což je dotaz na nepodmíněných značkách zařízení nebo hlášených vlastností. **Cílový obsah** je sada požadovaných vlastností, které se nastaví v rámci cílených vláken zařízení. Cílový obsah by měl být v souladu se strukturou s dvojitou strukturou v zařízení, kterou definuje výrobce nebo integrátor hardwaru IoT. **Metriky** jsou dotazy na nedokončené hlášené vlastnosti zařízení a měly by se také zarovnávat se strukturou s dvojitou strukturou, kterou definuje výrobce nebo integrátor hardwaru IoT.

   Automatické konfigurace zařízení se spouští poprvé po vytvoření konfigurace a pak v intervalu pěti minut. Můžou také těžit z IoT Hub provádění operací s dvojitým provozem zařízení rychlostí, která nikdy nepřekročí [omezení omezení](iot-hub-devguide-quotas-throttling.md) pro čtení a aktualizace nefungujících zařízení.

* **Použijte [službu Device Provisioning Service](../iot-dps/how-to-manage-enrollments.md):** vývojáři řešení by měli použít službu Device Provisioning k přiřazení značek zařízení, které jsou na nových zařízeních, tak, aby se automaticky nakonfigurovaly pomocí **automatických konfigurací zařízení** , které jsou zaměřené na vlákna s touto značkou. 

## <a name="iot-solution-operator"></a>Operátor řešení IoT

Níže jsou uvedené osvědčené postupy pro operátory řešení IoT, kteří používají řešení IoT postavené na Azure:

* **Uspořádat zařízení pro správu:** Řešení IoT by mělo definovat nebo umožňovat vytváření kanálů kvality nebo jiných sad zařízení na základě různých strategií nasazení, jako je například kanárskéie. Sady zařízení se použijí k zavedení změn konfigurace a k provádění dalších operací správy zařízení v rámci škálování.

* **Provádět změny konfigurace pomocí postupného zavedení:**  Postupná zavedení je celkový proces, při kterém operátor nasadí změny do rozšiřování sady zařízení IoT. Cílem je udělat postupně změny, abyste snížili riziko, že dojde k zásadním změnám v rámci škálování.Operátor by měl použít rozhraní řešení k vytvoření [automatické konfigurace zařízení](iot-hub-auto-device-config.md) a podmínka cílení by měla cílit na počáteční sadu zařízení (například na skupinu pro Kanárské). Operátor by pak měl ověřit změnu konfigurace v počáteční sadě zařízení.

   Po dokončení ověření bude operátor aktualizovat automatickou konfiguraci zařízení tak, aby zahrnoval větší sadu zařízení. Operátor by měl také nastavit prioritu konfigurace, která bude vyšší než jiné konfigurace, které jsou aktuálně cílem těchto zařízení. Zavedení se dá monitorovat pomocí metrik vykázaných automatickou konfigurací zařízení.

* **V případě chyb nebo chybných konfigurací provést vrácení zpět:**  Automatická konfigurace zařízení, která způsobuje chyby nebo chybné konfigurace, se může vrátit zpátky změnou **podmínky cíle** tak, že zařízení už nebudou splňovat podmínku cílení. Ujistěte se, že pro tato zařízení pořád cílí na další automatickou konfiguraci zařízení s nižší prioritou. Zkontrolujte, zda se vrácení zpět zdařilo zobrazením metrik: Konfigurace vracené zpět by již neměla zobrazovat stav pro Necílená zařízení a metriky druhé konfigurace by teď měly zahrnovat počty pro zařízení, která jsou pořád zacílená.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o implementaci zdvojených zařízení v [porozumět a používání vláken zařízení v IoT Hub](iot-hub-devguide-device-twins.md).

* Projděte si postup, jak vytvořit, aktualizovat nebo odstranit automatickou konfiguraci zařízení v části [Konfigurace a monitorování zařízení IoT ve velkém měřítku](iot-hub-auto-device-config.md).

* Implementace schématu aktualizace firmwaru pomocí nevláken zařízení a automatických konfigurací zařízení v [kurzu: implementace procesu aktualizace firmwaru zařízení](tutorial-firmware-update.md).
