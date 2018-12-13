---
title: Nastavení zotavení po havárii pro vícevrstvou Citrix XenDesktop XenApp nasazení a používání služby Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak nastavit zotavení po havárii fo Citrix XenDesktop a XenApp nasazení s využitím Azure Site Recovery.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 68f12bb7335da0a996aeadd752f59db0aa360a8e
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310507"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-citrix-xenapp-and-xendesktop-deployment"></a>nastavení zotavení po havárii pro vícevrstvou nasazení Citrix XenApp a XenDesktop



Citrix XenDesktop je virtualizace plochy u řešení, která poskytuje plochy a aplikace jako služby ondemand s žádným uživatelem, kdekoli. S technologií doručování FlexCast XenDesktop můžete rychle a bezpečně doručovat aplikacím a plochám uživatelům.
V současné době Citrix XenApp neposkytuje každé havárie funkcím pro obnovení.

Řešení pro zotavení po havárii dobré, by měl Povolit modelování plány zotavení po výše uvedené komplexních aplikačních architektur a také mít možnost přidávat vlastní postup zpracování aplikace mapování mezi různými úrovněmi proto poskytuje jedním kliknutím Zkontrolujte snímek řešení v případě havárie, což vede k nižší RTO.

Tento dokument obsahuje podrobné pokyny k vytváření řešení pro zotavení po havárii pro místní nasazení Citrix XenApp v Hyper-V a VMware vSphere platformy. Tento dokument taky popisuje, jak provést testovací převzetí služeb při selhání (zotavení po havárii) a neplánované převzetí služeb při selhání do Azure pomocí plánů obnovení, podporované konfigurace a požadavky.


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že rozumíte následující:

1. [Replikace virtuálního počítače do Azure](site-recovery-vmware-to-azure.md)
1. Jak [návrh sítě pro zotavení](site-recovery-network-design.md)
1. [Provádění testovací převzetí služeb při selhání do Azure](site-recovery-test-failover-to-azure.md)
1. [Provádění převzetí služeb při selhání do Azure](site-recovery-failover.md)
1. Jak [replikace řadiče domény](site-recovery-active-directory.md)
1. Jak [replikace SQL serveru](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Modely nasazení

Citrix XenApp a XenDesktop farmy mají obvykle vzor následující nasazení:

**Nasazení modelu**

Nasazení Citrix XenApp a XenDesktop serveru AD DNS, SQL serveru, Citrix Delivery Controlleru, prodejních míst serveru, XenApp Masteru (VDA), Citrix XenApp License serveru databáze

![Model nasazení 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Podpora Site Recovery

Pro účely tohoto článku Spravovat nasazení Citrix na virtuálních počítačích VMware vSphere 6.0 / System Center VMM 2012 R2 jste použili k nastavení zotavení po Havárii.

### <a name="source-and-target"></a>Zdroj a cíl

**Scénář** | **Do sekundární lokality** | **Do Azure**
--- | --- | ---
**Hyper-V** | Není v oboru | Ano
**VMware** | Není v oboru | Ano
**Fyzický server** | Není v oboru | Ano

### <a name="versions"></a>Verze
Zákazníci mohou nasadit komponenty XenApp jako virtuální počítače spuštěné na Hyper-V nebo VMware nebo fyzických serverů. Azure Site Recovery může chránit fyzických i virtuálních nasazení do Azure.
Protože XenApp 7,7 nebo vyšší je podporovaná v Azure, nasazení jenom s těmito verzemi mohou být přebrány do Azure pro zotavení po havárii nebo migrace.

### <a name="things-to-keep-in-mind"></a>Co je potřeba mít na paměti

1. Ochrana a obnovení místních nasazení operačního systému serveru pomocí počítačů k zajištění XenApp publikované aplikace a XenApp publikované stolní počítače podporována.

2. Ochrana a obnovení místních nasazení doručování infrastruktury virtuálních klientských počítačů Desktop pro klienta virtuálních klientů, včetně Windows 10 s využitím stolní počítače s operačním systémem se nepodporuje. Je to proto Site Recovery nepodporuje obnovení počítačů v desktopu OS'es.  Navíc některé virtuální plochy operační systémy (např.) Windows 7) se zatím nepodporují pro licencování v Azure. [Další informace](https://azure.microsoft.com/pricing/licensing-faq/) týkající se licencování pro plochy klienta nebo serveru v Azure

3.  Azure Site Recovery nejde replikovat a chránit existující místní relace s více Připojeními nebo systémy současné hodnoty duplicity.
Budete muset znovu vytvořit tyto klony pomocí Azure RM zřizování z kontroler doručování.

4. NetScaler nejde chránit pomocí Azure Site Recovery NetScaler vychází FreeBSD a Azure Site Recovery nepodporuje ochranu FreeBSD operačního systému. Je třeba k nasazení a konfiguraci nových zařízení NetScaler v Azure Marketplace po převzetí služeb při selhání do Azure.


## <a name="replicating-virtual-machines"></a>Replikace virtuálních počítačů

Následující součásti nasazení Citrix XenApp mají být chráněny povolit replikaci a obnovení.

* Ochrana serveru AD DNS
* Ochrana serveru SQL database
* Ochrana Citrix Delivery Controlleru
* Ochrana serveru StoreFront.
* Ochrana hlavní XenApp (VDA)
* Ochrana Citrix XenApp License serveru


**Replikace na serveru AD DNS**

Najdete [ochrana služby Active Directory a DNS pomocí Azure Site Recovery](site-recovery-active-directory.md) na pokyny, které se replikují a konfiguraci řadiče domény v Azure.

**Replikace databáze serveru SQL**

Najdete [Ochrana systému SQL Server pomocí zotavení po havárii pro SQL Server a Azure Site Recovery](site-recovery-sql.md) podrobné technické informace o doporučené možnosti pro ochranu serverů SQL.

Postupujte podle [návod](site-recovery-vmware-to-azure.md) ke spuštění replikace jiných součástí virtuálních počítačů do Azure.

![Ochrana XenApp komponent](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Výpočetní prostředky a nastavení sítě**

Jakmile počítače chráněné (zobrazí stav "Protected" v části replikované položky), je potřeba nakonfigurovat nastavení výpočty a síť.
V výpočty a síť > výpočetní vlastnosti, můžete zadat název a cílovou velikost virtuálního počítače Azure.
Název pro dosažení souladu s požadavky služby Azure, pokud je potřeba upravte. Můžete také zobrazit a přidat informace o cílové síti, podsíti a IP adresu, která bude přiřazena virtuálnímu počítači Azure.

Je třeba počítat s následujícím:

* Můžete nastavit cílovou IP adresu. Pokud adresu nezadáte, bude počítač, který převezme služby při selhání, používat DHCP. Pokud nastavíte adresu, která není k dispozici na převzetí služeb při selhání, převzetí služeb při selhání nebude fungovat. Stejnou cílovou IP adresu je možné použít pro testovací převzetí služeb při selhání, pokud je adresa k dispozici v testovací síti převzetí služeb při selhání.

* Pro server AD/DNS zachování adresy místní umožňuje zadejte stejnou adresu jako server DNS pro Azure Virtual network.

Počet síťových adaptérů závisí na velikosti, kterou zadáte pro cílový virtuální počítač, a to následujícím způsobem:

*   Pokud je počet síťových adaptérů na zdrojovém počítači menší nebo roven počtu adaptérů, které jsou povolené pro velikost cílového počítače, pak bude mít cíl stejný počet adaptérů jako zdroj.
*   Pokud počet adaptérů pro zdrojový virtuální počítač překračuje počet povolený pro cílovou velikost, použije se maximální velikost cíle.
* Pokud má například zdrojový počítač dva síťové adaptéry a velikost cílového počítače podporuje čtyři, bude mít cílový počítač dva adaptéry. Pokud má zdrojový počítač dva adaptéry, ale podporovaná velikost cíle podporuje pouze jeden, bude mít cílový počítač pouze jeden adaptér.
*   Pokud má virtuální počítač více síťových adaptérů, připojí se všechny ke stejné síti.
*   Pokud má virtuální počítač více síťových adaptérů, v seznamu zobrazí první z nich stane výchozí síťový adaptér ve virtuálním počítači Azure.


## <a name="creating-a-recovery-plan"></a>Vytváří se plán obnovení

Po povolení replikace pro virtuální počítače součástí XenApp, dalším krokem je vytvoření plánu obnovení.
Obnovení plánování skupin společně virtuálních počítačů podobné požadavkům pro převzetí služeb při selhání a obnovení.  

**Postup vytvoření plánu obnovení**

1. Přidejte virtuální počítače součástí XenApp v plánu obnovení.
2. Klikněte na možnost plány obnovení -> + plánu obnovení. Zadejte název intuitivní pro plán obnovení.
3. U virtuálních počítačů VMware: Vyberte zdroj jako procesový server VMware, cíl jako Microsoft Azure a modelu nasazení Resource Manager a klikněte na možnost vybrat položky.
4. Pro virtuální počítače Hyper-V: Vyberte jako server VMM, cíl jako Microsoft Azure a modelu nasazení Resource Manager jako zdroj a klikněte na možnost vybrat položky a potom vyberte XenApp nasazení virtuálních počítačů.

### <a name="adding-virtual-machines-to-failover-groups"></a>Přidávání virtuálních počítačů do skupiny převzetí služeb při selhání

Plány obnovení je možné přizpůsobit a přidejte skupiny převzetí služeb při selhání pro konkrétní spuštění pořadí, skripty a ručně prováděné akce. Tyto skupiny je potřeba přidat do plánu obnovení.

1. Group1 převzetí služeb při selhání: AD DNS
2. Skupina2 převzetí služeb při selhání: Virtuální počítače s SQL Serverem
2. Skupina3 převzetí služeb při selhání: VDA hlavního Image virtuálního počítače
3. Skupina převzetí služeb při selhání 4: Kontroler doručování a virtuální počítače z prodejních míst serveru


### <a name="adding-scripts-to-the-recovery-plan"></a>Přidávání skriptů do plánu obnovení

Skripty můžete spustit před nebo po konkrétní skupinu v plánu obnovení. Ručně prováděné akce můžete také zahrnout a prováděné během převzetí služeb při selhání.

Plán vlastní obnovení bude vypadat jako následující:

1. Group1 převzetí služeb při selhání: AD DNS
2. Skupina2 převzetí služeb při selhání: Virtuální počítače s SQL Serverem
3. Skupina3 převzetí služeb při selhání: VDA hlavního Image virtuálního počítače

   >[!NOTE]     
   >Kroky 4, 6 a 7 obsahující ruční nebo skript akce se vztahují na pouze XenApp v místním > prostředí s MCS nebo systémy současné hodnoty katalogů.

4. Ruční nebo skript akce skupiny 3: Vypnutí hlavní VDA virtuálního počítače.
Virtuální počítač k VDA hlavní při převzetí služeb při selhání do Azure budou ve spuštěném stavu. K vytvoření nové relace s více Připojeními katalogy používající hostování Azure, je hlavním virtuálním počítači VDA musí být v zastaveném (de přidělené) stavu. Vypnutí virtuálního počítače z webu Azure portal.

5. Skupina převzetí služeb při selhání 4: Kontroler doručování a virtuální počítače z prodejních míst serveru
6. Skupina3 ruční nebo skript akce 1:

    ***Přidat připojení hostitele Azure RM***

    V počítači Kontroleru doručování zřídit nové relace s více Připojeními katalogy v Azure vytvořte připojení hostitele Azure. Postupujte podle pokynů, jak je popsáno v tomto [článku](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/).

7. Skupina3 ruční nebo skript akce 2:

    ***Znovu vytvořit relace s více Připojeními katalogy v Azure***

    Existující relace s více Připojeními nebo systémy současné hodnoty duplicity v primární lokalitě, nebude možné replikovat do Azure. Budete muset znovu vytvořit tyto klony pomocí replikovaná hlavní VDA a zřizování z kontroler doručování Azure. Postupujte podle pokynů, jak je popsáno v tomto [článku](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) k vytvoření relace s více Připojeními katalogy v Azure.

![Plán obnovení pro součásti XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >Můžete použít skripty na [umístění](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts) aktualizace DNS pomocí nové IP adresy tohoto přes > virtuální počítače nebo se připojit nástroj pro vyrovnávání zatížení se přes virtuální počítač, v případě potřeby.


## <a name="doing-a-test-failover"></a>Provádění testovací převzetí služeb při selhání

Postupujte podle [návod](site-recovery-test-failover-to-azure.md) provést testovací převzetí služeb.

![Plán obnovení](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Převzetím služeb

Postupujte podle [návod](site-recovery-failover.md) při provádění převzetí služeb při selhání.

## <a name="next-steps"></a>Další postup

Je možné [Další](https://aka.ms/citrix-xenapp-xendesktop-with-asr) o replikaci nasazení Citrix XenApp a XenDesktop v tomto dokumentu white paper. Podívejte se na pokyny k [replikovat jiné aplikace](site-recovery-workload.md) pomocí služby Site Recovery.
