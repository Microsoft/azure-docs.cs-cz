---
title: Azure IoT Hub Device Provisioning Service – koncepty zařízení
description: Popisuje koncepty opětovného zřízení zařízení pro Azure IoT Hub Device Provisioning Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 9653a584382584d982c55008a6e8547de28691b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91842848"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>Koncepce opětovného zřizování zařízení IoT Hub

Během životního cyklu řešení IoT je běžné přesouvat zařízení mezi centra IoT. Důvody pro tento přesun můžou zahrnovat následující scénáře:

* **Zeměpisná poloha/geografická latence**: když se zařízení pohybuje mezi místy, zlepšuje se latence sítě díky tomu, že se zařízení migruje do bližšího IoT Hub.

* **Víceklientská architektura: zařízení** se dá používat v rámci stejného řešení IoT a znovu se přiřadí novému zákazníkovi nebo webu zákazníka. Tento nový zákazník se může obsluhovat pomocí jiného centra IoT.

* **Změna řešení**: zařízení se přesunulo do nového nebo aktualizovaného řešení IoT. Tato změna přiřazení může vyžadovat, aby zařízení komunikovalo s novou službou IoT Hub, která je připojená k ostatním back-endové součásti.

* **Quarantine**: podobná změně řešení. Zařízení, které je nefunkční, napadené nebo zastaralé, může být přiřazeno ke centru IoT, které může aktualizovat a vrátit se do kompatibility. Jakmile zařízení správně funguje, migruje se zpátky do hlavního centra.

Tato potřeba řeší opětovné zřizování podpory v rámci služby Device Provisioning. Zařízení je možné automaticky znovu přiřadit novým centrům IoT na základě zásad opětovného zřizování, která je nakonfigurovaná v položce registrace zařízení.

## <a name="device-state-data"></a>Data o stavu zařízení

Data o stavu zařízení se skládají z možností [zařízení a zařízení, která jsou](../iot-hub/iot-hub-devguide-device-twins.md) v pořádku. Tato data jsou uložená v instanci služby Device Provisioning a službě IoT Hub, ke které je zařízení přiřazené.

![Diagram, který ukazuje, jak zřizování funguje se službou Device Provisioning.](./media/concepts-device-reprovisioning/dps-provisioning.png)

Při počátečním zřizování zařízení s instancí služby Device Provisioning se provádí tyto kroky:

1. Zařízení odešle požadavek na zřízení instance služby Device Provisioning. Instance služby ověří identitu zařízení na základě položky registrace a vytvoří počáteční konfiguraci dat o stavu zařízení. Instance služby přiřadí zařízení ke službě IoT Hub na základě konfigurace registrace a vrátí toto přiřazení služby IoT Hub k zařízení.

2. Instance služby zřizování poskytuje kopii všech počátečních dat o stavu zařízení do přiřazeného centra IoT Hub. Zařízení se připojí k přiřazenému centru IoT a zahájí operace.

V průběhu času může být data o stavu zařízení ve službě IoT Hub aktualizována pomocí [operací zařízení](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) a [back-endové operace](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations). Počáteční informace o stavu zařízení uložené v instanci služby Device Provisioning zůstanou beze změny. Tato nepřipojená data stavu zařízení jsou počáteční konfigurací.

![Zřizování se službou Device Provisioning Service](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

V závislosti na scénáři může být v závislosti na tom, že se zařízení pohybuje mezi centry IoT, může být také nutné migrovat stav zařízení aktualizovaný v předchozí službě IoT Hub do nového centra IoT Hub. Tato migrace je podporovaná zásadami opětovného zřizování ve službě Device Provisioning.

## <a name="reprovisioning-policies"></a>Zásady opětovného zřizování

V závislosti na scénáři zařízení obvykle odesílá požadavek na instanci zřizovací služby při restartování. Podporuje také metodu ručního spuštění zřizování na vyžádání. Zásady opětovného zřizování na položce registrace určují, jak instance služby Device Provisioning zpracovává tyto požadavky zřizování. Zásady taky určují, jestli se mají při opětovném zřizování migrovat data stavu zařízení. Pro jednotlivé registrace a skupiny registrací jsou k dispozici stejné zásady:

* **Opětovné zřízení a migrace dat**: Tato zásada je výchozím nastavením pro nové položky registrace. Tato zásada provede akci, když zařízení přidružená k položce registrace odesílají novou žádost (1). V závislosti na konfiguraci položky registrace se může zařízení přiřadit k jinému centru IoT. Pokud zařízení mění centra IoT, odeberou se registrace zařízení pomocí počátečního centra IoT Hub. Aktualizované informace o stavu zařízení z tohoto počátečního centra IoT budou migrovány do nového centra IoT (2). Během migrace bude stav zařízení hlášeno jako **přiřazení**.

    ![Diagram znázorňující, že zásada provede akci, když zařízení přidružená k položce registrace odesílají novou žádost.](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **Opětovné zřízení a resetování na počáteční konfiguraci**: Tato zásada provede akci, když zařízení přidružená k položce registrace odesílají novou žádost o zřízení (1). V závislosti na konfiguraci položky registrace se může zařízení přiřadit k jinému centru IoT. Pokud zařízení mění centra IoT, odeberou se registrace zařízení pomocí počátečního centra IoT Hub. Počáteční konfigurační data, která instance zřizovací služby přijatá při zřízení zařízení, se poskytují nové službě IoT Hub (2). Během migrace bude stav zařízení hlášeno jako **přiřazení**.

    Tato zásada se často používá k obnovení továrního nastavení bez změny centra IoT.

    ![Diagram, který ukazuje, jak zásada provede akci, když zařízení přidružená k položce registrace odesílají novou žádost o zřízení.](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Nikdy nezřizování**: zařízení se nikdy znovu nepřiřazuje k jinému centru. Tato zásada je k dispozici pro správu zpětné kompatibility.

### <a name="managing-backwards-compatibility"></a>Správa zpětné kompatibility

V průběhu září 2018 mělo přiřazení zařízení do centra IoT rychlé chování. Když se zařízení během procesu zřizování převedlo, dá se přiřadit zpátky ke stejné službě IoT Hub.

U řešení, která se v tomto chování provedla, zahrnuje služba zřizování zpětnou kompatibilitu. Toto chování je v současnosti udržováno pro zařízení v závislosti na následujících kritériích:

1. Zařízení se připojují k verzi rozhraní API předtím, než bude dostupná podpora nativního opětovného zajišťování ve službě Device Provisioning. Podívejte se na tabulku rozhraní API níže.

2. Položka registrace pro zařízení nemá nastavenou zásadu opětovného zřízení.

Tato kompatibilita zajistí, že dříve nasazená zařízení mají stejné chování jako při počátečním testování. Chcete-li zachovat předchozí chování, neukládejte zásady opětovného zřizování pro tyto registrace. Pokud je nastavená zásada opětovného zřizování, má zásada opětovného zřizování přednost před chováním. Díky umožnění přednosti zásad opětovného zřizování můžou zákazníci aktualizovat chování zařízení, aniž by museli zařízení obnovit z image.

Následující vývojový diagram pomáhá Ukázat, kdy je chování k dispozici:

![Graf toku zpětné kompatibility](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

V následující tabulce jsou uvedené verze rozhraní API před tím, než bude dostupná podpora nativního opětovného zajišťování ve službě Device Provisioning:

| REST API | SADA C SDK | Python SDK |  Node SDK | Java SDK | .NET SDK |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 a starší](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 a starší](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 a starší](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 nebo starší](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 nebo starší](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 nebo starší](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Tyto hodnoty a odkazy se budou nejspíš měnit. Jedná se pouze o zástupný symbol, který určuje, kde mohou být verze určeny zákazníkem a jaké budou očekávané verze.

## <a name="next-steps"></a>Další kroky

* [Postup opětovného zřízení zařízení](how-to-reprovision.md)
