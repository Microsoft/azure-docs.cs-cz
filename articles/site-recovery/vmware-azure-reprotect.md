---
title: Opětovné zapnutí ochrany virtuálních počítačů VMware na místní lokalitu pomocí Azure Site Recovery
description: Přečtěte si, jak znovu chránit virtuální počítače VMware po převzetí služeb při selhání do Azure pomocí Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mayg
ms.openlocfilehash: 6a11e3d0cb41383b44b76975ecbd1c2ae2825015
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89441489"
---
# <a name="reprotect-from-azure-to-on-premises"></a>Zpětná replikace z Azure do místního prostředí

Po [převzetí služeb při selhání](site-recovery-failover.md) místních virtuálních počítačů VMware nebo fyzických serverů do Azure je prvním krokem při selhání zpět na místní lokalitu, aby se znovu chránily virtuální počítače Azure vytvořené během převzetí služeb při selhání. Tento článek popisuje, jak to provést. 

## <a name="before-you-begin"></a>Než začnete

1. Postupujte podle kroků v [tomto článku](vmware-azure-prepare-failback.md) a připravte se na opětovnou ochranu a navrácení služeb po obnovení, včetně nastavení procesového serveru v Azure, místního hlavního cílového serveru a konfigurace sítě Site-to-Site VPN nebo ExpressRoute privátního partnerského vztahu pro navrácení služeb po obnovení.
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
    - Virtuální počítače VMware se nemůžou navrátit zpátky do Hyper-V.
    - Virtuální počítače, které byly migrovány.
    - Virtuální počítač, který se přesunul do jiné skupiny prostředků.
    - Replika virtuálního počítače Azure, který se odstranil.
    - Replika virtuálního počítače Azure, která není chráněná (replikuje se na místní lokalitu).
10. [Zkontrolujte typy navrácení služeb](concepts-types-of-failback.md) po obnovení, které můžete použít – obnovení původního umístění a alternativní umístění obnovení.


## <a name="enable-reprotection"></a>Povolení ochrany

Povolte replikaci. Můžete znovu chránit konkrétní virtuální počítače nebo plán obnovení:

- Pokud znovu nastavíte ochranu plánu obnovení, je nutné zadat hodnoty pro každý chráněný počítač.
- Pokud virtuální počítače patří do replikační skupiny pro konzistenci více virtuálních počítačů, dají se znovu chránit jenom pomocí plánu obnovení. Virtuální počítače v replikační skupině musí používat stejný hlavní cílový server.

>[!NOTE]
>Objem dat odesílaných z Azure do Erstwhile zdroje během opětovného zapnutí ochrany může být cokoli z 0 bajtů a celková velikost disku pro všechny chráněné počítače a nedá se vypočítat.

### <a name="before-you-start"></a>Než začnete

- Až se virtuální počítač spustí v Azure po převzetí služeb při selhání, může se agent nějaký čas zaregistrovat zpátky na konfigurační server (až 15 minut). Během této doby nebudete moci znovu zapnout ochranu a chybová zpráva indikuje, že agent není nainstalován. Pokud k tomu dojde, počkejte několik minut a pak znovu nastavte ochranu.
- Pokud chcete převzít služby při selhání virtuálního počítače Azure na stávající místní virtuální počítač, připojte místní úložiště dat virtuálních počítačů s přístupem pro čtení a zápis na hostiteli ESXi hlavního cílového serveru.
- Pokud chcete navrátit služby po obnovení do alternativního umístění, například pokud místní virtuální počítač neexistuje, vyberte jednotku pro uchovávání a úložiště dat nakonfigurovanou pro hlavní cílový server. Po navrácení služeb po obnovení do místní lokality používá virtuální počítače VMware v plánu ochrany navrácení služeb po obnovení stejné úložiště dat jako hlavní cílový server. V vCenter se pak vytvoří nový virtuální počítač.

Povolte znovu ochranu následujícím způsobem:

1. Vyberte **úložiště**  >  **replikované položky**. Klikněte pravým tlačítkem na virtuální počítač, u kterého došlo k převzetí služeb při selhání, a pak vyberte **znovu zapnout ochranu**. Případně můžete z příkazových tlačítek vybrat počítač a pak vybrat možnost **znovu zapnout ochranu**.
2. Ověřte, že je vybraná možnost **Azure do místního** směru ochrany.
3. Na **hlavním cílovém serveru** a **procesovém serveru** vyberte místní hlavní cílový server a procesový Server.  
4. V části **úložiště dat** vyberte úložiště dat, do kterého chcete místně obnovit disky. Tato možnost se používá, když se místní virtuální počítač odstraní a potřebujete vytvořit nové disky. Tato možnost je ignorována, pokud již disky existují. Stále musíte zadat hodnotu.
5. Vyberte jednotku pro uchovávání.
6. Automaticky se vyberou zásady navrácení služeb po obnovení.
7. Kliknutím na **tlačítko OK** zahajte znovu ochranu.

    ![Dialogové okno opětovného zapnutí ochrany](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. Úloha začne replikovat virtuální počítač Azure do místní lokality. Průběh můžete sledovat na kartě **Úlohy**.
    - Po úspěšném provedení ochrany virtuální počítač přejde do chráněného stavu.
    - Místní virtuální počítač se během opětovného nastavování ochrany vypne. To pomáhá zajistit konzistenci dat během replikace.
    - Po dokončení opětovné ochrany nezapněte místní virtuální počítač.
   

## <a name="next-steps"></a>Další kroky

- Pokud narazíte na nějaké problémy, přečtěte si [článek věnované řešení potíží](vmware-azure-troubleshoot-failback-reprotect.md).
- Po ochraně virtuálních počítačů Azure můžete [Spustit navrácení služeb po obnovení](vmware-azure-failback.md). Navrácení služeb po obnovení vypne virtuální počítač Azure a spustí místní virtuální počítač. Pro aplikaci můžete očekávat nějaké výpadky a podle toho nastavit čas navrácení služeb po obnovení.


