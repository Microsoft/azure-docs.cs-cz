---
title: Řešení Azure VMware podle rychlého startu CloudSimple - využívání virtuálních počítačů VMware v Azure
description: Zjistěte, jak nakonfigurovat a využívání virtuálních počítačů VMware z webu Azure portal pomocí řešení VMware Azure podle CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3940adfaa42de8ac9c3f32a9eadc8f6d643ce3ce
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149546"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Rychlý start – využívání virtuálních počítačů VMware v Azure

K vytvoření virtuálního počítače na webu Azure Portal, pomocí šablon virtuálních počítačů, které má povolenou CloudSimple správce pro vaše předplatné. Tyto šablony virtuálních počítačů se nachází na infrastrukturu VMware.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>Vytvoření virtuálního počítače CloudSimple v Azure vyžaduje šablonu virtuálního počítače

Vytvoření virtuálního počítače v privátním cloudu ze serveru vCenter uživatelského rozhraní. Chcete-li vytvořit šablonu, postupujte podle pokynů v [Naklonovat virtuální počítač do šablony ve webovém klientovi vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Store šablony virtuálních počítačů na vCenter privátního cloudu.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Vytvoření virtuálního počítače na webu Azure Portal

1. Vyberte **Všechny služby**.

2. Vyhledejte **CloudSimple virtuálních počítačů**.

3. Klikněte na tlačítko **Add** (Přidat).

    ![Vytvoření virtuálního počítače CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Zadejte základní informace zobrazíte kliknutím **Další: velikost**.

    ![Vytvoření virtuálního počítače CloudSimple – základy](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Pole | Popis |
    | ------------ | ------------- |
    | Předplatné | Předplatné Azure spojené s privátního cloudu.  |
    | Skupina prostředků | Skupina prostředků, ke kterému bude přiřazena virtuálnímu počítači. Můžete vybrat existující skupinu nebo vytvořte novou. |
    | Název | Název pro identifikaci virtuálního počítače.  |
    | Location | Oblasti Azure, ve kterém je tento virtuální počítač hostovaný.  |
    | Private Cloud | CloudSimple privátního cloudu, ve kterém chcete vytvořit virtuální počítač. |
    | Fond zdrojů | Mapovat fondu zdrojů pro virtuální počítač. Vyberte fondy zdrojů k dispozici. |
    | vSphere šablony | Šablona vSphere pro virtuální počítač.  |
    | Uživatelské jméno | Uživatelské jméno správce virtuálního počítače (pro Windows šablony)|
    | Heslo |  Heslo správce virtuálního počítače (pro Windows šablony). |
    | Potvrzení hesla | Potvrzení hesla |

5. Vyberte počet jader a kapacita paměti pro virtuální počítač a klikněte na **Další: konfigurace**. Zaškrtněte políčko, pokud chcete vystavovat úplné virtualizace procesoru na hostovaném operačním systému. Aplikace, které vyžadují hardwarovou virtualizaci můžete spouštět na virtuálních počítačích bez binární překlad nebo paravirtualizace. Další informace najdete v článku VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">vystavit VMware hardwaru virtualizaci</a>.

    ![Vytvoření virtuálního počítače CloudSimple – velikost](media/create-cloudsimple-virtual-machine-size.png)

6. Nakonfigurujte síťová rozhraní a disky, jak je popsáno v následujících tabulkách a klikněte na **revize + vytvořit**.

    ![Vytvoření virtuálního počítače CloudSimple – konfigurace](media/create-cloudsimple-virtual-machine-configurations.png)

    Síťová rozhraní, klikněte na tlačítko **přidat síťové rozhraní** a nakonfigurujte následující nastavení.
    
    | Řízení | Popis |
    | ------------ | ------------- |
    | Název | Zadejte název pro identifikaci rozhraní.  |
    | Síť | Vyberte ze seznamu port nakonfigurovaný distribuované skupiny pro vSphere vašeho privátního cloudu.  |
    | Adaptér | Vyberte adaptér vSphere ze seznamu dostupných typů, které jsou nakonfigurované pro virtuální počítač. Další informace najdete v článku znalostní báze VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">výběr síťový adaptér pro virtuální počítač</a>. |
    | Zapnutí při spuštění počítače | Zvolte, jestli se má povolit hardwaru síťového adaptéru, je-li virtuální počítač spuštěn. Výchozí hodnota je **povolit**. |

    Disky, klikněte na tlačítko **přidat disk** a nakonfigurujte následující nastavení.

    | Položka | Popis | 
    | ------------ | ------------- | 
    | Název | Zadejte název pro identifikaci disku.  | 
    | Velikost | Vyberte jednu z dostupných velikostí.  | 
    | Řadič SCSI | Vyberte řadič SCSI pro disk.  |
    | Mode | Určuje, jak je disk součástí snímky. Vyberte jednu z těchto možností: <br> -Trvalé nezávislé: Všechna data zapsaná na disk je zapsána trvale.<br> -Nezávislé na dočasné: Při vypnutí nebo obnovit virtuální počítač se zahodí změny zapsaných na disk.  Nezávislé dočasné režim umožňuje vždy restartuje virtuální počítač ve stejném státě. Další informace najdete v tématu <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">dokumentaci k VMware</a>.

7. Po dokončení ověření, zkontrolujte nastavení a klikněte na tlačítko **vytvořit**. Pokud chcete něco změnit, klikněte na karty v horní části nebo klikněte na tlačítko.

    ![Vytvoření virtuálního počítače CloudSimple – kontrola](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Další postup

* [Zobrazení seznamu CloudSimple virtuálních počítačů](https://docs.azure.cloudsimple.com/azure-manage-vm/)
* [Správa CloudSimple virtuálního počítače z Azure](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)