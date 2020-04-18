---
title: Použití vzdálené plochy k virtuálnímu počítači SIO v Azure
description: Naučte se, jak nainstalovat a nakonfigurovat vzdálenou plochu (xrdp) pro připojení k virtuálnímu počítači Sn Linux v Azure pomocí grafických nástrojů
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/12/2019
ms.author: cynthn
ms.openlocfilehash: 2e97442d4104f52c1a76ba8cd1d81c99508bb242
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605187"
---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Instalace a konfigurace vzdálené plochy pro připojení k virtuálnímu počítači SIP v Azure
Virtuální počítače (VM) Linuxu v Azure se obvykle spravují z příkazového řádku pomocí zabezpečeného připojení prostředí (SSH). Pokud je v Linuxu nový nebo pro rychlé scénáře řešení potíží, může být použití vzdálené plochy jednodušší. Tento článek podrobně popisuje, jak nainstalovat a nakonfigurovat prostředí plochy ([xfce](https://www.xfce.org)) a vzdálenou plochu[(xrdp)](http://xrdp.org)pro virtuální počítač s Linuxem pomocí modelu nasazení Resource Manageru.


## <a name="prerequisites"></a>Požadavky
Tento článek vyžaduje existující ubuntu 18.04 LTS virtuální počítač v Azure. Pokud potřebujete vytvořit virtuální hod, použijte jednu z následujících metod:

- [Cli Azure](quick-create-cli.md)
- [Portál Azure](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Instalace desktopového prostředí na virtuální počítač s Linuxem
Většina virtuálních počítačů s Linuxem v Azure nemá ve výchozím nastavení nainstalované desktopové prostředí. Virtuální počítače s Linuxem se běžně spravují pomocí připojení SSH, nikoli pomocí desktopového prostředí. Existují různé desktopové prostředí v Linuxu, které si můžete vybrat. V závislosti na zvoleném prostředí plochy může spotřebovat jeden až 2 GB místa na disku a instalace a konfigurace všech požadovaných balíčků trvat 5 až 10 minut.

Následující příklad nainstaluje zjednodušené desktopové prostředí [xfce4](https://www.xfce.org/) na virtuální počítač Ubuntu 18.04 LTS. Příkazy pro jiné distribuce se `yum` mírně liší (slouží k instalaci `selinux` na Red `zypper` Hat Enterprise Linux a konfigurovat příslušná pravidla, nebo použít k instalaci na SUSE, například).

Za prvé, SSH do vašeho virtuálního počítače. Následující příklad se připojí k virtuálnímu počítači s názvem *myvm.westus.cloudapp.azure.com* s uživatelským jménem *azureuser*. Použijte své vlastní hodnoty:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Pokud používáte systém Windows a potřebujete další informace o používání ssh, přečtěte si informace [o použití klíčů SSH se systémem Windows](ssh-from-windows.md).

Dále nainstalujte xfce následujícím způsobem: `apt`

```bash
sudo apt-get update
sudo apt-get -y install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Instalace a konfigurace serveru vzdálené plochy
Nyní, když máte nainstalované desktopové prostředí, nakonfigurujte službu vzdálené plochy tak, aby naslouchala příchozím připojením. [xrdp](http://xrdp.org) je open source server RDP (Remote Desktop Protocol), který je k dispozici na většině distribucí Linuxu a dobře spolupracuje s xfce. Nainstalujte xrdp na vašem Ubuntu VM takto:

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

Řekněte xrdp, jaké desktopové prostředí použít při spuštění relace. Nakonfigurujte xrdp takto používat xfce jako desktopové prostředí takto:

```bash
echo xfce4-session >~/.xsession
```

Změny se projeví následujícím způsobem:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Nastavení hesla místního uživatelského účtu
Pokud jste při vytváření virtuálního počítače vytvořili heslo pro svůj uživatelský účet, tento krok přeskočte. Pokud používáte jenom ověřování pomocí klíče SSH a nemáte nastavené heslo místního účtu, zadejte heslo před použitím xrdpu k přihlášení k virtuálnímu počítači. xrdp nemůže přijmout klíče SSH pro ověřování. Následující příklad určuje heslo pro uživatelský účet *azureuser*:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Zadání hesla neaktualizuje konfiguraci SSHD, aby bylo možné povolit přihlášení hesla, pokud v současné době není. Z hlediska zabezpečení můžete chtít připojit k virtuálnímu počítači pomocí tunelového propojení SSH pomocí ověřování na základě klíče a pak se připojit k xrdp. Pokud ano, přeskočte následující krok při vytváření pravidla skupiny zabezpečení sítě, které umožní provoz na vzdálené ploše.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Vytvoření pravidla skupiny zabezpečení sítě pro provoz na vzdálené ploše
Chcete-li povolit provoz vzdálené plochy k dosažení vašeho virtuálního počítače s Linuxem, je třeba vytvořit pravidlo skupiny zabezpečení sítě, které umožňuje TCP na portu 3389 dosáhnout vašeho virtuálního počítače. Další informace o pravidlech skupiny zabezpečení sítě naleznete [v tématu Co je skupina zabezpečení sítě?](../../virtual-network/security-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Portál Azure můžete také [použít k vytvoření pravidla skupiny zabezpečení sítě](../windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Následující příklad vytvoří pravidlo skupiny zabezpečení sítě s [otevřeným portem az vm](/cli/azure/vm#az-vm-open-port) na portu *3389*. Z azure cli, ne relace SSH na virtuální počítač, otevřete následující pravidlo skupiny zabezpečení sítě:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Připojení virtuálního počítače s Linuxem ke klientovi vzdálené plochy
Otevřete místního klienta vzdálené plochy a připojte se k IP adrese nebo názvu DNS virtuálního počítače s Linuxem. Zadejte uživatelské jméno a heslo pro uživatelský účet na vašem virtuálním počítači takto:

![Připojení ke službě xrdp pomocí klienta vzdálené plochy](./media/use-remote-desktop/remote-desktop-client.png)

Po ověření se desktopové prostředí xfce načte a bude vypadat podobně jako v následujícím příkladu:

![desktopové prostředí xfce přes xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)

Pokud místní klient RDP používá ověřování na úrovni sítě (NLA), bude pravděpodobně nutné toto nastavení připojení zakázat. XRDP aktuálně nepodporuje nla. Můžete se také podívat na alternativní rdp řešení, která podporují NLA, jako je [FreeRDP](https://www.freerdp.com).


## <a name="troubleshoot"></a>Řešení potíží
Pokud se nemůžete připojit k virtuálnímu počítači `netstat` Linuxu pomocí klienta vzdálené plochy, použijte na virtuálním počítači SIP ověření, že váš virtuální počítač naslouchá připojením RDP takto:

```bash
sudo netstat -plnt | grep rdp
```

Následující příklad ukazuje naslouchající virtuální počítač na portu TCP 3389 podle očekávání:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Pokud služba *xrdp-sesman* neposlouchá, na virtuálním počítači Ubuntu restartujte službu takto:

```bash
sudo service xrdp restart
```

Zkontrolujte protokoly v */var/log* na vašem Ubuntu VM pro indikace, proč služba nemusí reagovat. Můžete také sledovat syslog během pokusu o připojení ke vzdálené ploše zobrazit všechny chyby:

```bash
tail -f /var/log/syslog
```

Jiné distribuce Linuxu, jako je Red Hat Enterprise Linux a SUSE, mohou mít různé způsoby restartování služeb a alternativní umístění souborů protokolu ke kontrole.

Pokud neobdržíte žádnou odpověď v klientovi vzdálené plochy a nevidíte žádné události v systémovém protokolu, toto chování znamená, že provoz vzdálené plochy nemůže dosáhnout virtuálního počítače. Zkontrolujte pravidla skupiny zabezpečení sítě a ujistěte se, že máte pravidlo povolit tcp na portu 3389. Další informace naleznete [v tématu Poradce při potížích s připojením aplikací](../windows/troubleshoot-app-connection.md).


## <a name="next-steps"></a>Další kroky
Další informace o vytváření a používání klíčů SSH pomocí virtuálních počítačích s Linuxem najdete v tématu [Vytváření klíčů SSH pro virtuální počítače s Linuxem v Azure](mac-create-ssh-keys.md).

Informace o používání ssh ze systému Windows naleznete v [tématu Použití klíčů SSH v systému Windows](ssh-from-windows.md).

