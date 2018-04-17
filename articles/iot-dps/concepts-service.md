---
title: Služba koncepty ve službě Azure IoT Hub zařízení zřizování Service | Microsoft Docs
description: Popisuje zřizování koncepty služby specifické pro zařízení s distribučních bodů a IoT Hub
services: iot-dps
keywords: ''
author: nberdy
ms.author: nberdy
ms.date: 03/30/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: d2bc58514ea716954ec3ac96151549168fedc2ed
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>Koncepty služby zřizování zařízení IoT Hub

Služba zřizování zařízení IoT Hub je služba Pomocník pro IoT Hub, který použijete ke konfiguraci zařízení nula touch zřizování do zadané služby IoT hub. Se službou zřizování zařízení můžete [automatického zřizování](concepts-auto-provisioning.md) miliony zařízení zabezpečené a škálovatelné způsobem.

Zřizování zařízení je proces dvě části. První část je vytvoření počátečního připojení mezi zařízením a řešení IoT podle *registrace* zařízení. Druhá část je použití správné *konfigurace* na zařízení, v závislosti na konkrétních požadavcích řešení. Po dokončení obou kroků zařízení bylo plně *zřízený*. Služba Device Provisioning oba kroky automatizuje a zajišťuje tak bezproblémové zřizování zařízení.

Tento článek poskytuje přehled zřizování konceptů nejvíce pro správu *služby*. Tento článek je nejdůležitější osoby, které se účastní [cloudu kroku](about-iot-dps.md#cloud-setup-step) z Příprava zařízení pro nasazení.

## <a name="service-operations-endpoint"></a>Koncový bod služby operations

Koncový bod služby operations je koncový bod pro správu nastavení služby a údržbu seznamu registrace. Tento koncový bod je použita pouze správcem služby; není využíván zařízení.

## <a name="device-provisioning-endpoint"></a>Koncový bod zřizování zařízení

Koncový bod zřizování zařízení je jeden koncový bod, který všechna zařízení použít pro automatické zřizování. Adresa URL je stejný pro všechny zřizování instance služby, eliminovat potřebu reflash v řetězu scénáře dodávky zařízení s nové informace o připojení. [ID oboru](#id-scope) zajišťuje izolaci klientů.

## <a name="linked-iot-hubs"></a>Propojená centra IoT Hub

Služba zřizování zařízení můžete zřídit jenom zařízení do centra IoT, které byly spojeny s ho. Propojení služby IoT hub službě zřizování zařízení poskytuje služby oprávnění pro čtení a zápis do registru zařízení služby IoT hub; s odkazem může služba zřizování zařízení zaregistrovat ID zařízení a nastavte počáteční konfigurace v dvojče zařízení. Propojené služby IoT hubs může být v libovolné oblasti Azure. Centra v jiných předplatných může propojit zřizování služby.

## <a name="allocation-policy"></a>Přidělení zásad

Úrovně služeb, nastavení, která určuje, jak služba zřizování zařízení přiřadí zařízení do služby IoT hub. Existují tři podporované zásady přidělování:
* **Rovnoměrně váha distribuce**: propojené centra IoT jsou stejně může mít zřízení k nim zařízení. Výchozí nastavení. Pokud zřizujete zařízení pouze v jednom centru IoT, můžete nechat toto nastavení.
* **Nejnižší latenci**: zřízení zařízení do služby IoT hub s nejnižší latencí do zařízení. Více propojené služby IoT hubs by poskytovat stejné nejnižší latenci, zřizování služby hashuje zařízení v těchto rozbočovače
* **Konfigurace statické prostřednictvím seznamu registrace**: specifikace požadované služby IoT hub v seznamu registrace přednost zásada přidělování úrovně služeb.

## <a name="enrollment"></a>Zápis

Zápisu je záznam zařízení nebo skupin zařízení, která může zaregistrovat prostřednictvím automatické zřizování. Záznam zápisu obsahuje informace o zařízení nebo skupině zařízení, včetně:
- [ověření mechanismu](concepts-security.md#attestation-mechanism) používá zařízení
- volitelné počáteční požadované konfigurace
- požadované služby IoT hub
- ID požadovaného zařízení

Existují dva typy registrace podporován službou zřizování zařízení:

### <a name="enrollment-group"></a>Registrace skupiny

Skupinu registrace je skupina zařízení, které sdílejí mechanismus konkrétní ověření. Všechna zařízení ve skupině registrace k dispozici certifikáty X.509, které byly podepsány stejnou kořenovou nebo zprostředkující certifikační Autority. Registrace skupiny lze použít pouze mechanismus ověření X.509. Název skupiny pro zápis a název certifikátu musí být alfanumerické znaky a malých písmen a může obsahovat pomlčky.

> [!TIP]
> Doporučujeme používat skupinu registrace pro velký počet zařízení, které sdílejí požadované počáteční konfigurace, nebo pro zařízení všechny má stejné klienta.

### <a name="individual-enrollment"></a>Jednotlivé registrace

Jednotlivé zápisu je záznam pro jedno zařízení, která může zaregistrovat. Jednotlivé registrace používat certifikáty X.509 typu list nebo tokeny SAS (od fyzické nebo virtuální čipu TPM), jak mechanismů ověření. ID registrace v jednotlivých zápisu je alfanumerické znaky a malých písmen a může obsahovat pomlčky. Jednotlivé registrace můžou mít zadané požadované ID zařízení centra IoT.

> [!TIP]
> Doporučujeme používat jednotlivé registrace pro zařízení, které vyžadují jedinečné počáteční konfigurace, nebo pro zařízení, která lze pouze ověření pomocí tokeny SAS prostřednictvím ověření čipu TPM.

## <a name="registration"></a>Registrace

Registrace je záznam zařízení úspěšně registrace nebo zřizování do služby IoT Hub prostřednictvím služby zřizování zařízení. Záznamy registrace jsou vytvořeny automaticky. můžete je odstranit, ale nelze aktualizovat.

## <a name="operations"></a>Operace

Operace jsou fakturace jednotka služby zřizování zařízení. Jedna operace je úspěšné dokončení jedné instrukce ke službě. Operace zahrnují registraci a opakovanou registraci zařízení a také změny na straně služby, jako je přidání položek seznamu registrací a jejich aktualizace.
