---
title: Služba konceptech v Azure IoT Hub Device Provisioning Service | Dokumentace Microsoftu
description: Popisuje zřizování koncepty služby specifické pro zařízení pomocí služby Azure Device Provisioning a centrem IoT
author: nberdy
ms.author: nberdy
ms.date: 03/30/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: ca2ea3c000e811223ded3022021c2516f547ae66
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "42054026"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>Koncepty IoT Hub Device Provisioning Service

IoT Hub Device Provisioning Service je pomocná služba pro IoT Hub, který použijete ke konfiguraci plně automatizované zřizování zařízení pro určité Centrum IoT. Ve službě Device Provisioning Service, můžete [automatické zřizování](concepts-auto-provisioning.md) miliony zařízení bezpečným a škálovatelným způsobem.

Zřizování zařízení provádí dvě části. První část je vytvoření počátečního připojení mezi zařízením a řešení IoT pomocí *registrace* zařízení. Druhá část je použití správných *konfigurace* zařízení podle konkrétních požadavků řešení. Po dokončení obou kroků zařízení po plně *zřízené*. Služba Device Provisioning oba kroky automatizuje a zajišťuje tak bezproblémové zřizování zařízení.

Tento článek s přehledem zřizování konceptů, které jsou vhodné pro správu *služby*. Tento článek je relevantní osobám, které jsou součástí [krok instalace cloud](about-iot-dps.md#cloud-setup-step) získání zařízení připravené na nasazení.

## <a name="service-operations-endpoint"></a>Operace koncového bodu služby

Koncový bod služby operací je koncový bod pro správu nastavení služby a údržbu seznamu registrací. Tento koncový bod používá pouze správce služeb; používá se zařízeními.

## <a name="device-provisioning-endpoint"></a>Koncový bod zřizování zařízení

Koncový bod pro zřizování zařízení je jeden koncový bod, který všechna zařízení používat pro účely automatického zřízení. Adresa URL je stejný pro všechny zřizování instance služby, eliminuje nutnost reflash zařízení pomocí nové informace o připojení ve scénářích dodavatelského řetězce. Rozsah ID zajistí izolaci klientů.

## <a name="linked-iot-hubs"></a>Propojená centra IoT Hub

Služby Device Provisioning můžete zřizovat zařízení pouze centra IoT hub, které byly spojeny s ji. Propojení centra IoT na instanci služby Device Provisioning poskytuje služby oprávnění čtení a zápis do registru zařízení služby IoT hub. služby Device Provisioning pomocí odkazu, který může vytvářet ID zařízení a nastavili počáteční konfiguraci ve dvojčeti zařízení. Propojená centra IoT hub může být v libovolné oblasti Azure. Může propojit hubs v jiných předplatných k vaší službě zřizování.

## <a name="allocation-policy"></a>Zásady přidělování

Úrovně služeb, nastavení, která určuje, jak přiřadí zařízení do služby IoT hub Device Provisioning Service. Existují tři podporované zásady přidělování:

* **Rovnoměrně vážená distribuce**: propojená centra IoT hub se stejnou pravděpodobností zařízení se zřizují k nim. Ve výchozím nastavení. Pokud zřizujete zařízení pouze v jednom centru IoT, můžete nechat toto nastavení.

* **Nejnižší latence**: zařízení se zřizují do služby IoT hub s nejnižší latencí na zařízení. Pokud by více propojená centra IoT hub stejnou nejnižší latenci, zřizovací služba hashuje zařízení mezi tyto uzly

* **Statická konfigurace přes seznam registrací**: specifikace požadovaného centra IoT v seznamu registrací má přednost před zásady přidělování na úrovni služby.

## <a name="enrollment"></a>Registrace

Registrace je záznam zařízení nebo skupin zařízení, které se můžou zaregistrovat prostřednictvím automatického zřizování. Záznam registrace obsahuje informace o zařízení nebo skupině zařízení, včetně:
- [mechanismus ověřování](concepts-security.md#attestation-mechanism) použité v zařízení
- volitelné počáteční požadované konfigurace
- požadované centrum IoT hub
- ID požadované zařízení

Existují dva typy registrací služby Device Provisioning podporuje:

### <a name="enrollment-group"></a>Skupiny registrací

Skupinu registrací je skupina zařízení, která sdílí konkrétní mechanismus ověřování. Všechna zařízení ve skupině pro registraci k dispozici certifikátů X.509 podepsaných stejnou kořenovou nebo zprostředkující certifikační autority (CA). Skupiny registrací lze použít pouze mechanismus ověřování X.509. Název skupiny registrací a název certifikátu musí být alfanumerický, malá písmena a může obsahovat pomlčky.

> [!TIP]
> Doporučujeme použít skupinu registrací pro velký počet zařízení, která sdílí požadovanou počáteční konfiguraci, nebo pro zařízení budou patřit do stejného tenanta.

### <a name="individual-enrollment"></a>Jednotlivé registrace

Jednotlivé registrace je záznam pro jedno zařízení, který může zaregistrovat. Jednotlivé registrace můžou jako mechanismus ověřování pomocí listové certifikáty X.509 nebo tokeny SAS (z fyzického nebo virtuálního čipu TPM). ID registrace v jednotlivou registraci je alfanumerický, malá písmena a může obsahovat pomlčky. Jednotlivé registrace můžou mít zadané požadované ID zařízení centra IoT.

> [!TIP]
> Doporučujeme používat jednotlivé registrace zařízení, která vyžadují jedinečnou počáteční konfiguraci, nebo pro zařízení, která můžete ověřit pouze pomocí tokeny SAS prostřednictvím ověření identity čipem TPM.

## <a name="registration"></a>Registrace

Registrace je záznam úspěšně registraci a zřizování zařízení do služby IoT Hub přes služby Device Provisioning. Záznamy registrace jsou vytvořeny automaticky. můžete je odstranit, ale nejde aktualizovat.

## <a name="operations"></a>Operace

Operace jsou fakturační jednotka služby Device Provisioning. Jedna operace je úspěšné dokončení jedné instrukce ke službě. Operace zahrnují registraci a opakovanou registraci zařízení a také změny na straně služby, jako je přidání položek seznamu registrací a jejich aktualizace.
