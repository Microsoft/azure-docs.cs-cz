---
title: Nejde se připojit pomocí protokolu RDP k virtuálnímu počítači s Windows v Azure | Microsoft Docs
description: Řešení potíží, když se nemůžete připojit k virtuálnímu počítači s Windows v Azure pomocí vzdálené plochy
keywords: Chyba vzdálené plochy, Chyba připojení ke vzdálené ploše, nelze se připojit k virtuálnímu počítači, odstraňování potíží vzdálené plochy
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
ms.openlocfilehash: 66e4ea622f546981d1f0651b20368590e59056d3
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201616"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Řešení potíží s připojením ke vzdálené ploše virtuálního počítače Azure
Připojení RDP (Remote Desktop Protocol) k vašemu virtuálnímu počítači Azure s Windows může z různých důvodů selhat a vy tak k němu můžete ztratit přístup. Problém může být ve službě Vzdálená plocha na virtuálním počítači, v síťovém připojením nebo v klientovi vzdálené plochy na hostitelském počítači. Tento článek vás provede několika nejběžnějšími metodami řešení potíží s připojením RDP. 

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.

 

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Rychlé kroky pro řešení potíží
Po každém kroku řešení potíží se pokuste znovu připojit k virtuálnímu počítači:

1. Resetovat konfiguraci vzdálené plochy.
2. Zkontroluje pravidla skupiny zabezpečení sítě/Cloud Services koncové body.
3. Zkontrolujte protokoly konzoly virtuálních počítačů.
4. Resetujte síťovou kartu virtuálního počítače.
5. Podívejte se na Resource Health virtuálního počítače.
6. Resetujte heslo virtuálního počítače.
7. Restartujte virtuální počítač.
8. Znovu nasaďte virtuální počítač.

Pokud potřebujete podrobnější kroky a vysvětlení, pokračujte ve čtení. Ověřte, že místní síťové zařízení, jako jsou směrovače a brány firewall, neblokuje odchozí port TCP 3389, jak je uvedeno v [podrobných scénářích řešení potíží s](detailed-troubleshoot-rdp.md)protokolem RDP.

> [!TIP]
> Pokud je tlačítko **připojit** pro váš virtuální počítač na portálu šedé a nejste připojeni k Azure prostřednictvím [expresního postupu](../../expressroute/expressroute-introduction.md) nebo připojení [VPN typu Site-to-site](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) , musíte před použitím protokolu RDP vytvořit a přiřadit virtuální počítač k veřejné IP adrese. Podle potřeby si můžete přečíst další informace o [veřejných IP adresách v Azure](../../virtual-network/public-ip-addresses.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Způsoby řešení potíží s protokolem RDP
Pomocí jedné z následujících metod můžete řešit problémy s virtuálními počítači vytvořenými pomocí modelu nasazení Správce prostředků:

* Azure Portal – Skvělé, pokud potřebujete rychle Resetovat konfiguraci RDP nebo přihlašovací údaje uživatele a nemáte nainstalované nástroje Azure Tools.
* Azure PowerShell – Pokud jste obeznámeni s příkazovým řádkem PowerShellu, můžete rychle obnovit konfiguraci RDP nebo přihlašovací údaje uživatele pomocí rutin Azure PowerShell.

Můžete také vyhledat postup řešení potíží s virtuálními počítači vytvořenými pomocí [modelu nasazení Classic](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Řešení potíží pomocí Azure Portal
Po každém kroku řešení potíží zkuste znovu připojit k vašemu VIRTUÁLNÍmu počítači. Pokud se stále nemůžete připojit, zkuste další krok.

1. **Resetujte připojení RDP**. Tento krok řešení potíží resetuje konfiguraci RDP, když jsou zakázaná vzdálená připojení nebo blokuje protokol RDP, například pravidla brány Windows Firewall.
   
    V Azure Portal vyberte svůj virtuální počítač. Posuňte se dolů v podokně nastavení do části **Podpora a řešení potíží** v blízkosti dolního okraje seznamu. Klikněte na tlačítko **resetovat heslo** . Nastavte **režim** pouze pro **resetování konfigurace** a potom klikněte na tlačítko **aktualizovat** :
   
    ![Resetujte konfiguraci R D P v Azure Portal.](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Ověřte pravidla skupiny zabezpečení sítě**. Pomocí [ověření toku protokolu IP](../../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) můžete potvrdit, jestli pravidlo ve skupině zabezpečení sítě blokuje provoz do nebo z virtuálního počítače. Můžete také zkontrolovat účinná pravidla skupiny zabezpečení, abyste měli jistotu, že příchozí pravidlo "Povolit" NSG existuje a že má nastavenou prioritu pro port RDP (standardně 3389). Další informace najdete v tématu [použití platných pravidel zabezpečení k řešení potíží s tokem provozu virtuálních počítačů](../../virtual-network/diagnose-network-traffic-filter-problem.md).

3. **Zkontrolujte diagnostiku spouštění virtuálního počítače**. Tento krok řešení potíží zkontroluje protokoly konzoly virtuálních počítačů a určí, jestli tento virtuální počítač hlásí problém. Ne všechny virtuální počítače mají zapnutou diagnostiku spouštění, takže tento krok odstraňování potíží může být nepovinný.
   
    Konkrétní kroky pro řešení potíží jsou nad rámec tohoto článku, ale mohou poukazovat na širší problém, který má vliv na připojení RDP. Další informace o kontrole protokolů konzoly a snímku obrazovky virtuálního počítače najdete v tématu [Diagnostika spouštění pro virtuální počítače](boot-diagnostics.md).

4. **Resetujte síťovou kartu virtuálního počítače**. Další informace najdete v tématu [Postup resetování síťové karty pro virtuální počítače Azure s Windows](./reset-network-interface.md).
5. **Podívejte se na Resource Health virtuálního počítače**. Tento krok pro odstraňování potíží ověřuje, že neexistují žádné známé problémy s platformou Azure, které by mohly mít vliv na připojení k virtuálnímu počítači.
   
    V Azure Portal vyberte svůj virtuální počítač. Posuňte se dolů v podokně nastavení do části **Podpora a řešení potíží** v blízkosti dolního okraje seznamu. Klikněte na tlačítko **stav prostředku** . **K dispozici** jsou sestavy virtuálních počítačů, které jsou v pořádku:
   
    ![Projděte si stav prostředků v M Azure Portal.](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Resetovat přihlašovací údaje uživatele** Tento krok řešení potíží resetuje heslo na účtu místního správce, pokud si nejste jistí nebo jste zapomněli přihlašovací údaje.  Po přihlášení k virtuálnímu počítači byste měli resetovat heslo pro tohoto uživatele.
   
    V Azure Portal vyberte svůj virtuální počítač. Posuňte se dolů v podokně nastavení do části **Podpora a řešení potíží** v blízkosti dolního okraje seznamu. Klikněte na tlačítko **resetovat heslo** . Ujistěte se, že je **režim** nastavený na **resetování hesla** , a pak zadejte uživatelské jméno a nové heslo. Nakonec klikněte na tlačítko **aktualizovat** :
   
    ![Resetujte přihlašovací údaje uživatele v Azure Portal.](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Restartujte virtuální počítač**. Tento krok řešení potíží může opravit všechny základní problémy, které vlastní virtuální počítač.
   
    Vyberte svůj virtuální počítač v Azure Portal a klikněte na kartu **Přehled** . Klikněte na tlačítko **restartovat** :
   
    ![Restartujte v M Azure Portal.](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Znovu nasaďte virtuální** počítač. Tento krok řešení potíží znovu nasadí váš virtuální počítač na jiného hostitele v rámci Azure, aby se opravily jakékoli základní platformy nebo problémy se sítí.
   
    V Azure Portal vyberte svůj virtuální počítač. Posuňte se dolů v podokně nastavení do části **Podpora a řešení potíží** v blízkosti dolního okraje seznamu. Klikněte na tlačítko **znovu nasadit** a potom klikněte na tlačítko **znovu nasadit**:
   
    ![Znovu nasaďte virtuální počítač do Azure Portal](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    Po dokončení této operace dojde ke ztrátě dočasných dat na disku a aktualizuje se dynamická IP adresa přidružená k virtuálnímu počítači.

9. **Ověřte směrování**. Použijte možnost [dalšího směrování](../../network-watcher/diagnose-vm-network-routing-problem.md) Network Watcher k potvrzení, že trasa nebrání směrování provozu do nebo z virtuálního počítače. Můžete si také projít efektivní trasy a zobrazit všechny efektivní trasy pro síťové rozhraní. Další informace najdete v tématu [použití efektivních tras k řešení potíží s tokem provozu virtuálních počítačů](../../virtual-network/diagnose-network-routing-problem.md).

10. Zajistěte, aby všechny místní brány firewall nebo brána firewall v počítači umožňovaly odchozí přenos TCP 3389 do Azure.

Pokud stále dochází k problémům s protokolem RDP, můžete [otevřít žádost o podporu](https://azure.microsoft.com/support/options/) nebo si přečtěte [podrobnější koncepty a kroky pro řešení potíží s](detailed-troubleshoot-rdp.md)protokolem RDP.

## <a name="troubleshoot-using-azure-powershell"></a>Řešení potíží pomocí Azure PowerShellu
Pokud jste to ještě neudělali, [nainstalujte a nakonfigurujte nejnovější Azure PowerShell](/powershell/azure/).

Následující příklady používají proměnné, jako například `myResourceGroup` , `myVM` a `myVMAccessExtension` . Nahraďte názvy a umístění těchto proměnných vlastními hodnotami.

> [!NOTE]
> Pomocí rutiny [set-AzVMAccessExtension](/powershell/module/az.compute/set-azvmaccessextension) prostředí PowerShell resetujete přihlašovací údaje uživatele a konfiguraci RDP. V následujících příkladech `myVMAccessExtension` je název, který zadáte jako součást procesu. Pokud jste již dříve pracovali s VMAccessAgent, můžete získat název existujícího rozšíření pomocí nástroje `Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"` a ověřit vlastnosti virtuálního počítače. Chcete-li zobrazit název, podívejte se do části "přípony" ve výstupu.

Po každém kroku řešení potíží zkuste znovu připojit k vašemu VIRTUÁLNÍmu počítači. Pokud se stále nemůžete připojit, zkuste další krok.

1. **Resetujte připojení RDP**. Tento krok řešení potíží resetuje konfiguraci RDP, když jsou zakázaná vzdálená připojení nebo blokuje protokol RDP, například pravidla brány Windows Firewall.
   
    Následující příklad obnoví připojení RDP na virtuálním počítači s názvem `myVM` v `WestUS` umístění a ve skupině prostředků s názvem `myResourceGroup` :
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Ověřte pravidla skupiny zabezpečení sítě**. Tento krok řešení potíží ověří, že máte ve skupině zabezpečení sítě pravidlo, které povoluje provoz protokolu RDP. Výchozím portem pro protokol RDP je port TCP 3389. Při vytváření virtuálního počítače se nemusí automaticky vytvořit pravidlo pro povolení provozu RDP.
   
    Nejprve přiřaďte k proměnné všechna konfigurační data pro skupinu zabezpečení sítě `$rules` . Následující příklad získá informace o skupině zabezpečení sítě s názvem `myNetworkSecurityGroup` ve skupině prostředků s názvem `myResourceGroup` :
   
    ```powershell
    $rules = Get-AzNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Nyní zobrazte pravidla konfigurovaná pro tuto skupinu zabezpečení sítě. Ověřte, že existuje pravidlo umožňující povolit port TCP 3389 pro příchozí připojení následujícím způsobem:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    Následující příklad ukazuje platné pravidlo zabezpečení, které povoluje provoz protokolu RDP. Můžete zobrazit `Protocol` ,, `DestinationPortRange` `Access` a `Direction` správně nakonfigurovat:
   
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
   
    Pokud nemáte pravidlo, které povoluje provoz protokolu RDP, [Vytvořte pravidlo skupiny zabezpečení sítě](../windows/nsg-quickstart-powershell.md). Povolte port TCP 3389.
3. **Resetovat přihlašovací údaje uživatele** Tento krok řešení potíží resetuje heslo na účtu místního správce, který zadáte, pokud si nejste jistí nebo jste zapomněli přihlašovací údaje.
   
    Nejdřív zadejte uživatelské jméno a nové heslo přiřazením přihlašovacích údajů k `$cred` proměnné následujícím způsobem:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Teď aktualizujte přihlašovací údaje na svém VIRTUÁLNÍm počítači. V následujícím příkladu se aktualizují přihlašovací údaje na virtuálním počítači s názvem `myVM` v `WestUS` umístění a ve skupině prostředků s názvem `myResourceGroup` :
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Restartujte virtuální počítač**. Tento krok řešení potíží může opravit všechny základní problémy, které vlastní virtuální počítač.
   
    Následující příklad restartuje virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup` :
   
    ```powershell
    Restart-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Znovu nasaďte virtuální** počítač. Tento krok řešení potíží znovu nasadí váš virtuální počítač na jiného hostitele v rámci Azure, aby se opravily jakékoli základní platformy nebo problémy se sítí.
   
    V následujícím příkladu se znovu nasadí virtuální počítač s názvem `myVM` v `WestUS` umístění a ve skupině prostředků s názvem `myResourceGroup` :
   
    ```powershell
    Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Ověřte směrování**. Použijte možnost [dalšího směrování](../../network-watcher/diagnose-vm-network-routing-problem.md) Network Watcher k potvrzení, že trasa nebrání směrování provozu do nebo z virtuálního počítače. Můžete si také projít efektivní trasy a zobrazit všechny efektivní trasy pro síťové rozhraní. Další informace najdete v tématu [použití efektivních tras k řešení potíží s tokem provozu virtuálních počítačů](../../virtual-network/diagnose-network-routing-problem.md).

7. Zajistěte, aby všechny místní brány firewall nebo brána firewall v počítači umožňovaly odchozí přenos TCP 3389 do Azure.

Pokud stále dochází k problémům s protokolem RDP, můžete [otevřít žádost o podporu](https://azure.microsoft.com/support/options/) nebo si přečtěte [podrobnější koncepty a kroky pro řešení potíží s](detailed-troubleshoot-rdp.md)protokolem RDP.

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Řešení potíží s virtuálními počítači vytvořenými pomocí modelu nasazení Classic

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Po každém kroku řešení potíží se pokuste znovu připojit k virtuálnímu počítači.

1. **Resetujte připojení RDP**. Tento krok řešení potíží resetuje konfiguraci RDP, když jsou zakázaná vzdálená připojení nebo blokuje protokol RDP, například pravidla brány Windows Firewall.
   
    V Azure Portal vyberte svůj virtuální počítač. Klikněte na **... Další** tlačítko a potom klikněte na **resetovat vzdálený přístup**:
   
    ![Resetujte konfiguraci R D P v Azure Portal pro V M pomocí modelu nasazení Classic.](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Ověřte Cloud Services koncové body**. Tento krok řešení potíží ověří, zda máte v Cloud Services koncové body, aby bylo možné provoz protokolu RDP povolit. Výchozím portem pro protokol RDP je port TCP 3389. Při vytváření virtuálního počítače se nemusí automaticky vytvořit pravidlo pro povolení provozu RDP.
   
   V Azure Portal vyberte svůj virtuální počítač. Kliknutím na tlačítko **koncové body** zobrazíte aktuálně nakonfigurované koncové body pro váš virtuální počítač. Ověřte, zda existují koncové body, které povolují provoz protokolu RDP na portu TCP 3389.
   
   Následující příklad ukazuje platné koncové body, které povolují přenosy protokolu RDP:
   
   ![Ověří Cloud Services koncových bodů v Azure Portal pro V M pomocí modelu nasazení Classic.](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Pokud nemáte koncový bod, který umožňuje provoz protokolu RDP, [vytvořte Cloud Services koncový bod](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints). Povolte protokol TCP na privátní port 3389.
3. **Zkontrolujte diagnostiku spouštění virtuálního počítače**. Tento krok řešení potíží zkontroluje protokoly konzoly virtuálních počítačů a určí, jestli tento virtuální počítač hlásí problém. Ne všechny virtuální počítače mají zapnutou diagnostiku spouštění, takže tento krok odstraňování potíží může být nepovinný.
   
    Konkrétní kroky pro řešení potíží jsou nad rámec tohoto článku, ale mohou poukazovat na širší problém, který má vliv na připojení RDP. Další informace o kontrole protokolů konzoly a snímku obrazovky virtuálního počítače najdete v tématu [Diagnostika spouštění pro virtuální počítače](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **Podívejte se na Resource Health virtuálního počítače**. Tento krok pro odstraňování potíží ověřuje, že neexistují žádné známé problémy s platformou Azure, které by mohly mít vliv na připojení k virtuálnímu počítači.
   
    V Azure Portal vyberte svůj virtuální počítač. Posuňte se dolů v podokně nastavení do části **Podpora a řešení potíží** v blízkosti dolního okraje seznamu. Klikněte na tlačítko **Resource Health** . **K dispozici** jsou sestavy virtuálních počítačů, které jsou v pořádku:
   
    ![Projděte si stav prostředků v M Azure Portal pro v M pomocí modelu nasazení Classic.](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Resetovat přihlašovací údaje uživatele** Tento krok řešení potíží resetuje heslo na účtu místního správce, který zadáte, pokud si nejste jistí nebo jste zapomněli přihlašovací údaje.  Po přihlášení k virtuálnímu počítači byste měli resetovat heslo pro tohoto uživatele.
   
    V Azure Portal vyberte svůj virtuální počítač. Posuňte se dolů v podokně nastavení do části **Podpora a řešení potíží** v blízkosti dolního okraje seznamu. Klikněte na tlačítko **resetovat heslo** . Zadejte své uživatelské jméno a nové heslo. Nakonec klikněte na tlačítko **Uložit** :
   
    ![Resetujte přihlašovací údaje uživatele v Azure Portal pro V M pomocí modelu nasazení Classic.](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Restartujte virtuální počítač**. Tento krok řešení potíží může opravit všechny základní problémy, které vlastní virtuální počítač.
   
    Vyberte svůj virtuální počítač v Azure Portal a klikněte na kartu **Přehled** . Klikněte na tlačítko **restartovat** :
   
    ![Restartujte v M v Azure Portal pro V M pomocí modelu nasazení Classic.](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Zajistěte, aby všechny místní brány firewall nebo brána firewall v počítači umožňovaly odchozí přenos TCP 3389 do Azure.

Pokud stále dochází k problémům s protokolem RDP, můžete [otevřít žádost o podporu](https://azure.microsoft.com/support/options/) nebo si přečtěte [podrobnější koncepty a kroky pro řešení potíží s](detailed-troubleshoot-rdp.md)protokolem RDP.

## <a name="troubleshoot-specific-rdp-errors"></a>Řešení konkrétních chyb protokolu RDP
Při pokusu o připojení k VIRTUÁLNÍmu počítači přes RDP se může objevit konkrétní chybová zpráva. Níže jsou uvedené nejběžnější chybové zprávy:

* [Vzdálená relace byla odpojena, protože nejsou k dispozici žádné licenční servery vzdálené plochy pro poskytnutí licence](troubleshoot-specific-rdp-errors.md#rdplicense).
* [Vzdálená plocha nemůže najít počítač "název"](troubleshoot-specific-rdp-errors.md#rdpname).
* [Došlo k chybě ověřování. Místní autoritu zabezpečení nelze kontaktovat](troubleshoot-specific-rdp-errors.md#rdpauth).
* [Chyba zabezpečení systému Windows: vaše přihlašovací údaje nefungovaly](troubleshoot-specific-rdp-errors.md#wincred).
* [Tento počítač se nemůže připojit ke vzdálenému počítači](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>Další zdroje
Pokud nedošlo k žádné z těchto chyb a stále se nemůžete připojit k virtuálnímu počítači přes vzdálenou plochu, přečtěte si podrobný [Průvodce odstraňováním potíží pro vzdálenou plochu](detailed-troubleshoot-rdp.md).
* Postup řešení potíží při přístupu k aplikacím běžícím na virtuálním počítači najdete v tématu [řešení potíží s přístupem k aplikaci spuštěné na virtuálním počítači Azure](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json).
* Pokud máte problémy s použitím Secure Shell (SSH) pro připojení k virtuálnímu počítači se systémem Linux v Azure, přečtěte si téma [řešení potíží s připojením SSH k virtuálnímu počítači se systémem Linux v Azure](./troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json).
