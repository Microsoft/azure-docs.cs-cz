---
title: Selhání a navrácení služeb po obnovení v Azure Site Recovery
description: Přečtěte si o převzetí služeb při selhání a selhání v Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: 3617683200aa3ffba08061b70993613fd0cc7241
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369875"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback"></a>Místní převzetí služeb při selhání a zotavení po havárii

Tento článek poskytuje přehled převzetí služeb při selhání a navrácení služeb po havárii při zotavení po havárii místních počítačů do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).

## <a name="recovery-stages"></a>Fáze obnovení

Převzetí služeb při selhání a navrácení služeb po obnovení v Site Recovery má čtyři fáze

- **Fáze 1: převzetí služeb při selhání z místního**prostředí: po nastavení replikace do Azure pro místní počítače dojde při výpadku vaší místní lokality k selhání těchto počítačů do Azure. Po převzetí služeb při selhání se virtuální počítače Azure vytvoří z replikovaných dat.
- **Fáze 2: opětovné zapnutí ochrany virtuálních počítačů Azure**: v Azure můžete znovu nastavit ochranu virtuálních počítačů Azure tak, aby se začaly replikovat zpátky na místní lokalitu. Místní virtuální počítač (Pokud je k dispozici) je během opětovné ochrany vypnutý, aby se zajistila konzistence dat.
- **Fáze 3:** převzetí služeb při selhání z Azure: Pokud je vaše místní lokalita znovu spuštěná v normálním provozu, spustíte jiné převzetí služeb při selhání, tentokrát dojde k selhání back-VM virtuálních počítačů Azure do vaší místní lokality. Můžete navrátit služby po obnovení do původního umístění, ze kterého došlo k převzetí služeb při selhání, nebo do alternativního umístění.
- **Fáze 4: opětovná ochrana místních počítačů**: po navrácení služeb po obnovení znovu povolte replikaci místních počítačů do Azure.

## <a name="failover"></a>Převzetí služeb při selhání

Převzetí služeb při selhání provedete v rámci strategie pro provozní kontinuitu a zotavení po havárii (BCDR).

- Jako první krok v strategii BCDR budete průběžně replikovat místní počítače do Azure. Uživatelé přistupují k úlohám a aplikacím běžícím na místních zdrojových počítačích.
- Pokud je potřeba, například pokud dojde k výpadku v místním prostředí, počítače replikace se přestanou přenášet do Azure. Virtuální počítače Azure se vytvářejí pomocí replikovaných dat.
- Pro zajištění kontinuity podnikových aplikací můžou uživatelé dál přistupovat k aplikacím na virtuálních počítačích Azure.

Převzetí služeb při selhání je dvě fáze:

- **Převzetí služeb při**selhání: převzetí služeb při selhání, které vytváří a přináší virtuální počítač Azure pomocí vybraného bodu obnovení.
- **Potvrzení změn**: po převzetí služeb při selhání ověříte virtuální počítač v Azure:
    - Pak můžete potvrdit převzetí služeb při selhání do vybraného bodu obnovení nebo vybrat jiný bod pro potvrzení.
    - Po potvrzení převzetí služeb při selhání se bod obnovení nedá změnit.


## <a name="connect-to-azure-after-failover"></a>Připojení k Azure po převzetí služeb při selhání

Pokud se chcete připojit k virtuálním počítačům Azure vytvořeným po převzetí služeb při selhání pomocí protokolu RDP/SSH, je potřeba mít několik požadavků.

**Převzetí služeb při selhání** | **Umístění** | **Akce**
--- | --- | ---
**Virtuální počítač Azure (Windows (** | Na místním počítači před převzetím služeb při selhání | **Přístup přes Internet**: Povolit protokol RDP. Ujistěte se, že jsou přidaná pravidla TCP a UDP pro **veřejné**a že protokol RDP je povolený pro všechny profily v povolených aplikacích **brány Windows Firewall**  >  **Allowed Apps**.<br/><br/> **Přístup přes síť VPN typu Site-to-site**: na počítači povolte RDP. Ověřte, že je protokol RDP povolený v **bráně Windows Firewall**  ->  **povolené aplikace a funkce**pro **domény a privátní** sítě.<br/><br/>  Ujistěte se, že je zásada SAN operačního systému nastavená na **OnlineAll**. [Další informace](https://support.microsoft.com/kb/3031135).<br/><br/> Při aktivaci převzetí služeb při selhání se ujistěte, že na virtuálním počítači nečekají žádné aktualizace Windows. Web Windows Update se může spustit při převzetí služeb při selhání a nebudete se moct přihlásit k virtuálnímu počítači, dokud se nedokončí aktualizace.
**Virtuální počítač Azure s Windows** | Na virtuálním počítači Azure po převzetí služeb při selhání |  [Přidejte veřejnou IP adresu](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) pro tento virtuální počítač.<br/><br/> Pravidla skupiny zabezpečení sítě na virtuálním počítači služby převzetí služeb při selhání (a v podsíti Azure, ke které je připojeno), musí umožňovat příchozí připojení k portu RDP.<br/><br/> Zkontrolujte **diagnostiku spouštění** a ověřte snímek obrazovky virtuálního počítače. Pokud se nemůžete připojit, zkontrolujte, že je virtuální počítač spuštěný, a přečtěte si [tipy k odstraňování potíží](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Virtuální počítač Azure se systémem Linux** | Na místním počítači před převzetím služeb při selhání | Ujistěte se, že je služba Secure Shell na virtuálním počítači nastavená tak, aby se automaticky spouštěla při spuštění systému.<br/><br/> Zkontrolujte, jestli pravidla brány firewall umožňují službě SSH připojit se k ní.
**Virtuální počítač Azure se systémem Linux** | Na virtuálním počítači Azure po převzetí služeb při selhání | Pravidla skupiny zabezpečení sítě na virtuálním počítači služby převzetí služeb při selhání (a v podsíti Azure, ke které je připojené), musí umožňovat příchozí připojení k portu SSH.<br/><br/> [Přidejte veřejnou IP adresu](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) pro tento virtuální počítač.<br/><br/> Podívejte se na **diagnostiku spouštění** pro snímek obrazovky virtuálního počítače.<br/><br/>

## <a name="types-of-failover"></a>Typy převzetí služeb při selhání

Site Recovery poskytuje různé možnosti převzetí služeb při selhání.

**Převzetí služeb při selhání** | **Podrobnosti** | **Obnovovací** | **Workflow** (Pracovní postup)
--- | --- | --- | ---
**Testovací převzetí služeb při selhání** | Používá se ke spuštění podrobného postupu, který ověřuje vaši strategii BCDR bez ztráty dat nebo výpadků.| Vytvoří kopii virtuálního počítače v Azure bez dopadu na probíhající replikaci nebo v produkčním prostředí. | 1. Spusťte testovací převzetí služeb při selhání na jednom virtuálním počítači nebo na více virtuálních počítačích v plánu obnovení.<br/><br/> 2. Vyberte bod obnovení, který se použije pro testovací převzetí služeb při selhání.<br/><br/> 3. Vyberte síť Azure, ve které bude virtuální počítač Azure umístěný po jeho vytvoření po převzetí služeb při selhání. Síť se používá pouze k testovacímu převzetí služeb při selhání.<br/><br/> 4. Ověřte, zda se rozpracovalo v podrobnostech podle očekávání. Site Recovery automaticky vyčistí během přechodu k virtuálním počítačům vytvořeným v Azure.
**Plánované převzetí služeb při selhání – Hyper-V**  | Obvykle se používá pro plánované výpadky.<br/><br/> Zdrojové virtuální počítače jsou vypnuté. Před spuštěním převzetí služeb při selhání se synchronizují nejnovější data. | Nulová ztráta dat pro plánovaný pracovní postup. | 1. Naplánování časového intervalu údržby a informování uživatelů<br/><br/> 2. Převeďte uživatelské aplikace do režimu offline.<br/><br/> 3. Inicializujte plánované převzetí služeb při selhání s nejnovějším bodem obnovení. Převzetí služeb při selhání se nespustí, pokud se počítač nevypne nebo pokud dojde k chybám.<br/><br/> 4. po převzetí služeb při selhání ověřte, že je replika virtuálního počítače Azure v Azure aktivní.<br/><br/> 5. Potvrďte převzetí služeb při selhání, aby se dokončilo. Akce potvrzení odstraní všechny body obnovení.
**Převzetí služeb při selhání – Hyper-V** | Obvykle se spouští v případě neplánovaného výpadku nebo není k dispozici primární lokalita.<br/><br/> Volitelně vypněte virtuální počítač a před zahájením převzetí služeb při selhání proveďte synchronizaci konečných změn.  | Minimální ztráta dat pro aplikace | 1. Inicializujte plán BCDR. <br/><br/> 2. Inicializujte převzetí služeb při selhání. Určete, jestli má Site Recovery před aktivací převzetí služeb při selhání vypnout virtuální počítač a synchronizovat/replikovat nejnovější změny.<br/><br/> 3. převzetí služeb při selhání můžete provést několika možnostmi bodu obnovení, které jsou shrnuté v následující tabulce.<br/><br/> Pokud nepovolíte možnost vypnout virtuální počítač nebo pokud Site Recovery nemůže vypnout, použije se nejnovější bod obnovení.<br/>Převzetí služeb při selhání běží i v případě, že počítač nejde vypnout.<br/><br/> 4. po převzetí služeb při selhání ověřte, že je replika virtuálního počítače Azure v Azure aktivní.<br/> V případě potřeby můžete vybrat jiný bod obnovení z okna uchování po dobu 24 hodin.<br/><br/> 5. Potvrďte převzetí služeb při selhání, aby se dokončilo. Akce potvrzení odstraní všechny dostupné body obnovení.
**Převzetí služeb při selhání – VMware** | Obvykle se spouští v případě neplánovaného výpadku nebo není k dispozici primární lokalita.<br/><br/> Volitelně můžete určit, že Site Recovery by se měla pokusit aktivovat virtuální počítač a synchronizovat a replikovat konečné změny před spuštěním převzetí služeb při selhání.  | Minimální ztráta dat pro aplikace | 1. Inicializujte plán BCDR. <br/><br/> 2. Inicializujte převzetí služeb při selhání z Site Recovery. Určete, jestli se má Site Recovery před spuštěním převzetí služeb při selhání zkusit spustit vypnutí virtuálního počítače a provést synchronizaci.<br/> Převzetí služeb při selhání běží i v případě, že počítače nejde vypnout.<br/><br/> 3. po převzetí služeb při selhání ověřte, že je replika virtuálního počítače Azure v Azure aktivní. <br/>V případě potřeby můžete vybrat jiný bod obnovení z intervalu uchování 72 hodin.<br/><br/> 5. Potvrďte převzetí služeb při selhání, aby se dokončilo. Akce potvrzení odstraní všechny body obnovení.<br/> U virtuálních počítačů s Windows Site Recovery zakáže nástroje VMware během převzetí služeb při selhání.

## <a name="failover-processing"></a>Zpracování převzetí služeb při selhání

V některých scénářích převzetí služeb při selhání vyžaduje další zpracování, které trvá přibližně 8 až 10 minut. Můžete si všimnout delší doby testovacího převzetí služeb při selhání pro:

* Virtuální počítače VMware s verzí služby mobility, která je starší než 9,8.
* Fyzické servery.
* Virtuální počítače VMware Linux.
* Virtuální počítače Hyper-V chráněné jako fyzické servery.
* Virtuální počítače VMware, které nemají povolenou službu DHCP.
* Virtuální počítače VMware, které nemají následující ovladače pro spouštění: storvsc, VMBus, storflt, Intelide, ATAPI.

## <a name="recovery-point-options"></a>Možnosti bodu obnovení

Během převzetí služeb při selhání můžete vybrat několik možností bodu obnovení.

**Možnost** | **Podrobnosti**
--- | ---
**Nejnovější (nejnižší RPO)** | Tato možnost poskytuje nejnižší cíl bodu obnovení (RPO). Nejprve zpracuje všechna data, která byla odeslána do služby Site Recovery Service, aby bylo možné vytvořit bod obnovení pro každý virtuální počítač, než je služba převzala do provozu. V tomto bodu obnovení jsou všechna data replikována do Site Recovery při aktivaci převzetí služeb při selhání.
**Poslední zpracovaná**  | Tato možnost převezme virtuální počítače na nejnovější bod obnovení zpracovaný Site Recovery. Chcete-li zobrazit nejnovější bod obnovení pro konkrétní virtuální počítač, zkontrolujte v nastavení virtuálního počítače **nejnovější body obnovení** . Tato možnost poskytuje nízkou plánovanou dobu obnovení (RTO), protože se neztrácí žádný čas zpracováním nezpracovaných dat.
**Nejnovější konzistentní vzhledem k aplikacím** |  Tato možnost při převzetí služeb při selhání virtuálních počítačů na nejnovější bod obnovení, který je konzistentní vzhledem k aplikacím, zpracovaná Site Recovery, pokud jsou povolené body obnovení konzistentní vzhledem k aplikacím. V nastavení virtuálního počítače ověřte poslední bod obnovení.
**Poslední zpracovaný vícenásobný virtuální počítač** | Tato možnost je k dispozici pro plány obnovení s povoleným konzistencí více virtuálních počítačů s jedním nebo více virtuálními počítači. Virtuální počítače s povoleným nastavením převezmou na nejnovější společný bod obnovení konzistentní s více virtuálními počítači. U všech ostatních virtuálních počítačů v plánu dojde k převzetí služeb při selhání na nejnovější zpracovaný bod obnovení.
**Nejnovější konzistentní vzhledem k aplikacím pro více virtuálních počítačů** |  Tato možnost je k dispozici pro plány obnovení s povoleným konzistencí více virtuálních počítačů s jedním nebo více virtuálními počítači. Virtuální počítače, které jsou součástí replikační skupiny, převezmou nejnovější běžný bod obnovení konzistentní s aplikacemi pro více virtuálních počítačů. Jiné virtuální počítače převezme služby při selhání do svého nejnovějšího bodu obnovení konzistentního vzhledem k aplikacím.
**Vlastní** | Tuto možnost použijte, pokud chcete převzít služby při selhání určitého virtuálního počítače do konkrétního bodu obnovení v čase. Tato možnost není pro plány obnovení k dispozici.

> [!NOTE]
> Body obnovení nelze migrovat do jiného trezoru Recovery Services.

## <a name="reprotectionfailback"></a>Znovu napřed navrácení služeb po obnovení

Po převzetí služeb při selhání do Azure jsou replikované virtuální počítače Azure v nechráněném stavu.

- Jako první krok při navrácení služeb po obnovení do místní lokality je potřeba spustit virtuální počítače Azure, které se replikují místně. Proces ochrany závisí na typu počítačů, u kterých došlo k převzetí služeb při selhání.
- Po replikaci počítačů z Azure do místního prostředí můžete spustit převzetí služeb při selhání z Azure do místní lokality.
- Po opětovném spuštění počítačů můžete replikaci povolit, aby se mohla replikovat na zotavení po havárii do Azure.

Navrácení služeb po obnovení funguje takto:

- Pro navrácení služeb po obnovení potřebuje virtuální počítač aspoň jeden bod obnovení, aby mohl navrátit služby po obnovení. V plánu obnovení všechny virtuální počítače v plánu potřebují alespoň jeden bod obnovení.
- Doporučujeme použít **nejnovější** bod obnovení pro navrácení služeb po obnovení (Jedná se o bod konzistentní vzhledem k selhání).
    - Existuje možnost bodu obnovení konzistentního vzhledem k aplikacím. V takovém případě se jeden virtuální počítač obnoví do nejnovějšího dostupného bodu obnovení konzistentního vzhledem k aplikacím. Pro plán obnovení s replikační skupinou se každá skupina replikace obnoví do svého společného dostupného bodu obnovení.
    - Body obnovení konzistentní vzhledem k aplikacím můžou být v čase a může dojít ke ztrátě dat.
- Při převzetí služeb při selhání z Azure do místní lokality Site Recovery vypne virtuální počítače Azure. Když převzetí služeb při selhání potvrdíte, Site Recovery odstraní virtuální počítače Azure, které selhaly v Azure.


## <a name="vmwarephysical-reprotectionfailback"></a>VMware/fyzická ochrana/navrácení služeb po obnovení

K opětovné ochraně a selhání počítačů VMware a fyzických serverů z Azure do místního prostředí budete potřebovat infrastrukturu navrácení služeb po obnovení a existuje několik požadavků.

- **Dočasný procesový Server v Azure**: Pokud chcete navrátit služby po obnovení z Azure, NASTAVÍTE virtuální počítač Azure, který bude fungovat jako procesový Server, který bude zpracovávat replikaci z Azure. Tento virtuální počítač je možné po navrácení služeb po obnovení odstranit.
- **Připojení VPN**: pro navrácení služeb po obnovení budete potřebovat připojení k síti VPN (nebo ExpressRoute) ze sítě Azure do místní lokality.
- **Samostatný hlavní cílový server**: ve výchozím nastavení se hlavní cílový server, který byl nainstalovaný s konfiguračním serverem na místním virtuálním počítači VMware, zpracovává navrácení služeb po obnovení. Pokud potřebujete navrátit navrácení velkých objemů dat, nastavte pro tento účel samostatný místní hlavní cílový server.
- **Zásady navrácení služeb po obnovení:** Pro zpětnou replikaci do vaší místní lokality budete potřebovat zásady navrácení služeb. Tato zásada se automaticky vytvoří při vytváření zásad replikace z místního prostředí do Azure.
    - Tato zásada je automaticky přidružena ke konfiguračnímu serveru.
    - Tuto zásadu nemůžete upravit.
    - Hodnoty zásad: prahová hodnota pro RPO – 15 minut; Uchování bodu obnovení – 24 hodin; Frekvence snímků konzistentní vzhledem k aplikacím – 60 minut.

Další informace o VMware/fyzické ochraně a navrácení služeb po obnovení:
- [Projděte si](vmware-azure-reprotect.md#before-you-begin) další požadavky na ochranu a navrácení služeb po obnovení.
- [Nasaďte](vmware-azure-prepare-failback.md#deploy-a-process-server-in-azure) procesový Server v Azure.
- [Nasaďte](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) samostatný hlavní cílový server.

Při opětovném zapnutí ochrany virtuálních počítačů Azure v místním prostředí můžete určit, že chcete navrátit služby po obnovení do původního umístění nebo do alternativního umístění.

- **Obnovení původního umístění**: převezme se z Azure zpátky na stejný zdrojový místní počítač, pokud existuje. V tomto scénáři se replikují jenom změny zpátky do místního prostředí.
- **Obnovení do alternativního umístění**: Pokud místní počítač neexistuje, můžete navrátit služby po obnovení z Azure do alternativního umístění. Při opětovném zapnutí ochrany virtuálního počítače Azure do místního počítače se vytvoří místní počítač. Úplná replikace dat probíhá z Azure do místního prostředí. -- [Přečtěte](concepts-types-of-failback.md) si požadavky a omezení pro navrácení služeb po obnovení.



## <a name="hyper-v-reprotectionfailback"></a>Ochrana Hyper-V/navrácení služeb po obnovení

Ochrana virtuálních počítačů Hyper-V z Azure do místního prostředí a jejich opětovného navrácení služeb po obnovení v místním prostředí:

- K navrácení služeb virtuálních počítačů Hyper-V do replikačního účtu můžete použít jenom účet úložiště. Navrácení služeb po obnovení virtuálních počítačů Hyper-V, které replikují pomocí spravovaných disků, se nepodporuje.
- Místní hostitelé Hyper-V (v případě použití nástroje System Center VMM) by měli být připojeni k Azure.
- Spustíte plánované navrácení služeb po obnovení z Azure do místního prostředí.
- Pro navrácení služeb virtuálního počítače s technologií Hyper-V není potřeba nastavit žádné konkrétní součásti.
- Během plánovaného převzetí služeb při selhání můžete vybrat možnosti pro synchronizaci dat před navrácením služeb po obnovení:
    - **Synchronizovat data před převzetím služeb při selhání**: Tato možnost minimalizuje výpadky virtuálních počítačů při synchronizaci počítačů bez jejich vypnutí.
        - Fáze 1: pořídí snímek virtuálního počítače Azure a zkopíruje ho na místního hostitele Hyper-V. Počítač pokračuje v běhu v Azure.
        - Fáze 2: ukončí virtuální počítač Azure, aby tam nedocházelo k žádným novým změnám. Poslední sada rozdílových změn se přenese na místní server a spustí se místní virtuální počítač.
    - **Synchronizovat data jenom během převzetí služeb při selhání**: Tato možnost je rychlejší, protože očekáváme, že se většina disku změnila, a proto neprovede výpočty kontrolního součtu. Provede stažení disku. Tuto možnost doporučujeme použít, pokud je virtuální počítač spuštěný v Azure po dobu (za měsíc nebo více), nebo pokud se místní virtuální počítač odstranil.

[Přečtěte si další informace](hyper-v-azure-failback.md) o ochraně Hyper-V a navrácení služeb po obnovení.

Při opětovném zapnutí ochrany virtuálních počítačů Azure v místním prostředí můžete určit, že chcete navrátit služby po obnovení do původního umístění nebo do alternativního umístění.

- **Obnovení původního umístění**: převezme se z Azure zpátky na stejný zdrojový místní počítač, pokud existuje. V tomto scénáři vyberete jednu z možností synchronizace popsaných v předchozím postupu.
- **Obnovení do alternativního umístění**: Pokud místní počítač neexistuje, můžete navrátit služby po obnovení z Azure do alternativního umístění. Při opětovném zapnutí ochrany virtuálního počítače Azure do místního počítače se vytvoří místní počítač. Pomocí této možnosti doporučujeme, abyste před převzetím služeb při selhání vybrali možnost synchronizovat data.
- [Přečtěte](hyper-v-azure-failback.md) si požadavky a omezení pro navrácení služeb po obnovení.


Po navrácení služeb po obnovení do místní lokality povolte **reverzní replikaci** a začněte replikovat virtuální počítač do Azure. tím se cyklus dokončí.




## <a name="next-steps"></a>Další kroky
- Převzetí služeb při selhání [konkrétními virtuálními počítači VMware](vmware-azure-tutorial-failover-failback.md)
- Převzetí služeb při selhání u [konkrétních virtuálních počítačů Hyper-V](hyper-v-azure-failover-failback-tutorial.md)
- [Vytvořte](site-recovery-create-recovery-plans.md) plán obnovení.
- Převzetí služeb při selhání [virtuálních počítačů v plánu obnovení](site-recovery-failover.md).
- [Příprava na](vmware-azure-failback.md) Ochrana VMware a navrácení služeb po obnovení.
- Navrácení služeb po obnovení [virtuálních počítačů Hyper-V](hyper-v-azure-failback.md)