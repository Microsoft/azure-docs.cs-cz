---
title: Řešení potíží problém virtuálního počítače Azure pomocí vnořená virtualizace v Azure | Dokumentace Microsoftu
description: Jak řešit problém virtuálního počítače Azure s využitím vnořená virtualizace v Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 425aa26c07675d57de6892adb5792eb5573f94b1
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50747979"
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Řešení potíží problém virtuálního počítače Azure pomocí vnořená virtualizace v Azure

Tento článek ukazuje, jak vytvořit prostředí vnořená virtualizace v Microsoft Azure, takže se můžete připojit disk problémový virtuální počítač na hostitele Hyper-V (virtuální počítač zachránit) pro účely odstraňování potíží.

## <a name="prerequisites"></a>Požadavky

Připojte problémový virtuální počítač, virtuální počítač zachránit musí splňovat následující požadavky:

-   Virtuální počítač zachránit musí být ve stejném umístění jako problémový virtuální počítač.

-   Virtuální počítač zachránit musí být ve stejné skupině prostředků jako problémový virtuální počítač.

-   Virtuální počítač zachránit musí používat stejný typ účtu úložiště (Standard nebo Premium) jako problémový virtuální počítač.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Krok 1: Vytvoření virtuálního počítače zachránit a nainstalujte roli Hyper-V

1.  Vytvořte nový virtuální počítač zachránit:

    -  Operační systém: Windows Server 2016 Datacenter

    -  Velikost: Libovolné řady V3 s nejméně dvěma jader že podporu vnořené virtualizace. Další informace najdete v tématu [Představujeme nové velikosti počítačů Dv3 a Ev3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Stejné umístění, účet úložiště a skupině prostředků jako problémový virtuální počítač.

    -  Vyberte stejný typ úložiště jako problémový virtuální počítač (Standard nebo Premium).

2.  Za virtuální počítač zachránit se vytvoří, vzdálené plochy k virtuálnímu počítači zachránit.

3.  Ve Správci serveru vyberte **spravovat** > **přidat role a funkce**.

4.  V **typ instalace** vyberte **instalace na základě rolí nebo na základě funkcí**.

5.  V **vybrat cílový server** části, ujistěte se, že je vybraná zachránit virtuálního počítače.

6.  Vyberte **role Hyper-V** > **přidat funkce**.

7.  Vyberte **Další** na **funkce** oddílu.

8.  Pokud virtuální přepínač je k dispozici, vyberte ji. V opačném případě vyberte **Další**.

9.  Na **migrace** vyberte **další**

10. Na **výchozí úložiště** vyberte **Další**.

11. Zaškrtněte políčko v případě potřeby automaticky restartovat server.

12. Vyberte **Install** (Nainstalovat).

13. Povolit server k instalaci role Hyper-V. Tato akce trvá několik minut a na server se automaticky restartuje.

## <a name="step-2-create-the-problem-vm-on-the-rescue-vms-hyper-v-server"></a>Krok 2: Vytvoření problémový virtuální počítač na serveru Hyper-V virtuálního počítače zachránit

1.  Zaznamenejte název disku v problémový virtuální počítač a pak odstraňte problém virtuálního počítače. Ujistěte se, abyste všechny připojené disky. 

2.  Připojte disk s operačním systémem problému virtuálního počítače jako datového disku virtuálního počítače zachránit.

    1.  Za problém se odstraní virtuální počítač, přejděte k virtuálnímu počítači zachránit.

    2.  Vyberte **disky**a potom **přidat datový disk**.

    3.  Vyberte disk virtuálního počítače problém a pak vyberte **Uložit**.

3.  Po disku byla úspěšně připojený, vzdálené plochy k virtuálnímu počítači zachránit.

4.  Otevřete správu disků (diskmgmt.msc). Ujistěte se, že disk problém virtuálního počítače je nastavena na **Offline**.

5.  Otevřete Správce technologie Hyper-V: V **správce serveru**, vyberte **role Hyper-V**. Klikněte pravým tlačítkem na server a pak vyberte **Správce technologie Hyper-V**.

6.  Ve Správci technologie Hyper-V klikněte pravým tlačítkem na virtuální počítač zachránit a pak vyberte **nový** > **virtuálního počítače** > **Další**.

7.  Zadejte název pro virtuální počítač a potom vyberte **Další**.

8.  Vyberte **1. generace**.

9.  Nastavte počáteční hodnotu paměti na 1024 MB nebo víc.

10. Pokud je k dispozici vyberte síťový přepínač technologie Hyper-V, který byl vytvořen. Jinak přejděte na další stránku.

11. Vyberte **připojit virtuální pevný disk později**.

    ![obrázek o možnost později virtuální pevný Disk Attach](/media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

12. Vyberte **Dokončit** při vytvoření virtuálního počítače.

13. Klikněte pravým tlačítkem na virtuální počítač, který jste vytvořili a pak vyberte **nastavení**.

14. Vyberte **řadič IDE 0**vyberte **pevném**a potom klikněte na tlačítko **přidat**.

    ![Přidá obrázek o novém pevném disku](/media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

15. V **fyzický pevný Disk**, vyberte disk problémový virtuální počítač, který jste připojili k virtuálnímu počítači Azure. Pokud nevidíte všechny disky uvedené, zaškrtněte, pokud je disk se nastaví na offline pomocí správy disků.

    ![obrázek o připojí disk](/media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


17. Vyberte **Apply** (Použít) a pak vyberte **OK**.

18. Dvakrát klikněte na virtuální počítač a spusťte ho.

19. Teď můžete pracovat ve virtuálním počítači jako místní virtuální počítač. Může postupujte podle všech kroků, které potřebujete.

## <a name="step-3-re-create-your-azure-vm-in-azure"></a>Krok 3: Opětovné vytvoření virtuálního počítače Azure v Azure

1.  Jakmile získáte zpět do režimu online virtuálního počítače, vypněte virtuální počítač ve Správci technologie Hyper-V.

2.  Přejděte [webu Azure portal](https://portal.azure.com) a vyberte virtuální počítač zachránit > disky, zkopírujte název disku. Název použijete v dalším kroku. Pevný disk z virtuálního počítače zachránit odpojte.

3.  Přejděte na **všechny prostředky**, vyhledejte název disku a pak vyberte disk.

     ![obrázek o prohledá disk](/media/troubleshoot-vm-by-use-nested-virtualization/search-disk.png)     

4. Klikněte na tlačítko **vytvoření virtuálního počítače**.

     ![obrázek o vytvoří virtuální počítač z disku](./media/troubleshoot-vm-by-use-nested-virtualization/create-vm-from-vhd.png) 

Můžete také pomocí Azure Powershellu k vytvoření virtuálního počítače z disku. Další informace najdete v tématu [vytvoření nového virtuálního počítače z existujícího disku pomocí prostředí PowerShell](../windows/create-vm-specialized.md#create-the-new-vm). 

## <a name="next-steps"></a>Další postup

Pokud máte problémy s připojením k virtuálnímu počítači, přečtěte si téma [připojení řešení potíží s RDP k virtuálnímu počítači Azure](troubleshoot-rdp-connection.md). Problémy s přístupem k aplikacím spuštěným na vašem virtuálním počítači, naleznete v tématu [řešit problémy s připojením aplikace na virtuálním počítači s Windows](troubleshoot-app-connection.md).
