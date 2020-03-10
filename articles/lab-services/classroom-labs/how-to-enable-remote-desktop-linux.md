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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: cb9a3e2b9ddcd0f74bfa4978f0bc3f4eb0688257
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360601"
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
> Povolení **připojení ke vzdálené ploše** otevírá jenom port **RDP** na počítačích se systémem Linux. Pokud je už v imagi virtuálního počítače nainstalovaný a nakonfigurovaný protokol RDP (například: Ubuntu Data Science Virtual Machine image), můžete se k virtuálním počítačům připojit pomocí protokolu RDP bez dalších kroků.
> 
> Pokud není v imagi virtuálního počítače nainstalovaný a nakonfigurovaný RDP, budete se muset poprvé připojit k počítači se systémem Linux a nainstalovat balíčky RDP a GUI, abyste se vy a studenti mohli připojit k počítači se systémem Linux pomocí protokolu RDP později. Další informace najdete v tématu [instalace a konfigurace vzdálené plochy pro připojení k virtuálnímu počítači se systémem Linux v Azure](../../virtual-machines/linux/use-remote-desktop.md). Potom obrázek publikujete, aby se studenti mohli připojit k virtuálním počítačům s Linuxem pro studenty. 

## <a name="supported-operating-systems"></a>Podporované operační systémy
V současné době se připojení ke vzdálené ploše podporuje pro následující operační systémy:

- openSUSE, přestupné 42,3
- CentOS-based 7,5
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="connect-to-the-template-vm"></a>Připojení k šabloně virtuálního počítače 
Učitelé se musí nejdřív připojit k virtuálnímu počítači šablony pomocí SSH a nainstalovat na něm balíčky RDP a GUI. Učitelé pak můžou pomocí protokolu RDP připojit se k virtuálnímu počítači šablony: 

1. Pokud se na panelu nástrojů zobrazí možnost **přizpůsobit šablonu** , vyberte ji. Pak v dialogovém okně **přizpůsobit šablonu** vyberte **pokračovat** . Tato akce spustí virtuální počítač šablony.  

    ![Přizpůsobení šablony](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. Po spuštění šablony virtuálního počítače můžete vybrat **Připojit šablonu** a pak na panelu nástrojů **připojit přes SSH** . 

    ![Připojení k šabloně přes protokol RDP po vytvoření testovacího prostředí](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. Zobrazí se následující dialogové okno **připojit k virtuálnímu počítači** . Kliknutím na tlačítko **Kopírovat** vedle textového pole ho zkopírujte do schránky. Uložte připojovací řetězec SSH. K připojení k virtuálnímu počítači použijte tento připojovací řetězec z terminálu SSH [(jako např](https://www.putty.org/).).
 
    ![Připojovací řetězec SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. Nainstalujte balíčky RDP a GUI, abyste se mohli a studenti mohli připojit k počítači se systémem Linux pomocí protokolu RDP později. Další informace najdete v tématu [instalace a konfigurace vzdálené plochy pro připojení k virtuálnímu počítači se systémem Linux v Azure](../../virtual-machines/linux/use-remote-desktop.md). Potom obrázek publikujete, aby se studenti mohli připojit k virtuálním počítačům s Linuxem pro studenty.
5. Po instalaci těchto balíčků můžete použít **šablonu připojit k** na panelu nástrojů a pak vybrat **připojit přes RDP** pro připojení k virtuálnímu počítači šablony přes RDP. Uložte soubor RDP a použijte ho pro připojení k virtuálnímu počítači šablony přes RDP. 

## <a name="next-steps"></a>Další kroky
Po povolení funkce připojení ke vzdálené ploše studentům se můžou studenti připojit ke svým virtuálním počítačům přes RDP/SSH. Další informace najdete v tématu [použití vzdálené plochy pro virtuální počítače se systémem Linux v prostředí učebny](how-to-use-remote-desktop-linux-student.md). 