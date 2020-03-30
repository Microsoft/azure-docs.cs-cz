---
title: Vytvoření virtuálního počítače se statickou veřejnou IP adresou – portál Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit virtuální počítač se statickou veřejnou IP adresou pomocí portálu Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 66050c16f40e0a06117327ef53e3aae87d03c5db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76043537"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Vytvoření virtuálního počítače se statickou veřejnou IP adresou pomocí portálu Azure

Můžete vytvořit virtuální počítač se statickou veřejnou IP adresou. Veřejná IP adresa umožňuje komunikovat s virtuálním počítačem z internetu. Přiřaďte statickou veřejnou IP adresu, nikoli dynamickou adresu, abyste zajistili, že se adresa nikdy nezmění. Další informace o [statických veřejných IP adresách](virtual-network-ip-addresses-overview-arm.md#allocation-method). Pokud chcete změnit veřejnou IP adresu přiřazenou existujícímu virtuálnímu počítači z dynamického na statický nebo pracovat se soukromými IP adresami, přečtěte si informace o [přidání, změně nebo odebrání IP adres](virtual-network-network-interface-addresses.md). Veřejné IP adresy mají [nominální poplatek](https://azure.microsoft.com/pricing/details/ip-addresses)a počet veřejných IP adres, které můžete použít na jedno předplatné, je [omezen.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **Výpočetní výkon**a pak vyberte **Windows Server 2016 VM**nebo jiný operační systém podle vašeho výběru.
3. Zadejte nebo vyberte následující informace, přijměte výchozí hodnoty zbývajících nastavení a pak vyberte **OK**:

    |Nastavení|Hodnota|
    |---|---|
    |Name (Název)|myVM|
    |Uživatelské jméno| Zadejte libovolné uživatelské jméno.|
    |Heslo| Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků| Vyberte **Použít existující** a pak vyberte **myResourceGroup**.|
    |Umístění| Vybrat **východní USA**|

4. Vyberte velikost virtuálního počítače a pak vyberte **Vybrat**.
5. V části **Nastavení**vyberte **Veřejná IP adresa**.
6. Zadejte *adresu myPublicIpAddress*, vyberte **Static**a pak vyberte **OK**, jak je znázorněno na následujícím obrázku:

   ![Vybrat statickou](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Pokud veřejná IP adresa musí být standardní skladová položka, vyberte **standardní** v části **Skladová položka**. Další informace o [sus veřejných IP adres](virtual-network-ip-addresses-overview-arm.md#sku). Pokud virtuální počítač bude přidán do back-endového fondu veřejného nástroje pro vyrovnávání zatížení Azure, skladová položka veřejné IP adresy virtuálního počítače se musí shodovat s skladovou položkou veřejné IP adresy nástroje pro vyrovnávání zatížení. Podrobnosti najdete [v tématu Azure Load Balancer](../load-balancer/concepts-limitations.md#skus).

6. Vyberte port nebo žádné porty v části **Vybrat veřejné příchozí porty**. Portál 3389 je vybrán, aby byl umožněn vzdálený přístup k virtuálnímu počítači se systémem Windows Server z internetu. Otevření portu 3389 z internetu se nedoporučuje pro produkční úlohy.

   ![Výběr portu](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Přijměte zbývající výchozí nastavení a vyberte **OK**.
8. Na stránce **Souhrn** vyberte **Vytvořit**. Nasazení virtuálního počítače trvá několik minut.
9. Po nasazení virtuálního počítače zadejte *myPublicIpAddress* do vyhledávacího pole v horní části portálu. Když se ve výsledcích hledání zobrazí **myPublicIpAddress,** vyberte ji.
10. Můžete zobrazit přiřazenou veřejnou IP adresu a její přiřazení k virtuálnímu počítači **myVM,** jak je znázorněno na následujícím obrázku:

    ![Zobrazit veřejnou IP adresu](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Azure přiřadil veřejnou IP adresu z adres používaných v oblasti, ve které jste virtuální počítač vytvořili. Můžete si stáhnout seznam rozsahů (předpon) pro [veřejný](https://www.microsoft.com/download/details.aspx?id=56519) cloud Azure a cloudy Azure [US Government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) a [Germany](https://www.microsoft.com/download/details.aspx?id=57064).

11. Vyberte **Konfigurace,** chcete-li ověřit, zda je přiřazení **statické**.

    ![Zobrazit veřejnou IP adresu](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> Neupravujte nastavení IP adresy v operačním systému virtuálního počítače. Operační systém neví o veřejných IP adresách Azure. I když můžete do operačního systému přidat nastavení privátní IP adresy, doporučujeme, abyste tak neučinili, pokud to není nutné, a to až po přečtení [Přidat soukromou IP adresu do operačního systému](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do pole **Hledat** v horní části portálu zadejte *myResourceGroup*. Jakmile se ve výsledcích hledání zobrazí skupina prostředků **myResourceGroup**, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. V části **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ** zadejte *myResourceGroup* a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

- Další informace o [veřejných IP adresách](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) v Azure
- Další informace o všech [nastaveních veřejných IP adres](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Další informace o [privátních IP adresách](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) a přiřazení [statické privátní IP adresy](virtual-network-network-interface-addresses.md#add-ip-addresses) k virtuálnímu počítači Azure
- Další informace o vytváření virtuálních počítačů [s Linuxem](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)