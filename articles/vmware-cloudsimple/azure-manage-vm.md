---
title: Řešení Azure VMware podle CloudSimple – Správa virtuálních počítačů s privátním cloudem v Azure
description: Popisuje, jak spravovat virtuální počítače CloudSimple privátního cloudu v Azure Portal, včetně přidávání disků, změny kapacity virtuálních počítačů a přidávání síťových rozhraní.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 44a0b8fe56477620c0ac47d5c5de8830dac46214
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71037477"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>Správa virtuálních počítačů privátního cloudu CloudSimple v Azure

Pro správu virtuálních počítačů, které jste [vytvořili pro privátní cloud CloudSimple](azure-create-vm.md), se přihlásíte k [Azure Portal](https://portal.azure.com). Vyhledejte a vyberte virtuální (vyhledávání v části **všechny služby** nebo **Virtual Machines** v postranní nabídce).

## <a name="control-virtual-machine-operation"></a>Kontrola operace virtuálního počítače

Následující ovládací prvky jsou k dispozici na stránce **Přehled** pro vybraný virtuální počítač.

| Ovládací prvek | Popis |
| ------------ | ------------- |
| Připojit | Připojte se k zadanému virtuálnímu počítači.  |
| Spustit | Spusťte zadaný virtuální počítač.  |
| Restart | Vypněte a pak zapněte zadaný virtuální počítač.  |
| Zastavit | Vypněte konkrétní virtuální počítač.  |
| Zachycování | Zachyťte image zadaného virtuálního počítače, aby ji bylo možné použít jako image k vytvoření dalších virtuálních počítačů. Viz [Vytvoření spravované image zobecněného virtuálního počítače v Azure](../virtual-machines/windows/classic/capture-image.md).   |
| Přesunout | Přejděte na zadaný virtuální počítač.  |
| Odstranění | Odeberte zadaný virtuální počítač.  |
| Aktualizovat | Aktualizuje data v zobrazení.  |

### <a name="view-performance-information"></a>Zobrazit informace o výkonu

Grafy v dolní oblasti stránky **přehledu** prezentují data o výkonu pro vybraný interval (poslední hodiny do 30 dní, výchozí hodnota je poslední hodina). V rámci každého grafu můžete v rámci intervalu zobrazit číselné hodnoty tak, že kurzor přesunete zpátky a zpátky přes graf.

Zobrazí se následující grafy.

| Položka | Popis |
| ------------ | ------------- |
| CPU (průměr) | Průměrné využití procesoru v procentech za vybraný interval.   |
| Síť | Provoz v síti a ze sítě (MB) v průběhu vybraného intervalu.  |
| Bajty disku | Celkový objem dat načtených z disku a zápis na disk (MB) za vybraný interval.  |
| Operace disků | Průměrná míra operací disku (operací za sekundu) v průběhu vybraného intervalu. |

## <a name="manage-vm-disks"></a>Správa disků virtuálních počítačů

Pokud chcete přidat disk virtuálního počítače, otevřete stránku **disky** pro vybraný virtuální počítač. Pokud chcete přidat disk, klikněte na **Přidat disk**. Proveďte konfiguraci každého z následujících nastavení zadáním nebo výběrem vložené možnosti. Klikněte na **Uložit**.

   | Položka | Popis |
   | ------------ | ------------- |
   | Name | Zadejte název pro identifikaci disku.  |
   | Size | Vyberte jednu z dostupných velikostí.  |
   | Řadič SCSI | Vyberte řadič SCSI. Dostupné řadiče se liší v různých podporovaných operačních systémech.  |
   | Režim | Určuje, jak se disk podílí na snímcích. Vyberte jednu z těchto možností: <br> -Nezávislé na trvalé: Všechna data zapsaná na disk se napíší trvale.<br> Nezávislé, netrvalé: Změny zapsané na disk se při vypnutí nebo resetování virtuálního počítače zahodí.  Tento režim umožňuje vždy restartovat virtuální počítač ve stejném stavu. Další informace najdete v [dokumentaci k VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html). |

Disk odstraníte tak, že ho vyberete a kliknete na **Odstranit**.

## <a name="change-the-capacity-of-the-vm"></a>Změna kapacity virtuálního počítače

Pokud chcete změnit kapacitu virtuálního počítače, otevřete stránku **Velikost** pro vybraný virtuální počítač. Zadejte některou z následujících možností a klikněte na **Uložit**.

| Položka | Popis |
| ------------ | ------------- |
| Počet jader | Počet jader přiřazených k virtuálnímu počítači.  |
| Virtualizace hardwaru | Zaškrtnutím políčka zpřístupníte hardwarovou virtualizaci hostovanému operačnímu systému. Podívejte se na článek o VMware [vystavte virtualizaci hardwaru VMware s asistencí](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html). |
| Velikost paměti | Vyberte velikost paměti, která se má přidělit virtuálnímu počítači.  

## <a name="manage-network-interfaces"></a>Správa síťových rozhraní

Chcete-li přidat rozhraní, klikněte na tlačítko **Přidat síťové rozhraní**. Proveďte konfiguraci každého z následujících nastavení tak, že zadáte nebo vyberete vloženou možnost. Klikněte na **Uložit**.

   | Ovládací prvek | Popis |
   | ------------ | ------------- |
   | Name | Zadejte název pro identifikaci rozhraní.  |
   | Síť | Vyberte ze seznamu konfigurovaných sítí v vSphere privátního cloudu.  |
   | Adaptér | Vyberte adaptér vSphere ze seznamu dostupných typů nakonfigurovaných pro virtuální počítač. Další informace najdete v článku znalostní báze VMware, který [vybírá síťový adaptér pro virtuální počítač](https://kb.vmware.com/s/article/1001805). |
   | Zapnout při spuštění | Určete, jestli se má při spuštění virtuálního počítače povolit hardware síťové karty. Výchozí hodnota je **Enable**. |

Síťové rozhraní odstraníte tak, že ho vyberete a kliknete na **Odstranit**.
