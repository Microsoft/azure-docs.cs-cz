---
title: Registrace u poskytovatele prostředků virtuálního aplikace SQL
description: Zaregistrujte svůj virtuální počítač Azure SQL Server u poskytovatele prostředků virtuálního počítače SQL, který umožní funkce pro virtuální počítače SQL Serveru nasazené mimo Azure Marketplace, dodržování předpisů a vylepšenou možnosti správy.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 01e683e31905281d25fdcf976bc58397c052a6c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243182"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Registrace virtuálního počítače SQL Serveru v Azure u poskytovatele prostředků virtuálního počítače SQL

Tento článek popisuje, jak zaregistrovat virtuální počítač SQL Server (VM) v Azure s poskytovatelem prostředků virtuálního počítače SQL. Registrace u poskytovatele prostředků vytvoří _prostředek_ **virtuálního počítače SQL** v rámci vašeho předplatného, což je samostatný prostředek od prostředku virtuálního počítače. Zrušení registrace virtuálního počítače SQL Server od poskytovatele prostředků odebere prostředek **virtuálního počítače SQL,** _resource_ ale nepřeruší skutečný virtuální počítač. 

Nasazení image SQL Serveru v oblasti Virtuálního počítače Azure Marketplace prostřednictvím portálu Azure automaticky zaregistruje virtuální počítač SQL Server u poskytovatele prostředků. Pokud se ale rozhodnete sám nainstalovat SQL Server na virtuální počítač Azure nebo zřídit virtuální počítač Azure z vlastního virtuálního počítače VHD, měli byste zaregistrovat virtuální počítač SQL Server u poskytovatele prostředků pro:

- **Výhody funkcí**: Registrace virtuálního počítače SQL Server u poskytovatele prostředků odemkne [automatické opravy](virtual-machines-windows-sql-automated-patching.md), [automatické zálohování](virtual-machines-windows-sql-automated-backup-v2.md)a také možnosti monitorování a správy. Odemkne také [flexibilitu licencí](virtual-machines-windows-sql-ahb.md) a [edice.](virtual-machines-windows-sql-change-edition.md) Dříve byly tyto funkce dostupné jenom pro image virtuálních počítačů SQL Server nasazené z Azure Marketplace. 

- **Dodržování předpisů**: Registrace u poskytovatele prostředků virtuálního počítače SQL nabízí zjednodušenou metodu plnění požadavku upozornit Microsoft, že hybridní výhoda Azure byla povolena, jak je uvedeno v podmínkách produktu. Tento proces neguje nutnost spravovat registrační formuláře licencí pro každý prostředek.  

- **Bezplatná správa**: Registrace u poskytovatele prostředků virtuálního virtuálního provozu SQL ve všech třech režimech správy je zcela zdarma. Neexistuje žádné další náklady spojené s poskytovatelem prostředků nebo s měnící se režimy správy. 

- **Zjednodušená správa licencí**: Registrace u poskytovatele prostředků virtuálního počítače SQL zjednodušuje správu licencí serveru SQL Server a umožňuje rychle identifikovat virtuální počítače SQL Serveru pomocí hybridní výhody Azure povolené pomocí [portálu Azure](virtual-machines-windows-sql-manage-portal.md), rozhraní CLI az nebo prostředí PowerShell: 

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---

Chcete-li využít zprostředkovatele prostředků virtuálního počítače SQL, musíte [nejprve zaregistrovat předplatné u poskytovatele prostředků](#register-subscription-with-rp), který poskytuje poskytovateli prostředků možnost vytvářet prostředky v rámci tohoto konkrétního předplatného.

Další informace o výhodách používání zprostředkovatele prostředků virtuálního média SQL našetvete na tomto videu [channel9:](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner) 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="Výhody využití zprostředkovatele prostředků virtuálního počítače SQL při samoinstalaci serveru SQL Server v Azure – Microsoft Channel 9 Video"></iframe>


## <a name="prerequisites"></a>Požadavky

Chcete-li zaregistrovat virtuální počítač SQL Server u poskytovatele prostředků, budete potřebovat: 

- Předplatné [Azure](https://azure.microsoft.com/free/).
- Virtuální počítač Azure Resource Model [SQL Server](virtual-machines-windows-portal-sql-server-provision.md) nasazený do veřejného cloudu nebo cloudu Azure Government. 
- Nejnovější verze [Azure CLI](/cli/azure/install-azure-cli) nebo [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="management-modes"></a>Režimy řízení

Pokud [rozšíření SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) ještě nebyla nainstalována, registrace u poskytovatele prostředků virtuálního zařízení SQL automaticky nainstaluje rozšíření SQL Server IaaS v jednom ze tří režimů správy, zadaný během procesu registrace. Není zadání režimu správy nainstaluje rozšíření SQL IaaS v režimu úplné správy.  

Pokud rozšíření SQL IaaS již byla nainstalována ručně, pak je již v režimu úplné správy a registrace u poskytovatele prostředků v plném režimu nebude restartovat službu SQL Server.

Tři režimy správy jsou:

- **Zjednodušený** režim nevyžaduje restartování serveru SQL Server, ale podporuje pouze změnu typu licence a edice serveru SQL Server. Tuto možnost použijte pro virtuální počítači SQL Server s více instancemi nebo účast v instanci clusteru s podporou převzetí služeb při selhání (FCI). Při použití zjednodušeného režimu není žádný vliv na paměť nebo procesor a nesouvisející náklady. Doporučujeme nejprve zaregistrovat virtuální počítač SQL Server v zjednodušeném režimu a potom upgradovat na úplný režim během naplánovaného časového období údržby.  

- **Plný** režim poskytuje všechny funkce, ale vyžaduje restartování sql serveru a oprávnění správce systému. Toto je možnost, která je nainstalována ve výchozím nastavení při ruční instalaci rozšíření SQL IaaS. Použijte jej pro správu virtuálního virtuálního soudu SQL Server s jedinou instancí. Plný režim nainstaluje dvě služby systému Windows, které mají minimální dopad na paměť a procesor - ty lze sledovat prostřednictvím správce úloh. S použitím režimu úplné správy nejsou spojeny žádné náklady. 

- Režim **NoAgent** je vyhrazen pro sql server 2008 a SQL Server 2008 R2 nainstalované v systému Windows Server 2008. Při použití režimu NoAgent není žádný vliv na paměť nebo procesor. S použitím režimu správy NoAgent nejsou spojeny žádné náklady. 

Aktuální režim agenta SERVERU SQL Server IaaS můžete zobrazit pomocí prostředí PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>Registrace předplatného u RP

Chcete-li zaregistrovat virtuální počítač SQL Server u poskytovatele prostředků virtuálního počítače SQL, musíte nejprve zaregistrovat předplatné u poskytovatele prostředků. To dává poskytovateli prostředků virtuálního počítače SQL možnost vytvářet prostředky v rámci vašeho předplatného.  Můžete tak učinit pomocí portálu Azure, Azure CLI nebo PowerShellu.

### <a name="azure-portal"></a>portál Azure

1. Otevřete portál Azure a přejděte na **Všechny služby**. 
1. Přejděte na **Předplatná** a vyberte předplatné zájmu.  
1. Na stránce **Předplatná** přejděte na **web poskytovatele prostředků**. 
1. Zadejte **sql** do filtru, aby se poskytovatelé prostředků související ch od SQL. 
1. Vyberte **Registrovat**, **Znovu zaregistrovat**nebo **Zrušit registraci** pro zprostředkovatele **Microsoft.SqlVirtualMachine,** v závislosti na požadované akci. 

![Změna zprostředkovatele](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>Příkazový řádek

Zaregistrujte svého poskytovatele prostředků virtuálního počítače SQL do předplatného Azure pomocí az CLI nebo PowerShellu. 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-sql-vm-with-rp"></a>Registrace virtuálního virtuálního připojení SQL pomocí RP 

### <a name="lightweight-management-mode"></a>Zjednodušený režim správy

Pokud [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) nebyl nainstalován na virtuálním počítači, pak doporučení je zaregistrovat u poskytovatele prostředků virtuálního zařízení SQL v zjednodušeném režimu. Tím nainstalujete rozšíření SQL IaaS v [zjednodušeném režimu](#management-modes) a zabráníte restartování služby SQL Server. Potom můžete kdykoli upgradovat na úplný režim, ale tím se restartuje služba SQL Server, takže se doporučuje počkat na naplánované okno údržby. 

Zadejte typ licence SQL Serveru jako průběžný`PAYG`platby ( ) k`AHUB`platbě za použití, hybridní výhodu`DR`Azure ( ) k použití vlastní licence nebo zotavení po havárii ( ) k aktivaci [bezplatné licence na repliku zotavení po Havárii](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure).

Instance clusteru s podporou převzetí služeb při selhání a nasazení s více instancemi lze zaregistrovat pouze u poskytovatele prostředků virtuálního počítače SQL v zjednodušeném režimu. 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

Registrace virtuálního serveru SQL Server v zjednodušeném režimu pomocí příkazového příkazového příkazu Az: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

Registrace virtuálního serveru SQL Server v zjednodušeném režimu pomocí Prostředí PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Režim úplné správy


Pokud rozšíření SQL IaaS již byla nainstalována do virtuálního zařízení ručně, pak můžete zaregistrovat sql server virtuální hod v plném režimu bez restartování služby SQL Server. **Pokud však nebylo nainstalováno rozšíření SQL IaaS, registrace v plném režimu nainstaluje rozšíření SQL IaaS v plném režimu a restartuje službu SQL Server. Postupujte opatrně.**


Chcete-li zaregistrovat virtuální počítač SQL Server přímo v plném režimu (a případně restartovat službu SQL Server), použijte následující příkaz prostředí PowerShell: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Režim správy NoAgent 

Sql Server 2008 a 2008 R2 nainstalovaný v systému Windows Server 2008 _(nikoli R2)_ lze zaregistrovat u poskytovatele prostředků virtuálního zařízení SQL v [režimu NoAgent](#management-modes). Tato možnost zajišťuje dodržování předpisů a umožňuje monitorování virtuálního počítače SQL Server na webu Azure Portal s omezenou funkčností.

Zadejte `AHUB` `PAYG`buď `DR` , nebo jako **sqlLicenseType**a buď `SQL2008-WS2008` nebo `SQL2008R2-WS2008` jako **sqlImageOffer**. 

Chcete-li zaregistrovat instanci serveru SQL Server 2008 nebo 2008 R2 v instanci systému Windows Server 2008, použijte následující fragment kódu Az CLI nebo PowerShell: 


# <a name="az-cli"></a>[AZ CLI](#tab/bash)

Zaregistrujte virtuální počítač SQL Server 2008 v režimu NoAgent pomocí příkazového příkazu k registraci az: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```
 
 
Zaregistrujte virtuální počítač SQL Server 2008 R2 v režimu NoAgent pomocí příkazového příkazu k registraci Az: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008R2
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Registrace virtuálního serveru SQL Server 2008 v režimu NoAgent pomocí prostředí PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  Registrace virtuálního serveru SQL Server 2008 R2 v režimu NoAgent pomocí prostředí PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full-management-mode"></a>Upgrade na režim úplné správy 

Virtuální počítače SQL Serveru, které mají nainstalované *zjednodušené* rozšíření IaaS, můžou upgradovat režim na _plný_ pomocí portálu Azure, cli Az nebo PowerShellu. Virtuální servery SQL Server v režimu _NoAgent_ můžete upgradovat na _plný_ po upgradu operačního systému na systém Windows 2008 R2 a vyšší. Není možné downgrade - chcete-li tak učinit, budete muset [zrušit registraci](#unregister-vm-from-rp) virtuálního serveru SQL Server od poskytovatele prostředků virtuálního virtuálního připojení SQL. Tím odeberete _prostředek_ **virtuálního počítače SQL** , ale neodstraníte skutečný virtuální počítač. 

Aktuální režim agenta SERVERU SQL Server IaaS můžete zobrazit pomocí prostředí PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

Upgrade režimu agenta na plný: 


### <a name="azure-portal"></a>portál Azure

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Přejděte na prostředek [virtuálních počítačů SQL.](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) 
1. Vyberte virtuální počítač SQL Server a vyberte **Přehled**. 
1. Pro virtuální servery SQL Server s režimem NoAgent nebo lightweight IaaS vyberte **pouze typ licence a edition aktualizace jsou k dispozici se zprávou rozšíření SQL IaaS.**

   ![Výběr pro změnu režimu z portálu](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Zaškrtněte **políčko Souhlasím s restartováním služby SQL Server ve virtuálním počítači** a pak vyberte **Potvrdit,** chcete-li upgradovat režim IaaS na plný. 

    ![Zaškrtávací políčko pro souhlas s restartováním služby SQL Server ve virtuálním počítači](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>Příkazový řádek

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

Spusťte následující fragment kódu Az CLI:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Spusťte následující fragment kódu Prostředí PowerShell:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Ověření stavu registrace
Můžete ověřit, jestli váš virtuální počítač SQL Server již byla zaregistrována u poskytovatele prostředků virtuálního počítače SQL pomocí portálu Azure, Azure CLI nebo PowerShell. 

### <a name="azure-portal"></a>portál Azure 

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 
1. Přejděte na [virtuální počítače SQL Serveru](virtual-machines-windows-sql-manage-portal.md).
1. Vyberte virtuální počítač SQL Server ze seznamu. Pokud váš virtuální počítač SQL Server není uveden zde, pravděpodobně nebyl aregistrován u poskytovatele prostředků virtuálního počítače SQL. 
1. Zobrazení hodnoty v části **Stav**. Pokud je **stav** **úspěšný**, byl virtuální modul SQL Server úspěšně zaregistrován u poskytovatele prostředků virtuálního virtuálního připojení SQL. 

![Ověření stavu pomocí registrace SQL RP](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>Příkazový řádek

Ověřte aktuální stav registrace virtuálního serveru SQL Server pomocí rozhraní Az CLI nebo PowerShellu. `ProvisioningState`pokud `Succeeded` byla registrace úspěšná. 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Chyba označuje, že virtuální modul SQL Server nebyl zaregistrován u poskytovatele prostředků. 


## <a name="unregister-vm-from-rp"></a>Zrušení registrace virtuálního virtuálního mísa z RP

Pokud chcete zrušit registraci virtuálního počítače SQL Serveru u poskytovatele prostředků virtuálního počítače SQL, odstraňte *prostředek* virtuálního počítače SQL pomocí portálu Azure nebo azure cli. Odstraněním *prostředku* virtuálního počítače SQL neodstraníte virtuální počítač SQL Server. Buďte však opatrní a postupujte opatrně, protože je možné neúmyslně odstranit virtuální počítač při pokusu o odebrání *prostředku*. 

Zrušení registrace virtuálního virtuálního provozu SQL u poskytovatele prostředků virtuálního virtuálního média SQL je nutné snížit přechod režimu správy z úplné. 

### <a name="azure-portal"></a>portál Azure

Pokud chcete zrušit registraci virtuálního počítače SQL Serveru u poskytovatele prostředků pomocí portálu Azure, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Přejděte na prostředek virtuálního soudu SQL Server. 
  
   ![Prostředek virtuálních počítačů SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)

1. Vyberte **Odstranit**. 

   ![Odstranění zprostředkovatele prostředků virtuálního aplikace SQL](media/virtual-machines-windows-sql-register-with-rp/delete-sql-vm-resource-provider.png)

1. Zadejte název virtuálního počítače SQL a **zrušte zaškrtnutí políčka vedle virtuálního počítače**.

   ![Odstranění zprostředkovatele prostředků virtuálního aplikace SQL](media/virtual-machines-windows-sql-register-with-rp/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Pokud neodstraníte zaškrtnutí políčka vedle názvu virtuálního *počítače,* virtuální počítač zcela odstraníte. Zrušte zaškrtnutí políčka pro zrušení registrace virtuálního počítače SQL Server od poskytovatele prostředků, ale *neodstraňujte skutečný virtuální počítač*. 

1. Výběrem **možnosti Odstranit** potvrďte odstranění *prostředku*virtuálního počítače SQL , nikoli virtuálního počítače serveru SQL Server. 

### <a name="command-line"></a>Příkazový řádek

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Chcete-li zrušit registraci virtuálního počítače SQL Server od poskytovatele prostředků pomocí azure cli, použijte příkaz [delete az sql vm.](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) Tím odeberete prostředek virtuálního počítače serveru SQL *Server,* ale neodstraníte virtuální počítač. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Chcete-li zrušit registraci virtuálního počítače SQL Server od poskytovatele prostředků pomocí azure cli, použijte příkaz [New-AzSqlVM.](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) Tím odeberete prostředek virtuálního počítače serveru SQL *Server,* ale neodstraníte virtuální počítač. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>Omezení

Zprostředkovatel prostředků virtuálního aplikace SQL podporuje pouze:
- Virtuální počítače SQL Serveru nasazené prostřednictvím Správce prostředků Azure. Virtuální servery SQL Server nasazené prostřednictvím klasického modelu nejsou podporovány. 
- Virtuální počítače SQL Server nasazené do veřejného cloudu nebo cloudu Azure Government. Nasazení do jiných privátních nebo vládních cloudů nejsou podporována. 


## <a name="frequently-asked-questions"></a>Nejčastější dotazy 

**Mám zaregistrovat svůj virtuální počítač SQL Server zřízený z image SQL Serveru na Azure Marketplace?**

Ne. Microsoft automaticky registruje virtuální počítače zřízené z ibi SQL Serveru na Azure Marketplace. Registrace u poskytovatele prostředků virtuálního počítače SQL je vyžadována jenom v případě, že virtuální počítač *nebyl* zřízen z ibi SQL Serveru na Azure Marketplace a SQL Server byl nainstalovaný sám.

**Je poskytovatel prostředků virtuálního počítače SQL dostupný pro všechny zákazníky?** 

Ano. Zákazníci by měli zaregistrovat své virtuální počítače SQL Server u poskytovatele prostředků virtuálního počítače SQL, pokud nepoužili image SQL Serveru z Azure Marketplace a místo toho si nainstalovali SQL Server nebo pokud přinesli vlastní virtuální pevný disk. Virtuální počítače vlastněné všemi typy předplatných (direct, enterprise agreement a cloud solution provider) se můžou zaregistrovat u poskytovatele prostředků virtuálního počítače SQL.

**Mám se zaregistrovat u poskytovatele prostředků virtuálního zařízení SQL, pokud můj virtuální modul SQL Server už má nainstalované rozšíření SQL Server IaaS?**

Pokud je váš virtuální počítač SQL Server nainstalovaný sám a není zřízen z ibi SQL Serveru na Azure Marketplace, měli byste se zaregistrovat u poskytovatele prostředků virtuálního počítače SQL, i když jste nainstalovali rozšíření SQL Server IaaS. Registrace u poskytovatele prostředků virtuálního počítače SQL vytvoří nový prostředek typu Microsoft.SqlVirtualMachines. Instalace rozšíření SQL Server IaaS nevytvoří tento prostředek.

**Jaký je výchozí režim správy při registraci u poskytovatele prostředků virtuálního virtuálního média SQL?**

Výchozí režim správy při registraci u poskytovatele prostředků virtuálního virtuálního média SQL je *plný*. Pokud není vlastnost správy serveru SQL Server nastavena při registraci u poskytovatele prostředků virtuálního počítače SQL, bude režim nastaven jako úplná správa a služba SQL Server se restartuje. Doporučujeme nejprve zaregistrovat u poskytovatele prostředků virtuálního média SQL v zjednodušeném režimu a poté upgradovat na plný během časového období údržby. 

**Jaké jsou předpoklady pro registraci u poskytovatele prostředků virtuálního virtuálního připojení SQL?**

Neexistují žádné předpoklady pro registraci u poskytovatele prostředků virtuálního virtuálního média SQL v zjednodušeném režimu nebo bez agenta. Předpokladem pro registraci u poskytovatele prostředků virtuálního počítače SQL v plném režimu je nainstalováno rozšíření SQL Server IaaS na virtuálním počítači, jinak se služba SQL Server restartuje. 

**Můžu se zaregistrovat u poskytovatele prostředků virtuálního počítači SQL, pokud nemám na virtuálním počítači nainstalované rozšíření SQL Server IaaS?**

Ano, můžete se zaregistrovat u poskytovatele prostředků virtuálního počítači SQL v režimu zjednodušené správy, pokud nemáte na virtuálním počítači nainstalované rozšíření SQL Server IaaS. V zjednodušeném režimu bude poskytovatel prostředků virtuálního aplikace SQL používat konzolovou aplikaci k ověření verze a edice instance serveru SQL Server. 

Výchozí režim správy SQL při registraci u zprostředkovatele prostředků virtuálního provozu SQL je _Úplný_. Pokud není vlastnost SQL Management nastavena při registraci u zprostředkovatele prostředků virtuálního zařízení SQL, bude režim nastaven jako úplná správa. Doporučujeme nejprve zaregistrovat u poskytovatele prostředků virtuálního média SQL v zjednodušeném režimu a poté upgradovat na plný během časového období údržby. 

**Bude registrace u poskytovatele prostředků virtuálního počítači SQL nainstalovat agenta na můj virtuální počítač?**

Ne. Registrace u poskytovatele prostředků virtuálního virtuálního aplikace SQL vytvoří pouze nový prostředek metadat. Nenainstaluje agenta na virtuální počítač.

Rozšíření SQL Server IaaS je potřeba pouze pro povolení úplné správy. Upgrade režimu správy z odlehčené na plnou nainstaluje rozšíření SQL Server IaaS a restartuje SQL Server.

**Porestartování registrace u poskytovatele prostředků virtuálního počítače SQL Serveru sql server na mém virtuálním počítači?**

Záleží na režimu určeném při registraci. Pokud je zadán režim zjednodušený nebo NoAgent, služba SQL Server se nerestartuje. Zadání režimu správy jako úplné nebo ponechání režimu správy prázdné nainstaluje rozšíření SQL IaaS v režimu úplné správy, což způsobí restartování služby SQL Server. 

**Jaký je rozdíl mezi režimy správy lehkých a bez agenta při registraci u poskytovatele prostředků virtuálního virtuálního provozu SQL?** 

Režim správy bez agentů je k dispozici pouze pro sql server 2008 a SQL Server 2008 R2 v systému Windows Server 2008. Je to jediný dostupný režim správy pro tyto verze. Pro všechny ostatní verze SQL Server dva dostupné režimy správy jsou lehké a plné. 

Režim no-agent vyžaduje, aby zákazník nastavil vlastnosti verze a edice serveru SQL Server. Zjednodušený režim dotazuje virtuálního virtuálního média, aby našel verzi a edici instance serveru SQL Server.

**Můžu se zaregistrovat u poskytovatele prostředků virtuálního virtuálního uživatele SQL bez zadání typu licence serveru SQL Server?**

Ne. Typ licence serveru SQL Server není volitelnou vlastností při registraci u poskytovatele prostředků virtuálního zařízení SQL. Při registraci u poskytovatele prostředků virtuálního počítače SQL ve všech režimech správy (bez agenta, zjednodušená a plná) je třeba nastavit typ licence SQL Serveru jako průběžně nebo hybridní výhody Azure.

**Je možné upgradovat rozšíření SQL Server IaaS z režimu no-agent do plného režimu?**

Ne. Upgrade režimu správy na plný nebo lehký není k dispozici pro režim bez agenta. Jedná se o technické omezení systému Windows Server 2008. Operační systém budete muset nejprve upgradovat na systém Windows Server 2008 R2 nebo vyšší a poté budete moci upgradovat do režimu úplné správy. 

**Je možné upgradovat rozšíření SQL Server IaaS z režimu zjednodušeného režimu do plného režimu?**

Ano. Upgrade režimu správy z odlehčeného na plný je podporovaný přes PowerShell nebo portál Azure. Vyžaduje restartování služby SQL Server.

**Je možné downgrade rozšíření SQL Server IaaS z úplného režimu do no-agent nebo zjednodušený režim správy?**

Ne. Snížení stupně rozšíření SQL Server IaaS režimu správy není podporována. Režim správy nelze snížit z režimu full mode na režim lightweight nebo no-agent a nelze jej snížit z režimu zjednodušeného režimu na režim bez agenta. 

Chcete-li změnit režim správy z úplné správy, [odregistrute](#unregister-vm-from-rp) virtuální počítač SQL Server od poskytovatele prostředků serveru SQL Server zrušením *prostředku* serveru SQL Server a znovu zaregistrujte virtuální počítač SQL Server s poskytovatelem prostředků virtuálního počítače SQL VM znovu v jiném režimu správy.

**Můžu se zaregistrovat u poskytovatele prostředků virtuálního počítače SQL z webu Azure?**

Ne. Registrace u poskytovatele prostředků virtuálního počítače SQL není na webu Azure Portal dostupná. Registrace u poskytovatele prostředků virtuálního počítače SQL je podporovaná jenom s Azure CLI nebo PowerShellem. 

**Můžu zaregistrovat virtuální ho s poskytovatelem prostředků virtuálního zařízení SQL před instalací SQL Serveru?**

Ne. Virtuální modul by měl mít alespoň jednu instanci SQL Serveru (Database Engine), aby se úspěšně zaregistroval u poskytovatele prostředků virtuálního virtuálního zařízení SQL. Pokud na virtuálním počítači není žádná instance serveru SQL Server, bude nový prostředek Microsoft.SqlVirtualMachine ve stavu, který selhal.

**Můžu zaregistrovat virtuální počítač u poskytovatele prostředků virtuálního počítači SQL, pokud existuje více instancí serveru SQL Server?**

Ano. Zprostředkovatel prostředků virtuálního zařízení SQL zaregistruje pouze jednu instanci SQL Serveru (Database Engine). Poskytovatel prostředků virtuálního počítače SQL zaregistruje výchozí instanci serveru SQL Server v případě více instancí. Pokud neexistuje žádná výchozí instance, je podporováno pouze registrace v zjednodušeném režimu. Chcete-li upgradovat z režimu zjednodušené na úplné správy, by měla existovat výchozí instance serveru SQL Server nebo by měl mít virtuální modul pouze jednu pojmenovanou instanci SERVERU SQL Server.

**Můžu zaregistrovat instanci clusteru s podporou převzetí služeb při selhání serveru SQL Server u poskytovatele prostředků virtuálního virtuálního připojení SQL?**

Ano. Instance clusteru sql serveru s podporou převzetí služeb při selhání na virtuálním počítači Azure lze zaregistrovat u poskytovatele prostředků virtuálního počítače SQL v zjednodušeném režimu. Instance clusteru sql server převzetí služeb při selhání však nelze upgradovat na režim úplné správy.

**Můžu zaregistrovat svůj virtuální počítač u poskytovatele prostředků virtuálního počítače SQL, pokud je nakonfigurovaná skupina dostupnosti Always On?**

Ano. Registrace instance serveru SQL Server na virtuálním počítači Azure u poskytovatele prostředků virtuálního počítače SQL neexistují žádná omezení, pokud se účastníte konfigurace skupiny dostupnosti always on.

**Jaké jsou náklady na registraci u poskytovatele prostředků virtuálního virtuálního média SQL nebo při upgradu na režim úplné správy?**
Žádné. Neexistuje žádný poplatek spojený s registrací u poskytovatele prostředků virtuálního virtuálního připojení SQL nebo s použitím některého ze tří režimů správy. Správa virtuálního počítače SQL Server s poskytovatelem prostředků je zcela zdarma. 

**Jaký je dopad použití různých režimů správy na výkon?**
Při použití režimů *NoAgent* a *lehkých* režimů správy není žádný dopad. Při použití režimu *úplné* správy ze dvou služeb, které jsou nainstalovány do operačního systému, je minimální dopad. Ty lze sledovat prostřednictvím správce úloh a vidět ve vestavěné konzoli služeb systému Windows. 

Dva názvy služeb jsou:
- `SqlIaaSExtensionQuery`(Zobrazovaný `Microsoft SQL Server IaaS Query Service`název - )
- `SQLIaaSExtension`(Zobrazovaný `Microsoft SQL Server IaaS Agent`název - )


## <a name="next-steps"></a>Další kroky

Další informace najdete v těchto článcích: 

* [Přehled sql serveru na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Nejčastější dotazy pro SQL Server na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Pokyny k cenovým hodnotě pro SQL Server na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Poznámky k verzi pro SQL Server na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
