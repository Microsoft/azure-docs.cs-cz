---
title: Registrace poskytovatele prostředků řešení Azure VMware
description: Postup registrace poskytovatele prostředků řešení Azure VMware
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: 80010a232f80865b20c2e3d953dc1d9d22ece1c6
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653159"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Pokud chcete používat řešení Azure VMware, musíte nejdřív zaregistrovat poskytovatele prostředků s vaším předplatným. Další informace o poskytovatelích prostředků najdete v tématu [poskytovatelé a typy prostředků Azure](/azure/azure-resource-manager/management/resource-providers-and-types).

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
