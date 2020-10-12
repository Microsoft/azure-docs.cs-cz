---
title: Součásti & požadavky
description: Podrobnosti všeho potřebného pro zahájení práce s Azure Defenderem pro požadavky služby IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.custom: references_regions
ms.openlocfilehash: 19263f8db58c8d20288d3ae74c24efd85667bc33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90935325"
---
# <a name="azure-defender-for-iot-prerequisites"></a>Předpoklady pro Azure Defender pro IoT

Tento článek obsahuje vysvětlení různých komponent programu Defender pro službu IoT, co je potřeba začít, a vysvětluje základní koncepty, které vám pomůžou pochopit službu.

## <a name="minimum-requirements"></a>Minimální požadavky

- Úroveň Standard IoT Hub
  - Oprávnění na úrovni **vlastníka** role Azure
- [Pracovní prostor Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Azure Security Center (doporučeno)
  - Použití Azure Security Center je doporučení, nikoli požadavek. Bez Azure Security Center nebudete moci zobrazit další prostředky Azure v rámci IoT Hub.

## <a name="working-with-defender-for-iot-service"></a>Práce s Defenderem pro službu IoT

Defender pro IoT Insights a vytváření sestav je dostupný pomocí Azure IoT Hub a Azure Security Center. Pokud chcete povolit Defender pro IoT na IoT Hub Azure, je potřeba účet s oprávněními na úrovni **vlastníka** . Po povolení funkce ASC pro IoT ve vašem IoT Hub se Defender pro IoT Insights zobrazuje jako funkce **zabezpečení** v Azure IoT Hub a jako  **IoT** v Azure Security Center.

## <a name="supported-service-regions"></a>Podporované oblasti služby

Defender for IoT se v současné době podporuje pro centra IoT v následujících oblastech Azure:

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
- Brazil South
- Francie – střed
- Spojené království – západ
- Spojené království – jih
- West Europe
- Northern Europe
- Japonsko – západ
- Japan East
- Australia Southeast
- Austrálie – východ
- Východní Asie
- Southeast Asia
- Jižní Korea – střed
- Jižní Korea – jih
- Indie – střed
- Indie – jih

Defender pro IoT směruje veškerý provoz ze všech evropských oblastí do Západní Evropa oblastních datových center a všech zbývajících oblastí do Střed USA oblastního datového centra.

## <a name="wheres-my-iot-hub"></a>Kde je můj IoT Hub?

Než začnete, zkontrolujte IoT Hub umístění a ověřte dostupnost služby.

1. Otevřete IoT Hub.
1. Klikněte na **Přehled**.
1. Ověřte, že uvedené umístění odpovídá jedné z [podporovaných oblastí služby](#supported-service-regions).

## <a name="supported-platforms-for-agents"></a>Podporované platformy pro agenty

Defender pro agenty IoT podporuje rostoucí seznam zařízení a platforem. Podívejte se na [seznam podporovaných platforem](how-to-deploy-agent.md) pro kontrolu existující nebo plánované knihovny zařízení.

## <a name="next-steps"></a>Další kroky

- Přečtěte si [Přehled](overview.md) zabezpečení Azure IoT.
- Informace o tom, jak [službu povolit](quickstart-onboard-iot-hub.md)
- Přečtěte si [Nejčastější dotazy k Defenderu pro IoT](resources-frequently-asked-questions.md)
- Prozkoumejte, jak [porozumět programu Defender pro výstrahy IoT](concept-security-alerts.md)
