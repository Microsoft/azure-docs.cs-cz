---
title: O převzetí služeb při selhání a navrácení služeb po obnovení v azure site recovery
description: Přečtěte si o převzetí služeb při selhání a failable v Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: d9b54f3c452212e12419a5ffd67b116c8660308d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281805"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback"></a>Informace o místním obnovení služeb při selhání zotavení po havárii nebo navrácení služeb po obnovení

Tento článek poskytuje přehled převzetí služeb při selhání a navrácení služeb po obnovení služeb při selhání během zotavení po havárii místních počítačů do Azure s [Azure Site Recovery](site-recovery-overview.md).

## <a name="recovery-stages"></a>Fáze obnovy

Převzetí služeb při selhání a navrácení služeb po obnovení služeb při selhání v obnovení webu má čtyři fáze:

- **Fáze 1: Převzetí služeb při selhání z místního prostředí**: Po nastavení replikace do Azure pro místní počítače, když vaše místní lokalita přejde dolů, selhání těchto počítačů do Azure. Po převzetí služeb při selhání se virtuální počítače Azure vytvoří z replikovaných dat.
- **Fáze 2: Znovu chránit virtuální počítače Azure**: V Azure znovu protectejete virtuální počítače Azure tak, aby se začaly replikovat zpět do místního webu. Místní virtuální počítač (pokud je k dispozici) je vypnutý během opětovné ochrany, aby byla zajištěna konzistence dat.
- **Fáze 3: Převzetí služeb při selhání z Azure**: Když váš místní web běží jako normální znovu, spustíte další převzetí služeb při selhání, tentokrát navrácení služeb po selhání virtuálních počítače Azure do místního webu. Můžete se vrátit zpět do původního umístění, ze kterého jste převzali služby při selhání, nebo do alternativního umístění.
- **Fáze 4: Znovu přizamknout místní počítače**: Po selhání zpět znovu povolte replikaci místních počítačů do Azure.

## <a name="failover"></a>Převzetí služeb při selhání

Převzetí služeb při selhání provádíte jako součást strategie kontinuity podnikání a zotavení po havárii (BCDR).

- Jako první krok ve strategii BCDR průběžně replikujete místní počítače do Azure. Uživatelé přistupují k úlohám a aplikacím spuštěným na místních zdrojových počítačích.
- Pokud vznikne potřeba, například pokud je výpadek místní, selhání replikace počítače do Azure. Virtuální počítače Azure se vytvářejí pomocí replikovaných dat.
- Pokud jde o kontinuitu podnikání, můžou uživatelé dál přistupovat k aplikacím na virtuálních počítačích Azure.

Převzetí služeb při selhání je dvoufázová aktivita:

- **Převzetí služeb při selhání**: Převzetí služeb při selhání, které vytvoří a vyvolá virtuální počítač Azure pomocí vybraného bodu obnovení.
- **Potvrzení**: Po převzetí služeb při selhání ověříte virtuální počítač v Azure:
    - Potom můžete potvrdit převzetí služeb při selhání do vybraného bodu obnovení nebo vybrat jiný bod pro potvrzení.
    - Po potvrzení převzetí služeb při selhání nelze bod obnovení změnit.


## <a name="connect-to-azure-after-failover"></a>Připojení k Azure po převzetí služeb při selhání

Chcete-li se připojit k virtuálním počítačům Azure vytvořeným po převzetí služeb při selhání pomocí RDP/SSH, existuje řada požadavků.

**Zabezpečení před selháním** | **Umístění** | **Akce**
--- | --- | ---
**Virtuální počítač Azure (Windows(** | V místním počítači před převzetím služeb při selhání | **Přístup přes internet**: Povolit PROTOKOL RDP. Ujistěte se, že jsou přidána pravidla Protokolu TCP a UDP pro **veřejné**a zda je rdp povolen pro všechny profily v**aplikacích allowed apps**brány Windows **Firewall** > .<br/><br/> **Přístup přes síť VPN mezi lokalitami**: Povolte v počítači protokol RDP. Zkontrolujte, zda je funkce rdp povolena v**aplikacích a funkcích** **programu Windows Firewall** -> , pro **domény a soukromé** sítě.<br/><br/>  Ujistěte se, že zásady sítě SAN operačního systému jsou nastaveny na **onlineall**. [Další informace](https://support.microsoft.com/kb/3031135).<br/><br/> Ujistěte se, že neexistují žádné aktualizace systému Windows čekající na virtuální ms při aktivaci převzetí služeb při selhání. Služba Windows Update se může spustit při převzetí služeb při selhání a dokud nebude provedena aktualizace, nebudete se moci přihlásit k virtuálnímu počítači.
**Virtuální počítač Azure s Windows** | Na virtuálním počítači Azure po převzetí služeb při selhání |  [Přidejte veřejnou IP adresu](https://aka.ms/addpublicip) pro tento virtuální počítač.<br/><br/> Pravidla skupiny zabezpečení sítě pro převzetí počítače s převzetím služby při selhání (a podsíť Azure, ke které je připojen) musí povolit příchozí připojení k portu RDP.<br/><br/> Zkontrolujte **diagnostiku spuštění** a ověřte snímek obrazovky virtuálního aplikace. Pokud se nemůžete připojit, zkontrolujte, jestli je spuštěný virtuální hod, a projděte si [tipy pro řešení potíží](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Virtuální počítač Azure s Linuxem** | V místním počítači před převzetím služeb při selhání | Ujistěte se, že služba Secure Shell na virtuálním počítači je nastavena na automatické spuštění při spuštění systému.<br/><br/> Zkontrolujte, jestli pravidla brány firewall umožňují službě SSH připojit se k ní.
**Virtuální počítač Azure s Linuxem** | Na virtuálním počítači Azure po převzetí služeb při selhání | Pravidla skupiny zabezpečení sítě pro převzetí počítače s připojením na vědomí (a podsíť Azure, ke které je připojen) musí povolit příchozí připojení k portu SSH.<br/><br/> [Přidejte veřejnou IP adresu](https://aka.ms/addpublicip) pro tento virtuální počítač.<br/><br/> Zkontrolujte **diagnostiku spuštění** pro snímek virtuálního aplikace.<br/><br/>

## <a name="types-of-failover"></a>Typy převzetí služeb při selhání

Obnovení webu poskytuje různé možnosti převzetí služeb při selhání.

**Zabezpečení před selháním** | **Podrobnosti** | **Obnovení** | **Pracovního postupu**
--- | --- | --- | ---
**Testovací převzetí služeb při selhání** | Používá se ke spuštění vrtáku, který ověřuje vaši strategii BCDR bez ztráty dat nebo výpadků.| Vytvoří kopii virtuálního počítače v Azure, bez dopadu na probíhající replikaci nebo na produkční prostředí. | 1. Spusťte test převzetí služeb při selhání na jednom virtuálním počítači nebo na více virtuálních počítačích v plánu obnovení.<br/><br/> 2. Vyberte bod obnovení, který chcete použít pro převzetí služeb při selhání testu.<br/><br/> 3. Vyberte síť Azure, ve které bude virtuální počítač Azure umístěn, když se vytvoří po převzetí služeb při selhání. Síť se používá pouze pro převzetí služeb při selhání testu.<br/><br/> 4. Ověřte, zda vrták fungoval podle očekávání. Site Recovery automaticky vyčistí virtuální počítače vytvořené v Azure během přechodu na vrták.
**Plánované převzetí služeb při selhání-Hyper-V**  | Obvykle se používá pro plánované prostoje.<br/><br/> Zdrojové virtuální mích jsou vypnuté. Nejnovější data jsou synchronizována před zahájením převzetí služeb při selhání. | Nulová ztráta dat pro plánovaný pracovní postup. | 1. Naplánujte okno údržby prostojů a upozorněte uživatele.<br/><br/> 2. Přepjte aplikace orientované na uživatele offline.<br/><br/> 3. Iniciujte plánované převzetí služeb při selhání s nejnovějším bodem obnovení. Převzetí služeb při selhání se nespustí, pokud není počítač vypnut, nebo pokud dojde k chybám.<br/><br/> 4. Po převzetí služeb při selhání zkontrolujte, zda je replika virtuálního počítače Azure aktivní v Azure.<br/><br/> 5. Potvrzení převzetí služeb při selhání dokončit. Akce potvrzení odstraní všechny body obnovení.
**Převzetí služeb při selhání-Hyper-V** | Obvykle se spustí, pokud dojde k neplánovanému výpadku nebo primární lokalita není k dispozici.<br/><br/> Volitelně vypněte virtuální ho a synchronizujte konečné změny před zahájením převzetí služeb při selhání.  | Minimální ztráta dat pro aplikace. | 1. Iniciujte svůj plán BCDR. <br/><br/> 2. Iniciovat převzetí služeb při selhání. Určete, zda má obnovení webu vypnout virtuální hospodařící a synchronizovat nebo replikovat nejnovější změny před aktivací převzetí služeb při selhání.<br/><br/> 3. Můžete přejít převzetí služeb při selhání na řadu možností bodu obnovení, shrnutých v následující tabulce.<br/><br/> Pokud nepovolíte možnost vypnutí virtuálního virtuálního serveru nebo pokud ho obnovení webu nejde vypnout, použije se nejnovější bod obnovení.<br/>Převzetí služeb při selhání běží i v případě, že počítač nelze vypnout.<br/><br/> 4. Po převzetí služeb při selhání zkontrolujte, zda je replika virtuálního počítače Azure aktivní v Azure.<br/> V případě potřeby můžete vybrat jiný bod obnovení z retenčního okna 24 hodin.<br/><br/> 5. Potvrzení převzetí služeb při selhání dokončit. Akce potvrzení odstraní všechny dostupné body obnovení.
**Převzetí služeb při selhání-VMware** | Obvykle se spustí, pokud dojde k neplánovanému výpadku nebo primární lokalita není k dispozici.<br/><br/> Volitelně určit, že site recovery by se měl pokusit spustit vypnutí virtuálního počítače a synchronizovat a replikovat konečné změny před zahájením převzetí služeb při selhání.  | Minimální ztráta dat pro aplikace. | 1. Iniciujte svůj plán BCDR. <br/><br/> 2. Iniciujte převzetí služeb při selhání z obnovení webu. Určete, zda se má obnovení webu pokusit spustit vypnutí virtuálního počítače a synchronizaci před spuštěním převzetí služeb při selhání.<br/> Převzetí služeb při selhání běží i v případě, že počítače nelze vypnout.<br/><br/> 3. Po převzetí služeb při selhání zkontrolujte, zda je replika virtuálního počítače Azure aktivní v Azure. <br/>V případě potřeby můžete vybrat jiný bod obnovení z retenčního okna 72 hodin.<br/><br/> 5. Potvrzení převzetí služeb při selhání dokončit. Akce potvrzení odstraní všechny body obnovení.<br/> Pro virtuální servery Windows site recovery zakáže nástroje VMware během převzetí služeb při selhání.

## <a name="failover-processing"></a>Zpracování převzetí služeb při selhání

V některých scénářích vyžaduje převzetí služeb při selhání další zpracování, jehož dokončení trvá přibližně 8 až 10 minut. Můžete si všimnout delší zkušební doby převzetí služeb při selhání pro:

* Virtuální počítačvsystému VMware s verzí služby Mobility starší než 9.8.
* Fyzické servery.
* Virtuální počítače VMware Linux.
* Virtuální počítače Hyper-V chráněné jako fyzické servery.
* Virtuální many VMware, které nemají povolenou službu DHCP.
* Virtuální zařízení VMware, která nemají následující spouštěcí ovladače: storvsc, vmbus, storflt, intelide, atapi.

## <a name="recovery-point-options"></a>Možnosti bodu obnovení

Během převzetí služeb při selhání můžete vybrat několik možností bodu obnovení.

**Možnost** | **Podrobnosti**
--- | ---
**Nejnovější (nejnižší RPO)** | Tato možnost poskytuje nejnižší cíl bodu obnovení (RPO). Nejprve zpracuje všechna data, která byla odeslána službě Site Recovery, aby vytvořilbod obnovení pro každý virtuální server, než k němu převezme teprve další funkce. Tento bod obnovení má všechna data replikována do site recovery při aktivaci převzetí služeb při selhání.
**Poslední zpracované**  | Tato možnost převezme služby virtuálních zařízení do nejnovějšího bodu obnovení zpracovaného obnovením webu. Pokud chcete zobrazit nejnovější bod obnovení pro konkrétní virtuální počítače, zkontrolujte **nejnovější body obnovení** v nastavení virtuálních počítače. Tato možnost poskytuje nízkou plánovanou dobu obnovení (RTO), protože se neztrácí žádný čas zpracováním nezpracovaných dat.
**Nejnovější aplikace konzistentní** |  Tato možnost převezme služby virtuálních zařízení do nejnovějšího bodu obnovení konzistentního s aplikací zpracovaného site recovery, pokud jsou povoleny body obnovení konzistentní s aplikací. Zkontrolujte nejnovější bod obnovení v nastavení virtuálního počítače.
**Nejnovější zpracování více virtuálních virtuálních montovek** | Tato možnost je k dispozici pro plány obnovení s jedním nebo více virtuálními aplikacemi, které mají povolenou konzistenci více virtuálních zařízení. Virtuální virtuální zařízení s povoleným nastavením převzetí služeb při selhání do nejnovějšího běžného bodu obnovení konzistentního s více virtuálními virtuálními zařízeními. Všechny ostatní virtuální virtuální společnosti v plánu převzetí služeb při selhání na nejnovější zpracovaný bod obnovení.
**Nejnovější aplikace konzistentní s více virtuálními virtuálními aplikacemi** |  Tato možnost je k dispozici pro plány obnovení s jedním nebo více virtuálními aplikacemi, které mají povolenou konzistenci více virtuálních zařízení. Virtuální aplikace, které jsou součástí replikační skupiny převzetí služeb při selhání na nejnovější společný bod obnovení konzistentní s více virtuálními aplikacemi. Ostatní virtuální virtuální společnosti převzetí služeb při selhání na jejich nejnovější bod obnovení konzistentní s aplikací.
**Vlastní** | Tuto možnost použijte k převzetí služeb při selhání konkrétního virtuálního virtuálního mísy do konkrétního bodu obnovení v čase. Tato možnost není k dispozici pro plány obnovení.

> [!NOTE]
> Body obnovení nelze migrovat do jiného trezoru služby Recovery Services.

## <a name="reprotectionfailback"></a>Opětovné protekce/navrácení služeb po obnovení

Po převzetí služeb při selhání do Azure jsou replikované virtuální počítače Azure v nechráněném stavu.

- Jako první krok k selhání zpět do místního webu, je potřeba spustit virtuální počítače Azure replikovat do místního. Proces opětovnéochrany závisí na typu počítačů, které jste převzali služby.
- Po replikaci počítačů z Azure do místního prostředí můžete spustit převzetí služeb při selhání z Azure do místního webu.
- Po spuštění počítačů v místním prostředí můžete povolit replikaci tak, aby se replikovaly do Azure pro zotavení po havárii.

Navrácení služeb po selhání funguje takto:

- Chcete-li obnovit, virtuální ho dispono potřebuje alespoň jeden bod obnovení, aby navrácení služeb po obnovení. V plánu obnovení všechny virtuální chody v plánu potřebují alespoň jeden bod obnovení.
- Doporučujeme použít **nejnovější** bod obnovení k navrácení služeb po obnovení (toto je bod konzistentní s selháním).
    - K dispozici je možnost bodu obnovení konzistentní s aplikací. V takovém případě se jeden virtuální virtuální může zotavit do svého nejnovějšího dostupného bodu obnovení konzistentního s aplikací. U plánu obnovení s replikační skupinou se každá replikační skupina obnoví do svého společného dostupného bodu obnovení.
    - Body obnovení konzistentní s aplikací mohou být v čase pozadu a může dojít ke ztrátě dat.
- Během převzetí služeb při selhání z Azure do místního webu site recovery vypne virtuální počítače Azure. Když potvrdíte převzetí služeb při selhání, site recovery odebere neúspěšné zpět virtuální počítače Azure v Azure.


## <a name="vmwarephysical-reprotectionfailback"></a>VMware/fyzická reprotekce/navrácení služeb po obnovení

Chcete-li znovu chránit a navrácení míslužeb vmware počítače a fyzické servery z Azure do místního, budete potřebovat infrastrukturu navrácení služeb po obnovení a existuje celá řada požadavků.

- **Dočasný procesní server v Azure:** Chcete-li z Azure navrácení zpět, nastavíte virtuální počítač Azure tak, aby fungoval jako procesní server pro zpracování replikace z Azure. Tento virtuální počítač je možné po navrácení služeb po obnovení odstranit.
- **Připojení VPN**: Chcete-li zpětnou vazbu, potřebujete připojení VPN (nebo ExpressRoute) ze sítě Azure do místního webu.
- **Samostatný hlavní cílový server**: Ve výchozím nastavení hlavní cílový server, který byl nainstalován s konfiguračním serverem v místním virtuálním počítači VMware, zpracovává navrácení služeb po selhání. Pokud potřebujete navrácení velkého množství provozu, nastavte pro tento účel samostatný místní hlavní cílový server.
- **Zásady navrácení služeb po obnovení:** Pro zpětnou replikaci do vaší místní lokality budete potřebovat zásady navrácení služeb. Tato zásada se automaticky vytvoří při vytváření zásad replikace z místního do Azure.
    - Tato zásada je automaticky přidružena k konfiguračnímu serveru.
    - Tuto zásadu nelze upravit.
    - Hodnoty zásad: Prahová hodnota rpo - 15 minut; Retence bodu obnovení - 24 hodin; Frekvence snímků konzistentní chod aplikace - 60 minut.

Další informace o vmware/fyzické reprotekci a navrácení služeb po obnovení:
- [Zkontrolujte](vmware-azure-reprotect.md#before-you-begin) další požadavky na opětovné protekci a navrácení služeb po obnovení.
- [Nasazení](vmware-azure-prepare-failback.md#deploy-a-process-server-in-azure) procesního serveru v Azure.
- [Nasaďte](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) samostatný hlavní cílový server.

Když znovu protectazure virtuálních počítačích do místního, můžete určit, že chcete poobnovení zpět do původního umístění nebo do alternativního umístění.

- **Obnovení původního umístění**: To se nezdaří zpět z Azure do stejného místního počítače, pokud existuje. V tomto scénáři jsou replikovány pouze změny zpět do místního.
- **Alternativní obnovení umístění**: Pokud místní počítač neexistuje, můžete obnovit služby poobnovení z Azure do alternativního umístění. Když znovu protect u virtuálního počítače Azure na místní, místní počítač se vytvoří. K úplné replikaci dat dochází z Azure do místního. - - [Zkontrolujte](concepts-types-of-failback.md) požadavky a omezení pro navrácení služeb po selhání umístění.



## <a name="hyper-v-reprotectionfailback"></a>Reprotection/failback technologie Hyper-V

Pokud chcete znovu chránit a napojit virtuální počítače Hyper-V z Azure do místního prostředí:

- Můžete jenom navrácení virtuálních počítače Hyper-V replikovat pomocí účtu úložiště. Navrácení služeb po selhání virtuálních počítačů Hyper-V, které se replikují pomocí spravovaných disků, není podporováno.
- Místní hostitelé Hyper-V (nebo System Center VMM, pokud se používá) by měli být připojeni k Azure.
- Spustíte plánované navrácení služeb po ubytovny z Azure do místního.
- Pro navrácení služeb po selhání virtuálním virtuálním virtuálním virtuálním ho Hyper-V není nutné nastavit žádné konkrétní součásti.
- Během plánovaného převzetí služeb při selhání můžete vybrat možnosti synchronizace dat před navrácením služeb po selhání:
    - **Synchronizace dat před převzetím služeb při selhání**: Tato možnost minimalizuje prostoje virtuálních počítačů, protože synchronizuje počítače bez jejich vypnutí.
        - Fáze 1: Pořídí snímek virtuálního počítače Azure a zkopíruje ho do místního hostitele Hyper-V. Počítač pokračuje v provozu v Azure.
        - Fáze 2: Vypne virtuální počítač Azure tak, aby žádné nové změny dojít tam. Konečná sada rozdílových změn se přenese na místní server a spustí se místní virtuální počítač.
    - **Synchronizovat data pouze během převzetí služeb při selhání**: Tato možnost je rychlejší, protože očekáváme, že většina disku se změnila, a proto neprovádějte výpočty kontrolního součtu. Provede stahování disku. Doporučujeme použít tuto možnost, pokud virtuální počítač běží v Azure na chvíli (měsíc nebo více), nebo pokud místní virtuální počítač byl odstraněn.

[Další informace](hyper-v-azure-failback.md) o opětovnéochraně technologie Hyper-V a navrácení služeb po obnovení.

Když znovu protectazure virtuálních počítačích do místního, můžete určit, že chcete poobnovení zpět do původního umístění nebo do alternativního umístění.

- **Obnovení původního umístění**: To se nezdaří zpět z Azure do stejného místního počítače, pokud existuje. V tomto scénáři vyberete jednu z možností synchronizace popsaných v předchozím postupu.
- **Alternativní obnovení umístění**: Pokud místní počítač neexistuje, můžete obnovit služby poobnovení z Azure do alternativního umístění. Když znovu protect u virtuálního počítače Azure na místní, místní počítač se vytvoří. Pomocí této možnosti doporučujeme vybrat možnost synchronizace dat před převzetím služeb při selhání.
- [Zkontrolujte](hyper-v-azure-failback.md) požadavky a omezení pro navrácení služeb po selhání umístění.


Po selhání zpět do místní lokality, můžete povolit **reverzní replikovat** a začít replikovat virtuální počítač do Azure, dokončení cyklu.




## <a name="next-steps"></a>Další kroky
- Převzetí služeb při selhání [konkrétních virtuálních měn VMware](vmware-azure-tutorial-failover-failback.md)
- Převzetí služeb při selhání [konkrétních virtuálních terminálů Hyper-V](hyper-v-azure-failover-failback-tutorial.md).
- [Vytvořte](site-recovery-create-recovery-plans.md) plán obnovení.
- Převzetí služeb při selhání [virtuálních společností v plánu obnovení](site-recovery-failover.md).
- [Připravte se na](vmware-azure-failback.md) Opětovné prosazení společnosti VMware a navrácení služeb po obnovení.
- Virtuální [aplikace Hyper-V](hyper-v-azure-failback.md), která si je zpět, navrácení služeb po selhání .

