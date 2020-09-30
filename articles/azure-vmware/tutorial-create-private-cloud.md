---
title: Kurz – nasazení clusteru vSphere v Azure
description: Naučte se nasadit cluster vSphere v Azure pomocí řešení Azure VMWare.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 3fab49640364ef1b2e68953d366b20f77556b486
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578308"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud-in-azure"></a>Kurz: nasazení privátního cloudu řešení Azure VMware v Azure

Řešení Azure VMware nabízí možnost nasadit cluster vSphere v Azure. Minimální počáteční nasazení je tři hostitele. Další hostitele je možné přidat najednou, maximálně 16 hostitelů na jeden cluster. 

Vzhledem k tomu, že řešení Azure VMware neumožňuje spravovat váš privátní cloud s vaším místním systémem vCenter při spuštění, je potřeba provést další konfiguraci. Tyto postupy a související požadavky jsou pokryté v tomto kurzu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření privátního cloudu řešení Azure VMware
> * Ověření nasazení privátního cloudu

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Odpovídající práva správce a oprávnění k vytvoření privátního cloudu.
- Ujistěte se, že máte nakonfigurované příslušné síťové služby, jak je popsáno v tématu [kurz: kontrolní seznam sítě](tutorial-network-checklist.md).

## <a name="register-the-resource-provider"></a>Registrace poskytovatele prostředků

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="create-a-private-cloud"></a>Vytvoření privátního cloudu

Privátní cloud řešení Azure VMware můžete vytvořit pomocí [Azure Portal](#azure-portal) nebo pomocí [Azure CLI](#azure-cli).

### <a name="azure-portal"></a>portál Azure

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

Místo Azure Portal vytvoření privátního cloudu řešení Azure VMware můžete použít rozhraní příkazového řádku Azure pomocí Azure Cloud Shell.  Seznam příkazů, které můžete použít s řešením Azure VMware, najdete v tématu [příkazy Azure VMware](https://docs.microsoft.com/cli/azure/ext/vmware/vmware).

#### <a name="open-azure-cloud-shell"></a>Otevření služby Azure Cloud Shell

Vyberte **vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/bash](https://shell.azure.com/bash) . Vyberte **Kopírovat** pro zkopírování bloků kódu, vložení do Cloud Shell a stisknutím klávesy **ENTER** ji spusťte.

#### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

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

## <a name="delete-an-azure-vmware-solution-private-cloud"></a>Odstranění privátního cloudu řešení Azure VMware

Pokud máte privátní cloud řešení Azure VMware, který už nepotřebujete, můžete ho odstranit. Privátní cloud řešení Azure VMware zahrnuje izolovanou síťovou doménu, jeden nebo více clusterů vSphere zřízených na vyhrazených uzlech serveru a obvykle mnoho virtuálních počítačů. Při odstranění privátního cloudu se odstraní všechny virtuální počítače, jejich data a clustery. Vyhrazené holé uzly se bezpečně vymažou a vrátí do bezplatného fondu. Síťová doména zřízená pro zákazníka se odstraní.  

> [!CAUTION]
> Odstranění privátního cloudu je nevratná operace. Po odstranění privátního cloudu se data nedají obnovit, protože ukončí všechny spuštěné úlohy a komponenty a zničí všechna data privátního cloudu a nastavení konfigurace, včetně veřejných IP adres.

### <a name="prerequisites"></a>Požadavky

Po odstranění privátního cloudu neexistuje žádný způsob, jak obnovit virtuální počítače a jejich data. Pokud budete později potřebovat data virtuálního počítače, správce musí před odstraněním privátního cloudu nejprve zálohovat všechna data.

### <a name="steps-to-delete-an-azure-vmware-solution-private-cloud"></a>Postup odstranění privátního cloudu řešení Azure VMware

1. Přejděte na stránku řešení Azure VMware v Azure Portal.

2. Vyberte privátní cloud, který chcete odstranit.
 
3. Zadejte název privátního cloudu a vyberte **Ano**. Během několika hodin se proces odstranění dokončí.  

## <a name="azure-vmware-commands"></a>Příkazy Azure VMware

Seznam příkazů, které můžete použít s řešením Azure VMware, najdete v tématu [příkazy Azure VMware](https://docs.microsoft.com/cli/azure/ext/vmware/vmware).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření privátního cloudu řešení Azure VMware
> * Ověření nasazení privátního cloudu
> * Odstranění privátního cloudu řešení Azure VMware

Přejděte k dalšímu kurzu, kde se dozvíte, jak vytvořit pole s odkazem. Pomocí tohoto pole se můžete připojit k vašemu prostředí, abyste mohli místně spravovat svůj privátní cloud.


> [!div class="nextstepaction"]
> [Přístup k privátnímu cloudu řešení Azure VMware](tutorial-access-private-cloud.md)
