---
title: Povolit Azure Security Center pro službu IoT v IoT Hub | Microsoft Docs
description: Naučte se, jak ve IoT Hub povolit Azure Security Center pro službu IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/16/2019
ms.author: mlottner
ms.openlocfilehash: a794ccea13323f38b20906458e216f85652bfc3e
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596961"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Rychlý start: Připojení Azure Security Center služby IoT v IoT Hub

Tento článek popisuje, jak povolit Azure Security Center pro službu IoT v existujícím IoT Hub. Pokud v tuto chvíli nemáte IoT Hub, přečtěte si téma [vytvoření IoT Hub pomocí Azure Portal](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) pro začátek. 

> [!NOTE]
> Azure Security Center pro IoT v současné době podporuje pouze centra IoT úrovně Standard.
> Azure Security Center pro IoT je jediné řešení. Pokud budete potřebovat více rozbočovačů, vyžadují se několik Azure Security Center řešení IoT. 

## <a name="prerequisites-for-enabling-the-service"></a>Předpoklady pro povolení služby

- Pracovní prostor Log Analytics
  - Ve výchozím nastavení se ve vašem Log Analytics pracovním prostoru ukládají dva typy informací Azure Security Center pro IoT. **výstrahy** a **doporučení**zabezpečení. 
  - Můžete zvolit, že chcete přidat úložiště pro další typ informací, **nezpracované události**. Pamatujte, že ukládání nezpracovaných **událostí** v Log Analytics přináší další náklady na úložiště. 
- IoT Hub (úroveň Standard)
- Splnění všech [požadavků služby](service-prerequisites.md) 

|Podporované oblasti služeb Azure | ||
|---|---|---|
| Střední USA |East US |Východní USA 2 |
| Středozápadní USA |USA – západ |Západní USA 2 |
| Střed USA – jih|Středoseverní USA | Kanada – střed|
| Kanada – východ| Severní Evropa|Brazílie – jih|
| Francie – střed| Spojené království – západ|Velká Británie – jih|
|Západní Evropa|Severní Evropa| Japonsko – západ|
|Japonsko – východ | Austrálie – jihovýchod|Austrálie – východ|
|Východní Asie| Jihovýchodní Asie| Jižní Korea – střed|
|Jižní Korea – jih| Střed Indie| Jižní Indie|
|

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>Povolení Azure Security Center pro IoT na IoT Hub 

Pokud chcete povolit zabezpečení IoT Hub, udělejte toto: 

1. Otevřete **IoT Hub** v Azure Portal. 
1. V nabídce **zabezpečení** klikněte na **zabezpečení řešení IoT** .
1. Jako výchozí ponechte vybranou **možnost Povolit** . 
1. Vyberte svůj pracovní prostor Log Analytics.
1. Zadejte podrobnosti o svém pracovním prostoru Log Analytics. 
   - Vyberte možnost povolit **dvojí shromažďování** tím, že zachováte **přepínač s** **dvojitou kolekcí** .
   - Výběrem možnosti **Uložit nezpracované události zabezpečení zařízení** v Log Analytics se kromě výchozích typů úložišť rozhodnete ukládat nezpracované **události** . Ponechte přepínač nezpracovaná **událost** **zapnutý**. 
    
1. Klikněte na **Uložit**. 

Blahopřejeme! Dokončili jste povolování Azure Security Center pro IoT na IoT Hub. 

## <a name="next-steps"></a>Další postup

Přejděte k dalšímu článku a nakonfigurujte vaše řešení...

> [!div class="nextstepaction"]
> [Konfigurace řešení](quickstart-configure-your-solution.md)


