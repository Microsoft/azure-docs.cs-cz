---
title: Možnosti migrace dat ze zařízení StorSimple 5000-7000 Series
description: Poskytuje přehled možností migrace dat z řady StorSimple 5000-7000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 2386a7f03508be2a3e6c57111ae206807efaf3ed
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567861"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Možnosti migrace dat ze zařízení StorSimple řady 5000–7000 

> [!IMPORTANT]
> 9. července 2019 bude StorSimple série 5000/7000 získat stav EOS (konec podpory). Zákazníkům řady StorSimple 5000/7000 doporučujeme migrovat na jednu z alternativ popsaných v dokumentu.

Série StorSimple 5000-7000 se blíží [konci podpory](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) v červenci 2019. Zákazníci, kteří používají sadu StorSimple 5000-7000, mají možnost upgradovat na jiné hybridní služby Azure First stran. Tento článek popisuje možnosti hybridního Azure, které jsou k dispozici pro migraci dat. 

## <a name="migration-options"></a>Možnosti migrace

Zákazníci, kteří používají řadu StorSimple 5000-7000, mají možnosti Azure nebo třetích stran.

### <a name="azure-options"></a>Možnosti Azure

#### <a name="upgrade-to-storsimple-8000-series"></a>Upgrade na řadu StorSimple 8000

Upgradujte na řady StorSimple 8000 a tak pokračujte na platformě StorSimple.  Tato cesta k upgradu vyžaduje, aby zákazníci nahradili své zařízení řady 5000-7000 s řadou 8000. Data se migrují ze zařízení řady 5000-7000 pomocí nástroje pro migraci. Až se migrace úspěšně dokončí, zařízení řady StorSimple 8000 budou dál zobrazovat data na úrovni na Azure Blob Storage. 

Další informace o tom, jak migrovat data pomocí řady StorSimple 8000, najdete v tématu [migrace dat z řady StorSimple 5000-7000 do zařízení 8000 series](storsimple-8000-migrate-from-5000-7000.md).

#### <a name="migrate-to-azure-file-sync"></a>Migrace do Synchronizace souborů Azure

Tato značka nové možnosti migrace umožňuje zákazníkům ukládat sdílené složky ve své organizaci do souborů Azure. Tyto sdílené soubory jsou potom centralizované pro místní přístup pomocí Synchronizace souborů Azure (AFS). AFS se dá nasadit na hostitele Windows serveru. Skutečná migrace dat se pak provede jako kopie hostitele nebo pomocí nástroje pro migraci.

Další informace o tom, jak migrovat data na Synchronizace souborů Azure, najdete v článku [migrace dat z řady StorSimple 5000-7000 na synchronizace souborů Azure](../storage/files/storage-files-migration-storsimple-8000.md).

### <a name="third-party-options"></a>Možnosti třetích stran

#### <a name="migrate-to-panzura-freedom-nas"></a>Migrace na server Panzura svobody

Zákazníci s StorSimple 5000-7000 se můžou rozhodnout migrovat na Panzura svobodu serveru Azure a uchovávat jejich data v Azure. Řešení svobody Panzura poskytuje řešení NAS, které pokrývá datová centra, pobočky, veřejné a privátní cloudy. Řešení umožňuje místní, hybridní a cloudové pracovní postupy dat pro NFS, SMB a mobilní klienty. 

Tato migrace je podporovaná Panzura a zákazníci můžou začít tím, že požádají o podporu migrace z [webu Panzura](https://panzura.com/storsimple-migration/).

#### <a name="migrate-to-cohesity"></a>Migrace na Cohesity

Cohesity vám umožňuje migrovat data z aktuální StorSimple 5000 – 7000 na Cohesity datovou platformu v Azure. Cohesity data Platform je softwarově definované řešení, které konsoliduje soubory, zálohy, objekty a virtuální počítače do jednoho nativního řešení v cloudu. Po migraci na datovou platformu můžete spravovat, chránit a zřizovat data a aplikace z cloudu do jádra přes jediné podokno skla. V Cohesity začněte s až malým počtem uzlů. 

Další informace o [migraci na datovou platformu Cohesity](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html)

#### <a name="migrate-to-nasuni"></a>Migrace na Nasuni

Nasuni usnadňuje zákazníkům s StorSimple 5000-7000 migraci a uchovávání dat v Azure.  Nasuni je špičkové řešení úložiště založené na Azure, které zákazníkům poskytuje výkon a zabezpečení, které očekávají od Prem řešení, s využitím cloudových hospodárnosti a škálování.  Kromě vysoce výkonného úložiště souborů Nasuni a Azure zaznamená zálohování a zotavení po havárii a zároveň vám umožní sdílet data po celém světě s využitím centralizované správy úložiště souborů a spolupracovat s nimi. 

Nasuni je prostředí, které usnadňuje migraci – Začněte ještě dnes: https://info.nasuni.com/nasuni-storsimple-migration

#### <a name="migrate-to-talon-fast"></a>Migrace na Talon FAST

Talon usnadňuje zákazníkům s StorSimple 5000-7000 i nadále využívání výhod, které oceňují, a to v StorSimple platformě (malými nároky na pracoviště zajištěnými neomezenými prostředky cloudu) a ještě větší funkcí.  Díky RYCHLÉmu řešení Talon můžou zákazníci migrovat a uchovávat data v Azure. teď ale mají ještě menší nároky na místo na pracovišti a přidávají výhody, jako je globální zamykání souborů, globální obor názvů a spolupráce s více lokalitami.  Talon je přední řešení ekosystému Azure, které spolupracuje s globálními zákazníky k migraci místních úloh souborového serveru do konsolidovaného prostředí založeného na Azure, aniž by došlo k narušení pracovního postupu nebo zkušeností uživatelů.  

Přečtěte si další informace o tom, jak vyvíjet do cloudu konsolidovaného podniku.

## <a name="migration---frequently-asked-questions"></a>Migrace – Nejčastější dotazy

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>Otázka: Když zařízení řady StorSimple 5000 a 7000 dosáhnou konce služby? 

A. StorSimple 5000-7000 Series dosahuje [konce služby](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) v červenci 2019. Konec služby znamená, že společnost Microsoft už nebude moct po 2019. července poskytovat podporu pro hardware i software těchto zařízení. Důrazně doporučujeme začít formulovat plán pro migraci dat ze svých zařízení.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>Otázka: Co se stane s daty uloženými v Azure?  

A. Data v Azure můžete po migraci do novější služby nadále používat. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>Otázka: Co se stane s daty uloženými místně na zařízení StorSimple? 

A. Data umístěná na místním zařízení je možné zkopírovat do novější služby, jak je popsáno v dokumentu migrace.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>Otázka: Co se stane, když si přeji zachovat zařízení řady StorSimple 5000/7000? 

A. I když mohou služby fungovat i nadále, společnost Microsoft již nebude moci poskytovat hardwarovou a softwarovou podporu. Migrace se důrazně doporučuje pro provozní kontinuitu.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>Otázka: Jaké možnosti jsou k dispozici pro migraci dat ze zařízení řady StorSimple 5000-7000? 

A. V závislosti na jejich scénáři mají uživatelé řady StorSimple 5000-7000 následující možnosti migrace. 

 - **Upgrade na 8000 series**: tuto možnost použijte, když chcete pokračovat na platformě StorSimple. 
 - **Migrace na synchronizace souborů Azure**: tuto možnost použijte, pokud chcete přepnout do nativního formátu Azure. Synchronizace souborů Azure můžete použít pro centralizovanou správu sdílených složek. 

Můžete kontaktovat podpora Microsoftu a diskutovat na možnosti migrace, které tady nejsou uvedené.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>Otázka: Je migrace na jiná řešení úložiště podporovaná?

A. Ano. Migrace na jiná řešení úložiště pomocí hostitelské kopie dat je podporovaná.

### <a name="q-is-migration-supported-by-microsoft"></a>Otázka: Je migrace podporovaná Microsoftem? 

A. Migrace z řady 5000 nebo 7000 je plně podporovaná operace. Společnost Microsoft ve skutečnosti doporučuje, abyste si před zahájením migrace vydosáhli podpory. Migrace je aktuálně pomocná operace. Pokud máte v úmyslu migrovat data ze zařízení řady StorSimple 5000-7000, [otevřete lístek podpory](storsimple-8000-contact-microsoft-support.md).

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>Otázka: Jaký je cenový model pro obě možnosti migrace?

A. Náklady na migraci se liší v závislosti na zvolené možnosti. I když je migrace sama o sobě, pokud se rozhodnete upgradovat na řadu StorSimple 8000, bude se jednat o náklady na hardwarové zařízení. 

Obdobně platí, že při použití Synchronizace souborů Azure mohou platit poplatky za předplatné služby. V každém případě budou muset zákazníci platit i průběžné náklady na úložiště. Pro odhad použijte následující informace: 
- [Ceny StorSimple](https://azure.microsoft.com/pricing/details/storsimple/)  
- [Ceny pro AFS]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>Otázka:  Jak dlouho trvá dokončení migrace?

A. Čas k migraci dat závisí na množství dat a vybrané možnosti upgradu. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>Otázka: Jaký je konec platnosti podpory pro řadu StorSimple 8000?

A. [Tady](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)se zveřejňuje datum podpory pro řadu StorSimple 8000.


## <a name="next-steps"></a>Další kroky
 - [Migrujte data z řady StorSimple 5000-7000 na zařízení řady 8000](storsimple-8000-migrate-from-5000-7000.md).
 - [Migrace dat z řady StorSimple 5000-7000 na Synchronizace souborů Azure](../storage/files/storage-files-migration-storsimple-8000.md)