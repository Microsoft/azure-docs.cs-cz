---
title: Informace o SAP HANA zálohování databáze na virtuálních počítačích Azure
description: V tomto článku se dozvíte o zálohování SAP HANA databází, které běží na virtuálních počítačích Azure.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 52c235c95cea73a0c51c62fcb55f7f711d2eff21
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "79476453"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Informace o SAP HANA zálohování databáze na virtuálních počítačích Azure

SAP HANA databází jsou důležité úlohy, které vyžadují nízký cíl bodu obnovení (RPO) a čas rychlého obnovení (RTO). Teď můžete [zálohovat SAP HANA databáze běžící na virtuálních počítačích Azure](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db) pomocí [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview).

Azure Backup je pro SAP [Backint certifikováno](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) , aby se zajistila nativní podpora zálohování díky využití nativních rozhraní API SAP HANA. Tato nabídka z Azure Backup se zarovnává s heslomi zálohováními s **nulovou infrastrukturou** Azure Backup a eliminuje nutnost nasazení a správy infrastruktury zálohování. Nyní můžete bezproblémově zálohovat a obnovovat SAP HANA databáze běžící na virtuálních počítačích Azure ([virtuální počítače řady M Series](../virtual-machines/m-series.md) se také teď podporují!) a využívat možnosti podnikové správy, které Azure Backup poskytuje.

## <a name="added-value"></a>Přidaná hodnota

Použití Azure Backup k zálohování a obnovení databází SAP HANA přináší následující výhody:

* **15 minut cíl bodu obnovení (RPO)**: je možné obnovit kritická data z až 15 minut.
* **Jedno kliknutí, obnovení k bodu v čase**: obnovení produkčních dat na alternativní servery Hana je snadné. Řetězení záloh a katalogů k provedení obnovení je spravováno službou Azure na pozadí.
* **Dlouhodobé uchovávání informací**: pro přísné dodržování předpisů a požadavky na audit. Zálohujte zálohy na roky na základě doby uchování, po jejímž uplynutí se body obnovení automaticky vyřadí pomocí integrované funkce správy životního cyklu.
* **Správa zálohování z Azure**: Využijte možnosti správy a monitorování Azure Backup pro lepší prostředí pro správu. Podporuje se taky Azure CLI.

Pokud chcete zobrazit scénáře zálohování a obnovení, které podporujeme dnes, přečtěte si [matrici podpora scénářů SAP HANA](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support).

## <a name="backup-architecture"></a>Architektura zálohování

![Diagram architektury zálohování](./media/sap-hana-db-about/backup-architecture.png)

* Proces zálohování začíná [vytvořením trezoru služby Recovery Services](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#create-a-recovery-service-vault) v Azure. Tento trezor se použije k uložení záloh a bodů obnovení vytvořených v průběhu času.
* Virtuální počítač Azure se spuštěným SAP HANA serverem je zaregistrován v trezoru a jsou [zjištěny](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#discover-the-databases)databáze, které mají být zálohovány. Aby služba Azure Backup mohla zjišťovat databáze, musí se na serveru HANA spustit [skript pro registraci](https://aka.ms/scriptforpermsonhana) jako uživatel root user.
* Tento skript vytvoří uživatele **AZUREWLBACKUPHANAUSER** DB a odpovídající klíč se stejným názvem v **hdbuserstore**. Další informace o tom, co skript dělá, najdete v části [co je to skript](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) pro předběžnou registraci.
* Služba Azure Backup nyní na zaregistrovaném serveru SAP HANA nainstaluje **modul plug-in Azure Backup pro Hana** .
* Uživatel **AZUREWLBACKUPHANAUSER** DB vytvořený pomocí předregistračního skriptu používá **modul plug-in Azure Backup pro Hana** k provádění všech operací zálohování a obnovení. Pokud se pokusíte nakonfigurovat zálohování pro SAP HANA databáze bez spuštění tohoto skriptu, může se zobrazit následující chyba: **UserErrorHanaScriptNotRun**.
* Pokud chcete [nakonfigurovat zálohování](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#configure-backup) u databází, které se zjistily, vyberte požadované zásady zálohování a povolte zálohování.

* Po nakonfigurovaném zálohování nastaví služba Azure Backup na úrovni databáze na chráněném serveru SAP HANA následující parametry Backint:
  * [catalog_backup_using_backint: true]
  * [enable_accumulated_catalog_backup: false]
  * [parallel_data_backup_backint_channels: 1]
  * [log_backup_timeout_s: 900)]
  * [backint_response_timeout: 7200]

>[!NOTE]
>Zajistěte, aby tyto parametry *nebyly* na úrovni hostitele k dispozici. Parametry na úrovni hostitele přepíšou tyto parametry a můžou způsobit neočekávané chování.
>

* **Modul plug-in Azure Backup pro Hana** udržuje všechny plány zálohování a podrobnosti o zásadách. Spouští naplánované zálohy a komunikuje s **modulem zálohování Hana** prostřednictvím rozhraní API Backint.
* **Modul zálohování Hana** vrátí datový proud Backint s daty, která se mají zálohovat.
* Všechna naplánovaná zálohování a zálohy na vyžádání (aktivované z Azure Portal), které jsou vyvolány buď jako úplné, nebo rozdílové, jsou iniciovány **modulem plug-in Azure Backup pro Hana**. Zálohy protokolu se ale spravují a spouštějí v samotném **zálohovacím stroji Hana** .
* Azure Backup pro SAP HANA se jedná o řešení, které je certifikované pro BackInt, nezávisí na podkladových typech disků nebo virtuálních počítačů. Zálohování se provádí pomocí datových proudů generovaných HANA.

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>Použití zálohování virtuálních počítačů Azure se službou Azure SAP HANA Backup

Kromě používání služby SAP HANA Backup v Azure, která poskytuje zálohování a obnovení na úrovni databáze, můžete použít řešení zálohování virtuálních počítačů Azure k zálohování operačního systému a jiných než databázových disků.

[Řešení zálohování Backint Certified pro Azure SAP HANA](#backup-architecture) se dá použít k zálohování a obnovení databáze.

[Zálohování virtuálních počítačů Azure](backup-azure-vms-introduction.md) se dá použít k zálohování operačního systému a dalších jiných nedatabázových disků. Zálohování virtuálního počítače se pokaždé denně vybere a zálohuje všechny disky (kromě **akcelerátor zápisu (WA) disků** a **UltraDisks**). Vzhledem k tomu, že se databáze zálohuje pomocí řešení zálohování Azure SAP HANA, můžete použít zálohování s konzistentními soubory jenom s operačním systémem a nedatabázovými disky pomocí funkce vyloučit disk, která je aktuálně ve verzi Preview.

>[!NOTE]
> Použití předzálohovacích skriptů se zálohováním virtuálního počítače Azure umožní zálohování datových svazků v databázi konzistentní vzhledem k aplikacím. Pokud se ale oblast protokolu nachází na discích s WA, nemusí mít snímek těchto disků zaručenou konzistenci oblasti protokolu. HANA má explicitní způsob generování záloh protokolů z tohoto přesného důvodu. Povolte ve svém SAP HANA stejné a můžou se zálohovat pomocí Azure SAP HANA Backup.

Pokud chcete obnovit virtuální počítač se spuštěným SAP HANA, postupujte podle těchto kroků:

* [Obnovte nový virtuální počítač ze zálohy virtuálního počítače Azure](backup-azure-arm-restore-vms.md) z posledního bodu obnovení. Nebo vytvořte nový prázdný virtuální počítač a připojte disky z posledního bodu obnovení.
* Vzhledem k tomu, že disky WA nejsou zálohovány, nejsou obnoveny. Vytvořte prázdné disky a oblast protokolu WA.
* Po nastavení všech ostatních konfigurací (například IP adresa, název systému atd.) je virtuální počítač nastavený na přijímání DATABÁZOVÝch dat ze služby Azure Backup.
* Teď obnovte databázi do virtuálního počítače ze [zálohy služby Azure SAP HANA DB](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point) na požadovaný časový okamžik.

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [obnovit databázi SAP HANA běžící na virtuálním počítači Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore) .
* Naučte se [spravovat SAP HANA databází zálohovaných pomocí Azure Backup](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
