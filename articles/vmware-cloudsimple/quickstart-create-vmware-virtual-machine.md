---
title: Rychlé zprovoznění řešení Azure VMware podle CloudSimple – využívání virtuálních počítačů VMware v Azure
description: V tomto rychlém startu se dozvíte, jak nakonfigurovat a využívat virtuální počítače VMware z Azure Portal pomocí řešení Azure VMware od CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: quickstart
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: dd4faf6df54d478654c59ffc18bf8c5873d576b9
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816650"
---
# <a name="quickstart-consume-vmware-vms-on-azure"></a>Rychlý start: Využívání virtuálních počítačů VMware v Azure

Pokud chcete vytvořit virtuální počítač v Azure Portal, můžete použít šablony virtuálních počítačů, které jsou dostupné ve vašem privátním cloudu vCenter. Správce vCenter může vytvořit další šablony v privátním cloudu.

## <a name="create-a-vm-template"></a>Vytvoření šablony virtuálního počítače

Nejdřív vytvořte virtuální počítač v privátním cloudu pomocí uživatelského rozhraní vCenter. Pokud chcete vytvořit šablonu, postupujte podle pokynů v článku na webu VMware naklonujte [virtuální počítač do šablony ve webovém klientovi vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Uložte šablonu virtuálního počítače do serveru vCenter privátního cloudu.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Vytvořit virtuální počítač v Azure Portal

1. Vyberte **Všechny služby**.

2. Vyhledejte **CloudSimple Virtual Machines**.

3. Vyberte **Přidat**.

    ![Vyberte Přidat](media/create-cloudsimple-virtual-machine.png)

4. Zadejte následující informace o virtuálním počítači a potom vyberte **další: Velikost**.

    ![Vytvořit virtuální počítač s CloudSimple – základní informace](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Pole | Popis |
    | ------------ | ------------- |
    | **Předplatné** | Předplatné Azure přidružené k vašemu privátnímu cloudu.  |
    | **Skupina prostředků** | Skupina prostředků, ke které se virtuální počítač přiřadí. Můžete vybrat existující skupinu nebo vytvořit novou. |
    | **Název** | Název, který identifikuje virtuální počítač.  |
    | **Location** | Oblast Azure, ve které je virtuální počítač hostovaný.  |
    | **Privátní cloud** | Privátní cloud CloudSimple, ve kterém chcete vytvořit virtuální počítač. |
    | **ResourcePool** | Namapovaný fond zdrojů pro virtuální počítač. Vyberte z dostupných fondů zdrojů. |
    | **Šablona vSphere** | Šablona vSphere pro virtuální počítač.  |
    | **Uživatelské jméno** | Uživatelské jméno správce virtuálního počítače (pro šablony systému Windows).|
    | **Heslo** |  Heslo správce virtuálního počítače (pro šablony systému Windows). |
    | **Potvrdit heslo** | Heslo zadané v předchozím poli |

5. Vyberte počet jader a kapacitu paměti pro virtuální počítač. Pokud chcete zpřístupnit úplnou virtualizaci procesoru hostovanému operačnímu systému, vyberte možnost **zveřejnit pro hostovaný** operační systém. Aplikace, které vyžadují virtualizaci hardwaru, můžou běžet na virtuálních počítačích bez binárního překladu nebo vlastnostmi paravirtualizace. Další informace najdete v článku o VMware vystavování <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">virtualizace hardwaru VMware</a>. Až budete hotovi, vyberte **další: Konfigurace**.

    ![Vytvořit virtuální počítač s CloudSimple – velikost](media/create-cloudsimple-virtual-machine-size.png)

6. Nakonfigurujte síťová rozhraní a disky, jak je popsáno v následujících tabulkách, a pak vyberte **zkontrolovat + vytvořit**.

    ![Vytvoření virtuálního počítače s CloudSimple – konfigurace](media/create-cloudsimple-virtual-machine-configurations.png)

    Pro síťová rozhraní vyberte **Přidat síťové rozhraní** a pak nakonfigurujte následující nastavení:
    
    | Nastavení | Popis |
    | ------------ | ------------- |
    | **Název** | Zadejte název pro identifikaci rozhraní.  |
    | **Sítě** | Vyberte ze seznamu nakonfigurovaných distribuovaných skupin portů v privátním cloudu vSphere.  |
    | **Adaptér** | Vyberte adaptér vSphere ze seznamu dostupných typů nakonfigurovaných pro virtuální počítač. Další informace najdete v článku o VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">výběru síťového adaptéru pro virtuální počítač</a>. |
    | **Zapnout při spuštění** | Určete, jestli se má při spuštění virtuálního počítače povolit hardware síťové karty. Výchozí hodnota je **Enable**. |

    Pro disky vyberte **Přidat disk** a pak nakonfigurujte následující nastavení:

    | Nastavení | Popis |
    | ------------ | ------------- |
    | **Název** | Zadejte název pro identifikaci disku.  |
    | **Velikost** | Vyberte jednu z dostupných velikostí.  |
    | **Řadič SCSI** | Vyberte řadič SCSI pro disk.  |
    | **Mode** | Režim určuje, jak se disk podílí na snímcích. Vyberte jednu z těchto možností: <br> **Nezávislé na trvalé**: Všechny změny zapsané na disk se napíší trvale.<br> **Nezávislá**netrvalá: Změny zapsané na disk se při vypnutí nebo resetování virtuálního počítače zahodí. Nezávislý režim bez trvalého režimu umožňuje vždy restartovat virtuální počítač ve stejném stavu. Další informace najdete v <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">dokumentaci k VMware</a>.

7. Po dokončení ověření zkontrolujte nastavení a vyberte **vytvořit**. Chcete-li provést změny, vyberte karty v horní části nebo **vyberte předchozí: Konfigurace**.

    ![Vytvoření virtuálního počítače s CloudSimple – kontrola a vytvoření](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Další postup

* [Zobrazit seznam virtuálních počítačů s CloudSimple](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)
* [Správa virtuálních počítačů s CloudSimple z Azure](https://docs.azure.cloudsimple.com/azure-manage-vm/)
