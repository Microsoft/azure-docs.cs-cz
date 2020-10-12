---
title: Připojení dat o aktivitě Azure ke službě Azure Sentinel | Microsoft Docs
description: Zasílat streamování protokolů aktivit Azure do Azure Sentinel jediným kliknutím. Protokol aktivit zaznamenává a zobrazuje události na úrovni předplatného v rámci Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 88f363406ec40dfa439a52ad351501d1c64dce95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85564489"
---
# <a name="connect-data-from-azure-activity-log"></a>Připojit data z protokolu aktivit Azure

Pomocí jediného kliknutí můžete streamovat protokoly z [Azure Activity log](../azure-monitor/platform/platform-logs-overview.md) do Azure Sentinel. Protokol aktivit je protokol předplatného, který zaznamenává a zobrazuje události na úrovni předplatného v rámci Azure, od Azure Resource Manager provozních dat až po aktualizace Service Healthch událostí. Pomocí protokolu aktivit můžete určit, kdo a kdy se má u prostředků ve vašem předplatném provést operace zápisu (PUT, POST, DELETE). Můžete si také přečíst stav operace a dalších relevantních vlastností. Protokol aktivit nezahrnuje operace čtení (GET) nebo operací pro prostředky, které používají model Classic/"RDFE". 

## <a name="prerequisites"></a>Požadavky

- Uživatel musí mít oprávnění přispěvatele k pracovnímu prostoru Log Analytics.
- Váš uživatel musí mít oprávnění čtenářů pro jakékoli předplatné, jehož protokoly chcete streamovat do služby Azure Sentinel.

## <a name="set-up-the-azure-activity-connector"></a>Nastavení konektoru aktivit Azure

1. V navigační nabídce Azure Sentinel vyberte **datové konektory**. V seznamu konektorů klikněte na **aktivita Azure**a pak na tlačítku pro **otevření konektoru** na pravé straně.

2. Na kartě **pokyny** klikněte na odkaz **Konfigurovat protokoly aktivit Azure >** .

3. V podokně **Protokol aktivit Azure** vyberte odběry, jejichž protokoly chcete streamovat do Azure Sentinel. 

4. V podokně odběr, který se otevře vpravo, klikněte na **připojit**.

5. Pokud chcete použít příslušné schéma v Log Analytics pro upozornění na aktivitu Azure, zadejte `AzureActivity` do okna dotazu.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit protokol aktivit Azure ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte s detekcí hrozeb pomocí služby Azure Sentinel a pomocí [předdefinovaných](tutorial-detect-threats-built-in.md) nebo [vlastních](tutorial-detect-threats-custom.md) pravidel.
