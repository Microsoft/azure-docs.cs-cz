---
title: Opětovné zřízení zařízení v Azure IoT Hub Device Provisioning Service
description: Naučte se, jak znovu zřídit zařízení pomocí instance služby Device Provisioning Service (DPS) a proč to může být nutné.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: e5cc5b557aa4dff793f7e87093eeb65028da4f8c
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91839788"
---
# <a name="how-to-reprovision-devices"></a>Postup opětovného zřízení zařízení

Během životního cyklu řešení IoT je běžné přesouvat zařízení mezi centra IoT. Důvody pro tento přesun můžou zahrnovat následující scénáře:

* **Geografická poloha**: když se zařízení pohybuje mezi místy, zlepšuje se latence sítě tím, že se zařízení migruje do služby IoT Hub blíž do každého umístění.

* **Víceklientská architektura: zařízení**se dá používat v rámci stejného řešení IoT, ale znovu přiřazeno nebo zapůjčení novému zákazníkovi nebo webu zákazníka. Tento nový zákazník se může obsluhovat pomocí jiného centra IoT.

* **Změna řešení**: zařízení se přesunulo do nového nebo aktualizovaného řešení IoT. Tato změna přiřazení může vyžadovat, aby zařízení komunikovalo s novým službou IoT Hub, která je připojená k ostatním back-endové součásti. 

* **Quarantine**: podobná změně řešení. Zařízení, které je v nefunkčním stavu, ohrožení zabezpečení nebo zastaralá, může být přiřazeno ke centru IoT, kde je vše možné aktualizovat a vrátit se do dodržování předpisů. Jakmile zařízení správně funguje, migruje se zpátky do hlavního centra.

Podrobnější přehled o opětovném zřízení najdete v tématu [IoT Hub konceptů opětovného zřízení zařízení](concepts-device-reprovision.md).


## <a name="configure-the-enrollment-allocation-policy"></a>Konfigurace zásad přidělení registrace

Zásady přidělování určují, jak se zařízení přidružená k registraci přidělují nebo přiřazují do služby IoT Hub po opětovném zřízení.

Pomocí následujících kroků můžete nakonfigurovat zásady přidělování pro registraci zařízení:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte k instanci služby Device Provisioning.

2. Klikněte na **Správa**registrací a potom klikněte na skupinu registrací nebo na jednotlivou registraci, kterou chcete nakonfigurovat pro opětovné zřízení. 

3. V části **Vyberte, jak chcete přiřadit zařízení k rozbočovačům**vyberte jednu z následujících zásad přidělování:

    * **Nejnižší latence**: Tato zásada přiřadí zařízení k připojeným IoT Hub, což způsobí nejnižší komunikaci mezi zařízením a IoT Hub. Tato možnost umožňuje zařízení komunikovat s nejbližším službou IoT Hub na základě umístění. 
    
    * **Rovnoměrně vážená distribuce**: Tato zásada distribuuje zařízení napříč propojenými centry IoT na základě váhy alokace přiřazené ke každému propojenému centru IoT. Tato zásada umožňuje vyrovnávat zatížení zařízení napříč skupinou propojených Center na základě vah přidělení nastavených na těchto centrech. Pokud zřizujete zařízení jenom pro jednu IoT Hub, doporučujeme toto nastavení. Toto nastavení je výchozí. 
    
    * **Statická konfigurace**: Tato zásada vyžaduje, aby byla v položce registrace pro zařízení, které se má zřídit, uvedená požadovaná IoT Hub. Tato zásada vám umožní určit jedno konkrétní centrum IoT, ke kterému chcete přiřadit zařízení.

4. V části **Vyberte centra IoT, ke kterým se má tato skupina přiřadit**, vyberte propojená centra IoT, která chcete zahrnout do zásad přidělování. Volitelně můžete přidat nové propojené centrum IoT pomocí tlačítka **propojit s novým IoT Hub** .

    S nejnižší zásadou přidělení **latence** budou vybraná centra zahrnovat do vyhodnocení latence k určení nejbližšího centra pro přiřazení zařízení.

    Díky zásadám přidělování **rovnoměrně vážených distribucí** se zařízení vyrovnávají přes vybraná centra na základě jejich nakonfigurovaných vah přidělení a jejich aktuálního zatížení.

    Pomocí zásad přidělování **statických konfigurací** vyberte Centrum IoT, ke kterému chcete přiřadit zařízení.

4. Klikněte na **Uložit**nebo přejděte k další části a nastavte zásady opětovného zřizování.

    ![Vyberte zásady přidělení registrace.](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>Nastavení zásad opětovného zřízení

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte k instanci služby Device Provisioning.

2. Klikněte na **Správa**registrací a potom klikněte na skupinu registrací nebo na jednotlivou registraci, kterou chcete nakonfigurovat pro opětovné zřízení.

3. V části **Vyberte, jak chcete, aby se data zařízení při opětovném zřizování zpracovala do jiného centra IoT**, vyberte jednu z následujících zásad opětovného zřízení:

    * **Opětovné zřízení a migrace dat**: Tato zásada provede akci, když zařízení přidružená k položce registrace odesílají novou žádost o zřízení. V závislosti na konfiguraci položky registrace se může zařízení přiřadit k jinému centru IoT. Pokud zařízení mění centra IoT, odeberou se registrace zařízení pomocí počátečního centra IoT Hub. Všechny informace o stavu zařízení z tohoto počátečního centra IoT budou migrovány do nového centra IoT Hub. Během migrace se stav zařízení ohlásí jako **přiřazení** .

    * **Opětovné zřízení a resetování na počáteční konfiguraci**: Tato zásada provede akci, když zařízení přidružená k položce registrace odesílají novou žádost o zřízení. V závislosti na konfiguraci položky registrace se může zařízení přiřadit k jinému centru IoT. Pokud zařízení mění centra IoT, odeberou se registrace zařízení pomocí počátečního centra IoT Hub. K novému centru IoT Hub se poskytuje počáteční konfigurační data, která instance zřizovací služby přijatá při zřizování zařízení nabízí. Během migrace bude stav zařízení hlášeno jako **přiřazení**.

4. Kliknutím na **Uložit** povolte opětovné zřízení zařízení na základě vašich změn.

    ![Snímek obrazovky, který zvýrazní změny, které jste udělali, a tlačítko Uložit.](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Odeslat žádost o zřízení ze zařízení

Aby se zařízení mohla znovu zřídit na základě změn konfigurace provedených v předchozích částech, musí tato zařízení požádat o opětovné zřízení. 

Jak často zařízení odesílá požadavek na zřízení, závisí na scénáři. Doporučujeme ale programovat vaše zařízení, aby při restartování odeslala do instance zřizovací služby požadavek na zřízení a podporovala [metodu](../iot-hub/iot-hub-devguide-direct-methods.md) ručního spuštění zřizování na vyžádání. Zřizování se taky dá aktivovat nastavením [požadované vlastnosti](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example). 

Zásady opětovného zřizování na položce registrace určují, jak instance služby Device Provisioning zpracovává tyto požadavky na zřizování, a jestli se mají při opětovném zřizování migrovat data stavu zařízení. Pro jednotlivé registrace a skupiny registrací jsou k dispozici stejné zásady:

Příklad kódu odeslání požadavků na zřizování ze zařízení během spouštěcí sekvence najdete v tématu věnovaném [automatickému zřizování simulovaného zařízení](quick-create-simulated-device.md).


## <a name="next-steps"></a>Další kroky

- Další informace o opětovném zřízení najdete v tématu Koncepty opětovného [zřizování zařízení IoT Hub](concepts-device-reprovision.md) 
- Další informace o zrušení zřízení najdete v tématu [Postup zrušení zřízení zařízení, která byla dříve automaticky zřízena](how-to-unprovision-devices.md) . 











