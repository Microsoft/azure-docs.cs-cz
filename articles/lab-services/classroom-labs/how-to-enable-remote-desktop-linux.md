---
title: Povolení vzdálené plochy pro Linux v Azure Lab Services | Dokumentace Microsoftu
description: Zjistěte, jak povolit vzdálenou plochu pro virtuální počítače s Linuxem v testovacím prostředí v Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 6985bd0bbae858ad258e723ef4d6d6d687b2c86e
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60005345"
---
# <a name="enable-and-use-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Povolení a používání vzdálené plochy pro virtuální počítače s Linuxem v testovacím prostředí v Azure Lab Services
Tento článek ukazuje, jak provádět následující úlohy:

- Povolení vzdálené plochy pro virtuální počítač s Linuxem
- Jak učitelů připojit do šablony virtuálního počítače přes připojení RDP (Remote Desktop).
- Jak studenti připojit k student virtuálnímu počítači pomocí protokolu RDP

## <a name="enable-remote-desktop-for-linux-vm"></a>Povolení vzdálené plochy pro virtuální počítač s Linuxem
Při vytváření testovacího prostředí, můžete povolit učitelé **připojení ke vzdálené ploše** pro **Linux** bitové kopie. **Povolit připojení ke vzdálené ploše** možnost se zobrazí, když je do image Linuxu pro šablonu vybrali. Pokud je tato možnost povolena, učitelé připojit k šablony virtuálního počítače a student virtuálních počítačů pomocí protokolu RDP (Vzdálená plocha). 

![Povolit připojení ke vzdálené ploše pro image Linuxu](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

> [!IMPORTANT] 
> Povolení **připojení ke vzdálené ploše** pouze se otevře **RDP** port na počítačích s Linuxem. Jako učitel, připojte se k počítači s Linuxem pomocí protokolu SSH poprvé a instalaci balíčků pomocí protokolu RDP a grafickým uživatelským rozhraním, aby mohl připojit k počítači s Linuxem pomocí protokolu RDP později. Potom můžete **publikovat** bitovou kopii, studenti mohli RDP v pro virtuální počítače s Linuxem studentů. 

## <a name="supported-operating-systems"></a>Podporované operační systémy
Připojení ke vzdálené ploše v současné době se podporuje pro následující operační systémy:

- openSUSE skok 42.3
- Založené na centOS 7.5
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="teachers-connecting-to-the-template-vm-using-rdp"></a>Učitelé propojení do šablony virtuálního počítače pomocí protokolu RDP
Učitelé se musí připojit k šabloně virtuálního počítače pomocí protokolu SSH nejdříve a nainstalovat balíčky pomocí protokolu RDP a grafickým uživatelským rozhraním. Učitelů pak můžete použít následující kroky pro připojení virtuálních počítačů s Linuxem pomocí protokolu RDP: 

Zobrazí **vzdálené plochy** možností pro připojení k šabloně virtuálního počítače v době vytvoření testovacího prostředí. 

![Připojení k šabloně přes protokol RDP v době vytvoření](../media/how-to-enable-remote-desktop-linux/connect-at-creation.png)

Zobrazí **vzdálené plochy** je spuštěná možnost na domovské stránce testovacího prostředí po vytvoření testovacího prostředí a šablony virtuálního počítače. Spusťte šablonu virtuálního počítače, pokud již není spuštěný. 

![Připojení k šabloně přes protokol RDP, po vytvoření testovacího prostředí](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 

Když vyberete **RDP** možnost, stáhne soubor RDP. Otevřete jej pro připojení k počítači s Linuxem. 

## <a name="teachers-connecting-to-a-student-vm-using-rdp"></a>Učitelé propojíte student virtuálních počítačů pomocí protokolu RDP
Vlastník testovacího prostředí (učitelů/profesor) se můžete připojit k student virtuálního počítače přepnutím na **virtuálních počítačů** zobrazení a výběr **připojení** ikonu. Před tímto, učitelé musí **publikovat** image šablony pomocí protokolu RDP a grafickým uživatelským rozhraním balíčků na něm nainstalován. 

![Učitelé propojíte student virtuálního počítače](../media/how-to-enable-remote-desktop-linux/teacher-connect-to-student-vm.png)

## <a name="students-connecting-to-the-student-vm"></a>Studenti propojíte student virtuálního počítače
Student může protokol RDP v jejich virtuálních počítačů s Linuxem po vlastník testovacího prostředí (učitelů/profesor) **publikuje** šablony virtuálního počítače pomocí protokolu RDP a grafickým uživatelským rozhraním balíčky nainstalované na počítači. Postup je následující: 

1. Když student přihlásí k portálu testovacích prostředí přímo (`http://labs.azure.com`) nebo pomocí odkazu registrace (`http://labs.azure.com/register/<registrationCode>`), se zobrazí dlaždice pro každého studenta testovacího prostředí má přístup k. 
2. Na dlaždici, vyberte **Start** Pokud virtuální počítač je zastavený. 
3. Vyberte **Connect** (Připojit). Tato akce stáhne soubor RDP do vašeho počítače. Uložte ho a otevřete pro připojení k počítači s Linuxem pomocí protokolu RDP. 

    ![Stáhnout protokol RDP student virtuálního počítače –](../media/how-to-enable-remote-desktop-linux/student-rdp-download.png)

    Do virtuálního počítače s Linuxem můžete pořád připojit pomocí protokolu SSH. Vyberte **... (tři tečky)**  zobrazíte možnosti SSH. 
    
    ![Student VM - SSH](../media/how-to-enable-remote-desktop-linux/student-ssh.png)

    Zkopírujte a uložte připojovací řetězec SSH na **připojit k virtuálnímu počítači** dialogové okno. Použít tento připojovací řetězec z terminálu SSH (například [Putty](https://www.putty.org/)) pro připojení k virtuálnímu počítači. 

## <a name="next-steps"></a>Další postup
Viz následující články:

- [Jako správce vytvářet a spravovat účty pro testovací prostředí](how-to-manage-lab-accounts.md)
- [Jako vlastník testovacího prostředí vytvoření a správa testovacích prostředí](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako uživatel, který testovací prostředí přístup k testovacím prostředím v učebnách](how-to-use-classroom-lab.md)

