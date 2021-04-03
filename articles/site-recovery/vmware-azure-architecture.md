---
title: Architektura zotavení po havárii virtuálního počítače VMware v Azure Site Recovery
description: Tento článek poskytuje přehled komponent a architektury, které se používají při nastavování zotavení po havárii místních virtuálních počítačů VMware do Azure pomocí Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 5cf4dc5123040fd2af8efe54153867a8709fe1ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97652224"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>Architektura zotavení po havárii z VMware do Azure

Tento článek popisuje architekturu a procesy používané při nasazení replikace pro zotavení po havárii, převzetí služeb při selhání a obnovení virtuálních počítačů VMware mezi místní lokalitou VMware a Azure pomocí služby [Azure Site Recovery](site-recovery-overview.md) .


## <a name="architectural-components"></a>Komponenty architektury

Následující tabulka a grafika obsahují podrobný pohled na součásti používané pro zotavení po havárii VMware do Azure.

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Azure** | Předplatné Azure, Azure Storage účet pro mezipaměť, spravovaný disk a síť Azure. | Replikovaná data z místních virtuálních počítačů se ukládají ve službě Azure Storage. Virtuální počítače Azure se při spuštění převzetí služeb při selhání z místního prostředí do Azure vytvoří s replikovanými daty. Virtuální počítače Azure se připojí k virtuální síti Azure po svém vytvoření.
**Počítač konfiguračního serveru** | Jeden místní počítač. Doporučujeme spustit ho jako virtuální počítač VMware, který se dá nasadit ze stažené šablony OVF.<br/><br/> Počítač spustí všechny místní Site Recovery komponenty, mezi které patří konfigurační server, procesový Server a hlavní cílový server. | **Konfigurační server**: koordinuje komunikaci mezi místními a Azure a spravuje replikaci dat.<br/><br/> **Procesový Server**: ve výchozím nastavení je na konfiguračním serveru nainstalován. Přijímá data replikace; optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování. a pošle ji Azure Storage. Procesový server také na virtuální počítače, které chcete replikovat, nainstaluje službu Azure Site Recovery Mobility Service a automaticky vyhledá místní virtuální počítače. Jak vaše nasazení poroste, můžete přidat další samostatné procesové servery, které budou zpracovávat větší objemy replikačních přenosů.<br/><br/> **Hlavní cílový server**: instaluje se ve výchozím nastavení na konfiguračním serveru. Zpracovává replikační data během navrácení služeb po obnovení z Azure. U rozsáhlých nasazení můžete přidat další samostatný hlavní cílový server pro navrácení služeb po obnovení.
**Servery VMware** | Virtuální počítače VMware se hostují na místních serverech vSphere ESXi. Pro správu hostitelů doporučujeme Server vCenter. | Během nasazení Site Recovery přidáte do trezoru Recovery Services servery VMware.
**Replikované počítače** | Na každém virtuálním počítači VMware, který budete replikovat, se nainstaluje služba mobility. | Doporučujeme, abyste povolili automatickou instalaci z procesového serveru. Alternativně můžete službu nainstalovat ručně nebo použít metodu automatizovaného nasazení, například Configuration Manager.

![Diagram znázorňující vztahy architektury VMware to Azure pro replikaci.](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="set-up-outbound-network-connectivity"></a>Nastavení odchozího připojení k síti

Aby mohla Site Recovery fungovat podle očekávání, musíte upravit odchozí síťové připojení, aby bylo možné prostředí replikovat.

> [!NOTE]
> Site Recovery nepodporuje připojení k síti pomocí ověřovacího proxy serveru.

### <a name="outbound-connectivity-for-urls"></a>Odchozí připojení pro adresy URL

Pokud k řízení odchozího připojení používáte proxy server brány firewall založený na adrese URL, povolte přístup k těmto adresám URL:

| **Název**                  | **Komerční**                               | **Státní správa**                                 | **Popis** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Umožňuje zápis dat z virtuálního počítače do účtu úložiště mezipaměti ve zdrojové oblasti. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Zajišťuje autorizaci a ověřování pro adresy URL služby Site Recovery. |
| Replikace               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Umožňuje komunikaci virtuálního počítače se službou Site Recovery. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Umožňuje virtuálnímu počítači zapisovat data monitorování a diagnostiky Site Recovery. |

Úplný seznam adres URL, které se mají filtrovat pro komunikaci mezi místní infrastrukturou Azure Site Recovery a službami Azure, najdete [v části požadavky na síť v článku](vmware-azure-deploy-configuration-server.md#prerequisites)požadavky.

## <a name="replication-process"></a>Proces replikace

1. Když pro virtuální počítač povolíte replikaci, začne počáteční replikace do Azure Storage s použitím zadaných zásad replikace. Všimněte si, že:
    - U virtuálních počítačů VMware je replikace na úrovni bloků téměř nepřetržitá a používá agenta služby mobility běžícího na virtuálním počítači.
    - Platí všechna nastavení zásad replikace:
        - **Prahová hodnota cíle RPO**. Toto nastavení nemá vliv na replikaci. Pomáhá s monitorováním. Dojde k vyvolání události a případně odeslání e-mailu, pokud aktuální cíl bodu obnovení překročí prahovou hodnotu, kterou určíte.
        - **Uchování bodu obnovení**. Toto nastavení určuje, jak daleko v čase chcete přejít, když dojde k přerušení. Maximální doba uchovávání Premium Storage je 24 hodin. Ve standardním úložišti je to 72 hodin. 
        - **Snímky konzistentní vzhledem k aplikacím**. Snímek konzistentní vzhledem k aplikacím se dá v závislosti na potřebách aplikace pořídit každých 1 až 12 hodin. Snímky jsou standardní snímky objektů BLOB v Azure. Agent mobility běžící na virtuálním počítači požádá o snímek služby Stínová kopie svazku (VSS) v souladu s tímto nastavením a záložky, které v daném okamžiku odpovídají bodu konzistentnímu vzhledem k aplikacím v datovém proudu replikace.

2. Provoz se replikuje do veřejných koncových bodů Azure Storage přes Internet. Alternativně můžete Azure ExpressRoute použít s [partnerským vztahem Microsoftu](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). Replikace provozu přes virtuální privátní síť (VPN) typu Site-to-Site z místní lokality do Azure se nepodporuje.
3. Operace počáteční replikace zajistí, že se do Azure pošle celá data v počítači v době povolení replikace. Po dokončení počáteční replikace se spustí replikace rozdílových změn do Azure. Sledované změny pro počítač se odesílají na procesový Server.
4. Komunikace probíhá takto:

    - Virtuální počítače komunikují s místním konfiguračním serverem na portu HTTPS 443 příchozí, pro správu replikací.
    - Konfigurační server orchestruje replikaci s Azure přes odchozí port HTTPS 443.
    - Virtuální počítače odesílají data replikace na procesový Server (spuštěný na počítači konfiguračního serveru) na portu HTTPS 9443 příchozí. Tento port lze změnit.
    - Procesový server přijímá data replikace, optimalizuje je a šifruje je a odesílá je do Azure Storage přes odchozí port 443.
5. Data replikace zaprotokolují první půdu v účtu úložiště mezipaměti v Azure. Tyto protokoly se zpracují a data se ukládají na spravovaný disk Azure (nazývaný jako počáteční disk ASR). Body obnovení jsou vytvořeny na tomto disku.

![Diagram znázorňující proces replikace VMware do Azure](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="resynchronization-process"></a>Proces opakované synchronizace

1. V době, kdy během počáteční replikace nebo při přenosu rozdílových změn dojde k problémům se síťovým připojením mezi zdrojovým počítačem a procesovým serverem nebo mezi procesovým serverem a Azure. Jedna z těchto z nich může vést k selhání přenosu dat do Azure.
2. Aby nedocházelo k problémům s integritou dat a minimalizovali náklady na přenos dat, Site Recovery označí počítač pro opakovanou synchronizaci.
3. Počítač může být taky označený pro opakovanou synchronizaci v situacích, jako je třeba zachování konzistence mezi zdrojovým počítačem a daty uloženými v Azure.
    - Pokud počítač napadne na vynucené vypnutí
    - Pokud počítač nahraje změny konfigurace, jako je změna velikosti disku (Změna velikosti disku z 2 TB na 4 TB)
4. Opětovná synchronizace pošle do Azure jenom rozdílová data. Přenos dat mezi místním prostředím a Azure, a to minimalizováním výpočetních kontrolních součtů dat mezi zdrojovým počítačem a daty uloženými v Azure.
5. Ve výchozím nastavení je automatické spuštění resynchronizace naplánováno mimo kancelářskou dobu. Pokud nechcete čekat na výchozí opětovnou synchronizaci mimo hodiny, můžete virtuální počítač znovu synchronizovat ručně. Provedete to tak, že přejdete na Azure Portal a > znovu **synchronizujete** virtuální počítač.
6. Pokud se výchozí opětovná synchronizace nezdařila mimo kancelářskou dobu a vyžaduje se ruční zásah, vygeneruje se na konkrétním počítači v Azure Portal chyba. Tuto chybu můžete vyřešit a ručním spuštěním synchronizace.
7. Po dokončení opětovné synchronizace bude obnovena replikace rozdílových změn.

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

## <a name="failover-and-failback-process"></a>Proces převzetí služeb při selhání a navrácení služeb po obnovení

Po nastavení replikace a spuštění postupu zotavení po havárii (testovací převzetí služeb při selhání) ke kontrole, že vše funguje podle očekávání, můžete v případě potřeby spustit převzetí služeb při selhání a navrácení služeb po obnovení.

1. Nedaří se vám spustit pro jeden počítač nebo vytvořit plány obnovení pro převzetí služeb při selhání více virtuálních počítačů ve stejnou dobu. Výhodou plánu obnovení, nikoli jednoho převzetí služeb při selhání pro jednotlivé počítače, zahrnují:
    - Můžete modelovat závislosti aplikací včetně všech virtuálních počítačů v rámci aplikace v jednom plánu obnovení.
    - Můžete přidat skripty, Runbooky Azure a pozastavit pro ruční akce.
2. Po aktivaci počátečního převzetí služeb při selhání se potvrdili, že budete moct začít přistupovat ke úlohám z virtuálního počítače Azure.
3. Až bude vaše místní lokalita opět dostupná, můžete připravit na navrácení služeb po obnovení. Pro navrácení služeb po obnovení je potřeba nastavit infrastrukturu navrácení služeb po obnovení, včetně těchto:

    * **Dočasný procesový Server v Azure**: Pokud chcete navrátit služby po obnovení z Azure, NASTAVÍTE virtuální počítač Azure, který bude fungovat jako procesový Server, který bude zpracovávat replikaci z Azure. Tento virtuální počítač je možné po navrácení služeb po obnovení odstranit.
    * **Připojení VPN**: pro navrácení služeb po obnovení budete potřebovat připojení k síti VPN (nebo ExpressRoute) ze sítě Azure do místní lokality.
    * **Samostatný hlavní cílový server**: ve výchozím nastavení se hlavní cílový server, který byl nainstalovaný s konfiguračním serverem na místním virtuálním počítači VMware, zpracovává navrácení služeb po obnovení. Pokud potřebujete navrátit navrácení velkých objemů dat, nastavte pro tento účel samostatný místní hlavní cílový server.
    * **Zásady navrácení služeb po obnovení:** Pro zpětnou replikaci do vaší místní lokality budete potřebovat zásady navrácení služeb. Tato zásada se automaticky vytvoří při vytváření zásad replikace z místního prostředí do Azure.
4. Po uvedení součástí dojde k navrácení služeb po obnovení ve třech akcích:

    - Fáze 1: znovu nastavte ochranu virtuálních počítačů Azure tak, aby se replikoval z Azure zpátky na místní virtuální počítače VMware.
    -  Fáze 2: spuštění převzetí služeb při selhání v místní lokalitě.
    - Fáze 3: po navrácení služby po selhání se znovu povolí replikace místních virtuálních počítačů.
    
 

![Diagram znázorňující navrácení služeb po obnovení VMware z Azure.](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Další kroky

Pokud chcete povolit replikaci VMware do Azure, postupujte podle [tohoto kurzu](vmware-azure-tutorial.md) .
