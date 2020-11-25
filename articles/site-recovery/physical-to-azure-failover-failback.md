---
title: Nastavení převzetí služeb při selhání a navrácení služeb po obnovení pro fyzické servery s Site Recovery
description: Přečtěte si, jak převzít služby při selhání fyzických serverů do Azure a navrácení služeb po obnovení do místní lokality pro zotavení po havárii pomocí Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2019
ms.author: raynew
ms.openlocfilehash: 2994f68e4159c7c4aa7d82bef7a5891deb5055a0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017419"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Převzetí služeb při selhání a obnovení fyzických serverů replikovaných do Azure

V tomto kurzu se dozvíte, jak převzít služby při selhání místních fyzických serverů, které se replikují do Azure pomocí [Azure Site Recovery](site-recovery-overview.md). Po převzetí služeb při selhání se vrátíte z Azure do místní lokality, až bude k dispozici.

## <a name="before-you-start"></a>Než začnete

- [Přečtěte si informace](failover-failback-overview.md) o procesu převzetí služeb při selhání při zotavení po havárii.
- Pokud chcete převzít služby při selhání více počítačů, [Přečtěte si](recovery-plan-overview.md) , jak v plánu obnovení shromažďovat počítače dohromady.
- Před provedením úplného převzetí služeb při selhání spusťte postup [zotavení po havárii](site-recovery-test-failover-to-azure.md) , abyste měli jistotu, že všechno funguje podle očekávání.
- Podle [těchto pokynů](site-recovery-failover.md#prepare-to-connect-after-failover) připravte připojení k virtuálním počítačům Azure po převzetí služeb při selhání.



## <a name="run-a-failover"></a>Spuštění převzetí služeb při selhání

### <a name="verify-server-properties"></a>Ověřit vlastnosti serveru

Ověřte vlastnosti serveru a ujistěte se, že jsou v souladu s [požadavky Azure](vmware-physical-azure-support-matrix.md#replicated-machines) na virtuální počítače Azure.

1. V části **chráněné položky** klikněte na **replikované položky** a vyberte počítač.
2. V podokně **Replikovaná položka** se zobrazí souhrn informací o počítači, stav a nejnovější dostupné body obnovení. Kliknutím na **Vlastnosti** zobrazíte další podrobnosti.
3. V části **výpočty a síť** můžete upravit název Azure, skupinu prostředků, cílovou velikost, skupinu [dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md)a nastavení spravovaného disku.
4. Můžete zobrazit a upravit nastavení sítě, včetně sítě a podsítě, do které se virtuální počítače Azure umístí po převzetí služeb při selhání, a IP adresy, která se jim přiřadí.
5. V části **disky** můžete zobrazit informace o operačním systému počítače a datových discích.

### <a name="fail-over-to-azure"></a>Převzetí služeb při selhání do Azure

1. V části **Nastavení**  >  **replikované položky** klikněte na počítač > **převzetí služeb při selhání**.
2. V části **Převzetí služeb při selhání** vyberte **Bod obnovení**, ke kterému se mají převzít služby při selhání. Můžete použít jednu z následujících možností:
   - **Nejnovější:** Tato možnost nejprve zpracuje veškerá data odeslaná do Site Recovery. Poskytuje nejnižší cíl bodu obnovení (RPO), protože se virtuální počítač Azure vytvoří teprve tehdy, až převzetí služeb při selhání bude mít veškerá data, která se do Site Recovery replikovala při aktivaci převzetí služeb při selhání.
   - **Poslední zpracování**: Tato možnost přes počítač převezme poslední bod obnovení zpracovaný Site Recovery. Tato možnost poskytuje nízkou plánovanou dobu obnovení (RTO), protože se neztrácí žádný čas zpracováním nezpracovaných dat.
   - **Nejnovější konzistentní vzhledem k aplikacím**: Tato možnost převzetí služeb při selhání počítače na nejnovější bod obnovení konzistentní vzhledem k aplikacím zpracovaný pomocí Site Recovery.
   - **Vlastní:** Zadáte vlastní bod obnovení.

3. Vyberte **vypnout počítač před spuštěním převzetí služeb při selhání** , pokud chcete, aby se Site Recovery před aktivací převzetí služeb při selhání ukončil zdrojový počítač. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **úlohy** .
4. Pokud jste připraveni na připojení k virtuálnímu počítači Azure, připojte se k němu, abyste ho po převzetí služeb při selhání ověřili.
5. Po ověření můžete převzetí služeb při selhání **Potvrdit**. Tím se odstraní všechny dostupné body obnovení.

> [!WARNING]
> Nemůžete zrušit probíhající převzetí služeb při selhání. Před zahájením převzetí služeb při selhání se zastaví replikace počítače. Pokud zrušíte převzetí služeb při selhání, zastaví se, ale počítač se znovu nereplikuje.
> U fyzických serverů může dokončení dalších zpracování převzetí služeb při selhání trvat přibližně osm až deset minut.

## <a name="automate-actions-during-failover"></a>Automatizace akcí během převzetí služeb při selhání

Během převzetí služeb při selhání možná budete chtít automatizovat akce. K tomu můžete použít skripty nebo Runbooky Azure Automation v plánech obnovení.

- [Přečtěte si](site-recovery-create-recovery-plans.md) o vytváření a přizpůsobení plánů obnovení, včetně přidávání skriptů.
- [Naučte](site-recovery-runbook-automation.md) se sousedit s přidávat Azure Automation Runbooky do plánů obnovení.

## <a name="configure-settings-after-failover"></a>Konfigurace nastavení po převzetí služeb při selhání

Po převzetí služeb při selhání musíte [nakonfigurovat nastavení Azure](site-recovery-failover.md#prepare-in-azure-to-connect-after-failover) pro připojení k replikovaným virtuálním počítačům Azure. Kromě toho nastavte [interní a veřejné](site-recovery-failover.md#set-up-ip-addressing) IP adresy.

## <a name="prepare-for-reprotection-and-failback"></a>Příprava na přeochranu a navrácení služeb po obnovení

Po převzetí služeb při selhání do Azure můžete znovu nastavit ochranu virtuálních počítačů Azure jejich replikací na místní lokalitu. Po jejich replikaci je můžete navrátit zpátky do místního prostředí spuštěním převzetí služeb při selhání z Azure do místní lokality.

1. Fyzické servery replikované do Azure pomocí Site Recovery můžou navracet služby po obnovení jenom jako virtuální počítače VMware. K navrácení služeb po obnovení potřebujete infrastrukturu VMware. Postupujte podle kroků v [tomto článku](vmware-azure-prepare-failback.md) a připravte se na opětovnou ochranu a navrácení služeb po obnovení, včetně nastavení procesového serveru v Azure, místního hlavního cílového serveru a konfigurace sítě Site-to-Site VPN nebo ExpressRoute privátního partnerského vztahu pro navrácení služeb po obnovení.
2. Ujistěte se, že místní konfigurační server běží a je připojený k Azure. Během převzetí služeb při selhání do Azure nemusí být místní lokalita přístupná a konfigurační server může být nedostupný nebo vypnutý. Během navrácení služeb po obnovení musí virtuální počítač existovat v databázi konfiguračního serveru. V opačném případě je navrácení služeb po obnovení neúspěšné.
3. Odstraňte všechny snímky na místním hlavním cílovém serveru. Pokud existují snímky, nebude ochrana fungovat.  Snímky na virtuálním počítači se během úlohy opětovného ochrany automaticky sloučí.
4. Pokud chráníte virtuální počítače shromážděné do replikační skupiny pro konzistenci s více virtuálními počítači, ujistěte se, že všechny mají stejný operační systém (Windows nebo Linux), a ujistěte se, že hlavní cílový server, který nasazujete, má stejný typ operačního systému. Všechny virtuální počítače v replikační skupině musí používat stejný hlavní cílový server.
5. Otevřete [požadované porty](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) pro navrácení služeb po obnovení.
6. Ujistěte se, že je vCenter Server připojená před navrácením služeb po obnovení. V opačném případě dojde k chybě odpojení disků a jejich připojení zpátky k virtuálnímu počítači.
7. Pokud Server vCenter spravuje virtuální počítače, ke kterým se vrátíte, ujistěte se, že máte požadovaná oprávnění. Pokud provádíte zjišťování vCenter uživatele, který je jen pro čtení, a ochranu virtuálních počítačů, ochrana bude úspěšná a funguje převzetí služeb při selhání. Během opakované ochrany ale převzetí služeb při selhání se nepovede, protože úložiště dat nejde zjistit a nejsou uvedené během ochrany. Chcete-li tento problém vyřešit, můžete aktualizovat přihlašovací údaje pro vCenter pomocí [příslušného účtu nebo oprávnění](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)a pak úlohu opakovat. 
8. Pokud jste pro vytváření virtuálních počítačů použili šablonu, ujistěte se, že každý virtuální počítač má vlastní UUID pro tyto disky. Pokud je identifikátor UUID místního virtuálního počítače v konfliktu s identifikátorem UUID hlavního cílového serveru, protože oba se vytvořily ze stejné šablony, znovu se ochrana nezdařila. Nasaďte z jiné šablony.
9. Pokud nasazujete zpět na alternativní vCenter Server, ujistěte se, že se zjistil nový vCenter Server a hlavní cílový server. Obvykle nejsou úložiště dat přístupná, nebo nejsou v rámci opětovného zapnutí **ochrany** viditelná.
10. Ověřte následující scénáře, ve kterých nelze provést navrácení služeb po obnovení:
    - Pokud používáte buď bezplatnou edici ESXi 5,5 nebo edici hypervisoru vSphere 6. Upgradujte na jinou verzi.
    - Pokud máte fyzický server se systémem Windows Server 2008 R2 SP1.
    - Virtuální počítače, které byly migrovány.
    - Virtuální počítač, který se přesunul do jiné skupiny prostředků.
    - Replika virtuálního počítače Azure, který se odstranil.
    - Replika virtuálního počítače Azure, která není chráněná (replikuje se na místní lokalitu).
10. [Zkontrolujte typy navrácení služeb](concepts-types-of-failback.md) po obnovení, které můžete použít – obnovení původního umístění a alternativní umístění obnovení.


## <a name="reprotect-azure-vms-to-an-alternate-location"></a>Opětovné zapnutí ochrany virtuálních počítačů Azure do alternativního umístění

Tento postup předpokládá, že místní virtuální počítač není k dispozici.

1. V trezoru > **Nastavení**  >  **replikované položky** klikněte pravým tlačítkem myši na počítač, u kterého došlo k převzetí služeb při selhání > **znovu zapnout ochranu**.
2. V části **Znovu nastavit ochranu** ověřte, že je vybraná možnost **Z Azure do místní lokality**.
3. Zadejte místní hlavní cílový server a procesový server.
4. V části **Úložiště dat** vyberte úložiště dat hlavního cílového serveru, do kterého chcete obnovit disky v místním prostředí.
       - Tuto možnost použijte, pokud se místní virtuální počítač odstranil nebo neexistuje a potřebujete vytvořit nové disky.
       - Toto nastavení se ignoruje, pokud už disky existují, ale je potřeba zadat hodnotu.
5. Vyberte jednotku hlavního cílového serveru pro uchovávání dat. Automaticky se vyberou zásady navrácení služeb po obnovení.
6. Kliknutím na **OK** zahajte opětovné nastavování ochrany. Úloha začne replikovat virtuální počítač Azure do místní lokality. Průběh můžete sledovat na kartě **Úlohy**.

> [!NOTE]
> Pokud chcete virtuální počítač Azure zotavit do existujícího místního virtuálního počítače, připojte úložiště dat místního virtuálního počítače s přístupem ke čtení i zápisu k hostiteli ESXi hlavního cílového serveru.


## <a name="fail-back-from-azure"></a>Navrácení služeb po obnovení z Azure

Spusťte převzetí služeb při selhání následujícím způsobem:

1. Na stránce **Replikované položky** klikněte pravým tlačítkem na počítač a pak na **Neplánované převzetí služeb při selhání**.
2. V části **Potvrdit převzetí služeb při selhání** ověřte, že převzetí služeb při selhání směřuje z Azure.
3. Vyberte bod obnovení, který chcete použít pro převzetí služeb při selhání.
    - Doporučujeme použít **nejnovější** bod obnovení. Bod konzistentní vzhledem k aplikacím je za posledním bodem v čase a způsobuje ztrátu dat.
    - **Nejnovější** je bod obnovení konzistentní vzhledem k selháním.
    - Po spuštění převzetí služeb při selhání Site Recovery vypne virtuální počítače Azure a spustí místní virtuální počítač. Dojde k určitému výpadku, takže zvolte vhodnou dobu.
4. Klikněte pravým tlačítkem na počítač a pak klikněte na **Potvrdit**. Tím se aktivuje úloha, která odebere virtuální počítače Azure.
5. Ověřte, že se virtuální počítače Azure vyply podle očekávání.


## <a name="reprotect-on-premises-machines-to-azure"></a>Znovunastavení ochrany místních počítačů do Azure

Data by teď měla být zpět ve vaší místní lokalitě, ale nereplikují se do Azure. Replikaci do Azure můžete znovu spustit následujícím způsobem:

1. V části trezor > **Nastavení** >**Replikované položky** vyberte virtuální počítače, u kterých došlo k navrácení služeb po obnovení, a klikněte na **Znovu nastavit ochranu**.
2. Vyberte procesový server, který se používá k odesílání replikovaných dat do Azure, a klikněte na **OK**.


## <a name="next-steps"></a>Další kroky

Po dokončení úlohy opětovné ochrany se místní virtuální počítač replikuje do Azure. Podle potřeby můžete [Spustit jiné převzetí služeb při selhání](site-recovery-failover.md) do Azure.
