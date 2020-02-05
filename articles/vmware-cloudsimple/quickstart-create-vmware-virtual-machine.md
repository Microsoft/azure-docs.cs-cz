---
title: Rychlé zprovoznění řešení Azure VMware (AVS) – vytvoření virtuálních počítačů VMware v Azure
description: Naučte se vytvářet a konfigurovat virtuální počítače VMware z Azure Portal pomocí řešení Azure VMware (AVS).
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 575d59d60ebfcfdbe4d234d608e8d988006773c2
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019549"
---
# <a name="quickstart---create-vmware-vms-on-azure"></a>Rychlý Start – vytvoření virtuálních počítačů VMware v Azure

Pokud chcete vytvořit virtuální počítač v Azure Portal, použijte šablony virtuálních počítačů, které váš správce služby AVS povolil pro vaše předplatné. Šablony virtuálních počítačů najdete v infrastruktuře VMware.

## <a name="avs-vm-creation-on-azure-requires-a-vm-template"></a>Vytvoření virtuálního počítače služby AVS v Azure vyžaduje šablonu virtuálního počítače.

Vytvořte virtuální počítač v privátním cloudu služby AVS z uživatelského rozhraní vCenter. Pokud chcete vytvořit šablonu, postupujte podle pokynů v části [klonování virtuálního počítače do šablony ve webovém klientovi vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Uložte šablonu virtuálního počítače na server služby AVS Private Cloud vCenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Vytvořit virtuální počítač v Azure Portal

1. Vyberte **Všechny služby**.

2. Hledání služby **AVS Virtual Machines**.

3. Klikněte na tlačítko **Add** (Přidat).

    ![Vytvořit virtuální počítač AVS](media/create-cloudsimple-virtual-machine.png)

4. Zadejte základní informace a klikněte na **Další: velikost**.

    ![Vytvořit virtuální počítač AVS – základní informace](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Pole | Popis |
    | ------------ | ------------- |
    | Předplatné | Předplatné Azure přidružené k vašemu privátnímu cloudu služby AVS |
    | Skupina prostředků | Skupina prostředků, ke které se virtuální počítač přiřadí. Můžete vybrat existující skupinu nebo vytvořit novou. |
    | Name (Název) | Název, který identifikuje virtuální počítač.  |
    | Umístění | Oblast Azure, ve které je tento virtuální počítač hostovaný.  |
    | Privátní cloud AVS | Privátní cloud pro funkci AVS, kde chcete vytvořit virtuální počítač. |
    | Fond zdrojů | Namapovaný fond zdrojů pro virtuální počítač. Vyberte z dostupných fondů zdrojů. |
    | Šablona vSphere | Šablona vSphere pro virtuální počítač  |
    | Uživatelské jméno | Uživatelské jméno správce virtuálního počítače (pro šablony systému Windows).|
    | Heslo |  Heslo správce virtuálního počítače (pro šablony systému Windows). |
    | Potvrzení hesla | Potvrďte heslo. |

5. Vyberte počet jader a kapacitu paměti pro virtuální počítač a klikněte na **Další: Konfigurace**. Zaškrtněte políčko, pokud chcete vystavit úplnou virtualizaci procesoru hostovanému operačnímu systému. Aplikace, které vyžadují virtualizaci hardwaru, můžou běžet na virtuálních počítačích bez binárního překladu nebo vlastnostmi paravirtualizace. Další informace najdete v článku o VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">vystavování virtualizace hardwaru VMware</a>.

    ![Vytvořit virtuální počítač AVS – velikost](media/create-cloudsimple-virtual-machine-size.png)

6. Nakonfigurujte síťová rozhraní a disky, jak je popsáno v následujících tabulkách, a klikněte na tlačítko **zkontrolovat + vytvořit**.

    ![Vytvořit virtuální počítač AVS – konfigurace](media/create-cloudsimple-virtual-machine-configurations.png)

    Pro síťová rozhraní klikněte na **Přidat síťové rozhraní** a nakonfigurujte následující nastavení.

    | Řízení | Popis |
    | ------------ | ------------- |
    | Name (Název) | Zadejte název pro identifikaci rozhraní.  |
    | Network (Síť) | V vSphere privátního cloudu pro funkci AVS vyberte ze seznamu nakonfigurovanou distribuovanou skupinu portů. |
    | Adaptér | Vyberte adaptér vSphere ze seznamu dostupných typů nakonfigurovaných pro virtuální počítač. Další informace najdete v článku znalostní báze VMware, který <a href="https://kb.vmware.com/s/article/1001805" target="_blank">vybírá síťový adaptér pro virtuální počítač</a>. |
    | Zapnout při spuštění | Určete, jestli se má při spuštění virtuálního počítače povolit hardware síťové karty. Výchozí hodnota je **Enable**. |

    V části disky klikněte na **Přidat disk** a nakonfigurujte následující nastavení.

    | Položka | Popis |
    | ------------ | ------------- |
    | Name (Název) | Zadejte název pro identifikaci disku. |
    | Velikost | Vyberte jednu z dostupných velikostí. |
    | Řadič SCSI | Vyberte řadič SCSI pro disk. |
    | Mode | Určuje, jak se disk podílí na snímcích. Vyberte jednu z těchto možností: <br> -Nezávislá trvalá: všechna data zapsaná na disk se napíší trvale.<br> Nezávislé na netrvalé: změny zapsané na disk se zahodí při vypnutí nebo resetování virtuálního počítače. Nezávislý režim bez trvalého režimu umožňuje vždy restartovat virtuální počítač ve stejném stavu. Další informace najdete v <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">dokumentaci k VMware</a>.

7. Po dokončení ověření zkontrolujte nastavení a klikněte na **vytvořit**. Chcete-li provést změny, klikněte na karty v horní části nebo na tlačítko.

    ![Vytvořit virtuální počítač AVS – kontrola](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Další kroky

* [Zobrazit seznam virtuálních počítačů AVS](azure-create-vm.md#view-list-of-avs-virtual-machines)
* [Správa virtuálního počítače se službou AVS z Azure](azure-manage-vm.md)
