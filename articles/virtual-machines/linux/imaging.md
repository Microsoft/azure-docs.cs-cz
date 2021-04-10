---
title: Přehled vytváření imagí pro Linux pro Azure
description: Postup převedení imagí virtuálních počítačů se systémem Linux nebo vytvoření nových imagí pro použití v Azure
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: a77c2bc69418b821933c0b62674500f7a32e40a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565202"
---
# <a name="bringing-and-creating-linux-images-in-azure"></a>Zavedení a vytváření imagí pro Linux v Azure

Tento přehled popisuje základní koncepty vytváření a způsobu úspěšného vytváření a používání imagí Linux v Azure. Než přenesete vlastní image do Azure, musíte mít na paměti typy a možnosti, které jsou vám k dispozici.

V tomto článku se seznámíte s rozhodovacími body a požadavky na image, vysvětlete klíčové koncepty, abyste mohli postupovat podle těchto pokynů a mohli si vytvořit vlastní image do vaší specifikace.

## <a name="difference-between-managed-disks-and-images"></a>Rozdíl mezi spravovanými disky a obrázky


Azure umožňuje převést virtuální pevný disk na platformu, použít ho jako [spravovaný disk](../faq-for-disks.md#managed-disks)nebo použít jako zdroj pro image. 

Azure Managed disks jsou jediné virtuální pevné disky. Můžete buď převzít existující virtuální pevný disk a vytvořit z něj spravovaný disk, nebo vytvořit prázdný spravovaný disk od začátku. Virtuální počítače můžete vytvořit ze spravovaných disků tak, že disk připojíte k virtuálnímu počítači, ale můžete použít jenom virtuální pevný disk s jedním virtuálním počítačem. Nemůžete změnit žádné vlastnosti operačního systému, Azure se jenom pokusí zapnout virtuální počítač a spustit ho pomocí tohoto disku. 

Image Azure můžou být tvořené několika disky s operačním systémem a datovými disky. Když použijete spravovanou bitovou kopii k vytvoření virtuálního počítače, platforma vytvoří kopii image a použije ji k vytvoření virtuálního počítače, takže spravovaná podpora spravovaných imagí znovu používá stejnou image pro víc virtuálních počítačů. Azure poskytuje taky pokročilé možnosti správy imagí, jako je globální replikace, a správa verzí prostřednictvím [Galerie sdílených imagí](../shared-image-galleries.md). 



## <a name="generalized-and-specialized"></a>Zobecněné a specializované

Azure nabízí dva hlavní typy obrázků, generalizované a specializované. Výrazy generalizované a specializované jsou původními podmínkami Windows, které se migrují do Azure. Tyto typy definují, jak platforma zpracuje virtuální počítač, když ho zapne. Oba typy mají výhody a nevýhody a požadavky. Než začnete, musíte znát, který typ obrázku budete potřebovat. Níže jsou shrnuté scénáře a typ, které byste si měli vybrat:

| Scenario      | Typ image  | Možnosti úložiště |
| ------------- |:-------------:| :-------------:| 
| Vytvořte image, která se dá nakonfigurovat tak, aby se použila pro víc virtuálních počítačů, a můžu nastavit název hostitele, přidat uživatele správce a provádět další úkoly během prvního spuštění. | Generalizovaná | Galerie sdílených imagí nebo samostatné spravované image |
| Vytvoření image z snímku virtuálního počítače nebo zálohování | Specializovaná |Galerie sdílených imagí nebo spravovaný disk |
| Rychlé vytvoření image, která nevyžaduje žádnou konfiguraci pro vytvoření více virtuálních počítačů |Specializovaná |Sdílená galerie obrázků |


### <a name="generalized-images"></a>Generalizované obrázky

Zobecněná Image je image, která vyžaduje, aby se instalace dokončila při prvním spuštění. Při prvním spuštění například nastavíte název hostitele, správce a další konfigurace specifické pro virtuální počítače. To je užitečné, pokud chcete, aby se obrázek opakovaně používal několikrát a když chcete při vytváření předat parametry. Pokud zobecněná bitová kopie obsahuje agenta Azure, agent zpracuje parametry a signál vrátí zpět na platformu, kterou dokončila počáteční konfigurace. Tento proces se nazývá [zřizování](./provisioning.md). 

Zřizování vyžaduje, aby byl v imagi zahrnutý zřizovací modul. Existují dva zřídí:
- [Agent Azure Linux](../extensions/agent-linux.md)
- [Cloud-init](./using-cloud-init.md)

Jedná se o [předpoklady](./create-upload-generic.md) pro vytvoření image.


### <a name="specialized-images"></a>Specializované obrázky
Jedná se o zcela nakonfigurované image, které nevyžadují virtuální počítače a speciální parametry. platforma bude jenom zapnout virtuální počítač. v rámci tohoto virtuálního počítače je potřeba zpracovat jedinečnost, jako je třeba nastavení názvu hostitele, aby nedocházelo ke konfliktům DNS ve stejné virtuální síti. 

Zřizovací agenti se pro tyto image nevyžadují, ale možná budete chtít mít funkce pro zpracování rozšíření. Můžete nainstalovat agenta pro Linux, ale zakázat možnost zřizování. I když nepotřebujete zřizovacího agenta, image musí splňovat [požadavky](./create-upload-generic.md)  pro Image Azure.


## <a name="image-storage-options"></a>Možnosti úložiště obrázků
Při načítání image pro Linux máte dvě možnosti:

- Spravované image pro jednoduché vytváření virtuálních počítačů ve vývojovém a testovacím prostředí.
- [Galerie sdílených imagí](../shared-image-galleries.md) pro vytváření a sdílení imagí v měřítku.


### <a name="managed-images"></a>Spravované image

Spravované image se dají použít k vytvoření několika virtuálních počítačů, ale mají hodně omezení. Spravované Image je možné vytvářet jenom z zobecněného zdroje (virtuálního počítače nebo virtuálního pevného disku). Dají se použít jenom k vytvoření virtuálních počítačů ve stejné oblasti a nedají se sdílet mezi předplatnými a klienty.

Spravované Image je možné použít pro vývojová a testovací prostředí, kde potřebujete několik jednoduchých zobecněných imagí, které se dají používat v rámci jedné oblasti a předplatného. 

### <a name="azure-shared-image-gallery-sig"></a>Galerie sdílených imagí Azure (SIG)

[Galerie sdílených imagí](../shared-image-galleries.md) se doporučují pro vytváření, správu a sdílení imagí ve velkém měřítku. Galerie sdílených imagí vám pomůžou sestavovat strukturu a organizaci kolem imagí.  

- Podpora zobecněných i specializovaných imagí.
- Podpora imagí pro Image generace 1 a 2.
- Globální replikace imagí.
- Správa verzí a seskupování imagí pro snadnější správu.
- Vysoce dostupné image s zónou redundantního úložiště (ZRS), v oblastech, které podporují Zóny dostupnosti. ZRS nabízí lepší odolnost proti chybám v rámci oblast.
- Sdílení mezi předplatnými a dokonce i mezi klienty služby Active Directory (AD), a to pomocí Azure RBAC.
- Škálování nasazení pomocí replik imagí v jednotlivých oblastech.

Na nejvyšší úrovni vytvoříte SIG a skládá se z těchto:
- Definice obrázků – jedná se o kontejnery, které obsahují skupiny imagí.
- Verze image – jedná se o skutečné obrázky.



## <a name="hyper-v-generation"></a>Generace technologie Hyper-V

Azure podporuje technologie Hyper-V Generation 1 (Gen1) a generaci 2 (Gen2), Gen2 je nejnovější generace a nabízí další funkce oproti Gen1. Například: zvýšená paměť, rozšíření Intel software Guard (Intel SGX) a virtualizovaná trvalá paměť (vPMEM). Virtuální počítače generace 2 s místním prostředím obsahují některé funkce, které ještě nejsou v Azure podporované. Další informace najdete v části funkce a možnosti. Další informace najdete v tomto [článku](../generation-2.md). Vytvářejte image Gen2, pokud požadujete další funkce.

Pokud stále potřebujete vytvořit vlastní image, ujistěte se, že splňuje požadavky na [Image](./create-upload-generic.md)a nahrává do Azure. Požadavky na distribuci specifické pro distribuci:


- [Distribuce založené na CentOS](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES a openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)


## <a name="next-steps"></a>Další kroky

Naučte se vytvořit [sdílenou galerii imagí](tutorial-custom-images.md).