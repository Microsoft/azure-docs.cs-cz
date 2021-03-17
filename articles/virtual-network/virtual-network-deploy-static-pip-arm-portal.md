---
title: Vytvoření virtuálního počítače se statickou veřejnou IP adresou – Azure Portal
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
ms.date: 11/12/2020
ms.author: allensu
ms.openlocfilehash: d416af3d3a8eb8ab8057f13cc0d9a133adcb849a
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221152"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Vytvoření virtuálního počítače se statickou veřejnou IP adresou pomocí Azure Portal

Veřejná IP adresa vám umožní komunikovat s virtuálním počítačem z Internetu. 

Přiřaďte statickou veřejnou IP adresu, nikoli dynamickou adresu, abyste zajistili, že se adresa nikdy nemění.   

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na web [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. V levé horní části portálu vyberte **vytvořit prostředek**  >    >  **virtuální počítač** COMPUTE nebo ve vyhledávacím poli vyhledejte **virtuální počítač** .
   
2. V části **vytvořit virtuální počítač** zadejte nebo vyberte hodnoty na kartě **základy** :

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vyberte, že chcete **vytvořit novou** IP adresu. </br> Do **název** zadejte **myResourceGroup**. </br> Vyberte **OK**. |
    | **Podrobnosti o instancích** |  |
    | Název virtuálního počítače | Zadejte **myVM** |
    | Oblast | Vyberte **východní USA** |
    | Možnosti dostupnosti | Vyberte možnost **nepožaduje se žádná redundance infrastruktury** . |
    | Image | Vyberte **Windows Server 2019 Datacenter – Gen1** |
    | Instance Azure Spot | Vybrat **ne** |
    | Velikost | Vyberte velikost virtuálního počítače nebo použijte výchozí nastavení. |
    | **Účet správce** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno. |
    | Heslo | Zadat heslo |
    | Potvrzení hesla | Znovu zadejte heslo. |

3. Vyberte kartu **síť** nebo vyberte **Další: disky** a **Další: síť**.
  
4. Na kartě sítě vyberte nebo zadejte:

    | Nastavení | Hodnota |
    |-|-|
    | **Síťové rozhraní** |  |
    | Virtuální síť | Přijměte výchozí název sítě. |
    | Podsíť | Přijměte výchozí konfiguraci podsítě. |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. </br> V nástroji **vytvořit veřejnou IP adresu** zadejte do název **myPublicIP**. </br> V případě **SKU** vyberte možnost **Standard**. </br> **Přiřazení**, vyberte **statické**. </br> Vyberte **OK**.  |
    | Skupina zabezpečení sítě NIC | Vybrat **základní**|
    | Veřejné příchozí porty | Vyberte možnost **Povolení vybraných portů**. |
    | Vyberte příchozí porty | Vybrat **RDP (3389)** |

    > [!WARNING]
    > Je vybrán portál 3389, který umožňuje vzdálený přístup k virtuálnímu počítači s Windows serverem z Internetu. Otevření portu 3389 na Internet se nedoporučuje spravovat produkční úlohy. </br> Pokud chcete získat zabezpečený přístup k virtuálním počítačům Azure, přečtěte si téma **[co je Azure bastionu?](../bastion/bastion-overview.md)**
   
5. Vyberte **Zkontrolovat a vytvořit**. 
  
6. Zkontrolujte nastavení a pak vyberte **vytvořit**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do pole **Hledat** v horní části portálu zadejte **myResourceGroup**. Jakmile se ve výsledcích hledání zobrazí skupina prostředků **myResourceGroup**, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. V části **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ** zadejte **myResourceGroup** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Viz [Přidání, změna nebo odebrání IP adres](virtual-network-network-interface-addresses.md):

* Změna veřejné IP adresy z dynamické na statickou.
* Pracujte s privátními IP adresami.

Veřejné IP adresy mají [nominální poplatek](https://azure.microsoft.com/pricing/details/ip-addresses). Existuje [omezení](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) počtu veřejných IP adres, které můžete použít v rámci předplatného.

SKU veřejné IP adresy virtuálního počítače se musí shodovat s SKU veřejné IP adresy Azure Load Balancer po přidání do fondu back-endu. Podrobnosti najdete v tématu [Azure Load Balancer](../load-balancer/skus.md).

Můžete si stáhnout seznam rozsahů (předpon) pro [veřejný](https://www.microsoft.com/download/details.aspx?id=56519) cloud Azure a cloudy Azure [US Government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) a [Germany](https://www.microsoft.com/download/details.aspx?id=57064).

- Přečtěte si další informace o [statických veřejných IP adresách](./public-ip-addresses.md#allocation-method).
- Přečtěte si další informace o [veřejných IP adresách](./public-ip-addresses.md#public-ip-addresses) v Azure.
- Přečtěte si další informace o všech [nastaveních veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Přečtěte si další informace o [privátních IP adresách](./private-ip-addresses.md) a přiřazení [statických privátních IP adres](virtual-network-network-interface-addresses.md#add-ip-addresses) k virtuálnímu počítači Azure.
