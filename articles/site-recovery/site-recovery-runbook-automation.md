---
title: Přidání runbooků Azure Automation do plánů obnovení Site Recovery | Dokumentace Microsoftu
description: Zjistěte, jak rozšířit plány obnovení s využitím Azure Automation pro zotavení po havárii pomocí Azure Site Recovery.
author: ruturaj
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: ruturajd@microsoft.com
ms.openlocfilehash: 5da623d07c34114d82c2b818a7c06420ffb9c886
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50211107"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Přidání runbooků Azure Automation do plánů obnovení
V tomto článku popisujeme, jak Azure Site Recovery se integruje s Azure Automation, aby vám pomohla rozšířit plány obnovení. Plány obnovení můžete orchestrovat obnovení virtuálních počítačů, které jsou chráněné pomocí služby Site Recovery. Plány obnovení fungovat i pro replikaci do sekundárního cloudu a pro replikaci do Azure. Plány obnovení také pomoci, ujistěte se, obnovení **přesné**, **opakovatelné**, a **automatizované**. Pokud převzetí služeb při selhání virtuálních počítačů do Azure, rozšiřuje integrace s Azure Automation plánech obnovení. Můžete ho použít ke spuštění sady runbook, který nabízí výkonné automatizaci úkolů.

Pokud jste ještě Azure Automation, můžete [zaregistrovat](https://azure.microsoft.com/services/automation/) a [stáhnout ukázkové skripty](https://azure.microsoft.com/documentation/scripts/). Další informace a zjistěte, jak orchestrovat obnovení do Azure s použitím [plány obnovení](./site-recovery-create-recovery-plans.md), naleznete v tématu [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/).

V tomto článku popisujeme, jak integrovat Azure Automation. runbooky plány obnovení. Příklady použít k automatizaci základní úkoly, které dříve vyžadovaly ručního zásahu. Také zjistíte, jak převést více kroky obnovení na akci obnovení jedním kliknutím.

## <a name="customize-the-recovery-plan"></a>Přizpůsobit plán obnovení
1. Přejděte **Site Recovery** okno prostředku plánu obnovení. V tomto příkladu plán obnovení obsahuje dva virtuální počítače přidat do něj pro obnovení. Chcete-li začít přidávat sady runbook, klikněte na tlačítko **vlastní** kartu.

    ![Klikněte na tlačítko Přizpůsobit](media/site-recovery-runbook-automation-new/essentials-rp.png)


2. Klikněte pravým tlačítkem na **skupiny 1: Start**a pak vyberte **přidat akci po**.

    ![Klikněte pravým tlačítkem na skupinu 1: Spuštění a přidat akci po](media/site-recovery-runbook-automation-new/customize-rp.png)

3. Klikněte na tlačítko **vyberte skript**.

4. Na **akce aktualizace** okna, název skriptu **Hello World**.

    ![Okno Akce aktualizace](media/site-recovery-runbook-automation-new/update-rp.png)

5. Zadejte název účtu služby Automation.
    >[!NOTE]
    > Účet Automation může být v libovolné oblasti Azure. Účet Automation musí být ve stejném předplatném jako trezor Azure Site Recovery.

6. Ve vašem účtu Automation vyberte sadu runbook. Tato sada runbook je skript, který se spustí při spuštění plánu zotavení po obnovení do první skupiny.

7. Pokud chcete uložit skript, klikněte na tlačítko **OK**. Skript se přidá do **1. skupina: kroky prováděné po zpracování**.

    ![Akce po skupiny 1:Start](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="considerations-for-adding-a-script"></a>Důležité informace týkající se přidání skriptu

* Možnosti **odstranit krok** nebo **aktualizujte skript v**, klikněte pravým tlačítkem na skript.
* Skript můžete spustit v Azure během převzetí služeb při selhání z místního počítače do Azure. Je také můžete spustit v Azure jako primární lokality skript před vypnutím, během navrácení služeb po obnovení z Azure do místního počítače.
* Po spuštění skriptu vkládá kontextu plánu obnovení. Následující příklad ukazuje kontextové proměnné:

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

    Následující tabulka uvádí název a popis každou proměnnou v kontextu.

    | **Název proměnné** | **Popis** |
    | --- | --- |
    | RecoveryPlanName |Název plánu spuštěn. Tato proměnná umožňuje provést různé akce na základě názvu plánu obnovení. Také můžete znovu použít skript. |
    | FailoverType |Určuje, zda je převzetí služeb při selhání testu, plánované nebo neplánované. |
    | FailoverDirection |Určuje, zda probíhá obnovení do primární nebo sekundární lokality. |
    | ID skupiny |Označuje číslo skupiny v plánu obnovení při spuštění plánu. |
    | VmMap |Pole všech virtuálních počítačů ve skupině. |
    | Klíč VMMap |Jedinečný klíč (GUID) pro každý virtuální počítač. Je stejný jako Azure Virtual Machine Manager (VMM) ID virtuálního počítače, kde je to možné. |
    | SubscriptionId |ID předplatného Azure, ve kterém byl virtuální počítač vytvořen. |
    | RoleName |Název virtuálního počítače Azure, který se obnovuje. |
    | CloudServiceName |Název služby Azure cloud v rámci které se vytvoří virtuální počítač. |
    | ResourceGroupName|Název skupiny prostředků Azure v rámci které se vytvoří virtuální počítač. |
    | RecoveryPointId|Časové razítko pro při obnovení virtuálního počítače. |

* Zkontrolujte, zda má účet služby Automation tyto moduly:
    * AzureRM.profile
    * AzureRM.Resources
    * AzureRM.Automation
    * AzureRM.Network
    * AzureRM.Compute

Všechny moduly musí být kompatibilní verzí. Snadný způsob, jak zajistit, že jsou kompatibilní všechny moduly, je použití nejnovější verze všech modulů.

### <a name="access-all-vms-of-the-vmmap-in-a-loop"></a>Přístup k všechny virtuální počítače z VMMap ve smyčce
Použijte následující kód k vytvoření smyčky ve všech virtuálních počítačích z VMMap společnosti Microsoft:

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
> Zdroj skupiny název role název hodnoty a jsou prázdné po před akcí pro skupinu spouštěcí skript. Hodnoty se vyplní pouze v případě, že virtuální počítač z této skupiny převzetí služeb při selhání úspěšně. Skript je akce po spuštění skupiny.

## <a name="use-the-same-automation-runbook-in-multiple-recovery-plans"></a>Použít stejnou sadu runbook Automation ve více plánů obnovení

Jeden skript můžete použít ve více plánů obnovení pomocí externích proměnných. Můžete použít [proměnné služeb automatizace Azure](../automation/automation-variables.md) k uložení parametrů, které můžete předat pro spuštění plánu obnovení. Tak, že přidáte název plánu obnovení jako předpona k proměnné, můžete vytvořit jednotlivé proměnné pro každý plán obnovení. Potom použijte proměnné jako parametry. Změna parametru bez úpravy skriptu, ale stále změnit způsob, jakým funguje skriptu.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Použití jednoduché řetězcové proměnné ve skriptu sady runbook

V tomto příkladu skript přijímá vstup ze skupiny zabezpečení sítě (NSG) a použije ho k virtuálním počítačům z plánu obnovení.

Pro skript k detekci obnovení, který je spuštěn plán, použijte kontextu plánu obnovení:

```
workflow AddPublicIPAndNSG {
    param (
          [parameter(Mandatory=$false)]
          [Object]$RecoveryPlanContext
    )

    $RPName = $RecoveryPlanContext.RecoveryPlanName
```

Chcete-li použít existující skupině NSG, musíte znát název skupiny zabezpečení sítě a název skupiny prostředků skupiny zabezpečení sítě. Tyto proměnné můžete použijte jako vstupy pro skripty plánu obnovení. K tomu vytvořte dvě proměnné v prostředků účtu Automation. Přidejte název plánu obnovení, kterou vytváříte parametry jako předpony názvu proměnné.

1. Vytvořte proměnnou pro uložení názvu skupiny zabezpečení sítě. Přidáte předponu názvu proměnné pomocí názvu plánu obnovení.

    ![Vytvořte proměnnou pro název skupiny zabezpečení sítě](media/site-recovery-runbook-automation-new/var1.png)

2. Vytvořte proměnnou pro uložení skupin zabezpečení sítě a název skupiny prostředků. Přidáte předponu názvu proměnné pomocí názvu plánu obnovení.

    ![Vytvoření skupiny zabezpečení sítě názvu skupiny prostředků](media/site-recovery-runbook-automation-new/var2.png)


3.  Ve skriptu použijte následující kód odkaz k získání hodnoty proměnných:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Použití proměnné v runbooku použít skupinu zabezpečení sítě k síťovému rozhraní virtuálního počítače převzetím služeb při selhání:

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

Pro každý plán obnovení vytvořte nezávislých proměnných, takže můžete znovu použít skript. Přidáte předponu pomocí název plánu obnovení. Kompletní a začátku do konce skript v tomto scénáři najdete v tématu [přidání veřejné IP adresy a skupiny zabezpečení sítě pro virtuální počítače během testovacího převzetí služeb při selhání plánu obnovení Site Recovery](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>Použít komplexní proměnné k ukládání Další informace

Představte si třeba situaci, ve kterém chcete jednoho skriptu zapnout veřejnou IP adresu na konkrétní virtuální počítače. V jiném případě můžete chtít použít různé skupiny zabezpečení sítě v různých virtuálních počítačů (ne pro všechny virtuální počítače). Můžete vytvořit skript, který je opakovaně použitelný pro všechny plánu obnovení. Každý plán obnovení může mít proměnný počet virtuálních počítačů. Například obnovení služby SharePoint obsahuje dva front-endů. Základní-obchodní (LOB) aplikace má pouze jeden front-endu. Nelze vytvořit samostatné proměnné pro každý plán obnovení.

V následujícím příkladu jsme pomocí nové techniky a vytvořit [komplexní proměnné](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureautomationvariable) v prostředků účtu služby Azure Automation. To provedete tak, že zadáte více hodnot. Pomocí Azure Powershellu musíte provést následující kroky:

1. V prostředí PowerShell Přihlaste se ke svému předplatnému Azure:

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Pokud chcete uložit parametry, vytvořte proměnnou komplexní pomocí názvu plánu obnovení:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. V této proměnné komplexní **VMDetails** je ID virtuálního počítače pro chráněný virtuální počítač. Chcete-li získat ID virtuálního počítače na webu Azure Portal, zobrazte vlastnosti virtuálního počítače. Následující snímek obrazovky ukazuje proměnná, která uchovává podrobnosti dva virtuální počítače:

    ![Použít jako identifikátor GUID ID virtuálního počítače](media/site-recovery-runbook-automation-new/vmguid.png)

4. Pomocí této proměnné v runbooku. Pokud je zadaný identifikátor GUID virtuálního počítače v rámci plánu obnovení, vztahují skupiny zabezpečení sítě na virtuálním počítači:

    ```
    $VMDetailsObj = Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName
    ```

4. V sadě runbook projděte virtuální počítače kontextu plánu obnovení. Zkontrolujte, zda virtuální počítač existuje v **$VMDetailsObj**. Pokud existuje, přístup k vlastnostem proměnné, chcete-li použít skupinu zabezpečení sítě:

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

Stejný skript můžete použít pro plány obnovení jinou. Zadejte jiné parametry uložením hodnota, která odpovídá plánu obnovení v jiné proměnné.

## <a name="sample-scripts"></a>Ukázkové skripty

Ukázky skriptů nasazení do vašeho účtu Automation, klikněte na tlačítko **nasadit do Azure** tlačítko.

[![Nasazení do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Další příklad najdete v následujícím videu. Ukazuje, jak obnovit dvouvrstvé aplikace WordPress na Azure:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="additional-resources"></a>Další zdroje informací:
* [Azure Automation spustit jako účet služby](../automation/automation-create-runas-account.md)
* [Přehled Azure Automation](http://msdn.microsoft.com/library/azure/dn643629.aspx "přehled Azure Automation.")
* [Azure Automation ukázkové skripty](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "ukázkové skripty Azure Automation.")

## <a name="next-steps"></a>Další postup
[Další informace](site-recovery-failover.md) o spuštění převzetí služeb při selhání.
