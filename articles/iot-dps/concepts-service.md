---
title: Koncepty služeb ve službě Azure IoT Hub DeviceProvisioning Service | Dokumenty společnosti Microsoft
description: Popisuje koncepty zřizování služeb specifické pro zařízení se službou Device Provisioning Service (DPS) a službou IoT Hub.
author: nberdy
ms.author: nberdy
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f42502ac4db12a060af5906243d3f8e7584c5df3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79285211"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>Koncepty služby zřizování zařízení služby IoT Hub

Služba zřizování zařízení služby IoT Hub je pomocná služba pro službu IoT Hub, kterou používáte ke konfiguraci zřizování zařízení bez dotykového ovládání do určeného centra IoT Hub. Se službou zřizování zařízení můžete [automaticky zřídit](concepts-auto-provisioning.md) miliony zařízení bezpečným a škálovatelným způsobem.

Zřizování zařízení je dvoudílný proces. První část je vytvoření počáteční spojení mezi zařízením a řešení IoT *registrací* zařízení. Druhá část je použití správné *konfigurace* na zařízení na základě specifických požadavků řešení. Po dokončení obou kroků bylo zařízení plně *zřídit*. Služba Device Provisioning oba kroky automatizuje a zajišťuje tak bezproblémové zřizování zařízení.

Tento článek poskytuje přehled konceptů, které se nejvíce vztahují na správu *služby*. Tento článek je nejrelevantnější pro osoby zapojené do [kroku nastavení cloudu,](about-iot-dps.md#cloud-setup-step) který připravuje zařízení pro nasazení.

## <a name="service-operations-endpoint"></a>Koncový bod operací služby

Koncový bod operací služby je koncový bod pro správu nastavení služby a udržování seznamu registrací. Tento koncový bod používá pouze správce služby. zařízení ji nepoužívají.

## <a name="device-provisioning-endpoint"></a>Koncový bod zřizování zařízení

Koncový bod zřizování zařízení je jediný koncový bod, který všechna zařízení používají pro automatické zřizování. Adresa URL je stejná pro všechny instance zřizovací služby, aby se eliminovala potřeba reflash zařízení s novými informacemi o připojení ve scénářích dodavatelského řetězce. Obor ID zajišťuje izolaci klienta.

## <a name="linked-iot-hubs"></a>Propojená centra IoT

Služba zřizování zařízení může zřizovat zařízení jenom do center IoT, které s ní byly propojeny. Propojení služby IoT hub s instancí služby Device Provisioning poskytuje službě oprávnění ke čtení a zápisu do registru zařízení služby. pomocí odkazu může služba Device Provisioning zaregistrovat ID zařízení a nastavit počáteční konfiguraci v dvojčeti zařízení. Propojené služby IoT huby mohou být v libovolné oblasti Azure. Centra v jiných předplatných můžete propojit s vaší zřizovací službou.

## <a name="allocation-policy"></a>Zásady přidělení

Nastavení úrovně služeb, které určuje, jak služba Device Provisioning service přiřazuje zařízení k centru IoT. Existují tři podporované zásady přidělování:

* **Rovnoměrně vážené rozdělení**: propojené centra IoT mají stejně pravděpodobně zařízení, která jsou pro ně zřízena. Výchozí nastavení. Pokud zřizujete zařízení pouze v jednom centru IoT, můžete nechat toto nastavení.

* **Nejnižší latence**: zařízení se zřazují do centra IoT hub s nejnižší latencí zařízení. Pokud by více propojených center IoT poskytovalo stejnou nejnižší latenci, zřizovací služba hashu zařízení napříč těmito rozbočovači

* **Statická konfigurace prostřednictvím seznamu zápisu**: specifikace požadovaného centra IoT v seznamu zápisu má přednost před zásadami přidělení na úrovni služby.

## <a name="enrollment"></a>Registrace

Registrace je záznam zařízení nebo skupin zařízení, které se mohou zaregistrovat prostřednictvím automatického zřizování. Záznam registrace obsahuje informace o zařízení nebo skupině zařízení, včetně:
- mechanismus [ověřování](concepts-security.md#attestation-mechanism) používaný přístrojem
- volitelná počáteční požadovaná konfigurace
- požadované centrum IoT
- požadované ID zařízení

Služba Device Provisioning Service podporuje dva typy registrací:

### <a name="enrollment-group"></a>Skupina zápisu

Skupina zápisu je skupina zařízení, která sdílejí specifický mechanismus ověřování. Skupiny zápisů podporují x.509 i symetrické. Všechna zařízení ve skupině pro zápis X.509 představují certifikáty X.509, které byly podepsány stejnou kořenovou nebo zprostředkující certifikační autoritou (CA). Každé zařízení ve skupině symetrický klíč zápis usebeů tokeny SAS odvozené ze skupiny symetrický klíč. Název skupiny zápisu a název certifikátu musí být alfanumerické, malá písmena a mohou obsahovat pomlčky.

> [!TIP]
> Doporučujeme používat skupinu registrace pro velký počet zařízení, která sdílejí požadovanou počáteční konfiguraci, nebo pro zařízení, která přejdou do stejného klienta.

### <a name="individual-enrollment"></a>Individuální zápis

Individuální registrace je položka pro jedno zařízení, které se může zaregistrovat. Jednotlivé zápisy mohou jako mechanismy ověřování používat listy X.509 nebo tokeny SAS (z fyzického nebo virtuálního čipu TPM). ID registrace v jednotlivých zápisech je alfanumerické, malá písmena a může obsahovat pomlčky. Jednotlivé registrace můžou mít zadané požadované ID zařízení centra IoT.

> [!TIP]
> Doporučujeme používat jednotlivé registrace pro zařízení, která vyžadují jedinečné počáteční konfigurace, nebo pro zařízení, která lze ověřit pouze pomocí tokenů SAS prostřednictvím ověřování tpm.

## <a name="registration"></a>Registrace

Registrace je záznam zařízení, které úspěšně registruje/zřizovat do služby IoT Hub prostřednictvím služby Zřizování zařízení. Záznamy registrace jsou vytvářeny automaticky; lze je odstranit, ale nelze je aktualizovat.

## <a name="operations"></a>Provoz

Operace jsou fakturační jednotkou služby Device Provisioning Service. Jednou z operací je úspěšné dokončení jedné instrukce do služby. Operace zahrnují registrace zařízení a opětovné registrace; operace také zahrnují změny na straně služby, jako je přidání položek seznamu zápisu a aktualizace položek seznamu zápisu.
