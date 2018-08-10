---
title: Vytvoření virtuálního počítače se statickou veřejnou IP adresu – Azure portal | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit virtuální počítač se statickou veřejnou IP adresu pomocí webu Azure portal.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: jdial
ms.openlocfilehash: 9b6db45e38267c70adef3f5a341b8b918b9e78fb
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714423"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Vytvoření virtuálního počítače se statickou veřejnou IP adresu pomocí webu Azure portal

Vytvoření virtuálního počítače se statickou veřejnou IP adresu. Veřejná IP adresa umožňuje komunikaci k virtuálnímu počítači z Internetu. Přiřadíte statickou veřejnou IP adresu, nikoli s dynamickou adresou, k zajištění, že adresa nikdy nemění. Další informace o [statické veřejné IP adresy](virtual-network-ip-addresses-overview-arm.md#allocation-method). Chcete-li změnit přiřazené do stávajícího virtuálního počítače z dynamické na statickou veřejnou IP adresu pro práci s privátními IP adresami, najdete v článku [přidání, změna nebo odebrání IP adres](virtual-network-network-interface-addresses.md). Mají veřejné IP adresy [nominální poplatek](https://azure.microsoft.com/pricing/details/ip-addresses)a je [limit](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) počtu veřejné IP adresy, které můžete použít jedno předplatné.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **Compute**a pak vyberte **virtuálního počítače s Windows serverem 2016**, nebo jiný operační systém podle vašeho výběru.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **OK**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVM|
    |Uživatelské jméno| Zadejte libovolné uživatelské jméno.|
    |Heslo| Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků| Vyberte **Použít existující** a pak vyberte **myResourceGroup**.|
    |Umístění| Vyberte **USA – východ**.|

4. Vyberte velikost virtuálního počítače a pak vyberte **Vybrat**.
5. V části **nastavení**vyberte **veřejnou IP adresu**.
6. Zadejte *myPublicIpAddress*vyberte **statické**a pak vyberte **OK**, jak je znázorněno na následujícím obrázku:

   ![Vyberte statickou](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Pokud veřejná IP adresa musí být standardní SKU, **standardní** pod **SKU**. Další informace o [veřejné IP adresy skladové položky](virtual-network-ip-addresses-overview-arm.md#sku). Pokud virtuální počítač se přidají do fondu back-end veřejný Azure Load Balancer, skladovou Položku virtuálního počítače veřejné IP adresy musí odpovídat SKU nástroje pro vyrovnávání zatížení veřejnou IP adresu. Podrobnosti najdete v tématu [Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

6. Vyberte port, žádné porty v rámci **vyberte veřejné příchozí porty**. Portál 3389 je vybraná, a povolte vzdálený přístup do virtuálních počítačů s Windows serverem z Internetu. Otevření portu 3389 z Internetu se nedoporučuje pro produkční úlohy.

   ![Výběr portu](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Potvrďte zbývající výchozí nastavení a vyberte **OK**.
8. Na **Souhrn** stránce **vytvořit**. Virtuálního počítače trvá několik minut, než nasazení.
9. Po nasazení virtuálního počítače zadejte *myPublicIpAddress* do vyhledávacího pole v horní části portálu. Když **myPublicIpAddress** se zobrazí ve výsledcích hledání vyberte ji.
10. Můžete zobrazit veřejnou IP adresu, která je přiřazena a, která je přiřazena adresa **myVM** virtuálního počítače, jak je znázorněno na následujícím obrázku:

    ![Zobrazení veřejné IP adresy](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Azure přiřadit veřejnou IP adresu z adresy používané v oblasti, kterou jste vytvořili virtuální počítač. Můžete si stáhnout seznam rozsahů (předpon) pro [veřejný](https://www.microsoft.com/download/details.aspx?id=56519) cloud Azure a cloudy Azure [US Government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) a [Germany](https://www.microsoft.com/download/details.aspx?id=57064).

11. Vyberte **konfigurace** chcete potvrdit, že přiřazení **statické**.

    ![Zobrazení veřejné IP adresy](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
Neprovádějte žádné změny nastavení IP adresy v rámci operačního systému virtuálního počítače. Operační systém je vědět o Azure veřejné IP adresy. I když nastavení privátní IP adresy můžete přidat do operačního systému, doporučujeme, pokud to neuděláte není-li nutné a nikoli až po čtení [přidejte privátní IP adresu pro operační systém](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do pole **Hledat** v horní části portálu zadejte *myResourceGroup*. Jakmile se ve výsledcích hledání zobrazí skupina prostředků **myResourceGroup**, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. V části **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ** zadejte *myResourceGroup* a vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

- Další informace o [veřejné IP adresy](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) v Azure
- Další informace o všech [nastavení veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Další informace o [privátních IP adres](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) a přiřazení [statickou privátní IP adresou](virtual-network-network-interface-addresses.md#add-ip-addresses) na virtuálním počítači Azure
- Další informace o vytváření [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuálních počítačů