---
title: Povolení vzdálené plochy pro Linux ve službě Azure Lab Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak povolit vzdálenou plochu pro virtuální počítače s Linuxem v testovacím prostředí ve službě Azure Lab Services.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: cb9a3e2b9ddcd0f74bfa4978f0bc3f4eb0688257
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270859"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Povolení vzdálené plochy pro virtuální počítače s Linuxem v testovacím prostředí ve službě Azure Lab Services
Tento článek ukazuje, jak provést následující úkoly:

- Povolení vzdálené plochy pro virtuální počítač s Linuxem
- Jak se učitel může připojit k virtuálnímu počítači šablony prostřednictvím připojení ke vzdálené ploše (RDP).

## <a name="enable-remote-desktop-for-linux-vm"></a>Povolení vzdálené plochy pro virtuální počítač s Linuxem
Během vytváření testovacího prostředí mohou učitelé povolit **připojení ke vzdálené ploše** pro bitové kopie **Linuxu.** Možnost **Povolit připojení ke vzdálené ploše** se zobrazí, když je pro šablonu vybrán obrázek Linuxu. Pokud je tato možnost povolena, učitelé se můžou připojit k virtuálním počítačům šablony a virtuálním počítačům studentů prostřednictvím RDP (Vzdálená plocha). 

![Povolení připojení ke vzdálené ploše pro bitovou kopii Linuxu](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

V poli **Povolení připojení ke vzdálené ploše** vyberte pokračovat pomocí funkce **Vzdálená plocha**. 

![Povolení připojení ke vzdálené ploše pro bitovou kopii Linuxu](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> Povolení **připojení ke vzdálené ploše** otevře pouze port **RDP** na počítačích s Linuxem. Pokud je RDP už nainstalovaný a nakonfigurovaný na bitové kopii virtuálního počítače (například: Image virtuálního počítače datové vědy Ubuntu), můžete se k virtuálním počítačům připojit prostřednictvím RDP bez dalších kroků.
> 
> Pokud bitová kopie virtuálního počítače nemá nainstalovaný a nakonfigurovaný RDP, musíte se poprvé připojit k počítači s Linuxem pomocí SSH a nainstalovat balíčky RDP a GUI, abyste se vy/studenti mohli připojit k počítači s Linuxem pomocí RDP později. Další informace najdete [v tématu Instalace a konfigurace vzdálené plochy pro připojení k virtuálnímu počítači SIP v Azure](../../virtual-machines/linux/use-remote-desktop.md). Potom publikujete obrázek tak, aby studenti mohou RDP do virtuálních počítačů studentlinux. 

## <a name="supported-operating-systems"></a>Podporované operační systémy
V současné době je připojení ke vzdálené ploše podporováno pro následující operační systémy:

- openSUSE Skok 42,3
- Na základě CentOS 7,5
- Debian 9 "Roztáhnout"
- Ubuntu Server 16.04 LTS

## <a name="connect-to-the-template-vm"></a>Připojení k šabloně virtuálního počítače 
Učitelé se musí nejprve připojit k virtuálnímu počítači šablony pomocí SSH a nainstalovat na něj balíčky RDP a GUI. Učitelé pak mohou pomocí rdp připojit k virtuálnímu počítače šablony: 

1. Pokud na panelu nástrojů vidíte **přizpůsobit šablonu,** vyberte ji. Potom vdialogovéce **Přizpůsobit šablonu** vyberte **Pokračovat.** Tato akce spustí virtuální počítače šablony.  

    ![Přizpůsobit šablonu](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. Po spuštění virtuálního počítače šablony můžete vybrat **připojit šablonu** a pak **připojit přes SSH** na panelu nástrojů. 

    ![Připojení k šabloně prostřednictvím funkce RDP po vytvoření testovacího prostředí](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. Zobrazí se následující dialogové okno **Připojit k virtuálnímu počítači.** Vyberte tlačítko **Kopírovat** vedle textového pole, chcete-li ho zkopírovat do schránky. Uložte připojovací řetězec SSH. Pomocí tohoto připojovacího řetězce z terminálu SSH (například [Putty)](https://www.putty.org/)se připojte k virtuálnímu počítači.
 
    ![Připojovací řetězec SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. Nainstalujte balíčky RDP a GUI, abyste se vy/studenti mohli připojit k počítači s Linuxem pomocí RDP později. Další informace najdete [v tématu Instalace a konfigurace vzdálené plochy pro připojení k virtuálnímu počítači SIP v Azure](../../virtual-machines/linux/use-remote-desktop.md). Potom publikujete obrázek tak, aby studenti mohou RDP do virtuálních počítačů studentlinux.
5. Po instalaci těchto balíčků můžete použít **šablonu Připojit k** na panelu nástrojů a pak vybrat **Připojit pomocí rdp** pro připojení k virtuálnímu počítači šablony prostřednictvím rdp. Uložte soubor RDP a použijte jej k připojení k virtuálnímu virtuálnímu počítače šablony prostřednictvím rdp. 

## <a name="next-steps"></a>Další kroky
Poté, co instruktor povolil funkci připojení ke vzdálené ploše, mohou se studenti připojit ke svým virtuálním počítačům prostřednictvím RDP/SSH. Další informace najdete [v tématu Použití vzdálené plochy pro virtuální počítače s Linuxem v učebně .](how-to-use-remote-desktop-linux-student.md) 