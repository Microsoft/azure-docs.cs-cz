---
title: Zotavení po havárii VMware pomocí Azure Site Recovery
description: Tento článek poskytuje přehled zotavení po havárii virtuálních počítačů VMware do Azure pomocí služby Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: dfbdff01064b483085233ece47d1d3b635b68743
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87021456"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>O zotavení po havárii virtuálních počítačů VMware do Azure

Tento článek poskytuje přehled zotavení po havárii pro místní virtuální počítače VMware do Azure pomocí služby [Azure Site Recovery](site-recovery-overview.md) .

## <a name="what-is-bcdr"></a>Co je BCDR?

Strategie pro provozní kontinuitu a zotavení po havárii (BCDR) pomáhá udržet vaše podnikání v provozu. Při plánovaném výpadku a neočekávaných výpadkech udržuje BCDR data bezpečná a dostupná a zajišťuje, aby aplikace pokračovaly v běhu. Kromě funkcí BCDR platforem, jako jsou místní párování a úložiště s vysokou dostupností, poskytuje Azure Recovery Services jako nedílnou součást vašeho řešení BCDR. Služba Recovery Services zahrnuje: 

- [Azure Backup](../backup/backup-overview.md) zálohuje vaše místní a data virtuálních počítačů Azure. Můžete zálohovat soubor a složky, konkrétní úlohy nebo celý virtuální počítač. 
- [Azure Site Recovery](site-recovery-overview.md) poskytuje odolnost a zotavení po havárii pro aplikace a úlohy běžící na místních počítačích nebo virtuálních počítačích Azure IaaS. Site Recovery orchestruje replikaci a zpracovává převzetí služeb při selhání do Azure, pokud dojde k výpadku. Také zpracovává obnovení z Azure do primární lokality. 

> [!NOTE]
> Site Recovery nepřesouvá ani neukládají zákaznická data mimo cílovou oblast, ve které byly pro zdrojové počítače nastavily zotavení po havárii. Zákazníci si můžou vybrat Recovery Services trezor z jiné oblasti, pokud si si je vyberou. Trezor Recovery Services obsahuje metadata, ale žádná skutečná zákaznická data.

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Jak Site Recovery zotavení po havárii?

1. Po přípravě Azure a místní lokality můžete nastavit a povolit replikaci místních počítačů.
2. Site Recovery orchestruje počáteční replikaci počítače v souladu s nastavením zásad.
3. Po počáteční replikaci Site Recovery replikují rozdílové změny do Azure. 
4. Když se všechno replikuje podle očekávání, spustíte postup zotavení po havárii.
    - Tato kontrola pomáhá zajistit, že převzetí služeb při selhání bude fungovat podle očekávání, když dojde k reálné potřebě.
    - Přechod k testovacímu převzetí služeb při selhání bez dopadu na produkční prostředí
5. Pokud dojde k výpadku, spustíte úplné převzetí služeb při selhání do Azure. Můžete převzít služby při selhání jednoho počítače nebo můžete vytvořit plán obnovení, který převezme více počítačů současně.
6. Při převzetí služeb při selhání se virtuální počítače Azure vytvářejí z dat virtuálních počítačů ve spravovaných discích nebo účtech úložiště. Uživatelé můžou dál přistupovat k aplikacím a úlohám z virtuálního počítače Azure.
7. Až bude vaše místní lokalita opět dostupná, vraťte se z Azure zpátky.
8. Po navrácení služeb po obnovení a jejich práci z primární lokality můžete znovu spustit replikaci místních virtuálních počítačů do Azure.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>Návody o tom, jestli je moje prostředí vhodné pro zotavení po havárii do Azure?

Site Recovery může replikovat jakékoli úlohy spuštěné na podporovaném virtuálním počítači VMware nebo fyzickém serveru. Tady jsou tyto věci, které potřebujete pro kontrolu vašeho prostředí:

- Pokud provádíte replikaci virtuálních počítačů VMware, spouštíte správné verze virtualizačních serverů VMware? [Podívejte se sem](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
- Jsou počítače, které chcete replikovat, spuštěn podporovaný operační systém? [Podívejte se sem](vmware-physical-azure-support-matrix.md#replicated-machines).
- V případě zotavení po havárii systému Linux jsou počítače s podporovaným systémem souborů/Host Storage? [Zaškrtnout](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)
- Jsou počítače, které chcete replikovat, v dodržování požadavků Azure? [Podívejte se sem](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
- Je vaše konfigurace sítě podporovaná? [Podívejte se sem](vmware-physical-azure-support-matrix.md#network).
- Je vaše konfigurace úložiště podporovaná? [Podívejte se sem](vmware-physical-azure-support-matrix.md#storage).


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>Co musím před zahájením nastavovat v Azure?

V Azure je potřeba připravit toto:

1. Ověřte, že váš účet Azure má oprávnění k vytváření virtuálních počítačů v Azure.
2. Vytvořte síť Azure, ke které se virtuální počítače Azure připojí po vytvoření z účtů úložiště nebo spravovaných disků po převzetí služeb při selhání.
3. Nastavte pro Site Recovery trezor služby Azure Recovery Services. Trezor se nachází v Azure Portal a používá se k nasazení, konfiguraci, orchestraci, monitorování a řešení potíží s nasazením Site Recovery.

*Potřebujete další pomoc?*

Naučte se, jak nastavit Azure pomocí [ověření účtu](tutorial-prepare-azure.md#verify-account-permissions), vytvoření [sítě](tutorial-prepare-azure.md#set-up-an-azure-network)a [Nastavení trezoru](tutorial-prepare-azure.md#create-a-recovery-services-vault).



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>Co potřebuji k nastavení místního prostředí před zahájením?

V místním prostředí je to, co je potřeba udělat:

1. Musíte nastavit několik účtů:

    - Pokud provádíte replikaci virtuálních počítačů VMware, účet je potřebný k tomu, Site Recovery pro přístup k vCenter Server nebo k hostitelům ESXi pro vSphere k automatickému zjišťování virtuálních počítačů.
    - K instalaci agenta služby mobility Site Recovery na každém fyzickém počítači nebo VIRTUÁLNÍm počítači, který chcete replikovat, se vyžaduje účet.

2. Pokud jste to předtím neudělali, musíte ověřit kompatibilitu vaší infrastruktury VMware.
3. Ujistěte se, že se můžete připojit k virtuálním počítačům Azure po převzetí služeb při selhání. Protokol RDP nastavíte na místních počítačích s Windows nebo na počítačích se systémem Linux.

*Potřebujete další pomoc?*
- Připravte účty pro [automatické zjišťování](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) a pro [instalaci služby mobility](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation).
- [Ověřte](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements) , zda jsou nastavení VMware kompatibilní.
- [Připravte](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover) se, abyste se po převzetí služeb při selhání připojili v Azure.
- Pokud chcete podrobnější nápovědu k nastavení adresování IP pro virtuální počítače Azure po převzetí služeb při selhání, [Přečtěte si tento článek](concepts-on-premises-to-azure-networking.md).

## <a name="how-do-i-set-up-disaster-recovery"></a>Návody nastavit zotavení po havárii?

Jakmile budete mít k dispozici svoji místní infrastrukturu Azure a místní infrastruktury, můžete nastavit zotavení po havárii.

1. Abyste pochopili komponenty, které budete muset nasadit, Projděte si [architekturu VMware to Azure](vmware-azure-architecture.md)a [fyzickou do architektury Azure](physical-azure-architecture.md). K dispozici je celá řada součástí, takže je důležité pochopit, jak se všechny vejdou dohromady.
2. **Zdrojové prostředí**: jako první krok nasazení nastavíte zdrojové prostředí replikace. Určete, co chcete replikovat, a kam se chcete replikovat.
3. **Konfigurační server**: musíte nastavit konfigurační server v místním zdrojovém prostředí:
    - Konfigurační server je jeden místní počítač. Pro zotavení po havárii VMware doporučujeme, abyste ho nasadili jako virtuální počítač VMware, který se dá nasadit ze šablony OVF ke stažení.
    - Konfigurační server koordinuje komunikaci mezi místními prostředími a Azure.
    - Na počítači konfiguračního serveru běží několik dalších komponent.
        - Procesový server přijímá, optimalizuje a odesílá data replikace do účtu úložiště mezipaměti v Azure. Také zpracovává automatickou instalaci služby mobility na počítačích, které chcete replikovat, a provádí automatické zjišťování virtuálních počítačů na serverech VMware.
        - Hlavní cílový server zpracovává replikační data během navrácení služeb z Azure po obnovení.
    - Nastavení zahrnuje registraci konfiguračního serveru v trezoru, stahování serveru MySQL a VMware PowerCLI a určení účtů vytvořených pro automatické zjišťování a instalaci služby mobility.
4. **Cílové prostředí**: nastavíte cílové prostředí Azure tak, že zadáte své předplatné Azure a nastavení sítě.
5. **Zásady replikace**: určujete, jak se má replikace provádět. Nastavení zahrnuje jak často se vytvářejí a ukládají body obnovení a jestli se mají vytvářet snímky konzistentní vzhledem k aplikacím.
6. **Povolte replikaci**. Povolíte replikaci pro místní počítače. Pokud jste vytvořili účet pro instalaci služby mobility, nainstaluje se, až povolíte replikaci pro daný počítač. 

*Potřebujete další pomoc?*

- Rychlý návod k těmto krokům si můžete vyzkoušet v [kurzu VMware](vmware-azure-tutorial.md)a na [fyzických serverech](physical-azure-disaster-recovery.md).
- [Přečtěte si další informace](vmware-azure-set-up-source.md) o nastavení zdrojového prostředí.
- [Seznamte](vmware-azure-deploy-configuration-server.md) se s požadavky na konfigurační server a nastavením konfiguračního serveru pomocí šablony OVF pro replikaci VMware. Pokud z nějakého důvodu nemůžete šablonu použít nebo pokud provádíte replikaci fyzických serverů, [použijte tyto pokyny](physical-azure-set-up-source.md#set-up-the-source-environment).
- [Přečtěte si další informace](vmware-azure-set-up-target.md) o nastavení cíle.
- [Získejte další informace](vmware-azure-set-up-replication.md) o nastavení zásad replikace.
- [Naučte](vmware-azure-enable-replication.md) se, jak povolit replikaci a [vyloučit](vmware-azure-exclude-disk.md) disky z replikace.


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>Něco se nepovedlo, jak řešit potíže?

- Jako první krok zkuste [sledovat nasazení](site-recovery-monitor-and-troubleshoot.md) a ověřit stav replikovaných položek, úloh a problémů infrastruktury a Identifikujte případné chyby.
- Pokud nemůžete dokončit počáteční replikaci nebo probíhající replikace nefunguje podle očekávání, [Přečtěte si tento článek](vmware-azure-troubleshoot-replication.md) , kde najdete běžné chyby a tipy pro řešení potíží.
- Pokud máte problémy s automatickou instalací služby mobility na počítačích, které chcete replikovat, zkontrolujte běžné chyby v [tomto článku](vmware-azure-troubleshoot-push-install.md).
- Pokud převzetí služeb při selhání nefunguje podle očekávání, Projděte si běžné chyby v [tomto článku](site-recovery-failover-to-azure-troubleshoot.md).
- Pokud navrácení služeb po obnovení nefunguje, ověřte, jestli se problém zobrazuje v [tomto článku](vmware-azure-troubleshoot-failback-reprotect.md).



## <a name="next-steps"></a>Další kroky

Díky replikaci teď můžete [Spustit postup zotavení po havárii](tutorial-dr-drill-azure.md) , abyste zajistili, že převzetí služeb při selhání funguje podle očekávání. 
