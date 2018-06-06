---
title: Řešení potíží s připojeními SSH pro virtuální počítač Azure | Microsoft Docs
description: Postup řešení potíží, třeba "Připojení SSH se nezdařilo" nebo "odmítl připojení SSH' pro virtuální počítač Azure s Linuxem.
keywords: SSH připojení odmítnuto, ssh chyby, azure ssh, připojení SSH se nezdařilo
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: dcb82e19-29b2-47bb-99f2-900d4cfb5bbb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: iainfou
ms.openlocfilehash: 9db1252ffdb705308c6bdaf77f394a0e57145fb5
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701917"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Řešení potíží s připojení SSH pro virtuální počítač Azure Linux který selže, chyby, nebo bylo odmítnuto
Existují různé příčiny, že dojde k chybám Secure Shell (SSH), selhání připojení SSH, nebo SSH bylo odmítnuto, při pokusu o připojení k virtuálnímu počítači (VM) Linux. Tento článek pomůže najít a opravit problémy. Portál Azure, rozhraní příkazového řádku Azure nebo rozšíření pro přístup virtuálních počítačů pro Linux můžete použít k řešení problémů s připojením.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, obraťte se na Azure odborníky na [fórech MSDN Azure a Stack Overflow](http://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](http://azure.microsoft.com/support/options/) a vyberte **získat podporu**. Informace o používání Azure podporovat, najdete v tématu [podporu Microsoft Azure – nejčastější dotazy](http://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Rychlé řešení potíží
Po dokončení každého kroku řešení potíží pokuste o připojení k virtuálnímu počítači.

1. Obnovte konfiguraci SSH.
2. Resetovat přihlašovací údaje pro uživatele.
3. Ověřte [skupinu zabezpečení sítě](../../virtual-network/security-overview.md) pravidla povolit provoz protokolu SSH.
   * Zkontrolujte, zda pravidlo skupinu zabezpečení sítě pro povolení komunikace SSH (ve výchozím nastavení je to TCP port 22).
   * Nemůžete použít přesměrování portu / mapování bez použití pro vyrovnávání zatížení Azure.
4. Zkontrolujte [stavu prostředků virtuálních počítačů](../../resource-health/resource-health-overview.md). 
   * Ujistěte se, že virtuální počítač hlásí, že je v pořádku.
   * Pokud máte povolené Diagnostika spouštění, ověřte, zda že virtuální počítač není reporting spouštěcí chyby v protokolech.
5. Restartujte virtuální počítač.
6. Znovu nasaďte virtuální počítač.

Pokračujte ve čtení pro podrobnější pro řešení potíží a vysvětlení.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Dostupné metody k řešení potíží s připojeními SSH
Můžete obnovit přihlašovací údaje nebo konfiguraci SSH pomocí jedné z následujících metod:

* [Portál Azure](#use-the-azure-portal) – skvělé, pokud budete potřebovat rychle resetovat konfiguraci SSH nebo klíč SSH a nemáte Azure nástroje nainstalované.
* [Azure CLI 2.0](#use-the-azure-cli-20) – Pokud jste už na příkazovém řádku, rychle obnovit konfiguraci SSH nebo přihlašovací údaje. Můžete také [1.0 rozhraní příkazového řádku Azure](#use-the-azure-cli-10)
* [Azure rozšíření VMAccessForLinux](#use-the-vmaccess-extension) – vytvoření a opakovaně soubory json definice se resetovat přihlašovací údaje pro konfiguraci nebo uživatele SSH.

Po dokončení každého kroku řešení potíží zkuste znovu připojit k virtuálnímu počítači. Pokud se pořád nemůžete připojit, zkuste na další krok.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal
Portál Azure poskytuje rychlý způsob, jak resetovat přihlašovací údaje pro konfiguraci nebo uživatele SSH bez instalace žádné nástroje na místním počítači.

Vyberte virtuální počítač na portálu Azure. Přejděte dolů k položce **podporu + Poradce při potížích s** a vyberte **resetovat heslo** jako v následujícím příkladu:

![Resetování konfigurace SSH nebo přihlašovací údaje na portálu Azure](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>Resetování konfigurace SSH
Jako první krok, vyberte `Reset configuration only` z **režimu** rozevírací nabídky jako v předchozím snímku obrazovky klikněte **resetovat** tlačítko. Po dokončení této akce pokusu o přístup k virtuálnímu počítači znovu.

### <a name="reset-ssh-credentials-for-a-user"></a>Resetovat SSH přihlašovací údaje pro uživatele
Se resetovat přihlašovací údaje stávajícího uživatele, vyberte buď `Reset SSH public key` nebo `Reset password` z **režimu** rozevírací nabídky jako v předchozím snímku obrazovky. Zadejte uživatelské jméno a klíč SSH nebo nové heslo a pak klikněte na **resetovat** tlačítko.

Můžete také vytvořit uživatele s oprávněními sudo do virtuálního počítače z této nabídky. Zadejte nové uživatelské jméno a přiřazené heslo nebo klíč SSH a pak klikněte **resetovat** tlačítko.

### <a name="check-security-rules"></a>Zkontrolujte pravidla zabezpečení

Použití [IP tok ověření](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) potvrďte, zda pravidla v skupinu zabezpečení sítě neblokuje přenosy do nebo z virtuálního počítače. Můžete také zkontrolovat pravidel skupiny zabezpečení efektivní zajistit příchozí "Povolit" NSG pravidlo existuje a prioritu pro SSH port (standardně 22). Další informace najdete v tématu [tok provozu pomocí pravidel efektivní zabezpečení řešení virtuálních počítačů](../../virtual-network/diagnose-network-traffic-filter-problem.md).

### <a name="check-routing"></a>Zkontrolujte směrování

Použít sledovací proces sítě [dalšího směrování](../../network-watcher/network-watcher-check-next-hop-portal.md) schopnost potvrďte, zda trasa nebrání provoz z směrovány do nebo z virtuálního počítače. Můžete také zkontrolovat efektivní trasy zobrazíte všechny efektivní trasy pro síťové rozhraní. Další informace najdete v tématu [řešení virtuálních počítačů pomocí efektivní směrování provozu toku](../../virtual-network/diagnose-network-routing-problem.md).

## <a name="use-the-azure-cli-20"></a>Použití Azure CLI 2.0
Pokud jste to ještě neudělali, nainstalujte nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) a přihlaste se k Azure účet pomocí [az přihlášení](/cli/azure/reference-index#az_login).

Pokud jste vytvořili a nahrát vlastní image disku Linux, zkontrolujte [Microsoft Azure Linux Agent](../extensions/agent-windows.md) verze 2.0.5 nebo novější je nainstalována. Pro virtuální počítače vytvořené pomocí Galerie obrázků toto rozšíření přístup k již instalovaných a konfigurace.

### <a name="reset-ssh-configuration"></a>Obnovte konfiguraci SSH
Můžete Nejdřív zkuste resetuje se konfigurace SSH výchozí hodnoty a restartování serveru SSH ve virtuálním počítači. Všimněte si, že to nezmění název uživatelského účtu, hesla nebo klíče SSH.
Následující příklad používá [az virtuálního počítače uživatele resetování-ssh](/cli/azure/vm/user#az_vm_user_reset_ssh) resetování konfigurace SSH pro virtuální počítač s názvem `myVM` v `myResourceGroup`. Použijte vlastní hodnoty takto:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Resetovat SSH přihlašovací údaje pro uživatele
Následující příklad používá [aktualizace uživatele virtuálního počítače az](/cli/azure/vm/user#az_vm_user_update) se resetovat přihlašovací údaje pro `myUsername` na hodnotu zadanou v `myPassword`, na virtuální počítač s názvem `myVM` v `myResourceGroup`. Použijte vlastní hodnoty takto:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Pokud používáte ověření pomocí klíče SSH, můžete resetovat klíč SSH pro daného uživatele. Následující příklad používá **az virtuální počítač přístup k set-linux-user** aktualizovat klíč SSH, které jsou uložené v `~/.ssh/id_rsa.pub` pro uživatele s názvem `myUsername`, na virtuální počítač s názvem `myVM` v `myResourceGroup`. Použijte vlastní hodnoty takto:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Používá rozšíření VMAccess
Rozšíření pro přístup virtuálních počítačů pro Linux přečte v souboru json, který definuje akce k provedení. Mezi ně patří resetování SSHD, resetování klíče SSH nebo přidání uživatele. Dál používat rozhraní příkazového řádku Azure k volání rozšíření VMAccess, ale můžete opakovaně použít soubory json napříč více virtuálními počítači v případě potřeby. Tento přístup umožňuje vytvořit úložiště json souborů, které může být volána pro daný scénáře.

### <a name="reset-sshd"></a>Resetování SSHD
Vytvořte soubor s názvem `settings.json` s následujícím obsahem:

```json
{  
    "reset_ssh":"True"
}
```

Použití Azure CLI, potom zavolejte `VMAccessForLinux` rozšíření pro resetování připojení SSHD zadáním souboru json. Následující příklad používá [nastavení rozšíření virtuálního az](/cli/azure/vm/extension#az_vm_extension_set) resetovat SSHD ve virtuálním počítači s názvem `myVM` v `myResourceGroup`. Použijte vlastní hodnoty takto:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Resetovat SSH přihlašovací údaje pro uživatele
Pokud se zobrazí SSHD fungoval správně, můžete resetovat přihlašovací údaje pro uživatele třetím osobám. Resetování hesla pro uživatele, vytvořte soubor s názvem `settings.json`. Následující příklad resetuje přihlašovací údaje pro `myUsername` na hodnotu zadanou v `myPassword`. Zadejte následující řádky do vaší `settings.json` souboru pomocí vlastní hodnoty:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Nebo si Pokud chcete resetovat klíč SSH pro uživatele, nejprve vytvořte soubor s názvem `settings.json`. Následující příklad resetuje přihlašovací údaje pro `myUsername` na hodnotu zadanou v `myPassword`, na virtuální počítač s názvem `myVM` v `myResourceGroup`. Zadejte následující řádky do vaší `settings.json` souboru pomocí vlastní hodnoty:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Po vytvoření souboru json, pomocí rozhraní příkazového řádku Azure k volání `VMAccessForLinux` rozšíření k resetování přihlašovacích údajů uživatele SSH zadáním souboru json. Následující příklad resetuje přihlašovací údaje u virtuálního počítače s názvem `myVM` v `myResourceGroup`. Použijte vlastní hodnoty takto:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-cli-10"></a>Použití Azure CLI 1.0
Pokud jste to ještě neudělali, [nainstalovat Azure CLI 1.0 a připojit se k předplatnému Azure](../../cli-install-nodejs.md). Ujistěte se, že režimu Resource Manager používáte následujícím způsobem:

```azurecli
azure config mode arm
```

Pokud jste vytvořili a nahrát vlastní image disku Linux, zkontrolujte [Microsoft Azure Linux Agent](../extensions/agent-windows.md) verze 2.0.5 nebo novější je nainstalována. Pro virtuální počítače vytvořené pomocí Galerie obrázků toto rozšíření přístup k již instalovaných a konfigurace.

### <a name="reset-ssh-configuration"></a>Obnovte konfiguraci SSH
Může být nesprávné konfigurace SSHD sám sebe nebo ve službě došlo k chybě. Můžete resetovat SSHD a ujistěte se, že samotný konfiguraci SSH je platný. Resetování SSHD musí být prvním krokem řešení potíží, které můžete provést.

Následující příklad resetuje SSHD na virtuálním počítači s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`. Používejte vlastní názvy virtuálních počítačů a prostředků skupiny takto:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Resetovat SSH přihlašovací údaje pro uživatele
Pokud se zobrazí SSHD fungoval správně, můžete resetovat heslo pro uživatele třetím osobám. Následující příklad resetuje přihlašovací údaje pro `myUsername` na hodnotu zadanou v `myPassword`, na virtuální počítač s názvem `myVM` v `myResourceGroup`. Použijte vlastní hodnoty takto:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Pokud používáte ověření pomocí klíče SSH, můžete resetovat klíč SSH pro daného uživatele. Následující příklad aktualizuje klíč SSH, které jsou uložené v `~/.ssh/id_rsa.pub` pro uživatele s názvem `myUsername`, na virtuální počítač s názvem `myVM` v `myResourceGroup`. Použijte vlastní hodnoty takto:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```


## <a name="restart-a-vm"></a>Restartování virtuálního počítače
Pokud máte resetování konfigurace a uživatelská pověření SSH, nebo došlo k chybě při tom, můžete zkusit restartování virtuálního počítače na adresu základní výpočetní problémy.

### <a name="azure-portal"></a>Azure Portal
Restartování virtuálního počítače pomocí portálu Azure, vyberte virtuální počítač a klikněte na **restartujte** tlačítko jako v následujícím příkladu:

![Restartujte virtuální počítač na portálu Azure](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli-10"></a>Azure CLI 1.0
Následující příklad restartuje virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`. Použijte vlastní hodnoty takto:

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Následující příklad používá [restartování virtuálního počítače az](/cli/azure/vm#az_vm_restart) restartovat virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`. Použijte vlastní hodnoty takto:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```


## <a name="redeploy-a-vm"></a>Znovunasazení virtuálního počítače
Můžete znovu nasadit virtuální počítač do jiného uzlu v rámci Azure, která může vyřešit problémy s základní sítě. Informace o opětovného nasazení virtuálního počítače najdete v tématu [znovu nasadit virtuální počítač do nového uzlu Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Po dokončení této operace dočasné disku data budou ztracena a zaktualizuje dynamické IP adresy, které jsou spojeny s virtuálním počítačem.
> 
> 

### <a name="azure-portal"></a>Azure Portal
K opětovnému nasazení virtuálního počítače pomocí portálu Azure, vyberte virtuální počítač a přejděte dolů k položce **podporu + Poradce při potížích s** části. Klikněte **znovu nasaďte** tlačítko jako v následujícím příkladu:

![Opětovné nasazení virtuálního počítače na portálu Azure](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli-10"></a>Azure CLI 1.0
Následující příklad opětovně nasadí virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`. Použijte vlastní hodnoty takto:

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Následující příklad použití [az virtuálního počítače znovu ho zaveďte](/cli/azure/vm#az_vm_redeploy) znovu nasadit virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`. Použijte vlastní hodnoty takto:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>Virtuální počítače vytvořené pomocí modelu nasazení Classic
Opakujte tyto kroky k vyřešení většiny běžných chyb připojení SSH pro virtuální počítače, které byly vytvořeny pomocí modelu nasazení classic. Po dokončení každého kroku pokuste o připojení k virtuálnímu počítači.

* Obnovte vzdálený přístup z [portál Azure](https://portal.azure.com). Na portálu Azure vyberte virtuální počítač a klikněte na **resetovat vzdálený...**  tlačítko.
* Restartujte virtuální počítač. Na [portál Azure](https://portal.azure.com), vyberte virtuální počítač a klikněte **restartujte** tlačítko.
    
* Opětovné nasazení virtuálního počítače do nového uzlu Azure. Informace o tom, jak znovu nasadit virtuální počítač najdete v tématu [znovu nasadit virtuální počítač do nového uzlu Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  
    Po dokončení této operace dočasné disku data budou ztracena a zaktualizuje dynamické IP adresy, které jsou spojeny s virtuálním počítačem.
* Postupujte podle pokynů v [jak resetovat heslo nebo SSH pro virtuální počítače se systémem Linux](classic/reset-access-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) na:
  
  * Resetovat heslo nebo klíč SSH.
  * Vytvoření *sudo* uživatelský účet.
  * Obnovte konfiguraci SSH.
* Zkontrolujte stav Virtuálního počítače prostředků pro nějaký problém s platformou.<br>
     Vyberte virtuální počítač a přejděte dolů **nastavení** > **Kontrola stavu**.

## <a name="additional-resources"></a>Další zdroje informací:
* Pokud jste stále se nedaří SSH pro virtuální počítač po provedení kroků po, najdete v části [podrobnější pokyny k odstraňování potíží](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) zobrazíte další kroky k vyřešení problému.
* Další informace o odstraňování potíží s přístup k aplikaci najdete v tématu [řešení potíží s přístupem k aplikaci spuštěné na virtuálním počítači Azure](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Další informace o odstraňování potíží s virtuálních počítačů, které byly vytvořeny pomocí modelu nasazení classic najdete v tématu [jak resetovat heslo nebo SSH pro virtuální počítače se systémem Linux](classic/reset-access-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

