---
title: Architektura azure to azure zotavení po havárii v Azure Site Recovery
description: Přehled architektury používané při nastavování zotavení po havárii mezi oblastmi Azure pro virtuální počítače Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: raynew
ms.openlocfilehash: 94da1639b5398a03b36fba3ff88877468a97ec36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294119"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Architektura zotavení po havárii Azure do Azure


Tento článek popisuje architekturu, komponenty a procesy používané při nasazení zotavení po havárii pro virtuální počítače Azure (VM) pomocí služby [Azure Site Recovery.](site-recovery-overview.md) S nastavením zotavení po havárii se virtuální počítače Azure průběžně replikují z jiné cílové oblasti. Pokud dojde k výpadku, můžete převzetí služeb při selhání virtuálních připojení do sekundární oblasti a přístup k nim odtud. Když je vše spuštěno normálně znovu, můžete znovu navrácení služeb po selhání a pokračovat v práci v primárním umístění.



## <a name="architectural-components"></a>Komponenty architektury

Součásti, které se účastní zotavení po havárii pro virtuální počítače Azure, jsou shrnuty v následující tabulce.

**Komponenta** | **Požadavky**
--- | ---
**Virtuální virtuální společnosti ve zdrojové oblasti** | Jeden z dalších virtuálních počítačů Azure ve [podporované zdrojové oblasti](azure-to-azure-support-matrix.md#region-support).<br/><br/> Virtuální můe spouštět libovolný [podporovaný operační systém](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).
**Zdrojové úložiště virtuálních zařízení** | Virtuální počítače Azure můžou být spravované nebo mají nespravované disky rozprostřené mezi účty úložiště.<br/><br/>[Přečtěte si o](azure-to-azure-support-matrix.md#replicated-machines---storage) podporovaném úložišti Azure.
**Zdrojové sítě virtuálních ms** | Virtuální počítače se můžou nacházet v jedné nebo více podsítích ve virtuální síti (Virtuální síť) ve zdrojové oblasti. [Přečtěte si další informace](azure-to-azure-support-matrix.md#replicated-machines---networking) o požadavcích na síť.
**Účet úložiště mezipaměti** | Potřebujete účet úložiště mezipaměti ve zdrojové síti. Během replikace se změny virtuálních zařízení ukládají do mezipaměti před odesláním do cílového úložiště.  Účty úložiště mezipaměti musí být standardní.<br/><br/> Použití mezipaměti zajišťuje minimální dopad na produkční aplikace, které jsou spuštěny na virtuálním počítači.<br/><br/> [Přečtěte si další informace](azure-to-azure-support-matrix.md#cache-storage) o požadavcích na úložiště mezipaměti. 
**Cílové zdroje** | Cílové prostředky se používají během replikace a při převzetí služeb při selhání. Site Recovery můžete nastavit cílový prostředek ve výchozím nastavení, nebo můžete vytvořit nebo přizpůsobit.<br/><br/> V cílové oblasti zkontrolujte, jestli můžete vytvářet virtuální počítače a jestli má vaše předplatné dostatek prostředků pro podporu velikostí virtuálních počítačů, které budou potřeba v cílové oblasti. 

![Zdrojová a cílová replikace](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

## <a name="target-resources"></a>Cílové zdroje

Když povolíte replikaci pro virtuální hod, obnovení lokality vám dává možnost vytvářet cílové prostředky automaticky. 

**Cílový prostředek** | **Výchozí nastavení**
--- | ---
**Cílové předplatné** | Stejné jako zdrojové předplatné.
**Cílová skupina prostředků** | Skupina prostředků, do které virtuální chod patří po převzetí služeb při selhání.<br/><br/> Může být v libovolné oblasti Azure s výjimkou zdrojové oblasti.<br/><br/> Obnovení webu vytvoří novou skupinu prostředků v cílové oblasti s příponou asr.<br/><br/>
**Cílová virtuální síť** | Virtuální síť (VNet), ve kterém jsou replikované virtuální počítače umístěny po převzetí služeb při selhání. Mapování sítě je vytvořeno mezi zdrojovými a cílovými virtuálními sítěmi a naopak.<br/><br/> Obnovení webu vytvoří novou virtuální síť a podsíť s příponou "asr".
**Cílový účet úložiště** |  Pokud virtuální počítače nepoužívá spravovaný disk, jedná se o účet úložiště, ke kterému se replikují data.<br/><br/> Site Recovery vytvoří nový účet úložiště v cílové oblasti, zrcadlit účet zdrojového úložiště.
**Replika spravovaných disků** | Pokud virtuální modul používá spravovaný disk, jedná se o spravované disky, na které se replikují data.<br/><br/> Site Recovery vytvoří repliky spravované disky v oblasti úložiště zrcadlit zdroj.
**Cílové skupiny dostupnosti** |  Dostupnost sada, ve kterém replikace virtuálních počítače jsou umístěny po převzetí služeb při selhání.<br/><br/> Site Recovery vytvoří sadu dostupnosti v cílové oblasti s příponou "asr", pro virtuální servery, které jsou umístěny v sadě dostupnosti ve zdrojovém umístění. Pokud existuje dostupnost, používá se a není vytvořena nová.
**Cílové zóny dostupnosti** | Pokud cílová oblast podporuje zóny dostupnosti, site recovery přiřadí stejné číslo zóny jako ve zdrojové oblasti.

### <a name="managing-target-resources"></a>Správa cílových zdrojů

Cílové zdroje můžete spravovat následujícím způsobem:

- Při povolování replikace můžete měnit nastavení cíle.
- Nastavení cíle můžete upravit po již fungující replikaci. Výjimkou je typ dostupnosti (jedna instance, sada nebo zóna). Chcete-li toto nastavení změnit, je třeba zakázat replikaci, upravit nastavení a poté znovu povolit.



## <a name="replication-policy"></a>Zásady replikace 

Když povolíte replikaci virtuálních počítačích Azure, ve výchozím nastavení site recovery vytvoří novou zásadu replikace s výchozím nastavením sumarovaným v tabulce.

**Nastavení zásad** | **Podrobnosti** | **Výchozí**
--- | --- | ---
**Uchování bodu obnovení** | Určuje, jak dlouho bude obnovení sítě uchovávat body obnovení. | 24 hodin
**Frekvence snímků konzistentních s aplikací** | Jak často site recovery trvá snímek konzistentní s aplikací. | Každé čtyři hodiny

### <a name="managing-replication-policies"></a>Správa zásad replikace

Výchozí nastavení zásad replikace můžete spravovat a upravovat následujícím způsobem:
- Nastavení můžete upravit při povolování replikace.
- Zásady replikace můžete kdykoli vytvořit a potom ji použít, když povolíte replikaci.

### <a name="multi-vm-consistency"></a>Konzistence více virtuálních mís

Pokud chcete, aby se virtuální ms replikovaly společně a při převzetí služeb při selhání sdílely body obnovení konzistentní s havárií a konzistentní s aplikací, můžete je shromáždit do replikační skupiny. Konzistence více virtuálních počítačů má vliv na výkon pracovního vytížení a měla by se používat jenom pro virtuální počítače se spuštěnou úlohou, která vyžadují konzistenci ve všech počítačích. 



## <a name="snapshots-and-recovery-points"></a>Snímky a body obnovení

Body obnovení se vytvářejí ze snímků disků virtuálních počítačů pořízených v určitém časovém okamžiku. Při převzetí služeb při selhání virtuálního virtuálního míse, použijete bod obnovení k obnovení virtuálního virtuálního provozu v cílovém umístění.

Při převzetí služeb při selhání obecně chceme zajistit, že virtuální počítač začíná bez poškození nebo ztráty dat a že data virtuálního počítače je konzistentní pro operační systém a pro aplikace, které běží na virtuálním počítači. To závisí na typu pořízených snímků.

Obnovení webu pořizuje snímky následujícím způsobem:

1. Site Recovery pořizuje snímky dat konzistentní s havárií ve výchozím nastavení a snímky konzistentní s aplikací, pokud pro ně zadáte frekvenci.
2. Body obnovení jsou vytvořeny ze snímků a uloženy v souladu s nastavením uchování v zásadách replikace.

### <a name="consistency"></a>Konzistence

Následující tabulka vysvětluje různé typy konzistence.

### <a name="crash-consistent"></a>Konzistentní s havárií

**Popis** | **Podrobnosti** | **Doporučení**
--- | --- | ---
Snímek konzistentní selhání zachycuje data, která byla na disku při pořízení snímku. Neobsahuje nic v paměti.<br/><br/> Obsahuje ekvivalent dat na disku, která by byla přítomna, pokud by došlo k chybě virtuálního počítače nebo byl napájecí kabel vyňat ze serveru v okamžiku pořízení snímku.<br/><br/> Konzistentní s havárií nezaručuje konzistenci dat pro operační systém nebo pro aplikace na virtuálním počítači. | Obnovení webu ve výchozím nastavení vytváří body obnovení konzistentní s havárií každých pět minut. Toto nastavení nelze změnit.<br/><br/>  | Dnes se většina aplikací dokáže dobře zotavit z bodů konzistentních při selhání.<br/><br/> Body obnovení konzistentní s havárií jsou obvykle dostačující pro replikaci operačních systémů a aplikací, jako jsou servery DHCP a tiskové servery.

### <a name="app-consistent"></a>Konzistentní s aplikacemi

**Popis** | **Podrobnosti** | **Doporučení**
--- | --- | ---
Body obnovení konzistentní s aplikací se vytvářejí ze snímků konzistentních s aplikací.<br/><br/> Snímek konzistentní s aplikací obsahuje všechny informace ve snímku konzistentním s havárií a všechna data v paměti a probíhající transakce. | Snímky konzistentní s aplikacemi používají službu Stínová kopie svazku (VSS):<br/><br/>   1) Při spuštění snímku VSS provést operaci kopírování při zápisu (COW) na svazku.<br/><br/>   2) Před provedením COW, VSS informuje každou aplikaci v počítači, že potřebuje vyprázdnit své paměti rezidentní data na disk.<br/><br/>   3) VSS pak umožňuje zálohování / zotavení po havárii aplikace (v tomto případě Site Recovery) číst data snímku a pokračovat. | Snímky konzistentní s aplikacemi se pořazují v souladu s zadanou frekvencí. Tato frekvence by měla být vždy menší, než jste nastavili pro zachování bodů obnovení. Pokud například zachováte body obnovení pomocí výchozího nastavení 24 hodin, měli byste nastavit frekvenci na méně než 24 hodin.<br/><br/>Jsou složitější a jejich dokončení trvá déle než snímky konzistentní s havárií.<br/><br/> Ovlivňují výkon aplikací spuštěných na virtuálním počítači povoleném pro replikaci. 

## <a name="replication-process"></a>Proces replikace

Když povolíte replikaci pro virtuální počítač Azure, stane se následující:

1. Rozšíření služby Mobility site recovery se automaticky nainstaluje na virtuální ms.
2. Rozšíření zaregistruje virtuální ho s site recovery.
3. Pro virtuální ho začne nepřetržitá replikace.  Zápisy na disku jsou okamžitě přeneseny do účtu úložiště mezipaměti ve zdrojovém umístění.
4. Site Recovery zpracuje data v mezipaměti a odešle je do cílového účtu úložiště nebo na spravované disky repliky.
5. Po zpracování dat jsou každých pět minut generovány body obnovení konzistentní s havárií. Body obnovení konzistentní s aplikací jsou generovány podle nastavení zadaného v zásadách replikace.

![Povolit proces replikace, krok 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

**Proces replikace**

## <a name="connectivity-requirements"></a>Požadavky na připojení

 Replikované virtuální počítače Azure vyžadují odchozí připojení. Obnovení webu nikdy nepotřebuje příchozí připojení k virtuálnímu virtuálnímu síti. 

### <a name="outbound-connectivity-urls"></a>Odchozí připojení (adresy URL)

Pokud je odchozí přístup pro virtuální hody řízen pomocí adres URL, povolte tyto adresy URL.

| **Adresa URL** | **Podrobnosti** |
| ------- | ----------- |
| *.blob.core.windows.net | Umožňuje zápis dat z virtuálního počítače do účtu úložiště mezipaměti ve zdrojové oblasti. |
| login.microsoftonline.com | Zajišťuje autorizaci a ověřování pro adresy URL služby Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Umožňuje komunikaci virtuálního počítače se službou Site Recovery. |
| *.servicebus.windows.net | Umožňuje virtuálnímu počítači zapisovat data monitorování a diagnostiky Site Recovery. |
| *.vault.azure.net | Umožňuje přístup k povolení replikace pro virtuální počítače s podporou ADE prostřednictvím portálu.
| *.automation.ext.azure.com | Umožňuje automatické upgradování agenta mobility pro replikovanou položku prostřednictvím portálu

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Odchozí připojení pro rozsahy IP adres

Chcete-li řídit odchozí připojení pro virtuální počítačů pomocí IP adres, povolte tyto adresy.
Vezměte prosím na vědomí, že podrobnosti o požadavcích na připojení k síti naleznete v [dokumentu white paper](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) 

#### <a name="source-region-rules"></a>Pravidla zdrojové oblasti

**Pravidlo** |  **Podrobnosti** | **Značka služby**
--- | --- | --- 
Povolit odchozí HTTPS: port 443 | Povolit rozsahy, které odpovídají účtům úložiště ve zdrojové oblasti | Úložiště. \<> názvu oblasti
Povolit odchozí HTTPS: port 443 | Povolit rozsahy, které odpovídají službě Azure Active Directory (Azure AD)  | AzureActiveDirectory
Povolit odchozí HTTPS: port 443 | Povolit rozsahy, které odpovídají události hub v cílové oblasti. | EventsHub. \<> názvu oblasti
Povolit odchozí HTTPS: port 443 | Povolit rozsahy, které odpovídají azure site recovery  | AzureSiteRecovery
Povolit odchozí HTTPS: port 443 | Povolit rozsahy, které odpovídají Azure Key Vault (To je nutné pouze pro povolení replikace virtuálních počítačů s podporou ADE prostřednictvím portálu) | AzureKeyVault
Povolit odchozí HTTPS: port 443 | Povolit rozsahy, které odpovídají řadiči Azure Automation Controller (To je vyžadováno pouze pro povolení automatického upgradu agenta mobility pro replikovanou položku prostřednictvím portálu) | GuestAndHybridManagement

#### <a name="target-region-rules"></a>Pravidla cílové oblasti

**Pravidlo** |  **Podrobnosti** | **Značka služby**
--- | --- | --- 
Povolit odchozí HTTPS: port 443 | Povolit rozsahy, které odpovídají účtům úložiště v cílové oblasti | Úložiště. \<> názvu oblasti
Povolit odchozí HTTPS: port 443 | Povolit rozsahy, které odpovídají službě Azure AD  | AzureActiveDirectory
Povolit odchozí HTTPS: port 443 | Povolit rozsahy, které odpovídají Události Hub ve zdrojové oblasti. | EventsHub. \<> názvu oblasti
Povolit odchozí HTTPS: port 443 | Povolit rozsahy, které odpovídají azure site recovery  | AzureSiteRecovery
Povolit odchozí HTTPS: port 443 | Povolit rozsahy, které odpovídají Azure Key Vault (To je nutné pouze pro povolení replikace virtuálních počítačů s podporou ADE prostřednictvím portálu) | AzureKeyVault
Povolit odchozí HTTPS: port 443 | Povolit rozsahy, které odpovídají řadiči Azure Automation Controller (To je vyžadováno pouze pro povolení automatického upgradu agenta mobility pro replikovanou položku prostřednictvím portálu) | GuestAndHybridManagement


#### <a name="control-access-with-nsg-rules"></a>Řízení přístupu pomocí pravidel nsg

Pokud řídíte připojení virtuálních počítačích filtrováním síťového provozu do a z sítí/podsítí Azure pomocí [pravidel skupiny zabezpečení sítě ,](https://docs.microsoft.com/azure/virtual-network/security-overview)poznamenejte si následující požadavky:

- Pravidla souborů nsg pro zdrojovou oblast Azure by měla umožňovat odchozí přístup pro provoz replikace.
- Doporučujeme vytvořit pravidla v testovacím prostředí před jejich uvázení do produkčního prostředí.
- Místo povolení jednotlivých adres IP používejte [značky služeb.](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)
    - Značky služeb představují skupinu předpon IP adres shromážděných společně, aby se minimalizovala složitost při vytváření pravidel zabezpečení.
    - Společnost Microsoft automaticky aktualizuje značky služeb v průběhu času. 
 
Další informace o [odchozím připojení](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) pro site recovery a [řízení připojení se sítěmi nsg .](concepts-network-security-group-with-site-recovery.md)


### <a name="connectivity-for-multi-vm-consistency"></a>Připojení pro konzistenci více virtuálních zařízení

Pokud povolíte konzistenci napříč několika virtuálními počítači, budou spolu počítače v replikační skupině komunikovat přes port 20004.
- Ujistěte se, že žádné zařízení brány firewall neblokuje interní komunikaci mezi virtuálními počítači přes port 20004.
- Pokud chcete do replikační skupiny zahrnout virtuální počítače s Linuxem, nezapomeňte ručně otevřít odchozí provoz na portu 20004 podle pokynů ke konkrétní verzi Linuxu.




## <a name="failover-process"></a>Proces převzetí služeb při selhání

Když zahájíte převzetí služeb při selhání, virtuální počítače se vytvoří v cílové skupině prostředků, cílové virtuální síti, cílové podsíti a v cílové skupině dostupnosti. Během převzetí služeb při selhání můžete použít libovolný bod obnovení.

![Proces převzetí služeb při selhání](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Další kroky

[Rychle replikovat](azure-to-azure-quickstart.md) virtuální počítač Azure do sekundární oblasti.
