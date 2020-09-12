---
title: Přidání sad Runbook Azure Automation do plánů obnovení Site Recovery
description: Naučte se, jak pomocí Azure Site Recovery rozšíříte plány obnovení pomocí Azure Automation pro zotavení po havárii.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: ramamill
ms.openlocfilehash: a141280338632fdad7053cbbe76c8bdf2797443d
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89424867"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Přidání runbooků Azure Automation do plánů obnovení

Tento článek popisuje, jak integrovat Azure Automation Runbooky pro rozšiřování plánů obnovení [Azure Site Recovery](site-recovery-overview.md) . Ukážeme vám, jak automatizovat základní úlohy, které by jinak vyžadovaly ruční zásah, a jak převést obnovení z více kroků na akci jedním kliknutím.

## <a name="recovery-plans"></a>Plány obnovení 

Můžete použít plány obnovení při převzetí služeb při selhání v místních počítačích nebo virtuálních počítačích Azure. Plány obnovení vám pomůžou definovat proces systematického obnovení, který definuje, jak se převezmou počítače, a jak se budou spouštět a obnovovat po převzetí služeb při selhání. 

Rozsáhlé aplikace pro obnovení můžou být složité. Plány obnovení vám pomůžou nařídit objednávku, aby bylo obnovení konzistentně přesné, možné a automatizované. Úkoly v rámci plánu obnovení můžete automatizovat pomocí skriptů i Azure Automation sad Runbook. Typické příklady můžou konfigurovat nastavení na virtuálním počítači Azure po převzetí služeb při selhání nebo znovu nakonfigurovat aplikaci, která běží na virtuálním počítači.

- [Přečtěte si další informace](recovery-plan-overview.md) o plánech obnovení.
- [Přečtěte si další informace](../automation/automation-runbook-types.md) o Azure Automation Runbooky.



## <a name="runbooks-in-recovery-plans"></a>Runbooky v plánech obnovení

Přidáte účet Azure Automation a runbooky do plánu obnovení. Sada Runbook se vyvolá při spuštění plánu obnovení.

- Účet Automation může být v libovolné oblasti Azure a musí být ve stejném předplatném jako trezor Site Recovery. 
- Sada Runbook se může spustit v plánu obnovení při převzetí služeb při selhání z primárního umístění do sekundárního nebo během navrácení služeb po obnovení ze sekundárního umístění na primární.
- Runbooky v plánu obnovení se v pořadí podle sady spouštějí sériově, jeden po druhém.
- Pokud Runbooky v plánu obnovení nakonfigurují virtuální počítače tak, aby se spouštěly v různých skupinách, bude plán obnovení pokračovat jenom v případě, že Azure hlásí všechny virtuální počítače jako spuštěné.
- Plány obnovení se nadále spouštějí i v případě, že se skript nezdařil.

### <a name="recovery-plan-context"></a>Kontext plánu obnovení

Když se skript spustí, vloží kontext plánu obnovení do Runbooku. Kontext obsahuje proměnné shrnuté v tabulce.

| **Název proměnné** | **Popis** |
| --- | --- |
| RecoveryPlanName |Název plánu obnovení Používá se v akcích založených na názvu. |
| FailoverType |Určuje, jestli jde o testovací nebo produkční převzetí služeb při selhání. 
| FailoverDirection | Určuje, zda obnovení probíhá do primárního nebo sekundárního umístění. |
| GroupID |Určuje číslo skupiny v plánu obnovení, když je plán spuštěný. |
| VmMap |Pole všech virtuálních počítačů ve skupině. |
| VMMap klíč |Jedinečný klíč (GUID) pro každý virtuální počítač. |
| SubscriptionId |ID předplatného Azure, ve kterém se vytvořil virtuální počítač |
| ResourceGroupName | Název skupiny prostředků, ve které se virtuální počítač nachází.
| CloudServiceName |Název cloudové služby Azure, pod kterou se vytvořil virtuální počítač. |
| RoleName (Název role) |Název virtuálního počítače Azure. |
| RecoveryPointId|Časové razítko pro obnovení virtuálního počítače. |

>[!Note]
>Hodnota proměnné ' FailoverDirection ' bude v případě převzetí služeb při selhání a ' SecondaryToPrimary ' v případě navrácení služeb po obnovení ' PrimaryToSecondary '.

Následující příklad ukazuje kontextovou proměnnou:

```
{"RecoveryPlanName":"hrweb-recovery",
"FailoverType":"Test",
"FailoverDirection":"PrimaryToSecondary",
"GroupId":"1",
"VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":
    { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",
    "ResourceGroupName":"ContosoRG",
    "CloudServiceName":"pod02hrweb-Chicago-test",
    "RoleName":"Fabrikam-Hrweb-frontend-test",
    "RecoveryPointId":"TimeStamp"}
    }
}
```

Pokud chcete mít přístup ke všem virtuálním počítačům v VMMap ve smyčce, můžete použít následující kód:

```
$VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
$vmMap = $RecoveryPlanContext.VmMap
    foreach($VMID in $VMinfo)
    {
        $VM = $vmMap.$VMID                
            if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
            #this check is to ensure that we skip when some data is not available else it will fail
    Write-output "Resource group name ", $VM.ResourceGroupName
    Write-output "Rolename " = $VM.RoleName
            }
        }
```


Blog Aman Sharma v rámci [sběru cloudů](http://harvestingclouds.com) má užitečný příklad [kontextu skriptu plánu obnovení](http://harvestingclouds.com/post/script-sample-azure-automation-runbook-for-asr-recovery-plan/).



## <a name="before-you-start"></a>Než začnete

- Pokud s Azure Automation začínáte, můžete si [zaregistrovat](https://azure.microsoft.com/services/automation/) a [stáhnout ukázkové skripty](https://azure.microsoft.com/documentation/scripts/).
- Ujistěte se, že účet Automation obsahuje následující moduly:
    - AzureRM. Profile
    - AzureRM.Resources
    - AzureRM.Automation
    - AzureRM.Network
    - AzureRM.Compute

    Všechny moduly by měly mít kompatibilní verze. Nejjednodušším způsobem je vždy používat nejnovější verze všech modulů.



## <a name="customize-the-recovery-plan"></a>Přizpůsobení plánu obnovení

1. V trezoru vyberte **plány obnovení (Site Recovery)** .
2. Pokud chcete vytvořit plán obnovení, klikněte na **+ plán obnovení**. [Přečtěte si další informace](site-recovery-create-recovery-plans.md). Pokud již máte plán obnovení, vyberte jej a otevřete ho.
3. Na stránce plán obnovení klikněte na **přizpůsobit**.

    ![Klikněte na tlačítko přizpůsobit.](media/site-recovery-runbook-automation-new/custom-rp.png)

2. Klikněte na tlačítko se třemi tečkami (...) vedle **skupiny 1: zahájit**  >  **akci přidat příspěvek**.
3. V části **Vložit akci**ověřte, že je vybraný **skript** , a zadejte název skriptu (**Hello World**).
4. Zadejte účet Automation a vyberte Runbook. Chcete-li skript uložit, klikněte na tlačítko **OK**. Skript se přidá do **skupiny 1: kroky po**.


## <a name="reuse-a-runbook-script"></a>Opakované použití skriptu Runbooku

Můžete použít jeden skript Runbooku v několika plánech obnovení, a to pomocí externích proměnných. 

- [Proměnné Azure Automation](../automation/shared-resources/variables.md) slouží k uložení parametrů pro spuštění plánu obnovení.
- Přidáním názvu plánu obnovení jako předpony proměnné můžete pro každý plán obnovení vytvořit jednotlivé proměnné. Pak použijte proměnné jako parametry.
- Můžete změnit parametr bez změny skriptu, ale stále měnit způsob, jakým funguje skript.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Použití jednoduché řetězcové proměnné ve skriptu Runbooku

V tomto příkladu skript převezme vstup do skupiny zabezpečení sítě (NSG) a použije ho pro virtuální počítače v plánu obnovení. 

1. Aby skript mohl zjistit, který plán obnovení běží, použijte tento kontext plánu obnovení:

    ```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
    ```

2. Poznamenejte si název NSG a skupinu prostředků. Tyto proměnné použijete jako vstupy pro skripty plánu obnovení. 
1. V prostředcích účtu Automation. Vytvořte proměnnou pro uložení názvu NSG. Přidejte předponu k názvu proměnné s názvem plánu obnovení.

    ![Vytvořit NSG proměnnou názvu](media/site-recovery-runbook-automation-new/var1.png)

2. Vytvořte proměnnou pro uložení názvu skupiny prostředků pro prostředek NSG. Přidejte předponu k názvu proměnné s názvem plánu obnovení.

    ![Vytvoření názvu skupiny prostředků NSG](media/site-recovery-runbook-automation-new/var2.png)


3.  Ve skriptu použijte tento referenční kód k získání hodnot proměnné:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Použijte proměnné v sadě Runbook pro použití NSG pro síťové rozhraní virtuálního počítače s podporou převzetí služeb při selhání:

    ```
    InlineScript {
    if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
    ```


Pro každý plán obnovení vytvořte nezávislé proměnné, abyste mohli znovu použít skript. Přidejte předponu pomocí názvu plánu obnovení. 

Úplný a koncový skript pro tento scénář najdete v [tomto skriptu](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>Pro ukládání dalších informací použijte komplexní proměnnou.

V některých případech možná nebudete moci vytvořit samostatné proměnné pro každý plán obnovení. Vezměte v úvahu scénář, ve kterém chcete, aby jeden skript přiřadil veřejnou IP adresu na konkrétní virtuální počítače. V jiném scénáři možná budete chtít použít různé skupin zabezpečení sítě na různých virtuálních počítačích (ne na všech virtuálních počítačích). Poznámky:

- Můžete vytvořit skript, který bude opakovaně použitelný pro libovolný plán obnovení.
- Každý plán obnovení může mít proměnný počet virtuálních počítačů.
- Například obnovení služby SharePoint má dva front-endy. Základní obchodní aplikace (LOB) má pouze jeden front-end.
- V tomto scénáři nemůžete pro každý plán obnovení vytvořit samostatné proměnné.

V následujícím příkladu vytvoříme [komplexní proměnnou](/powershell/module/servicemanagement/azure.service/set-azureautomationvariable) v účtu Azure Automation.

Provedeme to zadáním více hodnot pomocí Azure PowerShell.

1. V PowerShellu se přihlaste ke svému předplatnému Azure:

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Chcete-li uložit parametry, vytvořte komplexní proměnnou pomocí názvu plánu obnovení:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. V této komplexní proměnné je **VMDETAILS** ID virtuálního počítače pro chráněný virtuální počítač. ID virtuálního počítače získáte tak, že v Azure Portal zobrazíte vlastnosti virtuálního počítače. Následující snímek obrazovky ukazuje proměnnou, která ukládá podrobnosti dvou virtuálních počítačů:

    ![Použít ID virtuálního počítače jako identifikátor GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. Tuto proměnnou použijte v sadě Runbook. Pokud se uvedený identifikátor GUID virtuálního počítače nachází v kontextu plánu obnovení, použijte NSG na virtuálním počítači:

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. V sadě Runbook smyčkou prostřednictvím virtuálních počítačů kontextu plánu obnovení. Ověřte, jestli virtuální počítač existuje v **$VMDetailsObj**. Pokud existuje, přístup k vlastnostem proměnné pro použití NSG:

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            $VMDetails = $VMDetailsObj[$VMID].ToObject([hashtable]);
            Write-output $VMDetails
            if ($VMDetails -ne $Null) { #If the VM exists in the context, this will not be Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetails.NSGName
                $NSGRGname = $VMDetails.NSGResourceGroupName

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

Stejný skript můžete použít pro různé plány obnovení. Zadejte jiné parametry uložením hodnoty, která odpovídá plánu obnovení v různých proměnných.

## <a name="sample-scripts"></a>Ukázkové skripty

Pokud chcete nasadit ukázkové skripty do svého účtu Automation, klikněte na tlačítko **nasadit do Azure** .

[![Nasazení do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Toto video poskytuje další příklad. Ukazuje, jak obnovit dvě vícevrstvé aplikace WordPress do Azure:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="next-steps"></a>Další kroky

- Další informace o [Azure Automation účtu Spustit jako](../automation/manage-runas-account.md)
- Zkontrolujte [Azure Automation ukázkové skripty](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=User&f%5B0%5D.Value=SC%20Automation%20Product%20Team&f%5B0%5D.Text=SC%20Automation%20Product%20Team).
- [Přečtěte si další informace](site-recovery-failover.md) o spuštění převzetí služeb při selhání.
