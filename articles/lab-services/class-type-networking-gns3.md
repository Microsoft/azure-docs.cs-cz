---
title: Nastavte síťové prostředí s Azure Lab Services a GNS3 | Microsoft Docs
description: Naučte se, jak nastavit testovací prostředí pomocí Azure Lab Services pro učení sítě pomocí GNS3.
ms.topic: article
ms.date: 01/19/2021
ms.openlocfilehash: dec5dea13d5a89536a06da45fc57d33881a9b3ad
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500510"
---
# <a name="set-up-a-lab-to-teach-a-networking-class"></a>Nastavení testovacího prostředí pro výuku síťové třídy 
V tomto článku se dozvíte, jak nastavit třídu, která se zaměřuje na to, aby studenti mohli emulovat, konfigurovat, testovat a řešit potíže s virtuálními a skutečnými sítěmi pomocí [GNS3](https://www.gns3.com/) softwaru. 

Tento článek obsahuje dvě hlavní části. První část obsahuje informace o tom, jak vytvořit prostředí učebny. Druhá část popisuje, jak vytvořit počítač s šablonou s povolenou vnořenou virtualizací a s nainstalovanou a nakonfigurovanou GNS3.

## <a name="lab-configuration"></a>Konfigurace testovacího prostředí
K nastavení tohoto testovacího prostředí potřebujete předplatné Azure, abyste mohli začít. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/). Jakmile získáte předplatné Azure, můžete buď vytvořit nový účet testovacího prostředí v Azure Lab Services nebo použít existující účet. V následujícím kurzu najdete postup vytvoření nového účtu testovacího prostředí: [kurz nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md).

Podle [tohoto kurzu](tutorial-setup-classroom-lab.md) vytvořte nové testovací prostředí a pak použijte následující nastavení:

| Velikost virtuálního počítače | Image |
| -------------------- | ----- | 
| Velký (vnořená virtualizace) | Windows 10 pro, verze 1909 |

## <a name="template-machine"></a>Počítač šablony 

Po vytvoření počítače s šablonou spusťte počítač a připojte se k němu, abyste mohli dokončit následující tři hlavní úlohy. 
 
1. Připravte počítač šablony pro vnořenou virtualizaci.
2. Nainstalujte GNS3.
3. Vytvořte vnořený virtuální počítač s GNS3 v Hyper-V.
4. Nakonfigurujte GNS3 pro použití virtuálního počítače Windows Hyper-V.
5. Přidejte vhodná zařízení.
6. Publikovat šablonu.


### <a name="prepare-template-machine-for-nested-virtualization"></a>Připravit počítač šablony pro vnořenou virtualizaci
- Postupujte podle pokynů v [tomto článku](how-to-enable-nested-virtualization-template-vm.md) a připravte svůj virtuální počítač šablony pro vnořenou virtualizaci. 

### <a name="install-gns3"></a>Nainstalovat GNS3
- Postupujte podle pokynů pro [instalaci GNS3 ve Windows](https://docs.gns3.com/docs/getting-started/installation/windows).  Nezapomeňte zahrnout instalaci **virtuálního počítače s GNS3** do dialogu komponenty, viz níže.

![SelectGNS3vm](./media/class-type-networking-gns3/gns3-select-vm.png)

Nakonec se dostanete k výběru virtuálního počítače s GNS3. Ujistěte se, že jste vybrali možnost **technologie Hyper-V** .

![SelectHyperV](./media/class-type-networking-gns3/gns3-vm-hyper-v.png)

  Tato možnost stáhne skript prostředí PowerShell a soubory VHD k vytvoření virtuálního počítače s GNS3 ve Správci technologie Hyper-V. Pokračujte v instalaci s použitím výchozích hodnot. **Po dokončení instalace GNS3 spusťte**.

### <a name="create-gns3-vm"></a>Vytvoření virtuálního počítače s GNS3
Po dokončení instalace se soubor zip **"GNS3.VM.Hyper-V.2.2.17.zip"** stáhne do stejné složky jako instalační soubor, který obsahuje jednotky a skript prostředí PowerShell pro vytvoření virtuálního počítače s technologií Hyper-V.
- **Extrahujte vše** na GNS3.VM.Hyper-V.2.2.17.zip.  Tato akce extrahuje jednotky a skript PowerShellu, aby se vytvořil virtuální počítač.
- **Spusťte prostředí PowerShell** na skriptu PowerShellu "create-vm.ps1" tím, že kliknete pravým tlačítkem na soubor.
- Může se zobrazit žádost o změnu zásady spouštění. Zadáním "Y" spusťte skript.

![PSExecutionPolicy](./media/class-type-networking-gns3/powershell-execution-policy-change.png)

- Po dokončení skriptu si můžete ověřit, že se virtuální počítač GNS3 VM vytvořil ve Správci technologie Hyper-V.

### <a name="configure-gns3-to-use-hyper-v-vm"></a>Konfigurace GNS3 pro použití virtuálního počítače Hyper-V
Teď, když je nainstalovaná GNS3 a přidal se virtuální počítač GNS3, spusťte GNS3, aby se dva vzájemně propojí.  [Automaticky se spustí Průvodce instalací GNS3.](https://docs.gns3.com/docs/getting-started/setup-wizard-gns3-vm#local-gns3-vm-setup-wizard).  
- Použijte **spuštění zařízení z virtuálního počítače.** .  Použijte výchozí hodnoty pro zbytek průvodce, dokud nepřejdete na **Nástroj VMware vmrun, který se nedá najít.** .

![VMWareError](./media/class-type-networking-gns3/gns3-vmware-vmrun-tool-not-found.png)

- Klikněte na **tlačítko OK** a **zrušte** výběr z průvodce.
- Pokud chcete dokončit připojení k virtuálnímu počítači Hyper-V, otevřete   ->    ->  **virtuální počítač** upravit předvolby GNS3 a vyberte **Povolit virtuální počítač GNS3** a vyberte možnost **technologie Hyper-v** .
 
![EnableGNS3VMs](./media/class-type-networking-gns3/gns3-preference-vm.png)

### <a name="add-appropriate-appliances"></a>Přidat vhodná zařízení

V tomto okamžiku budete chtít přidat vhodná [zařízení pro třídu.](https://docs.gns3.com/docs/using-gns3/beginners/install-from-marketplace)

### <a name="publish-template"></a>Publikování šablony

Teď, když je virtuální počítač šablony správně nastavený a připravený k publikování, je potřeba zkontrolovat pár klíčových bodů.
- Ujistěte se, že je virtuální počítač GNS3 vypnutý nebo vypnutý.  Publikování v době, kdy je virtuální počítač stále spuštěný, bude mít za poškozený virtuální počítač.
- Zavřete GNS3 dolů, publikování během a běhu může vést k nežádoucím vedlejším účinkům.
- Vyčistěte instalační soubory nebo jiné nepotřebné soubory.

## <a name="cost"></a>Náklady  

Pokud byste chtěli odhadnout náklady na toto testovací prostředí, můžete použít následující příklad: 
 
Pro třídu 25 studentů s 20 hodinami plánovaného času třídy a 10 hodin pro domácí úlohy nebo přiřazení by cena za testovací prostředí byla: 

25 Students * (20 + 10) hod. × 84 jednotek testovacího prostředí × 0,01 USD za hodinu = 630 USD. 

**Důležité informace:** Odhad nákladů slouží pouze jako příklad pro účely.  Aktuální podrobnosti o cenách najdete v tématu [Azure Lab Services ceny](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Závěr
Tento článek vás vás provedl postupem, jak vytvořit testovací prostředí pro konfiguraci sítě pomocí GNS3.

## <a name="next-steps"></a>Další kroky
Další kroky jsou běžné pro nastavení testovacího prostředí:

- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavit plán](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Odkazy na registraci e-mailu studentům](how-to-configure-student-usage.md#send-invitations-to-users).