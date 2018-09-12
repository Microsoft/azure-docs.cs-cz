---
title: Převzetí služeb při selhání a navrácení služeb po obnovení fyzických serverů replikovaných do Azure pomocí Site Recovery | Dokumentace Microsoftu
description: Další informace o převzetí služeb při selhání fyzických serverů do Azure a navrácení služeb po obnovení místní lokality pomocí Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 09/11/2018
ms.author: raynew
ms.openlocfilehash: 4036ab6e62f4738f4b2906eb7571dc5d0e972988
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391143"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Převzetí služeb při selhání a navrácení služeb po obnovení fyzických serverů replikovaných do Azure

Tento kurz popisuje, jak převzít služby při selhání fyzického serveru do Azure. Po přes, převezme služby při server zpět do místní lokality až bude k dispozici.

## <a name="preparing-for-failover-and-failback"></a>Příprava na převzetí služeb při selhání a navrácení služeb po obnovení

Fyzických serverů replikovaných do Azure pomocí Site Recovery můžete předat jenom zpět jako virtuální počítače VMware. K navrácení služeb po obnovení budete potřebovat infrastrukturu VMware.

Převzetí služeb při selhání a navrácení služeb po obnovení probíhá ve čtyřech fázích:

1. **Převzetí služeb při selhání do Azure:** Převezmou se služby při selhání počítačů z místní lokality do Azure.
2. **Znovunastavení ochrany virtuálních počítačů Azure**: znovunastavení ochrany virtuálních počítačů Azure tak, aby se začaly replikovat zpět do místních virtuálních počítačů VMware.
3. **Převzetí služeb při selhání do místní lokality:** Spustí se převzetí služeb při selhání, které zajistí navrácení služeb po obnovení z Azure.
4. **Znovunastavení ochrany místních virtuálních počítačů**: po zpět navrácení dat znovunastavení ochrany virtuálních počítačů VMware v místním při selhání zpátky do tak, aby se začaly replikovat do Azure.

## <a name="verify-server-properties"></a>Zkontrolujte vlastnosti serveru

Zkontrolujte vlastnosti serveru a ujistěte se, že je v souladu s [požadavky služby Azure](vmware-physical-azure-support-matrix.md#replicated-machines) pro virtuální počítače Azure.

1. V **chráněné položky**, klikněte na tlačítko **replikované položky**a vyberte počítač.

2. V **replikovaná položka** podokně je souhrn informací o počítači, stav, a nejnovější dostupné body obnovení. Kliknutím na **Vlastnosti** zobrazíte další podrobnosti.
3. V části **Výpočty a síť** můžete upravit název Azure, skupinu prostředků, cílovou velikost, [skupinu dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md) a [nastavení spravovaného disku](#managed-disk-considerations).
4. Můžete zobrazit a upravit nastavení sítě, včetně sítě a podsítě, do které se virtuální počítače Azure umístí po převzetí služeb při selhání, a IP adresy, která se jim přiřadí.
5. V **disky**, zobrazí se informace o počítači operačního systému a datové disky.

## <a name="run-a-failover-to-azure"></a>Spuštění převzetí služeb při selhání do Azure

1. V části **Nastavení** > **Replikované položky** klikněte na počítač a pak na **Převzetí služeb při selhání**.
2. V části **Převzetí služeb při selhání** vyberte **Bod obnovení**, ke kterému se mají převzít služby při selhání. Můžete použít jednu z následujících možností:
   - **Nejnovější**: Tato možnost nejprve zpracuje veškerá data odeslaná do Site Recovery. Poskytuje nejnižší cíl bodu obnovení (RPO), protože se virtuální počítač Azure vytvoří teprve tehdy, až převzetí služeb při selhání bude mít veškerá data, která se do Site Recovery replikovala při aktivaci převzetí služeb při selhání.
   - **Nejnovější zpracovaný**: Tato možnost převezme služby při selhání počítače k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery. Tato možnost poskytuje nízkou plánovanou dobu obnovení (RTO), protože se neztrácí žádný čas zpracováním nezpracovaných dat.
   - **Nejnovější konzistentní vzhledem k**: Tato možnost převezme služby při selhání počítače do bodu nejnovější konzistentní vzhledem k obnovení zpracovanému službou Site Recovery.
   - **Vlastní:** Zadáte vlastní bod obnovení.

3. Vyberte **před spuštěním převzetí služeb při selhání vypnout počítač** Pokud chcete, aby Site Recovery se pokouší vypnout zdrojový počítač, před aktivací převzetí služeb. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
4. Pokud jste připraveni na připojení k virtuálnímu počítači Azure, připojte se k němu, abyste ho po převzetí služeb při selhání ověřili.
5. Po ověření můžete převzetí služeb při selhání **Potvrdit**. Tím se odstraní všechny dostupné body obnovení.

> [!WARNING]
> Nepřerušujte převzetí služeb při selhání v průběhu. Před zahájením převzetí služeb při selhání, zastaví se replikace počítačů. Pokud zrušíte převzetí služeb při selhání, zastaví, ale tento počítač se znovu nereplikuje.
> Pro fyzické servery převzetí služeb při selhání další zpracování, může trvat přibližně osm až deset minut na dokončení.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Pokud chcete připojit k virtuálním počítačům Azure po převzetí služeb při selhání pomocí protokolu RDP nebo SSH, postupujte podle požadavků shrnutých v tabulce [tady](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

Postupujte podle kroků popsaných [tady](site-recovery-failover-to-azure-troubleshoot.md) problémy řešit jakékoli připojení zveřejnit převzetí služeb při selhání.

## <a name="create-a-process-server-in-azure"></a>Vytvoření procesového serveru v Azure

Procesový server přijímá data z virtuálního počítače Azure a odesílá je do místní lokality. Síť s nízkou latencí je vyžadován mezi procesovým serverem a chráněný počítač.

- Pokud používáte připojení Azure ExpressRoute, můžete pro účely testování použít místní procesový server, který je automaticky nainstalovaný na konfiguračním serveru.
- Pokud používáte připojení VPN nebo pokud spouštíte navrácení služeb po obnovení v produkčním prostředí, musíte nastavit virtuální počítač Azure jako procesový server založený na Azure pro navrácení služeb po obnovení.
- Postupujte podle pokynů v [v tomto článku](vmware-azure-set-up-process-server-azure.md) nastavit procesový server v Azure.

## <a name="configure-the-master-target-server"></a>Konfigurace hlavního cílového serveru

Ve výchozím nastavení hlavní cílový server přijímá data navrácení služeb po obnovení. Běží na místní konfigurační server.

- Pokud virtuální počítač VMware, ke kterému jste navrácení služeb po obnovení je na hostiteli ESXi, kterého spravuje VMware vCenter Server, hlavní cílový server musí mít přístup k úložišti dat Virtuálního počítače (VMDK), zapisovat replikovaná data na disky virtuálního počítače. Ujistěte se, že je úložiště dat virtuálního počítače připojené k hostiteli hlavního cílového serveru a že umožňuje přístup pro čtení i zápis.
- Pokud hostitele ESXi, který není spravován serverem vCenter, služba Site Recovery vytvoří nový virtuální počítač během opětovného nastavování ochrany. Na hostiteli ESX, na kterém vytvoříte hlavní cílový virtuální počítač se vytvoří virtuální počítač. Pevný disk virtuálního počítače musí být v úložišti dat přístupném pro hostitele, na kterém běží hlavní cílový server.
- Pro fyzické počítače, které navrácení služeb po obnovení byste měli dokončit zjišťování hostitele, na kterém je spuštěný hlavního cílového serveru, před znovunastavením ochrany počítač.
- Další možností, pokud již existuje místní virtuální počítač pro navrácení služeb po obnovení, je ho odstranit, než se pustíte navrácení služeb po obnovení. Navrácení služeb po obnovení pak vytvoří nový virtuální počítač na stejném hostiteli, jako je hostitel ESX hlavního cílového serveru. Když provedete navrácení služeb po obnovení do alternativního umístění, data se obnoví do stejného úložiště dat a stejného hostitele ESX, které používá místní hlavní cílový server.
- Na hlavním cílovém serveru nemůžete používat funkci Storage vMotion. Jinak navrácení služeb po obnovení nebude fungovat, protože nebude mít přístup k diskům. Vylučte hlavní cílové servery ze svého seznamu vMotion.

## <a name="reprotect-azure-vms"></a>Znovunastavení ochrany virtuálních počítačů Azure

Tento postup předpokládá, že místní virtuální počítač není dostupný a znovu nastavujete ochranu do alternativního umístění.

1. V části **Nastavení** > **Replikované položky** klikněte pravým tlačítkem na virtuální počítač, u kterého došlo k převzetí služeb při selhání, a pak na **Znovu nastavit ochranu**.
2. V části **Znovu nastavit ochranu** ověřte, že je vybraná možnost **Z Azure do místní lokality**.
3. Zadejte místní hlavní cílový server a procesový server.

4. V části **Úložiště dat** vyberte úložiště dat hlavního cílového serveru, do kterého chcete obnovit disky v místním prostředí. Tuto možnost použijte v případě, že se místní virtuální počítač odstranil a potřebujete vytvořit nové disky. Pokud již disky existují, toto nastavení se ignoruje, ale musíte zadat nějakou hodnotu.
5. Vyberte jednotku hlavního cílového serveru pro uchovávání dat. Automaticky se vyberou zásady navrácení služeb po obnovení.
6. Kliknutím na **OK** zahajte opětovné nastavování ochrany. Úloha začne replikovat virtuální počítač z Azure do místní lokality. Průběh můžete sledovat na kartě **Úlohy**.

> [!NOTE]
> Pokud chcete virtuální počítač Azure zotavit do existujícího místního virtuálního počítače, připojte úložiště dat místního virtuálního počítače s přístupem ke čtení i zápisu k hostiteli ESXi hlavního cílového serveru.


## <a name="run-a-failover-from-azure-to-on-premises"></a>Spuštění převzetí služeb při selhání z Azure do místní lokality

Ke zpětné replikaci do místní lokality se používá zásada navrácení služeb po obnovení. Tato zásada se vytvoří automaticky při vytváření zásady replikace pro replikaci do Azure:

- Tato zásada se automaticky přidruží ke konfiguračnímu serveru.
- Tuto zásadu není možné upravit.
- Zásada má tyto hodnoty:
    - Prahová hodnota cíle bodu obnovení (RPO) = 15 minut
    - Uchovávání bodu obnovení = 24 hodin
    - Frekvence pořizování snímků konzistentních vzhledem k aplikacím = 60 minut

Spusťte převzetí služeb při selhání následujícím způsobem:

1. Na stránce **Replikované položky** klikněte pravým tlačítkem na počítač a pak na **Neplánované převzetí služeb při selhání**.
2. V části **Potvrdit převzetí služeb při selhání** ověřte, že převzetí služeb při selhání směřuje z Azure.

3. Vyberte bod obnovení, který chcete pro převzetí služeb při selhání použít. Bod obnovení konzistentní vzhledem k aplikacím je starší než nejnovější bod v čase, což způsobí ztrátu některých dat. Po spuštění převzetí služeb při selhání Site Recovery vypne virtuální počítače Azure a spustí místní virtuální počítač. Dojde k určitému výpadku, takže zvolte vhodnou dobu.
4. Klikněte pravým tlačítkem na počítač a pak klikněte na **Potvrdit**. Tím se aktivuje úloha, která odebere virtuální počítače Azure.
5. Ověřte, že se virtuální počítače Azure vyply podle očekávání.


## <a name="reprotect-on-premises-machines-to-azure"></a>Znovunastavení ochrany místních počítačů do Azure

Data by teď měla být zpět ve vaší místní lokalitě, ale nereplikují se do Azure. Replikaci do Azure můžete znovu spustit následujícím způsobem:

1. V části trezor > **Nastavení** > **Replikované položky** vyberte virtuální počítače, u kterých došlo k navrácení služeb po obnovení, a klikněte na **Znovu nastavit ochranu**.
2. Vyberte procesový server, který se používá k odesílání replikovaných dat do Azure, a klikněte na **OK**.

Po znovunastavení ochrany se virtuální počítač replikuje zpět do Azure a podle potřeby můžete spustit převzetí služeb při selhání.
