---
title: Spuštění testovacího převzetí služeb při selhání (procházení zotavení po havárii) do Azure v Azure Site Recovery
description: Přečtěte si o spuštění testovacího převzetí služeb při selhání z místního prostředí do Azure pomocí služby Azure Site Recovery.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 840243bd5a67bc97bdc1903908c4e25adad93062
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579294"
---
# <a name="run-a-test-failover-disaster-recovery-drill-to-azure"></a>Spuštění testovacího převzetí služeb při selhání (přechod zotavení po havárii) do Azure 


Tento článek popisuje, jak spustit postup zotavení po havárii do Azure pomocí Site Recovery testovacího převzetí služeb při selhání.  

Testovací převzetí služeb při selhání můžete použít k ověření vaší strategie replikace a zotavení po havárii, aniž by došlo ke ztrátě dat nebo výpadkům. Testovací převzetí služeb při selhání nemá vliv na probíhající replikaci nebo na vaše produkční prostředí. Testovací převzetí služeb při selhání můžete spustit na konkrétním virtuálním počítači (VM) nebo v [plánu obnovení](site-recovery-create-recovery-plans.md) , který obsahuje více virtuálních počítačů.


## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání
Tento postup popisuje, jak spustit testovací převzetí služeb při selhání pro plán obnovení. Pokud chcete spustit testovací převzetí služeb při selhání pro jeden virtuální počítač, postupujte podle kroků popsaných [tady](tutorial-dr-drill-azure.md#run-a-test-failover-for-a-single-vm) .

![Snímek stránky testovacího převzetí služeb při selhání v Azure Portal.](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. V Site Recovery Azure Portal klikněte na **plány obnovení**  >  *recoveryplan_name*  >  **testovací převzetí služeb při selhání**.
2. Vyberte **bod obnovení** , u kterého chcete převzít služby při selhání. Můžete použít jednu z následujících možností:
    - **Poslední zpracování**: Tato možnost převezme všechny virtuální počítače v plánu na nejnovější bod obnovení zpracovaný Site Recovery. Chcete-li zobrazit nejnovější bod obnovení pro konkrétní virtuální počítač, zkontrolujte v nastavení virtuálního počítače **nejnovější body obnovení** . Tato možnost poskytuje nízkou plánovanou dobu obnovení (RTO), protože se neztrácí žádný čas zpracováním nezpracovaných dat.
    - **Nejnovější konzistentní vzhledem k aplikacím**: Tato možnost převezme všechny virtuální počítače v plánu na nejnovější bod obnovení konzistentní vzhledem k aplikacím zpracovaného Site Recovery. Chcete-li zobrazit nejnovější bod obnovení pro konkrétní virtuální počítač, zkontrolujte v nastavení virtuálního počítače **nejnovější body obnovení** .
    - **Nejnovější**: Tato možnost nejprve zpracuje všechna data, která byla odeslána do služby Site Recovery Service, aby bylo možné vytvořit bod obnovení pro každý virtuální počítač před tím, než dojde k převzetí služeb při selhání. Tato možnost poskytuje nejnižší cíl bodu obnovení (RPO), protože virtuální počítač vytvořený po převzetí služeb při selhání bude mít všechna data replikována do Site Recovery při aktivaci převzetí služeb při selhání.
    - **Nejnovější zpracovaný vícenásobný virtuální počítač**: Tato možnost je k dispozici pro plány obnovení s povoleným virtuálním počítačem s více virtuálními počítači. Virtuální počítače s povoleným nastavením převezmou na nejnovější společný bod obnovení konzistentní s více virtuálními počítači. Ostatní virtuální počítače převezmou služby na nejnovější zpracovaný bod obnovení.  
    - **Nejnovější konzistentní vzhledem k aplikacím pro více virtuálních počítačů**: Tato možnost je k dispozici pro plány obnovení s povoleným zajištěním konzistence více virtuálních počítačů s jedním nebo více virtuálními počítači. Virtuální počítače, které jsou součástí replikační skupiny, převezmou nejnovější běžný bod obnovení konzistentní s aplikacemi pro více virtuálních počítačů. Jiné virtuální počítače převezme služby při selhání do svého nejnovějšího bodu obnovení konzistentního vzhledem k aplikacím.
    - **Vlastní**: tuto možnost použijte, pokud chcete převzít služby při selhání určitého virtuálního počítače na určitý bod obnovení.
3. Vyberte virtuální síť Azure, ve které se vytvoří testovací virtuální počítače.

    - Site Recovery se pokusí vytvořit testovací virtuální počítače v podsíti se stejným názvem a stejnou IP adresou, jaké jsou uvedené v nastavení **výpočty a síť** virtuálního počítače.
    - Pokud ve virtuální síti Azure použité pro testovací převzetí služeb při selhání není dostupná podsíť se stejným názvem, testovací virtuální počítač se vytvoří v první podsíti abecedně.
    - Pokud v podsíti není dostupná stejná IP adresa, pak virtuální počítač obdrží další dostupnou IP adresu v podsíti. [Další informace](#create-a-network-for-test-failover).
4. Pokud převezmete služby při selhání do Azure a povolíte šifrování dat, v **šifrovacím klíči** vyberte certifikát, který byl vydán, když jste povolili šifrování během instalace poskytovatele. Pokud není šifrování povolené, můžete tento krok ignorovat.
5. Sledujte průběh převzetí služeb při selhání na kartě **úlohy** . Měli byste být schopni vidět počítač testovací repliky v Azure Portal.
6. Pokud chcete iniciovat připojení RDP k virtuálnímu počítači Azure, musíte [Přidat veřejnou IP adresu](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) do síťového rozhraní virtuálního počítače, u kterého došlo k převzetí služeb při selhání.
7. Pokud vše funguje podle očekávání, klikněte na **vyčistit testovací převzetí služeb při selhání**. Tím se odstraní virtuální počítače vytvořené během testovacího převzetí služeb při selhání.
8. V části **poznámky** si zaznamenejte a uložte všechny poznámky spojené s testovacím převzetím služeb při selhání.


![Snímek obrazovky s kartou úlohy testovacího převzetí služeb při selhání](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

Při aktivaci testovacího převzetí služeb při selhání dojde k následujícímu:

1. **Požadavky**: při kontrole požadovaných součástí se ujistěte, že jsou splněné všechny podmínky vyžadované pro převzetí služeb při selhání.
2. **Převzetí služeb při selhání**: procesy převzetí služeb při selhání a Příprava dat, aby z nich bylo možné vytvořit virtuální počítač Azure.
3. **Nejnovější**: Pokud jste zvolili nejnovější bod obnovení, vytvoří se bod obnovení z dat, která byla odeslána do služby.
4. **Spustit**: Tento krok vytvoří virtuální počítač Azure pomocí dat zpracovaných v předchozím kroku.

### <a name="failover-timing"></a>Časování převzetí služeb při selhání

V následujících scénářích vyžaduje převzetí služeb při selhání další krok, který dokončení trvá přibližně 8 až 10 minut:

* Virtuální počítače VMware s verzí služby mobility, která je starší než 9,8
* Fyzické servery
* Virtuální počítače VMware Linux
* Virtuální počítač Hyper-V chráněný jako fyzický server
* Virtuální počítač VMware, ve kterém nejsou k spouštěcí ovladače následující ovladače:
    * storvsc
    * VMBus
    * storflt
    * Intelide
    * ovladač
* Virtuální počítač VMware bez povoleného DHCP bez ohledu na to, jestli používají DHCP nebo statické IP adresy.

Ve všech ostatních případech není žádný přechodný krok nutný a převzetí služeb při selhání bude trvat mnohem kratší dobu.


## <a name="create-a-network-for-test-failover"></a>Vytvoření sítě pro testovací převzetí služeb při selhání

Pro testovací převzetí služeb při selhání doporučujeme zvolit síť, která je izolovaná od produkční sítě lokality pro obnovení zadané v nastavení **Výpočty a síť** jednotlivých virtuálních počítačů. Když vytvoříte virtuální síť Azure, ve výchozím nastavení je izolovaná od ostatních sítí. Testovací síť by měla napodobovat produkční síť:

- Testovací síť by měla mít stejný počet podsítí jako produkční síť. Podsítě by měly mít stejné názvy.
- Testovací síť by měla používat stejný rozsah IP adres.
- Aktualizujte DNS testovací sítě s použitím IP adresy zadané pro virtuální počítač DNS v nastavení **Výpočty a síť**. Další podrobnosti najdete v tématu věnovaném [aspektům, které je třeba zvážit při testování převzetí služeb při selhání pro Active Directory](site-recovery-active-directory.md#test-failover-considerations).


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Test převzetí služeb při selhání do produkční sítě v lokalitě pro obnovení

I když doporučujeme, abyste používali testovací síť oddělenou od produkční sítě, pokud chcete otestovat postup zotavení po havárii do produkční sítě, pamatujte na toto:

- Ujistěte se, že je primární virtuální počítač vypnutý při spuštění testovacího převzetí služeb při selhání. V opačném případě budou mít dva virtuální počítače se stejnou identitou, které běží ve stejné síti ve stejnou dobu. To může vést k neočekávaným důsledkům.
- Při vyčištění převzetí služeb při selhání dojde ke ztrátě všech změn virtuálních počítačů vytvořených pro testovací převzetí služeb při selhání. Tyto změny se nereplikují zpátky na primární virtuální počítač.
- Testování v produkčním prostředí vede k výpadkům produkční aplikace. Uživatelé nemůžou používat aplikace běžící na virtuálních počítačích, když testovací převzetí služeb při selhání probíhá.  



## <a name="prepare-active-directory-and-dns"></a>Příprava služby Active Directory a DNS

Pokud chcete spustit testovací převzetí služeb při selhání pro testování aplikací, potřebujete kopii prostředí Active Directory v testovacím prostředí. Další informace najdete [v tématu o testovacím převzetí služeb při selhání pro Active Directory](site-recovery-active-directory.md#test-failover-considerations) .

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Pokud se chcete po převzetí služeb při selhání připojit k virtuálním počítačům Azure pomocí protokolu RDP/SSH, postupujte podle požadavků shrnutých v tabulce.

**Převzetí služeb při selhání** | **Umístění** | **Akce**
--- | --- | ---
**Virtuální počítač Azure s Windows** | Místní počítač před převzetím služeb při selhání | Pokud chcete získat přístup k virtuálnímu počítači Azure přes Internet, povolte RDP a ujistěte se, že jsou přidaná pravidla TCP a UDP pro **veřejné** a že protokol RDP je povolený pro všechny profily v povolených aplikacích **brány Windows Firewall**  >  .<br/><br/> Pokud chcete získat přístup k virtuálnímu počítači Azure přes připojení typu Site-to-site, povolte na počítači protokol RDP a zajistěte, aby byl v **bráně Windows Firewall**  ->  **povolené aplikace a funkce** pro **domény a privátní** sítě povolený protokol RDP.<br/><br/>  Ujistěte se, že je zásada SAN operačního systému nastavená na **OnlineAll**. [Další informace](https://support.microsoft.com/kb/3031135).<br/><br/> Při aktivaci převzetí služeb při selhání se ujistěte, že na virtuálním počítači nečekají žádné aktualizace Windows. Windows Update se může spustit, když převezmete služby při selhání a nebudete se moct přihlásit k virtuálnímu počítači, dokud se aktualizace nedokončí.
**Virtuální počítač Azure s Windows** | Virtuální počítač Azure po převzetí služeb při selhání |  [Přidejte veřejnou IP adresu](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) pro tento virtuální počítač.<br/><br/> Pravidla skupiny zabezpečení sítě na virtuálním počítači služby převzetí služeb při selhání (a v podsíti Azure, ke které je připojené), musí umožňovat příchozí připojení k portu RDP.<br/><br/> Zkontrolujte **diagnostiku spouštění** a ověřte snímek obrazovky virtuálního počítače.<br/><br/> Pokud se nemůžete připojit, zkontrolujte, že je virtuální počítač spuštěný, a přečtěte si tyto [tipy k odstraňování potíží](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Virtuální počítač Azure se systémem Linux** | Místní počítač před převzetím služeb při selhání | Ujistěte se, že je služba Secure Shell na virtuálním počítači nastavená tak, aby se automaticky spouštěla při spuštění systému.<br/><br/> Zkontrolujte, jestli pravidla brány firewall umožňují službě SSH připojit se k ní.
**Virtuální počítač Azure se systémem Linux** | Virtuální počítač Azure po převzetí služeb při selhání | Pravidla skupiny zabezpečení sítě na virtuálním počítači služby převzetí služeb při selhání (a v podsíti Azure, ke které je připojené), musí umožňovat příchozí připojení k portu SSH.<br/><br/> [Přidejte veřejnou IP adresu](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) pro tento virtuální počítač.<br/><br/> Podívejte se na **diagnostiku spouštění** pro snímek obrazovky virtuálního počítače.<br/><br/>

Při řešení problémů s připojením po převzetí služeb při selhání použijte [zde](site-recovery-failover-to-azure-troubleshoot.md) popsaný postup.

## <a name="next-steps"></a>Další kroky
Po dokončení postupu zotavení po havárii si přečtěte další informace o dalších typech [převzetí služeb při selhání](site-recovery-failover.md).