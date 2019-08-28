---
title: Registrace virtuálního počítače s SQL Server v Azure pomocí poskytovatele prostředků virtuálního počítače SQL | Microsoft Docs
description: Zaregistrujte svůj SQL Server virtuální počítač pomocí poskytovatele prostředků virtuálního počítače SQL pro zlepšení spravovatelnosti.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: a4e217ce3fcfae0f7d103c545ff385f2dffe582d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100495"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Registrace virtuálního počítače s SQL Server v Azure pomocí poskytovatele prostředků virtuálního počítače SQL

Tento článek popisuje, jak zaregistrovat SQL Server virtuální počítač v Azure pomocí poskytovatele prostředků virtuálního počítače SQL. 

Nasazení Azure Marketplace image SQL Server virtuálního počítače pomocí Azure Portal automaticky registruje SQL Server virtuální počítač s poskytovatelem prostředků. Pokud se rozhodnete vlastní instalaci SQL Server na virtuálním počítači Azure místo výběru image z Azure Marketplace nebo pokud zřídíte virtuální počítač Azure z vlastního virtuálního pevného disku s SQL Server, měli byste zaregistrovat SQL Server virtuální počítač s poskytovatelem prostředků pro :

- **Dodržování předpisů**: Podle podmínek produktu společnosti Microsoft musí zákazníci informovat společnost Microsoft, když používají [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). V takovém případě se musí zaregistrovat u poskytovatele prostředků virtuálního počítače SQL. 

- **Výhody funkcí**: Registrace SQL Server virtuálního počítače pomocí poskytovatele prostředků odemkne [automatizované opravy](virtual-machines-windows-sql-automated-patching.md), [automatizované zálohování](virtual-machines-windows-sql-automated-backup-v2.md)a možnosti monitorování a správy. Také odemkne licencování [](virtual-machines-windows-sql-ahb.md) a flexibilitu [edice](virtual-machines-windows-sql-change-edition.md) . Dříve byly tyto funkce k dispozici pouze pro SQL Server imagí virtuálních počítačů z Azure Marketplace.

Aby bylo možné využít poskytovatele prostředků virtuálního počítače SQL, musíte také zaregistrovat poskytovatele prostředků virtuálního počítače SQL s vaším předplatným. To můžete provést pomocí Azure Portal, rozhraní příkazového řádku Azure nebo PowerShellu. 

## <a name="prerequisites"></a>Požadavky

Pokud chcete zaregistrovat SQL Server virtuální počítač s poskytovatelem prostředků, budete potřebovat následující: 

- [Předplatného Azure](https://azure.microsoft.com/free/).
- [SQL Server virtuální počítač](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) a [PowerShellu](/powershell/azure/new-azureps-module-az). 


## <a name="register-with-sql-vm-resource-provider"></a>Zaregistrovat u poskytovatele prostředků virtuálního počítače SQL
Pokud na virtuálním počítači není nainstalované [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) , můžete se zaregistrovat s poskytovatelem prostředků SQL VM zadáním režimu zjednodušené správy SQL. V režimu zjednodušené správy SQL bude poskytovatel prostředků SQL VM automaticky instalovat rozšíření SQL IaaS v [jednoduchém režimu](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) a ověřit metadata SQL Server instance; Služba SQL Server službu nebude restartována. Při registraci s poskytovatelem prostředků SQL VM je nutné zadat typ licence SQL Server, který je požadován buď jako "PAYG" nebo "AHUB".

Registrace u poskytovatele prostředků virtuálního počítače SQL v [odlehčeném režimu](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) zajistí dodržování předpisů a umožní flexibilní licencování i aktualizace SQL Server edice na pracovišti. Instance clusteru s podporou převzetí služeb při selhání a nasazení s více instancemi se dají zaregistrovat s poskytovatelem prostředků SQL VM jenom ve zjednodušeném režimu. Podle pokynů v Azure Portal můžete upgradovat na [úplný režim](virtual-machines-windows-sql-server-agent-extension.md#install-in-full-mode) a povolit kompletní sadu funkcí spravovatelnosti pomocí SQL Server restartování kdykoli. 


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Pokud je na virtuálním počítači již nainstalováno rozšíření SQL Server IaaS, použijte následující fragment kódu pro registraci u poskytovatele prostředků SQL VM. Musíte zadat typ licence SQL Server, kterou chcete při registraci u poskytovatele prostředků SQL VM použít: průběžné platby (`PAYG`) nebo zvýhodněné hybridní využití Azure (`AHUB`). 

Pomocí následujícího fragmentu kódu prostředí PowerShell Zaregistrujte SQL Server virtuální počítač:

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Pro placené edice (Enterprise nebo Standard):

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type AHUB 

  ```

Bezplatné edice (Developer, web nebo Express):

  ```azurecli-interactive
  # Register Developer, Web, or Express self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```
---

Pokud je na virtuálním počítači už nainstalovaná přípona SQL IaaS, pak registrace pomocí poskytovatele prostředků SQL VM jednoduše vytvoří prostředek metadat typu Microsoft. SqlVirtualMachine/SqlVirtualMachines. Níže je fragment kódu pro registraci ve zprostředkovateli prostředků SQL VM, pokud je rozšíření SQL IaaS již na virtuálním počítači nainstalováno. Při registraci s poskytovatelem prostředků SQL VM je nutné zadat typ licence SQL Server, který je požadován buď jako "PAYG" nebo "AHUB".

  ```powershell-interactive
  # Get the existing  Compute VM
   $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
   # Register with SQL VM resource provider
   New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'}
  ```


## <a name="register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms"></a>Registrace SQL Server 2008 nebo 2008 R2 na virtuálních počítačích s Windows Serverem 2008

SQL Server 2008 a 2008 R2 nainstalované v systému Windows Server 2008 je možné zaregistrovat u poskytovatele prostředků SQL VM v režimu [No-agent](virtual-machines-windows-sql-server-agent-extension.md) . Tato možnost zaručuje dodržování předpisů a umožňuje monitorovat SQL Server virtuálním počítačem v Azure Portal s omezenými funkcemi.

Následující tabulka uvádí přijatelné hodnoty pro parametry zadané během registrace:

| Parametr | Přijatelné hodnoty                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `AHUB` Nebo `PAYG`                    |
| **sqlImageOffer**  | `SQL2008-WS2008` Nebo `SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


K registraci instance SQL Server 2008 nebo 2008 R2 v instanci Windows Server 2008 použijte následující příkaz PowerShellu nebo AZ CLI Code fragment:  

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type AHUB --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```

---

## <a name="verify-registration-status"></a>Ověřit stav registrace
Můžete ověřit, jestli váš virtuální počítač s SQL Server už je zaregistrovaný u poskytovatele prostředků SQL VM pomocí Azure Portal, Azure CLI nebo PowerShellu. 

### <a name="azure-portal"></a>portál Azure 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
1. Přejít na [virtuální počítače s SQL Server](virtual-machines-windows-sql-manage-portal.md).
1. Ze seznamu vyberte svůj virtuální počítač SQL Server. Pokud zde SQL Server virtuální počítač, pravděpodobně není zaregistrovaný u poskytovatele prostředků virtuálního počítače SQL. 
1. Zobrazit hodnotu v části **stav** Pokud je stav **úspěšný**, byl virtuální počítač SQL Server zaregistrován u poskytovatele prostředků virtuálního počítače SQL úspěšně. 

![Ověřit stav pomocí registrace SQL RP](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>Příkazový řádek

Ověřte aktuální SQL Server stav registrace virtuálního počítače pomocí AZ CLI nebo PowerShellu. `ProvisioningState`zobrazí se, zda byla registrace úspěšná. `Succeeded` 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> `
  -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```

---

Chyba naznačuje, že virtuální počítač SQL Server nebyl zaregistrován u poskytovatele prostředků. 

## <a name="register-the-sql-vm-resource-provider-with-a-subscription"></a>Registrace poskytovatele prostředků virtuálního počítače SQL pomocí předplatného 

Pokud chcete zaregistrovat SQL Server virtuální počítač s poskytovatelem prostředků virtuálního počítače SQL, musíte zaregistrovat poskytovatele prostředků u svého předplatného. Můžete to udělat pomocí Azure Portal, rozhraní příkazového řádku Azure nebo PowerShellu.

### <a name="azure-portal"></a>portál Azure

1. Otevřete Azure Portal a pokračujte na **všechny služby**. 
1. Přejít na **předplatná** a vyberte předplatné, které vás zajímá.  
1. Na stránce **předplatná** klikněte na **poskytovatelé prostředků**. 
1. Zadejte **SQL** do filtru pro uvedení zprostředkovatelů prostředků souvisejících s SQL. 
1. V závislosti na požadované akci vyberte **Registrovat**, **znovu zaregistrovat**nebo **zrušit registraci** pro poskytovatele **Microsoft. SqlVirtualMachine** . 

![Úprava zprostředkovatele](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>Příkazový řádek

Zaregistrujte poskytovatele prostředků virtuálního počítače SQL do svého předplatného Azure pomocí AZ CLI nebo PowerShellu. 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
Následující fragment kódu zaregistruje poskytovatele prostředků virtuálního počítače SQL do vašeho předplatného Azure. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Poznámky

- Poskytovatel prostředků virtuálního počítače SQL podporuje jenom SQL Server virtuální počítače nasazené prostřednictvím Azure Resource Manager. SQL Server virtuální počítače nasazené prostřednictvím klasického modelu se nepodporují. 
- Poskytovatel prostředků virtuálního počítače SQL podporuje jenom SQL Server virtuální počítače nasazené ve veřejném cloudu. Nasazení do privátního nebo státního cloudu se nepodporuje. 
 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy 

**Mám zaregistrovat SQL Server virtuální počítač zřízený z SQL Server Image v Azure Marketplace?**

Ne. Microsoft automaticky registruje virtuální počítače zřízené z SQL Server imagí v Azure Marketplace. Registrace u poskytovatele prostředků virtuálního počítače SQL se vyžaduje jenom v případě, že se virtuální počítač nezřídil z SQL Server imagí v Azure Marketplace a SQL Server byl samoobslužně nainstalován.

**Je poskytovatel prostředků virtuálního počítače SQL dostupný pro všechny zákazníky?** 

Ano. Zákazníci by měli zaregistrovat své SQL Server virtuální počítače s poskytovatelem prostředků SQL VM, pokud nepoužívali image SQL Server z Azure Marketplace a místo toho SQL Server, nebo pokud přenesli svůj vlastní VHD. Virtuální počítače vlastněné všemi typy předplatných (Direct, smlouva Enterprise a Cloud Solution Provider) se můžou registrovat u poskytovatele prostředků virtuálního počítače SQL.

**Mám se zaregistrovat u poskytovatele prostředků virtuálního počítače SQL, pokud už má SQL Server virtuální počítač rozšíření SQL Server IaaS nainstalované?**

Pokud je váš virtuální počítač SQL Server samoobslužně nainstalován a není zřízený z SQL Server imagí v Azure Marketplace, měli byste se zaregistrovat u poskytovatele prostředků virtuálního počítače SQL i v případě, že jste nainstalovali rozšíření SQL Server IaaS. Registrace u poskytovatele prostředků virtuálního počítače SQL vytvoří nový prostředek typu Microsoft. SqlVirtualMachines. Instalace rozšíření SQL Server IaaS nevytváří tento prostředek.

**Jaký je výchozí režim správy při registraci u poskytovatele prostředků virtuálního počítače SQL?**

Výchozí režim správy při registraci u poskytovatele prostředků virtuálního počítače SQL je *plný*. Pokud není nastavena vlastnost Management SQL Server při registraci u poskytovatele prostředků virtuálního počítače SQL, režim se nastaví jako plná Správa. Aby bylo na virtuálním počítači nainstalované rozšíření SQL Server IaaS, je předpokladem pro registraci u poskytovatele prostředků virtuálního počítače SQL v režimu úplné správy.

**Jaké jsou požadavky na registraci u poskytovatele prostředků virtuálních počítačů SQL?**

Neexistují žádné požadavky k registraci u poskytovatele prostředků virtuálního počítače SQL v režimu prostého režimu nebo v režimu No-agent. Předpokladem pro registraci u poskytovatele prostředků virtuálního počítače SQL v plném režimu je rozšíření SQL Server IaaS na virtuálním počítači nainstalované.

**Můžu se zaregistrovat u poskytovatele prostředků virtuálního počítače SQL, pokud nemám na virtuálním počítači nainstalovanou příponu SQL Server IaaS?**

Ano, pokud na virtuálním počítači nemáte nainstalované rozšíření SQL Server IaaS, můžete se zaregistrovat u poskytovatele prostředků virtuálního počítače SQL v režimu zjednodušené správy. V odlehčeném režimu poskytovatel prostředků virtuálního počítače SQL použije konzolovou aplikaci k ověření verze a edice instance SQL Server. 

Výchozí režim správy SQL při registraci u poskytovatele prostředků virtuálního počítače SQL je _plný_. Pokud při registraci u poskytovatele prostředků virtuálního počítače SQL není nastavená vlastnost SQL Management, nastaví se tento režim jako plná spravovatelnost. Je nutné, aby na virtuálním počítači byla nainstalovaná přípona SQL IaaS, která se zaregistruje u poskytovatele prostředků virtuálního počítače SQL v režimu úplné správy.

**Bude se registrovat u poskytovatele prostředků virtuálního počítače SQL instalace agenta na mém virtuálním počítači?**

Ne. Při registraci u poskytovatele prostředků virtuálního počítače SQL se vytvoří jenom nový prostředek metadat. Neinstaluje do virtuálního počítače agenta.

Rozšíření SQL Server IaaS je potřeba jenom k tomu, aby se povolila plná Správa. Upgrade režimu spravovatelnosti z jednoduchého na úplné bude instalovat rozšíření SQL Server IaaS a restartuje se SQL Server.

**Provede se registrace pomocí SQL Server poskytovatele prostředků virtuálního počítače SQL Server restartování na mém virtuálním počítači?**

Ne. Při registraci u poskytovatele prostředků virtuálního počítače SQL se vytvoří jenom nový prostředek metadat. Nerestartuje SQL Server na virtuálním počítači. 

Restartování SQL Server je potřeba jenom k instalaci rozšíření SQL Server IaaS. A rozšíření SQL Server IaaS je potřeba k tomu, aby se povolila plná Správa. Upgrade režimu spravovatelnosti z jednoduchého na úplné bude instalovat rozšíření SQL Server IaaS a restartuje se SQL Server.

**Jaký je rozdíl mezi režimy jednoduchého a bez agenta správy při registraci u poskytovatele prostředků virtuálního počítače SQL?** 

Režim správy No-Agent je k dispozici pouze pro SQL Server 2008 a SQL Server 2008 R2 v systému Windows Server 2008. Pro tyto verze je to jediný dostupný režim správy. Pro všechny ostatní verze SQL Server jsou dva dostupné režimy spravovatelnosti zjednodušené a úplné. 

Režim No-agent vyžaduje, SQL Server vlastnosti verze a edice, které zákazník nastaví. Odlehčený režim dotazuje virtuální počítač na nalezení verze a edice instance SQL Server.

**Můžu se zaregistrovat k poskytovateli prostředků SQL VM v odlehčeném nebo bezagentovém režimu pomocí Azure CLI?**

Ne. Vlastnost režim správy je k dispozici pouze při registraci u poskytovatele prostředků virtuálního počítače SQL pomocí Azure PowerShell. Rozhraní příkazového řádku Azure nepodporuje nastavení vlastnosti spravovatelnosti SQL Server. Vždy se registruje u poskytovatele prostředků virtuálního počítače SQL ve výchozím režimu, plná Správa.

**Můžu se zaregistrovat u poskytovatele prostředků SQL VM bez zadání typu licence SQL Server?**

Ne. Typ licence SQL Server není volitelnou vlastností při registraci u poskytovatele prostředků virtuálního počítače SQL. Musíte nastavit typ licence SQL Server jako průběžné platby nebo Zvýhodněné hybridní využití Azure při registraci u poskytovatele prostředků virtuálního počítače SQL ve všech režimech spravovatelnosti (No-agent, Lightweight a Full) pomocí rozhraní příkazového řádku Azure a PowerShellu.

**Můžu upgradovat rozšíření SQL Server IaaS z režimu No-Agent na režim Full?**

Ne. Upgrade režimu spravovatelnosti na úplný nebo lehký není pro režim bez agenta k dispozici. Toto je technické omezení Windows serveru 2008.

**Můžu upgradovat rozšíření SQL Server IaaS z režimu prostého režimu na režim Full?**

Ano. Upgrade režimu spravovatelnosti z jednoduchého na plný se podporuje přes PowerShell nebo Azure Portal. Vyžaduje restartování SQL Server.

**Můžu SQL Server rozšíření IaaS z úplného režimu snížit na režim No-agent nebo pro zjednodušenou správu?**

Ne. Přechod do režimu spravovatelnosti rozšíření SQL Server IaaS se nepodporuje. Režim spravovatelnosti nejde downgradovat z režimu úplného režimu na odlehčený nebo bez agenta a nedá se downgradovat z prostého režimu na režim bez agenta. 

Chcete-li změnit režim spravovatelnosti z možnosti úplné správy, odeberte rozšíření SQL Server IaaS. Pak vyřaďte prostředek Micorsoft. SqlVirtualMachine a znovu zaregistrujte SQL Server virtuálního počítače pomocí poskytovatele prostředků virtuálního počítače SQL.

**Můžu se zaregistrovat k poskytovateli prostředků SQL VM z Azure Portal?**

Ne. Registrace u poskytovatele prostředků virtuálního počítače SQL není k dispozici v Azure Portal. Registrace u poskytovatele prostředků virtuálního počítače SQL v úplném režimu spravovatelnosti se podporuje v Azure CLI nebo PowerShellu. Registrace u poskytovatele prostředků virtuálního počítače SQL ve zjednodušeném režimu nebo v režimu správy bez agenta podporuje jenom rozhraní Azure PowerShell API.

**Je možné zaregistrovat virtuální počítač s poskytovatelem prostředků SQL VM před tím, než se SQL Server nainstaluje?**

Ne. Aby se virtuální počítač mohl úspěšně zaregistrovat u poskytovatele prostředků virtuálního počítače SQL, musí mít aspoň jednu instanci SQL Server. Pokud na virtuálním počítači není žádná instance SQL Server, nový prostředek obraťte. SqlVirtualMachine bude ve stavu selhání.

**Je možné zaregistrovat virtuální počítač s poskytovatelem prostředků SQL VM, pokud existuje více instancí SQL Server?**

Ano. Poskytovatel prostředků virtuálního počítače SQL bude registrovat jenom jednu instanci SQL Server. Poskytovatel prostředků virtuálního počítače SQL zaregistruje výchozí instanci SQL Server v případě více instancí. Pokud neexistuje žádná výchozí instance, je podporována pouze registrace v režimu prostého režimu. Chcete-li provést upgrade z prostého režimu na režim správy, musí být použita výchozí instance SQL Server, nebo by měl mít virtuální počítač pouze jednu pojmenovanou instanci SQL Server.

**Můžu u SQL Server instance clusteru s podporou převzetí služeb při selhání zaregistrovat poskytovatele prostředků virtuálního počítače SQL?**

Ano. SQL Server instancí clusteru s podporou převzetí služeb při selhání na virtuálním počítači Azure je možné zaregistrovat u poskytovatele prostředků virtuálního počítače SQL v jednoduchém režimu. SQL Server instancí clusteru s podporou převzetí služeb při selhání se ale nedá upgradovat na režim úplné správy.

**Můžu zaregistrovat virtuální počítač k poskytovateli prostředků SQL VM, pokud je nakonfigurovaná Skupina dostupnosti Always On?**

Ano. Pokud se účastníte konfigurace skupiny dostupnosti AlwaysOn, neexistují žádná omezení k registraci instance SQL Server na virtuálním počítači Azure s poskytovatelem prostředků SQL VM.

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících článcích: 

* [Přehled SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Nejčastější dotazy k SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Doprovodné materiály k cenách pro SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Poznámky k verzi pro SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
