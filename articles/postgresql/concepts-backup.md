---
title: Zálohování a obnovení – databáze Azure pro PostgreSQL – jeden server
description: Přečtěte si o automatickém zálohování a obnovení databáze Azure pro postgreSQL server – jeden server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 3e6dfd5882e49ad903e8cff6f0ec7f3d6bd4a8b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77619631"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---single-server"></a>Zálohování a obnovení v databázi Azure pro PostgreSQL – jeden server

Azure Database for PostgreSQL automaticky vytvoří zálohy serveru a uloží je do místně nakonfigurovaného místně redundantního nebo geograficky redundantního úložiště. Zálohy lze použít k obnovení serveru do určitého bodu v čase. Zálohování a obnovení jsou nezbytnou součástí každé strategie kontinuity podnikání, protože chrání vaše data před náhodným poškozením nebo odstraněním.

## <a name="backups"></a>Zálohování

Azure Database for PostgreSQL přebírá zálohy datových souborů a transakční protokol. V závislosti na podporované maximální velikosti úložiště buď pořizujeme úplné a rozdílové zálohy (4 TB max servery úložiště) nebo zálohy snímků (až 16 TB max servery úložiště). Tyto zálohy umožňují obnovit server na libovolný bod v čase v rámci nakonfigurované doby uchovávání záloh. Výchozí doba uchovávání záloh je sedm dní. Volitelně jej můžete nakonfigurovat až na 35 dní. Všechny zálohy jsou šifrovány pomocí 256bitového šifrování AES.

Tyto záložní soubory nelze exportovat. Zálohy lze použít pouze pro operace obnovení v Azure Database pro PostgreSQL. Ke kopírování databáze můžete použít [pg_dump.](howto-migrate-using-dump-and-restore.md)

### <a name="backup-frequency"></a>Frekvence zálohování

Obecně platí, že úplné zálohy dochází týdně, rozdílové zálohy dochází dvakrát denně pro servery s maximální podporované úložiště 4 TB. U serverů podporujících až 16 TB úložiště probíhá zálohování snímků alespoň jednou denně. Zálohování transakčních protokolů v obou případech probíhá každých pět minut. První snímek úplného zálohování je naplánován ihned po vytvoření serveru. Počáteční úplné zálohování může trvat déle na velkém obnoveném serveru. Nejstarší bod v čase, do kterého lze obnovit nový server, je čas dokončení počáteční úplné zálohy. Vzhledem k tomu, že snímky jsou okamžité, servery s podporou až 16 TB úložiště lze obnovit až do doby vytvoření.

### <a name="backup-redundancy-options"></a>Možnosti redundance zálohování

Azure Database for PostgreSQL poskytuje flexibilitu při výběru mezi místně redundantním nebo geograficky redundantním úložištěm záloh v úrovních optimalizace pro obecné účely a paměť. Pokud jsou zálohy uloženy v geograficky redundantním úložišti záloh, jsou uloženy nejen v oblasti, ve které je server hostován, ale jsou také replikovány do [spárovaného datového centra](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). To poskytuje lepší ochranu a možnost obnovit server v jiné oblasti v případě havárie. Úroveň Basic nabízí pouze místně redundantní úložiště záloh.

> [!IMPORTANT]
> Konfigurace místně redundantního nebo geograficky redundantního úložiště pro zálohování je povolena pouze při vytváření serveru. Jakmile je server zřízen, nelze změnit možnost redundance úložiště záloh.

### <a name="backup-storage-cost"></a>Náklady na úložiště záloh

Azure Database for PostgreSQL poskytuje až 100 % úložiště zřízeného serveru jako úložiště záloh bez dalších nákladů. Obvykle je to vhodné pro zálohování uchovávání sedm dní. Jakékoli další použité úložiště záloh se účtuje v gb-měsíc.

Pokud jste například zřídit server s 250 GB, máte 250 GB úložiště záloh bez dalších poplatků. Úložiště nad 250 GB se účtuje.

## <a name="restore"></a>Obnovení

V Azure Database for PostgreSQL se provedením obnovení vytvoří nový server ze záloh původního serveru.

K dispozici jsou dva typy obnovení:

- **Obnovení bodu v čase** je k dispozici s možností redundance zálohování a vytvoří nový server ve stejné oblasti jako původní server.
- **Geografické obnovení** je k dispozici pouze v případě, že jste server nakonfigurovali pro geograficky redundantní úložiště a umožňuje obnovit server do jiné oblasti.

Odhadovaná doba obnovení závisí na několika faktorech, včetně velikosti databáze, velikosti transakčního protokolu, šířky pásma sítě a celkového počtu databází, které se současně obnovují ve stejné oblasti. Doba zotavení je obvykle kratší než 12 hodin.

> [!IMPORTANT]
> Odstraněné servery **nelze** obnovit. Pokud odstraníte server, budou odstraněny také všechny databáze, které patří k serveru, a nelze je obnovit. K ochraně prostředků serveru mohou správci po nasazení před náhodným odstraněním nebo neočekávanými změnami využívat [zámky správy](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

### <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase

Nezávisle na možnosti redundance zálohování můžete provést obnovení do libovolného bodu v čase během doby uchovávání záloh. Nový server se vytvoří ve stejné oblasti Azure jako původní server. Je vytvořen s konfigurací původního serveru pro cenovou úroveň, generování výpočetních prostředků, počet virtuálních jader, velikost úložiště, dobu uchování záloh a možnost redundance zálohování.

Obnovení bodu v čase je užitečné ve více scénářích. Například když uživatel omylem odstraní data, klesne důležitou tabulku nebo databázi nebo pokud aplikace omylem přepíše dobrá data se špatnými daty z důvodu vady aplikace.

Možná budete muset počkat na další záloha transakční protokol, které mají být přijata před obnovením do bodu v čase během posledních pěti minut.

### <a name="geo-restore"></a>Geografické obnovení

Server můžete obnovit do jiné oblasti Azure, kde je služba k dispozici, pokud jste nakonfigurovali server pro geograficky redundantní zálohy. Servery, které podporují až 4 TB úložiště, lze obnovit do geograficky spárované oblasti nebo do jakékoli oblasti, která podporuje až 16 TB úložiště. U serverů, které podporují úložiště o velikosti až 16 TB, lze geografické zálohy obnovit v libovolné oblasti, která podporuje také servery o velikosti 16 TB. Zkontrolujte [Azure Database pro cenové úrovně PostgeSQL](concepts-pricing-tiers.md) pro seznam podporovaných oblastí.

Geografické obnovení je výchozí možnost obnovení, pokud je server nedostupný z důvodu incidentu v oblasti, kde je server hostován. Pokud rozsáhlý incident v oblasti vede k nedostupnosti databázové aplikace, můžete obnovit server z geograficky redundantních záloh na server v jakékoli jiné oblasti. Existuje prodleva mezi při zálohování a při replikaci do jiné oblasti. Toto zpoždění může být až hodinu, takže pokud dojde k havárii, může dojít ke ztrátě dat až jednu hodinu.

Během geografického obnovení zahrnují konfigurace serveru, které lze změnit, výpočetní generování, virtuální jádro, dobu uchování záloh a možnosti redundance zálohování. Změna cenové úrovně (základní, obecné účely nebo optimalizované pro paměť) nebo velikostúložiště není podporována.

### <a name="perform-post-restore-tasks"></a>Provádění úloh po obnovení

Po obnovení z obou mechanismů obnovení byste měli provést následující úkoly, abyste obnovili a zprovoznili uživatele a aplikace:

- Pokud má nový server nahradit původní server, přesměrujte klienty a klientské aplikace na nový server.
- Ujistěte se, že jsou pro uživatele k připojení zavedena příslušná pravidla brány firewall a virtuální sítě. Tato pravidla nejsou zkopírována z původního serveru.
- Zajistěte, aby byla zavedena příslušná přihlášení a oprávnění na úrovni databáze.
- Podle potřeby nakonfigurujte výstrahy.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak obnovit pomocí [portálu Azure](howto-restore-server-portal.md).
- Přečtěte si, jak obnovit pomocí [příkazového příkazového příkazu Konto Azure](howto-restore-server-cli.md).
- Další informace o kontinuitě provozu najdete v přehledu [kontinuity provozu](concepts-business-continuity.md).
