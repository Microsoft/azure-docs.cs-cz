---
title: Možnosti migrace dat ze zařízení StorSimple 8000 series
description: Poskytuje přehled možností migrace dat z řady StorSimple 8000.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 03/25/2020
ms.author: alkohli
ms.openlocfilehash: 78ac7e849a3a8f1742701006638a2cb72299bad7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90055771"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>Možnosti migrace dat z řady StorSimple 8000

> [!IMPORTANT]
> 31. prosince 2022 bude StorSimple série 8000 získat stav EOS (konec podpory). Zákazníkům řady StorSimple 8000 doporučujeme migrovat na jednu z alternativ popsaných v dokumentu.

Série StorSimple 8000 se blíží [konci podpory](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) v prosinci 2022. Zákazníci, kteří používají sadu StorSimple 8000, mají možnost upgradovat na jiné hybridní služby Azure First stran. Tento článek popisuje možnosti hybridního Azure, které jsou k dispozici pro migraci dat.

## <a name="migration-options"></a>Možnosti migrace

Zákazníci, kteří používají řadu StorSimple 8000, mají možnosti Azure nebo třetích stran.

### <a name="azure-options"></a>Možnosti Azure

#### <a name="migrate-to-azure-file-sync"></a>Migrace do Synchronizace souborů Azure

Tato značka nové možnosti migrace umožňuje zákazníkům ukládat sdílené složky ve své organizaci do souborů Azure. Tyto sdílené soubory jsou potom centralizované pro místní přístup pomocí Azure File Sync (AFS). AFS se dá nasadit na hostitele Windows serveru. Skutečná migrace dat se pak provede jako kopie hostitele nebo pomocí nástroje pro migraci.

Další informace o tom, jak migrovat data na Azure File Sync, najdete v článku [migrace StorSimple 8100 a 8600 do Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000).

### <a name="third-party-options"></a>Možnosti třetích stran

#### <a name="migrate-to-panzura-freedom-nas"></a>Migrace na server Panzura svobody

Zákazníci řady StorSimple 5000-7000 a StorSimple 8000 Series se můžou rozhodnout migrovat na server Panzura svobody, aby si zachovali jejich data v Azure. Řešení Panzura svobodu poskytuje řešení pro NAS, které pokrývá datová centra, pobočky, veřejné a privátní cloudy. Řešení umožňuje místní, hybridní a cloudové pracovní postupy dat pro NFS, SMB a mobilní klienty.

Tato migrace je podporovaná Panzura a zákazníci můžou začít tím, že požádají o podporu migrace z [webu Panzura](https://panzura.com/migrate-storsimple-panzura/).

#### <a name="migrate-to-nasuni"></a>Migrace na Nasuni

Přesun celého prostředí StorSimple na stabilní, bezpečnou a vysoce výkonnou platformu souborových služeb je snadno Nasuni. Nasuni nabízí zabezpečení a výkon místních úložišť souborů při kombinaci s škálovatelností a odolností Azure.  Nasuni má jako špičkového nezávislého výrobce softwaru (ISV) k dispozici všechny nástroje, které jsou nezbytné k přesunu dat StorSimple na moderní platformu, která umožňuje sdílet soubory a spolupracovat s nimi v různých umístěních.

Začínáme dnes: [Web Nasuni](https://info.nasuni.com/storsimple8000-webinar).

<!-- 04/09/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
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

## <a name="migration---frequently-asked-questions"></a>Migrace – Nejčastější dotazy

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>Otázka: Když zařízení řady StorSimple 8000 dosáhnou konce služby?

A. StorSimple 8000 řady dorazí na [konec podpory](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) v prosinci 2022. Konec podpory znamená, že společnost Microsoft už nebude moct po uplynutí prosince 2022 poskytovat podporu pro hardware i software těchto zařízení. Důrazně doporučujeme začít formulovat plán pro migraci dat ze svých zařízení.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>Otázka: Co se stane s daty uloženými v Azure?  

A. Data v Azure můžete po migraci do novější služby nadále používat.

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>Otázka: Co se stane s daty uloženými místně na zařízení StorSimple?

A. Data umístěná na místním zařízení je možné zkopírovat do novější služby, jak je popsáno v dokumentu migrace.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>Otázka: Co se stane, když si přeji zachovat zařízení řady StorSimple 8000?

A. I když mohou služby fungovat i nadále, společnost Microsoft již nebude moci poskytovat hardwarovou a softwarovou podporu. Migrace se důrazně doporučuje pro provozní kontinuitu.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>Otázka: Jaké možnosti jsou k dispozici pro migraci dat ze zařízení řady StorSimple 8000?

A. V závislosti na jejich scénáři mají uživatelé řady StorSimple 8000 následující možnosti migrace:

* **Migrace na Azure File Sync**: tuto možnost použijte, pokud chcete přepnout do nativního formátu Azure. Azure File Sync můžete použít pro centralizovanou správu sdílených složek.

* **Další možnosti**: můžete kontaktovat podpora Microsoftu a diskutovat na možnosti migrace, které tady nejsou uvedené.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>Otázka: Je migrace na jiná řešení úložiště podporovaná?

A. Ano. Migrace na jiná řešení úložiště pomocí hostitelské kopie dat je podporovaná.

### <a name="q-is-migration-supported-by-microsoft"></a>Otázka: Je migrace podporovaná Microsoftem?

A. Migrace z 8000 Series je plně podporovaná operace. Společnost Microsoft ve skutečnosti doporučuje, abyste si před zahájením migrace vydosáhli podpory. Migrace je aktuálně pomocná operace. Pokud máte v úmyslu migrovat data ze zařízení řady StorSimple 8000, [obraťte se na podporu StorSimple](mailto:storsimp@microsoft.com).

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>Otázka: Jaký je cenový model pro migraci do Azure File Sync?

A. Při použití Azure File Sync může platit poplatky za předplatné služby. Zákazníci budou muset platit i průběžné náklady na úložiště. Odhad najdete v tématu [ceny pro AFS]( https://azure.microsoft.com/pricing/details/storage/files/) .

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>Otázka: Jak dlouho trvá dokončení migrace?

A. Čas k migraci dat závisí na množství dat a vybrané možnosti upgradu.

## <a name="next-steps"></a>Další kroky

* [Migrace dat z řady StorSimple 8000 na Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)
