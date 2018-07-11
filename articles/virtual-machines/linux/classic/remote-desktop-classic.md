---
title: Vzdálené plochy k virtuálnímu počítači s Linuxem | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat a nakonfigurovat vzdálené plochy pro připojení k virtuálnímu počítači Microsoft Azure Linux pro model nasazení Classic
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 34348659-ddb7-41da-82d6-b5885859e7e4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 5e68774c3edb7d82fef388c593a6b96c52857be6
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37927736"
---
# <a name="using-remote-desktop-to-connect-to-a-microsoft-azure-linux-vm"></a>Použití Vzdálené plochy pro připojení k virtuálnímu počítači Microsoft Azure s Linuxem
> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Aktualizovaná verze správce prostředků v tomto článku najdete v části [tady](../use-remote-desktop.md).

## <a name="overview"></a>Přehled
Protokol RDP (Remote Desktop Protocol) je speciální protokol použitý pro Windows. Jak jsme použitím protokolu RDP pro vzdálené připojení k virtuálnímu počítači s Linuxem (VM)?

Tento návod vám poskytne odpověď. To pomůže k instalaci a konfiguraci xrdp na Microsoft Azure virtuálního počítače s Linuxem, které vám umožní připojit se k němu přes vzdálenou plochu z počítače s Windows. Budeme používat systém Linux virtuálního počítače s Ubuntu nebo OpenSUSE stejně jako v příkladu v tomto dokumentu.

Nástroj xrdp je server opensourcové RDP, který umožňuje připojení serveru Linux přes vzdálenou plochu z počítače s Windows. Protokol RDP má lepší výkon než VNC (výpočetní virtuální sítě). VNC vykreslí pomocí kvality JPEG grafiky a může být pomalé, že protokol RDP je rychlý a jasný.

> [!NOTE]
> Musíte již mít Virtuálním počítači Microsoft Azure s Linuxem. Vytvoření a nastavení virtuálního počítače s Linuxem najdete v tématu [Linuxový virtuální počítač Azure kurz](createportal-classic.md).
> 
> 

## <a name="create-an-endpoint-for-remote-desktop"></a>Vytvoření koncového bodu pro vzdálenou plochu
Budeme používat výchozí koncový bod 3389 pro vzdálenou plochu v tomto dokumentu. Nastavení 3389 koncový bod jako `Remote Desktop` k virtuálním počítačům s Linuxem jako níže:

![image](./media/remote-desktop/endpoint-for-linux-server.png)

Pokud si nejste jisti, jak nastavit koncový bod pro virtuální počítač, přečtěte si téma [návod](setup-endpoints.md).

## <a name="install-gnome-desktop"></a>Nainstalujte Gnome Desktop
Připojení k virtuálním počítačům s Linuxem prostřednictvím `putty`a nainstalujte `Gnome Desktop`.

Pro Ubuntu použijte:

```bash
sudo apt-get update
sudo apt-get install ubuntu-desktop
```

Pro OpenSUSE použijte:

```bash
sudo zypper install gnome-session
```

## <a name="install-xrdp"></a>Nainstalujte xrdp
Pro Ubuntu použijte:

```bash
sudo apt-get install xrdp
```

Pro OpenSUSE použijte:

> [!NOTE]
> Aktualizujte verzi systému OpenSUSE. k verzi, kterou použijete v následujícím příkazu. Následující příklad je určený pro `OpenSUSE 13.2`.
> 
> 

```bash
sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc
```

## <a name="start-xrdp-and-set-xdrp-service-at-boot-up"></a>Spusťte xrdp a nastavte xdrp při spuštění
Pro OpenSUSE použijte:

```bash
sudo systemctl start xrdp
sudo systemctl enable xrdp
```

Pro Ubuntu bude spuštěn a automaticky povolí při spuštění po instalaci xrdp.

## <a name="using-xfce-if-you-are-using-an-ubuntu-version-later-than-ubuntu-1204lts"></a>Pokud používáte verzi Ubuntu později než Ubuntu 12.04LTS pomocí xfce
Vzhledem k tomu, že aktuální verze xrdp nepodporuje Gnome Desktop pro Ubuntu verze novější než Ubuntu 12.04LTS, budeme používat `xfce` Desktopu místo.

Chcete-li nainstalovat `xfce`, použijte tento příkaz:

```bash
sudo apt-get install xubuntu-desktop
```

Potom povolte `xfce` pomocí tohoto příkazu:

```bash
echo xfce4-session >~/.xsession
```

Upravit konfigurační soubor `/etc/xrdp/startwm.sh`:

```bash
sudo vi /etc/xrdp/startwm.sh   
```

Přidejte řádek `xfce4-session` před řádkem `/etc/X11/Xsession`.

Restartujte službu xrdp, použijte toto:

```bash
sudo service xrdp restart
```

## <a name="connect-your-linux-vm-from-a-windows-machine"></a>Připojení virtuálního počítače s Linuxem z počítače s Windows
V počítači s Windows spusťte klienta vzdálené plochy a zadejte název DNS virtuálního počítače Linux. Přejít na řídicí panel virtuálního počítače na webu Azure Portal a klikněte na tlačítko `Connect` pro připojení virtuálního počítače s Linuxem. V takovém případě se zobrazí okno přihlášení:

![image](./media/remote-desktop/no2.png)

Přihlaste se pomocí uživatelského jména a hesla z virtuálního počítače s Linuxem.

## <a name="next-steps"></a>Další postup
Další informace o používání xrdp najdete v tématu [ http://www.xrdp.org/ ](http://www.xrdp.org/).
