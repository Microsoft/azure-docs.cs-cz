---
title: Povolit vzdálenou plochu pro Linux v Azure Lab Services | Microsoft Docs
description: Přečtěte si, jak povolit vzdálenou plochu pro virtuální počítače se systémem Linux v testovacím prostředí v Azure Lab Services.
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
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: c67ca111bf87c9dbfa69c93149d29dbd32767fbd
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350755"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Povolení vzdálené plochy pro virtuální počítače se systémem Linux v testovacím prostředí v Azure Lab Services
V tomto článku se dozvíte, jak provádět následující úlohy:

- Povolit virtuální počítač vzdálené plochy pro Linux
- Jak se učitel může připojit k virtuálnímu počítači šablony prostřednictvím Připojení ke vzdálené ploše (RDP)

## <a name="enable-remote-desktop-for-linux-vm"></a>Povolit virtuální počítač vzdálené plochy pro Linux
Při vytváření testovacího prostředí můžou učitelé povolit **připojení ke vzdálené ploše** pro Image **Linux** . Možnost **povolit připojení ke vzdálené ploše** se zobrazí, když je pro šablonu vybraná image pro Linux. Když je tato možnost povolená, učitelé se můžou připojit k virtuálnímu počítači šablony a virtuálním počítačům studenta přes RDP (Vzdálená plocha). 

![Povolení připojení ke vzdálené ploše pro bitovou kopii operačního systému Linux](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

V poli **povolit připojení ke vzdálené plošeovou** zprávu vyberte **pokračovat u možnosti Vzdálená plocha**. 

![Povolení připojení ke vzdálené ploše pro bitovou kopii operačního systému Linux](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> Povolení **připojení ke vzdálené ploše** otevírá jenom port **RDP** na počítačích se systémem Linux. Pokud je už v imagi virtuálního počítače nainstalovaný a nakonfigurovaný protokol RDP (například: Ubuntu Data Science Virtual Machine image) se můžete k virtuálním počítačům a studentům připojit pomocí protokolu RDP, aniž byste museli postupovat podle dalších kroků.
> 
> Pokud není v imagi virtuálního počítače nainstalovaný a nakonfigurovaný RDP, budete se muset poprvé připojit k počítači se systémem Linux a nainstalovat balíčky RDP a GUI, abyste se vy a studenti mohli připojit k počítači se systémem Linux pomocí protokolu RDP později. Další informace najdete v tématu [instalace a konfigurace vzdálené plochy pro připojení k virtuálnímu počítači se systémem Linux v Azure](../../virtual-machines/linux/use-remote-desktop.md). Potom obrázek publikujete, aby se studenti mohli připojit k virtuálním počítačům s Linuxem pro studenty. 

## <a name="supported-operating-systems"></a>Podporované operační systémy
V současné době se připojení ke vzdálené ploše podporuje pro následující operační systémy:

- openSUSE, přestupné 42,3
- CentOS-based 7,5
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="teachers-connecting-to-the-template-vm-using-rdp"></a>Učitelé, kteří se připojují k virtuálnímu počítači šablony pomocí protokolu RDP
Učitelé se musí nejdřív připojit k virtuálnímu počítači šablony pomocí SSH a nainstalovat na něm balíčky RDP a GUI. Učitelé pak můžou pomocí následujících kroků připojit se k virtuálním počítačům se systémem Linux pomocí protokolu RDP: 

V okamžiku vytvoření testovacího prostředí se zobrazí možnost **Vzdálená plocha** , která se připojí k virtuálnímu počítači šablony. 

![Připojení k šabloně prostřednictvím protokolu RDP v době jejího vytvoření](../media/how-to-enable-remote-desktop-linux/connect-at-creation.png)

Po vytvoření testovacího prostředí a spuštění šablony virtuálního počítače se na domovské stránce testovacího prostředí zobrazí možnost **Vzdálená plocha** . Spusťte šablonu VM, pokud už není spuštěná. 

![Připojení k šabloně přes protokol RDP po vytvoření testovacího prostředí](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 

Další informace o připojení k virtuálnímu počítači pomocí protokolu SSH nebo RDP najdete v tématu [připojení pomocí protokolu SSH nebo RDP] ((#connect-using-SSH-nebo-RDP). 

## <a name="teachers-connecting-to-a-student-vm-using-rdp"></a>Učitelé připojující se k virtuálnímu počítači studenta pomocí protokolu RDP
Učitel/profesor se může připojit k virtuálnímu počítači studenta tak, že přepne na zobrazení **Virtual Machines** a vybere ikonu **připojit** . Před tím učitelům musí **publikovat** image šablony s nainstalovanými balíčky RDP a GUI. 

![Učitelé připojující se k virtuálnímu počítači studenta](../media/how-to-enable-remote-desktop-linux/teacher-connect-to-student-vm.png)

Další informace o připojení k virtuálnímu počítači pomocí protokolu SSH nebo RDP najdete v tématu [připojení pomocí protokolu SSH nebo RDP] ((#connect-using-SSH-nebo-RDP). 

## <a name="connect-using-ssh-or-rdp"></a>Připojení pomocí protokolu SSH nebo RDP
Pokud vyberete možnost **SSH** , zobrazí se následující dialogové okno **připojit k virtuálnímu počítači** :  

![Připojovací řetězec SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Kliknutím na tlačítko **Kopírovat** vedle textového pole ho zkopírujte do schránky. Uložte připojovací řetězec SSH. K připojení k virtuálnímu počítači použijte tento připojovací řetězec [](https://www.putty.org/)z terminálu SSH (jako např.).

Pokud vyberete možnost **RDP** , na váš počítač se stáhne soubor RDP. Uložte ho a otevřete ho pro připojení k počítači. 

## <a name="next-steps"></a>Další kroky
Po povolení funkce připojení ke vzdálené ploše studentům se můžou studenti připojit ke svým virtuálním počítačům přes RDP/SSH. Další informace najdete v tématu [použití vzdálené plochy pro virtuální počítače se systémem Linux v prostředí učebny](how-to-use-remote-desktop-linux-student.md). 