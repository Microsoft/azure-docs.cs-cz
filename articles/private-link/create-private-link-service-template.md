---
title: 'Rychlý Start: vytvoření služby privátního propojení ve službě Azure Private Link'
description: V tomto rychlém startu použijete šablonu Azure Resource Manager (šablona ARM) k vytvoření služby privátního propojení.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: 34993ad3d3d0494f89bd264a8b7194f52129ad7c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555053"
---
# <a name="quickstart-create-a-private-link-service-by-using-an-arm-template"></a>Rychlý Start: vytvoření služby privátního propojení pomocí šablony ARM

V tomto rychlém startu použijete šablonu Azure Resource Manager (šablona ARM) k vytvoření služby privátního propojení.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Tento rychlý Start můžete také dokončit pomocí [Azure Portal](create-private-link-service-portal.md), [Azure PowerShell](create-private-link-service-powershell.md)nebo rozhraní příkazového [řádku Azure](create-private-link-service-cli.md).

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Potřebujete účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Kontrola šablony

Tato šablona vytvoří službu privátního propojení.

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-privatelink-service/).

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json":::

V šabloně je definováno víc prostředků Azure:

- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): pro každý virtuální počítač existuje jedna virtuální síť.
- [**Microsoft. Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers): Nástroj pro vyrovnávání zatížení, který zveřejňuje virtuální počítače, které jsou hostiteli služby.
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): Existují dvě síťová rozhraní, jedna pro každý virtuální počítač.
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): Existují dva virtuální počítače, jeden, který je hostitelem služby, a jeden, který testuje připojení k privátnímu koncovému bodu.
- [**Microsoft. COMPUTE/virtualMachines/Extensions**](/azure/templates/Microsoft.Compute/virtualMachines/extensions): rozšíření, které instaluje webový server.
- [**Microsoft. Network/privateLinkServices**](/azure/templates/microsoft.network/privateLinkServices): služba privátního propojení, která vystavuje službu.
- [**Microsoft. Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses): Existují dvě veřejné IP adresy, jednu pro každý virtuální počítač.
- [**Microsoft. Network/privateendpoints**](/azure/templates/microsoft.network/privateendpoints): soukromý koncový bod pro přístup ke službě.

## <a name="deploy-the-template"></a>Nasazení šablony

Tady je postup, jak nasadit šablonu ARM do Azure:

1. Pokud se chcete přihlásit k Azure a otevřít šablonu, vyberte **nasadit do Azure**. Šablona vytvoří virtuální počítač, službu Load Balancer úrovně Standard, privátního koncového bodu, sítě a virtuální počítač k ověření.

   [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

2. Vyberte nebo vytvořte skupinu prostředků.
3. Zadejte uživatelské jméno a heslo správce virtuálního počítače.
4. Přečtěte si prohlášení o podmínkách a ujednáních. Pokud souhlasíte, vyberte Souhlasím **s podmínkami a ujednáními uvedenými nad**  >  **nákupem**.

## <a name="validate-the-deployment"></a>Ověření nasazení

> [!NOTE]
> Šablona ARM vygeneruje jedinečný název pro prostředek myConsumerVm<b>{UniqueID}</b> virtuálního počítače. Vygenerovanou hodnotu pro **{UniqueID}** nahraďte.

### <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Připojte se k virtuálnímu počítači _myConsumerVm {UniqueID}_ z Internetu následujícím způsobem:

1.  Na panelu hledání na portálu zadejte _myConsumerVm {UniqueID}_.

2.  Vyberte **Connect** (Připojit). **Připojení k virtuálnímu počítači** se otevře.

3.  Vyberte **Stáhnout soubor RDP**. Azure vytvoří soubor protokol RDP (Remote Desktop Protocol) (_. RDP_) a stáhne ho do vašeho počítače.

4.  Otevřete stažený soubor .rdp.

    a. Pokud se zobrazí výzva, vyberte **Připojit**.

    b. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.
    
    > [!NOTE]
    > Možná budete muset vybrat **Další volby**  >  **použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

5.  Vyberte **OK**.

6.  Při přihlášení se může zobrazit upozornění na certifikát. Pokud se zobrazí upozornění na certifikát, vyberte **Ano** nebo **Pokračovat**.

7.  Po zobrazení plochy virtuálního počítače ho minimalizujte, abyste se mohli vrátit k místnímu počítači.

### <a name="access-the-http-service-privately-from-the-vm"></a>Přístup ke službě HTTP soukromě z virtuálního počítače

Tady je postup, jak se připojit ke službě HTTP z virtuálního počítače pomocí privátního koncového bodu.

1.  Přejít do vzdálené plochy _myConsumerVm {UniqueID}_
2.  Otevřete prohlížeč a zadejte adresu privátního koncového bodu: `http://10.0.0.5/` .
3.  Zobrazí se výchozí stránka IIS.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete prostředky, které jste vytvořili ve službě privátního propojení, odstraňte skupinu prostředků. Tím se odebere služba privátního propojení a všechny související prostředky.

Pokud chcete odstranit skupinu prostředků, zavolejte `Remove-AzResourceGroup` rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Další kroky


Další informace o službách, které podporují soukromý koncový bod, najdete v těchto tématech:
> [!div class="nextstepaction"]
> [Dostupnost privátního propojení](private-link-overview.md#availability)
