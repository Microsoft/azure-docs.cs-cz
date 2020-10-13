---
title: Řešení potíží s připojením SSH k virtuálnímu počítači Azure | Microsoft Docs
description: Jak řešit problémy, jako je "připojení SSH selhalo" nebo "odmítnutí připojení SSH" pro virtuální počítač Azure se systémem Linux.
keywords: odmítnuté připojení SSH, chyba SSH, Azure SSH, připojení SSH selhalo.
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
ms.openlocfilehash: 678bad67b454ec0930d2cf30df45ba7b2c822e35
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371452"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Řešení potíží s připojeními SSH k virtuálnímu počítači Azure s Linuxem, která selžou, skončí chybou nebo se zamítnou
Tento článek vám pomůže najít a opravit problémy, ke kterým dochází kvůli chybám Secure Shell (SSH), chybám připojení SSH nebo SSH, pokud se pokusíte připojit k virtuálnímu počítači se systémem Linux (VM). K řešení potíží a řešení problémů s připojením můžete použít rozšíření Azure Portal, Azure CLI nebo rozhraní pro přístup k virtuálnímu počítači pro Linux.


Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Rychlé kroky pro řešení potíží
Po každém kroku řešení potíží se pokuste znovu připojit k virtuálnímu počítači.

1. [Resetovat konfiguraci SSH](#reset-the-ssh-configuration).
2. [Resetujte přihlašovací údaje](#reset-ssh-credentials-for-a-user) pro uživatele.
3. Ověřte, že pravidla [skupiny zabezpečení sítě](../../virtual-network/network-security-groups-overview.md) povolují přenosy SSH.
   * Zajistěte, aby existovalo [pravidlo skupiny zabezpečení sítě](#check-security-rules) , které povoluje provoz SSH (ve výchozím nastavení port TCP 22).
   * Přesměrování a mapování portů nemůžete použít bez použití nástroje pro vyrovnávání zatížení Azure.
4. Ověřte [stav prostředku virtuálního počítače](../../service-health/resource-health-overview.md).
   * Ujistěte se, že se virtuální počítač hlásí jako dobrý.
   * Pokud máte [zapnutou diagnostiku spouštění](boot-diagnostics.md), ověřte, že virtuální počítač nehlásí chyby spouštění v protokolech.
5. [Restartujte virtuální počítač](#restart-a-vm).
6. [Znovu nasaďte virtuální počítač](#redeploy-a-vm).

Podrobnější postup řešení potíží a vysvětlení najdete v tématu Další informace.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Dostupné metody řešení potíží s připojením SSH
Přihlašovací údaje nebo konfiguraci SSH můžete resetovat pomocí jedné z následujících metod:

* [Azure Portal](#use-the-azure-portal) – Skvělé, pokud potřebujete rychle Resetovat konfiguraci SSH nebo klíč SSH a nemáte nainstalované nástroje Azure Tools.
* [Azure VM Serial Console](https://aka.ms/serialconsolelinux) – sériová konzola virtuálních počítačů bude fungovat bez ohledu na konfiguraci SSH a poskytne vám interaktivní konzolu k vašemu virtuálnímu počítači. V některých případech se nejedná o situaci, kdy se služba sériového portu navržená tak, aby pomohla vyřešit. Další podrobnosti najdete níže.
* [Azure CLI](#use-the-azure-cli) – Pokud už jste na příkazovém řádku, rychle resetujte konfiguraci SSH nebo přihlašovací údaje. Pokud pracujete s klasickým virtuálním počítačem, můžete použít rozhraní příkazového [řádku Azure Classic](#use-the-azure-classic-cli).
* [Rozšíření Azure VMAccessForLinux](#use-the-vmaccess-extension) – vytvořte a znovu použijte soubory definice JSON pro resetování přihlašovacích údajů nebo přihlašovacích údajů uživatele.

Po každém kroku řešení potíží zkuste znovu připojit k vašemu VIRTUÁLNÍmu počítači. Pokud se stále nemůžete připojit, zkuste další krok.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal
Azure Portal poskytuje rychlý způsob, jak resetovat konfiguraci SSH nebo přihlašovací údaje uživatele, aniž byste museli instalovat žádné nástroje na místním počítači.

Začněte tím, že v Azure Portal vyberete svůj virtuální počítač. Přejděte dolů k části **Podpora a řešení potíží** a vyberte **resetovat heslo** jako v následujícím příkladu:

![Resetovat konfiguraci SSH nebo přihlašovací údaje v Azure Portal](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>Resetovat konfiguraci SSH
Pokud chcete resetovat konfiguraci SSH, vyberte `Reset configuration only` v části **režim** jako na předchozím snímku obrazovky a pak vyberte **aktualizovat**. Po dokončení této akce se znovu pokuste o přístup k VIRTUÁLNÍmu počítači.

### <a name="reset-ssh-credentials-for-a-user"></a>Resetování přihlašovacích údajů SSH pro uživatele
Pokud chcete resetovat přihlašovací údaje stávajícího uživatele, vyberte buď `Reset SSH public key` nebo `Reset password` v oddílu **režim** jako na předchozím snímku obrazovky. Zadejte uživatelské jméno a klíč SSH nebo nové heslo a pak vyberte  **aktualizovat**.

Z této nabídky můžete také vytvořit uživatele s oprávněními sudo na virtuálním počítači. Zadejte nové uživatelské jméno a přidružené heslo nebo klíč SSH a pak vyberte **aktualizovat**.

### <a name="check-security-rules"></a>Ověřit pravidla zabezpečení

Pomocí [ověření toku protokolu IP](../../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) můžete potvrdit, jestli pravidlo ve skupině zabezpečení sítě blokuje provoz do nebo z virtuálního počítače. Můžete si také projít pravidla efektivních skupin zabezpečení, abyste měli jistotu, že příchozí pravidlo "Povolit" NSG existuje a že je nastavená priorita pro port SSH (výchozí 22). Další informace najdete v tématu [použití platných pravidel zabezpečení k řešení potíží s tokem provozu virtuálních počítačů](../../virtual-network/diagnose-network-traffic-filter-problem.md).

### <a name="check-routing"></a>Kontrolovat směrování

Použijte možnost [dalšího směrování](../../network-watcher/diagnose-vm-network-routing-problem.md) Network Watcher k potvrzení, že trasa nebrání směrování provozu do nebo z virtuálního počítače. Můžete si také projít efektivní trasy a zobrazit všechny efektivní trasy pro síťové rozhraní. Další informace najdete v tématu [použití efektivních tras k řešení potíží s tokem provozu virtuálních počítačů](../../virtual-network/diagnose-network-routing-problem.md).

## <a name="use-the-azure-vm-serial-console"></a>Použití sériové konzole virtuálního počítače Azure
[Konzola sériového virtuálního počítače Azure](./serial-console-linux.md) poskytuje přístup k textové konzole pro virtuální počítače se systémem Linux. Konzolu můžete použít k řešení potíží s připojením SSH v interaktivním prostředí. Ujistěte se, že jste splnili [požadavky](./serial-console-linux.md#prerequisites) na používání služby sériového portu, a vyzkoušejte následující příkazy, abyste mohli dále řešit problémy s připojením SSH.

### <a name="check-that-ssh-is-running"></a>Ověřte, že je spuštěný SSH.
Pomocí následujícího příkazu můžete ověřit, jestli je na VIRTUÁLNÍm počítači spuštěný SSH:

```console
ps -aux | grep ssh
```

Pokud je nějaký výstup, je SSH v provozu.

### <a name="check-which-port-ssh-is-running-on"></a>Ověřit, na kterém portu je spuštěný protokol SSH

Pomocí následujícího příkazu můžete ověřit, na kterém portu SSH běží:

```console
sudo grep Port /etc/ssh/sshd_config
```

Váš výstup bude vypadat nějak takto:

```output
Port 22
```

## <a name="use-the-azure-cli"></a>Použití Azure CLI
Pokud jste to ještě neudělali, nainstalujte si nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a přihlaste se k účtu Azure pomocí [AZ Login](/cli/azure/reference-index).

Pokud jste vytvořili a nahráli vlastní image disku se systémem Linux, ujistěte se, že je nainstalovaná verze [agenta Microsoft Azure Linux](../extensions/agent-linux.md) verze 2.0.5 nebo novější. Pro virtuální počítače vytvořené pomocí imagí z Galerie je tato rozšíření pro přístup už nainstalovaná a nakonfigurovaná za vás.

### <a name="reset-ssh-configuration"></a>Resetovat konfiguraci SSH
Můžete se nejdřív pokusit Resetovat konfiguraci SSH na výchozí hodnoty a restartovat server SSH na virtuálním počítači. Tato funkce nemění název uživatelského účtu, heslo ani klíče SSH.
Následující příklad používá [příkaz AZ VM User Reset-SSH](/cli/azure/vm/user) k resetování konfigurace SSH na virtuálním počítači s názvem `myVM` `myResourceGroup` . Použijte vlastní hodnoty následujícím způsobem:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Resetování přihlašovacích údajů SSH pro uživatele
Následující příklad používá příkaz [AZ VM User Update](/cli/azure/vm/user) k resetování přihlašovacích údajů pro `myUsername` hodnotu určenou v nástroji na `myPassword` virtuálním počítači s názvem `myVM` v `myResourceGroup` . Použijte vlastní hodnoty následujícím způsobem:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Pokud používáte ověřování pomocí klíče SSH, můžete resetovat klíč SSH pro daného uživatele. V následujícím příkladu se pomocí **AZ VM Access set-Linux-User** aktualizuje klíč SSH uložený v `~/.ssh/id_rsa.pub` pro uživatele s názvem `myUsername` na virtuálním počítači s `myVM` názvem `myResourceGroup` . Použijte vlastní hodnoty následujícím způsobem:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Použití rozšíření VMAccess
Rozšíření přístupu virtuálních počítačů pro Linux čte v souboru JSON, který definuje akce, které se mají provést. Mezi tyto akce patří resetování SSHD, resetování klíče SSH nebo přidání uživatele. Rozhraní příkazového řádku Azure stále používáte k volání rozšíření VMAccess, ale v případě potřeby můžete soubory JSON znovu použít napříč několika virtuálními počítači. Tento přístup umožňuje vytvořit úložiště souborů JSON, které je pak možné volat pro dané scénáře.

### <a name="reset-sshd"></a>Resetovat SSHD
Vytvořte soubor s názvem `settings.json` s následujícím obsahem:

```json
{
    "reset_ssh":True
}
```

Pomocí Azure CLI pak voláním `VMAccessForLinux` rozšíření resetujete připojení sshd zadáním souboru JSON. Následující příklad používá [AZ VM Extension set](/cli/azure/vm/extension) k resetování sshd na virtuálním počítači s názvem `myVM` v `myResourceGroup` . Použijte vlastní hodnoty následujícím způsobem:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Resetování přihlašovacích údajů SSH pro uživatele
Pokud se zdá, že SSHD funguje správně, můžete resetovat přihlašovací údaje pro uživatele giver. Pokud chcete resetovat heslo pro uživatele, vytvořte soubor s názvem `settings.json` . Následující příklad obnoví přihlašovací údaje pro `myUsername` na hodnotu zadanou v `myPassword` . Do souboru zadejte následující řádky s `settings.json` použitím vlastních hodnot:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Nebo chcete-li obnovit klíč SSH pro uživatele, nejprve vytvořte soubor s názvem `settings.json` . Následující příklad obnoví přihlašovací údaje pro `myUsername` hodnotu zadanou v `myPassword` , na virtuálním počítači s názvem `myVM` v `myResourceGroup` . Do souboru zadejte následující řádky s `settings.json` použitím vlastních hodnot:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Po vytvoření souboru JSON použijte rozhraní příkazového řádku Azure pro volání `VMAccessForLinux` rozšíření k resetování přihlašovacích údajů uživatele SSH zadáním souboru JSON. Následující příklad obnoví přihlašovací údaje na virtuálním počítači s názvem `myVM` v `myResourceGroup` . Použijte vlastní hodnoty následujícím způsobem:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>Použití rozhraní příkazového řádku Azure Classic
Pokud jste to ještě neudělali, [nainstalujte rozhraní příkazového řádku Azure Classic a připojte se k předplatnému Azure](/cli/azure/install-classic-cli). Ujistěte se, že používáte režim Správce prostředků následujícím způsobem:

```azurecli
azure config mode arm
```

Pokud jste vytvořili a nahráli vlastní image disku se systémem Linux, ujistěte se, že je nainstalovaná verze [agenta Microsoft Azure Linux](../extensions/agent-linux.md) verze 2.0.5 nebo novější. Pro virtuální počítače vytvořené pomocí imagí z Galerie je tato rozšíření pro přístup už nainstalovaná a nakonfigurovaná za vás.

### <a name="reset-ssh-configuration"></a>Resetovat konfiguraci SSH
Vlastní konfigurace SSHD může být nesprávně nakonfigurovaná nebo došlo k chybě služby. Můžete resetovat SSHD, abyste měli jistotu, že je samotná konfigurace SSH platná. Resetování SSHD by mělo být prvním krokem pro odstraňování potíží, který byste měli provést.

Následující příklad obnoví SSHD na virtuálním počítači s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup` . Použijte vlastní názvy virtuálních počítačů a skupin prostředků následujícím způsobem:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Resetování přihlašovacích údajů SSH pro uživatele
Pokud se zdá, že SSHD funguje správně, můžete resetovat heslo pro uživatele giver. Následující příklad obnoví přihlašovací údaje pro `myUsername` hodnotu zadanou v `myPassword` , na virtuálním počítači s názvem `myVM` v `myResourceGroup` . Použijte vlastní hodnoty následujícím způsobem:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Pokud používáte ověřování pomocí klíče SSH, můžete resetovat klíč SSH pro daného uživatele. Následující příklad aktualizuje klíč SSH uložený v `~/.ssh/id_rsa.pub` pro uživatele s názvem `myUsername` na virtuálním počítači s názvem `myVM` v `myResourceGroup` . Použijte vlastní hodnoty následujícím způsobem:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="restart-a-vm"></a>Restartování virtuálního počítače
Pokud jste obnovili konfiguraci SSH a přihlašovací údaje uživatele nebo při tom došlo k chybě, můžete zkusit restartovat virtuální počítač a vyřešit základní výpočetní problémy.

### <a name="azure-portal"></a>portál Azure
Pokud chcete virtuální počítač restartovat pomocí Azure Portal, vyberte svůj virtuální počítač a pak vyberte **restartovat** jako v následujícím příkladu:

![Restartování virtuálního počítače v Azure Portal](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
Následující příklad používá [AZ VM restart](/cli/azure/vm) k restartování virtuálního počítače `myVM` ve skupině prostředků s názvem `myResourceGroup` . Použijte vlastní hodnoty následujícím způsobem:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Azure Classic CLI

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Následující příklad restartuje virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup` . Použijte vlastní hodnoty následujícím způsobem:

```console
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="redeploy-a-vm"></a>Znovunasazení virtuálního počítače
Virtuální počítač můžete znovu nasadit do jiného uzlu v rámci Azure, což může opravit případné problémy se sítí. Informace o opětovném nasazení virtuálního počítače najdete v tématu [opětovné nasazení virtuálního počítače do nového uzlu Azure](./redeploy-to-new-node-windows.md?toc=/azure/virtual-machines/windows/toc.json).

> [!NOTE]
> Po dokončení této operace dojde ke ztrátě dočasných dat disku a k aktualizaci dynamických IP adres přidružených k virtuálnímu počítači.
>
>

### <a name="azure-portal"></a>portál Azure
Pokud chcete virtuální počítač znovu nasadit pomocí Azure Portal, vyberte svůj virtuální počítač a přejděte dolů k části **Podpora a řešení potíží** . Vyberte **znovu nasadit** jako v následujícím příkladu:

![Opětovné nasazení virtuálního počítače v Azure Portal](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
V následujícím příkladu použijte [AZ VM redeploy](/cli/azure/vm) pro opětovné nasazení virtuálního počítače s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup` . Použijte vlastní hodnoty následujícím způsobem:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Azure Classic CLI

V následujícím příkladu se znovu nasadí virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup` . Použijte vlastní hodnoty následujícím způsobem:

```console
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>Virtuální počítače vytvořené pomocí modelu nasazení Classic

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Zkuste vyřešit nejběžnější chyby připojení SSH pro virtuální počítače, které byly vytvořené pomocí modelu nasazení Classic. Po každém kroku se pokuste znovu připojit k virtuálnímu počítači.

* Resetovat vzdálený přístup z [Azure Portal](https://portal.azure.com). Na Azure Portal vyberte svůj virtuální počítač a pak vyberte **resetovat vzdálené úložiště...**.
* Restartujte virtuální počítač. Na [Azure Portal](https://portal.azure.com)vyberte svůj virtuální počítač a vyberte **restartovat**.

* Znovu nasaďte virtuální počítač do nového uzlu Azure. Informace o tom, jak znovu nasadit virtuální počítač, najdete v tématu [opětovné nasazení virtuálního počítače do nového uzlu Azure](./redeploy-to-new-node-windows.md?toc=/azure/virtual-machines/windows/toc.json).

    Po dokončení této operace dojde ke ztrátě dočasných dat na disku a aktualizuje se dynamická IP adresa přidružená k virtuálnímu počítači.
* Postupujte podle pokynů v části [Postup resetování hesla nebo SSH pro virtuální počítače](/previous-versions/azure/virtual-machines/linux/classic/reset-access-classic) se systémem Linux na:

  * Resetujte heslo nebo klíč SSH.
  * Vytvořte uživatelský účet *sudo* .
  * Resetovat konfiguraci SSH.
* Projděte si stav prostředků virtuálního počítače pro všechny problémy s platformou.<br>
     Vyberte svůj virtuální počítač a posuňte **Nastavení**a Projděte si  >  **stav**.

## <a name="additional-resources"></a>Další zdroje
* Pokud ke svému VIRTUÁLNÍmu počítači stále nemůžete přihlašovat přes následující postup, přečtěte si [podrobnější postup odstraňování potíží](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a Projděte si další kroky k vyřešení vašeho problému.
* Další informace o řešení potíží s přístupem k aplikacím najdete v tématu [řešení potíží s přístupem k aplikaci spuštěné na virtuálním počítači Azure](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json) .
* Další informace o řešení potíží s virtuálními počítači, které byly vytvořeny pomocí modelu nasazení Classic, najdete v tématu [Postup resetování hesla nebo SSH pro virtuální počítače](/previous-versions/azure/virtual-machines/linux/classic/reset-access-classic)se systémem Linux.
