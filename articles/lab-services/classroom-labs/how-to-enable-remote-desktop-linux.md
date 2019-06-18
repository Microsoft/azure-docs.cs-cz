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
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 389d467bd9672743d4a086e8a1c505fb0366dba7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237126"
---
# <a name="enable-and-use-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Povolení a používání vzdálené plochy pro virtuální počítače s Linuxem v testovacím prostředí v Azure Lab Services
Tento článek ukazuje, jak provádět následující úlohy:

- Povolení vzdálené plochy pro virtuální počítač s Linuxem
- Jak učitelů připojit do šablony virtuálního počítače přes připojení RDP (Remote Desktop).
- Jak studenti připojit k student virtuálnímu počítači pomocí protokolu RDP

## <a name="enable-remote-desktop-for-linux-vm"></a>Povolení vzdálené plochy pro virtuální počítač s Linuxem
Při vytváření testovacího prostředí, můžete povolit učitelé **připojení ke vzdálené ploše** pro **Linux** bitové kopie. **Povolit připojení ke vzdálené ploše** možnost se zobrazí, když je do image Linuxu pro šablonu vybrali. Pokud je tato možnost povolena, učitelé připojit k šablony virtuálního počítače a student virtuálních počítačů pomocí protokolu RDP (Vzdálená plocha). 

![Povolit připojení ke vzdálené ploše pro image Linuxu](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

Na **povolení připojení ke vzdálené ploše** okno se zprávou, vyberte **pokračovat přes vzdálenou plochu**. 

![Povolit připojení ke vzdálené ploše pro image Linuxu](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

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

Další informace o připojení k virtuálnímu počítači pomocí SSH nebo RDP najdete v tématu [připojit pomocí SSH nebo RDP]((#connect-using-ssh-or-rdp). 

## <a name="teachers-connecting-to-a-student-vm-using-rdp"></a>Učitelé propojíte student virtuálních počítačů pomocí protokolu RDP
Učitelů/profesor se můžete připojit k student virtuálního počítače přepnutím na **virtuálních počítačů** zobrazení a výběr **připojení** ikonu. Před tímto, učitelé musí **publikovat** image šablony pomocí protokolu RDP a grafickým uživatelským rozhraním balíčků na něm nainstalován. 

![Učitelé propojíte student virtuálního počítače](../media/how-to-enable-remote-desktop-linux/teacher-connect-to-student-vm.png)

Další informace o připojení k virtuálnímu počítači pomocí SSH nebo RDP najdete v tématu [připojit pomocí SSH nebo RDP]((#connect-using-ssh-or-rdp). 

## <a name="students-connecting-to-the-student-vm"></a>Studenti propojíte student virtuálního počítače
Student může protokol RDP v jejich virtuálních počítačů s Linuxem po vlastník testovacího prostředí (učitelů/profesor) **publikuje** šablony virtuálního počítače pomocí protokolu RDP a grafickým uživatelským rozhraním balíčky nainstalované na počítači. Postup je následující: 

1. Když student přihlásí k portálu testovacích prostředí přímo (`https://labs.azure.com`) nebo pomocí odkazu registrace (`https://labs.azure.com/register/<registrationCode>`), se zobrazí dlaždice pro každého studenta testovacího prostředí má přístup k. 
2. Na dlaždici, vyberte **Start** Pokud virtuální počítač je zastavený. 
3. Vyberte **Connect** (Připojit). Zobrazí se dvě možnosti, jak se připojit k virtuálnímu počítači: **SSH** a **vzdálené plochy**.

    ![Student virtuálního počítače – možnosti připojení](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Připojte se přes SSH nebo RDP
Pokud vyberete **SSH** možnost, zobrazí se následující **připojit k virtuálnímu počítači** dialogové okno:  

![Připojovací řetězec SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Vyberte **kopírování** tlačítko vedle textového pole zkopírujte do schránky. Uložte připojovací řetězec SSH. Použít tento připojovací řetězec z terminálu SSH (například [Putty](https://www.putty.org/)) pro připojení k virtuálnímu počítači.

Pokud vyberete **RDP** možnost, soubor RDP se stáhne do vašeho počítače. Uložte ho a otevřete jej pro připojení k počítači. 

## <a name="next-steps"></a>Další postup
Viz následující články:

- [Jako správce vytvářet a spravovat účty pro testovací prostředí](how-to-manage-lab-accounts.md)
- [Jako vlastník testovacího prostředí vytvoření a správa testovacích prostředí](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako uživatel, který testovací prostředí přístup k testovacím prostředím v učebnách](how-to-use-classroom-lab.md)

