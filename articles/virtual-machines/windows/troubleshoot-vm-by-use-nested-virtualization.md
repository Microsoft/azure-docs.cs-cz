---
title: Řešení potíží problém virtuálního počítače Azure pomocí vnořené virtualizace v Azure | Microsoft Docs
description: Řešení problému virtuálního počítače Azure pomocí vnořené virtualizace v Azure
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
ms.date: 11/06/2017
ms.author: genli
ms.openlocfilehash: 2743a00404a2ee990147dfb6e73e9c2369eb4753
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Řešení potíží problém virtuálního počítače Azure pomocí vnořené virtualizace v Azure

Tento článek ukazuje, jak vytvářet vnořené virtualizované prostředí ve službě Microsoft Azure, takže můžete připojit disk problému virtuální počítač na hostitele Hyper-V (obnovení virtuálních počítačů) pro účely odstraňování potíží.

## <a name="prerequisite"></a>Požadavek

Připojit problém virtuálních počítačů, virtuálních počítačů pro obnovení musí splňovat následující požadavek:

-   Obnovení virtuálního počítače musí být ve stejném umístění jako problém virtuálních počítačů.

-   Obnovení virtuálního počítače musí být ve stejné skupině prostředků jako problém virtuálních počítačů.

-   Obnovení virtuálního počítače musí používat stejný typ účtu úložiště (Standard nebo Premium) jako problém virtuálních počítačů.

## <a name="step-1-create-a-recovery-vm-and-install-hyper-v-role"></a>Krok 1: Vytvořte virtuální počítač pro obnovení a nainstalujte roli Hyper-V

1.  Vytvořte nový virtuální počítač pro obnovení:

    -  Operační systém: Windows Server 2016 Datacenter

    -  Velikost: Všechny řady V3 s alespoň dvě cores tuto podporu vnořené virtualizace. Další informace najdete v tématu [Představujeme nové velikosti Dv3 a virtuálních počítačů Ev3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Stejné umístění, účet úložiště a skupině prostředků jako problém virtuálních počítačů.

    -  Vyberte stejný typ úložiště jako problém virtuálních počítačů (Standard nebo Premium).

2.  Po obnovení virtuálního počítače je vytvořen, vzdálené plochy k obnovení virtuálního počítače.

3.  Ve Správci serveru vyberte **spravovat** > **přidat role a funkce**.

4.  V **typ instalace** vyberte **instalace na základě rolí nebo na základě funkcí**.

5.  V **vybrat cílový server** Ujistěte se, že je vybraný virtuální počítač pro obnovení.

6.  Vyberte **role Hyper-V** > **přidat funkce**.

7.  Vyberte **Další** na **funkce** části.

8.  Pokud virtuální přepínač je k dispozici, vyberte ho. V opačném případě vyberte **Další**.

9.  Na **migrace** vyberte **další**

10. Na **výchozí úložiště** vyberte **Další**.

11. Zaškrtněte políčko v případě potřeby automaticky restartovat server.

12. Vyberte **nainstalovat**.

13. Povolí server k instalaci role Hyper-V. Tato akce trvá několik minut a na server se automaticky restartuje.

## <a name="step-2-create-the-problem-vm-on-the-recovery-vms-hyper-v-server"></a>Krok 2: Vytvoření problém virtuálního počítače na serveru pro obnovení Virtuálního počítače technologie Hyper-V

1.  Zaznamenejte název disku v problém virtuálních počítačů a pak odstraňte problém virtuálních počítačů. Ujistěte se, že zachováte všechny připojené disky. 

2.  Připojte disk operačního systému vašeho problému virtuálního počítače jako datový disk virtuálního počítače pro obnovení.

    1.  Po problém se odstraní virtuální počítač, přejděte na virtuální počítač pro obnovení.

    2.  Vyberte **disky**a potom **přidat datový disk**.

    3.  Vyberte disk problém Virtuálního počítače a potom vyberte **Uložit**.

3.  Po disk se úspěšně připojené, vzdálené plochy k obnovení virtuálního počítače.

4.  Open Disk Management (diskmgmt.msc). Ujistěte se, že disk problému virtuálního počítače je nastavená na **Offline**.

5.  Otevřete Správce technologie Hyper-V: V **správce serveru**, vyberte **role Hyper-V**. Pravým tlačítkem na server a pak vyberte **Správce technologie Hyper-V**.

6.  Ve Správci technologie Hyper-V, klikněte pravým tlačítkem na virtuální počítač pro obnovení a pak vyberte **nový** > **virtuálního počítače** > **Další**.

7.  Zadejte název pro virtuální počítač a potom vyberte **Další**.

8.  Vyberte **generace 1**.

9.  Nastavte spouštěcí paměť na 1024 MB nebo víc.

10. Pokud je k dispozici vyberte síťový přepínač technologie Hyper-V, který byl vytvořen. Else Přesun na další stránku.

11. Vyberte **připojit virtuální pevný disk později**.

    ![obrázek o připojení možnost později virtuální pevný Disk](./media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

12. Vyberte **Dokončit** při vytvoření virtuálního počítače.

13. Klikněte pravým tlačítkem na virtuální počítač, který jste vytvořili a potom vyberte **nastavení**.

14. Vyberte **řadič IDE 0**, vyberte **pevný disk**a potom klikněte na **přidat**.

    ![obrázek o přidá nový pevný disk](./media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

15. V **fyzický pevný Disk**, vyberte disk, který problému virtuální počítač, který je připojen k virtuálnímu počítači Azure. Pokud nevidíte všechny disky uvedené, zkontrolujte, pokud je disk se nastaví na offline pomocí správy disků.

    ![obrázek o připojí na disk](./media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


17. Vyberte **použít**a potom vyberte **OK**.

18. Dvakrát klikněte na virtuální počítač a spusťte ho.

19. Nyní můžete pracovat na virtuálním počítači jako místní virtuální počítač. Můžete sledovat kroky řešení potíží, které potřebujete.

## <a name="step-3-re-create-your-azure-vm-in-azure"></a>Krok 3: Znovu vytvořte Azure virtuálního počítače v Azure

1.  Jakmile se zobrazí zpět do režimu online virtuálního počítače, vypněte virtuální počítač ve Správci technologie Hyper-V.

2.  Přejděte na [portál Azure](https://portal.azure.com) a vyberte virtuální počítač pro obnovení > disky, zkopírujte název disku. V dalším kroku použijete název. Odpojte pevný disk z virtuální počítač pro obnovení.

3.  Přejděte na **všechny prostředky**, vyhledejte název disku a pak vyberte disk.

     ![obrázek o vyhledá disku](./media/troubleshoot-vm-by-use-nested-virtualization/search-disk.png)     

4. Klikněte na tlačítko **vytvoření virtuálního počítače**.

     ![obrázek o vytvoří virtuální počítač z disku](./media/troubleshoot-vm-by-use-nested-virtualization/create-vm-from-vhd.png) 

Můžete taky prostředí Azure PowerShell k vytvoření virtuálního počítače z disku. Další informace najdete v tématu [vytvoření nového virtuálního počítače z existujícího disku pomocí prostředí PowerShell](create-vm-specialized.md#create-the-new-vm). 

## <a name="next-steps"></a>Další postup

Pokud máte problémy s připojením k virtuálnímu počítači, přečtěte si téma [připojení řešení potíží s RDP na virtuální počítač Azure](troubleshoot-rdp-connection.md). Problémy s přístupem k aplikacím spuštěným na vašem virtuálním počítači najdete v tématu [problémů s připojením aplikace na virtuálním počítači Windows](troubleshoot-app-connection.md).
