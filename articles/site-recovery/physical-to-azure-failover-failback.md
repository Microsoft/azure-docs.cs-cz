---
title: Nastavení převzetí služeb při selhání a navrácení služeb po obnovení pro fyzické servery s obnovením webu
description: Zjistěte, jak si pořídit služby při selhání fyzické servery do Azure a vrátit se zpět do místního webu pro zotavení po havárii pomocí Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2019
ms.author: raynew
ms.openlocfilehash: ea5893f45962d67f4b6f3e9a261c65aa0ec926bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75497854"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Převzetí služeb při selhání a navrácení služeb po selhání fyzické servery replikované do Azure

Tento kurz popisuje, jak převzetí služeb při selhání místní fyzické servery, které se replikují do Azure s [Azure Site Recovery](site-recovery-overview.md). Po převzetí služeb při selhání, můžete navrácení služeb po selhání z Azure do místního webu, když je k dispozici.

## <a name="before-you-start"></a>Než začnete

- [Další informace](failover-failback-overview.md) o procesu převzetí služeb při selhání v zotavení po havárii.
- Pokud chcete převzetí služeb při selhání více počítačů, [přečtěte si,](recovery-plan-overview.md) jak shromáždit počítače společně v plánu obnovení.
- Před úplným převzetím služeb při selhání spusťte [cvičení zotavení po havárii,](site-recovery-test-failover-to-azure.md) abyste zajistili, že vše funguje podle očekávání.
- Podle [těchto pokynů](site-recovery-failover.md#prepare-to-connect-after-failover) se připravte na připojení k virtuálním počítačům Azure po převzetí služeb při selhání.



## <a name="run-a-failover"></a>Spuštění převzetí služeb při selhání

### <a name="verify-server-properties"></a>Ověření vlastností serveru

Ověřte vlastnosti serveru a ujistěte se, že splňuje [požadavky Azure](vmware-physical-azure-support-matrix.md#replicated-machines) pro virtuální počítače Azure.

1. V **části Chráněné položky**klepněte na **položku Replikované položky**a vyberte počítač.
2. V podokně **Replikované položky** je souhrn informací o počítači, stavu stavu a nejnovějších dostupných bodů obnovení. Kliknutím na **Vlastnosti** zobrazíte další podrobnosti.
3. V **aplikaci Compute and Network**můžete upravit název Azure, skupinu prostředků, velikost cíle, sadu [dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md)a nastavení spravovaného disku.
4. Můžete zobrazit a upravit nastavení sítě, včetně sítě a podsítě, do které se virtuální počítače Azure umístí po převzetí služeb při selhání, a IP adresy, která se jim přiřadí.
5. V **části Disky**můžete zobrazit informace o operačním systému počítače a datových discích.

### <a name="fail-over-to-azure"></a>Převzetí služeb při selhání do Azure

1. V **nastavení** > **Replikované položky** klepněte na počítač > **převzetí služeb při selhání**.
2. V části **Převzetí služeb při selhání** vyberte **Bod obnovení**, ke kterému se mají převzít služby při selhání. Můžete použít jednu z následujících možností:
   - **Nejnovější:** Tato možnost nejprve zpracuje veškerá data odeslaná do Site Recovery. Poskytuje nejnižší cíl bodu obnovení (RPO), protože se virtuální počítač Azure vytvoří teprve tehdy, až převzetí služeb při selhání bude mít veškerá data, která se do Site Recovery replikovala při aktivaci převzetí služeb při selhání.
   - **Poslední zpracované**: Tato možnost převezme převzetí počítače k nejnovějšímu bodu obnovení zpracovanému site recovery. Tato možnost poskytuje nízkou plánovanou dobu obnovení (RTO), protože se neztrácí žádný čas zpracováním nezpracovaných dat.
   - **Nejnovější aplikace konzistentní**: Tato možnost selže přes počítač na nejnovější bod obnovení konzistentní s aplikací zpracované site recovery.
   - **Vlastní:** Zadáte vlastní bod obnovení.

3. Vyberte **Vypnout počítač před zahájením převzetí služeb při selhání,** pokud chcete, aby se obnovení webu pokusilo vypnout zdrojový počítač před spuštěním převzetí služeb při selhání. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
4. Pokud jste připraveni na připojení k virtuálnímu počítači Azure, připojte se k němu, abyste ho po převzetí služeb při selhání ověřili.
5. Po ověření můžete převzetí služeb při selhání **Potvrdit**. Tím se odstraní všechny dostupné body obnovení.

> [!WARNING]
> Neznevažuj probíhající převzetí služeb při selhání. Před zahájením převzetí služeb při selhání se replikace počítače zastaví. Pokud převzetí služeb při selhání zrušíte, zastaví se, ale počítač se znovu nereplikuje.
> U fyzických serverů může zpracování převzetí služeb při selhání trvat přibližně osm až deset minut.

## <a name="automate-actions-during-failover"></a>Automatizace akcí během převzetí služeb při selhání

Můžete chtít automatizovat akce během převzetí služeb při selhání. Chcete-li to provést, můžete použít skripty nebo azure automatizace runbooky v plánech obnovení.

- [Přečtěte si o](site-recovery-create-recovery-plans.md) vytváření a přizpůsobení plánů obnovení, včetně přidávání skriptů.
- [Naučte](site-recovery-runbook-automation.md) se přidání runbooků Azure Automation do plánů obnovení.

## <a name="configure-settings-after-failover"></a>Konfigurace nastavení po převzetí služeb při selhání

Po převzetí služeb při selhání je potřeba [nakonfigurovat nastavení Azure](site-recovery-failover.md#prepare-in-azure-to-connect-after-failover) pro připojení k replikovaných virtuálních počítačích Azure. Kromě toho nastavte [interní a veřejné](site-recovery-failover.md#set-up-ip-addressing) ip adresování.

## <a name="prepare-for-reprotection-and-failback"></a>Příprava na opětovnou ochranu a navrácení služeb po obnovení

Po převzetí služby při selhání do Azure znovu protectazure virtuálních počítačích replikací do místního webu. Potom po jejich replikaci, můžete je vypočte zpět do místního prostředí spuštěním převzetí služeb při selhání z Azure do místního webu.

1. Fyzické servery replikované do Azure pomocí site recovery můžou napojit na poobnovení jenom jako virtuální počítače VMware. K navrácení služeb po selhání potřebujete infrastrukturu VMware. Postupujte podle kroků v [tomto článku](vmware-azure-prepare-failback.md) připravit pro opětovné zajištění a navrácení služeb po obnovení, včetně nastavení procesního serveru v Azure a místní hlavní cílový server a konfigurace sítě VPN na webu nebo ExpressRoute privátní partnerský vztah pro navrácení služeb po obnovení.
2. Ujistěte se, že místní konfigurační server běží a je připojený k Azure. Během převzetí služeb při selhání do Azure, místní web nemusí být přístupné a konfigurační server může být nedostupný nebo vypnout. Během navrácení služeb po obnovení musí virtuální počítače existovat v databázi konfiguračního serveru. V opačném případě je navrácení služeb po selhání neúspěšné.
3. Odstraňte všechny snímky na místním hlavním cílovém serveru. Opětovné protekce nebude fungovat, pokud existují snímky.  Snímky na virtuálním počítači jsou automaticky sloučeny během úlohy opětovného ochrany.
4. Pokud znovu chráníte virtuální počítače shromážděné do replikační skupiny pro konzistenci více virtuálních počítačů, ujistěte se, že všechny mají stejný operační systém (Windows nebo Linux) a ujistěte se, že hlavní cílový server, který nasadíte, má stejný typ operačního systému. Všechny virtuální servery v replikační skupině musí používat stejný hlavní cílový server.
5. Otevřete [požadované porty](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) pro navrácení služeb po selhání.
6. Před navrácením služeb po selhání se ujistěte, že je server vCenter připojen. V opačném případě se odpojení disků a jejich připojení zpět k virtuálnímu počítači nezdaří.
7. Pokud server vCenter spravuje virtuální servery, na které budete navrácení služeb po službě, ujistěte se, že máte požadovaná oprávnění. Pokud provedete zjišťování vCenter jen pro čtení a chráníte virtuální počítače, ochrana bude úspěšná a funguje převzetí služeb při selhání. Však během opětovnéochrany převzetí služeb při selhání nezdaří, protože úložiště dat nelze zjistit a nejsou uvedeny během opětovného protekce. Chcete-li tento problém vyřešit, můžete aktualizovat pověření vCenter s [příslušným účtem nebo oprávněními](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)a pak úlohu opakovat. 
8. Pokud jste k vytvoření virtuálních počítačů použili šablonu, ujistěte se, že každý virtuální počítač má pro disky vlastní UUID. Pokud místní UUID v místním virtuálním počítači koovuje s UUID hlavního cílového serveru, protože obě byly vytvořeny ze stejné šablony, opětovné protekce se nezdaří. Nasazení z jiné šablony.
9. Pokud se vám nedaří vrátit se k alternativnímu serveru vCenter, zkontrolujte, zda jsou zjištěny nové servery vCenter a hlavní cílový server. Obvykle, pokud nejsou datastores nejsou přístupné nebo nejsou viditelné v **Znovu zamknout**.
10. Ověřte následující scénáře, ve kterých nelze zpět nouzi:
    - Pokud používáte esxi 5.5 free edice nebo vSphere 6 Hypervisor free edition. Upgradujte na jinou verzi.
    - Pokud máte fyzický server Windows Server 2008 R2 SP1.
    - Virtuální aplikace, které [byly migrovány](migrate-overview.md#what-do-we-mean-by-migration).
    - Virtuální ho virtuálního uživatele, který byl přesunut do jiné skupiny prostředků.
    - Replika virtuálního počítače Azure, který byl odstraněn.
    - Replika virtuálního počítače Azure, který není chráněný (replikace do místní lokality).
10. [Zkontrolujte typy navrácení služeb po obnovení,](concepts-types-of-failback.md) které můžete použít – obnovení původního umístění a obnovení alternativního umístění.


## <a name="reprotect-azure-vms-to-an-alternate-location"></a>Opětovné přizabezpečení virtuálních počítačích Azure do alternativního umístění

Tento postup předpokládá, že místní virtuální počítač není k dispozici.

1. V úschovně > **Replikovaných** > **položek**klepněte pravým tlačítkem myši na počítač, který selhal v průběhu > **znovu chránit**.
2. V části **Znovu nastavit ochranu** ověřte, že je vybraná možnost **Z Azure do místní lokality**.
3. Zadejte místní hlavní cílový server a procesový server.
4. V části **Úložiště dat** vyberte úložiště dat hlavního cílového serveru, do kterého chcete obnovit disky v místním prostředí.
       - Tuto možnost použijte, pokud byl místní virtuální počítač odstraněn nebo neexistuje a potřebujete vytvořit nové disky.
       - Toto nastavení je ignorováno, pokud disky již existují, ale je třeba zadat hodnotu.
5. Vyberte jednotku hlavního cílového serveru pro uchovávání dat. Automaticky se vyberou zásady navrácení služeb po obnovení.
6. Kliknutím na **OK** zahajte opětovné nastavování ochrany. Úloha začne replikovat virtuální počítač Azure do místního webu. Průběh můžete sledovat na kartě **Úlohy**.

> [!NOTE]
> Pokud chcete virtuální počítač Azure zotavit do existujícího místního virtuálního počítače, připojte úložiště dat místního virtuálního počítače s přístupem ke čtení i zápisu k hostiteli ESXi hlavního cílového serveru.


## <a name="fail-back-from-azure"></a>Navrácení služeb po obnovení z Azure

Spusťte převzetí služeb při selhání následujícím způsobem:

1. Na stránce **Replikované položky** klikněte pravým tlačítkem na počítač a pak na **Neplánované převzetí služeb při selhání**.
2. V části **Potvrdit převzetí služeb při selhání** ověřte, že převzetí služeb při selhání směřuje z Azure.
3.Vyberte bod obnovení, který chcete použít pro převzetí služeb při selhání.
    - Doporučujeme použít **nejnovější** bod obnovení. Bod konzistentní s aplikací je za nejnovější bod v čase a způsobuje ztrátu některých dat.
    - **Poslední** je místo zotavení konzistentní při selhání.
    - Po spuštění převzetí služeb při selhání Site Recovery vypne virtuální počítače Azure a spustí místní virtuální počítač. Dojde k určitému výpadku, takže zvolte vhodnou dobu.
4. Klikněte pravým tlačítkem na počítač a pak klikněte na **Potvrdit**. Tím se aktivuje úloha, která odebere virtuální počítače Azure.
5. Ověřte, že se virtuální počítače Azure vyply podle očekávání.


## <a name="reprotect-on-premises-machines-to-azure"></a>Znovunastavení ochrany místních počítačů do Azure

Data by teď měla být zpět ve vaší místní lokalitě, ale nereplikují se do Azure. Replikaci do Azure můžete znovu spustit následujícím způsobem:

1. V části trezor > **Nastavení** >**Replikované položky** vyberte virtuální počítače, u kterých došlo k navrácení služeb po obnovení, a klikněte na **Znovu nastavit ochranu**.
2. Vyberte procesový server, který se používá k odesílání replikovaných dat do Azure, a klikněte na **OK**.


## <a name="next-steps"></a>Další kroky

Po dokončení úlohy znovu se místní virtuální počítač replikuje do Azure. Podle potřeby můžete [spustit další převzetí služeb při selhání](site-recovery-failover.md) do Azure.
