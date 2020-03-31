---
title: Řešení Azure VMware od CloudSimple – správa virtuálních počítačích privátního cloudu v Azure
description: Popisuje, jak spravovat virtuální počítače CloudSimple Privátní cloud na webu Azure Portal, včetně přidání disků, změny kapacity virtuálních počítačů a přidání síťových rozhraní.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 44a0b8fe56477620c0ac47d5c5de8830dac46214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014993"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>Správa virtuálních počítačů CloudSimple Pripricloud v Azure

Pokud chcete spravovat virtuální počítače, které jste [vytvořili pro svůj CloudSimple Private Cloud](azure-create-vm.md), přihlaste se na [portál Azure](https://portal.azure.com). Vyhledejte a vyberte virtuální (vyhledejte v části **Všechny služby** nebo **virtuální počítače** v boční nabídce).

## <a name="control-virtual-machine-operation"></a>Řízení provozu virtuálního počítače

Následující ovládací prvky jsou k dispozici na stránce **Přehled** pro vybraný virtuální počítač.

| Řízení | Popis |
| ------------ | ------------- |
| Připojení | Připojte se k zadanému virtuálnímu virtuálnímu bodu.  |
| Start | Spusťte zadaný virtuální ms.  |
| Restartování | Vypněte a potom napněte zadaný virtuální ms.  |
| Zastavit | Vypněte konkrétní virtuální ms.  |
| Zachycování | Zachyťte obraz zadaného virtuálního virtuálního aplikace, aby ho bylo možné použít jako image k vytvoření dalších virtuálních virtuálních měn. Viz [Vytvoření spravované image generalizovaného virtuálního počítače v Azure](../virtual-machines/windows/classic/capture-image.md).   |
| Přesunout | Přechod na zadaný virtuální ms.  |
| Odstranění | Odeberte zadaný virtuální ms.  |
| Obnovení | Aktualizujte data na displeji.  |

### <a name="view-performance-information"></a>Zobrazit informace o výkonu

Grafy v dolní části stránky **Přehled** představují údaje o výkonu pro vybraný interval (poslední hodina za posledních 30 dní; výchozí hodnota je poslední hodina). V rámci každého grafu můžete zobrazit číselné hodnoty pro libovolné časy v rámci intervalu přesunutím kurzoru tam a zpět přes graf.

Zobrazí se následující grafy.

| Položka | Popis |
| ------------ | ------------- |
| CPU (průměr) | Průměrné využití procesoru v procentech za vybraný interval.   |
| Network (Síť) | Přenosy do a ze sítě (MB) ve vybraném intervalu.  |
| Bajty disku | Celkový počet dat přečtených z disku a zapsaného na disk (MB) ve vybraném intervalu.  |
| Operace na disku | Průměrná rychlost operací na disku (operace/sekunda) za vybraný interval. |

## <a name="manage-vm-disks"></a>Správa disků virtuálních počítačů

Chcete-li přidat disk virtuálního počítače, otevřete stránku **Disky** pro vybraný virtuální virtuální počítače. Chcete-li přidat disk, klepněte na tlačítko **Přidat disk**. Každé z následujících nastavení nakonfigurujte zadáním nebo výběrem možnosti vsazení. Klikněte na **Uložit**.

   | Položka | Popis |
   | ------------ | ------------- |
   | Name (Název) | Zadejte název k identifikaci disku.  |
   | Velikost | Vyberte jednu z dostupných velikostí.  |
   | Řadič SCSI | Vyberte řadič SCSI. Dostupné řadiče se liší pro různé podporované operační systémy.  |
   | Mode | Určuje, jak se disk účastní snímků. Zvolte jednu z těchto možností: <br> - Nezávislé persistent: Všechna data zapsaná na disk jsou zapsána trvale.<br> - Nezávislé, netrvalé: Změny zapsané na disk jsou zahozeny při vypnutí nebo resetování virtuálního počítače.  Tento režim umožňuje vždy restartovat virtuální počítač ve stejném stavu. Další informace naleznete v dokumentaci společnosti [VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html). |

Chcete-li disk odstranit, vyberte jej a klepněte na tlačítko **Odstranit**.

## <a name="change-the-capacity-of-the-vm"></a>Změna kapacity virtuálního virtuálního mísa

Pokud chcete změnit kapacitu virtuálního počítače, otevřete stránku **Velikost** pro vybraný virtuální hod. Zadejte některou z následujících možností a klepněte na **tlačítko Uložit**.

| Položka | Popis |
| ------------ | ------------- |
| Počet jader | Počet jader přiřazených k virtuálnímu účtu.  |
| Virtualizace hardwaru | Zaškrtnutím políčka zpřístupníte virtualizaci hardwaru hostovanému osu. Viz článek společnosti VMware [vystavit virtualizaci s asistencí hardwaru společnosti VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html). |
| Velikost paměti | Vyberte velikost paměti, která se má přidělit virtuálnímu soudu.  

## <a name="manage-network-interfaces"></a>Správa síťových rozhraní

Chcete-li přidat rozhraní, klepněte na tlačítko **Přidat síťové rozhraní**. Každé z následujících nastavení nakonfigurujte zadáním nebo výběrem vsazené možnosti. Klikněte na **Uložit**.

   | Řízení | Popis |
   | ------------ | ------------- |
   | Name (Název) | Zadejte název pro identifikaci rozhraní.  |
   | Network (Síť) | Vyberte ze seznamu nakonfigurovaných sítí ve vašem privátním cloudu vSphere.  |
   | Adaptér | Vyberte adaptér vSphere ze seznamu dostupných typů nakonfigurovaných pro virtuální počítače. Další informace naleznete v článku znalostní báze VMware [Výběr síťového adaptéru pro virtuální počítač](https://kb.vmware.com/s/article/1001805). |
   | Zapnutí při startu | Zvolte, jestli chcete povolit hardware nic při spuštění virtuálního mísy. Výchozí hodnota je **Povolit**. |

Chcete-li odstranit síťové rozhraní, vyberte jej a klepněte na tlačítko **Odstranit**.
