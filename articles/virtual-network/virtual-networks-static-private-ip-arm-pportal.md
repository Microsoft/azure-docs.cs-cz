---
title: Konfigurace privátních IP adres pro virtuální počítače – portál Azure
description: Zjistěte, jak nakonfigurovat privátní IP adresy pro virtuální počítače pomocí portálu Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: 946926a8a805ec3c53ea3c57dc3eded2462f7673
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461544"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>Konfigurace privátní IP adresy pro virtuální počítač pomocí portálu Azure

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Následující ukázkové kroky očekávají jednoduché prostředí, které již bylo vytvořeno. Pokud chcete spustit kroky tak, jak jsou zobrazeny v tomto dokumentu, [nejprve vytvořte virtuální síť](quick-create-portal.md#create-a-virtual-network). V kroku 3 však použijte tyto hodnoty:

| Nastavení | Hodnota |
| ------- | ----- |
| Název | *Testovací síť* |
| Adresní prostor | *192.168.0.0/16* |
| Skupina prostředků | **TestRG** (v případě potřeby vyberte **vytvořit nový** pro jeho vytvoření) |
| Podsíť – název | *FrontEnd* |
| Podsíť – Rozsah adres | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>Vytvoření virtuálního virtuálního počítačů pro testování statických privátních IP adres
Když vytvoříte virtuální počítač v režimu nasazení Správce prostředků, nemůžete nastavit statickou privátní IP adresu pomocí portálu Azure. Místo toho nejprve vytvoříte virtuální ho. Pak můžete nastavit jeho privátní IP adresu jako statickou.

Pokud chcete vytvořit virtuální počítač s názvem *DNS01* v podsíti *front-endu* virtuální sítě s názvem *TestVNet*, postupujte takto:

1. V nabídce [Portál Azure](https://portal.azure.com) vyberte **Vytvořit prostředek**.

    ![Vytvoření prostředku, portál Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. Vyberte **Compute** > **Virtual Machine**.

    ![Vytvoření virtuálního počítače, portál Azure](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. V **části Základy**zadejte hodnoty pro položky, jak je popsáno v následující tabulce. Pak vyberte **&nbsp;Další :&nbsp;Disky** a potom Další **&nbsp;:&nbsp;Síť**.

    | Položka | Hodnota |
    | --- | --- |
    | **Předplatné** | Vaše aktuální předplatné |
    | **Skupina prostředků** | **TestRG** (výběr z rozevíracího seznamu) |
    | **Název virtuálního počítače** | *DNS01* |
    | **Oblasti** | **(USA) Východní USA** |
    | **Image** | **Windows Server 2019 Datacenter** |
    | **Velikost** | **Velikost virtuálního počítače** **B1ls**, **Nabídka** **standardních** |
    | **Username** | Uživatelské jméno účtu správce |
    | **Heslo** | Heslo pro uživatelské jméno účtu správce |
    | **Potvrdit heslo** | Heslo znovu |

    ![Karta Základy, Vytvoření virtuálního počítače, Portál Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. V **části Síť**zadejte hodnoty pro položky popsané v následující tabulce a vyberte možnost **Další**.

    | Položka | Hodnota |
    | --- | --- |
    | **Virtuální síť** | **Testovací síť** |
    | **Podsíť** | **FrontEnd** |

    ![Karta Sítě, Vytvoření virtuálního počítače, portál Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. V **části Správa**v části Účet úložiště **diagnostiky**zvolte úložiště **vnetstorage**. Pokud se tento účet úložiště v seznamu nezobrazuje, vyberte **Vytvořit nový**, zadejte **název** *úložiště vnetstorage*a vyberte **OK**. Nakonec vyberte **zkontrolovat&nbsp;+&nbsp;vytvořit**.

    ![Karta Správa, Vytvoření virtuálního počítače, portál Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. V **části Revize + vytvoření**zkontrolujte informace o přehledu a vyberte **Vytvořit**.

    ![Recenze + vytvořit kartu, Vytvořit virtuální počítač, Portál Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

Po vytvoření virtuálního počítače se zobrazí následující zpráva.

![Zpráva o dokončení nasazení, Vytvoření virtuálního počítače, portál Azure](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>Načtení informací o privátní IP adrese pro virtuální hosti
Zobrazení informací o privátní IP adrese pro váš nový virtuální počítač:

1. Přejděte na [portál Azure](https://portal.azure.com) a najděte svůj virtuální počítač. Vyhledejte a vyberte **virtuální počítače**.

    ![Virtuální počítače, Vyhledávací pole, Portál Azure](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. Vyberte název nového virtuálního počítače (**DNS01**).

    ![Seznam virtuálních strojů, portál Azure](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. Zvolte **Síť**a vyberte jediné síťové rozhraní uvedené.

    ![Síťové rozhraní, sítě, virtuální počítač, portál Azure](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. Zvolte **konfigurace IP**a vyberte konfiguraci IP uvedenou v tabulce.

    ![Konfigurace IP, Síťové rozhraní, sítě, virtuální počítač, Portál Azure](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. V **nastavení privátní IP adresy**v části Virtuální **síť/podsíť TestVNet/FrontEnd** poznamenejte si hodnotu **Přiřazení** **(dynamická** nebo **statická)** a **ADRESU IP**.

    ![Dynamické nebo statické přiřazení, stará nastavení privátní IP adresy, konfigurace IP adresy, síťové rozhraní, sítě, virtuální počítač, portál Azure](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>Přidání statické privátní IP adresy do existujícího virtuálního počítačů
Přidání statické privátní IP adresy do nového virtuálního počítače:

1. Na stránce konfigurace PROTOKOLU IP nastavte přiřazení privátní IP adresy na **Static**.
2. Změňte svou soukromou **IP adresu** na *192.168.1.101*a pak vyberte **Uložit**.
   
    ![Dynamické nebo statické přiřazení, nová nastavení privátní IP adresy, konfigurace IP adres, síťové rozhraní, síť, virtuální počítač, portál Azure](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> Pokud si po výběru **možnosti Uložit všimnete,** že přiřazení je stále nastaveno na **dynamické**, zapsaná adresa IP se již používá. Zkuste jinou IP adresu.

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Odebrání statické privátní IP adresy z virtuálního počítačů
Odebrání statické privátní IP adresy z virtuálního počítače:

Na stránce konfigurace PROTOKOLU IP nastavte přiřazení privátní IP adresy na **dynamickou**a pak vyberte **Uložit**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Nastavení ADRES IP v operačním systému

V rámci operačního systému virtuálního počítače byste neměli staticky přiřazovat *privátní* IP adresu, která je přiřazená k virtuálnímu počítači Azure. Statické přiřazení privátní IP adresy jenom v případě, že je to nezbytné, například při [přiřazování mnoha IP adres virtuálním počítačům](virtual-network-multiple-ip-addresses-portal.md). Pokud ručně nastavíte privátní IP adresu v operačním systému, ujistěte se, že odpovídá privátní IP adrese přiřazené [síťovému rozhraní](virtual-network-network-interface-addresses.md#change-ip-address-settings)Azure . V opačném případě můžete ztratit připojení k virtuálnímu virtuálnímu zařízení. Přečtěte si další informace o nastavení [privátní IP adresy.](virtual-network-network-interface-addresses.md#private)

Také byste nikdy neměli ručně přiřadit *veřejnou* IP adresu přiřazenou virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače.

## <a name="next-steps"></a>Další kroky

Informace o správě [nastavení IP adres](virtual-network-network-interface-addresses.md).
