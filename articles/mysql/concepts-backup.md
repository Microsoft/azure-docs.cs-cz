---
title: Zálohování a obnovení v Azure Database for MySQL
description: Přečtěte si o automatických zálohách a obnovení serveru Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: fbd595c7de0bde4e8ba8b7aaa9a65aa5880c1165
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151915"
---
# <a name="backup-and-restore-in-azure-database-for-mysql"></a>Zálohování a obnovení v Azure Database for MySQL

Azure Database for MySQL automaticky vytvoří zálohy serveru a uloží je v uživatelsky nakonfigurovaném místně redundantním nebo geograficky redundantním úložišti. Zálohy lze použít k obnovení serveru do určitého bodu v čase. Zálohování a obnovení jsou důležitou součástí jakékoli strategie pro provozní kontinuitu, protože chrání vaše data před náhodným poškozením nebo odstraněním.

## <a name="backups"></a>Zálohování

Azure Database for MySQL přebírá zálohy datových souborů a transakčního protokolu. V závislosti na podporované maximální velikosti úložiště vezmeme úplné a rozdílové zálohy (4 TB max. servery úložiště) nebo zálohy snímků (až 16 TB maximálních úložných serverů). Tyto zálohy umožňují obnovit server k jakémukoli časovému okamžiku v rámci nakonfigurované doby uchovávání záloh. Výchozí doba uchovávání záloh je sedm dní. Volitelně je můžete [nakonfigurovat](howto-restore-server-portal.md#set-backup-configuration) až 35 dní. Všechny zálohy se šifrují pomocí šifrování AES 256-bit.

### <a name="backup-frequency"></a>Frekvence zálohování

Obecně platí, že k úplným zálohováním dochází týdně, rozdílové zálohování probíhá dvakrát denně pro servery s maximálním podporovaným úložištěm 4 TB. Zálohování snímků probíhá alespoň jednou denně pro servery, které podporují až 16 TB úložiště. V obou případech se k zálohování protokolu transakcí vyskytuje každých pět minut. První snímek úplného zálohování je naplánován ihned po vytvoření serveru. Počáteční úplná záloha může trvat delší dobu jako u velkého obnoveného serveru. Nejdřívějším bodem v čase, kdy je možné obnovit nový server, je čas, kdy bylo dokončeno prvotní úplné zálohování. Jelikož jsou snímky instantanious, servery s podporou až 16 TB úložiště je možné obnovit až do doby vytvoření.

### <a name="backup-redundancy-options"></a>Možnosti redundance zálohy

Azure Database for MySQL poskytuje flexibilitu při výběru místně redundantního nebo geograficky redundantního úložiště záloh v Pro obecné účely a paměťově optimalizovaných úrovních. Když jsou zálohy uložené v geograficky redundantním úložišti zálohování, neukládají se jenom v oblasti, ve které je váš server hostovaný, ale taky se replikují do [spárovaného datového centra](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). To zajišťuje lepší ochranu a možnost obnovení serveru v jiné oblasti v případě havárie. Úroveň Basic nabízí jenom místně redundantní úložiště záloh.

> [!IMPORTANT]
> Konfigurace místně redundantního nebo geograficky redundantního úložiště pro zálohování je povolená jenom během vytváření serveru. Po zřízení serveru nemůžete změnit možnost redundance úložiště zálohování.

### <a name="backup-storage-cost"></a>Náklady na úložiště zálohování

Azure Database for MySQL poskytuje úložiště zřízeného serveru jako úložiště pro zálohování až 100%, a to bez dalších nákladů. Obvykle je to vhodné pro uchovávání záloh sedmi dnů. Jakékoli další využité úložiště záloh se účtuje za GB-měsíc.

Pokud jste například zřídili Server s 250 GB, máte k dispozici až 250 GB úložiště zálohování bez dalších poplatků. Účtují se za úložiště převyšující 250 GB.

## <a name="restore"></a>Obnovení

Při obnovení se v Azure Database for MySQL vytvoří nový server ze zálohy původního serveru.

K dispozici jsou dva typy obnovení:

- Obnovení k určitému **bodu v čase** je dostupné s možností redundance zálohy a vytvoří nový server ve stejné oblasti jako původní server.
- **Geografické obnovení** je k dispozici pouze v případě, že jste server nakonfigurovali pro geograficky redundantní úložiště a máte možnost obnovit server do jiné oblasti.

Odhadovaná doba obnovení závisí na několika faktorech, včetně velikostí databází, velikosti transakčního protokolu, šířky pásma sítě a celkového počtu databází obnovování ve stejné oblasti ve stejnou dobu. Doba obnovení je obvykle méně než 12 hodin.

> [!IMPORTANT]
> Odstraněné servery **nelze** obnovit. Pokud server odstraníte, odstraní se i všechny databáze patřící do serveru a nebude možné je obnovit. Pro ochranu prostředků serveru, po nasazení, před náhodným odstraněním nebo neočekávaným změnám můžou správci využít [zámky pro správu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

### <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase

Nezávisle na možnosti redundance záloh můžete provést obnovení do libovolného bodu v čase v rámci doby uchovávání záloh. Nový server se vytvoří ve stejné oblasti Azure jako původní server. Vytvoří se s konfigurací původního serveru pro cenovou úroveň, generování výpočtů, počet virtuální jádra, velikost úložiště, dobu uchování zálohy a možnost redundance zálohy.

Obnovení k bodu v čase je užitečné ve více scénářích. Například když uživatel omylem odstraní data, ponechá důležitou tabulku nebo databázi, nebo pokud aplikace náhodně přepíše dobrá data s chybnými daty z důvodu vady aplikace.

Možná budete muset počkat, než bude provedena další záloha protokolu transakcí, než bude možné provést obnovení k určitému bodu v čase během posledních pěti minut.

### <a name="geo-restore"></a>Geografické obnovení

Server můžete obnovit do jiné oblasti Azure, kde je služba k dispozici, pokud jste server nakonfigurovali pro geograficky redundantní zálohy. Pro servery, které podporují až 16 TB úložiště, se geografické zálohy dají obnovit jenom v oblastech, které podporují i 16 TB serverů. Seznam podporovaných oblastí najdete v [Azure Database for MySQL cenové úrovně](concepts-pricing-tiers.md) . 

Geografické obnovení je výchozí možností obnovení v případě, že server není k dispozici z důvodu incidentu v oblasti, kde je server hostován. Pokud má velký incident v oblasti nedostupnost vaší databázové aplikace, můžete obnovit server z geograficky redundantní zálohy na server v jakékoli jiné oblasti. Doba mezi vytvořením zálohy a při replikaci do jiné oblasti trvá zpoždění. Tato prodleva může trvat až jednu hodinu, takže pokud dojde k havárii, může dojít ke ztrátě dat o hodinu.

Během geografického obnovení můžou konfigurace serveru, které je možné změnit, zahrnovat výpočetní generování, vCore, dobu uchování záloh a možnosti redundance zálohování. Změna cenové úrovně (Basic, Pro obecné účely nebo paměťově optimalizovaná) nebo velikosti úložiště během geografického obnovení není podporovaná.

### <a name="perform-post-restore-tasks"></a>Provádění úloh po obnovení

Po obnovení z některého mechanismu obnovení byste měli provést následující úlohy, aby se uživatelé a aplikace mohli zálohovat a spustit:

- Pokud má nový server nahradit původní server, přesměrujte klienty a klientské aplikace na nový server.
- Zajistěte, aby se pro uživatele připojovala odpovídající pravidla brány firewall na úrovni serveru.
- Zajistěte, aby byla zajištěna příslušná přihlášení a oprávnění na úrovni databáze.
- Podle potřeby nakonfigurujte výstrahy.

## <a name="next-steps"></a>Další kroky

- Další informace o provozní kontinuitě najdete v tématu [Přehled provozní kontinuity](concepts-business-continuity.md).
- Obnovení k určitému bodu v čase pomocí Azure Portal najdete v tématu [obnovení databáze k určitému bodu v čase pomocí Azure Portal](howto-restore-server-portal.md).
- Postup obnovení do bodu v čase pomocí rozhraní příkazového řádku Azure najdete v tématu [obnovení databáze k určitému bodu v čase pomocí](howto-restore-server-cli.md)rozhraní příkazového řádku (CLI).
