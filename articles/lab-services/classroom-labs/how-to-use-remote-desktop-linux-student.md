---
title: Použití vzdálené plochy pro Linux ve službách Azure Lab Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak používat vzdálenou plochu pro virtuální počítače s Linuxem v testovacím prostředí ve službě Azure Lab Services.
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
ms.openlocfilehash: 0f45af2730b05998fc82212c63778c89bb16b6ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73585084"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Použití vzdálené plochy pro virtuální počítače s Linuxem v učebně v laboratoři Azure Lab Services
Tento článek ukazuje, jak se studenti můžou připojit k virtuálnímu počítači (VM) linuxového počítače v testovacím prostředí pomocí RDP/SSH. 

Instruktor musí povolit funkci připojení ke vzdálené ploše, než se studenti budou moct připojit k virtuálnímu počítači učebny. Pokyny k tomu, jak může instruktor povolit funkci připojení ke vzdálené ploše, naleznete v tématu [Povolení vzdálené plochy pro virtuální počítače s Linuxem](how-to-enable-remote-desktop-linux.md).

> [!IMPORTANT] 
> Povolení **připojení ke vzdálené ploše** otevře pouze port **RDP** na počítačích s Linuxem. Instruktor se může poprvé připojit k počítači s Linuxem pomocí SSH a nainstalovat balíčky RDP a GUI, abyste se později mohli připojit k počítači s Linuxem pomocí RDP. 

## <a name="connect-to-the-student-vm"></a>Připojení k virtuálnímu virtuálnímu virtuálnímu virtuálnímu zařízení studenta
Studenti mohou RDP do svých virtuálních počítačů s Linuxem poté, co vlastník testovacího prostředí (učitel/profesor) **publikuje** šablonu Virtuální počítač s BALÍČKY RDP a GUI nainstalovanými v počítači. Postup je následující: 

1. Když se student přihlásí k portálu`https://labs.azure.com`Labs přímo (`https://labs.azure.com/register/<registrationCode>`) nebo pomocí registračního odkazu ( ), zobrazí se dlaždice pro každou laboratoř, ke které má student přístup. 
2. Na dlaždici přepněte tlačítko pro spuštění virtuálního počítače, pokud je v zastaveném stavu. 
3. Vyberte **Connect** (Připojit). Zobrazí se dvě možnosti připojení k virtuálnímu počítači: **SSH** a **vzdálená plocha**.

    ![Studentský virtuální virtuální měn – možnosti připojení](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Připojení pomocí SSH nebo RDP
Pokud vyberete možnost **SSH,** zobrazí se následující dialogové okno **Připojit k virtuálnímu počítači:**  

![Připojovací řetězec SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Vyberte tlačítko **Kopírovat** vedle textového pole, chcete-li ho zkopírovat do schránky. Uložte připojovací řetězec SSH. Pomocí tohoto připojovacího řetězce z terminálu SSH (například [Putty)](https://www.putty.org/)se připojte k virtuálnímu počítači.

Pokud vyberete možnost **RDP,** soubor RDP se stáhne do počítače. Uložte jej a otevřete jej pro připojení k zařízení. 

## <a name="next-steps"></a>Další kroky
Informace o povolení funkce připojení ke vzdálené ploše pro virtuální počítače s Linuxem v testovacím prostředí učebny najdete v tématu [Povolení vzdálené plochy pro virtuální počítače s Linuxem](how-to-enable-remote-desktop-linux.md). 

