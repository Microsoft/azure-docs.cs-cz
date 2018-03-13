---
title: "Vytvoření virtuální sítě | Šablona Azure Resource Manageru | Microsoft Docs"
description: "Naučte se vytvořit virtuální síť pomocí šablony Azure Resource Manager."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 69530861-2f97-4a6e-b336-a7baf2690044
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bb096f64a6bc41ad2e75c058c7a9f00bbe480207
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-virtual-network-using-an-azure-resource-manager-template"></a>Vytvoření virtuální sítě pomocí šablony Azure Resource Manager

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure nabízí dva modely nasazení: Azure Resource Manager a Classic. Microsoft doporučuje vytváření prostředků prostřednictvím modelu nasazení Resource Manager. Další informace o rozdílech mezi těmito dvěma modely najdete v článku [Vysvětlení modelů nasazení Azure](../azure-resource-manager/resource-manager-deployment-model.md).
 
Tento článek vysvětluje, jak vytvořit virtuální síť pomocí modelu nasazení Resource Manager pomocí šablony Azure Resource Manager. Virtuální síť můžete vytvořit také prostřednictvím modelu nasazení Resource Manager pomocí jiných nástrojů nebo prostřednictvím modelu nasazení Classic. Pokud to chcete provést, vyberte odpovídající možnost z následujícího seznamu:

> [!div class="op_single_selector"]
- [Azure Portal](quick-create-portal.md)
- [PowerShell](quick-create-powershell.md)
- [Rozhraní příkazového řádku](quick-create-cli.md)
- [Šablona](virtual-networks-create-vnet-arm-template-click.md)
- [Portál (Classic)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (Classic)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [Rozhraní příkazového řádku (Classic)](virtual-networks-create-vnet-classic-cli.md)

Zjistěte, jak stáhnout a upravit a existující šablonu Azure Resource Manageru z webu GitHub a nasazení šablony z Githubu, prostředí PowerShell a rozhraní příkazového řádku Azure.

Pokud jednoduše nasazujete přímo z Githubu, beze změn, šablony Azure Resource Manageru pokračujte [nasazení šablony z githubu](#deploy-the-arm-template-by-using-click-to-deploy).

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Stažení a pochopení šablony Azure Resource Manageru
Můžete stáhnout existující šablonu pro vytvoření virtuální sítě a dvě podsítě z Githubu, proveďte požadované změny, může být vhodné a opakovaně ji používat. Uděláte to tak, proveďte následující kroky:

1. Přejděte na stránku [vzorové šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Klikněte na **azuredeploy.json** a potom klikněte na **RAW**.
3. Uložte soubor do místní složky v počítači.
4. Pokud jste obeznámeni s šablonami, přejděte ke kroku 7.
5. Otevřete soubor jste uložili a prohlédněte si obsah v části **parametry** na řádku 5. Parametry šablony Azure Resource Manageru představují zástupce hodnot, které můžete doplnit během nasazování.
   
   | Parametr | Popis |
   | --- | --- |
   | **vnetName** |Název nové sítě VNet |
   | **addressPrefix** |Adresní prostor sítě VNet ve formátu CIDR |
   | **subnet1Name** |Název první sítě VNet |
   | **subnet1Prefix** |Blok CIDR pro první podsíť |
   | **subnet2Name** |Název druhé sítě VNet |
   | **subnet2Prefix** |Blok CIDR pro druhou podsíť |
   
   > [!IMPORTANT]
   > Šablony Azure Resource Manageru udržované na webu GitHub se můžou časem změnit. Před použitím proto šablonu vždy nejprve zkontrolujte.
   > 
   > 
6. Prohlédněte si obsah v části **resources** a všimněte si následujících parametrů:
   
   * **type**. Typ prostředku vytvořeného šablonou. V tomto případě se jedná o prostředek **Microsoft.Network/virtualNetworks**, který reprezentuje síť VNet.
   * **name**. Název prostředku. Všimněte si použití **[parameters('vnetName')]**, což znamená, že zadaný jako vstup uživatelem nebo ze souboru parametrů během nasazování název.
   * **properties**. Seznam vlastností prostředku. Tato šablona používá během vytváření sítě VNet vlastnosti adresního prostoru a podsítě.
7. Přejděte zpět na stránku [vzorové šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Klikněte na **azuredeploy-paremeters.json** a potom klikněte na **RAW**.
9. Uložte soubor do místní složky v počítači.
10. Otevřete soubor, který jste právě uložili, a upravte hodnoty parametrů. Pro nasazení sítě VNet popsané v tomto scénáři použijte níže uvedené následující hodnoty:

    ```json
        {
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }
    ```

11. Uložte soubor.


## <a name="deploy-the-template-using-powershell"></a>Šablonu nasadit pomocí prostředí PowerShell

Proveďte následující kroky k nasazení šablony, které jste stáhli pomocí prostředí PowerShell:

1. Instalace a konfigurace prostředí Azure PowerShell pomocí kroků v [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview) článku.
2. Spuštěním následujícího příkazu vytvoříte novou skupinu prostředků:

    ```powershell
    New-AzureRmResourceGroup -Name TestRG -Location centralus
    ```

    Příkaz vytvoří skupinu prostředků s názvem *TestRG* v *střed USA* oblast azure. Další informace o skupinách prostředků najdete v článku [Přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md).

    Očekávaný výstup:

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/[Id]/resourceGroups/TestRG

3. Spusťte následující příkaz, který nasadíte novou síť VNet pomocí šablony a parametr soubory stáhli a upravili v předchozích krocích:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestVNetDeployment -ResourceGroupName TestRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

    Očekávaný výstup:
   
        DeploymentName    : TestVNetDeployment
        ResourceGroupName : TestRG
        ProvisioningState : Succeeded
        Timestamp         : [Date and time]
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            location         String                     Central US
                            vnetName         String                     TestVNet
                            addressPrefix    String                     192.168.0.0/16
                            subnet1Prefix    String                     192.168.1.0/24
                            subnet1Name      String                     FrontEnd
                            subnet2Prefix    String                     192.168.2.0/24
                            subnet2Name      String                     BackEnd
   
        Outputs           :
4. Spusťte následující příkaz zobrazíte vlastnosti nové sítě vnet:

    ```powershell
    Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    ```

    Očekávaný výstup:

        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : centralus
        Id                : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"[Id]"
        ProvisioningState : Succeeded
        Tags              :
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              },
                              {
                                "Name": "BackEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              }
                            ]

## <a name="deploy-the-template-using-click-to-deploy"></a>Šablonu nasadit pomocí, klikněte na tlačítko nasadit

Můžete opakovaně používat předdefinované šablony Azure Resource Manager nahrán do úložiště GitHub spravován společností Microsoft a otevřené celé komunitě. Tyto šablony je možné nasazovat přímo z Githubu, nebo stáhnout a upravit tak, aby vyhovovaly vašim potřebám. Pokud chcete nasadit šablonu, která vytvoří síť VNet se dvěma podsítěmi, proveďte následující kroky:

1. V prohlížeči přejděte na stránku [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).
2. Přejděte dolů v seznamu šablon a klikněte na šablonu **101-vnet-two-subnets**. Podívejte se do souboru **README.md** znázorněného níže.

    ![Soubor READEME.md na webu GitHub](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. Klikněte na **Deploy to Azure** (Nasadit do Azure). V případě potřeby zadejte své přihlašovací údaje Azure. 
4. V okně **Parametry** zadejte hodnoty, které chcete použít k vytvoření nové sítě VNet, a klikněte na **OK**. Následující obrázek znázorňuje hodnoty pro tento scénář:
   
    ![Parametry šablony ARM](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

5. Klikněte na **Skupina prostředků** a vyberte skupinu prostředků, do které chcete přidat síť VNet, případně klikněte na **Vytvořit novou** a přidejte síť VNet do nové skupiny prostředků. Následující obrázek znázorňuje prostředku nastavení skupiny pro novou skupinu prostředků s názvem **TestRG**:

    ![Skupina prostředků](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

6. V případě potřeby změňte nastavení **Předplatné** a **Umístění** sítě VNet.
7. Pokud nechcete tuto síť VNet zobrazovat jako dlaždici v zobrazení **Úvodní panel**, zakažte možnost **Připnout na Úvodní panel**.
8. Klikněte na tlačítko **právní podmínky**, přečtěte si podmínky a klikněte na tlačítko **koupit** souhlasit. 
9. Kliknutím na **Vytvořit** vytvořte síť VNet.
   
    ![Dlaždice Odesílá se nasazení na portálu Preview](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

10. Po dokončení v portálu Azure klikněte na nasazení **všechny služby**, typ *virtuální sítě* do pole filtr, který se zobrazí, pak klikněte na virtuální sítě najdete v okně virtuální sítě. V okně klikněte na tlačítko *TestVNet*. V *TestVNet* okně klikněte na tlačítko **podsítě** zobrazíte vytvořený podsítě, jak je znázorněno na následujícím obrázku:
    
     ![Vytvoření sítě VNet na portálu Preview](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.png)

## <a name="next-steps"></a>Další postup

Zjistěte, jak připojit:

- Virtuální počítač k virtuální síti pomocí informací v článcích [Vytvoření virtuálního počítače s Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) nebo [Vytvoření virtuálního počítače s Linuxem](../virtual-machines/linux/quick-create-portal.md). Místo vytváření virtuální sítě a podsítě v rámci kroků v těchto článcích můžete vybrat existující virtuální síť a podsíť, ke které se má virtuální počítač připojit.
- Virtuální síť k jiným virtuálním sítím pomocí informací v článku [Propojení virtuálních sítí](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- Virtuální síť k místní síti pomocí virtuální privátní sítě (VPN) typu Site-to-Site nebo okruhu ExpressRoute. Informace najdete v článcích [Připojení virtuální sítě k místní síti pomocí sítě VPN typu Site-to-Site](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) a [Propojení virtuální sítě s okruhem ExpressRoute](../expressroute/expressroute-howto-linkvnet-arm.md).
