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
ms.openlocfilehash: 58edadb553730b646f23f4981d6cbf1bdbfe76d5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577717"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Rychlý start – využívání virtuálních počítačů VMware v Azure

K vytvoření virtuálního počítače na webu Azure Portal, pomocí šablon virtuálních počítačů, které má povolenou CloudSimple správce pro vaše předplatné. Tyto šablony virtuálních počítačů se nachází na infrastrukturu VMware.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>Vytvoření virtuálního počítače CloudSimple v Azure vyžaduje šablonu virtuálního počítače

Vytvoření virtuálního počítače v privátním cloudu ze serveru vCenter uživatelského rozhraní. Chcete-li vytvořit šablonu, postupujte podle pokynů v [Naklonovat virtuální počítač do šablony ve webovém klientovi vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Store šablony virtuálních počítačů na vCenter privátního cloudu.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Vytvoření virtuálního počítače na webu Azure Portal

1. V nabídce vlevo klikněte na tlačítko **+** nebo **vytvořit prostředek**.

2. V nabídce vlevo klikněte na tlačítko **Compute**a potom klikněte na tlačítko **CloudSimple virtuálního počítače**.

3. Klikněte na tlačítko **potvrdit** k ověření, že chcete vytvořit nový virtuální počítač.

4. Nastavení základní konfigurace, jak je popsáno v následující tabulce a potom klikněte na tlačítko **Další: Velikost**.

    | Pole | Popis |
    | ------------ | ------------- |
    | Předplatné | Předplatné Azure spojené s nasazením privátního cloudu.  |
    | Skupina prostředků | Nasazení skupiny, ke kterému bude přiřazena virtuálnímu počítači. Můžete vybrat existující skupinu nebo vytvořte novou. |
    | Název | Název pro identifikaci virtuálního počítače.  |
    | Location | Oblasti Azure, ve kterém je tento virtuální počítač hostovaný.  |
    | Fond zdrojů | Fyzické prostředky pro virtuální počítač. Vyberte fondy zdrojů k dispozici. |
    | vSphere šablony | Typ operačního systému šablony virtuálního počítače.  |
    | Uživatelské jméno | Uživatelské jméno správce virtuálního počítače. |
    | Heslo potvrzení hesla | Heslo správce virtuálního počítače.  |

5. Vyberte počet jader a kapacita paměti pro virtuální počítač.

6. (Volitelné) Pokud chcete vystavit kompletních virtualizačních procesoru v operačním systému hosta, vyberte **vystavit do hostovaného operačního systému** zaškrtávací políčko.
Tato volba umožňuje aplikacím, které vyžadují hardwarovou virtualizaci pro spuštění na virtuálních počítačích bez binární překlad nebo paravirtualizace. Další informace najdete v článku VMware [vystavit VMware hardwaru virtualizaci](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html).

7. Klikněte na tlačítko **Další: Konfigurace**.

8. Nakonfigurujte síťová rozhraní a disky, jak je popsáno v následujících tabulkách.

    Síťová rozhraní, klikněte na tlačítko **přidat síťové rozhraní** a nakonfigurujte následující nastavení.

    | Řízení | Popis |
    | ------------ | ------------- |
    | Název | Zadejte název pro identifikaci rozhraní.  |
    | Síť | Vyberte ze seznamu nakonfigurovaných sítí v vSphere vašeho privátního cloudu.  |
    | Adaptér | Vyberte adaptér vSphere ze seznamu dostupných typů, které jsou nakonfigurované pro virtuální počítač. Další informace najdete v článku znalostní báze VMware [výběr síťový adaptér pro virtuální počítač](https://kb.vmware.com/s/article/1001805). |
    | Zapnutí při spuštění počítače | Zvolte, jestli se má povolit hardwaru síťového adaptéru, je-li virtuální počítač spuštěn. Výchozí hodnota je **povolit**. |

    Disky, klikněte na tlačítko **přidat disk** a nakonfigurujte následující nastavení.

    | Položka | Popis |
    | ------------ | ------------- |
    | Název | Zadejte název pro identifikaci disku.  |
    | Velikost | Vyberte jednu z dostupných velikostí.  |
    | Řadič SCSI | Vyberte řadič SCSI. K dispozici řadiče lišit pro různé podporované operační systémy.  |
    | Mode | Určuje, jak je disk součástí snímky. Vyberte jednu z těchto možností: <br> -Trvalé nezávislé: Všechna data zapsaná na disk je zapsána trvale.<br> -Nezávislé na dočasné: Při vypnutí nebo obnovit virtuální počítač se zahodí změny zapsaných na disk.  Nezávislé dočasné režim umožňuje vždy restartuje virtuální počítač ve stejném státě. Další informace najdete v tématu [dokumentace ke službě VMware.](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)

9. Zkontrolujte nastavení. Pokud chcete něco změnit, klikněte na karty v horní části.

10. Klikněte na tlačítko **vytvořit** uložte nastavení a vytvoření virtuálního počítače.

## <a name="next-steps"></a>Další postup

* [Zobrazení seznamu CloudSimple virtuálních počítačů](https://docs.azure.cloudsimple.com/azurelistvms/)
* [Správa CloudSimple virtuálního počítače z Azure](https://docs.azure.cloudsimple.com/azureoverviewpage/)