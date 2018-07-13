---
title: Vytvoření virtuálního počítače se statickou veřejnou IP adresu – Azure portal | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit virtuální počítač se statickou veřejnou IP adresu pomocí webu Azure portal.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 524293f9a1ded73ee7cb6ba4f53208a9f9c54ffa
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38670980"
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>Vytvoření virtuálního počítače se statickou veřejnou IP adresu pomocí webu Azure portal

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (Classic)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Resource Manager, který Microsoft doporučuje pro většinu nových nasazení namísto modelu nasazení classic.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Vytvoření virtuálního počítače se statickou veřejnou IP Adresou

Vytvoření virtuálního počítače se statickou veřejnou IP adresu na webu Azure Portal, proveďte následující kroky:

1. V prohlížeči přejděte na portál [Azure Portal](https://portal.azure.com) a v případě potřeby se přihlaste pomocí účtu Azure.
2. V levém horním rohu na portálu klikněte na tlačítko **vytvořit prostředek**>>**Compute**>**systému Windows Server 2012 R2 Datacenter**.
3. V **vybrat model nasazení** vyberte **Resource Manageru** a klikněte na tlačítko **vytvořit**.
4. V **Základy** podokně zadejte následující informace virtuálního počítače a pak klikněte na **OK**.
   
    ![Azure portal – základy](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. V **zvolte velikost** podokně klikněte na tlačítko **A1 Standard** jako způsobem a pak klikněte na tlačítko **vyberte**.
   
    ![Azure portal – zvolit velikost](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. V **nastavení** podokně klikněte na tlačítko **veřejnou IP adresu**, pak v **vytvoření veřejné IP adresy** podokně v části **přiřazení**, klikněte na tlačítko  **Statické** následujícím způsobem. A pak klikněte na tlačítko **OK**.
   
    ![Azure portal – vytvoření veřejné IP adresy](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. V **nastavení** podokně klikněte na tlačítko **OK**.
8. Zkontrolujte **Souhrn** podokně jako způsobem a pak klikněte na tlačítko **OK**.
   
    ![Azure portal – vytvoření veřejné IP adresy](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. Všimněte si, že nová dlaždice na řídicím panelu.
   
    ![Azure portal – vytvoření veřejné IP adresy](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. Po vytvoření virtuálního počítače **nastavení** podokně se zobrazí takto:
    
    ![Azure portal – vytvoření veřejné IP adresy](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

## <a name="set-ip-addresses-within-the-operating-system"></a>Nastavení IP adresy v rámci operačního systému

Veřejná IP adresa přidělená k virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače by nikdy ručně přiřadit. Doporučuje se, že nepřiřadíte staticky privátní IP adresa přiřazená virtuální počítač Azure v rámci operačního systému virtuálního počítače, není-li nezbytné, například kdy [přidělení více IP adres k virtuálnímu počítači s Windows](virtual-network-multiple-ip-addresses-portal.md). Pokud jste ručně nastavili privátní IP adresu v rámci operačního systému, ujistěte se, že se jedná o stejné adrese jako privátní IP adresy přiřazené k Azure [síťové rozhraní](virtual-network-network-interface-addresses.md#change-ip-address-settings), nebo může ztratit připojení k virtuálnímu počítači. Další informace o [privátní IP adresu](virtual-network-network-interface-addresses.md#private) nastavení.

## <a name="next-steps"></a>Další postup

Veškerý provoz sítě může téct do a z virtuálního počítače v rámci tohoto článku vytvořili. Můžete definovat příchozích a odchozích pravidel zabezpečení v rámci skupiny zabezpečení sítě neomezují provoz, který může téct do a z rozhraní sítě a podsítě. Další informace o skupinách zabezpečení sítě najdete v tématu [přehled skupin zabezpečení sítě](security-overview.md).