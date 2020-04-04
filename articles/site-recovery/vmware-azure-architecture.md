---
title: Architektura zotavení po havárii virtuálního počítače VMware v Azure Site Recovery
description: Tento článek obsahuje přehled součástí a architektury používaných při nastavování zotavení po havárii místních virtuálních virtuálních mandů VMware do Azure pomocí Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 77b4dd4c0efbe6d03e64865f18c2c87614aaecb5
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632528"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>Architektura zotavení po havárii v systému VMware to Azure

Tento článek popisuje architekturu a procesy používané při nasazení replikace zotavení po havárii, převzetí služeb při selhání a obnovení virtuálních počítačů VMware mezi místním webem VMware a Azure pomocí služby [Azure Site Recovery.](site-recovery-overview.md)


## <a name="architectural-components"></a>Komponenty architektury

Následující tabulka a grafika poskytují zobrazení na vysoké úrovni komponent používaných pro zotavení po havárii společnosti VMware do Azure.

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Azure** | Předplatné Azure, účet Azure Storage pro mezipaměť, spravovaný disk a síť Azure. | Replikovaná data z místních virtuálních počítačů se ukládají v úložišti Azure. Virtuální počítače Azure se vytvářejí s replikovanámi daty, když spustíte převzetí služeb při selhání z místního do Azure. Virtuální počítače Azure se připojí k virtuální síti Azure po svém vytvoření.
**Počítač konfiguračního serveru** | Jeden místní počítač. Doporučujeme jej spustit jako virtuální virtuální počítače VMware, který lze nasadit ze stažené šablony OVF.<br/><br/> Počítač spouští všechny místní součásti site recovery, které zahrnují konfigurační server, procesní server a hlavní cílový server. | **Konfigurační server**: Koordinuje komunikaci mezi místním a Azure a spravuje replikaci dat.<br/><br/> **Procesní server**: Ve výchozím nastavení je nainstalován na konfiguračním serveru. Přijímá data replikace; optimalizuje jej pomocí ukládání do mezipaměti, komprese a šifrování; a odešle ji do Služby Azure Storage. Procesový server také na virtuální počítače, které chcete replikovat, nainstaluje službu Azure Site Recovery Mobility Service a automaticky vyhledá místní virtuální počítače. Jak vaše nasazení roste, můžete přidat další samostatné procesní servery pro zpracování větších svazků replikačního provozu.<br/><br/> **Hlavní cílový server**: Ve výchozím nastavení je nainstalován na konfiguračním serveru. Zpracovává data replikace během navrácení služeb po selhání z Azure. U velkých nasazení můžete přidat další samostatný hlavní cílový server pro navrácení služeb po selhání.
**Servery VMware** | Virtuální počítačvsystému VMware jsou hostované na místních serverech vSphere ESXi. Pro správu hostitelů doporučujeme server vCenter. | Během nasazení site recovery přidáte servery VMware do trezoru služby Recovery Services.
**Replikované počítače** | Služba mobility se nainstaluje na každém virtuálním počítači VMware, který replikujete. | Doporučujeme povolit automatickou instalaci z procesního serveru. Případně můžete službu nainstalovat ručně nebo použít metodu automatického nasazení, například Nástroj configuration manager.

**Architektura replikace z VMware do Azure**

![Komponenty](./media/vmware-azure-architecture/arch-enhanced.png)


## <a name="replication-process"></a>Proces replikace

1. Když povolíte replikaci pro virtuální počítač, začne počáteční replikace do úložiště Azure pomocí zadaných zásad replikace. Je třeba počítat s následujícím:
    - Pro virtuální počítačvsystému VMware je replikace na úrovni bloku, téměř nepřetržitá, pomocí agenta služby Mobility spuštěného na virtuálním počítači.
    - Použijí se všechna nastavení zásad replikace:
        - **Prahová hodnota rpo**. Toto nastavení nemá vliv na replikaci. Pomáhá při sledování. Událost je vyvolána a volitelně e-mail odeslán, pokud aktuální RPO překročí limit, který zadáte.
        - **Uchování bodu obnovení**. Toto nastavení určuje, jak daleko v čase chcete přejít, když dojde k narušení. Maximální uchování prémiového úložiště je 24 hodin. Na standardním skladování je to 72 hodin. 
        - **Snímky konzistentní s aplikacemi**. Snímek konzistentní s aplikací lze pořizovat každých 1 až 12 hodin v závislosti na potřebách vaší aplikace. Snímky jsou standardní snímky objektů blob Azure. Agent mobility spuštěný na virtuálním počítači požaduje snímek VSS v souladu s tímto nastavením a záložky, které point-in-time jako konzistentní bod aplikace v datovém proudu replikace.

2. Provoz se replikuje do veřejných koncových bodů úložiště Azure přes internet. Alternativně můžete použít Azure ExpressRoute s [partnerským vztahem Microsoftu](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). Replikace provozu přes virtuální privátní síť (VPN) mezi lokalitami z místního webu do Azure není podporována.
3. Počáteční operace replikace zajišťuje, že do Azure se odešlou celá data v počítači v době povolení replikace. Po dokončení počáteční replikace začne replikace rozdílových změn do Azure. Sledované změny pro počítač jsou odesílány na procesový server.
4. Komunikace probíhá takto:

    - Virtuální počítače komunikují s místním konfiguračním serverem na příchozím portu HTTPS 443 pro správu replikace.
    - Konfigurační server orchestruje replikaci s Azure přes port HTTPS 443 odchozí.
    - Virtuální počítače odesílají data replikace na procesní server (spuštěný na konfiguračním serveru) na příchozím portu HTTPS 9443. Tento port lze upravit.
    - Procesní server přijímá replikační data, optimalizuje je a šifruje a odesílá je do úložiště Azure přes odchozí port 443.
5. Protokoly dat replikace se nejprve připonou v účtu úložiště mezipaměti v Azure. Tyto protokoly jsou zpracovány a data se ukládají na spravovaném disku Azure (nazývá se jako asr seed disk). Body obnovení jsou vytvořeny na tomto disku.

**Proces replikace VMware do Azure**

![Proces replikace](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="resynchronization-process"></a>Proces resynchronizace

1. Někdy během počáteční replikace nebo při přenosu rozdílových změn mohou existovat problémy s připojením k síti mezi zdrojovým počítačem ke zpracování serveru nebo mezi procesním serverem do Azure. Buď z nich může vést k selhání při přenosu dat do Azure na okamžik.
2. Chcete-li se vyhnout problémům s integritou dat a minimalizovat náklady na přenos dat, site recovery označí počítač pro resynchronizaci.
3. Počítač může být také označen pro resynchronizaci v situacích, jako je sledování zachovat konzistenci mezi zdrojovým počítačem a daty uloženými v Azure
    - Pokud stroj podstoupí odstavení
    - Pokud počítač prochází konfiguračnízměny, jako je změna velikosti disku (změna velikosti disku od 2 TB do 4 TB)
4. Resynchronizace odesílá do Azure jenom rozdílová data. Přenos dat mezi místním a Azure minimalizovat výpočetní kontrolní součty dat mezi zdrojovým počítačem a daty uloženými v Azure.
5. Ve výchozím nastavení je naplánováno automatické spuštění synchronizace mimo úřední hodiny. Pokud nechcete čekat na výchozí resynchronizaci mimo hodiny, můžete znovu synchronizovat virtuální hospodařící ručně. Chcete-li to provést, přejděte na portál Azure, vyberte virtuální počítač > **znovu synchronizovat**.
6. Pokud výchozí resynchronizace selže mimo úřední hodiny a ruční zásah je vyžadována, pak se vygeneruje chyba na konkrétním počítači na webu Azure Portal. Chybu můžete vyřešit a aktivovat synchronizaci ručně.
7. Po dokončení resynchronizace bude replikace rozdílových změn pokračovat.

## <a name="failover-and-failback-process"></a>Proces převzetí služeb při selhání a navrácení služeb po obnovení

Po nastavení replikace a spuštění masérky zotavení po havárii (test převzetí služeb při selhání) zkontrolujte, zda vše funguje podle očekávání, můžete spustit převzetí služeb při selhání a navrácení služeb po obnovení podle potřeby.

1. Spuštění selhání pro jeden počítač nebo vytvořit plány obnovení k převzetí služeb při selhání více virtuálních počítačů současně. Výhodou plánu obnovení spíše než převzetí služeb při selhání jednoho počítače patří:
    - Závislosti aplikací můžete modelovat zahrnutím všech virtuálních aplikací v celé aplikaci do jednoho plánu obnovení.
    - Můžete přidat skripty, runbooky Azure a pozastavit pro ruční akce.
2. Po aktivaci počáteční převzetí služeb při selhání, potvrdíte jej začít přístup k zatížení z virtuálního počítače Azure.
3. Když je primární místní lokalita opět k dispozici, můžete se připravit na vrácení služeb po selhání. Chcete-li navrácení služeb po selhání, je třeba nastavit infrastrukturu navrácení služeb po selhání, včetně:

    * **Dočasný procesní server v Azure:** Chcete-li z Azure navrácení zpět, nastavíte virtuální počítač Azure tak, aby fungoval jako procesní server pro zpracování replikace z Azure. Tento virtuální počítač je možné po navrácení služeb po obnovení odstranit.
    * **Připojení VPN**: Chcete-li zpětnou vazbu, potřebujete připojení VPN (nebo ExpressRoute) ze sítě Azure do místního webu.
    * **Samostatný hlavní cílový server**: Ve výchozím nastavení hlavní cílový server, který byl nainstalován s konfiguračním serverem v místním virtuálním počítači VMware, zpracovává navrácení služeb po selhání. Pokud potřebujete navrácení velkého množství provozu, nastavte pro tento účel samostatný místní hlavní cílový server.
    * **Zásady navrácení služeb po obnovení:** Pro zpětnou replikaci do vaší místní lokality budete potřebovat zásady navrácení služeb. Tato zásada se automaticky vytvoří při vytváření zásad replikace z místního do Azure.
4. Po komponenty jsou na místě, navrácení služeb po selhání dochází ve třech akcích:

    - Fáze 1: Znovu přizamkněte virtuální počítače Azure tak, aby se replikovaly z Azure zpět do místních virtuálních počítačů VMware.
    -  Fáze 2: Spusťte převzetí služeb při selhání na místní web.
    - Fáze 3: Po selhání úloh zpět, znovu povolit replikaci pro místní virtuální počítač.
    
 
**Navrácení služeb po selhání v Mware z Azure**

![Navrácení služeb po obnovení](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Další kroky

Podle [tohoto kurzu](vmware-azure-tutorial.md) povolte replikaci VMware do Azure.
