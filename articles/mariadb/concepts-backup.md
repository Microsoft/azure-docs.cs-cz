---
title: Zálohování a obnovení – Azure Database for MariaDB
description: Přečtěte si o automatických zálohách a obnovení serveru Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 8/13/2020
ms.openlocfilehash: b46efa53bba3b845fa5837b91a3707f4a85d298e
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258771"
---
# <a name="backup-and-restore-in-azure-database-for-mariadb"></a>Zálohování a obnovení v Azure Database for MariaDB

Azure Database for MariaDB automaticky vytvoří zálohy serveru a uloží je v uživatelsky nakonfigurovaném místně redundantním nebo geograficky redundantním úložišti. Zálohy lze použít k obnovení serveru do určitého bodu v čase. Zálohování a obnovení jsou základní součástí jakékoli strategie kontinuity podnikových procesů, protože chrání data před náhodným poškozením nebo odstraněním.

## <a name="backups"></a>Zálohování

Azure Database for MariaDB přebírá zálohy datových souborů a transakčního protokolu. Tyto zálohy umožňují obnovit server k jakémukoli časovému okamžiku v rámci nakonfigurované doby uchovávání záloh. Výchozí doba uchovávání záloh je sedm dní. Volitelně je můžete [nakonfigurovat](howto-restore-server-portal.md#set-backup-configuration) až 35 dní. Všechny zálohy se šifrují s využitím 256bitového šifrování AES.

Tyto záložní soubory nejsou přístupné uživatelům a není možné je exportovat. Tyto zálohy lze použít pouze pro operace obnovení v Azure Database for MySQL. Pomocí [mysqldump](howto-migrate-dump-restore.md) můžete zkopírovat databázi.

Typ a četnost zálohování závisí na úložišti back-endu serverů.

### <a name="backup-type-and-frequency"></a>Typ a frekvence zálohování

#### <a name="basic-storage-servers"></a>Servery úrovně Basic Storage

Základní úložiště je back-end úložiště podporující [servery základních vrstev](concepts-pricing-tiers.md). Zálohy na základních serverech úložiště jsou založené na snímcích. Celý snímek databáze se provádí denně. Pro základní servery úložiště nejsou prováděny žádné rozdílové zálohy a zálohy všech snímků jsou pouze úplné zálohy databáze.

Zálohování transakčních protokolů probíhá každých pět minut.

#### <a name="general-purpose-storage-servers-with-up-to-4-tb-storage"></a>Servery úložiště pro obecné účely s úložištěm s kapacitou až 4 TB

Úložiště pro obecné účely je úložiště back-endu podporující [pro obecné účely](concepts-pricing-tiers.md) a [paměťově optimalizovanou úroveň](concepts-pricing-tiers.md) serveru. U serverů s úložištěm pro obecné účely až do 4 TB dojde k úplnému zálohování v každém týdnu. Rozdílové zálohy se vyskytují dvakrát denně. Zálohování transakčních protokolů probíhá každých pět minut. Zálohy v úložišti pro obecné účely až do 4 TB úložiště nejsou založené na snímcích a v době zálohování nevyužívají propustnost v/v. U rozsáhlých databází (> 1 TB) na 4 TB úložiště doporučujeme zvážit

- Zřizování dalších IOPs pro účet pro zálohování IOs nebo
- Případně můžete migrovat na úložiště pro obecné účely, které podporuje až 16 TB úložiště, pokud je v preferovaných [oblastech Azure](./concepts-pricing-tiers.md#storage)dostupná základní infrastruktura úložiště. Pro účely úložiště pro obecné účely, který podporuje až 16 TB úložiště, se neúčtují žádné další náklady. Pokud potřebujete pomoc s migrací do úložiště o 16 TB, otevřete prosím lístek podpory z Azure Portal.

#### <a name="general-purpose-storage-servers-with-up-to-16-tb-storage"></a>Servery úložiště pro obecné účely s úložištěm až 16 TB

V podmnožině [oblastí Azure](./concepts-pricing-tiers.md#storage)můžou všechny nově zřízené servery podporovat úložiště pro obecné účely až do 16 TB úložišť. Jinými slovy je úložiště s úložištěm až 16 TB výchozím úložištěm pro obecné účely pro všechny [oblasti](concepts-pricing-tiers.md#storage) , kde je podpora podporovaná. Zálohy na těchto 16 TB úložných serverech jsou založené na snímcích. První úplné zálohování snímků je naplánované okamžitě po vytvoření serveru. Tato první úplná záloha snímku se uchová jako základní záloha serveru. Další zálohování snímků je pouze rozdílové.

Rozdílové zálohování snímků se provádí alespoň jednou denně. Rozdílové zálohování snímků se neprovádí podle pevně daného plánu. Rozdílové zálohování snímků probíhá každých 24 hodin, pokud transakční protokol (binlog v MariaDB) překračuje 50 GB od poslední rozdílové zálohy. Každý den je možné provést rozdílové zálohování snímků maximálně šestkrát.

Zálohování transakčních protokolů probíhá každých pět minut.
 

### <a name="backup-retention"></a>Uchování záloh

Zálohy se uchovávají na základě nastavení období uchovávání záloh na serveru. Můžete vybrat dobu uchování 7 až 35 dní. Výchozí doba uchování je 7 dní. Dobu uchování během vytváření serveru nebo později můžete nastavit tak, že aktualizujete konfiguraci zálohování pomocí [Azure Portal](howto-restore-server-portal.md#set-backup-configuration) nebo rozhraní příkazového [řádku Azure CLI](howto-restore-server-cli.md#set-backup-configuration). 

Doba uchovávání záloh určuje, jak daleko se obnovení k určitému bodu v čase dá načíst, protože je založené na dostupných zálohách. Období uchovávání záloh lze také považovat za okno obnovení z perspektivy obnovení. Všechny zálohy potřebné k provedení obnovení k určitému bodu v čase v rámci období uchovávání záloh jsou uchovávány v úložišti záloh. Pokud je například doba uchovávání záloh nastavená na 7 dní, okno obnovení se považuje za posledních 7 dní. V tomto scénáři jsou zachovány všechny zálohy potřebné k obnovení serveru za posledních 7 dní. Okno uchování zálohy po dobu sedmi dnů:

- Servery s úložištěm až 4 TB budou uchovávat až 2 úplné zálohy databáze, všechny rozdílové zálohy a zálohy transakčního protokolu byly provedeny od nejstarší úplné zálohy databáze.
-   Servery s až 16 TB úložiště uchovávají úplný snímek databáze, všechny rozdílové snímky a zálohy protokolů transakcí za posledních 8 dní.

#### <a name="long-term-retention-of-backups"></a>Dlouhodobé uchovávání záloh
Dlouhodobá doba uchovávání záloh přesahujících 35 dní není v tuto chvíli službou nativně podporována. Máte možnost použít mysqldump k ukládání záloh a jejich uložení na dlouhodobé uchovávání. Náš tým podpory blogged [krok za](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/automate-backups-of-your-azure-database-for-mysql-server-to/ba-p/1791157) krokem ke sdílení toho, jak ho můžete dosáhnout. 

### <a name="backup-redundancy-options"></a>Možnosti redundance zálohy

Azure Database for MariaDB poskytuje flexibilitu při výběru místně redundantního nebo geograficky redundantního úložiště záloh v Pro obecné účely a paměťově optimalizovaných úrovních. Když jsou zálohy uložené v geograficky redundantním úložišti zálohování, neukládají se jenom v oblasti, ve které je váš server hostovaný, ale taky se replikují do [spárovaného datového centra](../best-practices-availability-paired-regions.md). To zajišťuje lepší ochranu a možnost obnovení serveru v jiné oblasti v případě havárie. Úroveň Basic nabízí jenom místně redundantní úložiště záloh.

#### <a name="moving-from-locally-redundant-to-geo-redundant-backup-storage"></a>Přechod z místně redundantní do geograficky redundantního úložiště zálohování
Místně redundantní nebo geograficky redundantní úložiště zálohování je možné nakonfigurovat pouze při vytváření serveru. Po zřízení serveru není možné změnit možnost redundance úložiště zálohování. Aby bylo možné přesunout úložiště záloh z místně redundantního úložiště do geograficky redundantního úložiště, je jedinou podporovanou možností vytvoření nového serveru a migrace dat pomocí [výpisu paměti a obnovení](howto-migrate-dump-restore.md) .

### <a name="backup-storage-cost"></a>Náklady na úložiště zálohování

Azure Database for MariaDB poskytuje úložiště zřízeného serveru jako úložiště pro zálohování až 100%, a to bez dalších nákladů. Jakékoli další využité úložiště záloh se účtuje za GB za měsíc. Pokud jste například zřídili Server s 250 GB úložiště, máte k dispozici 250 GB dalšího úložiště pro zálohy serveru bez dalších poplatků. Úložiště spotřebované za zálohy větší než 250 GB se účtuje podle [cenového modelu](https://azure.microsoft.com/pricing/details/mariadb/). 

K monitorování úložiště záloh spotřebovaného serverem můžete použít metriku [použitou pro úložiště zálohování](concepts-monitoring.md) v Azure monitor k dispozici prostřednictvím Azure Portal. Metrika využitého úložiště záloh představuje součet úložiště spotřebovaného všemi úplnými zálohami databáze, rozdílové zálohy a zálohy protokolů, které jsou zachovány na základě nastaveného období uchovávání záloh pro server. Frekvence zálohování je spravována službou a byla vysvětlena dříve. Náročné transakční aktivity na serveru můžou způsobit zvýšení využití úložiště zálohování bez ohledu na celkovou velikost databází. V případě geograficky redundantního úložiště je využití úložiště zálohování dvakrát místní redundantní úložiště. 

Hlavním prostředkem řízení nákladů na úložiště zálohování je nastavení vhodné doby uchovávání záloh a výběr správné možnosti redundance zálohování, která bude vyhovovat požadovaným cílům obnovení. Můžete vybrat dobu uchování z rozsahu 7 až 35 dní. Pro obecné účely a paměťově optimalizované servery se můžou rozhodnout pro zálohování geograficky redundantního úložiště.

## <a name="restore"></a>Obnovení

Při obnovení se v Azure Database for MariaDB vytvoří nový server ze zálohy původního serveru a obnoví všechny databáze obsažené na serveru.

K dispozici jsou dva typy obnovení:

- Obnovení k určitému **bodu v čase** je dostupné s možností redundance zálohy a vytvoří nový server ve stejné oblasti jako původní server, který využívá kombinaci úplných záloh a zálohování protokolu transakcí.
- **Geografické obnovení** je k dispozici pouze v případě, že jste server nakonfigurovali pro geograficky redundantní úložiště, a umožňuje obnovit váš server do jiné oblasti s využitím nejaktuálnějšího zálohování.

Odhadovaná doba obnovení závisí na několika faktorech, včetně velikostí databází, velikosti transakčního protokolu, šířky pásma sítě a celkového počtu databází obnovování ve stejné oblasti ve stejnou dobu. Doba obnovení je obvykle méně než 12 hodin.

> [!IMPORTANT]
> Odstraněné servery **nelze** obnovit. Pokud server odstraníte, odstraní se i všechny databáze patřící do serveru a nebude možné je obnovit. Pro ochranu prostředků serveru, po nasazení, před náhodným odstraněním nebo neočekávaným změnám můžou správci využít [zámky pro správu](../azure-resource-manager/management/lock-resources.md).

### <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase

Nezávisle na možnosti redundance záloh můžete provést obnovení do libovolného bodu v čase v rámci doby uchovávání záloh. Nový server se vytvoří ve stejné oblasti Azure jako původní server. Vytvoří se s konfigurací původního serveru pro cenovou úroveň, generování výpočtů, počet virtuální jádra, velikost úložiště, dobu uchování zálohy a možnost redundance zálohy.

Obnovení k bodu v čase je užitečné ve více scénářích. Například když uživatel omylem odstraní data, ponechá důležitou tabulku nebo databázi, nebo pokud aplikace náhodně přepíše dobrá data s chybnými daty z důvodu vady aplikace.

Možná budete muset počkat, než bude provedena další záloha protokolu transakcí, než bude možné provést obnovení k určitému bodu v čase během posledních pěti minut.

### <a name="geo-restore"></a>Geografické obnovení

Server můžete obnovit do jiné oblasti Azure, kde je služba k dispozici, pokud jste server nakonfigurovali pro geograficky redundantní zálohy. Servery, které podporují až 4 TB úložiště, se dají obnovit do geografické spárované oblasti nebo do jakékoli oblasti, která podporuje až 16 TB úložiště. Pro servery, které podporují až 16 TB úložiště, se geografické zálohy dají obnovit v libovolné oblasti, která podporuje i 16 TB serverů. Seznam podporovaných oblastí najdete v [Azure Database for MariaDB cenové úrovně](concepts-pricing-tiers.md) .

Geografické obnovení je výchozí možností obnovení v případě, že server není k dispozici z důvodu incidentu v oblasti, kde je server hostován. Pokud má velký incident v oblasti nedostupnost vaší databázové aplikace, můžete obnovit server z geograficky redundantní zálohy na server v jakékoli jiné oblasti. Geografické obnovení využívá nejnovější zálohu serveru. Doba mezi vytvořením zálohy a při replikaci do jiné oblasti trvá zpoždění. Tato prodleva může trvat až jednu hodinu, takže pokud dojde k havárii, může dojít ke ztrátě dat o hodinu.

> [!IMPORTANT]
>Pokud se u nově vytvořeného serveru provede geografické obnovení, může počáteční synchronizace zálohování trvat více než 24 hodin v závislosti na velikosti dat, protože počáteční kopie úplného zálohování snímku je mnohem vyšší. Následné zálohy snímků jsou přírůstkové kopírování, takže obnovení jsou rychlejší po 24 hodinách vytváření serveru. Pokud vyhodnocujete geografické obnovení, abyste definovali RTO, doporučujeme, abyste počkali a vyhodnotili geografické obnovení **jenom po 24 hodinách** vytváření serveru pro lepší odhady.

Během geografického obnovení můžou konfigurace serveru, které je možné změnit, zahrnovat výpočetní generování, vCore, dobu uchování záloh a možnosti redundance zálohování. Změna cenové úrovně (Basic, Pro obecné účely nebo paměťově optimalizovaná) nebo velikosti úložiště během geografického obnovení není podporovaná.

Odhadovaná doba obnovení závisí na několika faktorech, včetně velikostí databází, velikosti transakčního protokolu, šířky pásma sítě a celkového počtu databází obnovování ve stejné oblasti ve stejnou dobu. Doba obnovení je obvykle méně než 12 hodin.

### <a name="perform-post-restore-tasks"></a>Provádění úloh po obnovení

Po obnovení z některého mechanismu obnovení byste měli provést následující úlohy, aby se uživatelé a aplikace mohli zálohovat a spustit:

- Pokud má nový server nahradit původní server, přesměrujte klienty a klientské aplikace na nový server.
- Aby se uživatelé mohli připojit, zajistěte, aby byla k dismístě vhodná pravidla VNet. Tato pravidla se nekopírují z původního serveru.
- Zajistěte, aby byla zajištěna příslušná přihlášení a oprávnění na úrovni databáze.
- Podle potřeby nakonfigurujte výstrahy.

## <a name="next-steps"></a>Další kroky

- Další informace o provozní kontinuitě najdete v tématu [Přehled provozní kontinuity](concepts-business-continuity.md).
- Chcete-li obnovit k určitému bodu v čase pomocí Azure Portal, přečtěte si téma [obnovení serveru k určitému bodu v čase pomocí Azure Portal](howto-restore-server-portal.md).
- Obnovení k určitému bodu v čase pomocí rozhraní příkazového řádku Azure najdete v tématu [obnovení serveru k určitému bodu v čase pomocí](howto-restore-server-cli.md)rozhraní příkazového řádku (CLI).
