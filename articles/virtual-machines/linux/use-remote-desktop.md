---
title: Použití vzdálené plochy k virtuálnímu počítači s Linuxem v Azure | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat a nakonfigurovat vzdálené plochy (xrdp) pro připojení k virtuálnímu počítači s Linuxem v Azure pomocí grafických nástrojů
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: aa1891ecec139746d6051dcabdb3c9db4f6062c6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996345"
---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Instalace a konfigurace vzdálené plochy pro připojení k virtuálnímu počítači s Linuxem v Azure
Virtuální počítače s Linuxem (VM) v Azure jsou obvykle spravovat z příkazového řádku pomocí připojení (SSH secure shell). Při nové po Linux, nebo pro rychlé řešení problémů s scénáře, využívání vzdálené plochy může být jednodušší. Tento článek podrobně popisuje, jak nainstalovat a nakonfigurovat desktopové prostředí ([xfce](https://www.xfce.org)) a vzdálené plochy ([xrdp](http://www.xrdp.org)) pro virtuální počítač s Linuxem pomocí modelu nasazení Resource Manager.


## <a name="prerequisites"></a>Požadavky
Tento článek vyžaduje existující Ubuntu 16.04 LTS virtuální počítač v Azure. Pokud potřebujete vytvořit virtuální počítač, použijte jednu z následujících metod:

- [Rozhraní příkazového řádku Azure](quick-create-cli.md)
- [Azure Portal](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Instalovat desktopové prostředí na virtuální počítač s Linuxem
Většina virtuálních počítačů s Linuxem v Azure nemají desktopové prostředí, ve výchozím nastavení nainstalovaná. Virtuální počítače s Linuxem se běžně spravují pomocí připojení SSH a místo desktopové prostředí. Existují různé desktopové prostředí v Linuxu, která můžete použít. V závislosti na vašem výběru desktopové prostředí může využívat jeden až 2 GB místa na disku a trvat 5 až 10 minut, než instalace a konfigurace požadované balíčky.

Následující příklad nainstaluje jednoduchá [xfce4](https://www.xfce.org/) desktopové prostředí na virtuální počítač s Ubuntu 16.04 LTS. Příkazy pro jiné distribuce se mírně liší (použijte `yum` instalace na Red Hat Enterprise Linux a konfigurace odpovídající `selinux` pravidla, nebo použijte `zypper` k instalaci na SUSE, třeba).

První, přístup k vašemu virtuálnímu počítači přes SSH. Následující příklad se připojí k virtuálnímu počítači s názvem *myvm.westus.cloudapp.azure.com* k uživatelskému jménu *azureuser*. Použijte vlastní hodnoty:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Pokud používáte Windows a potřebujete další informace o používání SSH, přečtěte si téma [klíče, jak použít SSH s Windows](ssh-from-windows.md).

V dalším kroku nainstalovat s použitím xfce `apt` následujícím způsobem:

```bash
sudo apt-get update
sudo apt-get install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Instalace a konfigurace serveru vzdálené plochy
Teď, když máte desktopové prostředí nainstalovaná, nakonfigurujte služby vzdálené plochy k naslouchání pro příchozí připojení. [xrdp](http://xrdp.org) je opensourcový server protokolu RDP (Remote Desktop), který je k dispozici ve většině distribucí systému Linux a dobře funguje s xfce. Na svém virtuálním počítači se systémem Ubuntu nainstalujte xrdp následujícím způsobem:

```bash
sudo apt-get install xrdp
```

Řekněte xrdp jaké desktopové prostředí pro použití při spuštění relace. Nakonfigurujte xrdp určený xfce jako desktopové prostředí:

```bash
echo xfce4-session >~/.xsession
```

Restartujte službu xrdp změny se projeví následujícím způsobem:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Nastavení hesla místního uživatelského účtu
Pokud heslo pro uživatelský účet vytvořili jste vytvořili virtuální počítač, tento krok přeskočte. Pokud používáte ověřování pomocí klíče SSH pouze a nemají heslo místního účtu nastavit, zadejte heslo, než použijete xrdp přihlásit ke svému virtuálnímu počítači. xrdp nemůže přijmout klíče SSH pro ověřování. Následující příklad určuje heslo pro uživatelský účet *azureuser*:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Zadání hesla neaktualizuje konfigurace SSHD tak, aby povolovala přihlašování pomocí hesla, pokud aktuálně neexistuje. Z hlediska zabezpečení může se chcete připojit k virtuálnímu počítači pomocí tunelu SSH pomocí ověřování založeného na klíč a připojte se k xrdp. Pokud ano, přeskočte následující kroky k vytvoření pravidlo skupiny zabezpečení sítě umožňující provoz vzdálené plochy.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Vytvoření pravidla skupiny zabezpečení sítě pro provoz vzdálené plochy
Pro povolení provozu vzdálené plochy k dosažení virtuálního počítače s Linuxem, zabezpečení sítě skupiny pravidlo musí být vytvořen, který umožňuje TCP na portu 3389 pro přístup k virtuálnímu počítači. Další informace o pravidlech skupiny zabezpečení sítě najdete v tématu [co je skupina zabezpečení sítě?](../../virtual-network/security-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Můžete také [pomocí webu Azure portal k vytvoření pravidla skupiny zabezpečení sítě](../windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Následující příklad vytvoří pravidlo skupiny zabezpečení sítě s [az vm open-port](/cli/azure/vm#az-vm-open-port) na portu *3389*. Z příkazového řádku Azure ne relaci SSH k virtuálnímu počítači, otevřete následující pravidlo skupiny zabezpečení sítě:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Připojení virtuálního počítače s Linuxem pomocí klienta vzdálené plochy
Otevřete váš místní klient služby Vzdálená plocha a připojte k IP adresu nebo název DNS virtuálního počítače s Linuxem. Zadejte uživatelské jméno a heslo pro uživatelský účet na váš virtuální počítač následujícím způsobem:

![Připojte se k xrdp pomocí klienta vzdálené plochy](./media/use-remote-desktop/remote-desktop-client.png)

Po ověření, se načtou xfce desktopové prostředí a vypadat podobně jako v následujícím příkladu:

![desktopové prostředí xfce prostřednictvím xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)

Pokud váš místní klient RDP používá ověření na úrovni sítě (NLA), budete muset zakázat toto nastavení připojení. XRDP NLA aktuálně nepodporuje. Můžete se také podívat na alternativní řešení RDP, které podporují NLA, jako je například [FreeRDP](http://www.freerdp.com).


## <a name="troubleshoot"></a>Řešení potíží
Pokud se nemůžete připojit k virtuálním počítačům s Linuxem pomocí klienta vzdálené plochy, použijte `netstat` na virtuální počítač s Linuxem k ověření, že je váš virtuální počítač naslouchání pro připojení RDP:

```bash
sudo netstat -plnt | grep rdp
```

Následující příklad ukazuje virtuální počítač naslouchá na portu TCP 3389 podle očekávání:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Pokud *xrdp sesman* služba nenaslouchá, na Virtuálním počítači se systémem Ubuntu restartujte službu následujícím způsobem:

```bash
sudo service xrdp restart
```

Zkontrolujte protokoly */var/log* na vašem virtuálním počítači se systémem Ubuntu pro označení důvod, proč služba nereaguje. Během pokusu o připojení ke vzdálené ploše Chcete-li zobrazit všechny chyby, můžete také sledovat syslog:

```bash
tail -f /var/log/syslog
```

Ostatní Linuxové distribuce, jako je Red Hat Enterprise Linux a SUSE může mít různé způsoby, jak restartování služby a umístění souboru protokolu alternativní ke kontrole.

Pokud neobdrží žádnou odpověď klientovi vzdálené plochy a nezobrazí všechny události v protokolu systému, toto chování znamená, že provozu vzdálené plochy nelze kontaktovat virtuálního počítače. Zkontrolujte vaše pravidla skupin zabezpečení sítě k zajištění, že máte pravidlo pro povolení protokolu TCP na portu 3389. Další informace najdete v tématu [řešit problémy s připojením aplikace](../windows/troubleshoot-app-connection.md).


## <a name="next-steps"></a>Další postup
Další informace o vytváření a používání klíčů SSH s Linuxovými virtuálními počítači najdete v tématu [vytvoření klíčů SSH pro virtuální počítače s Linuxem v Azure](mac-create-ssh-keys.md).

Informace o použití SSH z Windows najdete v tématu [klíče, jak použít SSH s Windows](ssh-from-windows.md).

