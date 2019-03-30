---
title: zahrnout soubor
description: zahrnout soubor
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 710bb8cba7fbbe4bc9b9fdc52b0767c96f97fe72
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631089"
---
## <a name="create-base-resources"></a>Vytváření základních prostředků

Než budete moct nakonfigurovat směrování zpráv, musíte k vytvoření služby IoT hub, účet úložiště a fronty služby Service Bus. Tyto prostředky lze vytvořit pomocí jedné z čtyři články, které jsou dostupné pro části 1 tohoto kurzu: rozhraní příkazového řádku Azure, Azure Powershellu, na webu Azure portal nebo šablony Azure Resource Manageru.

Použijte pro všechny prostředky stejnou skupinu a umístění. Klikněte na konci, můžete odebrat všechny prostředky v jednom kroku tak, že odstraníte skupinu prostředků.

Následující části popisují postup provést.

1. Vytvořte [skupinu prostředků](../articles/azure-resource-manager/resource-group-overview.md).

2. Vytvořte centrum IoT ve vrstvě S1. Přidejte do centra IoT skupinu uživatelů. Skupinu uživatelů používá služba Azure Stream Analytics při získávání dat.

   > [!NOTE]
   > K dokončení tohoto kurzu, je nutné použít v placené úrovně služby Iot hub. Úroveň free umožňuje nastavit jeden koncový bod, a tento kurz vyžaduje více koncových bodů.
   > 

3. Vytvořte standardní účet úložiště V1 s replikací Standard_LRS.

4. Vytvořte oboru názvů a frontu Service Bus.

5. Vytvořte identitu zařízení pro simulované zařízení, které bude odesílat zprávy do vašeho centra. Uložte klíč pro fázi testování. (Pokud vytváříte šablonu Resource Manageru, to se provádí po nasazení šablony.)