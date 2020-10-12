---
title: Konfigurace privátních IP adres pro virtuální počítače – Azure Portal
description: Přečtěte si, jak nakonfigurovat privátní IP adresy pro virtuální počítače pomocí Azure Portal.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: c8fdba59a8d31c064745c7a1904204359b386a7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84707850"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>Konfigurace privátní IP adresy pro virtuální počítač pomocí Azure Portal

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Následující vzorový postup očekává, že se už nevytvoří jednoduché prostředí. Chcete-li spustit postup, který je zobrazen v tomto dokumentu, nejprve [vytvořte virtuální síť](quick-create-portal.md#create-a-virtual-network). V kroku 3 však místo toho použijte tyto hodnoty:

| Nastavení | Hodnota |
| ------- | ----- |
| Název | *TestVNet* |
| Adresní prostor | *192.168.0.0/16* |
| Skupina prostředků | **TestRG** (Pokud je to nutné, vyberte **vytvořit nové** a vytvořte ho.) |
| Název podsítě | *FrontEnd* |
| Podsíť – Rozsah adres | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>Vytvoření virtuálního počítače pro testování statických privátních IP adres
Když vytvoříte virtuální počítač v režimu nasazení Správce prostředků, nemůžete nastavit statickou privátní IP adresu pomocí Azure Portal. Místo toho nejprve vytvoříte virtuální počítač. Pak můžete nastavit privátní IP adresu jako statickou.

Pokud chcete vytvořit virtuální počítač s názvem *DNS01* v podsíti *front-endu* virtuální sítě s názvem *TestVNet*, postupujte takto:

1. V nabídce [Azure Portal](https://portal.azure.com) vyberte **vytvořit prostředek**.

    ![Vytvořte prostředek Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. Vyberte **výpočetní**  >  **virtuální počítač**.

    ![Vytvoření virtuálního počítače, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. V části **základy**zadejte hodnoty pro položky, jak je popsáno v následující tabulce. Pak vyberte **Další &nbsp; : &nbsp; disky** a pak **Další &nbsp; : &nbsp; sítě**.

    | Položka | Hodnota |
    | --- | --- |
    | **Předplatné** | Vaše aktuální předplatné |
    | **Skupina prostředků** | **TestRG** (výběr z rozevíracího seznamu) |
    | **Název virtuálního počítače** | *DNS01* |
    | **Oblast** | **(USA) USA – východ** |
    | **Image** | **Windows Server 2019 Datacenter** |
    | **Velikost** | **Velikost virtuálního počítače** **B1ls**, **Nabídka** **Standard** |
    | **Uživatelské jméno** | Uživatelské jméno účtu správce |
    | **Heslo** | Heslo pro uživatelské jméno účtu správce |
    | **Potvrzení hesla** | Znovu heslo |

    ![Karta základy, vytvořit virtuální počítač, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. V části **sítě**zadejte hodnoty pro položky, jak je popsáno v následující tabulce, a pak vyberte **Další**.

    | Položka | Hodnota |
    | --- | --- |
    | **Virtuální síť** | **TestVNet** |
    | **Podsíť** | **FrontEnd** |

    ![Karta sítě, vytvořte virtuální počítač Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. V části **Správa**vyberte v části **účet úložiště diagnostiky**možnost **vnetstorage**. Pokud se tento účet úložiště v seznamu nezobrazuje, vyberte **vytvořit nový**, zadejte **název** *vnetstorage*a vyberte **OK**. Nakonec vyberte **zkontrolovat &nbsp; + &nbsp; vytvořit**.

    ![Karta Správa, vytvořit virtuální počítač, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. V části **Revize + vytvořit**zkontrolujte informace o přehledu a potom vyberte **vytvořit**.

    ![Projděte si kartu + vytvořit, vytvořte virtuální počítač Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

Po vytvoření virtuálního počítače se zobrazí následující zpráva.

![Zpráva o dokončení nasazení, vytvořit virtuální počítač, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>Načtení informací o privátních IP adresách pro virtuální počítač
Chcete-li zobrazit informace o privátních IP adresách pro nový virtuální počítač:

1. Najděte svůj virtuální počítač tak, že přejdete na [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **virtuální počítače**.

    ![Virtuální počítače, vyhledávací pole, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. Vyberte název nového virtuálního počítače (**DNS01**).

    ![Seznam virtuálních počítačů, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. Zvolte **sítě**a vyberte jediné síťové rozhraní v seznamu.

    ![Síťové rozhraní, sítě, virtuální počítač, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. Zvolte **Konfigurace protokolu IP**a vyberte konfiguraci protokolu IP uvedenou v tabulce.

    ![Konfigurace protokolu IP, síťové rozhraní, sítě, virtuální počítač, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. V **Nastavení privátní IP adresa**v části virtuální síť nebo podsíť **TestVNet/front-endu** si poznamenejte hodnotu **přiřazení** (**dynamickou** nebo **statickou**) a **IP adresu**.

    ![Dynamické nebo statické přiřazení, staré nastavení privátních IP adres, konfigurace protokolu IP, síťové rozhraní, sítě, virtuální počítač, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>Přidání statické privátní IP adresy do existujícího virtuálního počítače
Přidání statické privátní IP adresy do nového virtuálního počítače:

1. Na stránce konfigurace protokolu IP nastavte přiřazení privátní IP adresy na **statickou**.
2. Změňte svou privátní **IP adresu** na *192.168.1.101*a pak vyberte **Uložit**.
   
    ![Dynamické nebo statické přiřazení, nové nastavení privátních IP adres, konfigurace protokolu IP, síťové rozhraní, sítě, virtuální počítač, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> Pokud si vyberete možnost **Uložit** , že je přiřazení pořád nastavené na **dynamickou**, IP adresa, kterou jste zadali, se už používá. Zkuste použít jinou IP adresu.

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Odebrání statické privátní IP adresy z virtuálního počítače
Odebrání statické privátní IP adresy z virtuálního počítače:

Na stránce konfigurace protokolu IP nastavte možnost přiřazení privátní IP adresy na **dynamickou**a pak vyberte **Uložit**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Nastavení IP adres v operačním systému

V operačním systému virtuálního počítače byste neměli *privátní* IP adresu přiřazenou k virtuálnímu počítači Azure přiřadit staticky. Pouze statické přiřazení privátní IP adresy, pokud je to nezbytné, například při [přiřazování velkého počtu IP adres k virtuálním](virtual-network-multiple-ip-addresses-portal.md)počítačům. Pokud jste ručně nastavili privátní IP adresu v operačním systému, ujistěte se, že odpovídá privátní IP adrese přiřazené [síťovému rozhraní](virtual-network-network-interface-addresses.md#change-ip-address-settings)Azure. V opačném případě můžete ztratit připojení k virtuálnímu počítači. Přečtěte si další informace o nastavení [privátních IP adres](virtual-network-network-interface-addresses.md#private) .

Nikdy byste neměli ručně přiřadit *veřejnou* IP adresu přiřazenou k virtuálnímu počítači Azure v operačním systému virtuálního počítače.

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o správě [nastavení IP adres](virtual-network-network-interface-addresses.md).
