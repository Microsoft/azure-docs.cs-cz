---
title: Vytváření vlastních akcí a prostředků
description: Tento kurz se podrobně popisuje, jak vytvořit vlastní akce a prostředky ve Správci prostředků Azure. Také ukazuje, jak vlastní pracovní postupy spolupracují se šablonami Azure Resource Manager, Azure CLI, Azure Policy a Azure Activity Log.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 45f18727b53b802ba746da41b47fe955543ed9d0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75649894"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Vytváření vlastních akcí a prostředků v Azure

Vlastní zprostředkovatel je smlouva mezi Azure a koncovýbod. S vlastními poskytovateli můžete změnit pracovní postupy v Azure přidáním nových api do Azure Resource Manageru. Pomocí těchto vlastních api může Správce prostředků používat nové možnosti nasazení a správy.

Tento kurz prochází jednoduchý příklad, jak přidat nové akce a prostředky do Azure a jak je integrovat.

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>Nastavení funkcí Azure pro vlastní zprostředkovatele Azure

První část tohoto kurzu popisuje, jak nastavit aplikaci funkce Azure pro práci s vlastními poskytovateli:

- [Nastavení funkcí Azure pro vlastní zprostředkovatele Azure](./tutorial-custom-providers-function-setup.md)

Vlastní poskytovatelé mohou pracovat s libovolnou veřejnou adresou URL.

## <a name="author-a-restful-endpoint-for-custom-providers"></a>Vytvoření koncového bodu RESTful pro vlastní zprostředkovatele

Druhá část tohoto kurzu popisuje, jak vytvořit koncový bod RESTful pro vlastní zprostředkovatele:

- [Vytváření koncového bodu RESTful pro vlastní zprostředkovatele](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>Vytvoření a použití vlastního zprostředkovatele

Třetí část tohoto kurzu popisuje, jak vytvořit vlastního zprostředkovatele a použít jeho vlastní akce a prostředky:

- [Vytvoření a použití vlastního zprostředkovatele](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o vlastní zprostředkovatelé a jak vytvořit jeden. Další kurz najdete v [tématu: Nastavení funkcí Azure pro vlastní zprostředkovatele Azure](./tutorial-custom-providers-function-setup.md).

Pokud hledáte reference nebo rychlý start, zde jsou některé užitečné odkazy:

- [Úvodní příručka: Vytvoření vlastního poskytovatele prostředků Azure a nasazení vlastních prostředků](./create-custom-provider.md)
- [Postup: Přidání vlastních akcí do rozhraní AZURE REST API](./custom-providers-action-endpoint-how-to.md)
- [Postup: Přidání vlastních prostředků do rozhraní AZURE REST API](./custom-providers-resources-endpoint-how-to.md)
