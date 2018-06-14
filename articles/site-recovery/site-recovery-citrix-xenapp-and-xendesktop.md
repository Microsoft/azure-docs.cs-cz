---
title: Replikovat vícevrstvé Citrix XenDesktop a XenApp nasazení pomocí Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje, jak k ochraně a obnovování Citrix XenDesktop a XenApp nasazení pomocí Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: ponatara
manager: abhemraj
editor: ''
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: ponatara
ms.openlocfilehash: b117525a4851dee5366aeda77c8aaefd1fdde375
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
ms.locfileid: "29559702"
---
# <a name="replicate-a-multi-tier-citrix-xenapp-and-xendesktop-deployment-using-azure-site-recovery"></a>Replikovat vícevrstvé Citrix XenApp a XenDesktop nasazení pomocí Azure Site Recovery

## <a name="overview"></a>Přehled

Citrix XenDesktop je řešení virtualizace plochy, které nabízí plochy a aplikace jako služba ondemand s žádným uživatelem, kdekoli. S technologií doručení FlexCast XenDesktop můžete rychle a bezpečně poskytovat aplikacím a plochám pro uživatele.
V současné době Citrix XenApp neposkytuje žádné po havárii možnosti obnovení.

Řešení pro zotavení po havárii funkční, by měl Povolit modelování plány obnovení kolem výše architektury komplexní aplikace a také mít možnost přidat vlastní postup zpracování aplikace mapování mezi různými úrovněmi proto opravdu poskytování jedním kliknutím v případě havárie vedoucí k nižší RTO snímek řešení.

Tento dokument obsahuje podrobné pokyny pro vytváření řešení zotavení po havárii pro místní nasazení Citrix XenApp na Hyper-V a VMware vSphere platformách. Tento dokument také popisuje, jak provést testovací převzetí služeb při selhání (postupu zotavení po havárii) a neplánované převzetí služeb při selhání do Azure pomocí plánů obnovení, podporované konfigurace a požadavky.


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že rozumíte následující:

1. [Replikaci virtuálního počítače do Azure](site-recovery-vmware-to-azure.md)
1. Postup [návrh k síti pro obnovení](site-recovery-network-design.md)
1. [Provádění převzetí služeb při selhání do Azure](site-recovery-test-failover-to-azure.md)
1. [Provádění převzetí služeb při selhání do Azure](site-recovery-failover.md)
1. Postup [replikace řadiče domény](site-recovery-active-directory.md)
1. Postup [replikaci systému SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Vzory pro nasazení

Farmu Citrix XenApp a XenDesktop má obvykle vzoru následující nasazení:

**Vzor nasazení**

Citrix XenApp a XenDesktop nasazením AD DNS serveru a SQL serveru, Citrix doručení řadiči výkladní skříň serveru, XenApp Master (VDA), Citrix XenApp licenční Server databáze

![Vzor nasazení 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Podpora pro obnovení lokality

Pro účely tohoto článku spravovat Citrix nasazení na virtuální počítače VMware vSphere 6.0 nebo System Center VMM 2012 R2 jste použili k nastavení zotavení po Havárii.

### <a name="source-and-target"></a>Zdroj a cíl

Scénář | Sekundární lokality | **Do Azure**
--- | --- | ---
**Hyper-V** | Není v oboru | Ano
**VMware** | Není v oboru | Ano
**Fyzický server** | Není v oboru | Ano

### <a name="versions"></a>Verze
Zákazníci můžou nasazovat XenApp součásti, jako virtuální počítače běžící na Hyper-V nebo VMware nebo fyzických serverů. Azure Site Recovery můžete chránit fyzické i virtuální nasazení do Azure.
Vzhledem k tomu, že XenApp 7,7 nebo novější je podporovaná v Azure, jenom nasazení s těmito verzemi mohou být přebrány do Azure pro zotavení po havárii nebo migrace.

### <a name="things-to-keep-in-mind"></a>Co je potřeba mějte na paměti

1. Ochrana a obnovení místního nasazení pomocí serveru operačního systému počítače, které chcete poskytovat XenApp publikované aplikace a XenApp publikovaná stolní počítače podporována.

2. Ochrana a obnovení místního nasazení pomocí stolních počítačích operačního systému k poskytování klientských počítačů VDI pro klienta virtuálních ploch, včetně Windows 10, není podporována. Toto je, protože automatické obnovení systému nepodporuje obnovení počítače pomocí plochy OS'es.  Navíc některé klientské virtuální plochy operační systémy (např. Windows 7) se ještě nepodporují pro licencování v Azure. [Další informace](https://azure.microsoft.com/pricing/licensing-faq/) týkající se licencování pro plochy klienta nebo serveru v Azure

3.  Azure Site Recovery nelze replikovat a chránit existující místní relace s více Připojeními nebo systémy současné hodnoty klonovat.
Budete muset znovu vytvořit tyto klony pomocí Azure RM zřizování z řadiče doručení.

4. NetScaler nelze chránit pomocí Azure Site Recovery, protože NetScaler je založena na FreeBSD a Azure Site Recovery nepodporuje ochranu FreeBSD operačního systému. Potřebovali byste k nasazení a konfiguraci nové zařízení NetScaler z Azure Marketplace po převzetí služeb při selhání do Azure.


## <a name="replicating-virtual-machines"></a>Replikace virtuálních počítačů

Následující komponenty aplikace Citrix XenApp nasazení musí být chráněny povolit replikaci a obnovení.

* Ochrana serveru AD DNS
* Ochranu databáze serveru SQL
* Ochrana systému Citrix doručení řadiče
* Ochrana serveru výkladní skříň.
* Ochrana XenApp hlavního serveru (VDA)
* Ochrana systému Citrix XenApp licenčního serveru


**Replikace na serveru AD DNS**

Naleznete [chránit Active Directory a DNS s Azure Site Recovery](site-recovery-active-directory.md) na pokyny pro replikaci a konfiguraci řadiče domény v Azure.

**Replikace databáze serveru SQL**

Naleznete [chránit SQL Server s zotavení po havárii serveru SQL a Azure Site Recovery](site-recovery-sql.md) podrobné technické informace o doporučené možnosti pro ochranu serverů SQL.

Postupujte podle [v tomto návodu](site-recovery-vmware-to-azure.md) k zahájení replikace ostatní součásti virtuálních počítačů do Azure.

![Ochranu XenApp součásti](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Nastavení výpočtu a sítě**

Poté, co jsou chráněné na počítače (stav zobrazuje jako "Chráněné" v části replikované položky), nastavení výpočtů a sítě je potřeba nakonfigurovat ji tak.
V výpočty a síť > výpočetní vlastnosti, můžete zadat název a cílovou velikost virtuálního počítače Azure.
Název pro dosažení souladu s požadavky na Azure, pokud potřebujete změňte. Můžete také zobrazit a přidejte informace o Cílová síť, podsíť a IP adresu, která bude přiřazena virtuálnímu počítači Azure.

Je třeba počítat s následujícím:

* Můžete nastavit cílovou IP adresu. Pokud adresu nezadáte, bude počítač, který převezme služby při selhání, používat DHCP. Pokud nastavíte adresu, která není k dispozici na převzetí služeb při selhání, převzetí služeb při selhání nebude fungovat. Stejnou cílovou IP adresu je možné použít pro testovací převzetí služeb při selhání, pokud je adresa k dispozici v testovací síti převzetí služeb při selhání.

* Pro server AD a DNS ponechá adresu místní umožňuje zadejte stejnou adresu jako server DNS pro Azure Virtual network.

Počet síťových adaptérů závisí na velikosti, kterou zadáte pro cílový virtuální počítač, a to následujícím způsobem:

*   Pokud je počet síťových adaptérů na zdrojovém počítači menší nebo roven počtu adaptérů, které jsou povolené pro velikost cílového počítače, pak bude mít cíl stejný počet adaptérů jako zdroj.
*   Pokud počet adaptérů pro zdrojový virtuální počítač překračuje počet povolený pro cílovou velikost, použije se maximální velikost cíle.
* Pokud má například zdrojový počítač dva síťové adaptéry a velikost cílového počítače podporuje čtyři, bude mít cílový počítač dva adaptéry. Pokud má zdrojový počítač dva adaptéry, ale podporovaná velikost cíle podporuje pouze jeden, bude mít cílový počítač pouze jeden adaptér.
*   Pokud má virtuální počítač více síťových adaptérů připojí se všechny ke stejné síti.
*   Pokud virtuální počítač má několik síťových adaptérů, uvedené v seznamu první z nich stane výchozí síťový adaptér ve virtuálním počítači Azure.


## <a name="creating-a-recovery-plan"></a>Vytvoření plánu obnovení

Po povolení replikace pro virtuální počítače XenApp součásti, dalším krokem je vytvoření plánu obnovení.
Obnovení naplánujte skupiny společně virtuálních počítačů s podobné požadavky pro převzetí služeb při selhání a obnovení.  

**Postup vytvoření plánu obnovení**

1. Přidejte XenApp součást virtuální počítače v plánu obnovení.
2. Klikněte na tlačítko plány obnovení -> + plán obnovení. Zadejte název intuitivní pro plán obnovení.
3. U virtuálních počítačů VMware: Zvolit zdroj jako procesní server VMware, cíl jako Microsoft Azure a modelu nasazení Resource Manager a klikněte na možnost vybrat položky.
4. Pro virtuální počítače Hyper-V: Vyberte zdroj jako VMM server, jako Microsoft Azure a modelu nasazení Resource Manager jako cíl a klikněte na možnost vybrat položky a pak vyberte XenApp nasazení virtuálních počítačů.

### <a name="adding-virtual-machines-to-failover-groups"></a>Probíhá přidávání virtuálních počítačů do skupin převzetí služeb při selhání

Přidání skupin převzetí služeb při selhání pro konkrétní spuštění pořadí, skripty a ručně prováděné akce lze přizpůsobit plány obnovení. Následující skupiny je třeba přidat do plánu obnovení.

1. Group1 převzetí služeb při selhání: DNS AD
2. Skupina2 převzetí služeb při selhání: Virtuální počítače serveru SQL
2. Převzetí služeb při selhání skupina3: VDA hlavní bitové kopie virtuálního počítače
3. Skupina 4 převzetí služeb při selhání: Řadič doručování a virtuální počítače výkladní skříň serveru


### <a name="adding-scripts-to-the-recovery-plan"></a>Probíhá přidávání skriptů do plánu obnovení

Skripty, můžete spustit před nebo po určité skupiny v plánu obnovení. Ruční akce může být také být zahrnutý a prováděné během převzetí služeb při selhání.

Plán obnovení přizpůsobené vypadá níže:

1. Group1 převzetí služeb při selhání: DNS AD
2. Skupina2 převzetí služeb při selhání: Virtuální počítače serveru SQL
3. Převzetí služeb při selhání skupina3: VDA hlavní bitové kopie virtuálního počítače

   >[!NOTE]     
   >Kroky 4, 6 a 7 obsahující ruční nebo skript akce platí pro pouze místní XenApp > prostředí s MCS/systémy současné hodnoty katalogů.

4. Ruční nebo skript akce skupina 3: vypnutí hlavní VDA virtuálních počítačů hlavní VDA virtuální počítač při převzetí služeb při selhání do Azure bude ve spuštěném stavu. Pokud chcete vytvořit novou relaci s více Připojeními katalogů pomocí Azure ARM hostování, je hlavní VDA virtuální počítač nemusí být v zastaveném (de přidělené) stavu. Vypněte virtuální počítač z portálu Azure.

5. Skupina 4 převzetí služeb při selhání: Řadič doručování a virtuální počítače výkladní skříň serveru
6. Skupina3 ruční nebo skript akce 1:

    ***Přidat připojení hostitele Azure RM***

    Vytvořte připojení hostitele Azure ARM v doručení řadiče počítače zřídit nové relace s více Připojeními katalogů v Azure. Postupujte podle kroků, jak je popsáno v tomto [článku](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/).

7. Skupina3 ruční nebo skript akce 2:

    ***Znovu vytvořit relace s více Připojeními katalogů v Azure***

    Existující relace s více Připojeními nebo systémy současné hodnoty klony v primární lokalitě, nebude možné replikovat do Azure. Budete muset znovu vytvořit tyto klony pomocí replikované hlavní VDA a Azure ARM zřizování z řadiče doručení. Postupujte podle kroků, jak je popsáno v tomto [článku](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) k vytvoření relace s více Připojeními katalogů v Azure.

![Plán obnovení pro XenApp součásti](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >Můžete použít skripty v [umístění](https://github.com/Azure/azure-quickstart-templates/blob/>master/asr-automation-recovery/scripts) k aktualizaci služby DNS s nové IP adresy došlo přes > virtuální počítače nebo pro připojení služby Vyrovnávání zatížení na neúspěšný přes virtuální počítač, v případě potřeby.


## <a name="doing-a-test-failover"></a>Provádění testovací převzetí služeb

Postupujte podle [v tomto návodu](site-recovery-test-failover-to-azure.md) provedete testovací převzetí služeb.

![Plán obnovení](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Převzetím služeb

Postupujte podle [v tomto návodu](site-recovery-failover.md) při dělají převzetí služeb při selhání.

## <a name="next-steps"></a>Další postup

Můžete [Další](https://aka.ms/citrix-xenapp-xendesktop-with-asr) o replikaci Citrix XenApp a XenDesktop nasazení v tomto dokumentu. Podívejte se na pokyny, které [replikovat jiné aplikace](site-recovery-workload.md) pomocí Site Recovery.
