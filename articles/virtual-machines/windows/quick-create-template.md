---
title: 'Rychlý Start: použití šablony Správce prostředků k vytvoření virtuálního počítače s Windows'
description: V tomto rychlém startu se dozvíte, jak pomocí šablony Správce prostředků vytvořit virtuální počítač s Windows.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/04/2020
ms.author: cynthn
ms.custom: subject-armqs
ms.openlocfilehash: ded59b2f517c5b109dfd00bde2fb73f8351bf821
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88649665"
---
# <a name="quickstart-create-a-windows-virtual-machine-using-an-arm-template"></a>Rychlý Start: Vytvoření virtuálního počítače s Windows pomocí šablony ARM

V tomto rychlém startu se dozvíte, jak použít šablonu Azure Resource Manager (šablonu ARM) k nasazení virtuálního počítače s Windows (VM) v Azure.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-windows%2fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

:::code language="json" source="~/quickstart-templates/101-vm-simple-windows/azuredeploy.json":::


V šabloně je definováno několik prostředků:

- [**Microsoft. Network/virtualNetworks/subnets**](/azure/templates/Microsoft.Network/virtualNetworks/subnets): vytvořit podsíť.
- [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts): vytvořte účet úložiště.
- [**Microsoft. Network/publicIPAddresses**](/azure/templates/Microsoft.Network/publicIPAddresses): Vytvoří veřejnou IP adresu.
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/Microsoft.Network/networkSecurityGroups): Vytvořte skupinu zabezpečení sítě.
- [**Microsoft. Network/virtualNetworks**](/azure/templates/Microsoft.Network/virtualNetworks): vytvořte virtuální síť.
- [**Microsoft. Network/networkInterfaces**](/azure/templates/Microsoft.Network/networkInterfaces): vytvořte síťovou kartu.
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/Microsoft.Compute/virtualMachines): vytvořte virtuální počítač.



## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří Trezor klíčů a tajný klíč.

    [![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-windows%2fazuredeploy.json)

1. Vyberte nebo zadejte následující hodnoty. Použijte výchozí hodnoty, pokud je k dispozici.

    - **Předplatné:** Vyberte předplatné Azure.
    - **Skupina prostředků**: z rozevíracího seznamu vyberte existující skupinu prostředků nebo vyberte **vytvořit novou**, pro skupinu prostředků zadejte jedinečný název a pak klikněte na **OK**.
    - **Umístění:** Vyberte prosím umístění.  Například **USA – střed**.
    - **Uživatelské jméno správce**: zadejte uživatelské jméno, například *azureuser*.
    - **Heslo správce**: zadejte heslo, které se má použít pro účet správce. Heslo musí mít délku aspoň 12 znaků a musí splňovat [definované požadavky na složitost](faq.md#what-are-the-password-requirements-when-creating-a-vm).
    - **Předpona popisku DNS**: Zadejte jedinečný identifikátor, který se použije jako součást popisku DNS.
    - **Verze operačního systému Windows**: vyberte verzi Windows, kterou chcete na virtuálním počítači spustit.
    - **Velikost virtuálního počítače**: vyberte [Velikost](../sizes.md) , která se má použít pro virtuální počítač.
    - **Umístění**: výchozí hodnota je stejné umístění jako skupina prostředků, pokud již existuje.
1. Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověření vyberte **vytvořit** a vytvořte a NASAĎTE virtuální počítač.


K nasazení šablony se použije Azure Portal. Kromě Azure Portal můžete použít také Azure PowerShell, Azure CLI a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Pomocí Azure Portal můžete ověřit virtuální počítač a další prostředky, které byly vytvořeny. Po dokončení nasazení vyberte **Přejít do skupiny prostředků** , abyste viděli virtuální počítač a další prostředky.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, která odstraní virtuální počítač a všechny prostředky ve skupině prostředků. 

1. Vyberte **skupinu prostředků**.
1. Na stránce skupiny prostředků vyberte **Odstranit**.
1. Po zobrazení výzvy zadejte název skupiny prostředků a pak vyberte **Odstranit**.


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili jednoduchý virtuální počítač pomocí šablony ARM. Další informace o virtuálních počítačích Azure najdete v kurzu pro virtuální počítače s Linuxem.


> [!div class="nextstepaction"]
> [Kurzy pro virtuální počítače Azure s Windows](./tutorial-manage-vm.md)