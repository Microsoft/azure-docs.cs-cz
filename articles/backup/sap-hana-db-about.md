---
title: Zálohování databáze SAP HANA ve virtuálních počítačích Azure
description: V tomto článku se dozvíte o zálohování databází SAP HANA, které běží na virtuálních počítačích Azure.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 52c235c95cea73a0c51c62fcb55f7f711d2eff21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476453"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Zálohování databáze SAP HANA ve virtuálních počítačích Azure

Databáze SAP HANA jsou kritické úlohy, které vyžadují cíl nízkého bodu obnovení (RPO) a cíl rychlé doby obnovení (RTO). Teď můžete [zálohovat databáze SAP HANA spuštěné na virtuálních počítačích Azure](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db) pomocí [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview).

Azure Backup je [backint certifikovaný](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) sap, poskytovat nativní podporu zálohování využitím SAP HANA nativní rozhraní API. Tato nabídka z Azure Backup je v souladu s mantrou Azure Backup o zálohách **s nulovou infrastrukturou,** což eliminuje potřebu nasazovat a spravovat infrastrukturu zálohování. Teď můžete bez problémů zálohovat a obnovovat databáze SAP HANA spuštěné na virtuálních počítačích Azure[(virtuální počítače řady M,](../virtual-machines/m-series.md) které jsou teď taky podporované!) a využít možnosti správy rozlehlé sítě, které služba Azure Backup poskytuje.

## <a name="added-value"></a>Přidaná hodnota

Použití Azure Backup k zálohování a obnovení databází SAP HANA poskytuje následující výhody:

* **15minutový cíl bodu obnovení (RPO):** Obnovení kritických dat až 15 minut je nyní možné.
* **Obnovení jedním kliknutím a bodem v čase**: Snadné obnovení produkčních dat na alternativní servery HANA. Řetězení záloh a katalogů k provádění obnovení spravuje Azure na pozadí.
* **Dlouhodobé uchovávání**: Pro důsledné dodržování a potřeby auditu. Uchovávejte zálohy po celá léta na základě doby uchovávání informací, po jejímž uplynutí budou body obnovení automaticky prořezány integrovanou funkcí správy životního cyklu.
* **Správa zálohování z Azure:** Využijte možnosti správy a monitorování Azure Backup pro lepší možnosti správy. Azure CLI je také podporována.

Chcete-li zobrazit scénáře zálohování a obnovení, které dnes podporujeme, podívejte se na [matici podpory scénáře SAP HANA](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support).

## <a name="backup-architecture"></a>Architektura zálohování

![Diagram záložní architektury](./media/sap-hana-db-about/backup-architecture.png)

* Proces zálohování začíná [vytvořením trezoru služeb obnovení](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#create-a-recovery-service-vault) v Azure. Tento trezor bude použit k ukládání záloh a bodů obnovení vytvořených v průběhu času.
* Virtuální počítač Azure se systémem SAP HANA server je registrován v trezoru a databáze, které mají být zálohovány jsou [zjištěny](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#discover-the-databases). Chcete-li povolit službě Azure Backup ke zjišťování databází, musí být skript [předběžné registrace](https://aka.ms/scriptforpermsonhana) spuštěn na serveru HANA jako kořenový uživatel.
* Tento skript vytvoří **UŽIVATELE AZUREWLBACKUPHANAUSER** DB a odpovídající klíč se stejným názvem v **hdbuserstore**. Naleznete v části [Co dělá skript předběžné registrace,](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) abyste pochopili více o tom, co skript dělá.
* Služba Azure Backup Service teď nainstaluje **modul plug-in zálohování Azure pro HANA** na registrovaný server SAP HANA.
* Uživatel **AZUREWLBACKUPHANAUSER** DB vytvořený skriptem pro předběžnou registraci používá **modul plug-in zálohování Azure pro HANA** k provedení všech operací zálohování a obnovení. Pokud se pokusíte nakonfigurovat zálohování pro SAP HANA DBs bez spuštění tohoto skriptu, může se zobrazit následující chyba: **UserErrorHanaScriptNotRun**.
* Chcete-li [nakonfigurovat zálohování](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#configure-backup) v databázích, které jsou zjištěny, zvolte požadované zásady zálohování a povolte zálohování.

* Po konfiguraci zálohy služba Azure Backup nastaví následující parametry Backint na úrovni DATABÁZE na chráněném serveru SAP HANA:
  * [catalog_backup_using_backint:true]
  * [enable_accumulated_catalog_backup:false]
  * [parallel_data_backup_backint_channels:1]
  * [log_backup_timeout_s:900)]
  * [backint_response_timeout:7200]

>[!NOTE]
>Ujistěte se, že tyto parametry *nejsou* k dispozici na úrovni HOST. Parametry na úrovni hostitele přepíší tyto parametry a mohou způsobit neočekávané chování.
>

* **Azure Backup Plugin pro HANA** udržuje všechny plány zálohování a podrobnosti o zásadách. Aktivuje naplánované zálohy a komunikuje s **HANA Backup Engine** prostřednictvím rozhraní API Backint.
* **Hana Backup Engine** vrátí Backint stream s daty, která mají být zálohována.
* Všechny naplánované zálohy a zálohy na vyžádání (aktivované z portálu Azure), které jsou úplné nebo rozdílové, jsou iniciovány **modulem Azure Backup Plugin pro HANA**. Zálohy protokolů jsou však spravovány a spuštěny samotným **hana backup engineem.**
* Azure Backup pro SAP HANA, což je řešení s certifikací BackInt, nezávisí na základní typy disků nebo virtuálních počítačů. Zálohování se provádí datovými proudy generovanými hana.

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>Použití zálohování virtuálních počítačů Azure se zálohou Azure SAP HANA

Kromě použití zálohy SAP HANA v Azure, která poskytuje zálohování a obnovení na úrovni databáze, můžete použít řešení zálohování virtuálních počítačů Azure k zálohování operačních a nedatabázových disků.

[Backint certifikované Azure SAP HANA řešení zálohování](#backup-architecture) lze použít pro zálohování a obnovení databáze.

[Zálohování virtuálních počítačů Azure](backup-azure-vms-introduction.md) se dá použít k zálohování operačního systému a dalších nedatabázových disků. Záloha virtuálního počítače se přebíjí jednou denně a zálohuje všechny disky (kromě **disků akcelerátoru zápisu (WA)** a **UltraDisků**). Vzhledem k tomu, že databáze je zálohována pomocí řešení zálohování Azure SAP HANA, můžete provést zálohu konzistentní se souborem pouze operačního systému a nedatabázových disků pomocí funkce vyloučit disk, která je aktuálně ve verzi preview.

>[!NOTE]
> Použití skriptů před příspěvkem se zálohami virtuálních počítačích Azure umožní zálohování datových svazků databáze konzistentní ch od aplikací. Pokud je však oblast protokolu umístěna na discích WA, pořízení snímku těchto disků nemusí zaručit konzistenci oblasti protokolu. HANA má explicitní způsob generování záloh protokolu z tohoto přesného důvodu. Povolte totéž ve vašem SAP HANA a mohou být zálohovány pomocí zálohy Azure SAP HANA.

Pokud chcete obnovit virtuální hod se systémem SAP HANA, postupujte takto:

* [Obnovte nový virtuální počítač ze zálohy virtuálních počítačů Azure](backup-azure-arm-restore-vms.md) z nejnovějšího bodu obnovení. Nebo vytvořte nový prázdný virtuální počítače a připojte disky z nejnovějšího bodu obnovení.
* Vzhledem k tomu, že disky WA nejsou zálohovány, nejsou obnoveny. Vytvořte prázdné WA disky a oblast protokolu.
* Po nastavení všech ostatních konfigurací (například IP, název systému a tak dále) je virtuální počítač nastaven na příjem dat DB ze zálohy Azure.
* Teď obnovte DB do virtuálního počítače ze [zálohy Azure SAP HANA DB](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point) do požadovaného bodu v čase.

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak [obnovit databázi SAP HANA spuštěnou na virtuálním počítači Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Zjistěte, jak [spravovat databáze SAP HANA, které jsou zálohované pomocí azure backupu](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
