---
title: 'Rychlý Start: použití šablony Správce prostředků k vytvoření virtuálního počítače s Ubuntu Linux'
description: V tomto rychlém startu se dozvíte, jak pomocí šablony Správce prostředků vytvořit virtuální počítač se systémem Linux.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/04/2020
ms.author: cynthn
ms.custom: subject-armqs
ms.openlocfilehash: 7fc7e4d5d402bfb21a30798f64e31cbbef8ccdd0
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84677946"
---
# <a name="quickstart-create-an-ubuntu-linux-virtual-machine-using-a-resource-manager-template"></a>Rychlý Start: Vytvoření virtuálního počítače s Ubuntu Linux pomocí šablony Správce prostředků

V tomto rychlém startu se dozvíte, jak pomocí šablony Správce prostředků nasadit virtuální počítač s Ubuntu Linux (VM) v Azure. 

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="prerequisites"></a>Požadavky

Žádné

## <a name="create-an-ubuntu-linux-virtual-machine"></a>Vytvoření virtuálního počítače s Ubuntu Linux

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/).

:::code language="json" source="~/quickstart-templates/101-vm-simple-linux/azuredeploy.json" range="1-261" highlight="110-260":::


V šabloně je definováno několik prostředků:

- [**Microsoft. Network/virtualNetworks/subnets**](/azure/templates/Microsoft.Network/virtualNetworks/subnets): vytvořit podsíť.
- [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts): vytvořte účet úložiště.
- [**Microsoft. Network/networkInterfaces**](/azure/templates/Microsoft.Network/networkInterfaces): vytvořte síťovou kartu.
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/Microsoft.Network/networkSecurityGroups): Vytvořte skupinu zabezpečení sítě.
- [**Microsoft. Network/virtualNetworks**](/azure/templates/Microsoft.Network/virtualNetworks): vytvořte virtuální síť.
- [**Microsoft. Network/publicIPAddresses**](/azure/templates/Microsoft.Network/publicIPAddresses): Vytvoří veřejnou IP adresu.
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/Microsoft.Compute/virtualMachines): vytvořte virtuální počítač.

### <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří Trezor klíčů a tajný klíč.

    [![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-linux%2fazuredeploy.json)

1. Vyberte nebo zadejte následující hodnoty. Použijte výchozí hodnoty, pokud je k dispozici.

    - **Předplatné:** Vyberte předplatné Azure.
    - **Skupina prostředků**: z rozevíracího seznamu vyberte existující skupinu prostředků nebo vyberte **vytvořit novou**, pro skupinu prostředků zadejte jedinečný název a pak klikněte na **OK**.
    - **Umístění:** Vyberte prosím umístění.  Například **USA – střed**.
    - **Uživatelské jméno správce**: zadejte uživatelské jméno, například *azureuser*.
    - **Typ ověřování**: můžete si vybrat mezi použitím klíče SSH nebo hesla.
    - **Heslo nebo klíč správce** v závislosti na tom, co zvolíte pro typ ověřování:
        - Pokud zvolíte možnost **heslo**, musí mít heslo délku alespoň 12 znaků a musí splňovat [definované požadavky na složitost](faq.md#what-are-the-password-requirements-when-creating-a-vm).
        - Pokud zvolíte **sshPublicKey**, vložte obsah svého veřejného klíče.
    - **Předpona popisku DNS**: Zadejte jedinečný identifikátor, který se použije jako součást popisku DNS.
    - **Verze operačního systému Ubuntu**: vyberte, kterou verzi Ubuntu chcete na virtuálním počítači spustit.
    - **Umístění**: výchozí hodnota je stejné umístění jako skupina prostředků, pokud již existuje.
    - **Velikost virtuálního počítače**: vyberte [Velikost](sizes.md) , která se má použít pro virtuální počítač.
    - **Virtual Network název**: název, který se má použít pro virtuální síť.
    - **Název podsítě**: název podsítě, kterou by měl virtuální počítač používat.
    - **Název skupiny zabezpečení sítě**: název pro NSG.
1. Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověření vyberte **vytvořit** a vytvořte a NASAĎTE virtuální počítač.


K nasazení šablony se použije Azure Portal. Kromě Azure Portal můžete použít také rozhraní příkazového řádku Azure CLI, Azure PowerShell a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Pomocí Azure Portal můžete ověřit virtuální počítač a další prostředky, které byly vytvořeny. Po dokončení nasazení vyberte **Přejít do skupiny prostředků** , abyste viděli virtuální počítač a další prostředky.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, která odstraní virtuální počítač a všechny prostředky ve skupině prostředků. 

1. Vyberte **skupinu prostředků**.
1. Na stránce skupiny prostředků vyberte **Odstranit**.
1. Po zobrazení výzvy zadejte název skupiny prostředků a pak vyberte **Odstranit**.


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili jednoduchý virtuální počítač pomocí šablony Správce prostředků. Další informace o virtuálních počítačích Azure najdete v kurzu pro virtuální počítače s Linuxem.


> [!div class="nextstepaction"]
> [Kurzy pro virtuální počítače Azure s Linuxem](./tutorial-manage-vm.md)