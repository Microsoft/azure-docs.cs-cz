---
title: Opětovné přichýžení virtuální počítače VMware k místnímu webu pomocí Azure Site Recovery
description: Zjistěte, jak znovu chránit virtuální počítače VMware po převzetí služeb při selhání azure pomocí Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mayg
ms.openlocfilehash: 976888f57269cc9fe6107a38e30d78c73eb5c124
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257170"
---
# <a name="reprotect-from-azure-to-on-premises"></a>Zpětná replikace z Azure do místního prostředí

Po [převzetí služeb při selhání](site-recovery-failover.md) místních virtuálních počítačích VMware nebo fyzických serverů do Azure je prvním krokem při selhání zpět do místního webu opětovné zabezpečení virtuálních počítačích Azure, které byly vytvořeny během převzetí služeb při selhání. Tento článek popisuje, jak to provést. 

## <a name="before-you-begin"></a>Než začnete

1. Postupujte podle kroků v [tomto článku](vmware-azure-prepare-failback.md) připravit pro opětovné zajištění a navrácení služeb po obnovení, včetně nastavení procesního serveru v Azure a místní hlavní cílový server a konfigurace sítě VPN na webu nebo ExpressRoute privátní partnerský vztah pro navrácení služeb po obnovení.
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
    - Virtuální mamy VMware se nemůžou vrátit zpět do technologie Hyper-V.
    - Virtuální aplikace, které [byly migrovány](migrate-overview.md#what-do-we-mean-by-migration).
    - Virtuální ho virtuálního uživatele, který byl přesunut do jiné skupiny prostředků.
    - Replika virtuálního počítače Azure, který byl odstraněn.
    - Replika virtuálního počítače Azure, který není chráněný (replikace do místní lokality).
10. [Zkontrolujte typy navrácení služeb po obnovení,](concepts-types-of-failback.md) které můžete použít – obnovení původního umístění a obnovení alternativního umístění.


## <a name="enable-reprotection"></a>Povolit opětovnou ochranu

Povolte replikaci. Můžete znovu chránit konkrétní virtuální hody nebo plán obnovení:

- Pokud znovu protect plán obnovení, je nutné zadat hodnoty pro každý chráněný počítač.
- Pokud virtuální počítače patří do replikační skupiny pro konzistenci více virtuálních počítačů, lze jet pouze znovu chráněné pomocí plánu obnovení. Virtuální servery v replikační skupině musí používat stejný hlavní cílový server.

### <a name="before-you-start"></a>Než začnete

- Po spuštění virtuálního počítače v Azure po převzetí služeb při selhání trvá agent zaregistrovat zpět na konfigurační server (až 15 minut). Během této doby nebudete moci znovu chránit a chybová zpráva označuje, že agent není nainstalován. Pokud k tomu dojde, počkejte několik minut a pak znovu chraňte.
- Pokud chcete navrácení virtuálního počítače Azure zpět do existujícího místního virtuálního počítače, připojte místní úložiště dat virtuálního počítače s přístupem pro čtení a zápis na hostiteli ESXi hlavního cílového serveru.
- Pokud chcete vrátit zpět do alternativního umístění, například pokud místní virtuální počítač neexistuje, vyberte jednotku pro uchovávání informací a úložiště dat, které jsou nakonfigurované pro hlavní cílový server. Když se vrátíte do místního webu, virtuální počítače VMware v plánu ochrany navrácení služeb po selhání používají stejné úložiště dat jako hlavní cílový server. Nový virtuální virtuální město se pak vytvoří v vCentru.

Povolit opětovné protekci takto:

1. Vyberte možnost**Replikované položky** **úložiště** > . Klikněte pravým tlačítkem myši na virtuální počítač, který převzal a vyberte **znovu zamknout**. Nebo z příkazových tlačítek vyberte zařízení a pak vyberte **Znovu chránit**.
2. Ověřte, že je vybrán směr ochrany **Azure na místní.**
3. V **části Hlavní cílový server** a procesní **server**vyberte místní hlavní cílový server a procesový server.  
4. V **části Datastore**vyberte úložiště dat, do kterého chcete disky obnovit místně. Tato možnost se používá při odstranění místního virtuálního počítače a potřebujete vytvořit nové disky. Tato možnost je ignorována, pokud disky již existují. Stále je třeba zadat hodnotu.
5. Vyberte retenční jednotku.
6. Automaticky se vyberou zásady navrácení služeb po obnovení.
7. Chcete-li zahájit reprotekci, vyberte **možnost OK.**

    ![Dialogové okno Znovu zamknout](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. Úloha začne replikovat virtuální počítač Azure do místního webu. Průběh můžete sledovat na kartě **Úlohy**.
    - Když je reprotection úspěšné, virtuální počítač přejde do chráněného stavu.
    - Místní virtuální počítač se během opětovného nastavování ochrany vypne. To pomáhá zajistit konzistenci dat během replikace.
    - Nezapínejte místní virtuální počítač po dokončení opětovné ochrany.
   

## <a name="next-steps"></a>Další kroky

- Pokud narazíte na nějaké problémy, přečtěte si [článek o řešení potíží](vmware-azure-troubleshoot-failback-reprotect.md).
- Po azure virtuálních počítačích jsou chráněné, můžete [spustit navrácení služeb po selhání](vmware-azure-failback.md). Navrácení služeb po službu po službě 14: vypnutí virtuálního počítače Azure a spuštění místního virtuálního počítače. Očekávejte některé prostoje pro aplikaci a zvolte dobu navrácení služeb po selhání odpovídajícím způsobem.


