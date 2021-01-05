---
title: Registrace poskytovatele prostředků řešení Azure VMware
description: Postup registrace poskytovatele prostředků řešení Azure VMware
ms.topic: include
ms.date: 12/24/2020
ms.openlocfilehash: 7d24ce86f24c941c7d48d3b73576dcdfda120f51
ms.sourcegitcommit: 489ce69c0ff3f5188889ecfef5ffa76f7121e0d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/24/2020
ms.locfileid: "97770811"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Pokud chcete používat řešení Azure VMware, musíte nejdřív zaregistrovat poskytovatele prostředků s vaším předplatným.  

### <a name="azure-cli"></a>Azure CLI 

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```


### <a name="azure-portal"></a>portál Azure
 
1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

1. V nabídce webu Azure Portal vyberte **Všechny služby**.

1. V poli **všechny služby** zadejte **předplatné** a pak vyberte **předplatná**.

1. Vyberte předplatné ze seznamu odběrů, které chcete zobrazit.

1. Vyberte **poskytovatelé prostředků** a do vyhledávacího pole zadejte **Microsoft. AVS** . 
 
1. Pokud poskytovatel prostředků není zaregistrovaný, vyberte **zaregistrovat**.