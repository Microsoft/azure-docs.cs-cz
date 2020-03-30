---
title: Předpoklady Centra zabezpečení Azure pro IoT| Dokumenty společnosti Microsoft
description: Podrobnosti o všem, co potřebujete k zahájení práce s požadavky služby Azure Security Center for IoT.
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
ms.openlocfilehash: 4440fec98d1f561da6375bcaadba4282076cc53b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71299478"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Předpoklady Centra zabezpečení Azure pro IoT

Tento článek obsahuje vysvětlení různých stavebních bloků služby Azure Security Center for IoT, co je potřeba začít a vysvětluje základní koncepty, které vám pomohou pochopit službu. 

## <a name="minimum-requirements"></a>Minimální požadavky

- Úroveň Standard centra IoT
    - Oprávnění úrovně **vlastníka** role RBAC 
- [Pracovní prostor analýzy protokolů](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Azure Security Center (doporučeno)
    - Použití Azure Security Center je doporučení a není požadavek. Bez Azure Security Center nebudete moci zobrazit další prostředky Azure v rámci služby IoT Hub. 
 
## <a name="working-with-azure-security-center-for-iot-service"></a>Práce se službou Azure Security Center for IoT

Přehledy a sestavy Azure Security Center for IoT jsou dostupné pomocí Azure IoT Hub a Azure Security Center. Pokud chcete povolit Azure Security Center pro IoT na azure iot hub, je vyžadován účet s oprávněními na úrovni **vlastníka.** Po povolení ASC pro IoT ve vašem IoT Hub, Azure Security Center pro IoT přehledy se zobrazí jako funkce **zabezpečení** v Azure IoT Hub a jako **IoT** v Azure Security Center. 

## <a name="supported-service-regions"></a>Podporované oblasti služeb 

Azure Security Center pro IoT je momentálně podporované pro Centra IoT v následujících oblastech Azure:
  - USA – střed  
  - USA – východ 
  - USA – východ 2
  - USA – středozápad
  - USA – západ
  - USA – západ 2
  - Střední USA – jih
  - USA – středosever
  - Střední Kanada
  - Kanada – východ 
  - Severní Evropa    
  - Brazílie – jih
  - Francie – střed  
  - Spojené království – západ 
  - Spojené království – jih
  - Západní Evropa 
  - Northern Europe 
  - Japonsko – západ  
  - Japonsko – východ  
  - Austrálie – jihovýchod
  - Austrálie – východ
  - Východní Asie   
  - Jihovýchodní Asie
  - Jižní Korea – střed
  - Jižní Korea – jih 
  - Indie – střed
  - Indie – jih

Azure Security Center pro IoT směruje veškerý provoz ze všech evropských oblastí do regionálního datového centra západní Evropy a všech zbývajících oblastí do centrálního amerického regionálního datového centra.  
  
## <a name="wheres-my-iot-hub"></a>Kde je můj IoT Hub?

Než začnete, zkontrolujte umístění služby IoT Hub a ověřte dostupnost služby. 

1. Otevřete svůj IoT Hub. 
2. Klikněte na **Přehled**. 
3. Ověřte, zda uvedené umístění odpovídá jedné z [podporovaných oblastí služeb](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Podporované platformy pro agenty 

Azure Security Center pro agenty IoT podporuje rostoucí seznam zařízení a platforem. Podívejte se na [seznam podporovaných platforem](how-to-deploy-agent.md) a zkontrolujte stávající nebo plánovanou knihovnu zařízení.  

## <a name="next-steps"></a>Další kroky
- Přečtěte si [přehled](overview.md) zabezpečení Azure IoT
- Přečtěte si, jak [povolit službu](quickstart-onboard-iot-hub.md)
- Přečtěte si [nejčastější dotazy k Centru zabezpečení Azure pro IoT](resources-frequently-asked-questions.md)
- Zjistěte, jak [porozumět výstrahám Azure Security Center for IoT](concept-security-alerts.md)
