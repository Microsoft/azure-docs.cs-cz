---
title: Řešení Azure VMware od CloudSimple – vytvoření virtuálního počítače v Azure pomocí šablon virtuálních her
description: Popisuje, jak vytvořit virtuální počítače v Azure pomocí šablon virtuálních počítačů na infrastruktuře VMware pro váš CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ee3029de9826aee17dc76d0e69f08b3c1068423b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244690"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>Vytvoření virtuálního počítače v Azure pomocí šablon virtuálních zařízení na infrastruktuře VMware

Virtuální počítač můžete vytvořit na webu Azure Portal pomocí šablon virtuálních účtů na infrastruktuře VMware, kterou váš správce CloudSimple povolil pro vaše předplatné.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [portálu Azure](https://portal.azure.com).

## <a name="create-cloudsimple-virtual-machine"></a>Vytvoření virtuálního počítače CloudSimple

1. Vyberte **Všechny služby**.

2. Vyhledejte **cloudsimple virtuální počítače**.

3. Klikněte na **Přidat**.

    ![Vytvoření virtuálního počítače CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Zadejte základní informace, klikněte na **Další:Velikost**.

    > [!NOTE]
    > CloudSimple vytvoření virtuálního počítače v Azure vyžaduje šablonu virtuálního počítače.  Tato šablona virtuálního počítače by měla existovat ve vašem virtuálním počítači privátního cloudu.  Vytvořte virtuální počítač ve svém privátním cloudu z una vCenter s požadovaným operačním systémem a konfiguracemi.  Pomocí pokynů v části [Klonování virtuálního počítače k šabloně ve webovém klientovi vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html)vytvořte šablonu.

    ![Vytvoření cloudového virtuálního počítače – základy](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Pole | Popis |
    | ------------ | ------------- |
    | Předplatné | Předplatné Azure spojené s vaším privátním cloudem.  |
    | Skupina prostředků | Skupina prostředků, ke které bude virtuální medailon přiřazen. Můžete vybrat existující skupinu nebo vytvořit novou. |
    | Name (Název) | Název k identifikaci virtuálního soudu.  |
    | Umístění | Oblast Azure, ve které je tento virtuální počítač hostovaný.  |
    | Privátní cloud | CloudSimple Private Cloud, kde chcete vytvořit virtuální počítač. |
    | Fond zdrojů | Namapovaný fond prostředků pro virtuální hod. Vyberte z dostupných fondů zdrojů. |
    | Šablona vSphere | šablona vSphere pro virtuální počítače.  |
    | Uživatelské jméno | Uživatelské jméno správce virtuálního počítače (pro šablony Systému Windows)|
    | Heslo <br>Potvrzení hesla | Heslo pro správce virtuálního počítače (pro šablony systému Windows).  |

5. Vyberte počet jader a kapacitu paměti pro virtuální počítače a klepněte na **tlačítko Další:Konfigurace**. Pokud chcete zpřístupnit úplnou virtualizaci procesoru hostovanému operačnímu systému, zaškrtněte toto políčko, aby aplikace, které vyžadují virtualizaci hardwaru, mohly běžet na virtuálních počítačích bez binárního překladu nebo paravirtualizace. Další informace naleznete v článku společnosti VMware [expose VMware Hardware Assisted Virtualization](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html).

    ![Vytvořit cloudsimple virtuální počítač – velikost](media/create-cloudsimple-virtual-machine-size.png)

6. Nakonfigurujte síťová rozhraní a disky, jak je popsáno v následujících tabulkách, a klepněte na tlačítko **Revize + vytvořit**.

    ![Vytvoření virtuálního počítače CloudSimple – konfigurace](media/create-cloudsimple-virtual-machine-configurations.png)

    U síťových rozhraní klepněte na tlačítko **Přidat síťové rozhraní** a nakonfigurujte následující nastavení.

    | Řízení | Popis |
    | ------------ | ------------- |
    | Name (Název) | Zadejte název pro identifikaci rozhraní.  |
    | Network (Síť) | Vyberte ze seznamu nakonfigurovaných distribuovaných portů ve vašem privátním cloudu vSphere.  |
    | Adaptér | Vyberte adaptér vSphere ze seznamu dostupných typů nakonfigurovaných pro virtuální počítače. Další informace naleznete v článku znalostní báze VMware [Výběr síťového adaptéru pro virtuální počítač](https://kb.vmware.com/s/article/1001805). |
    | Zapnutí při startu | Zvolte, jestli chcete povolit hardware nic při spuštění virtuálního mísy. Výchozí hodnota je **Povolit**. |

    U disků klepněte na tlačítko **Přidat disk** a nakonfigurujte následující nastavení.

    | Položka | Popis |
    | ------------ | ------------- |
    | Name (Název) | Zadejte název k identifikaci disku.  |
    | Velikost | Vyberte jednu z dostupných velikostí.  |
    | Řadič SCSI | Vyberte řadič SCSI pro disk.  |
    | Mode | Určuje, jak se disk účastní snímků. Zvolte jednu z těchto možností: <br> - Nezávislé persistent: Všechna data zapsaná na disk jsou zapsána trvale.<br> - Nezávislé non-perzistentní: Změny zapsané na disk jsou zahozeny při vypnutí nebo resetování virtuálního počítače.  Nezávislý netrvalý režim umožňuje vždy restartovat virtuální počítač ve stejném stavu. Další informace naleznete v dokumentaci společnosti [VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html).

7. Po dokončení ověření zkontrolujte nastavení a klepněte na tlačítko **Vytvořit**. Chcete-li provést nějaké změny, klikněte na karty v horní části nebo klikněte na.

    ![Vytvořit cloudsimple virtuální stroj - recenze](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>Zobrazit seznam virtuálních počítačů CloudSimple

1. Vyberte **Všechny služby**.

2. Vyhledejte **cloudsimple virtuální počítače**.

3. Vyberte, na kterém byl váš privátní cloud vytvořen.

    ![Seznam cloudových virtuálních počítačů](media/list-cloudsimple-virtual-machines.png)

Seznam virtuálních počítačů CloudSimple zahrnuje virtuální počítače vytvořené z portálu Azure.  Virtuální počítače vytvořené v centru Privátního cloudu v mapovaném fondu prostředků vcentru se zobrazí v seznamu.  
