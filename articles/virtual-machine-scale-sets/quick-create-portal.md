---
title: Rychlý Start – vytvoření sady škálování virtuálního počítače v Azure Portal
description: Začněte s nasazeními a Naučte se, jak rychle vytvořit virtuální počítač škáluje Azure Portal.
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.subservice: ''
ms.date: 06/30/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 0408341eff483de71d76a24a933ece4cbdc2b947
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100535276"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Rychlý start: Vytvoření škálovací sady virtuálních počítačů na webu Azure Portal

Sada škálování virtuálního počítače umožňuje nasadit a spravovat sadu automatických škálování virtuálních počítačů. Počet virtuálních počítačů ve škálovací sadě můžete škálovat ručně nebo můžete definovat pravidla pro automatické škálování podle využití prostředků, například podle požadavků na CPU a paměť nebo podle provozu. Nástroj pro vyrovnávání zatížení Azure pak bude distribuovat provoz do instancí virtuálních počítačů ve škálovací sadě. V tomto rychlém startu vytvoříte škálovací sadu virtuálních počítačů na webu Azure Portal.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="log-in-to-azure"></a>Přihlaste se k Azure.
Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

[Nástroj pro vyrovnávání zatížení](../load-balancer/load-balancer-overview.md) Azure distribuuje příchozí provoz mezi instance virtuálních počítačů v pořádku. 

Nejdřív vytvořte pomocí portálu veřejné Standard Load Balancer. Název a veřejná IP adresa, které vytvoříte, se automaticky nakonfigurují jako front-end nástroje pro vyrovnávání zatížení.

1. Do vyhledávacího pole zadejte **Nástroj pro vyrovnávání zatížení**. V části **Marketplace** ve výsledcích hledání zvolte **Nástroj pro vyrovnávání zatížení**.
1. Na kartě **základy** na stránce **vytvořit nástroj pro vyrovnávání zatížení** zadejte nebo vyberte následující informace:

    | Nastavení                 | Hodnota   |
    | ---| ---|
    | Předplatné  | Vyberte své předplatné.    |    
    | Skupina prostředků | Vyberte **vytvořit nový** a do textového pole zadejte *myVMSSResourceGroup* .|
    | Name           | *myLoadBalancer*         |
    | Oblast         | Vyberte **USA – východ**.       |
    | Typ          | Vyberte **Veřejný**.       |
    | SKU           | Vyberte **Standard**.       |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. |
    | Název veřejné IP adresy  | *myPip*   |
    | Přiřazení| Static |
    | Zóna dostupnosti | Vyberte **zóna – redundantní**. |

1. Až budete hotovi, vyberte **zkontrolovat + vytvořit** . 
1. Jakmile ověření projde, vyberte **vytvořit**. 

![Vytvoření nástroje pro vyrovnávání zatížení](./media/virtual-machine-scale-sets-create-portal/load-balancer.png)

## <a name="create-virtual-machine-scale-set"></a>Vytvoření škálovací sady virtuálních počítačů
Škálovací sadu můžete nasadit s použitím image Windows Serveru nebo image Linuxu, jako je například RHEL, CentOS, Ubuntu nebo SLES.

1. Do vyhledávacího pole zadejte **Scale set** . Ve výsledcích klikněte v části **Marketplace** na možnost **Virtual Machine Scale Sets**. Na stránce **sady škálování virtuálních počítačů** vyberte **vytvořit** a otevře se stránka **vytvořit sadu škálování virtuálního počítače** . 
1. Na kartě **základy** v části **Project Details (podrobnosti projektu**) Zkontrolujte, zda je vybráno správné předplatné a vyberte *myVMSSResourceGroup* ze seznamu skupin prostředků. 
1. Jako název sady škálování zadejte *myScaleSet* .
1. V **oblasti oblast** vyberte oblast, která je blízko vaší oblasti.
1. Vyberte obrázek Marketplace pro **Obrázek**. V tomto příkladu jsme zvolili *Ubuntu Server 18,04 LTS*.
1. Zadejte požadované uživatelské jméno a vyberte upřednostňovaný typ ověřování.
   - **Heslo** musí mít alespoň 12 znaků a musí splňovat tři ze čtyř bezpečnostních požadavků: jedno malé písmeno, jedno velké písmeno, jedna číslice a jeden speciální znak. Další informace najdete v [požadavcích na uživatelské jméno a heslo](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm).
   - Pokud vyberete image disku s operačním systémem Linux, můžete místo toho zvolit **Veřejný klíč SSH**. Stačí zadat veřejný klíč, například *~/.ssh/id_rsa.pub*. K [vytvoření a použití klíčů SSH](../virtual-machines/linux/mac-create-ssh-keys.md) můžete použít Azure Cloud Shell na portálu.
   
    :::image type="content" source="./media/virtual-machine-scale-sets-create-portal/quick-create-scale-set.png" alt-text="Image zobrazuje možnosti vytváření pro sady škálování v Azure Portal.":::

1. Kliknutím na tlačítko **Další** přesuňte ostatní stránky. 
1. Ponechte výchozí nastavení stránky **instance** a **disky** .
1. Na stránce **sítě** v části **Vyrovnávání zatížení** vyberte **Ano** , pokud chcete umístit instance sady škálování za nástroj pro vyrovnávání zatížení. 
1. V **možnostech vyrovnávání zatížení** vyberte **Azure Load Balancer**.
1. V **Vyberte nástroj pro vyrovnávání zatížení** vyberte *myLoadBalancer* , které jste vytvořili dříve.
1. V případě **Vyberte fond back-end** vyberte **vytvořit nový**, zadejte *myBackendPool* a pak vyberte **vytvořit**.
1. Až budete hotovi, vyberte **zkontrolovat + vytvořit**. 
1. Po úspěšném ověření vyberte **vytvořit** a nasaďte sadu škálování.


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už je nepotřebujete, odstraňte skupinu prostředků, škálovací sadu a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků pro sadu škálování a pak vyberete **Odstranit**.


## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste vytvořili základní škálovací sadu na webu Azure Portal. Další informace najdete v kurzu věnovaném vytváření a správě škálovacích sad virtuálních počítačů Azure.

> [!div class="nextstepaction"]
> [Vytváření a správa škálovacích sad virtuálních počítačů Azure](tutorial-create-and-manage-powershell.md)
