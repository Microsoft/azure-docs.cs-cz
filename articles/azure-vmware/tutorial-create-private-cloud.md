---
title: Kurz – nasazení clusteru vSphere v Azure
description: Naučte se nasadit cluster vSphere v Azure pomocí řešení Azure VMWare (AVS).
ms.topic: tutorial
ms.date: 07/15/2020
ms.openlocfilehash: 4f3b33ea401c62124ae5f8a4c881d86d2f19b40c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079413"
---
# <a name="tutorial-deploy-an-avs-private-cloud-in-azure"></a>Kurz: nasazení privátního cloudu služby AVS v Azure

Řešení Azure VMware (AVS) poskytuje možnost nasadit cluster vSphere v Azure. Minimální počáteční nasazení je tři hostitele. Další hostitele je možné přidat najednou, maximálně 16 hostitelů na jeden cluster. 

Protože aplikace AVS neumožňuje spravovat váš privátní cloud s vaším místním systémem vCenter při spuštění, je potřeba další konfigurace a připojení k místní instanci vCenter, virtuální síti a další. Tyto postupy a související požadavky jsou pokryté v tomto kurzu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření privátního cloudu AVS
> * Ověření nasazení privátního cloudu

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Odpovídající práva správce a oprávnění k vytvoření privátního cloudu.
- Ujistěte se, že máte nakonfigurované příslušné síťové služby, jak je popsáno v tématu [kurz: kontrolní seznam sítě](tutorial-network-checklist.md).

## <a name="register-the-resource-provider"></a>Registrace poskytovatele prostředků

Pokud chcete používat funkci AVS, musíte nejdřív zaregistrovat poskytovatele prostředků s vaším předplatným.

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Další způsoby registrace poskytovatele prostředků najdete v tématu [poskytovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md).


## <a name="create-a-private-cloud"></a>Vytvoření privátního cloudu

Privátní cloud služby AVS můžete vytvořit pomocí [Azure Portal](#azure-portal) nebo pomocí [Azure CLI](#azure-cli).

### <a name="azure-portal"></a>Portál Azure Portal

1. Přihlaste se na portál [Azure Portal](https://portal.azure.com).

1. Vyberte **vytvořit nový prostředek**. Do textového pole **Hledat na Marketplace** zadejte `Azure VMware Solution` a ze seznamu vyberte **Řešení Azure VMware** . V okně **Řešení Azure VMware** vyberte **vytvořit** .

1. Na kartě **základy** zadejte hodnoty polí. V následující tabulce jsou uvedeny vlastnosti polí.

   | Pole   | Hodnota  |
   | ---| --- |
   | **Předplatné** | Předplatné, které plánujete použít pro nasazení.|
   | **Skupina prostředků** | Skupina prostředků pro prostředky privátního cloudu. |
   | **Umístění** | Vyberte umístění, jako je například **východní USA**.|
   | **Název prostředku** | Název vašeho privátního cloudu služby AVS |
   | **Skladová jednotka (SKU)** | Vyberte následující hodnotu SKU: AV36 |
   | **Dvou** | Počet hostitelů, kteří mají být přidáni do clusteru privátního cloudu. Výchozí hodnota je 3, což může být vyvoláno nebo sníženo po nasazení.  |
   | **heslo správce vCenter** | Zadejte heslo správce cloudu. |
   | **Heslo správce NSX-T** | Zadejte heslo správce NSX-T. |
   | **Blok adresy** | Zadejte blok IP adres pro síť CIDR privátního cloudu, například 10.175.0.0/22. |

   :::image type="content" source="./media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Vytvoření privátního cloudu" border="true":::

1. Po dokončení vyberte **zkontrolovat + vytvořit**. Na další obrazovce ověřte zadané informace. Pokud jsou informace správné, vyberte **vytvořit**.

   > [!NOTE]
   > Tento krok trvá zhruba dvě hodiny. 

1. Ověřte, že nasazení proběhlo úspěšně. Přejděte do skupiny prostředků, kterou jste vytvořili, a vyberte svůj privátní cloud.  Po dokončení nasazení se zobrazí stav **dokončeno** . 

   :::image type="content" source="./media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Ověření nasazení privátního cloudu" border="true":::

### <a name="azure-cli"></a>Azure CLI

Místo Azure Portal vytvoření privátního cloudu služby AVS můžete použít rozhraní příkazového řádku Azure pomocí Azure Cloud Shell. Je to bezplatné interaktivní prostředí s předinstalovanými a nakonfigurovanými běžnými nástroji Azure pro použití s vaším účtem. 

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
| **-n** (název privátního cloudu)     | Název vašeho privátního cloudu služby AVS        |
| **--umístění**     | Umístění používané pro váš privátní cloud.         |
| **--cluster-Size**     | Velikost clusteru. Minimální hodnota je 3.         |
| **--Network-Block**     | Blok IP adres CIDR pro síť, který se má použít pro privátní cloud. Blok adres by neměl překrývat bloky adres používané v jiných virtuálních sítích, které jsou ve vašem předplatném a v místních sítích.        |
| **--SKU** | Hodnota SKU: AV36 |

```
azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-a-private-cloud-azure-portal"></a>Odstranění privátního cloudu (Azure Portal)

Pokud máte privátní cloud služby AVS, který už nepotřebujete, můžete ho odstranit. Při odstranění privátního cloudu se odstraní všechny clustery společně se všemi svými součástmi.

Provedete to tak, že v Azure Portal přejdete do svého privátního cloudu a vyberete **Odstranit**. Na stránce potvrzení potvrďte název privátního cloudu a vyberte **Ano**.

> [!CAUTION]
> Odstranění privátního cloudu je nevratná operace. Po odstranění privátního cloudu se data nedají obnovit, protože ukončí všechny spuštěné úlohy, komponenty a zničí všechna data privátního cloudu a nastavení konfigurace, včetně veřejných IP adres. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření privátního cloudu AVS
> * Ověření nasazení privátního cloudu

Přejděte k dalšímu kurzu, kde se dozvíte, jak vytvořit virtuální síť pro použití s privátním cloudem v rámci nastavení místní správy pro clustery privátních cloudů.

> [!div class="nextstepaction"]
> [Vytvoření Virtual Network](tutorial-configure-networking.md)
