---
title: Kurz – nasazení clusteru vSphere v Azure
description: Naučte se nasadit cluster vSphere v Azure pomocí řešení Azure VMWare (AVS).
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 6a192454367f2e6ca071e9cfe0a9f1a94868cbdb
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82977691"
---
# <a name="tutorial-deploy-an-avs-private-cloud-in-azure"></a>Kurz: nasazení privátního cloudu služby AVS v Azure

Řešení Azure VMware (AVS) poskytuje možnost nasadit cluster vSphere v Azure. Minimální počáteční nasazení je tři hostitele. Další hostitele je možné přidat najednou, maximálně 16 hostitelů na jeden cluster. 

Protože možnost AVS vám neumožní spravovat privátní cloud s vaším místním systémem vCenter při spuštění, budete muset provést další konfiguraci a připojení k místní instanci vCenter, virtuální síti a dalším. V této sérii kurzů budou zahrnuty tyto postupy a související požadavky.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření privátního cloudu AVS
> * Ověření nasazení privátního cloudu

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Odpovídající práva správce a oprávnění k vytvoření privátního cloudu.
- Ujistěte se, že máte nakonfigurované příslušné síťové služby, jak je popsáno v tématu [kurz: kontrolní seznam sítě](tutorial-network-checklist.md).

## <a name="register-the-resource-provider"></a>Registrace poskytovatele prostředků

Aby bylo možné používat řešení Azure VMWare, musíte nejdřív zaregistrovat poskytovatele prostředků. Následující příklad registruje poskytovatele prostředků v rámci vašeho předplatného.

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Další způsoby registrace poskytovatele prostředků najdete v tématu [poskytovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Vytvoření privátního cloudu

Privátní cloud služby AVS můžete vytvořit pomocí [Azure Portal](#azure-portal) nebo pomocí [Azure CLI](#azure-cli).

### <a name="azure-portal"></a>portál Azure

V Azure Portal vyberte **+ vytvořit nový prostředek**. Do textového pole **Hledat na Marketplace** zadejte `Azure VMware Solution`a ze seznamu vyberte **Řešení Azure VMware** . V okně **Řešení Azure VMware** vyberte **vytvořit** .

Na kartě **základy** zadejte hodnoty polí. V následující tabulce je uveden podrobný seznam vlastností.

| Pole   | Hodnota  |
| ---| --- |
| **Předplatné** | Předplatné, které plánujete použít pro nasazení.|
| **Skupina prostředků** | Skupina prostředků pro prostředky privátního cloudu. |
| **Umístění** | Vyberte umístění, jako je například **východní USA**.|
| **Název prostředku** | Název vašeho privátního cloudu služby AVS |
| **SKLADOVÉ** | Vyberte hodnotu následující SKU: AV36 |
| **Dvou** | Toto je počet hostitelů, které se mají přidat do clusteru privátního cloudu. Výchozí hodnota je 3. Tuto hodnotu je možné vyhodnotit nebo snížit po nasazení.  |
| **heslo správce vCenter** | Zadejte heslo správce cloudu. |
| **Heslo správce NSX-T** | Zadejte heslo správce NSX-T. |
| **Blok adresy** | Zadejte blok IP adres pro síť CIDR privátního cloudu. Příklad: 10.175.0.0/22. |

:::image type="content" source="./media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Vytvoření privátního cloudu" border="true":::

Po dokončení vyberte **zkontrolovat + vytvořit**. Na další obrazovce ověřte zadané informace. Pokud jsou informace správné, vyberte **vytvořit**.

> [!NOTE]
> Tento krok trvá zhruba dvě hodiny. 

### <a name="azure-cli"></a>Azure CLI

Alternativně můžete pomocí rozhraní příkazového řádku Azure vytvořit privátní cloud služby AVS v Azure. Pokud to chcete provést, můžete použít Azure Cloud Shell následující kroky ukazují, jak to provést.

#### <a name="open-azure-cloud-shell"></a>Otevření služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem.

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče na adrese https://shell.azure.com/bash. Vyberte **Kopírovat** pro zkopírování bloků kódu, vložení do Cloud Shell a stisknutím klávesy **ENTER** ji spusťte.

#### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Vytvoření privátního cloudu

Pokud chcete vytvořit privátní cloud služby AVS, musíte zadat název skupiny prostředků, název privátního cloudu, umístění, velikost clusteru.


|Vlastnost  |Popis  |
|---------|---------|
|Název skupiny prostředků     | Název skupiny prostředků, do které nasazujete privátní cloud.        |
|Název privátního cloudu     | Název privátního cloudu.        |
|Umístění     | Umístění, které se používá pro privátní cloud         |
|Velikost clusteru     | Velikost clusteru. Minimální hodnota je 3.         |
|Síťový blok     | Rozsah CIDR, který se má použít pro privátní cloud. Doporučuje se, aby bylo jedinečné v místním prostředí i v prostředí Azure.        |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22
```

## <a name="verify-deployment-was-successful"></a>Ověření nasazení bylo úspěšné.

Přejděte do skupiny prostředků, kterou jste vytvořili, a vyberte svůj privátní cloud. po dokončení nasazení se zobrazí následující obrazovka se stavem **úspěch**.

:::image type="content" source="./media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Ověření nasazení privátního cloudu" border="true":::

## <a name="delete-a-private-cloud"></a>Odstranění privátního cloudu

Pokud máte privátní cloud služby AVS, který jste ověřili, že už nepotřebujete, můžete ho odstranit. Při odstranění privátního cloudu se odstraní všechny clustery společně se všemi svými součástmi.

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
