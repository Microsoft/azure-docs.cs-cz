---
title: Spuštění postupu zotavení po havárii do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Další informace o spuštění postupu zotavení po havárii z místního do Azure pomocí služby Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: raynew
ms.openlocfilehash: 6eb1ee90b22b9e37dcae900cd80f80cb549090e9
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50213946"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Spuštění postupu zotavení po havárii do Azure 


Tento článek popisuje, jak spuštění postupu zotavení po havárii do Azure pomocí Site Recovery testovací převzetí služeb.  

Můžete spustit testovací převzetí služeb k ověření replikace a strategii zotavení po havárii, bez ztráty dat nebo výpadek. Testovací převzetí služeb při selhání nebude mít vliv na probíhající replikaci, nebo v provozním prostředí. Testovací převzetí služeb můžete spouštět na konkrétní virtuální počítač (VM) nebo [plánu obnovení](site-recovery-create-recovery-plans.md) obsahující více virtuálních počítačů.


## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání
Tento postup popisuje, jak spustit testovací převzetí služeb při selhání pro plán obnovení. Pokud chcete spustit testovací převzetí služeb jednoho virtuálního počítače, postupujte podle kroků popsaných [zde](tutorial-dr-drill-azure.md#run-a-test-failover-for-a-single-vm)

![Testovací převzetí služeb při selhání](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. Ve službě Site Recovery na webu Azure Portal, klikněte na tlačítko **plány obnovení** > *recoveryplan_name* > **testovací převzetí služeb při selhání**.
2. Vyberte **bod obnovení** do kterého chcete převzetí služeb při selhání. Můžete použít jednu z následujících možností:
    - **Nejnovější zpracovaný**: Tato možnost převezme služby při selhání všech virtuálních počítačů v plánu do nejnovějšího bodu obnovení zpracovanému službou Site Recovery. Pokud chcete zobrazit nejnovější obnovení bodu pro konkrétní virtuální počítač, zkontrolujte **nejnovější body obnovení** v nastavení virtuálního počítače. Tato možnost poskytuje nízkou plánovanou dobu obnovení (RTO), protože se neztrácí žádný čas zpracováním nezpracovaných dat.
    - **Nejnovější konzistentní vzhledem k**: Tato možnost převezme služby při selhání všech virtuálních počítačů v plánu bodu nejnovější konzistentní obnovení zpracovanému službou Site Recovery. Pokud chcete zobrazit nejnovější obnovení bodu pro konkrétní virtuální počítač, zkontrolujte **nejnovější body obnovení** v nastavení virtuálního počítače.
    - **Nejnovější**: Tato možnost nejprve zpracuje všechna data, ke které byl odeslán do služby Site Recovery, chcete-li vytvořit bod obnovení pro každý virtuální počítač před přebírání služeb při selhání se. Tato možnost poskytuje nejnižší cíl bodu obnovení bodu obnovení (rpo), protože virtuální počítač vytvořen po převzetí služeb při selhání bude mít všechna data do Site Recovery replikovala při aktivaci převzetí služeb při selhání.
    - **Nejnovější více virtuálních počítačů zpracovat**: Tato možnost je dostupná pro plány obnovení s jeden nebo více virtuálních počítačů, které mají povolenou konzistencí pro víc virtuálních počítačů. Virtuální počítače s povoleným nastavením převzetí služeb při selhání na nejnovější společný bod obnovení konzistentní vzhledem k několika virtuálním počítačům. Ostatní virtuální počítače převzetí služeb při selhání do nejnovějšího bodu obnovení zpracované.  
    - **Nejnovější více virtuálních počítačů konzistentní**: Tato možnost je dostupná pro plány obnovení s jeden nebo více virtuálních počítačů, které mají povolenou konzistencí pro víc virtuálních počítačů. Virtuální počítače, které jsou součástí replikační skupiny převzetí služeb při selhání na nejnovější společný bod obnovení konzistentní vzhledem k aplikaci pro více virtuálních počítačů. Ostatní virtuální počítače převzetí služeb při selhání na jejich nejnovější bod obnovení konzistentní s aplikací.
    - **Vlastní**: tuto možnost použijte k převzetí služeb při selhání konkrétní virtuální počítač, který konkrétnímu bodu obnovení.
3. Výběr služby Azure virtual network, ve kterém se vytvoří testovací virtuální počítače.

    - Site Recovery pokusy o vytvoření testovací virtuální počítače v podsíti se stejným názvem a stejné IP adresy, který je součástí **výpočty a síť** nastavení virtuálního počítače.
    - Pokud podsíť se stejným názvem není k dispozici ve službě Azure virtual network pro testovací převzetí služeb při selhání, pak test vytvoření virtuálního počítače v první podsíť podle abecedy.
    - Pokud stejnou IP adresu není k dispozici v podsíti, virtuálnímu počítači obdrží jinou dostupnou IP adresu v podsíti. [Další informace](#create-a-network-for-test-failover).
4. Pokud jste už převzetí služeb při selhání do Azure a je povolené šifrování dat, v **šifrovací klíč**, vyberte certifikát, který byl vydán po povolení šifrování během instalace zprostředkovatele. Můžete tento krok ignorovat šifrování není povoleno.
5. Sledovat průběh převzetí služeb při selhání **úlohy** kartu. Byste měli vidět testovací počítač repliky na portálu Azure portal.
6. K zahájení připojení RDP k virtuálnímu počítači Azure, budete muset [přidejte veřejnou IP adresu](https://aka.ms/addpublicip) na rozhraní sítě převzetí virtuálního počítače.
7. Pokud všechno funguje podle očekávání, klikněte na tlačítko **vyčištění testovacího převzetí služeb při selhání**. Tím se odstraní virtuální počítače, které byly vytvořené během testovacího převzetí služeb při selhání.
8. V části **Poznámky** si zaznamenejte a uložte jakékoli připomínky související s testovacím převzetím služeb při selhání.


![Testovací převzetí služeb při selhání](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

Když se aktivuje testovací převzetí služeb, dojde k následujícímu:

1. **Požadavky**: Kontrola spuštění, abyste měli jistotu, že jsou splněny všechny podmínky pro převzetí služeb při selhání požadavků.
2. **Převzetí služeb při selhání**: zpracovává převzetí služeb při selhání a přípravy dat, aby virtuální počítač Azure je vytvořit z něj.
3. **Nejnovější**: Pokud jste vybrali nejnovější bod obnovení, se vytvoří bod obnovení z dat, který se poslal na službu.
4. **Spustit**: Tento krok vytvoří virtuální počítač Azure pomocí dat zpracovaných v předchozím kroku.

### <a name="failover-timing"></a>Časování převzetí služeb při selhání

V následujících scénářích vyžaduje převzetí služeb při selhání velmi přechodný krok, který obvykle trvá přibližně 8 až 10 minut na dokončení:

* Virtuální počítače VMware s verzí služby Mobility starší než 9.8
* Fyzické servery
* Virtuální počítače VMware s Linuxem
* Virtuální počítač Hyper-V ochranu, protože fyzické servery
* Virtuální počítač VMware, kde nejsou následující ovladače ovladače spuštění:
    * storvsc
    * VMBus
    * storflt
    * Intelide
    * ATAPI
* Virtuální počítač VMware, které nemají DHCP je povolena, určuje, zda jsou použití bez ohledu DHCP nebo statické IP adresy.

Ve všech ostatních případech žádný přechodný krok není povinný a převzetí služeb při selhání trvat podstatně kratší dobu.


## <a name="create-a-network-for-test-failover"></a>Vytvoření sítě pro testovací převzetí služeb při selhání

Pro testovací převzetí služeb při selhání doporučujeme zvolit síť, která je izolovaná od produkční sítě lokality pro obnovení zadané v nastavení **Výpočty a síť** jednotlivých virtuálních počítačů. Když vytvoříte virtuální síť Azure, ve výchozím nastavení je izolovaná od ostatních sítí. Testovací síť by měla napodobovat produkční síť:

- Testovací síť by měla mít stejný počet podsítí jako produkční síť. Podsítě by měly mít stejné názvy.
- Testovací síť by měla používat stejný rozsah IP adres.
- Aktualizujte DNS testovací sítě s použitím IP adresy zadané pro virtuální počítač DNS v nastavení **Výpočty a síť**. Další podrobnosti najdete v tématu věnovaném [aspektům, které je třeba zvážit při testování převzetí služeb při selhání pro Active Directory](site-recovery-active-directory.md#test-failover-considerations).


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Testovací převzetí služeb při selhání do produkční sítě v recovery site

Přestože doporučujeme používat testovací síti oddělená od produkční sítě, pokud chcete testovat zotavení po havárii do produkční sítě, vezměte na vědomí následující:

- Ujistěte se, že primární virtuální počítač je vypnutý při spuštění testu převzetí služeb. Jinak bude mít dva virtuální počítače se stejnou identitou, spuštěná ve stejné síti ve stejnou dobu. To může vést k neočekávaným důsledkům.
- Při čištění převzetí služeb při selhání budou ztraceny všechny změny na virtuální počítače vytvořené pro testovací převzetí služeb při selhání. Tyto změny se nereplikují zpět do primárního virtuálního počítače.
- Testování v produkčním prostředí vede k výpadek aplikace v produkčním prostředí. Uživatelé neměli používat aplikace běžící na virtuálních počítačích, když probíhá převzetí služeb při selhání testu.  



## <a name="prepare-active-directory-and-dns"></a>Příprava služby Active Directory a DNS

Ke spuštění testovací převzetí služeb při selhání pro testování aplikace, budete potřebovat kopii vašeho produkčního prostředí služby Active Directory ve vašem testovacím prostředí. Čtení [testovací převzetí služeb při selhání důležité informace týkající se služby Active Directory](site-recovery-active-directory.md#test-failover-considerations) Další informace.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Pokud chcete připojit k virtuálním počítačům Azure po převzetí služeb při selhání pomocí protokolu RDP nebo SSH, postupujte podle požadavků shrnutých v tabulce.

**Převzetí služeb při selhání** | **Umístění** | **Akce**
--- | --- | ---
**Virtuální počítač Azure s Windows** | V místním počítači před převzetí služeb při selhání | Pro přístup k virtuálnímu počítači Azure přes internet, povolte protokol RDP a ujistěte se, že jsou přidaná pravidla TCP a UDP pro **veřejné**, a že je povolený protokol RDP pro všechny profily ve **brány Windows Firewall**  >  **Povolené aplikace**.<br/><br/> Pro přístup k virtuálnímu počítači Azure přes připojení site-to-site, povolte na počítači protokol RDP a zajistěte, aby byl protokol RDP v **brány Windows Firewall** -> **povolené aplikace a funkce**, pro **Doménovou a privátní** sítě.<br/><br/>  Ujistěte se, že zásada SAN operačního systému nastavená **OnlineAll**. [Další informace](https://support.microsoft.com/kb/3031135).<br/><br/> Ujistěte se, že nejsou žádné čekající aktualizace Windows na virtuálním počítači při aktivaci převzetí služeb při selhání. Windows update může spustit, když se převzetí služeb při selhání, a nebude schopna se přihlásit do virtuálního počítače, dokud se aktualizace nedokončí.
**Virtuální počítač Azure s Windows** | Virtuální počítač Azure po převzetí služeb při selhání |  [Přidejte veřejnou IP adresu](https://aka.ms/addpublicip) pro tento virtuální počítač.<br/><br/> Pravidla skupiny zabezpečení sítě na převzetí virtuálního počítače (a v podsíti Azure, ke kterému je připojený) musí povolovat příchozí připojení k portu RDP.<br/><br/> Zkontrolujte **Diagnostika spouštění** ověření snímek obrazovky virtuálního počítače.<br/><br/> Pokud se nemůžete připojit, zkontrolujte, zda je virtuální počítač spuštěný a najdete v těchto [tipy pro řešení potíží](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Virtuální počítač Azure s Linuxem** | V místním počítači před převzetí služeb při selhání | Ujistěte se, že služba Secure Shell na virtuálním počítači je nastavena na automatické spuštění při spuštění systému.<br/><br/> Zkontrolujte, jestli pravidla brány firewall umožňují službě SSH připojit se k ní.
**Virtuální počítač Azure s Linuxem** | Virtuální počítač Azure po převzetí služeb při selhání | Pravidla skupiny zabezpečení sítě na převzetí virtuálního počítače (a v podsíti Azure, ke kterému je připojený) musí povolovat příchozí připojení k portu SSH.<br/><br/> [Přidejte veřejnou IP adresu](https://aka.ms/addpublicip) pro tento virtuální počítač.<br/><br/> Zkontrolujte **Diagnostika spouštění** pro snímek obrazovky virtuálního počítače.<br/><br/>

Při řešení problémů s připojením po převzetí služeb při selhání použijte [zde](site-recovery-failover-to-azure-troubleshoot.md) popsaný postup.

## <a name="next-steps"></a>Další postup
Po dokončení postupu zotavení po havárii, další informace o dalších typech [převzetí služeb při selhání](site-recovery-failover.md).
