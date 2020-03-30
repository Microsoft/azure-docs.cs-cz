---
title: Spuštění převzetí služeb při selhání během zotavení po havárii pomocí Azure Site Recovery
description: Jak převzetí služeb při selhání virtuálních počítačů nebo fyzických serverů do Azure pomocí Azure Site Recovery.
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 99a197e8f5ebac8a3b0be1b567ee41b43a2c4476
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471264"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>Spuštění převzetí služeb při selhání z místního do Azure

Tento článek popisuje, jak převzetí služeb při selhání místní počítače do Azure v [Azure Site Recovery](site-recovery-overview.md)

## <a name="before-you-start"></a>Než začnete

- [Další informace](failover-failback-overview.md) o procesu převzetí služeb při selhání v zotavení po havárii.
- Pokud chcete převzetí služeb při selhání více počítačů, [přečtěte si,](recovery-plan-overview.md) jak shromáždit počítače společně v plánu obnovení.
- Před úplným převzetím služeb při selhání spusťte [cvičení zotavení po havárii,](site-recovery-test-failover-to-azure.md) abyste zajistili, že vše funguje podle očekávání.

## <a name="prepare-to-connect-after-failover"></a>Příprava na připojení po převzetí služeb při selhání

Pokud se ujistěte, že se můžete připojit k virtuálním počítačům Azure, které se vytvoří po převzetí služeb při selhání, tady je řada věcí, které musíte před převzetím služeb při selhání provést v místním prostředí.


### <a name="prepare-on-premises-to-connect-after-failover"></a>Příprava místního připojení po převzetí služeb při selhání

Pokud se chcete připojit k virtuálním počítačům Azure pomocí RDP/SSH po převzetí služeb při selhání, existuje řada věcí, které musíte udělat místní před převzetím služeb při selhání.

**Po převzetí služeb při selhání** | **Umístění** | **Akce**
--- | --- | ---
**Virtuální počítač Azure s Windows** | Místní počítač před převzetím služeb při selhání | Chcete-li získat přístup k virtuálnímu počítači Azure přes internet, povolte protokol RDP a ujistěte se, že jsou pro **veřejné**přidána pravidla Protokolu TCP a UDP a že protokol RDP je povolen pro všechny profily v**aplikacích povolených pro** **bránu** > Windows Firewall .<br/><br/> Přístup k virtuálnímu počítači Azure přes připojení mezi lokalitami povolte v počítači zásady rdp a ujistěte se, že je rdp povolen v**aplikacích a funkcích povolené bránou** **Windows Firewall** -> pro **domény a privátní** sítě.<br/><br/> <br/><br/> Odeberte všechny statické trvalé trasy a proxy server WinHTTP. Ujistěte se, že zásady sítě SAN operačního systému jsou nastaveny na **onlineall**. [Další informace](https://support.microsoft.com/kb/3031135).<br/><br/> Ujistěte se, že neexistují žádné aktualizace systému Windows čekající na virtuální ms při aktivaci převzetí služeb při selhání. Služba Windows Update se může spustit při převzetí služeb při selhání a dokud nebude aktualizace dokončena, nebudete se moci k virtuálnímu počítači přihlásit.
**Virtuální počítač Azure s Linuxem** | Místní počítač před převzetím služeb při selhání | Ujistěte se, že služba Secure Shell na virtuálním počítači je nastavena na automatické spuštění při spuštění systému.<br/><br/> Zkontrolujte, jestli pravidla brány firewall umožňují službě SSH připojit se k ní.


## <a name="run-a-failover"></a>Spuštění převzetí služeb při selhání

Tento postup popisuje, jak spustit převzetí služeb při selhání pro [plán obnovení](site-recovery-create-recovery-plans.md). Pokud chcete spustit převzetí služeb při selhání pro jeden virtuální virtuální ms, postupujte podle pokynů pro [virtuální hod VMware](vmware-azure-tutorial-failover-failback.md), [fyzický server](physical-to-azure-failover-failback.md)nebo [virtuální hod Hyper-V](hyper-v-azure-failover-failback-tutorial.md).


Spusťte převzetí služeb při selhání plánu obnovení následujícím způsobem:

1. V trezoru obnovení webu vyberte **plány** > obnovení*recoveryplan_name*.
2. Klepněte na **položku Převzetí služeb při selhání**.

    ![Převzetí služeb při selhání](./media/site-recovery-failover/Failover.png)

3. Ve **Failover** > **směru převzetí služeb při selhání**ponechte výchozí, pokud se replikujete do Azure.
4. V **převzetí služeb při selhání**vyberte bod **obnovení,** do kterého chcete přepojit.

    - **Nejnovější**: Použijte nejnovější bod. To zpracuje všechna data, která byla odeslána do služby Site Recovery a vytvoří bod obnovení pro každý počítač. Tato možnost poskytuje nejnižší RPO (cíl bodu obnovení), protože virtuální ms vytvořený po převzetí služeb při selhání má všechna data, která byla replikována do obnovení sítě při aktivaci převzetí služeb při selhání.
   - **Poslední zpracované**: Pomocí této možnosti můžete přepojit virtuální chod na nejnovější bod obnovení, který již bylo zpracováno obnovením webu. Nejnovější zpracovaný bod obnovení se zobrazí v bodech obnovení nejnovějších obnovení virtuálního **montovny**. Tato možnost poskytuje nízké RTO, protože není čas strávený zpracováním nezpracovaných dat.
   - **Nejnovější konzistentní aplikace**: Pomocí této možnosti selhání virtuálních připojení k nejnovější aplikace konzistentní bod obnovení, který byl zpracován site recovery.
   - **Nejnovější zpracování více virtuálních virtuálních stránek**: Pomocí této možnosti virtuálních stránek, které jsou součástí převzetí služeb při selhání replikační skupiny na nejnovější společný bod obnovení konzistentní více virtuálních stránek. Ostatní virtuální počítače převzetí služeb při selhání na jejich nejnovější zpracované bod obnovení. Tato možnost je jenom pro plány obnovení, které mají alespoň jeden virtuální virtuální ms s povolenou konzistencí více virtuálních zařízení.
   - **Nejnovější aplikace konzistentní s více virtuálními virtuálními aplikacemi**: Pomocí této možnosti virtuální chody, které jsou součástí replikační skupiny převzetí služeb při selhání na nejnovější společný bod obnovení konzistentní s více virtuálními aplikacemi. Ostatní virtuální počítače převzetí služeb při selhání na jejich nejnovější bod obnovení konzistentní s aplikací. Jenom pro plány obnovení, které mají alespoň jeden virtuální virtuální ms s povolenou konzistencí více virtuálních zařízení.
   - **Vlastní**: Není k dispozici pro plány obnovení. Tato možnost je jenom pro převzetí služeb při selhání jednotlivých virtuálních ms.

5. Vyberte **Vypnout počítač před zahájením převzetí služeb při selhání,** pokud chcete, aby site recovery vypnout zdrojové virtuální počítače před zahájením převzetí služeb při selhání. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede.  

    > [!NOTE]
    > Pokud jste převzetí služeb při selhání virtuálních počítačích Hyper-V, vypnutí se pokusí synchronizovat a replikovat místní data, která ještě nebyla odeslána do služby, před aktivací převzetí služeb při selhání. 

6. Postupujte podle postupu převzetí služeb při selhání na stránce **Úlohy.** I v případě, že dojde k chybám, plán obnovení spustí, dokud není dokončena.
7. Po převzetí služeb při selhání se přihlaste k virtuálnímu virtuálnímu virtuálnímu mněmu a ověřte ho. 
8. Pokud chcete přepnout na jiný bod obnovení, který chcete použít pro převzetí služeb při selhání, použijte **změnit bod obnovení**.
9. Až budete připraveni, můžete převzetí služeb při selhání potvrdit. Akce **Potvrzení** odstraní všechny body obnovení, které jsou ve službě k dispozici. Možnost **Změnit bod obnovení** již nebude k dispozici.

## <a name="run-a-planned-failover-hyper-v"></a>Spuštění plánovaného převzetí služeb při selhání (Hyper-V)

Můžete spustit plánované převzetí služeb při selhání pro virtuální aplikace Hyper-V.

- Plánované převzetí služeb při selhání je možnost převzetí služeb při selhání s nulovou ztrátou dat.
- Při aktivaci plánovaného převzetí služeb při selhání se nejprve vypnou zdrojové virtuální počítače, synchronizují se nejnovější data a pak se aktivuje převzetí služeb při selhání.
- Plánované převzetí služeb při selhání spustíte pomocí **možnosti Plánované převzetí služeb při selhání.** Běží podobným způsobem jako běžné převzetí služeb při selhání.
 
## <a name="track-failovers"></a>Sledování převzetí služeb při selhání

Existuje několik úloh spojených s převzetím služeb při selhání.

![Převzetí služeb při selhání](./media/site-recovery-failover/FailoverJob.png)

- **Kontrola požadavků**: Zajišťuje, že jsou splněny všechny podmínky požadované pro převzetí služeb při selhání.
- **Převzetí služeb při selhání**: Zpracuje data tak, aby z něj bylo možné vytvořit virtuální počítač Azure. Pokud jste zvolili **poslední** bod obnovení, bod obnovení je vytvořen z dat, která byla odeslána do služby.
- **Start**: Vytvoří virtuální počítač Azure pomocí dat zpracovaných v předchozím kroku.

> [!WARNING]
> **Nerušit převzetí služeb při selhání probíhá**: Před spuštěním převzetí služeb při selhání, replikace s zastavena pro virtuální hod. Pokud zrušíte probíhající úlohu, převzetí služeb při selhání se zastaví, ale virtuální ho dispozice se nezačne replikovat. Replikaci nelze znovu spustit.


### <a name="extra-failover-time"></a>Extra doba převzetí služeb při selhání

V některých případech vyžaduje převzetí služeb při selhání virtuálního měsíčku převzetí služeb při selhání mezikrok, který obvykle trvá přibližně osm až 10 minut. Jedná se o počítače, které jsou ovlivněny tento další krok/ čas:

* Virtuální počítače VMware s verzí služby Mobility starší než 9.8.
* Fyzické servery a virtuální počítače Hyper-V chráněné jako fyzické servery.
* Virtuální počítače VMware Linux.
* Virtuální počítačvsystému VMware, u kterých tyto ovladače nejsou k dispozici jako spouštěcí ovladače:
    * storvsc
    * vmbus
    * storflt řekl:
    * srozumitelný
    * Atapi
* Virtuální servery VMware, které nemají povolenou službu DHCP, bez ohledu na to, zda používají adresy DHCP nebo statické ADRESY IP.


## <a name="automate-actions-during-failover"></a>Automatizace akcí během převzetí služeb při selhání

Můžete chtít automatizovat akce během převzetí služeb při selhání. Chcete-li to provést, můžete použít skripty nebo azure automatizace runbooky v plánech obnovení.

- [Přečtěte si o](site-recovery-create-recovery-plans.md) vytváření a přizpůsobení plánů obnovení, včetně přidávání skriptů.
- [Přečtěte si](site-recovery-runbook-automation.md) o přidávání runbooků Azure Automation do plánů obnovení.


## <a name="configure-settings-after-failover"></a>Konfigurace nastavení po převzetí služeb při selhání

### <a name="retain-drive-letters-after-failover"></a>Zachovat písmena jednotek po převzetí služeb při selhání

Obnovení webu zpracovává uchovávání písmen jednotek. Pokud během replikace virtuálního počítače vylučujete disky, [zkontrolujte příklad,](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) jak to funguje.

### <a name="prepare-in-azure-to-connect-after-failover"></a>Příprava v Azure na připojení po převzetí služeb při selhání

Pokud se chcete připojit k virtuálním počítačům Azure, které se vytvoří po převzetí služeb při selhání pomocí rdp nebo SSH, postupujte podle požadavků shrnutých v tabulce.

**Zabezpečení před selháním** | **Umístění** | **Akce**
--- | --- | ---
**Virtuální počítač Azure s Windows** | Virtuální počítač Azure po převzetí služeb při selhání |  [Přidejte veřejnou IP adresu](https://aka.ms/addpublicip) pro tento virtuální počítač.<br/><br/> Pravidla skupiny zabezpečení sítě pro převzetí počítače s převzetím počítače převzetím služby (a podsíť Azure, ke které je připojen) musí povolit příchozí připojení k portu RDP.<br/><br/> Zkontrolujte **diagnostiku spuštění** a ověřte snímek obrazovky virtuálního aplikace.<br/><br/> Pokud se nemůžete připojit, zkontrolujte, jestli je spuštěný virtuální hod, a projděte si tyto [tipy pro řešení potíží](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Virtuální počítač Azure s Linuxem** | Virtuální počítač Azure po převzetí služeb při selhání | Pravidla skupiny zabezpečení sítě pro převzetí počítače s připojením na vědomí (a podsíť Azure, ke které je připojen) musí povolit příchozí připojení k portu SSH.<br/><br/> [Přidejte veřejnou IP adresu](https://aka.ms/addpublicip) pro tento virtuální počítač.<br/><br/> Zkontrolujte **diagnostiku spuštění** pro snímek virtuálního aplikace.<br/><br/>

Při řešení problémů s připojením po převzetí služeb při selhání použijte [zde](site-recovery-failover-to-azure-troubleshoot.md) popsaný postup.

## <a name="set-up-ip-addressing"></a>Nastavení adresování IP

- **Interní IP adresy**: Nastavení interní IP adresy virtuálního počítače Azure po převzetí služeb při selhání máte několik možností:
    - Zachovat stejnou IP adresu: Můžete použít stejnou IP adresu na virtuálním počítači Azure jako ten, který je přidělen místnímu počítači.
    - Použití jiné IP adresy: Pro virtuální počítač Azure můžete použít jinou IP adresu.
    - [Přečtěte si další informace](concepts-on-premises-to-azure-networking.md#assign-an-internal-address) o nastavení interních IP adres.
- **Externí IP adresy**: Veřejné IP adresy můžete uchovávat při převzetí služeb při selhání. Virtuální počítače Azure vytvořené jako součást procesu převzetí služeb při selhání musí být přiřazeny veřejné IP adresy Azure k dispozici v oblasti Azure. Veřejnou IP adresu můžete přiřadit ručně nebo automatizací procesu pomocí plánu obnovení. [Další informace](concepts-public-ip-address-with-site-recovery.md).


## <a name="next-steps"></a>Další kroky

Po převzetí služby při selhání, musíte znovu chránit a začít replikovat virtuální počítače Azure zpět do místního webu. Po dokončení replikace můžete po obnovení služby znovu místní, když jste připraveni.

- [Další informace](failover-failback-overview.md#reprotectionfailback) o opětovné ochraně a navrácení služeb po obnovení.
- [Připravte se](vmware-azure-reprotect.md) na opětovné protektorství vmware a navrácení služeb po obnovení.
- [Vrácení služeb při selhání](hyper-v-azure-failback.md) Virtuální aplikace Hyper-V.
- [Informace o](physical-to-azure-failover-failback.md) procesu převzetí služeb při selhání a navrácení služeb po selhání pro fyzické servery.

