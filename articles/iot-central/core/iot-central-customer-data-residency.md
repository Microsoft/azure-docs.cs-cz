---
title: Zasídlí zákaznických dat v Azure IoT Central | Microsoft Docs
description: Tento článek popisuje zasídlí zákaznických dat v Azure IoT Centralch aplikacích.
author: dominicbetts
ms.author: dobett
ms.date: 11/02/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9cf803ddd5a3c9329cadefffc35ae6d6999323a1
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280690"
---
# <a name="azure-iot-central-customer-data-residency"></a>Zasídlí zákaznických dat v Azure IoT Central

IoT Central neukládají zákaznická data mimo zadané geografické oblasti zákazníka s výjimkou těchto scénářů:

- Když se do existující aplikace IoT Central přidá nový uživatel, může se e-mailová adresa uživatele uložit mimo zeměpisnou verzi, dokud tento přizvaný uživatel nepřipojí k aplikaci poprvé.

- Dlaždice mapového řídicího panelu IoT Central použít [Azure Maps](../../azure-maps/about-azure-maps.md). Když přidáte dlaždici mapy do existující aplikace IoT Central, data o umístění mohou být zpracována nebo ukládána v souladu s pravidly geografického umístění služby Azure Maps.
