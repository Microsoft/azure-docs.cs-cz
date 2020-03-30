---
title: Spuštění testovacího převzetí služeb při selhání (cvičení zotavení po havárii) do Azure v Azure Site Recovery
description: Další informace o spuštění testu převzetí služeb při selhání z místního do Azure pomocí služby Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 26c734b7a2e9f5592ee6d51dfee4650a3998ab1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257521"
---
# <a name="run-a-test-failover-disaster-recovery-drill-to-azure"></a>Spuštění testovacího převzetí služeb při selhání (cvičení zotavení po havárii) do Azure 


Tento článek popisuje, jak spustit postuppro zotavení po havárii do Azure pomocí převzetí služeb při selhání testu site recovery.  

Spuštění míse test převzetí služeb při selhání k ověření vaší strategie replikace a zotavení po havárii, bez ztráty dat nebo výpadky. Převzetí služeb při selhání testu nemá vliv na probíhající replikaci ani na produkční prostředí. Můžete spustit test převzetí služeb při selhání na konkrétním virtuálním počítači (VM) nebo na [plán obnovení](site-recovery-create-recovery-plans.md) obsahující více virtuálních počítačů.


## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání
Tento postup popisuje, jak spustit test převzetí služeb při selhání pro plán obnovení. Pokud chcete spustit test převzetí služeb při selhání pro jeden virtuální virtuální podnik, postupujte podle [kroků popsaných zde](tutorial-dr-drill-azure.md#run-a-test-failover-for-a-single-vm)

![Testovací převzetí služeb při selhání](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. V obnovení webu na webu na webu Azure portal klikněte na **plány** > obnovení*recoveryplan_name* > **test převzetí služeb při selhání**.
2. Vyberte **bod obnovení,** do kterého chcete přepojit. Můžete použít jednu z následujících možností:
    - **Poslední zpracované**: Tato možnost selže přes všechny virtuální chody v plánu na nejnovější bod obnovení zpracované site recovery. Pokud chcete zobrazit nejnovější bod obnovení pro konkrétní virtuální počítače, zkontrolujte **nejnovější body obnovení** v nastavení virtuálních počítače. Tato možnost poskytuje nízkou plánovanou dobu obnovení (RTO), protože se neztrácí žádný čas zpracováním nezpracovaných dat.
    - **Nejnovější konzistentní aplikace**: Tato možnost selže přes všechny virtuální chody v plánu na nejnovější bod obnovení konzistentní s aplikací zpracované site recovery. Pokud chcete zobrazit nejnovější bod obnovení pro konkrétní virtuální počítače, zkontrolujte **nejnovější body obnovení** v nastavení virtuálních počítače.
    - **Nejnovější**: Tato možnost nejprve zpracuje všechna data, která byla odeslána službě Site Recovery, a vytvoří bod obnovení pro každý virtuální virtuální server před převzetím služeb při selhání. Tato možnost poskytuje nejnižší RPO (Cíl bodu obnovení), protože virtuální ms vytvořený po převzetí služeb při selhání bude mít všechna data replikovaná do obnovení sítě při aktivaci převzetí služeb při selhání.
    - **Nejnovější zpracování více virtuálních virtuálních**zařízení : Tato možnost je k dispozici pro plány obnovení s jedním nebo více virtuálními aplikacemi, které mají povolenou konzistenci více virtuálních zařízení. Virtuální virtuální zařízení s povoleným nastavením převzetí služeb při selhání do nejnovějšího běžného bodu obnovení konzistentního s více virtuálními virtuálními zařízeními. Ostatní virtuální virtuální společnosti převzetí služeb při selhání na nejnovější zpracované bod obnovení.  
    - **Nejnovější aplikace konzistentní s více virtuálními virtuálními**zařízeními : Tato možnost je dostupná pro plány obnovení s jedním nebo více virtuálními aplikacemi, které mají povolenou konzistenci více virtuálních zařízení. Virtuální aplikace, které jsou součástí replikační skupiny převzetí služeb při selhání na nejnovější společný bod obnovení konzistentní s více virtuálními aplikacemi. Ostatní virtuální virtuální společnosti převzetí služeb při selhání na jejich nejnovější bod obnovení konzistentní s aplikací.
    - **Vlastní**: Pomocí této možnosti můžete převést na služebnou služeb při selhání konkrétní hovirtuální ho dispozičního programu do konkrétního bodu obnovení.
3. Vyberte virtuální síť Azure, ve které se vytvoří testovací virtuální počítače.

    - Obnovení lokality se pokusí vytvořit testovací virtuální počítače v podsíti se stejným názvem a stejnou IP adresou, která je k dispozici v nastavení **výpočetních prostředků a sítě** virtuálního počítače.
    - Pokud podsíť se stejným názvem není k dispozici ve virtuální síti Azure používané pro převzetí služeb při selhání testu, pak testovací virtuální počítač se vytvoří v první podsíti abecedně.
    - Pokud stejná adresa IP není v podsíti k dispozici, virtuální ho důchek obdrží další dostupnou ADRESU IP v podsíti. [Další informace](#create-a-network-for-test-failover).
4. Pokud přejíždíte azure a šifrování dat je povoleno, vyberte v **šifrovacím klíči**certifikát, který byl vydán, když jste povolili šifrování během instalace zprostředkovatele. Tento krok můžete ignorovat, pokud šifrování není povoleno.
5. Sledujte průběh převzetí služeb při selhání na kartě **Úlohy.** Měli byste být schopni zobrazit testovací repliky počítače na webu Azure Portal.
6. Chcete-li zahájit připojení RDP k virtuálnímu počítači Azure, je třeba [přidat veřejnou IP adresu](https://aka.ms/addpublicip) v síťovém rozhraní virtuálního počítače, který selhal.
7. Pokud vše funguje podle očekávání, klepněte na tlačítko **Vyčištění test převzetí služeb při selhání**. Tím se odstraní virtuální chod, které byly vytvořeny během převzetí služeb při selhání testu.
8. V **poznámkách**zaznamenejte a uložte všechna pozorování spojená s převzetím služeb při selhání testu.


![Testovací převzetí služeb při selhání](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

Při spuštění testu převzetí služeb při selhání dojde k následující:

1. **Požadavky**: Kontrola požadavků se spustí, aby bylo zajištěno, že jsou splněny všechny podmínky požadované pro převzetí služeb při selhání.
2. **Převzetí služeb při selhání**: Převzetí služeb při selhání zpracovává a připravil data tak, aby virtuální počítač Azure lze vytvořit z něj.
3. **Nejnovější**: Pokud jste zvolili nejnovější bod obnovení, je vytvořen bod obnovení z dat, která byla odeslána do služby.
4. **Start**: Tento krok vytvoří virtuální počítač Azure pomocí dat zpracovávaných v předchozím kroku.

### <a name="failover-timing"></a>Časování převzetí služeb při selhání

V následujících scénářích vyžaduje převzetí služeb při selhání další mezikrok, který obvykle trvá přibližně 8 až 10 minut:

* Virtuální počítači VMware s verzí služby Mobility starší než 9,8
* Fyzické servery
* Virtuální počítače VMware linux
* Virtuální počítač Hyper-V chráněný jako fyzické servery
* Virtuální vak VMware, kde následující ovladače nejsou spouštěcí ovladače:
    * storvsc
    * vmbus
    * storflt řekl:
    * srozumitelný
    * Atapi
* Virtuální vod vm ware, který nemá dhcp povolenou , bez ohledu na to, zda používá DHCP nebo statické IP adresy.

Ve všech ostatních případech není vyžadován žádný mezikrok a převzetí služeb při selhání trvá podstatně méně času.


## <a name="create-a-network-for-test-failover"></a>Vytvoření sítě pro testovací převzetí služeb při selhání

Pro testovací převzetí služeb při selhání doporučujeme zvolit síť, která je izolovaná od produkční sítě lokality pro obnovení zadané v nastavení **Výpočty a síť** jednotlivých virtuálních počítačů. Když vytvoříte virtuální síť Azure, ve výchozím nastavení je izolovaná od ostatních sítí. Testovací síť by měla napodobovat produkční síť:

- Testovací síť by měla mít stejný počet podsítí jako produkční síť. Podsítě by měly mít stejné názvy.
- Testovací síť by měla používat stejný rozsah IP adres.
- Aktualizujte DNS testovací sítě s použitím IP adresy zadané pro virtuální počítač DNS v nastavení **Výpočty a síť**. Další podrobnosti najdete v tématu věnovaném [aspektům, které je třeba zvážit při testování převzetí služeb při selhání pro Active Directory](site-recovery-active-directory.md#test-failover-considerations).


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Testování převzetí služeb při selhání v produkční síti v lokalitě pro obnovení

Přestože jsme doporučili použít testovací síť oddělenou od produkční sítě, pokud chcete otestovat postup pro zotavení po havárii do produkční sítě, poznamenejte si následující:

- Ujistěte se, že primární virtuální hod se vypne při spuštění testu převzetí služeb při selhání. V opačném případě budou dva virtuální aplikace se stejnou identitou, spuštěné ve stejné síti ve stejnou dobu. To může vést k neočekávaným důsledkům.
- Všechny změny virtuálních disponecí vytvořené pro převzetí služeb při selhání testu budou ztraceny při vyčištění převzetí služeb při selhání. Tyto změny nejsou replikovány zpět do primárního virtuálního virtuálního ms.
- Testování ve vašem produkčním prostředí vede k prostojům vaší produkční aplikace. Uživatelé by neměli používat aplikace spuštěné na virtuálních počítačích, když probíhá zkušební převzetí služeb při selhání.  



## <a name="prepare-active-directory-and-dns"></a>Příprava služby Active Directory a služby DNS

Chcete-li spustit zkušební převzetí služeb při selhání pro testování aplikací, potřebujete kopii produkčního prostředí služby Active Directory v testovacím prostředí. Další informace najdete [v informacích, na](site-recovery-active-directory.md#test-failover-considerations) které se dozvíte další informace.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Pokud se chcete připojit k virtuálním počítačům Azure pomocí RDP/SSH po převzetí služeb při selhání, postupujte podle požadavků shrnutých v tabulce.

**Zabezpečení před selháním** | **Umístění** | **Akce**
--- | --- | ---
**Virtuální počítač Azure s Windows** | Místní počítač před převzetím služeb při selhání | Chcete-li získat přístup k virtuálnímu počítači Azure přes internet, povolte protokol RDP a ujistěte se, že jsou pro **veřejné**přidána pravidla Protokolu TCP a UDP a že protokol RDP je povolen pro všechny profily v**aplikacích povolených pro** **bránu** > Windows Firewall .<br/><br/> Přístup k virtuálnímu počítači Azure přes připojení mezi lokalitami povolte v počítači zásady rdp a ujistěte se, že je rdp povolen v**aplikacích a funkcích povolené bránou** **Windows Firewall** -> pro **domény a privátní** sítě.<br/><br/>  Ujistěte se, že zásady sítě SAN operačního systému jsou nastaveny na **onlineall**. [Další informace](https://support.microsoft.com/kb/3031135).<br/><br/> Ujistěte se, že neexistují žádné aktualizace systému Windows čekající na virtuální ms při aktivaci převzetí služeb při selhání. Služba Windows Update se může spustit při převzetí služeb při selhání a dokud nebude aktualizace dokončena, nebudete se moci k virtuálnímu počítači přihlásit.
**Virtuální počítač Azure s Windows** | Virtuální počítač Azure po převzetí služeb při selhání |  [Přidejte veřejnou IP adresu](https://aka.ms/addpublicip) pro tento virtuální počítač.<br/><br/> Pravidla skupiny zabezpečení sítě pro převzetí počítače s převzetím počítače převzetím služby (a podsíť Azure, ke které je připojen) musí povolit příchozí připojení k portu RDP.<br/><br/> Zkontrolujte **diagnostiku spuštění** a ověřte snímek obrazovky virtuálního aplikace.<br/><br/> Pokud se nemůžete připojit, zkontrolujte, jestli je spuštěný virtuální hod, a projděte si tyto [tipy pro řešení potíží](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Virtuální počítač Azure s Linuxem** | Místní počítač před převzetím služeb při selhání | Ujistěte se, že služba Secure Shell na virtuálním počítači je nastavena na automatické spuštění při spuštění systému.<br/><br/> Zkontrolujte, jestli pravidla brány firewall umožňují službě SSH připojit se k ní.
**Virtuální počítač Azure s Linuxem** | Virtuální počítač Azure po převzetí služeb při selhání | Pravidla skupiny zabezpečení sítě pro převzetí počítače s připojením na vědomí (a podsíť Azure, ke které je připojen) musí povolit příchozí připojení k portu SSH.<br/><br/> [Přidejte veřejnou IP adresu](https://aka.ms/addpublicip) pro tento virtuální počítač.<br/><br/> Zkontrolujte **diagnostiku spuštění** pro snímek virtuálního aplikace.<br/><br/>

Při řešení problémů s připojením po převzetí služeb při selhání použijte [zde](site-recovery-failover-to-azure-troubleshoot.md) popsaný postup.

## <a name="next-steps"></a>Další kroky
Po dokončení cvičení pro zotavení po havárii se dozvíte další informace o dalších typech [převzetí služeb při selhání](site-recovery-failover.md).
