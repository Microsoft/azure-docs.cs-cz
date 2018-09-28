---
title: Řešení problémů s připojením SSH k virtuálnímu počítači Azure | Dokumentace Microsoftu
description: Jak řešit problémy, jako je "Připojení SSH se nezdařilo" nebo "odmítl připojení SSH' pro virtuální počítač Azure s Linuxem.
keywords: SSH připojení se odmítlo, ssh chyba, azure ssh, připojení SSH se nezdařilo
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: dcb82e19-29b2-47bb-99f2-900d4cfb5bbb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 7cd5d1e621a5a2feae6585edce6a626454bdfe50
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47413431"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Řešení potíží s připojením SSH k virtuálnímu počítači Azure Linux, který selže, chyby, nebo bylo odmítnuto
Existují různé důvody, že narazíte na chyby Secure Shell (SSH), selhání připojení SSH, nebo SSH byla odmítnuta, při pokusu o připojení pro virtuální počítač s Linuxem (VM). Tento článek pomůže najít a opravit problémy. Webu Azure portal, rozhraní příkazového řádku Azure, nebo rozšíření přístupu virtuálních počítačů pro Linux můžete odstraňovat potíže a řešit problémy s připojením.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](http://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](http://azure.microsoft.com/support/options/) a vyberte **získat podporu**. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](http://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Rychlé kroky pro řešení potíží
Po provedení každého kroku Poradce při potížích opakujte pokus o připojení k virtuálnímu počítači.

1. Resetujte konfiguraci SSH.
2. Resetujte přihlašovací údaje pro uživatele.
3. Ověřte, [skupinu zabezpečení sítě](../../virtual-network/security-overview.md) pravidla povolit provoz SSH.
   * Ujistěte se, že existuje pravidlo skupiny zabezpečení sítě tak, aby povolovala provoz SSH (ve výchozím nastavení TCP port 22).
   * Nelze použít přesměrování portu / mapování bez použití služby Azure load balancer.
4. Zkontrolujte, [stavu prostředků virtuálních počítačů](../../resource-health/resource-health-overview.md). 
   * Ujistěte se, že virtuální počítač hlásí stav v pořádku.
   * Pokud budete mít povolenou diagnostikou spuštění, ověřte, že virtuální počítač nehlásí spouštěcí chyby v protokolech.
5. Restartujte virtuální počítač.
6. Opětovné nasazení virtuálního počítače.

Pokračujte ve čtení pro více podrobný postup řešení potíží a vysvětlení.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Dostupné metody k řešení potíží s připojením SSH
Můžete resetovat přihlašovací údaje nebo konfigurace SSH pomocí jedné z následujících metod:

* [Azure portal](#use-the-azure-portal) – oceníte, pokud potřebujete rychle resetovat konfiguraci SSH nebo klíč SSH a nemáte nainstalované Azure nástroje.
* [Azure CLI](#use-the-azure-cli) – Pokud jste už na příkazovém řádku, rychle resetovat konfiguraci SSH nebo přihlašovací údaje. Můžete také použít [rozhraní příkazového řádku Azure](#use-the-azure-classic-cli)
* [Azure rozšíření VMAccessForLinux](#use-the-vmaccess-extension) – vytvoření a opakovaně používat soubory definice json se resetovat přihlašovací údaje pro konfiguraci nebo uživatele SSH.

Po provedení každého kroku Poradce při potížích zkuste se znovu připojit k virtuálnímu počítači. Pokud se pořád nemůžete připojit, vyzkoušejte další krok.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal
Na webu Azure portal poskytuje rychlý způsob, jak resetovat přihlašovací údaje pro konfiguraci nebo uživatele SSH bez instalace všech nástrojů v místním počítači.

Vyberte svůj virtuální počítač na webu Azure Portal. Přejděte dolů k položce **podpora a řešení potíží** a vyberte **resetovat heslo** jako v následujícím příkladu:

![Resetovat konfiguraci SSH nebo přihlašovacích údajů na webu Azure Portal](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>Resetovat konfiguraci SSH
Jako první krok, vyberte `Reset configuration only` z **režimu** rozevírací nabídce jako v předchozím snímku obrazovky, klikněte **resetování** tlačítko. Po dokončení této akce se pokusí znovu přístup k vašemu virtuálnímu počítači.

### <a name="reset-ssh-credentials-for-a-user"></a>Resetovat přihlašovací údaje SSH pro uživatele
Resetovat přihlašovací údaje stávajícího uživatele, vyberte buď `Reset SSH public key` nebo `Reset password` z **režimu** rozevírací nabídky stejně jako v předchozím snímku obrazovky. Zadejte uživatelské jméno a klíč SSH nebo nové heslo a klikněte **resetování** tlačítko.

Můžete také vytvořit uživatele s oprávněními sudo na virtuálním počítači v této nabídce. Zadejte nové uživatelské jméno a přiřazené heslo nebo klíč SSH a pak klikněte na tlačítko **resetování** tlačítko.

### <a name="check-security-rules"></a>Check – pravidla zabezpečení

Použití [ověření toku protokolu IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) pro potvrzení, pokud je pravidlo skupiny zabezpečení sítě blokuje provoz do nebo z virtuálního počítače. Můžete také zkontrolovat platná pravidla skupin zabezpečení a ověřte, že příchozí "skupina zabezpečení sítě existuje pravidlo a je nastaveno jako prioritní pro port SSH (ve výchozím nastavení 22). Další informace najdete v tématu [přenosy dat pomocí platná pravidla zabezpečení a řešení potíží s virtuálním počítači](../../virtual-network/diagnose-network-traffic-filter-problem.md).

### <a name="check-routing"></a>Zkontrolujte směrování

Network Watcher můžete využít [směrování](../../network-watcher/network-watcher-check-next-hop-portal.md) schopnost potvrďte trasu nebrání provoz z směrovány do nebo z virtuálního počítače. Můžete také zkontrolovat efektivní trasy, pokud chcete zobrazit všechny efektivní trasy pro síťové rozhraní. Další informace najdete v tématu [provoz řešení potíží s virtuálního počítače pomocí efektivních tras](../../virtual-network/diagnose-network-routing-problem.md).

## <a name="use-the-azure-cli"></a>Použití Azure CLI
Pokud jste tak dosud neučinili, nainstalujte nejnovější [rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2) a přihlaste se k Azure pomocí účtu [az login](/cli/azure/reference-index#az_login).

Pokud jste vytvořili a nahrát vlastní image Linuxu disku, ujistěte se, že [Microsoft Azure Linux Agent](../extensions/agent-windows.md) verze 2.0.5 nebo novější nainstalován. Pro virtuální počítače vytvořené pomocí Image z Galerie tato rozšíření přístup k již nainstalován a nakonfigurován pro vás.

### <a name="reset-ssh-configuration"></a>Resetovat konfiguraci SSH
Nejprve můžete zkusit resetuje se konfigurace SSH na výchozí hodnoty a restartování serveru SSH na virtuálním počítači. Všimněte si, že to nezmění název uživatelského účtu, hesla nebo klíčů SSH.
V následujícím příkladu [az vm uživatele reset-ssh](/cli/azure/vm/user#az_vm_user_reset_ssh) resetovat konfiguraci SSH na virtuálním počítači s názvem `myVM` v `myResourceGroup`. Použijte vlastní hodnoty následujícím způsobem:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Resetovat přihlašovací údaje SSH pro uživatele
Následující příklad používá [az vm se aktualizace uživatele](/cli/azure/vm/user#az_vm_user_update) se resetovat přihlašovací údaje pro `myUsername` na hodnotu zadanou v `myPassword`, na virtuálním počítači s názvem `myVM` v `myResourceGroup`. Použijte vlastní hodnoty následujícím způsobem:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Pokud používáte ověřování pomocí klíče SSH, můžete obnovit klíč SSH pro daného uživatele. Následující příklad používá **az vm přístup set-linux-user** aktualizovat klíč SSH uložený v `~/.ssh/id_rsa.pub` pro uživatele s názvem `myUsername`, na virtuálním počítači s názvem `myVM` v `myResourceGroup`. Použijte vlastní hodnoty následujícím způsobem:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Použití rozšíření VMAccess
Rozšíření přístupu virtuálních počítačů pro Linux se načte v souboru json, který definuje akce provádět. Tyto akce patří SSHD obnovení, obnovení klíče SSH nebo přidání uživatele. Dál používat rozhraní příkazového řádku Azure k rozšíření VMAccess volání, ale můžete znovu použít soubory json napříč několika virtuálními počítači v případě potřeby. Tento přístup umožňuje vytvářet úložiště soubory json, které může být volána pro určité scénáře.

### <a name="reset-sshd"></a>Resetovat SSHD
Vytvořte soubor s názvem `settings.json` s následujícím obsahem:

```json
{  
    "reset_ssh":"True"
}
```

Pomocí Azure CLI, poté je zapotřebí zavolat `VMAccessForLinux` rozšíření k resetování připojení SSHD tak, že určíte soubor json. Následující příklad používá [sada rozšíření az vm](/cli/azure/vm/extension#az_vm_extension_set) resetovat SSHD na virtuálním počítači s názvem `myVM` v `myResourceGroup`. Použijte vlastní hodnoty následujícím způsobem:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Resetovat přihlašovací údaje SSH pro uživatele
Pokud se zobrazí SSHD fungoval správně, můžete resetovat přihlašovací údaje pro uživatele třetím osobám. K resetování hesla pro uživatele, vytvořte soubor s názvem `settings.json`. Následující příklad resetuje přihlašovací údaje pro `myUsername` na hodnotu zadanou v `myPassword`. Zadejte následující řádky do vaší `settings.json` soubor pomocí vlastní hodnoty:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Nebo pokud chcete obnovit klíč SSH pro uživatele, nejprve vytvořte soubor s názvem `settings.json`. Následující příklad resetuje přihlašovací údaje pro `myUsername` na hodnotu zadanou v `myPassword`, na virtuálním počítači s názvem `myVM` v `myResourceGroup`. Zadejte následující řádky do vaší `settings.json` soubor pomocí vlastní hodnoty:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Po vytvoření souboru json pomocí rozhraní příkazového řádku Azure k volání `VMAccessForLinux` rozšíření se resetovat přihlašovací údaje uživatele SSH tak, že určíte soubor json. Následující příklad resetuje přihlašovací údaje u virtuálního počítače s názvem `myVM` v `myResourceGroup`. Použijte vlastní hodnoty následujícím způsobem:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>Použití Azure classic CLI
Pokud jste tak dosud neučinili, [instalace Azure classic CLI a připojte se ke svému předplatnému Azure](../../cli-install-nodejs.md). Ujistěte se, že se režimu Resource Manageru pomocí následujícím způsobem:

```azurecli
azure config mode arm
```

Pokud jste vytvořili a nahrát vlastní image Linuxu disku, ujistěte se, že [Microsoft Azure Linux Agent](../extensions/agent-windows.md) verze 2.0.5 nebo novější nainstalován. Pro virtuální počítače vytvořené pomocí Image z Galerie tato rozšíření přístup k již nainstalován a nakonfigurován pro vás.

### <a name="reset-ssh-configuration"></a>Resetovat konfiguraci SSH
Konfigurace sshd Instrukci samotného je možná špatně nakonfigurovaná nebo ve službě došlo k chybě. Můžete resetovat SSHD zajistit, aby že se konfigurace SSH samotného je platný. Resetuje se SSHD by měl být prvním krokem řešení problémů, které můžete provést.

Následující příklad resetuje SSHD na virtuálním počítači s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`. Použijte vlastní názvy virtuálních počítačů a prostředků skupiny takto:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Resetovat přihlašovací údaje SSH pro uživatele
Pokud se zobrazí SSHD fungoval správně, můžete resetovat heslo uživatele třetím osobám. Následující příklad resetuje přihlašovací údaje pro `myUsername` na hodnotu zadanou v `myPassword`, na virtuálním počítači s názvem `myVM` v `myResourceGroup`. Použijte vlastní hodnoty následujícím způsobem:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Pokud používáte ověřování pomocí klíče SSH, můžete obnovit klíč SSH pro daného uživatele. Následující příklad aktualizuje klíč SSH uložený v `~/.ssh/id_rsa.pub` pro uživatele s názvem `myUsername`, na virtuálním počítači s názvem `myVM` v `myResourceGroup`. Použijte vlastní hodnoty následujícím způsobem:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```


## <a name="restart-a-vm"></a>Restartování virtuálního počítače
Pokud máte resetovat přihlašovací údaje pro konfiguraci a uživatele SSH, nebo došlo k chybě při provádění, můžete zkusit restartování virtuálního počítače na základní výpočetní problémy adresu.

### <a name="azure-portal"></a>portál Azure
Pokud chcete restartovat virtuální počítač pomocí webu Azure portal, vyberte svůj virtuální počítač a klikněte na tlačítko **restartovat** tlačítko jako v následujícím příkladu:

![Restartování virtuálního počítače na webu Azure Portal](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-classic-cli"></a>Azure Classic CLI
Následující příklad restartuje virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`. Použijte vlastní hodnoty následujícím způsobem:

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli"></a>Azure CLI
Následující příklad používá [az vm restart](/cli/azure/vm#az_vm_restart) restartovat virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`. Použijte vlastní hodnoty následujícím způsobem:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```


## <a name="redeploy-a-vm"></a>Znovunasazení virtuálního počítače
Můžete znovu nasadit virtuální počítač do jiného uzlu v rámci Azure, které může opravit nějaké základní síťové potíže. Informace o opětovné nasazení virtuálního počítače najdete v tématu [opětovné nasazení virtuálního počítače do nového uzlu Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Po dokončení této operace dočasné disky data se ztratí a aktualizují dynamické IP adresy, které jsou spojené s virtuálním počítačem.
> 
> 

### <a name="azure-portal"></a>portál Azure
Opětovné nasazení virtuálního počítače pomocí webu Azure portal, vyberte svůj virtuální počítač a přejděte dolů k položce **podpora a řešení potíží** oddílu. Klikněte na tlačítko **znovu nasadit** tlačítko jako v následujícím příkladu:

![Opětovné nasazení virtuálního počítače na webu Azure Portal](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-classic-cli"></a>Azure Classic CLI
Následující příklad znovu nasadí virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`. Použijte vlastní hodnoty následujícím způsobem:

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli"></a>Azure CLI
Následující příklad použití [opětovné nasazení virtuálního počítače v rámci az](/cli/azure/vm#az_vm_redeploy) znovu nasadit virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`. Použijte vlastní hodnoty následujícím způsobem:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>Virtuální počítače vytvořené pomocí modelu nasazení Classic
Zkuste tyto kroky k řešení nejběžnějších chyb připojení SSH pro virtuální počítače, které byly vytvořeny pomocí modelu nasazení classic. Po provedení každého kroku zkuste se znovu připojit k virtuálnímu počítači.

* Resetování vzdáleného přístupu z [webu Azure portal](https://portal.azure.com). Na portálu Azure portal, vyberte svůj virtuální počítač a klikněte na tlačítko **resetovat vzdálený přístup...**  tlačítko.
* Restartujte virtuální počítač. Na [webu Azure portal](https://portal.azure.com), vyberte svůj virtuální počítač a klikněte na tlačítko **restartovat** tlačítko.
    
* Opětovné nasazení virtuálního počítače do nového uzlu Azure. Informace o tom, jak znovu nasadit virtuální počítač najdete v tématu [opětovné nasazení virtuálního počítače do nového uzlu Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  
    Po dokončení této operace dočasné disky data se ztratí a aktualizují dynamické IP adresy, které jsou spojené s virtuálním počítačem.
* Postupujte podle pokynů v [resetování hesla nebo klíče SSH pro virtuální počítače se systémem Linux](../linux/classic/reset-access-classic.md?) na:
  
  * Resetujte heslo nebo klíč SSH.
  * Vytvoření *sudo* uživatelský účet.
  * Resetujte konfiguraci SSH.
* Kontrola stavu prostředků Virtuálních počítačů v případě problémů platformy.<br>
     Vyberte svůj virtuální počítač a přejděte dolů **nastavení** > **zkontrolovat stav**.

## <a name="additional-resources"></a>Další zdroje informací:
* Pokud jste stále nemáte přístup k SSH k virtuálnímu počítači po provedení kroků po, přečtěte si téma [podrobný postup řešení potíží](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) zkontrolovat další kroky k vyřešení vašeho problému.
* Další informace o řešení potíží s přístupu k aplikacím, najdete v části [řešení potíží s přístupem k aplikaci spuštěné na virtuálním počítači Azure](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Další informace o řešení potíží s virtuálním počítačům, které byly vytvořeny pomocí modelu nasazení classic najdete v tématu [resetování hesla nebo klíče SSH pro virtuální počítače se systémem Linux](../linux/classic/reset-access-classic.md).


