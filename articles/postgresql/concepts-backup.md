---
title: Zálohování a obnovení – Azure Database for PostgreSQL – jeden server
description: Přečtěte si o automatických zálohách a obnovení serveru Azure Database for PostgreSQL Server-Single.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: e74c96e0c03d75f34a16d95d0bed642c1900f558
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219719"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---single-server"></a>Zálohování a obnovení v Azure Database for PostgreSQL – jeden server

Azure Database for PostgreSQL automaticky vytvoří zálohy serveru a uloží je v uživatelsky nakonfigurovaném místně redundantním nebo geograficky redundantním úložišti. Zálohy lze použít k obnovení serveru do určitého bodu v čase. Zálohování a obnovení jsou základní součástí jakékoli strategie kontinuity podnikových procesů, protože chrání data před náhodným poškozením nebo odstraněním.

## <a name="backups"></a>Zálohování

Azure Database for PostgreSQL přebírá zálohy datových souborů a transakčního protokolu. V závislosti na podporované maximální velikosti úložiště vezmeme úplné a rozdílové zálohy (4 TB max. servery úložiště) nebo zálohy snímků (až 16 TB maximálních úložných serverů). Tyto zálohy umožňují obnovit server k jakémukoli časovému okamžiku v rámci nakonfigurované doby uchovávání záloh. Výchozí doba uchovávání záloh je sedm dní. Volitelně je můžete nakonfigurovat až 35 dní. Všechny zálohy se šifrují s využitím 256bitového šifrování AES.

Tyto záložní soubory nelze exportovat. Zálohy lze použít pouze pro operace obnovení v Azure Database for PostgreSQL. K zkopírování databáze můžete použít [pg_dump](howto-migrate-using-dump-and-restore.md) .

### <a name="backup-frequency"></a>Frekvence zálohování

#### <a name="servers-with-up-to-4-tb-storage"></a>Servery s úložištěm až 4 TB

Pro servery, které podporují až 4 TB maximálního úložiště, se k úplným zálohám dochází každý týden. Rozdílové zálohy se vyskytují dvakrát denně. Zálohování transakčních protokolů probíhá každých pět minut.


#### <a name="servers-with-up-to-16-tb-storage"></a>Servery s až 16 TB úložiště

V podmnožině [oblastí Azure](./concepts-pricing-tiers.md#storage)můžou všechny nově zřízené servery podporovat úložiště až o 16 TB. Zálohy na těchto velkých serverech úložiště jsou založené na snímcích. První úplné zálohování snímků je naplánované okamžitě po vytvoření serveru. Tato první úplná záloha snímku se uchová jako základní záloha serveru. Další zálohování snímků je pouze rozdílové. Rozdílové zálohování snímků se neprovádí podle pevně daného plánu. Za den se provádí tři rozdílové zálohy snímků. Zálohování transakčních protokolů probíhá každých pět minut. 

### <a name="backup-retention"></a>Uchování záloh

Zálohy se uchovávají na základě nastavení období uchovávání záloh na serveru. Můžete vybrat dobu uchování 7 až 35 dní. Výchozí doba uchování je 7 dní. Dobu uchování během vytváření serveru nebo později můžete nastavit tak, že aktualizujete konfiguraci zálohování pomocí [Azure Portal](./howto-restore-server-portal.md#set-backup-configuration) nebo rozhraní příkazového [řádku Azure CLI](./howto-restore-server-cli.md#set-backup-configuration). 

Doba uchovávání záloh určuje, jak daleko se obnovení k určitému bodu v čase dá načíst, protože je založené na dostupných zálohách. Období uchovávání záloh lze také považovat za okno obnovení z perspektivy obnovení. Všechny zálohy potřebné k provedení obnovení k určitému bodu v čase v rámci období uchovávání záloh jsou uchovávány v úložišti záloh. Pokud je například doba uchování zálohy nastavená na 7 dní, okno obnovení se považuje za posledních 7 dní. V tomto scénáři jsou zachovány všechny zálohy potřebné k obnovení serveru za posledních 7 dní. Okno uchování zálohy po dobu sedmi dnů:
- Servery s úložištěm až 4 TB budou uchovávat až 2 úplné zálohy databáze, všechny rozdílové zálohy a zálohy transakčního protokolu byly provedeny od nejstarší úplné zálohy databáze.
-   Servery s až 16 TB úložiště uchovávají úplný snímek databáze, všechny rozdílové snímky a zálohy protokolů transakcí za posledních 8 dní.

### <a name="backup-redundancy-options"></a>Možnosti redundance zálohy

Azure Database for PostgreSQL poskytuje flexibilitu při výběru místně redundantního nebo geograficky redundantního úložiště záloh v Pro obecné účely a paměťově optimalizovaných úrovních. Když jsou zálohy uložené v geograficky redundantním úložišti zálohování, neukládají se jenom v oblasti, ve které je váš server hostovaný, ale taky se replikují do [spárovaného datového centra](../best-practices-availability-paired-regions.md). To zajišťuje lepší ochranu a možnost obnovení serveru v jiné oblasti v případě havárie. Úroveň Basic nabízí jenom místně redundantní úložiště záloh.

> [!IMPORTANT]
> Místně redundantní nebo geograficky redundantní úložiště zálohování je možné nakonfigurovat pouze při vytváření serveru. Po zřízení serveru není možné změnit možnost redundance úložiště zálohování.

### <a name="backup-storage-cost"></a>Náklady na úložiště zálohování

Azure Database for PostgreSQL poskytuje úložiště zřízeného serveru jako úložiště pro zálohování až 100%, a to bez dalších nákladů. Jakékoli další využité úložiště záloh se účtuje za GB za měsíc. Pokud jste například zřídili Server s 250 GB úložiště, máte k dispozici 250 GB dalšího úložiště pro zálohy serveru bez dalších poplatků. Úložiště spotřebované za zálohy větší než 250 GB se účtuje podle [cenového modelu](https://azure.microsoft.com/pricing/details/postgresql/).

Pomocí metriky [úložiště zálohování](concepts-monitoring.md) v Azure monitor k dispozici v Azure Portal můžete monitorovat úložiště zálohování spotřebované serverem. Metrika využitého úložiště záloh představuje součet úložiště spotřebovaného všemi úplnými zálohami databáze, rozdílové zálohy a zálohy protokolů, které jsou zachovány na základě nastaveného období uchovávání záloh pro server. Frekvence zálohování je spravována službou a byla vysvětlena dříve. Náročné transakční aktivity na serveru můžou způsobit zvýšení využití úložiště zálohování bez ohledu na celkovou velikost databází. V případě geograficky redundantního úložiště je využití úložiště zálohování dvakrát místní redundantní úložiště. 

Hlavním prostředkem řízení nákladů na úložiště zálohování je nastavení vhodné doby uchovávání záloh a výběr správné možnosti redundance zálohování, která bude vyhovovat požadovaným cílům obnovení. Můžete vybrat dobu uchování z rozsahu 7 až 35 dní. Pro obecné účely a paměťově optimalizované servery se můžou rozhodnout pro zálohování geograficky redundantního úložiště.

## <a name="restore"></a>Obnovení

Při obnovení se v Azure Database for PostgreSQL vytvoří nový server ze zálohy původního serveru. 

K dispozici jsou dva typy obnovení:

- Obnovení k určitému **bodu v čase** je dostupné s možností redundance zálohy a vytvoří nový server ve stejné oblasti jako původní server.
- **Geografické obnovení** je k dispozici pouze v případě, že jste server nakonfigurovali pro geograficky redundantní úložiště a máte možnost obnovit server do jiné oblasti.

Odhadovaná doba obnovení závisí na několika faktorech, včetně velikostí databází, velikosti transakčního protokolu, šířky pásma sítě a celkového počtu databází obnovování ve stejné oblasti ve stejnou dobu. Doba obnovení je obvykle méně než 12 hodin.

> [!NOTE] 
> Pokud je váš zdrojový server PostgreSQL zašifrovaný pomocí klíčů spravovaných zákazníkem, přečtěte si prosím [dokumentaci](concepts-data-encryption-postgresql.md) , kde najdete další informace. 

> [!NOTE]
> Pokud chcete obnovit odstraněný PostgreSQL Server, postupujte [podle popsané procedury.](howto-restore-dropped-server.md)

### <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase

Nezávisle na možnosti redundance záloh můžete provést obnovení do libovolného bodu v čase v rámci doby uchovávání záloh. Nový server se vytvoří ve stejné oblasti Azure jako původní server. Vytvoří se s konfigurací původního serveru pro cenovou úroveň, generování výpočtů, počet virtuální jádra, velikost úložiště, dobu uchování zálohy a možnost redundance zálohy.

Obnovení k bodu v čase je užitečné ve více scénářích. Například když uživatel omylem odstraní data, ponechá důležitou tabulku nebo databázi, nebo pokud aplikace náhodně přepíše dobrá data s chybnými daty z důvodu vady aplikace.

Možná budete muset počkat, než bude provedena další záloha protokolu transakcí, než bude možné provést obnovení k určitému bodu v čase během posledních pěti minut.

Pokud chcete obnovit vyřazenou tabulku, 
1. Obnoví zdrojový server pomocí metody v čase.
2. Vypíše tabulku pomocí `pg_dump` obnoveného serveru.
3. Přejmenovat zdrojovou tabulku na původním serveru.
4. Import tabulky pomocí příkazového řádku psql na původním serveru.
5. Můžete volitelně odstranit obnovený server.

>[!Note]
> Doporučuje se nevytvářet více obnovení pro stejný server současně. 

### <a name="geo-restore"></a>Geografické obnovení

Server můžete obnovit do jiné oblasti Azure, kde je služba k dispozici, pokud jste server nakonfigurovali pro geograficky redundantní zálohy. Servery, které podporují až 4 TB úložiště, se dají obnovit do geografické spárované oblasti nebo do jakékoli oblasti, která podporuje až 16 TB úložiště. Pro servery, které podporují až 16 TB úložiště, se geografické zálohy dají obnovit v libovolné oblasti, která podporuje i 16 TB serverů. Seznam podporovaných oblastí najdete v [Azure Database for PostgreSQL cenové úrovně](concepts-pricing-tiers.md) .

Geografické obnovení je výchozí možností obnovení v případě, že server není k dispozici z důvodu incidentu v oblasti, kde je server hostován. Pokud má velký incident v oblasti nedostupnost vaší databázové aplikace, můžete obnovit server z geograficky redundantní zálohy na server v jakékoli jiné oblasti. Doba mezi vytvořením zálohy a při replikaci do jiné oblasti trvá zpoždění. Tato prodleva může trvat až jednu hodinu, takže pokud dojde k havárii, může dojít ke ztrátě dat o hodinu.

Během geografického obnovení můžou konfigurace serveru, které je možné změnit, zahrnovat výpočetní generování, vCore, dobu uchování záloh a možnosti redundance zálohování. Změna cenové úrovně (Basic, Pro obecné účely nebo paměťově optimalizovaná) nebo velikosti úložiště se nepodporuje.

> [!NOTE]
> Pokud váš zdrojový server používá dvojité šifrování infrastruktury pro obnovení serveru, existují omezení, včetně oblastí, které jsou k dispozici. Další podrobnosti najdete v tématu [šifrování s dvojitou infrastrukturou](concepts-infrastructure-double-encryption.md) .

### <a name="perform-post-restore-tasks"></a>Provádění úloh po obnovení

Po obnovení z některého mechanismu obnovení byste měli provést následující úlohy, aby se uživatelé a aplikace mohli zálohovat a spustit:

- Pokud by nový server chtěl nahradit původní server, přesměrujte klienty a klientské aplikace na nový server. Také změňte uživatelské jméno na `username@new-restored-server-name` .
- Aby se uživatelé mohli připojit, zajistěte, aby byla k dismístě vhodná pravidla brány firewall na úrovni serveru a sítě. Tato pravidla se nekopírují z původního serveru.
- Zajistěte, aby byla zajištěna příslušná přihlášení a oprávnění na úrovni databáze.
- Podle potřeby nakonfigurujte výstrahy.

## <a name="next-steps"></a>Další kroky

- Naučte se, jak obnovit pomocí [Azure Portal](howto-restore-server-portal.md).
- Naučte se, jak obnovit pomocí [Azure CLI](howto-restore-server-cli.md).
- Další informace o provozní kontinuitě najdete v tématu [Přehled provozní kontinuity](concepts-business-continuity.md).