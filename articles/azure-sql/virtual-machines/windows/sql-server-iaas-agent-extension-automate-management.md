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
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/30/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 070058eae36bf4f8546cfcf4beb85ac5023e9c79
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286179"
---
# <a name="what-is-the-sql-server-iaas-agent-extension"></a>Co je rozšíření agenta SQL Server IaaS?
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Rozšíření agenta SQL Server IaaS (SqlIaasExtension) běží na SQL Server na Azure Virtual Machines (virtuálních počítačích) pro automatizaci úloh správy a správy. 

Tento článek poskytuje přehled o rozšíření. Pokud chcete nainstalovat rozšíření SQL Server IaaS pro SQL Server na virtuálních počítačích Azure, přečtěte si články pro [automatickou instalaci](sql-vm-resource-provider-automatic-registration.md), [jednotlivé virtuální počítače](sql-vm-resource-provider-register.md)nebo [virtuální počítače](sql-vm-resource-provider-bulk-register.md). 

## <a name="overview"></a>Přehled

Rozšíření agenta SQL Server IaaS poskytuje řadu výhod pro SQL Server na virtuálních počítačích Azure: 

- **Výhody funkcí** : rozšíření odzamkne řadu výhod funkce automatizace, jako je Správa portálu, flexibilita licencí, automatizované zálohování, automatizované opravy a další. Podrobnosti najdete v tématu [výhody funkcí](#feature-benefits) dále v tomto článku. 

- **Dodržování předpisů** : rozšíření nabízí zjednodušenou metodu, která vyžaduje, abyste Microsoftu informovali, že zvýhodněné hybridní využití Azure je povolená, jak je uvedeno v dodacích číslech produktu. Tento proces vyžaduje, aby ve všech zdrojích spravovali formuláře pro registraci licencí.  

- **Free** : rozšíření v všech třech režimech spravovatelnosti je zcela bezplatné. K poskytovateli prostředků se nevztahují žádné další náklady ani se měnícími se režimy správy. 

- **Zjednodušená správa licencí** : rozšíření zjednodušuje správu licencí SQL Server a umožňuje rychle identifikovat SQL Server virtuálních počítačů pomocí zvýhodněné hybridní využití Azure povolených pomocí [Azure Portal](manage-sql-vm-portal.md), Azure CLI nebo PowerShellu: 

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


> [!IMPORTANT]
> Rozšíření agenta SQL IaaS shromažďuje data pro účely výslovného účelu poskytování dobrovolných výhod při používání SQL Server v rámci Azure Virtual Machines. Microsoft nebude tato data používat pro audity licencování bez předchozího souhlasu zákazníka. Další informace najdete v tématu [SQL Server ochrany osobních údajů](/sql/sql-server/sql-server-privacy#non-personal-data) .


## <a name="feature-benefits"></a>Výhody funkcí 

Rozšíření agenta SQL Server IaaS odemkne řadu výhod funkce pro správu virtuálního počítače SQL Server. 

Následující tabulka popisuje tyto výhody: 


| Funkce | Popis |
| --- | --- |
| **Správa portálu** | Odemkne [správu na portálu](manage-sql-vm-portal.md), abyste mohli zobrazit všechny SQL Server virtuálních počítačů na jednom místě, abyste mohli povolit a zakázat funkce specifické pro SQL přímo z portálu. 
| **Automatizované zálohování** |Automatizuje plánování záloh pro všechny databáze, a to buď pro výchozí instanci, nebo pro [správnou nainstalovanou](frequently-asked-questions-faq.md#administration) pojmenovanou instanci SQL Server na virtuálním počítači. Další informace najdete v tématu [automatizované zálohování pro SQL Server ve virtuálních počítačích Azure (Správce prostředků)](automated-backup-sql-2014.md). |
| **Automatizované opravy** |Nakonfiguruje časový interval pro správu a údržbu, během kterého může probíhat důležité aktualizace zabezpečení Windows a SQL Server pro váš virtuální počítač, takže se můžete vyhnout aktualizacím během špičky pro vaše zatížení. Další informace najdete v tématu [automatizované opravy pro SQL Server ve virtuálních počítačích Azure (Správce prostředků)](automated-patching.md). |
| **Integrace se službou Azure Key Vault** |Umožňuje automaticky nainstalovat a nakonfigurovat Azure Key Vault na VIRTUÁLNÍm počítači s SQL Server. Další informace najdete v tématu [Konfigurace integrace Azure Key Vault pro SQL Server v Azure Virtual Machines (Správce prostředků)](azure-key-vault-integration-configure.md). |
| **Flexibilní licencování** | Díky [bezproblémovému převodu](licensing-model-azure-hybrid-benefit-ahb-change.md) z vlastní licence (označované také jako zvýhodněné hybridní využití Azure) do licenčního modelu s průběžnými platbami a obnovením můžete ušetřit náklady. | 
| **Flexibilní verze/edice** | Pokud se rozhodnete změnit [verzi](change-sql-server-version.md) nebo [edici](change-sql-server-edition.md) SQL Server, můžete metadata v rámci Azure Portal aktualizovat, aniž byste museli znovu nasadit celý virtuální počítač SQL Server.  | 


## <a name="management-modes"></a>Režimy správy

Rozšíření SQL IaaS má tři režimy správy:

- **Odlehčený** režim nevyžaduje restartování SQL Server, ale podporuje pouze změnu typu licence a edice SQL Server. Tato možnost slouží k SQL Server virtuálních počítačů s několika instancemi nebo účasti v instanci clusteru s podporou převzetí služeb při selhání (FCI). Tento režim správy udržuje v počítači binární soubory rozšíření agenta SQL IaaS, ale neinstaluje agenta. Zjednodušený režim je výchozím režimem správy při použití funkce [automatické registrace](sql-vm-resource-provider-automatic-registration.md) nebo když během Ruční registrace není zadaný typ správy. Při použití zjednodušeného režimu nemá žádný vliv na paměť nebo procesor a nejsou k dispozici žádné náklady. Doporučuje se nejdřív zaregistrovat SQL Server virtuální počítač v jednoduchém režimu a potom během naplánovaného časového období údržby upgradovat na úplný režim.

- **Úplný** režim zajišťuje všechny funkce, ale vyžaduje restartování oprávnění SQL Server a správce systému. Použijte ji ke správě SQL Server virtuálního počítače s jedinou instancí. Úplný režim nainstaluje dvě služby systému Windows, které mají minimální dopad na paměť a procesor – dá se monitorovat pomocí Správce úloh. Neexistují žádné náklady spojené s používáním úplného režimu spravovatelnosti. 

- Režim **Neagentů** je vyhrazen pro SQL Server 2008 a SQL Server 2008 R2 nainstalované v systému Windows Server 2008. Při použití režimu neagenta není ovlivněná paměť ani procesor. Neexistují žádné náklady spojené s používáním režimu správy neagentů. 

Aktuální režim SQL Server agenta IaaS můžete zobrazit pomocí Azure PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="installation"></a>Instalace

Rozšíření agenta SQL Server IaaS se nainstaluje při registraci SQL Server virtuálních počítačů pomocí poskytovatele prostředků virtuálních počítačů SQL. Při registraci u poskytovatele prostředků se vytvoří _prostředek_ **virtuálního počítače SQL** v rámci vašeho předplatného, což je _samostatný_ prostředek z prostředku virtuálního počítače. Zrušení registrace SQL Server virtuálního počítače od poskytovatele prostředků odebere _prostředek_ **virtuálního počítače SQL** , ale neodstraní skutečný virtuální počítač.

Nasazení Azure Marketplace image SQL Server virtuálního počítače pomocí Azure Portal automaticky registruje SQL Server virtuální počítač s poskytovatelem prostředků. Pokud se ale rozhodnete vlastní instalaci SQL Server na virtuálním počítači Azure nebo zřídit virtuální počítač Azure z vlastního virtuálního pevného disku, musíte zaregistrovat SQL Server virtuální počítač s poskytovatelem prostředků SQL VM a nainstalovat rozšíření agenta SQL IaaS. 

Pokud chcete nainstalovat rozšíření, zaregistrujte SQL Server virtuální počítač u poskytovatele prostředků:
- [Automaticky pro všechny aktuální a budoucí virtuální počítače v předplatném](sql-vm-resource-provider-automatic-registration.md)
- [Pro jeden virtuální počítač](sql-vm-resource-provider-register.md)
- [Pro více virtuálních počítačů hromadně](sql-vm-resource-provider-bulk-register.md)

### <a name="named-instance-support"></a>Podpora pojmenovaných instancí

Rozšíření SQL Server IaaS bude fungovat s pojmenovanou instancí SQL Server, pokud se jedná o jedinou instanci SQL Server dostupnou na virtuálním počítači. Rozšíření se nepodaří nainstalovat na virtuální počítače, které mají víc instancí SQL Server. 

Pokud chcete použít pojmenovanou instanci SQL Server, nasaďte virtuální počítač Azure, nainstalujte do něj jednu pojmenovanou instanci SQL Server a pak ji zaregistrujte u [poskytovatele prostředků virtuálního počítače SQL](sql-vm-resource-provider-register.md) , aby se rozšíření nainstalovalo.

Případně chcete-li použít pojmenovanou instanci s Azure Marketplace SQL Server Image, postupujte podle následujících kroků: 

   1. Nasazení virtuálního počítače s SQL Server z Azure Marketplace. 
   1. [Zrušte registraci](sql-vm-resource-provider-register.md#unregister-from-rp) SQL Server virtuálního počítače od poskytovatele prostředků virtuálního počítače SQL. 
   1. Odinstalace SQL Server kompletně v rámci SQL Server virtuálního počítače.
   1. Nainstalujte SQL Server s pojmenovanou instancí v rámci SQL Server virtuálního počítače. 
   1. Nainstalujte rozšíření agenta SQL IaaS tak, že [zaregistrujete virtuální počítač SQL Server pomocí poskytovatele prostředků virtuálního počítače SQL](sql-vm-resource-provider-register.md#register-with-rp). 

## <a name="verify-status-of-extension"></a>Ověřit stav rozšíření

Ke kontrole stavu rozšíření použijte Azure Portal nebo Azure PowerShell. 


### <a name="azure-portal"></a>Azure Portal

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


## <a name="frequently-asked-questions"></a>Nejčastější dotazy 

**Mám zaregistrovat SQL Server virtuální počítač zřízený z SQL Server Image v Azure Marketplace?**

Ne. Microsoft automaticky registruje virtuální počítače zřízené z SQL Server imagí v Azure Marketplace. Registrace u poskytovatele prostředků virtuálního počítače SQL se vyžaduje jenom v případě, že se virtuální počítač *nezřídil z* SQL Server imagí v Azure Marketplace a SQL Server byl samoobslužně nainstalován.

**Je poskytovatel prostředků virtuálního počítače SQL dostupný pro všechny zákazníky?** 

Ano. Zákazníci by měli zaregistrovat své SQL Server virtuální počítače s poskytovatelem prostředků SQL VM, pokud nepoužívali image SQL Server z Azure Marketplace a místo toho SQL Server, nebo pokud přenesli svůj vlastní VHD. Virtuální počítače vlastněné všemi typy předplatných (Direct, smlouva Enterprise a Cloud Solution Provider) se můžou registrovat u poskytovatele prostředků virtuálního počítače SQL.

**Jaký je výchozí režim správy při registraci u poskytovatele prostředků virtuálního počítače SQL?**

Výchozí režim správy při registraci u poskytovatele prostředků virtuálního počítače SQL je *odlehčený*. Pokud není nastavena vlastnost Management SQL Server při registraci u poskytovatele prostředků virtuálního počítače SQL, režim se nastaví jako odlehčený a vaše služba SQL Server se nerestartuje. Doporučuje se nejprve zaregistrovat u poskytovatele prostředků virtuálních počítačů SQL v prostém režimu a potom v okně údržby provést upgrade na úplný. Podobně je výchozí Správa také odlehčená při použití [funkce automatické registrace](sql-vm-resource-provider-automatic-registration.md).

**Jaké jsou požadavky na registraci u poskytovatele prostředků virtuálních počítačů SQL?**

Nejsou k dispozici žádné požadavky k registraci u poskytovatele prostředků virtuálního počítače SQL, který není na virtuálním počítači nainstalovaný SQL Server. Všimněte si, že pokud je rozšíření agenta SQL IaaS nainstalované v plném režimu, SQL Server služba se restartuje, takže to uděláte během doporučeného časového období údržby.

**Bude se registrovat u poskytovatele prostředků virtuálního počítače SQL instalace agenta na mém virtuálním počítači?**

Ano, registrace u poskytovatele prostředků virtuálního počítače SQL v úplném režimu spravovatelnosti nainstaluje agenta do virtuálního počítače. Registruje se v odlehčeném nebo neagentovém režimu. 

Registrace u poskytovatele prostředků virtuálního počítače SQL ve zjednodušeném režimu kopíruje jenom *binární soubory* rozšíření agenta SQL IaaS do virtuálního počítače, ale neinstaluje agenta. Tyto binární soubory se pak použijí k instalaci agenta, když se režim správy upgraduje na úplný.


**Provede se registrace na mém virtuálním počítači pomocí poskytovatele prostředků virtuálního počítače SQL SQL Server restartování?**

Závisí na režimu zadaném během registrace. Je-li zadán režim Lightweight nebo unagent, služba SQL Server nebude restartována. Pokud ale nastavíte režim správy jako plný, služba SQL Server se restartuje. Funkce automatické registrace registruje virtuální počítače s SQL Server v jednoduchém režimu, pokud verze Windows serveru není 2008. v takovém případě se SQL Server virtuální počítač zaregistruje v režimu bez agenta. 

**Jaký je rozdíl mezi režimy jednoduchého a neagent správy při registraci u poskytovatele prostředků virtuálního počítače SQL?** 

Režim správy neagentů je jediným dostupným režimem správy pro SQL Server 2008 a SQL Server 2008 R2 v systému Windows Server 2008. Pro všechny novější verze Windows serveru jsou dva dostupné režimy spravovatelnosti zjednodušené a úplné. 

Režim neagentů vyžaduje, aby byly vlastnosti SQL Server verze a edice nastavené zákazníkem. Odlehčený režim dotazuje virtuální počítač na nalezení verze a edice instance SQL Server.

**Můžu se zaregistrovat u poskytovatele prostředků SQL VM bez zadání typu licence SQL Server?**

Ne. Typ licence SQL Server není volitelnou vlastností při registraci u poskytovatele prostředků virtuálního počítače SQL. Je nutné nastavit typ licence SQL Server jako průběžné platby nebo Zvýhodněné hybridní využití Azure při registraci u poskytovatele prostředků virtuálního počítače SQL ve všech režimech spravovatelnosti (agent, odlehčené a úplné). Pokud máte nainstalovanou bezplatnou verzi SQL Server, jako je vývojář nebo zkušební edice, musíte se zaregistrovat s průběžnými platbami. Zvýhodněné hybridní využití Azure je k dispozici jenom pro placené verze SQL Server, jako jsou edice Enterprise a Standard.

**Můžu upgradovat rozšíření SQL Server IaaS z režimu bez agenta na režim Full?**

Ne. Upgrade režimu spravovatelnosti na úplný nebo lehký není pro režim bez agenta k dispozici. Toto je technické omezení Windows serveru 2008. Nejprve budete muset upgradovat operační systém na Windows Server 2008 R2 nebo novější a pak budete moct upgradovat na režim úplné správy. 

**Můžu upgradovat rozšíření SQL Server IaaS z režimu prostého režimu na režim Full?**

Ano. Upgrade režimu spravovatelnosti z jednoduchého na plný se podporuje prostřednictvím Azure PowerShell nebo Azure Portal. Tím se aktivuje restartování služby SQL Server.

**Můžu SQL Server rozšíření IaaS z úplného režimu snížit na režim bez agenta nebo režimu prosté správy?**

Ne. Přechod do režimu spravovatelnosti rozšíření SQL Server IaaS se nepodporuje. Režim spravovatelnosti nejde downgradovat z úplného režimu na režim Lightweight nebo unagent a nedá se downgradovat z prostého režimu na režim bez agenta. 

Pokud chcete změnit režim spravovatelnosti z plné možnosti správy, [zrušte registraci](sql-vm-resource-provider-register.md#unregister-from-rp) SQL Server virtuálního počítače od poskytovatele prostředků SQL VM vyřazením _prostředku_ virtuálního počítače SQL a opětovným registrem SQL Server virtuálního počítače pomocí poskytovatele prostředků virtuálního počítače SQL znovu v jiném režimu správy.

**Můžu se zaregistrovat k poskytovateli prostředků SQL VM z Azure Portal?**

Ne. Registrace u poskytovatele prostředků virtuálního počítače SQL není k dispozici v Azure Portal. Registrace u poskytovatele prostředků virtuálního počítače SQL je podporovaná jenom pomocí Azure CLI nebo Azure PowerShell. 

**Je možné zaregistrovat virtuální počítač s poskytovatelem prostředků SQL VM před tím, než se SQL Server nainstaluje?**

Ne. Aby se virtuální počítač mohl úspěšně zaregistrovat u poskytovatele prostředků virtuálního počítače SQL, musí mít aspoň jednu instanci SQL Server (databázový stroj). Pokud na virtuálním počítači není žádná SQL Server instance, nový prostředek Microsoft. SqlVirtualMachine bude ve stavu selhání.

**Je možné zaregistrovat virtuální počítač s poskytovatelem prostředků SQL VM, pokud existuje více instancí SQL Server?**

Ano. Poskytovatel prostředků virtuálního počítače SQL bude registrovat jenom jednu instanci SQL Server (databázový stroj). Poskytovatel prostředků virtuálního počítače SQL zaregistruje výchozí instanci SQL Server v případě více instancí. Pokud neexistuje žádná výchozí instance, je podporována pouze registrace v režimu prostého režimu. Chcete-li provést upgrade z prostého režimu na režim správy, musí být použita výchozí instance SQL Server, nebo by měl mít virtuální počítač pouze jednu pojmenovanou instanci SQL Server.

**Můžu u SQL Server instance clusteru s podporou převzetí služeb při selhání zaregistrovat poskytovatele prostředků virtuálního počítače SQL?**

Ano. SQL Server instancí clusteru s podporou převzetí služeb při selhání na virtuálním počítači Azure je možné zaregistrovat u poskytovatele prostředků virtuálního počítače SQL v jednoduchém režimu. SQL Server instancí clusteru s podporou převzetí služeb při selhání se ale nedá upgradovat na režim úplné správy.

**Můžu zaregistrovat virtuální počítač k poskytovateli prostředků SQL VM, pokud je nakonfigurovaná Skupina dostupnosti Always On?**

Ano. Pokud se účastníte konfigurace skupiny dostupnosti AlwaysOn, neexistují žádná omezení k registraci instance SQL Server na virtuálním počítači Azure s poskytovatelem prostředků SQL VM.

**Jaké jsou náklady na registraci u poskytovatele prostředků SQL VM nebo při upgradu na režim úplné správy?**

Žádné K registraci u poskytovatele prostředků SQL VM nebo pomocí žádného ze tří režimů správy se neúčtují žádné poplatky. Správa virtuálního počítače s SQL Server pomocí poskytovatele prostředků je zcela zadarmo. 

**Jaký je dopad na výkon při použití různých režimů správy?**

Při použití režimů nespravovaného *agenta* a *zjednodušené* správy to nemá žádný vliv. Použití *úplného* režimu správy ze dvou služeb, které jsou nainstalovány do operačního systému, má minimální dopad. Dají se monitorovat pomocí Správce úloh a zobrazují se v integrované konzole Windows Services. 

Názvy těchto dvou služeb:
- `SqlIaaSExtensionQuery` (Zobrazované jméno- `Microsoft SQL Server IaaS Query Service` )
- `SQLIaaSExtension` (Zobrazované jméno- `Microsoft SQL Server IaaS Agent` )

**Návody odebrat rozšíření?**

Odeberte rozšíření zrušením [registrace](sql-vm-resource-provider-register.md#unregister-from-rp) SQL Server virtuálního počítače od poskytovatele prostředků virtuálního počítače SQL. 

## <a name="next-steps"></a>Další kroky

Pokud chcete nainstalovat rozšíření SQL Server IaaS pro SQL Server na virtuálních počítačích Azure, přečtěte si články pro [automatickou instalaci](sql-vm-resource-provider-automatic-registration.md), [jednotlivé virtuální počítače](sql-vm-resource-provider-register.md)nebo [virtuální počítače](sql-vm-resource-provider-bulk-register.md).

Další informace o spouštění SQL Server v Azure Virtual Machines najdete v tématu [co je SQL Server na Virtual Machines Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).
