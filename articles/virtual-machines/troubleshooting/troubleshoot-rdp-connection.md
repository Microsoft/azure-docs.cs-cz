---
title: Nelze se připojit pomocí RDP k virtuálnímu počítači s Windows v Azure | Dokumentace Microsoftu
description: Řešení potíží, pokud se nemůžete připojit k virtuálnímu počítači Windows v Azure pomocí vzdálené plochy
keywords: Chyba vzdálené plochy, Chyba připojení ke vzdálené ploše, se nemůže připojit k virtuálnímu počítači, řešení potíží vzdálené plochy
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/23/2018
ms.author: roiyz
ms.openlocfilehash: a4fb31721da679b21fa311340269cf07f93cd903
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981260"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Řešení potíží s připojením ke vzdálené ploše na virtuálním počítači Azure
Připojení protokolu RDP (Remote Desktop) k založené na Windows Azure virtuálnímu počítači (VM) může selhat z různých důvodů, poskytne vám nelze získat přístup k vašemu virtuálnímu počítači. Pomocí služby Vzdálená plocha na virtuální počítač, síťové připojení nebo klient vzdálené plochy na hostitelském počítači může být problém. Tento článek vás provede některé z nejběžnějších metody k řešení problémů s připojením RDP. 

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Rychlé kroky pro řešení potíží
Po provedení každého kroku Poradce při potížích opakujte pokus o připojení k virtuálnímu počítači:

1. Resetujte konfiguraci vzdálené plochy.
2. Zkontrolujte skupinu zabezpečení sítě pravidla / Cloud Services – koncové body.
3. Zkontrolujte protokoly konzoly virtuálního počítače.
4. Resetování síťové rozhraní virtuálního počítače.
5. Kontrola stavu prostředků virtuálních počítačů.
6. Resetování hesla virtuálního počítače.
7. Restartujte virtuální počítač.
8. Opětovné nasazení virtuálního počítače.

Pokud potřebujete podrobnější kroky a vysvětlení pokračujte ve čtení. Ověřte tuto místní síťové vybavení, jako jsou například routery a bránami firewall neblokují odchozí TCP port 3389, jak je uvedeno v [podrobné řešení potíží se scénáři RDP](detailed-troubleshoot-rdp.md).

> [!TIP]
> Pokud **připojit** tlačítko pro váš virtuální počítač je zobrazeno šedě na portálu a nejste připojení k Azure prostřednictvím [Express Route](../../expressroute/expressroute-introduction.md) nebo [Site-to-Site VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) připojení, musíte vytvořit a před použitím protokolu RDP přiřadit veřejnou IP adresu vašeho virtuálního počítače. Podle potřeby si můžete přečíst další informace o [veřejných IP adresách v Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Způsoby řešení potíží s RDP
Řešení potíží s virtuální počítače vytvořené pomocí modelu nasazení Resource Manager pomocí jedné z následujících metod:

* Azure portal – nemají výborné Pokud potřebujete rychle resetovat přihlašovací údaje pro konfiguraci nebo uživatel RDP a nástroje Azure, nainstalované.
* Azure PowerShell – Pokud umíte pracovat se příkazový řádek Powershellu, rychle resetovat protokol RDP konfigurace nebo přihlašovací údaje uživatele pomocí rutin Azure Powershellu.

Můžete také vyhledat kroky pro řešení potíží virtuální počítače vytvořené pomocí [model nasazení Classic](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Řešení potíží pomocí webu Azure portal
Po provedení každého kroku Poradce při potížích zkuste se znovu připojit k virtuálnímu počítači. Pokud se pořád nemůžete připojit, vyzkoušejte další krok.

1. **Resetujte připojení RDP**. Tento krok řešení problémů s resetuje se konfigurace RDP když jsou zakázaná vzdálená připojení nebo brány Windows Firewall protokol RDP blokují pravidla, třeba.
   
    Vyberte svůj virtuální počítač na webu Azure Portal. Přejděte dolů podokně nastavení **podpora a řešení potíží** v dolní části seznamu části. Klikněte na tlačítko **resetovat heslo** tlačítko. Nastavit **režimu** k **resetovat jenom konfiguraci** a potom klikněte na tlačítko **aktualizace** tlačítka:
   
    ![Resetovat konfiguraci RDP na webu Azure Portal](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Pravidla skupiny zabezpečení sítě ověřte, zda**. Použijte [Ověření toku protokolu IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) a ověřte, jestli pravidlo ve skupině zabezpečení sítě blokuje provoz do nebo z virtuálního počítače. Můžete také zkontrolovat platná pravidla skupin zabezpečení a ověřte, že příchozí "skupina zabezpečení sítě existuje pravidlo a je nastaveno jako prioritní pro port RDP (ve výchozím nastavení 3389). Další informace najdete v tématu [přenosy dat pomocí efektivní pravidla zabezpečení pro řešení potíží s virtuálním počítači](../../virtual-network/diagnose-network-traffic-filter-problem.md).

3. **Diagnostika spouštění virtuálních počítačů zkontrolujte**. Tento poradce při potížích krok kontroluje protokoly konzoly virtuálního počítače k určení, jestli se virtuální počítač hlásí problém. Ne všechny virtuální počítače mají povolení diagnostiky spouštění tak tento krok řešení problémů může být volitelný.
   
    Konkrétní kroky jsou nad rámec tohoto článku, ale může to znamenat problém širší, ovlivňuje možnosti připojení RDP. Další informace o kontrole protokoly konzoly a snímku obrazovky virtuálního počítače najdete v tématu [diagnostiky spouštění pro virtuální počítače](boot-diagnostics.md).

4. **Resetování síťové rozhraní virtuálního počítače**. Další informace najdete v tématu [resetování síťové karty pro virtuální počítač Windows Azure](../windows/reset-network-interface.md).
5. **Kontrola stavu prostředků virtuálních počítačů**. Tento poradce při potížích krok ověřuje, že nejsou žádné známé problémy s platformou Azure, který může mít vliv na připojení k virtuálnímu počítači.
   
    Vyberte svůj virtuální počítač na webu Azure Portal. Přejděte dolů podokně nastavení **podpora a řešení potíží** v dolní části seznamu části. Klikněte na tlačítko **Resource health** tlačítko. V dobrém stavu virtuálního počítače hlásí jako **dostupné**:
   
    ![Kontrola stavu prostředků virtuálních počítačů na webu Azure Portal](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Resetovat přihlašovací údaje uživatele**. Toto řešení potíží resetuje heslo k účtu místního správce, když nejste jistí nebo jste zapomněli přihlašovací údaje.  Po přihlášení k virtuálnímu počítači, měli byste resetovat heslo tohoto uživatele.
   
    Vyberte svůj virtuální počítač na webu Azure Portal. Přejděte dolů podokně nastavení **podpora a řešení potíží** v dolní části seznamu části. Klikněte na tlačítko **resetovat heslo** tlačítko. Ujistěte se, že **režimu** je nastavena na **resetovat heslo** a pak zadejte svoje uživatelské jméno a nové heslo. Nakonec klikněte na tlačítko **aktualizace** tlačítka:
   
    ![Resetovat přihlašovací údaje uživatele na webu Azure Portal](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Restartujte virtuální počítač**. Tento krok Poradce při potížích můžete opravit základní problémů, na které má virtuální počítač.
   
    Vyberte svůj virtuální počítač na webu Azure Portal a klikněte na tlačítko **přehled** kartu. Klikněte na tlačítko **restartovat** tlačítka:
   
    ![Restartujte virtuální počítač na webu Azure Portal](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Opětovné nasazení virtuálního počítače**. Tento poradce při potížích krok znovu nasadí virtuální počítač do jiného hostitele v rámci Azure a opravte všechny základní platformy nebo problémy se sítí.
   
    Vyberte svůj virtuální počítač na webu Azure Portal. Přejděte dolů podokně nastavení **podpora a řešení potíží** v dolní části seznamu části. Klikněte na tlačítko **znovu nasadit** tlačítko a pak klikněte na tlačítko **znovu nasadit**:
   
    ![Opětovné nasazení virtuálního počítače na webu Azure Portal](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    Po dokončení této operace, dojde ke ztrátě dat dočasné disky a dynamické IP adresy, které jsou spojené s virtuálním Počítačem se aktualizují.

9. **Zkontrolujte směrování**. Network Watcher můžete využít [směrování](../../network-watcher/network-watcher-check-next-hop-portal.md) schopnost potvrďte trasu nebrání provoz z směrovány do nebo z virtuálního počítače. Můžete také zkontrolovat efektivní trasy, pokud chcete zobrazit všechny efektivní trasy pro síťové rozhraní. Další informace najdete v tématu [provoz řešení potíží s virtuálního počítače pomocí efektivních tras](../../virtual-network/diagnose-network-routing-problem.md).

10. Ujistěte se, že všechny místní bráně firewall nebo brána firewall v počítači, povoluje odchozí provoz TCP 3389 do Azure.

Pokud stále dochází k potíží s RDP, můžete si [žádost o podporu](https://azure.microsoft.com/support/options/) nebo si přečtěte [podrobnější koncepty a kroky pro řešení potíží s RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-using-azure-powershell"></a>Řešení potíží s využitím Azure Powershellu
Pokud jste tak dosud neučinili, [instalace a konfigurace nejnovější Azure PowerShell](/powershell/azure/overview).

Následující příklady používají proměnné, jako `myResourceGroup`, `myVM`, a `myVMAccessExtension`. Tyto názvy proměnných a umístění nahraďte vlastními hodnotami.

> [!NOTE]
> Resetovat přihlašovací údaje uživatele a konfigurace protokolu RDP s použitím [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) rutiny Powershellu. V následujících příkladech `myVMAccessExtension` je název, který zadáte jako součást procesu. Pokud jste již dříve pracovali s VMAccessAgent, můžete získat název existující rozšíření pomocí `Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"` zkontrolovat vlastnosti virtuálního počítače. Chcete-li zobrazit název, vyhledejte v části "Rozšíření" výstupu.

Po provedení každého kroku Poradce při potížích zkuste se znovu připojit k virtuálnímu počítači. Pokud se pořád nemůžete připojit, vyzkoušejte další krok.

1. **Resetujte připojení RDP**. Tento krok řešení problémů s resetuje se konfigurace RDP když jsou zakázaná vzdálená připojení nebo brány Windows Firewall protokol RDP blokují pravidla, třeba.
   
    Následující příklad resetuje připojení RDP na virtuální počítač s názvem `myVM` v `WestUS` umístění a skupině prostředků s názvem `myResourceGroup`:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Pravidla skupiny zabezpečení sítě ověřte, zda**. Tento poradce při potížích krok ověřuje, že máte pravidlo skupiny zabezpečení sítě tak, aby povolovala provoz protokolu RDP. Výchozí port pro protokol RDP je TCP port 3389. Pravidlo pro povolení provozu protokolu RDP nemusí být vytvořen automaticky při vytvoření virtuálního počítače.
   
    Nejprve přiřadit všechna konfigurační data pro vaše skupiny zabezpečení sítě `$rules` proměnné. Následující příklad získá informace o skupině zabezpečení sítě s názvem `myNetworkSecurityGroup` ve skupině prostředků s názvem `myResourceGroup`:
   
    ```powershell
    $rules = Get-AzNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Nyní zobrazte pravidla, které jsou nakonfigurované pro tuto skupinu zabezpečení sítě. Ověřte, zda existuje pravidlo umožňující TCP port 3389 pro příchozí připojení následujícím způsobem:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    Následující příklad ukazuje zabezpečení platné pravidlo, které povoluje provoz protokolu RDP. Můžete zobrazit `Protocol`, `DestinationPortRange`, `Access`, a `Direction` jsou správně nakonfigurovány:
   
    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```
   
    Pokud nemáte pravidlo, které umožní provoz protokolu RDP, [vytvořte pravidlo skupiny zabezpečení sítě](../windows/nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Povolte TCP port 3389.
3. **Resetovat přihlašovací údaje uživatele**. Tento krok Poradce při potížích resetuje heslo pro účet místního správce, který zadáte, pokud si nejste jisti, nebo jste zapomněli přihlašovací údaje.
   
    Nejprve zadejte uživatelské jméno a nové heslo tak, že přiřadíte přihlašovací údaje, aby `$cred` proměnné následujícím způsobem:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Nyní aktualizujte přihlašovací údaje na vašem virtuálním počítači. Následující příklad aktualizuje přihlašovací údaje na virtuálním počítači s názvem `myVM` v `WestUS` umístění a skupině prostředků s názvem `myResourceGroup`:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Restartujte virtuální počítač**. Tento krok Poradce při potížích můžete opravit základní problémů, na které má virtuální počítač.
   
    Následující příklad restartuje virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`:
   
    ```powershell
    Restart-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Opětovné nasazení virtuálního počítače**. Tento poradce při potížích krok znovu nasadí virtuální počítač do jiného hostitele v rámci Azure a opravte všechny základní platformy nebo problémy se sítí.
   
    Následující příklad znovu nasadí virtuální počítač s názvem `myVM` v `WestUS` umístění a skupině prostředků s názvem `myResourceGroup`:
   
    ```powershell
    Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Zkontrolujte směrování**. Network Watcher můžete využít [směrování](../../network-watcher/network-watcher-check-next-hop-portal.md) schopnost potvrďte trasu nebrání provoz z směrovány do nebo z virtuálního počítače. Můžete také zkontrolovat efektivní trasy, pokud chcete zobrazit všechny efektivní trasy pro síťové rozhraní. Další informace najdete v tématu [provoz řešení potíží s virtuálního počítače pomocí efektivních tras](../../virtual-network/diagnose-network-routing-problem.md).

7. Ujistěte se, že všechny místní bráně firewall nebo brána firewall v počítači, povoluje odchozí provoz TCP 3389 do Azure.

Pokud stále dochází k potíží s RDP, můžete si [žádost o podporu](https://azure.microsoft.com/support/options/) nebo si přečtěte [podrobnější koncepty a kroky pro řešení potíží s RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Řešení potíží virtuální počítače vytvořené pomocí modelu nasazení Classic
Po provedení každého kroku Poradce při potížích opakujte pokus o připojení k virtuálnímu počítači.

1. **Resetujte připojení RDP**. Tento krok řešení problémů s resetuje se konfigurace RDP když jsou zakázaná vzdálená připojení nebo brány Windows Firewall protokol RDP blokují pravidla, třeba.
   
    Vyberte svůj virtuální počítač na webu Azure Portal. Klikněte na tlačítko **... Další** tlačítko a pak klikněte na **resetovat vzdálený přístup**:
   
    ![Resetovat konfiguraci RDP na webu Azure Portal](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Ověřte koncové body cloudových služeb**. Tento poradce při potížích krok ověřuje, že máte koncové body ve vašich cloudových službách tak, aby povolovala provoz protokolu RDP. Výchozí port pro protokol RDP je TCP port 3389. Pravidlo pro povolení provozu protokolu RDP nemusí být vytvořen automaticky při vytvoření virtuálního počítače.
   
   Vyberte svůj virtuální počítač na webu Azure Portal. Klikněte na tlačítko **koncové body** tlačítko, zobrazí se koncové body aktuálně nakonfigurovaný pro váš virtuální počítač. Ověřte, že existují koncové body, které umožní provoz protokolu RDP na portu TCP 3389.
   
   Následující příklad ukazuje platné koncové body, které umožňují provoz protokolu RDP:
   
   ![Ověřte koncové body služby Cloud Services na webu Azure Portal](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Pokud nemáte koncový bod, který umožňuje provoz protokolu RDP, [vytvoření koncového bodu služby Cloud Services](../windows/classic/setup-endpoints.md). Povolit TCP na privátní port 3389.
3. **Diagnostika spouštění virtuálních počítačů zkontrolujte**. Tento poradce při potížích krok kontroluje protokoly konzoly virtuálního počítače k určení, jestli se virtuální počítač hlásí problém. Ne všechny virtuální počítače mají povolení diagnostiky spouštění tak tento krok řešení problémů může být volitelný.
   
    Konkrétní kroky jsou nad rámec tohoto článku, ale může to znamenat problém širší, ovlivňuje možnosti připojení RDP. Další informace o kontrole protokoly konzoly a snímku obrazovky virtuálního počítače najdete v tématu [diagnostiky spouštění pro virtuální počítače](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **Kontrola stavu prostředků virtuálních počítačů**. Tento poradce při potížích krok ověřuje, že nejsou žádné známé problémy s platformou Azure, který může mít vliv na připojení k virtuálnímu počítači.
   
    Vyberte svůj virtuální počítač na webu Azure Portal. Přejděte dolů podokně nastavení **podpora a řešení potíží** v dolní části seznamu části. Klikněte na tlačítko **Resource Health** tlačítko. V dobrém stavu virtuálního počítače hlásí jako **dostupné**:
   
    ![Kontrola stavu prostředků virtuálních počítačů na webu Azure Portal](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Resetovat přihlašovací údaje uživatele**. Tento krok Poradce při potížích resetuje heslo pro účet místního správce, který zadáváte při nejste jistí nebo jste zapomněli přihlašovací údaje.  Po přihlášení k virtuálnímu počítači, měli byste resetovat heslo tohoto uživatele.
   
    Vyberte svůj virtuální počítač na webu Azure Portal. Přejděte dolů podokně nastavení **podpora a řešení potíží** v dolní části seznamu části. Klikněte na tlačítko **resetovat heslo** tlačítko. Zadejte svoje uživatelské jméno a nové heslo. Nakonec klikněte na tlačítko **Uložit** tlačítka:
   
    ![Resetovat přihlašovací údaje uživatele na webu Azure Portal](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Restartujte virtuální počítač**. Tento krok Poradce při potížích můžete opravit základní problémů, na které má virtuální počítač.
   
    Vyberte svůj virtuální počítač na webu Azure Portal a klikněte na tlačítko **přehled** kartu. Klikněte na tlačítko **restartovat** tlačítka:
   
    ![Restartujte virtuální počítač na webu Azure Portal](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Ujistěte se, že všechny místní bráně firewall nebo brána firewall v počítači, povoluje odchozí provoz TCP 3389 do Azure.

Pokud stále dochází k potíží s RDP, můžete si [žádost o podporu](https://azure.microsoft.com/support/options/) nebo si přečtěte [podrobnější koncepty a kroky pro řešení potíží s RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-specific-rdp-errors"></a>Poradce při potížích s konkrétními chybami protokolu RDP
Při pokusu o připojení k vašemu virtuálnímu počítači pomocí protokolu RDP, může dojít k určité chybové zprávě. Následují nejběžnější chybové zprávy:

* [Vzdálená relace byla odpojena, protože nejsou žádné vzdálené plochy licenční servery mohly poskytnout licenci k dispozici](troubleshoot-specific-rdp-errors.md#rdplicense).
* [Vzdálená plocha nenašla počítač "name"](troubleshoot-specific-rdp-errors.md#rdpname).
* [Došlo k chybě ověřování. Místní úřad zabezpečení nelze kontaktovat](troubleshoot-specific-rdp-errors.md#rdpauth).
* [Chyba zabezpečení Windows: Vaše přihlašovací údaje nefungovala](troubleshoot-specific-rdp-errors.md#wincred).
* [Počítač se nemůže připojit ke vzdálenému počítači](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>Další materiály
Pokud žádná z těchto chyb došlo k chybě a se pořád nemůžete připojit k virtuálnímu počítači přes vzdálenou plochu, přečtěte si na podrobné [Průvodce odstraňováním potíží pro vzdálenou plochu](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Řešení potíží s kroky k používání aplikace běžící na virtuálním počítači, naleznete v tématu [řešení potíží s přístupem k aplikaci spuštěné na Virtuálním počítači Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Pokud máte problémy s použitím Secure Shell (SSH) k připojení k virtuálnímu počítači s Linuxem v Azure, najdete v článku [řešení potíží s připojení SSH k virtuálnímu počítači s Linuxem v Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


