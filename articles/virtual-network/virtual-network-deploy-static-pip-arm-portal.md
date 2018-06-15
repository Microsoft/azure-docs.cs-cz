---
title: Vytvoření virtuálního počítače se statickou veřejnou IP adresu - portálu Azure | Microsoft Docs
description: Naučte se vytvořit virtuální počítač se statickou veřejnou IP adresu pomocí portálu Azure.
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
ms.openlocfilehash: 50ae4d6e8c275db16f811a2a1a063eda441f150b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31525535"
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>Vytvoření virtuálního počítače se statickou veřejnou IP adresu pomocí portálu Azure

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (Classic)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Resource Manager, které společnost Microsoft doporučuje pro většinu nových nasazení místo modelu nasazení classic.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Vytvoření virtuálního počítače se statickou veřejnou IP adresu

Postup vytvoření virtuálního počítače se statickou veřejnou IP adresu na portálu Azure, proveďte následující kroky:

1. V prohlížeči přejděte na portál [Azure Portal](https://portal.azure.com) a v případě potřeby se přihlaste pomocí účtu Azure.
2. V levém horním rohu na portálu klikněte na tlačítko **vytvořit prostředek**>>**výpočetní**>**Windows Server 2012 R2 Datacenter**.
3. V **vybrat model nasazení** vyberte **Resource Manager** a klikněte na tlačítko **vytvořit**.
4. V **Základy** podokně zadejte následující informace o virtuálních počítačů a pak klikněte na tlačítko **OK**.
   
    ![Portál Azure – základy](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. V **zvolte velikost** podokně klikněte na tlačítko **A1 standardní** způsobem a potom klikněte na **vyberte**.
   
    ![Portál Azure – zvolte velikost](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. V **nastavení** podokně klikněte na tlačítko **veřejnou IP adresu**, potom v **vytvoření veřejné IP adresy** podokně v části **přiřazení**, klikněte na tlačítko  **Statické** jako následujícím způsobem. A pak klikněte na **OK**.
   
    ![Portál Azure – vytvoření veřejné IP adresy](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. V **nastavení** podokně klikněte na tlačítko **OK**.
8. Zkontrolujte **Souhrn** podokně způsobem a potom klikněte na **OK**.
   
    ![Portál Azure – vytvoření veřejné IP adresy](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. Všimněte si nové dlaždice na řídicím panelu.
   
    ![Portál Azure – vytvoření veřejné IP adresy](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. Po vytvoření virtuálního počítače **nastavení** podokně zobrazí následujícím způsobem:
    
    ![Portál Azure – vytvoření veřejné IP adresy](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

## <a name="set-ip-addresses-within-the-operating-system"></a>Nastavení IP adresy v rámci operačního systému

Byste měli přiřadit nikdy ručně veřejnou IP adresu přiřazené pro virtuální počítač Azure v rámci operačního systému virtuálního počítače. Doporučuje se, že nepřiřadíte staticky privátní IP přiřazené k virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače, pokud nezbytné, jako např. kdy [přiřazení více IP adres pro virtuální počítač s Windows](virtual-network-multiple-ip-addresses-portal.md). Pokud ručně nastavit privátní IP adresu v operačním systému, zajistěte, aby byl stejnou adresu jako přiřazené Azure privátní IP adresy [síťové rozhraní](virtual-network-network-interface-addresses.md#change-ip-address-settings), nebo můžete ztratit připojení k virtuálnímu počítači. Další informace o [privátní IP adresu](virtual-network-network-interface-addresses.md#private) nastavení.

## <a name="next-steps"></a>Další postup

Síťové přenosy můžete procházet do a z virtuálního počítače vytvořit v tomto článku. Můžete definovat pravidla pro příchozí a odchozí zabezpečení v rámci skupiny zabezpečení sítě, které omezit přenos, který může obtékat do a z rozhraní sítě, podsítě nebo obojí. Další informace o skupinách zabezpečení sítě najdete v tématu [přehled skupiny zabezpečení sítě](security-overview.md).