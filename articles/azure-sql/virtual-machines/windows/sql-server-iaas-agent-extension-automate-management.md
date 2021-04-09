---
title: Co je rozšíření agenta SQL Server IaaS?
description: Tento článek popisuje, jak SQL Server rozšíření agenta IaaS pomáhá automatizovat správu specifických úloh správy SQL Server na virtuálních počítačích Azure. Patří mezi ně funkce, jako je automatické zálohování, automatizované opravy, Azure Key Vault integrace, Správa licencí, konfigurace úložiště a centrální Správa všech SQL Server instancí virtuálních počítačů.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: fdff3f6144f7099f3f61cfe57186357e17136e9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225477"
---
# <a name="automate-management-with-the-sql-server-iaas-agent-extension"></a>Automatizovaná správa pomocí rozšíření agenta SQL Server IaaS
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Rozšíření agenta SQL Server IaaS (SqlIaasExtension) běží na SQL Server na Azure Virtual Machines (virtuálních počítačích) pro automatizaci úloh správy a správy. 

Tento článek poskytuje přehled o rozšíření. Pokud chcete nainstalovat rozšíření SQL Server IaaS pro SQL Server na virtuálních počítačích Azure, přečtěte si články pro [automatickou instalaci](sql-agent-extension-automatic-registration-all-vms.md), [jednotlivé virtuální počítače](sql-agent-extension-manually-register-single-vm.md)nebo [virtuální počítače](sql-agent-extension-manually-register-vms-bulk.md). 

## <a name="overview"></a>Přehled

Rozšíření agenta SQL Server IaaS umožňuje integraci s Azure Portal a v závislosti na režimu správy odemkne řadu výhod funkcí pro SQL Server na virtuálních počítačích Azure: 

- **Výhody funkcí**: rozšíření odzamkne řadu výhod funkce automatizace, jako je Správa portálu, flexibilita licencí, automatizované zálohování, automatizované opravy a další. Podrobnosti najdete v tématu [výhody funkcí](#feature-benefits) dále v tomto článku. 

- **Dodržování předpisů**: rozšíření nabízí zjednodušenou metodu, která vyžaduje, abyste Microsoftu informovali, že zvýhodněné hybridní využití Azure je povolená, jak je uvedeno v dodacích číslech produktu. Tento proces vyžaduje, aby ve všech zdrojích spravovali formuláře pro registraci licencí.  

- **Free**: rozšíření v všech třech režimech spravovatelnosti je zcela bezplatné. K rozšíření se nevztahují žádné další náklady ani se měnícími se režimy správy. 

- **Zjednodušená správa licencí**: rozšíření zjednodušuje správu licencí SQL Server a umožňuje rychle identifikovat SQL Server virtuálních počítačů pomocí zvýhodněné hybridní využití Azure povolených pomocí [Azure Portal](manage-sql-vm-portal.md), PowerShellu nebo rozhraní příkazového řádku Azure. 

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $ az sql vm list --query "[?sqlServerLicenseType=='AHUB']"
   ```
   ---


> [!IMPORTANT]
> Rozšíření agenta SQL IaaS shromažďuje data pro účely výslovného účelu poskytování dobrovolných výhod při používání SQL Server v rámci Azure Virtual Machines. Microsoft nebude tato data používat pro audity licencování bez předchozího souhlasu zákazníka. Další informace najdete v tématu [SQL Server ochrany osobních údajů](/sql/sql-server/sql-server-privacy#non-personal-data) .


## <a name="feature-benefits"></a>Výhody funkcí 

Rozšíření agenta SQL Server IaaS odemkne řadu výhod funkce pro správu virtuálního počítače SQL Server. 

Následující tabulka popisuje tyto výhody: 


| Funkce | Popis |
| --- | --- |
| **Správa portálu** | Odemkne [správu na portálu](manage-sql-vm-portal.md), abyste mohli zobrazit všechny SQL Server virtuálních počítačů na jednom místě, abyste mohli povolit a zakázat funkce specifické pro SQL přímo z portálu. <br/> Režim správy: odlehčený & plný|  
| **Automatizované zálohování** |Automatizuje plánování záloh pro všechny databáze, a to buď pro výchozí instanci, nebo pro [správnou nainstalovanou](frequently-asked-questions-faq.md#administration) pojmenovanou instanci SQL Server na virtuálním počítači. Další informace najdete v tématu [automatizované zálohování pro SQL Server ve virtuálních počítačích Azure (Správce prostředků)](automated-backup-sql-2014.md). <br/> Režim správy: úplný|
| **Automatizované opravy** |Nakonfiguruje časový interval pro správu a údržbu, během kterého může probíhat důležité aktualizace zabezpečení Windows a SQL Server pro váš virtuální počítač, takže se můžete vyhnout aktualizacím během špičky pro vaše zatížení. Další informace najdete v tématu [automatizované opravy pro SQL Server ve virtuálních počítačích Azure (Správce prostředků)](automated-patching.md). <br/> Režim správy: úplný|
| **Integrace Azure Key Vault** |Umožňuje automaticky nainstalovat a nakonfigurovat Azure Key Vault na VIRTUÁLNÍm počítači s SQL Server. Další informace najdete v tématu [Konfigurace integrace Azure Key Vault pro SQL Server v Azure Virtual Machines (Správce prostředků)](azure-key-vault-integration-configure.md). <br/> Režim správy: úplný|
| **Zobrazení využití disku na portálu** | Slouží k zobrazení grafické reprezentace využití disku vašich datových souborů SQL v Azure Portal.  <br/> Režim správy: úplný | 
| **Flexibilní licencování** | Díky [bezproblémovému převodu](licensing-model-azure-hybrid-benefit-ahb-change.md) z vlastní licence (označované také jako zvýhodněné hybridní využití Azure) do licenčního modelu s průběžnými platbami a obnovením můžete ušetřit náklady. <br/> Režim správy: odlehčený & plný| 
| **Flexibilní verze/edice** | Pokud se rozhodnete změnit [verzi](change-sql-server-version.md) nebo [edici](change-sql-server-edition.md) SQL Server, můžete metadata v rámci Azure Portal aktualizovat, aniž byste museli znovu nasadit celý virtuální počítač SQL Server.  <br/> Režim správy: odlehčený & plný| 


## <a name="management-modes"></a>Režimy správy

Můžete si vybrat, že se má vaše rozšíření SQL IaaS zaregistrovat ve třech režimech správy: 

- **Zjednodušený** režim kopíruje binární soubory rozšíření do virtuálního počítače, ale neinstaluje agenta a nerestartuje službu SQL Server. Odlehčený režim podporuje pouze změnu typu licence a edice SQL Server a poskytuje omezená Správa portálu. Tuto možnost použijte pro SQL Server virtuálních počítačů s víc instancemi nebo těch, které se podílejí na instanci clusteru s podporou převzetí služeb při selhání (FCI). Zjednodušený režim je výchozím režimem správy při použití funkce [automatické registrace](sql-agent-extension-automatic-registration-all-vms.md) nebo když během Ruční registrace není zadaný typ správy. Při použití zjednodušeného režimu nemá žádný vliv na paměť nebo procesor a nejsou k dispozici žádné náklady. Doporučuje se nejdřív zaregistrovat SQL Server virtuální počítač v jednoduchém režimu a potom během naplánovaného časového období údržby upgradovat na úplný režim. 

- **Úplný** režim nainstaluje do virtuálního počítače agenta SQL IaaS, aby bylo možné doručovat všechny funkce, ale vyžaduje restart služby SQL Server a oprávnění správce systému. Použijte ji ke správě SQL Server virtuálního počítače s jedinou instancí. Úplný režim nainstaluje dvě služby systému Windows, které mají minimální dopad na paměť a procesor – dá se monitorovat pomocí Správce úloh. Neexistují žádné náklady spojené s používáním úplného režimu spravovatelnosti. 

- Režim **Neagentů** je vyhrazen pro SQL Server 2008 a SQL Server 2008 R2 nainstalované v systému Windows Server 2008. Při použití režimu neagenta není ovlivněná paměť ani procesor. K použití režimu správy nespravovaného agenta se neúčtují žádné náklady, SQL Server se nerestartuje a Agent není nainstalovaný na virtuálním počítači. 

Aktuální režim SQL Server agenta IaaS můžete zobrazit pomocí Azure PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```


## <a name="installation"></a>Instalace

Zaregistrujte svůj SQL Server virtuální počítač s rozšířením agenta SQL Server IaaS k vytvoření _prostředku_ **virtuálního počítače SQL** v rámci vašeho předplatného, což je _samostatný_ prostředek z prostředku virtuálního počítače. Zrušení registrace SQL Server virtuálního počítače z rozšíření odebere _prostředek_ **virtuálního počítače SQL** , ale neodstraní skutečný virtuální počítač.

Nasazení Azure Marketplace image SQL Server virtuálního počítače pomocí Azure Portal automaticky registruje SQL Server virtuální počítač s příponou. Pokud se ale rozhodnete k samoobslužné instalaci SQL Server na virtuálním počítači Azure nebo zřídit virtuální počítač Azure z vlastního virtuálního pevného disku, musíte zaregistrovat SQL Server virtuální počítač s rozšířením SQL IaaS a odemknout výhody funkcí. 

Při registraci rozšíření v prostém režimu se zkopírují binární soubory, ale nenainstaluje se agent na virtuální počítač. Agent se nainstaluje do virtuálního počítače, když se rozšíření upgraduje na režim úplné správy. 

Existují tři způsoby, jak s rozšířením zaregistrovat: 
- [Automaticky pro všechny aktuální a budoucí virtuální počítače v předplatném](sql-agent-extension-automatic-registration-all-vms.md)
- [Ruční pro jeden virtuální počítač](sql-agent-extension-manually-register-single-vm.md)
- [Ruční Ruční pro více virtuálních počítačů](sql-agent-extension-manually-register-vms-bulk.md)

### <a name="named-instance-support"></a>Podpora pojmenovaných instancí

Rozšíření agenta SQL Server IaaS funguje s pojmenovanou instancí SQL Server, pokud se jedná o jedinou instanci SQL Server dostupnou na virtuálním počítači. Pokud na virtuálním počítači není žádná výchozí instance, rozšíření se nedá nainstalovat na virtuální počítače, které mají víc pojmenovaných instancí SQL Server. 

Pokud chcete použít pojmenovanou instanci SQL Server, nasaďte virtuální počítač Azure, nainstalujte do něj jednu pojmenovanou instanci SQL Server a pak ji Zaregistrujte pomocí [IaaS rozšíření SQL](sql-agent-extension-manually-register-single-vm.md).

Případně chcete-li použít pojmenovanou instanci s Azure Marketplace SQL Server Image, postupujte podle následujících kroků: 

   1. Nasazení virtuálního počítače s SQL Server z Azure Marketplace. 
   1. [Zrušte registraci](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) SQL Server virtuálního počítače z rozšíření agenta SQL IaaS. 
   1. Odinstalace SQL Server kompletně v rámci SQL Server virtuálního počítače.
   1. Nainstalujte SQL Server s pojmenovanou instancí v rámci SQL Server virtuálního počítače. 
   1. [Zaregistrujte virtuální počítač pomocí rozšíření agenta SQL IaaS](sql-agent-extension-manually-register-single-vm.md#register-with-extension). 

## <a name="verify-status-of-extension"></a>Ověřit stav rozšíření

Ke kontrole stavu rozšíření použijte Azure Portal nebo Azure PowerShell. 

### <a name="azure-portal"></a>portál Azure

Ověřte, že je rozšíření nainstalované v Azure Portal. 

V podokně virtuální počítač vyberte **všechna nastavení** a pak vyberte **rozšíření**. Měla by se zobrazit uvedená rozšíření **SqlIaasExtension** .

![Stav rozšíření agenta SQL Server IaaS v Azure Portal](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)


### <a name="azure-powershell"></a>Azure PowerShell

Můžete také použít rutinu **Get-AzVMSqlServerExtension** Azure PowerShell:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Předchozí příkaz potvrdí, že je agent nainstalovaný a poskytuje obecné informace o stavu. Konkrétní informace o stavu automatizovaného zálohování a oprav můžete získat pomocí následujících příkazů:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```


## <a name="limitations"></a>Omezení

Rozšíření agenta SQL IaaS podporuje pouze: 

- SQL Server virtuální počítače nasazené prostřednictvím Azure Resource Manager. SQL Server virtuální počítače nasazené prostřednictvím klasického modelu se nepodporují. 
- SQL Server virtuální počítače nasazené do veřejného nebo Azure Governmentho cloudu. Nasazení do jiných privátních nebo státních cloudů se nepodporuje. 


## <a name="in-region-data-residency"></a>Zasídlí dat v oblasti
Virtuální počítač Azure SQL a rozšíření agenta SQL IaaS nepřesouvá ani neukládají zákaznická data mimo oblast, ve které jsou nasazená.

## <a name="next-steps"></a>Další kroky

Pokud chcete nainstalovat rozšíření SQL Server IaaS pro SQL Server na virtuálních počítačích Azure, přečtěte si články pro [automatickou instalaci](sql-agent-extension-automatic-registration-all-vms.md), [jednotlivé virtuální počítače](sql-agent-extension-manually-register-single-vm.md)nebo [virtuální počítače](sql-agent-extension-manually-register-vms-bulk.md).

Další informace o spouštění SQL Server v Azure Virtual Machines najdete v tématu [co je SQL Server na Virtual Machines Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).

Další informace najdete v článku [Nejčastější dotazy](frequently-asked-questions-faq.md). 
