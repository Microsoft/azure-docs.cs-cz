---
title: Automatické, geograficky redundantní zálohy
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database a Azure SQL Managed instance automaticky vytvoří zálohování místní databáze každých pár minut a pro geografickou redundanci použije geograficky redundantní úložiště s přístupem pro čtení z Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: references_regions
ms.topic: conceptual
author: shkale-msft
ms.author: shkale
ms.reviewer: mathoma, stevestein, danil
ms.date: 11/18/2020
ms.openlocfilehash: 862d33e523562511796999d82b67d2b4b11efaf3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690603"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>Automatizované zálohování – Azure SQL Database & spravované instance SQL

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-database-backup"></a>Co je zálohování databáze?

Zálohy databází jsou důležitou součástí jakékoli strategie pro provozní kontinuitu a zotavení po havárii, protože chrání vaše data před poškozením nebo odstraněním. Tyto zálohy umožňují obnovení databáze k určitému bodu v čase v rámci nakonfigurované doby uchování. Pokud vaše pravidla ochrany dat vyžadují, aby vaše zálohy byly dostupné po delší dobu (až 10 let), můžete nakonfigurovat [dlouhodobé uchovávání](long-term-retention-overview.md) pro databáze s jednou i ve fondu.

### <a name="backup-frequency"></a>Frekvence zálohování

SQL Database i spravované instance SQL používají SQL Server technologie k vytváření [úplných záloh](/sql/relational-databases/backup-restore/full-database-backups-sql-server) každý týden, [rozdílové zálohování](/sql/relational-databases/backup-restore/differential-backups-sql-server) každých 12-24 hodin a [zálohování protokolů transakcí](/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) každých 5 až 10 minut. Frekvence zálohování protokolu transakcí je založena na výpočetní velikosti a množství aktivity databáze.

Při obnovení databáze služba Určuje, které zálohy úplného, rozdílového a transakčního protokolu se musí obnovit.

### <a name="backup-storage-redundancy"></a>Redundance úložiště zálohování

Ve výchozím nastavení SQL Database a SQL Managed instance ukládají data v geograficky redundantních objektech [BLOB úložiště](../../storage/common/storage-redundancy.md) , které se replikují do [spárované oblasti](../../best-practices-availability-paired-regions.md). To pomáhá chránit před výpadky, které mají vliv na úložiště zálohování v primární oblasti a umožňují obnovit server do jiné oblasti v případě havárie. 

Možnost konfigurace redundance záložního úložiště poskytuje flexibilitu pro výběr mezi místně redundantními, redundantními a geograficky redundantními objekty blob úložiště pro spravovanou instanci SQL nebo SQL Database. Aby se zajistilo, že vaše data zůstanou ve stejné oblasti, ve které je nasazená vaše spravovaná instance nebo databáze SQL, můžete změnit výchozí geograficky redundantní záložní úložiště záloh a nakonfigurovat místně redundantní úložiště nebo objekty blob redundantní v zóně pro zálohování. Mechanismy redundance úložiště ukládají více kopií vašich dat, aby byly chráněné před plánovanými i neplánovanými událostmi, včetně přechodného selhání hardwaru, sítě nebo výpadků napájení nebo obrovského přirozeného katastrofy. Nakonfigurovaná redundance záložního úložiště se používá pro nastavení krátkodobého uchovávání záloh, která se používají pro obnovení v časovém intervalu (PITR) a dlouhodobé zálohy uchovávání dat, které se používají k dlouhodobému zálohování (LTR). 

Pro SQL Database lze redundanci úložiště zálohování nakonfigurovat v době vytváření databáze nebo aktualizovat pro existující databázi. změny provedené v existující databázi platí pouze pro budoucí zálohy. Po aktualizaci redundance záložního úložiště existující databáze může trvat až 48 hodin, než se změny použijí. Všimněte si, že je geografická obnova zakázaná, jakmile se databáze aktualizuje tak, aby používala místní nebo zóny redundantní úložiště. 


> [!IMPORTANT]
> Konfigurace redundance úložiště zálohování během procesu vytváření spravované instance, když je prostředek zřízený, už není možné změnit redundanci úložiště. 

> [!IMPORTANT]
> Redundantní úložiště v zóně je aktuálně dostupné jenom v [určitých oblastech](../../storage/common/storage-redundancy.md#zone-redundant-storage). 

> [!NOTE]
> Konfigurovatelná redundance úložiště zálohování pro Azure SQL Database je aktuálně dostupná ve veřejné verzi Preview v oblasti Brazílie – jih a obecně dostupná jenom v oblasti jihovýchodní Asie – Azure. Tato funkce ještě není k dispozici pro úroveň škálování. 

### <a name="backup-usage"></a>Využití zálohy

Tyto zálohy rovněž umožňují:

- **Obnovení existující databáze**  -  v daném časovém okamžiku [Obnovte stávající databázi k určitému bodu v čase v minulosti](recovery-using-backups.md#point-in-time-restore) v době uchování pomocí Azure Portal, Azure PowerShell, Azure CLI nebo REST API. V případě SQL Database Tato operace vytvoří novou databázi na stejném serveru jako původní databázi, ale používá jiný název, aby nedošlo k přepsání původní databáze. Po dokončení obnovení můžete původní databázi odstranit. Alternativně můžete [Přejmenovat](/sql/relational-databases/databases/rename-a-database) původní databázi a pak znovu přejmenovat obnovenou databázi na původní název databáze. Podobně pro spravovanou instanci SQL Tato operace vytvoří kopii databáze na stejné nebo jiné spravované instanci v rámci stejného předplatného a stejné oblasti.
- **Obnovení odstraněné databáze**  -  v okamžiku v čase [Obnovení odstraněné databáze do doby odstranění](recovery-using-backups.md#deleted-database-restore) nebo do libovolného bodu v čase v rámci doby uchování. Odstraněnou databázi lze obnovit pouze na stejném serveru nebo ve spravované instanci, kde byla vytvořena původní databáze. Při odstraňování databáze služba před odstraněním zabere v konečném zálohování protokolu transakcí, aby nedošlo ke ztrátě dat.
- **Geografické obnovení**  -  [Obnovte databázi do jiné geografické oblasti](recovery-using-backups.md#geo-restore). Geografické obnovení umožňuje obnovení z geografické havárie, když nemůžete získat přístup k databázi nebo zálohám v primární oblasti. Vytvoří novou databázi na jakémkoli existujícím serveru nebo spravované instanci v libovolné oblasti Azure.
   > [!IMPORTANT]
   > Geografické obnovení je dostupné jenom pro databáze SQL nebo spravované instance nakonfigurované s geograficky redundantním úložištěm záloh.
- **Obnovení z dlouhodobého zálohování**  -  [Obnovte databázi z určité dlouhodobé zálohy](long-term-retention-overview.md) izolované databáze nebo databáze ve fondu, pokud je databáze nakonfigurovaná s použitím dlouhodobých zásad uchovávání informací (LTR). LTR umožňuje obnovit starou verzi databáze pomocí [Azure Portal](long-term-backup-retention-configure.md#using-the-azure-portal) nebo [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) , aby splňovala požadavek na dodržování předpisů nebo spustil starou verzi aplikace. Další informace najdete v tématu [Dlouhodobé uchovávání](long-term-retention-overview.md).

Chcete-li provést obnovení, přečtěte si téma [obnovení databáze ze zálohy](recovery-using-backups.md).

> [!NOTE]
> V Azure Storage pojem *replikace* označuje kopírování objektů BLOB z jednoho umístění do druhého. V jazyce SQL *replikace databáze* odkazuje na různé technologie, které se používají k synchronizaci více sekundárních databází s primární databází.

Operaci konfigurace zálohování a obnovení můžete vyzkoušet v následujících příkladech:

| Operace | portál Azure | Azure PowerShell |
|---|---|---|
| **Změna uchovávání záloh** | [SQL Database](automated-backups-overview.md?tabs=single-database#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Spravovaná instance SQL](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [SQL Database](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Spravovaná instance SQL](/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| **Změna dlouhodobého uchovávání záloh** | [SQL Database](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Spravovaná instance SQL – N/A  | [SQL Database](long-term-backup-retention-configure.md)<br/>[Spravovaná instance SQL](../managed-instance/long-term-backup-retention-configure.md)  |
| **Obnovení databáze z určitého bodu v čase** | [SQL Database](recovery-using-backups.md#point-in-time-restore)<br>[Spravovaná instance SQL](../managed-instance/point-in-time-restore.md) | [SQL Database](/powershell/module/az.sql/restore-azsqldatabase) <br/> [Spravovaná instance SQL](/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| **Obnovení odstraněné databáze** | [SQL Database](recovery-using-backups.md)<br>[Spravovaná instance SQL](../managed-instance/point-in-time-restore.md#restore-a-deleted-database) | [SQL Database](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Spravovaná instance SQL](/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| **Obnovení databáze ze služby Azure Blob Storage** | SQL Database – N/A <br/>Spravovaná instance SQL – N/A  | SQL Database – N/A <br/>[Spravovaná instance SQL](../managed-instance/restore-sample-database-quickstart.md) |

## <a name="backup-scheduling"></a>Plánování zálohování

První úplná záloha je naplánována ihned po vytvoření nebo obnovení nové databáze. Tato záloha se obvykle dokončí do 30 minut, ale pokud je databáze velká, může trvat déle. Například počáteční záloha může trvat déle na obnovenou databázi nebo kopii databáze, která by obvykle byla větší než nová databáze. Po prvním úplném zálohování se naplánují a spravují všechny další zálohy automaticky. Přesné časování všech záloh databáze určuje služba SQL Database nebo služba SQL Managed instance, protože vyrovnává celkovou úlohu systému. Nemůžete změnit plán úloh zálohování ani je zakázat.

> [!IMPORTANT]
> V případě nové, obnovené nebo zkopírované databáze bude možnost obnovení k určitému bodu v čase dostupná z doby, kdy se vytvoří počáteční záloha protokolu transakcí, která následuje po počátečním úplném zálohování.

## <a name="backup-storage-consumption"></a>Spotřeba úložiště záloh

Díky SQL Server technologie zálohování a obnovení vyžaduje obnovení databáze k určitému bodu v čase nepřerušený řetězec zálohy sestávající z jedné úplné zálohy, volitelně jednu rozdílovou zálohu a jednu nebo více záloh protokolu transakcí. Plán zálohování pro SQL Database a SQL Managed instance zahrnuje jednu úplnou zálohu každý týden. Proto aby bylo možné povolit PITR v rámci celé doby uchovávání, systém musí ukládat další zálohy úplného, rozdílového a transakčního protokolu až do týden delšího, než je nastavená doba uchování. 

Jinými slovy, pro libovolný bod v čase během doby uchování, musí existovat úplná záloha, která je starší než nejstarší čas doby uchování, a také nepřerušený řetězec rozdílového a záložního zálohování protokolu z tohoto úplného zálohování do dalšího úplného zálohování.

> [!NOTE]
> Pokud chcete povolit PITR, ukládají se další zálohy až do týden delšího, než je nastavená doba uchování. Úložiště zálohování se účtuje stejnou sazbou za všechny zálohy. 

Zálohy, které už nejsou potřeba k poskytování funkcí PITR, se automaticky odstraní. Vzhledem k tomu, že rozdílové zálohy a zálohy protokolů vyžadují, aby se obnovitelné dřívější úplné zálohování, všechny tři typy zálohování se v týdenních sadách vyprázdní.

Pro všechny databáze, včetně [TDE šifrovaných](transparent-data-encryption-tde-overview.md) databází, jsou zálohy komprimovány, aby se snížila komprese a náklady úložiště zálohování. Průměrná kompresní kompresní poměr je 3-4 časů, ale může být výrazně nižší nebo vyšší v závislosti na povaze dat a na tom, jestli se v databázi používá komprese dat.

Služba SQL Database a SQL Managed instance COMPUTE celkově používala úložiště zálohování jako kumulativní hodnotu. Každou hodinu se tato hodnota oznamuje fakturačnímu kanálu Azure, který zodpovídá za agregaci tohoto hodinového využití za účelem výpočtu spotřeby na konci každého měsíce. Po odstranění databáze se spotřeba sníží, protože stáří záloh vyprší a odstraní se. Jakmile se všechny zálohy odstraní a PITR už není možné, fakturace se zastaví.
   
> [!IMPORTANT]
> Zálohy databáze jsou zachovány a umožňují PITR i v případě, že byla databáze odstraněna. Při odstraňování a opětovném vytváření databáze může dojít k úspory nákladů na úložiště a výpočetní prostředky, protože služba uchovává zálohy pro každou odstraněnou databázi, pokaždé, když se odstraní. 

### <a name="monitor-consumption"></a>Monitorovat spotřebu

Pro databáze vCore se úložiště spotřebované jednotlivými typy zálohování (úplné, rozdílové a protokolu) hlásí v okně monitorování databáze jako samostatná metrika. Následující diagram ukazuje, jak monitorovat spotřebu úložiště zálohování pro jednu databázi. Tato funkce není pro spravované instance aktuálně k dispozici.

![Monitorovat spotřebu zálohy databáze v Azure Portal](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Doladit spotřebu úložiště zálohování

Využití úložiště zálohování až do maximální velikosti dat pro databázi se neúčtují. Nadlimitní využití úložiště záloh bude záviset na zatížení a maximální velikosti jednotlivých databází. Vezměte v úvahu některé z následujících technik optimalizace, abyste snížili spotřebu úložiště záloh:

- Snižte [dobu uchovávání záloh](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) na minimum, co potřebujete.
- Vyhněte se provádění rozsáhlých operací zápisu, jako je například opětovné sestavení indexu, častěji než potřebujete.
- V případě operací s velkým objemem dat zvažte použití [clusterovaných indexů columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview) a následující související osvědčené [postupy](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance)a nebo snižte počet neclusterovaných indexů.
- Ve vrstvě služby Pro obecné účely zřízené úložiště dat je levnější než cena úložiště zálohování. Pokud máte průběžné vysoké náklady na úložiště záloh, můžete zvážit zvýšení úložiště dat pro uložení do úložiště zálohování.
- Použijte databázi TempDB místo trvalých tabulek v aplikační logice pro ukládání dočasných výsledků nebo přechodných dat.
- Pokud je to možné, používejte místně redundantní úložiště zálohování (například vývojové a testovací prostředí).

## <a name="backup-retention"></a>Uchování záloh

U všech nových, obnovených a kopírovaných databází jsou Azure SQL Database a Azure SQL Managed instance zachovány dostatečné zálohy, aby ve výchozím nastavení povolovaly PITR během posledních 7 dnů. S výjimkou databází s úrovněmi škálování a úrovně Basic můžete [změnit dobu uchovávání záloh](#change-the-pitr-backup-retention-period) na každou aktivní databázi v rozsahu 1-35 dní. Jak je popsáno v části [spotřeba úložiště zálohy](#backup-storage-consumption), zálohy uložené do povolit PITR můžou být starší než doba uchování. Jenom pro Azure SQL Managed instance je možné nastavit míru uchovávání záloh PITR, jakmile se databáze odstraní v rozsahu 0-35 dnů. 

Pokud databázi odstraníte, systém bude uchovávat zálohy stejným způsobem jako online databáze s určitou dobou uchování. Nemůžete změnit dobu uchování zálohy pro odstraněnou databázi.

> [!IMPORTANT]
> Odstraníte-li server nebo spravovanou instanci, jsou odstraněny také všechny databáze na tomto serveru nebo spravované instance a nelze je obnovit. Odstraněné servery nebo spravované instance se nedají obnovit. Pokud jste ale u databáze nebo spravované instance nakonfigurovali dlouhodobé uchovávání dat (LTR), zálohy dlouhodobého uchovávání se neodstraní a dají se použít k obnovení databází na jiném serveru nebo spravované instanci ve stejném předplatném, k určitému bodu v čase, kdy se vytvořila dlouhodobá záloha uchovávání.

Uchovávání záloh pro účely PITR během posledních 1-35 dnů se někdy označuje jako krátkodobé uchovávání záloh. Pokud potřebujete uchovat zálohy po dobu delší, než je maximální doba pro krátkodobé uchovávání 35 dní, můžete povolit [dlouhodobé uchovávání](long-term-retention-overview.md).

### <a name="long-term-retention"></a>Dlouhodobé uchovávání

V případě SQL Database i SQL spravované instance můžete v úložišti objektů BLOB v Azure nakonfigurovat dlouhodobé uchovávání po dobu až 10 let (LTR). Po nakonfigurování zásad LTR se všechny zálohy automaticky zkopírují do jiného kontejneru úložiště. Pro splnění různých požadavků na dodržování předpisů můžete pro týdenní, měsíční nebo roční úplné zálohování vybrat jiné doby uchování. Spotřeba úložiště závisí na zvolené četnosti a období uchovávání záloh LTR. Pomocí [cenové kalkulačky ltr](https://azure.microsoft.com/pricing/calculator/?service=sql-database) můžete odhadnout náklady na úložiště ltr.

> [!IMPORTANT]
> Aktualizace redundance záložního úložiště pro existující Azure SQL Database se týká pouze budoucích záloh provedených pro databázi. Všechny existující zálohy LTR pro databázi budou nadále umístěny v existujícím objektu BLOB úložiště a nové zálohy budou uloženy v požadovaném typu objektu BLOB úložiště. 

Další informace o LTR najdete v tématu [dlouhodobé uchovávání záloh](long-term-retention-overview.md).

## <a name="storage-costs"></a>Náklady na úložiště

Cena za úložiště zálohování se liší a závisí na vašem nákupním modelu (DTU nebo vCore), zvolené možnosti redundance úložiště zálohování a také ve vaší oblasti. Úložiště zálohování se účtuje za GB za měsíc spotřebovaný. ceny najdete na stránce s cenami [Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/) a na stránce s [cenami Azure SQL Managed instance](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/) .

### <a name="dtu-model"></a>Model DTU

V modelu DTU se za úložiště zálohování pro databáze a elastické fondy neúčtují žádné další poplatky. Cena za úložiště záloh je součástí ceny databáze nebo fondu.

### <a name="vcore-model"></a>Model virtuálních jader

U izolovaných databází v SQL Database se hodnota úložiště zálohy rovnající 100% maximální velikosti úložiště dat pro databázi poskytuje bez dalších poplatků. V případě elastických fondů a spravovaných instancí se hodnota úložiště zálohy rovná 100% maximálního úložiště dat pro fond nebo maximální velikost úložiště instance se poskytuje bez dalších poplatků. 

V případě izolovaných databází se tato rovnice používá k výpočtu celkového využití fakturovatelných úložiště záloh:

`Total billable backup storage size = (size of full backups + size of differential backups + size of log backups) – maximum data storage`

V případě databází ve fondu je celková fakturovatelná velikost úložiště záloh agregovaná na úrovni fondu a vypočte se takto:

`Total billable backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - maximum pool data storage`

V případě spravovaných instancí je celková fakturovatelná velikost úložiště záloh agregovaná na úrovni instance a vypočte se takto:

`Total billable backup storage size = (total size of full backups + total size of differential backups + total size of log backups) – maximum instance data storage`

Celkové Fakturovatelné úložiště zálohování, pokud je nějaké, se bude účtovat za GB za měsíc podle sazby využité redundance úložiště zálohy. Tato spotřeba úložiště záloh bude záviset na zatížení a velikosti jednotlivých databází, elastických fondů a spravovaných instancí. Vysoce upravované databáze mají větší rozdílové a zaprotokolované zálohy, protože velikost těchto záloh je úměrná množství změn dat. Proto budou mít tyto databáze vyšší poplatky za zálohování.

Služba SQL Database a SQL Managed instance počítá vaše celkové Fakturovatelné úložiště záloh jako kumulativní hodnotu ve všech zálohovaných souborech. Každou hodinu se tato hodnota oznamuje fakturačnímu kanálu Azure, který agreguje Toto hodinové použití, aby se na konci každého měsíce využívala spotřeba úložiště záloh. Pokud dojde k odstranění databáze, spotřeba úložiště zálohování se postupně sníží, protože staré stáří záloh vyprší a odstraní se. Vzhledem k tomu, že rozdílové zálohy a zálohy protokolů vyžadují, aby se obnovitelné dřívější úplné zálohování, všechny tři typy zálohování se v týdenních sadách vyprázdní. Po odstranění všech záloh se ukončí fakturace. 

Jako zjednodušený příklad předpokládáme, že databáze nashromáždila 744 GB úložiště zálohování a tato částka zůstane v celém měsíci konstantní, protože databáze je zcela nečinná. Pokud chcete tuto kumulativní spotřebu úložiště převést na hodinové použití, rozdělte ji o 744,0 (31 dnů za měsíc × 24 hodin denně). SQL Database bude hlásit fakturačnímu kanálu Azure, který databáze využila 1 GB PITR zálohování každou hodinu, a to za ustálenou sazbou. Fakturace Azure agreguje tuto spotřebu a za celý měsíc ukáže využití 744 GB. Náklady budou založené na sazbách za GB za GB a měsíc ve vaší oblasti.

Teď je to složitější příklad. Předpokládejme, že stejná databáze s nečinným se zvýšila z 7 dnů na 14 dní uprostřed měsíce. Výsledkem tohoto zvýšení je celková velikost úložiště zálohování od zdvojnásobení až 1 488 GB. SQL Database by nahlásil 1 GB využití v hodinách 1 až 372 (první polovina měsíce). Vykazovat využití jako 2 GB po dobu 373 až 744 (druhá polovina měsíce). Toto použití by bylo agregované do konečné faktury 1 116 GB/měsíc.

Skutečné scénáře fakturace ze zálohy jsou složitější. Vzhledem k tomu, že frekvence změn v databázi závisí na zatížení a je v průběhu času proměnná, velikost jednotlivých rozdílů a zálohování protokolů se budou lišit, což způsobí, že se odpovídajícím způsobem mění hodinová spotřeba úložiště zálohování. Kromě toho každá rozdílová záloha obsahuje všechny změny provedené v databázi od posledního úplného zálohování, takže celková velikost všech rozdílných záloh se postupně zvyšuje v průběhu týdne a pak se prudce rozrůstá, jakmile se uvolní starší sada úplných, rozdílových a záložních záloh protokolu. Například pokud je po dokončení úplného zálohování spuštěná operace silného zápisu, jako je třeba opětovné sestavení indexu, pak se změny provedené při opětovném sestavení indexu budou zahrnovat do záloh protokolu transakcí pořízených po dobu trvání opětovného sestavení, v další rozdílové záloze a v každé rozdílové záloze, dokud nedojde k dalšímu úplnému zálohování. Pro druhý scénář ve větších databázích vytvoří optimalizace ve službě úplnou zálohu místo rozdílového zálohování, pokud by rozdílové zálohování bylo příliš velké, jinak. Tím se zmenší velikost všech rozdílových záloh až do následujících úplných záloh.

V průběhu času můžete monitorovat celkovou spotřebu úložiště zálohování pro každý typ zálohy (úplný, rozdílový a transakční protokol), jak je popsáno v tématu [monitorování spotřeby](#monitor-consumption).

### <a name="backup-storage-redundancy"></a>Redundance úložiště zálohování

Redundance záložního úložiště ovlivňuje náklady na zálohování následujícím způsobem:
- místně redundantní cena = x
- zóna – redundantní cena = 1,25 ×
- geograficky redundantní cena = 2x

Další podrobnosti o cenách za úložiště zálohování najdete na stránce s cenami [Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/) a na [stránce s cenami Azure SQL Managed instance](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

> [!IMPORTANT]
> Konfigurovatelná redundance úložiště zálohování pro spravovanou instanci SQL je dostupná ve všech oblastech Azure a aktuálně dostupná v oblasti jihovýchodní Asie – Azure jenom pro SQL Database. U spravované instance se dá zadat jenom během procesu vytváření spravované instance. Po zřízení prostředku nemůžete změnit možnost redundance úložiště zálohování.

### <a name="monitor-costs"></a>Sledovat náklady

Pokud chcete pochopit náklady na úložiště zálohování, v Azure Portal klikněte na **cost management + fakturace** , vyberte **cost management** a pak vyberte **Analýza nákladů**. Vyberte požadované předplatné jako **obor** a potom vyfiltrujte časový interval a službu, které vás zajímají.

Přidejte filtr pro **název služby** a potom v rozevíracím seznamu vyberte **SQL Database** . Filtr **podkategorie měřiče** použijte k výběru čítače fakturace pro vaši službu. Pro izolovanou databázi nebo fond elastické databáze vyberte **Single/elastický fond PITR úložiště zálohování**. V případě spravované instance vyberte **mi PITR úložiště zálohování**. Podkategorie **úložišť** a **výpočtů** vám můžou zajímat i to, že nejsou spojené s náklady na úložiště zálohování.

![Analýza nákladů na úložiště zálohování](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

  >[!NOTE]
  > Měřiče jsou viditelné pouze pro čítače, které jsou právě používány. Pokud není čítač k dispozici, je pravděpodobná kategorie aktuálně nepoužitá. Například čítače spravované instance nebudou k dispozici pro zákazníky, kteří nemají nasazenou spravovanou instanci. Podobně se nebudou zobrazovat čítače úložiště pro prostředky, které nespotřebovávají úložiště. 

## <a name="encrypted-backups"></a>Šifrovaná zálohování

Pokud je databáze zašifrovaná pomocí TDE, zálohy se automaticky zašifrují v klidovém stavu, včetně záloh LTR. Všechny nové databáze v Azure SQL jsou ve výchozím nastavení nakonfigurované s povoleným TDE. Další informace o TDE najdete v tématu  [transparentní šifrování dat s SQL Database & spravované instance SQL](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Integrita zálohy

Na průběžném základě technický tým Azure SQL automaticky testuje obnovení automatizovaných záloh databáze. (Toto testování není aktuálně k dispozici ve spravované instanci SQL.) Při obnovení k bodu v čase získají databáze také kontroly integrity DBCC CHECKDB.

Všechny problémy zjištěné během kontroly integrity budou mít za následek upozornění technickému týmu. Další informace najdete v tématu [Integrita dat v SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

Všechny zálohy databáze jsou pořízeny s možností KONTROLNÍho SOUČTu, která poskytuje další integritu zálohování.

## <a name="compliance"></a>Dodržování předpisů

Při migraci databáze z úrovně služby založené na DTU na úroveň služby založené na vCore se uchování PITR zachová, aby se zajistilo ohrožení zásad obnovení dat vaší aplikace. Pokud výchozí doba uchovávání nevyhovuje vašim požadavkům na dodržování předpisů, můžete změnit dobu uchování PITR. Další informace najdete v tématu [Změna doby uchování zálohy PITR](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>Změna doby uchování zálohy PITR

Výchozí dobu uchování zálohy PITR můžete změnit pomocí Azure Portal, PowerShellu nebo REST API. Následující příklady ukazují, jak změnit PITR uchování na 28 dní.

> [!WARNING]
> Pokud omezíte dobu uchování, ztratíte možnost obnovení do bodů v čase, který je starší než nová doba uchování. Zálohy, které už nejsou potřeba k poskytování PITR v rámci nové doby uchování, se odstraní. Pokud zvýšíte aktuální dobu uchovávání, okamžitě nezískáte možnost obnovení do starších bodů v čase v rámci nové doby uchování. Tuto možnost získáte v průběhu času, protože systém začne uchovávat zálohy déle.

> [!NOTE]
> Tato rozhraní API budou mít vliv jenom na dobu uchovávání PITR. Pokud jste nakonfigurovali LTR pro vaši databázi, nebude to mít vliv na. Informace o tom, jak změnit dobu uchování LTR, najdete v tématu [dlouhodobé uchovávání](long-term-retention-overview.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Změňte dobu uchování zálohy PITR pomocí Azure Portal

Pokud chcete změnit dobu uchovávání záloh PITR pro aktivní databáze pomocí Azure Portal, přejděte na server nebo na spravovanou instanci s databázemi, jejichž doba uchovávání se má změnit. V levém podokně vyberte **zálohy** a pak vyberte kartu **zásady uchovávání** . Vyberte databáze, pro které chcete změnit PITR uchovávání záloh. Pak vyberte **Konfigurovat uchovávání** z panelu akcí.



#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

![Změna uchovávání PITR, úrovně serveru](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[Spravovaná instance SQL](#tab/managed-instance)

![Změnit uchovávání PITR, spravovaná instance](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>Změna doby uchovávání záloh PITR pomocí prostředí PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell AzureRM je stále podporován SQL Database a SQL Managed instance, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Další informace naleznete v tématu [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty příkazů v modulu AZ jsou v podstatě stejné jako v modulech AzureRm.

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

Pro změnu uchovávání záloh PITR pro aktivní databáze SQL Azure použijte následující příklad PowerShellu.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

#### <a name="sql-managed-instance"></a>[Spravovaná instance SQL](#tab/managed-instance)

Chcete-li změnit uchovávání záloh PITR pro **jednotlivé aktivní** databáze spravované instance SQL, použijte následující příklad prostředí PowerShell.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase -RetentionDays 1
```

Pro změnu uchovávání záloh PITR pro **všechny aktivní** databáze spravované instance SQL použijte následující příklad prostředí PowerShell.

```powershell
# SET new PITR backup retention period for ALL active databases
# Valid backup retention must be between 1 and 35 days
Get-AzSqlInstanceDatabase -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 1
```

Pro změnu uchovávání záloh PITR pro **jednotlivé odstraněné** databáze spravované instance SQL použijte následující příklad PowerShellu.
 
```powershell
# SET new PITR backup retention on an individual deleted database
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database.
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

Pro změnu uchovávání záloh PITR pro **všechny odstraněné** databáze spravované instance SQL použijte následující příklad PowerShellu.

```powershell
# SET new PITR backup retention for ALL deleted databases
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

Nula (0) dnů uchování znamená, že se záloha okamžitě odstraní a nebude se už uchovávat pro odstraněnou databázi.
Po zmenšení PITR zálohy pro odstraněnou databázi již nebude možné ji zvýšit.

---

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>Změňte dobu uchování zálohy PITR pomocí REST API

#### <a name="sample-request"></a>Ukázková žádost

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Text požadavku

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Ukázková odpověď

Stavový kód: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Další informace najdete v tématu [REST API uchovávání záloh](/rest/api/sql/backupshorttermretentionpolicies).

#### <a name="sample-request"></a>Ukázková žádost

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Text požadavku

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Ukázková odpověď

Stavový kód: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Další informace najdete v tématu [REST API uchovávání záloh](/rest/api/sql/backupshorttermretentionpolicies).

## <a name="configure-backup-storage-redundancy"></a>Konfigurace redundance úložiště zálohování

> [!NOTE]
> Nastavitelná redundance úložiště pro zálohování pro spravovanou instanci SQL se dá zadat jenom během procesu vytváření spravované instance. Po zřízení prostředku nemůžete změnit možnost redundance úložiště zálohování. Pro SQL Database je verze Public Preview této funkce aktuálně dostupná v oblasti Brazílie – jih a je všeobecně dostupná v oblasti jihovýchodní Asie v Azure. 

Redundanci úložiště zálohy spravované instance lze nastavit pouze během vytváření instance. Pro SQL Database může být nastavená při vytváření databáze nebo může být aktualizována pro existující databázi. Výchozí hodnota je geograficky redundantní úložiště. Rozdíly v cenách mezi místně redundantním, redundantním a geograficky redundantním úložištěm záloh najdete na [stránce s cenami za Managed instance](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

### <a name="configure-backup-storage-redundancy-by-using-the-azure-portal"></a>Konfigurace redundance úložiště zálohování pomocí Azure Portal

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

V Azure Portal můžete v okně **vytvořit SQL Database** nakonfigurovat redundanci úložiště zálohování. Tato možnost je k dispozici v části redundance záložního úložiště. 
![Otevřít okno pro vytvoření SQL Database](./media/automated-backups-overview/sql-database-backup-storage-redundancy.png)

#### <a name="sql-managed-instance"></a>[Spravovaná instance SQL](#tab/managed-instance)

V Azure Portal se při vytváření spravované instance SQL na kartě základy v případě, že vytváříte spravovanou instanci SQL, v okně **COMPUTE a úložiště** , které je dostupné na kartě **základy** , **nachází možnost změnit** redundanci záložního úložiště.
![Otevření konfigurace COMPUTE + úložiště – okno](./media/automated-backups-overview/open-configuration-blade-managedinstance.png)

V okně **COMPUTE + úložiště** Najděte možnost vybrat redundanci úložiště zálohování.
![Konfigurace redundance úložiště zálohování](./media/automated-backups-overview/select-backup-storage-redundancy-managedinstance.png)

---

### <a name="configure-backup-storage-redundancy-by-using-powershell"></a>Konfigurace redundance úložiště zálohování pomocí PowerShellu

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

Při konfiguraci redundance záložního úložiště při vytváření nové databáze můžete zadat parametr-BackupStoageRedundancy. Možné hodnoty jsou geografické, zóna a místní. Ve výchozím nastavení používají všechny databáze SQL geograficky redundantní úložiště k zálohování. Geografická Obnova je zakázaná, pokud je databáze vytvořená s místním nebo záložním úložištěm zálohování zóny. 

```powershell
# Create a new database with geo-redundant backup storage.  
New-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -ServerName "Server01" -DatabaseName "Database03" -Edition "GeneralPurpose" -Vcore 2 -ComputeGeneration "Gen5" -BackupStorageRedundancy Geo
```

Podrobnosti najdete v [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).

Pokud chcete aktualizovat redundanci záložního úložiště existující databáze, můžete použít parametr-BackupStorageRedundancy. Možné hodnoty jsou geografické, zóna a místní.
Všimněte si, že může trvat až 48 hodin, než se změny použijí v databázi. Přepnutí z geograficky redundantního úložiště zálohování na místní nebo do redundantního úložiště zóny zakáže geografické obnovení. 

```powershell
# Change the backup storage redundancy for Database01 to zone-redundant. 
Set-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -DatabaseName "Database01" -ServerName "Server01" -BackupStorageRedundancy Zone
```

Podrobnosti najdete v [sadě set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) .

> [!NOTE]
> Pokud chcete použít parametr-BackupStorageRedundancy s obnovením databáze, kopírováním databáze nebo vytvořením sekundárních operací, použijte Azure PowerShell verze AZ. SQL 2.11.0. 


#### <a name="sql-managed-instance"></a>[Spravovaná instance SQL](#tab/managed-instance)

Pro konfiguraci redundance záložního úložiště během vytváření spravované instance můžete zadat parametr-BackupStoageRedundancy. Možné hodnoty jsou geografické, zóna a místní.

```powershell
New-AzSqlInstance -Name managedInstance2 -ResourceGroupName ResourceGroup01 -Location westcentralus -AdministratorCredential (Get-Credential) -SubnetId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/resourcegroup01/providers/Microsoft.Network/virtualNetworks/vnet_name/subnets/subnet_name" -LicenseType LicenseIncluded -StorageSizeInGB 1024 -VCore 16 -Edition "GeneralPurpose" -ComputeGeneration Gen4 -BackupStorageRedundancy Geo
```

Další podrobnosti najdete v [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance).

---

## <a name="use-azure-policy-to-enforce-backup-storage-redundancy"></a>Použití Azure Policy k vymáhání redundance úložiště zálohování

Pokud máte požadavky na data, které vyžadují, abyste zachovali všechna vaše data v jedné oblasti Azure, můžete pro SQL Database nebo spravovanou instanci vynutit zálohy na základě zóny nebo místně redundantního zálohování pomocí Azure Policy. Azure Policy je služba, kterou můžete použít k vytváření, přiřazování a správě zásad, které používají pravidla pro prostředky Azure. Azure Policy vám pomůže zajistit, aby tyto prostředky vyhovovaly vašim firemním standardům a smlouvám o úrovni služeb. Další informace najdete v tématu [přehled Azure Policy](../../governance/policy/overview.md). 

### <a name="built-in-backup-storage-redundancy-policies"></a>Integrované zásady redundance záložního úložiště 

Přidají se nové předdefinované zásady, které se dají přiřadit na úrovni předplatného nebo skupiny prostředků, aby se blokovaly vytváření nových databází nebo instancí s geograficky redundantním úložištěm záloh. 

[SQL Database by se nemělo používat redundanci zálohování GRS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb219b9cf-f672-4f96-9ab0-f5a3ac5e1c13)

[Spravované instance SQL by se neměly používat redundanci GRS Backup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9934fd7-29f2-4e6d-ab3d-607ea38e9079)

Úplný seznam předdefinovaných definic zásad pro SQL Database a spravovanou instanci najdete [tady](./policy-reference.md).

K vykonání požadavků na uspořádání dat na úrovni organizace je možné tyto zásady přiřadit k předplatnému. Po přiřazení na úrovni předplatného nebudou uživatelé v daném předplatném moct vytvořit databázi nebo spravovanou instanci s geograficky redundantním úložištěm zálohování prostřednictvím Azure Portal nebo Azure PowerShell. 

> [!IMPORTANT]
> Při vytváření databáze prostřednictvím T-SQL se neuplatňují zásady Azure. Pokud chcete vyhodnotit zajistění dat při vytváření databáze pomocí T-SQL, [použijte v příkazu CREATE DATABASE možnost Local nebo Zone jako vstup BACKUP_STORAGE_REDUNDANCY parametr](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#create-database-using-zone-redundancy-for-backups).

Naučte se přiřazovat zásady pomocí [Azure Portal](../../governance/policy/assign-policy-portal.md) nebo [Azure PowerShell](../../governance/policy/assign-policy-powershell.md)


## <a name="next-steps"></a>Další kroky

- Zálohy databází jsou důležitou součástí jakékoli strategie pro provozní kontinuitu a zotavení po havárii, protože chrání vaše data před náhodným poškozením nebo odstraněním. Další informace o dalších SQL Database řešení pro provozní kontinuitu najdete v tématu [Přehled provozní kontinuity](business-continuity-high-availability-disaster-recover-hadr-overview.md).
- Získejte další informace o tom, jak [obnovit databázi k určitému bodu v čase pomocí Azure Portal](recovery-using-backups.md).
- Získejte další informace o tom, jak [obnovit databázi k určitému bodu v čase pomocí prostředí PowerShell](scripts/restore-database-powershell.md).
- Informace o tom, jak nakonfigurovat, spravovat a obnovit dlouhodobé uchovávání automatizovaných záloh v úložišti objektů BLOB v Azure pomocí Azure Portal, najdete v tématu [Správa dlouhodobého uchovávání záloh pomocí Azure Portal](long-term-backup-retention-configure.md).
- Informace o tom, jak nakonfigurovat, spravovat a obnovit dlouhodobé uchovávání automatizovaných záloh v úložišti objektů BLOB v Azure pomocí PowerShellu, najdete v tématu [Správa dlouhodobého uchovávání záloh pomocí PowerShellu](long-term-backup-retention-configure.md).
- Informace o tom, jak vyladit uchování a náklady na úložiště zálohování pro spravovanou instanci Azure SQL, najdete v tématu [jemné vyladění nákladů na úložiště zálohování ve spravované instanci](https://aka.ms/mi-backup-tuning).
