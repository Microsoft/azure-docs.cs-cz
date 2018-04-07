---
title: Nelze se připojit pomocí protokolu RDP pro virtuální počítač s Windows v Azure | Microsoft Docs
description: Řešení problémů, pokud se nemůžete připojit k virtuálnímu počítači Windows v Azure pomocí vzdálené plochy
keywords: Vzdálené plochy chyba, Chyba připojení ke vzdálené ploše, nelze se připojit k virtuální počítač, řešení potíží vzdálené plochy
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: danis
ms.openlocfilehash: 60c54850c1ca5de0e9bda4b48688ba297874e48e
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Řešení potíží s připojení ke vzdálené ploše pro virtuální počítač Azure
Protokol RDP (Remote Desktop) připojení k systému Windows Azure virtuálního počítače (VM) může selhat z různých důvodů, můžete ponechat nelze získat přístup k virtuálnímu počítači. Tento problém může být pomocí služby Vzdálená plocha na virtuální počítač, síťové připojení nebo klienta vzdálené plochy v hostitelském počítači. Tento článek vás provede některé z nejběžnějších metod k vyřešení potíží s připojeními RDP. 

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, obraťte se na Azure odborníky na [fórech MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Rychlé řešení potíží
Po dokončení každého kroku řešení potíží pokuste o připojení k virtuálnímu počítači:

1. Resetování konfigurace vzdálené plochy.
2. Zkontrolujte skupiny zabezpečení sítě pravidla / Cloud Services koncové body.
3. Zkontrolujte protokoly konzoly virtuálního počítače.
4. Resetovat síťový adaptér pro virtuální počítač.
5. Zkontrolujte stav prostředků virtuálních počítačů.
6. Resetování hesla virtuálních počítačů.
7. Restartujte virtuální počítač.
8. Opětovné nasazení virtuálního počítače.

Pokud potřebujete podrobnější kroky a vysvětlení pokračujte ve čtení. Ověřte, že místní síťové vybavení, jako jsou směrovače a brány firewall neblokují odchozí TCP port 3389, jak jsme uvedli v [podrobné RDP scénáře řešení potíží](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!TIP]
> Pokud **připojit** tlačítko pro virtuální počítač na portálu odhlašování neaktivní a nejste připojení k Azure přes [Express Route](../../expressroute/expressroute-introduction.md) nebo [Site-to-Site VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) připojení, musíte vytvořit a přiřadit veřejnou IP adresu virtuálního počítače před použitím protokolu RDP. Podle potřeby si můžete přečíst další informace o [veřejných IP adresách v Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Způsoby řešení potíží protokolu RDP
Řešení potíží s virtuální počítače vytvořené pomocí modelu nasazení Resource Manager pomocí jedné z následujících metod:

* [Portál Azure](#using-the-azure-portal) – skvělé, pokud budete potřebovat rychle resetovat RDP konfigurace nebo uživatelské přihlašovací údaje a nemáte Azure nástroje nainstalované.
* [Prostředí Azure PowerShell](#using-azure-powershell) – Pokud umíte pracovat s příkazovém řádku prostředí PowerShell rychle obnovit RDP konfigurace nebo přihlašovací údaje uživatele pomocí rutin prostředí Azure PowerShell.

Můžete také získat kroky k vyřešení problémů s virtuální počítače vytvořené pomocí [model nasazení Classic](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Řešení potíží pomocí portálu Azure
Po dokončení každého kroku řešení potíží zkuste znovu připojit k virtuálnímu počítači. Pokud se pořád nemůžete připojit, zkuste na další krok.

1. **Resetování připojení RDP**. Tento krok řešení potíží obnoví konfiguraci RDP, pokud vzdálená připojení jsou zakázané nebo pravidla brány Windows Firewall blokuje protokolu RDP, například.
   
    Vyberte virtuální počítač na portálu Azure. Projděte dolů podokno nastavení **podporu + Poradce při potížích s** části téměř dolní části seznamu. Klikněte **resetovat heslo** tlačítko. Nastavte **režimu** k **jenom resetování konfigurace** a pak klikněte na tlačítko **aktualizace** tlačítko:
   
    ![Konfiguraci RDP na portálu Azure](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Skupina zabezpečení sítě ověřte, zda pravidla**. Použijte [Ověření toku protokolu IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) a ověřte, jestli pravidlo ve skupině zabezpečení sítě blokuje provoz do nebo z virtuálního počítače. Můžete také zkontrolovat pravidel skupiny zabezpečení efektivní zajistit příchozí "Povolit" NSG pravidlo existuje a prioritu pro RDP port (standardně 3389). Další informace najdete v tématu [tok provozu pomocí efektivní pravidla zabezpečení pro řešení potíží s virtuálních počítačů](../../virtual-network/virtual-network-nsg-troubleshoot-portal.md#using-effective-security-rules-to-troubleshoot-vm-traffic-flow).

3. **Diagnostika spouštění virtuálních počítačů zkontrolujte**. Tento krok řešení potíží zkontroluje protokoly konzoly virtuálního počítače, abyste zjistili, pokud je virtuální počítač oznámení chyby. Ne všechny virtuální počítače mají Diagnostika spouštění povoleno, takže tento krok řešení problémů může být volitelné.
   
    Konkrétní kroky pro řešení potíží jsou nad rámec tohoto článku, ale může znamenat širší problém, který ovlivňuje připojení RDP. Další informace o kontrole protokoly konzoly a snímek virtuálního počítače najdete v tématu [Diagnostika spouštění pro virtuální počítače](boot-diagnostics.md).

4. **Resetovat síťový adaptér pro virtuální počítač**. Další informace najdete v tématu [jak resetovat síťový adaptér pro virtuální počítač s Windows Azure](reset-network-interface.md).
5. **Zkontrolujte stav virtuálního počítače prostředků**. Toto řešení potíží krok ověřuje, že zde nejsou žádné známé problémy s platformou Azure, který může mít vliv na připojení k virtuálnímu počítači.
   
    Vyberte virtuální počítač na portálu Azure. Projděte dolů podokno nastavení **podporu + Poradce při potížích s** části téměř dolní části seznamu. Klikněte **stav prostředku** tlačítko. V pořádku virtuálního počítače hlásí, že je **dostupné**:
   
    ![Zkontrolujte stav virtuálního počítače prostředků na portálu Azure](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Resetovat přihlašovací údaje uživatele**. Tento krok řešení potíží resetuje heslo pro účet místního správce, když nejste jistí nebo zapomněli přihlašovací údaje.  Po přihlášení do virtuálního počítače byste měli obnovit heslo pro daného uživatele.
   
    Vyberte virtuální počítač na portálu Azure. Projděte dolů podokno nastavení **podporu + Poradce při potížích s** části téměř dolní části seznamu. Klikněte **resetovat heslo** tlačítko. Zajistěte, aby **režimu** je nastaven na **resetovat heslo** a pak zadejte svoje uživatelské jméno a nové heslo. Nakonec klikněte na **aktualizace** tlačítko:
   
    ![Resetovat přihlašovací údaje uživatele na portálu Azure](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Restartujte virtuální počítač**. Tento krok řešení potíží můžete vyřešit základní problémů, které má virtuální počítač.
   
    Vyberte virtuální počítač na portálu Azure a klikněte na **přehled** kartě. Klikněte **restartujte** tlačítko:
   
    ![Restartujte virtuální počítač na portálu Azure](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Opětovné nasazení virtuálního počítače**. Tento krok řešení potíží opětovně nasadí virtuálního počítače na jiného hostitele v rámci Azure a opravte všechny základní platformu nebo problémům se sítí.
   
    Vyberte virtuální počítač na portálu Azure. Projděte dolů podokno nastavení **podporu + Poradce při potížích s** části téměř dolní části seznamu. Klikněte **znovu nasaďte** tlačítko a potom klikněte na **znovu nasaďte**:
   
    ![Znovu nasaďte virtuální počítač na portálu Azure](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    Po dokončení této operace, dojde ke ztrátě dat v dočasných disku a jsou aktualizovány dynamické IP adresy, které jsou spojeny s virtuálním Počítačem.

9. **Ověřte směrování**. Použít sledovací proces sítě [dalšího směrování](../../network-watcher/network-watcher-check-next-hop-portal.md) schopnost potvrďte, zda trasa nebrání provoz z směrovány do nebo z virtuálního počítače. Můžete také zkontrolovat efektivní trasy zobrazíte všechny efektivní trasy pro síťové rozhraní. Další informace najdete v tématu [řešení virtuálních počítačů pomocí efektivní směrování provozu toku](../../virtual-network/virtual-network-routes-troubleshoot-portal.md#using-effective-routes-to-troubleshoot-vm-traffic-flow).

10. Ujistěte se, že žádné místní brány firewall nebo brána firewall v počítači, umožňuje odchozí přenosy TCP 3389 do Azure.

Pokud jsou stále dochází k problémům RDP, můžete [otevřete žádost o podporu](https://azure.microsoft.com/support/options/) nebo si můžete přečíst [podrobnější RDP, koncepty a kroky pro řešení potíží](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-using-azure-powershell"></a>Řešení potíží pomocí prostředí Azure PowerShell
Pokud jste to ještě neudělali, [instalace a konfigurace prostředí Azure PowerShell nejnovější](/powershell/azure/overview).

Následující příklady pomocí proměnných, jako například `myResourceGroup`, `myVM`, a `myVMAccessExtension`. Tyto názvy proměnných a umístění nahraďte vlastními hodnotami.

> [!NOTE]
> Resetovat přihlašovací údaje uživatele a konfiguraci RDP pomocí [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) rutiny prostředí PowerShell. V následujících příkladech `myVMAccessExtension` je název, který zadáte jako součást procesu. Jestliže jste dříve pracovali s VMAccessAgent, můžete jej získat název existující rozšíření pomocí `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` a zkontrolujte vlastnosti virtuálního počítače. Chcete-li zobrazit název, vyhledejte v části "Rozšíření" výstupu.

Po dokončení každého kroku řešení potíží zkuste znovu připojit k virtuálnímu počítači. Pokud se pořád nemůžete připojit, zkuste na další krok.

1. **Resetování připojení RDP**. Tento krok řešení potíží obnoví konfiguraci RDP, pokud vzdálená připojení jsou zakázané nebo pravidla brány Windows Firewall blokuje protokolu RDP, například.
   
    Následující příklad resetuje připojení RDP na virtuální počítač s názvem `myVM` v `WestUS` umístění a ve skupině prostředků s názvem `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Skupina zabezpečení sítě ověřte, zda pravidla**. Tento řešení potíží krok ověřuje, že máte pravidlo ve skupině zabezpečení sítě tak, aby povolovala provoz protokolu RDP. Výchozí port pro protokol RDP je TCP port 3389. Pravidlo pro provoz protokolu RDP povolení nemusí být vytvořen automaticky, když vytvoříte virtuální počítač.
   
    Nejprve přiřadit všechna konfigurační data pro vaší skupinu zabezpečení sítě `$rules` proměnné. Následující příklad získává informace o skupině zabezpečení sítě s názvem `myNetworkSecurityGroup` ve skupině prostředků s názvem `myResourceGroup`:
   
    ```powershell
    $rules = Get-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Prohlédnout pravidla, které jsou nakonfigurované pro tuto skupinu zabezpečení sítě. Ověřte, zda pravidlo existuje umožňující TCP port 3389 pro příchozí připojení následujícím způsobem:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    Následující příklad ukazuje pravidlo platný zabezpečení, které povoluje provoz protokolu RDP. Můžete zobrazit `Protocol`, `DestinationPortRange`, `Access`, a `Direction` jsou správně nakonfigurovány:
   
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
   
    Pokud nemáte pravidlo, které povoluje provoz protokolu RDP, [vytvořte skupinu zabezpečení sítě pravidlo](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Povolte TCP port 3389.
3. **Resetovat přihlašovací údaje uživatele**. Tento krok řešení potíží resetuje heslo pro účet místního správce, který zadáte, pokud si nejste jisti nebo zapomněli, přihlašovací údaje.
   
    Nejdřív zadejte uživatelské jméno a nové heslo přiřazením přihlašovací údaje, aby `$cred` proměnná následujícím způsobem:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Nyní aktualizujte přihlašovací údaje na vašem virtuálním počítači. Následující příklad aktualizuje pověření na virtuálním počítači s názvem `myVM` v `WestUS` umístění a ve skupině prostředků s názvem `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Restartujte virtuální počítač**. Tento krok řešení potíží můžete vyřešit základní problémů, které má virtuální počítač.
   
    Následující příklad restartuje virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`:
   
    ```powershell
    Restart-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Opětovné nasazení virtuálního počítače**. Tento krok řešení potíží opětovně nasadí virtuálního počítače na jiného hostitele v rámci Azure a opravte všechny základní platformu nebo problémům se sítí.
   
    Následující příklad opětovně nasadí virtuální počítač s názvem `myVM` v `WestUS` umístění a ve skupině prostředků s názvem `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Ověřte směrování**. Použít sledovací proces sítě [dalšího směrování](../../network-watcher/network-watcher-check-next-hop-portal.md) schopnost potvrďte, zda trasa nebrání provoz z směrovány do nebo z virtuálního počítače. Můžete také zkontrolovat efektivní trasy zobrazíte všechny efektivní trasy pro síťové rozhraní. Další informace najdete v tématu [řešení virtuálních počítačů pomocí efektivní směrování provozu toku](../../virtual-network/virtual-network-routes-troubleshoot-powershell.md#using-effective-routes-to-troubleshoot-vm-traffic-flow).

7. Ujistěte se, že žádné místní brány firewall nebo brána firewall v počítači, umožňuje odchozí přenosy TCP 3389 do Azure.

Pokud jsou stále dochází k problémům RDP, můžete [otevřete žádost o podporu](https://azure.microsoft.com/support/options/) nebo si můžete přečíst [podrobnější RDP, koncepty a kroky pro řešení potíží](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Řešení potíží s virtuální počítače vytvořené pomocí modelu nasazení Classic
Po dokončení každého kroku řešení potíží pokuste o připojení k virtuálnímu počítači.

1. **Resetování připojení RDP**. Tento krok řešení potíží obnoví konfiguraci RDP, pokud vzdálená připojení jsou zakázané nebo pravidla brány Windows Firewall blokuje protokolu RDP, například.
   
    Vyberte virtuální počítač na portálu Azure. Klikněte **... Další** tlačítko a pak klikněte na **resetovat vzdálený přístup**:
   
    ![Konfiguraci RDP na portálu Azure](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Ověření koncových bodů cloudové služby**. Tento krok řešení potíží ověřuje, že máte koncové body ve vašich cloudových službách tak, aby povolovala provoz protokolu RDP. Výchozí port pro protokol RDP je TCP port 3389. Pravidlo pro provoz protokolu RDP povolení nemusí být vytvořen automaticky, když vytvoříte virtuální počítač.
   
   Vyberte virtuální počítač na portálu Azure. Klikněte **koncové body** tlačítko zobrazí se koncové body konfigurovaná pro virtuální počítač. Ověřte, že existují koncových bodů, povolit provoz protokolu RDP na TCP port 3389.
   
   Následující příklad ukazuje platný koncové body, které umožňují provoz protokolu RDP:
   
   ![Ověření koncových bodů cloudové služby na portálu Azure](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Pokud nemáte koncový bod, který umožňuje provoz protokolu RDP, [vytvoření koncového bodu služby Cloud Services](classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Povolit TCP privátní port 3389.
3. **Diagnostika spouštění virtuálních počítačů zkontrolujte**. Tento krok řešení potíží zkontroluje protokoly konzoly virtuálního počítače, abyste zjistili, pokud je virtuální počítač oznámení chyby. Ne všechny virtuální počítače mají Diagnostika spouštění povoleno, takže tento krok řešení problémů může být volitelné.
   
    Konkrétní kroky pro řešení potíží jsou nad rámec tohoto článku, ale může znamenat širší problém, který ovlivňuje připojení RDP. Další informace o kontrole protokoly konzoly a snímek virtuálního počítače najdete v tématu [Diagnostika spouštění pro virtuální počítače](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **Zkontrolujte stav virtuálního počítače prostředků**. Toto řešení potíží krok ověřuje, že zde nejsou žádné známé problémy s platformou Azure, který může mít vliv na připojení k virtuálnímu počítači.
   
    Vyberte virtuální počítač na portálu Azure. Projděte dolů podokno nastavení **podporu + Poradce při potížích s** části téměř dolní části seznamu. Klikněte **stav prostředku** tlačítko. V pořádku virtuálního počítače hlásí, že je **dostupné**:
   
    ![Zkontrolujte stav virtuálního počítače prostředků na portálu Azure](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Resetovat přihlašovací údaje uživatele**. Tento krok řešení potíží resetuje heslo pro účet místního správce, který zadáte, pokud nejste jistí nebo zapomněli přihlašovací údaje.  Po přihlášení do virtuálního počítače byste měli obnovit heslo pro daného uživatele.
   
    Vyberte virtuální počítač na portálu Azure. Projděte dolů podokno nastavení **podporu + Poradce při potížích s** části téměř dolní části seznamu. Klikněte **resetovat heslo** tlačítko. Zadejte svoje uživatelské jméno a nové heslo. Nakonec klikněte na **Uložit** tlačítko:
   
    ![Resetovat přihlašovací údaje uživatele na portálu Azure](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Restartujte virtuální počítač**. Tento krok řešení potíží můžete vyřešit základní problémů, které má virtuální počítač.
   
    Vyberte virtuální počítač na portálu Azure a klikněte na **přehled** kartě. Klikněte **restartujte** tlačítko:
   
    ![Restartujte virtuální počítač na portálu Azure](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Ujistěte se, že žádné místní brány firewall nebo brána firewall v počítači, umožňuje odchozí přenosy TCP 3389 do Azure.

Pokud jsou stále dochází k problémům RDP, můžete [otevřete žádost o podporu](https://azure.microsoft.com/support/options/) nebo si můžete přečíst [podrobnější RDP, koncepty a kroky pro řešení potíží](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-specific-rdp-errors"></a>Řešení specifických chyb protokolu RDP
Při pokusu o připojení k virtuálnímu počítači prostřednictvím protokolu RDP se můžete setkat s konkrétní chybová zpráva. Tady jsou nejběžnější chybové zprávy:

* [Vzdálená relace byla odpojena, protože nejsou k dispozici vzdálené plochy licence k dispozici žádné servery k poskytnutí licence](troubleshoot-specific-rdp-errors.md#rdplicense).
* [Vzdálená plocha nenašla počítač "název"](troubleshoot-specific-rdp-errors.md#rdpname).
* [Došlo k chybě ověřování. Místní úřad zabezpečení nelze kontaktovat](troubleshoot-specific-rdp-errors.md#rdpauth).
* [Chyba zabezpečení systému Windows: přihlašovací údaje nebyla úspěšná](troubleshoot-specific-rdp-errors.md#wincred).
* [Počítač se nemůže připojit ke vzdálenému počítači](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>Další zdroje informací:
Pokud žádná z těchto chyb došlo k chybě a se pořád nemůžete připojit k virtuálnímu počítači prostřednictvím vzdálené plochy, najdete v podrobné [Průvodce řešením potíží pro vzdálené plochy](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Řešení potíží s kroky v přístupu k aplikacím spuštěným na virtuálním počítači, najdete v části [řešení potíží s přístupem k aplikaci spuštěné na virtuálním počítači Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Pokud máte problémy s Secure Shell (SSH) pro připojení k virtuální počítač s Linuxem v Azure najdete v části [řešení SSH připojení k virtuální počítač s Linuxem v Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

