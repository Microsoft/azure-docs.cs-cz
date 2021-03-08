---
title: 'Rychlý Start: Přidání prostředků Azure do řešení IoT'
description: V tomto rychlém startu se dozvíte, jak nakonfigurovat komplexní řešení IoT pomocí Azure Defenderu pro IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: Shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2021
ms.author: shhazam
ms.openlocfilehash: afe62e5cf255df28ea395405fc894ec5c15bb18c
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449709"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>Rychlý Start: konfigurace řešení Azure Defender pro IoT

Tento článek poskytuje vysvětlení, jak provést počáteční konfiguraci řešení zabezpečení IoT pomocí programu Defender pro IoT.

## <a name="prerequisites"></a>Požadavky

Žádné

## <a name="what-is-defender-for-iot"></a>Co je Defender pro IoT?

Defender pro IoT poskytuje ucelené zabezpečení pro řešení IoT založená na Azure.

S programem Defender pro IoT můžete monitorovat celé řešení IoT v jednom řídicím panelu, zpřístupnění všechna vaše zařízení IoT, platformy IoT a prostředky back-endu v Azure.

Po povolení v IoT Hub Defender pro IoT automaticky identifikuje další služby Azure, které se také připojí k vašemu IoT Hub a souvisí s vaším řešením IoT.

Kromě automatického zjišťování vztahů můžete také vybrat, které další skupiny prostředků Azure chcete označit jako součást řešení IoT.

Vaše výběry umožňují přidat celé odběry, skupiny prostředků nebo jeden prostředek.

Po definování všech vztahů prostředků Defender pro IoT používá Defender k poskytnutí doporučení a výstrah zabezpečení pro tyto prostředky.

## <a name="add-azure-resources-to-your-iot-solution"></a>Přidání prostředků Azure do řešení IoT

Přidání nového prostředku do řešení IoT:

1. Otevřete **IoT Hub** v Azure Portal.

1. V části **zabezpečení** vyberte **Přehled** a **potom** vyberte **monitorované prostředky**.

1. Vyberte **Upravit** a vyberte monitorované prostředky, které patří do vašeho řešení IoT.

1. Vyberte **Přidat**.

Gratulujeme! Přidali jste do řešení IoT novou skupinu prostředků.

Defender pro IoT teď monitoruje nově přidávané skupiny prostředků a v rámci vašeho řešení IoT surfuje relevantní doporučení a výstrahy zabezpečení.

## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte, jak vytvářet moduly zabezpečení...

> [!div class="nextstepaction"]
> [Vytváření modulů zabezpečení](quickstart-create-security-twin.md)
