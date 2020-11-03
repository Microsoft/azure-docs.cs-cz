---
title: Nainstalovat SQL Server rozšíření agenta IaaS
description: Zaregistrujte svůj virtuální počítač s SQL Server Azure pomocí poskytovatele prostředků virtuálního počítače SQL, abyste povolili funkce pro SQL Server virtuální počítače nasazené mimo Azure Marketplace, a taky dodržování předpisů a vylepšené možnosti správy.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/30/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8099bf87410e8359894d8b40d7637451891ad664
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286145"
---
# <a name="install-sql-server-iaas-agent-extension"></a>Nainstalovat SQL Server rozšíření agenta IaaS
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Když zaregistrujete SQL Server virtuální počítač pomocí poskytovatele prostředků virtuálního počítače SQL, nainstaluje se [rozšíření agenta SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md). 

V tomto článku se dozvíte, jak zaregistrovat jeden virtuální počítač s SQL Server pomocí poskytovatele prostředků virtuálního počítače SQL. Případně můžete registrovat všechny SQL Server virtuální počítače [automaticky](sql-vm-resource-provider-automatic-registration.md) nebo [hromadně](sql-vm-resource-provider-bulk-register.md).


## <a name="overview"></a>Přehled

Když se zaregistrujete u poskytovatele prostředků, nainstaluje se [rozšíření agenta SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md) a také se vytvoří _prostředek_ **virtuálního počítače SQL** v rámci vašeho předplatného, což je _samostatný_ prostředek z prostředku virtuálního počítače. Zrušení registrace SQL Server virtuálního počítače od poskytovatele prostředků odebere _prostředek_ **virtuálního počítače SQL** , ale neodstraní skutečný virtuální počítač.

Nasazení Azure Marketplace image SQL Server virtuálního počítače pomocí Azure Portal automaticky registruje SQL Server virtuální počítač s poskytovatelem prostředků. Pokud se ale rozhodnete vlastní instalaci SQL Server na virtuálním počítači Azure nebo zřídit virtuální počítač Azure z vlastního virtuálního pevného disku, musíte zaregistrovat SQL Server virtuální počítač s poskytovatelem prostředků SQL VM a nainstalovat rozšíření agenta SQL IaaS. 

Abyste mohli využít poskytovatele prostředků virtuálního počítače SQL, musíte nejdřív [zaregistrovat předplatné u poskytovatele prostředků](#register-subscription-with-rp), který poskytovateli prostředků umožní vytvářet prostředky v rámci tohoto konkrétního předplatného.

> [!IMPORTANT]
> Rozšíření agenta SQL IaaS shromažďuje data pro účely výslovného účelu poskytování dobrovolných výhod při používání SQL Server v rámci Azure Virtual Machines. Microsoft nebude tato data používat pro audity licencování bez předchozího souhlasu zákazníka. Další informace najdete v tématu [SQL Server ochrany osobních údajů](/sql/sql-server/sql-server-privacy#non-personal-data) .

## <a name="prerequisites"></a>Předpoklady

Pokud chcete zaregistrovat SQL Server virtuální počítač s poskytovatelem prostředků, budete potřebovat: 

- [Předplatné Azure](https://azure.microsoft.com/free/)
- Model prostředků Azure [Windows Server 2008 (nebo novější)](../../../virtual-machines/windows/quick-create-portal.md) , který je nasazený s [SQL Server 2008 (nebo novější)](https://www.microsoft.com/sql-server/sql-server-downloads) , který je nasazený do veřejného nebo Azure Governmentho cloudu. 
- Nejnovější verzi rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) nebo [Azure PowerShell (minimálně 5,0)](/powershell/azure/install-az-ps). 


## <a name="register-subscription-with-rp"></a>Zaregistrovat předplatné s RP

Pokud chcete zaregistrovat SQL Server virtuální počítač s poskytovatelem prostředků virtuálního počítače SQL, musíte nejdřív zaregistrovat předplatné u poskytovatele prostředků. Díky tomu má poskytovatel prostředků virtuálního počítače SQL možnost vytvářet prostředky v rámci vašeho předplatného.  Můžete to udělat pomocí Azure Portal, rozhraní příkazového řádku Azure nebo Azure PowerShell.

### <a name="azure-portal"></a>Azure Portal

1. Otevřete Azure Portal a pokračujte na **všechny služby**. 
1. Přejít na **předplatná** a vyberte předplatné, které vás zajímá.  
1. Na stránce **předplatná** klikněte na **poskytovatelé prostředků**. 
1. Zadejte **SQL** do filtru pro uvedení zprostředkovatelů prostředků souvisejících s SQL. 
1. V závislosti na požadované akci vyberte **Registrovat** , **znovu zaregistrovat** nebo **zrušit registraci** pro poskytovatele  **Microsoft. SqlVirtualMachine** . 

   ![Úprava zprostředkovatele](./media/sql-vm-resource-provider-register/select-resource-provider-sql.png)


### <a name="command-line"></a>Příkazový řádek

Zaregistrujte poskytovatele prostředků virtuálního počítače SQL do svého předplatného Azure pomocí rozhraní příkazového řádku Azure nebo Azure PowerShell. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-rp"></a>Zaregistrovat v RP

Existují tři režimy správy pro [SQL Server rozšíření agenta IaaS](sql-server-iaas-agent-extension-automate-management.md#management-modes). 

Instalace rozšíření v režimu úplné správy restartuje službu SQL Server, takže se doporučuje nejdřív nainstalovat rozšíření v jednoduchém režimu a potom [upgradovat na plný](#upgrade-to-full) v časovém intervalu pro správu a údržbu. 

### <a name="lightweight-management-mode"></a>Režim zjednodušené správy

Pomocí Azure CLI nebo Azure PowerShell zaregistrujete virtuální počítač s SQL Server pomocí poskytovatele prostředků a nainstalujete rozšíření SQL IaaS v jednoduchém režimu. Tato akce nerestartuje službu SQL Server. Následně můžete upgradovat na úplný režim kdykoli, ale tím dojde k restartování služby SQL Server, aby bylo doporučeno počkat do plánovaného časového období údržby. 

Zadejte SQL Server Typ licence jako průběžné platby ( `PAYG` ) a platíte za použití, zvýhodněné hybridní využití Azure ( `AHUB` ) pro použití vlastní licence nebo zotavení po havárii ( `DR` ) k aktivaci [bezplatné licence na repliku Dr](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).

Instance clusteru s podporou převzetí služeb při selhání a nasazení s více instancemi se dají zaregistrovat jenom u poskytovatele prostředků virtuálního počítače SQL v odlehčeném režimu. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Registrace virtuálního počítače s SQL Server v jednoduchém režimu pomocí Azure CLI: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Zaregistrujte SQL Server virtuální počítač v jednoduchém režimu s Azure PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Režim úplné správy

Když zaregistrujete SQL Server virtuální počítač v plném režimu, restartuje se služba SQL Server. Postupujte opatrně. 

Pokud chcete virtuální počítač SQL Server zaregistrovat přímo v plném režimu (a případně restartovat službu SQL Server), použijte následující Azure PowerShell příkaz: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Režim správy neagentů 

SQL Server 2008 a 2008 R2 nainstalované v systému Windows Server 2008 ( _ne R2_ ) je možné zaregistrovat u poskytovatele prostředků SQL VM v [režimu neagent](sql-server-iaas-agent-extension-automate-management.md#management-modes). Tato možnost zaručuje dodržování předpisů a umožňuje monitorovat SQL Server virtuálním počítačem v Azure Portal s omezenými funkcemi.

Zadejte buď `AHUB` , `PAYG` nebo `DR` jako **sqlLicenseType** , a `SQL2008-WS2008` nebo `SQL2008R2-WS2008` jako **sqlImageOffer**. 

K registraci SQL Server 2008 nebo 2008 R2 v instanci Windows Server 2008 použijte následující příkaz Azure CLI nebo Azure PowerShell fragment kódu: 


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

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Zaregistrujte svůj virtuální počítač s SQL Server 2008 v režimu neagentů s Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  

Zaregistrujte svůj virtuální počítač SQL Server 2008 R2 v režimu neagentů s Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="verify-mode"></a>Režim ověření

Aktuální režim SQL Server agenta IaaS můžete zobrazit pomocí Azure PowerShell: 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>Upgradovat na úplný  

SQL Server virtuální počítače s nainstalovanou *zjednodušenou* příponou IaaS mohou upgradovat režim na _úplný_ pomocí Azure Portal, rozhraní příkazového řádku Azure nebo Azure PowerShell. SQL Server virtuálních počítačů v režimu bez _agenta_ se může upgradovat na _úplnou_ po upgradu operačního systému na Windows 2008 R2 a novější. Není možné nadowngradovat – k tomu je potřeba [zrušit registraci](#unregister-from-rp) virtuálního počítače s SQL Server od poskytovatele prostředků virtuálního počítače SQL. Tím dojde k odebrání prostředku **virtuálního počítače SQL** _resource_ , ale neodstraní se skutečný virtuální počítač. 


### <a name="azure-portal"></a>Azure Portal

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
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

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Spusťte následující fragment kódu Azure PowerShell:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Ověřit stav registrace
Můžete ověřit, jestli váš virtuální počítač s SQL Server už je s poskytovatelem prostředků SQL VM zaregistrovaný pomocí Azure Portal, rozhraní příkazového řádku Azure nebo Azure PowerShell. 

### <a name="azure-portal"></a>Azure Portal 

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). 
1. Přejít na [virtuální počítače s SQL Server](manage-sql-vm-portal.md).
1. Ze seznamu vyberte svůj virtuální počítač SQL Server. Pokud zde SQL Server virtuální počítač, pravděpodobně není zaregistrovaný u poskytovatele prostředků virtuálního počítače SQL. 
1. Zobrazit hodnotu v části **stav** Pokud **Status** je stav **úspěšný** , byl virtuální počítač SQL Server zaregistrován u poskytovatele prostředků virtuálního počítače SQL úspěšně. 

   ![Ověřit stav pomocí registrace SQL RP](./media/sql-vm-resource-provider-register/verify-registration-status.png)

### <a name="command-line"></a>Příkazový řádek

Ověřte aktuální SQL Server stav registrace virtuálního počítače pomocí rozhraní příkazového řádku Azure CLI nebo Azure PowerShell. `ProvisioningState` zobrazí se `Succeeded` , zda byla registrace úspěšná. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Chyba naznačuje, že virtuální počítač SQL Server nebyl zaregistrován u poskytovatele prostředků. 


## <a name="unregister-from-rp"></a>Zrušit registraci z RP

Pokud chcete zrušit registraci SQL Server virtuálního počítače pomocí poskytovatele prostředků virtuálního počítače SQL, odstraňte *prostředek* virtuálního počítače sql pomocí Azure Portal nebo Azure CLI. Odstranění *prostředku* virtuálního počítače SQL neodstraní virtuální počítač SQL Server. Buďte ale opatrní a postupujte opatrně, protože je možné při pokusu o odebrání *prostředku* neúmyslně odstranit virtuální počítač. 

Zrušení registrace virtuálního počítače SQL pomocí poskytovatele prostředků virtuálního počítače SQL je nezbytné pro přechod z úplného režimu správy. 

### <a name="azure-portal"></a>Azure Portal

Chcete-li zrušit registraci SQL Serverho virtuálního počítače poskytovatelem prostředků pomocí Azure Portal, postupujte podle následujících kroků:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Přejděte k prostředku virtuálního počítače SQL. 
  
   ![Prostředek virtuálních počítačů SQL](./media/sql-vm-resource-provider-register/sql-vm-manage.png)

1. Vyberte **Odstranit**. 

   ![Výběr odstranit v horním navigačním panelu](./media/sql-vm-resource-provider-register/delete-sql-vm-resource-provider.png)

1. Zadejte název virtuálního počítače SQL a **zrušte zaškrtnutí políčka u virtuálního počítače**.

   ![Zrušte zaškrtnutí políčka virtuální počítač, aby nedošlo k odstranění skutečného virtuálního počítače, a pak vyberte odstranit, abyste mohli pokračovat v odstraňování prostředku virtuálního počítače SQL.](./media/sql-vm-resource-provider-register/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Nepovedlo se zrušit zaškrtnutí políčka u názvu virtuálního počítače, aby se virtuální počítač zcela *odstranil* . Zrušením zaškrtnutí políčka zrušíte registraci SQL Server virtuálního počítače od poskytovatele prostředků, ale *neodstraníte skutečný virtuální počítač*. 

1. Vyberte **Odstranit** a potvrďte odstranění *prostředku* virtuálního počítače SQL, nikoli SQL Server virtuálního počítače. 

### <a name="command-line"></a>Příkazový řádek

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Pokud chcete zrušit registraci SQL Server virtuálního počítače od poskytovatele prostředků pomocí Azure CLI, použijte příkaz [AZ SQL VM Delete](/cli/azure/sql/vm?view=azure-cli-latest&preserve-view=true#az-sql-vm-delete) . Tím se odebere *prostředek* virtuálního počítače SQL Server, ale virtuální počítač se neodstraní. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pokud chcete zrušit registraci SQL Server virtuálního počítače od poskytovatele prostředků s Azure PowerShell, použijte příkaz [Remove-AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm). Tím se odebere *prostředek* virtuálního počítače SQL Server, ale virtuální počítač se neodstraní. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---



## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích: 

* [Přehled SQL Server na virtuálním počítači s Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Nejčastější dotazy k SQL Server na virtuálním počítači s Windows](frequently-asked-questions-faq.md)  
* [Doprovodné materiály k cenách pro SQL Server na virtuálním počítači s Windows](pricing-guidance.md)
* [Poznámky k verzi pro SQL Server na virtuálním počítači s Windows](../../database/doc-changes-updates-release-notes.md)
