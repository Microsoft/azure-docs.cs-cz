---
title: Kurz – nasazení privátního cloudu řešení Azure VMware
description: Naučte se vytvářet a nasazovat privátní cloud řešení Azure VMware.
ms.topic: tutorial
ms.date: 02/22/2021
ms.openlocfilehash: ed916305cd1a67162f07c24e3bf97766e5389b74
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462163"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud"></a>Kurz: nasazení privátního cloudu řešení Azure VMware

Řešení Azure VMware nabízí možnost nasadit cluster vSphere v Azure. Minimální počáteční nasazení je tři hostitele. Další hostitele je možné přidat najednou, maximálně 16 hostitelů na jeden cluster.

Vzhledem k tomu, že řešení Azure VMware neumožňuje spravovat váš privátní cloud s vaším místním systémem vCenter při spuštění, je potřeba provést další konfiguraci. Tyto postupy a související požadavky jsou pokryté v tomto kurzu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření privátního cloudu řešení Azure VMware
> * Ověření nasazení privátního cloudu

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Odpovídající práva správce a oprávnění k vytvoření privátního cloudu. V předplatném musíte mít minimální úroveň přispěvatele.
- Podle informací, které jste shromáždili v článku [plánování](production-ready-deployment-steps.md) , nasaďte řešení Azure VMware.
- Ujistěte se, že máte nakonfigurované příslušné síťové služby, jak je popsáno v tématu [kurz: kontrolní seznam sítě](tutorial-network-checklist.md).
- Byli zřízeni hostitelé a poskytovatele prostředků Microsoft. AVS, jak je popsáno v [části hostitelé požadavku a povolení poskytovatele prostředků Microsoft. AVS](enable-azure-vmware-solution.md).

## <a name="create-a-private-cloud"></a>Vytvoření privátního cloudu

Privátní cloud řešení Azure VMware můžete vytvořit pomocí [Azure Portal](#azure-portal) nebo pomocí [Azure CLI](#azure-cli).

### <a name="azure-portal"></a>portál Azure

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

Místo Azure Portal vytvoření privátního cloudu řešení Azure VMware můžete použít rozhraní příkazového řádku Azure pomocí Azure Cloud Shell.  Seznam příkazů, které můžete použít s řešením Azure VMware, najdete v tématu [příkazy Azure VMware](/cli/azure/ext/vmware/vmware).

#### <a name="open-azure-cloud-shell"></a>Otevření služby Azure Cloud Shell

Vyberte **vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/bash](https://shell.azure.com/bash) . Vyberte **Kopírovat** pro zkopírování bloků kódu, vložení do Cloud Shell a stisknutím klávesy **ENTER** ji spusťte.

#### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [AZ Group Create](/cli/azure/group) . Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

```azurecli-interactive

az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Vytvoření privátního cloudu

Zadejte název skupiny prostředků a privátního cloudu, umístění a velikost clusteru.

| Vlastnost  | Popis  |
| --------- | ------------ |
| **-g** (název skupiny prostředků)     | Název skupiny prostředků pro prostředky privátního cloudu.        |
| **-n** (název privátního cloudu)     | Název vašeho privátního cloudu řešení Azure VMware.        |
| **--umístění**     | Umístění používané pro váš privátní cloud.         |
| **--cluster-Size**     | Velikost clusteru. Minimální hodnota je 3.         |
| **--Network-Block**     | Blok IP adres CIDR pro síť, který se má použít pro privátní cloud. Blok adres by neměl překrývat bloky adres používané v jiných virtuálních sítích, které jsou ve vašem předplatném a v místních sítích.        |
| **--SKU** | Hodnota SKU: AV36 |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="azure-vmware-commands"></a>Příkazy Azure VMware

Seznam příkazů, které můžete použít s řešením Azure VMware, najdete v tématu [příkazy Azure VMware](/cli/azure/ext/vmware/vmware).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření privátního cloudu řešení Azure VMware
> * Ověření nasazení privátního cloudu
> * Odstranění privátního cloudu řešení Azure VMware

Přejděte k dalšímu kurzu, kde se dozvíte, jak vytvořit pole s odkazem. Pomocí tohoto pole se můžete připojit k vašemu prostředí, abyste mohli místně spravovat svůj privátní cloud.


> [!div class="nextstepaction"]
> [Přístup k privátnímu cloudu řešení Azure VMware](tutorial-access-private-cloud.md)