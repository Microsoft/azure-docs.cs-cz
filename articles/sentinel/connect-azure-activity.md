---
title: Připojení dat aktivit Azure k Azure Sentinelu | Dokumenty společnosti Microsoft
description: Zjistěte, jak propojit data aktivit y Azure s Azure Sentinelem.
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
ms.openlocfilehash: 82dfcaf3394703aae531c828a1b96ad290bab798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124988"
---
# <a name="connect-data-from-azure-activity-log"></a>Připojení dat z protokolu aktivit Azure

Protokoly z [protokolu aktivit Azure](../azure-monitor/platform/platform-logs-overview.md) můžete streamovat do Azure Sentinelu jediným kliknutím. Protokol aktivit je protokol předplatného, který zaznamenává a zobrazuje události na úrovni předplatného v rámci Azure, od provozních dat Azure Resource Manageru až po aktualizace událostí stavu služby. Pomocí protokolu aktivit můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) provedené na prostředky ve vašem předplatném. Můžete také zjistit stav operace a další relevantní vlastnosti. Protokol aktivit nezahrnuje operace čtení (GET) nebo operace pro prostředky, které používají model Classic/"RDFE". 

## <a name="prerequisites"></a>Požadavky

- Uživatel musí mít oprávnění přispěvatele do pracovního prostoru Log Analytics.
- Váš uživatel musí mít oprávnění čtečky pro jakékoli předplatné, jehož protokoly, které chcete streamovat do Azure Sentinelu.

## <a name="set-up-the-azure-activity-connector"></a>Nastavení konektoru aktivity Azure

1. V navigační nabídce Azure Sentinel vyberte **Datové konektory**. Ze seznamu konektorů klikněte na **Aktivita Azure**a potom na tlačítko **Otevřít konektor stránky** v pravém dolním.

2. Na kartě **Pokyny** klikněte na odkaz **Konfigurovat protokoly aktivit Azure >.**

3. V podokně **protokolu aktivit Azure** vyberte předplatná, jejichž protokoly, které chcete streamovat do Azure Sentinelu. 

4. V podokně odběrů, které se otevře vpravo, klikněte na **připojit**.

5. Chcete-li použít příslušné schéma v Log Analytics pro `AzureActivity` výstrahy aktivit Azure, zadejte okno dotazu.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit protokol aktivit Azure k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Můžete začít zjišťovat hrozby pomocí Azure Sentinelu pomocí [integrovaných](tutorial-detect-threats-built-in.md) nebo [vlastních](tutorial-detect-threats-custom.md) pravidel.
