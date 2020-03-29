---
title: Principy sdílených IP adres v azure devtest labs | Dokumenty společnosti Microsoft
description: Zjistěte, jak Azure DevTest Labs používá sdílené IP adresy k minimalizaci veřejných IP adres potřebných pro přístup k virtuálním počítačům testovacího prostředí.
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
ms.date: 05/12/2019
ms.author: spelluru
ms.openlocfilehash: f7c9feedddab1aea031cb3a8879e868aae04df00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65236856"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Principy sdílených IP adres v Azure DevTest Labs

Azure DevTest Labs umožňuje testovacím virtuálním počítačům sdílet stejnou veřejnou IP adresu, aby se minimalizoval počet veřejných IP adres potřebných pro přístup k jednotlivým virtuálním počítačům testovacího prostředí.  Tento článek popisuje, jak sdílené IP adresy fungují a jejich související možnosti konfigurace.

## <a name="shared-ip-setting"></a>Nastavení sdílené IP adresy

Když vytvoříte testovací prostředí, vytvoří se v podsíti virtuální sítě.  Ve výchozím nastavení je tato podsíť vytvořena s **hodnotou Povolit sdílenou veřejnou adresu IP** nastavenou na *hodnotu Ano*.  Tato konfigurace vytvoří jednu veřejnou IP adresu pro celou podsíť.  Další informace o konfiguraci virtuálních sítí a podsítí najdete [v tématu Konfigurace virtuální sítě v laboratořích Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Nová podsíť testovacího prostředí](media/devtest-lab-shared-ip/lab-subnet.png)

Pro existující testovací prostředí můžete tuto možnost povolit výběrem **možnosti Konfigurace a zásady > virtuálních sítí**. Potom vyberte virtuální síť ze seznamu a zvolte **POVOLIT SDÍLENOu VEŘEJNOu IP adresu** pro vybranou podsíť. Tuto možnost můžete také zakázat v libovolném testovacím prostředí, pokud nechcete sdílet veřejnou IP adresu mezi virtuálními počítačůmi testovacího prostředí.

Všechny virtuální uživatele vytvořené v této laboratoři výchozí použití sdílené IP adresy.  Při vytváření virtuálního počítače lze toto nastavení sledovat na stránce **Upřesnit nastavení** v části **Konfigurace IP adresy**.

![Nový virtuální virtuální město](media/devtest-lab-shared-ip/new-vm.png)

- **Sdíleno:** Všechny virtuální uživatele vytvořené jako **Sdílené** se umístí do jedné skupiny prostředků (RG). Pro tento RG je přiřazena jedna ADRESA IP a všechny virtuální společnosti v RG budou tuto IP adresu používat.
- **Veřejné:** Každý vytvořený virtuální virtuální virtuální podnik má vlastní IP adresu a je vytvořen ve vlastní skupině prostředků.
- **Soukromé:** Každý virtuální virtuální počítačů, který vytvoříte, používá privátní IP adresu. K tomuto virtuálnímu počítači se nemůžete připojit přímo z internetu pomocí vzdálené plochy.

Vždy, když je do podsítě přidán virtuální počítač s povolenou sdílenou IP adresou, DevTest Labs automaticky přidá virtuální počítač do ekvizéry a přiřadí číslo portu TCP na veřejnou IP adresu a přepošle se na port RDP na virtuálním počítači.  

## <a name="using-the-shared-ip"></a>Použití sdílené IP adresy

- **Uživatelé Linuxu:** SSH do virtuálního počítačů pomocí IP adresy nebo plně kvalifikovaný název domény, následovaný dvojtečkou, následovaný portem. Například na obrázku níže je `mydevtestlab597975021002.eastus.cloudapp.azure.com:50661`adresa RDP pro připojení k virtuálnímu virtuálnímu virtuálnímu virtuálnímu zařízení .

  ![Příklad virtuálního virtuálního mísy](media/devtest-lab-shared-ip/vm-info.png)

- **Uživatelé systému Windows:** Vyberte tlačítko **Připojit** na webu Azure Portal, abyste si stáhli předem nakonfigurovaný soubor RDP a měli přístup k virtuálnímu počítači.

## <a name="next-steps"></a>Další kroky

* [Definování zásad testovacího prostředí v laboratořích Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Konfigurace virtuální sítě v Azure DevTest Labs](devtest-lab-configure-vnet.md)





