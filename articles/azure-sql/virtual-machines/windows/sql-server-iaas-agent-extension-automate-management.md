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
ms.openlocfilehash: 7ddc13306f4adb1730169c4811b9d2227dedca33
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632762"
---
# <a name="automate-management-with-the-sql-server-iaas-agent-extension"></a>Automatizovaná správa pomocí rozšíření agenta SQL Server IaaS
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Rozšíření agenta SQL Server IaaS (SqlIaasExtension) běží na SQL Server na Azure Virtual Machines (virtuálních počítačích) pro automatizaci úloh správy a správy. 

Tento článek poskytuje přehled o rozšíření. Pokud chcete nainstalovat rozšíření SQL Server IaaS pro SQL Server na virtuálních počítačích Azure, přečtěte si články pro [automatickou instalaci](sql-agent-extension-automatic-registration-all-vms.md), [jednotlivé virtuální počítače](sql-agent-extension-manually-register-single-vm.md)nebo [virtuální počítače](sql-agent-extension-manually-register-vms-bulk.md). 

## <a name="overview"></a>Přehled

Rozšíření agenta SQL Server IaaS poskytuje řadu výhod pro SQL Server na virtuálních počítačích Azure: 

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
| **Správa portálu** | Odemkne [správu na portálu](manage-sql-vm-portal.md), abyste mohli zobrazit všechny SQL Server virtuálních počítačů na jednom místě, abyste mohli povolit a zakázat funkce specifické pro SQL přímo z portálu. 
| **Automatizované zálohování** |Automatizuje plánování záloh pro všechny databáze, a to buď pro výchozí instanci, nebo pro [správnou nainstalovanou](frequently-asked-questions-faq.md#administration) pojmenovanou instanci SQL Server na virtuálním počítači. Další informace najdete v tématu [automatizované zálohování pro SQL Server ve virtuálních počítačích Azure (Správce prostředků)](automated-backup-sql-2014.md). |
| **Automatizované opravy** |Nakonfiguruje časový interval pro správu a údržbu, během kterého může probíhat důležité aktualizace zabezpečení Windows a SQL Server pro váš virtuální počítač, takže se můžete vyhnout aktualizacím během špičky pro vaše zatížení. Další informace najdete v tématu [automatizované opravy pro SQL Server ve virtuálních počítačích Azure (Správce prostředků)](automated-patching.md). |
| **Integrace Azure Key Vault** |Umožňuje automaticky nainstalovat a nakonfigurovat Azure Key Vault na VIRTUÁLNÍm počítači s SQL Server. Další informace najdete v tématu [Konfigurace integrace Azure Key Vault pro SQL Server v Azure Virtual Machines (Správce prostředků)](azure-key-vault-integration-configure.md). |
| **Flexibilní licencování** | Díky [bezproblémovému převodu](licensing-model-azure-hybrid-benefit-ahb-change.md) z vlastní licence (označované také jako zvýhodněné hybridní využití Azure) do licenčního modelu s průběžnými platbami a obnovením můžete ušetřit náklady. | 
| **Flexibilní verze/edice** | Pokud se rozhodnete změnit [verzi](change-sql-server-version.md) nebo [edici](change-sql-server-edition.md) SQL Server, můžete metadata v rámci Azure Portal aktualizovat, aniž byste museli znovu nasadit celý virtuální počítač SQL Server.  | 


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


## <a name="frequently-asked-questions"></a>Nejčastější dotazy 

**Mám zaregistrovat SQL Server virtuální počítač zřízený z SQL Server Image v Azure Marketplace?**

No. Microsoft automaticky registruje virtuální počítače zřízené z SQL Server imagí v Azure Marketplace. Registrace s rozšířením se vyžaduje jenom v případě, že se virtuální počítač *nezřídil z* SQL Server imagí v Azure Marketplace a SQL Server byl samoobslužně nainstalován.

**Je k dispozici rozšíření agenta SQL IaaS pro všechny zákazníky?** 

Yes. Zákazníci by měli své SQL Server virtuálních počítačů registrovat s příponou, pokud nepoužívali SQL Server Image ze Azure Marketplace a místo toho SQL Server, nebo pokud se vlastní VHD nainstalují. Virtuální počítače vlastněné všemi typy předplatných (Direct, smlouva Enterprise a Cloud Solution Provider) se můžou registrovat pomocí rozšíření agenta SQL IaaS.

**Jaký je výchozí režim správy při registraci pomocí rozšíření agenta SQL IaaS?**

Výchozí režim správy při registraci v rozšíření agenta SQL IaaS je *odlehčený*. Pokud není při registraci v rozšíření nastavená vlastnost SQL Server Management, režim se nastaví jako odlehčený a vaše služba SQL Server se nerestartuje. Doporučuje se nejprve v jednoduchém režimu zaregistrovat s rozšířením agenta SQL IaaS a pak v časovém intervalu pro správu a údržbu provést upgrade na úplný. Podobně je výchozí Správa také odlehčená při použití [funkce automatické registrace](sql-agent-extension-automatic-registration-all-vms.md).

**Jaké jsou požadavky pro registraci pomocí rozšíření agenta SQL IaaS?**

Nejsou k dispozici žádné požadavky k registraci s rozšířením agenta SQL IaaS, než je na virtuálním počítači nainstalováno SQL Server. Všimněte si, že pokud je rozšíření agenta SQL IaaS nainstalované v plném režimu, SQL Server služba se restartuje, takže to uděláte během doporučeného časového období údržby.

**Provede se registrace pomocí rozšíření agenta SQL IaaS instalace agenta na mém virtuálním počítači?**

Ano, registrace pomocí rozšíření agenta SQL IaaS v režimu úplné správy nainstaluje agenta do virtuálního počítače. Registruje se v odlehčeném nebo neagentovém režimu. 

Registrace pomocí rozšíření agenta SQL IaaS ve zjednodušeném režimu kopíruje jenom *binární soubory* rozšíření agenta SQL IaaS do virtuálního počítače, ale neinstaluje agenta. Tyto binární soubory se pak použijí k instalaci agenta, když se režim správy upgraduje na úplný.


**Provede se registrace na svém virtuálním počítači pomocí SQL Server IaaS pro restartování rozšíření agenta SQL?**

Závisí na režimu zadaném během registrace. Je-li zadán režim Lightweight nebo unagent, služba SQL Server nebude restartována. Pokud ale nastavíte režim správy jako plný, služba SQL Server se restartuje. Funkce automatické registrace registruje virtuální počítače s SQL Server v jednoduchém režimu, pokud verze Windows serveru není 2008. v takovém případě se SQL Server virtuální počítač zaregistruje v režimu bez agenta. 

**Jaký je rozdíl mezi režimy jednoduchého a neagent správy při registraci v rozšíření agenta SQL IaaS?** 

Režim správy neagentů je jediným dostupným režimem správy pro SQL Server 2008 a SQL Server 2008 R2 v systému Windows Server 2008. Pro všechny novější verze Windows serveru jsou dva dostupné režimy spravovatelnosti zjednodušené a úplné. 

Režim neagentů vyžaduje, aby byly vlastnosti SQL Server verze a edice nastavené zákazníkem. Odlehčený režim dotazuje virtuální počítač na nalezení verze a edice instance SQL Server.

**Můžu se zaregistrovat s rozšířením agenta SQL IaaS bez zadání typu licence SQL Server?**

No. Typ licence SQL Server není volitelnou vlastností při registraci s rozšířením agenta SQL IaaS. Musíte nastavit typ licence SQL Server jako průběžné platby nebo Zvýhodněné hybridní využití Azure při registraci pomocí rozšíření agenta SQL IaaS ve všech režimech spravovatelnosti (agent, odlehčené a úplné). Pokud máte nainstalovanou bezplatnou verzi SQL Server, jako je například vývojář nebo zkušební edice, musíte se zaregistrovat s průběžnými platbami podle aktuálního využití. Zvýhodněné hybridní využití Azure je k dispozici jenom pro placené verze SQL Server, jako jsou edice Enterprise a Standard.

**Můžu upgradovat rozšíření SQL Server IaaS z režimu bez agenta na režim Full?**

No. Upgrade režimu spravovatelnosti na úplný nebo lehký není pro režim bez agenta k dispozici. Toto je technické omezení Windows serveru 2008. Nejprve budete muset upgradovat operační systém na Windows Server 2008 R2 nebo novější a pak budete moct upgradovat na režim úplné správy. 

**Můžu upgradovat rozšíření SQL Server IaaS z režimu prostého režimu na režim Full?**

Yes. Upgrade režimu spravovatelnosti z jednoduchého na plný se podporuje prostřednictvím Azure PowerShell nebo Azure Portal. Tím se aktivuje restartování služby SQL Server.

**Můžu SQL Server rozšíření IaaS z úplného režimu snížit na režim bez agenta nebo režimu prosté správy?**

No. Přechod do režimu spravovatelnosti rozšíření SQL Server IaaS se nepodporuje. Režim spravovatelnosti nejde downgradovat z úplného režimu na režim Lightweight nebo unagent a nedá se downgradovat z prostého režimu na režim bez agenta. 

Pokud chcete změnit režim spravovatelnosti z plné možnosti správy, [zrušte registraci](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) SQL Server virtuálního počítače z rozšíření agenta SQL IaaS vyřazením _prostředku_ virtuálního počítače SQL a opětovným registrem SQL Server virtuálního počítače pomocí rozšíření agenta SQL IaaS v jiném režimu správy.

**Můžu se zaregistrovat pomocí rozšíření agenta SQL IaaS z Azure Portal?**

No. Registrace pomocí rozšíření agenta SQL IaaS není k dispozici v Azure Portal. Registrace pomocí rozšíření agenta SQL IaaS se podporuje jenom pomocí Azure CLI nebo Azure PowerShell. 

**Je možné zaregistrovat virtuální počítač s rozšířením agenta SQL IaaS ještě před tím, než se nainstaluje SQL Server?**

No. Aby se virtuální počítač mohl úspěšně zaregistrovat do rozšíření agenta SQL IaaS, musí mít aspoň jednu instanci SQL Server (databázový stroj). Pokud na virtuálním počítači není žádná SQL Server instance, nový prostředek Microsoft. SqlVirtualMachine bude ve stavu selhání.

**Můžu zaregistrovat virtuální počítač s rozšířením agenta SQL IaaS, pokud existuje více instancí SQL Server?**

Ano, pokud je na virtuálním počítači výchozí instance. Rozšíření agenta SQL IaaS bude registrovat pouze jednu instanci SQL Server (databázový stroj). Rozšíření agenta SQL IaaS bude v případě více instancí registrovat výchozí instanci SQL Server.

**Můžu u SQL Server instance clusteru s podporou převzetí služeb při selhání zaregistrovat rozšíření agenta SQL IaaS?**

Yes. SQL Server instancí clusteru s podporou převzetí služeb při selhání na virtuálním počítači Azure se dá v jednoduchém režimu zaregistrovat s rozšířením agenta SQL IaaS. SQL Server instancí clusteru s podporou převzetí služeb při selhání se ale nedá upgradovat na režim úplné správy.

**Můžu zaregistrovat virtuální počítač s rozšířením agenta SQL IaaS, pokud je nakonfigurovaná Skupina dostupnosti Always On?**

Yes. Neexistují žádná omezení pro registraci instance SQL Server na virtuálním počítači Azure s rozšířením agenta SQL IaaS, pokud se účastníte konfigurace skupiny dostupnosti Always On.

**Jaké jsou náklady na registraci pomocí rozšíření agenta SQL IaaS nebo upgrade na úplný režim správy?**

Žádné K registraci s rozšířením agenta SQL IaaS nebo pomocí některého ze tří režimů správy se neúčtují žádné poplatky. Správa virtuálního počítače s SQL Server s rozšířením je zcela zadarmo. 

**Jaký je dopad na výkon při použití různých režimů správy?**

Při použití režimů nespravovaného *agenta* a *zjednodušené* správy to nemá žádný vliv. Použití *úplného* režimu správy ze dvou služeb, které jsou nainstalovány do operačního systému, má minimální dopad. Dají se monitorovat pomocí Správce úloh a zobrazují se v integrované konzole Windows Services. 

Názvy těchto dvou služeb:
- `SqlIaaSExtensionQuery` (Zobrazované jméno- `Microsoft SQL Server IaaS Query Service` )
- `SQLIaaSExtension` (Zobrazované jméno- `Microsoft SQL Server IaaS Agent` )

**Návody odebrat rozšíření?**

Odeberte rozšíření zrušením [registrace](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) SQL Server virtuálního počítače z rozšíření agenta SQL IaaS. 

## <a name="next-steps"></a>Další kroky

Pokud chcete nainstalovat rozšíření SQL Server IaaS pro SQL Server na virtuálních počítačích Azure, přečtěte si články pro [automatickou instalaci](sql-agent-extension-automatic-registration-all-vms.md), [jednotlivé virtuální počítače](sql-agent-extension-manually-register-single-vm.md)nebo [virtuální počítače](sql-agent-extension-manually-register-vms-bulk.md).

Další informace o spouštění SQL Server v Azure Virtual Machines najdete v tématu [co je SQL Server na Virtual Machines Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).
