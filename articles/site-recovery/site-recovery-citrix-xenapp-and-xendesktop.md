---
title: Nastavení zotavení po havárii Citrix XenDesktop/XenApp pomocí Azure Site Recovery
description: Tento článek popisuje, jak nastavit zotavení po havárii pro nasazení Citrix XenDesktop a XenApp pomocí Azure Site Recovery.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 90d54a8ded99dd8ab43aed688036add6aede20ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86134843"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-citrix-xenapp-and-xendesktop-deployment"></a>nastavení zotavení po havárii pro multi-vrstvou Citrix XenApp a XenDesktop Deployment



Citrix XenDesktop je virtualizační řešení pro stolní počítače, které doručuje desktopy a aplikace jako službu OnDemand pro každého uživatele, a to kdekoli. Díky technologii doručování FlexCast může XenDesktop rychle a bezpečně doručovat aplikace a stolní počítače uživatelům.
V současné době Citrix XenApp neposkytuje žádné možnosti zotavení po havárii.

Dobrým řešením zotavení po havárii by mělo být umožněno modelování plánů obnovení kolem výše uvedených komplexních architektur aplikace a také mít možnost přidat přizpůsobené kroky ke zpracování mapování aplikací mezi různými úrovněmi a tím zajistit, že se v případě havárie v případě havárie, který vede k menšímu RTO, zadává řešení snímku jediným kliknutím.

Tento dokument poskytuje podrobné pokyny pro vytvoření řešení zotavení po havárii pro místní nasazení Citrix XenApp na platformách Hyper-V a VMware vSphere. Tento dokument také popisuje, jak provést testovací převzetí služeb při selhání (postup zotavení po havárii) a neplánované převzetí služeb při selhání do Azure pomocí plánů obnovení, podporovaných konfigurací a požadavků.


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že rozumíte následujícímu:

1. [Replikace virtuálního počítače do Azure](./vmware-azure-tutorial.md)
1. [Návrh sítě pro obnovení](./concepts-on-premises-to-azure-networking.md)
1. [Test převzetí služeb při selhání do Azure](site-recovery-test-failover-to-azure.md)
1. [Převzetí služeb při selhání do Azure](site-recovery-failover.md)
1. Postup [replikace řadiče domény](site-recovery-active-directory.md)
1. Postup [replikace SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Vzory nasazení

Farma Citrix XenApp a XenDesktop má obvykle následující vzor nasazení:

**Vzor nasazení**

Nasazení Citrix XenApp a XenDesktop se serverem DNS služby AD, serverem SQL Database, řadičem pro doručování Citrix, serverem prezentace, XenApp Master (VDA), serverem licencí Citrix XenApp

![Vzor nasazení 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Podpora Site Recovery

Pro účely tohoto článku se k nastavení zotavení po havárii použila nasazení Citrix na virtuálních počítačích VMware spravovaných přes vSphere 6,0 nebo System Center VMM 2012 R2.

### <a name="source-and-target"></a>Zdroj a cíl

**Scénář** | **Do sekundární lokality** | **Do Azure**
--- | --- | ---
**Hyper-V** | Není v oboru | Yes
**VMware** | Není v oboru | Yes
**Fyzický server** | Není v oboru | Yes

### <a name="versions"></a>Verze
Zákazníci můžou nasazovat komponenty XenApp jako Virtual Machines spuštěné v Hyper-V nebo VMware nebo jako fyzické servery. Azure Site Recovery může chránit jak fyzické, tak i virtuální nasazení v Azure.
Vzhledem k tomu, že v Azure je XenApp 7,7 nebo novější, je možné, že Azure při zotavení po havárii a při zotavení po havárii podporuje jenom nasazení s těmito verzemi.

### <a name="things-to-keep-in-mind"></a>Co je potřeba mít na paměti

1. Ochrana a obnovení místních nasazení pomocí počítačů s operačním systémem serveru pro doručování publikovaných aplikací XenApp a XenApp publikovaných ploch je podporováno.

2. Ochrana a obnovení místních nasazení pomocí počítačů s desktopovým operačním systémem k dodávání plochy VDI pro klientské virtuální plochy, včetně Windows 10, se nepodporuje. Důvodem je to, že Site Recovery nepodporuje obnovení počítačů s desktopovým OS'es.  Také některé operační systémy klientů s virtuálními počítači (např. Systém Windows 7) zatím není podporován pro licencování v Azure. [Další informace](https://azure.microsoft.com/pricing/licensing-faq/) týkající se licencování pro plochy klienta nebo serveru v Azure

3.  Azure Site Recovery nemůže replikovat a chránit existující místní MCS nebo PVS klony.
Tyto klony je potřeba znovu vytvořit pomocí zřizování Azure RM z kontroleru doručení.

4. NetScaler nelze chránit pomocí Azure Site Recovery, protože NetScaler je založen na FreeBSD a Azure Site Recovery nepodporuje ochranu operačního systému FreeBSD. Po převzetí služeb při selhání do Azure budete muset na svém trhu Azure nasadit a nakonfigurovat nové zařízení NetScaler.


## <a name="replicating-virtual-machines"></a>Replikace virtuálních počítačů

Aby bylo možné povolit replikaci a obnovení, je třeba chránit následující komponenty nasazení Citrix XenApp.

* Ochrana serveru DNS služby AD
* Ochrana serveru SQL Database
* Ochrana řadiče pro doručení Citrix
* Ochrana serveru prezentace
* Ochrana hlavního serveru XenApp (VDA)
* Ochrana serveru Citrix XenApp License Server


**Replikace serveru DNS služby AD**

Pokyny pro replikaci a konfiguraci řadiče domény v Azure najdete v tématu věnovaném [ochraně služby Active Directory a DNS pomocí Azure Site Recovery](site-recovery-active-directory.md) .

**Replikace serveru služby SQL Database**

Podrobné technické pokyny týkající se doporučených možností pro ochranu SQL serveru najdete v tématu [ochrana SQL Server v SQL Server zotavení po havárii a Azure Site Recovery](site-recovery-sql.md) .

Postupujte podle [těchto pokynů](./vmware-azure-tutorial.md) a začněte replikovat ostatní virtuální počítače součástí do Azure.

![Ochrana součástí XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Nastavení výpočtů a sítě**

Po ochraně počítačů (stav je "chráněno" v části replikované položky), je nutné nakonfigurovat nastavení výpočtů a sítě.
V výpočetních a síťových > výpočetních vlastnostech můžete zadat název a cílovou velikost virtuálního počítače Azure.
Podle potřeby upravte název tak, aby byl souladu s požadavky Azure. Můžete také zobrazit a přidat informace o cílové síti, podsíti a IP adrese, která bude přiřazena k virtuálnímu počítači Azure.

Všimněte si, že:

* Můžete nastavit cílovou IP adresu. Pokud adresu nezadáte, bude počítač, který převezme služby při selhání, používat DHCP. Pokud nastavíte adresu, která není dostupná při převzetí služeb při selhání, převzetí služeb při selhání nebude fungovat. Stejnou cílovou IP adresu je možné použít pro testovací převzetí služeb při selhání, pokud je adresa k dispozici v testovací síti převzetí služeb při selhání.

* Pro server AD nebo DNS si ponechá místní adresa a umožní vám zadat stejnou adresu jako server DNS pro virtuální síť Azure.

Počet síťových adaptérů závisí na velikosti, kterou zadáte pro cílový virtuální počítač, a to následujícím způsobem:

*   Pokud je počet síťových adaptérů na zdrojovém počítači menší nebo roven počtu adaptérů, které jsou povolené pro velikost cílového počítače, pak bude mít cíl stejný počet adaptérů jako zdroj.
*   Pokud počet adaptérů pro zdrojový virtuální počítač překračuje počet povolený pro cílovou velikost, použije se maximální velikost cíle.
* Pokud má například zdrojový počítač dva síťové adaptéry a velikost cílového počítače podporuje čtyři, bude mít cílový počítač dva adaptéry. Pokud má zdrojový počítač dva adaptéry, ale podporovaná velikost cíle podporuje pouze jeden, bude mít cílový počítač pouze jeden adaptér.
*   Pokud má virtuální počítač několik síťových adaptérů, připojí se ke stejné síti.
*   Pokud má virtuální počítač několik síťových adaptérů, první z nich, který je zobrazený v seznamu, se bude nacházet jako výchozí síťový adaptér na virtuálním počítači Azure.


## <a name="creating-a-recovery-plan"></a>Vytvoření plánu obnovení

Po povolení replikace pro virtuální počítače součásti XenApp je dalším krokem vytvoření plánu obnovení.
Plán obnovení seskupuje virtuální počítače s podobnými požadavky pro převzetí služeb při selhání a obnovení.  

**Postup vytvoření plánu obnovení**

1. Přidejte virtuální počítače součásti XenApp do plánu obnovení.
2. Klikněte na plány obnovení – > a plán obnovení. Zadejte pro plán obnovení intuitivní název.
3. Pro virtuální počítače VMware: Vyberte zdroj jako procesový Server VMware, cíl jako Microsoft Azure a model nasazení jako Správce prostředků a klikněte na vybrat položky.
4. Pro virtuální počítače Hyper-V: Vyberte zdroj jako server VMM, cíl jako Microsoft Azure a model nasazení jako Správce prostředků a klikněte na vybrat položky a pak vyberte virtuální počítače pro nasazení XenApp.

### <a name="adding-virtual-machines-to-failover-groups"></a>Přidávání virtuálních počítačů do skupin s podporou převzetí služeb při selhání

Plány obnovení je možné přizpůsobit a přidat skupiny převzetí služeb při selhání pro konkrétní pořadí spouštění, skripty nebo ruční akce. Do plánu obnovení je třeba přidat následující skupiny.

1. Převzetí služeb při selhání Group1: AD DNS
2. Převzetí služeb při selhání skupina2: SQL Server virtuálních počítačů
2. Převzetí služeb při selhání Skupina3: virtuální počítač s hlavní imagí VDA
3. Převzetí služeb při selhání Group4: řadič pro doručování a virtuální počítače prezentace serveru


### <a name="adding-scripts-to-the-recovery-plan"></a>Přidávání skriptů do plánu obnovení

Skripty můžete spustit před nebo po určité skupině v plánu obnovení. Při převzetí služeb při selhání je také možné zahrnout a provést ruční akce.

Přizpůsobený plán obnovení vypadá následovně:

1. Převzetí služeb při selhání Group1: AD DNS
2. Převzetí služeb při selhání skupina2: SQL Server virtuálních počítačů
3. Převzetí služeb při selhání Skupina3: virtuální počítač s hlavní imagí VDA

   >[!NOTE]     
   >Kroky 4, 6 a 7, které obsahují ruční nebo skriptovací akce, se vztahují jenom na místní XenApp >prostředí s katalogy MCS/PVS.

4. Skupina 3 akce ručního nebo skriptu: vypněte hlavní virtuální počítač VDA.
Hlavní virtuální počítač VDA při převzetí služeb při selhání do Azure bude v běžícím stavu. Pokud chcete vytvořit nové katalogy MCS pomocí hostování Azure, je nutné, aby byl hlavní virtuální počítač VDA ve stavu Zastaveno (přidělení zrušeno). Vypněte virtuální počítač z Azure Portal.

5. Převzetí služeb při selhání Group4: řadič pro doručování a virtuální počítače prezentace serveru
6. Ruční skupina3 nebo akce skriptu 1:

    ***Přidat připojení hostitele Azure RM***

    Vytvořte připojení k hostiteli Azure v počítači se službou Delivery Controller a zřiďte nové katalogy MCS v Azure. Postupujte podle kroků popsaných v tomto [článku](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/).

7. Ruční skupina3 nebo akce skriptu 2:

    ***Opětovné vytvoření katalogů MCS v Azure***

    Existující klony MCS nebo PVS v primární lokalitě nebudou replikovány do Azure. Tyto klony je potřeba znovu vytvořit pomocí replikovaného hlavního VDA a zřizování Azure z kontroleru doručení. Pokud chcete vytvořit katalogy MCS v Azure, postupujte podle kroků popsaných v tomto [článku](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) .

![Plán obnovení pro součásti XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >V případě potřeby můžete pomocí skriptů v [umístění](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts) aktualizovat DNS nové IP adresy virtuálních počítačů, u kterých došlo k pře>vzetí služeb při selhání, nebo pro připojení nástroje pro vyrovnávání zatížení na virtuálním počítači s převzetím služeb při selhání.


## <a name="doing-a-test-failover"></a>Test převzetí služeb při selhání

Při provádění testovacího převzetí služeb při selhání postupujte podle [těchto pokynů](site-recovery-test-failover-to-azure.md) .

![Plán obnovení](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Provedení převzetí služeb při selhání

Když provádíte převzetí služeb při selhání, postupujte podle [těchto pokynů](site-recovery-failover.md) .

## <a name="next-steps"></a>Další kroky

[Další informace](https://aka.ms/citrix-xenapp-xendesktop-with-asr) o replikaci nasazení Citrix XenApp a XenDesktop najdete v tomto dokumentu White Paper. Přečtěte si pokyny pro [replikaci dalších aplikací](site-recovery-workload.md) pomocí Site Recovery.
