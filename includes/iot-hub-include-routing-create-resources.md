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
ms.openlocfilehash: 5680bd1c3fadab8edcde1634c708ca8683c0e99c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76020874"
---
## <a name="create-base-resources"></a>Vytvoření základních prostředků

Než budete moct nakonfigurovat směrování zpráv, musíte vytvořit centrum IoT, účet úložiště a frontu Service Bus. Tyto prostředky je možné vytvořit pomocí jednoho ze čtyř článků, které jsou k dispozici v části 1 tohoto kurzu: Azure Portal, Azure Resource Manager šablona, rozhraní příkazového řádku Azure nebo Azure PowerShell.

Použijte pro všechny prostředky stejnou skupinu a umístění. Pak na konci můžete odebrat všechny prostředky v jednom kroku odstraněním skupiny prostředků.

Níže je uveden seznam kroků, které je třeba provést v následujících částech: 

1. Vytvořte [skupinu prostředků](../articles/azure-resource-manager/management/overview.md).

2. Vytvořte centrum IoT ve vrstvě S1. Přidejte do centra IoT skupinu uživatelů. Skupinu uživatelů používá služba Azure Stream Analytics při získávání dat.

   > [!NOTE]
   > K dokončení tohoto kurzu musíte použít Centrum IoT v placené úrovni. Úroveň Free umožňuje nastavit pouze jeden koncový bod a tento kurz vyžaduje více koncových bodů.
   > 

3. Vytvořte standardní účet úložiště V1 s replikací Standard_LRS.

4. Vytvořte oboru názvů a frontu Service Bus.

5. Vytvořte identitu zařízení pro simulované zařízení, které bude odesílat zprávy do vašeho centra. Uložte klíč pro fázi testování. (Pokud vytváříte šablonu Správce prostředků, to se provádí po nasazení šablony.)