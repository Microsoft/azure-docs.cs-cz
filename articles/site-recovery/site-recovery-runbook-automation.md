---
title: "Přidat Azure Automation runbook do plánů obnovení v Azure Site Recovery | Microsoft Docs"
description: "Zjistěte, jak Azure Site Recovery můžete rozšířit plánů obnovení pomocí Azure Automation. Zjistěte, jak dokončit složité úlohy během obnovení do Azure."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: ecece14d-5f92-4596-bbaf-5204addb95c2
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/09/2018
ms.author: ruturajd@microsoft.com
ms.openlocfilehash: 4802215f903eb196afbf05637ad5e38dbbbc09a3
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Přidání sad Azure Automation runbook do plánů obnovení
V tomto článku jsme popisují, jak Azure Site Recovery integruje se službou Azure Automation můžete rozšířit plánu obnovení. Plány obnovení můžete orchestraci obnovení virtuálních počítačů, které jsou chráněné službou Site Recovery. Plány obnovení fungovat pro replikaci do sekundární cloudu i pro replikaci do Azure. Plány obnovení také pomoci zajistit, aby obnovení **přesné**, **repeatable**, a **automatizované**. Pokud jste převzetí služeb při selhání virtuálních počítačů do Azure, integraci s Azure Automation rozšiřuje plánu obnovení. Můžete ho spuštění sady runbook, které nabízí výkonné automatizace úloh.

Pokud začínáte Azure Automation, můžete [zaregistrovat](https://azure.microsoft.com/services/automation/) a [stažení ukázkové skripty](https://azure.microsoft.com/documentation/scripts/). Další informace a další informace o orchestraci obnovení do Azure pomocí [plány obnovení](./site-recovery-create-recovery-plans.md), najdete v části [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/).

V tomto článku jsme popisují, jak můžete integrovat Azure Automation runbook do plánu obnovení. Příklady použít k automatizaci základní úlohy, které dříve vyžadovaly ruční zásah. Můžeme také popisují, jak převést obnovení několika kroky akce obnovení jedním kliknutím.

## <a name="customize-the-recovery-plan"></a>Přizpůsobit plán obnovení
1. Přejděte na **Site Recovery** okna prostředků plánu obnovení. V tomto příkladu plán obnovení má dva virtuální počítače přidat do něj pro obnovení. Chcete-li začít přidávat sady runbook, klikněte na tlačítko **přizpůsobit** kartě.

    ![Klikněte na tlačítko Upravit](media/site-recovery-runbook-automation-new/essentials-rp.png)


2. Klikněte pravým tlačítkem na **1. skupina: spuštění**a potom vyberte **akce po přidání**.

    ![Klikněte pravým tlačítkem na skupinu 1: Spuštění a přidejte po akce](media/site-recovery-runbook-automation-new/customize-rp.png)

3. Klikněte na tlačítko **vyberte skript**.

4. Na **akce aktualizace** okně Název skriptu **Hello, World**.

    ![V okně Akce aktualizace](media/site-recovery-runbook-automation-new/update-rp.png)

5. Zadejte název účtu Automation.
    >[!NOTE]
    > Účet Automation může být v libovolné oblasti Azure. Účet Automation musí být ve stejném předplatném jako trezor Azure Site Recovery.

6. V účtu Automation vyberte sadu runbook. Tato sada runbook je skript, který běží během provádění plán obnovení po obnovení první skupinu.

7. Chcete-li uložit skript, klikněte na tlačítko **OK**. Skript se přidá do **1. skupina: po kroky**.

    ![1:Start skupiny následné akce](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="considerations-for-adding-a-script"></a>Důležité informace týkající se přidání skriptu

* Pro možnost **odstranit krok** nebo **skript aktualizovat**, klikněte pravým tlačítkem na skript.
* Skript můžete spustit v Azure při převzetí služeb při selhání z místního počítače do Azure. Ho také můžete spustit v Azure jako skript primární lokalitu před vypnutí, během navrácení služeb po obnovení z Azure do místního počítače.
* Při spuštění skriptu, vloží kontextu plánu obnovení. Následující příklad ukazuje kontextové proměnné:

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

    Následující tabulka uvádí název a popis pro každou proměnnou v kontextu.

    | **Název proměnné** | **Popis** |
    | --- | --- |
    | RecoveryPlanName |Název plánu spuštěn. Tato proměnná umožňuje provádět různé akce na základě názvu plánu obnovení. Také můžete znovu použít skript. |
    | FailoverType |Určuje, jestli je převzetí služeb při selhání testu, plánované, nebo neplánované. |
    | FailoverDirection |Určuje, zda probíhá obnovení do primární nebo sekundární lokality. |
    | GroupID |Identifikuje číslo skupiny v plánu obnovení, když běží plánu. |
    | VmMap |Pole všechny virtuální počítače ve skupině. |
    | Klíč VMMap |Jedinečný klíč (GUID) pro každý virtuální počítač. Je stejný jako Identifikátor Azure Virtual Machine Manager (VMM) virtuálního počítače, kde je to možné. |
    | SubscriptionId |ID předplatného Azure, ve kterém byl vytvořen virtuální počítač. |
    | RoleName |Název virtuálního počítače Azure, který se obnovuje. |
    | CloudServiceName |Název služby cloudu Azure, ve kterém byl vytvořen virtuální počítač. |
    | ResourceGroupName|Název skupiny prostředků Azure, ve kterém byl vytvořen virtuální počítač. |
    | RecoveryPointId|Časové razítko pro při obnovení virtuálního počítače. |

* Zkontrolujte, zda má účet služby Automation následující moduly:
    * AzureRM.profile
    * AzureRM.Resources
    * AzureRM.Automation
    * AzureRM.Network
    * AzureRM.Compute

Všechny moduly musí být kompatibilní verze. Snadný způsob, jak všechny moduly musí být kompatibilní se má používat nejnovější verze všech modulů.

### <a name="access-all-vms-of-the-vmmap-in-a-loop"></a>Přístup k všechny virtuální počítače VMMap ve smyčce
Použijte následující kód na opakování mezi všechny virtuální počítače Microsoft VMMap:

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

> [!NOTE]
> Skupina název a role název hodnoty prostředků jsou prázdné po před akcí pro skupinu spouštěcí skript. Pouze v případě, že virtuální počítač této skupiny úspěšné v převzetí služeb při selhání, naplní se hodnoty. Skript je po akce spouštěcí skupiny.

## <a name="use-the-same-automation-runbook-in-multiple-recovery-plans"></a>Použijte stejné sady Automation runbook v více plánů obnovení

Můžete pomocí jednoho skriptu v více plánů obnovení pomocí externích proměnných. Můžete použít [proměnné automatizace Azure](../automation/automation-variables.md) k uložení parametry, které můžete předat provedení plánu obnovení. Přidáním předpony název plánu obnovení proměnnou můžete vytvořit jednotlivé proměnných pro každý plán obnovení. Pak použijte proměnné jako parametry. Můžete změnit parametr beze změny skript, ale stále Změna způsobu práce skript.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Použití jednoduché řetězec proměnné ve skriptu runbook

V tomto příkladu skript přijímá vstup z skupina zabezpečení sítě (NSG) a platí pro virtuální počítače v plánu obnovení.

Pro skript k detekci obnovení, který je spuštěn plán, použijte kontext plánu obnovení:

```
workflow AddPublicIPAndNSG {
    param (
          [parameter(Mandatory=$false)]
          [Object]$RecoveryPlanContext
    )

    $RPName = $RecoveryPlanContext.RecoveryPlanName
```

Použít existující skupina NSG, musíte znát název skupiny NSG a název skupiny prostředků NSG. Tyto proměnné můžete použijte jako vstupy pro skripty plánu obnovení. K tomu, vytvořte dvě proměnné v prostředky účet Automation. Přidejte název plánu obnovení, který vytváříte parametry jako předpony názvu proměnné.

1. Vytvořte proměnnou pro uložení názvu skupiny NSG. Přidání předpony k názvu proměnné pomocí názvu plánu obnovení.

    ![Vytvořte proměnnou Název skupiny NSG](media/site-recovery-runbook-automation-new/var1.png)

2. Vytvořte proměnnou pro uložení této skupině název skupiny prostředků. Přidání předpony k názvu proměnné pomocí názvu plánu obnovení.

    ![Vytvoření názvu skupiny prostředků NSG](media/site-recovery-runbook-automation-new/var2.png)


3.  Ve skriptu použijte následující kód odkaz získat hodnoty proměnné:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Pomocí proměnné v runbooku použít NSG k síťové rozhraní virtuálního počítače při selhání:

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

Pro každý plán obnovení vytvořte nezávislých proměnných, takže můžete opakovaně použít skript. Přidání předpony pomocí název plánu obnovení. Pro dokončení, začátku do konce skript pro tento scénář, najdete v části [přidat veřejné IP adresy a NSG na virtuálních počítačích během testovacího převzetí služeb při selhání plánu obnovení Site Recovery](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>Použití proměnné komplexní ukládat další informace

Vezměte v úvahu scénář, ve kterém chcete jednoho skriptu zapnout veřejnou IP adresu pro konkrétní virtuální počítače. V jiné scénáři můžete chtít použít odlišné skupiny Nsg na různé virtuální počítače (ne na všech virtuálních počítačích). Můžete použít skript, který je opakovaně použitelné pro každého plánu obnovení. Proměnný počet virtuálních počítačů může mít každý plán obnovení. Například obnovení služby SharePoint má dva elementy front end. Základní-obchodní (LOB) aplikace má pouze jeden front-endu. Nelze vytvořit samostatné proměnných pro každý plán obnovení.

V následujícím příkladu jsme nové způsobem a vytvořte [komplexní proměnná](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396) v prostředky účet Azure Automation. To uděláte tak, že zadáte více hodnot. Pokud chcete provést následující kroky je nutné použít prostředí Azure PowerShell:

1. V prostředí PowerShell Přihlaste se k předplatnému Azure:

    ```
    login-azurermaccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Uložit parametry, vytvořte proměnnou komplexní pomocí název plánu obnovení:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. V této proměnné komplexní **VMDetails** je ID virtuálního počítače pro chráněný virtuální počítač. Chcete-li získat ID virtuálního počítače na portálu Azure, zobrazte vlastnosti virtuálního počítače. Následující snímek obrazovky ukazuje proměnné, která jsou uloženy podrobnosti o dva virtuální počítače:

    ![Pomocí ID virtuálního počítače jako identifikátor GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. Pomocí této proměnné v runbooku. Pokud se najde uvedené identifikátor GUID virtuálního počítače v rámci plánu obnovení, použít NSG na virtuálním počítači:

    ```
    $VMDetailsObj = Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName
    ```

4. V sadě runbook projděte virtuálních počítačů kontextu plánu obnovení. Zkontrolujte, zda existuje virtuální počítač v **$VMDetailsObj**. Pokud existuje, přístup k vlastnostem proměnnou použít NSG:

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            Write-output $VMDetailsObj.value.$VMID

            if ($VMDetailsObj.value.$VMID -ne $Null) { #If the VM exists in the context, this will not b Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetailsObj.value.$VMID.'NSGName'
                $NSGRGname = $VMDetailsObj.value.$VMID.'NSGResourceGroupName'

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

Můžete použít stejný skriptu pro různé obnovení plány. Zadejte jiné parametry uložením hodnotu, která odpovídá do plánu obnovení v různých proměnných.

## <a name="sample-scripts"></a>Ukázkové skripty

Chcete-li nasadit ukázkové skripty do vašeho účtu Automation, klikněte na tlačítko **nasadit do Azure** tlačítko.

[![Nasazení do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Další příklad najdete v následujícím videu. Ukazuje, jak obnovit dvouvrstvé WordPress aplikace do Azure:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="additional-resources"></a>Další zdroje informací:
* [Azure Automation spustit jako účet služby](../automation/automation-create-runas-account.md)
* [Přehled Azure Automation](http://msdn.microsoft.com/library/azure/dn643629.aspx "přehled Azure Automation.")
* [Azure Automation ukázkové skripty](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "ukázkové skripty Azure Automation.")

## <a name="next-steps"></a>Další postup
[Další informace](site-recovery-failover.md) o spuštění převzetí služeb při selhání.
