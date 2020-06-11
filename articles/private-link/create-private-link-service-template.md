---
title: Šablona ARM služby privátního propojení Azure
description: Šablona ARM služby privátního propojení
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: 304ee8c1180c318dd6e99b6e81eb964e264951d1
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84667025"
---
# <a name="quickstart-create-a-private-link-service---resource-manager-template"></a>Rychlý Start: vytvoření služby privátního propojení – Správce prostředků šablonu

V tomto rychlém startu použijete šablonu Správce prostředků k vytvoření služby privátního propojení.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Tento rychlý Start můžete také dokončit pomocí [Azure Portal](create-private-link-service-portal.md), [Azure PowerShell](create-private-link-service-powershell.md)nebo rozhraní příkazového [řádku Azure CLI](create-private-link-service-cli.md).

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-private-link-service"></a>Vytvoření služby privátního propojení

Tato šablona vytvoří službu privátního propojení.

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-privatelink-service/).

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json" range="001-432" highlight="263-289":::

V šabloně je definováno víc prostředků Azure:

- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks) : jedna pro každý virtuální počítač
- [**Microsoft. Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers) : Nástroj pro vyrovnávání zatížení, který zveřejňuje virtuální počítače, které hostují službu.
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : 2 síťová rozhraní, jedna pro každý virtuální počítač
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : 2 virtuální počítače, jeden, který je hostitelem služby a druhý pro otestování připojení k privátnímu koncovému bodu
- [**Microsoft. COMPUTE/virtualMachines/rozšíření**](/azure/templates/Microsoft.Compute/virtualMachines/extensions) : rozšíření, které instaluje webový server
- [**Microsoft. Network/privateLinkServices**](/azure/templates/microsoft.network/privateLinkServices) : služba privátního propojení k zveřejnění soukromé služby
- [**Microsoft. Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses) : 2 Veřejná IP adresa, jedna pro každý virtuální počítač
- [**Microsoft. Network/privateendpoints**](/azure/templates/microsoft.network/privateendpoints) : privátní koncový bod pro přístup k soukromé službě

### <a name="deploy-the-template"></a>Nasazení šablony

Nasadit šablonu Správce prostředků do Azure:

1. Vyberte **nasadit do Azure** a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří virtuální počítač, službu Load Balancer úrovně Standard, privátní koncový bod, síť a virtuální počítač k ověření.

   [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

2. Vyberte nebo vytvořte skupinu prostředků,
3. Zadejte uživatelské jméno a heslo správce virtuálního počítače.
4. Vyberte Souhlasím **s podmínkami a ujednáními uvedenými nahoře** a pak vyberte **koupit**.

## <a name="validate-the-deployment"></a>Ověření nasazení

> [!NOTE]
> Šablona ARM vygeneruje pro prostředek myConsumerVm<b>{UniqueID}</b> jedinečný název, nahraďte prosím <b>{UniqueID}</b> vaší vygenerovanou hodnotou.

### <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Připojte se k virtuálnímu počítači _myConsumerVm {UniqueID}_ z Internetu následujícím způsobem:

1.  Na panelu hledání na portálu zadejte _myConsumerVm {UniqueID}_.

2.  Klikněte na tlačítko **Připojit**. Po výběru tlačítka **připojit** se **připojte k virtuálnímu počítači** .

3.  Vyberte **Stáhnout soubor RDP**. Azure vytvoří soubor protokol RDP (Remote Desktop Protocol) (_. RDP_) a stáhne ho do vašeho počítače.

4.  Otevřete stažený soubor. RDP \* .

    a. Pokud se zobrazí výzva, vyberte **Připojit**.

    b. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.
    
    > [!NOTE]
    > Možná budete muset vybrat **Další volby**  >  **použít jiný účet**a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

5.  Vyberte **OK**.

6.  Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění certifikátu, vyberte **Ano** nebo **pokračovat**.

7.  Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji tak, aby se vrátila k místnímu počítači.

### <a name="access-the-http-service-privately-from-the-vm"></a>Přístup ke službě HTTP soukromě z virtuálního počítače

V této části se připojíte ke službě HTTP z virtuálního počítače pomocí privátního koncového bodu.

1.  Ve vzdálené ploše _myConsumerVm {UniqueID}_
2.  Otevřete prohlížeč a zadejte adresu privátního koncového bodu.http://10.0.0.5/
3.  Zobrazí se výchozí stránka IIS.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete prostředky, které jste vytvořili ve službě privátního propojení, odstraňte skupinu prostředků. Tím se odebere služba privátního propojení a všechny související prostředky.

Pokud chcete odstranit skupinu prostředků, zavolejte `Remove-AzResourceGroup` rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Další kroky

- Další informace o [privátním propojení Azure](private-link-overview.md)
