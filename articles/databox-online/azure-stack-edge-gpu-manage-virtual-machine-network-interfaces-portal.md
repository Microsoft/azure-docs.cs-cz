---
title: Jak spravovat síťová rozhraní virtuálních počítačů na Azure Stack Edge pro prostřednictvím Azure Portal
description: Naučte se Spravovat síťová rozhraní na virtuálních počítačích, které jsou nasazené v grafickém procesoru Azure Stack Edge pro pomocí Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/23/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage network interfaces on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: 84077f174fabd02afcd5171e8d365e8cbd3a52c2
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027589"
---
# <a name="use-the-azure-portal-to-manage-network-interfaces-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Pomocí Azure Portal můžete spravovat síťová rozhraní na virtuálních počítačích na procesorovém GPU Azure Stack Edge pro.

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Můžete vytvářet a spravovat virtuální počítače na Azure Stack hraničním zařízení pomocí Azure Portal, šablon, Azure PowerShell rutin a prostřednictvím skriptů Azure CLI/Python. Tento článek popisuje, jak spravovat síťová rozhraní na VIRTUÁLNÍm počítači, který běží na zařízení Azure Stack Edge pomocí Azure Portal. 

Když vytváříte virtuální počítač, zadáváte jedno virtuální síťové rozhraní, které se má vytvořit. Až se virtuální počítač vytvoří, možná budete chtít přidat jedno nebo víc síťových rozhraní. Můžete také změnit výchozí nastavení síťového rozhraní pro stávající síťové rozhraní.

Tento článek vysvětluje, jak přidat síťové rozhraní k existujícímu virtuálnímu počítači, změnit stávající nastavení, jako je typ IP adresy (statické vs. dynamické), a nakonec odebrat nebo odpojit existující rozhraní. 

        
## <a name="about-network-interfaces-on-vms"></a>O síťových rozhraních na virtuálních počítačích

Síťové rozhraní umožňuje virtuálnímu počítači, který běží na zařízení Azure Stack Edge pro, komunikovat s Azure a místními prostředky. Když na svém zařízení povolíte port pro výpočetní síť, vytvoří se na tomto síťovém rozhraní virtuální přepínač. Tento virtuální přepínač se pak použije k nasazení výpočetních úloh, jako jsou virtuální počítače nebo aplikace s využitím kontejnerů na vašem zařízení. 

Vaše zařízení podporuje jenom jeden virtuální přepínač, ale několik virtuálních síťových rozhraní. Každé síťové rozhraní na vašem VIRTUÁLNÍm počítači má přiřazenou statickou nebo dynamickou IP adresu. V případě IP adres přiřazených k několika síťovým rozhraním na VIRTUÁLNÍm počítači jsou na vašem VIRTUÁLNÍm počítači povolené určité možnosti. Virtuální počítač může například hostovat více webů nebo služeb s různými IP adresami a certifikáty SSL na jednom serveru. Virtuální počítač v zařízení může sloužit jako síťové virtuální zařízení, jako je brána firewall nebo nástroj pro vyrovnávání zatížení. <!--Is it possible to do that on ASE?-->

<!--There is a limit to how many virtual network interfaces can be created on the virtual switch on your device. See the Azure Stack Edge Pro limits article for details.--> 


## <a name="prerequisites"></a>Požadavky

Než začnete spravovat virtuální počítače na svém zařízení prostřednictvím Azure Portal, ujistěte se, že:

1. Povolili jste síťové rozhraní pro výpočty na svém zařízení. Tato akce vytvoří na VIRTUÁLNÍm počítači virtuální přepínač na tomto síťovém rozhraní. 
    1. V místním uživatelském rozhraní zařízení, pokračujte na **COMPUTE**. Vyberte síťové rozhraní, které budete používat k vytvoření virtuálního přepínače.

        > [!IMPORTANT] 
        > Pro výpočetní výkon můžete nakonfigurovat jenom jeden port.

    1. Povolte výpočetní prostředky v síťovém rozhraní. Grafický procesor Azure Stack Edge pro vytváří a spravuje virtuální přepínač odpovídající tomuto síťovému rozhraní.

1. V zařízení máte nasazený aspoň jeden virtuální počítač. Pokud chcete vytvořit tento virtuální počítač, přečtěte si pokyny v tématu [nasazení virtuálního počítače na Azure Stack Edge pro prostřednictvím Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).

1. Váš virtuální počítač by měl být v **zastaveném** stavu. Pokud chcete virtuální počítač zastavit, v části **virtuální počítače > přehled** a vyberte virtuální počítač, který chcete zastavit. Na stránce vlastností virtuálního počítače vyberte **zastavit** a po zobrazení výzvy k potvrzení vyberte **Ano** . Před přidáním, úpravou nebo odstraněním síťových rozhraní musíte virtuální počítač zastavit.

    ![Stránka pro zastavení virtuálního počítače z vlastností virtuálního počítače](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="add-a-network-interface"></a>Přidat síťové rozhraní

Pomocí těchto kroků přidáte síťové rozhraní k virtuálnímu počítači nasazenému v zařízení. 

1. Přejít na virtuální počítač, který jste zastavili, a pak na stránku **vlastností virtuálního počítače** . Vyberte **Sítě**.
    
    ![Stránka Vybrat síť na vlastnosti virtuálního počítače](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-1.png)

2. V okně **sítě** klikněte na panelu příkazů na **+ Přidat síťové rozhraní**.

    ![Vyberte Přidat síťové rozhraní.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-2.png)

3. V okně **Přidat síťové rozhraní** zadejte následující parametry:

    
    |Sloupec1  |Sloupec2  |
    |---------|---------|
    |Name     | Jedinečný název v rámci skupiny prostředků. Po vytvoření síťového rozhraní se název nedá změnit. Chcete-li snadno spravovat více síťových rozhraní, použijte návrhy uvedené v [zásadách vytváření názvů](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming).     |
    |Virtuální síť| Virtuální síť přidružená k virtuálnímu přepínači vytvořenému v zařízení, pokud jste povolili výpočty v síťovém rozhraní. K vašemu zařízení je přidružená jenom jedna virtuální síť.         |         
    |Podsíť     | Podsíť v rámci vybrané virtuální sítě. Toto pole se vyplní automaticky podsítí přidruženou k síťovému rozhraní, na kterém jste povolili výpočetní výkon.         |       
    |Přiřazení IP adresy   | Statická nebo dynamická IP adresa pro vaše síťové rozhraní. Statická IP adresa by měla být dostupná, volná IP adresa ze zadaného rozsahu podsítě. Pokud v prostředí existuje server DHCP, vyberte možnost dynamická.        | 

    ![Okno Přidat síťové rozhraní](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-3.png)

4. Zobrazí se oznámení, že probíhá vytváření síťového rozhraní.

    ![Oznámení, když se vytvoří síťové rozhraní](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-4.png)

5.  Po úspěšném vytvoření síťového rozhraní se seznam síťových rozhraní aktualizuje, aby se zobrazilo nově vytvořené rozhraní.

    ![Aktualizovaný seznam síťových rozhraní](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-5.png)


## <a name="edit-a-network-interface"></a>Úprava síťového rozhraní

Pomocí těchto kroků upravíte síťové rozhraní přidružené k virtuálnímu počítači nasazenému na vašem zařízení.

1. Přejít na virtuální počítač, který jste zastavili, a přejít na stránku **vlastností virtuálního počítače** . Vyberte **Sítě**.

1. V seznamu síťových rozhraní vyberte rozhraní, které chcete upravit. V pravém rohu vybraného síťového rozhraní vyberte ikonu pro úpravy (tužka).  

    ![Vyberte síťové rozhraní, které chcete upravit.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-1.png)

1. V okně **Upravit síťové rozhraní** můžete změnit jenom přiřazení IP adresy síťového rozhraní. Název, virtuální síť a podsíť, které jsou přidružené k síťovému rozhraní, se po vytvoření nedají změnit. Změňte **přiřazení IP adresy** na statické a uložte změny.

    ![Změnit přiřazení IP pro síťové rozhraní](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-2.png)

1. Seznam síťových rozhraní se aktualizuje a zobrazí se aktualizované síťové rozhraní.


## <a name="detach-a-network-interface"></a>Odpojení síťového rozhraní

Pomocí těchto kroků odpojíte nebo odeberete síťové rozhraní přidružené k virtuálnímu počítači nasazenému na vašem zařízení.

1. Přejít na virtuální počítač, který jste zastavili, a přejít na stránku **vlastností virtuálního počítače** . Vyberte **Sítě**.

1. V seznamu síťových rozhraní vyberte rozhraní, které chcete upravit. V pravém rohu vybraného síťového rozhraní vyberte ikonu odpojit (odpojte).  

    ![Vyberte síťové rozhraní, které se má odpojit.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/detach-nic-1.png)

1. Po úplném odpojení rozhraní se seznam síťových rozhraní aktualizuje, aby se zobrazila zbývající rozhraní.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak nasadit virtuální počítače na zařízení Azure Stack Edge pro, najdete v tématu [nasazení virtuálních počítačů prostřednictvím Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
