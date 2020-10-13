---
title: 'Rychlý Start: využívání virtuálních počítačů VMware v Azure'
titleSuffix: Azure VMware Solution by CloudSimple
description: Naučte se konfigurovat a využívat virtuální počítače VMware z Azure Portal pomocí řešení Azure VMware od CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ab613c251bc43a025e0381046805ec998a04227
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77019549"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Rychlý Start – využívání virtuálních počítačů VMware v Azure

Pokud chcete vytvořit virtuální počítač v Azure Portal, použijte šablony virtuálních počítačů, které správce CloudSimple povolil pro vaše předplatné. Šablony virtuálních počítačů najdete v infrastruktuře VMware.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>Vytváření virtuálních počítačů CloudSimple v Azure vyžaduje šablonu virtuálního počítače.

Vytvořte virtuální počítač v privátním cloudu z uživatelského rozhraní vCenter. Pokud chcete vytvořit šablonu, postupujte podle pokynů v části [klonování virtuálního počítače do šablony ve webovém klientovi vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Uložte šablonu virtuálního počítače do serveru vCenter privátního cloudu.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Vytvoření virtuálního počítače na webu Azure Portal

1. Vyberte **Všechny služby**.

2. Vyhledejte **CloudSimple Virtual Machines**.

3. Klikněte na **Přidat**.

    ![Vytvořit virtuální počítač s CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Zadejte základní informace a klikněte na **Další: velikost**.

    ![Vytvořit virtuální počítač s CloudSimple – základní informace](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Pole | Popis |
    | ------------ | ------------- |
    | Předplatné | Předplatné Azure přidružené k vašemu privátnímu cloudu.  |
    | Resource Group | Skupina prostředků, ke které se virtuální počítač přiřadí. Můžete vybrat existující skupinu nebo vytvořit novou. |
    | Název | Název, který identifikuje virtuální počítač.  |
    | Umístění | Oblast Azure, ve které je tento virtuální počítač hostovaný.  |
    | Privátní cloud | CloudSimple privátní cloud, ve kterém chcete vytvořit virtuální počítač. |
    | Fond zdrojů | Namapovaný fond zdrojů pro virtuální počítač. Vyberte z dostupných fondů zdrojů. |
    | Šablona vSphere | Šablona vSphere pro virtuální počítač  |
    | Uživatelské jméno | Uživatelské jméno správce virtuálního počítače (pro šablony systému Windows).|
    | Heslo |  Heslo správce virtuálního počítače (pro šablony systému Windows). |
    | Potvrzení hesla | Potvrďte heslo. |

5. Vyberte počet jader a kapacitu paměti pro virtuální počítač a klikněte na **Další: Konfigurace**. Zaškrtněte políčko, pokud chcete vystavit úplnou virtualizaci procesoru hostovanému operačnímu systému. Aplikace, které vyžadují virtualizaci hardwaru, můžou běžet na virtuálních počítačích bez binárního překladu nebo vlastnostmi paravirtualizace. Další informace najdete v článku o VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">vystavování virtualizace hardwaru VMware</a>.

    ![Vytvořit virtuální počítač s CloudSimple – velikost](media/create-cloudsimple-virtual-machine-size.png)

6. Nakonfigurujte síťová rozhraní a disky, jak je popsáno v následujících tabulkách, a klikněte na tlačítko **zkontrolovat + vytvořit**.

    ![Vytvoření virtuálního počítače s CloudSimple – konfigurace](media/create-cloudsimple-virtual-machine-configurations.png)

    Pro síťová rozhraní klikněte na **Přidat síťové rozhraní** a nakonfigurujte následující nastavení.

    | Řízení | Popis |
    | ------------ | ------------- |
    | Název | Zadejte název pro identifikaci rozhraní.  |
    | Síť | V vSphere privátního cloudu vyberte ze seznamu nakonfigurované distribuované skupiny portů.  |
    | Adaptér | Vyberte adaptér vSphere ze seznamu dostupných typů nakonfigurovaných pro virtuální počítač. Další informace najdete v článku znalostní báze VMware, který <a href="https://kb.vmware.com/s/article/1001805" target="_blank">vybírá síťový adaptér pro virtuální počítač</a>. |
    | Zapnout při spuštění | Určete, jestli se má při spuštění virtuálního počítače povolit hardware síťové karty. Výchozí hodnota je **Enable**. |

    V části disky klikněte na **Přidat disk** a nakonfigurujte následující nastavení.

    | Položka | Popis |
    | ------------ | ------------- |
    | Název | Zadejte název pro identifikaci disku.  |
    | Velikost | Vyberte jednu z dostupných velikostí.  |
    | Řadič SCSI | Vyberte řadič SCSI pro disk.  |
    | Mode | Určuje, jak se disk podílí na snímcích. Vyberte jednu z těchto možností: <br> -Nezávislá trvalá: všechna data zapsaná na disk se napíší trvale.<br> Nezávislé na netrvalé: změny zapsané na disk se zahodí při vypnutí nebo resetování virtuálního počítače.  Nezávislý režim bez trvalého režimu umožňuje vždy restartovat virtuální počítač ve stejném stavu. Další informace najdete v <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">dokumentaci k VMware</a>.

7. Po dokončení ověření zkontrolujte nastavení a klikněte na **vytvořit**. Chcete-li provést změny, klikněte na karty v horní části nebo na tlačítko.

    ![Vytvoření virtuálního počítače s CloudSimple – kontrola](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Další kroky

* [Zobrazit seznam virtuálních počítačů s CloudSimple](azure-create-vm.md#view-list-of-cloudsimple-virtual-machines)
* [Správa virtuálního počítače s CloudSimple z Azure](azure-manage-vm.md)
