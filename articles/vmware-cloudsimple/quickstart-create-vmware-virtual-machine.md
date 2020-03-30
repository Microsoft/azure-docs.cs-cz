---
title: 'Úvodní příručka: Využití virtuálních počítačů VMware v Azure'
titleSuffix: Azure VMware Solution by CloudSimple
description: Zjistěte, jak nakonfigurovat a využívat virtuální počítače VMware z webu Azure Portal pomocí řešení Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ab613c251bc43a025e0381046805ec998a04227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019549"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Úvodní příručka – využití virtuálních počítačů VMware v Azure

Pokud chcete vytvořit virtuální počítač na webu Azure Portal, použijte šablony virtuálních strojů, které váš správce CloudSimple povolil pro vaše předplatné. Šablony virtuálních počítače se nacházejí v infrastruktuře VMware.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>CloudSimple Vytvoření virtuálních počítačů v Azure vyžaduje šablonu virtuálního počítače

Vytvořte virtuální počítač v privátním cloudu z ui vcenter. Chcete-li vytvořit šablonu, postupujte podle pokynů v [části Klonování virtuálního počítače k šabloně ve webovém klientovi vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Uložte šablonu virtuálního počítače do virtuálního počítače Privátního cloudu.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Vytvoření virtuálního počítače na webu Azure Portal

1. Vyberte **Všechny služby**.

2. Vyhledejte **cloudsimple virtuální počítače**.

3. Klikněte na **Přidat**.

    ![Vytvoření virtuálního počítače CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Zadejte základní informace a klepněte na **tlačítko Další:Velikost**.

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
    | Uživatelské jméno | Uživatelské jméno správce virtuálního počítače (pro šablony systému Windows).|
    | Heslo |  Heslo pro správce virtuálního počítače (pro šablony systému Windows). |
    | Potvrzení hesla | Potvrďte heslo. |

5. Vyberte počet jader a kapacitu paměti pro virtuální počítače a klepněte na **tlačítko Další:Konfigurace**. Pokud chcete zpřístupnit úplnou virtualizaci procesoru hostovanému operačnímu systému, zaškrtněte toto políčko. Aplikace, které vyžadují virtualizaci hardwaru, lze spustit na virtuálních počítačích bez binárního překladu nebo paravirtualizace. Další informace naleznete v článku společnosti VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">expose VMware Hardware Assisted Virtualization</a>.

    ![Vytvořit cloudsimple virtuální počítač – velikost](media/create-cloudsimple-virtual-machine-size.png)

6. Nakonfigurujte síťová rozhraní a disky, jak je popsáno v následujících tabulkách, a klepněte na tlačítko **Revize + vytvořit**.

    ![Vytvoření virtuálního počítače CloudSimple – konfigurace](media/create-cloudsimple-virtual-machine-configurations.png)

    U síťových rozhraní klepněte na tlačítko **Přidat síťové rozhraní** a nakonfigurujte následující nastavení.

    | Řízení | Popis |
    | ------------ | ------------- |
    | Name (Název) | Zadejte název pro identifikaci rozhraní.  |
    | Network (Síť) | Vyberte ze seznamu nakonfigurovaných distribuovaných portů ve vašem privátním cloudu vSphere.  |
    | Adaptér | Vyberte adaptér vSphere ze seznamu dostupných typů nakonfigurovaných pro virtuální počítače. Další informace naleznete v článku znalostní báze VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">Výběr síťového adaptéru pro virtuální počítač</a>. |
    | Zapnutí při startu | Zvolte, jestli chcete povolit hardware nic při spuštění virtuálního mísy. Výchozí hodnota je **Povolit**. |

    U disků klepněte na tlačítko **Přidat disk** a nakonfigurujte následující nastavení.

    | Položka | Popis |
    | ------------ | ------------- |
    | Name (Název) | Zadejte název k identifikaci disku.  |
    | Velikost | Vyberte jednu z dostupných velikostí.  |
    | Řadič SCSI | Vyberte řadič SCSI pro disk.  |
    | Mode | Určuje, jak se disk účastní snímků. Zvolte jednu z těchto možností: <br> - Nezávislé persistent: Všechna data zapsaná na disk jsou zapsána trvale.<br> - Nezávislé non-perzistentní: Změny zapsané na disk jsou zahozeny při vypnutí nebo resetování virtuálního počítače.  Nezávislý netrvalý režim umožňuje vždy restartovat virtuální počítač ve stejném stavu. Další informace naleznete v dokumentaci společnosti <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">VMware</a>.

7. Po dokončení ověření zkontrolujte nastavení a klepněte na tlačítko **Vytvořit**. Chcete-li provést nějaké změny, klikněte na karty v horní části nebo klikněte na.

    ![Vytvořit cloudsimple virtuální stroj - recenze](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Další kroky

* [Zobrazit seznam virtuálních počítačů CloudSimple](azure-create-vm.md#view-list-of-cloudsimple-virtual-machines)
* [Správa virtuálního počítače CloudSimple z Azure](azure-manage-vm.md)
