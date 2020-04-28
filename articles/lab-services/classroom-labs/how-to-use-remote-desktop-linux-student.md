---
title: Použití vzdálené plochy pro Linux v Azure Lab Services | Microsoft Docs
description: Naučte se používat vzdálenou plochu pro virtuální počítače se systémem Linux v testovacím prostředí v Azure Lab Services.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "73585084"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Použití vzdálené plochy pro virtuální počítače se systémem Linux v laboratoři učebny Azure Lab Services
V tomto článku se dozvíte, jak se můžou studenti připojit k virtuálnímu počítači se systémem Linux v testovacím prostředí pomocí protokolu RDP/SSH. 

Instruktor musí povolit funkci připojení ke vzdálené ploše, aby se studenti mohli připojit k virtuálnímu počítači prostředí učeben. Pokyny, jak může instruktor povolit funkci připojení ke vzdálené ploše, najdete v tématu [Povolení vzdálené plochy pro virtuální počítače se systémem Linux](how-to-enable-remote-desktop-linux.md).

> [!IMPORTANT] 
> Povolení **připojení ke vzdálené ploše** otevírá jenom port **RDP** na počítačích se systémem Linux. Instruktor se může připojit k počítači se systémem Linux pomocí protokolu SSH poprvé a nainstalovat balíčky RDP a GUI, abyste se mohli připojit k počítači se systémem Linux pomocí protokolu RDP později. 

## <a name="connect-to-the-student-vm"></a>Připojení k virtuálnímu počítači studenta
Studenti se můžou ke svým virtuálním počítačům se systémem Linux připojit až poté, co vlastník testovacího prostředí (učitel/profesor) **publikuje** virtuální počítač šablony s balíčky RDP a GUI nainstalovanými v počítači. Postup je následující: 

1. Když se student přihlašuje přímo k portálu Labs (`https://labs.azure.com`) nebo pomocí odkazu na registraci (`https://labs.azure.com/register/<registrationCode>`), zobrazí se dlaždice pro každé testovací prostředí, ke kterému má student přístup. 
2. Na dlaždici přepněte tlačítko tak, aby se virtuální počítač spustil, pokud je v zastaveném stavu. 
3. Vyberte **Connect** (Připojit). Zobrazí se dvě možnosti, jak se připojit k virtuálnímu počítači: **SSH** a **Vzdálená plocha**.

    ![Virtuální počítač student – možnosti připojení](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Připojení pomocí protokolu SSH nebo RDP
Pokud vyberete možnost **SSH** , zobrazí se následující dialogové okno **připojit k virtuálnímu počítači** :  

![Připojovací řetězec SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Kliknutím na tlačítko **Kopírovat** vedle textového pole ho zkopírujte do schránky. Uložte připojovací řetězec SSH. K připojení k virtuálnímu počítači použijte tento připojovací řetězec z terminálu SSH [(jako např](https://www.putty.org/).).

Pokud vyberete možnost **RDP** , na váš počítač se stáhne soubor RDP. Uložte ho a otevřete ho pro připojení k počítači. 

## <a name="next-steps"></a>Další kroky
Informace o tom, jak povolit funkci připojení ke vzdálené ploše pro virtuální počítače se systémem Linux v prostředí učebny, najdete v tématu [Povolení vzdálené plochy pro virtuální počítače se systémem Linux](how-to-enable-remote-desktop-linux.md). 

