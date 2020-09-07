---
title: 'Rychlý Start: konfigurace řešení'
description: V tomto rychlém startu se dozvíte, jak nakonfigurovat komplexní řešení IoT pomocí Azure Security Center pro IoT.
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
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: e6ab713715cacc799d2b980c2bce2a2a15b76887
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505450"
---
# <a name="quickstart-configure-your-iot-solution"></a>Rychlý Start: konfigurace řešení IoT

Tento článek poskytuje vysvětlení, jak provést počáteční konfiguraci řešení zabezpečení IoT pomocí Azure Security Center pro IoT.

## <a name="azure-security-center-for-iot"></a>Azure Security Center for IoT

Azure Security Center pro IoT poskytují ucelené zabezpečení pro řešení IoT založená na Azure.

Díky Azure Security Center pro IoT můžete monitorovat celé řešení IoT na jednom řídicím panelu, zpřístupnění všechna vaše zařízení IoT, platformy IoT a prostředky back-endu v Azure.

Po povolení IoT Hub Azure Security Center pro IoT automaticky identifikuje další služby Azure, připojí se taky k vašemu IoT Hub a v souvislosti s vaším řešením IoT.

Kromě automatického zjišťování vztahů můžete také vybrat, které další skupiny prostředků Azure chcete označit jako součást řešení IoT.

Vaše výběry umožňují přidat celé odběry, skupiny prostředků nebo jeden prostředek.

Po definování všech vztahů prostředků Azure Security Center pro IoT využít Azure Security Center k poskytnutí doporučení a výstrah zabezpečení pro tyto prostředky.

## <a name="add-azure-resources-to-your-iot-solution"></a>Přidání prostředků Azure do řešení IoT

Pokud chcete přidat nový prostředek do řešení IoT, udělejte toto:

1. Otevřete **IoT Hub** v Azure Portal.
1. V levé nabídce vyberte a otevřete **Nastavení** v části **zabezpečení** a pak vyberte **monitorované prostředky**.
1. Vyberte **Upravit** a vyberte monitorované prostředky, které patří do vašeho řešení IoT.
1. Klikněte na **Přidat**.

Blahopřejeme! Přidali jste do řešení IoT novou skupinu prostředků.

Azure Security Center pro IoT teď monitoruje nově přidávané skupiny prostředků a v rámci vašeho řešení IoT obnáší relevantní doporučení a výstrahy zabezpečení.

## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte, jak vytvářet moduly zabezpečení...

> [!div class="nextstepaction"]
> [Vytváření modulů zabezpečení](quickstart-create-security-twin.md)
