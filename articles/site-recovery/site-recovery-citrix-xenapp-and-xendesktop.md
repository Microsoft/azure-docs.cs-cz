---
title: Nastavení zotavení po havárii Citrix XenDesktop/XenApp pomocí azure site recovery
description: Tento článek popisuje, jak nastavit zotavení po havárii pro nasazení Citrix XenDesktop a XenApp pomocí Azure Site Recovery.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 29fbe5389da924a2ecc660aa5ce5c4bb0a0902b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74084542"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-citrix-xenapp-and-xendesktop-deployment"></a>nastavení zotavení po havárii pro víceúrovňové nasazení Citrix XenApp a XenDesktop



Citrix XenDesktop je řešení virtualizace stolních počítačů, které poskytuje stolní počítače a aplikace jako službu na vyžádání pro každého uživatele a kdekoli. Díky technologii doručování FlexCast může XenDesktop rychle a bezpečně dodávat aplikace a stolní počítače uživatelům.
Společnost Citrix XenApp dnes neposkytuje žádné možnosti zotavení po havárii.

Dobré řešení zotavení po havárii by mělo umožnit modelování plánů obnovení kolem výše uvedených složitých architektur aplikací a také mít možnost přidat přizpůsobené kroky pro zpracování mapování aplikací mezi různými vrstvami, a proto poskytuje jedno kliknutí v případě katastrofy vedoucí k nižšímu rto.

Tento dokument obsahuje podrobné pokyny pro vytváření řešení pro zotavení po havárii pro vaše místní nasazení Citrix XenApp na platformách Hyper-V a VMware vSphere. Tento dokument také popisuje, jak provést zkušební převzetí služeb při selhání (cvičení zotavení po havárii) a neplánované převzetí služeb při selhání do Azure pomocí plánů obnovení, podporovaných konfigurací a předpokladů.


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že rozumíte následujícím uvažnostem:

1. [Replikace virtuálního počítače do Azure](site-recovery-vmware-to-azure.md)
1. Jak [navrhnout síť pro obnovení](site-recovery-network-design.md)
1. [Provedení testovacího převzetí služeb při selhání v Azure](site-recovery-test-failover-to-azure.md)
1. [Převzetí služeb při selhání v Azure](site-recovery-failover.md)
1. [Replikace řadiče domény](site-recovery-active-directory.md)
1. Replikace [serveru SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Vzory nasazení

Farma Citrix XenApp a XenDesktop mají obvykle následující způsob nasazení:

**Vzor nasazení**

Nasazení Citrix XenApp a XenDesktop se serverem AD DNS, databázovým serverem SQL, řadičem pro doručování Citrix, serverem StoreFront, xenapp masterem (VDA), licenčním serverem Citrix XenApp

![Vzor nasazení 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Podpora Site Recovery

Pro účely tohoto článku byla nasazení společnosti Citrix na virtuálních počítačích VMware spravovaných službou vSphere 6.0 / System Center VMM 2012 R2 použita k nastavení zotavení po havárii.

### <a name="source-and-target"></a>Zdroj a cíl

**Scénář** | **Do sekundární lokality** | **Do Azure**
--- | --- | ---
**Hyper-V** | Není v rozsahu | Ano
**Vmware** | Není v rozsahu | Ano
**Fyzický server** | Není v rozsahu | Ano

### <a name="versions"></a>Verze
Zákazníci mohou nasadit komponenty XenApp jako virtuální počítače spuštěné na Hyper-V nebo VMware nebo jako fyzické servery. Azure Site Recovery můžou chránit fyzické i virtuální nasazení do Azure.
Vzhledem k tomu, že xenapp 7.7 nebo novější je v Azure podporovaný, jenom nasazení s těmito verzemi můžete převést na službu Azure for Disaster Recovery nebo migrace.

### <a name="things-to-keep-in-mind"></a>Co je potřeba mít na paměti

1. Ochrana a obnovení místních nasazení pomocí počítačů se serverovým operačním systémem k doručování publikovaných aplikací XenApp a desktopů publikovaných xenappem je podporována.

2. Ochrana a obnovení místních nasazení pomocí stolních počítačů s operačním systémem k doručování desktopových virtuálních desktopů pro klientské virtuální plochy, včetně Windows 10, není podporována. Důvodem je, že site recovery nepodporuje obnovu počítačů s desktop OS'es.  Také některé klientské virtuální desktopové operační systémy (např. Windows 7) ještě nejsou podporované pro licencování v Azure. [Další informace](https://azure.microsoft.com/pricing/licensing-faq/) týkající se licencování pro plochy klienta nebo serveru v Azure

3.  Azure Site Recovery nemůže replikovat a chránit existující místní klony MCS nebo PVS.
Je třeba znovu tyto klony pomocí Azure RM zřizování z řadiče doručení.

4. NetScaler nelze chránit pomocí Azure Site Recovery, protože NetScaler je založený na FreeBSD a Azure Site Recovery nepodporuje ochranu freebsd operačního systému. Budete muset nasadit a nakonfigurovat nové zařízení NetScaler z Azure Market místo po převzetí služeb při selhání do Azure.


## <a name="replicating-virtual-machines"></a>Replikace virtuálních počítačů

Následující součásti nasazení Citrix XenApp je třeba chránit, aby bylo možné replikovat a obnovovat.

* Ochrana serveru AD DNS
* Ochrana databázového serveru SQL
* Ochrana doručovacího řadiče Citrix
* Ochrana serveru StoreFront.
* Ochrana xenapp masteru (VDA)
* Ochrana licenčního serveru Citrix XenApp


**Replikace serveru DNS služby AD**

Informace o replikaci a konfiguraci řadiče domény v Azure najdete v centru [služby Chránit službu Active Directory a DNS pomocí azure site recovery.](site-recovery-active-directory.md)

**Replikace databázového serveru SQL**

Podrobné technické pokyny k doporučeným možnostem ochrany serverů SQL Server naleznete v [článku Ochrana serveru SQL Server s zotavením po havárii serveru SQL Server a Obnovení webu Azure.](site-recovery-sql.md)

Podle [těchto pokynů](site-recovery-vmware-to-azure.md) začněte replikovat virtuální počítače s další komponentou do Azure.

![Ochrana komponent XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Výpočetní a síťové nastavení**

Po chráněné počítače (stav se zobrazí jako "Chráněné" v části Replikované položky), výpočetní a síťové nastavení je třeba nakonfigurovat.
Ve vlastnostech Výpočetní a síťové > výpočetní prostředky můžete zadat název virtuálního počítače Azure a velikost cíle.
Podle potřeby upravte název tak, aby byl souladu s požadavky Azure. Můžete také zobrazit a přidat informace o cílové síti, podsíti a IP adrese, která bude přiřazena k virtuálnímu počítači Azure.

Je třeba počítat s následujícím:

* Můžete nastavit cílovou IP adresu. Pokud adresu nezadáte, bude počítač, který převezme služby při selhání, používat DHCP. Pokud nastavíte adresu, která není k dispozici při převzetí služeb při selhání, převzetí služeb při selhání nebude fungovat. Stejnou cílovou IP adresu je možné použít pro testovací převzetí služeb při selhání, pokud je adresa k dispozici v testovací síti převzetí služeb při selhání.

* U serveru AD/DNS umožňuje zachování místní adresy zadat stejnou adresu jako server DNS pro virtuální síť Azure.

Počet síťových adaptérů závisí na velikosti, kterou zadáte pro cílový virtuální počítač, a to následujícím způsobem:

*   Pokud je počet síťových adaptérů na zdrojovém počítači menší nebo roven počtu adaptérů, které jsou povolené pro velikost cílového počítače, pak bude mít cíl stejný počet adaptérů jako zdroj.
*   Pokud počet adaptérů pro zdrojový virtuální počítač překračuje počet povolený pro cílovou velikost, použije se maximální velikost cíle.
* Pokud má například zdrojový počítač dva síťové adaptéry a velikost cílového počítače podporuje čtyři, bude mít cílový počítač dva adaptéry. Pokud má zdrojový počítač dva adaptéry, ale podporovaná velikost cíle podporuje pouze jeden, bude mít cílový počítač pouze jeden adaptér.
*   Pokud má virtuální počítač více síťových adaptérů, připojí se všechny ke stejné síti.
*   Pokud má virtuální počítač více síťových adaptérů, stane se první síťový adaptér v seznamu jako výchozí síťový adaptér ve virtuálním počítači Azure.


## <a name="creating-a-recovery-plan"></a>Vytvoření plánu obnovení

Po replikace je povolena pro virtuální chod komponent XenApp, dalším krokem je vytvoření plánu obnovení.
Plán obnovení seskupuje virtuální počítače s podobnými požadavky na převzetí služeb při selhání a obnovení.  

**Kroky k vytvoření plánu obnovení**

1. Přidejte virtuální počítače komponenty XenApp do plánu obnovení.
2. Klikněte na plány obnovení -> + Plán obnovy. Zadejte intuitivní název plánu obnovení.
3. Pro virtuální počítače VMware: Vyberte zdroj jako procesní server VMware, zacilte jako Microsoft Azure a model nasazení jako Správce prostředků a klikněte na Vybrat položky.
4. Pro virtuální počítače Hyper-V: Vyberte zdroj jako server VMM, zacilte jako Microsoft Azure a model nasazení jako Správce prostředků a klikněte na Vybrat položky a vyberte virtuální počítače pro nasazení XenApp.

### <a name="adding-virtual-machines-to-failover-groups"></a>Přidání virtuálních počítačů do skupin převzetí služeb při selhání

Plány obnovení lze přizpůsobit tak, aby přidávaly skupiny s podporou převzetí služeb při selhání pro konkrétní pořadí spuštění, skripty nebo ruční akce. Do plánu obnovení je třeba přidat následující skupiny.

1. Skupina s podporou převzetí služeb při selhání1: Služba Dns služby AD
2. Skupina s podporou převzetí služeb při selhání2: Virtuální servery SQL Server
2. Skupina s podporou převzetí služeb při selhání3: Virtuální virtuální virtuální virtuální server hlavního obrázku VDA
3. Skupina s podporou převzetí služeb při selhání4: Řadič doručení a virtuální servery StoreFront server


### <a name="adding-scripts-to-the-recovery-plan"></a>Přidání skriptů do plánu obnovení

Skripty lze spustit před nebo za určitou skupinou v plánu obnovení. Ruční akce mohou být také zahrnuty a provedeny během převzetí služeb při selhání.

Přizpůsobený plán obnovení vypadá takto:

1. Skupina s podporou převzetí služeb při selhání1: Služba Dns služby AD
2. Skupina s podporou převzetí služeb při selhání2: Virtuální servery SQL Server
3. Skupina s podporou převzetí služeb při selhání3: Virtuální virtuální virtuální virtuální server hlavního obrázku VDA

   >[!NOTE]     
   >Kroky 4, 6 a 7 obsahující ruční nebo skriptové akce se vztahují pouze na místní prostředí XenApp >s katalogy MCS/PVS.

4. Akce Ručně nebo skriptu skupiny 3: Vypnutí hlavního virtuálního virtuálního zařízení VDA.
Hlavní virtuální počítač VDA při převzetí služby převzetí počítače do Azure bude ve spuštěném stavu. Chcete-li vytvořit nové katalogy MCS pomocí hostingu Azure, hlavní virtuální počítač VDA musí být ve stavu Zastaveno (de přiděleno). Vypnutí virtuálního počítače z portálu Azure.

5. Skupina s podporou převzetí služeb při selhání4: Řadič doručení a virtuální servery StoreFront server
6. Akce skupiny3 nebo skriptu 1:

    ***Přidání připojení hostitele služby Azure RM***

    Vytvořte připojení hostitele Azure v počítači řadiče doručení a zřizte nové katalogy MCS v Azure. Postupujte podle pokynů, jak je vysvětleno v tomto [článku](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/).

7. Akce skupiny3 nebo skriptu 2:

    ***Opětovné vytvoření katalogů MCS v Azure***

    Existující klony MCS nebo PVS v primární lokalitě se nebudou replikovat do Azure. Je třeba znovu tyto klony pomocí replikované hlavní VDA a Azure zřizování z řadiče doručení. Podle pokynů, jak je vysvětleno v tomto [článku](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) k vytvoření katalogů MCS v Azure.

![Plán obnovy pro komponenty XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >Skripty v [místě můžete](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts) použít k aktualizaci SLUŽBY DNS pomocí nových IP služeb pomocí služeb, které selhaly >virtuálnípočítače, nebo v případě potřeby připojit nástroj pro vyrovnávání zatížení k převzetí služeb při selhání nad virtuálním počítačem.


## <a name="doing-a-test-failover"></a>Provedení zkušebního převzetí služeb při selhání

Postupujte podle [těchto pokynů](site-recovery-test-failover-to-azure.md) provést test převzetí služeb při selhání.

![Plán obnovení](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Převzetí služeb při selhání

Při převzetí služeb při selhání postupujte [podle těchto pokynů.](site-recovery-failover.md)

## <a name="next-steps"></a>Další kroky

Další [informace](https://aka.ms/citrix-xenapp-xendesktop-with-asr) o replikaci nasazení Citrix XenApp a XenDesktop najdete v tomto dokumentu white paper. Podívejte se na pokyny k [replikaci jiných aplikací](site-recovery-workload.md) pomocí site recovery.
