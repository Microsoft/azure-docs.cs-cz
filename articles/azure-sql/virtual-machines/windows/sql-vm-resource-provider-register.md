---
title: Registrace u poskytovatele prostředků virtuálního počítače SQL
description: Zaregistrujte svůj virtuální počítač s SQL Server Azure pomocí poskytovatele prostředků virtuálního počítače SQL, abyste povolili funkce pro SQL Server virtuální počítače nasazené mimo Azure Marketplace, a taky dodržování předpisů a vylepšené možnosti správy.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 11e8a2fd709b40c68b90e5ed139f18997e4cb29e
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89396960"
---
# <a name="register-a-sql-server-vm-in-azure-with-the-sql-vm-resource-provider-rp"></a>Registrace virtuálního počítače s SQL Server v Azure pomocí poskytovatele prostředků virtuálního počítače SQL (RP)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek popisuje, jak zaregistrovat SQL Server virtuální počítač v Azure pomocí poskytovatele prostředků virtuálního počítače SQL (RP). Při registraci u poskytovatele prostředků se vytvoří _prostředek_ **virtuálního počítače SQL** v rámci vašeho předplatného, což je samostatný prostředek z prostředku virtuálního počítače. Zrušení registrace SQL Server virtuálního počítače od poskytovatele prostředků odebere _prostředek_ **virtuálního počítače SQL** , ale neodstraní skutečný virtuální počítač. 

Nasazení Azure Marketplace image SQL Server virtuálního počítače pomocí Azure Portal automaticky registruje SQL Server virtuální počítač s poskytovatelem prostředků. Pokud se ale rozhodnete k samoobslužné instalaci SQL Server na virtuálním počítači Azure nebo zřídit virtuální počítač Azure z vlastního virtuálního pevného disku, měli byste zaregistrovat SQL Server virtuální počítač s poskytovatelem prostředků pro:

- **Výhody funkcí**: registrace virtuálního počítače s SQL Server u poskytovatele prostředků odemkne [automatizované opravy](automated-patching.md), [automatizované zálohování](automated-backup.md)a možnosti monitorování a správy. Také odemkne [licencování](licensing-model-azure-hybrid-benefit-ahb-change.md) a flexibilitu [edice](change-sql-server-edition.md) . Dříve byly tyto funkce dostupné jenom pro SQL Server imagí virtuálních počítačů nasazených z Azure Marketplace. 

- **Dodržování předpisů**: registrace pomocí poskytovatele prostředků virtuálního počítače SQL nabízí zjednodušenou metodu, která vyžaduje, abyste Microsoftu informovali, že zvýhodněné hybridní využití Azure je povolená, jak je uvedeno v dodacích číslech. Tento proces vyžaduje, aby ve všech zdrojích spravovali formuláře pro registraci licencí.  

- **Bezplatná Správa**: registrace u poskytovatele prostředků virtuálního počítače SQL ve všech třech režimech správy je úplně zadarmo. K poskytovateli prostředků se nevztahují žádné další náklady ani se měnícími se režimy správy. 

- **Zjednodušená správa licencí**: registrace pomocí poskytovatele prostředků virtuálního počítače SQL zjednodušuje správu licencí SQL Server a umožňuje rychlou identifikaci SQL Server virtuálních počítačů s povoleným Zvýhodněné hybridní využití Azure pomocí [Azure Portal](manage-sql-vm-portal.md), Azure CLI nebo PowerShellu: 

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

Abyste mohli využít poskytovatele prostředků virtuálního počítače SQL, musíte nejdřív [zaregistrovat předplatné u poskytovatele prostředků](#register-subscription-with-rp), který poskytovateli prostředků umožní vytvářet prostředky v rámci tohoto konkrétního předplatného.

## <a name="prerequisites"></a>Požadavky

Pokud chcete zaregistrovat SQL Server virtuální počítač s poskytovatelem prostředků, budete potřebovat: 

- [Předplatné Azure](https://azure.microsoft.com/free/)
- Model prostředků Azure [SQL Server virtuální počítač](create-sql-vm-portal.md) nasazený do veřejného nebo Azure Governmentho cloudu. 
- Nejnovější verzi rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) nebo [PowerShellu](/powershell/azure/new-azureps-module-az). 

## <a name="management-modes"></a>Režimy správy

Pokud [rozšíření SQL IaaS](sql-server-iaas-agent-extension-automate-management.md) ještě není nainstalované, registrace do poskytovatele prostředků virtuálního počítače SQL automaticky nainstaluje rozšíření SQL Server IaaS v jednom ze tří režimů správy určených během procesu registrace. Pokud neurčíte režim správy, nainstaluje se rozšíření SQL IaaS v režimu úplné správy.  

Pokud už je rozšíření SQL IaaS nainstalované ručně, pak už je v režimu úplné správy a při registraci u poskytovatele prostředků v plném režimu se služba SQL Server nerestartuje.

Existují tři režimy správy:

- **Odlehčený** režim nevyžaduje restartování SQL Server, ale podporuje pouze změnu typu licence a edice SQL Server. Tato možnost slouží k SQL Server virtuálních počítačů s několika instancemi nebo účasti v instanci clusteru s podporou převzetí služeb při selhání (FCI). Při použití zjednodušeného režimu nemá žádný vliv na paměť nebo procesor a nejsou k dispozici žádné náklady. Doporučuje se nejdřív zaregistrovat SQL Server virtuální počítač v jednoduchém režimu a potom během naplánovaného časového období údržby upgradovat na úplný režim.  

- **Úplný** režim zajišťuje všechny funkce, ale vyžaduje restartování oprávnění SQL Server a správce systému. Toto je možnost, která se instaluje standardně při ruční instalaci rozšíření SQL IaaS. Použijte ji ke správě SQL Server virtuálního počítače s jedinou instancí. Úplný režim nainstaluje dvě služby systému Windows, které mají minimální dopad na paměť a procesor – dá se monitorovat pomocí Správce úloh. Neexistují žádné náklady spojené s používáním úplného režimu spravovatelnosti. 

- Režim **Neagentů** je vyhrazen pro SQL Server 2008 a SQL Server 2008 R2 nainstalované v systému Windows Server 2008. Při použití režimu neagenta není ovlivněná paměť ani procesor. Neexistují žádné náklady spojené s používáním režimu správy neagentů. 

Aktuální režim SQL Server agenta IaaS můžete zobrazit pomocí prostředí PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>Zaregistrovat předplatné s RP

Pokud chcete zaregistrovat SQL Server virtuální počítač s poskytovatelem prostředků virtuálního počítače SQL, musíte nejdřív zaregistrovat předplatné u poskytovatele prostředků. Díky tomu má poskytovatel prostředků virtuálního počítače SQL možnost vytvářet prostředky v rámci vašeho předplatného.  Můžete to udělat pomocí Azure Portal, rozhraní příkazového řádku Azure nebo PowerShellu.

### <a name="azure-portal"></a>portál Azure

1. Otevřete Azure Portal a pokračujte na **všechny služby**. 
1. Přejít na **předplatná** a vyberte předplatné, které vás zajímá.  
1. Na stránce **předplatná** klikněte na **poskytovatelé prostředků**. 
1. Zadejte **SQL** do filtru pro uvedení zprostředkovatelů prostředků souvisejících s SQL. 
1. V závislosti na požadované akci vyberte **Registrovat**, **znovu zaregistrovat**nebo **zrušit registraci** pro poskytovatele  **Microsoft. SqlVirtualMachine** . 

   ![Úprava zprostředkovatele](./media/sql-vm-resource-provider-register/select-resource-provider-sql.png)


### <a name="command-line"></a>Příkazový řádek

Zaregistrujte poskytovatele prostředků virtuálního počítače SQL do svého předplatného Azure pomocí rozhraní příkazového řádku Azure CLI nebo PowerShellu. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

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

## <a name="register-with-rp"></a>Zaregistrovat v RP

### <a name="lightweight-management-mode"></a>Režim zjednodušené správy

Pokud na virtuálním počítači není nainstalované [rozšíření agenta SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md) , doporučujeme, abyste se v odlehčeném režimu zaregistrovali u poskytovatele prostředků virtuálního počítače SQL. Tím se nainstaluje rozšíření SQL IaaS v [jednoduchém režimu](#management-modes) a zabrání se restartování služby SQL Server. Následně můžete upgradovat na úplný režim kdykoli, ale tím dojde k restartování služby SQL Server, aby bylo doporučeno počkat do plánovaného časového období údržby. 

Zadejte SQL Server Typ licence jako průběžné platby ( `PAYG` ) a platíte za použití, zvýhodněné hybridní využití Azure ( `AHUB` ) pro použití vlastní licence nebo zotavení po havárii ( `DR` ) k aktivaci [bezplatné licence na repliku Dr](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).

Instance clusteru s podporou převzetí služeb při selhání a nasazení s více instancemi se dají zaregistrovat jenom u poskytovatele prostředků virtuálního počítače SQL v odlehčeném režimu. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Registrace virtuálního počítače s SQL Server v jednoduchém režimu pomocí Azure CLI: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

Zaregistrujte SQL Server virtuální počítač v jednoduchém režimu pomocí prostředí PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Režim úplné správy


Pokud už je rozšíření SQL IaaS nainstalované do virtuálního počítače ručně, můžete virtuální počítač SQL Server zaregistrovat v plném režimu bez restartování služby SQL Server. **Pokud ale není rozšíření SQL IaaS nainstalované, při registraci v plném režimu se rozšíření SQL IaaS nainstaluje v plném režimu a restartuje službu SQL Server. Postupujte opatrně.**


Pokud chcete virtuální počítač SQL Server zaregistrovat přímo v plném režimu (a případně restartovat službu SQL Server), použijte následující příkaz prostředí PowerShell: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Režim správy neagentů 

SQL Server 2008 a 2008 R2 nainstalované v systému Windows Server 2008 (_ne R2_) je možné zaregistrovat u poskytovatele prostředků SQL VM v [režimu neagent](#management-modes). Tato možnost zaručuje dodržování předpisů a umožňuje monitorovat SQL Server virtuálním počítačem v Azure Portal s omezenými funkcemi.

Zadejte buď `AHUB` , `PAYG` nebo `DR` jako **sqlLicenseType**, a `SQL2008-WS2008` nebo `SQL2008R2-WS2008` jako **sqlImageOffer**. 

K registraci SQL Server 2008 nebo 2008 R2 v instanci Windows Server 2008 použijte následující fragment kódu Azure CLI nebo PowerShellu: 


# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Zaregistrujte svůj virtuální počítač s SQL Server 2008 v režimu bez agenta pomocí Azure CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
Zaregistrujte svůj virtuální počítač SQL Server 2008 R2 v režimu neagentů pomocí Azure CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Zaregistrujte svůj virtuální počítač s SQL Server 2008 v režimu bez agenta pomocí prostředí PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  Zaregistrujte svůj virtuální počítač SQL Server 2008 R2 v režimu bez agenta pomocí prostředí PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full"></a>Upgradovat na úplný  

SQL Server virtuální počítače s nainstalovanou *zjednodušenou* příponou IaaS mohou upgradovat režim na _úplný_ pomocí Azure Portal, rozhraní příkazového řádku Azure nebo PowerShellu. SQL Server virtuálních počítačů v režimu bez _agenta_ se může upgradovat na _úplnou_ po upgradu operačního systému na Windows 2008 R2 a novější. Není možné nadowngradovat – k tomu je potřeba [zrušit registraci](#unregister-from-rp) virtuálního počítače s SQL Server od poskytovatele prostředků virtuálního počítače SQL. Tím dojde k odebrání prostředku **virtuálního počítače SQL** _resource_, ale neodstraní se skutečný virtuální počítač. 

Aktuální režim SQL Server agenta IaaS můžete zobrazit pomocí prostředí PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

Postup upgradu režimu agenta na úplný: 


### <a name="azure-portal"></a>portál Azure

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Přejít na prostředek [virtuálních počítačů SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) . 
1. Vyberte svůj virtuální počítač SQL Server a vyberte **Přehled**. 
1. V případě SQL Server virtuálních počítačů pomocí agenta nebo režimu zjednodušeného IaaS vyberte možnost **jediný typ licence a aktualizace edice jsou k dispozici ve zprávě rozšíření SQL IaaS** .

   ![Výběry pro změnu režimu z portálu](./media/sql-vm-resource-provider-register/change-sql-iaas-mode-portal.png)

1. Zaškrtněte políčko **Souhlasím s restartováním služby SQL Server na virtuálním počítači** a pak vyberte **Potvrdit** , abyste upgradovali režim IaaS na úplný. 

    ![Zaškrtávací políčko pro vyjádření souhlasu s restartováním služby SQL Server na virtuálním počítači](./media/sql-vm-resource-provider-register/enable-full-mode-iaas.png)

### <a name="command-line"></a>Příkazový řádek

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Spusťte následující fragment kódu Azure CLI:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Spusťte následující fragment kódu prostředí PowerShell:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Ověřit stav registrace
Můžete ověřit, jestli váš virtuální počítač s SQL Server už je zaregistrovaný u poskytovatele prostředků SQL VM pomocí Azure Portal, Azure CLI nebo PowerShellu. 

### <a name="azure-portal"></a>portál Azure 

1. Přihlaste se na [Azure Portal](https://portal.azure.com). 
1. Přejít na [virtuální počítače s SQL Server](manage-sql-vm-portal.md).
1. Ze seznamu vyberte svůj virtuální počítač SQL Server. Pokud zde SQL Server virtuální počítač, pravděpodobně není zaregistrovaný u poskytovatele prostředků virtuálního počítače SQL. 
1. Zobrazit hodnotu v části **stav** Pokud **Status** je stav **úspěšný**, byl virtuální počítač SQL Server zaregistrován u poskytovatele prostředků virtuálního počítače SQL úspěšně. 

   ![Ověřit stav pomocí registrace SQL RP](./media/sql-vm-resource-provider-register/verify-registration-status.png)

### <a name="command-line"></a>Příkazový řádek

Ověřte aktuální SQL Server stav registrace virtuálního počítače pomocí rozhraní příkazového řádku Azure CLI nebo PowerShellu. `ProvisioningState` zobrazí se `Succeeded` , zda byla registrace úspěšná. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Chyba naznačuje, že virtuální počítač SQL Server nebyl zaregistrován u poskytovatele prostředků. 


## <a name="unregister-from-rp"></a>Zrušit registraci z RP

Pokud chcete zrušit registraci SQL Server virtuálního počítače pomocí poskytovatele prostředků virtuálního počítače SQL, odstraňte *prostředek* virtuálního počítače sql pomocí Azure Portal nebo Azure CLI. Odstranění *prostředku* virtuálního počítače SQL neodstraní virtuální počítač SQL Server. Buďte ale opatrní a postupujte opatrně, protože je možné při pokusu o odebrání *prostředku*neúmyslně odstranit virtuální počítač. 

Zrušení registrace virtuálního počítače SQL pomocí poskytovatele prostředků virtuálního počítače SQL je nezbytné pro přechod z úplného režimu správy. 

### <a name="azure-portal"></a>portál Azure

Chcete-li zrušit registraci SQL Serverho virtuálního počítače poskytovatelem prostředků pomocí Azure Portal, postupujte podle následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte k prostředku virtuálního počítače SQL. 
  
   ![Prostředek virtuálních počítačů SQL](./media/sql-vm-resource-provider-register/sql-vm-manage.png)

1. Vyberte **Odstranit**. 

   ![Odstranit poskytovatele prostředků virtuálního počítače SQL](./media/sql-vm-resource-provider-register/delete-sql-vm-resource-provider.png)

1. Zadejte název virtuálního počítače SQL a **zrušte zaškrtnutí políčka u virtuálního počítače**.

   ![Odstranit poskytovatele prostředků virtuálního počítače SQL](./media/sql-vm-resource-provider-register/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Nepovedlo se zrušit zaškrtnutí políčka u názvu virtuálního počítače, aby se virtuální počítač zcela *odstranil* . Zrušením zaškrtnutí políčka zrušíte registraci SQL Server virtuálního počítače od poskytovatele prostředků, ale *neodstraníte skutečný virtuální počítač*. 

1. Vyberte **Odstranit** a potvrďte odstranění *prostředku*virtuálního počítače SQL, nikoli SQL Server virtuálního počítače. 

### <a name="command-line"></a>Příkazový řádek

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Pokud chcete zrušit registraci SQL Server virtuálního počítače od poskytovatele prostředků pomocí Azure CLI, použijte příkaz [AZ SQL VM Delete](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) . Tím se odebere *prostředek* virtuálního počítače SQL Server, ale virtuální počítač se neodstraní. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pokud chcete zrušit registraci SQL Server virtuálního počítače od poskytovatele prostředků pomocí PowerShellu, použijte příkaz [Remove-AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm). Tím se odebere *prostředek* virtuálního počítače SQL Server, ale virtuální počítač se neodstraní. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>Omezení

Poskytovatel prostředků virtuálního počítače SQL podporuje jenom:
- SQL Server virtuální počítače nasazené prostřednictvím Azure Resource Manager. SQL Server virtuální počítače nasazené prostřednictvím klasického modelu se nepodporují. 
- SQL Server virtuální počítače nasazené do veřejného nebo Azure Governmentho cloudu. Nasazení do jiných privátních nebo státních cloudů se nepodporuje. 


## <a name="frequently-asked-questions"></a>Nejčastější dotazy 

**Mám zaregistrovat SQL Server virtuální počítač zřízený z SQL Server Image v Azure Marketplace?**

No. Microsoft automaticky registruje virtuální počítače zřízené z SQL Server imagí v Azure Marketplace. Registrace u poskytovatele prostředků virtuálního počítače SQL se vyžaduje jenom v případě, že se virtuální počítač *nezřídil z* SQL Server imagí v Azure Marketplace a SQL Server byl samoobslužně nainstalován.

**Je poskytovatel prostředků virtuálního počítače SQL dostupný pro všechny zákazníky?** 

Ano. Zákazníci by měli zaregistrovat své SQL Server virtuální počítače s poskytovatelem prostředků SQL VM, pokud nepoužívali image SQL Server z Azure Marketplace a místo toho SQL Server, nebo pokud přenesli svůj vlastní VHD. Virtuální počítače vlastněné všemi typy předplatných (Direct, smlouva Enterprise a Cloud Solution Provider) se můžou registrovat u poskytovatele prostředků virtuálního počítače SQL.

**Mám se zaregistrovat u poskytovatele prostředků virtuálního počítače SQL, pokud už má SQL Server virtuální počítač rozšíření SQL Server IaaS nainstalované?**

Pokud je váš virtuální počítač SQL Server samoobslužně nainstalován a není zřízený z SQL Server imagí v Azure Marketplace, měli byste se zaregistrovat u poskytovatele prostředků virtuálního počítače SQL i v případě, že jste nainstalovali rozšíření SQL Server IaaS. Registrace u poskytovatele prostředků virtuálního počítače SQL vytvoří nový prostředek typu Microsoft. SqlVirtualMachine. Instalace rozšíření SQL Server IaaS nevytváří tento prostředek.

**Jaký je výchozí režim správy při registraci u poskytovatele prostředků virtuálního počítače SQL?**

Výchozí režim správy při registraci u poskytovatele prostředků virtuálního počítače SQL je *plný*. Pokud není nastavena vlastnost Management SQL Server při registraci u poskytovatele prostředků virtuálního počítače SQL, režim se nastaví jako plná spravovatelnost a vaše služba SQL Server se restartuje. Doporučuje se nejprve zaregistrovat u poskytovatele prostředků virtuálních počítačů SQL v prostém režimu a potom v okně údržby provést upgrade na úplný. 

**Jaké jsou požadavky na registraci u poskytovatele prostředků virtuálních počítačů SQL?**

Neexistují žádné požadavky k registraci u poskytovatele prostředků virtuálního počítače SQL v režimu prostého režimu nebo v režimu No-agent. Předpokladem pro registraci k poskytovateli prostředků SQL VM v plném režimu je rozšíření SQL Server IaaS na virtuálním počítači nainstalované, jinak se služba SQL Server restartuje. 

**Můžu se zaregistrovat u poskytovatele prostředků virtuálního počítače SQL, pokud nemám na virtuálním počítači nainstalovanou příponu SQL Server IaaS?**

Ano, pokud na virtuálním počítači nemáte nainstalované rozšíření SQL Server IaaS, můžete se zaregistrovat u poskytovatele prostředků virtuálního počítače SQL v režimu zjednodušené správy. V odlehčeném režimu poskytovatel prostředků virtuálního počítače SQL použije konzolovou aplikaci k ověření verze a edice instance SQL Server. 

Výchozí režim správy SQL při registraci u poskytovatele prostředků virtuálního počítače SQL je _plný_. Pokud při registraci u poskytovatele prostředků virtuálního počítače SQL není nastavená vlastnost SQL Management, nastaví se tento režim jako plná spravovatelnost. Doporučuje se nejprve zaregistrovat u poskytovatele prostředků virtuálních počítačů SQL v prostém režimu a potom v okně údržby provést upgrade na úplný. 

**Bude se registrovat u poskytovatele prostředků virtuálního počítače SQL instalace agenta na mém virtuálním počítači?**

Ano, registrace do poskytovatele prostředků virtuálního počítače SQL nainstaluje agenta na virtuální počítač.

Rozšíření SQL Server IaaS spoléhá na to, že agent má dotaz na metadata pro SQL Server. Pokud je poskytovatel prostředků virtuálního počítače SQL regsitered v režimu neagentů, jediný čas, kdy není nainstalován agent.

**Provede se registrace na mém virtuálním počítači pomocí poskytovatele prostředků virtuálního počítače SQL SQL Server restartování?**

Závisí na režimu zadaném během registrace. Je-li zadán režim Lightweight nebo unagent, služba SQL Server nebude restartována. Pokud ale nastavíte režim správy jako plný, nebo necháte v režimu správy prázdné, nainstaluje se rozšíření SQL IaaS v režimu úplné správy, což způsobí restartování služby SQL Server. 

**Jaký je rozdíl mezi režimy jednoduchého a bez agenta správy při registraci u poskytovatele prostředků virtuálního počítače SQL?** 

Režim správy No-Agent je k dispozici pouze pro SQL Server 2008 a SQL Server 2008 R2 v systému Windows Server 2008. Pro tyto verze je to jediný dostupný režim správy. Pro všechny ostatní verze SQL Server jsou dva dostupné režimy spravovatelnosti zjednodušené a úplné. 

Režim No-agent vyžaduje, SQL Server vlastnosti verze a edice, které zákazník nastaví. Odlehčený režim dotazuje virtuální počítač na nalezení verze a edice instance SQL Server.

**Můžu se zaregistrovat u poskytovatele prostředků SQL VM bez zadání typu licence SQL Server?**

No. Typ licence SQL Server není volitelnou vlastností při registraci u poskytovatele prostředků virtuálního počítače SQL. Je nutné nastavit typ licence SQL Server jako průběžné platby nebo Zvýhodněné hybridní využití Azure při registraci u poskytovatele prostředků virtuálního počítače SQL ve všech režimech spravovatelnosti (No-agent, Lightweight a Full).

**Můžu upgradovat rozšíření SQL Server IaaS z režimu No-Agent na režim Full?**

No. Upgrade režimu spravovatelnosti na úplný nebo lehký není pro režim bez agenta k dispozici. Toto je technické omezení Windows serveru 2008. Nejprve budete muset upgradovat operační systém na Windows Server 2008 R2 nebo novější a pak budete moct upgradovat na režim úplné správy. 

**Můžu upgradovat rozšíření SQL Server IaaS z režimu prostého režimu na režim Full?**

Yes. Upgrade režimu spravovatelnosti z jednoduchého na plný se podporuje přes PowerShell nebo Azure Portal. Vyžaduje restart SQL Server služby.

**Můžu SQL Server rozšíření IaaS z úplného režimu snížit na režim No-agent nebo pro zjednodušenou správu?**

No. Přechod do režimu spravovatelnosti rozšíření SQL Server IaaS se nepodporuje. Režim spravovatelnosti nejde downgradovat z režimu úplného režimu na odlehčený nebo bez agenta a nedá se downgradovat z prostého režimu na režim bez agenta. 

Chcete-li změnit režim spravovatelnosti z možnosti úplné správy, [zrušte registraci](#unregister-from-rp) SQL Server virtuálního počítače od poskytovatele prostředků SQL VM tím, že vyřadíte *prostředek* SQL Server a znovu zaregistrujete SQL Server virtuálního počítače pomocí poskytovatele prostředků SQL VM v jiném režimu správy.

**Můžu se zaregistrovat k poskytovateli prostředků SQL VM z Azure Portal?**

No. Registrace u poskytovatele prostředků virtuálního počítače SQL není k dispozici v Azure Portal. Registrace u poskytovatele prostředků virtuálního počítače SQL je podporovaná jenom pomocí Azure CLI nebo PowerShellu. 

**Je možné zaregistrovat virtuální počítač s poskytovatelem prostředků SQL VM před tím, než se SQL Server nainstaluje?**

No. Aby se virtuální počítač mohl úspěšně zaregistrovat u poskytovatele prostředků virtuálního počítače SQL, musí mít aspoň jednu instanci SQL Server (databázový stroj). Pokud na virtuálním počítači není žádná SQL Server instance, nový prostředek Microsoft. SqlVirtualMachine bude ve stavu selhání.

**Je možné zaregistrovat virtuální počítač s poskytovatelem prostředků SQL VM, pokud existuje více instancí SQL Server?**

Yes. Poskytovatel prostředků virtuálního počítače SQL bude registrovat jenom jednu instanci SQL Server (databázový stroj). Poskytovatel prostředků virtuálního počítače SQL zaregistruje výchozí instanci SQL Server v případě více instancí. Pokud neexistuje žádná výchozí instance, je podporována pouze registrace v režimu prostého režimu. Chcete-li provést upgrade z prostého režimu na režim správy, musí být použita výchozí instance SQL Server, nebo by měl mít virtuální počítač pouze jednu pojmenovanou instanci SQL Server.

**Můžu u SQL Server instance clusteru s podporou převzetí služeb při selhání zaregistrovat poskytovatele prostředků virtuálního počítače SQL?**

Yes. SQL Server instancí clusteru s podporou převzetí služeb při selhání na virtuálním počítači Azure je možné zaregistrovat u poskytovatele prostředků virtuálního počítače SQL v jednoduchém režimu. SQL Server instancí clusteru s podporou převzetí služeb při selhání se ale nedá upgradovat na režim úplné správy.

**Můžu zaregistrovat virtuální počítač k poskytovateli prostředků SQL VM, pokud je nakonfigurovaná Skupina dostupnosti Always On?**

Yes. Pokud se účastníte konfigurace skupiny dostupnosti AlwaysOn, neexistují žádná omezení k registraci instance SQL Server na virtuálním počítači Azure s poskytovatelem prostředků SQL VM.

**Jaké jsou náklady na registraci u poskytovatele prostředků SQL VM nebo při upgradu na režim úplné správy?**
Žádné K registraci u poskytovatele prostředků SQL VM nebo pomocí žádného ze tří režimů správy se neúčtují žádné poplatky. Správa virtuálního počítače s SQL Server pomocí poskytovatele prostředků je zcela zadarmo. 

**Jaký je dopad na výkon při použití různých režimů správy?**
Při použití režimů nespravovaného *agenta* a *zjednodušené* správy to nemá žádný vliv. Použití *úplného* režimu správy ze dvou služeb, které jsou nainstalovány do operačního systému, má minimální dopad. Dají se monitorovat pomocí Správce úloh a zobrazují se v integrované konzole Windows Services. 

Názvy těchto dvou služeb:
- `SqlIaaSExtensionQuery` (Zobrazované jméno- `Microsoft SQL Server IaaS Query Service` )
- `SQLIaaSExtension` (Zobrazované jméno- `Microsoft SQL Server IaaS Agent` )


## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích: 

* [Přehled SQL Server na virtuálním počítači s Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Nejčastější dotazy k SQL Server na virtuálním počítači s Windows](frequently-asked-questions-faq.md)  
* [Doprovodné materiály k cenách pro SQL Server na virtuálním počítači s Windows](pricing-guidance.md)
* [Poznámky k verzi pro SQL Server na virtuálním počítači s Windows](../../database/doc-changes-updates-release-notes.md)
