---
title: 'Rychlý Start: Přidání prostředků Azure do řešení IoT'
description: V tomto rychlém startu se dozvíte, jak nakonfigurovat komplexní řešení IoT pomocí Azure Defenderu pro IoT.
ms.topic: quickstart
ms.date: 01/25/2021
ms.openlocfilehash: 6a90e8c3007f7b3448fd3f1b6e4fa46ba861081b
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384576"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>Rychlý Start: konfigurace řešení Azure Defender pro IoT

Tento článek poskytuje vysvětlení, jak provést počáteční konfiguraci řešení zabezpečení IoT pomocí programu Defender pro IoT.

## <a name="prerequisites"></a>Požadavky

- Žádné

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

V dalším článku se dozvíte, jak vytvořit Defender – IoT-Micro-Agents...

> [!div class="nextstepaction"]
> [Vytvoření Defenderu – IoT-mikro-agenti](quickstart-create-security-twin.md)
