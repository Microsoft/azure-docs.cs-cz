---
title: VMware do Azure replikace architektury v Azure Site Recovery | Microsoft Docs
description: Tento článek obsahuje přehled součásti a architektura použít při replikaci místní virtuální počítače VMware do Azure s Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 01/15/2018
ms.author: raynew
ms.openlocfilehash: 4dd9d4f5f26d70f9130f48e2bf40ce71943a6c6d
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2018
ms.locfileid: "29677452"
---
# <a name="vmware-to-azure-replication-architecture"></a>Z VMware do Azure replikace architektura

Tento článek popisuje architektuře a procesech používá při replikaci, převzetí služeb při selhání a obnovení virtuálních počítačů VMware (VM) mezi místními servery VMware a Azure pomocí [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Komponenty architektury

Následující obrázek a tabulka poskytují souhrnné zobrazení komponenty používané pro replikaci VMware do Azure.

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Azure** | Předplatné Azure, účet úložiště Azure a sítě Azure. | Replikovaná data z místně virtuálních počítačů je uložený v účtu úložiště. Virtuální počítače Azure vytvořené mají replikovaných dat při spuštění převzetí služeb při selhání z místního do Azure. Virtuální počítače Azure se připojí k virtuální síti Azure po svém vytvoření.
**Konfigurace počítače serveru** | Jeden na místním počítači. Doporučujeme, abyste spustili ho jako virtuální počítače VMware, které se dá nasadit z ve stažené šabloně OVF.<br/><br/> Počítač se spustí všechny součásti Site Recovery na místě, například konfigurační server, procesový server a hlavní cílový server. | **Konfigurační server**: koordinuje komunikaci mezi místními a Azure a spravuje replikaci dat.<br/><br/> **Procesový server**: nainstalované ve výchozím nastavení na konfiguračním serveru. Obdrží data replikace; optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování; a odešle ji do úložiště Azure. Procesový server nainstaluje taky službu Azure Site Recovery Mobility na virtuálních počítačích, které chcete replikovat, a provádí automatického zjišťování počítačů místně. S růstem nasazení můžete přidat další, samostatný proces serverů, aby zvládla větší objemy přenosů replikace.<br/><br/> **Hlavní cílový server**: nainstalované ve výchozím nastavení na konfiguračním serveru. Zpracovává replikační data během navrácení služeb po obnovení z Azure. Nasazení ve velkých organizacích můžete přidat další, samostatné hlavní cílový server navrácení služeb po obnovení.
**Servery VMware** | Virtuální počítače VMware jsou hostovány na místních vSphere ESXi serverů. Doporučujeme, abyste server vCenter pro správu hostitele. | Během nasazování Site Recovery přidat servery VMware do trezoru služeb zotavení.
**Replikované počítače** | Služba mobility je nainstalovaná na jednotlivé virtuální počítače VMware, kterého budete replikovat. | Doporučujeme vám, že povolíte automatické instalace z procesového serveru. Alternativně můžete nainstalovat službu ručně nebo použít metodu automatického nasazení, jako je například System Center Configuration Manager.

**Architektura replikace z VMware do Azure**

![Komponenty](./media/concepts-vmware-to-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Proces replikace

1. Příprava Azure prostředky a místní komponenty.
2. V trezoru služeb zotavení zadejte nastavení zdroje replikace. V rámci tohoto procesu se nastavte místní konfigurační server. Pokud chcete nasadit tento server jako virtuální počítače VMware, stažení připravené šablony OVF a naimportujte ho do VMware vytvořte virtuální počítač.
3. Zadejte nastavení replikace cíl, vytvořte zásadu replikace a povolení replikace pro virtuální počítače VMware.
4. Replikovat počítače v souladu s zásady replikace a počáteční kopii data virtuálního počítače se replikují do úložiště Azure.
5. Po dokončení počáteční replikace začne replikace rozdílové změny do Azure. Sledované změny se pro jednotlivé počítače ukládají do souboru .hrl.

    * Počítače komunikují s konfiguračním serverem na portu 443 protokolu HTTPS příchozí pro správu replikace.

    * Počítače odesílat data replikace na procesový server na port HTTPS 9443 příchozí (lze upravit).

    * Konfigurační server orchestruje správu replikace s Azure přes odchozí port HTTPS 443.

    * Procesový server přijímá data ze zdrojového počítače, optimalizuje je zašifruje a odešle ji do úložiště Azure přes port 443 odchozí.

    * Pokud povolíte konzistenci napříč několika virtuálními počítači, budou spolu počítače v replikační skupině komunikovat přes port 20004. Konzistence více virtuálních počítačů znamená, že seskupíte víc virtuálních počítačů do replikační skupiny, v rámci které se sdílí body obnovení konzistentní vzhledem k selháním a konzistentní vzhledem k aplikacím, když dojde k převzetí služeb při selhání. Tato metoda je užitečná, pokud počítače běží stejné zatížení a musí být v souladu.

6. Provoz replikuje do úložiště Azure veřejné koncové body přes internet. Alternativně můžete použít [veřejný partnerský vztah](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) Azure ExpressRoute. Replikace provozu přes virtuální privátní síť site-to-site (VPN) z místního serveru do Azure se nepodporuje.


**Z VMware do Azure replikační proces**

![Proces replikace](./media/concepts-vmware-to-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proces převzetí služeb při selhání a navrácení služeb po obnovení

Po nastavení replikace a spustíte postupu zotavení po havárii (převzetí služeb při selhání), zkontrolujte, zda vše funguje podle očekávání, můžete spustit převzetí služeb při selhání a navrácení služeb po obnovení je potřeba pro.

1. Můžete převzít jeden počítač nebo vytvořit plány obnovení pro převzít služby několika virtuálními počítači.

2. Při spuštění převzetí služeb při selhání se virtuální počítače Azure vytvořené z replikovaná data v úložišti Azure.

3. Po spuštění počáteční převzetí služeb při selhání, můžete provést tak, aby přístup k zatížení z virtuálního počítače Azure.

Až bude vaše místní lokalita opět dostupná, můžete službu navrátit.
1. Budete muset nastavit infrastrukturu navrácení služeb po obnovení, včetně:

    * **Dočasný procesní server v Azure**: při selhání zpátky do Azure, musíte nastavit virtuální počítač Azure tak, aby fungoval jako server proces pro zpracování replikace z Azure. Tento virtuální počítač je možné po navrácení služeb po obnovení odstranit.

    * **Připojení k síti VPN**: pro navrácení služeb po obnovení, musíte připojení VPN (nebo ExpressRoute) ze sítě Azure k místní lokalitě.

    * **Samostatné hlavní cílový server**: ve výchozím nastavení, který byl nainstalován pomocí konfigurační server na virtuálních počítačů VMware na místní hlavní cílový server zpracovává navrácení služeb po obnovení. Pokud potřebujete selhání zpět velké objemy přenosů, nastavte samostatný místní hlavní cílový server pro tento účel.

    * **Zásady navrácení služeb po obnovení:** Pro zpětnou replikaci do vaší místní lokality budete potřebovat zásady navrácení služeb. Tato zásada byla vytvořena automaticky při vytvoření vaší zásady replikace z místního do Azure.
2. Jakmile jsou součástí na místě, dojde k navrácení služeb po obnovení ve třech fázích:

    a. Fáze 1: Nastavte znovu virtuální počítače Azure tak, aby se replikace z Azure zpátky na virtuální počítače VMware na místě.

    b. Fáze 2: Spuštění převzetí služeb při selhání k místní lokalitě.

    c. Fáze 3: Po úlohy se nezdařilo zpět, je-li znovu povolit replikaci pro virtuální počítače na místě.

**VMware navrácení služeb po obnovení z Azure**

![Navrácení služeb po obnovení](./media/concepts-vmware-to-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Další postup

Postupujte podle [v tomto kurzu](tutorial-vmware-to-azure.md) povolit VMware do Azure replikace.
