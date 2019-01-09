---
title: Architektura replikace z Azure do Azure ve službě Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek obsahuje přehled komponent a architektury používané při nastavování zotavení po havárii mezi oblastmi Azure pro virtuální počítače Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: raynew
ms.openlocfilehash: 797838b077993ddcb4120bcf48b026063abbe1ab
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105317"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Architektura pro zotavení po havárii Azure do Azure


Tento článek popisuje architekturu, komponenty a procesy používané při nasazení zotavení po havárii pro Azure virtual machines (VM) pomocí [Azure Site Recovery](site-recovery-overview.md) služby. Pomocí nastavení zotavení po havárii virtuální počítače Azure nepřetržitě replikovat z jiné cílové oblasti. Pokud dojde k výpadku, můžete převzít služby virtuálních počítačů do sekundární oblasti a přistupovat k nim tam. Když všechno, co běží normálně. znovu, můžete navrátit služby po obnovení a pokračovat v práci v primárním umístění.



## <a name="architectural-components"></a>Komponenty architektury

Součásti účastnící se zotavení po havárii pro virtuální počítače Azure jsou shrnuty v následující tabulce.

**Komponenta** | **Požadavky**
--- | ---
**Virtuální počítače ve zdrojové oblasti** | Jeden z virtuálních počítačů Azure v [podporované zdrojové oblasti](azure-to-azure-support-matrix.md#region-support).<br/><br/> Virtuální počítače může běžet jakýkoli [s podporovaným operačním systémem](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).
**Úložiště zdrojového virtuálního počítače** | Je možné spravovat virtuální počítače Azure, nebo mají nespravované disky rozprostřené mezi různými účty úložiště.<br/><br/>[Další informace o](azure-to-azure-support-matrix.md#replicated-machines---storage) podporované služby Azure storage.
**Zdrojové sítě virtuálních počítačů** | Virtuální počítače lze umístit do jedné nebo několika podsítí ve virtuální síti (VNet) ve zdrojové oblasti. [Další informace](azure-to-azure-support-matrix.md#replicated-machines---networking) o požadavky na síť.
**Účet úložiště mezipaměti** | Budete potřebovat účet úložiště mezipaměti ve zdrojové síti. Během replikace změny virtuálního počítače jsou uloženy v mezipaměti před odesláním do cílového úložiště.<br/><br/> Použití mezipaměti zajistí minimální dopad na produkční aplikace, které jsou spuštěny na virtuálním počítači.<br/><br/> [Další informace](azure-to-azure-support-matrix.md#cache-storage) o požadavcích na úložiště mezipaměti. 
**Prostředky cíle** | Cílové prostředky se používají během replikace a dojde k selhání. Site Recovery můžete nastavit cílový prostředek ve výchozím nastavení, nebo můžete vytvořit a přizpůsobit je.<br/><br/> V cílové oblasti zkontrolujte, že budete moct vytvořit virtuální počítače a že vaše předplatné má dostatek prostředků pro podporu velikosti virtuálních počítačů, které se mají provést v cílové oblasti. 

![Zdroj a cíl replikace](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

## <a name="target-resources"></a>Prostředky cíle

Když povolíte replikaci virtuálního počítače, Site Recovery poskytuje možnost vytvořit cílové prostředky automaticky. 

**Cílový prostředek** | **Výchozí nastavení**
--- | ---
**Cílové předplatné** | Stejné jako zdrojové předplatné.
**Cílová skupina prostředků** | Skupina prostředků, do které patří virtuální počítače po převzetí služeb při selhání.<br/><br/> Může být v libovolné oblasti Azure, s výjimkou zdrojové oblasti.<br/><br/> Site Recovery vytvoří novou skupinu prostředků v cílové oblasti s příponou "Azure Site Recovery".<br/><br/>
**Cílové virtuální sítě** | Virtuální síť (VNet) ve kterém jsou replikované virtuální počítače umístěné po převzetí služeb při selhání. Vytvoření mapování sítě mezi zdrojovou a cílovou virtuální sítí a naopak.<br/><br/> Site Recovery vytvoří novou virtuální síť a podsíť, s příponou "Azure Site Recovery".
**Cílový účet úložiště** |  Pokud virtuální počítač nepoužívá spravovaného disku, toto je účet úložiště, do kterého se data replikují.<br/><br/> Site Recovery vytvoří nový účet úložiště v cílové oblasti, pro zrcadlení zdrojového účtu úložiště.
**Spravované disky replik** | Pokud virtuální počítač používá spravovaný disk, je to spravované disky, které budou replikovat data.<br/><br/> Spravované disky repliky v oblasti úložiště zrcadlící zdrojového nastavení vytvoří Site Recovery.
**Cílové skupiny dostupnosti** |  Skupinu dostupnosti, ve které replikující virtuální počítače jsou umístěné po převzetí služeb při selhání.<br/><br/> Site Recovery vytvoří skupinu dostupnosti v cílové oblasti s příponou "Azure Site Recovery", pro virtuální počítače, které jsou umístěné ve skupině dostupnosti ve zdrojovém umístění. Pokud skupina dostupnosti existuje, se používá a není vytvořen nový.
**Cílové zóny dostupnosti** | Pokud cílové oblasti podporují zóny dostupnosti, Site Recovery přiřadí číslo zóny jako, který používá ve zdrojové oblasti.

### <a name="managing-target-resources"></a>Správa cílové prostředky

Cílové prostředky můžete spravovat takto:

- Nastavení cíle můžete upravit, jak povolit replikaci.
- Nastavení cíle můžete upravit po již funguje replikace. Výjimkou je typ dostupnosti (jednu instanci, set nebo zóny). Toto nastavení změníte, budete muset zakázat replikaci, změnit nastavení a potom znovu povolit.



## <a name="replication-policy"></a>Zásady replikace 

Když povolíte replikaci virtuálního počítače Azure, ve výchozím nastavení Site Recovery vytvoří novou zásadu replikace s výchozím nastavením uvedené v tabulce.

**Nastavení zásad** | **Podrobnosti** | **Výchozí**
--- | --- | ---
**Uchování bodu obnovení** | Určuje, jak dlouho uchovává body obnovení Site Recovery | 24 hodin
**Frekvence pořizování snímků konzistentních s aplikací** | Jak často Site Recovery pořídí konzistentního snímku. | Každých 60 minut.

### <a name="managing-replication-policies"></a>Správa zásad replikace

Můžete spravovat a upravovat výchozí nastavení zásady replikace následujícím způsobem:
- Nastavení můžete upravit, jak povolit replikaci.
- Můžete vytvořit zásady replikace v okamžiku a pak ji použijte, když povolíte replikaci.

### <a name="multi-vm-consistency"></a>Konzistence vzhledem k více virtuálním počítačům

Pokud chcete, aby virtuální počítače replikovat společně a sdíleli konzistentní při selhání a obnovení s konzistentní aplikací odkazuje na převzetí služeb při selhání, můžete shromáždit je společně do replikační skupiny. Konzistence více virtuálních počítačů má vliv na výkon úloh a by měla sloužit pouze pro virtuální počítače spuštěné úlohy, které je třeba konzistence ve všech počítačích. 



## <a name="snapshots-and-recovery-points"></a>Snímky a body obnovení

Body obnovení jsou vytvářeny ze snímků disků virtuálních počítačů, které jsou provedeny v konkrétním bodě v čase. Převzetí služeb při selhání virtuálního počítače, použijete k obnovení virtuálního počítače v cílovém umístění bodu obnovení.

Při převzetí služeb při selhání, obecně chceme se ujistit, že virtuální počítač spustí bez poškození nebo ztrátě dat a že data virtuálního počítače konzistentní vzhledem k aplikacím pro operační systém a pro aplikace, které běží na virtuálním počítači. To závisí na typu snímkům pořízeným.

Site Recovery pořídí snímky následujícím způsobem:

1. Site Recovery pořídí konzistentní snímky dat ve výchozím nastavení a snímky konzistentní s, pokud je pro ně zadat frekvenci.
2. Body obnovení jsou vytvářeny ze snímků a uložená v souladu s nastavení uchovávání dat v zásadách replikace.

### <a name="consistency"></a>Konzistence

Následující tabulka vysvětluje různé typy konzistence.

### <a name="crash-consistent"></a>Konzistentní vzhledem k selháním

**Popis** | **Podrobnosti** | **Doporučení**
--- | --- | ---
Snímek konzistentní s havárií shromažďuje data, která byla na disku při pořízení snímku. Nic neobsahuje v paměti.<br/><br/> Obsahuje ekvivalentní data na disku, která by byla k dispozici, pokud došlo k chybě virtuální počítač nebo napájecí kabel byl stažen ze serveru v okamžiku, která pořízení snímku.<br/><br/> Konzistentní při selhání nezaručuje konzistenci dat pro operační systém nebo aplikace na virtuálním počítači. | Site Recovery vytvoří body obnovení konzistentní při selhání každých 5 minut, ve výchozím nastavení. Toto nastavení nejde změnit.<br/><br/>  | V současné době většina aplikací můžete obnovit také ze body konzistentní při selhání.<br/><br/> Body obnovení konzistentní při selhání jsou obvykle stačí pro replikaci operačních systémů a aplikací, jako jsou servery DHCP a tiskových serverů.

### <a name="app-consistent"></a>Konzistentní vzhledem k aplikacím
**Popis** | **Podrobnosti** | **Doporučení**
--- | --- | ---
Body obnovení konzistentní vzhledem k aplikaci vytvořené ze snímků konzistentní vzhledem k.<br/><br/> Konzistentní vzhledem k snímku obsahovat všechny informace v snímky konzistentní s havárií a veškerá data v paměti a probíhající transakce. | Snímky konzistentní s pomocí svazků služby Stínová kopie (VSS):<br/><br/>   1) při zahájení snímku VSS provést operaci kopírování při zápisu (krávy) na svazku.<br/><br/>   (2) předtím, než provede krávy, informuje VSS každá aplikace na počítači, který je třeba vyprázdnění rezidentní data na disk.<br/><br/>   (3) stínové kopie svazku pak umožní aplikaci obnovení zálohování/zotavení po havárii (v tomto případě Site Recovery) číst data snímku a pokračovat. | Snímky konzistentní s přejdete v souladu s frekvencí, kterou zadáte. Četnost odesílání by měla být vždy nižší než nastavení pro zachování body obnovení. Například pokud můžete zachovat body obnovení pomocí výchozího nastavení 24 hodin, měli byste nastavit frekvenci, s méně než 24 hodin.<br/><br/>Jsou mnohem složitější a trvat déle než snímky konzistentní při selhání.<br/><br/> Jejich vliv na výkon aplikace, které běží na virtuálním počítači povolena replikace. | <br/><br/>Body obnovení konzistentní se doporučují pro databázi operačních systémů a aplikací, jako jsou SQL.<br/><br/> Snímky konzistentní se podporují jenom pro virtuální počítače se systémem Windows.

## <a name="replication-process"></a>Proces replikace

Když povolíte replikaci pro virtuální počítač Azure, dojde k následujícímu:

1. Rozšíření služby Site Recovery Mobility se automaticky nainstaluje na virtuálním počítači.
2. Rozšíření zaregistruje virtuální počítač pomocí Site Recovery.
3. Začíná průběžnou replikaci pro virtuální počítač.  Zápisy na disk okamžitě přenášejí do účtu úložiště mezipaměti ve zdrojovém umístění.
4. Site Recovery zpracovává data v mezipaměti a posílá ji do cílového účtu úložiště nebo do repliky spravovaných disků.
5. Po zpracování dat, vygenerují se body obnovení konzistentní při selhání každých pět minut. Body obnovení konzistentní se generují podle nastavení nakonfigurované v zásadách replikace.


   ![Povolit replikaci, krok 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

**Proces replikace**

## <a name="connectivity-requirements"></a>Požadavky na připojení

 Virtuální počítače Azure, které replikujete potřebují odchozí připojení. Site Recovery potřebuje nikdy příchozí připojení k virtuálnímu počítači. 

### <a name="outbound-connectivity-urls"></a>Odchozí připojení (adresy URL)

Pokud odchozí přístup pro virtuální počítače se řídí pomocí adresy URL, povolte tyto adresy URL.

| **Adresa URL** | **Podrobnosti** |
| ------- | ----------- |
| *.blob.core.windows.net | Umožňuje zápis dat z virtuálního počítače do účtu úložiště mezipaměti ve zdrojové oblasti. |
| login.microsoftonline.com | Zajišťuje autorizaci a ověřování pro adresy URL služby Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Umožňuje komunikaci virtuálního počítače se službou Site Recovery. |
| *.servicebus.windows.net | Umožňuje virtuálnímu počítači zapisovat data monitorování a diagnostiky Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Odchozí připojení pro rozsahy IP adres

K řízení odchozího připojení pro virtuální počítače pomocí IP adresy, povolte tyto adresy.

#### <a name="source-region-rules"></a>Zdrojové oblasti pravidla

**Pravidlo** |  **Podrobnosti** | **Značka služby**
--- | --- | --- 
Povolit HTTPS odchozí: port 443 | Povolte rozsahy, které odpovídají na účty úložiště ve zdrojové oblasti | Úložiště. < název oblasti >.
Povolit HTTPS odchozí: port 443 | Povolte rozsahy, které odpovídají Azure Active Directory (Azure AD).<br/><br/> Pokud se v budoucnu přidají adresy služby Azure AD musíte vytvořit nová pravidla skupiny zabezpečení sítě (NSG).  | AzureActiveDirectory
Povolit HTTPS odchozí: port 443 | Povolit přístup k [koncovými body Site Recovery](https://aka.ms/site-recovery-public-ips) , které odpovídají do cílového umístění. 

#### <a name="target-region-rules"></a>Cílové oblasti pravidla

**Pravidlo** |  **Podrobnosti** | **Značka služby**
--- | --- | --- 
Povolit HTTPS odchozí: port 443 | Povolte rozsahy, které odpovídají na účty úložiště v cílové oblasti. | Úložiště. < název oblasti >.
Povolit HTTPS odchozí: port 443 | Povolte rozsahy, které odpovídají do služby Azure AD.<br/><br/> Pokud se v budoucnu přidají adresy služby Azure AD musíte vytvořit nová pravidla skupiny zabezpečení sítě.  | AzureActiveDirectory
Povolit HTTPS odchozí: port 443 | Povolit přístup k [koncovými body Site Recovery](https://aka.ms/site-recovery-public-ips) , které odpovídají umístění zdroje. 


#### <a name="control-access-with-nsg-rules"></a>Řízení přístupu pomocí pravidel skupiny zabezpečení sítě

Pokud připojení virtuálních počítačů pomocí filtrování síťového provozu do a z Azure pomocí sítí/podsítí [pravidla NSG](https://docs.microsoft.com/azure/virtual-network/security-overview), mějte na paměti následující požadavky:

- Pravidla NSG pro zdrojovou oblast Azure by měl povolit odchozí přístup pro přenosy replikace.
- Doporučujeme že vytvořit pravidla v testovacím prostředí před uvést do provozu.
- Použití [značky služeb](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) namísto povolení jednotlivých IP adres.
    - Značky služby představuje skupinu předpon IP adres shromážděných společně za účelem minimalizovat složitost při vytváření pravidel zabezpečení.
    - Microsoft automaticky aktualizuje značky služeb v čase. 
 
Další informace o [odchozí připojení](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) pro Site Recovery, a [řízení připojení pomocí skupin zabezpečení sítě](concepts-network-security-group-with-site-recovery.md).


### <a name="connectivity-for-multi-vm-consistency"></a>Připojení pro zajištění konzistence více virtuálních počítačů

Pokud povolíte konzistenci napříč několika virtuálními počítači, budou spolu počítače v replikační skupině komunikovat přes port 20004.
- Ujistěte se, že žádné zařízení brány firewall neblokuje interní komunikaci mezi virtuálními počítači přes port 20004.
- Pokud chcete do replikační skupiny zahrnout virtuální počítače s Linuxem, nezapomeňte ručně otevřít odchozí provoz na portu 20004 podle pokynů ke konkrétní verzi Linuxu.




## <a name="failover-process"></a>Proces převzetí služeb při selhání

Při zahájení převzetí služeb virtuálních počítačů vytvořená v cílovou skupinu prostředků, cílovou virtuální sítí, cílové podsítě a ve skupině dostupnosti cílové nastavovat. Při selhání můžete použít jakýkoli bod obnovení.

![Proces převzetí služeb při selhání](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Další postup

[Rychle replikovat](azure-to-azure-quickstart.md) virtuálního počítače Azure do sekundární oblasti.
