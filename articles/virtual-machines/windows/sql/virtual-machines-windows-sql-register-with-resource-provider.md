---
title: Registrace SQL Server virtuálního počítače v Azure pomocí poskytovatele prostředků virtuálního počítače SQL | Microsoft Docs
description: Zaregistrujte svůj SQL Server virtuální počítač pomocí poskytovatele prostředků virtuálního počítače SQL pro zlepšení spravovatelnosti.
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
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 95a167cbc8fde4488e3f46ffd850f0619cf1b651
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305875"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Registrace SQL Server virtuálního počítače v Azure pomocí poskytovatele prostředků virtuálního počítače SQL

Tento článek popisuje, jak zaregistrovat virtuální počítač s SQL Server Azure pomocí poskytovatele prostředků virtuálního počítače SQL. 

Nasazení image virtuálního počítače s SQL Server pomocí Azure Portal automaticky registruje SQL Server virtuálního počítače poskytovatelem prostředků. Pokud se ale rozhodnete vlastní instalaci SQL Server na virtuálním počítači Azure místo výběru image z Azure Marketplace, měli byste svůj virtuální počítač s SQL Server zaregistrovat do poskytovatele prostředků ještě dnes pro:

-  **Dodržování předpisů** – zákazníci, kteří používají [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) , musí při použití zvýhodněné hybridní využití Azure nahlásit společnosti Microsoft, musí se zaregistrovat u poskytovatele prostředků virtuálního počítače SQL. 

-  **Výhody funkcí** – registrace virtuálního počítače s SQL Server u poskytovatele prostředků odemkne [Automatické opravy](virtual-machines-windows-sql-automated-patching.md), možnosti [automatického zálohování](virtual-machines-windows-sql-automated-backup-v2.md), monitorování a správy a také flexibilitu licencování a [](virtual-machines-windows-sql-ahb.md) [edice](virtual-machines-windows-sql-change-edition.md) . Dříve byly dostupné jenom pro SQL Server imagí virtuálních počítačů z Azure Marketplace.

Samoobslužná instalace SQL Server na virtuálním počítači Azure nebo zřizování virtuálního počítače Azure z vlastního virtuálního pevného disku s SQL Server je kompatibilní prostřednictvím Zvýhodněné hybridní využití Azure pro SQL Server s podmínkou, že zákazníci nahlásí, že při registraci pomocí prostředku virtuálního počítače SQL budou používat Microsoft. zprostředkovatele. 

Aby bylo možné využít poskytovatele prostředků virtuálního počítače SQL, musíte také zaregistrovat poskytovatele prostředků virtuálního počítače SQL s vaším předplatným. Můžete to provést pomocí Azure Portal, Azure CLI a PowerShellu. 

## <a name="prerequisites"></a>Požadavky

Pokud chcete zaregistrovat SQL Server virtuální počítač s poskytovatelem prostředků, budete potřebovat následující: 

- [Předplatného Azure](https://azure.microsoft.com/free/).
- [SQL Server virtuální počítač](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- Rozhraní příkazového [](/powershell/azure/new-azureps-module-az) [řádku Azure](/cli/azure/install-azure-cli) a PowerShellu 

## <a name="register-with-sql-vm-resource-provider"></a>Zaregistrovat u poskytovatele prostředků virtuálního počítače SQL
Pokud je na virtuálním počítači už nainstalovaná [přípona SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) , pak registrace pomocí poskytovatele prostředků SQL VM jednoduše vytvoří prostředek metadat typu Microsoft. SqlVirtualMachine/SqlVirtualMachines. Níže je fragment kódu pro registraci ve zprostředkovateli prostředků SQL VM, pokud je rozšíření SQL IaaS již na virtuálním počítači nainstalováno. Při registraci s poskytovatelem prostředků SQL VM je nutné zadat typ licence SQL Server, který je požadován buď jako "PAYG" nebo "AHUB". 

Pomocí PowerShellu Zaregistrujte SQL Server virtuální počítač s následujícím fragmentem kódu:

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'}  
  
  ```

Pokud na virtuálním počítači není nainstalované rozšíření SQL IaaS, můžete se zaregistrovat u poskytovatele prostředků virtuálního počítače SQL zadáním režimu zjednodušené správy SQL. V režimu zjednodušené správy SQL bude poskytovatel prostředků SQL VM automaticky instalovat rozšíření SQL IaaS v [jednoduchém režimu](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) a ověřit metadata SQL Server instance; Služba SQL Server službu nebude restartována. Při registraci s poskytovatelem prostředků SQL VM je nutné zadat typ licence SQL Server, který je požadován buď jako "PAYG" nebo "AHUB". 

Zaregistrujte SQL Server virtuální počítač v režimu prosté správy SQL pomocí PowerShellu s následujícím fragmentem kódu:

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

Registrace u poskytovatele prostředků virtuálního počítače SQL v [odlehčeném režimu](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) zajistí dodržování předpisů a umožní flexibilní licencování i aktualizace SQL Server edice na pracovišti. Instance clusteru s podporou převzetí služeb při selhání a nasazení s více instancemi se dají zaregistrovat s poskytovatelem prostředků SQL VM jenom ve zjednodušeném režimu. Můžete postupovat podle pokynů uvedených v Azure Portal k upgradu na [úplný režim](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation) a povolení úplné sady funkcí správy pomocí SQL Server restartování kdykoli. 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>Registrace SQL Server 2008/R2 na virtuálních počítačích s Windows Serverem 2008

SQL Server 2008 a 2008 R2 nainstalované na Windows serveru 2008 je možné zaregistrovat u prostředku virtuálního počítače SQL, který poskytuje [](virtual-machines-windows-sql-server-agent-extension.md) v režimu neagent. Tato možnost zaručuje dodržování předpisů a umožňuje monitorovat SQL Server virtuálním počítačem v Azure Portal s omezenými funkcemi.

Následující tabulka uvádí přijatelné hodnoty pro parametry zadané během registrace:

| Parametr | Přijatelné hodnoty                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'`, nebo`'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` Nebo `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


K registraci SQL Server 2008 nebo 2008 R2 v instanci Windows Server 2008 použijte následující fragment kódu prostředí PowerShell:  

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

## <a name="verify-registration-status"></a>Ověřit stav registrace
Můžete ověřit, jestli váš SQL Server už je zaregistrovaný u poskytovatele prostředků virtuálního počítače SQL pomocí Azure Portal, Azure CLI nebo PowerShellu. 

### <a name="azure-portal"></a>portál Azure 
Pokud chcete ověřit stav registrace pomocí Azure Portal, udělejte toto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
1. Přejděte na [virtuální počítače SQL](virtual-machines-windows-sql-manage-portal.md).
1. Ze seznamu vyberte svůj virtuální počítač SQL Server. Pokud zde SQL Server váš virtuální počítač, je nejspíš SQL Server virtuální počítač nebyl zaregistrován u poskytovatele prostředků virtuálního počítače SQL. 
1. Podívejte se na hodnotu `Status`v části. V `Status = Succeeded`případě, že se virtuální počítač s SQL Server zaregistroval u poskytovatele prostředků virtuálního počítače SQL úspěšně. 

    ![Ověřit stav pomocí registrace SQL RP](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="az-cli"></a>AZ CLI

Pomocí následujícího příkazu AZ CLI ověřte aktuální stav registrace virtuálního počítače SQL Server. `ProvisioningState`zobrazí se, zda byla registrace úspěšná. `Succeeded` 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


### <a name="powershell"></a>PowerShell

Ověřte aktuální SQL Server stav registrace virtuálního počítače pomocí následující rutiny prostředí PowerShell. `ProvisioningState`zobrazí se, zda byla registrace úspěšná. `Succeeded` 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
Chyba naznačuje, že virtuální počítač SQL Server nebyl zaregistrován u poskytovatele prostředků. 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>Registrace poskytovatele prostředků virtuálního počítače SQL s předplatným 

Pokud chcete zaregistrovat SQL Server virtuální počítač s poskytovatelem prostředků virtuálního počítače SQL, musíte zaregistrovat poskytovatele prostředků k vašemu předplatnému. Můžete to udělat pomocí Azure Portal nebo rozhraní příkazového řádku Azure CLI.

### <a name="azure-portal"></a>portál Azure

Následující kroky zaregistrují poskytovatele prostředků virtuálního počítače SQL do svého předplatného Azure pomocí Azure Portal. 

1. Otevřete Azure Portal a přejděte do části **všechny služby**. 
1. Přejděte na **předplatná** a vyberte předplatné, které vás zajímá.  
1. Na stránce **předplatná** přejděte na **poskytovatelé prostředků**. 
1. Zadáním `sql` filtru otevřete poskytovatele prostředků souvisejících s SQL. 
1. V závislosti na požadované akci vyberte možnost *Registrovat*, *znovu zaregistrovat*nebo *zrušit registraci* pro poskytovatele **Microsoft. SqlVirtualMachine** . 

   ![Úprava zprostředkovatele](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="az-cli"></a>AZ CLI
Následující fragment kódu zaregistruje poskytovatele prostředků virtuálního počítače SQL do vašeho předplatného Azure. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="powershell"></a>PowerShell

Následující fragment kódu PowerShellu zaregistruje poskytovatele prostředků virtuálního počítače SQL do vašeho předplatného Azure.

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Poznámky

 - Poskytovatel prostředků virtuálního počítače SQL podporuje jenom SQL Server virtuální počítače nasazené pomocí Správce prostředků. SQL Server virtuální počítače nasazené pomocí klasického modelu se nepodporují. 
 - Poskytovatel prostředků virtuálního počítače SQL podporuje jenom SQL Server virtuální počítače nasazené ve veřejném cloudu. Nasazení do privátního cloudu nebo do cloudu pro státní správu se nepodporuje. 
 
## <a name="frequently-asked-questions"></a>Nejčastější dotazy 

**Mám zaregistrovat SQL Server virtuální počítač zřízený z image SQL na Azure Marketplace?**

Ne. Microsoft autoregistruje virtuální počítače zřízené z imagí SQL na Azure Marketplace. Registrace u poskytovatele prostředků virtuálního počítače SQL se vyžaduje jenom v případě, že se virtuální počítač nezřídil z imagí SQL na webu Azure Marketplace a SQL Server byl samoobslužně nainstalován.

**Je poskytovatel prostředků virtuálního počítače SQL dostupný pro všechny zákazníky?** 

Ano. Zákazníci by měli zaregistrovat svůj SQL Server virtuální počítač s poskytovatelem prostředků SQL VM, pokud nepoužívali image SQL Server z webu Azure Marketplace a samoobslužně nainstalovaného SQL Server nebo si vlastní virtuální pevný disk. Virtuální počítače vlastněné všemi typy předplatných (Direct, EA a CSP) se můžou registrovat u poskytovatele prostředků SQL VM.

**Je potřeba se zaregistrovat u poskytovatele prostředků virtuálního počítače SQL, pokud už má SQL Server virtuální počítač nainstalované rozšíření SQL IaaS?**

Pokud je váš virtuální počítač s SQL Server samoobslužně nainstalován, není zajištěn z imagí SQL na webu Azure Marketplace, měli byste se zaregistrovat u poskytovatele prostředků virtuálního počítače SQL i v případě, že jste nainstalovali rozšíření SQL IaaS. Registrace pomocí poskytovatele prostředků virtuálního počítače SQL vytvoří nový prostředek typu Microsoft. SqlVirtualMachines. Instalace rozšíření SQL IaaS nevytváří tento prostředek.

**Jaký je výchozí režim správy SQL při registraci ve zprostředkovateli prostředků SQL VM?**

Výchozí režim správy SQL při registraci pomocí SQL VM RP je _plný_. Pokud při registraci u poskytovatele prostředků virtuálního počítače SQL není nastavená vlastnost SQL Management, nastaví se tento režim jako plná spravovatelnost. Je nutné, aby na virtuálním počítači byla nainstalovaná přípona SQL IaaS, která se zaregistruje u poskytovatele prostředků virtuálního počítače SQL v režimu úplné správy.

**Jaké jsou požadavky na registraci u poskytovatele prostředků virtuálních počítačů SQL?**

Neexistují žádné požadavky k registraci u poskytovatele prostředků virtuálního počítače SQL v režimu prostého režimu nebo v režimu No-agent. Požadavky na registraci u poskytovatele prostředků virtuálního počítače SQL v plném režimu mají na virtuálním počítači nainstalovaný IaaS rozšíření SQL.

**Můžu se zaregistrovat k poskytovateli prostředků SQL VM, pokud nemám na virtuálním počítači nainstalovanou příponu SQL IaaS?**

Ano, pokud nemáte na virtuálním počítači nainstalovanou příponu SQL IaaS, můžete se zaregistrovat u poskytovatele prostředků virtuálních počítačů SQL v režimu zjednodušené správy. Ve zjednodušeném režimu bude poskytovatel prostředků SQL VM použít konzolovou aplikaci k ověření verze a edice instance SQL. Konzolová aplikace se po ověření, že na virtuálním počítači běží aspoň jedna instance SQL, vypne. Registrace u poskytovatele prostředků virtuálních počítačů SQL v odlehčeném režimu se nerestartuje SQL Server a na virtuálním počítači se nevytvoří agent.

**Bude se registrovat pomocí poskytovatele prostředků virtuálního počítače SQL nainstalovat na mém virtuálním počítači agenta?**

Ne. Registrace pomocí poskytovatele prostředků virtuálního počítače SQL vytvoří jenom nový prostředek metadat a nenainstaluje na virtuální počítač agenta. Rozšíření SQL IaaS je potřeba jenom k tomu, aby se povolila plná Správa, takže upgrade režimu spravovatelnosti z jednoduchého na plný bude instalovat rozšíření SQL IaaS a restartuje se SQL Server.

**Provede se registrace pomocí poskytovatele prostředků virtuálního počítače SQL SQL Server na mém virtuálním počítači?**

Ne. Při registraci u poskytovatele prostředků virtuálního počítače SQL se vytvoří jenom nový prostředek metadat a SQL Server se na virtuálním počítači nerestartuje. Restartování SQL Server je potřeba jenom k instalaci rozšíření SQL IaaS; a rozšíření SQL IaaS je potřeba k tomu, aby se povolila plná Správa. Upgrade režimu spravovatelnosti z jednoduchého na úplné bude instalovat rozšíření SQL IaaS a restartuje se SQL Server.

**Jaký je rozdíl mezi režimy jednoduchého a bez agenta správy při registraci u poskytovatele prostředků virtuálního počítače SQL?** 

Režim správy No-Agent je k dispozici pouze pro SQL Server 2008/R2 v systému Windows Server 2008; a je to jediný dostupný režim správy pro tyto verze. Pro všechny ostatní verze SQL Server jsou dostupné tyto dva režimy spravovatelnosti: zjednodušená a plná. Režim No-agent vyžaduje, SQL Server vlastnosti verze a edice, které zákazník nastaví; odlehčený režim dotazuje virtuální počítač na nalezení verze a edice instance SQL.

**Můžu se pomocí Azure CLI zaregistrovat k poskytovateli prostředků SQL VM v režimu Lightweight nebo No Agent?**

Ne. Vlastnost režimu správy SQL je k dispozici pouze při registraci u poskytovatele prostředků virtuálního počítače SQL s Azure PowerShell. Rozhraní příkazového řádku Azure nepodporuje nastavení vlastnosti spravovatelnosti SQL a vždy se registruje u poskytovatele prostředků virtuálního počítače SQL ve výchozím režimu s možností úplné správy.

**Můžu se zaregistrovat s poskytovatelem prostředků SQL VM bez zadání typu licence SQL?**

Ne. Typ licence SQL není volitelnou vlastností při registraci pomocí SQL VM RP. Je nutné nastavit typ licence SQL jako PAYG nebo AHUB při registraci pomocí poskytovatele prostředků virtuálního počítače SQL ve všech režimech spravovatelnosti (No-agent, Lightweight a Full) pomocí AZ CLI a PowerShellu.

**Můžu upgradovat rozšíření SQL IaaS z režimu No-Agent na režim Full?**

Ne. Upgrade režimu správy SQL na úplný nebo odlehčený není pro režim bez agenta k dispozici. Toto je technické omezení Windows serveru 2008.

**Můžu upgradovat rozšíření SQL IaaS z prostého režimu na celý režim?**

Ano. Upgrade režimu správy SQL z odlehčeného na plný se podporuje přes PowerShell nebo Azure Portal; a vyžaduje restartování SQL Server.

**Je možné IaaS rozšíření SQL serveru z úplného režimu na režimy No-agent nebo zjednodušené správy?**

Ne. Režim spravovatelnosti rozšíření SQL IaaS není podporován. Režim správy SQL nelze downgradovat z úplného režimu do režimu Lightweight nebo No-agent. a nedá se downgradovat z prostého režimu na režim bez agenta. Chcete-li změnit režim spravovatelnosti z možnosti úplné správy; Odeberte rozšíření SQL IaaS; vyřaďte prostředek Micorsoft. SqlVirtualMachine a znovu zaregistrujte SQL Server virtuálního počítače pomocí poskytovatele prostředků virtuálního počítače SQL.

**Můžu se zaregistrovat k poskytovateli prostředků SQL VM z Azure Portal?**

Ne. Registrace k poskytovateli prostředků virtuálního počítače SQL není k dispozici v Azure Portal. Registrace u poskytovatele prostředků virtuálního počítače SQL v plném režimu spravovatelnosti se podporuje pomocí Azure CLI nebo PowerShellu. a registrace u poskytovatele prostředků virtuálního počítače SQL v zjednodušeném nebo nespravovaných režimech bez agenta podporuje jenom rozhraní API pro Azure PowerShell.

**Je možné zaregistrovat virtuální počítač s poskytovatelem prostředků SQL VM před tím, než se SQL Server nainstaluje?**

Ne. Aby se virtuální počítač mohl úspěšně zaregistrovat u poskytovatele prostředků virtuálního počítače SQL, musí mít aspoň jednu instanci SQL. Pokud na virtuálním počítači není žádná instance SQL, nový prostředek obraťte. SqlVirtualMachine bude ve stavu selhání.

**Můžu zaregistrovat virtuální počítač s prostředkem SQL VM, pokud existuje víc instancí SQL?**

Ano. Poskytovatel prostředků virtuálního počítače SQL bude registrovat jenom jednu instanci SQL. Poskytovatel prostředků virtuálního počítače SQL bude v případě více instancí registrovat výchozí instanci SQL. Pokud neexistuje žádná výchozí instance, je podporována pouze registrace v režimu prostého režimu. Aby bylo možné provést upgrade z prostého režimu na režim spravovatelnosti, musí existovat buď výchozí instance SQL, nebo by měl virtuální počítač obsahovat pouze jednu pojmenovanou instanci SQL.

**Můžu zaregistrovat SQL Server instanci clusteru s podporou převzetí služeb při selhání s poskytovatelem prostředků SQL VM?**

Ano. Instance SQL FCI na virtuálním počítači Azure je možné zaregistrovat u poskytovatele prostředků virtuálních počítačů SQL v odlehčeném režimu. Instance SQL FCI však nelze upgradovat na režim úplné správy.

**Můžu zaregistrovat virtuální počítač s SQL VM RP, pokud je nakonfigurovaná Skupina dostupnosti Always ON?**

Ano. Pro registraci instance SQL Server na virtuálním počítači Azure s poskytovatelem prostředků virtuálního počítače SQL se neexistují žádná omezení, pokud se účastní konfigurace skupiny dostupnosti Always ON.

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících článcích: 

* [Přehled SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Nejčastější dotazy k SQL Server na VIRTUÁLNÍm počítači s Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server doprovodné materiály k cenám pro virtuální počítače s Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Zpráva k vydání verze Windows VM SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)
