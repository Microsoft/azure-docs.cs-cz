---
title: Nejčastější dotazy – zálohování databází SAP HANA na virtuálních počítačích Azure
description: V tomto článku najdete odpovědi na běžné dotazy týkající se zálohování SAP HANA databází pomocí služby Azure Backup.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a1d6012ec064b5ec582896ac3484161a6e25f2bf
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659960"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Nejčastější dotazy – zálohování SAP HANA databází na virtuálních počítačích Azure

Tento článek obsahuje odpovědi na běžné dotazy týkající se zálohování SAP HANA databází pomocí služby Azure Backup.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Kolik úplných záloh se podporuje za den?

Podporujeme jenom jednu úplnou zálohu za den. Nemůžete mít ke stejnému dni spuštěnou rozdílovou zálohu a úplné zálohování.

### <a name="do-successful-backup-jobs-create-alerts"></a>Vytvářejí úspěšné úlohy zálohování upozornění?

Ne. Úspěšné úlohy zálohování negenerují výstrahy. Výstrahy se odesílají jenom pro úlohy zálohování, které selžou. Podrobné chování výstrah na portálu [najdete tady](./backup-azure-monitoring-built-in-monitor.md). Pokud ale máte zájem o výstrahy i pro úspěšné úlohy, můžete použít [Azure monitor](./backup-azure-monitoring-use-azuremonitor.md).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Můžu v nabídce úlohy zálohování Zobrazit naplánované úlohy zálohování?

V nabídce Úloha zálohování se zobrazí pouze úlohy zálohování ad-hoc. V případě naplánovaných úloh použijte [Azure monitor](./backup-azure-monitoring-use-azuremonitor.md).

### <a name="are-future-databases-automatically-added-for-backup"></a>Budou se automaticky zálohovat i budoucí databáze?

Ne, aktuálně se nepodporuje.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Když odstraním databázi z instance, co se stane s zálohami?

Pokud je databáze vyřazena z instance SAP HANA, zálohování databáze se stále pokouší. To znamená, že Odstraněná databáze začne v rámci **zálohované položky** zobrazovat stav není v pořádku a je pořád chráněná.
Správný způsob, jak zastavit ochranu této databáze, je provést **zastavení zálohování s odstraňováním dat** v této databázi.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Když změním název databáze po jejím ochraně, jaká chování budou?

Přejmenovaná databáze je považována za novou databázi. Proto se služba bude považovat za tuto situaci, jako kdyby nebyla nalezena databáze a selhala zálohování. Přejmenovaná databáze se zobrazí jako nová databáze a je nutné ji nakonfigurovat pro ochranu.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Jaké jsou požadavky pro zálohování SAP HANA databází na virtuálním počítači Azure?

Přečtěte si o [požadavcích](tutorial-backup-sap-hana-db.md#prerequisites) a o [tom, co skript předregistrací zahrnuje](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) oddíly.

### <a name="what-permissions-should-be-set-so-azure-can-back-up-sap-hana-databases"></a>Jaká oprávnění by se měla nastavit, aby Azure mohl zálohovat SAP HANA databází?

Spuštění předregistračního skriptu nastaví požadovaná oprávnění, která umožní službě Azure zálohovat SAP HANA databáze. Další informace o tom, co skript před registrací dělá, najdete [tady](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="will-backups-work-after-migrating-sap-hana-from-sdc-to-mdc"></a>Budou zálohy fungovat po migraci SAP HANA z SDC do MDC?

Informace najdete v [této části](./backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid) Průvodce odstraňováním potíží.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>Je možné vytvořit zálohu v Azure HANA proti virtuální IP adrese (Nástroj pro vyrovnávání zatížení) a ne virtuálnímu počítači?

V současné době není k dispozici možnost k nastavení řešení pouze pro virtuální IP adresu. K provedení řešení potřebujeme virtuální počítač.

### <a name="how-can-i-move-an-on-demand-backup-to-the-local-file-system-instead-of-the-azure-vault"></a>Jak můžu přesunout zálohu na vyžádání do místního systému souborů místo do trezoru Azure?

1. Počkejte, než se aktuálně spuštěné zálohování dokončí v požadované databázi (pro dokončení proveďte kontrolu od studia).
1. Zakažte zálohy protokolů a nastavte zálohu katalogu na **systém souborů** pro požadovanou databázi pomocí následujících kroků:
1. Dvakrát klikněte na **SYSTEMDB**  ->  **Konfigurace**  ->  **vybrat databázový**  ->  **filtr (protokol)** .
    1. Nastavit enable_auto_log_backup na **ne**
    1. Nastavit catalog_backup_using_backint na **hodnotu false**
1. V požadované databázi proveďte zálohování na vyžádání (úplné/rozdílové/přírůstkové) a počkejte, než se dokončí zálohování a zálohování katalogu.
1. Pokud chcete také přesunout zálohy protokolu do systému souborů, nastavte enable_auto_log_backup na **Ano** .
1. Vraťte se k předchozímu nastavení, aby bylo možné zálohy do trezoru Azure přesměrovat:
    1. Nastavit enable_auto_log_backup na **Ano**
    1. Nastavit catalog_backup_using_backint na **hodnotu true**

>[!NOTE]
>Přesunutí záloh do místního systému souborů a opětovné přepnutí zpátky do trezoru Azure může způsobit přerušení řetězů protokolů v trezoru. Tím se aktivuje úplná záloha, která po úspěšném dokončení spustí zálohování protokolů.

### <a name="how-can-i-use-sap-hana-backup-with-my-hana-replication-set-up"></a>Jak můžu použít zálohování SAP HANA s nastavením replikace v HANA?

V současné době Azure Backup nemá schopnost pochopit HSRou sadu. To znamená, že primární a sekundární uzly HSR budou považovány za dva jednotlivé nesouvisející virtuální počítače. Nejdřív budete muset nakonfigurovat zálohování na primárním uzlu. Pokud dojde k převzetí služeb při selhání, musí být záloha nakonfigurovaná na sekundárním uzlu (který se teď stane primárním uzlem). Neexistuje žádná Automatická převzetí služeb při selhání v rámci zálohování do druhého uzlu.

Chcete-li zálohovat data z aktivního (primárního) uzlu v libovolném daném časovém okamžiku, můžete **Přepnout ochranu**  na sekundární uzel, který se nyní stane primárním po převzetí služeb při selhání.

Chcete-li provést **ochranu tohoto přepínače**, postupujte podle těchto kroků:

- [Zastavení ochrany](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) (při zachování dat) na primárním
- Spuštění [předregistračního skriptu](https://aka.ms/scriptforpermsonhana) na sekundárním uzlu
- [Zjistit databáze](tutorial-backup-sap-hana-db.md#discover-the-databases) na sekundárním uzlu a nakonfigurovat na nich [zálohy](tutorial-backup-sap-hana-db.md#configure-backup)

Tyto kroky je třeba provést ručně po každém převzetí služeb při selhání. Tyto kroky můžete provést prostřednictvím příkazového řádku nebo protokolu HTTP REST kromě Azure Portal. K automatizaci těchto kroků můžete použít Runbook Azure.

Tady je podrobný příklad toho, jak se musí provést **Ochrana přepínače** :

V tomto příkladu máte v HSR nastavení dva uzly-Node 1 (primární) a Node 2 (sekundární).  V uzlu 1 jsou nakonfigurovány zálohy. Jak je uvedeno výše, nepokoušejte se ještě nepostupovat v konfiguraci záloh v uzlu 2.

Když dojde k prvnímu převzetí služeb při selhání, uzel 2 se stane primárním. Stisknutím

1. Zastavte ochranu uzlu 1 (předchozí primární) pomocí možnosti zachovat data.
1. Spusťte skript před registrací na uzlu 2 (který je teď primární).
1. Objevte databáze v uzlu 2, přiřaďte zásady zálohování a nakonfigurujte zálohy.

Pak se v uzlu 2 spustí první úplné zálohování a po dokončení se spustí zálohování protokolu.

Když dojde k dalšímu převzetí služeb při selhání, uzel 1 se znovu stane primárním uzlem a uzel 2 se stane sekundárním. Nyní tento proces opakujte:

1. Zastavte ochranu uzlu 2 s možností zachovat data.
1. Spusťte skript před registrací v uzlu 1 (který se stane primárním uzlem).
1. Pak [obnovte zálohu](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) v uzlu 1 pomocí požadovaných zásad (protože zálohy byly zastaveny dříve v uzlu 1).

Pak se znovu spustí úplná záloha v uzlu 1 a po dokončení se spustí zálohování protokolu.

## <a name="restore"></a>Obnovení

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Proč se mi nedá zobrazit systém HANA, na který chci obnovit moji databázi?

Ověřte, zda jsou splněny všechny požadavky pro cíl obnovení na instanci SAP HANA. Další informace najdete v tématu [požadavky – obnovení SAP HANA databází ve virtuálním počítači Azure](./sap-hana-db-restore.md#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Proč se pro moji databázi nezdaří obnovení přepsání databáze?

Zajistěte, aby při obnovení byla vybrána možnost **Vynutit přepsání** .

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Proč se zobrazuje chyba "zdrojový a cílový systém pro obnovení jsou nekompatibilní"?

Informace o tom, jaké typy obnovení se aktuálně podporují, najdete v SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) .

### <a name="can-i-use-a-backup-of-a-database-running-on-sles-to-restore-to-an-rhel-hana-system-or-vice-versa"></a>Můžu k obnovení do systému RHEL HANA použít zálohu databáze běžící na SLES nebo naopak?

Ano, zálohy streamování aktivované v databázi HANA běžící na SLES můžete použít k obnovení do systému RHEL HANA a naopak. To znamená, že při zálohování přes streamování je možné provést obnovení mezi různými operačními systémy. Budete ale muset zajistit, aby systém HANA, do kterého chcete obnovit, a systém HANA, který se používá k obnovení, byly kompatibilní pro obnovení podle SAP. Chcete-li zjistit, které typy obnovení jsou kompatibilní, přečtěte si SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) .

## <a name="policy"></a>Zásada

### <a name="different-options-available-during-creation-of-a-new-policy-for-sap-hana-backup"></a>Během vytváření nové zásady pro SAP HANA Backup jsou dostupné různé možnosti.

Předtím, než vytvoříte zásadu, by jedna měla být jasná na požadavcích bodu RPO a RTO a na relevantních nákladových důsledcích.

RPO (bod obnovení-cíl) indikuje, kolik ztrát dat je v pořádku pro uživatele nebo zákazníka. To závisí na četnosti zálohování protokolu. Častější zálohování protokolů indikuje nižší RPO a minimální hodnota podporovaná službou Azure Backup je 15 minut, tj. četnost zálohování protokolů může být 15 minut nebo vyšší.

RTO (doba obnovení-cíl) indikuje, jak rychle mají být data obnovena do posledního dostupného bodu v čase po skončení ztráty dat. To závisí na strategii obnovení používané v HANA, která je obvykle závislá na tom, kolik souborů je potřeba k obnovení. To má vliv na náklady a následující tabulka by měla pomáhat při porozumění všem scénářům a jejich dopadům.

|Zásady zálohování  |RTO  |Cost  |
|---------|---------|---------|
|Denní plný + protokol     |   Nejrychlejší vzhledem k tomu, že potřebujeme pro obnovení k bodu v čase pouze jednu úplnou kopii a požadované protokoly.      |    Možnost Costliest, protože úplné kopírování se provádí denně, takže se v back-endu nashromáždí více a další data, dokud doba uchování nedosáhne   |
|Týdenní úplná + denní rozdílová + protokoly     |   Pomalejší než výše uvedená možnost, ale je rychlejší než níže, protože pro obnovení k časovému okamžiku vyžadujeme jednu úplnou kopii a jednu rozdílnou kopii + protokoly.      |    Levnější možnost, protože denní rozdíl je obvykle menší než úplný a úplné kopírování je prováděno pouze jednou za týden.      |
|Týdenní úplná + denní přírůstková + protokoly     |  Nejpomalejší, protože pro obnovení k bodu v čase potřebujeme jedno úplné kopírování + ' n '.       |     Nejméně náročná možnost od denního přírůstku bude menší než rozdílová a celá kopie bude pocházet jenom týdně.    |

> [!NOTE]
> Výše uvedené možnosti jsou nejběžnější, ale ne jediné možnosti. Například jedna může mít týdenní úplnou zálohu + rozdíly dvakrát za týden + protokoly.

Proto jeden může vybrat variantu zásady na základě cílů RPO a RTO a nákladů.

### <a name="impact-of-modifying-a-policy"></a>Dopad změny zásady

Při určování dopadu přepínání zásad zálohovaných položek ze zásad 1 (P1) na zásadu 2 (P2) nebo pro úpravu zásady 1 (P1) byste měli mít na paměti několik zásad.

- Všechny změny se také aplikují zpětně. Nejnovější zásady zálohování se uplatní i pro body obnovení, které byly dříve odebrány. Předpokládejme například, že denní úplné uchovávání je 30 dní a 10 bodů obnovení bylo pořízeno v závislosti na aktuálně aktivní zásadě. Pokud se denní doba uchovávání změní na 10 dní, pak se čas vypršení platnosti předchozího bodu také přepočítá jako čas zahájení + 10 dnů a v případě vypršení platnosti se odstraní.
- Rozsah změny zahrnuje také den zálohování, typ zálohy spolu s uchováním. Příklad: Pokud se zásada změní z denního intervalu na každý týden plný v neděli, všechna předchozí plná, která nejsou v neděli, se označí k odstranění.
- Nadřazený objekt není odstraněn, dokud není aktivní nebo nevypršela platnost podřízeného objektu. U každého typu zálohování je čas vypršení platnosti podle aktuálně aktivních zásad. Ale typ úplné zálohy se považuje za nadřazený pro následné "diferenciály", "přírůstky" a "logs". ' Differential ' a ' log ' nejsou nadřazeny jiným uživatelům. "Přírůstková" může být nadřazeným objektem následného "přírůstkového". I když je nadřazený objekt označený k odstranění, ve skutečnosti se neodstraní, pokud nevypršela platnost podřízených rozdílových objektů nebo protokolů. Pokud se například zásada změní z denního rozsahu na každý týden plný v neděli, všechny předchozí plné verze, které nejsou v neděli, budou označeny k odstranění. Ale ve skutečnosti se neodstraní, dokud platnost protokolů, které uplynuly dříve, neprošly. Jinými slovy se uchovávají v závislosti na nejnovější době trvání protokolu. Jakmile vyprší platnost protokolů, odeberou se oba protokoly i všechny.

V těchto principech může jeden z následujících tabulek přečíst informace o důsledcích změny zásad.

|Stará zásada/nové zásady  |Denní úplný objem a protokoly  | Týdenní úplné a denní rozdíly + protokoly  |Týdenní úplné a denní přírůstky + protokoly  |
|---------|---------|---------|---------|
|Denní úplný objem a protokoly     |   -      |    Předchozí plná data, která nejsou ve stejném dnu v týdnu, jsou označena k odstranění, ale budou uchována až po dobu uchování protokolu.     |    Předchozí plná data, která nejsou ve stejném dnu v týdnu, jsou označena k odstranění, ale budou uchována až po dobu uchování protokolu.     |
|Týdenní úplné a denní rozdíly + protokoly     |   Předchozí týdenní úplné uchovávání se přepočítá podle nejnovějších zásad. Předchozí rozdíly jsou okamžitě odstraněny.      |    -     |    Předchozí rozdíly jsou okamžitě odstraněny.     |
|Týdenní úplné a denní přírůstky + protokoly     |     Předchozí týdenní úplné uchovávání se přepočítá podle nejnovějších zásad. Předchozí přírůstky jsou okamžitě odstraněny    |     Předchozí přírůstky jsou okamžitě odstraněny    |    -     |

## <a name="next-steps"></a>Další kroky

Naučte se, jak [zálohovat SAP HANA databáze](./backup-azure-sap-hana-database.md) běžící na virtuálních počítačích Azure.
