---
title: Připojení dat o aktivitě Azure ke službě Azure Sentinel | Microsoft Docs
description: Přečtěte si, jak propojit data o aktivitách Azure s Sentinel Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 807a5ede3feee115b1a8dc51fe14966731fc7784
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240776"
---
# <a name="connect-data-from-azure-activity-log"></a>Připojit data z protokolu aktivit Azure



Pomocí jediného kliknutí můžete streamovat protokoly z [Azure Activity log](../azure-monitor/platform/activity-logs-overview.md) do Azure Sentinel. Protokol aktivit je protokol předplatného, který poskytuje přehled o událostech na úrovni předplatného, k nimž došlo v Azure. To zahrnuje rozsah dat, od Azure Resource Manager provozních dat až po aktualizace Service Healthch událostí. Pomocí protokolu aktivit můžete určit, kdo a kdy použít pro všechny operace zápisu (PUT, POST, DELETE) u prostředků v rámci vašeho předplatného. Můžete také zjištění stavu operace a další relevantní vlastnosti. Protokol aktivit nezahrnuje operace čtení (GET) nebo operací pro prostředky, které používají model Classic/"RDFE". 


## <a name="prerequisites"></a>Požadavky

- Uživatel s oprávněními globálního správce nebo správce zabezpečení


## <a name="connect-to-azure-activity-log"></a>Připojit k protokolu aktivit Azure

1. V Azure Sentinel vyberte **datové konektory** a pak klikněte na dlaždici **protokolu aktivit Azure** .

2. V podokně protokol aktivit Azure vyberte předplatná, která chcete streamovat do Azure Sentinel. 

3. Klikněte na **Připojit**.

4. Pokud chcete použít příslušné schéma v Log Analytics pro výstrahy aktivity Azure, vyhledejte **AzureActivity**.


 

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit protokol aktivit Azure ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
