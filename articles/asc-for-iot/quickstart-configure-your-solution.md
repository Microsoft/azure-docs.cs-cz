---
title: Konfigurace Azure Security Center pro řešení IoT | Microsoft Docs
description: Naučte se, jak nakonfigurovat komplexní řešení IoT pomocí Azure Security Center pro IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 15c8aabbcb19d009bb202d111dc7f80da4cebff1
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597029"
---
# <a name="quickstart-configure-your-iot-solution"></a>Rychlý start: Konfigurace řešení IoT

Tento článek poskytuje vysvětlení, jak provést počáteční konfiguraci řešení zabezpečení IoT pomocí Azure Security Center pro IoT. 

## <a name="azure-security-center-for-iot"></a>Azure Security Center for IoT

Azure Security Center pro IoT poskytují ucelené zabezpečení pro řešení IoT založená na Azure.

Díky Azure Security Center pro IoT můžete monitorovat celé řešení IoT na jednom řídicím panelu, zpřístupnění všechna vaše zařízení IoT, platformy IoT a prostředky back-endu v Azure.

Po povolení v IoT Hub Azure Security Center pro IoT automaticky identifikuje další služby Azure, které se také připojí k vašemu centru IoT a souvisí s vaším řešením IoT.

Kromě automatického zjišťování vztahů můžete také vybrat, které další skupiny prostředků Azure chcete označit jako součást řešení IoT.

Vaše výběry umožňují přidat celé odběry, skupiny prostředků nebo jeden prostředek.

Po definování všech vztahů prostředků Azure Security Center pro IoT využít Azure Security Center k poskytnutí doporučení a výstrah zabezpečení pro tyto prostředky.

## <a name="add-azure-resources-to-your-iot-solution"></a>Přidání prostředků Azure do řešení IoT

Pokud chcete přidat nový prostředek do řešení IoT, udělejte toto: 

1. Otevřete **IoT Hub** v Azure Portal. 
2. V nabídce vlevo vyberte a otevřete **prostředky** v části **zabezpečení** . 
3. Vyberte **Upravit** a zvolte skupiny prostředků, které patří do vašeho řešení IoT.
5. Klikněte na **Přidat**. 

Blahopřejeme! Přidali jste do řešení IoT novou skupinu prostředků.

Azure Security Center pro IoT teď monitoruje nově přidávané skupiny prostředků a v rámci vašeho řešení IoT obnáší relevantní doporučení a výstrahy zabezpečení.

## <a name="next-steps"></a>Další postup

V dalším článku se dozvíte, jak vytvářet moduly zabezpečení...

> [!div class="nextstepaction"]
> [Vytváření modulů zabezpečení](quickstart-create-security-twin.md)