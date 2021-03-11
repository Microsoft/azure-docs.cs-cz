---
title: Použití xrdp se systémem Linux
description: Přečtěte si, jak nainstalovat a nakonfigurovat vzdálenou plochu (xrdp) pro připojení k virtuálnímu počítači se systémem Linux v Azure pomocí grafických nástrojů.
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cynthn
ms.openlocfilehash: 1f7eb3b38b4ae04e81839fce2b14c1a84f3f0204
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564590"
---
# <a name="install-and-configure-xrdp-to-use-remote-desktop-with-ubuntu"></a>Instalace a konfigurace xrdp pro používání vzdálené plochy s Ubuntu

Virtuální počítače se systémem Linux v Azure jsou obvykle spravovány z příkazového řádku pomocí připojení Secure Shell (SSH). Když je novinkou pro Linux nebo rychlé řešení potíží, může být používání vzdálené plochy jednodušší. Tento článek podrobně popisuje, jak nainstalovat a nakonfigurovat desktopové prostředí ([desktop Xfce](https://www.xfce.org)) a vzdálenou plochu ([xrdp](http://xrdp.org)) pro virtuální počítač Linux se systémem Ubuntu.

Článek byl WRITEN a testován pomocí virtuálního počítače s Ubuntu 18,04. 

## <a name="prerequisites"></a>Požadavky

Tento článek vyžaduje existující virtuální počítač s Ubuntu 18,04 LTS v Azure. Pokud potřebujete vytvořit virtuální počítač, použijte jednu z následujících metod:

- Rozhraní příkazového [řádku Azure](quick-create-cli.md)
- [Azure Portal](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Instalace desktopového prostředí na VIRTUÁLNÍm počítači se systémem Linux

Většina virtuálních počítačů se systémem Linux v Azure nemá ve výchozím nastavení nainstalované desktopové prostředí. Virtuální počítače se systémem Linux se běžně spravují pomocí připojení SSH, nikoli desktopové prostředí. V systému Linux jsou k dispozici různé desktopové prostředí, které si můžete vybrat. V závislosti na vaší volbě desktopového prostředí může aplikace využívat jednu až 2 GB místa na disku a instalaci a konfiguraci všech požadovaných balíčků trvá 5 až 10 minut.

Následující příklad nainstaluje zjednodušené desktopové prostředí [Xfce4](https://www.xfce.org/) na virtuální počítač s Ubuntu 18,04 LTS. Příkazy pro ostatní distribuce se mírně liší (použijte `yum` k instalaci na Red Hat Enterprise Linux a ke konfiguraci vhodných `selinux` pravidel nebo použijte `zypper` k instalaci na SUSE).

Nejdřív k vašemu VIRTUÁLNÍmu počítači připojte SSH. Následující příklad se připojí k virtuálnímu počítači s názvem *myvm.westus.cloudapp.Azure.com* s uživatelským jménem *azureuser*. Použijte vlastní hodnoty:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Pokud používáte systém Windows a potřebujete další informace o použití SSH, přečtěte si téma [použití klíčů ssh v systému Windows](ssh-from-windows.md).

Dále nainstalujte desktop Xfce pomocí `apt` následujícího postupu:

```bash
sudo apt-get update
sudo apt-get -y install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Instalace a konfigurace serveru vzdálené plochy
Teď, když máte nainstalované desktopové prostředí, nakonfigurujte službu Vzdálená plocha, aby naslouchala příchozím připojením. [xrdp](http://xrdp.org) je server open source protokol RDP (Remote Desktop Protocol) (RDP), který je k dispozici ve většině distribucí systému Linux, a funguje dobře s desktop Xfce. Nainstalujte xrdp na virtuální počítač s Ubuntu následujícím způsobem:

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

Sdělte xrdp, jaké prostředí plochy se má použít při spuštění relace. Nakonfigurujte xrdp tak, aby jako své desktopové prostředí používal desktop Xfce, jak je znázorněno níže:

```bash
echo xfce4-session >~/.xsession
```

Restartujte službu xrdp, aby se změny projevily takto:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Nastavit heslo účtu místního uživatele
Pokud jste při vytváření virtuálního počítače vytvořili heslo pro svůj uživatelský účet, tento krok přeskočte. Pokud používáte jenom ověřování pomocí klíče SSH a nemáte nastavené heslo místního účtu, před použitím xrdp k přihlášení k VIRTUÁLNÍmu počítači zadejte heslo. xrdp nemůže přijmout klíče SSH pro ověřování. Následující příklad určuje heslo pro uživatelský účet *azureuser*:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Zadání hesla neaktualizuje konfiguraci SSHD, aby povolovala přihlášení k heslům, pokud v tuto chvíli neplatí. Z hlediska zabezpečení se můžete chtít připojit k VIRTUÁLNÍmu počítači pomocí tunelového propojení SSH pomocí ověřování založeného na klíčích a pak se připojit k xrdp. Pokud ano, přeskočte následující krok vytvoření pravidla skupiny zabezpečení sítě, které povolí přenos vzdálené plochy.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Vytvoření pravidla skupiny zabezpečení sítě pro provoz vzdálené plochy
Pokud chcete povolit, aby se provoz vzdálené plochy dostal k vašemu VIRTUÁLNÍmu počítači Linux, je nutné vytvořit pravidlo skupiny zabezpečení sítě, které umožňuje TCP na portu 3389 k dosažení vašeho virtuálního počítače. Další informace o pravidlech skupin zabezpečení sítě najdete v tématu [co je skupina zabezpečení sítě](../../virtual-network/network-security-groups-overview.md) . [K vytvoření pravidla skupiny zabezpečení sítě můžete použít taky Azure Portal](../windows/nsg-quickstart-portal.md).

Následující příklad vytvoří pravidlo skupiny zabezpečení sítě pomocí [AZ VM Open-port](/cli/azure/vm#az-vm-open-port) na portu *3389*. Z Azure CLI, ne z relace SSH k vašemu VIRTUÁLNÍmu počítači, otevřete následující pravidlo skupiny zabezpečení sítě:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Připojení virtuálního počítače se systémem Linux pomocí klienta vzdálené plochy

Otevřete místního klienta vzdálené plochy a připojte se k IP adrese nebo názvu DNS virtuálního počítače se systémem Linux. 

:::image type="content" source="media/use-remote-desktop/remote-desktop.png" alt-text="Snímek obrazovky klienta vzdálené plochy":::

Zadejte uživatelské jméno a heslo pro uživatelský účet na svém VIRTUÁLNÍm počítači následujícím způsobem:

:::image type="content" source="media/use-remote-desktop/xrdp-login.png" alt-text="Snímek obrazovky s přihlašovací obrazovkou xrdp":::

Po ověření se desktop Xfce desktopové prostředí načte a bude vypadat podobně jako v následujícím příkladu:

![desktop Xfce desktopové prostředí prostřednictvím xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)

Pokud Váš místní klient RDP používá ověřování na úrovni sítě (NLA), možná budete muset toto nastavení připojení zakázat. XRDP v současné době nepodporuje NLA. Můžete se také podívat na alternativní řešení protokolu RDP, která podporují NLA, například [FreeRDP](https://www.freerdp.com).


## <a name="troubleshoot"></a>Řešení potíží
Pokud se k VIRTUÁLNÍmu počítači se systémem Linux nemůžete připojit pomocí klienta vzdálené plochy, pomocí nástroje `netstat` na virtuálním počítači se systémem Linux ověřte, zda váš virtuální počítač naslouchá připojením RDP následujícím způsobem:

```bash
sudo netstat -plnt | grep rdp
```

Následující příklad ukazuje, že virtuální počítač naslouchá na portu TCP 3389, jak bylo očekáváno:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Pokud služba *xrdp-sesman* nenaslouchá, na virtuálním počítači Ubuntu restartujte službu následujícím způsobem:

```bash
sudo service xrdp restart
```

Podívejte se na protokoly v */var/log* na virtuálním počítači s Ubuntu, kde najdete indikaci, proč služba nereaguje. Protokol syslog můžete monitorovat také při pokusu o připojení ke vzdálené ploše, aby se zobrazily případné chyby:

```bash
tail -f /var/log/syslog
```

Jiné distribuce systému Linux, například Red Hat Enterprise Linux a SUSE, mohou mít různé způsoby, jak restartovat služby a alternativní umístění souborů protokolu ke kontrole.

Pokud neobdržíte žádnou odpověď v klientovi vzdálené plochy a nezobrazují žádné události v systémovém protokolu, toto chování indikuje, že přenos z vzdálené plochy se nemůže připojit k virtuálnímu počítači. Zkontrolujte pravidla skupiny zabezpečení sítě, abyste měli jistotu, že máte pravidlo, které povoluje TCP na portu 3389. Další informace najdete v tématu [řešení potíží s připojením k aplikacím](../troubleshooting/troubleshoot-app-connection.md).


## <a name="next-steps"></a>Další kroky
Další informace o vytváření a používání klíčů SSH s virtuálními počítači se systémem Linux najdete v tématu [vytváření klíčů ssh pro virtuální počítače se systémem Linux v Azure](mac-create-ssh-keys.md).

Informace o použití SSH ze systému Windows najdete v tématu [použití klíčů ssh v systému Windows](ssh-from-windows.md).
