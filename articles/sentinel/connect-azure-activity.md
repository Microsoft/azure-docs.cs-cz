---
title: Připojení dat o aktivitě Azure ke službě Azure Sentinel | Microsoft Docs
description: Přečtěte si, jak propojit data o aktivitách Azure s Sentinel Azure.
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
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 28d5acd80deef193c7d2fea46f682f26abd756aa
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588599"
---
# <a name="connect-data-from-azure-activity-log"></a>Připojit data z protokolu aktivit Azure



Pomocí jediného kliknutí můžete streamovat protokoly z [Azure Activity log](../azure-monitor/platform/platform-logs-overview.md) do Azure Sentinel. Protokol aktivit je protokol předplatného, který poskytuje přehled o událostech na úrovni předplatného, k nimž došlo v Azure. To zahrnuje rozsah dat, od Azure Resource Manager provozních dat až po aktualizace Service Healthch událostí. Pomocí protokolu aktivit můžete určit, kdo a kdy použít pro všechny operace zápisu (PUT, POST, DELETE) u prostředků v rámci vašeho předplatného. Můžete také zjištění stavu operace a další relevantní vlastnosti. Protokol aktivit nezahrnuje operace čtení (GET) nebo operací pro prostředky, které používají model Classic/"RDFE". 


## <a name="prerequisites"></a>Předpoklady

- Uživatel s oprávněními přispěvatele pro Log Analytics pracovní prostor 


## <a name="connect-to-azure-activity-log"></a>Připojit k protokolu aktivit Azure

1. V Azure Sentinel vyberte **datové konektory** a pak klikněte na dlaždici **protokolu aktivit Azure** .

2. V podokně protokol aktivit Azure vyberte předplatná, která chcete streamovat do Azure Sentinel. 

3. Klikněte na **Připojit**.

4. Pokud chcete použít příslušné schéma v Log Analytics pro výstrahy aktivity Azure, vyhledejte **AzureActivity**.


 

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit protokol aktivit Azure ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
