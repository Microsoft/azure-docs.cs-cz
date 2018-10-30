---
title: Převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů VMware a fyzických serverů replikovaných do Azure pomocí Site Recovery | Microsoft Docs
description: Zjistěte, jak provést převzetí služeb při selhání virtuálních počítačů VMware a fyzických serverů do Azure a navrácení služeb po obnovení do místní lokality pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/11/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 7e586e7e3ec8c16dcd215dbc11251d1b9fe928e1
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457036"
---
# <a name="fail-over-and-fail-back-vmware-vms-and-physical-servers-replicated-to-azure"></a>Převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů VMware a fyzických serverů replikovaných do Azure

Tento kurz popisuje, jak provést převzetí služeb při selhání virtuálního počítače VMware do Azure. Po převzetí služeb při selhání navrátíte služby po obnovení do místní lokality, až bude dostupná. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověření vlastností virtuálního počítače VMware a kontrola, že odpovídá požadavkům Azure
> * Spuštění převzetí služeb při selhání do Azure
> * Vytvoření procesového serveru a hlavního cílového serveru pro navrácení služeb po obnovení
> * Znovunastavení ochrany virtuálních počítačů Azure do místní lokality
> * Převzetí služeb při selhání z Azure do místní lokality
> * Znovunastavení ochrany místních virtuálních počítačů a opětovné spuštění replikace do Azure

>[!NOTE]
>Tyto kurzy demonstrují ten nejjednodušší způsob nasazení určitého scénáře. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Podrobné informace ke krokům testovacího převzetí služeb při selhání najdete v tomto [průvodci](site-recovery-failover.md).

Tento kurz je pátou částí série. Tento kurz předpokládá, že jste už dokončili úlohy z předchozích kurzů.

1. [Příprava Azure](tutorial-prepare-azure.md)
2. [Příprava VMware v místním prostředí](vmware-azure-tutorial-prepare-on-premises.md)
3. [Nastavení zotavení po havárii](vmware-azure-tutorial.md)
4. [Spuštění postupu zotavení po havárii](tutorial-dr-drill-azure.md)
5. Kromě výše uvedených kroků doporučujeme [zkontrolovat architekturu](vmware-azure-architecture.md) pro scénář zotavení po havárii.

## <a name="failover-and-failback"></a>Převzetí služeb při selhání a navrácení služeb po obnovení

Převzetí služeb při selhání a navrácení služeb po obnovení probíhá ve čtyřech fázích:

1. **Převzetí služeb při selhání do Azure:** Převezmou se služby při selhání počítačů z místní lokality do Azure.
2. **Znovunastavení ochrany virtuálních počítačů Azure:** Znovu se nastaví ochrana virtuálních počítačů Azure, aby se začaly replikovat zpět do místních virtuálních počítačů VMware. Místní virtuální počítač se během opětovného nastavování ochrany vypne. To pomáhá zajistit konzistenci dat během replikace.
3. **Převzetí služeb při selhání do místní lokality:** Spustí se převzetí služeb při selhání, které zajistí navrácení služeb po obnovení z Azure.
4. **Znovunastavení ochrany místních virtuálních počítačů:** Po navrácení dat po obnovení se znovu nastaví ochrana místních virtuálních počítačů, na které jste provedli navrácení služeb po obnovení, aby se začaly replikovat do Azure.

## <a name="verify-vm-properties"></a>Ověření vlastností virtuálního počítače

Ověřte vlastnosti virtuálního počítače a ujistěte se, že splňuje [požadavky Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. V části **Chráněné položky** klikněte na **Replikované položky** a pak na virtuální počítač.

2. V podokně **Replikovaná položka** se zobrazí souhrn informací o virtuálním počítači, jeho stav a nejnovější dostupné body obnovení. Kliknutím na **Vlastnosti** zobrazíte další podrobnosti.

3. V části **Výpočty a síť** můžete upravit název Azure, skupinu prostředků, cílovou velikost, [skupinu dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md) a [nastavení spravovaného disku](#managed-disk-considerations).

4. Můžete zobrazit a upravit nastavení sítě, včetně sítě a podsítě, do které se virtuální počítače Azure umístí po převzetí služeb při selhání, a IP adresy, která se jim přiřadí.

5. V části **Disky** se zobrazí informace o operačním systému a datových discích ve virtuálním počítači.

## <a name="run-a-failover-to-azure"></a>Spuštění převzetí služeb při selhání do Azure

1. V části **Nastavení** > **Replikované položky** klikněte na virtuální počítač a pak na **Převzetí služeb při selhání**.

2. V části **Převzetí služeb při selhání** vyberte **Bod obnovení**, ke kterému se mají převzít služby při selhání. Můžete použít jednu z následujících možností:
   - **Nejnovější:** Tato možnost nejprve zpracuje veškerá data odeslaná do Site Recovery. Poskytuje nejnižší cíl bodu obnovení (RPO), protože se virtuální počítač Azure vytvoří teprve tehdy, až převzetí služeb při selhání bude mít veškerá data, která se do Site Recovery replikovala při aktivaci převzetí služeb při selhání.
   - **Nejnovější zpracovaný:** Tato možnost převezme služby při selhání virtuálního počítače k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery. Tato možnost poskytuje nízkou plánovanou dobu obnovení (RTO), protože se neztrácí žádný čas zpracováním nezpracovaných dat.
   - **Nejnovější konzistentní vzhledem k aplikacím:** Tato možnost převezme služby při selhání virtuálního počítače k nejnovějšímu bodu obnovení konzistentnímu vzhledem k aplikacím zpracovanému službou Site Recovery.
   - **Vlastní:** Zadáte vlastní bod obnovení.

3. Vyberte možnost **Před spuštěním převzetí služeb při selhání vypnout počítač**, pokud se chcete pokusit před aktivací převzetí služeb při selhání vypnout zdrojové virtuální počítače. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.

V některých scénářích vyžaduje převzetí služeb při selhání další zpracování, které trvá asi osm až deset minut. Možná si všimnete **delšího trvání testovacího převzetí služeb při selhání** u virtuálních počítačů VMware využívajících službu Mobility starší verze než 9.8, fyzických serverů, virtuálních počítačů VMware s Linuxem, virtuálních počítačů Hyper-V chráněných jako fyzické servery, virtuálních počítačů VMware bez povolené služby DHCP a virtuálních počítačů VMware bez následujících ovladačů spuštění: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> **Nepřerušujte v průběhu proces převzetí služeb při selhání**: před zahájením procesu převzetí služeb při selhání se zastaví replikace virtuálního počítače.
> Pokud proces převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.

## <a name="connect-to-failed-over-virtual-machine-in-azure"></a>Připojení k virtuálnímu počítači v Azure, pro který bylo provedeno převzetí služeb při selhání

1. Pokud se chcete připojit k virtuálním počítačům Azure pomocí protokolů RDP/SSH po převzetí služeb při selhání, musíte dodržet požadavky shrnuté v této [tabulce](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. Po převzetí služeb při selhání přejděte k virtuálnímu počítači a ověřte ho tím, že se k němu [připojíte](../virtual-machines/windows/connect-logon.md).
3. Po ověření kliknutím na **Potvrdit** dokončete bod obnovení virtuálního počítače po převzetí služeb při selhání. Po potvrzení se odstraní všechny ostatní dostupné body obnovení. Tím se dokončí aktivita převzetí služeb při selhání.

>[!TIP]
> **Změna bodu obnovení** vám pomůže zvolit jiný bod obnovení po převzetí služeb při selhání, pokud nejste spokojeni s virtuálním počítačem, pro který bylo provedeno. Po **potvrzení** už tato možnost nebude dostupná.

Při řešení problémů s připojením po převzetí služeb při selhání použijte [zde](site-recovery-failover-to-azure-troubleshoot.md) popsaný postup.

## <a name="preparing-for-reprotection-of-azure-vm"></a>Příprava na znovunastavení ochrany virtuálního počítače Azure

- **Pokud používáte připojení Azure ExpressRoute**, můžete použít místní procesový server (integrovaný procesový server), který se automaticky nainstaluje na konfigurační server v rámci nastavení.

> [!IMPORTANT]
> Pokud mezi místním prostředím a Azure používáte připojení VPN, musíte jako procesový server pro znovunastavení ochrany a navrácení služeb po obnovení nastavit virtuální počítač Azure. Pokud chcete nastavit procesový server v Azure, postupujte podle pokynů v [tomto článku](vmware-azure-set-up-process-server-azure.md).

Další informace o požadavcích na znovunastavení ochrany a navrácení služeb po obnovení najdete v této [části](vmware-azure-reprotect.md##before-you-begin). 

### <a name="configure-the-master-target-server"></a>Konfigurace hlavního cílového serveru

Hlavní cílový server přijímá a zpracovává replikační data během navrácení služeb z Azure po obnovení. Ve výchozím nastavení je k dispozici na místním konfiguračním serveru. V tomto kurzu použijeme výchozí hlavní cílový server.

>[!NOTE]
>Ochrana virtuálního počítače založeného na Linuxu vyžaduje vytvoření samostatného hlavního cílového serveru. Další informace získáte po [kliknutí sem](vmware-azure-install-linux-master-target.md).

Pokud je virtuální počítač na **hostiteli ESXi spravovaném serverem vCenter**, musí mít hlavní cílový server přístup k úložišti dat virtuálního počítače (VMDK), aby na disky virtuálního počítače mohl zapisovat replikovaná data. Ujistěte se, že je úložiště dat virtuálního počítače připojené k hostiteli hlavního cílového serveru a že umožňuje přístup pro čtení i zápis.

Pokud je virtuální počítač na **hostiteli ESXi nespravovaném serverem vCenter**, služba Site Recovery během opětovného nastavování ochrany vytvoří nový virtuální počítač. Virtuální počítač se vytvoří na hostiteli ESX, na kterém vytvoříte hlavní cílový server.
Pevný disk virtuálního počítače musí být v úložišti dat přístupném pro hostitele, na kterém běží hlavní cílový server.

Pokud virtuální počítač **nepoužívá vCenter**, musíte před znovunastavením ochrany tohoto počítače dokončit zjišťování hostitele, na kterém běží hlavní cílový server. To platí i pro navrácení služeb po obnovení fyzických serverů. Další možností v případě, že existuje místní virtuální počítač, je odstranit ho před provedením navrácení služeb po obnovení. Navrácení služeb po obnovení pak vytvoří nový virtuální počítač na stejném hostiteli, jako je hostitel ESX hlavního cílového serveru. Když provedete navrácení služeb po obnovení do alternativního umístění, data se obnoví do stejného úložiště dat a stejného hostitele ESX, které používá místní hlavní cílový server.

Na hlavním cílovém serveru nemůžete používat funkci Storage vMotion. Jinak navrácení služeb po obnovení nebude fungovat, protože nebude mít přístup k diskům. Vylučte hlavní cílové servery ze svého seznamu vMotion.

>[!Warning]
>Pokud k znovunastavení ochrany replikační skupiny použijete jiný hlavní cílový server, nemůže server zajistit společný bod v čase.

## <a name="reprotect-azure-vms"></a>Znovunastavení ochrany virtuálních počítačů Azure

Po znovunastavení ochrany virtuálního počítače Azure se budou data replikovat do místního virtuálního počítače. Jedná se o povinný krok před provedením převzetí služeb při selhání z Azure do místního virtuálního počítače. Postupujte podle následujících pokynů a proveďte znovunastavení ochrany.

1. V části **Nastavení** > **Replikované položky** klikněte pravým tlačítkem na virtuální počítač, u kterého došlo k převzetí služeb při selhání, a pak na **Znovu nastavit ochranu**.
2. V části **Znovu nastavit ochranu** ověřte, že je vybraná možnost **Z Azure do místní lokality**.
3. Zadejte místní hlavní cílový server a procesový server.
4. V části **Úložiště dat** vyberte úložiště dat hlavního cílového serveru, do kterého chcete obnovit disky v místním prostředí. Pokud se virtuální počítač odstranil, v tomto úložišti dat se vytvoří nové disky. Pokud již disky existují, toto nastavení se ignoruje, ale musíte zadat nějakou hodnotu.
5. Vyberte jednotku hlavního cílového serveru pro uchovávání dat. Automaticky se vyberou zásady navrácení služeb po obnovení.
6. Kliknutím na **OK** zahajte opětovné nastavování ochrany. Úloha začne replikovat virtuální počítač z Azure do místní lokality. Průběh můžete sledovat na kartě **Úlohy**.
7. Jakmile se stav virtuálního počítače v části **Replikované položky** změní na **Chráněno**, je počítač připravený na převzetí služeb při selhání do místního prostředí.

> [!NOTE]
> Virtuální počítač Azure je možné obnovit do stávajícího místního virtuálního počítače nebo do alternativního umístění. Další informace najdete v [tomto článku](concepts-types-of-failback.md).

## <a name="run-a-failover-from-azure-to-on-premises"></a>Spuštění převzetí služeb při selhání z Azure do místní lokality

Ke zpětné replikaci do místní lokality se používá zásada navrácení služeb po obnovení. Tato zásada se vytvoří automaticky při vytváření zásady replikace pro replikaci do Azure:

- Tato zásada se automaticky přidruží ke konfiguračnímu serveru.
- Tuto zásadu není možné upravit.
- Zásada má tyto hodnoty:
    - Prahová hodnota cíle bodu obnovení (RPO) = 15 minut
    - Uchovávání bodu obnovení = 24 hodin
    - Frekvence pořizování snímků konzistentních vzhledem k aplikacím = 60 minut

Spusťte převzetí služeb při selhání následujícím způsobem:

1. Na stránce **Replikované položky** klikněte pravým tlačítkem na počítač a pak na **Převzetí služeb při selhání**.
2. V části **Potvrdit převzetí služeb při selhání** ověřte, že převzetí služeb při selhání směřuje z Azure.
    ![Směr převzetí služeb při selhání](media/vmware-azure-tutorial-failover-failback/failover-direction.PNG)
3. Vyberte bod obnovení, který chcete pro převzetí služeb při selhání použít. Bod obnovení konzistentní vzhledem k aplikacím je starší než nejnovější bod v čase, což způsobí ztrátu některých dat.

    >[!WARNING]
    >Po spuštění převzetí služeb při selhání Site Recovery vypne virtuální počítače Azure a spustí místní virtuální počítač. Dojde k určitému výpadku, takže zvolte vhodnou dobu.

4. Průběh úlohy můžete sledovat v části **Trezor služby Recovery Services** > **Monitorování a Sestavy** > **Úlohy Site Recovery**.
5. Po dokončení převzetí služeb při selhání klikněte pravým tlačítkem na virtuální počítač a pak klikněte na **Potvrdit**. Tím se aktivuje úloha, která odebere virtuální počítače Azure.
6. Ověřte, že se virtuální počítače Azure vyply podle očekávání.

## <a name="reprotect-on-premises-machines-to-azure"></a>Znovunastavení ochrany místních počítačů do Azure

Data by teď měla být zpět ve vaší místní lokalitě, ale nereplikují se do Azure. Replikaci do Azure můžete znovu spustit následujícím způsobem:

1. V trezoru v části **Chráněné položky** >**Replikované položky** vyberte virtuální počítač, pro který bylo provedeno navrácení služeb po obnovení, a klikněte na **Znovu nastavit ochranu**.
2. Vyberte procesový server, který se má použít k odesílání replikovaných dat do Azure, a klikněte na **OK**.

Po znovunastavení ochrany se virtuální počítač replikuje zpět do Azure a podle potřeby můžete spustit převzetí služeb při selhání.
