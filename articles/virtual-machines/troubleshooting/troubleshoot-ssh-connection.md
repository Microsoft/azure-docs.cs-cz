---
title: Řešení potíží s připojením SSH k virtuálnímu počítači Azure | Dokumenty společnosti Microsoft
description: Jak řešit problémy, jako je například "Připojení SSH se nezdařilo" nebo "SSH připojení odmítnuto" pro virtuální počítač Azure se systémem Linux.
keywords: Ssh připojení odmítnuto, ssh error, azure ssh, SSH připojení se nezdařilo
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 05/30/2017
ms.author: genli
ms.openlocfilehash: f221a0bdf579dbbf42ecf64e18803decfb718456
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060661"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Řešení potíží s připojeními SSH k virtuálnímu počítači Azure s Linuxem, která selžou, skončí chybou nebo se zamítnou
Tento článek vám pomůže najít a opravit problémy, ke kterým dochází v důsledku chyb secure shellu (SSH), selhání připojení SSH nebo SSH je odmítnutpři pokusu o připojení k virtuálnímu počítači Linux (VM). K řešení a řešení problémů s připojením můžete použít portál Azure, Azure CLI nebo Rozšíření přístupu k virtuálním počítači pro Linux.


Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/forums/). Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**. Informace o používání podpory Azure načtete v [nejčastějších dotazech k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Rychlé kroky řešení potíží
Po každém kroku řešení potíží zkuste znovu připojit k virtuálnímu virtuálnímu zařízení.

1. [Resetujte konfiguraci SSH](#reset-config).
2. [Obnovte pověření](#reset-credentials) pro uživatele.
3. Ověřte, zda pravidla [skupiny zabezpečení sítě](../../virtual-network/security-overview.md) povolují provoz SSH.
   * Ujistěte se, že existuje [pravidlo skupiny zabezpečení sítě,](#security-rules) které povoluje přenosy SSH (ve výchozím nastavení port TCP 22).
   * Přesměrování / mapování portů nelze použít bez použití správce zatížení Azure.
4. Zkontrolujte [stav prostředků virtuálního soudu](../../resource-health/resource-health-overview.md).
   * Ujistěte se, že virtuální ho disponizuje jako v pořádku.
   * Pokud máte [povolenou diagnostiku spouštění](boot-diagnostics.md), ověřte, zda virtuální adaptér nehlásí chyby při spuštění v protokolech.
5. [Restartujte virtuální počítač](#restart-vm).
6. [Znovu nasadit virtuální hod](#redeploy-vm).

Pokračujte ve čtení pro podrobnější kroky a vysvětlení řešení potíží.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Dostupné metody řešení problémů s připojením SSH
Pověření nebo konfiguraci SSH můžete obnovit jedním z následujících způsobů:

* [Azure portal](#use-the-azure-portal) – skvělé, pokud potřebujete rychle resetovat konfiguraci SSH nebo klíč SSH a nemáte nainstalované nástroje Azure.
* [Konzola Pro masovou konzolu virtuálního počítače](https://aka.ms/serialconsolelinux) Azure – sériová konzola virtuálního počítače bude fungovat bez ohledu na konfiguraci SSH a poskytne vám interaktivní konzolu pro váš virtuální počítač. Ve skutečnosti, "nemůže SSH" situace jsou konkrétně to, co sériová konzole byla navržena tak, aby pomohla vyřešit. Další podrobnosti najdete níže.
* [Azure CLI](#use-the-azure-cli) – pokud jste již na příkazovém řádku, rychle obnovit konfiguraci SSH nebo pověření. Pokud pracujete s klasickým virtuálním počítačem, můžete použít [klasické příkazové příkazové příkazové příkazy Azure](#use-the-azure-classic-cli).
* [Rozšíření Azure VMAccessForLinux](#use-the-vmaccess-extension) – vytvořte a znovu použijte soubory definic json k obnovení konfigurace SSH nebo pověření uživatele.

Po každém kroku řešení potíží zkuste znovu připojit k virtuálnímu počítači. Pokud se stále nemůžete připojit, vyzkoušejte další krok.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal
Portál Azure poskytuje rychlý způsob, jak obnovit konfiguraci SSH nebo přihlašovací údaje uživatele bez instalace nástrojů v místním počítači.

Chcete-li začít, vyberte virtuální počítač na webu Azure Portal. Přejděte dolů do části **Podpora + Poradce při potížích** a vyberte Obnovit **heslo** jako v následujícím příkladu:

![Obnovení konfigurace nebo přihlašovacích údajů SSH na webu Azure Portal](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a><a id="reset-config" />Obnovení konfigurace SSH
Chcete-li obnovit konfiguraci `Reset configuration only` SSH, vyberte v části **Režim** jako na předchozím snímku obrazovky možnost **Aktualizovat**. Po dokončení této akce zkuste znovu získat přístup k virtuálnímu počítači.

### <a name="reset-ssh-credentials-for-a-user"></a><a id="reset-credentials" />Resetování přihlašovacích údajů SSH pro uživatele
Chcete-li obnovit pověření existujícího uživatele, vyberte buď `Reset SSH public key` nebo `Reset password` v části **Režim** jako na předchozím snímku obrazovky. Zadejte uživatelské jméno a klíč SSH nebo nové heslo a pak vyberte **Aktualizovat**.

Můžete také vytvořit uživatele s oprávněními sudo na virtuálním počítači z této nabídky. Zadejte nové uživatelské jméno a přidružené heslo nebo klíč SSH a vyberte **aktualizovat**.

### <a name="check-security-rules"></a><a id="security-rules" />Kontrola pravidel zabezpečení

Pomocí [ověření toku IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) můžete ověřit, jestli pravidlo ve skupině zabezpečení sítě blokuje provoz do nebo z virtuálního počítače. Můžete také zkontrolovat účinná pravidla skupiny zabezpečení, abyste zajistili, že příchozí pravidlo skupiny zabezpečení nsg existuje a má prioritu pro port SSH (výchozí 22). Další informace najdete [v tématu Použití účinných pravidel zabezpečení k řešení potíží s tokem provozu virtuálních montovací](../../virtual-network/diagnose-network-traffic-filter-problem.md).

### <a name="check-routing"></a>Zkontrolovat směrování

Pomocí funkce [dalšího směrování](../../network-watcher/network-watcher-check-next-hop-portal.md) sledování sítě můžete potvrdit, že trasa nebrání směrování přenosů do nebo z virtuálního počítače. Můžete také zkontrolovat efektivní trasy a zobrazit všechny efektivní trasy pro síťové rozhraní. Další informace najdete [v tématu Použití efektivní chodů k řešení potíží s tokem provozu virtuálních montovny](../../virtual-network/diagnose-network-routing-problem.md).

## <a name="use-the-azure-vm-serial-console"></a>Použití sériové konzoly virtuálního počítače Azure
[Konzola Xbox VM Serial Console](./serial-console-linux.md) poskytuje přístup k textové konzoli pro virtuální počítače s Linuxem. Konzolu můžete použít k řešení potíží s připojením SSH v interaktivním prostředí. Ujistěte se, že jste splnili požadavky pro použití [konzoly](./serial-console-linux.md#prerequisites) Serial Console a vyzkoušejte níže uvedené příkazy k dalšímu řešení potíží s připojením SSH.

### <a name="check-that-ssh-is-running"></a>Zkontrolujte, zda je spuštěns SSH
Pomocí následujícího příkazu můžete ověřit, jestli je na vašem virtuálním počítači spuštěný SSH:

```console
ps -aux | grep ssh
```

Pokud existuje nějaký výstup, SSH je v provozu.

### <a name="check-which-port-ssh-is-running-on"></a>Zkontrolujte, na kterém portu Je spuštěn SSH

Pomocí následujícího příkazu můžete zkontrolovat, na kterém portu SSH je spuštěn:

```console
sudo grep Port /etc/ssh/sshd_config
```

Váš výstup bude vypadat nějak takto:

```output
Port 22
```

## <a name="use-the-azure-cli"></a>Použití Azure CLI
Pokud jste tak ještě neučinili, nainstalujte nejnovější [azure cli](/cli/azure/install-az-cli2) a přihlaste se k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index).

Pokud jste vytvořili a nahráli vlastní bitovou kopii disku Linux, ujistěte se, že je nainstalován [Microsoft Azure Linux Agent](../extensions/agent-linux.md) verze 2.0.5 nebo novější. Pro virtuální počítače vytvořené pomocí iniciál Galerie je toto rozšíření přístupu už nainstalované a nakonfigurované pro vás.

### <a name="reset-ssh-configuration"></a>Obnovit konfiguraci SSH
Zpočátku můžete zkusit resetovat konfiguraci SSH na výchozí hodnoty a restartovat server SSH na virtuálním počítači. Tím se nezmění název uživatelského účtu, heslo ani klíče SSH.
Následující příklad používá [az vm user reset-ssh](/cli/azure/vm/user) obnovit konfiguraci `myVM` SSH na virtuálním počítači s názvem v `myResourceGroup`. Použijte své vlastní hodnoty takto:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Resetování přihlašovacích údajů SSH pro uživatele
Následující příklad používá [aktualizaci uživatele az vm](/cli/azure/vm/user) k obnovení `myUsername` `myPassword`pověření pro hodnotu `myVM` `myResourceGroup`zadanou v aplikaci na virtuálním počítači s názvem v . Použijte své vlastní hodnoty takto:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Pokud používáte ověřování pomocí klíče SSH, můžete obnovit klíč SSH pro daného uživatele. Následující příklad používá **az vm přístup set-linux-user** aktualizovat `~/.ssh/id_rsa.pub` klíč SSH `myUsername`uložené v pro `myVM` `myResourceGroup`uživatele s názvem , na virtuálním počítači s názvem v . Použijte své vlastní hodnoty takto:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Použití rozšíření VMAccess
Rozšíření pro přístup k virtuálnímu počítači pro Linux čte v souboru json, který definuje akce k provedení. Mezi tyto akce patří resetování sshd, resetování klíče SSH nebo přidání uživatele. Stále používáte Azure CLI k volání rozšíření VMAccess, ale můžete znovu použít soubory json přes více virtuálních počítačů v případě potřeby. Tento přístup umožňuje vytvořit úložiště souborů json, které pak mohou být volány pro dané scénáře.

### <a name="reset-sshd"></a>Obnovit sshd
Vytvořte soubor `settings.json` s názvem s následujícím obsahem:

```json
{
    "reset_ssh":"True"
}
```

Pomocí rozhraní příkazového příkazového `VMAccessForLinux` příkazu Azure pak zavoláte rozšíření k obnovení připojení SSHD zadáním souboru json. Následující příklad používá [sadu rozšíření az vm](/cli/azure/vm/extension) k obnovení `myVM` sshd na virtuálním počítači s názvem v `myResourceGroup`. Použijte své vlastní hodnoty takto:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Resetování přihlašovacích údajů SSH pro uživatele
Pokud se zdá, že sshd funguje správně, můžete obnovit přihlašovací údaje pro uživatele dárky. Chcete-li obnovit heslo pro uživatele, `settings.json`vytvořte soubor s názvem . Následující příklad obnoví pověření pro `myUsername` hodnotu zadanou v . `myPassword` Zadejte do `settings.json` souboru následující řádky pomocí vlastních hodnot:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Chcete-li obnovit klíč SSH pro uživatele, `settings.json`nejprve vytvořte soubor s názvem . Následující příklad obnoví pověření pro `myUsername` hodnotu zadanou v , `myPassword` `myVM` na `myResourceGroup`virtuálním počítači s názvem v . Zadejte do `settings.json` souboru následující řádky pomocí vlastních hodnot:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Po vytvoření souboru json použijte azure CLI volat `VMAccessForLinux` rozšíření obnovit pověření uživatele SSH zadáním souboru json. Následující příklad obnoví pověření na virtuálním `myVM` `myResourceGroup`počítači s názvem v . Použijte své vlastní hodnoty takto:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>Použití klasického příkazového příkazového příkazu Azure
Pokud jste tak ještě neučinili, [nainstalujte klasické vázačovací příkazové příkazové příkazy Azure a připojte se k předplatnému Azure](../../cli-install-nodejs.md). Ujistěte se, že používáte režim Správce prostředků následujícím způsobem:

```azurecli
azure config mode arm
```

Pokud jste vytvořili a nahráli vlastní bitovou kopii disku Linux, ujistěte se, že je nainstalován [Microsoft Azure Linux Agent](../extensions/agent-linux.md) verze 2.0.5 nebo novější. Pro virtuální počítače vytvořené pomocí iniciál Galerie je toto rozšíření přístupu už nainstalované a nakonfigurované pro vás.

### <a name="reset-ssh-configuration"></a>Obnovit konfiguraci SSH
Samotná konfigurace SSHD může být chybně nakonfigurována nebo služba zjistila chybu. Můžete obnovit SSHD, abyste se ujistili, že samotná konfigurace SSH je platná. Obnovení sshd by mělo být prvním krokem řešení potíží, který provedete.

Následující příklad obnoví sshd na virtuálním počítači s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`. Použijte vlastní názvy virtuálních počítačů a skupin prostředků takto:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Resetování přihlašovacích údajů SSH pro uživatele
Pokud se zdá, že SSHD funguje správně, můžete obnovit heslo pro uživatele dárky. Následující příklad obnoví pověření pro `myUsername` hodnotu zadanou v , `myPassword` `myVM` na `myResourceGroup`virtuálním počítači s názvem v . Použijte své vlastní hodnoty takto:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Pokud používáte ověřování pomocí klíče SSH, můžete obnovit klíč SSH pro daného uživatele. Následující příklad aktualizuje klíč SSH `~/.ssh/id_rsa.pub` uložený `myUsername`v aplikaci pro `myVM` `myResourceGroup`uživatele s názvem na virtuálním počítači s názvem v . Použijte své vlastní hodnoty takto:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="restart-a-vm"></a><a id="restart-vm" />Restartování virtuálního počítače
Pokud jste resetovali konfiguraci SSH a pověření uživatele nebo při tom došlo k chybě, můžete zkusit restartovat virtuální počítač a vyřešit základní problémy s výpočetními prostředky.

### <a name="azure-portal"></a>portál Azure
Pokud chcete restartovat virtuální počítač pomocí portálu Azure, vyberte virtuální počítač a pak vyberte **Restartovat** jako v následujícím příkladu:

![Restartování virtuálního počítače na webu Azure Portal](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
Následující příklad používá [restartování virtuálního počítače az](/cli/azure/vm) k restartování virtuálního počítače pojmenovaného `myVM` ve skupině prostředků s názvem `myResourceGroup`. Použijte své vlastní hodnoty takto:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Azure Classic CLI

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Následující příklad restartuje virtuální `myVM` počítač pojmenovaný `myResourceGroup`ve skupině prostředků s názvem . Použijte své vlastní hodnoty takto:

```console
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="redeploy-a-vm"></a><a id="redeploy-vm" />Opětovné nasazení virtuálního virtuálního montovana
Virtuální počítač můžete znovu nasadit do jiného uzlu v rámci Azure, což může opravit všechny základní problémy se sítí. Informace o opětovném nasazení virtuálního počítače najdete v [tématu Opětovné nasazení virtuálního počítače do nového uzlu Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Po dokončení této operace dochází ke ztrátě dočasných dat disku a budou aktualizovány dynamické adresy IP, které jsou přidruženy k virtuálnímu počítači.
>
>

### <a name="azure-portal"></a>portál Azure
Pokud chcete virtuální počítač znovu nasadit pomocí portálu Azure, vyberte virtuální počítač a přejděte dolů do části **Podpora + Řešení potíží.** Vyberte **Znovu nasadit** jako v následujícím příkladu:

![Opětovné nasazení virtuálního počítače na webu Azure Portal](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
Následující příklad použít [az vm znovu nasadit](/cli/azure/vm) `myVM` znovu nasadit `myResourceGroup`virtuálního virtuálního ms pojmenované ve skupině prostředků s názvem . Použijte své vlastní hodnoty takto:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Azure Classic CLI

Následující příklad znovu nasadí `myVM` virtuální hod `myResourceGroup`pojmenovaný ve skupině prostředků s názvem . Použijte své vlastní hodnoty takto:

```console
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>Virtuální počítače vytvořené pomocí modelu klasického nasazení

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Vyzkoušejte tyto kroky k vyřešení nejběžnějších selhání připojení SSH pro virtuální počítače, které byly vytvořeny pomocí klasického modelu nasazení. Po každém kroku zkuste znovu připojit k virtuálnímu virtuálnímu zařízení.

* Obnovte vzdálený přístup z [portálu Azure](https://portal.azure.com). Na webu Azure Portal vyberte virtuální počítač a pak vyberte **Obnovit vzdálené...**.
* Restartujte virtuální počítač. Na [webu Azure Portal](https://portal.azure.com)vyberte virtuální počítač a vyberte **Restartovat**.

* Znovu nasadit virtuální počítač do nového uzlu Azure. Informace o tom, jak znovu nasadit virtuální počítač, najdete v [tématu opětovné nasazení virtuálního počítače do nového uzlu Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

    Po dokončení této operace dojde ke ztrátě dočasných dat disku a budou aktualizovány dynamické adresy IP, které jsou přidruženy k virtuálnímu počítači.
* Postupujte podle pokynů v [části Jak resetovat heslo nebo SSH pro virtuální počítače s Linuxem:](../linux/classic/reset-access-classic.md)

  * Resetujte heslo nebo klíč SSH.
  * Vytvořte uživatelský účet *sudo.*
  * Resetujte konfiguraci SSH.
* Zkontrolujte stav prostředků virtuálního soudu pro všechny problémy platformy.<br>
     Vyberte virtuální počítač a posuňte se dolů **Nastavení** > **Zkontrolujte stav**.

## <a name="additional-resources"></a>Další zdroje
* Pokud se vám stále nedaří SSH do virtuálního počítače po provedení následujícíkroky, [přečtěte](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) si podrobnější kroky řešení potíží pro kontrolu dalších kroků k vyřešení problému.
* Další informace o řešení potíží s přístupem k aplikacím [najdete v tématu Poradce při potížích s přístupem k aplikaci spuštěné na virtuálním počítači Azure.](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Další informace o řešení potíží s virtuálními počítači, které byly vytvořeny pomocí klasického modelu nasazení, najdete v [tématu Jak resetovat heslo nebo SSH pro virtuální počítače založené na Linuxu](../linux/classic/reset-access-classic.md).
