---
title: Principy sdílených IP adres ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak Azure DevTest Labs používá sdílených IP adres pro minimalizaci veřejné IP adresy, které jsou nutné pro přístup k testovacího prostředí virtuálních počítačů.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: e7080901118dde33ed07c8a80f254b9b0d2e221c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622991"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Principy sdílených IP adres ve službě Azure DevTest Labs

Azure DevTest Labs umožňuje virtuální počítače testovacího prostředí, sdílet stejnou veřejnou IP adresu minimalizovat počet veřejných IP adres, které jsou nutné pro přístup k jednotlivým virtuálním počítačům testovacího prostředí.  Tento článek popisuje, jak sdílených pracovních IP adresy a jejich související možnosti konfigurace.

## <a name="shared-ip-setting"></a>Sdílet nastavení protokolu IP

Při vytváření testovacího prostředí se nachází v podsíti virtuální sítě.  Ve výchozím nastavení, tato podsíť se vytvoří s **povolit sdílenou veřejnou IP adresu** nastavena na *Ano*.  Tato konfigurace vytvoří jednu veřejnou IP adresu pro celou podsíť.  Další informace o konfiguraci virtuální sítě a podsítě, naleznete v tématu [konfigurace virtuální sítě ve službě Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Nová podsíť testovacího prostředí](media/devtest-lab-shared-ip/lab-subnet.png)

Pro existující testovací prostředí, můžete tuto možnost povolíte tak, že vyberete **konfigurace a zásad > virtuálních sítí**. Potom vyberte virtuální síť v seznamu a zvolte **povolení SDÍLENÉ veřejné IP adresy** pro vybranou podsíť. Tato možnost v jakékoli testovacího prostředí můžete také zakázat Pokud už nechcete sdílet veřejnou IP adresu mezi testovacím virtuálním počítačům.

Všechny virtuální počítače vytvořené v toto výchozí nastavení testovacího prostředí pomocí sdílených IP.  Při vytváření virtuálního počítače, toto nastavení může být dodržen v **upřesňující nastavení** okně v části **konfiguraci IP adresy**.

![Nový virtuální počítač](media/devtest-lab-shared-ip/new-vm.png)

- **Sdílené:** Všechny virtuální počítače vytvořené jako **Shared** se umístí do jedné skupiny prostředků (RG). Jedna IP adresa se přiřadí pro, který se skupinami prostředků a všechny virtuální počítače v skupinami prostředků použijte tuto IP adresu.
- **Veřejná:** Každý virtuální počítač, který vytvoříte má svou vlastní IP adresu a je vytvořen ve vlastní skupině prostředků.
- **Privátní:** Každý virtuální počítač, který vytvoříte pomocí privátní IP adresu. Nebudete moct připojit k tomuto virtuálnímu počítači přímo z Internetu pomocí vzdálené plochy.

Pokaždé, když virtuální počítač pomocí sdílených IP protokol povolen přidá k podsíti, DevTest Labs automaticky přidá virtuální počítač ke službě Vyrovnávání zatížení a přiřadí číslo portu TCP na veřejnou IP adresu, předávání k portu RDP na virtuálním počítači.  

## <a name="using-the-shared-ip"></a>Pomocí sdílených IP adresy

- **Uživatelé Linuxu:** Připojte přes SSH k virtuálnímu počítači s použitím IP adresy nebo plně kvalifikovaný název domény, za nímž následuje dvojtečka, za nímž následuje port. Například na následujícím obrázku je adresa protokolu RDP pro připojení k virtuálnímu počítači `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686`.

  ![Příkladu s virtuálním Počítačem](media/devtest-lab-shared-ip/vm-info.png)

- **Uživatelé Windows:** Vyberte **připojit** tlačítko na webu Azure portal ke stažení předem nakonfigurovaný soubor RDP a přístup k virtuálnímu počítači.

## <a name="next-steps"></a>Další postup

* [Definice zásad testovacího prostředí ve službě Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Konfigurace virtuální sítě ve službě Azure DevTest Labs](devtest-lab-configure-vnet.md)





