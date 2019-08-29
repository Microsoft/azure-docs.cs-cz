---
title: Řešení potíží s virtuálním počítačem Azure pomocí vnořené virtualizace v Azure | Microsoft Docs
description: Řešení potíží s virtuálním počítačem Azure pomocí vnořené virtualizace v Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 18d7e9b0ab44dfe18df0dcd7cd36fb708649a4bc
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089681"
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Řešení potíží s virtuálním počítačem Azure pomocí vnořené virtualizace v Azure

V tomto článku se dozvíte, jak vytvořit vnořené prostředí virtualizace v nástroji Microsoft Azure, abyste mohli připojit disk problémového virtuálního počítače na hostiteli Hyper-V (záchranný virtuální počítač) pro účely řešení potíží.

## <a name="prerequisites"></a>Požadavky

Chcete-li připojit problémový virtuální počítač, musí záchranný virtuální počítač splňovat následující požadavky:

-   Záchranný virtuální počítač musí být ve stejném umístění jako virtuální počítač problému.

-   Záchranný virtuální počítač musí být ve stejné skupině prostředků jako problémový virtuální počítač.

-   Záchranný virtuální počítač musí používat stejný typ účtu úložiště (Standard nebo Premium) jako virtuální počítač problému.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Krok 1: Vytvoření záchranného virtuálního počítače a instalace role Hyper-V

1.  Vytvořte nový záchranný virtuální počítač:

    -  Operační systém: Windows Server 2016 Datacenter

    -  Velikost: Všechny řady v3 s alespoň dvěma jádry, které podporují vnořenou virtualizaci. Další informace najdete v tématu [Představujeme nové velikosti virtuálních počítačů s Dv3 a Ev3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Stejné umístění, účet úložiště a skupina prostředků jako virtuální počítač problému.

    -  Vyberte stejný typ úložiště jako virtuální počítač problému (Standard nebo Premium).

2.  Po vytvoření záchranného virtuálního počítače se jedná o vzdálenou plochu na záchranný virtuální počítač.

3.  V správce serveru vyberte **Spravovat** > **Přidat role a funkce**.

4.  V části **typ instalace** vyberte instalace na základě **rolí nebo na základě funkcí**.

5.  V části **Vybrat cílový server** se ujistěte, že je vybraný záchranný virtuální počítač.

6.  Vyberte >  **role Hyper-V** **Přidat funkce**.

7.  V části **funkce** vyberte **Další** .

8.  Pokud je k dispozici virtuální přepínač, vyberte ho. V opačném případě vyberte **Další**.

9.  V části **migrace** vyberte **Další** .

10. V části **výchozí úložiště** vyberte **Další**.

11. V případě potřeby zaškrtněte políčko pro restartování serveru.

12. Vyberte **Install** (Nainstalovat).

13. Povolí serveru instalaci role Hyper-V. Tato akce trvá několik minut a server se automaticky restartuje.

## <a name="step-2-create-the-problem-vm-on-the-rescue-vms-hyper-v-server"></a>Krok 2: Vytvoření virtuálního počítače s problémem na serveru Hyper-V záchranného virtuálního počítače

1.  Poznamenejte si název disku ve virtuálním počítači problému a pak odstraňte problémový virtuální počítač. Ujistěte se, že jste zachovali všechny připojené disky. 

2.  Připojte disk s operačním systémem pro váš problémový virtuální počítač jako datový disk pro záchranný virtuální počítač.

    1.  Po odstranění problému zkuste virtuální počítač přejít na záchranný virtuální počítač.

    2.  Vyberte **disky**a pak **přidat datový disk**.

    3.  Vyberte disk s problematickým virtuálním počítačem a pak vyberte **Uložit**.

3.  Po úspěšném připojení disku k záchrannému VIRTUÁLNÍmu počítači se připojí Vzdálená plocha.

4.  Spusťte správu disků (diskmgmt. msc). Ujistěte se, že je disk virtuálního počítače problému nastavený na **offline**.

5.  Otevřít Správce technologie Hyper-V: V **Správce serveru**vyberte **roli technologie Hyper-V**. Pravým tlačítkem myši klikněte na server a pak vyberte **Správce technologie Hyper-V**.

6.  Ve Správci technologie Hyper-V klikněte pravým tlačítkem na záchranný virtuální počítač a potom vyberte **Nový** > **virtuální počítač** > **Další**.

7.  Zadejte název virtuálního počítače a pak vyberte **Další**.

8.  Vyberte možnost **generace 1**.

9.  Nastavte spouštěcí paměť v 1024 MB nebo více.

10. Pokud je to možné, vyberte přepínač sítě Hyper-V, který byl vytvořen. V opačném případě přejděte na další stránku.

11. Vyberte **připojit virtuální pevný disk později**.

    ![Obrázek možnosti připojit virtuální pevný disk později](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

12. Po vytvoření virtuálního počítače vyberte **Dokončit** .

13. Klikněte pravým tlačítkem na virtuální počítač, který jste vytvořili, a pak vyberte **Nastavení**.

14. Vyberte možnost **řadič IDE 0**, vyberte možnost **pevný disk**a klikněte na tlačítko **Přidat**.

    ![Obrázek týkající se přidání nového pevného disku](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

15. Na stránce **fyzický pevný disk**vyberte disk s PROBLEMATICKým virtuálním počítačem, který jste připojili k virtuálnímu počítači Azure. Pokud nevidíte žádné disky uvedené na seznamu, zkontrolujte, jestli je disk nastavený na offline pomocí správy disků.

    ![obrázek o připojení disku](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


17. Vyberte **použít**a pak vyberte **OK**.

18. Dvakrát klikněte na virtuální počítač a potom ho spusťte.

19. Nyní můžete pracovat na VIRTUÁLNÍm počítači jako místní virtuální počítač. Můžete postupovat podle všech potřebných kroků pro řešení potíží.

## <a name="step-3-re-create-your-azure-vm-in-azure"></a>Krok 3: Opětovné vytvoření virtuálního počítače Azure v Azure

1.  Po opětovném obnovení virtuálního počítače do režimu online vypněte virtuální počítač ve Správci technologie Hyper-V.

2.  Otevřete [Azure Portal](https://portal.azure.com) a vyberte záchranné virtuální počítače > disky, zkopírujte název disku. V dalším kroku použijete název. Odpojte pevný disk od záchranného virtuálního počítače.

3.  Přejdete na **všechny prostředky**, vyhledejte název disku a pak vyberte disk.

     ![obrázek o hledání na disku](media/troubleshoot-vm-by-use-nested-virtualization/search-disk.png)     

4. Klikněte na **vytvořit virtuální počítač**.

     ![obrázek o vytvoření virtuálního počítače z disku](media/troubleshoot-vm-by-use-nested-virtualization/create-vm-from-vhd.png) 

K vytvoření virtuálního počítače z disku můžete použít taky Azure PowerShell. Další informace najdete v tématu [Vytvoření nového virtuálního počítače z existujícího disku pomocí prostředí PowerShell](../windows/create-vm-specialized.md#create-the-new-vm). 

## <a name="next-steps"></a>Další postup

Pokud máte problémy s připojením k VIRTUÁLNÍmu počítači, přečtěte si téma [řešení potíží s připojením RDP k virtuálnímu počítači Azure](troubleshoot-rdp-connection.md). Problémy s přístupem k aplikacím běžícím na vašem VIRTUÁLNÍm počítači najdete v tématu [řešení potíží s připojením aplikací na virtuálním počítači s Windows](troubleshoot-app-connection.md).
