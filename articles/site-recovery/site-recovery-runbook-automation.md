---
title: Přidání runbooků Azure Automation do plánů obnovení webu
description: Zjistěte, jak rozšířit plány obnovení pomocí Azure Automation pro zotavení po havárii pomocí Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: rajanaki
ms.openlocfilehash: ecfe993a137ca63c84438870ec54ac1e6d6707da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257482"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Přidání runbooků Azure Automation do plánů obnovení

Tento článek popisuje, jak integrovat runbooky Azure Automation k rozšíření plánů [obnovení webu Azure.](site-recovery-overview.md) Ukážeme vám, jak automatizovat základní úkoly, které by jinak potřebovaly ruční zásah, a jak převést vícestupňové obnovení na akci s jedním kliknutím.

## <a name="recovery-plans"></a>Plány obnovení 

Plány obnovení můžete použít při převzetí služeb při selhání místní počítače nebo virtuální počítače Azure. Plány obnovení vám pomohou definovat systematický proces obnovení, který definuje způsob převzetí služeb při selhání počítačů a způsob jejich spuštění a obnovení po převzetí služeb při selhání. 

Obnovení velké aplikace může být složité. Plány obnovení pomáhají zavést pořadí tak, aby obnovení bylo konzistentně přesné, opakovatelné a automatizované. Můžete automatizovat úlohy v rámci plánu obnovení pomocí skriptů, stejně jako Azure Automation runbooky. Typickými příklady může být konfigurace nastavení na virtuálním počítači Azure po převzetí služeb při selhání nebo změna konfigurace aplikace, která běží na virtuálním počítači.

- [Přečtěte si další informace](recovery-plan-overview.md) o plánech obnovení.
- [Přečtěte si další informace](../automation/automation-runbook-types.md) o runbookech Azure Automation.



## <a name="runbooks-in-recovery-plans"></a>Sady Runbook v plánech obnovení

Do plánu obnovení přidáte účet Azure Automation a runbooky. Runbook je vyvolána při spuštění plánu obnovení.

- Účet automatizace může být v libovolné oblasti Azure a musí být ve stejném předplatném jako trezor obnovení webu. 
- Runbook lze spustit v plánu obnovení během převzetí služeb při selhání z primárního umístění na sekundární nebo při navrácení služeb po obnovení ze sekundárního umístění do primárního.
- Sady Runbook v plánu obnovení běží sériově, jeden po druhém, v nastaveném pořadí.
- Pokud sady Runbook v plánu obnovení nakonfigurují virtuální počítače tak, aby se spouštěla v různých skupinách, plán obnovení bude pokračovat pouze v případě, že Azure hlásí všechny virtuální počítače jako spuštěné.
- Plány obnovení budou nadále spuštěny, i když se skript nezdaří.

### <a name="recovery-plan-context"></a>Kontext plánu obnovení

Při spuštění skriptu vloží kontext plánu obnovení do runbooku. Kontext obsahuje proměnné shrnuté v tabulce.

| **Název proměnné** | **Popis** |
| --- | --- |
| Název_recoveryplan |Název plánu obnovení. Používá se v akcích založených na názvu. |
| Typ převzetí služeb při selhání |Určuje, zda se jedná o test nebo převzetí služeb při selhání výroby. 
| Přesměrování převzetí služeb při selhání | Určuje, zda je obnovení do primárního nebo sekundárního umístění. |
| Groupid |Identifikuje číslo skupiny v plánu obnovení, když je plán spuštěn. |
| Vmmap |Pole všech virtuálních zařízení ve skupině. |
| Klíč VMMap |Jedinečný klíč (GUID) pro každý virtuální virtuální mě. |
| SubscriptionId |ID předplatného Azure, ve kterém byl vytvořen virtuální počítač. |
| ResourceGroupName | Název skupiny prostředků, ve kterém je virtuální město umístěno.
| CloudServiceName |Název cloudové služby Azure, pod kterým byl virtuální počítač vytvořen. |
| RoleName (Název role) |Název virtuálního počítače Azure. |
| RecoveryPointId|Časové razítko pro obnovení virtuálního soudu. |

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

Pokud chcete získat přístup ke všem virtuálním mům ve smyčce VMMap, můžete použít následující kód:

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


Aman Sharma blog po dobu [Sklizeň Mraky](http://harvestingclouds.com) má užitečný příklad [plánu obnovy kontextu skriptu](http://harvestingclouds.com/post/script-sample-azure-automation-runbook-for-asr-recovery-plan/).



## <a name="before-you-start"></a>Než začnete

- Pokud s Azure Automation tečujete, můžete [se zaregistrovat](https://azure.microsoft.com/services/automation/) a [stáhnout ukázkové skripty](https://azure.microsoft.com/documentation/scripts/).
- Ujistěte se, že účet automatizace má následující moduly:
    - AzureRM.profil
    - AzureRM.Resources
    - AzureRM.Automation
    - AzureRM.Network
    - AzureRM.Compute

    Všechny moduly by měly být kompatibilní verze. Nejjednodušší způsob je vždy používat nejnovější verze všech modulů.



## <a name="customize-the-recovery-plan"></a>Přizpůsobení plánu obnovení

1. V trezoru vyberte **Plány obnovení (Obnovení webu)**
2. Chcete-li vytvořit plán obnovení, klepněte na tlačítko **+Plán obnovení**. [Další informace](site-recovery-create-recovery-plans.md). Pokud již plán obnovení máte, vyberte jej, chcete-li jej otevřít.
3. Na stránce plánu obnovení klepněte na **tlačítko Přizpůsobit**.

    ![Klikněte na tlačítko Přizpůsobit.](media/site-recovery-runbook-automation-new/custom-rp.png)

2. Klikněte na tři tečky (...) vedle **skupiny 1: Spustit** > **akci Přidat příspěvek**.
3. V **akci Vložit**ověřte, zda je vybrána možnost **Skript,** a zadejte název skriptu **(Hello World**).
4. Zadejte účet automatizace a vyberte runbook. Chcete-li skript uložit, klepněte na tlačítko **OK**. Skript je přidán do **skupiny 1: Post-steps**.


## <a name="reuse-a-runbook-script"></a>Opětovné použití skriptu runbooku

Můžete použít jeden skript runbooku ve více plánech obnovení pomocí externích proměnných. 

- Proměnné [Azure Automation](../automation/automation-variables.md) se používají k ukládání parametrů pro spuštění plánu obnovení.
- Přidáním názvu plánu obnovení jako předpony proměnné můžete vytvořit jednotlivé proměnné pro každý plán obnovení. Potom použijte proměnné jako parametry.
- Parametr můžete změnit beze změny skriptu, ale přesto změnit způsob, jakým skript funguje.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Použití jednoduché proměnné řetězce ve skriptu runbooku

V tomto příkladu skript přebírá vstup skupiny zabezpečení sítě (NSG) a použije jej na virtuální chody v plánu obnovení. 

1. Aby skript mohl zjistit, který plán obnovení je spuštěn, použijte tento kontext plánu obnovení:

    ```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
    ```

2. Poznamenejte si název skupiny nsg a skupinu prostředků. Tyto proměnné se používají jako vstupy pro skripty plánu obnovení. 
1. V majetku účtu automatizace. vytvořte proměnnou pro uložení názvu skupiny nsg. Přidejte předponu k názvu proměnné s názvem plánu obnovení.

    ![Vytvoření proměnné názvu skupiny nsg](media/site-recovery-runbook-automation-new/var1.png)

2. Vytvořte proměnnou pro uložení názvu skupiny prostředků pro prostředek skupiny nsg. Přidejte předponu k názvu proměnné s názvem plánu obnovení.

    ![Vytvoření názvu skupiny prostředků skupiny nsg](media/site-recovery-runbook-automation-new/var2.png)


3.  Ve skriptu použijte tento referenční kód k získání hodnot proměnných:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Pomocí proměnných v aplikaci Runbook použijte skupinu zabezpečení sítě pro síťové rozhraní virtuálního virtuálního zařízení s připojením k selhání:

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


Pro každý plán obnovení vytvořte nezávislé proměnné, abyste mohli skript znovu použít. Přidejte předponu pomocí názvu plánu obnovení. 

Úplný skript od konce k dokončení tohoto scénáře naleznete v [tomto skriptu](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>Použití komplexní proměnné k uložení dalších informací

V některých případech nemusí být možné vytvořit samostatné proměnné pro každý plán obnovení. Zvažte scénář, ve kterém chcete, aby jeden skript přiřadil veřejnou IP adresu na konkrétní virtuální počítače. V jiném scénáři můžete chtít použít různé nsgs na různých virtuálních počítačích (ne na všech virtuálních počítačích). Poznámky:

- Můžete vytvořit skript, který je opakovaně použitelný pro jakýkoli plán obnovení.
- Každý plán obnovení může mít proměnný počet virtuálních zařízení.
- Například obnovení služby SharePoint má dva front-endy. Základní obchodní aplikace (LOB) má pouze jeden front-end.
- V tomto scénáři nelze vytvořit samostatné proměnné pro každý plán obnovení.

V následujícím příkladu vytvoříme [komplexní proměnnou](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureautomationvariable) v účtu Azure Automation.

Děláme to zadáním více hodnot pomocí Azure PowerShellu.

1. V PowerShellu se přihlaste k předplatnému Azure:

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

3. V této komplexní proměnné **VMDetails** je ID virtuálního počítače pro chráněný virtuální počítač. Pokud chcete získat ID virtuálního počítače, na webu Azure Portal zobrazte vlastnosti virtuálního počítače. Následující snímek obrazovky ukazuje proměnnou, která ukládá podrobnosti o dvou virtuálních discích:

    ![Použití ID virtuálního měn jako identifikátorGUI](media/site-recovery-runbook-automation-new/vmguid.png)

4. Tuto proměnnou použijte v aplikaci Runbook. Pokud je v kontextu plánu obnovení nalezen uvedený identifikátor GUID virtuálního počítače, použijte na virtuálním počítači zdroj zabezpečení:

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. V runbooku projděte virtuální počítače v kontextu plánu obnovení. Zkontrolujte, jestli virtuální hod existuje v **$VMDetailsObj**. Pokud existuje, přístup k vlastnostem proměnné použít nsg:

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

Stejný skript můžete použít pro různé plány obnovení. Zadejte různé parametry uložením hodnoty, která odpovídá plánu obnovení v různých proměnných.

## <a name="sample-scripts"></a>Ukázkové skripty

Pokud chcete nasadit ukázkové skripty do svého účtu Automation, klikněte na tlačítko **Nasadit do Azure.**

[![Nasazení do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Toto video poskytuje další příklad. Ukazuje, jak obnovit dvouvrstvou aplikaci WordPress do Azure:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="next-steps"></a>Další kroky

- Informace o [účtu Azure Automation Run Run As](../automation/automation-create-runas-account.md)
- Prohlédněte si [ukázkové skripty Azure Automation](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=User&f%5B0%5D.Value=SC%20Automation%20Product%20Team&f%5B0%5D.Text=SC%20Automation%20Product%20Team).
- [Přečtěte si další informace](site-recovery-failover.md) o spuštění převzetí služeb při selhání.



