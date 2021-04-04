---
title: Řešení Azure VMware podle CloudSimple – vytvoření virtuálního počítače v Azure pomocí šablon virtuálních počítačů
description: Popisuje, jak vytvořit virtuální počítače v Azure pomocí šablon virtuálních počítačů v infrastruktuře VMware pro privátní cloud CloudSimple.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b01afe60a78a746eb0dc5f03cc7b45989f8cf81e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97898755"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>Vytvoření virtuálního počítače v Azure pomocí šablon virtuálních počítačů v infrastruktuře VMware

Virtuální počítač můžete v Azure Portal vytvořit pomocí šablon virtuálních počítačů v infrastruktuře VMware, kterou správce CloudSimple povolil pro vaše předplatné.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="create-cloudsimple-virtual-machine"></a>Vytvořit virtuální počítač s CloudSimple

1. Vyberte **Všechny služby**.

2. Vyhledejte **CloudSimple Virtual Machines**.

3. Klikněte na **Přidat**.

    ![Vytvořit virtuální počítač s CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Zadejte základní informace klikněte na **Další: velikost**.

    > [!NOTE]
    > Vytváření virtuálních počítačů s CloudSimple v Azure vyžaduje šablonu virtuálního počítače.  Tato šablona virtuálního počítače by měla existovat ve vašem privátním cloudu vCenter.  Vytvořte virtuální počítač v privátním cloudu z uživatelského rozhraní vCenter s požadovaným operačním systémem a konfiguracemi.  Pomocí pokynů v části [klonování virtuálního počítače do šablony ve webovém klientovi vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html)vytvořte šablonu.

    ![Vytvořit virtuální počítač s CloudSimple – základní informace](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Pole | Popis |
    | ------------ | ------------- |
    | Předplatné | Předplatné Azure přidružené k vašemu privátnímu cloudu.  |
    | Skupina prostředků | Skupina prostředků, ke které se virtuální počítač přiřadí. Můžete vybrat existující skupinu nebo vytvořit novou. |
    | Name | Název, který identifikuje virtuální počítač.  |
    | Umístění | Oblast Azure, ve které je tento virtuální počítač hostovaný.  |
    | Privátní cloud | CloudSimple privátní cloud, ve kterém chcete vytvořit virtuální počítač. |
    | Fond zdrojů | Namapovaný fond zdrojů pro virtuální počítač. Vyberte z dostupných fondů zdrojů. |
    | Šablona vSphere | Šablona vSphere pro virtuální počítač  |
    | Uživatelské jméno | Uživatelské jméno správce virtuálního počítače (pro šablony systému Windows)|
    | Heslo <br>Potvrzení hesla | Heslo správce virtuálního počítače (pro šablony systému Windows).  |

5. Vyberte počet jader a kapacitu paměti pro virtuální počítač a klikněte na **Další: Konfigurace**. Zaškrtněte políčko, pokud chcete vystavit úplnou virtualizaci CPU hostovanému operačnímu systému, aby aplikace, které vyžadují virtualizaci hardwaru, mohly běžet na virtuálních počítačích bez binárního překladu nebo vlastnostmi paravirtualizace. Další informace najdete v článku o VMware [vystavování virtualizace hardwaru VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html).

    ![Vytvořit virtuální počítač s CloudSimple – velikost](media/create-cloudsimple-virtual-machine-size.png)

6. Nakonfigurujte síťová rozhraní a disky, jak je popsáno v následujících tabulkách, a klikněte na tlačítko **zkontrolovat + vytvořit**.

    ![Vytvoření virtuálního počítače s CloudSimple – konfigurace](media/create-cloudsimple-virtual-machine-configurations.png)

    Pro síťová rozhraní klikněte na **Přidat síťové rozhraní** a nakonfigurujte následující nastavení.

    | Řízení | Popis |
    | ------------ | ------------- |
    | Název | Zadejte název pro identifikaci rozhraní.  |
    | Síť | V vSphere privátního cloudu vyberte ze seznamu nakonfigurované distribuované skupiny portů.  |
    | Adaptér | Vyberte adaptér vSphere ze seznamu dostupných typů nakonfigurovaných pro virtuální počítač. Další informace najdete v článku znalostní báze VMware, který [vybírá síťový adaptér pro virtuální počítač](https://kb.vmware.com/s/article/1001805). |
    | Zapnout při spuštění | Určete, jestli se má při spuštění virtuálního počítače povolit hardware síťové karty. Výchozí hodnota je **Enable**. |

    V části disky klikněte na **Přidat disk** a nakonfigurujte následující nastavení.

    | Položka | Popis |
    | ------------ | ------------- |
    | Název | Zadejte název pro identifikaci disku.  |
    | Velikost | Vyberte jednu z dostupných velikostí.  |
    | Řadič SCSI | Vyberte řadič SCSI pro disk.  |
    | Režim | Určuje, jak se disk podílí na snímcích. Vyberte jednu z těchto možností: <br> -Nezávislá trvalá: všechna data zapsaná na disk se napíší trvale.<br> Nezávislé na netrvalé: změny zapsané na disk se zahodí při vypnutí nebo resetování virtuálního počítače.  Nezávislý režim bez trvalého režimu umožňuje vždy restartovat virtuální počítač ve stejném stavu. Další informace najdete v [dokumentaci k VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html).

7. Po dokončení ověření zkontrolujte nastavení a klikněte na **vytvořit**. Chcete-li provést změny, klikněte na karty v horní části nebo na tlačítko.

    ![Vytvoření virtuálního počítače s CloudSimple – kontrola](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>Zobrazit seznam virtuálních počítačů s CloudSimple

1. Vyberte **Všechny služby**.

2. Vyhledejte **CloudSimple Virtual Machines**.

3. Vyberte, na jakém privátním cloudu byl vytvořen.

    ![Seznam Virtual Machines CloudSimple](media/list-cloudsimple-virtual-machines.png)

Seznam virtuálních počítačů s CloudSimple obsahuje virtuální počítače vytvořené z Azure Portal.  V seznamu se zobrazí virtuální počítače vytvořené v privátním cloudu vCenter v namapovaném fondu zdrojů vCenter.  
