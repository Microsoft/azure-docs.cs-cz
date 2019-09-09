---
title: Architektura zotavení po havárii z VMware do Azure v Azure Site Recovery
description: Tento článek poskytuje přehled komponent a architektury, které se používají při nastavování zotavení po havárii místních virtuálních počítačů VMware do Azure pomocí Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 7c21b8d7a4a2723ddf10c4ac88f8b1ce4a5d6b47
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814586"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>Architektura zotavení po havárii z VMware do Azure

Tento článek popisuje architekturu a procesy používané při nasazení replikace pro zotavení po havárii, převzetí služeb při selhání a obnovení virtuálních počítačů VMware mezi místní lokalitou VMware a Azure pomocí služby [Azure Site Recovery](site-recovery-overview.md) .


## <a name="architectural-components"></a>Komponenty architektury

Následující tabulka a grafika obsahují podrobný pohled na součásti používané pro zotavení po havárii VMware do Azure.

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Azure** | Předplatné Azure Azure Storage účet pro mezipaměť, spravovaný disk a síť Azure. | Replikovaná data z místních virtuálních počítačů se ukládají ve službě Azure Storage. Virtuální počítače Azure se při spuštění převzetí služeb při selhání z místního prostředí do Azure vytvoří s replikovanými daty. Virtuální počítače Azure se připojí k virtuální síti Azure po svém vytvoření.
**Počítač konfiguračního serveru** | Jeden místní počítač. Doporučujeme spustit ho jako virtuální počítač VMware, který se dá nasadit ze stažené šablony OVF.<br/><br/> Počítač spustí všechny místní Site Recovery komponenty, mezi které patří konfigurační server, procesový Server a hlavní cílový server. | **Konfigurační server**: Koordinuje komunikaci mezi místními a Azure a spravuje replikaci dat.<br/><br/> **Procesový server:** Obvykle se instaluje na konfigurační server. Přijímá data replikace; optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování. a pošle ji Azure Storage. Procesový server také na virtuální počítače, které chcete replikovat, nainstaluje službu Azure Site Recovery Mobility Service a automaticky vyhledá místní virtuální počítače. Jak vaše nasazení poroste, můžete přidat další samostatné procesové servery, které budou zpracovávat větší objemy replikačních přenosů.<br/><br/> **Hlavní cílový server**: Obvykle se instaluje na konfigurační server. Zpracovává replikační data během navrácení služeb po obnovení z Azure. U rozsáhlých nasazení můžete přidat další samostatný hlavní cílový server pro navrácení služeb po obnovení.
**Servery VMware** | Virtuální počítače VMware se hostují na místních serverech vSphere ESXi. Pro správu hostitelů doporučujeme Server vCenter. | Během nasazení Site Recovery přidáte do trezoru Recovery Services servery VMware.
**Replikované počítače** | Na každém virtuálním počítači VMware, který budete replikovat, se nainstaluje služba mobility. | Doporučujeme, abyste povolili automatickou instalaci z procesového serveru. Alternativně můžete službu nainstalovat ručně nebo použít metodu automatizovaného nasazení, například System Center Configuration Manager.

**Architektura replikace z VMware do Azure**

![Komponenty](./media/vmware-azure-architecture/arch-enhanced.png)



## <a name="replication-process"></a>Proces replikace

1. Když pro virtuální počítač povolíte replikaci, začne počáteční replikace do Azure Storage s použitím zadaných zásad replikace. Vezměte na vědomí následující:
    - U virtuálních počítačů VMware je replikace na úrovni bloků téměř nepřetržitá a používá agenta služby mobility běžícího na virtuálním počítači.
    - Platí všechna nastavení zásad replikace:
        - **Prahová hodnota cíle RPO**. Toto nastavení nemá vliv na replikaci. Pomáhá s monitorováním. Dojde k vyvolání události a případně odeslání e-mailu, pokud aktuální cíl bodu obnovení překročí prahovou hodnotu, kterou určíte.
        - **Uchování bodu obnovení**. Toto nastavení určuje, jak daleko v čase chcete přejít, když dojde k přerušení. Maximální doba uchovávání Premium Storage je 24 hodin. Ve standardním úložišti je to 72 hodin. 
        - **Snímky konzistentní vzhledem k aplikacím**. Snímek konzistentní vzhledem k aplikacím může v závislosti na potřebách vaší aplikace trvat každých 1 až 12 hodin. Snímky jsou standardní snímky objektů BLOB v Azure. Agent mobility běžící na virtuálním počítači požádá o snímek služby Stínová kopie svazku (VSS) v souladu s tímto nastavením a záložky, které v daném okamžiku odpovídají bodu konzistentnímu vzhledem k aplikacím v datovém proudu replikace.

2. Provoz se replikuje do veřejných koncových bodů Azure Storage přes Internet. Alternativně můžete Azure ExpressRoute použít s [partnerským vztahem Microsoftu](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). Replikace provozu přes virtuální privátní síť (VPN) typu Site-to-Site z místní lokality do Azure se nepodporuje.
3. Po dokončení počáteční replikace se spustí replikace rozdílových změn do Azure. Sledované změny pro počítač se odesílají na procesový Server.
4. Komunikace probíhá takto:

    - Virtuální počítače komunikují s místním konfiguračním serverem na portu HTTPS 443 příchozí, pro správu replikací.
    - Konfigurační server orchestruje replikaci s Azure přes odchozí port HTTPS 443.
    - Virtuální počítače odesílají data replikace na procesový Server (spuštěný na počítači konfiguračního serveru) na portu HTTPS 9443 příchozí. Tento port lze změnit.
    - Procesový server přijímá data replikace, optimalizuje je a šifruje a odesílá je do Azure Storage přes odchozí port 443.
5. Data replikace zaprotokolují první půdu v účtu úložiště mezipaměti v Azure. Tyto protokoly se zpracují a data se ukládají na spravovaný disk Azure (nazývaný jako počáteční disk ASR). Body obnovení jsou vytvořeny na tomto disku.




**Proces replikace z VMware do Azure**

![Proces replikace](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proces převzetí služeb při selhání a navrácení služeb po obnovení

Po nastavení replikace a spuštění postupu zotavení po havárii (testovací převzetí služeb při selhání) ke kontrole, že vše funguje podle očekávání, můžete v případě potřeby spustit převzetí služeb při selhání a navrácení služeb po obnovení.

1. Nedaří se vám spustit pro jeden počítač nebo vytvořit plány obnovení pro převzetí služeb při selhání více virtuálních počítačů ve stejnou dobu. Výhodou plánu obnovení, nikoli jednoho převzetí služeb při selhání pro jednotlivé počítače, zahrnují:
    - Můžete modelovat závislosti aplikací včetně všech virtuálních počítačů v rámci aplikace v jednom plánu obnovení.
    - Můžete přidat skripty, Runbooky Azure a pozastavit pro ruční akce.
2. Po aktivaci počátečního převzetí služeb při selhání se potvrdili, že budete moct začít přistupovat ke úlohám z virtuálního počítače Azure.
3. Až bude vaše místní lokalita opět dostupná, můžete připravit na navrácení služeb po obnovení. Pro navrácení služeb po obnovení je potřeba nastavit infrastrukturu navrácení služeb po obnovení, včetně těchto:

    * **Dočasný procesový Server v Azure**: Pokud chcete navrátit služby po obnovení z Azure, nastavte virtuální počítač Azure tak, aby fungoval jako procesový Server pro zpracování replikace z Azure. Tento virtuální počítač je možné po navrácení služeb po obnovení odstranit.
    * **Připojení VPN**: K navrácení služeb po obnovení potřebujete připojení VPN (nebo ExpressRoute) ze sítě Azure do místní lokality.
    * **Samostatný hlavní cílový server**: Ve výchozím nastavení se hlavní cílový server, který byl nainstalovaný s konfiguračním serverem na místním virtuálním počítači VMware, zpracovává navrácení služeb po obnovení. Pokud potřebujete navrátit navrácení velkých objemů dat, nastavte pro tento účel samostatný místní hlavní cílový server.
    * **Zásada navrácení služeb po obnovení**: Pokud chcete provést replikaci zpátky na místní lokalitu, budete potřebovat zásadu navrácení služeb po obnovení. Tato zásada se automaticky vytvoří při vytváření zásad replikace z místního prostředí do Azure.
4. Po uvedení součástí dojde k navrácení služeb po obnovení ve třech akcích:

    - Fáze 1: Znovu nastavte ochranu virtuálních počítačů Azure tak, aby se replikují z Azure zpátky na místní virtuální počítače VMware.
    -  Fáze 2: Spusťte převzetí služeb při selhání na místní lokalitu.
    - Fáze 3: Po úspěšném dokončení úloh se znovu povolí replikace místních virtuálních počítačů.
    
 
**VMware navrácení služeb po obnovení z Azure**

![Navrácení služeb po obnovení](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Další postup

Pokud chcete povolit replikaci VMware do Azure, postupujte podle [tohoto kurzu](vmware-azure-tutorial.md) .
