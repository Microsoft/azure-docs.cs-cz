---
title: Opětovné zřízení zařízení ve službě Azure IoT Hub DeviceProvisioning Service
description: Zjistěte, jak znovu zřídit zařízení s instancí Služby zřizování zařízení (DPS) a proč to možná budete muset udělat.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0ded494debab19daa15a953715b1ab7b0b10ad18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974900"
---
# <a name="how-to-reprovision-devices"></a>Jak znovu zřídit zařízení

Během životního cyklu řešení IoT je běžné přesouvat zařízení mezi centry IoT. Důvody tohoto kroku mohou zahrnovat následující scénáře:

* **Geolokace**: Jak se zařízení pohybuje mezi umístěními, latence sítě se zlepší tím, že zařízení migruje do služby IoT hub blíže ke každému umístění.

* **Víceklientské :** Zařízení může být použito v rámci stejného řešení IoT, ale přeřazeno nebo pronajato novému zákazníkovi nebo webu zákazníka. Tento nový zákazník může být obsluhován pomocí jiného centra IoT hub.

* **Změna řešení**: Zařízení může být přesunuto do nového nebo aktualizovaného řešení IoT. Toto přeřazení může vyžadovat, aby zařízení komunikovalo s novým centrem IoT hub, který je připojený k jiným back-endovým součástem. 

* **Karanténa**: Podobně jako změna řešení. Zařízení, které je nefunkční, kompromitované nebo zastaralé, může být znovu přiřazeno k centru IoT hubu, kde jediné, co může udělat, je aktualizovat a vrátit se do souladu. Jakmile zařízení funguje správně, je přeneseno zpět do hlavního rozbočovače.

Podrobnější přehled reprovisioningu najdete v tématu [Koncepty opětovného zřizování zařízení ioT Hub](concepts-device-reprovision.md).


## <a name="configure-the-enrollment-allocation-policy"></a>Konfigurace zásad přidělení registrace

Zásady přidělení určují, jak budou zařízení přidružená k registraci přidělena nebo přiřazena k centru IoT, jakmile budou znovu zřízena.

Následující kroky nakonfigurují zásady přidělení pro registraci zařízení:

1. Přihlaste se na [portál Azure](https://portal.azure.com) a přejděte na instanci služby zřizování zařízení.

2. Klikněte na **Spravovat registrace**a klikněte na skupinu registrace nebo na jednotlivé registrace, které chcete nakonfigurovat pro opětovné zřízení. 

3. V části **Vyberte způsob přiřazení zařízení k rozbočovačům**vyberte jednu z následujících zásad přidělení:

    * **Nejnižší latence**: Tato zásada přiřazuje zařízení k propojenému centru IoT Hub, což bude mít za následek nejnižší latenci komunikace mezi zařízením a IoT Hub. Tato možnost umožňuje zařízení komunikovat s nejbližším centrem IoT hub na základě umístění. 
    
    * **Rovnoměrně vážené rozdělení**: Tato zásada distribuuje zařízení mezi propojené služby IoT Hubs na základě alokační váhy přiřazené každému propojenému centru IoT Hub. Tato zásada umožňuje vyvážit zatížení zařízení napříč skupinou propojených rozbočovačů na základě přidělení váhy nastavené na těchto rozbočovačů. Pokud zřizujete zařízení pouze do jednoho centra IoT Hub, doporučujeme toto nastavení. Toto nastavení je výchozí. 
    
    * **Statická konfigurace**: Tato zásada vyžaduje, aby požadované služby IoT Hub byly uvedeny v položce registrace pro zařízení, které má být zřízeno. Tato zásada umožňuje určit jednu konkrétní centrum IoT hub, ke kterému chcete přiřadit zařízení.

4. V **části Vyberte centra IoT, ke kterým lze tuto skupinu přiřadit**, vyberte propojené centra IoT, které chcete zahrnout do zásad přidělení. Volitelně můžete přidat nový propojený centrum Iot pomocí tlačítka Propojit nový portál **IoT Hub.**

    S nejnižší **latence zásady** přidělení, centra, které vyberete budou zahrnuty do vyhodnocení latence k určení nejbližší rozbočovač pro přiřazení zařízení.

    Díky zásadám rovnoměrně **váženého přidělení distribuce** budou zařízení vyvažována napříč rozbočovači, které vyberete, na základě jejich nakonfigurovaných hmotností přidělení a jejich aktuálního zatížení zařízení.

    Pomocí zásad statické **konfigurace** přidělení vyberte službu IoT hub, ke které chcete zařízení přiřadit.

4. Klepněte na tlačítko **Uložit**nebo přejděte k další části a nastavte zásady opětovného zřízení.

    ![Vybrat zásadu přidělení zápisu](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>Nastavení zásad opětovného zřízení

1. Přihlaste se na [portál Azure](https://portal.azure.com) a přejděte na instanci služby zřizování zařízení.

2. Klikněte na **Spravovat registrace**a klikněte na skupinu registrace nebo na jednotlivé registrace, které chcete nakonfigurovat pro opětovné zřízení.

3. V **části Vyberte, jak chcete, aby se s daty zařízení zacházelo při opětovném zřizování do jiného centra IoT hub**, zvolte jednu z následujících zásad opětovného zřízení:

    * **Opětovné zřizování a migrace dat**: Tato zásada provede akci, když zařízení přidružená k položce registrace odešle novou žádost o zřízení. V závislosti na konfiguraci položky registrace může být zařízení znovu přiřazeno k jinému centru IoT hub. Pokud zařízení mění centra IoT, registrace zařízení s počátečním centrem IoT hub se odebere. Všechny informace o stavu zařízení z tohoto počátečního centra IoT hub budou přeneseny do nového centra IoT hub. Během migrace bude stav zařízení nahlášen jako **Přiřazení**

    * **Opětovné zřizování a obnovení počáteční konfigurace**: Tato zásada provede akci, když zařízení přidružená k položce registrace odešle nový požadavek na zřizování. V závislosti na konfiguraci položky registrace může být zařízení znovu přiřazeno k jinému centru IoT hub. Pokud zařízení mění centra IoT, registrace zařízení s počátečním centrem IoT hub se odebere. Počáteční konfigurační data, která instance zřizovací služby obdržela při zřízení zařízení, jsou k dispozici do nového centra IoT Hub. Během migrace bude stav zařízení nahlášen jako **Přiřazení**.

4. Kliknutím na **Uložit** povolíte opětovné zřízení zařízení na základě vašich změn.

    ![Vybrat zásadu přidělení zápisu](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Odeslání požadavku na zřízení ze zařízení

Aby zařízení, která mají být znovu zřízena na základě změn konfigurace provedených v předchozích částech, tato zařízení musí požádat o opětovné zřízení. 

Jak často zařízení odešle požadavek na zřizování závisí na scénáři. Doporučuje se však naprogramovat vaše zařízení k odeslání požadavku na zřízení do instance služby zřizování při restartování a podporovat [metodu](../iot-hub/iot-hub-devguide-direct-methods.md) ruční aktivace zřizování na vyžádání. Zřizování může být také spuštěna nastavením [požadované vlastnosti](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example). 

Zásady opětovného zřízení v položce registrace určují, jak instance služby zřizování zařízení zpracovává tyto požadavky na zřizování a zda by měla být během opětovného zřizování migrována data stavu zařízení. Stejné zásady jsou k dispozici pro jednotlivé registrace a skupiny registrací:

Například kód odesílání požadavků na zřizování ze zařízení během spouštěcí sekvence, naleznete [v tématu Automatické zřizování simulovaného zařízení](quick-create-simulated-device.md).


## <a name="next-steps"></a>Další kroky

- Další informace o opětovném zřízení najdete v tématu [Koncepty opětovného zřizování zařízení centra IoT Hub](concepts-device-reprovision.md) 
- Další informace o zrušení zřízení najdete v tématu [Jak odzřízení zařízení, která byla dříve automaticky zřízených](how-to-unprovision-devices.md) 











