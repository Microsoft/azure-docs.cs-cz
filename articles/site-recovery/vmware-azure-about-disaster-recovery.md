---
title: Informace o zotavení po havárii virtuálních počítačů VMware do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek poskytuje přehled o zotavení po havárii virtuálních počítačů VMware do Azure pomocí služby Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.date: 08/29/2018
ms.author: raynew
ms.openlocfilehash: 22036ff6141b5892a8ba100893d3037f51e19af5
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43288700"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>Informace o zotavení po havárii virtuálních počítačů VMware do Azure

Tento článek poskytuje přehled o zotavení po havárii pro místní virtuální počítače VMware do Azure s využitím [Azure Site Recovery](site-recovery-overview.md) služby.

## <a name="what-is-bcdr"></a>Co je BCDR?

Zajištění provozní kontinuity a po havárii (BCDR) strategii zotavení pomáhá chránit vaši firmu, rychle zprovoznit. Při plánovaných nebo neočekávaných výpadků BCDR zajišťuje zabezpečení a dostupných dat a zajišťuje, že aplikace pokračovat v běhu. Kromě funkcemi BCDR platformy, jako jsou oblastní párování a úložiště s vysokou dostupností poskytuje Azure Recovery Services jako nedílnou součástí vašeho řešení BCDR. Obnovení služby patří: 

- [Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) zálohuje vaše místní a data virtuálního počítače Azure. Můžete zálohovat soubor a složky, konkrétní úlohy nebo celý virtuální počítač. 
- [Azure Site Recovery](site-recovery-overview.md) poskytuje odolnost a zotavení po havárii pro aplikace a úlohy běžící na místních počítačích nebo virtuálních počítačů Azure IaaS. Site Recovery orchestruje replikace a zpracovává převzetí služeb při selhání do Azure, když dojde k výpadku. Také obstará obnovení z Azure do primární lokality. 

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Jak Site Recovery poskytnout zotavení po havárii?

1. Po přípravě Azure a místní sítí, nastavení a povolení replikace pro místní počítače.
2. Site Recovery orchestruje počáteční replikace počítače, v souladu se nastavení zásad.
3. Po počáteční replikaci Site Recovery replikuje rozdílových dat do Azure. 
4. Pokud vše, co je replikace podle očekávání, spuštění postupu zotavení po havárii.
    - Na postup zotavení pomáhá zajistit, že tento převzetí služeb při selhání bude fungovat podle očekávání, pokud skutečné potřeby.
    - Na postup zotavení provede test převzetí bez dopadu na vaše produkční prostředí.
5. Pokud dojde k výpadku, spusťte úplné převzetí služeb při selhání do Azure. Je převzetí služeb při selhání jednoho počítače, nebo můžete vytvořit plán obnovení, který převezme služby při selhání více počítačů současně.
6. Na převzetí služeb při selhání se vytvoří virtuální počítače Azure z dat virtuálního počítače ve službě Azure Storage. Uživatelé můžou dál přístup k aplikací a úloh virtuálního počítače Azure
7. Pokud vaše místní lokalita opět dostupná, převezme služby při obnovení z Azure.
8. Po navrácení služeb po obnovení a ještě jednou pracují z primární lokality, můžete spustit znovu replikaci místních virtuálních počítačů do Azure.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>Jak poznám, jestli je vhodný pro zotavení po havárii do Azure Moje prostředí?

Site Recovery dokáže replikovat jakoukoli úlohu spuštěnou na podporovaném virtuálním počítači VMware nebo fyzický server. Tady jsou věci, které je potřeba zkontrolovat ve vašem prostředí:

- Pokud provádíte replikaci virtuálních počítačů VMware, používáte správné verze virtualizační servery VMware? [Tady](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
- Jsou počítače, které chcete replikovat, spuštěn podporovaný operační systém? [Tady](vmware-physical-azure-support-matrix.md#replicated-machines).
- Pro zotavení po havárii pro Linux počítače běží na podporované souborové úložiště systému/hosta? [Zkontrolujte, zde](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)
- Počítače, které chcete replikovat v souladu s požadavky na Azure? [Tady](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
- Podporované konfigurace sítě [Tady](vmware-physical-azure-support-matrix.md#network).
- Podporované konfigurace úložiště [Tady](vmware-physical-azure-support-matrix.md#storage).


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>Co je potřeba nastavit v Azure před mám začít?

V Azure budete muset připravit následující:

1. Ověřte, že váš účet Azure má oprávnění k vytvoření virtuálních počítačů v Azure.
2. Vytvořte účet úložiště pro uložení bitové kopie replikovaných počítačů.
3. Vytvořte síť Azure, ke které virtuální počítače Azure připojí při jejich vytvoření z úložiště po převzetí služeb při selhání.
4. Nastavte trezor služby Azure Recovery Services pro Site Recovery. Trezor se nachází na webu Azure Portal a slouží k nasazení, konfigurace, Orchestrace, monitorování a řešení potíží s nasazením Site Recovery.

*Potřebujete další pomoc?*

Zjistěte, jak nastavit službu Azure podle [ověření vašeho účtu](tutorial-prepare-azure.md#verify-account-permissions), vytváření [účtu úložiště](tutorial-prepare-azure.md#create-a-storage-account) a [sítě](tutorial-prepare-azure.md#set-up-an-azure-network), a [nastavení trezoru](tutorial-prepare-azure.md#create-a-recovery-services-vault).



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>Co je potřeba nastavit místní než začnu?

Pro místní zde je, co je potřeba udělat:

1. Budete muset nastavit několik účtů:

    - Pokud provádíte replikaci virtuálních počítačů VMware, je potřeba účet pro přístup k serveru vCenter nebo hostiteli vSphere ESXi za Site Recovery k automatickému zjišťování virtuálních počítačů.
    - Účet je potřebný k instalaci agenta služby Site Recovery Mobility na každý fyzický počítač nebo virtuální počítač, který chcete replikovat.

2. Je potřeba zkontrolovat kompatibilitu vaší infrastruktury VMware, pokud jste to dřív.
3. Zkontrolujte, zda se můžete připojit k virtuálním počítačům Azure po převzetí služeb. Nastavíte RDP na místních počítačích Windows nebo SSH na počítačích s Linuxem.

*Potřebujete další pomoc?*
- Příprava účty pro [automatické zjišťování](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) a [instalace služby Mobility](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation).
- [Ověřte](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements) vaše nastavení VMware musí být kompatibilní.
- [Příprava](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover) tak, aby připojení v Azure po převzetí služeb při selhání.
- Pokud chcete podrobnější nápovědu o nastavení přidělování IP adres pro virtuální počítače Azure po převzetí služeb při selhání, [v tomto článku](concepts-on-premises-to-azure-networking.md).

## <a name="how-do-i-set-up-disaster-recovery"></a>Jak nastavit zotavení po havárii?

Jakmile máte infrastrukturu Azure a místní na místě, můžete nastavit zotavení po havárii.

1. Seznámení s komponentami, které budete potřebovat k nasazení, najdete v tématu [replikaci z VMware do Azure architektury](vmware-azure-architecture.md)a [z fyzických prostředků do Azure architektury](physical-azure-architecture.md). Existuje několik komponent, takže je důležité pochopit, jak všechny zapadají.
2. **Zdrojové prostředí**: jako první krok v nasazení, můžete nastavit zdrojové prostředí replikace. Určíte, co chcete replikovat, a ve které chcete replikovat.
3. **Konfigurační server**: je potřeba nastavit konfigurační server ve svém zdrojovém prostředí v místním:
    - Konfigurační server je jeden místní počítač. Pro zotavení po havárii VMware doporučujeme nasadit jako virtuální počítač VMware, které se dají nasadit z ke stažení šablony OVF.
    - Konfigurační server koordinuje komunikaci mezi místním a Azure
    - Několik dalších komponent spustit na počítači serveru konfigurace.
        - Procesový server přijímá, optimalizuje a odesílá data replikace do úložiště Azure. Také obstará automatickou instalaci služby Mobility na počítače, které chcete replikovat, a provádí automatické zjišťování virtuálních počítačů na servery VMware.
        - Hlavní cílový server zpracovává replikační data během navrácení služeb z Azure po obnovení.
    - Nastavení zahrnuje registrace konfiguračního serveru v trezoru, stahování MySQL Server a VMware PowerCLI a určení účty vytvořené pro automatické zjišťování a instalace služby Mobility.
4. **Cílové prostředí**: můžete nastavit cílové prostředí Azure tak, že zadáte předplatné Azure, úložiště a nastavení sítě.
5. **Zásady replikace**: Určete způsob spuštění by měl replikace. Nastavení zahrnuje jak často body obnovení jsou vytvořeny a uloženy a, jestli se má vytvořit snímky konzistentní.
6. **Povolení replikace**. Povolení replikace místních počítačů. Pokud jste vytvořili účet, který chcete nainstalovat službu Mobility, pak ho nainstaluje, když povolíte replikaci pro počítač. 

*Potřebujete další pomoc?*

- Rychlý návod z těchto kroků můžete vyzkoušet naše [VMware kurzu](vmware-azure-tutorial.md), a [fyzický server návod](physical-azure-disaster-recovery.md).
- [Další informace](vmware-azure-set-up-source.md) o nastavení zdrojového prostředí.
- [Další informace o](vmware-azure-deploy-configuration-server.md) požadavky na konfiguraci serveru a nastavení konfiguračního serveru pomocí šablony OVF pro replikaci VMware. Pokud z nějakého důvodu nemůžete použít šablonu, nebo replikaci fyzických serverů, [těmito pokyny se řiďte](physical-azure-set-up-source.md#set-up-the-source-environment).
- [Další informace](vmware-azure-set-up-target.md) o nastavení cíle.
- [Další informace získáte](vmware-azure-set-up-replication.md) o nastavení zásad replikace.
- [Přečtěte si](vmware-azure-enable-replication.md) povolení replikace, a [vyloučit](vmware-azure-exclude-disk.md) disky z replikace.


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>Něco neproběhlo, jak řešit?

- Jako první krok, zkuste [monitorování vašeho nasazení](site-recovery-monitor-and-troubleshoot.md) ověřte stav replikované položky, úloh a problémů s infrastrukturou, a identifikovat chyby.
- Pokud nemůžete pro dokončení prvotní replikace nebo probíhající replikaci nefunguje podle očekávání, [k tomuto článku](vmware-azure-troubleshoot-replication.md) pro běžné chyby a tipy pro řešení potíží.
- Pokud máte problémy s automatickou instalaci služby Mobility na počítačích, které chcete replikovat, zkontrolujte běžné chyby v [v tomto článku](vmware-azure-troubleshoot-push-install.md).
- Pokud převzetí služeb při selhání nefunguje podle očekávání, zkontrolujte běžné chyby [v tomto článku](site-recovery-failover-to-azure-troubleshoot.md).
- Pokud se navrácení služeb po obnovení není funkční, zkontrolujte, zda se zobrazí váš problém v [v tomto článku](vmware-azure-troubleshoot-failback-reprotect.md).



## <a name="next-steps"></a>Další postup

S replikací nyní na místě, měli byste [spuštění postupu zotavení po havárii](tutorial-dr-drill-azure.md) zajistit, že převzetí služeb při selhání funguje podle očekávání. 
