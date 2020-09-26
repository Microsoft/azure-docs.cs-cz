---
title: Architektura zotavení po havárii z Azure do Azure v Azure Site Recovery
description: Přehled architektury používané při nastavování zotavení po havárii mezi oblastmi Azure pro virtuální počítače Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: raynew
ms.openlocfilehash: 08796b0a9b232c7b42b3f62fea69ab49b8957c60
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322083"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Architektura zotavení po havárii Azure do Azure


Tento článek popisuje architekturu, komponenty a procesy používané při nasazení zotavení po havárii pro virtuální počítače Azure pomocí služby [Azure Site Recovery](site-recovery-overview.md) . Po nastavení zotavení po havárii se virtuální počítače Azure průběžně replikují z do jiné cílové oblasti. Pokud dojde k výpadku, můžete převzít služby virtuálních počítačů do sekundární oblasti a získat k nim přístup. Když všechno běží normálně, můžete navrátit služby po obnovení a pokračovat v práci v primárním umístění.



## <a name="architectural-components"></a>Komponenty architektury

Komponenty zapojené do zotavení po havárii pro virtuální počítače Azure jsou shrnuté v následující tabulce.

**Komponenta** | **Požadavky**
--- | ---
**Virtuální počítače ve zdrojové oblasti** | Jeden z dalších virtuálních počítačů Azure v [podporované zdrojové oblasti](azure-to-azure-support-matrix.md#region-support).<br/><br/> Virtuální počítače můžou běžet s [podporovaným operačním systémem](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).
**Úložiště zdrojového virtuálního počítače** | Virtuální počítače Azure je možné spravovat nebo mít nespravované disky, které jsou rozdělené mezi účty úložiště.<br/><br/>[Přečtěte si o](azure-to-azure-support-matrix.md#replicated-machines---storage) podporované službě Azure Storage.
**Zdrojové sítě virtuálních počítačů** | Virtuální počítače se můžou nacházet v jedné nebo několika podsítích ve virtuální síti (VNet) ve zdrojové oblasti. [Přečtěte si další informace](azure-to-azure-support-matrix.md#replicated-machines---networking) o požadavcích na síť.
**Účet úložiště mezipaměti** | V zdrojové síti potřebujete účet úložiště mezipaměti. Během replikace se změny virtuálních počítačů ukládají do mezipaměti, než se odešlou do cílového úložiště.  Účty úložiště mezipaměti musí být standardní.<br/><br/> Použití mezipaměti zajišťuje minimální dopad na provozní aplikace, které běží na virtuálním počítači.<br/><br/> [Další informace](azure-to-azure-support-matrix.md#cache-storage) o požadavcích na úložiště mezipaměti. 
**Cílové prostředky** | Cílové prostředky se používají během replikace a když dojde k převzetí služeb při selhání. Site Recovery může nastavit cílový prostředek ve výchozím nastavení, nebo je můžete vytvořit nebo přizpůsobit.<br/><br/> V cílové oblasti ověřte, že můžete vytvářet virtuální počítače a že vaše předplatné má dostatek prostředků pro podporu velikostí virtuálních počítačů, které budou potřeba v cílové oblasti. 

![Diagram znázorňující replikaci zdrojové a cílové replikace](./media/concepts-azure-to-azure-architecture/enable-replication-step-1-v2.png)

## <a name="target-resources"></a>Cílové prostředky

Když pro virtuální počítač povolíte replikaci, Site Recovery vám nabídne možnost automatického vytváření cílových prostředků. 

**Cílový prostředek** | **Výchozí nastavení**
--- | ---
**Cílové předplatné** | Stejné jako zdrojové předplatné.
**Cílová skupina prostředků** | Skupina prostředků, do které patří virtuální počítače po převzetí služeb při selhání.<br/><br/> Může to být v jakékoli oblasti Azure s výjimkou zdrojové oblasti.<br/><br/> Site Recovery vytvoří novou skupinu prostředků v cílové oblasti s příponou ASR.<br/><br/>
**Cílová virtuální síť** | Virtuální síť (VNet), ve které se po převzetí služeb při selhání nacházejí replikované virtuální počítače. Mapování sítě je vytvořeno mezi zdrojovou a cílovou virtuální sítí a naopak.<br/><br/> Site Recovery vytvoří novou virtuální síť a podsíť s příponou ASR.
**Cílový účet úložiště** |  Pokud virtuální počítač nepoužívá spravovaný disk, jedná se o účet úložiště, do kterého se data replikují.<br/><br/> Site Recovery vytvoří v cílové oblasti nový účet úložiště pro zrcadlení účtu zdrojového úložiště.
**Spravované disky replik** | Pokud virtuální počítač používá spravovaný disk, jedná se o spravované disky, na které se replikují data.<br/><br/> Site Recovery pro zrcadlení zdrojů vytvoří v oblasti úložiště spravované disky repliky.
**Cílové skupiny dostupnosti** |  Skupina dostupnosti, ve které se po převzetí služeb při selhání nacházejí replikované virtuální počítače.<br/><br/> Site Recovery vytvoří skupinu dostupnosti v cílové oblasti s příponou ASR pro virtuální počítače, které jsou umístěné ve skupině dostupnosti v umístění zdroje. Pokud existuje skupina dostupnosti, použije se a nová se nevytvoří.
**Cílové zóny dostupnosti** | Pokud cílová oblast podporuje zóny dostupnosti, Site Recovery přiřadí stejné číslo zóny jako to, které se používá ve zdrojové oblasti.

### <a name="managing-target-resources"></a>Správa cílových prostředků

Cílové prostředky můžete spravovat následujícím způsobem:

- Při povolování replikace můžete změnit cílová nastavení.
- Po dokončení replikace můžete změnit cílová nastavení. Počítejte s tím, že výchozí SKU pro virtuální počítač cílové oblasti je stejný jako SKU zdrojového virtuálního počítače (nebo další nejlepší dostupná skladová jednotka v porovnání s SKU zdrojového virtuálního počítače). Podobně jako u jiných prostředků, jako je cílová skupina prostředků, název cíle a další, je možné po replikaci aktualizovat také SKLADOVOU položku virtuálního počítače cílové oblasti. Prostředek, který nejde aktualizovat, je typ dostupnosti (jedna instance, sada nebo zóna). Pokud chcete toto nastavení změnit, musíte zakázat replikaci, upravit nastavení a pak znovu povolit. 


## <a name="replication-policy"></a>Zásady replikace 

Když povolíte replikaci virtuálních počítačů Azure, Site Recovery ve výchozím nastavení vytvoří novou zásadu replikace s výchozími nastaveními shrnutými v tabulce.

**Nastavení zásad** | **Podrobnosti** | **Výchozí**
--- | --- | ---
**Uchování bodu obnovení** | Určuje, jak dlouho Site Recovery udržuje body obnovení. | 24 hodin
**Frekvence snímků konzistentní vzhledem k aplikacím** | Jak často Site Recovery přebírá snímek konzistentní vzhledem k aplikacím. | Každé čtyři hodiny

### <a name="managing-replication-policies"></a>Správa zásad replikace

Výchozí nastavení zásad replikace můžete spravovat a upravovat následujícím způsobem:
- Nastavení můžete upravit při povolování replikace.
- Zásadu replikace můžete vytvořit kdykoli a pak ji použít při povolování replikace.

### <a name="multi-vm-consistency"></a>Konzistence s více virtuálními počítači

Pokud chcete, aby se virtuální počítače replikoval společně a aby při převzetí služeb při selhání sdílely body obnovení konzistentní vzhledem k aplikacím a konzistentní vzhledem k aplikacím, můžete je shromáždit dohromady do replikační skupiny. Konzistence s více virtuálními počítači ovlivňuje výkon úloh a měla by se používat jenom pro virtuální počítače, na kterých běží úlohy, které vyžadují konzistenci napříč všemi počítači. 



## <a name="snapshots-and-recovery-points"></a>Snímky a body obnovení

Body obnovení jsou vytvářeny ze snímků disků virtuálních počítačů pořízených v určitém časovém okamžiku. Při převzetí služeb při selhání virtuálního počítače použijete bod obnovení k obnovení virtuálního počítače v cílovém umístění.

Při převzetí služeb při selhání obecně chceme zajistit, aby se virtuální počítač spouštěl bez poškození nebo ztráty dat a aby se pro aplikace, které běží na virtuálním počítači, zajistila konzistence dat virtuálního počítače. To závisí na typu provedených snímků.

Site Recovery pořizuje snímky následujícím způsobem:

1. Když pro ně zadáte četnost, Site Recovery se ve výchozím nastavení přebírají snímky dat konzistentní vzhledem k chybám a snímky konzistentní vzhledem k aplikacím.
2. Body obnovení se vytvářejí ze snímků a ukládají se v souladu s nastaveními uchovávání v zásadách replikace.

### <a name="consistency"></a>Konzistence

Následující tabulka vysvětluje různé typy konzistence.

### <a name="crash-consistent"></a>Konzistentní vzhledem k selháním

**Popis** | **Podrobnosti** | **Doporučení**
--- | --- | ---
Snímek konzistentní se selháním zachycuje data, která byla na disku při pořízení snímku. Neobsahuje žádné množství paměti.<br/><br/> Obsahuje ekvivalent dat na disku, která by byla k dispozici v případě, že došlo k chybě virtuálního počítače nebo napájecí kabel byl získán ze serveru v okamžiku, kdy se snímek povedl.<br/><br/> Konzistentní se selháním nezaručuje konzistenci dat pro operační systém nebo pro aplikace na virtuálním počítači. | Ve výchozím nastavení vytvoří Site Recovery body obnovení konzistentní vzhledem k chybě každých pět minut. Toto nastavení nelze změnit.<br/><br/>  | V současné době se většina aplikací může obnovovat i z bodů konzistentních vzhledem k selháním.<br/><br/> Body obnovení konzistentní vzhledem k havárii jsou obvykle dostačující pro replikaci operačních systémů a aplikace, jako jsou servery DHCP a tiskové servery.

### <a name="app-consistent"></a>Konzistentní vzhledem k aplikacím

**Popis** | **Podrobnosti** | **Doporučení**
--- | --- | ---
Body obnovení konzistentní vzhledem k aplikacím se vytvářejí z snímků konzistentních vzhledem k aplikacím.<br/><br/> Snímek konzistentní vzhledem k aplikacím obsahuje všechny informace v snímku konzistentním s chybou a také všechna data v paměti a probíhajících transakcích. | Snímky konzistentní vzhledem k aplikacím používají služba Stínová kopie svazku (VSS):<br/><br/>   1) Azure Site Recovery používá metodu kopírování pouze pro zálohování (VSS_BT_COPY), která nemění čas zálohování protokolu transakce Microsoft SQL a číslo sekvence. </br></br> 2) když se spustí snímek, služba VSS provede na svazku operaci kopírování na zápis (KRÁVy).<br/><br/>   3) před provedením KRÁVy vytvoří služba Stínová kopie svazku každou aplikaci v počítači, kterou potřebuje k vyprázdnit data rezidentní paměti na disk.<br/><br/>   4) služba VSS pak umožní aplikaci pro zálohování nebo zotavení po havárii (v tomto případě Site Recovery) ke čtení dat snímku a pokračování. | Snímky konzistentní vzhledem k aplikacím jsou pořízeny podle četnosti, kterou zadáte. Tato frekvence by měla být vždy menší než nastavení pro zachování bodů obnovení. Pokud například zachováte body obnovení s použitím výchozího nastavení 24 hodin, měli byste nastavit četnost na méně než 24 hodin.<br/><br/>Jsou složitější a jejich dokončení trvá déle než snímky konzistentní se selháním.<br/><br/> Mají vliv na výkon aplikací spuštěných na virtuálním počítači, který je povolen pro replikaci. 

## <a name="replication-process"></a>Proces replikace

Pokud povolíte replikaci pro virtuální počítač Azure, dojde k následujícímu:

1. Na virtuálním počítači se automaticky nainstaluje rozšíření služby mobility Site Recovery.
2. Rozšíření registruje virtuální počítač s Site Recovery.
3. Pro virtuální počítač se spustí průběžná replikace.  Zápisy na disk se okamžitě přenesou do účtu úložiště mezipaměti ve zdrojovém umístění.
4. Site Recovery zpracuje data v mezipaměti a pošle je do cílového účtu úložiště nebo na spravované disky repliky.
5. Po zpracování dat se body obnovení konzistentní vzhledem k chybě generují každých pět minut. Body obnovení konzistentní vzhledem k aplikacím se generují podle nastavení určeného v zásadách replikace.

![Diagram znázorňující proces replikace, krok 2.](./media/concepts-azure-to-azure-architecture/enable-replication-step-2-v2.png)

**Proces replikace**

## <a name="connectivity-requirements"></a>Požadavky na připojení

 Virtuální počítače Azure, které replikujte, potřebují odchozí připojení. Site Recovery nikdy nepotřebuje příchozí připojení k virtuálnímu počítači. 

### <a name="outbound-connectivity-urls"></a>Odchozí připojení (adresy URL)

Pokud se odchozí přístup pro virtuální počítače ovládá pomocí adres URL, povolte tyto adresy URL.

| **Název**                  | **Komerční**                               | **Státní správa**                                 | **Popis** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`               | Umožňuje zápis dat z virtuálního počítače do účtu úložiště mezipaměti ve zdrojové oblasti. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Zajišťuje autorizaci a ověřování pro adresy URL služby Site Recovery. |
| Replikace               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`     | Umožňuje komunikaci virtuálního počítače se službou Site Recovery. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Umožňuje virtuálnímu počítači zapisovat data monitorování a diagnostiky Site Recovery. |
| Key Vault                 | `*.vault.azure.net`                        | `*.vault.usgovcloudapi.net`                  | Umožňuje přístup k povolení replikace pro virtuální počítače s podporou ADE přes portál. |
| Azure Automation          | `*.automation.ext.azure.com`               | `*.azure-automation.us`                      | Umožňuje povolit automatický upgrade agenta mobility pro replikovanou položku prostřednictvím portálu. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Odchozí připojení pro rozsahy IP adres

Chcete-li řídit odchozí připojení pro virtuální počítače pomocí IP adres, povolte tyto adresy.
Podrobnosti o požadavcích na připojení k síti najdete v [dokumentu White paper o sítích](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) . 

#### <a name="source-region-rules"></a>Pravidla zdrojové oblasti

**Pravidlo** |  **Podrobnosti** | **Značka služby**
--- | --- | --- 
Povolení odchozího HTTPS: port 443 | Umožňuje použít rozsahy, které odpovídají účtům úložiště ve zdrojové oblasti. | Pamì.\<region-name>
Povolení odchozího HTTPS: port 443 | Umožňuje použít rozsahy, které odpovídají Azure Active Directory (Azure AD).  | Azureactivedirectory selhala
Povolení odchozího HTTPS: port 443 | Povolí rozsahy, které odpovídají centru událostí v cílové oblasti. | EventsHub.\<region-name>
Povolení odchozího HTTPS: port 443 | Umožňuje použít rozsahy, které odpovídají Azure Site Recovery  | AzureSiteRecovery
Povolení odchozího HTTPS: port 443 | Povolit rozsahy, které odpovídají Azure Key Vault (to se vyžaduje jenom pro povolení replikace virtuálních počítačů s podporou ADE přes portál) | AzureKeyVault
Povolení odchozího HTTPS: port 443 | Povolit rozsahy, které odpovídají Azure Automation kontroler (to se vyžaduje jenom pro povolení automatického upgradu agenta mobility pro replikovanou položku prostřednictvím portálu) | GuestAndHybridManagement

#### <a name="target-region-rules"></a>Pravidla cílové oblasti

**Pravidlo** |  **Podrobnosti** | **Značka služby**
--- | --- | --- 
Povolení odchozího HTTPS: port 443 | Umožňuje použít rozsahy, které odpovídají účtům úložiště v cílové oblasti. | Pamì.\<region-name>
Povolení odchozího HTTPS: port 443 | Umožňuje použít rozsahy, které odpovídají službě Azure AD.  | Azureactivedirectory selhala
Povolení odchozího HTTPS: port 443 | Povolí rozsahy, které odpovídají centru událostí ve zdrojové oblasti. | EventsHub.\<region-name>
Povolení odchozího HTTPS: port 443 | Umožňuje použít rozsahy, které odpovídají Azure Site Recovery  | AzureSiteRecovery
Povolení odchozího HTTPS: port 443 | Povolit rozsahy, které odpovídají Azure Key Vault (to se vyžaduje jenom pro povolení replikace virtuálních počítačů s podporou ADE přes portál) | AzureKeyVault
Povolení odchozího HTTPS: port 443 | Povolit rozsahy, které odpovídají Azure Automation kontroler (to se vyžaduje jenom pro povolení automatického upgradu agenta mobility pro replikovanou položku prostřednictvím portálu) | GuestAndHybridManagement


#### <a name="control-access-with-nsg-rules"></a>Řízení přístupu pomocí pravidel NSG

Pokud ovládáte připojení virtuálních počítačů pomocí filtrování síťového provozu do a z sítí nebo podsítí Azure pomocí [pravidel NSG](../virtual-network/security-overview.md), vezměte v vědomí následující požadavky:

- Pravidla NSG pro zdrojovou oblast Azure by měla umožňovat odchozí přístup pro provoz replikace.
- Před vložením do produkčního prostředí doporučujeme vytvořit pravidla v testovacím prostředí.
- Místo povolování jednotlivých IP adres používejte [značky služeb](../virtual-network/security-overview.md#service-tags) .
    - Značky služby reprezentují skupinu předpon IP adres shromážděných dohromady, aby se při vytváření pravidel zabezpečení minimalizovala složitost.
    - Microsoft automaticky aktualizuje značky služeb v průběhu času. 
 
Přečtěte si další informace o [odchozím připojení](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) pro Site Recovery a o [řízení připojení pomocí skupin zabezpečení sítě](concepts-network-security-group-with-site-recovery.md).


### <a name="connectivity-for-multi-vm-consistency"></a>Konektivita pro konzistenci s více virtuálními počítači

Pokud povolíte konzistenci napříč několika virtuálními počítači, budou spolu počítače v replikační skupině komunikovat přes port 20004.
- Ujistěte se, že žádné zařízení brány firewall neblokuje interní komunikaci mezi virtuálními počítači přes port 20004.
- Pokud chcete do replikační skupiny zahrnout virtuální počítače s Linuxem, nezapomeňte ručně otevřít odchozí provoz na portu 20004 podle pokynů ke konkrétní verzi Linuxu.




## <a name="failover-process"></a>Proces převzetí služeb při selhání

Když zahájíte převzetí služeb při selhání, vytvoří se virtuální počítače v cílové skupině prostředků, cílové virtuální síti, cílové podsíti a v cílové sadě dostupnosti. Během převzetí služeb při selhání můžete použít libovolný bod obnovení.

![Diagram znázorňující proces převzetí služeb při selhání se zdrojovým a cílovým prostředím](./media/concepts-azure-to-azure-architecture/failover-v2.png)

## <a name="next-steps"></a>Další kroky

[Rychlá replikace](azure-to-azure-quickstart.md) virtuálního počítače Azure do sekundární oblasti.
