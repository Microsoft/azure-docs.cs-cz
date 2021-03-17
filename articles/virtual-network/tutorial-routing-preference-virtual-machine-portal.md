---
title: Konfigurace předvolby směrování pro virtuální počítač Azure Portal
description: Přečtěte si, jak vytvořit virtuální počítač s veřejnou IP adresou s volbou předvolby směrování pomocí Azure Portal.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.openlocfilehash: 0559d02ec603d12578fa46d9790d0711fde5e38b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670903"
---
# <a name="configure-routing-preference-for-a-vm-using-the-azure-portal"></a>Konfigurace předvolby směrování pro virtuální počítač pomocí Azure Portal

V tomto článku se dozvíte, jak nakonfigurovat předvolby směrování pro virtuální počítač. Internetový provoz z virtuálního počítače bude směrován přes síť poskytovatele internetových služeb, pokud zvolíte možnost **Internet** jako předvolby směrování. Výchozí směrování je prostřednictvím globální sítě Microsoftu.

V tomto článku se dozvíte, jak vytvořit virtuální počítač s veřejnou IP adresou, která je nastavená na směrování provozu přes veřejný Internet pomocí Azure Portal.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com/).

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **COMPUTE** a potom vyberte **virtuální počítač s Windows serverem 2016** nebo jiný operační systém podle vašeho výběru.
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
5. Na kartě **sítě** klikněte na **vytvořit novou** pro **veřejnou IP adresu**.
6. Zadejte *myPublicIpAddress*, vyberte SKU jako **Standard** a pak vyberte směrování předvolby **Internetu** a potom stiskněte **OK**, jak je znázorněno na následujícím obrázku:

   ![Vybrat statickou](./media/tutorial-routing-preference-virtual-machine-portal/routing-preference-internet-new.png)

6. Vyberte port nebo žádné porty v části **Vybrat veřejné příchozí porty**. Je vybrán portál 3389, který umožňuje vzdálený přístup k virtuálnímu počítači s Windows serverem z Internetu. Pro produkční úlohy se nedoporučuje otevřít port 3389 z Internetu.

   ![Vybrat port](./media/tutorial-routing-preference-virtual-machine-portal/pip-ports-new.png)

7. Přijměte zbývající výchozí nastavení a vyberte **OK**.
8. Na stránce **Souhrn** vyberte **Vytvořit**. Nasazení virtuálního počítače trvá několik minut.
9. Po nasazení virtuálního počítače do vyhledávacího pole v horní části portálu zadejte *myPublicIpAddress* . Pokud se ve výsledcích hledání zobrazí **myPublicIpAddress** , vyberte ji.
10. Můžete zobrazit veřejnou IP adresu, která je přiřazená a adresa je přiřazena k virtuálnímu počítači **myVM** , jak je znázorněno na následujícím obrázku:

    ![Snímek obrazovky zobrazuje veřejnou síťovou kartu a P/MYNIC síťového rozhraní.](./media/tutorial-routing-preference-virtual-machine-portal/pip-properties-new.png)

11. Vyberte **sítě** a pak klikněte na síťový adaptér **MYNIC** a potom vyberte veřejnou IP adresu a potvrďte, že je priorita směrování přiřazená jako **Internet**.

    ![Snímek obrazovky ukazuje předvolby I adresy P a směrování pro veřejnou adresu P.](./media/tutorial-routing-preference-virtual-machine-portal/pip-routing-internet-new.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do pole **Hledat** v horní části portálu zadejte *myResourceGroup*. Jakmile se ve výsledcích hledání zobrazí skupina prostředků **myResourceGroup**, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. V části **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ** zadejte *myResourceGroup* a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [veřejné IP adrese s upřednostněním směrování](routing-preference-overview.md).
- Přečtěte si další informace o [veřejných IP adresách](./public-ip-addresses.md#public-ip-addresses) v Azure.
- Přečtěte si další informace o všech [nastaveních veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address).