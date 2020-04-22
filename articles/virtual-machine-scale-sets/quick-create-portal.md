---
title: Úvodní příručka – vytvoření škálovací sady virtuálních strojů na webu Azure Portal
description: S nasazením můžete začít tím, že se naučíte, jak rychle vytvořit škálování virtuálního počítače na webu Azure Portal.
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: quickstart
ms.custom: mvc, H1Hack27Feb2017
ms.date: 10/23/2019
ms.author: jushiman
ms.openlocfilehash: 5855c53a73ac94985f34f229fbe5ab77067ef17d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680419"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Rychlý start: Vytvoření škálovací sady virtuálních počítačů na webu Azure Portal

Škálovací sada virtuálních počítačů umožňuje nasadit a spravovat sadu identických virtuálních počítačů s automatickým škálováním. Počet virtuálních počítačů ve škálovací sadě můžete škálovat ručně nebo můžete definovat pravidla pro automatické škálování podle využití prostředků, například podle požadavků na CPU a paměť nebo podle provozu. Nástroj pro vyrovnávání zatížení Azure pak bude distribuovat provoz do instancí virtuálních počítačů ve škálovací sadě. V tomto rychlém startu vytvoříte škálovací sadu virtuálních počítačů na webu Azure Portal.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.


## <a name="log-in-to-azure"></a>Přihlaste se k Azure.
Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

Nástroj [pro vyrovnávání zatížení](../load-balancer/load-balancer-overview.md) Azure distribuuje příchozí provoz mezi instancemi virtuálních strojů v pořádku. 

Nejprve vytvořte veřejný standardní vyrovnávání zatížení pomocí portálu. Název a veřejná IP adresa, kterou vytvoříte, jsou automaticky konfigurovány jako front-end vykladače zatížení.

1. Do vyhledávacího pole zadejte **nástrojů pro vyrovnávání zatížení**. V části **Marketplace** ve výsledcích hledání vyberte **Balancer načítání**.
1. Na kartě **Základy** na stránce **Vytvořit vyrovnávání zatížení** zadejte nebo vyberte následující informace:

    | Nastavení                 | Hodnota   |
    | ---| ---|
    | Předplatné  | Vyberte své předplatné.    |    
    | Skupina prostředků | Vyberte **Vytvořit nový** a do textového pole zadejte *myVMSSResourceGroup.*|
    | Název           | *myLoadBalancer*         |
    | Region (Oblast)         | Vyberte **USA – východ**.       |
    | Typ          | Vyberte **Možnost Veřejné**.       |
    | Skladová jednotka (SKU)           | Vyberte **standardní**.       |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. |
    | Název veřejné IP adresy  | *MyPip*   |
    | Přiřazení| Statická |

1. Až budete hotovi, vyberte **Zkontrolovat + vytvořit** 
1. Po uplynutí ověření vyberte **Vytvořit**. 

![Vytvoření nástroje pro vyrovnávání zatížení](./media/virtual-machine-scale-sets-create-portal/load-balancer.png)

## <a name="create-virtual-machine-scale-set"></a>Vytvoření škálovací sady virtuálních počítačů
Škálovací sadu můžete nasadit s použitím image Windows Serveru nebo image Linuxu, jako je například RHEL, CentOS, Ubuntu nebo SLES.

1. Do vyhledávacího pole **zadejte sadu měřítka.** Ve výsledcích vyberte v části **Marketplace** **škálovací sady virtuálních strojů**. Otevře se stránka Vytvořit **nastavení měřítka virtuálního počítače.** 
1. Přesvědčte se, že je na kartě **Základy** v části **Podrobnosti o projektu** vybrané správné předplatné a pak ve Skupině prostředků vyberte **Vytvořit nový**. Zadejte *název myVMSSResourceGroup* a pak vyberte **OK** . 
1. Jako název škálovací sady zadejte *myScaleSet.*
1. V **oblasti**vyberte oblast, která se nachází v blízkosti vaší oblasti.
1. Ponechte výchozí hodnotu **virtuálních virtuálních montovek ScaleSet** pro **Orchestrator**.
1. Vyberte obrázek tržiště pro **obrázek**. V tomto příkladu jsme zvolili *Ubuntu Server 18.04 LTS*.
1. Zadejte požadované uživatelské jméno a vyberte upřednostňovaný typ ověřování.
   - **Heslo** musí mít alespoň 12 znaků a musí splňovat tři ze čtyř bezpečnostních požadavků: jedno malé písmeno, jedno velké písmeno, jedna číslice a jeden speciální znak. Další informace najdete v [požadavcích na uživatelské jméno a heslo](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm).
   - Pokud vyberete image disku s operačním systémem Linux, můžete místo toho zvolit **Veřejný klíč SSH**. Stačí zadat veřejný klíč, například *~/.ssh/id_rsa.pub*. K [vytvoření a použití klíčů SSH](../virtual-machines/linux/mac-create-ssh-keys.md) můžete použít Azure Cloud Shell na portálu.
   
    ![Vytvoření škálovací sady virtuálních počítačů](./media/virtual-machine-scale-sets-create-portal/quick-create-scaleset.png)

1. Chcete-li přesunout ostatní stránky, vyberte **možnost Další.** 
1. Ponechte výchozí hodnoty pro stránky **Instance** a **Disky.**
1. Na stránce **Síť** v části **Vyrovnávání zatížení**vyberte **Ano,** chcete-li instance škálovací sady umístit za nástroje pro vyrovnávání zatížení. 
1. V **možnostech vyrovnávání zatížení**vyberte **Azure balancer**.
1. V **části Vyberte vyvažovač zatížení**vyberte *myLoadBalancer,* který jste vytvořili dříve.
1. V **části Vybrat back-endový fond**vyberte Vytvořit **nový**, zadejte *myBackendPool*a pak vyberte **Vytvořit**.
1. Až budete hotovi, vyberte **Zkontrolovat + vytvořit**. 
1. Po uplynutí ověření vyberte **Vytvořit** a nasaďte škálovací sadu.


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už je nepotřebujete, odstraňte skupinu prostředků, škálovací sadu a všechny související prostředky. Chcete-li tak učinit, vyberte skupinu prostředků pro škálovací sadu a pak vyberte **Odstranit**.


## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste vytvořili základní škálovací sadu na webu Azure Portal. Další informace najdete v kurzu věnovaném vytváření a správě škálovacích sad virtuálních počítačů Azure.

> [!div class="nextstepaction"]
> [Vytváření a správa škálovacích sad virtuálních počítačů Azure](tutorial-create-and-manage-powershell.md)
