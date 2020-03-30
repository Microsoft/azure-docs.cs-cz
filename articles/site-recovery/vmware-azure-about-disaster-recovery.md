---
title: Zotavení po havárii v systému VMware pomocí azure site recovery
description: Tento článek obsahuje přehled zotavení po havárii virtuálních počítačích VMware do Azure pomocí služby Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 589dda80d68fba73a729da4b6e59270cc09c18cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954396"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>Obnovení virtuálních počítačů VMware do Azure o zotavení po havárii

Tento článek poskytuje přehled zotavení po havárii pro místní virtuální počítače VMware do Azure pomocí služby [Azure Site Recovery.](site-recovery-overview.md)

## <a name="what-is-bcdr"></a>Co je BCDR?

Strategie kontinuity podnikání a zotavení po havárii (BCDR) pomáhá udržet vaše podnikání v provozu. Během plánovaných výpadků a neočekávaných výpadků udržuje BCDR data v bezpečí a dostupná a zajišťuje, že aplikace budou i nadále spuštěny. Kromě funkcí BCDR platformy, jako je regionální párování a úložiště s vysokou dostupností, poskytuje Azure služby obnovení jako nedílnou součást vašeho řešení BCDR. Služby obnovení zahrnují: 

- [Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) zálohuje vaše místní data virtuálních zařízení a virtuálních zařízení Azure. Můžete zálohovat soubor a složky, konkrétní úlohy nebo celý virtuální virtuální ms. 
- [Azure Site Recovery](site-recovery-overview.md) poskytuje odolnost a zotavení po havárii pro aplikace a úlohy spuštěné na místních počítačích nebo virtuálních počítačích Azure IaaS. Site Recovery orchestruje replikaci a zpracovává převzetí služeb při selhání do Azure, když dojde k výpadkům. Také zpracovává obnovení z Azure do primární lokality. 

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Jak site recovery provádí zotavení po havárii?

1. Po přípravě Azure a místní lokality nastavíte a povolíte replikaci pro místní počítače.
2. Site Recovery orchestruje počáteční replikaci počítače v souladu s nastavením zásad.
3. Po počáteční replikaci site recovery replikuje rozdílové změny do Azure. 
4. Když se vše replikuje podle očekávání, spustíte cvičení zotavení po havárii.
    - Vrtačka pomáhá zajistit, že převzetí služeb při selhání bude fungovat podle očekávání, když vznikne skutečná potřeba.
    - Vrtačka provede zkušební převzetí služeb při selhání bez dopadu na produkční prostředí.
5. Pokud dojde k výpadku, spustíte úplné převzetí služeb při selhání do Azure. Můžete přepojit jeden počítač nebo můžete vytvořit plán obnovení, který převezme služby při selhání více počítačů současně.
6. Při převzetí služeb při selhání se virtuální počítače Azure vytvářejí z dat virtuálních počítačů na spravovaných discích nebo účtech úložiště. Uživatelé můžou dál přistupovat k aplikacím a úlohám z virtuálního počítače Azure.
7. Když je váš místní web znovu k dispozici, můžete z Azure navrácení mů e-li.
8. Po vrácení po službě na poslužbě a práci z primární lokality ještě jednou, začnete replikovat místní virtuální počítače do Azure znovu.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>Jak poznám, jestli je moje prostředí vhodné pro zotavení po havárii do Azure?

Site Recovery můžete replikovat všechny úlohy spuštěné na podporovaném virtuálním počítači VMware nebo fyzickém serveru. Zde jsou věci, které potřebujete zkontrolovat ve vašem prostředí:

- Pokud replikujete virtuální počítače VMware, používáte správné verze virtualizačních serverů VMware? [Zkontrolujte zde](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
- Jsou počítače, které chcete replikovat, spuštěny s podporovaným operačním systémem? [Zkontrolujte zde](vmware-physical-azure-support-matrix.md#replicated-machines).
- Pro zotavení po havárii Linuxu jsou počítače s podporovaným souborovým systémem / úložištěm hosta? [Zkontrolujte zde](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)
- Splňují počítače, které chcete replikovat, požadavky Azure? [Zkontrolujte zde](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
- Je konfigurace sítě podporována? [Zkontrolujte zde](vmware-physical-azure-support-matrix.md#network).
- Je konfigurace úložiště podporována? [Zkontrolujte zde](vmware-physical-azure-support-matrix.md#storage).


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>Co je potřeba nastavit v Azure, než začnu?

V Azure je potřeba připravit následující:

1. Ověřte, že váš účet Azure má oprávnění k vytváření virtuálních počítačů v Azure.
2. Vytvořte síť Azure, ke které se připojí virtuální počítače Azure, když se po převzetí služeb při selhání vytvoří z účtů úložiště nebo spravovaných disků.
3. Nastavte trezor služby Azure Recovery Services pro obnovení webu. Trezor je umístěn na webu Azure Portal a používá se k nasazení, konfiguraci, orchestraci, monitorování a řešení potíží s nasazením site recovery.

*Potřebujete další pomoc?*

Zjistěte, jak nastavit Azure [ověřením účtu](tutorial-prepare-azure.md#verify-account-permissions), vytvořením [sítě](tutorial-prepare-azure.md#set-up-an-azure-network)a [nastavením trezoru](tutorial-prepare-azure.md#create-a-recovery-services-vault).



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>Co musím nastavit místně, než začnu?

Místní řešení je to, co musíte udělat:

1. Musíte nastavit několik účtů:

    - Pokud replikujete virtuální počítače VMware, je potřeba účet pro site recovery pro přístup k hostitelům vCenter Serveru nebo vSphere ESXi k automatickému zjišťování virtuálních účtů.
    - Účet je potřeba k instalaci agenta služby Site Recovery Mobility na každém fyzickém počítači nebo virtuálním počítači, který chcete replikovat.

2. Pokud jste to dříve neudělali, musíte zkontrolovat kompatibilitu infrastruktury VMware.
3. Ujistěte se, že se po převzetí služeb při selhání můžete připojit k virtuálním počítačům Azure. Rdp nastavíte na místních počítačích se systémem Windows nebo SSH na počítačích s Linuxem.

*Potřebujete další pomoc?*
- Připravte účty pro [automatické zjišťování](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) a [instalaci služby Mobility](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation).
- [Ověřte, zda](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements) jsou nastavení v systému VMware kompatibilní.
- [Připravte](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover) se tak, že se připojíte v Azure po převzetí služeb při selhání.
- Pokud chcete podrobnější nápovědu k nastavení adresování IP adres pro virtuální počítače Azure po převzetí služeb při selhání, [přečtěte si tento článek](concepts-on-premises-to-azure-networking.md).

## <a name="how-do-i-set-up-disaster-recovery"></a>Jak nastavím zotavení po havárii?

Až budete mít nastavenou infrastrukturu Azure a místní, můžete nastavit zotavení po havárii.

1. Pokud chcete porozumět součástem, které budete potřebovat nasadit, zkontrolujte [architekturu VMware do Azure](vmware-azure-architecture.md)a [architekturu Physical to Azure](physical-azure-architecture.md). Existuje celá řada komponent, takže je důležité pochopit, jak se všechny zapadají do sebe.
2. **Zdrojové prostředí**: Jako první krok v nasazení nastavíte zdrojové prostředí replikace. Můžete určit, co chcete replikovat a kam chcete replikovat.
3. **Konfigurační server**: Je třeba nastavit konfigurační server v místním zdrojovém prostředí:
    - Konfigurační server je jeden místní počítač. Pro zotavení po havárii společnosti VMware doporučujeme nasadit ho jako virtuální ho virtuálního počítače VMware, který lze nasadit ze šablony OVF ke stažení.
    - Konfigurační server koordinuje komunikaci mezi místním a Azure
    - V počítači konfiguračního serveru je spuštěno několik dalších součástí.
        - Procesní server přijímá, optimalizuje a odesílá data replikace do účtu úložiště mezipaměti v Azure. Také zpracovává automatickou instalaci služby Mobility na počítačích, které chcete replikovat, a provádí automatické zjišťování virtuálních počítačů na serverech VMware.
        - Hlavní cílový server zpracovává replikační data během navrácení služeb z Azure po obnovení.
    - Nastavení zahrnuje registraci konfiguračního serveru v trezoru, stažení mysql serveru a rozhraní VMware PowerCLI a určení účtů vytvořených pro automatické zjišťování a instalaci služby Mobility.
4. **Cílové prostředí**: Cílové prostředí Azure nastavíte zadáním předplatného Azure a nastavení sítě.
5. **Zásady replikace**: Můžete zadat, jak by mělo dojít k replikaci. Nastavení zahrnují, jak často jsou body obnovení vytvářeny a ukládány a zda mají být vytvořeny snímky konzistentní s aplikací.
6. **Povolte replikaci**. Povolit replikaci pro místní počítače. Pokud jste vytvořili účet pro instalaci služby Mobility, bude nainstalována, když povolíte replikaci pro počítač. 

*Potřebujete další pomoc?*

- Pro rychlý návod k těmto krokům si můžete vyzkoušet náš [výukový program VMware](vmware-azure-tutorial.md)a [návod k fyzickému serveru](physical-azure-disaster-recovery.md).
- [Přečtěte si další informace](vmware-azure-set-up-source.md) o nastavení zdrojového prostředí.
- [Získejte informace o](vmware-azure-deploy-configuration-server.md) požadavcích na konfigurační server a nastavení konfiguračního serveru pomocí šablony OVF pro replikaci společnosti VMware. Pokud z nějakého důvodu nelze použít šablonu nebo replikujete fyzické servery, [použijte tyto pokyny](physical-azure-set-up-source.md#set-up-the-source-environment).
- [Přečtěte si další informace](vmware-azure-set-up-target.md) o nastavení cílů.
- [Získejte další informace](vmware-azure-set-up-replication.md) o nastavení zásad replikace.
- [Zjistěte,](vmware-azure-enable-replication.md) jak povolit replikaci a [vyloučit](vmware-azure-exclude-disk.md) disky z replikace.


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>Něco se pokazilo, jak mohu řešit problémy?

- Jako první krok zkuste [sledovat nasazení](site-recovery-monitor-and-troubleshoot.md) k ověření stavu replikovaných položek, úloh a problémů s infrastrukturou a identifikovat případné chyby.
- Pokud se vám nedaří dokončit počáteční replikaci nebo probíhající replikace nefunguje podle očekávání, [přečtěte si v tomto článku](vmware-azure-troubleshoot-replication.md) běžné chyby a tipy pro řešení potíží.
- Pokud máte problémy s automatickou instalací služby Mobility na počítačích, které chcete replikovat, přečtěte si běžné chyby v [tomto článku](vmware-azure-troubleshoot-push-install.md).
- Pokud převzetí služeb při selhání nefunguje podle očekávání, zkontrolujte běžné chyby v [tomto článku](site-recovery-failover-to-azure-troubleshoot.md).
- Pokud navrácení služeb po selhání nefunguje, zkontrolujte, zda se problém zobrazí v [tomto článku](vmware-azure-troubleshoot-failback-reprotect.md).



## <a name="next-steps"></a>Další kroky

S replikace nyní na místě, měli byste [spustit zotavení po havárii vrták](tutorial-dr-drill-azure.md) zajistit, že převzetí služeb při selhání funguje podle očekávání. 
