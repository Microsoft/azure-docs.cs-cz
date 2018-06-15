---
title: Pochopení sdílené IP adresy v Azure DevTest Labs | Microsoft Docs
description: Zjistěte, jak Azure DevTest Labs využívá sdílené IP adresy, chcete-li minimalizovat veřejné IP adresy, které jsou nutné pro přístup k testovacího prostředí virtuálních počítačů.
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
ms.openlocfilehash: c62f8808565022371484b936f5a2bdaba1f8900e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787378"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Pochopení sdílené IP adresy v Azure DevTest Labs

Azure DevTest Labs umožňuje testovacího prostředí virtuálních počítačů sdílet stejnou veřejnou IP adresu minimalizovat počet veřejných IP adres, které jsou nutné pro přístup k testovacího prostředí jednotlivé virtuální počítače.  Tento článek popisuje, jak sdílené pracovní IP adresy a jejich související možnosti konfigurace.

## <a name="shared-ip-setting"></a>Sdílené nastavení protokolu IP

Při vytváření testovacího prostředí se nachází v podsíti virtuální sítě.  Ve výchozím nastavení, tato podsíť je vytvořena s **povolení sdíleného veřejnou IP adresu** nastavena na *Ano*.  Tato konfigurace vytvoří jednu veřejnou IP adresu pro celou podsíť.  Další informace o konfiguraci virtuální sítě a podsítě, najdete v části [konfigurace virtuální sítě v Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Novou podsíť testovacího prostředí](media/devtest-lab-shared-ip/lab-subnet.png)

Pro existující labs, můžete tuto možnost můžete povolit výběrem **konfiguraci a zásady > virtuální sítě**. Potom vyberte virtuální síť ze seznamu a zvolte **povolit SDÍLENÉ VEŘEJNOU IP adresu** pro vybranou podsíť. Tato možnost v jakékoli prostředí lze zakázat i pokud nechcete sdílet veřejnou IP adresu v rámci prostředí virtuálních počítačů.

Všechny virtuální počítače vytvořené v toto výchozí nastavení testovacího prostředí pro použití sdílené IP.  Při vytváření virtuálního počítače, toto nastavení může být dodržen v **upřesňující nastavení** okno pod **konfiguraci IP adresy**.

![Nový virtuální počítač](media/devtest-lab-shared-ip/new-vm.png)

- **Sdílené:** všechny virtuální počítače vytvořené jako **sdílené** se umístí do jedné skupiny prostředků (RG). Jedna IP adresa je přiřazen, pro který RG a všechny virtuální počítače ve RG použije tuto IP adresu.
- **Veřejná:** každý virtuální počítač vytvoříte má svou vlastní IP adresu a je vytvořen v jeho vlastní skupiny prostředků.
- **Privátní:** každý virtuální počítač vytvoříte používá privátní IP adresy. Nebudete moci připojit k tomuto virtuálnímu počítači přímo z Internetu pomocí vzdálené plochy.

Vždy, když je virtuální počítač s sdílené IP protokol povolen přidány k podsíti, DevTest Labs automaticky přidá virtuální počítač ke službě Vyrovnávání zatížení a přiřadí číslo portu TCP na veřejnou IP adresu, předávání k portu RDP na virtuální počítač.  

## <a name="using-the-shared-ip"></a>Použití sdíleného IP

- **Uživatelé Linux:** SSH k virtuálnímu počítači pomocí IP adresy nebo plně kvalifikovaný název domény, následovaným dvojtečkou, za nímž následuje port. Například následující obrázek je adresu protokolu RDP pro připojení k virtuálnímu počítači `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686`.

  ![Příklad virtuálních počítačů](media/devtest-lab-shared-ip/vm-info.png)

- **Uživatelé systému Windows:** vyberte **Connect** tlačítko na portálu Azure ke stažení předem nakonfigurovaný soubor RDP a přístup k virtuálnímu počítači.

## <a name="next-steps"></a>Další postup

* [Definovat zásady testovacího prostředí v Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Konfigurace virtuální sítě v Azure DevTest Labs](devtest-lab-configure-vnet.md)





