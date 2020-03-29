---
title: Nelze se připojit k rdp k virtuálnímu počítači windows v Azure | Dokumenty společnosti Microsoft
description: Řešení problémů, když se nemůžete připojit k virtuálnímu počítači s Windows v Azure pomocí vzdálené plochy
keywords: Chyba vzdálené plochy,chyba připojení ke vzdálené ploše,nelze se připojit k virtuálnímu počítači,řešení potíží se vzdálenou plochou
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/23/2018
ms.author: akjosh
ms.openlocfilehash: cbca8e631da8b99aa0ea4bdc6d099f3dbd2ed9b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916604"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Poradce při potížích s připojením ke vzdálené ploše k virtuálnímu počítači Azure
Připojení RDP (Remote Desktop Protocol) k vašemu virtuálnímu počítači Azure s Windows může z různých důvodů selhat a vy tak k němu můžete ztratit přístup. Problém může být ve službě Vzdálená plocha na virtuálním počítači, v síťovém připojením nebo v klientovi vzdálené plochy na hostitelském počítači. Tento článek vás provede několika nejběžnějšími metodami řešení potíží s připojením RDP. 

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/forums/). Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.

 

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Rychlé kroky řešení potíží
Po každém kroku řešení potíží zkuste znovu připojit k virtuálnímu virtuálnímu mísu:

1. Obnovit konfiguraci vzdálené plochy.
2. Zkontrolujte pravidla skupiny zabezpečení sítě / koncové body cloudových služeb.
3. Zkontrolujte protokoly konzoly virtuálních vod.
4. Resetujte nic pro virtuální ho disponiál.
5. Zkontrolujte stav prostředků virtuálního soudu.
6. Resetujte heslo virtuálního počítače.
7. Restartujte virtuální počítač.
8. Znovu nasaďte virtuální počítač.

Pokračujte ve čtení, pokud potřebujete podrobnější kroky a vysvětlení. Ověřte, zda místní síťová zařízení, jako jsou směrovače a brány firewall, neblokují odchozí port TCP 3389, jak je uvedeno v [podrobných scénářích řešení potíží protokolu RDP](detailed-troubleshoot-rdp.md).

> [!TIP]
> Pokud je tlačítko **Připojit** pro váš virtuální počítač na portálu zašedlou a nejste připojeni k Azure prostřednictvím [expresní trasy](../../expressroute/expressroute-introduction.md) nebo připojení [VPN site-to-site,](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) musíte vytvořit a přiřadit virtuálnímu počítači veřejnou IP adresu, než budete moct používat RDP. Podle potřeby si můžete přečíst další informace o [veřejných IP adresách v Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Způsoby řešení problémů s prv
Řešení potíží s virtuálními počítačemi vytvořenými pomocí modelu nasazení Správce prostředků můžete řešit pomocí jedné z následujících metod:

* Azure Portal – skvělé, pokud potřebujete rychle obnovit konfiguraci RDP nebo přihlašovací údaje uživatele a nemáte nainstalované nástroje Azure.
* Azure PowerShell – pokud se vám líbí výzva k Prostředí PowerShell, rychle resetujte konfiguraci RDP nebo přihlašovací údaje uživatele pomocí rutin Azure PowerShell.

Můžete také najít kroky při řešení potíží s virtuálními počítači vytvořenými pomocí [modelu klasického nasazení](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Poradce při potížích s používáním portálu Azure
Po každém kroku řešení potíží zkuste znovu připojit k virtuálnímu počítači. Pokud se stále nemůžete připojit, vyzkoušejte další krok.

1. **Obnovte připojení RDP**. Tento krok řešení potíží obnoví konfiguraci protokolu RDP, když jsou zakázána vzdálená připojení nebo například pravidla brány WINDOWS Firewall blokují protokol RDP.
   
    Vyberte virtuální počítač na webu Azure Portal. Posuňte se dolů v podokně nastavení do části **Podpora + Poradce při potížích** v dolní části seznamu. Klepněte na tlačítko **Obnovit heslo.** Nastavte **pouze konfiguraci režimu** na **resetování** a klepněte na tlačítko **Aktualizovat:**
   
    ![Obnovení konfigurace PROTOKOLU RDP na webu Azure Portal](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Ověřte pravidla skupiny zabezpečení sítě**. Pomocí [ověření toku IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) můžete ověřit, jestli pravidlo ve skupině zabezpečení sítě blokuje provoz do nebo z virtuálního počítače. Můžete také zkontrolovat účinná pravidla skupiny zabezpečení, abyste zajistili, že příchozí pravidlo skupiny zabezpečení zabezpečení existuje a má prioritu pro port RDP (výchozí 3389). Další informace naleznete [v tématu Použití efektivních pravidel zabezpečení k řešení potíží s tokem provozu virtuálních montovací](../../virtual-network/diagnose-network-traffic-filter-problem.md).

3. **Zkontrolujte diagnostiku spuštění virtuálního aplikace**. Tento krok řešení potíží zkontroluje protokoly konzoly virtuálního trhu a zjistíte, jestli virtuální ho správu hlásí problém. Ne všechny virtuální aplikace mají povolenou diagnostiku spouštění, takže tento krok řešení potíží může být volitelný.
   
    Konkrétní kroky řešení potíží jsou nad rámec tohoto článku, ale může znamenat širší problém, který ovlivňuje připojení RDP. Další informace o kontrole protokolů konzoly a snímku obrazovky virtuálního počítače najdete v tématu [Diagnostika spouštění virtuálních počítačů](boot-diagnostics.md).

4. **Resetování nic pro virtuální hod**. Další informace najdete v tématu [jak obnovit nic pro Azure Windows VM](../windows/reset-network-interface.md).
5. **Zkontrolujte stav prostředků virtuálního soudu**. Tento krok řešení potíží ověří, že neexistují žádné známé problémy s platformou Azure, které mohou mít vliv na připojení k virtuálnímu počítači.
   
    Vyberte virtuální počítač na webu Azure Portal. Posuňte se dolů v podokně nastavení do části **Podpora + Poradce při potížích** v dolní části seznamu. Klepněte na tlačítko **Stav zdroje.** V pořádku virtuálního média hlásí jako **k dispozici**:
   
    ![Kontrola stavu prostředků virtuálního počítače na webu Azure Portal](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Obnovit pověření uživatele**. Tento krok řešení potíží obnoví heslo k účtu místního správce, pokud si nejste jisti nebo jste zapomněli pověření.  Po přihlášení k virtuálnímu počítače byste měli resetovat heslo pro tohoto uživatele.
   
    Vyberte virtuální počítač na webu Azure Portal. Posuňte se dolů v podokně nastavení do části **Podpora + Poradce při potížích** v dolní části seznamu. Klepněte na tlačítko **Obnovit heslo.** Ujistěte se, že **režim** je nastaven na **Resetování hesla** a zadejte své uživatelské jméno a nové heslo. Nakonec klikněte na tlačítko **Aktualizovat:**
   
    ![Obnovení přihlašovacích údajů uživatele na webu Azure Portal](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Restartujte virtuální počítač**. Tento krok řešení potíží můžete opravit všechny základní problémy virtuálního mísa sama má.
   
    Vyberte virtuální počítač na webu Azure Portal a klikněte na kartu **Přehled.** Klikněte na tlačítko **Restartovat:**
   
    ![Restartování virtuálního počítače na webu Azure Portal](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Znovu nasadit virtuální počítač**. Tento krok řešení potíží znovu nasadí váš virtuální počítač do jiného hostitele v rámci Azure opravit všechny základní platformu nebo síťové problémy.
   
    Vyberte virtuální počítač na webu Azure Portal. Posuňte se dolů v podokně nastavení do části **Podpora + Poradce při potížích** v dolní části seznamu. Klikněte na tlačítko **Znovu nasadit** a potom klikněte na **Znovu nasadit**:
   
    ![Opětovné nasazení virtuálního počítače na webu Azure Portal](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    Po dokončení této operace se ztratí data dočasných disků a budou aktualizovány dynamické IP adresy, které jsou přidruženy k virtuálnímu počítači.

9. **Ověřte směrování**. Pomocí funkce [dalšího směrování](../../network-watcher/network-watcher-check-next-hop-portal.md) sledování sítě můžete potvrdit, že trasa nebrání směrování přenosů do nebo z virtuálního počítače. Můžete také zkontrolovat efektivní trasy a zobrazit všechny efektivní trasy pro síťové rozhraní. Další informace najdete [v tématu Použití efektivní chodů k řešení potíží s tokem provozu virtuálních montovny](../../virtual-network/diagnose-network-routing-problem.md).

10. Ujistěte se, že jakákoli místní brána firewall nebo brána firewall v počítači umožňuje odchozí přenos protokolu TCP 3389 do Azure.

Pokud stále dochází k problémům s rdp, můžete [otevřít žádost o podporu](https://azure.microsoft.com/support/options/) nebo si přečíst podrobnější [koncepty řešení potíží s rdp a kroky](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-using-azure-powershell"></a>Řešení potíží pomocí Azure PowerShellu
Pokud jste to ještě neudělali, [nainstalujte a nakonfigurujte nejnovější Azure PowerShell](/powershell/azure/overview).

Následující příklady používají proměnné, `myResourceGroup` `myVM`jako `myVMAccessExtension`jsou například , a . Nahraďte tyto názvy proměnných a umístění vlastními hodnotami.

> [!NOTE]
> Obnovení pověření uživatele a konfigurace protokolu RDP pomocí rutiny [prostředí PowerShell Set-AzVMAccessExtension.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) V následujících příkladech je název, `myVMAccessExtension` který zadáte jako součást procesu. Pokud jste dříve pracovali s VMAccessAgent, můžete získat název existující `Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"` rozšíření pomocí zkontrolovat vlastnosti virtuálního počítače. Chcete-li zobrazit název, podívejte se do části "Rozšíření" výstupu.

Po každém kroku řešení potíží zkuste znovu připojit k virtuálnímu počítači. Pokud se stále nemůžete připojit, vyzkoušejte další krok.

1. **Obnovte připojení RDP**. Tento krok řešení potíží obnoví konfiguraci protokolu RDP, když jsou zakázána vzdálená připojení nebo například pravidla brány WINDOWS Firewall blokují protokol RDP.
   
    Následující příklad obnoví připojení RDP na virtuálním počítači pojmenovaném `myVM` `WestUS` v `myResourceGroup`umístění a ve skupině prostředků s názvem :
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Ověřte pravidla skupiny zabezpečení sítě**. Tento krok řešení potíží ověří, zda máte ve skupině zabezpečení sítě pravidlo umožňující provoz protokolu RDP. Výchozí port pro protokol RDP je port TCP 3389. Pravidlo umožňující provoz RDP nemusí být vytvořeno automaticky při vytváření virtuálního počítače.
   
    Nejprve přiřaďte k proměnné všechna `$rules` konfigurační data pro skupinu zabezpečení sítě. Následující příklad získá informace o skupině `myNetworkSecurityGroup` zabezpečení sítě s `myResourceGroup`názvem ve skupině prostředků s názvem :
   
    ```powershell
    $rules = Get-AzNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Nyní zobrazte pravidla, která jsou nakonfigurována pro tuto skupinu zabezpečení sítě. Ověřte, zda existuje pravidlo umožňující port TCP 3389 pro příchozí připojení následujícím způsobem:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    Následující příklad ukazuje platné pravidlo zabezpečení, které povoluje provoz protokolu RDP. Můžete vidět `Protocol` `DestinationPortRange`, `Access`, `Direction` a jsou správně nakonfigurovány:
   
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
   
    Pokud nemáte pravidlo, které by umožňovala provoz protokolu RDP, [vytvořte pravidlo skupiny zabezpečení sítě](../windows/nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Povolit port TCP 3389.
3. **Obnovit pověření uživatele**. Tento krok řešení potíží obnoví heslo k účtu místního správce, který zadáte, když si nejste jisti nebo jste zapomněli pověření.
   
    Nejprve zadejte uživatelské jméno a nové heslo přiřazením pověření `$cred` proměnné následujícím způsobem:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Teď aktualizujte přihlašovací údaje na vašem virtuálním počítači. Následující příklad aktualizuje pověření na virtuálním `myVM` počítači pojmenovanév `WestUS` umístění `myResourceGroup`a ve skupině prostředků s názvem :
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Restartujte virtuální počítač**. Tento krok řešení potíží můžete opravit všechny základní problémy virtuálního mísa sama má.
   
    Následující příklad restartuje virtuální `myVM` počítač pojmenovaný `myResourceGroup`ve skupině prostředků s názvem :
   
    ```powershell
    Restart-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Znovu nasadit virtuální počítač**. Tento krok řešení potíží znovu nasadí váš virtuální počítač do jiného hostitele v rámci Azure opravit všechny základní platformu nebo síťové problémy.
   
    Následující příklad znovu nasadí `myVM` virtuální `WestUS` hod pojmenovaný v `myResourceGroup`umístění a ve skupině prostředků s názvem :
   
    ```powershell
    Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Ověřte směrování**. Pomocí funkce [dalšího směrování](../../network-watcher/network-watcher-check-next-hop-portal.md) sledování sítě můžete potvrdit, že trasa nebrání směrování přenosů do nebo z virtuálního počítače. Můžete také zkontrolovat efektivní trasy a zobrazit všechny efektivní trasy pro síťové rozhraní. Další informace najdete [v tématu Použití efektivní chodů k řešení potíží s tokem provozu virtuálních montovny](../../virtual-network/diagnose-network-routing-problem.md).

7. Ujistěte se, že jakákoli místní brána firewall nebo brána firewall v počítači umožňuje odchozí přenos protokolu TCP 3389 do Azure.

Pokud stále dochází k problémům s rdp, můžete [otevřít žádost o podporu](https://azure.microsoft.com/support/options/) nebo si přečíst podrobnější [koncepty řešení potíží s rdp a kroky](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Poradce při potížích s virtuálními počítačemi vytvořenými pomocí modelu klasického nasazení

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Po každém kroku řešení potíží zkuste znovu připojit k virtuálnímu virtuálnímu zařízení.

1. **Obnovte připojení RDP**. Tento krok řešení potíží obnoví konfiguraci protokolu RDP, když jsou zakázána vzdálená připojení nebo například pravidla brány WINDOWS Firewall blokují protokol RDP.
   
    Vyberte virtuální počítač na webu Azure Portal. Klikněte na **... Tlačítko Další** a potom klepněte na **tlačítko Obnovit vzdálený přístup**:
   
    ![Obnovení konfigurace PROTOKOLU RDP na webu Azure Portal](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Ověřte koncové body cloudových služeb**. Tento krok řešení potíží ověří, že máte koncové body ve vašich cloudových služeb povolit provoz RDP. Výchozí port pro protokol RDP je port TCP 3389. Pravidlo umožňující provoz RDP nemusí být vytvořeno automaticky při vytváření virtuálního počítače.
   
   Vyberte virtuální počítač na webu Azure Portal. Kliknutím na tlačítko **Koncové body** zobrazíte koncové body aktuálně nakonfigurované pro váš virtuální počítač. Ověřte, zda existují koncové body, které umožňují přenosy protokolu RDP na portu TCP 3389.
   
   Následující příklad ukazuje platné koncové body, které povolují provoz RDP:
   
   ![Ověření koncových bodů cloudových služeb na webu Azure Portal](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Pokud nemáte koncový bod, který umožňuje provoz RDP, [vytvořte koncový bod cloudových služeb](../windows/classic/setup-endpoints.md). Povolit tcp na soukromý port 3389.
3. **Zkontrolujte diagnostiku spuštění virtuálního aplikace**. Tento krok řešení potíží zkontroluje protokoly konzoly virtuálního trhu a zjistíte, jestli virtuální ho správu hlásí problém. Ne všechny virtuální aplikace mají povolenou diagnostiku spouštění, takže tento krok řešení potíží může být volitelný.
   
    Konkrétní kroky řešení potíží jsou nad rámec tohoto článku, ale může znamenat širší problém, který ovlivňuje připojení RDP. Další informace o kontrole protokolů konzoly a snímku obrazovky virtuálního počítače najdete v tématu [Diagnostika spouštění virtuálních počítačů](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **Zkontrolujte stav prostředků virtuálního soudu**. Tento krok řešení potíží ověří, že neexistují žádné známé problémy s platformou Azure, které mohou mít vliv na připojení k virtuálnímu počítači.
   
    Vyberte virtuální počítač na webu Azure Portal. Posuňte se dolů v podokně nastavení do části **Podpora + Poradce při potížích** v dolní části seznamu. Klepněte na tlačítko **Stav zdroje.** V pořádku virtuálního média hlásí jako **k dispozici**:
   
    ![Kontrola stavu prostředků virtuálního počítače na webu Azure Portal](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Obnovit pověření uživatele**. Tento krok řešení potíží obnoví heslo k účtu místního správce, který zadáte, když si nejste jisti nebo jste zapomněli pověření.  Po přihlášení k virtuálnímu počítače byste měli resetovat heslo pro tohoto uživatele.
   
    Vyberte virtuální počítač na webu Azure Portal. Posuňte se dolů v podokně nastavení do části **Podpora + Poradce při potížích** v dolní části seznamu. Klepněte na tlačítko **Obnovit heslo.** Zadejte své uživatelské jméno a nové heslo. Nakonec klikněte na tlačítko **Uložit:**
   
    ![Obnovení přihlašovacích údajů uživatele na webu Azure Portal](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Restartujte virtuální počítač**. Tento krok řešení potíží můžete opravit všechny základní problémy virtuálního mísa sama má.
   
    Vyberte virtuální počítač na webu Azure Portal a klikněte na kartu **Přehled.** Klikněte na tlačítko **Restartovat:**
   
    ![Restartování virtuálního počítače na webu Azure Portal](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Ujistěte se, že jakákoli místní brána firewall nebo brána firewall v počítači umožňuje odchozí přenos protokolu TCP 3389 do Azure.

Pokud stále dochází k problémům s rdp, můžete [otevřít žádost o podporu](https://azure.microsoft.com/support/options/) nebo si přečíst podrobnější [koncepty řešení potíží s rdp a kroky](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-specific-rdp-errors"></a>Řešení konkrétních chyb protokolu RDP
Při pokusu o připojení k virtuálnímu počítači prostřednictvím rdp se může zobrazit určitá chybová zpráva. Následují nejběžnější chybové zprávy:

* [Vzdálená relace byla odpojena, protože nejsou k dispozici žádné licenční servery vzdálené plochy, které by poskytovaly licenci](troubleshoot-specific-rdp-errors.md#rdplicense).
* [Vzdálená plocha nemůže najít "název" počítače](troubleshoot-specific-rdp-errors.md#rdpname).
* [Došlo k chybě ověřování. Místní bezpečnostní úřad nelze kontaktovat](troubleshoot-specific-rdp-errors.md#rdpauth).
* [Chyba zabezpečení systému Windows: Pověření nefungovala](troubleshoot-specific-rdp-errors.md#wincred).
* [Tento počítač se nemůže připojit ke vzdálenému počítači](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>Další zdroje
Pokud nedošlo k žádné z těchto chyb a stále se nemůžete připojit k virtuálnímu počítači prostřednictvím vzdálené plochy, přečtěte si podrobný [průvodce odstraňováním potíží pro vzdálenou plochu](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Postup řešení potíží při přístupu k aplikacím spuštěným na virtuálním počítači najdete [v tématu Poradce při potížích s přístupem k aplikaci spuštěné na virtuálním počítači Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Pokud máte problémy s používáním zabezpečeného prostředí (SSH) pro připojení k virtuálnímu počítači SZvu v Azure, [přečtěte si článek Řešení potíží s připojením SSH k virtuálnímu počítači s Linuxem v Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


