---
title: Spuštění převzetí služeb při selhání při zotavení po havárii pomocí Azure Site Recovery
description: Jak převzít služby při selhání virtuálních počítačů nebo fyzických serverů do Azure pomocí Azure Site Recovery.
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 481e7c692be24bbebd14584f8158740a5b7043ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317884"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>Spuštění převzetí služeb při selhání z místního prostředí do Azure

Tento článek popisuje, jak převzít služby při selhání místních počítačů do Azure v [Azure Site Recovery](site-recovery-overview.md)

## <a name="before-you-start"></a>Než začnete

- [Přečtěte si informace](failover-failback-overview.md) o procesu převzetí služeb při selhání při zotavení po havárii.
- Pokud chcete převzít služby při selhání více počítačů, [Přečtěte si](recovery-plan-overview.md) , jak v plánu obnovení shromažďovat počítače dohromady.
- Před provedením úplného převzetí služeb při selhání spusťte postup [zotavení po havárii](site-recovery-test-failover-to-azure.md) , abyste měli jistotu, že všechno funguje podle očekávání.

## <a name="prepare-to-connect-after-failover"></a>Příprava na připojení po převzetí služeb při selhání

Abyste se ujistili, že se můžete připojit k virtuálním počítačům Azure vytvořeným po převzetí služeb při selhání, najdete tady několik věcí, které je třeba provést v místním prostředí před převzetím služeb při selhání.


### <a name="prepare-on-premises-to-connect-after-failover"></a>Příprava místního připojení po převzetí služeb při selhání

Pokud se chcete připojit k virtuálním počítačům Azure pomocí protokolu RDP/SSH po převzetí služeb při selhání, je třeba provést několik věcí ještě před převzetím služeb při selhání.

**Po převzetí služeb při selhání** | **Umístění** | **Akce**
--- | --- | ---
**Virtuální počítač Azure s Windows** | Místní počítač před převzetím služeb při selhání | Pokud chcete získat přístup k virtuálnímu počítači Azure přes Internet, povolte RDP a ujistěte se, že jsou přidaná pravidla TCP a UDP pro **veřejné**a že protokol RDP je povolený pro všechny profily v povolených aplikacích **brány Windows Firewall**  >  **Allowed Apps**.<br/><br/> Pokud chcete získat přístup k virtuálnímu počítači Azure přes připojení typu Site-to-site, povolte na počítači protokol RDP a zajistěte, aby byl v **bráně Windows Firewall**  ->  **povolené aplikace a funkce**pro **domény a privátní** sítě povolený protokol RDP.<br/><br/> <br/><br/> Odeberte všechny statické trvalé trasy a proxy WinHTTP. Ujistěte se, že je zásada SAN operačního systému nastavená na **OnlineAll**. [Další informace](https://support.microsoft.com/kb/3031135).<br/><br/> Při aktivaci převzetí služeb při selhání se ujistěte, že na virtuálním počítači nečekají žádné aktualizace Windows. Windows Update se může spustit, když převezmete služby při selhání a nebudete se moct přihlásit k virtuálnímu počítači, dokud se aktualizace nedokončí.
**Virtuální počítač Azure se systémem Linux** | Místní počítač před převzetím služeb při selhání | Ujistěte se, že je služba Secure Shell na virtuálním počítači nastavená tak, aby se automaticky spouštěla při spuštění systému.<br/><br/> Zkontrolujte, jestli pravidla brány firewall umožňují službě SSH připojit se k ní.


## <a name="run-a-failover"></a>Spuštění převzetí služeb při selhání

Tento postup popisuje, jak spustit převzetí služeb při selhání pro [plán obnovení](site-recovery-create-recovery-plans.md). Pokud chcete spustit převzetí služeb při selhání pro jeden virtuální počítač, postupujte podle pokynů pro [virtuální počítač VMware](vmware-azure-tutorial-failover-failback.md), [fyzický server](physical-to-azure-failover-failback.md)nebo [virtuální počítač Hyper-V](hyper-v-azure-failover-failback-tutorial.md).


Spusťte plán obnovení převzetí služeb při selhání následujícím způsobem:

1. V Site Recovery trezoru vyberte **plány obnovení**  >  *recoveryplan_name*.
2. Klikněte na **převzetí služeb při selhání**.

    ![Snímek obrazovky z Azure Site Recovery zobrazující podokno ADRP s převzetím služeb při selhání vybrané z nabídky další.](./media/site-recovery-failover/Failover.png)

3. V **Failover**  >  části**směr převzetí**služeb při selhání přenechejte výchozí nastavení, pokud se chystáte replikovat do Azure.
4. V části **převzetí služeb při selhání**vyberte **bod obnovení** , u kterého chcete převzít služby při selhání.

    - **Nejnovější**: použijte nejnovější bod. Tato operace zpracuje všechna data, která jsou odeslána do služby Site Recovery Service, a vytvoří bod obnovení pro každý počítač. Tato možnost poskytuje nejnižší cíl bodu obnovení (RPO), protože virtuální počítač vytvořený po převzetí služeb při selhání má všechna data, která se replikují do Site Recovery při aktivaci převzetí služeb při selhání.
    Počítejte s tím, že pokud zdrojová oblast nefunguje, není možné žádné další zpracování protokolu. Proto budete muset převzít převzetí služeb při selhání na nejnovější zpracovaný bod obnovení. Podívejte se na další bod, kde najdete další informace.
   - **Poslední zpracovaná**: tuto možnost použijte, pokud chcete převzít služby virtuálních počítačů na nejnovější bod obnovení, který už Site Recovery zpracoval. V případě, že se na virtuálním počítači nacházejí **nejnovější body obnovení**, můžete vidět nejnovější zpracovaný bod obnovení. Tato možnost poskytuje nízkou RTOi, protože nestráví zpracováním nezpracovaných dat žádného času.
   - **Nejnovější konzistentní vzhledem k aplikacím**: tuto možnost použijte, pokud chcete provést selhání virtuálních počítačů do nejnovějšího bodu obnovení konzistentního vzhledem k aplikacím, který byl zpracován Site Recovery.
   - **Poslední zpracovaná aplikace pro více virtuálních počítačů**: s touto možností virtuální počítače, které jsou součástí replikační skupiny, převzetí služeb při selhání nejnovějším běžným bodem obnovení s více virtuálními počítači. U ostatních virtuálních počítačů převezme služby při selhání nejnovější zpracovaný bod obnovení. Tato možnost je dostupná jenom pro plány obnovení, které mají aspoň jeden virtuální počítač s povolenou konzistencí pro víc virtuálních počítačů.
   - **Nejnovější konzistentní vzhledem k aplikacím pro více virtuálních počítačů**: s touto možností virtuální počítače, které jsou součástí replikační skupiny, převezme nejnovější společný bod obnovení konzistentní s aplikacemi pro více virtuálních počítačů. Ostatní virtuální počítače převzetí služeb při selhání nejnovějším bodem obnovení konzistentním vzhledem k aplikacím. Jenom pro plány obnovení, které mají povolený konzistenci s více virtuálními počítači aspoň jeden virtuální počítač.
   - **Vlastní**: není k dispozici pro plány obnovení. Tato možnost je určena jenom pro převzetí služeb při selhání pro jednotlivé virtuální počítače.

5. Před zahájením **převzetí služeb při selhání vyberte možnost vypnout počítač** , pokud chcete Site Recovery vypnout zdrojové virtuální počítače před spuštěním převzetí služeb při selhání. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede.  

    > [!NOTE]
    > Pokud dojde k převzetí služeb při selhání virtuálních počítačů Hyper-V, před aktivací převzetí služeb při selhání se nástroj pro vypnutí pokusí synchronizovat a replikovat místní data, která ještě nebyla odeslána do služby. 

6. Sledujte postup převzetí služeb při selhání na stránce **úlohy** . I když dojde k chybám, bude plán obnovení spuštěn až do dokončení.
7. Po převzetí služeb při selhání se přihlaste k virtuálnímu počítači a ověřte ho. 
8. Pokud chcete pro převzetí služeb při selhání přepnout na jiný bod obnovení, použijte **změnu bodu obnovení**.
9. Až budete připraveni, můžete převzetí služeb při selhání potvrdit. Akce **potvrzení** odstraní všechny body obnovení, které jsou k dispozici ve službě. Možnost **změnit bod obnovení** již nebude k dispozici.

## <a name="run-a-planned-failover-hyper-v"></a>Spuštění plánovaného převzetí služeb při selhání (Hyper-V)

Pro virtuální počítače Hyper-V můžete spustit plánované převzetí služeb při selhání.

- Plánované převzetí služeb při selhání je možnost převzetí služeb při selhání s nulovou ztrátou.
- Při aktivaci plánovaného převzetí služeb při selhání se nejdřív odpojí zdrojové virtuální počítače, synchronizují se nejnovější data a pak se aktivuje převzetí služeb při selhání.
- Plánované převzetí služeb při selhání spustíte pomocí možnosti **plánovaného převzetí služeb při selhání** . Spouští se podobným způsobem jako běžné převzetí služeb při selhání.
 
## <a name="track-failovers"></a>Sledovat převzetí služeb při selhání

K převzetí služeb při selhání je přidruženo několik úloh.

![Snímek obrazovky stránky úlohy zobrazující seznam úloh se skupinou 1: začátek (1) rozbalený ve sloupci název. Řádek pro úlohu SQLServer je zvýrazněný.](./media/site-recovery-failover/FailoverJob.png)

- **Kontroly předpokladů**: zajišťuje splnění všech podmínek vyžadovaných pro převzetí služeb při selhání.
- **Převzetí služeb při selhání**: zpracovává data, aby bylo možné z ní vytvořit virtuální počítač Azure. Pokud jste zvolili **nejnovější** bod obnovení, vytvoří se bod obnovení z dat, která byla odeslána do služby.
- **Start**: vytvoří virtuální počítač Azure pomocí dat zpracovaných v předchozím kroku.

> [!WARNING]
> **Nemůžete zrušit probíhající převzetí služeb při selhání**: před spuštěním převzetí služeb při selhání se replikace pro virtuální počítač zastavila. Pokud zrušíte probíhající úlohu, převzetí služeb při selhání se zastaví, ale virtuální počítač se nezačne replikovat. Replikaci nelze spustit znovu.


### <a name="extra-failover-time"></a>Dodatečný čas převzetí služeb při selhání

V některých případech vyžaduje převzetí služeb virtuálního počítače při selhání přechodný krok, který dokončení trvá přibližně osm až 10 minut. Jedná se o počítače, které jsou ovlivněny tímto dalším krokem/časem:

* Virtuální počítače VMware s verzí služby mobility, která je starší než 9,8.
* Fyzické servery a virtuální počítače Hyper-V chráněné jako fyzické servery.
* Virtuální počítače VMware Linux.
* Virtuální počítače VMware, na kterých tyto ovladače nejsou přítomné jako ovladače pro spouštění:
    * storvsc
    * VMBus
    * storflt
    * Intelide
    * ovladač
* Virtuální počítače VMware bez povoleného DHCP bez ohledu na to, jestli používají DHCP nebo statické IP adresy.


## <a name="automate-actions-during-failover"></a>Automatizace akcí během převzetí služeb při selhání

Během převzetí služeb při selhání možná budete chtít automatizovat akce. K tomu můžete použít skripty nebo Runbooky Azure Automation v plánech obnovení.

- [Přečtěte si](site-recovery-create-recovery-plans.md) o vytváření a přizpůsobení plánů obnovení, včetně přidávání skriptů.
- [Přečtěte si](site-recovery-runbook-automation.md) , jak přidat Azure Automation Runbooky do plánů obnovení.


## <a name="configure-settings-after-failover"></a>Konfigurace nastavení po převzetí služeb při selhání

### <a name="retain-drive-letters-after-failover"></a>Po převzetí služeb při selhání zachovat písmena jednotek

Site Recovery zpracovává uchovávání písmen jednotek. Pokud při replikaci virtuálních počítačů vyloučíte disky, [Podívejte se na příklad](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) toho, jak to funguje.

### <a name="prepare-in-azure-to-connect-after-failover"></a>Příprava v Azure pro připojení po převzetí služeb při selhání

Pokud se chcete připojit k virtuálním počítačům Azure vytvořeným po převzetí služeb při selhání pomocí protokolu RDP nebo SSH, postupujte podle požadavků shrnutých v tabulce.

**Převzetí služeb při selhání** | **Umístění** | **Akce**
--- | --- | ---
**Virtuální počítač Azure s Windows** | Virtuální počítač Azure po převzetí služeb při selhání |  [Přidejte veřejnou IP adresu](https://aka.ms/addpublicip) pro tento virtuální počítač.<br/><br/> Pravidla skupiny zabezpečení sítě na virtuálním počítači služby převzetí služeb při selhání (a v podsíti Azure, ke které je připojené), musí umožňovat příchozí připojení k portu RDP.<br/><br/> Zkontrolujte **diagnostiku spouštění** a ověřte snímek obrazovky virtuálního počítače.<br/><br/> Pokud se nemůžete připojit, zkontrolujte, že je virtuální počítač spuštěný, a přečtěte si tyto [tipy k odstraňování potíží](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Virtuální počítač Azure se systémem Linux** | Virtuální počítač Azure po převzetí služeb při selhání | Pravidla skupiny zabezpečení sítě na virtuálním počítači služby převzetí služeb při selhání (a v podsíti Azure, ke které je připojené), musí umožňovat příchozí připojení k portu SSH.<br/><br/> [Přidejte veřejnou IP adresu](https://aka.ms/addpublicip) pro tento virtuální počítač.<br/><br/> Podívejte se na **diagnostiku spouštění** pro snímek obrazovky virtuálního počítače.<br/><br/>

Při řešení problémů s připojením po převzetí služeb při selhání použijte [zde](site-recovery-failover-to-azure-troubleshoot.md) popsaný postup.

## <a name="set-up-ip-addressing"></a>Nastavení adresování IP

- **Interní IP adresy**: Pokud chcete nastavit interní IP adresu virtuálního počítače Azure po převzetí služeb při selhání, máte několik možností:
    - Zachovat stejnou IP adresu: můžete použít stejnou IP adresu na VIRTUÁLNÍm počítači Azure jako počítač přidělený místnímu počítači.
    - Použijte jinou IP adresu: pro virtuální počítač Azure můžete použít jinou IP adresu.
    - [Přečtěte si další informace](concepts-on-premises-to-azure-networking.md#assign-an-internal-address) o nastavení interních IP adres.
- **Externí IP adresy**: veřejné IP adresy můžete uchovávat při převzetí služeb při selhání. Virtuálním počítačům Azure vytvořeným v rámci procesu převzetí služeb při selhání musí být k dispozici veřejná IP adresa Azure dostupná v oblasti Azure. Veřejnou IP adresu můžete přiřadit buď ručně, nebo automatizací procesu s plánem obnovení. [Další informace](concepts-public-ip-address-with-site-recovery.md).


## <a name="next-steps"></a>Další kroky

Po převzetí služeb při selhání budete muset znovu zapnout ochranu a začít replikovat virtuální počítače Azure zpátky do místní lokality. Po zprovoznění replikace můžete provést obnovení místně, až budete připraveni.

- [Přečtěte si další informace](failover-failback-overview.md#reprotectionfailback) o reochraně a navrácení služeb po obnovení.
- [Příprava](vmware-azure-reprotect.md) na reochranu VMware a navrácení služeb po obnovení.
- Navrácení [služeb po obnovení](hyper-v-azure-failback.md) Virtuální počítače Hyper-V.
- [Další informace o](physical-to-azure-failover-failback.md) procesu převzetí služeb při selhání a navrácení služeb po obnovení pro fyzické servery

