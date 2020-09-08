---
title: Kurz – nasazení clusteru vSphere v Azure
description: Naučte se nasadit cluster vSphere v Azure pomocí řešení Azure VMWare.
ms.topic: tutorial
ms.date: 09/07/2020
ms.openlocfilehash: 69a29a459ba283bb34169112ac2fa174ac6a14af
ms.sourcegitcommit: 8791f69d44150767807d215cafc4076f3ed43f9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2020
ms.locfileid: "89512355"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud-in-azure"></a>Kurz: nasazení privátního cloudu řešení Azure VMware v Azure

Řešení Azure VMware nabízí možnost nasadit cluster vSphere v Azure. Minimální počáteční nasazení je tři hostitele. Další hostitele je možné přidat najednou, maximálně 16 hostitelů na jeden cluster. 

Vzhledem k tomu, že řešení Azure VMware vám neumožňuje spravovat váš privátní cloud s vaším místním systémem vCenter při spuštění, je potřeba další konfigurace a připojení k místní instanci vCenter, virtuální síti a další. Tyto postupy a související požadavky jsou pokryté v tomto kurzu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření privátního cloudu řešení Azure VMware
> * Ověření nasazení privátního cloudu

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Odpovídající práva správce a oprávnění k vytvoření privátního cloudu.
- Ujistěte se, že máte nakonfigurované příslušné síťové služby, jak je popsáno v tématu [kurz: kontrolní seznam sítě](tutorial-network-checklist.md).

## <a name="register-the-resource-provider"></a>Registrace poskytovatele prostředků

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="create-a-private-cloud"></a>Vytvoření privátního cloudu

Privátní cloud řešení Azure VMware můžete vytvořit pomocí [Azure Portal](#azure-portal) nebo pomocí [Azure CLI](#azure-cli).

### <a name="azure-portal"></a>portál Azure

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-avs-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

Místo Azure Portal vytvoření privátního cloudu řešení Azure VMware můžete použít rozhraní příkazového řádku Azure pomocí Azure Cloud Shell. Je to bezplatné interaktivní prostředí s předinstalovanými a nakonfigurovanými běžnými nástroji Azure pro použití s vaším účtem. 

#### <a name="open-azure-cloud-shell"></a>Otevření služby Azure Cloud Shell

Chcete-li otevřít Cloud Shell, vyberte možnost **vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/bash](https://shell.azure.com/bash) . Vyberte **Kopírovat** pro zkopírování bloků kódu, vložení do Cloud Shell a stisknutím klávesy **ENTER** ji spusťte.

#### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

```
azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Vytvoření privátního cloudu

Zadejte název skupiny prostředků, název privátního cloudu, umístění a velikost clusteru.

| Vlastnost  | Popis  |
| --------- | ------------ |
| **-g** (název skupiny prostředků)     | Název skupiny prostředků pro prostředky privátního cloudu.        |
| **-n** (název privátního cloudu)     | Název vašeho privátního cloudu řešení Azure VMware.        |
| **--umístění**     | Umístění používané pro váš privátní cloud.         |
| **--cluster-Size**     | Velikost clusteru. Minimální hodnota je 3.         |
| **--Network-Block**     | Blok IP adres CIDR pro síť, který se má použít pro privátní cloud. Blok adres by neměl překrývat bloky adres používané v jiných virtuálních sítích, které jsou ve vašem předplatném a v místních sítích.        |
| **--SKU** | Hodnota SKU: AV36 |

```
azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-a-private-cloud-azure-portal"></a>Odstranění privátního cloudu (Azure Portal)

Pokud máte privátní cloud řešení Azure VMware, který už nepotřebujete, můžete ho odstranit. Při odstranění privátního cloudu se odstraní všechny clustery společně se všemi svými součástmi.

Provedete to tak, že v Azure Portal přejdete do svého privátního cloudu a vyberete **Odstranit**. Na stránce potvrzení potvrďte název privátního cloudu a vyberte **Ano**.

> [!CAUTION]
> Odstranění privátního cloudu je nevratná operace. Po odstranění privátního cloudu se data nedají obnovit, protože ukončí všechny spuštěné úlohy, komponenty a zničí všechna data privátního cloudu a nastavení konfigurace, včetně veřejných IP adres. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření privátního cloudu řešení Azure VMware
> * Ověření nasazení privátního cloudu

Přejděte k dalšímu kurzu, kde se dozvíte, jak vytvořit virtuální síť pro použití s privátním cloudem v rámci nastavení místní správy pro clustery privátních cloudů.

> [!div class="nextstepaction"]
> [Vytvoření Virtual Network](tutorial-configure-networking.md)
