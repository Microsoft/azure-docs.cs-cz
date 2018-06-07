---
title: Testovací převzetí služeb při selhání do Azure ve službě Azure Site Recovery | Microsoft Docs
description: Další informace o spouštění testovací převzetí služeb z místního do Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/08/2018
ms.author: raynew
ms.openlocfilehash: bdbeee0e0caaa0e6db7249c2f4aeaa19d5d2ed0d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643647"
---
# <a name="test-failover-to-azure-in-site-recovery"></a>Testovací převzetí služeb při selhání do Azure ve službě Site Recovery


Tento článek popisuje, jak spustit postupu zotavení po havárii na Azure, pomocí Site Recovery testovací převzetí služeb.  

Můžete spustit převzetí služeb při selhání k ověření replikace a strategie zotavení po havárii, aniž by ztrátě dat nebo výpadek. Testovací převzetí služeb nebude mít vliv na probíhající replikace, nebo v provozním prostředí. Můžete spustit testovací převzetí služeb na konkrétní virtuální počítač (VM), nebo na [plán obnovení](site-recovery-create-recovery-plans.md) obsahující více virtuálních počítačů. 


## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání
Tento postup popisuje, jak spouštět testovací převzetí služeb při selhání pro plán obnovení. 

![Testovací převzetí služeb při selhání](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. Ve službě Site Recovery na portálu Azure, klikněte na tlačítko **plány obnovení** > *recoveryplan_name* > **testovací převzetí služeb při selhání**.
2. Vyberte **bod obnovení** ke kterému chcete převzetí služeb při selhání. Můžete použít jednu z následujících možností:
    - **Nejnovější zpracované**: tuto možnost převezme všechny virtuální počítače v plánu do nejnovějšího bodu obnovení, které jsou zpracovávány Site Recovery. Chcete-li zobrazit nejnovější obnovení bodu pro konkrétní virtuální počítač, zkontrolujte **body obnovení nejnovější** v nastavení virtuálního počítače. Tato možnost poskytuje nízkou plánovanou dobu obnovení (RTO), protože se neztrácí žádný čas zpracováním nezpracovaných dat.
    - **Nejnovější aplikace konzistentní**: tuto možnost převezme všechny virtuální počítače v plánu s bodem nejnovější obnovení konzistentních s aplikací zpracovaných službou Site Recovery. Chcete-li zobrazit nejnovější obnovení bodu pro konkrétní virtuální počítač, zkontrolujte **body obnovení nejnovější** v nastavení virtuálního počítače. 
    - **Nejnovější**: tuto možnost napřed zpracuje všechna data, která byl odeslán do služby Site Recovery k vytvoření bodu obnovení pro každý virtuální počítač před převzetí služeb při selhání do ní. Tato možnost poskytuje nejnižší plánovaný bod obnovení (plánovaného bodu obnovení), protože virtuální počítač vytvořené po převzetí služeb při selhání budou mít všechna data replikované Site Recovery, kdy bylo spuštěno převzetí služeb při selhání.
    - **Nejnovější více virtuálních počítačů zpracovat**: Tato možnost je dostupná pro plánů obnovení pomocí jednoho nebo více virtuálních počítačů, které mají povolenou konzistencí pro víc virtuálních počítačů. Virtuální počítače s povoleným nastavením převzetí služeb při selhání nejnovější společný bod obnovení konzistentních s více virtuálních počítačů. Ostatní virtuální počítače převzetí služeb při selhání do nejnovějšího bodu obnovení zpracování.  
    - **Nejnovější více virtuálních počítačů konzistentní**: Tato možnost je dostupná pro plánů obnovení pomocí jednoho nebo více virtuálních počítačů, které mají povolenou konzistencí pro víc virtuálních počítačů. Virtuální počítače, které jsou součástí replikační skupiny převzetí služeb při selhání nejnovější společný bod obnovení konzistentních s aplikací více virtuálních počítačů. Ostatní virtuální počítače převzetí služeb při selhání na jejich nejnovější bod obnovení konzistentních s aplikací. 
    - **Vlastní**: tuto možnost použijte k převzetí služeb konkrétní virtuální počítač do bodu konkrétní obnovení.
3. Vyberte Azure virtuální síť, ve kterém se vytvoří testovací virtuální počítače.

    - Pokusy o obnovení lokality vytvořit testovací virtuální počítače v podsíti se stejným názvem a stejné IP adresy, který je součástí **výpočty a síť** nastavení virtuálního počítače.
    - Pokud se stejným názvem podsíť není k dispozici ve virtuální síti Azure, použít pro testovací převzetí služeb při selhání, pak testovací virtuální počítač je vytvořený na první podsíť abecedně.
    - Pokud stejnou IP adresu není dostupná v podsíti, virtuální počítač přijímá další dostupnou IP adresu v podsíti. [Další informace](#create-a-network-for-test-failover).
4. Pokud jste selhání do Azure a je-li povoleno šifrování dat, v **šifrovací klíč**, vyberte certifikát, který byl vydán, když je povolené šifrování během instalace zprostředkovatele. Můžete tento krok ignorovat šifrování není povoleno.
5. Sledovat průběh převzetí služeb při selhání **úlohy** kartě. Nyní byste měli mít najdete v části testovací počítač repliky na portálu Azure.
6. Chcete-li iniciovat připojení ke vzdálené ploše virtuálního počítače Azure, je potřeba [přidejte veřejnou IP adresu](https://aka.ms/addpublicip) v síťovém rozhraní selhání virtuálního počítače. 
7. Pokud se vše funguje podle očekávání, klikněte na tlačítko **vyčistit testovací převzetí služeb při selhání**. Tím odstraní virtuální počítače, které byly vytvořeny během testovacího převzetí služeb při selhání.
8. V části **Poznámky** si zaznamenejte a uložte jakékoli připomínky související s testovacím převzetím služeb při selhání. 


![Testovací převzetí služeb při selhání](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

Když se aktivuje testovací převzetí služeb, dojde k následujícímu:

1. **Požadavky**: Kontrola spustí, abyste měli jistotu, že jsou splněny všechny podmínky požadované pro převzetí služeb při selhání předpokladů.
2. **Převzetí služeb při selhání**: převzetí služeb při selhání zpracovává a připravit data, aby virtuální počítač Azure můžete vytvořit z něj.
3. **Nejnovější**: Pokud jste vybrali nejnovější bod obnovení, je bod obnovení vytvořený z data, která je byl odeslán do služby.
4. **Spustit**: Tento krok vytvoří virtuální počítač Azure pomocí dat, která zpracuje v předchozím kroku.

### <a name="failover-timing"></a>Převzetí služeb při selhání časování

V následujících scénářích převzetí služeb při selhání vyžaduje velmi přechodný krok, který obvykle trvá přibližně 8 až 10 minut na dokončení:

* Virtuální počítače VMware s verzí starší než 9.8 služby Mobility
* Fyzické servery
* Virtuální počítače s Linuxem VMware
* Virtuální počítač chráněný jako fyzické servery technologie Hyper-V
* Virtuální počítač VMware, kde nejsou spouštěcí ovladače v následující ovladače:
    * storvsc
    * VMBus
    * storflt
    * Intelide
    * ATAPI
* VMware virtuální počítač, který není k dispozici server DHCP, rrespective, jestli jsou použití DHCP nebo statické IP adresy.

Ve všech ostatních případech žádný zprostředkující krok není povinný a převzetí služeb při selhání zabere to výrazně méně času.


## <a name="create-a-network-for-test-failover"></a>Vytvoření sítě pro testovací převzetí služeb při selhání

Je vhodné pro testovací převzetí služeb při selhání, zvolit sítě, která bude izolovaná od obnovení lokality produkční konkrétní v sítě **výpočty a síť** nastavení pro každý virtuální počítač. Ve výchozím nastavení když vytvoříte virtuální síť Azure, je izolovaná od jiných sítích. Testovací síti by měl napodobovat produkční sítě:

- Testovací síť by měla mít stejný počet podsítí jako produkční sítě. Podsítě by měl mít stejné názvy.
- Testovací síti by měl použít stejného rozsahu IP adres.
- Aktualizovat DNS testovací síti s IP adresou, zadaný pro virtuální počítač DNS v **výpočty a síť** nastavení. Čtení [testovací převzetí služeb při selhání důležité informace týkající se služby Active Directory](site-recovery-active-directory.md#test-failover-considerations) další podrobnosti.


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Testovací převzetí služeb při selhání pro produkční síť v lokalitě pro obnovení

Přestože doporučujeme používat testovací síti odděleně od produkční sítě, pokud chcete testovat postupu zotavení po havárii do produkční sítě, vezměte na vědomí následující: 

- Ujistěte se, že primární virtuální počítač je vypnutý při spuštění testu převzetí služeb. V opačném případě budou existovat dva virtuální počítače se stejnou identitou, spuštěná ve stejné síti současně. To může způsobit neočekávané důsledky.
- Veškeré změny pro virtuální počítače vytvořené pro testovací převzetí služeb při selhání budou ztraceny, když vyčištění převzetí služeb při selhání. Tyto změny nejsou replikovat zpět na primární virtuální počítač.
- Testování v provozním prostředí vede k výpadek produkční aplikace. Uživatelé neměli používat aplikace běžící na virtuálních počítačích, když probíhá testu převzetí služeb.  



## <a name="prepare-active-directory-and-dns"></a>Příprava služby Active Directory a DNS

Pokud chcete spustit testovací převzetí služeb pro testování aplikace, musíte kopii produkční prostředí služby Active Directory v testovacím prostředí. Čtení [testovací převzetí služeb při selhání důležité informace týkající se služby Active Directory](site-recovery-active-directory.md#test-failover-considerations) Další informace.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Pokud se chcete připojit k virtuálním počítačům Azure po převzetí služeb při selhání pomocí protokolu RDP, postupujte podle požadavků shrnuto v tabulce.

**Převzetí služeb při selhání** | **Umístění** | **Akce**
--- | --- | ---
**Virtuální počítač Azure s Windows** | Na místním počítači před převzetí služeb při selhání | Přístup k virtuálnímu počítači Azure přes internet, povolení protokolu RDP a ujistěte se, že přidána pravidla TCP a UDP pro **veřejné**, a že protokol RDP je povoleno pro všechny profily v **brány Windows Firewall**  >  **Aplikace s povoleným**.<br/><br/> Přístup k virtuálnímu počítači Azure přes připojení site-to-site, povolte RDP na počítači a zajistit, aby se v protokolu RDP **brány Windows Firewall** -> **povolené aplikace a funkce**, pro **Domény a privátní** sítě.<br/><br/>  Ujistěte se, že operační systém zásad sítě SAN je nastaven **OnlineAll**. [Další informace](https://support.microsoft.com/kb/3031135).<br/><br/> Ujistěte se, že nejsou dostupné žádné aktualizace Windows čekající na vyřízení ve virtuálním počítači při aktivaci převzetí služeb při selhání. Služby Windows update může spustit, když jste převzetí služeb při selhání a nebudete moci přihlásit do virtuálního počítače, až do dokončení aktualizace. 
**Virtuální počítač Azure s Windows** | Virtuální počítač Azure po převzetí služeb při selhání |  [Přidejte veřejnou IP adresu](https://aka.ms/addpublicip) pro tento virtuální počítač.<br/><br/> Pravidla skupiny zabezpečení sítě na selhání virtuálního počítače (a v podsíti Azure, ke kterému je připojený) je potřeba povolit příchozí připojení k portu RDP.<br/><br/> Zkontrolujte **spouštění diagnostiky** ověření snímek virtuálního počítače.<br/><br/> Pokud se nemůžete připojit, zkontrolujte, zda je virtuální počítač spuštěn a zkontrolujte tyto [tipy pro odstraňování potíží](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Virtuální počítač Azure s Linuxem** | Na místním počítači před převzetí služeb při selhání | Zkontrolujte, že služba Secure Shell na virtuálním počítači je nastavená na automatické spuštění při spuštění systému.<br/><br/> Zkontrolujte, jestli pravidla brány firewall umožňují službě SSH připojit se k ní.
**Virtuální počítač Azure s Linuxem** | Virtuální počítač Azure po převzetí služeb při selhání | Pravidla skupiny zabezpečení sítě na selhání virtuálního počítače (a v podsíti Azure, ke kterému je připojený) je potřeba povolit příchozí připojení na portu SSH.<br/><br/> [Přidejte veřejnou IP adresu](https://aka.ms/addpublicip) pro tento virtuální počítač.<br/><br/> Zkontrolujte **spouštění diagnostiky** pro snímek virtuálního počítače.<br/><br/>



## <a name="next-steps"></a>Další postup
Po dokončení postupu zotavení po havárii, další informace o dalších typů [převzetí služeb při selhání](site-recovery-failover.md).
