---
title: Koncepce služeb v Azure IoT Hub Device Provisioning Service | Microsoft Docs
description: Popisuje koncepty zřizování služeb, které jsou specifické pro zařízení se službou Device Provisioning Service (DPS) a IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f42502ac4db12a060af5906243d3f8e7584c5df3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79285211"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>IoT Hub Device Provisioning Service koncepty

IoT Hub Device Provisioning Service je pomocná služba pro IoT Hub, která slouží ke konfiguraci zařízení s nulovým dotykem pro zadané centrum IoT. Se službou Device Provisioning můžete [automaticky zřizovat](concepts-auto-provisioning.md) miliony zařízení zabezpečeným a škálovatelným způsobem.

Zřizování zařízení je proces dvou částí. První část vytváří počáteční připojení mezi zařízením a řešením IoT tím, že zařízení *registruje* . Druhá část aplikuje správnou *konfiguraci* na zařízení na základě konkrétních požadavků řešení. Po dokončení obou kroků se zařízení kompletně *zřídilo*. Služba Device Provisioning oba kroky automatizuje a zajišťuje tak bezproblémové zřizování zařízení.

Tento článek poskytuje přehled konceptů zřizování, které jsou použitelné pro správu *služby*. Tento článek se nejvíce hodí pro osoby zapojené do [Nastavení cloudu](about-iot-dps.md#cloud-setup-step) , které vám pomůžou při přípravě zařízení na nasazení.

## <a name="service-operations-endpoint"></a>Koncový bod operací služby

Koncový bod operací služby je koncový bod pro správu nastavení služby a údržbu seznamu registrací. Tento koncový bod používá jenom Správce služby. zařízení je nepoužívá.

## <a name="device-provisioning-endpoint"></a>Koncový bod zřizování zařízení

Koncový bod zřizování zařízení je jedním koncovým bodem, který všechna zařízení používají pro Automatické zřizování. Adresa URL je stejná pro všechny instance služby zřizování a eliminuje nutnost opětovného doplňování zařízení s novými informacemi o připojení ve scénářích dodavatelských řetězců. Rozsah ID zajišťuje izolaci tenanta.

## <a name="linked-iot-hubs"></a>Propojené IoT Huby

Služba Device Provisioning může zřizovat zařízení jenom pro centra IoT, která jsou s ní propojená. Propojení služby IoT Hub s instancí služby Device Provisioning poskytuje oprávnění ke čtení a zápisu do registru zařízení služby IoT Hub. pomocí tohoto odkazu může služba Device Provisioning zaregistrovat ID zařízení a nastavit počáteční konfiguraci v zařízení s dvojitou vazbou. Propojená centra IoT můžou být v libovolné oblasti Azure. Můžete propojit centra v jiných předplatných s vaší službou zřizování.

## <a name="allocation-policy"></a>Zásady přidělování

Nastavení na úrovni služby, které určuje, jak služba Device Provisioning přiřazuje zařízení do služby IoT Hub. Existují tři podporované zásady přidělování:

* **Rovnoměrně vážená distribuce**: propojená centra IoT mají stejně často zařízení, která jsou jim zřízena. Výchozí nastavení. Pokud zřizujete zařízení pouze v jednom centru IoT, můžete nechat toto nastavení.

* **Nejnižší latence**: zařízení se zřídí do služby IoT Hub s nejnižší latencí pro zařízení. Pokud by více propojených hub IoT poskytovalo stejnou nejnižší latenci, služba zřizování vyhodnotí hodnoty hash zařízení v těchto centrech.

* **Statická konfigurace prostřednictvím seznamu**registrací: specifikace požadovaného centra IoT v seznamu registrací má prioritu pro zásady přidělování na úrovni služby.

## <a name="enrollment"></a>Registrace

Registrace je záznam zařízení nebo skupin zařízení, která se můžou registrovat prostřednictvím automatického zřizování. Záznam zápisu obsahuje informace o zařízení nebo skupině zařízení, včetně:
- [mechanismus ověřování](concepts-security.md#attestation-mechanism) používaný zařízením
- Volitelná počáteční požadovaná konfigurace
- požadované centrum IoT Hub
- požadované ID zařízení

Služba Device Provisioning podporuje dva typy registrace:

### <a name="enrollment-group"></a>Skupina registrace

Skupina registrace je skupina zařízení, která sdílejí konkrétní mechanismus ověřování. Skupiny registrací podporují rozhraní X. 509 i symetrický. Všechna zařízení ve skupině pro registraci X. 509 obsahují certifikáty X. 509 podepsané stejnou kořenovou nebo zprostředkující certifikační autoritou (CA). Každé zařízení ve skupině pro zápis symetrického klíče prezentuje tokeny SAS odvozené ze skupiny symetrického klíče. Název skupiny registrací a název certifikátu musí být alfanumerické a malými písmeny a můžou obsahovat spojovníky.

> [!TIP]
> Doporučujeme používat skupinu registrací pro velký počet zařízení, která sdílejí požadovanou počáteční konfiguraci, nebo pro všechna zařízení, která se budou napojovat do stejného tenanta.

### <a name="individual-enrollment"></a>Jednotlivé registrace

Jednotlivá registrace je záznam pro jedno zařízení, které se může zaregistrovat. Jednotlivé registrace můžou jako mechanismus ověřování použít buď certifikáty na list X. 509, nebo tokeny SAS (od fyzického nebo virtuálního čipu TPM). ID registrace v individuální registraci je alfanumerické a malé písmeno a může obsahovat spojovníky. Jednotlivé registrace můžou mít zadané požadované ID zařízení centra IoT.

> [!TIP]
> Pro zařízení, která vyžadují jedinečné počáteční konfigurace, doporučujeme používat jednotlivé registrace nebo pro zařízení, která se dají ověřovat jenom pomocí tokenů SAS prostřednictvím ověření identity čipem TPM.

## <a name="registration"></a>Registrace

Registrace znamená, že se zařízení úspěšně zaregistrovalo nebo zřídí do IoT Hub prostřednictvím služby Device Provisioning. Registrační záznamy jsou vytvořeny automaticky; je možné je odstranit, ale nelze je aktualizovat.

## <a name="operations"></a>Operace

Operace jsou fakturační jednotka služby Device Provisioning. Jedna operace představuje úspěšné dokončení jedné instrukce ke službě. Operace zahrnují registraci a opakovanou registraci zařízení. operace také zahrnují změny na straně služby, jako je přidání položek seznamu registrací a aktualizace položek seznamu registrací.
