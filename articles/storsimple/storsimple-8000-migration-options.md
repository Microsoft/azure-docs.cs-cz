---
title: Vyhodnocení možností pro migraci dat ze StorSimple řad 5000 – 7000 | Dokumentace Microsoftu
description: Poskytuje přehled možností pro migraci dat z řady StorSimple 5000 – 7000.
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
ms.date: 08/23/2018
ms.author: alkohli
ms.openlocfilehash: 8f34d5a38f09f015547f52cc4b44819b780932bb
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818854"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Možnosti, jak migrovat data ze StorSimple řad 5000 – 7000 

> [!IMPORTANT]
> Do 31. července 2019 řady StorSimple 5000/7000 skončí jeho stavu podpory (SESTAVENÁ). Doporučujeme vám, že zákazníci řady StorSimple 5000/7000 migrace na jednu z alternativ popsané v dokumentu.

StorSimple řad 5000 – 7000 dosahuje [konec podpory](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) v červenci 2019. Zákazníci, kteří používají StorSimple řad 5000 – 7000 máte možnost upgradu na jiné službě Azure první strany hybridní služby. Tento článek popisuje dostupné k migraci dat Azure hybridní možnosti. 

## <a name="migration-options"></a>Možnosti migrace

Zákazníci, kteří používají StorSimple řad 5000 – 7000 mají následující dvě klíčové možnosti:

- **Upgrade na StorSimple řady 8000** – Upgrade na StorSimple 8000 series a proto pokračovat na platformě StorSimple.  Tento způsob upgradu zvolte vyžaduje, aby zákazníci na svých zařízeních řad 5000 – 7000 nahraďte 8000 series. Pomocí nástroje pro migraci se migrují data ze zařízení řady 5000-7000. Po úspěšném dokončení migrace zařízeních StorSimple řady 8000 nadále úroveň dat do Azure Blob Storage. 

    Další informace o tom, jak migrovat data pomocí řady StorSimple 8000, přejděte na [migrovat data ze StorSimple řad 5000 – 7000 zařízení řady 8000](storsimple-8000-migrate-from-5000-7000.md).

- **Migrace do Azure File Sync** – tento úplně nový možnost migrace umožňuje zákazníkům umožňuje ukládat sdílené složky organizace ve složce služby soubory Azure. Pro přístup k místnímu používání Azure File Sync (AFS) jsou pak centralizované těchto sdílených složek. AFS můžete nasadit na hostitelském Windows serveru. Skutečná data migrace je pak provést, protože hostitel kopírování nebo použití nástroj pro migraci.

    Další informace o tom, jak migrovat data do Azure File Sync, přejděte na [migrovat data ze StorSimple řad 5000 – 7000 do Azure File Sync](https://aka.ms/StorSimpleMigrationAFS).

## <a name="migration---frequently-asked-questions"></a>Migrace – nejčastější dotazy

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>Otázka: Když StorSimple 5000 a 7000 series zařízení dosáhnout konce služby? 

A. StorSimple řad 5000 – 7000 oslovit [konec služby](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) v červenci 2019. Konec služby znamená, že Microsoft bude už moct poskytovat podporu pro hardware a software takových zařízení po 2019. července. Důrazně doporučujeme, že začínáte zpracovávající plán pro migraci dat ze zařízení teď.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>Otázka: Co se stane s daty, která jsem uloženého v Azure?  

A. Můžete nadále používat data v Azure po migraci do služby novější. 


### <a name="q--what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>Otázka:  Co se stane s daty, která jsem ukládají místně na svém zařízení StorSimple? 

A. Data, která je na místním zařízení je možné zkopírovat do novější službu tak, jak je popsáno v dokumentech migrace.

### <a name="what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>Co se stane, když budu chtít zachovat Moje zařízení řady StorSimple 5000/7000? 

A. Služby mohou pokračovat v práci, Microsoft si už ho může poskytovat podporu hardwaru a softwaru. Migrace je důrazně doporučujeme pro zajištění kontinuity.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>Otázka: Jaké možnosti jsou k dispozici pro migraci dat ze StorSimple 5000 – 7000 series zařízení? 

A. V závislosti na jejich scénáři StorSimple 5000 – 7000 series uživatele máte následující možnosti migrace. 

 - **Upgrade na 8000 series**: tuto možnost použijte, pokud chcete pokračovat na platformě StorSimple. 
 - **Migrace do Azure File Sync**: tuto možnost použijte, pokud chcete přejít na Azure nativním formátu. Pro centralizovanou správu sdílených složek můžete použít Azure File Sync. 

Obraťte se na Microsoft Support můžete projednávat možnosti migrace, které tu nejsou uvedené.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>Otázka: Jiná řešení úložišť, které jsou podporovány, je migrace?

A. Ano. Migrace na jiné řešení úložiště, pomocí kopie dat je podporovaná.

### <a name="q-is-migration-supported-by-microsoft"></a>Otázka: Společnost Microsoft podporuje migrace? 

A. Migrace z řad 5000 a 7000 je plně podporovaná operace. Ve skutečnosti společnost Microsoft doporučuje, obraťte se na podporu před zahájením migrace. Migrace se aktuálně operaci s asistencí. Pokud máte v úmyslu migrovat data ze StorSimple 5000 – 7000 series zařízení, [vytvořit lístek podpory](storsimple-8000-contact-microsoft-support.md).

### <a name="q-how-does-the-cost-compare-for-the-two-listed-migrations-to-azure-hybrid-services"></a>Otázka: Jak náklady na porovnání pro dva uvedené migrace do služby Azure hybrid? 

A. Náklady na migraci se liší v závislosti na vámi zvolené možnosti. Při migraci samotného je zdarma, pokud se rozhodnete pro upgrade na řadu StorSimple 8000, bude existovat náklady na hardwarovém zařízení. Podobně při používání Azure File Sync, může použít předplatné poplatků za služby. V obou případech se zákazníci také mají platit náklady na průběžnou úložiště. Odkazovat na [Microsoft pomocí cenové kalkulačky pro příslušné služby](https://azure.microsoft.com/pricing/#product-picker) odhad.  

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>Otázka:  Jak dlouho trvá dokončení migrace?

A. Čas na migraci dat závisí na množství dat a možnost pro upgrade vybrali. 

## <a name="next-steps"></a>Další postup
 - [Migrovat data z StorSimple řad 5000 – 7000 zařízení řady 8000](storsimple-8000-migrate-from-5000-7000.md).
 - [Migrovat data z StorSimple 5000 – 7000 series do Azure File Sync](storsimple-5000-7000-afs-migration.md)
