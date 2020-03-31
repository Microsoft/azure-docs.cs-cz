---
title: Služba zřizování zařízení služby Azure IoT Hub – koncepty zařízení
description: Popisuje koncepty opětovného zřizování zařízení pro službu DPS služby Azure IoT Hub .
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 2bf369b784cddf307abc59d2b8766fc8a87e0985
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975342"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>Koncepty reprovisioningu zařízení centra IoT Hub

Během životního cyklu řešení IoT je běžné přesouvat zařízení mezi centry IoT. Důvody tohoto kroku mohou zahrnovat následující scénáře:

* **Geolocation / GeoLatency**: Jako zařízení se pohybuje mezi umístěními, latence sítě se zlepší tím, že zařízení migrováno do bližšího centra IoT Hub.

* **Víceklient :** Zařízení může být použito v rámci stejného řešení IoT a přeřazeno novému zákazníkovi nebo webu zákazníka. Tento nový zákazník může být obsluhován pomocí jiného centra IoT hub.

* **Změna řešení**: Zařízení může být přesunuto do nového nebo aktualizovaného řešení IoT. Toto přeřazení může vyžadovat, aby zařízení komunikovalo s novým centrem IoT hub, který je připojený k jiným back-endovým součástem.

* **Karanténa**: Podobně jako změna řešení. Zařízení, které je nefunkční, kompromitované nebo zastaralé, může být znovu přiřazeno k centru IoT hubu, které může jenom aktualizovat a vrátit se do souladu s předpisy. Jakmile zařízení funguje správně, je pak přeneseno zpět do hlavního rozbočovače.

Reprovisioning podpora v rámci služby zřizování zařízení řeší tyto potřeby. Zařízení lze automaticky přeřadit do nových center IoT na základě zásad opětovného zřízení, které jsou nakonfigurované v položce registrace zařízení.

## <a name="device-state-data"></a>Data o stavu zařízení

Data o stavu zařízení se skládají z [dvojčete zařízení](../iot-hub/iot-hub-devguide-device-twins.md) a možností zařízení. Tato data jsou uložena v instanci služby Zřizování zařízení a v centru IoT, ke kterému je zařízení přiřazeno.

![Zřizování pomocí služby Zřizování zařízení](./media/concepts-device-reprovisioning/dps-provisioning.png)

Když je zařízení zpočátku zřízeno s instancí služby Zřizování zařízení, provedese následující kroky:

1. Zařízení odešle požadavek na zřízení instanci služby Zřizování zařízení. Instance služby ověřuje identitu zařízení na základě položky registrace a vytvoří počáteční konfiguraci dat o stavu zařízení. Instance služby přiřadí zařízení k centru IoT na základě konfigurace registrace a vrátí přiřazení služby IoT hub zařízení.

2. Instance zřizovací služby poskytuje kopii všech počátečních dat stavu zařízení do přiřazené služby IoT hub. Zařízení se připojí k přiřazené službě IoT hub a zahájí operace.

V průběhu času mohou být data o stavu zařízení v centru IoT aktualizována [operacemi zařízení](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) a [operacemi back-endu](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations). Informace o počátečním stavu zařízení uložené v instanci služby Zřizování zařízení zůstanou nedotčeny. Tato data o stavu nedotčená zařízení jsou počáteční konfigurací.

![Zřizování pomocí služby Zřizování zařízení](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

V závislosti na scénáři, jako zařízení přesune mezi ioT huby, může být také nutné migrovat stav zařízení aktualizovány na předchozí ioT hub do nového centra IoT hub. Tato migrace je podporována obnovením zásad ve službě Device Provisioning Service.

## <a name="reprovisioning-policies"></a>Zásady opětovného zřízení

V závislosti na scénáři zařízení obvykle odešle požadavek na instanci zřizovací služby při restartování. Podporuje také metodu ruční aktivace zřizování na vyžádání. Zásady opětovného zřízení v položce registrace určují, jak instance služby zřizování zařízení zpracovává tyto požadavky na zřizování. Zásada také určuje, zda by měla být během opětovného zřizování migrována data o stavu zařízení. Stejné zásady jsou k dispozici pro jednotlivé registrace a skupiny registrací:

* **Opětovné zřizování a migrace dat**: Tato zásada je výchozí pro nové položky zápisu. Tato zásada provede akci, když zařízení přidružená k položce registrace odešle nový požadavek (1). V závislosti na konfiguraci položky registrace může být zařízení znovu přiřazeno k jinému centru IoT hub. Pokud zařízení mění centra IoT, registrace zařízení s počátečním centrem IoT hub se odebere. Aktualizované informace o stavu zařízení z tohoto počátečního centra IoT hub budou přeneseny do nového centra IoT hub (2). Během migrace bude stav zařízení nahlášen jako **Přiřazení**.

    ![Zřizování pomocí služby Zřizování zařízení](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **Opětovné zřízení a obnovení počáteční konfigurace**: Tato zásada provede akci, když zařízení přidružená k položce registrace odešle nový požadavek na zřizování (1). V závislosti na konfiguraci položky registrace může být zařízení znovu přiřazeno k jinému centru IoT hub. Pokud zařízení mění centra IoT, registrace zařízení s počátečním centrem IoT hub se odebere. Počáteční konfigurační data, která instance zřizovací služby obdržela při zřízení zařízení, jsou k dispozici v novém centru IoT hub (2). Během migrace bude stav zařízení nahlášen jako **Přiřazení**.

    Tato zásada se často používá pro obnovení továrního nastavení bez změny služby IoT huby.

    ![Zřizování pomocí služby Zřizování zařízení](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Nikdy znovu nezřazovat**: Zařízení je nikdy znovu přiřazeno k jinému rozbočovači. Tato zásada je k dispozici pro správu zpětné kompatibility.

### <a name="managing-backwards-compatibility"></a>Správa zpětné kompatibility

Před zářím 2018 došlo k nepřístupu zařízení do center IoT Hubs. Když zařízení přešel zpět prostřednictvím procesu zřizování, by se přiřazeny pouze zpět do stejného centra IoT hub.

Pro řešení, která přijala závislost na toto chování, zřizovací služba zahrnuje zpětnou kompatibilitu. Toto chování je v současné době udržována pro zařízení podle následujících kritérií:

1. Zařízení se připojují s verzí rozhraní API před dostupností nativní podpory opětovného zřízení ve službě Device Provisioning Service. Viz tabulka rozhraní API níže.

2. Položka registrace pro zařízení nemá nastavené zásady opětovného zřízení.

Tato kompatibilita zajišťuje, že dříve nasazená zařízení zaznamenat stejné chování, které je k dispozici během počátečního testování. Chcete-li zachovat předchozí chování, neukládejte zásady opětovného zřízení těchto registrací. Pokud je nastavena zásada opětovného zřízení, má zásada opětovného zřízení přednost před chováním. Povolením zásad y reprovisioning upřednostnění mohou zákazníci aktualizovat chování zařízení, aniž by museli zařízení znovu sledovat.

Následující vývojový diagram pomáhá zobrazit, když je k dispozici chování:

![vývojový diagram zpětné kompatibility](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

V následující tabulce jsou uvedeny verze rozhraní API před dostupností nativní podpory opětovného zřízení ve službě Device Provisioning Service:

| REST API | C SDK | Python SDK |  Node SDK | Java SDK | .NET SDK |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 a starší](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 a starší](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 a starší](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 nebo starší](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 nebo starší](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 nebo starší](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Tyto hodnoty a odkazy se pravděpodobně změní. Toto je pouze zástupný pokus o určení, kde mohou být verze určeny zákazníkem a jaké budou očekávané verze.

## <a name="next-steps"></a>Další kroky

* [Jak znovu zřídit zařízení](how-to-reprovision.md)
