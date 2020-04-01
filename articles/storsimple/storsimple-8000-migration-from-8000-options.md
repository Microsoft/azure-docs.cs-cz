---
title: Možnosti migrace dat ze zařízení řady StorSimple 8000
description: Obsahuje přehled možností migrace dat z řady StorSimple 8000.
services: storsimple
author: priestlg
ms.service: storsimple
ms.topic: article
ms.date: 03/25/2020
ms.author: v-grpr
ms.openlocfilehash: f712486c8035bbc913fbd229759f3415a1005449
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80438317"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>Možnosti migrace dat z řady StorSimple 8000

> [!IMPORTANT]
> 31. prosince 2022 dosáhne řada StorSimple 8000 stavu ukončení podpory (EOS). Doporučujeme, aby zákazníci řady StorSimple 8000 migrovali na jednu z alternativ popsaných v dokumentu.

Řada StorSimple 8000 se blíží [ke konci podpory](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) v prosinci 2022. Zákazníci, kteří používají řadu StorSimple 8000, mají možnost upgradovat na jiné hybridní služby Azure první strany. Tento článek popisuje hybridní možnosti Azure, které jsou k dispozici pro migraci dat.

## <a name="migration-options"></a>Možnosti migrace

Zákazníci, kteří používají řadu StorSimple 8000, mají možnosti Azure nebo třetích stran.

### <a name="azure-options"></a>Možnosti Azure

#### <a name="migrate-to-azure-file-sync"></a>Migrace do Synchronizace souborů Azure

Tato zcela nová možnost migrace umožňuje zákazníkům ukládat sdílené složky jejich organizace do souborů Azure. Tyto sdílené složky jsou pak centralizované pro místní přístup pomocí Azure File Sync (AFS). Systém AFS lze nasadit na hostitele systému Windows Server. Skutečná migrace dat se pak provádí jako kopie hostitele nebo pomocí nástroje pro migraci.

Další informace o tom, jak migrovat data do Azure File Sync, přejděte na [StorSimple 8100 a 8600 migrace do Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000).

<!-- 03/25/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
### Third-party options

#### Migrate to Panzura Freedom NAS

StorSimple 5000-7000 customers can choose to migrate to Panzura Freedom NAS to keep their data in Azure. Panzura Freedom solution provides a NAS solution that spans datacenters, offices, public and private clouds. The solution enables local, hybrid, and in-cloud data workflows for NFS, SMB, and mobile clients. 

This migration is supported by Panzura and customers can get started by requesting migration support from the [Panzura website](https://panzura.com/storsimple-migration/).

#### Migrate to Cohesity

Cohesity enables you to migrate data from your current StorSimple 5000–7000 to the Cohesity Data Platform on Azure. The Cohesity Data Platform is a software-defined web-scale solution that consolidates files, backups, objects, and VMs onto a single cloud-native solution. After migration to the Data Platform, you can manage, protect, and provision data and apps from cloud to core through a single pane of glass. With Cohesity, start with as few as three nodes. 

Learn more on [migration to the Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### Migrate to Nasuni

Nasuni makes it easy for StorSimple 5000-7000 customers to migrate and keep their data in Azure.  Nasuni is a leading Azure-based NAS storage solution, giving customers the performance and security they expect from on-prem solutions, with cloud economics and scale.  In addition to high performance file storage, Nasuni and Azure handle backup and DR, while allowing you to share and collaborate on your data around the globe with centralized file storage management. 

Nasuni has the experience to make your migration easy – get started today: https://info.nasuni.com/nasuni-storsimple-migration

#### Migrate to Talon FAST

Talon makes it easy for StorSimple 5000-7000 customers to continue to leverage the benefits they valued so much in the StorSimple platform (small on-site footprint backed by unlimited cloud resources) with even greater function.  With the Talon FAST solution, customers can migrate and keep their data in Azure, while now having an even smaller software-only onsite footprint and adding benefits such as global file locking, global namespace, and multi-site collaboration.  Talon is a leading Azure ecosystem solution, working with global customers to migrate their on-premises file server workloads into a consolidated, Azure-based footprint without compromising user workflow or experience.  

Learn more about how to evolve to a cloud-consolidated enterprise at https://www.talonstorage.com/alliances/microsoft-storsimple.
-->

## <a name="migration---frequently-asked-questions"></a>Migrace – nejčastější dotazy

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>Otázka: Kdy se zařízení řady StorSimple 8000 dostanou do konce služby?

A. Řada StorSimple 8000 [dosáhla konce podpory](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) v prosinci 2022. Ukončení podpory znamená, že společnost Microsoft již nebude moci poskytovat podporu hardwaru i softwaru těchto zařízení po prosinci 2022. Důrazně doporučujeme, abyste nyní začali formulovat plán migrace dat ze svých zařízení.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>Otázka: Co se stane s daty, která jsem uložil v Azure?  

A. Data v Azure můžete dál používat, jakmile je migrujete do novější služby.

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>Otázka: Co se stane s daty uloženými místně na zařízení StorSimple?

A. Data, která je na místním zařízení lze zkopírovat do novější služby, jak je popsáno v dokumentech migrace.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>Otázka: Co se stane, když si chci ponechat svůj přístroj řady StorSimple 8000?

A. Zatímco služby mohou nadále fungovat, společnost Microsoft již nebude moci poskytovat hardwarovou a softwarovou podporu. Migrace se důrazně doporučuje pro kontinuitu provozu.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>Otázka: Jaké možnosti jsou k dispozici pro migraci dat ze zařízení řady StorSimple 8000?

A. V závislosti na jejich scénáři mají uživatelé řady StorSimple 8000 následující možnosti migrace:

* **Migrace do Azure File Sync:** Tuto možnost použijte, když chcete přepnout do nativního formátu Azure. Azure File Sync můžete použít pro centralizovanou správu sdílených složek.

* **Další možnosti**: Můžete se obrátit na podporu společnosti Microsoft a projednat zde uvedené možnosti migrace.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>Otázka: Je migrace na jiná řešení úložiště podporována?

A. Ano. Migrace do jiných řešení úložiště pomocí hostitelské kopie dat je podporována.

### <a name="q-is-migration-supported-by-microsoft"></a>Otázka: Je migrace podporována společností Microsoft?

A. Migrace ze série 8000 je plně podporovaná operace. Společnost Microsoft ve skutečnosti doporučuje před zahájením migrace kontaktovat podporu. Migrace je v současné době asistovaná operace. Pokud máte v úmyslu migrovat data ze zařízení řady StorSimple 8000, [obraťte se na podporu StorSimple](mailto:storsimp@microsoft.com).

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>Otázka: Jaký je cenový model pro migraci do Azure File Sync?

A. Při použití Azure File Sync, může platit poplatky za předplatné pro službu. Zákazníci budou také muset platit průběžné náklady na skladování. Podívejte se na [afs ceny]( https://azure.microsoft.com/pricing/details/storage/files/) pro odhad.

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>Otázka: Jak dlouho trvá dokončení migrace?

A. Doba migrace dat závisí na množství dat a vybrané možnosti upgradu.

## <a name="next-steps"></a>Další kroky

* [Migrace dat z řady StorSimple 8000 do Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)
