---
title: Vytváření vlastních akcí a prostředků v Azure
description: V tomto kurzu se naučíte vytvářet vlastní akce a prostředky v Azure Resource Manager. Také ukazuje, jak vlastní pracovní postupy spolupracují s Azure Resource Managermi šablonami, Azure CLI, Azure Policy a protokolem aktivit Azure.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: dc1601e344c371a5f0feaadd272a2c6ff40af031
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172936"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Vytváření vlastních akcí a prostředků v Azure

Vlastní poskytovatel je kontrakt mezi Azure a koncovým bodem. Pomocí vlastních zprostředkovatelů můžete změnit pracovní postupy v Azure přidáním nových rozhraní API do Azure Resource Manager. Pomocí těchto vlastních rozhraní API můžou Správce prostředků využívat nové možnosti nasazení a správy.

V tomto kurzu se seznámíte s jednoduchým příkladem, jak přidat nové akce a prostředky do Azure a jak je integrovat.

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>Nastavení Azure Functions pro vlastní zprostředkovatele Azure

V první části tohoto kurzu se dozvíte, jak nastavit aplikaci Azure Functions pro práci s vlastními poskytovateli:

- [Nastavení Azure Functions pro vlastní zprostředkovatele Azure](./tutorial-custom-providers-function-setup.md)

Vlastní zprostředkovatelé můžou pracovat s jakoukoli veřejnou adresou URL.

## <a name="author-a-restful-endpoint-for-custom-providers"></a>Vytvořit RESTful koncový bod pro vlastní zprostředkovatele

Druhá část tohoto kurzu popisuje, jak vytvořit RESTful koncový bod pro vlastní zprostředkovatele:

- [Vytváření RESTful koncového bodu pro vlastní zprostředkovatele](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>Vytvoření a použití vlastního zprostředkovatele

Třetí část tohoto kurzu popisuje, jak vytvořit vlastního poskytovatele a použít vlastní akce a prostředky:

- [Vytvoření a použití vlastního zprostředkovatele](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o vlastních poskytovatelích a o tom, jak je sestavit. Pokud chcete pokračovat k dalšímu kurzu, přečtěte si téma [kurz: Nastavte Azure Functions pro vlastní poskytovatele](./tutorial-custom-providers-function-setup.md)Azure.

Pokud hledáte odkazy nebo rychlý Start, tady je několik užitečných odkazů:

- [Rychlé zprovoznění: Vytvoření vlastního poskytovatele prostředků Azure a nasazení vlastních prostředků](./create-custom-provider.md)
- [Postup: Přidání vlastních akcí do Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Postup: Přidání vlastních prostředků do Azure REST API](./custom-providers-resources-endpoint-how-to.md)
