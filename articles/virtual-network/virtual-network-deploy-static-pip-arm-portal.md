---
title: Vytvoření virtuálního počítače se statickou veřejnou IP adresou – Azure Portal | Microsoft Docs
description: Naučte se, jak vytvořit virtuální počítač se statickou veřejnou IP adresou pomocí Azure Portal.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: allensu
ms.openlocfilehash: 9e06e4079a5118e0aa9dedb1fca719f0b28e5716
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448623"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Vytvoření virtuálního počítače se statickou veřejnou IP adresou pomocí Azure Portal

Můžete vytvořit virtuální počítač se statickou veřejnou IP adresou. Veřejná IP adresa vám umožní komunikovat s virtuálním počítačem z Internetu. Přiřaďte statickou veřejnou IP adresu, nikoli dynamickou adresu, abyste zajistili, že se adresa nikdy nemění. Přečtěte si další informace o [statických veřejných IP adresách](virtual-network-ip-addresses-overview-arm.md#allocation-method). Postup změny veřejné IP adresy přiřazené existujícímu virtuálnímu počítači z dynamického na statickou nebo pro práci s privátními IP adresami najdete v tématu [Přidání, změna nebo odebrání IP adres](virtual-network-network-interface-addresses.md). Veřejné IP adresy mají [nominální poplatek](https://azure.microsoft.com/pricing/details/ip-addresses)a [omezení](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) počtu veřejných IP adres, které můžete použít v rámci předplatného.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **COMPUTE**a potom vyberte **virtuální počítač s Windows serverem 2016**nebo jiný operační systém podle vašeho výběru.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **OK**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVM|
    |Uživatelské jméno| Zadejte libovolné uživatelské jméno.|
    |Heslo| Zadejte libovolné heslo. Heslo musí mít délku aspoň 12 znaků a musí splňovat [definované požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků| Vyberte **Použít existující** a pak vyberte **myResourceGroup**.|
    |Umístění| Vyberte **východní USA**|

4. Vyberte velikost virtuálního počítače a pak vyberte **Vybrat**.
5. V části **Nastavení**vyberte **Veřejná IP adresa**.
6. Zadejte *myPublicIpAddress*, vyberte **static**a pak vyberte **OK**, jak je znázorněno na následujícím obrázku:

   ![Vybrat statickou](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Pokud veřejná IP adresa musí být standardní SKU, vyberte v části **SKU**možnost **Standard** . Přečtěte si další informace o [SKU veřejných IP adres](virtual-network-ip-addresses-overview-arm.md#sku). Pokud bude virtuální počítač přidán do fondu back-end veřejné Azure Load Balancer, musí SKU veřejné IP adresy virtuálního počítače odpovídat SKU veřejné IP adresy nástroje pro vyrovnávání zatížení. Podrobnosti najdete v tématu [Azure Load Balancer](../load-balancer/skus.md).

6. Vyberte port nebo žádné porty v části **Vybrat veřejné příchozí porty**. Je vybrán portál 3389, který umožňuje vzdálený přístup k virtuálnímu počítači s Windows serverem z Internetu. Pro produkční úlohy se nedoporučuje otevřít port 3389 z Internetu.

   ![Vybrat port](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Přijměte zbývající výchozí nastavení a vyberte **OK**.
8. Na stránce **Souhrn** vyberte **Vytvořit**. Nasazení virtuálního počítače trvá několik minut.
9. Po nasazení virtuálního počítače do vyhledávacího pole v horní části portálu zadejte *myPublicIpAddress* . Pokud se ve výsledcích hledání zobrazí **myPublicIpAddress** , vyberte ji.
10. Můžete zobrazit veřejnou IP adresu, která je přiřazená a adresa je přiřazena k virtuálnímu počítači **myVM** , jak je znázorněno na následujícím obrázku:

    ![Snímek obrazovky se zobrazí v podokně adresa pro veřejné I P s adresou I P a názvem, který se nazývá. ](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Azure přiřadil veřejnou IP adresu z adres používaných v oblasti, ve které jste virtuální počítač vytvořili. Můžete si stáhnout seznam rozsahů (předpon) pro [veřejný](https://www.microsoft.com/download/details.aspx?id=56519) cloud Azure a cloudy Azure [US Government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) a [Germany](https://www.microsoft.com/download/details.aspx?id=57064).

11. Vyberte **konfiguraci** a potvrďte, že přiřazení je **statické**.

    ![Snímek obrazovky zobrazuje podokno adresa veřejné I P s vybranou položkou konfigurace.](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> Neměňte nastavení IP adresy v operačním systému virtuálního počítače. Operační systém neznáte veřejné IP adresy Azure. I když můžete do operačního systému přidat nastavení privátních IP adres, doporučujeme, abyste to neučinili, pokud není potřeba, a ne až po čtení [Přidání privátní IP adresy do operačního systému](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do pole **Hledat** v horní části portálu zadejte *myResourceGroup*. Jakmile se ve výsledcích hledání zobrazí skupina prostředků **myResourceGroup**, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. V části **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ** zadejte *myResourceGroup* a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

- Další informace o [veřejných IP adresách](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) v Azure
- Další informace o všech [nastaveních veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Další informace o [privátních IP adresách](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) a přiřazení [statických privátních IP adres](virtual-network-network-interface-addresses.md#add-ip-addresses) k virtuálnímu počítači Azure
- Další informace o vytváření virtuálních počítačů se systémy [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)