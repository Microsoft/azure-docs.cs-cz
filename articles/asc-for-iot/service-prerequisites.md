---
title: Azure Security Center pro požadavky IoT | Microsoft Docs
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
ms.date: 07/28/2019
ms.author: mlottner
ms.openlocfilehash: cc2dc3a190e3ad06bdc048f2a5770eae2a6990ec
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596826"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Azure Security Center pro požadavky IoT

Tento článek obsahuje vysvětlení různých stavebních bloků Azure Security Center (ASC) pro službu IoT, co je potřeba začít, a vysvětluje základní koncepty, které vám pomůžou pochopit službu. 

## <a name="minimum-requirements"></a>Minimální požadavky

- Úroveň Standard IoT Hub
    - Oprávnění na úrovni **vlastníka** role RBAC 
- [Pracovní prostor Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Azure Security Center (doporučeno)
    - Použití Azure Security Center je doporučení, nikoli požadavek. Bez Azure Security Center nebudete moci zobrazit další prostředky Azure v rámci IoT Hub. 
 
## <a name="working-with-asc-for-iot-service"></a>Práce se službou ASC for IoT

ASC pro IoT Insights a vytváření sestav jsou k dispozici pomocí Azure IoT Hub a Azure Security Center. Pokud chcete na IoT Hub Azure povolit ASC pro IoT, musí se zadat účet s oprávněním na úrovni **vlastníka** . Po povolení funkce ASC pro IoT v IoT Hub se ASC pro IoT Insights zobrazuje jako funkce **zabezpečení** v Azure IoT Hub a jako **IoT** v Azure Security Center. 

## <a name="supported-service-regions"></a>Podporované oblasti služby 

ASC pro IoT se v současné době podporuje pro centra IoT v následujících oblastech Azure:
  - Střed USA  
  - East US 
  - Východní USA 2
  - Západní střed USA
  - USA – západ
  - Západní USA 2
  - Střed USA – jih
  - Střed USA – sever
  - Kanada – střed
  - Kanada – východ 
  - Severní Evropa    
  - Brazílie – jih
  - Francie – střed  
  - Spojené království – západ 
  - Velká Británie – jih
  - Západní Evropa 
  - Severní Evropa 
  - Japonsko – západ  
  - Japonsko – východ  
  - Austrálie – jihovýchod
  - Austrálie – východ
  - Východní Asie   
  - Jihovýchodní Asie
  - Jižní Korea – střed
  - Jižní Korea – jih 
  - Střed Indie
  - Jižní Indie
  
## <a name="wheres-my-iot-hub"></a>Kde je můj IoT Hub?

Než začnete, zkontrolujte IoT Hub umístění a ověřte dostupnost služby. 

1. Otevřete IoT Hub. 
2. Klikněte na **Přehled**. 
3. Ověřte, že uvedené umístění odpovídá jedné z [podporovaných oblastí služby](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Podporované platformy pro agenty 

Zprostředkovatelé ASC pro IoT podporují rostoucí seznam zařízení a platforem. Podívejte se na [seznam podporovaných platforem](how-to-deploy-agent.md) pro kontrolu existující nebo plánované knihovny zařízení.  

## <a name="next-steps"></a>Další postup
- Přečtěte si [Přehled](overview.md) zabezpečení Azure IoT.
- Informace o tom, jak [službu povolit](quickstart-onboard-iot-hub.md)
- Přečtěte si [Nejčastější dotazy k Azure Security Center pro IoT](resources-frequently-asked-questions.md)
- Seznamte se s tím, jak [porozumět Azure Security Center pro výstrahy IoT](concept-security-alerts.md)
