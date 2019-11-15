---
title: Architektura zotavení po havárii fyzického serveru v Azure Site Recovery
description: Tento článek poskytuje přehled komponent a architektury používaných při zotavení po havárii místních fyzických serverů do Azure pomocí služby Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 23e8e4f9a092e871e62da27c8bf0c58a3bb8eb5b
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084682"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Architektura fyzického serveru do Azure pro zotavení po havárii

Tento článek popisuje architekturu a procesy používané při replikaci, převzetí služeb při selhání a obnovování fyzických serverů s Windows a Linux mezi místními lokalitami a Azure pomocí služby [Azure Site Recovery](site-recovery-overview.md) .


## <a name="architectural-components"></a>Komponenty architektury

Následující tabulka a grafika obsahují podrobný pohled na součásti používané pro replikaci fyzického serveru do Azure.  

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Azure** | Předplatné Azure a síť Azure. | Replikovaná data z místních fyzických počítačů se ukládají ve službě Azure Managed disks. Virtuální počítače Azure se vytvářejí s replikovanými daty při převzetí služeb při selhání z místního prostředí do Azure. Virtuální počítače Azure se připojí k virtuální síti Azure po svém vytvoření.
**Konfigurační server** | Je nasazený jeden místní fyzický počítač nebo virtuální počítač VMware, aby se spouštěly všechny místní Site Recovery komponenty. Virtuální počítač spouští konfigurační server, procesový Server a hlavní cílový server. | Konfigurační server koordinuje komunikaci mezi místním prostředím a Azure a spravuje replikaci dat.
 **Procesový server:**  | Instaluje se ve výchozím nastavení společně s konfiguračním serverem. | Funguje jako replikační brána. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do úložiště Azure.<br/><br/> Procesový Server také nainstaluje službu mobility na servery, které chcete replikovat.<br/><br/> Jak vaše nasazení poroste, můžete přidat další samostatné procesové servery, které budou zpracovávat větší objemy replikačních přenosů.
 **Hlavní cílový server** | Instaluje se ve výchozím nastavení společně s konfiguračním serverem. | Zpracovává replikační data během navracení služeb z Azure po obnovení.<br/><br/> U rozsáhlých nasazení můžete přidat další samostatný hlavní cílový server pro navrácení služeb po obnovení.
**Replikované servery** | Služba mobility je nainstalovaná na každém serveru, který budete replikovat. | Doporučujeme, abyste povolili automatickou instalaci z procesového serveru. Případně můžete službu nainstalovat ručně nebo použít metodu automatizovaného nasazení, například System Center Configuration Manager.

**Z fyzické do architektury Azure**

![Komponenty](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Proces replikace

1. Nastavili jste nasazení, včetně místních a Azure komponent. V Recovery Services trezoru zadáte zdroj a cíl replikace, nastavíte konfigurační server, vytvoříte zásadu replikace a povolíte replikaci.
2. Počítače se replikují v souladu se zásadami replikace a počáteční kopie dat serveru se replikují do služby Azure Storage.
3. Po dokončení počáteční replikace se spustí replikace rozdílových změn do Azure. Sledované změny se pro jednotlivé počítače ukládají do souboru .hrl.
    - Počítače komunikují s konfiguračním serverem na portu HTTPS 443 příchozí, pro správu replikací.
    - Počítače odesílají data replikace na procesový Server na portu HTTPS 9443 příchozí (dá se upravit).
    - Konfigurační server orchestruje správu replikace s Azure přes odchozí port HTTPS 443.
    - Procesový server přijímá data ze zdrojového počítače, optimalizuje je a šifruje, a pak je odesílá do úložiště Azure přes odchozí port 443.
    - Pokud povolíte konzistenci napříč několika virtuálními počítači, budou spolu počítače v replikační skupině komunikovat přes port 20004. Konzistence více virtuálních počítačů znamená, že seskupíte víc virtuálních počítačů do replikační skupiny, v rámci které se sdílí body obnovení konzistentní vzhledem k selháním a konzistentní vzhledem k aplikacím, když dojde k převzetí služeb při selhání. To je užitečné, pokud je počítačích spuštěná stejná úloha a je třeba, aby zůstala konzistentní.
4. Provoz se přes internet replikuje do veřejných koncových bodů úložiště Azure. Alternativně můžete použít [veřejný partnerský vztah](../expressroute/expressroute-circuit-peerings.md#publicpeering) Azure ExpressRoute. Přenos replikačních dat přes síť site-to-site VPN z místního serveru do Azure není podporovaný.


**Z fyzického procesu replikace do Azure**

![Proces replikace](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proces převzetí služeb při selhání a navrácení služeb po obnovení

Po nastavení replikace a spuštění postupu pro zotavení po havárii (testovací převzetí služeb při selhání) ke kontrole, jestli všechno funguje podle očekávání, můžete v případě potřeby spustit převzetí služeb při selhání a navrácení služeb po obnovení. Všimněte si, že:

- Plánované převzetí služeb není podporované.
- Je nutné provést navrácení služeb po obnovení do místního virtuálního počítače VMware. To znamená, že budete potřebovat místní infrastrukturu VMware, a to i při replikaci místních fyzických serverů do Azure.
- Dojde k převzetí služeb při selhání jednoho počítače nebo k převzetí služeb při selhání více počítačů dohromady.
- Když spustíte převzetí služeb při selhání, vytvoří se virtuální počítače Azure z replikovaných dat ve službě Azure Storage.
- Po aktivaci počátečního převzetí služeb při selhání se potvrdili, že budete moct začít přistupovat ke úlohám z virtuálního počítače Azure.
- Až bude vaše místní lokalita opět dostupná, můžete službu navrátit.
- Musíte nastavit infrastrukturu navrácení služeb po obnovení, včetně:
    - **Dočasný procesový Server v Azure**: Pokud chcete navrátit služby po obnovení z Azure, nastavte virtuální počítač Azure, který bude fungovat jako procesový Server, a zpracujte replikaci z Azure. Tento virtuální počítač je možné po navrácení služeb po obnovení odstranit.
    - **Připojení VPN**: pro navrácení služeb po obnovení potřebujete připojení k síti VPN (nebo Azure ExpressRoute) ze sítě Azure do místní lokality.
    - **Samostatný hlavní cílový server**: ve výchozím nastavení se hlavní cílový server, který byl nainstalovaný s konfiguračním serverem, na místním virtuálním počítači VMware zpracovává navrácení služeb po obnovení. Pokud ale potřebujete navrátit navrácení velkých objemů dat, měli byste pro tento účel nastavit samostatný místní hlavní cílový server.
    - **Zásady navrácení služeb po obnovení:** Pro zpětnou replikaci do vaší místní lokality budete potřebovat zásady navrácení služeb. Tato služba se automaticky vytvořila při vytváření zásad replikace z místního prostředí do Azure.
    - **Infrastruktura VMware**: pro navrácení služeb po obnovení potřebujete infrastrukturu VMware. Nelze navrátit služby po obnovení v případě fyzického serveru.
- Po uvedení součástí dojde k navrácení služeb po obnovení ve třech fázích:
    - Fáze 1: znovu nastavte ochranu virtuálních počítačů Azure tak, aby se replikoval z Azure zpátky na místní virtuální počítače VMware.
    - Fáze 2: spuštění převzetí služeb při selhání v místní lokalitě.
    - Fáze 3: po navrácení služby po selhání se znovu povolí replikace.

**VMware navrácení služeb po obnovení z Azure**

![Navrácení služeb po obnovení](./media/physical-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Další kroky

Podle [tohoto kurzu](physical-azure-disaster-recovery.md) Povolte replikaci fyzického serveru do Azure.
