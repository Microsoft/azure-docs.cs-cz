---
title: Vytvoření vlastní akce a prostředků v Azure
description: V tomto kurzu se přenášejí prostřednictvím vytvoření vlastní akce a prostředků v Azure Resource Manageru a integrovat je do vlastních pracovních postupů pro šablon Azure Resource Manageru, rozhraní příkazového řádku Azure, Azure Policy a protokolu aktivit.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 4bbfcf070611e3df5c0fe47070f2ab6961111e07
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800040"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Vytvoření vlastní akce a prostředků v Azure

Vlastní zprostředkovatelé umožňují přizpůsobit pracovní postupy v Azure. Vlastní zprostředkovatel je kontrakt mezi Azure a `endpoint`. Umožňuje přidání nové vlastní rozhraní API do Azure Resource Manageru k povolení nové nasazení a možnosti správy. V tomto kurzu budou procházet jednoduchý příklad, jak přidat nové akce a prostředky do Azure a tom, jak integrovat se.

V tomto kurzu je rozdělen do následujících kroků:

- Nastavení funkce Azure pro Azure Vlastní zprostředkovatelé
- Vytváření koncový bod RESTful pro vlastní zprostředkovatelé
- Vytvoření a použití vlastního zprostředkovatele

## <a name="setup-azure-functions-for-azure-custom-providers"></a>Nastavení funkce Azure pro Azure Vlastní zprostředkovatelé

Tato část kurzu přejde do podrobností o tom, jak nastavit funkce Azure pro práci s vlastní poskytovatele. Vlastní zprostředkovatelé můžete pracovat se žádné veřejné adresy URL.

- [Nastavení funkce Azure pro Azure Vlastní zprostředkovatelé](./tutorial-custom-providers-function-setup.md)

## <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Vytváření koncový bod RESTful pro vlastní zprostředkovatelé

Tato část kurzu přejde do podrobností o vytváření koncový bod RESTful pro vlastní zprostředkovatele.

- [Vytváření koncový bod RESTful pro vlastní zprostředkovatelé](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-and-utilizing-the-custom-provider"></a>Vytvoření a použití vlastního zprostředkovatele

Tato část kurzu přejde do podrobností o tom, jak vytvořit vlastního zprostředkovatele a používání vlastních akcí a prostředky.

- [Vytvářet a využívat Azure vlastního zprostředkovatele](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Další kroky

V tomto článku jsme se dozvěděli o vlastních poskytovatelů a jak takovou sestavit. Chcete pokračovat v dalším kroku kurzu:

- [Kurz: Nastavení funkce Azure pro Azure Vlastní zprostředkovatelé](./tutorial-custom-providers-function-setup.md)

Pokud hledáte rychlý start nebo odkazů, tady je pár užitečných odkazů:

- [Rychlé zprovoznění: Vytvoření vlastního poskytovatele prostředků Azure a nasazení vlastních prostředků](./create-custom-provider.md)
- [Postup: Přidání vlastní akce k rozhraní Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Postup: Přidání vlastních prostředků do Azure REST API](./custom-providers-resources-endpoint-how-to.md)
