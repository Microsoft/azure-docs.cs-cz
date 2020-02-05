---
title: Řešení Azure VMware (AVS) – vytvoření virtuálního počítače v Azure pomocí šablon virtuálních počítačů
description: Popisuje, jak vytvořit virtuální počítače v Azure pomocí šablon virtuálních počítačů v infrastruktuře VMware pro váš privátní cloud služby AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 533aaab13f1b957e709f66b23b511fc199ee0285
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015197"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>Vytvoření virtuálního počítače v Azure pomocí šablon virtuálních počítačů v infrastruktuře VMware

Virtuální počítač můžete v Azure Portal vytvořit pomocí šablon virtuálních počítačů v infrastruktuře VMware, kterou váš správce služby AVS povolil pro vaše předplatné.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [Portálu Azure](https://portal.azure.com).

## <a name="create-avs-virtual-machine"></a>Vytvořit virtuální počítač AVS

1. Vyberte **Všechny služby**.

2. Hledání služby **AVS Virtual Machines**.

3. Klikněte na tlačítko **Add** (Přidat).

    ![Vytvořit virtuální počítač AVS](media/create-cloudsimple-virtual-machine.png)

4. Zadejte základní informace klikněte na **Další: velikost**.

    > [!NOTE]
    > Vytvoření virtuálního počítače se službou AVS v Azure vyžaduje šablonu virtuálního počítače. Tato šablona virtuálního počítače by měla existovat ve vašem privátním cloudu vCenter. Vytvořte virtuální počítač v privátním cloudu z uživatelského rozhraní vCenter s požadovaným operačním systémem a konfiguracemi. Pomocí pokynů v části [klonování virtuálního počítače do šablony ve webovém klientovi vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html)vytvořte šablonu.

    ![Vytvořit virtuální počítač AVS – základní informace](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Pole | Popis |
    | ------------ | ------------- |
    | Předplatné | Předplatné Azure přidružené k vašemu privátnímu cloudu.  |
    | Skupina prostředků | Skupina prostředků, ke které se virtuální počítač přiřadí. Můžete vybrat existující skupinu nebo vytvořit novou. |
    | Name (Název) | Název, který identifikuje virtuální počítač.  |
    | Umístění | Oblast Azure, ve které je tento virtuální počítač hostovaný.  |
    | Privátní cloud | Privátní cloud pro funkci AVS, kde chcete vytvořit virtuální počítač. |
    | Fond zdrojů | Namapovaný fond zdrojů pro virtuální počítač. Vyberte z dostupných fondů zdrojů. |
    | Šablona vSphere | Šablona vSphere pro virtuální počítač  |
    | Uživatelské jméno | Uživatelské jméno správce virtuálního počítače (pro šablony systému Windows)|
    | Heslo <br>Potvrzení hesla | Heslo správce virtuálního počítače (pro šablony systému Windows).  |

5. Vyberte počet jader a kapacitu paměti pro virtuální počítač a klikněte na **Další: Konfigurace**. Zaškrtněte políčko, pokud chcete vystavit úplnou virtualizaci CPU hostovanému operačnímu systému, aby aplikace, které vyžadují virtualizaci hardwaru, mohly běžet na virtuálních počítačích bez binárního překladu nebo vlastnostmi paravirtualizace. Další informace najdete v článku o VMware [vystavování virtualizace hardwaru VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html).

    ![Vytvořit virtuální počítač AVS – velikost](media/create-cloudsimple-virtual-machine-size.png)

6. Nakonfigurujte síťová rozhraní a disky, jak je popsáno v následujících tabulkách, a klikněte na tlačítko **zkontrolovat + vytvořit**.

    ![Vytvořit virtuální počítač AVS – konfigurace](media/create-cloudsimple-virtual-machine-configurations.png)

    Pro síťová rozhraní klikněte na **Přidat síťové rozhraní** a nakonfigurujte následující nastavení.

    | Řízení | Popis |
    | ------------ | ------------- |
    | Name (Název) | Zadejte název pro identifikaci rozhraní.  |
    | Network (Síť) | V vSphere privátního cloudu vyberte ze seznamu nakonfigurované distribuované skupiny portů.  |
    | Adaptér | Vyberte adaptér vSphere ze seznamu dostupných typů nakonfigurovaných pro virtuální počítač. Další informace najdete v článku znalostní báze VMware, který [vybírá síťový adaptér pro virtuální počítač](https://kb.vmware.com/s/article/1001805). |
    | Zapnout při spuštění | Určete, jestli se má při spuštění virtuálního počítače povolit hardware síťové karty. Výchozí hodnota je **Enable**. |

    V části disky klikněte na **Přidat disk** a nakonfigurujte následující nastavení.

    | Položka | Popis |
    | ------------ | ------------- |
    | Name (Název) | Zadejte název pro identifikaci disku.  |
    | Velikost | Vyberte jednu z dostupných velikostí.  |
    | Řadič SCSI | Vyberte řadič SCSI pro disk.  |
    | Mode | Určuje, jak se disk podílí na snímcích. Vyberte jednu z těchto možností: <br> -Nezávislá trvalá: všechna data zapsaná na disk se napíší trvale.<br> Nezávislé na netrvalé: změny zapsané na disk se zahodí při vypnutí nebo resetování virtuálního počítače. Nezávislý režim bez trvalého režimu umožňuje vždy restartovat virtuální počítač ve stejném stavu. Další informace najdete v [dokumentaci k VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html).

7. Po dokončení ověření zkontrolujte nastavení a klikněte na **vytvořit**. Chcete-li provést změny, klikněte na karty v horní části nebo na tlačítko.

    ![Vytvořit virtuální počítač AVS – kontrola](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-avs-virtual-machines"></a>Zobrazit seznam virtuálních počítačů AVS

1. Vyberte **Všechny služby**.

2. Hledání služby **AVS Virtual Machines**.

3. Vyberte, na jakém privátním cloudu byl vytvořen.

    ![Seznam Virtual Machines pro funkci AVS](media/list-cloudsimple-virtual-machines.png)

Seznam virtuálních počítačů služby AVS zahrnuje virtuální počítače vytvořené z Azure Portal.  V seznamu se zobrazí virtuální počítače vytvořené v privátním cloudu vCenter v namapovaném fondu zdrojů vCenter.  
