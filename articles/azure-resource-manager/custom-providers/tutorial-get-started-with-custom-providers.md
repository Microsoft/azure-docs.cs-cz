---
title: Vytváření vlastních akcí a prostředků
description: V tomto kurzu se naučíte vytvářet vlastní akce a prostředky v Azure Resource Manager. Také ukazuje, jak vlastní pracovní postupy spolupracují s Azure Resource Managermi šablonami, Azure CLI, Azure Policy a protokolem aktivit Azure.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 45f18727b53b802ba746da41b47fe955543ed9d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75649894"
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

V tomto kurzu jste se dozvěděli o vlastních poskytovatelích a o tom, jak je sestavit. Pokud chcete pokračovat k dalšímu kurzu, přečtěte si téma [kurz: nastavení Azure Functions pro vlastní poskytovatele Azure](./tutorial-custom-providers-function-setup.md).

Pokud hledáte odkazy nebo rychlý Start, tady je několik užitečných odkazů:

- [Rychlý Start: Vytvoření vlastního poskytovatele prostředků Azure a nasazení vlastních prostředků](./create-custom-provider.md)
- [Postupy: Přidání vlastních akcí do Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Postupy: Přidání vlastních prostředků do Azure REST API](./custom-providers-resources-endpoint-how-to.md)
