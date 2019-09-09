---
title: Převzetí služeb při selhání a převzetí služeb při selhání fyzických serverů pro zotavení po havárii do Azure pomocí Site Recovery | Microsoft Docs
description: Přečtěte si, jak převzít služby při selhání fyzických serverů do Azure a navrácení služeb po obnovení do místní lokality pro zotavení po havárii pomocí Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 49b61423b33282be7f0ace52c2a164d52ba20314
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814410"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Převzetí služeb při selhání a obnovení fyzických serverů replikovaných do Azure

V tomto kurzu se dozvíte, jak převzít služby při selhání fyzického serveru do Azure. Po převzetí služeb při selhání dojde k selhání serveru zpátky do místní lokality, až bude k dispozici.

## <a name="preparing-for-failover-and-failback"></a>Příprava na převzetí služeb při selhání a navrácení služeb po obnovení

Fyzické servery replikované do Azure pomocí Site Recovery můžou navracet služby po obnovení jenom jako virtuální počítače VMware. K navrácení služeb po obnovení potřebujete infrastrukturu VMware.

Převzetí služeb při selhání a navrácení služeb po obnovení probíhá ve čtyřech fázích:

1. **Převzetí služeb při selhání do Azure**: Z místní lokality do Azure převezme selhání počítačů.
2. Znovu **zapnout ochranu virtuálních počítačů Azure**: Znovu nastavte ochranu virtuálních počítačů Azure, aby se začaly replikovat zpátky na místní virtuální počítače VMware.
3. **Převzetí služeb při selhání místním nasazením**: Spusťte převzetí služeb při selhání, abyste mohli navrátit služby po obnovení z Azure.
4. Znovu zapnout **ochranu místních virtuálních počítačů**: Po neúspěšném obnovení dat znovu nastavte ochranu místních virtuálních počítačů VMware, na které jste se nevrátili, aby se začaly replikovat do Azure.

## <a name="verify-server-properties"></a>Ověřit vlastnosti serveru

Ověřte vlastnosti serveru a ujistěte se, že jsou v souladu s [požadavky Azure](vmware-physical-azure-support-matrix.md#replicated-machines) na virtuální počítače Azure.

1. V části **chráněné položky**klikněte na **replikované položky**a vyberte počítač.

2. V podokně **Replikovaná položka** se zobrazí souhrn informací o počítači, stav a nejnovější dostupné body obnovení. Kliknutím na **Vlastnosti** zobrazíte další podrobnosti.
3. V části **výpočty a síť**můžete upravit název Azure, skupinu prostředků, cílovou velikost, skupinu [dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md)a nastavení spravovaného disku.
4. Můžete zobrazit a upravit nastavení sítě, včetně sítě a podsítě, do které se virtuální počítače Azure umístí po převzetí služeb při selhání, a IP adresy, která se jim přiřadí.
5. V části **disky**můžete zobrazit informace o operačním systému počítače a datových discích.

## <a name="run-a-failover-to-azure"></a>Spuštění převzetí služeb při selhání do Azure

1. V části **Nastavení** > **Replikované položky** klikněte na počítač a pak na **Převzetí služeb při selhání**.
2. V části **Převzetí služeb při selhání** vyberte **Bod obnovení**, ke kterému se mají převzít služby při selhání. Můžete použít jednu z následujících možností:
   - **Nejnovější**: Tato možnost nejprve zpracuje všechna data odesílaná do Site Recovery. Poskytuje nejnižší cíl bodu obnovení (RPO), protože se virtuální počítač Azure vytvoří teprve tehdy, až převzetí služeb při selhání bude mít veškerá data, která se do Site Recovery replikovala při aktivaci převzetí služeb při selhání.
   - **Poslední zpracovaná**: Tato možnost převezme počítač k nejnovějšímu bodu obnovení zpracovanému pomocí Site Recovery. Tato možnost poskytuje nízkou plánovanou dobu obnovení (RTO), protože se neztrácí žádný čas zpracováním nezpracovaných dat.
   - **Nejnovější konzistentní vzhledem k aplikacím**: Tato možnost převezme počítače na nejnovější bod obnovení konzistentní vzhledem k aplikacím zpracovaného Site Recovery.
   - **Vlastní**: Zadejte bod obnovení.

3. Vyberte **vypnout počítač před spuštěním převzetí služeb při selhání** , pokud chcete, aby se Site Recovery před aktivací převzetí služeb při selhání ukončil zdrojový počítač. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
4. Pokud jste připraveni na připojení k virtuálnímu počítači Azure, připojte se k němu, abyste ho po převzetí služeb při selhání ověřili.
5. Po ověření můžete převzetí služeb při selhání **Potvrdit**. Tím se odstraní všechny dostupné body obnovení.

> [!WARNING]
> Nemůžete zrušit probíhající převzetí služeb při selhání. Před zahájením převzetí služeb při selhání se zastaví replikace počítače. Pokud zrušíte převzetí služeb při selhání, zastaví se, ale počítač se znovu nereplikuje.
> U fyzických serverů může dokončení dalších zpracování převzetí služeb při selhání trvat přibližně osm až deset minut.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Pokud se chcete připojit k virtuálním počítačům Azure pomocí protokolů RDP/SSH po převzetí služeb při selhání, musíte dodržet požadavky shrnuté v této [tabulce](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

Při řešení problémů s připojením po převzetí služeb při selhání použijte [zde](site-recovery-failover-to-azure-troubleshoot.md) popsaný postup.

## <a name="create-a-process-server-in-azure"></a>Vytvoření procesového serveru v Azure

Procesový server přijímá data z virtuálního počítače Azure a odesílá je do místní lokality. Mezi procesovým serverem a chráněným počítačem se vyžaduje síť s nízkou latencí.

- Pokud používáte připojení Azure ExpressRoute, můžete pro účely testování použít místní procesový server, který je automaticky nainstalovaný na konfiguračním serveru.
- Pokud používáte připojení VPN nebo pokud spouštíte navrácení služeb po obnovení v produkčním prostředí, musíte nastavit virtuální počítač Azure jako procesový server založený na Azure pro navrácení služeb po obnovení.
- Podle pokynů v [tomto článku](vmware-azure-set-up-process-server-azure.md) nastavte procesový Server v Azure.

## <a name="configure-the-master-target-server"></a>Konfigurace hlavního cílového serveru

Hlavní cílový server ve výchozím nastavení obdrží data navrácení služeb po obnovení. Spouští se na místním konfiguračním serveru.

- Pokud je virtuální počítač VMware, na který jste nastavili navrácení služeb po obnovení, na hostiteli ESXi spravovaném VMware vCenter Server, musí mít hlavní cílový server přístup k úložišti dat virtuálního počítače (VMDK), aby mohl zapisovat replikovaná data na disky virtuálních počítačů. Ujistěte se, že je úložiště dat virtuálního počítače připojené k hostiteli hlavního cílového serveru a že umožňuje přístup pro čtení i zápis.
- Pokud hostitel ESXi nespravuje Server vCenter, služba Site Recovery Service vytvoří nový virtuální počítač během opětovné ochrany. Virtuální počítač se vytvoří na hostiteli ESX, na kterém vytvoříte hlavní cílový virtuální počítač. Pevný disk virtuálního počítače musí být v úložišti dat přístupném pro hostitele, na kterém běží hlavní cílový server.
- U fyzických počítačů, u kterých dojde k navrácení služeb po obnovení, byste měli dokončit zjišťování hostitele, na kterém je spuštěný hlavní cílový server, než budete moct počítač znovu zapnout.
- Další možnost – Pokud už místní virtuální počítač existuje pro navrácení služeb po obnovení, je potřeba ho před navrácením služeb po obnovení odstranit. Navrácení služeb po obnovení pak vytvoří nový virtuální počítač na stejném hostiteli, jako je hostitel ESX hlavního cílového serveru. Když provedete navrácení služeb po obnovení do alternativního umístění, data se obnoví do stejného úložiště dat a stejného hostitele ESX, které používá místní hlavní cílový server.
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
