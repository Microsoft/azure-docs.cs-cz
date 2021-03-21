---
title: Povolit grafický vzdálenou plochu pro Linux v Azure Lab Services | Microsoft Docs
description: Přečtěte si, jak povolit vzdálenou plochu pro virtuální počítače se systémem Linux v testovacím prostředí v Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 34c940fec388bb0e79ab5e1db9be6d52fb223873
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94647949"
---
# <a name="enable-graphical-remote-desktop-for-linux-virtual-machines-in-azure-lab-services"></a>Povolení grafické vzdálené plochy pro virtuální počítače se systémem Linux v Azure Lab Services
V tomto článku se dozvíte, jak provádět následující úlohy:

- Povolení grafických relací vzdálené plochy pro virtuální počítač se systémem Linux
- Jak se připojit k virtuálnímu počítači se systémem Linux pomocí protokolu RDP (protokol RDP (Remote Desktop Protocol)) nebo klientů vzdálené plochy X2Go

## <a name="set-up-graphical-remote-desktop-solution"></a>Nastavení grafického řešení vzdálené plochy
Při vytvoření testovacího prostředí z image systému **Linux** se automaticky nakonfiguruje přístup **SSH** (Secure Shell), aby se instruktor mohl připojit k virtuálnímu počítači šablony z příkazového řádku pomocí SSH.  Podobně platí, že když je virtuální počítač šablony publikovaný, studenti se můžou ke svým virtuálním počítačům připojit taky pomocí SSH.

K připojení k virtuálnímu počítači se systémem Linux pomocí **grafického** uživatelského rozhraní (grafické uživatelské rozhraní) doporučujeme použít buď protokol **RDP** , nebo **X2Go**.  Obě tyto možnosti vyžadují, aby instruktor provede další nastavení na virtuálním počítači šablony:

### <a name="rdp-setup"></a>Nastavení RDP
Chcete-li použít protokol RDP, instruktor musí:
  - Povolit připojení ke vzdálené ploše; To je konkrétně nutné pro otevření portu virtuálního počítače pro protokol RDP.
  - Nainstalujte server vzdálené plochy RDP.
  - Nainstalujte grafické prostředí se systémem Linux (například důstojník, desktop Xfce atd.).

### <a name="x2go-setup"></a>Nastavení X2Go
Chcete-li použít X2Go, instruktor musí:
- Nainstalujte server X2Go Remote Desktop.
- Nainstalujte grafické prostředí se systémem Linux (například důstojník, desktop Xfce atd.).

X2Go používá stejný port, který je už povolený pro SSH.  V důsledku toho není nutná žádná další konfigurace pro otevření portu na virtuálním počítači pro X2Go.

> [!NOTE]
> V některých případech, například s Ubuntu LTS 18,04, poskytuje X2Go lepší výkon.  Pokud při interakci s grafickým prostředím plochy používáte latenci protokolu RDP a upozornění, zvažte možnost vyzkoušet si X2Go, protože může zvýšit výkon.

> [!IMPORTANT]
>  Na některých imagích Marketplace už máte nainstalované grafické prostředí plochy a server pro vzdálenou plochu.  Například [Data Science Virtual Machine pro Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) už má [nainstalovaný desktop Xfce a server X2Go a je nakonfigurovaný tak, aby přijímal připojení klienta](../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#x2go).

## <a name="enable-remote-desktop-connection-for-rdp"></a>Povolit připojení ke vzdálené ploše pro RDP

Tento krok je potřebný jenom pro připojení pomocí protokolu RDP.  Pokud místo toho plánujete používat X2Go, můžete přejít k další části, protože X2Go používá port SSH.

1.  Při vytváření testovacího prostředí má instruktor možnost **povolit připojení ke vzdálené ploše**.  Instruktor musí tuto možnost **Povolit** , aby otevřel port na virtuálním počítači Linux, který je potřeba pro relaci vzdálené plochy RDP.  V opačném případě, pokud je tato možnost **zakázána**, je otevřen pouze port pro SSH.
  
    ![Snímek obrazovky, který zobrazuje okno nové testovací prostředí s možností Enable Připojení ke vzdálené ploše.](./media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

2. V poli **povolit připojení ke vzdálené plošeovou** zprávu vyberte **pokračovat u možnosti Vzdálená plocha**. 

    ![Povolení připojení ke vzdálené ploše pro bitovou kopii operačního systému Linux](./media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

## <a name="install-rdp-or-x2go"></a>Instalace RDP nebo X2Go

Po vytvoření testovacího prostředí musí instruktor zajistit, aby na virtuálním počítači šablony bylo nainstalované grafické prostředí plochy a server vzdálené plochy.  Instruktoři se musí nejdřív připojit k virtuálnímu počítači šablony pomocí SSH a nainstalovat balíčky pro:
- Buď vzdálený desktopový Server RDP, nebo X2Go.
- Grafické prostředí pro stolní počítače, jako je například důstojník, desktop Xfce atd.

Po nastavení se může instruktor připojit k virtuálnímu počítači šablony pomocí klienta **Vzdálená plocha Microsoft (RDP)** nebo klienta **X2Go** .

Pomocí následujících kroků nastavte virtuální počítač šablony:

1. Pokud se na panelu nástrojů zobrazí možnost **přizpůsobit šablonu** , vyberte ji. Pak v dialogovém okně **přizpůsobit šablonu** vyberte **pokračovat** . Tato akce spustí virtuální počítač šablony.  

    ![Přizpůsobení šablony](./media/how-to-enable-remote-desktop-linux/customize-template.png)
1. Po spuštění šablony virtuálního počítače můžete vybrat **Připojit šablonu** a pak na panelu nástrojů **připojit přes SSH** . 

    ![Připojení k šabloně přes protokol RDP po vytvoření testovacího prostředí](./media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
1. Zobrazí se následující dialogové okno **připojit k virtuálnímu počítači** . Kliknutím na tlačítko **Kopírovat** vedle textového pole ho zkopírujte do schránky. Uložte informace o připojení SSH. Pro připojení k virtuálnímu počítači použijte tyto informace o připojení z terminálu SSH [(jako např](https://www.putty.org/).).
 
    ![Připojovací řetězec SSH](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. Nainstalujte buď RDP, nebo X2Go spolu s grafickým prostředím, které jste si vybrali.  Přečtěte si následující pokyny:
    - [Instalace a konfigurace protokolu RDP](../virtual-machines/linux/use-remote-desktop.md)
    - [Instalace a konfigurace X2Go](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/X2GoRemoteDesktop)

## <a name="connect-to-the-template-vm-via-the-gui"></a>Připojení k virtuálnímu počítači šablony prostřednictvím grafického uživatelského rozhraní

Po nastavení virtuálního počítače šablony se může instruktor připojit prostřednictvím grafického uživatelského rozhraní pomocí klienta **Vzdálená plocha Microsoft (RDP)** nebo klienta **X2Go** .  Klient, který použijete, závisí na tom, jestli je protokol RDP nebo X2Go nakonfigurovaný jako server vzdálené plochy na virtuálním počítači šablony.  

### <a name="microsoft-remote-desktop-rdp-client"></a>Klient Vzdálená plocha Microsoft (RDP)

Klient Vzdálená plocha Microsoft (RDP) se používá pro připojení k virtuálnímu počítači šablony s nakonfigurovaným protokolem RDP.  Klient vzdálené plochy se dá použít na Windows, Chromebooks, Mac a dalších.  Další podrobnosti najdete v článku o [klientech vzdálené plochy](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients) .

Postupujte podle následujících kroků v závislosti na typu počítače použitého pro připojení k VIRTUÁLNÍmu počítači šablony:

- Windows
  1. Na panelu nástrojů testovacího prostředí klikněte na **připojit k šabloně** a vyberte **připojit přes RDP** a připojte se k virtuálnímu počítači šablony. 
  1. Uložte soubor RDP a použijte ho k připojení k virtuálnímu počítači šablony pomocí klienta vzdálené plochy. 
  1. Klient vzdálené plochy je obvykle nainstalován a nakonfigurován v systému Windows.  V důsledku toho stačí k otevření souboru RDP kliknout na něj a spustit vzdálenou relaci.

- Mac
  1. Na panelu nástrojů testovacího prostředí klikněte na **připojit k šabloně** a pak vyberte **připojit přes RDP** a uložte soubor RDP.  
  1. Pak si přečtěte článek Jak se [připojit k virtuálnímu počítači pomocí protokolu RDP na Macu](connect-virtual-machine-mac-remote-desktop.md).

- Chromebook
  1. Na panelu nástrojů testovacího prostředí klikněte na **připojit k šabloně** a pak vyberte **připojit přes RDP** a uložte soubor RDP.  
  1. Pak se podívejte na článek s postupem, jak se [připojit k virtuálnímu počítači pomocí protokolu RDP na Chromebook](connect-virtual-machine-chromebook-remote-desktop.md).

### <a name="x2go-client"></a>Klient X2Go

Klient X2Go se používá pro připojení k virtuálnímu počítači šablony s nakonfigurovaným X2Go.  Pomocí informací o připojení SSH pro daný virtuální počítač použijte postup v článku [připojení k virtuálnímu počítači pomocí X2Go](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-x2go).

## <a name="next-steps"></a>Další kroky
Jakmile instruktor nastaví RDP nebo X2Go na svém virtuálním počítači šablony a publikuje, studenti se můžou připojit ke svým virtuálním počítačům přes vzdálenou plochu GUI nebo SSH.

Další informace naleznete v tématu:
 - [Připojení k virtuálnímu počítači s Linuxem](how-to-use-remote-desktop-linux-student.md)