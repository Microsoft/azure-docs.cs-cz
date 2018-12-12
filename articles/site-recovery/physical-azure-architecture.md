---
title: Architektura pro zotavení po havárii fyzického serveru do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek obsahuje přehled komponent a architektury používané při zotavení po havárii místních fyzických serverů do Azure pomocí služby Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: raynew
ms.openlocfilehash: 3f31fa8d26b0fb5f247a0b4c8c65abd50c5bc1e4
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52865297"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Fyzický server architektury pro zotavení po havárii Azure

Tento článek popisuje, architektury a procesy používané při replikaci, převzetí služeb při selhání a obnovení fyzických serverů s Windows nebo Linuxem mezi místní lokalitou a Azure, pomocí [Azure Site Recovery](site-recovery-overview.md) služby.


## <a name="architectural-components"></a>Komponenty architektury

Následující tabulka a obrázek poskytuje souhrnný přehled komponenty používané pro replikaci fyzických serverů do Azure.  

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Azure** | Předplatné Azure, účet úložiště Azure a síť Azure. | Replikovaná data z místních virtuálních počítačů se ukládají v účtu úložiště. Virtuální počítače Azure se vytvoří s replikovanými daty při můžete v průběhu převzetí místních do Azure. Virtuální počítače Azure se připojí k virtuální síti Azure po svém vytvoření.
**Konfigurační server** | Jediný on-premises fyzický počítač nebo virtuální počítač VMware je nasazen na spustit všechny místní komponenty Site Recovery. Virtuální počítač běží konfigurační server, procesový server a hlavní cílový server. | Konfigurační server koordinuje komunikaci mezi místním prostředím a Azure a spravuje replikaci dat.
 **Procesový server:**  | Nainstalované ve výchozím nastavení spolu s konfiguračním serverem. | Funguje jako replikační brána. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do úložiště Azure.<br/><br/> Procesní server nainstaluje služba Mobility na serverech, které chcete replikovat.<br/><br/> Jak vaše nasazení poroste, můžete přidat další, samostatné procesní servery pro zpracování větší objemy přenosů replikace.
 **Hlavní cílový server** | Nainstalované ve výchozím nastavení spolu s konfiguračním serverem. | Zpracovává replikační data během navracení služeb z Azure po obnovení.<br/><br/> Pro velká nasazení můžete přidat další, samostatný hlavní cílový server navrácení služeb po obnovení.
**Replikované servery** | Služba Mobility je nainstalovaná na všech serverech, které replikujete. | Doporučujeme že povolit automatické instalace z procesového serveru. Případně můžete ručně nainstalovat službu nebo použít metodu automatického nasazení, jako je System Center Configuration Manager.

**Z fyzických prostředků do architektury Azure**

![Komponenty](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Proces replikace

1. Nastavíte nasazení, včetně místních komponent Azure. V trezoru služby Recovery Services zadejte zdroj a cíl replikace, nastavte konfigurační server, vytvořit zásady replikace a povolení replikace.
2. Replikace počítačů v souladu se zásadami replikace a počáteční kopie dat serveru se replikuje do úložiště Azure.
3. Po dokončení počáteční replikace začne probíhat rozdílová replikace do Azure. Sledované změny se pro jednotlivé počítače ukládají do souboru .hrl.
    - Počítače komunikovat s konfiguračním serverem na port HTTPS 443 příchozí kvůli správě replikace.
    - Počítače odesílají data k procesového serveru na příchozím portu HTTPS 9443 příchozí (je možné upravit).
    - Konfigurační server orchestruje správu replikace s Azure přes odchozí port HTTPS 443.
    - Procesový server přijímá data ze zdrojového počítače, optimalizuje je a šifruje, a pak je odesílá do úložiště Azure přes odchozí port 443.
    - Pokud povolíte konzistenci napříč několika virtuálními počítači, budou spolu počítače v replikační skupině komunikovat přes port 20004. Konzistence více virtuálních počítačů znamená, že seskupíte víc virtuálních počítačů do replikační skupiny, v rámci které se sdílí body obnovení konzistentní vzhledem k selháním a konzistentní vzhledem k aplikacím, když dojde k převzetí služeb při selhání. To je užitečné, pokud je počítačích spuštěná stejná úloha a je třeba, aby zůstala konzistentní.
4. Provoz se přes internet replikuje do veřejných koncových bodů úložiště Azure. Alternativně můžete použít [veřejný partnerský vztah](../expressroute/expressroute-circuit-peerings.md#publicpeering) Azure ExpressRoute. Přenos replikačních dat přes síť site-to-site VPN z místního serveru do Azure není podporovaný.


**Z fyzických prostředků do Azure replikaci**

![Proces replikace](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proces převzetí služeb při selhání a navrácení služeb po obnovení

Po nastavení replikace a spuštění postupu zotavení po havárii (testovací převzetí služeb) zkontrolujte, že všechno funguje podle očekávání, můžete spustit převzetí služeb při selhání a navrácení služeb po obnovení pro potřeby. Poznámky:

- Plánované převzetí služeb není podporované.
- Musíte navrátit služby po zpět do VMware v místním prostředí virtuálních počítačů. To znamená, že budete potřebovat infrastrukturu VMware v místním prostředí i v případě, že replikace místní fyzických serverů do Azure.
- Převzetí služeb při selhání jednoho počítače nebo vytvořit plány obnovení pro převzetí služeb při selhání více počítačů současně.
- Při spuštění převzetí služeb při selhání virtuálních počítačů Azure se vytvoří z replikovaná data ve službě Azure storage.
- Po aktivaci počáteční převzetí služeb při selhání, potvrdíte ho začít používat úlohu z virtuálního počítače Azure.
- Až bude vaše místní lokalita opět dostupná, můžete službu navrátit.
- Budete muset nastavit infrastrukturu navrácení služeb po obnovení, včetně:
    - **Dočasný procesní server v Azure**: selhání obnovení z Azure, můžete nastavit virtuální počítač Azure tak, aby fungoval jako procesový server, pro zpracování replikace z Azure. Tento virtuální počítač je možné po navrácení služeb po obnovení odstranit.
    - **Připojení k síti VPN**: K navrácení služeb po obnovení, potřebujete připojení VPN (nebo Azure ExpressRoute) ze sítě Azure k místní lokalitě.
    - **Samostatný hlavní cílový server**: ve výchozím nastavení, hlavní cílový server, který se nainstaloval s konfiguračním serverem, na VMware v místním prostředí virtuálních počítačů, zpracovává navrácení služeb po obnovení. Ale pokud potřebujete selhání zpět velký objem provozu, by měl nastavíte samostatný místní hlavní cílový server pro tento účel.
    - **Zásady navrácení služeb po obnovení:** Pro zpětnou replikaci do vaší místní lokality budete potřebovat zásady navrácení služeb. To se automaticky vytvořil při vytvoření zásad replikace z místního do Azure.
    - **Infrastruktura VMware**: budete potřebovat infrastrukturu VMware pro navrácení služeb po obnovení. Nelze navrátit služby po obnovení v případě fyzického serveru.
- Po součásti jsou na místě, dojde k navrácení služeb po obnovení ve třech fázích:
    - Fáze 1: Znovunastavení ochrany virtuálních počítačů Azure tak, aby se replikace z Azure zpět do místních virtuálních počítačů VMware.
    - Fáze 2: Spuštění převzetí služeb při selhání do místní lokality.
    - Fáze 3: Po úloh se nepodařilo vrátit, můžete znovu povolit replikaci.

**VMware navrácení služeb po obnovení z Azure**

![Navrácení služeb po obnovení](./media/physical-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Další postup

Postupujte podle [v tomto kurzu](physical-azure-disaster-recovery.md) umožňující fyzického serveru do Azure replikace.
