---
title: "Převzetí služeb při selhání a selhání zpět fyzických serverů, které se replikují do Azure pomocí Site Recovery | Microsoft Docs"
description: "Naučte se vrátí zpátky na místní lokalitu s Azure Site Recovery a selhání fyzických serverů do Azure"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: raynew
ms.openlocfilehash: 6334bdef73ea55ba0a0e15c5880daf03264e5c60
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Převzetí služeb při selhání a selhání zpět fyzických serverů replikovat do Azure

Tento kurz popisuje, jak při selhání fyzického serveru do Azure. Poté, co jste při selhání, můžete nezdaří serveru zpět do místního webu případě, že je k dispozici. 

## <a name="preparing-for-failover-and-failback"></a>Příprava na převzetí služeb při selhání a navrácení služeb po obnovení

Fyzické servery replikovat do Azure pomocí Site Recovery může selhat pouze zpět jako virtuální počítače VMware. Chcete-li navrácení služeb po obnovení musíte infrastruktury VMware. 

Převzetí služeb při selhání a navrácení služeb po obnovení probíhá ve čtyřech fázích:

1. **Převzetí služeb při selhání do Azure:** Převezmou se služby při selhání počítačů z místní lokality do Azure.
2. **Znovu nastavte ochranu virtuálních počítačů Azure**: znovu nastavte ochranu virtuálních počítačů Azure, tak, aby jejich spuštění replikovat místní virtuální počítače VMware.
3. **Převzetí služeb při selhání do místní lokality:** Spustí se převzetí služeb při selhání, které zajistí navrácení služeb po obnovení z Azure.
4. **Opětovné ochrany místní virtuální počítače**: po dat se nezdařila zpět, znovu aktivujte ochranu virtuálních počítačů VMware místní při selhání zpátky do tak, aby jejich spuštění replikaci do Azure.

## <a name="verify-server-properties"></a>Ověřte vlastnosti serveru

Ověřte vlastnosti serveru a ujistěte se, zda je v souladu s [požadavky pro Azure](vmware-physical-azure-support-matrix.md#replicated-machines) pro virtuální počítače Azure.

1. V **chráněné položky**, klikněte na tlačítko **replikované položky**a vyberte počítač.

2. V **replikované položky** podokně je souhrn informace o počítači, stav, a nejnovější dostupný bod obnovení. Kliknutím na **Vlastnosti** zobrazíte další podrobnosti.
3. V části **Výpočty a síť** můžete upravit název Azure, skupinu prostředků, cílovou velikost, [skupinu dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md) a [nastavení spravovaného disku](#managed-disk-considerations).
4. Můžete zobrazit a upravit nastavení sítě, včetně sítě a podsítě, do které se virtuální počítače Azure umístí po převzetí služeb při selhání, a IP adresy, která se jim přiřadí.
5. V **disky**, zobrazí se informace o operačního systému počítače a datové disky.

## <a name="run-a-failover-to-azure"></a>Spuštění převzetí služeb při selhání do Azure

1. V části **Nastavení** > **Replikované položky** klikněte na počítač a pak na **Převzetí služeb při selhání**.
2. V části **Převzetí služeb při selhání** vyberte **Bod obnovení**, ke kterému se mají převzít služby při selhání. Můžete použít jednu z následujících možností:
   - **Nejnovější (výchozí):** Tato možnost nejprve zpracuje veškerá data odeslaná do Site Recovery. Poskytuje nejnižší cíl bodu obnovení (RPO), protože se virtuální počítač Azure vytvoří teprve tehdy, až převzetí služeb při selhání bude mít veškerá data, která se do Site Recovery replikovala při aktivaci převzetí služeb při selhání.
   - **Nejnovější zpracované**: tuto možnost převezme počítače do nejnovějšího bodu obnovení, které jsou zpracovávány Site Recovery. Tato možnost poskytuje nízkou plánovanou dobu obnovení (RTO), protože se neztrácí žádný čas zpracováním nezpracovaných dat.
   - **Nejnovější aplikace konzistentní**: tuto možnost převezme počítače do bodu nejnovější konzistentní obnovení zpracovaných službou Site Recovery.
   - **Vlastní:** Zadáte vlastní bod obnovení.

3. Vyberte **vypnout počítač před zahájením převzetí služeb při selhání** Pokud chcete, aby Site Recovery a zkuste to vypnout zdrojový počítač před spuštěním převzetí služeb při selhání. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
4. Pokud jste připraveni na připojení k virtuálnímu počítači Azure, připojte se k němu, abyste ho po převzetí služeb při selhání ověřili.
5. Po ověření můžete převzetí služeb při selhání **Potvrdit**. Tím se odstraní všechny dostupné body obnovení.

> [!WARNING]
> Nemáte zrušit převzetí služeb při selhání v průběhu. Před zahájením převzetí služeb při selhání, zastaví replikaci počítače. Pokud zrušíte převzetí služeb při selhání, zastaví, ale tento počítač nebude znovu replikovat.
> Pro fyzické servery zpracování dalších převzetí služeb při selhání může trvat přibližně osm až deset minut. 

## <a name="create-a-process-server-in-azure"></a>Vytvoření procesového serveru v Azure

Procesový server přijímá data z virtuálního počítače Azure a odesílá je do místní lokality. Síť s nízkou latencí je vyžadován mezi procesovým serverem a chráněný počítač.

- Pokud používáte připojení Azure ExpressRoute, můžete pro účely testování použít místní procesový server, který je automaticky nainstalovaný na konfiguračním serveru.
- Pokud používáte připojení VPN nebo pokud spouštíte navrácení služeb po obnovení v produkčním prostředí, musíte nastavit virtuální počítač Azure jako procesový server založený na Azure pro navrácení služeb po obnovení.
- Postupujte podle pokynů v [v tomto článku](vmware-azure-set-up-process-server-azure.md) nastavit procesní server v Azure.

## <a name="configure-the-master-target-server"></a>Konfigurace hlavního cílového serveru

Ve výchozím nastavení hlavní cílový server přijímá data navrácení služeb po obnovení. Běží na serveru místní konfigurace.

- Pokud virtuální počítač VMware, ke kterému jste navrácení služeb po obnovení je na hostiteli ESXi, který je spravován pomocí serveru VMware vCenter Server, hlavní cílový server musí mít přístup k úložišti dat Virtuálního počítače (VMDK), k zápisu replikovaná data na disky virtuálních počítačů. Ujistěte se, že je úložiště dat virtuálního počítače připojené k hostiteli hlavního cílového serveru a že umožňuje přístup pro čtení i zápis.
- Pokud hostitele ESXi, který není spravován systémem vCenter server, služba Site Recovery vytvoří při vytvoření nového virtuálního počítače. Virtuální počítač se vytvoří na hostiteli ESX, ve kterém vytvoříte virtuální počítač na hlavním cíli. Pevný disk virtuálního počítače musí být v úložišti dat přístupném pro hostitele, na kterém běží hlavní cílový server.
- Pro fyzické počítače, které jste navrácení služeb po obnovení musíte provést zjišťování hostitele, na kterém je spuštěný hlavní cílový server, než můžete znovu nastavte ochranu na počítač.
- Další možnost, pokud místní počítač již existuje pro navrácení služeb po obnovení, je před provedením navrácení služeb po obnovení odstranit. Navrácení služeb po obnovení pak vytvoří nový virtuální počítač na stejném hostiteli, jako je hostitel ESX hlavního cílového serveru. Když provedete navrácení služeb po obnovení do alternativního umístění, data se obnoví do stejného úložiště dat a stejného hostitele ESX, které používá místní hlavní cílový server.
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

