---
title: "Nejčastější dotazy k Azure SQL Database | Microsoft Docs"
description: "Odpovědi na běžné otázky zákazníků, požádejte o cloudu databáze a databáze SQL Azure, společnosti Microsoft systému správy relačních databází (RDBMS) a databáze jako služba v cloudu."
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: article
ms.date: 02/12/2018
ms.author: carlrab
ms.openlocfilehash: 59058b3445fdaf75df331d771afd28599491c52e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="sql-database-faq"></a>SQL Database – Nejčastější dotazy

## <a name="what-is-the-current-version-of-sql-database"></a>Co je aktuální verze SQL Database?
Je aktuální verze databáze SQL verze 12. Verze 11 verze byla vyřazena.

## <a name="what-is-the-sla-for-sql-database"></a>Co je smlouvě SLA pro databázi SQL?
Garantujeme, že minimálně 99,99% času budou mít zákazníci připojení jediné nebo elastické služby Microsoft Azure SQL Database úrovně Basic, Standard nebo Premium k naší internetové bráně. Další informace najdete v tématu [SLA](http://azure.microsoft.com/support/legal/sla/).

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>Jak resetovat heslo správce serveru
V [portál Azure](https://portal.azure.com), klikněte na tlačítko **servery SQL**, vyberte server, ze seznamu a pak klikněte na tlačítko **resetovat heslo**.

## <a name="how-do-i-manage-databases-and-logins"></a>Jak spravovat databází a přihlašovacích údajů?
V tématu [Správa databází a přihlašovacích údajů](sql-database-manage-logins.md).

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>Jak se ujistěte se, jsou povoleny pouze autorizovaným IP adres přístup k serveru?
V tématu [postupy: Konfigurace nastavení brány firewall pro službu SQL Database](sql-database-configure-firewall-settings.md).

## <a name="how-does-the-usage-of-sql-database-show-up-on-my-bill"></a>Jak použití databáze SQL objeví na Moje faktury?
Ve vrstvě služeb na základě faktur SQL Database na předvídatelný hodinové míra + úroveň výkonu pro izolované databáze nebo Edtu za elastického fondu. Skutečné využití je počítaný a poměrně každou hodinu, takže vaše faktura může zobrazovat zlomků jednu hodinu. Například pokud databáze existuje 12 hodin za měsíc, vaše faktura zobrazuje využití 0,5 dní. Úrovně služeb + úroveň výkonu a Edtu na fond jsou navíc rozdělená v faktury, aby bylo snazší zobrazíte počet počet dnů používání databáze, které jste použili pro každou za jeden měsíc.

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>Co v případě jedné databáze je aktivní méně než jednu hodinu nebo používá vyšší úroveň služby pro méně než jednu hodinu?
Fakturuje se pro každou hodinu, kterou databáze existuje, pomocí nejvyšší úroveň služby + úroveň výkonu, které použijí danou dobu, bez ohledu na využití nebo zda byla databáze active méně než jednu hodinu. Například pokud vytvoříte jedné databáze a odstranit ji pěti minutách vaše faktura odráží zdarma pro jednu databázi hodinu. 

Příklady:

* Pokud chcete vytvořit základní databáze a pak okamžitě upgradovat na Standard S1, budeme vám účtovat rychlostí Standard S1 první hodiny.
* Pokud upgradujete databázi z Basic Premium na 10:00. a dokončení upgradu v 1:35 hodin Následující den budou se vám účtovat rychlostí Premium spouštění v 1:00 
* Pokud ponížit na základní databázi z Premium ve 20:00 a dokončení na 14:15:00 a pak databázi je účtován rychlostí Premium až 3:00 hodin, po jejímž uplynutí je účtován základní tempem.

## <a name="how-does-elastic-pool-usage-show-up-on-my-bill-and-what-happens-when-i-change-edtus-per-pool"></a>Jak využití elastického fondu zobrazit na můj fakturaci a co se stane, když změnit počet jednotek Edtu na fond?
Elastický fond účtuje zobrazit nahoru na vaší faktuře jako elastické jednotky Dtu (Edtu) v přírůstcích po zobrazený pod Edtu na fond na [na stránce s cenami](https://azure.microsoft.com/pricing/details/sql-database/). Je bezplatná jednotlivé databáze pro elastické fondy. Fakturuje se pro každou hodinu, které fond existuje v nejvyšší jednotek eDTU, bez ohledu na využití nebo jestli byl fond aktivní méně než jednu hodinu. 

Příklady:

* Pokud vytvoříte standardní elastický fond s 200 Edtu v 11:18:00, přidávání pět databází do fondu, budeme vám účtovat 200 Edtu pro celou hodinu od v 11: 00 ve zbývající části dne.
* Den 2 na 5:05:00. 1 databáze začne využívání 50 Edtu a má konstantní prostřednictvím dne. Databáze 2 až 5 kolísá mezi 0 a 80 Edtu. Během dne přidejte pět jiných databází, které využívají různých Edtu během dne. Den 2 je plný den účtovat podle 200 eDTU. 
* Den 3, ve 5: 00. můžete přidat další 15 databáze. Využití databáze se zvyšuje během dne do bodu, kde se rozhodnete zvýšit počet jednotek Edtu fondu z hodnoty 200 na 400 ve 20:05. Poplatky na úrovni 200 eDTU bylo platné až 20: 00 a zvýší na 400 Edtu pro zbývající čtyři hodiny. 

## <a name="elastic-pool-billing-and-pricing-information"></a>Elastický fond fakturaci a informace o cenách
Elastické fondy fakturují za následující vlastnosti:

* Elastický fond se fakturuje po jeho vytvoření i v případě, že nejsou žádné databáze ve fondu.
* Elastický fond se fakturuje každou hodinu. Jedná se o stejné měření četnost jako úrovně výkonu izolovaných databází.
* Pokud elastickém fondu se změnila velikost na nový počet jednotek Edtu, není až po dokončení operace změny velikosti fondu účtují podle nové množství Edtu. To se následující stejné jako změna úrovně výkonu izolovaných databází.
* Počet jednotek Edtu fondu podle cenu fondu elastické databáze. Cena fondu elastické databáze je nezávislé na číslo a využití elastické databáze v něm.
* Cena se počítá podle (počet jednotek Edtu fondu) x (jednotkové ceny za eDTU).

Cena jednotky eDTU fondu elastické databáze je vyšší než cena jednotky DTU pro jednu databázi ve stejné vrstvě služby. Podrobnosti najdete na stránce s [cenami služby SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). 

Pochopení úrovní Edtu a služby, najdete v tématu [výkon a možnosti databáze SQL](sql-database-service-tiers.md).

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>Jak funguje použití aktivní geografickou replikací v elastický fond se zobrazí na Moje faktury?
Na rozdíl od izolovaných databází můžete pomocí [aktivní geografickou replikací](sql-database-geo-replication-overview.md) s elastické databáze nemá přímý vliv fakturace.  Se účtují poplatky pro Edtu zřízené pro každou z fondů (fondu primární a sekundární fond)

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>Jak se v mém vyúčtování projeví používání funkce auditování?
Auditování je integrovaná do služby SQL Database bez jakýchkoli nákladů a je k dispozici pro databáze Basic, Standard a Premium. Však k ukládání protokolů auditu, auditování použití funkce, které účet služby Azure Storage a sazby za tabulky a fronty ve službě Azure Storage se vztahují na základě velikosti protokolu auditu.

## <a name="how-do-i-find-the-right-service-tier-and-performance-level-for-single-databases-and-elastic-pools"></a>Jak najít správné služby vrstvy a úroveň výkonu pro izolované databáze i elastické fondy?
Jsou k dispozici několik nástrojů: 

* Pro místní databáze, použijte [DTU nastavení velikosti advisor](http://dtucalculator.azurewebsites.net/) doporučujeme databáze a požadované Dtu a vyhodnotit více databází pro elastické fondy.
* Pokud jedné databáze by využívat výhody ve fondu, inteligentního modul Azure pokud ji vidí historie využití vzor, která vyžaduje se doporučuje fondu elastické databáze. V tématu [monitorování a správa fondu elastické databáze pomocí portálu Azure](sql-database-elastic-pool-manage-portal.md). Podrobnosti o tom, jak si můžete výpočty provést sami najdete v tématu [cenové a výkonové požadavky fondu elastické databáze](sql-database-elastic-pool.md)
* Informace, zda je nutné vytočit jedné databáze nahoru nebo dolů, najdete v tématu [výkonu pokyny pro izolované databáze](sql-database-performance-guidance.md).

## <a name="how-often-can-i-change-the-service-tier-or-performance-level-of-a-single-database"></a>Jak často můžete změnit úroveň služby výkon nebo úroveň služby jedné databáze?
Můžete tak často, jak chcete změnit úroveň služby (mezi Basic, Standard a Premium) nebo na úroveň výkonu v rámci vrstvu služby (například S1 s2). Pro starší verze databáze můžete změnit úroveň služby vrstvě nebo výkonu celkem čtyřikrát za období 24 hodin.

## <a name="how-often-can-i-adjust-the-edtus-per-pool"></a>Jak často můžete upravit počet jednotek Edtu na fond?
Tolikrát, kolikrát chcete.

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>Jak dlouho trvá změnit úroveň služby výkon nebo úroveň služby jedné databáze nebo přesun databáze do/z fondu elastické databáze?
Změna úrovně služby databáze a přesouvání a deaktivovat fondu vyžaduje databázi, který se má zkopírovat na platformě jako operaci na pozadí. Změna úrovně služby může trvat několik minut až několik hodin v závislosti na velikosti databáze. V obou případech databáze zůstat online a dostupné během přesunu. Podrobnosti o změně izolované databáze najdete v tématu [změnit úroveň služby databázi](sql-database-service-tiers.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Kdy použít jednu databázi oproti elastické databáze?
Obecně platí, elastické fondy navržených pro typické [vzor aplikace software jako služba (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md), kde je jedna databáze na zákazníka nebo klienta. Nakupování jednotlivých databází a jejich předimenzování kvůli měnícím se požadavkům každé databáze ve špičce často není hospodárné z hlediska nákladů. Fondy spravovat celkový výkon fondu a databáze škálovat nahoru a dolů automaticky. Inteligentní modul Azure doporučuje fond pro databáze při vzor používání zaručuje ho. Podrobnosti najdete v tématu [elastického fondu pokyny](sql-database-elastic-pool.md).

## <a name="what-does-it-mean-to-have-up-to-200-of-your-maximum-provisioned-database-storage-for-backup-storage"></a>Co znamená, že je k dispozici úložiště pro zálohování, které má maximální velikost až 200 % objemu úložiště zřízené databáze?
Úložiště zálohování je úložiště přidružený k vaší zálohy automatizované databáze, které se používají pro [bodu-v-čas – obnovení](sql-database-recovery-using-backups.md#point-in-time-restore) a [geografické obnovení](sql-database-recovery-using-backups.md#geo-restore). Microsoft Azure SQL Database nabízí bez dodatečných nákladů objem úložiště pro zálohování o maximální velikosti až 200 % objemu úložiště databáze, kterou máte zřízenou. Například pokud máte standardní DB instance s zřízené DB velikosti 250 GB, jsou k dispozici 500 GB úložiště zálohování bez dalších poplatků. Pokud vaše databáze překročí zadané úložiště záloh, můžete zkrátit dobu uchování kontaktováním podpory Azure nebo platit za úložiště velmi zálohování účtovat standardní sazbou přístup pro čtení geograficky redundantní úložiště (RA-GRS). Další informace o fakturaci RA-GRS najdete v části Podrobnosti o cenách úložiště.

## <a name="im-moving-from-webbusiness-to-the-new-service-tiers-what-do-i-need-to-know"></a>Jsem se přesun z webového nebo obchodní na nové úrovně služeb, Co potřebuji vědět?
Nyní jsou vyřazené databáze Azure SQL Web a Business. Úrovně Basic, Standard a Premium a Elastická nahraďte retiring databáze Web a Business. 

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>Co je očekávané replikace funkce lag při geografickou replikaci databáze mezi dvěma oblastmi v rámci stejné geography Azure?
Jsme se aktuálně podporují RPO pět sekund a je zpoždění replikace byla menší než, když je sekundární geograficky hostované ve službě Azure doporučujeme spárované oblasti a ve stejné vrstvě služby.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>Co je očekávané replikace funkce lag při geograficky sekundární ve stejné oblasti jako primární databáze?
Na základě na základě zkušeností data, není příliš mnoho rozdíl mezi uvnitř oblasti a prodleva mezi oblast replikace při Azure doporučoval spárované oblast se používá. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>Pokud je mezi dvěma oblastmi selhání sítě, jak logika opakovaných pokusů funguje při geografická replikace je nastaven?
Pokud dojde k odpojení, opakované každých 10 sekund znovu navázat připojení.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>Co můžete dělat zaručit, že se replikují na kritické změnu primární databáze?
Je geo sekundární repliku asynchronní a jsme nesnažte se mějte úplnou synchronizaci s primární. Ale poskytujeme metoda vynutit synchronizaci zajistit replikaci důležité změny (např. aktualizace hesel). Vynucené synchronizace ovlivňuje výkon, protože blokuje volající vlákno, dokud se replikují všechny potvrzené transakce. Podrobnosti najdete v tématu [uložená procedura sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>Jaké nástroje jsou k dispozici ke sledování replikace prodleva mezi primární databáze a geografická sekundární?
Funkce lag v reálném čase replikace mezi primární databáze a geo sekundární prostřednictvím DMV zveřejňujeme. Podrobnosti najdete v tématu [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Chcete-li přesunout databázi na jiný server v rámci stejného předplatného
V [portál Azure](https://portal.azure.com), klikněte na tlačítko **databází SQL**, vyberte databázi ze seznamu a pak klikněte na tlačítko **kopie**. V tématu [zkopírujte Azure SQL database](sql-database-copy.md) další podrobnosti.

## <a name="to-move-a-database-between-subscriptions"></a>Přesunutí databáze mezi předplatnými
V [portál Azure](https://portal.azure.com), klikněte na tlačítko **servery SQL** a pak vyberte server, který je hostitelem databáze ze seznamu. Klikněte na tlačítko **přesunout**a pak vyberte zdroje, které chcete přesunout a předplatné pro přesun do.
