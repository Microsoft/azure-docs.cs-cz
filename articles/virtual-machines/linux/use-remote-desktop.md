---
title: Použití služby Vzdálená plocha pro virtuální počítač s Linuxem v Azure | Microsoft Docs
description: Postup instalace a konfigurace vzdálené plochy (xrdp) pro připojení k virtuální počítač s Linuxem v Azure pomocí nástroje s grafickým rozhraním
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: c47822bebdc8b3cc8896fe56b8f9a4ce317495c3
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Instalace a konfigurace vzdálené plochy pro připojení k virtuální počítač s Linuxem v Azure
Linux virtuálních počítačů (VM) v Azure jsou obvykle spravovat z příkazového řádku pomocí připojení zabezpečené shell (SSH). Při vydání nových do systému Linux, nebo pro rychlé řešení potíží scénáře, může být snazší pomocí vzdálené plochy. Tento článek podrobně popisují postup instalace a konfigurace prostředí plochy ([xfce](https://www.xfce.org)) a vzdálené plochy ([xrdp](http://www.xrdp.org)) pro váš virtuální počítač s Linuxem pomocí modelu nasazení Resource Manager.


## <a name="prerequisites"></a>Požadavky
Tento článek vyžaduje existující 16.04 LTS virtuálního počítače s Ubuntu v Azure. Pokud potřebujete k vytvoření virtuálního počítače, použijte jednu z následujících metod:

- [Rozhraní příkazového řádku Azure 2.0](quick-create-cli.md)
- [Portálu Azure](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Instalace prostředí plochy na virtuálním počítačům s Linuxem
Většina virtuální počítače s Linuxem v Azure, nemají prostředí plochy nainstalované ve výchozím nastavení. Virtuální počítače s Linuxem se běžně spravují pomocí připojení SSH a místo prostředí plochy. Existují různé prostředí plochy v systému Linux, které můžete. V závislosti na zvoleném prostředí plochy může využívat jeden až 2 GB místa na disku a trvat 5 až 10 minut, nainstalovat a nakonfigurovat všechny požadované balíčky.

Následující příklad nainstaluje jednoduchá [xfce4](https://www.xfce.org/) prostředí plochy na virtuálního počítače s Ubuntu 16.04 LTS. Příkazy pro jiné distribuce jsou mírně odlišné (použijte `yum` na Red Hat Enterprise Linux nainstalovat a nakonfigurovat příslušný `selinux` pravidla, nebo použijte `zypper` k instalaci na SUSE, třeba).

První, SSH k virtuálnímu počítači. V následujícím příkladu se připojuje k virtuálnímu počítači s názvem *myvm.westus.cloudapp.azure.com* k uživatelskému jménu *azureuser*:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Pokud používáte systém Windows a jsou nutné další informace o používání SSH, přečtěte si téma [klíče použití SSH se systémem Windows](ssh-from-windows.md).

Dále nainstalujte pomocí xfce `apt` následujícím způsobem:

```bash
sudo apt-get update
sudo apt-get install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Instalace a konfigurace serveru vzdálené plochy
Teď, když máte prostředí plochy nainstalovat, nakonfigurujte služby vzdálené plochy k přijímat příchozí připojení. [xrdp](http://xrdp.org) serveru protokolu RDP (Remote Desktop) s otevřeným zdrojem, která je k dispozici na většině Linuxových distribucích a pracuje s xfce. Nainstalujte xrdp vašeho virtuálního počítače s Ubuntu následujícím způsobem:

```bash
sudo apt-get install xrdp
```

Řekněte xrdp jaké prostředí plochy použít při spuštění relace. Nakonfigurujte xrdp používat xfce jako prostředí plochy následujícím způsobem:

```bash
echo xfce4-session >~/.xsession
```

Restartujte službu xrdp změny se projeví následujícím způsobem:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Nastavení hesla místního uživatelského účtu
Pokud jste vytvořili heslo pro uživatelský účet při vytváření virtuálního počítače, tento krok přeskočte. Pokud používáte ověření pomocí klíče SSH pouze a nemají heslo místního účtu nastaven, zadejte heslo, než použijete xrdp k přihlášení k virtuálnímu počítači. xrdp nemůže přijmout klíče SSH pro ověřování. Následující příklad určuje heslo pro uživatelský účet *azureuser*:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Zadání hesla nelze aktualizovat konfiguraci SSHD k povolení přihlášení heslo, pokud je aktuálně nepoužívá. Z hlediska zabezpečení, můžete se chcete připojit k virtuálnímu počítači pomocí tunelového propojení SSH pomocí ověřování na základě klíčů a potom se připojte k xrdp. Pokud ano, přejděte na vytváření pravidla skupiny zabezpečení sítě umožňující vzdálené plochy přenos následující krok.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Vytvoření pravidla skupiny zabezpečení sítě pro přenosy vzdálené plochy
Pokud chcete povolit přenosy vzdálené plochy k dosažení virtuálním počítačům s Linuxem, zabezpečení sítě skupiny pravidlo musí být vytvořen, který umožňuje TCP na portu 3389 k dosažení virtuálního počítače. Další informace o pravidel skupiny zabezpečení sítě najdete v tématu [co je skupina zabezpečení sítě?](../../virtual-network/security-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Můžete také [pomocí portálu Azure k vytvoření pravidla skupiny zabezpečení sítě](../windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Následující příklad vytvoří pravidlo skupiny zabezpečení sítě s [az virtuálních počítačů open-port](/cli/azure/vm#az_vm_open_port) na portu *3389*.

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Připojení virtuálním počítačům s Linuxem pomocí klienta vzdálené plochy
Otevřete váš místní klient vzdálené plochy a připojení k IP adresu nebo název DNS vašeho virtuálního počítače s Linuxem. Zadejte uživatelské jméno a heslo pro uživatelský účet na vašem virtuálním počítači následujícím způsobem:

![Připojení k xrdp pomocí vašeho klienta vzdálené plochy](./media/use-remote-desktop/remote-desktop-client.png)

Po ověření prostředí plochy xfce načíst a vypadat podobně jako v následujícím příkladu:

![prostředí plochy xfce prostřednictvím xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)


## <a name="troubleshoot"></a>Řešení potíží
Pokud se nemůžete připojit k virtuálním počítačům s Linuxem pomocí klienta vzdálené plochy, použijte `netstat` na virtuálním počítačům s Linuxem k ověření, že je váš virtuální počítač nenaslouchá pro připojení RDP:

```bash
sudo netstat -plnt | grep rdp
```

Následující příklad ukazuje, virtuální počítač naslouchá na TCP port 3389 podle očekávání:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Pokud *xrdp sesman* nenaslouchá služby, na virtuálního počítače s Ubuntu restartujte službu následujícím způsobem:

```bash
sudo service xrdp restart
```

Zkontrolujte protokoly */var/log* na vaše virtuálního počítače s Ubuntu pro indikaci, proč služba nereaguje. Také můžete monitorovat syslog při pokusu o připojení ke vzdálené ploše zobrazíte všechny chyby:

```bash
tail -f /var/log/syslog
```

Další Linuxových distribucích například Red Hat Enterprise Linux a SUSE může mít různé způsoby, jak restartujte služby a umístění souborů alternativní protokolu ke kontrole.

Pokud neobdrží všechny odpovědi v klientovi vzdálené plochy a nejsou vidět všechny události v systémovém protokolu, toto chování Určuje, že vzdálené plochy provoz nemůže připojit virtuální počítač. Zkontrolujte vaše pravidel skupiny zabezpečení sítě k zajištění, že máte pravidlo pro povolení TCP na portu 3389. Další informace najdete v tématu [problémů s připojením aplikace](../windows/troubleshoot-app-connection.md).


## <a name="next-steps"></a>Další postup
Další informace o vytváření a používání klíčů SSH s virtuální počítače s Linuxem najdete v tématu [vytvořit SSH klíče pro virtuální počítače s Linuxem v Azure](mac-create-ssh-keys.md).

Informace o používání SSH ze systému Windows najdete v tématu [klíče použití SSH se systémem Windows](ssh-from-windows.md).

