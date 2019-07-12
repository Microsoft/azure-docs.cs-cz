---
title: Azure Security Center pro IoT požadavky ve verzi Preview | Dokumentace Microsoftu
description: Podrobnosti o vše potřebné pro začátek práce s Azure Security Center pro požadavky služby IoT.
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
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 198459887ff19b16e897b2a8dde55bca1217c8ac
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616432"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Azure Security Center pro požadavky IoT

> [!IMPORTANT]
> Azure Security Center pro IoT je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek obsahuje vysvětlení různých stavební kameny nástroje Azure Security Center (ASC) IoT služby, co vám umožní začít a základní koncepty se chcete o službě. 

## <a name="minimum-requirements"></a>Minimální požadavky

- Úroveň Standard centra IoT
    - RBAC role **vlastníka** úroveň oprávnění 
- [Pracovní prostor log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Azure Security Center (doporučeno)
    - Při použití služby Azure Security Center je jenom na doporučení a není povinné bez toho nebudete moci zobrazit ostatní prostředky Azure ve službě IoT Hub. 
 
## <a name="working-with-asc-for-iot-service"></a>Práce s ASC pro služby Azure IoT

ASC pro IoT přehledy a sestavy jsou k dispozici prostřednictvím služby Azure IoT Hub a Azure Security Center. Povolit ASC pro IoT ve službě Azure IoT Hub s účtem **vlastníka** úrovně oprávnění je povinný. Po povolení ASC pro IoT ve službě IoT Hub, ASC IoT přehledy zobrazují jako **zabezpečení** funkcí ve službě Azure IoT Hub a jako **IoT** ve službě Azure Security Center. 

## <a name="supported-service-regions"></a>Oblasti podporované služby 

ASC pro IoT je aktuálně podporované pro IoT hub v těchto oblastech Azure:
  - Střed USA
  - Severní Evropa
  - Jihovýchodní Asie

## <a name="wheres-my-iot-hub"></a>Kde je Moje Centrum IoT?

Zkontrolujte umístění vaší služby IoT Hub k ověření dostupnosti služby, než začnete. 

1. Otevřete své Centrum IoT. 
2. Klikněte na **Přehled**. 
3. Ověřte umístění uvedená odpovídá jednomu z [podporované oblasti služby](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Podporované platformy pro agenty 

ASC pro agenty IoT podporuje rostoucí seznam zařízení a platformy. Najdete v článku [podporované platformy seznamu](how-to-deploy-agent.md) zkontrolovat vaše existující nebo plánovaný knihovna zařízení.  

## <a name="next-steps"></a>Další kroky
- [Přehled](overview.md)
- [Povolit službu](quickstart-onboard-iot-hub.md)
- [ASC pro IoT – nejčastější dotazy](resources-frequently-asked-questions.md)
- [Principy ASC pro IoT výstrahy](concept-security-alerts.md)
