---
title: Pochopení sdílených IP adres v Azure DevTest Labs | Microsoft Docs
description: Přečtěte si, jak Azure DevTest Labs používá sdílené IP adresy k minimalizaci veřejných IP adres potřebných pro přístup k virtuálním počítačům v testovacím prostředí.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 36a5da1b2b6252d0adb480a622c461b33425e675
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85484090"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Pochopení sdílených IP adres v Azure DevTest Labs

Azure DevTest Labs umožňuje testovacím počítačům sdílet stejnou veřejnou IP adresu, aby se minimalizoval počet veřejných IP adres potřebných pro přístup k jednotlivým virtuálním počítačům testovacího prostředí.  Tento článek popisuje, jak fungují sdílené IP adresy a jejich související možnosti konfigurace.

## <a name="shared-ip-setting"></a>Nastavení sdílených IP adres

Když vytváříte testovací prostředí, vytvoří se v podsíti virtuální sítě.  Ve výchozím nastavení je tato podsíť vytvořená s **možnost Povolit sdílenou veřejnou IP adresu** nastavenou na *Ano*.  Tato konfigurace vytvoří jednu veřejnou IP adresu pro celou podsíť.  Další informace o konfiguraci virtuálních sítí a podsítí najdete v tématu [Konfigurace virtuální sítě v Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Nová podsíť testovacího prostředí](media/devtest-lab-shared-ip/lab-subnet.png)

U stávajících cvičení můžete tuto možnost povolit tak, že vyberete **Konfigurace a zásady > virtuální sítě**. Pak ze seznamu vyberte virtuální síť a pro vybranou podsíť zvolte **Povolit sdílenou veřejnou IP adresu** . Tuto možnost můžete v jakémkoli testovacím prostředí zakázat i v případě, že nechcete sdílet veřejnou IP adresu mezi testovacími počítači.

Všechny virtuální počítače vytvořené v tomto testovacím prostředí ve výchozím nastavení používají sdílenou IP adresu.  Při vytváření virtuálního počítače se toto nastavení dá pozorovat na stránce **Upřesnit nastavení** v části **Konfigurace IP adresy**.

![Nový virtuální počítač](media/devtest-lab-shared-ip/new-vm.png)

- **Sdílená:** Všechny virtuální počítače vytvořené jako **sdílené** jsou umístěné do jedné skupiny prostředků (RG). K tomuto RG je přiřazena jedna IP adresa a všechny virtuální počítače v RG budou tuto IP adresu používat.
- **Veřejné:** Každý virtuální počítač, který vytvoříte, má svoji vlastní IP adresu a vytvoří se ve vlastní skupině prostředků.
- **Privátní:** Každý virtuální počítač, který vytvoříte, používá privátní IP adresu. K tomuto virtuálnímu počítači se nemůžete připojit přímo z Internetu pomocí vzdálené plochy.

Pokaždé, když se do podsítě přidá virtuální počítač se zapnutou sdílenou IP adresou, DevTest Labs automaticky přidá virtuální počítač do nástroje pro vyrovnávání zatížení a přiřadí číslo portu TCP na veřejné IP adrese, které se přesměruje na port RDP na virtuálním počítači.  

## <a name="using-the-shared-ip"></a>Pomocí sdílené IP adresy

- **Uživatelé systému Linux:** SSH k virtuálnímu počítači pomocí IP adresy nebo plně kvalifikovaného názvu domény následovaný dvojtečkou a portem. Například na následujícím obrázku je adresa RDP pro připojení k virtuálnímu počítači `mydevtestlab597975021002.eastus.cloudapp.azure.com:50661` .

  ![Příklad virtuálního počítače](media/devtest-lab-shared-ip/vm-info.png)

- **Uživatelé systému Windows:** Výběrem tlačítka **připojit** na Azure Portal Stáhněte předem NAKONFIGUROVANÝ soubor RDP a přejděte k virtuálnímu počítači.

## <a name="next-steps"></a>Další kroky

* [Definování zásad testovacího prostředí v Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Konfigurace virtuální sítě v Azure DevTest Labs](devtest-lab-configure-vnet.md)





