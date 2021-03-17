---
title: Registrovat s rozšířením agenta SQL IaaS
description: Zaregistrujte virtuální počítač Azure SQL Server s rozšířením agenta SQL IaaS, abyste povolili funkce pro SQL Server virtuální počítače nasazené mimo Azure Marketplace, a taky dodržování předpisů a vylepšené možnosti správy.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell, contperf-fy21q2
ms.openlocfilehash: 983cdab0c5f5b856537c661c7427a83099f30ed4
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181427"
---
# <a name="register-sql-server-vm-with-sql-iaas-agent-extension"></a>Registrace SQL Server virtuálního počítače s rozšířením agenta SQL IaaS
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Když zaregistrujete SQL Server virtuální počítač s [rozšířením agenta SQL IaaS](sql-server-iaas-agent-extension-automate-management.md) , budete moct na virtuálním počítači Azure odemknout spoustu výhod funkcí pro váš SQL Server. 

V tomto článku se dozvíte, jak zaregistrovat jeden SQL Server virtuální počítač s rozšířením agenta SQL IaaS. Alternativně můžete registrovat všechny SQL Server virtuální počítače [automaticky](sql-agent-extension-automatic-registration-all-vms.md) nebo [hromadně více virtuálních počítačů](sql-agent-extension-manually-register-vms-bulk.md).


## <a name="overview"></a>Přehled

Když se zaregistrujete pomocí [rozšíření agenta SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md) , vytvoří se _prostředek_ **virtuálního počítače SQL** v rámci vašeho předplatného, což je _samostatný_ prostředek z prostředku virtuálního počítače. Zrušení registrace SQL Server virtuálního počítače z rozšíření odebere _prostředek_ **virtuálního počítače SQL** , ale neodstraní skutečný virtuální počítač.

Nasazení Azure Marketplace image SQL Server virtuálního počítače pomocí Azure Portal automaticky registruje SQL Server virtuální počítač s příponou. Pokud se ale rozhodnete sami nainstalovat SQL Server na virtuálním počítači Azure nebo zřídit virtuální počítač Azure z vlastního virtuálního pevného disku, musíte zaregistrovat svůj SQL Server virtuální počítač s rozšířením agenta SQL IaaS k odemčení úplných výhod funkcí a možností spravovatelnosti. 

Abyste mohli využít rozšíření agenta SQL IaaS, musíte nejdřív [zaregistrovat předplatné u poskytovatele **Microsoft. SqlVirtualMachine**](#register-subscription-with-rp), který poskytuje rozšíření SQL IaaS, aby bylo možné vytvářet prostředky v rámci tohoto konkrétního předplatného.

> [!IMPORTANT]
> Rozšíření agenta SQL IaaS shromažďuje data pro účely výslovného účelu poskytování dobrovolných výhod při používání SQL Server v rámci Azure Virtual Machines. Microsoft nebude tato data používat pro audity licencování bez předchozího souhlasu zákazníka. Další informace najdete v tématu [SQL Server ochrany osobních údajů](/sql/sql-server/sql-server-privacy#non-personal-data) .

## <a name="prerequisites"></a>Požadavky

Pokud chcete zaregistrovat SQL Server virtuální počítač s rozšířením, budete potřebovat: 

- [Předplatné Azure](https://azure.microsoft.com/free/)
- Model prostředků Azure [Windows Server 2008 (nebo novější)](../../../virtual-machines/windows/quick-create-portal.md) , který je nasazený s [SQL Server 2008 (nebo novější)](https://www.microsoft.com/sql-server/sql-server-downloads) , který je nasazený do veřejného nebo Azure Governmentho cloudu. 
- Nejnovější verzi rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) nebo [Azure PowerShell (minimálně 5,0)](/powershell/azure/install-az-ps). 


## <a name="register-subscription-with-rp"></a>Zaregistrovat předplatné s RP

Pokud chcete zaregistrovat SQL Server virtuální počítač s rozšířením agenta SQL IaaS, musíte nejdřív zaregistrovat předplatné u poskytovatele **Microsoft. SqlVirtualMachine** . Díky tomu má rozšíření agenta SQL IaaS možnost vytvářet prostředky v rámci vašeho předplatného.  Můžete to udělat pomocí Azure Portal, rozhraní příkazového řádku Azure nebo Azure PowerShell.

### <a name="azure-portal"></a>portál Azure

1. Otevřete Azure Portal a pokračujte na **všechny služby**. 
1. Přejít na **předplatná** a vyberte předplatné, které vás zajímá.  
1. Na stránce **předplatná** pokračujte na **rozšíření**. 
1. Zadejte **SQL** do filtru k uvedení rozšíření souvisejících s SQL. 
1. V závislosti na požadované akci vyberte **Registrovat**, **znovu zaregistrovat** nebo **zrušit registraci** pro poskytovatele  **Microsoft. SqlVirtualMachine** . 

   ![Úprava zprostředkovatele](./media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)


### <a name="command-line"></a>Příkazový řádek

Zaregistrujte své předplatné Azure pomocí poskytovatele **Microsoft. SqlVirtualMachine** pomocí Azure CLI nebo Azure PowerShell. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

```azurecli-interactive
# Register the SQL IaaS Agent extension to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL IaaS Agent extension to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-extension"></a>Registrovat s rozšířením

Existují tři režimy správy pro [SQL Server rozšíření agenta IaaS](sql-server-iaas-agent-extension-automate-management.md#management-modes). 

Po registraci rozšíření v režimu úplné správy se služba SQL Server restartuje, takže se nejdřív doporučuje zaregistrovat rozšíření v jednoduchém režimu a pak [upgradovat na plný](#upgrade-to-full) v časovém intervalu pro správu a údržbu. 

### <a name="lightweight-management-mode"></a>Režim zjednodušené správy

Použijte rozhraní příkazového řádku Azure nebo Azure PowerShell k registraci SQL Server virtuálního počítače s příponou v jednoduchém režimu. Tato akce nerestartuje službu SQL Server. Následně můžete upgradovat na úplný režim kdykoli, ale tím dojde k restartování služby SQL Server, aby bylo doporučeno počkat do plánovaného časového období údržby. 

Zadejte SQL Server Typ licence jako průběžné platby ( `PAYG` ) pro platbu za použití, zvýhodněné hybridní využití Azure ( `AHUB` ) pro použití vlastní licence nebo zotavení po havárii ( `DR` ) k aktivaci [bezplatné licence na repliku Dr](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).

Instance clusteru s podporou převzetí služeb při selhání a nasazení s více instancemi se dají zaregistrovat jenom s rozšířením agenta SQL IaaS v jednoduchém režimu. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Registrace virtuálního počítače s SQL Server v jednoduchém režimu pomocí Azure CLI: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> 
  ```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Zaregistrujte SQL Server virtuální počítač v jednoduchém režimu s Azure PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license_type>  -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Režim úplné správy

Když zaregistrujete SQL Server virtuální počítač v plném režimu, restartuje se služba SQL Server. Postupujte opatrně. 

Pokud chcete virtuální počítač SQL Server zaregistrovat přímo v plném režimu (a případně restartovat službu SQL Server), použijte následující Azure PowerShell příkaz: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Režim správy neagentů 

SQL Server 2008 a 2008 R2 nainstalované v systému Windows Server 2008 (_ne R2_) je možné zaregistrovat pomocí rozšíření agenta SQL IaaS v [režimu neagent](sql-server-iaas-agent-extension-automate-management.md#management-modes). Tato možnost zaručuje dodržování předpisů a umožňuje monitorovat SQL Server virtuálním počítačem v Azure Portal s omezenými funkcemi.


Jako **typ licence** zadejte buď: `AHUB` , `PAYG` , nebo `DR` . Pro **nabídku image** zadejte buď `SQL2008-WS2008` nebo `SQL2008R2-WS2008`

Pokud chcete zaregistrovat SQL Server 2008 ( `SQL2008-WS2008` ) nebo 2008 R2 ( `SQL2008R2-WS2008` ) na instanci Windows serveru 2008, použijte následující příkaz Azure CLI nebo Azure PowerShell fragment kódu: 


# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Zaregistrujte svůj SQL Server virtuální počítač v režimu bez agenta pomocí Azure CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type <license type>  --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer <image offer> 
 ```
 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)


Zaregistrujte svůj SQL Server virtuální počítač v režimu bez agenta pomocí Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license type> -SqlManagementType NoAgent -Sku Standard -Offer <image offer>
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

SQL Server virtuálních počítačů, které mají rozšíření v *prostém* režimu, se může upgradovat na _úplnou_ pomocí Azure Portal, rozhraní příkazového řádku Azure nebo Azure PowerShell. SQL Server virtuálních počítačů v režimu bez _agenta_ se může upgradovat na _úplnou_ po upgradu operačního systému na Windows 2008 R2 a novější. Není možné nadowngradovat – k tomu je potřeba [zrušit registraci](#unregister-from-extension) virtuálního počítače s SQL Server z rozšíření agenta SQL IaaS. Tím dojde k odebrání prostředku **virtuálního počítače SQL** , ale neodstraní se skutečný virtuální počítač. 

> [!NOTE]
> Když provedete upgrade režimu správy pro rozšíření SQL IaaS na Full, restartuje službu SQL Server. V některých případech může restartování způsobit změnu hlavního názvu služby (SPN) přidružených ke službě SQL Server, aby se změnil na nesprávný uživatelský účet. Pokud máte problémy s připojením po upgradu režimu správy na úplný, [zrušte registraci a znovu zaregistrujte své hlavní názvy služby (SPN](/sql/database-engine/configure-windows/register-a-service-principal-name-for-kerberos-connections)).


### <a name="azure-portal"></a>portál Azure

Chcete-li upgradovat rozšíření na režim úplného použití Azure Portal, postupujte podle následujících kroků: 

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Přejít na prostředek [virtuálních počítačů SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) . 
1. Vyberte svůj virtuální počítač SQL Server a vyberte **Přehled**. 
1. V případě SQL Server virtuálních počítačů pomocí agenta nebo režimu zjednodušeného IaaS vyberte možnost **jediný typ licence a aktualizace edice jsou k dispozici ve zprávě rozšíření SQL IaaS** .

   ![Výběry pro změnu režimu z portálu](./media/sql-agent-extension-manually-register-single-vm/change-sql-iaas-mode-portal.png)

1. Zaškrtněte políčko **Souhlasím s restartováním služby SQL Server na virtuálním počítači** a pak vyberte **Potvrdit** , abyste upgradovali režim IaaS na úplný. 

    ![Zaškrtávací políčko pro vyjádření souhlasu s restartováním služby SQL Server na virtuálním počítači](./media/sql-agent-extension-manually-register-single-vm/enable-full-mode-iaas.png)

### <a name="command-line"></a>Příkazový řádek

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Pokud chcete upgradovat rozšíření na plný režim, spusťte následující fragment kódu Azure CLI:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Chcete-li upgradovat rozšíření na úplný režim, spusťte následující fragment kódu Azure PowerShell:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Ověřit stav registrace
Můžete ověřit, jestli se váš SQL Server virtuální počítač už zaregistroval s rozšířením agenta SQL IaaS pomocí Azure Portal, rozhraní příkazového řádku Azure nebo Azure PowerShell. 

### <a name="azure-portal"></a>portál Azure 

Chcete-li ověřit stav registrace pomocí Azure Portal, postupujte podle následujících kroků: 

1. Přihlaste se na [Azure Portal](https://portal.azure.com). 
1. Přejít na [virtuální počítače s SQL Server](manage-sql-vm-portal.md).
1. Ze seznamu vyberte svůj virtuální počítač SQL Server. Pokud zde SQL Server váš virtuální počítač, pravděpodobně nebyl zaregistrován s rozšířením agenta SQL IaaS. 
1. Zobrazit hodnotu v části **stav** Pokud  je stav **úspěšný**, je virtuální počítač SQL Server zaregistrovaný s rozšířením agenta SQL IaaS úspěšně. 

   ![Ověřit stav pomocí registrace SQL RP](./media/sql-agent-extension-manually-register-single-vm/verify-registration-status.png)

### <a name="command-line"></a>Příkazový řádek

Ověřte aktuální SQL Server stav registrace virtuálního počítače pomocí rozhraní příkazového řádku Azure CLI nebo Azure PowerShell. `ProvisioningState` zobrazí se `Succeeded` , zda byla registrace úspěšná. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Pokud chcete ověřit stav registrace pomocí Azure CLI, spusťte následující fragment kódu:  

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Chcete-li ověřit stav registrace pomocí Azure PowerShell, spusťte následující fragment kódu:

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Chyba indikuje, že virtuální počítač SQL Server nebyl s rozšířením zaregistrován. 


## <a name="unregister-from-extension"></a>Zrušit registraci v rozšíření

Pokud chcete zrušit registraci SQL Serverho virtuálního počítače pomocí rozšíření agenta SQL IaaS, odstraňte *prostředek* virtuálního počítače sql pomocí Azure Portal nebo rozhraní příkazového řádku Azure. Odstranění *prostředku* virtuálního počítače SQL neodstraní virtuální počítač SQL Server. Buďte ale opatrní a postupujte opatrně, protože je možné při pokusu o odebrání *prostředku* neúmyslně odstranit virtuální počítač. 

Zrušení registrace virtuálního počítače SQL s rozšířením agenta SQL IaaS je nezbytné pro přechod z úplného režimu správy. 

### <a name="azure-portal"></a>portál Azure

Chcete-li zrušit registraci SQL Serverho virtuálního počítače z rozšíření pomocí Azure Portal, postupujte podle následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte k prostředku virtuálního počítače SQL. 
  
   ![Prostředek virtuálních počítačů SQL](./media/sql-agent-extension-manually-register-single-vm/sql-vm-manage.png)

1. Vyberte **Odstranit**. 

   ![Výběr odstranit v horním navigačním panelu](./media/sql-agent-extension-manually-register-single-vm/delete-sql-vm-resource.png)

1. Zadejte název virtuálního počítače SQL a **zrušte zaškrtnutí políčka u virtuálního počítače**.

   ![Zrušte zaškrtnutí políčka virtuální počítač, aby nedošlo k odstranění skutečného virtuálního počítače, a pak vyberte odstranit, abyste mohli pokračovat v odstraňování prostředku virtuálního počítače SQL.](./media/sql-agent-extension-manually-register-single-vm/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Nepovedlo se zrušit zaškrtnutí políčka u názvu virtuálního počítače, aby se virtuální počítač zcela *odstranil* . Zrušením zaškrtnutí políčka zrušíte registraci SQL Server virtuálního počítače z rozšíření, ale *neodstraníte skutečný virtuální počítač*. 

1. Vyberte **Odstranit** a potvrďte odstranění *prostředku* virtuálního počítače SQL, nikoli SQL Server virtuálního počítače. 

### <a name="command-line"></a>Příkazový řádek

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete zrušit registraci SQL Server virtuálního počítače z rozšíření pomocí Azure CLI, použijte příkaz [AZ SQL VM Delete](/cli/azure/sql/vm#az-sql-vm-delete) . Tím se odebere *prostředek* virtuálního počítače SQL Server, ale virtuální počítač se neodstraní. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete zrušit registraci SQL Server virtuálního počítače z rozšíření pomocí Azure PowerShell, použijte příkaz [Remove-AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm). Tím se odebere *prostředek* virtuálního počítače SQL Server, ale virtuální počítač se neodstraní. 

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
