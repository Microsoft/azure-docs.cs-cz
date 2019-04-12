---
title: Jak na bázi zařízení v Azure IoT Hub Device Provisioning Service | Dokumentace Microsoftu
description: Jak na bázi zařízení s vaším zařízením zřizování instance služby
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 92680a453d93c8dc0189c6ae376449a8e7a22076
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59499848"
---
# <a name="how-to-reprovision-devices"></a>Jak na bázi zařízení

Během životního cyklu řešení IoT se běžně přesouvat zařízení mezi IoT huby. Důvody pro tento krok může zahrnovat následující scénáře:

* **Informace o zeměpisné poloze**: Při přesunu zařízení mezi umístěními, zlepší se latence sítě tím, že zařízení migrovat do služby IoT hub blíže s jednotlivými umístěními.

* **Víceklientská architektura**: Zařízení může používat ve stejném řešení IoT, ale znovu přiřazen nebo pronajatý na nového zákazníka nebo zákazníka. Tento nový zákazník může údržba používání různých služby IoT hub.

* **Změna řešení**: Zařízení může přesunout do nové nebo aktualizované řešení IoT. Této změně přiřazení může vyžadovat, aby zařízení komunikovat s nového centra IoT, která je připojena k jiné komponenty back-endu. 

* **Karantény**: Podobně jako řešení změnit. Zařízení, které je nefunkční, ohrožení zabezpečení nebo zastaralý může být přeřazen do služby IoT hub, kde je všechno, co můžete dělat aktualizaci a zase dostat dodržování předpisů. Když zařízení pracuje správně, je potom migrovat zpět na jeho hlavní centra.

Další podrobnější přehled o neukončil, naleznete v tématu [IoT Hub Device neukončil koncepty](concepts-device-reprovision.md).


## <a name="configure-the-enrollment-allocation-policy"></a>Konfigurace zásad přidělování pro zápis

Zásady přidělování Určuje, jak zařízení přidružená k registraci budou přiděleny, nebo přiřazené do služby IoT hub, jakmile ji znovu zajistit.

Následující kroky konfigurace zásad přidělování pro registraci zařízení:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte k vaší instanci služby Device Provisioning.

2. Klikněte na tlačítko **Správa registrací**a klikněte na skupinu registrací nebo jednotlivé registrace, který chcete konfigurovat pro neukončil. 

3. V části **vyberte, jak chcete přiřadit zařízení k centrům**, vyberte jednu z následujících zásad přidělování:

    * **Nejnižší latence**: Tyto zásady přiřadí zařízení k propojené Centrum IoT, který bude mít za následek nejnižší latence komunikace mezi zařízením a centrem IoT. Tato možnost umožní zařízení komunikovat na základě umístění co nejblíže služby IoT hub. 
    
    * **Rovnoměrně vážená distribuce**: Tato zásada distribuuje zařízení mezi propojená centra IoT hub podle váha přidělení přiřazená každé propojené Centrum IoT. Tato zásada umožňuje zařízení na Vyrovnávání zatížení mezi skupinu propojená centra podle váhy přidělení nastavit na těchto rozbočovače. Pokud zřizujete zařízení pouze v jednom centru IoT, doporučujeme toto nastavení. Toto nastavení je výchozí. 
    
    * **Statická konfigurace**: Tato zásada vyžaduje požadované centrum IoT Hub uvedené v položce registrace pro zařízení, které se mají zřídit. Tato zásada umožňuje určit jedno konkrétní Centrum IoT, kterou chcete přiřadit k zařízení.

4. V části **vyberte centra IoT hub je možné přiřadit tato skupina**, vyberte propojená centra IoT hub, které chcete, aby vaše zásady přidělování je součástí. Volitelně můžete přidat nové propojené Iot hubu pomocí **propojit novou službu IoT Hub** tlačítko.

    S **nejnižší latenci** zásady přidělování, hubs vyberete budou zahrnuta ve vyhodnocení latence k určení nejbližší Centrum pro přiřazení zařízení.

    S **rovnoměrně vážená distribuce** zásady přidělování, zařízení budou vyrovnávat zatížení napříč hubs vyberete podle jejich váhy nakonfigurované přidělení a jejich aktuálního zatížení zařízení.

    S **statickou konfiguraci** zásady přidělování, vyberte centrum IoT, které se mají zařízení přiřazená.

4. Klikněte na tlačítko **Uložit**, nebo přejít k další části a nastavit zásady tak reprovisioning.

    ![Vybrat zásady zápisu přidělení](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>Nastavit reprovisioning zásady

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte k vaší instanci služby Device Provisioning.

2. Klikněte na tlačítko **Správa registrací**a klikněte na skupinu registrací nebo jednotlivé registrace, který chcete konfigurovat pro neukončil.

3. V části **vyberte požadovaný způsob zařízení data zpracovávat na nové zřízení do jiné služby IoT hub**, zvolte jednu z následujících reprovisioning zásad:

    * **Znovu zřídit a migraci dat**: Tato zásada pořizuje akce, když zařízení přidružená k položce registrace odešlete novou žádost o zřízení. V závislosti na konfiguraci položku registrace zařízení může být přeřazen jiné služby IoT hub. Pokud zařízení se mění centra IoT hub, odebere se registrace zařízení s počáteční služby IoT hub. Všechny informace o stavu zařízení ze služby IoT hub tento počáteční se budou migrovat na novou službu IoT hub. Během migrace, bude hlásit stav tohoto zařízení **přiřazení**

    * **Znovu zřídit a resetování na počáteční konfigurace**: Tato zásada pořizuje akce, když zařízení přidružená k položce registrace odešlete novou žádost o zřízení. V závislosti na konfiguraci položku registrace zařízení může být přeřazen jiné služby IoT hub. Pokud zařízení se mění centra IoT hub, odebere se registrace zařízení s počáteční služby IoT hub. Počáteční konfigurace data, která je k dispozici zřízení instance služby přijal při zřizování zařízení pro novou službu IoT hub. Během migrace, bude hlásit stav tohoto zařízení **přiřazování**.

4. Klikněte na tlačítko **Uložit** povolit neukončil zařízení na základě provedených změn.

    ![Vybrat zásady zápisu přidělení](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Odeslat žádost o zřízení ze zařízení

Aby se zařízení k opětovnému zajištění podle konfigurace změny provedené v předchozích částech, tato zařízení musíte požádat o neukončil. 

Jak často zařízení odešle žádost o zřízení, závisí na scénáři. Doporučujeme však program vašich zařízení k odeslání zřizování žádosti o zřízení instance služby na restartování a podporu [metoda](../iot-hub/iot-hub-devguide-direct-methods.md) k ruční aktivaci zřizování na vyžádání. Zřizování může taky spustit tím, že nastavíte [požadovanou vlastnost](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example). 

Reprovisioning zásady registrace položky určuje způsob, jakým zpracovává tyto žádosti o zřízení instance služby zřizování zařízení, a pokud se data o stavu zařízení by se měly migrovat během neukončil. Jsou dostupné pro jednotlivé registrace a skupin registrací stejné zásady:

Například zřizování požadavky ze zařízení během sekvence spouštění kódu odesílání najdete v článku [automatické zřízení simulovaného zařízení](quick-create-simulated-device.md).


## <a name="next-steps"></a>Další postup

- Přečtěte si další Reprovisioning, najdete v článku [neukončil koncepty zařízení centra IoT](concepts-device-reprovision.md) 
- Zrušení zřízení Další informace najdete v tématu [jak zrušit zřízení zařízení, které byly dříve automatické zřizování](how-to-unprovision-devices.md) 











