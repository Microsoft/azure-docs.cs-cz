---
title: Registrace poskytovatele prostředků řešení Azure VMware
description: Postup registrace poskytovatele prostředků řešení Azure VMware
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: d2363ca2672f7f668d8f9b3816447f316d8b7347
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555874"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Pokud chcete používat řešení Azure VMware, musíte nejdřív zaregistrovat poskytovatele prostředků s vaším předplatným. Další informace o poskytovatelích prostředků najdete v tématu [poskytovatelé a typy prostředků Azure](../../azure-resource-manager/management/resource-providers-and-types.md).


### <a name="portal"></a>[Azure Portal](#tab/azure-portal)
 
1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

1. V nabídce webu Azure Portal vyberte **Všechny služby**.

1. V poli **všechny služby** zadejte **předplatné** a pak vyberte **předplatná**.

1. Vyberte předplatné ze seznamu odběrů, které chcete zobrazit.

1. Vyberte **poskytovatelé prostředků** a do vyhledávacího pole zadejte **Microsoft. AVS** . 
 
1. Pokud poskytovatel prostředků není zaregistrovaný, vyberte **zaregistrovat**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li začít používat rozhraní příkazového řádku Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Přihlaste se k předplatnému Azure, které používáte pro nasazení řešení Azure VMware prostřednictvím rozhraní příkazového řádku Azure CLI. Zaregistrujte `Microsoft.AVS` poskytovatele prostředků pomocí příkazu [AZ Provider Register](/cli/azure/provider#az_provider_register) :

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

K zobrazení všech dostupných zprostředkovatelů můžete použít příkaz [AZ Provider list](/cli/azure/provider#az_provider_list) .

---


 
