---
title: Z VMware do architektury pro zotavení po havárii Azure ve službě Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek obsahuje přehled komponent a architektury používané při nastavování zotavení po havárii místních virtuálních počítačů VMware do Azure pomocí Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: raynew
ms.openlocfilehash: 58129ece0fb1b7f446e5f8d738b5d1135aa0256e
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212382"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>Z VMware do architektury pro zotavení po havárii Azure

Tento článek popisuje mezi místní lokality VMware a Azure s využitím architektury a procesy používané při nasazení replikace pro zotavení po havárii, převzetí služeb při selhání a obnovení virtuálních počítačů VMware (VM) [Azure Site Recovery](site-recovery-overview.md) servuce.


## <a name="architectural-components"></a>Komponenty architektury

Následující tabulka a obrázek poskytuje souhrnný přehled komponenty použité pro zotavení po havárii VMware do Azure.

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Azure** | Předplatné Azure, účet úložiště Azure a síť Azure. | Replikovaná data z místních virtuálních počítačů se ukládají v účtu úložiště. Virtuální počítače Azure se vytvoří s replikovanými daty při spuštění převzetí služeb při selhání z místního do Azure. Virtuální počítače Azure se připojí k virtuální síti Azure po svém vytvoření.
**Konfigurace počítače serveru** | Jeden místní počítač. Doporučujeme vám, že jej spouštíte jako virtuální počítač VMware, který je možné nasadit z ve stažené šabloně OVF.<br/><br/> Počítači běží všechny místní komponenty Site Recovery, mezi které patří konfigurační server, procesový server a hlavní cílový server. | **Konfigurační server**: koordinuje komunikaci mezi místním a Azure a spravuje replikaci dat.<br/><br/> **Procesový server**: ve výchozím nastavení na konfiguračním serveru nainstalované. Přijímá data replikace; optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování. a odesílá je do služby Azure Storage. Procesní server nainstaluje služba Mobility Azure Site Recovery na virtuálních počítačích, které chcete replikovat, a provádí automatické zjišťování místních počítačů. Jak vaše nasazení poroste, můžete přidat další, samostatné procesní servery pro zpracování větší objemy přenosů replikace.<br/><br/> **Hlavní cílový server**: ve výchozím nastavení na konfiguračním serveru nainstalované. Zpracovává replikační data během navrácení služeb po obnovení z Azure. Pro velká nasazení můžete přidat další, samostatný hlavní cílový server navrácení služeb po obnovení.
**Servery VMware** | Virtuální počítače VMware jsou hostované na místních serverech vSphere ESXi. Doporučujeme, abyste server vCenter pro správu hostitele. | Během nasazování Site Recovery servery VMware přidáte do trezoru služby Recovery Services.
**Replikované počítače** | Služba mobility je nainstalovaná na každý virtuální počítač VMware, které replikujete. | Doporučujeme vám, že povolíte automatické instalace z procesového serveru. Alternativně můžete ručně nainstalovat službu nebo použít metody automatického nasazení, jako je System Center Configuration Manager.

**Architektura replikace z VMware do Azure**

![Komponenty](./media/vmware-azure-architecture/arch-enhanced.png)



## <a name="replication-process"></a>Proces replikace

1. Když povolíte replikaci pro virtuální počítač, začíná počáteční replikace do úložiště Azure, pomocí zásad replikace zadaný. Je třeba počítat s následujícím:
    - Replikace pro virtuální počítače VMware, je na úrovni bloku, téměř nepřetržité, pomocí agenta služby Mobility spuštěného na virtuálním počítači.
    - Použijí se všechna nastavení zásad replikace:
        - **Prahová hodnota cíle bodu obnovení**. Toto nastavení nemá vliv na replikaci. Pomáhá s monitorováním. Vyvolá událost, a volitelně odešle e-mail, pokud aktuální cíl bodu obnovení překročí limit prahové hodnoty, který zadáte.
        - **Uchování bodu obnovení**. Toto nastavení určuje, jak daleko zpět v čase, který chcete vrátit, když dojde k přerušení. Maximální doba uchování ve službě premium storage je 24 hodin. Na úložiště úrovně standard je 72 hodin. 
        - **Snímky konzistentní s**. Snímky konzistentní s aplikací může trvat každých 1 až 12 hodin v závislosti na potřebách aplikace. Snímky jsou snímky objektů blob v Azure standardní. Agent Mobility spuštěný na virtuálním počítači požádá o snímek služby VSS v souladu se toto nastavení a záložek, které odkazují bodu v čase jako aplikace konzistentní vzhledem k aplikacím ve streamu replikace.

2. Provoz replikuje do služby Azure storage veřejné koncové body přes internet. Alternativně můžete použít Azure ExpressRoute s [veřejného partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#azure-public-peering). Přenos replikačních dat přes virtuální privátní síť site-to-site (VPN) z místní lokality do Azure se nepodporuje.
3. Po dokončení počáteční replikace začne probíhat rozdílová replikace do Azure. Sledované změny se pro počítač se odesílají na procesní server.
4. Komunikace se stane, následujícím způsobem:

    - Virtuální počítače komunikovat s místní konfigurační server na portu HTTPS 443 příchozí kvůli správě replikace.
    - Konfigurační server orchestruje replikaci pomocí Azure přes odchozí port HTTPS 443.
    - Příchozí data replikace k procesového serveru (běžícího na počítači serveru configuration) na příchozím portu HTTPS 9443 poslat virtuálních počítačů. Tento port je možné upravit.
    - Procesový server přijímá data replikace, optimalizuje je zašifruje a odesílá je do úložiště Azure přes port 443 odchozí.




**Z VMware do Azure replikaci**

![Proces replikace](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proces převzetí služeb při selhání a navrácení služeb po obnovení

Po nastavení replikace a spuštění postupu zotavení po havárii (testovací převzetí služeb) zkontrolujte, že vše funguje podle očekávání, můžete spustit převzetí služeb při selhání a navrácení služeb po obnovení pro potřeby.

1. Spustit selhání pro jeden počítač nebo vytvořit plány na převzetí služeb při selhání několika virtuálních počítačů ve stejnou dobu obnovení. Výhodou plánu obnovení spíše než převzetí služeb při selhání jednoho počítače patří:
    - Závislosti aplikace lze modelovat zahrnutím všech virtuálních počítačů napříč aplikace v jednom plánu obnovení.
    - Můžete přidat skripty, runbooky v Azure a pozastavit pro ruční akce.
2. Po aktivaci počáteční převzetí služeb při selhání, potvrdíte ho začít používat úlohu z virtuálního počítače Azure.
3. Pokud vaše místní lokalita opět dostupná, můžete připravit pro navrácení služeb po obnovení. Aby bylo možné po navrácení služeb po obnovení, budete muset nastavit infrastrukturu navrácení služeb po obnovení, včetně:

    * **Dočasný procesní server v Azure**: selhání obnovení z Azure, můžete nastavit virtuální počítač Azure tak, aby fungoval jako procesový server pro zpracování replikace z Azure. Tento virtuální počítač je možné po navrácení služeb po obnovení odstranit.
    * **Připojení k síti VPN**: K navrácení služeb po obnovení, potřebujete připojení VPN (nebo ExpressRoute) ze sítě Azure k místní lokalitě.
    * **Samostatný hlavní cílový server**: ve výchozím nastavení, hlavní cílový server, který se nainstaloval s konfiguračním serverem na virtuálních počítačů VMware v místním zpracovává navrácení služeb po obnovení. Pokud potřebujete selhání zpět velký objem provozu, nastavte samostatný místní hlavní cílový server pro tento účel.
    * **Zásady navrácení služeb po obnovení:** Pro zpětnou replikaci do vaší místní lokality budete potřebovat zásady navrácení služeb. Tato zásada se vytvoří automaticky při vytváření zásady replikace z místního do Azure.
4. Po součásti jsou na místě, dojde k navrácení služeb po obnovení v tři akce:

    - Fáze 1: Znovunastavení ochrany virtuálních počítačů Azure tak, aby se replikace z Azure zpět do místních virtuálních počítačů VMware.
    -  Fáze 2: Spuštění převzetí služeb při selhání do místní lokality.
    - Fáze 3: Po úloh se nepodařilo vrátit, povolíte replikaci místních virtuálních počítačů.
    
 
**VMware navrácení služeb po obnovení z Azure**

![Navrácení služeb po obnovení](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Další postup

Postupujte podle [v tomto kurzu](vmware-azure-tutorial.md) k povolení z VMware do Azure replikace.
