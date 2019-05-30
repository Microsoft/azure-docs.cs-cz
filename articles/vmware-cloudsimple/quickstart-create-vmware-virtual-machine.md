---
title: Řešení Azure VMware podle rychlého startu CloudSimple - využívání virtuálních počítačů VMware v Azure
description: V tomto rychlém startu se dozvíte jak nakonfigurovat a používat virtuální počítače VMware na webu Azure Portal pomocí řešení VMware Azure podle CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: quickstart
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b430efbc931d72de4b095a7eac4c1e7ca496b1b9
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393497"
---
# <a name="quickstart-consume-vmware-vms-on-azure"></a>Rychlý start: Využívání virtuálních počítačů VMware v Azure

K vytvoření virtuálního počítače na webu Azure Portal, můžete použít šablony virtuálních počítačů, které jsou k dispozici na vCenter privátního cloudu. K serveru vCenter oprávnění správce, můžete vytvořit další šablony do privátního cloudu.

## <a name="create-a-vm-template"></a>Vytvořte šablonu virtuálního počítače

Nejprve vytvořte virtuální počítač v privátním cloudu přes vCenter uživatelského rozhraní. Pokud chcete vytvořit šablonu, postupujte podle pokynů v článku VMware [Naklonovat virtuální počítač do šablony ve webovém klientovi vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Store šablony virtuálních počítačů na vCenter privátního cloudu.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Vytvoření virtuálního počítače na webu Azure Portal

1. Vyberte **Všechny služby**.

2. Vyhledejte **CloudSimple virtuálních počítačů**.

3. Vyberte **Přidat**.

    ![Výběr možnosti Přidat](media/create-cloudsimple-virtual-machine.png)

4. Zadejte následující informace o virtuálním počítači a pak vyberte **Další: Velikost**.

    ![Vytvoření virtuálního počítače CloudSimple – základy](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Pole | Popis |
    | ------------ | ------------- |
    | **Předplatné** | Předplatné Azure spojené s privátním cloudu.  |
    | **Skupina prostředků** | Skupina prostředků, ke kterému bude přiřazena virtuálnímu počítači. Můžete vybrat existující skupinu nebo vytvořte novou. |
    | **Název** | Název pro identifikaci virtuálního počítače.  |
    | **Umístění** | Oblasti Azure, ve kterém je virtuální počítač hostovaný.  |
    | **Private Cloud** | CloudSimple privátního cloudu ve kterém chcete vytvořit virtuální počítač. |
    | **ResourcePool** | Fond namapovaný prostředek pro virtuální počítač. Vyberte fondy zdrojů k dispozici. |
    | **vSphere šablony** | Šablona vSphere pro virtuální počítač.  |
    | **Uživatelské jméno** | Uživatelské jméno správce virtuálního počítače (pro Windows šablony).|
    | **Heslo** |  Heslo správce virtuálního počítače (pro Windows šablony). |
    | **Potvrdit heslo** | Heslo zadané v poli předchozí. |

5. Vyberte počet jader a kapacita paměti pro virtuální počítač. Vyberte **vystavit do hostovaného operačního systému** Pokud chcete vystavovat úplné virtualizace procesoru na hostovaném operačním systému. Aplikace, které vyžadují hardwarovou virtualizaci můžete spouštět na virtuálních počítačích bez binární překlad nebo paravirtualizace. Další informace najdete v článku VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">vystavit VMware hardwaru virtualizaci</a>. Jakmile budete hotovi, vyberte **Další: Konfigurace**.

    ![Vytvoření virtuálního počítače CloudSimple – velikost](media/create-cloudsimple-virtual-machine-size.png)

6. Nakonfigurujte síťová rozhraní a disky, jak je popsáno v následujících tabulkách a potom vyberte **revize + vytvořit**.

    ![Vytvořte virtuální počítač CloudSimple – konfigurace](media/create-cloudsimple-virtual-machine-configurations.png)

    Síťová rozhraní, vyberte **přidat síťové rozhraní** a nakonfigurujte následující nastavení:
    
    | Nastavení | Popis |
    | ------------ | ------------- |
    | **Název** | Zadejte název pro identifikaci rozhraní.  |
    | **Sítě** | Vyberte ze seznamu skupin distribuované port nakonfigurovaný v vSphere vašeho privátního cloudu.  |
    | **Adaptér** | Vyberte adaptér vSphere ze seznamu dostupných typů, které jsou nakonfigurované pro virtuální počítač. Další informace najdete v článku VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">výběr síťový adaptér pro virtuální počítač</a>. |
    | **Zapnutí při spuštění počítače** | Zvolte, jestli se má povolit hardwaru síťového adaptéru, je-li virtuální počítač spuštěn. Výchozí hodnota je **povolit**. |

    Pro disky, vyberte **přidat disk** a nakonfigurujte následující nastavení:

    | Nastavení | Popis |
    | ------------ | ------------- |
    | **Název** | Zadejte název pro identifikaci disku.  |
    | **Velikost** | Vyberte jednu z dostupných velikostí.  |
    | **Řadič SCSI** | Vyberte řadič SCSI pro disk.  |
    | **Mode** | Režim určuje, jak je disk součástí snímky. Vyberte jednu z těchto možností: <br> **Nezávislé na trvalé**: Všechny změny zapsaných na disk zapisují trvale.<br> **Nezávislé na dočasné**: Při vypnutí nebo obnovit virtuální počítač se zahodí změny zapsaných na disk. Nezávislé dočasné režim umožňuje vždy restartuje virtuální počítač ve stejném státě. Další informace najdete v tématu <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">dokumentaci k VMware</a>.

7. Po dokončení ověření, zkontrolujte nastavení a vyberte **vytvořit**. Pokud chcete provést změny, vyberte na karty v horní části nebo vyberte **předchozí: Konfigurace**.

    ![Vytvořit virtuální počítač CloudSimple – zkontrolovat a vytvořit](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Další postup

* [Zobrazení seznamu CloudSimple virtuálních počítačů](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)
* [Spravovat CloudSimple virtuální počítače z Azure](https://docs.azure.cloudsimple.com/azure-manage-vm/)
