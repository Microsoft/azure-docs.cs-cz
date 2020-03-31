---
title: Možnosti migrace dat ze zařízení řady StorSimple řady 5000-7000
description: Obsahuje přehled možností migrace dat z řady StorSimple 5000-7000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 44a05ecb273bdf7582300c3b6a9110e2ada0994c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471818"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Možnosti migrace dat ze zařízení StorSimple řady 5000–7000 

> [!IMPORTANT]
> Července 9, 2019 Série StorSimple 5000/7000 dosáhne stavu ukončení podpory (EOS). Doporučujeme, aby zákazníci řady StorSimple 5000/7000 migrovali na jednu z alternativ popsaných v dokumentu.

Řada StorSimple 5000-7000 se blíží [ke konci podpory](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) v červenci 2019. Zákazníci, kteří používají řadu StorSimple 5000-7000, mají možnost upgradovat na jiné hybridní služby Azure první strany. Tento článek popisuje hybridní možnosti Azure, které jsou k dispozici pro migraci dat. 

## <a name="migration-options"></a>Možnosti migrace

Zákazníci, kteří používají řadu StorSimple 5000-7000, mají možnosti Azure nebo třetích stran.

### <a name="azure-options"></a>Možnosti Azure

#### <a name="upgrade-to-storsimple-8000-series"></a>Upgrade na řadu StorSimple 8000

Upgradujte na řadu StorSimple 8000 a pokračujte tak na platformě StorSimple.  Tato cesta upgradu vyžaduje, aby zákazníci nahradili zařízení řady 5000-7000 řadou 8000. Data jsou migrována ze zařízení řady 5000-7000 pomocí nástroje pro migraci. Po úspěšném dokončení migrace zařízení řady StorSimple 8000 budou nadále vrstvit data do úložiště objektů blob Azure. 

Další informace o migraci dat pomocí řady StorSimple 8000 naleznete [v části Migrace dat ze série StorSimple 5000-7000 do zařízení řady 8000](storsimple-8000-migrate-from-5000-7000.md).

#### <a name="migrate-to-azure-file-sync"></a>Migrace do Synchronizace souborů Azure

Tato zcela nová možnost migrace umožňuje zákazníkům ukládat sdílené složky jejich organizace do souborů Azure. Tyto sdílené složky jsou pak centralizované pro místní přístup pomocí Azure File Sync (AFS). Systém AFS lze nasadit na hostitele systému Windows Server. Skutečná migrace dat se pak provádí jako kopie hostitele nebo pomocí nástroje pro migraci.

Další informace o tom, jak migrovat data do Azure File Sync, přejděte k [migraci dat z řady StorSimple 5000-7000 do Azure File Sync](storsimple-5000-7000-afs-migration.md).

### <a name="third-party-options"></a>Možnosti třetích stran

#### <a name="migrate-to-panzura-freedom-nas"></a>Migrace na Panzura Svoboda NAS

Zákazníci StorSimple 5000-7000 se mohou rozhodnout migrovat na Panzura Freedom NAS, aby uchovávali svá data v Azure. Řešení Panzura Freedom poskytuje řešení NAS, které zahrnuje datová centra, kanceláře, veřejné i soukromé cloudy. Toto řešení umožňuje místní, hybridní a cloudové datové pracovní postupy pro klienty nfs, smb a mobilní klienty. 

Tuto migraci podporuje panzura a zákazníci mohou začít tím, že požádá o podporu migrace z [webových stránek Panzura](https://panzura.com/storsimple-migration/).

#### <a name="migrate-to-cohesity"></a>Migrace do konhezity

Cohesity umožňuje migrovat data z aktuálního StorSimple 5000–7000 na platformu Cohesity Data V Azure. Cohesity Data Platform je softwarově definované webové řešení, které konsoliduje soubory, zálohy, objekty a virtuální počítače do jednoho cloudového nativního řešení. Po migraci na datovou platformu můžete spravovat, chránit a zřazovat data a aplikace z cloudu do jádra prostřednictvím jediného skleněného podokna. S Cohesity začněte s tak málo, jak tři uzly. 

Další informace o [migraci na platformu Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### <a name="migrate-to-nasuni"></a>Migrace do Nasuni

Nasuni usnadňuje zákazníkům StorSimple 5000-7000 migraci a uchovávanost dat v Azure.  Nasuni je přední řešení úložišť NAS založené na Azure, které zákazníkům poskytuje výkon a zabezpečení, které očekávají od řešení on-prem, s cloudovou ekonomií a škálovat.  Kromě vysoce výkonného úložiště souborů zpracovávají Nasuni a Azure zálohování a zotavení po havárii a zároveň vám umožňují sdílet data po celém světě a spolupracovat na ní díky centralizované správě úložiště souborů. 

Nasuni má zkušenosti, aby vaše migrace snadné - začít ještě dnes:https://info.nasuni.com/nasuni-storsimple-migration

#### <a name="migrate-to-talon-fast"></a>Migrace do Talonu FAST

Talon usnadňuje zákazníkům StorSimple 5000-7000 nadále využívat výhody, které tolik oceňují na platformě StorSimple (malá stopa na místě s neomezenými cloudovými prostředky) s ještě větší funkcí.  Díky řešení Talon FAST můžou zákazníci migrovat a uchovávat svá data v Azure, zatímco teď mají ještě menší nároky na vlastní prostředí a přidávají výhody, jako je globální zamykání souborů, globální obor názvů a spolupráce na více pracovištích.  Talon je přední ekosystémové řešení Azure, které spolupracuje s globálními zákazníky na migraci jejich místních úloh souborového serveru do konsolidované stopy založené na Azure, aniž by došlo ke zlepšení pracovního postupu nebo prostředí uživatelů.  

Další informace o tom, jak se vyvíjet na cloudově konsolidovaný podnik na jdete na . https://www.talonstorage.com/alliances/microsoft-storsimple


## <a name="migration---frequently-asked-questions"></a>Migrace – nejčastější dotazy

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>Otázka: Kdy se zařízení řady StorSimple 5000 a 7000 dostanou do konce služby? 

A. Řada StorSimple 5000-7000 dosáhne [konce služby](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) v červenci 2019. Ukončení služby znamená, že společnost Microsoft již nebude moci poskytovat podporu hardwaru i softwaru těchto zařízení po červenci 2019. Důrazně doporučujeme, abyste nyní začali formulovat plán migrace dat ze svých zařízení.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>Otázka: Co se stane s daty, která jsem uložil v Azure?  

A. Data v Azure můžete dál používat, jakmile je migrujete do novější služby. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>Otázka: Co se stane s daty uloženými místně na zařízení StorSimple? 

A. Data, která je na místním zařízení lze zkopírovat do novější služby, jak je popsáno v dokumentech migrace.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>Otázka: Co se stane, když si chci ponechat svůj přístroj řady StorSimple 5000/7000? 

A. Zatímco služby mohou nadále fungovat, společnost Microsoft již nebude moci poskytovat hardwarovou a softwarovou podporu. Migrace se důrazně doporučuje pro kontinuitu provozu.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>Otázka: Jaké možnosti jsou k dispozici pro migraci dat ze zařízení řady StorSimple 5000-7000? 

A. V závislosti na jejich scénáři mají uživatelé řady StorSimple 5000-7000 následující možnosti migrace. 

 - **Upgrade na řadu 8000**: Tuto možnost použijte, pokud chcete pokračovat na platformě StorSimple. 
 - **Migrace do Azure File Sync:** Tuto možnost použijte, když chcete přepnout do nativního formátu Azure. Azure File Sync můžete použít pro centralizovanou správu sdílených složek. 

Můžete se obrátit na podporu společnosti Microsoft a projednat možnosti migrace, které zde nejsou uvedeny.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>Otázka: Je migrace na jiná řešení úložiště podporována?

A. Ano. Migrace do jiných řešení úložiště pomocí hostitelské kopie dat je podporována.

### <a name="q-is-migration-supported-by-microsoft"></a>Otázka: Je migrace podporována společností Microsoft? 

A. Migrace ze série 5000 nebo 7000 je plně podporovaná operace. Společnost Microsoft ve skutečnosti doporučuje před zahájením migrace kontaktovat podporu. Migrace je v současné době asistovaná operace. Pokud máte v úmyslu migrovat data ze zařízení řady StorSimple 5000-7000, [otevřete lístek podpory](storsimple-8000-contact-microsoft-support.md).

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>Otázka: Jaký je cenový model pro obě možnosti migrace?

A. Náklady na migraci se liší v závislosti na zvolené možnosti. Zatímco samotná migrace je zdarma, pokud se rozhodnete upgradovat na řadu StorSimple 8000, budou náklady na hardwarové zařízení. 

Podobně při použití Azure File Sync, mohou platit poplatky za předplatné pro službu. V každém případě budou zákazníci muset také hradit průběžné náklady na skladování. Odhad naleznete v následujících postupech: 
- [StorSimple ceny](https://azure.microsoft.com/pricing/details/storsimple/)  
- [Ceny AFS]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>Otázka:  Jak dlouho trvá dokončení migrace?

A. Doba migrace dat závisí na množství dat a vybrané možnosti upgradu. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>Otázka: Jaké je datum ukončení podpory pro řadu StorSimple 8000?

A. Datum ukončení podpory pro řadu StorSimple 8000 je publikováno [zde](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).


## <a name="next-steps"></a>Další kroky
 - [Migrace dat ze série StorSimple 5000-7000 do zařízení řady 8000](storsimple-8000-migrate-from-5000-7000.md).
 - [Migrace dat z řady StorSimple 5000-7000 do Azure File Sync](storsimple-5000-7000-afs-migration.md)
