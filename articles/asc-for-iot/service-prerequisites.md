---
title: Součásti & požadavky
description: Podrobnosti o všech potřebách pro začátek Azure Security Center pro požadavky služby IoT
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2019
ms.author: mlottner
ms.custom: references_regions
ms.openlocfilehash: 9ce903271accd3f63df7634dc5045932655ac2a7
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2020
ms.locfileid: "87531145"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Azure Security Center pro požadavky IoT

Tento článek obsahuje vysvětlení různých komponent Azure Security Center pro službu IoT, co je potřeba začít, a vysvětluje základní koncepty, které vám pomůžou pochopit službu.

## <a name="minimum-requirements"></a>Minimální požadavky

- Úroveň Standard IoT Hub
  - Oprávnění na úrovni **vlastníka** role Azure
- [Pracovní prostor Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Azure Security Center (doporučeno)
  - Použití Azure Security Center je doporučení, nikoli požadavek. Bez Azure Security Center nebudete moci zobrazit další prostředky Azure v rámci IoT Hub.

## <a name="working-with-azure-security-center-for-iot-service"></a>Práce s Azure Security Center pro službu IoT

Azure Security Center pro IoT Insights a vytváření sestav jsou k dispozici pomocí Azure IoT Hub a Azure Security Center. Pokud chcete povolit Azure Security Center pro IoT na IoT Hub Azure, je potřeba účet s oprávněními na úrovni **vlastníka** . Po povolení funkce ASC pro IoT ve vašem IoT Hub se Azure Security Center pro IoT Insights zobrazí jako funkce **zabezpečení** v Azure IoT Hub a jako **IoT** v Azure Security Center.

## <a name="supported-service-regions"></a>Podporované oblasti služby

Azure Security Center pro IoT se v současné době podporují pro centra IoT v následujících oblastech Azure:

- Střední USA
- East US
- USA – východ 2
- USA – středozápad
- USA – západ
- USA – západ 2
- Střed USA – jih
- USA – středosever
- Střední Kanada
- Kanada – východ
- Severní Evropa
- Brazílie – jih
- Francie – střed
- Spojené království – západ
- Spojené království – jih
- West Europe
- Northern Europe
- Japonsko – západ
- Japonsko – východ
- Austrálie – jihovýchod
- Austrálie – východ
- Východní Asie
- Southeast Asia
- Jižní Korea – střed
- Jižní Korea – jih
- Indie – střed
- Indie – jih

Azure Security Center pro IoT směruje veškerý provoz ze všech evropských oblastí do Západní Evropa oblastního datového centra a všech zbývajících oblastí do Střed USA regionální datové centrum.

## <a name="wheres-my-iot-hub"></a>Kde je můj IoT Hub?

Než začnete, zkontrolujte IoT Hub umístění a ověřte dostupnost služby.

1. Otevřete IoT Hub.
1. Klikněte na **Přehled**.
1. Ověřte, že uvedené umístění odpovídá jedné z [podporovaných oblastí služby](#supported-service-regions).

## <a name="supported-platforms-for-agents"></a>Podporované platformy pro agenty

Azure Security Center pro agenty IoT podporuje rostoucí seznam zařízení a platforem. Podívejte se na [seznam podporovaných platforem](how-to-deploy-agent.md) pro kontrolu existující nebo plánované knihovny zařízení.

## <a name="next-steps"></a>Další kroky

- Přečtěte si [Přehled](overview.md) zabezpečení Azure IoT.
- Informace o tom, jak [službu povolit](quickstart-onboard-iot-hub.md)
- Přečtěte si [Nejčastější dotazy k Azure Security Center pro IoT](resources-frequently-asked-questions.md)
- Seznamte se s tím, jak [porozumět Azure Security Center pro výstrahy IoT](concept-security-alerts.md)
