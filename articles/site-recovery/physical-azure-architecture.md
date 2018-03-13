---
title: "Fyzický server do architektury Azure replikace v Azure Site Recovery | Microsoft Docs"
description: "Tento článek obsahuje přehled součásti a architektura používá při replikace místní fyzických serverů do Azure se službou Azure Site Recovery"
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/09/2018
ms.author: raynew
ms.openlocfilehash: a8af2ee4a32925603d24aee2403ab504a0ca05a8
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="physical-server-to-azure-replication-architecture"></a>Fyzický server do architektury Azure replikace

Tento článek popisuje architektuře a procesech používá při replikaci, převzetí služeb při selhání a obnovení fyzických serverů systému Windows a Linux mezi místními servery a Azure, pomocí [Azure Site Recovery](site-recovery-overview.md) služby.


## <a name="architectural-components"></a>Komponenty architektury

Následující obrázek a tabulka poskytují souhrnné zobrazení komponenty používané pro fyzický server replikaci do Azure.  

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Azure** | Předplatné Azure, účet úložiště Azure a sítě Azure. | Replikovaná data z místně virtuálních počítačů je uložený v účtu úložiště. Virtuální počítače Azure vytvořené mají replikovaných dat při spuštění selhání z místního do Azure. Virtuální počítače Azure se připojí k virtuální síti Azure po svém vytvoření.
**Konfigurační server** | Jeden místní fyzický počítač nebo virtuální počítač VMware je nasazen na všechny místní spuštění součásti Site Recovery. Virtuální počítač se spustí konfigurační server, procesový server a hlavní cílový server. | Konfigurační server koordinuje komunikaci mezi místním prostředím a Azure a spravuje replikaci dat.
 **Procesový server:**  | Ve výchozím nastavení společně s konfiguračním serveru nainstalovaná. | Funguje jako replikační brána. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do úložiště Azure.<br/><br/> Procesový server také nainstaluje služba Mobility na serverech, které chcete replikovat.<br/><br/> S růstem nasazení můžete přidat další, samostatný proces serverů, aby zvládla větší objemy přenosů replikace.
 **Hlavní cílový server** | Ve výchozím nastavení společně s konfiguračním serveru nainstalovaná. | Zpracovává replikační data během navracení služeb z Azure po obnovení.<br/><br/> Nasazení ve velkých organizacích můžete přidat další, samostatné hlavní cílový server navrácení služeb po obnovení.
**Replikované servery** | Služba Mobility je nainstalovaná na každém serveru, který můžete replikovat. | Doporučujeme že povolit automatické instalace z procesového serveru. Případně můžete ručně nainstalovat službu, nebo použijte metodu automatického nasazení, jako je například System Center Configuration Manager.

**Fyzické Azure architektury**

![Komponenty](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Proces replikace

1. Nastavení nasazení, včetně místní a Azure součásti. V trezoru služeb zotavení určíte replikace zdroje a cíle, nastavení konfigurace serveru, vytvořte zásadu replikace a povolení replikace.
2. Replikovat počítače v souladu s zásady replikace a počáteční kopii dat serveru se replikují do úložiště Azure.
3. Po dokončení počáteční replikace začne replikace rozdílové změny do Azure. Sledované změny se pro jednotlivé počítače ukládají do souboru .hrl.
    - Počítače komunikují s konfiguračním serverem na portu 443 protokolu HTTPS příchozí pro správu replikace.
    - Počítače odesílat data replikace na procesový server na port HTTPS 9443 příchozí (lze upravit).
    - Konfigurační server orchestruje správu replikace s Azure přes odchozí port HTTPS 443.
    - Procesový server přijímá data ze zdrojového počítače, optimalizuje je a šifruje, a pak je odesílá do úložiště Azure přes odchozí port 443.
    - Pokud povolíte konzistenci napříč několika virtuálními počítači, budou spolu počítače v replikační skupině komunikovat přes port 20004. Konzistence více virtuálních počítačů znamená, že seskupíte víc virtuálních počítačů do replikační skupiny, v rámci které se sdílí body obnovení konzistentní vzhledem k selháním a konzistentní vzhledem k aplikacím, když dojde k převzetí služeb při selhání. To je užitečné, pokud je počítačích spuštěná stejná úloha a je třeba, aby zůstala konzistentní.
4. Provoz se přes internet replikuje do veřejných koncových bodů úložiště Azure. Alternativně můžete použít [veřejný partnerský vztah](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) Azure ExpressRoute. Přenos replikačních dat přes síť site-to-site VPN z místního serveru do Azure není podporovaný.


**Fyzické procesu Azure replikace**

![Proces replikace](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proces převzetí služeb při selhání a navrácení služeb po obnovení

Po nastavení replikace a spustíte postupu zotavení po havárii (převzetí služeb při selhání), a zkontrolovat, zda že vše funguje podle očekávání, můžete spustit převzetí služeb při selhání a navrácení služeb po obnovení je potřeba pro. Poznámky:

- Plánované převzetí služeb není podporované.
- Musí selhat, vrátit na VMware místní počítač. To znamená, že potřebujete místní infrastruktury VMware, i v případě, že replikace místní fyzických serverů do Azure.
- Selhání v jednom počítači, nebo vytvořit plány obnovení, pro převzetí služeb při selhání více počítačů současně.
- Při spuštění převzetí služeb při selhání se virtuální počítače Azure vytvořené z replikovaná data v úložišti Azure.
- Po spuštění počáteční převzetí služeb při selhání, můžete provést tak, aby přístup k zatížení z virtuálního počítače Azure.
- Až bude vaše místní lokalita opět dostupná, můžete službu navrátit.
- Budete muset nastavit infrastrukturu navrácení služeb po obnovení, včetně:
    - **Dočasný procesní server v Azure**: při selhání zpátky do Azure, musíte nastavit virtuální počítač Azure tak, aby fungoval jako server proces pro zpracování replikace z Azure. Tento virtuální počítač je možné po navrácení služeb po obnovení odstranit.
    - **Připojení k síti VPN**: pro navrácení služeb po obnovení, musíte připojení VPN (nebo Azure ExpressRoute) ze sítě Azure k místní lokalitě.
    - **Samostatné hlavní cílový server**: ve výchozím nastavení, hlavní cílový server, který byl nainstalován pomocí konfigurační server, na místní VMware virtuálních počítačů, zpracovává navrácení služeb po obnovení. Ale pokud budete potřebovat nezdaří zpět velké objemy přenosů, měli byste nastavit samostatný místní hlavní cílový server pro tento účel.
    - **Zásady navrácení služeb po obnovení:** Pro zpětnou replikaci do vaší místní lokality budete potřebovat zásady navrácení služeb. Byl automaticky vytvořen při vytváření zásady vaší replikace z místního do Azure.
    - **Infrastruktury VMware**: pro navrácení služeb po obnovení musíte infrastruktury VMware. Nelze navrátit služby po obnovení v případě fyzického serveru.
- Jakmile jsou součástí na místě, dojde k navrácení služeb po obnovení ve třech fázích:
    - Fáze 1: Nastavte znovu virtuální počítače Azure tak, aby se replikace z Azure zpátky na virtuální počítače VMware na místě.
    - Fáze 2: Spuštění převzetí služeb při selhání k místní lokalitě.
    - Fáze 3: Po úlohy se nezdařilo zpět, je-li znovu povolit replikaci.

**VMware navrácení služeb po obnovení z Azure**

![Navrácení služeb po obnovení](./media/physical-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Další postup

Postupujte podle [v tomto kurzu](physical-azure-disaster-recovery.md) k povolení fyzického serveru a Azure replikace.
